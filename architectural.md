# Home Health Cycle — Architecture

A Medicare home health **date-cycle calculator**. Given a **Start of Care (SOC) date**, the app computes every key regulatory date for that episode of care, mirroring the Med-Pass OASIS Compliance Compass.

## Purpose

Home health agencies must track a dense set of regulatory deadlines tied to the SOC date. Missing one (NOA, OASIS transmission, recertification, final claim) can mean payment reductions or denials. This tool removes the manual calendar math and gives clinicians/billers a single, auditable view of the episode timeline.

## Regulatory basis (all dates keyed off SOC)

| Date | Rule | Source |
|------|------|--------|
| Face-to-Face Encounter window | 90 days **before** SOC through 30 days **after** SOC | Medicare Benefit Policy Manual Ch 7 §30.5.2 |
| Initial assessment visit | Within 48 hrs of referral / return home, or on SOC date | 42 CFR §484.55 |
| Comprehensive OASIS assessment due | No later than **5 calendar days** after SOC | 42 CFR §484.55 |
| OASIS transmission due | Within **30 days** of assessment completion | Medicare Claims Processing Manual Ch 10 |
| NOA (Notice of Admission) due | Within **5 calendar days** of SOC | Medicare Claims Processing Manual Ch 10 |
| Initial physician certification due | No later than **30 days** after SOC | Medicare Benefit Policy Manual Ch 7 §30.5 |
| 30-day billing periods (PDGM) | Unit of payment = 30-day periods of care | 42 CFR §484.205 / PDGM |
| Recertification | At least every **60 days** | 42 CFR §484.60 |
| Supervisory visits | At least every **14 days** (skilled nursing / aide) | 42 CFR §484.80 |
| Final claim due | Within **12 months** of last date of service | Medicare Claims Processing Manual Ch 1 |

## Architecture

**Stack:** Single self-contained static web app — one `index.html` with embedded CSS + vanilla JavaScript. No build step, no dependencies, no server. Deployable to any static host (Cloudflare Pages, GitHub Pages, Netlify, S3).

```
homehealthcycle/
├── index.html          # The entire app (markup + styles + logic)
├── architectural.md    # This document
├── README.md           # Usage + deploy notes
└── .gitignore
```

### Why single-file static?

- **Zero install / zero build** — open it or host it, it just works.
- **No PHI leaves the browser** — all date math runs client-side in JavaScript. No patient data is transmitted anywhere. This is critical for a healthcare-adjacent tool.
- **Trivially deployable** — one file to any static host.
- **Auditable** — the entire logic is in one place for review.

### Data flow

```
User enters SOC date
        │
        ▼
┌─────────────────────────────┐
│  computeEpisode(socDate)    │
│  (pure function, no I/O)    │
└─────────────────────────────┘
        │  returns { f2f, oasis, noa, cert,
        │            periods[], recerts[],
        │            supervisory[], claim }
        ▼
Render into color-coded date cards
```

The core logic is a **pure function** `computeEpisode(socDate)` — given a date, it returns a deterministic object of all derived dates. This makes it unit-testable and easy to reason about.

### Date math conventions

- **30-day billing period** = 30 consecutive days. Period 1 = SOC through SOC+29; Period 2 = SOC+30 through SOC+59; etc.
- **60-day episode / recert** = every 60 days from SOC (SOC+60, +120, +180…).
- **Supervisory visits** = every 14 days from SOC (SOC+14, +28, +42…).
- **Feb 29 quirk** (per Med-Pass): if Feb 29 falls within a 60-day episode, subtract 1 day from the episode end date. Implemented as a documented note in the UI.
- All arithmetic uses UTC-normalized dates to avoid timezone off-by-one errors.

## Security & privacy

- **No data leaves the browser.** No network calls, no analytics, no storage of patient data.
- No authentication needed — there is nothing to protect server-side.
- Suitable for use with real SOC dates because nothing is transmitted or persisted.

## Testing

The date math is a pure function, so it is testable in isolation. Manual test cases to verify:

1. SOC on a normal date → all derived dates shift by expected offsets.
2. SOC near month boundaries → 30-day periods roll correctly.
3. SOC in January → Feb 29 quirk note appears if applicable.
4. Leap-year SOC → day counts remain correct.
5. SOC on Dec 31 → year rollover handled.

## Deploy

```bash
# Cloudflare Pages
wrangler pages deploy . --project-name=homehealthcycle

# GitHub Pages
# push to main, enable Pages from / (root), branch main
```

## Roadmap

- [ ] Add a printable / exportable episode timeline (PDF).
- [ ] Add multiple-episode view (show recert chain across 6+ months).
- [ ] Add discipline-specific supervisory visit rules (SN vs aide).
- [ ] Add LUPA threshold indicator per 30-day period.
- [ ] Add unit tests for `computeEpisode` (extract to `app.js` + test runner).
