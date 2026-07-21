# Mobile App

## Mental Model

The worker-facing app is the entire product experience for the primary
user — there is no separate "driver web portal" to fall back on. Every
screen exists to answer one of three questions a worker has mid-shift:
*"Am I covered right now?"*, *"Is something disrupting my zone?"*, and
*"Where's my payout?"* See [Architecture](./02-architecture.md) for the
full mobile-first rationale.

## Stack

React Native / Expo, with `MockDataContext` + `AuthContext` providing
state during the hackathon build (polling the live backend rather than
using genuinely static mock data — see
[Deployment](./07-deployment.md)'s live-vs-mocked table).

## Screen Structure

| Screen | Purpose |
|---|---|
| `welcome` / `onboarding` | First-run flow |
| `login` / `login-form` / `signup` | Auth |
| `(tabs)/index` (Home) | Trip status, coverage banner, recent payout card |
| `(tabs)/claims` | Claim filing form, status timeline |
| `(tabs)/coverage` | Coverage details/eligibility |
| `(tabs)/profile` | Account/profile management |
| `modal` | Generic modal shell |

## Home Screen States (from README)

| Screen State | What Worker Sees | Action Available |
|---|---|---|
| Trip Active | Green "Insurance Active / Trip Protected" banner | Tap to end trip |
| Disruption Detected | Red alert card with disruption message + "File a Claim" | Tap to open Claims tab |
| Post-Payout | "Recent Payout" card — amount, destination, date | — |

## Claims Flow States

```
Empty State → "Report a Disruption" CTA + eligibility info
  ↓
Form → disruption type picker, description, optional photo upload
  ↓
Under Review → 5-step timeline, current step highlighted
  ↓
Paid → timeline complete, green "Payout completed" step
```

The `CoverageHonoredCard` component (added in Phase 2) handles the
shift-cap case explicitly — when a second disruption of the same type
is detected within the 8-hour window (see
[Financial Model](./03-financial-model.md)), the UI must communicate
"we saw this, you're covered, but no new payout" rather than silently
doing nothing, which would look like a bug to the worker.

## Offline-First Design

React Native + AsyncStorage caches trip state and queues claim
submissions locally, so state survives a network drop mid-storm —
exactly the moment a claim is most likely to be filed and network is
most likely to be unreliable. This is architecturally load-bearing, not
a nice-to-have: a web app losing state on refresh at this exact moment
would be a core product failure, not an edge case.

## Distribution

- **Expo Go** — scan QR for development/demo access
- **Web build** — deployed to Vercel (`quick-cover-865h.vercel.app`)
- **Android APK** — pre-built via EAS Build, sideloadable without
  Android Studio or Expo tooling, connecting directly to the live
  Render backend with a pre-seeded demo account (25 trips, full
  eligibility)

## Common Mistakes to Avoid When Extending This

- **Building a new screen that requires connectivity to render basic
  state** — breaks the offline-first guarantee the whole app is built
  around; cached state should render first, live data should refresh
  over it.
- **Adding a new claim-status transition without updating the 5-step
  timeline UI** — the timeline is the worker's primary trust signal
  during the anxious "did my claim go through" window; a missing step
  reads as the app being broken.

## Related Docs

[Backend & Admin](./06-backend-and-admin.md) for the API endpoints this
app calls. [AI/ML Models](./04-ai-ml-models.md) for what happens after a
claim is submitted.
