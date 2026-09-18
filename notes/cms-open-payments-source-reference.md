# CMS Open Payments: source reference

Reusable reference for the Open Payments datasets. Facts verified on 14 and 15 September 2026 against the live site.

## What the programme is

US Sunshine Act disclosure of payments and other transfers of value from drug and device manufacturers and group purchasing organisations to physicians, teaching hospitals, and from program year 2021 onward additional provider types.

Published annually on or by 30 June, with a refresh each January. All program years are restated at each publication, so figures move between publications. The June 2026 publication refreshed every year from 2019 to 2025.

## Download layout

One zip per program year, roughly 1.12 GB for 2025, containing:

- `OP_DTL_GNRL_PGYR<year>_P<publication date>_<submission date>.csv`
- `OP_DTL_OWNRSHP_PGYR<year>_P<publication date>_<submission date>.csv`
- `OP_DTL_RSRCH_PGYR<year>_P<publication date>_<submission date>.csv`
- `OP_PGYR<year>_README_P<publication date>.txt`

The Covered Recipient Profile Supplement is a separate single file covering all years. The Deleted and Removed Records file lists previously published records since withdrawn, with record ID, payment type, program year and a change type.

The date stamps in the filename change at every refresh, so the filename must never be hard coded.

## APIs

Two separate APIs, easily confused.

**Metastore.** The catalogue. Returns title, description, publisher, licence, temporal coverage, modified date, and a distribution block holding the current download URL, media type and format. Contains no rows.

`https://openpaymentsdata.cms.gov/api/1/metastore/schemas/dataset/items/<dataset id>`

**Datastore.** The rows, queryable, JSON or CSV.

`https://openpaymentsdata.cms.gov/api/1/datastore/query/<dataset id>/0?limit=&offset=&count=true`
`https://openpaymentsdata.cms.gov/api/1/datastore/query/<dataset id>/0/download?format=csv`

Paging caps at 500 rows per request. A request for 1,000 returns HTTP 400. At 500 rows a request, one program year of general payments is roughly 32,000 requests, so the API is not a bulk path.

Indexed query keys: covered recipient profile ID, NPI, record ID, teaching hospital CCN, manufacturer payment ID, submitting manufacturer name, nature of payment, and recipient state combined with recipient type. Anything else filters unindexed.

The download endpoint takes the same conditions and column selection server side, which is the cheap way to pull a filtered extract without moving the whole file.

## Vocabulary, and one word that traps

The site runs on DKAN, which implements the DCAT vocabulary used by most government data catalogues. Three terms matter and one of them collides with everyday statistical usage.

**Dataset.** The abstract thing being published, for example "2021 General Payments". Carries a title, description, publisher, licence, temporal coverage and a modified date.

**Distribution.** One concrete downloadable form of a dataset. The same dataset offered as CSV and as JSON is one dataset with two distributions. A distribution holds a download URL, a media type, a format, usually a title, and in DKAN an identifier tying it to the datastore resource.

**Nothing in the metastore is computed from the rows.** It is a catalogue. Any statistical property of the data, including an actual distribution in the statistical sense, is produced by profiling the files, never read from the API.

The trap: "how many distributions does this dataset have" is a question about how many files are offered for download, not about the shape of any variable.

## Scale, program year 2025

| File | Rows | Columns |
|---|---|---|
| General payments | 16,131,856 | 91 |
| Research payments | 931,959 | 252 |
| Ownership and investment | 2,646 | 30 |
| Covered Recipient Profile Supplement | approx 1.5 million | 32 |

Published 2025 totals: $14.67bn overall, $3.92bn general, $9.50bn research, $1.25bn ownership. 17.07 million records. 1,833 paying companies, 667,898 physicians, 362,261 non-physician practitioners, 1,327 teaching hospitals.

The Facts page at `/summary` carries these per program year with a year selector covering 2019 to 2025, and states that all numbers above one million are rounded. Row counts from the datastore API are exact; these dollar totals are not.

## Field notes

**Dates.** General payments carries one real transaction date, `Date_of_Payment`. `Payment_Publication_Date` is a single constant across the whole file, so it cannot role play against the transaction date.

**Change_Type.** Four values, comparing the current publication against the previous one.

- `NEW`: added between the previous submission deadline and the current one.
- `ADD`: not eligible at the previous publication, eligible now, published for the first time.
- `CHANGED`: previously published and modified since, including a change in dispute status.
- `UNCHANGED`: previously published and unmodified.

Records deleted before the correction period closes never appear. Records deleted after it appear in the initial publication and are removed from the refresh onward.

**Specialty** is a pipe delimited taxonomy path, not a field. Example: `Allopathic & Osteopathic Physicians|Orthopaedic Surgery|Sports Medicine`. Up to six slots per record, so a rule is needed for which one counts, and the path needs splitting into levels before it is groupable.

**City** is not clean. Mixed casing confirmed in the 2025 file: YOUNGSTOWN, Venice, St Petersburg, HOOVER, Sanford, SANFORD. A distinct count of cities is wrong until normalised.

**Wide blocks.** General payments carries five product slots of six columns each. Research payments carries five principal investigator blocks of roughly twenty-eight columns each, which is where its 252 columns come from. Both are genuine unpivot targets, and the research one multiplies rows hardest.

**Coverage break.** Non-physician practitioners only enter the programme from 2021, so any series crossing 2020 to 2021 has a structural change in recipient coverage, not just a change in volume.

## Licence

US government work. See https://www.usa.gov/government-works
