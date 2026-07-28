# AU Education Data Relay

Companion repo for the **AU Tertiary Education Dashboard**. A daily GitHub Action downloads the
government spreadsheets that the dashboard's sandbox cannot reach (Department of Education monthly
summary data tables, Home Affairs visa pivots on data.gov.au, NCVER tables, ABS labour force), parses
them on GitHub's runners, and commits machine-readable outputs. The dashboard pipeline then pulls the
parsed data through github.com.

## One-time setup (~10 minutes)

1. Create a **public** GitHub repository (any name, e.g. `au-education-data-relay`).
2. Upload the contents of this `relay/` folder to the repo root, keeping the structure:
   - `.github/workflows/fetch.yml`
   - `fetch_sources.py`
   - `requirements.txt`
   - `README.md`
   (Web UI: *Add file → Upload files*, drag the folder contents in. The `.github/workflows/` path matters.)
3. Go to the repo's **Actions** tab → enable workflows → open **Fetch AU education data** → *Run workflow*
   (this first manual run seeds the `data/` folder; afterwards it runs daily at 18:10 UTC ≈ 4am AEST).
4. In the workflow permissions (*Settings → Actions → General → Workflow permissions*), select
   **Read and write permissions** so the Action can commit its output.
5. Tell the dashboard the repo path (`<user>/<repo>`) — it goes into
   `auto_dashboard/relay_config.json` and the daily dashboard task takes it from there.

## What the Action produces

- `data/series_bundle.json` — compact series in the dashboard's uniform schema (the pipeline ingests this)
- `data/*.csv` — full parsed tables for the record
- `data/manifest.json` — per-source status: fetched_at, URL used, rows parsed, sheet inventory, errors
- `raw/` — the original spreadsheets (small ones only), for audit

## Expectation management

Government spreadsheet layouts are undocumented and change without notice. The parsers are written
defensively: anything they cannot parse is still downloaded, inventoried in `manifest.json` (sheet
names + first rows), and committed raw, so the parser can be adjusted after the first real run.
Treat the first run as calibration, not failure.
