# Home Health Cycle Calculator

A Medicare home health **date-cycle calculator**. Enter a **Start of Care (SOC) date** and get every key regulatory date for the episode — billing periods, OASIS, NOA, certification, recertification, and supervisory visits.

Mirrors the Med-Pass OASIS Compliance Compass, rebuilt as a free, self-contained web tool.

## What it computes (from SOC)

| Deadline | Offset | Rule |
|----------|--------|------|
| Notice of Admission (NOA) | +5 days | Claims Processing Manual Ch. 10 |
| Comprehensive OASIS assessment | +5 days | 42 CFR §484.55 |
| OASIS transmission | +35 days (5 + 30) | Claims Processing Manual Ch. 10 |
| Initial physician certification | +30 days | Benefit Policy Manual Ch. 7 §30.5 |
| Face-to-Face encounter | −90 → +30 days | Benefit Policy Manual Ch. 7 §30.5.2 |
| 30-day billing periods (PDGM) | every 30 days | 42 CFR §484.205 |
| Recertification | every 60 days | 42 CFR §484.60 |
| Supervisory visits | every 14 days | 42 CFR §484.80 |
| Final claim | +12 months | Claims Processing Manual Ch. 1 |

## Use

Open `index.html` in any browser — no build, no server, no dependencies. Everything runs client-side; **no patient data leaves the browser**.

Or deploy it:

```bash
# Cloudflare Pages
wrangler pages deploy . --project-name=homehealthcycle

# GitHub Pages
# push to main → Settings → Pages → root / main
```

## Files

- `index.html` — the entire app (markup + styles + logic)
- `architectural.md` — design + regulatory rationale
- `README.md` — this file

## Disclaimer

Reference tool only. Deadlines on weekends/holidays may roll to the next business day; verify final billing deadlines with your MAC. Not payment or compliance advice.