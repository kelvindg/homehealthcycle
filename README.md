# Home Health Cycle Calculator

A Medicare home health **date-cycle calculator**. Enter a **Start of Care (SOC) date** and get every key regulatory date for the episode — billing periods, OASIS, NOA, certification, recertification, and supervisory visits.

Mirrors the Med-Pass OASIS Compliance Compass, rebuilt as a free, self-contained web tool.

## What it computes (from SOC)

| Deadline | Offset | Rule |
|----------|--------|------|
| Notice of Admission (NOA) | +5 days | Claims Processing Manual Ch. 10 |
| Comprehensive OASIS assessment | +48 hours | 42 CFR §484.55 (stricter than 5-day federal minimum) |
| OASIS transmission | +32 days (2 + 30) | Claims Processing Manual Ch. 10 |
| Plan of Care certification | +30 days | Benefit Policy Manual Ch. 7 §30.5 |
| Face-to-Face encounter | −90 → +30 days | Benefit Policy Manual Ch. 7 §30.5.2 |
| 30-day billing periods (PDGM) | every 30 days | 42 CFR §484.205 |
| Recertification | every 60 days | 42 CFR §484.60 |
| HHA supervisory visits | every 14 days (from SOC or first visit) | agency policy |
| LVN supervisory visits | every 30 days (from SOC or first visit) | agency policy |
| Therapy supervisory visits | every 30 days (from Therapy Evaluation date) | agency policy |
| Final claim | +12 months | Claims Processing Manual Ch. 1 |

## Deploy workflow (source of truth)

**Cloudflare Pages is the source of truth.** This GitHub repo is a **mirror** that stays in sync with the live deployment — it is *not* wired up for CF Git-integration builds.

To ship a change:

```bash
# 1. Deploy the live site (source of truth)
wrangler pages deploy . --project-name=hhcycle --branch=main

# 2. Mirror to GitHub (keep the repo in sync)
git add -A && git commit -m "describe change" && git push origin main
```

Live URL: **https://hhcycle.d3guzman.com/** (also `https://hhcycle.pages.dev/`).

## Use

Open `index.html` in any browser — no build, no server, no dependencies. Everything runs client-side; **no patient data leaves the browser**.

## Files

- `index.html` — the entire app (markup + styles + logic)
- `architectural.md` — design + regulatory rationale
- `README.md` — this file

## Disclaimer

Reference tool only. Deadlines on weekends/holidays may roll to the next business day; verify final billing deadlines with your MAC. Supervisory cadences reflect agency policy; confirm with your state/agency. Not payment or compliance advice.