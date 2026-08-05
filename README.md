# Production Summary Dashboard

A fully offline, self-contained production dashboard for spinning-mill MIS
(Management Information System) reports. Built for a monthly `.xlsx` workbook
where each sheet is one calendar day.

**Live demo:** once GitHub Pages is enabled for this repo, the dashboard is
published at `index.html` — see [Publishing to GitHub Pages](#publishing-to-github-pages)
below. `index.html` in this repo ships with **no data pre-loaded**; open it and
use the **Manage Data** tab to upload your own monthly workbook. Nothing is
uploaded anywhere — the file is parsed entirely in your browser.

## What it does

- **Daily Report** — pick any date and see that day's actual vs target
  production, efficiency, top stoppage reasons by unit, waste breakdown,
  item-level (yarn count) production, and utilities.
- **Monthly Overview** — KPI summary and trend charts (production, efficiency,
  unit split, waste, production mix by yarn count), filterable to a single
  month or all loaded months.
- **Yearly Overview** — month-by-month rollup table and chart, once more than
  one month of data is loaded.
- **Monthly Stoppage & Usage Summary** — stoppage Pareto by category/unit,
  electricity usage, units-per-kg vs efficiency.
- **Daily Detail Overview** — efficiency heatmap and a sortable daily register
  with click-through to item-level detail.
- **Manage Data** — upload a workbook to load or refresh data, or delete a
  single day, all without leaving the page.

## Design constraints

- **Zero external dependencies.** No CDN scripts, no build step. Every chart
  is hand-rolled inline SVG, and the `.xlsx` reader is a from-scratch ZIP +
  DEFLATE (`DecompressionStream('deflate-raw')`) + OOXML parser written in
  plain JavaScript. This matters for mill/corporate networks that block
  external CDN requests — the whole dashboard works completely offline.
- **Single HTML file.** Everything (CSS, JS, and the currently-loaded data)
  lives in one `.html` file you can email, save locally, or host as a static
  page.

## Regenerating a filled-in dashboard from your own workbook

```bash
python3 generate_production_dashboard.py path/to/your-mis-report.xlsx output.html
```

Expected workbook layout: one sheet per day, sheet names like `01.07.2026` or
`01.07.26`. Each sheet has Unit 1 / Unit 2 production tables (Sl no, Item,
No. of Mcs, Mix/Count, Target Production, T-Gps, Actual Production, A-Gps,
Waste Qty), a Grand Total row, a Stoppage % block, a Units Consumed block
(EB / Solar / Total / UKG), and a Waste Stock block (LD, FS, Sweeping, Trench,
H.W, Roving, Micro dust, Green Belt, Cloth bales by Unit 1/Unit 2).

If your layout differs, adjust the column offsets in `parse_workbook()`.

To regenerate the public, no-data template (`index.html` in this repo):

```bash
python3 generate_production_dashboard.py --blank index.html
```

## ⚠️ Keep real production data out of this repo

`generate_production_dashboard.py` run against a real workbook produces an
HTML file with that workbook's actual production numbers embedded in it. If
this repo is public, **do not commit a generated dashboard that contains real
company data** — only `index.html` (the blank template) is meant to be here.
Generate and keep filled-in dashboards locally, or in a private location.
`.gitignore` in this repo already excludes `Production_Dashboard_*.html` and
`output.html` to help prevent accidental commits.

## Publishing to GitHub Pages

1. Push this repo to GitHub (see the commands your assistant gave you, or):
   ```bash
   git remote add origin https://github.com/<your-username>/<repo-name>.git
   git branch -M main
   git push -u origin main
   ```
2. On GitHub: **Settings → Pages → Source → Deploy from a branch → `main` / `/(root)`** → Save.
3. GitHub will publish `index.html` at `https://<your-username>.github.io/<repo-name>/`.

Note: GitHub Pages sites are publicly reachable at that URL by anyone who has
it, regardless of whether the repo itself is public or private (private-repo
Pages restriction is a GitHub Enterprise feature). That's why `index.html`
here ships blank.
