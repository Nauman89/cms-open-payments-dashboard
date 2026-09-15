# PLAN: CMS Open Payments Power BI Dashboard

Signed off 15 September 2026.

---

## 1. Brief

A Power BI portfolio dashboard built on CMS Open Payments, for Nauman's Upwork profile.

Not based on a single job post. Assembled from the skills clients ask for across many Power BI posts, since different posts prioritise different things, some the data schema, some digestible visuals.

The skills list is not a checklist. A skill that does not serve the report does not go in.

Two readers, not treated as mutually exclusive: the report serves a notional end user, the documentation serves a prospective client. Nothing is claimed that was not done.

---

## 2. Deliverable

A public GitHub repo containing:

- The pbip project folder with TMDL enabled, cache folder gitignored.
- A pbix exported at delivery.
- README with screenshots and a PDF export of the report.
- Technical notes.
- Data dictionary covering every table and field.
- The acquisition script, as a first class deliverable: documented, configurable, runnable by a third party.
- Refresh and connection instructions.
- Sample data.
- `project-log/` and `notes/`.
- LICENSE and .gitignore.

The report lives as a repo and is not published to the Power BI Service, so the 1 GB Pro semantic model cap does not apply. Staying under 1 GB preserves the option of publishing later at no extra cost.

---

## 3. Scope

**In**

- Program years 2021 to 2025, five years.
- Covered Recipient Profile Supplement, for non-physician practitioner specialties and licence states.
- Teaching hospitals as recipients, alongside physicians and non-physician practitioners.
- The product, drug and device dimension.
- Geographic grain down to city and postcode.
- Record types: general, research and ownership, subject to confirmation at source assessment.

**Out**

- Program years before 2021. 2019 and 2020 add complication without narrative payoff, and the non-physician practitioner coverage break falls at 2021.
- External data joins, unless a locked question cannot be answered without one.
- Publishing to the Power BI Service.

**Expected but not decided.** Recipient names and street addresses are expected to come out, on utility grounds rather than privacy, since the data is public. Whether any visual uses them has not been decided, so this closes with column selection at stage 2 rather than being settled here.

---

## 4. Acceptance criteria

Provisional at intake. Each locks at the stage named.

**Reconciliation.** Three parts, because the published figures do not all carry the same precision. The Facts page rounds every figure above one million.

- Record counts match the datastore API capture exactly, per program year and record type.
- Dollar totals match the published CMS figures at the precision published.
- Full precision dollar totals match the sum of the raw files. This is the part that proves nothing was lost in transformation.

Locks at stage 2.

**Question coverage.** The report answers 10 business questions, each traceable to a specific named visual. If the evidence supports a different number, changing it is a logged decision rather than a number that quietly grew.

Locks at stage 2, with the question list.

**Performance.** Split across three locking points, because the three components become measurable at different times.

- Full refresh from raw, and model size. Locks at stage 2.
- Query duration for the heaviest measures, timed in DAX Studio against the model. Locks at the end of stage 5, before report design begins.
- Page render and cross filter response. Locked on the first built page early in stage 6, before any polish.

The acceptance metric stays separate from the optimisation objective. No performance threshold is set after the thing it measures has been tuned.

Once the criteria are met the deliverable is done. Further improvement goes to BACKLOG.

---

## 5. Stages and exit criteria

Nine stages. Repo and environment setup sits inside planning. Data cleaning is merged into Power Query transformation, and splits out again only if cleaning moves to Python under the fallback.

### Stage 0: Planning and design

- Every bucket A decision recorded.
- Every bucket B item has named evidence and a stage that will produce it.
- Stage list, exit criteria, stopping rules, provisional acceptance criteria and milestones all agreed.
- PLAN.md written and signed off.
- Repo created: TMDL enabled, gitignore covering the pbip cache folder and the data directories, project-log skeleton, notes folder, LICENSE.
- Theme JSON committed, covering only what does not depend on the visuals: a categorical sequence of eight colours that stay distinguishable in greyscale and under common colour vision deficiencies, one neutral sequential ramp, a reserved accent, page and visual backgrounds, a font stack with a defined size scale, and defaults for gridlines, borders and number formats. Diverging scales and conditional formatting are deliberately excluded and set at stage 6.

### Stage 1: Acquisition

Raw files onto disk, reproducibly, by script. Nothing selected, filtered or cleaned.

- The script resolves the current download URL for each dataset through the metastore API. No hard coded filenames.
- Five program years plus the Covered Recipient Profile Supplement downloaded and unzipped into a raw directory that nothing downstream writes to.
- For every file: row count, column count and summed payment value match the figures captured from CMS on the download date.
- The CMS baseline capture is stored alongside the data as a machine readable file, not pasted into a document.
- Publication date and download date recorded.
- Parquet conversion complete, every Parquet file reconciling to its source CSV on row count, column count and summed payment value.
- The script runs end to end from a clean checkout on another machine: configurable output path, declared dependencies, resumable after interruption, logging what it did and skipped.
- Script documented to the standard of a deliverable.

Out of scope for this stage: filtering, column selection, cleaning, type coercion beyond what Parquet requires.

### Stage 2: Source assessment

The measuring stage. Produces evidence, not choices. Profiling runs in Python over the Parquet files.

- A written profile covering, per candidate column: distinct count, null rate, sample values, and for text columns the variant problem.
- Per program year and record type: row counts and dollar totals, reconciled to the captured baseline.
- Fill rate per specialty slot, distinct taxonomy paths, depth distribution.
- Change_Type value distribution per year, and the contents of the Deleted and Removed Records file.
- Product slot fill rates and the row multiplication factor a full unpivot would produce.
- Column overlap between general, research and ownership, and what a shared fact grain would require.
- A measured VertiPaq model size for one program year with the candidate column set, extrapolated to five years against the memory ceiling.
- A measured Power Query refresh time for that year including the product unpivot. This is the probe that tests the thin Python decision before the rest of the chain is built on it.
- Every bucket B item closed with a decision recorded in DECISIONS, or explicitly deferred with a stated reason.
- Acceptance criteria locked.

The choices this evidence enables are made at the start of stage 3 and remain open to amendment. This stage decides nothing.

### Stage 3: Power Query transformation

Everything between the files on disk and the tables the model loads, including all cleaning.

- Multi year folder ingest, with program year and record type parsed from the filename.
- Every step renamed to say what it does.
- An explicit data type on every column, chosen rather than inherited.
- Errors and nulls handled deliberately, with the handling stated.
- No manual row edits.
- Query folding checked where supported, its absence noted where not.
- Every cleaning rule traceable to the source assessment evidence that justified it.
- Product slots unpivoted to a long product table.
- Specialty taxonomy split into levels.
- Dimension tables derived with keys.
- The whole transformation reproducible from raw with no manual steps.
- If any step moved to Python under the fallback, the handover contract documented: which tables, what grain, what types.

### Stage 4: Data model design

- Star schema, or a logged reason for something else.
- Facts and dimensions separated. Measures on a dedicated measures table.
- Single direction relationships unless bidirectional is justified and logged.
- Every relationship's cardinality verified against the data rather than assumed.
- A proper date table, marked as such. Auto Date/Time disabled.
- Unused columns removed.
- Model size measured and inside the agreed ceiling.

### Stage 5: DAX measures

- Measures rather than calculated columns wherever the calculation allows.
- No implicit measures anywhere.
- Consistent naming, base measures separated from derived, organised into display folders.
- Every measure reconciled against an independently verifiable figure.
- Measure definitions documented in the data dictionary.
- Query durations timed in DAX Studio, and the performance threshold for measures locked.

### Stage 6: Report design

- Every question on the locked list traceable to a specific visual.
- Theme applied from the JSON file. No visual by visual formatting.
- Every visual titled with the question it answers, not the chart type.
- Slicer and filter behaviour deliberate and documented, including cross filter directions.
- Colour legible and never the only carrier of meaning.
- No chart junk.
- Performance Analyzer run, slow visuals identified and addressed.
- Page render and cross filter thresholds locked on the first built page, before polish.
- Diverging scales and conditional formatting added to the theme file rather than applied visual by visual.

### Stage 7: Validation

- Totals reconcile to the captured CMS baseline on the defined list of figures.
- Cross filter and interaction behaviour tested against hand calculated expectations, not eyeballed.
- Edge cases checked: empty selection, single item selection, a date range with no data, and a program year with no non-physician practitioner records.
- Refresh tested end to end from raw on a clean run.
- A pass asking what a client could reasonably challenge, with each of those things checked.

### Stage 8: Handover

- pbix exported.
- Screenshots and PDF export in the README.
- Data dictionary written.
- Refresh and connection instructions written.
- Sample data generated to the agreed rule, and the pbip verified to open and render against it from a clean clone.
- Acquisition script verified from a clean checkout on a machine that has never run it.
- README and technical notes written by Nauman, then style passed: British spelling throughout, no dashes, answer first.
- AI disclosure present. No Co-Authored-By trailer or session link in any commit.
- LESSONS.md triaged and the process review carried out against the milestone record.

---

## 6. Milestone timeline

Business days. Dates assume a Monday to Friday week beginning Monday 14 September 2026 and make no allowance for public holidays.

Two estimates are recorded. Nauman's is the planning figure. Claude's is kept as a second reading so that after two or three projects the direction and size of the estimating bias becomes visible.

| Stage | Nauman | Claude | Planned finish | Actual | Slip reason |
|---|---|---|---|---|---|
| 0 Planning and design | 2 | 2 | Tue 15 Sep | | |
| 1 Acquisition | 3 | 4 | Fri 18 Sep | | |
| 2 Source assessment | 2 | 4 | Tue 22 Sep | | |
| 3 Power Query transformation | 3 | 5 | Fri 25 Sep | | |
| 4 Data model design | 2 | 3 | Tue 29 Sep | | |
| 5 DAX measures | 2 | 3 | Thu 1 Oct | | |
| 6 Report design | 2 | 3 | Mon 5 Oct | | |
| 7 Validation | 2 | 2 | Wed 7 Oct | | |
| 8 Handover | 2 | 3 | Fri 9 Oct | | |
| **Total** | **20** | **29** | | | |

Claude's estimate would put delivery at Thursday 22 October.

**Rules on dates.** A planned date is frozen when set and never rewritten. A stage runs as long as it needs, and the overrun is recorded as a slip against the original figure.

**Slip reasons** are recorded in three categories: learning time, genuine stage difficulty, and machine time. Machine time covers long refreshes, downloads and conversions. It belongs to neither of the other two and would corrupt the learning rate if folded into it.

---

## 7. Stopping rules

**Acquisition.** The unbounded part is hardening the script, not downloading. At 3 days, stop adding robustness, ship whatever downloads and reconciles, move the remaining hardening to BACKLOG.

**Source assessment.** The unbounded part is profiling depth. Four outputs are mandatory: the reconciliation, the cardinality and null profile, the unpivot multiplication factor, and the measured one year model size and refresh time. Everything else is optional within the time. At 2 days, stop profiling and close the bucket B items on the evidence in hand, recording any that remain open and why.

**Power Query transformation.** At 5 days, drop the principal investigator reshaping and load research payments with core columns only. Research payments come out entirely only if something more fundamental fails: they are 931,959 rows against 16.1 million general payments, so the fact table is cheap, and they carry $9.50bn of the $14.67bn published for 2025.

**Visual polish.** Stop when the question list is covered, or 2 days, whichever comes first.

**Dependency created by the stage 3 rule.** If any question locked at stage 2 depends on research payments, firing this rule invalidates the acceptance criteria. To be settled when the list is locked: either research dependent questions are marked optional, or firing the rule explicitly reopens the acceptance criteria as a logged decision.

---

## 8. Environment and tooling

- Machine: 16 GB RAM, 15.4 usable. This is the binding constraint, not disk. Refresh needs roughly twice the model size in memory, so the working ceiling is a compressed model of two to three gigabytes. 241 GB free local disk.
- File format: pbip with TMDL enabled. Cache folder gitignored. pbix exported at delivery.
- Refresh model: import.
- Transformation boundary: thin Python. Python does acquisition and Parquet conversion only. All cleaning and shaping lives in Power Query. Moves to balanced if the measured cost at stage 2 proves too high.
- Python environment: uv, inside the repo, so the acquisition script's dependencies are declared where a third party can install them.
- Profiling: Python over Parquet, not by loading eighty million rows into Power BI to find out what to load.
- Tools to be introduced: DAX Studio and VertiPaq Analyzer at stage 2.
- Source: openpaymentsdata.cms.gov, licensed as a US government work, terms checked.
- Data vintage stated in the README: publication date and download date. All program years were refreshed in June 2026, the next refresh is due January 2027.

---

## 9. Decisions pending source assessment

Each closes at stage 2 with named evidence.

Record types in scope, column selection (including recipient names and street addresses), whether five years fits the memory ceiling, geographic grain, specialty handling, Change_Type handling, the question list, page count and structure, acceptance criteria numbers, sample data volume and stratification rule, whether any external join is needed, and whether the transformation boundary stays thin Python.
