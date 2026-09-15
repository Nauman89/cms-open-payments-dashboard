# STATE

Current state only. Overwritten at every update. No history.

**Updated:** 15 September 2026

## Active stage

Stage 0 complete, on time against the planned Tue 15 Sep. Stage 1, acquisition, has not started. Planned finish Fri 18 Sep.

## Stage 0 exit criteria, all met

- Bucket A decisions recorded, bucket B items each carry named evidence and an owning stage.
- Stage list, exit criteria, stopping rules, provisional acceptance criteria and milestones agreed.
- PLAN.md signed off.
- Repo created and pushed to github.com/Nauman89/cms-open-payments-dashboard, branch main. Two commits: 740b529 skeleton, 2afe01f pbip and theme.
- pbip saved with TMDL confirmed. `definition/database.tmdl`, `model.tmdl` and `cultures/en-US.tmdl` present. No `.pbi` cache files tracked.
- Theme imported and applied.
- uv project initialised, pyproject.toml committed. No dependencies yet.

Outstanding from stage 0: the DECISIONS entries in `decisions/01-planning.md` are headings only. Nauman writes them.

## What exists and what it holds

- `project-log/PLAN.md` - the signed off plan. Brief, deliverable, scope, acceptance criteria, nine stages with exit criteria, milestones with both estimates, stopping rules, environment.
- `project-log/BACKLOG.md` - two open items.
- `project-log/LESSONS.md` - gitignored. Process lessons from stage 0.
- `project-log/decisions/01-planning.md` - thirteen decision headings, entries to be written.
- `project-log/handoffs/stage-1-acquisition.md` - opening message for the stage 1 chat.
- `theme/open-payments-theme.json` - theme source of truth. A second copy lives inside the report at `StaticResources/RegisteredResources/`, written there on import. Edits to the source must be re-imported.
- `notes/cms-open-payments-source-reference.md` - APIs, file naming, counts, field traps.
- `notes/powerbi-theme-json.md` - theme JSON schema and the property name traps.
- `CMS Open Payments Dashboard.pbip` plus its `.Report` and `.SemanticModel` folders. Empty report, empty model.

## Immediate next action

Open the stage 1 chat using `project-log/handoffs/stage-1-acquisition.md` as the opening message. First work: the acquisition script.

## Blockers

None. Claude's shell still cannot reach the local filesystem, a Windows update from 8 September 2026 broke the mount, so Claude writes files through the file bridge and Nauman runs every command. The folder connection also drops intermittently and has to be re-granted.
