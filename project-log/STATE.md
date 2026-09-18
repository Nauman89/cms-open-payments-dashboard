# STATE

Current state only. Overwritten at every update. No history.

**Updated:** 16 September 2026

## Standing instruction, set 16 September 2026

Process observations raised during this project are logged in `project-log/process-review.md` and acted on at project close, not during it. The project runs exactly as planned so the gaps and the benefits of the current process are both visible at the end. **Carry this into every stage handoff.**

## Active stage

Stage 1, acquisition. Day 1 of 3, started Wed 16 Sep, planned finish Fri 18 Sep. Stage 0 closed on time on Tue 15 Sep.

Day 1 went entirely on decisions. Every stage 1 decision is now closed and no acquisition code has been written yet.

## Decisions taken today, entries still to be written by Nauman

To go in `project-log/decisions/02-acquisition.md`, which does not exist yet.

- **D1.1** Conversion engine: DuckDB. Polars and pandas rejected. The reasoning that matters is the criterion correction: the first answer was chosen on speed and memory from a general comparison, when the stage is scored on reconciliation.
- **D1.2** Type policy: every column written as text, one payment column cast to `DECIMAL` at verification time, cast failures and coverage counted, column headers pinned per file and compared rather than assumed. Chosen because it keeps stage 1 from pre-empting the measuring stage.
- **D1.3** External dollar reconciliation kept as a documented manual check rather than scripted. Evidence: the CMS Facts page is client side rendered and carries no figures in its HTML, and CMS documents no aggregate or SQL endpoint, only a 500 row paged datastore query. Automating it would need an undocumented endpoint or a headless browser. An exact external row count already covers what the dollar total would catch, and the published figure is rounded above one million. **This amends the acceptance criteria in PLAN section 4, which remain provisional until stage 2.**
- **D1.4** Layout and resumability. Raw separated from derived, year folders under raw, the profile supplement outside the year structure, zips kept, reconciliation evidence committed outside `data/`. Resumable means write to a temporary name and rename on completion, so presence at the final path proves completeness. Applies to download, extract and convert alike.
- **D1.5** Dataset identification: discover the five datasets through the metastore dataset list by title, log the ids it resolves, and keep those ids as a fallback.

## Settled and mechanical, no decision entry needed

- Dependencies: `duckdb` and `requests`. Standard library covers zip extraction, JSON, paths, logging and command line arguments. No robots parser needed, since the script calls documented APIs and resolved download URLs rather than crawling.
- Self imposed limit: five second delay between HTTP requests, per the site's robots.txt, which allows all agents on all paths with `Crawl-delay: 5`.
- Layout under `data/`: `raw/zips/`, `raw/program_year=YYYY/`, `raw/supplement/`, `parquet/program_year=YYYY/`, `parquet/supplement/`, `sample/`. Scripts in `scripts/`, reconciliation evidence in `reconciliation/`.

## Script structure, agreed

1. Command line entry point. Output root, program years, force, dry run.
2. Metastore resolution. Dataset id in, current download URL and publication date out.
3. External baseline capture. Row count per dataset from the datastore count endpoint, column list, publication date, capture date. Writes the baseline JSON.
4. Download. Streamed, temporary name, rename on completion, skip if the final path exists.
5. Extract. Same temporary and rename rule.
6. Header check. First line of each CSV against the expected column list, stop on mismatch.
7. Convert. DuckDB, every column as text, temporary name and rename.
8. Measure. Row count, column count, decimal sum with cast failures and coverage, over both CSV and Parquet.
9. Compare and report. Writes the results JSON.
10. Throttle. Five second delay between HTTP requests.

Skeleton for the first half of step 2 has been given. Nothing has been typed yet.

## Repo state

Branch `main`. Latest commit 8b47c5b. Uncommitted: `PLAN.md` and `.gitignore`.

- `.gitattributes` in place, `* text=auto` plus binary declarations. Tree stays clean across a Power BI Desktop save.
- `data/` renamed from `Data`, empty.
- uv virtual environment created. No dependencies added yet, so no `uv.lock`.
- PLAN amended in two places today: section 4 reconciliation criteria per D1.3, section 8 tooling per D1.1 and D1.2.
- `.gitignore` now also excludes `project-log/process-review.md`.

## What exists and what it holds

- `project-log/PLAN.md` - the signed off plan, amended 16 Sep.
- `project-log/STATE.md` - this file.
- `project-log/BACKLOG.md` - two open items, four closed.
- `project-log/LESSONS.md` - gitignored. Stage 0 and stage 1 incidents.
- `project-log/process-review.md` - gitignored. Five candidate amendments to the working instructions, each with the test that decides it at close.
- `project-log/decisions/01-planning.md` - thirteen stage 0 decisions, written up in full.
- `project-log/handoffs/stage-1-acquisition.md` - opening message for this chat. Its "Constraint" section is out of date, see below.
- `theme/open-payments-theme.json` - theme source of truth. A second copy sits inside the report at `StaticResources/RegisteredResources/` and edits must be re-imported.
- `notes/cms-open-payments-source-reference.md` - APIs, file naming, counts, field traps.
- `notes/powerbi-theme-json.md` - theme JSON schema and property name traps.
- `notes/git-line-endings.md` - CRLF and LF, `.gitattributes`, renormalisation. Reusable.
- `CMS Open Payments Dashboard.pbip` plus `.Report` and `.SemanticModel`. Empty report, empty model.

## Working arrangement

Claude's shell reaches the local filesystem, so the stage 0 blocker no longer applies and the handoff's "Constraint" section is wrong. The arrangement is unchanged by choice: Nauman writes and runs all code, Claude does not open the acquisition script and does not run it, and reads only `project-log/`, `notes/` and config files. Run output reaches Claude by paste.

Claude must use `git --no-optional-locks` for inspection. A plain `git status` from Claude's shell leaves a stale `.git/index.lock` that Claude cannot delete and that blocks Nauman's next commit.

## Immediate next action

1. Nauman writes D1.1 to D1.5 in `decisions/02-acquisition.md`.
2. Commit the amended PLAN and `.gitignore`.
3. Add `duckdb` and `requests`, commit `uv.lock`.
4. Start typing the script at step 2, metastore resolution.

Nauman is doing the dataset literature and methodology reading over the weekend of 19 and 20 September, ahead of the stage 2 question list. Logged as PR2.

## Blockers

None.

A stale `.git/index.lock` was left by Claude earlier today and must be deleted by Nauman before the next commit.
