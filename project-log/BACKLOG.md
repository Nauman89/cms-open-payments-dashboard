# BACKLOG

One file, not split, because items cross stage boundaries.

Each item is tagged with the stage it arose in and a severity.

**Severity:** `blocker` stops a stage passing, `should` is worth doing before handover, `nice` is phase two.

Format: `- [stage] [severity] description`

---

- [0] [should] Decide how the delivered pbix is distributed. A pbix built against the full five years will be far too large for git, so it is either built against the sample data or published as a release asset rather than committed. `.gitignore` currently excludes `*.pbix` on that assumption. Settle at stage 8.
- [0] [should] The theme exists in two places: `theme/open-payments-theme.json` and a copy inside the report at `StaticResources/RegisteredResources/`. Any edit to the source must be re-imported or the report keeps the old one. Relevant at stage 6 when diverging scales and conditional formatting are added.
- [0] [should] DECISIONS entries for stage 0 are headings only. Write them before stage 1 closes, while the reasoning is still fresh.

---

## Closed

- [0] Local folder was spelled `CMS Open Payments Dashbaord`. Renamed to `CMS Open Payments Dashboard` on 15 Sep 2026.
