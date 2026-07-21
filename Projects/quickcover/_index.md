# QuickCover Knowledge Pack

*AI-powered parametric income protection for India's gig-economy delivery workers — consumer-funded via a micro-surcharge, paying out automatically on verified weather/outage disruptions with zero paperwork.*

**Status:** Hackathon-complete (Guidewire DEVTrails 2026: Unicorn Chase). "Phase 3 — Soar" marked ✅ COMPLETE in the repo's own timeline. Live demo deployments exist for backend, mobile web, and admin dashboard.

**Tags:** #status/active #type/project #stack/react-native #stack/express #stack/postgres

**Key Constraint:** The driver pays nothing, ever. Every architectural and financial decision traces back to this — coverage is funded entirely by a consumer-side per-order micro-surcharge, not a worker premium.

## Quick Navigation

- **[Overview](./01-overview.md)** — Problem, solution, the Arjun persona walkthrough, hackathon context
- **[Architecture](./02-architecture.md)** — System diagram, mobile-first rationale, repo structure
- **[Financial Model](./03-financial-model.md)** — Surcharge pricing, payout triggers, shift-level capping, reinsurance strategy, unit economics
- **[AI/ML Models](./04-ai-ml-models.md)** — XGBoost dynamic pricing, Isolation Forest fraud detection, GenAI vision adjudication, anti-spoofing defense
- **[Mobile App](./05-mobile-app.md)** — React Native/Expo worker app, screen flows, offline-first design
- **[Backend & Admin](./06-backend-and-admin.md)** — Express API surface, admin dashboard, database schema
- **[Deployment](./07-deployment.md)** — Render/Vercel/Supabase topology, what's live vs. mocked
- **[Market Research](./08-market-research.md)** — SEWA precedent, regulatory tailwinds, business risks
- **[Roadmap](./09-roadmap.md)** — Development phase timeline, post-hackathon gaps

## Key Facts

| Aspect | Detail |
|--------|--------|
| **Repo** | [github.com/TarunSitaraman/QuickCover](https://github.com/TarunSitaraman/QuickCover) |
| **Live backend** | quickcover.onrender.com |
| **Live mobile (web)** | quick-cover-865h.vercel.app |
| **Live admin** | quick-cover-neon.vercel.app |
| **Demo account** | `demo@quickcover.in` / `demo1234` |
| **Target users** | Q-commerce delivery partners (Blinkit, Zepto, Swiggy Instamart) |
| **Core mechanism** | Parametric insurance — objective data triggers payout, no manual claims adjustment |

## Architecture At a Glance

```
Consumer checkout (₹2-5 surcharge, AI-priced)
  ↓
Premium Pool Manager (Express/Postgres)
  ↓
Parametric Trigger Engine ← polls IMD/OpenWeatherMap (rain, heat, AQI) + platform outage webhooks
  ↓
Disruption detected → Zero-touch claim auto-created for active workers in zone
  ↓
Isolation Forest fraud score → Auto-Approve / Quarantine (photo + GenAI vision check) / Auto-Reject
  ↓
Razorpay UPI payout → Worker's wallet (real transaction ID)
```

**The one-sentence pitch:** *"From a flooded road to ₹450 in a worker's UPI wallet in 37 minutes — with zero paperwork, zero cost, and zero prior insurance knowledge required."*

## For Future You (Resuming Work on This Project)

Start with [Roadmap](./09-roadmap.md) for the phase-by-phase build log and the "what's live vs. mocked" table — the single most useful artifact for understanding exactly how far this went past the hackathon demo.

---

**Source repo analyzed:** `TarunSitaraman/QuickCover` @ `main`, via `README.md` (684 lines — the primary source for this entire pack), file tree across `mobile/`, `mock-backend/`, `admin-frontend/`, and `mock-backend/server.js` route listing (2026-07-21).

---

## Related Systems / Reference

**If you're working on similar architecture:**
- [`Technologies/Docs/ai-agents.md`](../../Technologies/Docs/ai-agents.md) — compare QuickCover's GenAI vision-adjudication model against general agent architecture patterns
- [`Projects/personal-agent/`](../personal-agent/) — sibling project; compare its LLM fallback chain against QuickCover's single-model GenAI adjudication step
- [`DSA/01_Patterns/`](../../DSA/01_Patterns/) — the XGBoost/Isolation Forest model selection reasoning here is a good real-world companion to algorithmic pattern-recognition thinking, even though ML model selection isn't itself a DSA topic

**If this project needs interview-style talking points:**
- The financial model, fraud-defense three-pillar strategy, and "why mobile-first" sections in this pack are already written in a talking-points style — consider promoting the strongest of these directly into an `INTERVIEW_GUIDE.md` (see `Projects/smartresq/INTERVIEW_GUIDE.md` for the format) if pitching this project in interviews or to investors.
