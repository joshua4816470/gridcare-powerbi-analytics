# GridCare — Electricity Reliability & Maintenance Analytics

GridCare is a Power BI portfolio project that explores electricity distribution reliability and maintenance priorities using synthetic operational data.

The project brings outage performance and maintenance workload into one reporting model, helping users understand where customers experience the greatest disruption and which outstanding jobs need review.

**Status:** In development. The core semantic model and report visuals have been built. Final report formatting and validation are ongoing, with machine learning and automation extensions planned.

## Business problem

Outage records and maintenance work orders answer different operational questions. Reviewing them separately can make it difficult to understand customer impact alongside outstanding maintenance.

GridCare connects these datasets through shared feeder and date dimensions to answer questions such as:

- Which feeders contribute the most customer interruption minutes?
- How frequently do customers experience interruptions?
- How long do interruptions last?
- Which feeders have the largest outstanding maintenance workload?
- Which high-priority work orders are overdue?

## Dataset

The project uses fictional data designed for an electricity distribution reporting scenario.

| Table         | Contents                         |
| ------------- | -------------------------------- |
| DimDate       | 365 calendar dates               |
| DimFeeder     | 12 fictional electricity feeders |
| DimCause      | 6 outage cause classifications   |
| FactOutage    | 480 outage events                |
| FactWorkOrder | 180 maintenance orders           |

The maintenance dataset represents a fixed snapshot as at **30 September 2026**.

**This is an independent portfolio project. It does not contain Endeavour Energy operational data or represent the performance of any actual electricity network.**

## What I built

### Semantic model

I implemented a star-schema approach with two fact tables for outages and maintenance orders, supported by dimension tables for dates, feeders and outage causes.

The model uses:

- One-to-many relationships between dimensions and fact tables.
- Single-direction filtering from dimensions to facts.
- A shared feeder dimension for consistent filtering across outage and maintenance reporting.
- Separate fact tables to preserve each dataset’s level of detail and avoid multiplying records through a direct fact-to-fact join.

Each outage fact row represents an outage event, while each maintenance fact row represents a work order.

### Page 1: Reliability Overview

The first report page explores customer interruption experience through:

- **SAIDI:** Average total interruption minutes per customer during the selected period.
- **SAIFI:** Average number of interruptions per customer during the selected period.
- **CAIDI:** Average duration of a customer interruption.
- Monthly reliability trends.
- Outage cause analysis.
- Top six feeders ranked by customer interruption minutes.

These visuals help users identify where to investigate further and distinguish interruption frequency from interruption duration.

### Page 2: Maintenance Snapshot

The second report page focuses on maintenance status at the snapshot date through:

- Open work orders.
- Overdue work orders.
- High-priority overdue work orders.
- Estimated planned hours for unfinished work.
- A clustered bar chart comparing open and overdue orders by feeder.
- A work-order table being refined into a high-priority overdue review queue.

Overdue orders are included within open orders, so the chart displays these counts side by side.

Snapshot KPI measures remove calendar-date filters while retaining feeder filters, keeping the maintenance reporting consistent with the fixed snapshot.

## Example findings

For the full synthetic demo scope:

- DEMO-F10 contributes approximately **25.3% of customer interruption minutes**.
- The same feeder has **five overdue maintenance orders** at the snapshot date.
- Across all feeders, **74 work orders remain unfinished**.
- Of those, **65 are overdue**, including **21 high-priority orders**.
- Unfinished orders represent **930 estimated hours of planned effort**.

These findings support investigation and maintenance planning. They do not establish that overdue maintenance caused outages.

## What the project achieves

GridCare demonstrates how operational records can be organised into a reusable semantic model and translated into understandable business measures.

The report supports:

- Identifying feeders with high customer interruption impact.
- Reviewing outstanding maintenance alongside outage performance.
- Finding specific overdue jobs for discussion with maintenance planners.
- Applying consistent KPI definitions across report visuals.
- Communicating findings and data limitations to nontechnical stakeholders.

This is a demonstration of decision support. Actual improvements in reliability, productivity or cost would require implementation and measurement in an operational environment.

## Tools and techniques

- **Power BI Desktop:** Semantic modelling and interactive reporting.
- **Power Query:** Data import and preparation.
- **DAX:** Reliability KPIs and maintenance snapshot measures.
- **Python:** Synthetic dataset generation and supporting analysis.
- **CSV:** Source datasets for the reporting model.

## Current limitations

- The data is synthetic and does not establish real-world operational performance.
- Maintenance records contain one snapshot, so they cannot show how historical backlog changed.
- Estimated hours represent planned effort, not actual time spent or hours saved.
- A feeder’s outage impact and overdue maintenance do not prove a causal relationship.
- Report formatting and final validation are still being completed.

## Planned enhancements

- Complete report formatting, filter checks and KPI validation.
- Extend the Python notebook to estimate unplanned outage duration.
- Compare model performance against a simple median-duration baseline using a chronological data split.
- Add automated data-quality checks and repeatable reporting outputs.
- Introduce daily maintenance snapshots or status-change records to analyse backlog trends.

## Project learning

This project develops practical skills in dimensional modelling, DAX, operational reporting and communicating technical findings in business terms.

The focus is on building a report whose calculations, assumptions and intended actions can be clearly explained.
