# AI/ML Models

## Mental Model

Three distinct models, three distinct jobs, run at three distinct
moments in a claim's lifecycle: **XGBoost prices risk before the trip
starts** (dynamic surcharge), **Isolation Forest verifies the claim
after it's filed** (fraud scoring), and **a GenAI vision model closes
the loop on quarantined claims** (automated photo adjudication). None
of the three is a black box — all three produce interpretable,
auditable scores.

## Model 1 — XGBoost Dynamic Premium Pricing

**Why XGBoost:** handles tabular, mixed-type features with non-linear
interactions well, trains fast on modest hardware, and produces
directly interpretable feature-importance scores — the last point is
called out explicitly as important for regulatory transparency under
IRDAI's micro-insurance sandbox.

### Feature Inputs

| Feature | Source | Why It Matters |
|---|---|---|
| `rainfall_mm_hr` | IMD/weather API (zone grid) | Primary trigger signal |
| `temp_celsius` | Weather API | Extreme heat threshold (>43°C) |
| `aqi_index` | CPCB Air Quality API | High AQI correlates with reduced trip completion |
| `zone_disruption_history_7d` | Internal trips table | Recent disruptions elevate near-term risk |
| `active_driver_count_zone` | Platform webhook | Driver shortage amplifies per-driver exposure |
| `pool_balance_normalised` | Internal state table | Low balance → increase surcharge to replenish |
| `hour_of_day` | System clock | Evening peak (18:00-22:00) has 2.3x higher claim rate |
| `day_of_week` | System clock | Weekend spikes; Monday pool often depleted |
| `platform_outage_flag` | Platform status webhook | Active outage immediately elevates surcharge ceiling |

### Output Mapping

| Predicted Risk Score | Surcharge | Label |
|---|---|---|
| 0.0–0.30 | ₹1.50–₹2.00 | Low |
| 0.31–0.60 | ₹2.00–₹3.50 | Medium |
| 0.61–0.80 | ₹3.50–₹4.50 | High |
| 0.81–1.00 | ₹4.50–₹5.00 | Critical |

### Retraining Loop (Weekly, Sunday 02:00 IST)

```
1. Pull last 7 days of trips, disruptions, actual payouts
2. Label each trip: disrupted (1) or completed (0)
3. Retrain on rolling 90-day window (prevents concept drift)
4. Validate on held-out 10% of last week's data — require AUC > 0.78
5. Shadow-run new model 24hrs alongside production; compare surcharge distributions
6. Promote if MAE on surcharge prediction < ₹0.40; else alert on-call
```

**Why this loop matters as a pattern:** shadow-running before promotion,
gated on a concrete metric (MAE < ₹0.40), is a solid template for
retraining any pricing/scoring model that directly touches money —
prevents a retrained model from silently degrading production pricing.

## Model 2 — Isolation Forest Fraud Detection

**Why Isolation Forest:** unsupervised anomaly detection — doesn't
require labeled fraud examples to train, which matters at launch when
there are zero historical fraud cases. It isolates anomalies by random
feature-space partitioning; points requiring fewer partitions to isolate
are more anomalous.

### Feature Inputs

| Feature | What Anomaly Looks Like |
|---|---|
| `gps_velocity_kmh_max` | >80 km/h between pings = physically impossible on a motorcycle ("teleportation") |
| `gps_coordinate_entropy` | Perfectly static coordinates during a 45-min "active trip" = likely spoofed |
| `accelerometer_variance` | Near-zero variance during a moving trip = stationary device with animated GPS |
| `mock_location_flag` | Android mock-location provider active = third-party GPS spoofing app |
| `gps_vs_cell_tower_delta_km` | GPS says Zone A, cell tower says Zone C = coordinate spoofing |
| `claims_per_driver_7d` | >3 claims in 7 days = systematic abuse pattern |
| `zone_claim_density_zscore` | 50 claims from one zone in 10 min during low-rainfall = coordinated fraud ring |
| `ip_geolocation_delta_km` | Device IP resolves far from claimed GPS = VPN/proxy spoofing |

### 3-Tier Scoring

| Anomaly Score | Tier | Action |
|---|---|---|
| 0.0–0.45 | Auto-Approve | Payout released immediately |
| 0.46–0.70 | Quarantine | Worker prompted for 1 timestamped photo, routed to Model 3 |
| 0.71–1.00 | Auto-Reject | Claim denied, fraud flag logged for analyst |

## Model 3 — GenAI Vision Adjudication (Gemini 1.5 Flash)

**The role:** traditional ML flags a claim as suspicious; the GenAI
agent *closes the loop* by reading unstructured visual evidence and
making a binary authenticity call — eliminating the human-adjuster
queue entirely for the quarantine tier.

### Execution Steps

1. **Parse scene content** — visually identify disruption evidence
   (waterlogged roads, barricades, shuttered stores, flooding).
2. **Cross-reference claim context** — compare scene against filed
   disruption type, zone, timestamp (a "heavy rain" claim with a dry
   sunny road photo fails).
3. **Validate metadata integrity** — check image EXIF geotag/capture
   time against claim timestamp and last-known GPS.
4. **Return structured decision** — `is_authentic_disruption: true/false`
   with a `confidence_score` (0.0–1.0). ≥0.75 auto-releases funds;
   <0.40 escalates to human review.

### Business Value (vs. Manual Adjudication)

| Metric | Without GenAI | With GenAI |
|---|---|---|
| Adjudication time | 24–72 hrs | <3 minutes |
| Cost per claim | ₹150–300 | ₹2–5 |
| False positive rate | ~15% | ~8% |

## Anti-Spoofing Strategy (Three Pillars)

Because payouts are automatic, GPS spoofing is the primary fraud
vector. Three-pillar defense:

1. **Behavioral anomaly detection** — teleportation velocity filtering,
   clustering-anomaly detection (real storms produce naturally *drifting*
   GPS across many honest workers; spoofing produces unnaturally
   identical/static coordinates), context-aware movement analysis
   (spoofed devices don't show the slower/detour movement pattern real
   flooded-road navigation produces).
2. **Beyond raw GPS** — OS-level mock-location flags, network
   triangulation/IP cross-check against claimed GPS, accelerometer/
   gyroscope telematics confirming physical movement consistent with
   route navigation.
3. **Quarantine, don't deny (UX balance)** — heavy rain causes *natural*
   network drops; auto-denying flagged claims would punish honest
   workers. Instead: flag as "Pending Review" (not rejected) → wait for
   connectivity → push notification once network stabilizes → low-
   friction single-photo verification → GenAI (Model 3) reviews it.

**Why the quarantine pattern matters generally:** this is a reusable
design principle beyond fraud detection — when a detection signal has a
plausible *benign* cause (network drop during a storm) as well as a
malicious one (spoofing), the system should defer/verify rather than
immediately deny, to avoid punishing legitimate users for an ambiguous
signal.

## Common Mistakes to Avoid When Extending This

- **Treating XGBoost's risk score as the fraud signal** — pricing risk
  (will a disruption happen) and fraud risk (is this specific claim
  fake) are different questions with different models; don't conflate
  them.
- **Auto-rejecting on Isolation Forest score alone without the
  quarantine tier** — loses the benign-cause-handling the 3-pillar
  anti-spoofing strategy is built around.
- **Skipping the shadow-run step when retraining any of these models**
  — especially risky for the pricing model, since a bad retrain
  directly changes what consumers are charged in production.

## Related Docs

[Financial Model](./03-financial-model.md) for how the pricing output
feeds pool economics. [Backend & Admin](./06-backend-and-admin.md) for
the actual API endpoints (`/claim/adjudicate`, `/pricing/zone`) wrapping
these models.
