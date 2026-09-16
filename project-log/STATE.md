# STATE

Current state only. Overwritten at every update. No history.

**Updated:** 16 September 2026

## Active stage

Stage 1, acquisition. Started Wed 16 Sep. Planned finish Fri 18 Sep. Stage 0 closed on time against its planned Tue 15 Sep.

No acquisition code written yet. The stage is at its decision point: the items under "Immediate next action" have to be settled before there is anything to write a skeleton against.

## Repo state

Branch `main`, tree clean, level with `origin/main` as of commit c3769f8.

- 740b529 repo skeleton and plan
- 2afe01f pbip project, theme and theme reference note
- 68dbc18 stage 0 closed: decisions, STATE, BACKLOG and PLAN actuals
- c3769f8 `.gitattributes` and line endings reference note

Settled since stage 0 closed:

- `.gitattributes` added at the root, `* text=auto` plus binary declarations for pbix, pbit, zip and parquet. The fifteen spurious pbip modifications that appeared after opening Power BI Desktop are gone, and the tree stays clean across a save.
- `Data` renamed to `data`, so `.gitignore` line 10 matches by rule rather than through Windows case insensitivity. Confirmed: `data/raw/*` is ignored, `data/sample/*` is not.
- uv project initialised and `pyproject.toml` committed. No dependencies and no virtual environment yet.

## What exists and what it holds

- `project-log/PLAN.md` - the signed off plan. Brief, deliverable, scope, acceptance criteria, nine stages with exit criteria, milestones with both estimates, stopping rules, environment.
- `project-log/BACKLOG.md` - two open items, two closed.
- `project-log/LESSONS.md` - gitignored. Stage 0 and stage 1 process lessons.
- `project-log/decisions/01-planning.md` - thirteen stage 0 decisions, written up in full.
- `project-log/handoffs/stage-1-acquisition.md` - opening message for this chat. Its "Constraint" section is now out of date, see the working arrangement below.
- `theme/open-payments-theme.json` - theme source of truth. A second copy lives inside the report at `StaticResources/RegisteredResources/`, written there on import. Edits to the source must be re-imported.
- `notes/cms-open-payments-source-reference.md` - APIs, file naming, counts, field traps.
- `notes/powerbi-theme-json.md` - theme JSON schema and the property name traps.
- `notes/git-line-endings.md` - CRLF and LF, why Windows tools produce empty diffs, `.gitattributes` and the renormalisation procedure. Reusable across projects.
- `CMS Open Payments Dashboard.pbip` plus its `.Report` and `.SemanticModel` folders. Empty report, empty model.
- `data/` - empty. Nothing downloaded.

## Working arrangement

Claude's shell now reaches the local filesystem, so the stage 0 blocker no longer applies. The arrangement is unchanged by choice: Nauman writes and runs all code, Claude does not open the acquisition script and does not run it, and reads only `project-log/`, `notes/` and config files directly. Run output reaches Claude by paste. Revisit at stage close if it proves inefficient.

## Immediate next action

1. Create the virtual environment with uv. Commit `uv.lock`, `.venv/` is already ignored.
2. Settle the stage 1 decisions, none of which are taken yet:
   - Parquet conversion engine and strategy, against 15.4 GB usable RAM and 16.1 million rows by 91 columns for 2025 general alone.
   - Type policy at conversion, and whether the schema is pinned across the five years or inferred per file.
   - What the baseline file holds and where each figure comes from. The published dollar totals sit on the CMS Facts page, which Claude does not visit under the standing source rule.
   - Layout under `data/`, and what resumable after interruption means concretely.
   - Dependencies to add to `pyproject.toml`, which follows from the first two.
3. Then the acquisition script skeleton.

## Blockers

None.

The misspelled folder `CMS Open Payments Dashbaord` was dropped in the app but stays mounted for the life of this session. Empty, nothing reads from it. Should be gone by the stage 2 chat.
