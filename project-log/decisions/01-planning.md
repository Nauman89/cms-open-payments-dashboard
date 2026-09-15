# Decisions: Stage 0, Planning and Design

Append only. One entry per decision: what was chosen, why, what was rejected and why. Written by Nauman in his own words, never edited, never condensed.

Headings below list the decisions taken during intake on 14 and 15 September 2026. Entries to be written.

---

## D0.1 Dataset selection: CMS Open Payments
Two reasons for choosing this dataset: 
- We needed a dataset that could help me demonstrate different skills that were being demanded by clients on Upwork pertaining to Power BI. This dataset emerged as a good candidate to help portray Data modeling, Power Query, DAX, Report Design, Performance and API-based refresh related skills. Olist dataset available on Kaggle was a strong candidate but was rejected because the data was old (2018) and the dataset itself is commonly used by data visualizer as part of their portfolio project.
- We wanted to use a dataset that was Public Domain or CCO so that licensing does not become a hurdle. We previously explored Yelp Open Dataset but it was not available to use due to TOS restrictions. CMS Open Payment is a public open dataset so license is not a problem. 

## D0.2 Program years: 2021 to 2025
These five years give us a plethora of data to work with. Also, 2021 marks a structural change in the data as the Open Payment program was expanded to include Non-Physician Practitioners (NPPs) that include physician assistant, nurse practitioners, clinical nurse specialist, certified registered nurse anesthetist, anesthesiologist assistant and certified midwives. Additionally, 2020 was covid year and the largest general payment by category by volume is food and beverage. So choosing 2021-2025 removes the coverage break, keeps time intelligence intact and gives a model large enough to pose a performance problem. 

## D0.3 Acquisition method: bulk download with the metastore API resolving the URL
The Metastore API will return the current URL for each dataset. This will allow our automated pull to find the right file after a data refresh. 

## D0.4 Acquisition is scripted rather than manual
The acquisition is scripted since it will allow us to flex out data acquisition skills and also show off the API refresh skill 

## D0.5 No filtering before download
I can only decide which columns I want when I see the data and conduct EDA. So dropping any columns prematurely is incorrect

## D0.6 Transformation boundary: thin Python
I chose thin python for data cleaning because I wanted to practice and demonstrate my skill with Power Query. It must be stated that given the volume of data, if Power Query is slow, data cleaning will be done using Python. Also, doing the transformation in Power Query is what automates the dashboard as new data is added. Otherwise, a python script will need to run to clean/transform the data before it can be used by Power BI.

## D0.7 Repo delivery rather than publishing to the Power BI Service
The report will ship a public Github repo and Power BI Service is not utilized. Publishing on Power BI Service means that only prospective clients with a pro license can browse my work. The downside is that the client can only experience the dashboard in screenshots and pdf export. However, a clone of the repo will allow the prospective clients to run the dashboard on a stratified data sample.

## D0.8 pbip with TMDL rather than pbix
While a pbix can sit in a repo, it renders as a binary blob that can not be diffed and carries embedded data. pbip with TDML is what makes the repo viable  

## D0.9 Sample data: stratified, no synthetic data
This data is public and there are no restrictions on sharing it. So synthetic data is not required. Stratified data will help preserve the essence of the data by mimicking the mix of payment natures and recipients.  

## D0.10 Acceptance criteria: reconciliation in three parts, ten questions, performance split across three locking points
- Reconciliation since it is the easiest validation available. Actual row counts and column counts are known beforehand so our extracted data must match that. CMS rounds every published figure above one million, so published dollar totals will only match at the precision published. 
- Ten questions mean that each visual must answer a question about the data. Important to note, ten is just an estimation. The actual number may vary and will be finalized during EDA. Performance is important since it is a skill we wish to demonstrate and good performance on this dashboard is the only way it will run given millions of rows and high cardinality columns. 
- Performance is split across three points based on where it can be measured: (1) Full refresh from raw can be measured at stage 2 (2) Query duration can be measured at stage 5 and (3) Page render and cross filter progression can be measured in stage 6

## D0.11 Milestones: 20 business days, recorded alongside Claude's 29
The idea was that a tighter deadline is more realistic of client expectations and the added pressure will push me to work faster without sacrificing efficiency. 

## D0.12 Stopping rules for acquisition, source assessment and visual polish
Stopping rules exist to prevent a stage of the project to become a time sink and are as follows:
- Acquisition: 3 days. This is in days because the time sink here is making the script robust
- Source assessment: 2 days. The time sink here is profiling depth. Reconciliation, cardinality and null profile, unpivot multiplication factor and the measured one-year model size and refresh time is mandatory. Anything beyond this is a bonus.
- Power Query: 3 days (hardstop at 5). The stretch goals here are the principal investigator reshaping and loading research payments. They will be dropped if time does not permit it
- Visual polish: stop when the question list is exhausted 
- Known dependency: if any question locked at stage 2 depends on research payments, firing the stage 3 rule invalidates the acceptance criteria. Settled at stage 2, either by marking those questions optional or by accepting that firing the rule reopens the criteria as a logged decision.   

## D0.13 Theme defined at planning, scales deferred to report design
Theme applied at the start presets categorical data colour sequence, font family and sizes, background and foreground, and default properties like borders, gridlines, title alignment and number formats. This means I will not be doing this manually for each visual 