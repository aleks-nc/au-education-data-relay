# AU Education Data Relay

Companion repo for the **AU Tertiary Education Dashboard**. A daily GitHub Action (18:10 UTC, ~4:10am AEST)
downloads the government spreadsheets the dashboard's sandbox cannot reach — Department of Education
international student monthly-summary data tables, Home Affairs visa pivots on data.gov.au, NCVER
apprentice tables, ABS Labour Force table 1 — parses them on GitHub's runners, and commits
machine-readable output. The dashboard pipeline pulls the parsed data through github.com.

## Outputs

- data/series_bundle.json — compact series in the dashboard's uniform schema (the pipeline ingests this)
- data/csv/<source>/ — every sheet of every workbook dumped to CSV
- data/manifest.json — per-source status: URLs used, sheet inventories (names + first rows), errors
- raw/ — original spreadsheets (up to 20 MB each), for audit

## Notes

Government spreadsheet layouts are undocumented and change without notice. Parsers are defensive:
anything unparsed is still downloaded, dumped to CSV and inventoried in the manifest, so parsers can
be calibrated against real files after the first runs. The ABS unemployment series (A84423050A) is
fully parsed into the bundle; DoE/Home Affairs/NCVER structured extraction is calibrated iteratively.

Run manually: Actions tab -> "Fetch AU education data" -> Run workflow.
