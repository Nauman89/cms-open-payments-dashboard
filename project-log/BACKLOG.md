# BACKLOG

One file, not split, because items cross stage boundaries.

Each item is tagged with the stage it arose in and a severity.

**Severity:** `blocker` stops a stage passing, `should` is worth doing before handover, `nice` is phase two.

Format: `- [stage] [severity] description`

---

- [0] [should] Decide how the delivered pbix is distributed. A pbix built against the full five years will be far too large for git, so it is either built against the sample data or published as a release asset rather than committed. `.gitignore` currently excludes `*.pbix` on that assumption. Settle at stage 8.
- [0] [should] The theme exists in two places: `theme/open-payments-theme.json` and a copy inside the report at `StaticResources/RegisteredResources/`. Any edit to the source must be re-imported or the report keeps the old one. Relevant at stage 6 when diverging scales and conditional formatting are added.

---

## Closed

- [0] Local folder was spelled `CMS Open Payments Dashbaord`. Renamed to `CMS Open Payments Dashboard` on 15 Sep 2026.
- [0] DECISIONS entries for stage 0 written up in full in `decisions/01-planning.md` on 15 Sep 2026.
- [0] No `.gitattributes`, so opening the pbip produced fifteen modified files that held no content change. Fixed on 16 Sep 2026 in commit c3769f8. Reference note at `notes/git-line-endings.md`.
- [0] `.gitignore` matched the raw data folder only through Windows case insensitivity. `Data` renamed to `data` on 16 Sep 2026.
