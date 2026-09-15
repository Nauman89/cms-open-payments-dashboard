# Handoff: Stage 1, Acquisition

Opening message for the stage 1 chat.

## Read first

`00-instructions-general.md`, `20-instructions-powerbi.md`, `project-log/PLAN.md`, `project-log/STATE.md`, `project-log/BACKLOG.md`, `project-log/decisions/01-planning.md`, and `notes/cms-open-payments-source-reference.md`.

## What is complete

Stage 0. The plan is signed off, the repo exists, the theme stub is committed. Every decision taken during intake is listed in `decisions/01-planning.md`.

## What stage 1 does

Gets the raw files onto disk reproducibly, by script. Nothing is selected, filtered or cleaned. Five program years, 2021 to 2025, plus the Covered Recipient Profile Supplement, then converted to Parquet.

The exit criteria are in PLAN section 5, stage 1. The short version: the script resolves URLs through the metastore API rather than hard coding filenames, every file reconciles on row count, column count and summed payment value against a CMS baseline captured on the download date, the Parquet files reconcile to their CSVs on the same three, and the script runs end to end from a clean checkout on another machine.

## What to watch out for

**The download filename changes at every refresh.** It carries publication and submission date stamps, so the metastore call is not optional decoration, it is how the script finds the current file.

**The baseline must be captured at download time, not looked up later.** CMS restates prior years at each refresh, so a baseline captured in January will not match files downloaded in September. Store it as a machine readable file beside the data.

**The Facts page rounds every figure above one million.** Row counts from the datastore API are exact, dollar totals on the Facts page are not. The reconciliation criterion has three parts for this reason, see PLAN section 4.

**Roughly 5.6 GB of zipped downloads, unpacking to something near 40 GB.** Disk is not a constraint at 241 GB free, but the time is machine time and is logged as its own slip category.

**The stopping rule bites on script hardening, not on downloading.** At 3 days, stop adding robustness, ship what downloads and reconciles, move the rest to BACKLOG.

## Working arrangement for this stage

Nauman writes the code. Claude supplies the skeleton: signature, docstring, numbered steps in plain English, no method name hints, and gives a method name immediately and plainly on request. Which steps needed a name hint goes in LESSONS.

## Constraint

Claude's shell cannot reach the local filesystem, so Claude cannot run or test the script. Every run is Nauman's, and output comes back into the chat.

## Domain rules that apply

General instructions throughout. The Power BI domain file governs from the emitted tables onward and does not apply to this stage, except that the handover contract it describes is what stage 1 is producing the inputs for.
