<div align="center">

# ⚡ GridCare

### Electricity Reliability & Maintenance Analytics

*One semantic model. Two questions answered together: **where do customers feel the most disruption**, and **which maintenance jobs need review first?***

<br>

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-008080?style=for-the-badge&logo=microsoft&logoColor=white)
![Power Query](https://img.shields.io/badge/Power%20Query-217346?style=for-the-badge&logo=microsoftexcel&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![CSV](https://img.shields.io/badge/CSV-Source%20Data-555555?style=for-the-badge)

![Status](https://img.shields.io/badge/status-in%20development-orange?style=flat-square)
![Data](https://img.shields.io/badge/data-100%25%20synthetic-blue?style=flat-square)
![Model](https://img.shields.io/badge/model-star%20schema-teal?style=flat-square)
![Snapshot](https://img.shields.io/badge/snapshot-30%20Sep%202026-lightgrey?style=flat-square)

<br>

<img src="images/reliability-overview.png" alt="GridCare Reliability Overview dashboard" width="92%">

<br><br>

<table>
  <tr>
    <td align="center"><b>📈 Reliability Overview</b><br><br><img src="images/reliability-overview.png" alt="Reliability Overview" width="100%"></td>
    <td align="center"><b>🛠️ Maintenance Snapshot</b><br><br><img src="images/maintenance-snapshot.png" alt="Maintenance Snapshot" width="100%"></td>
  </tr>
</table>

</div>

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Business Problem](#-business-problem)
- [Dashboard Preview](#-dashboard-preview)
- [Key Findings](#-key-findings)
- [Data Model](#-data-model)
- [Dataset](#-dataset)
- [KPI Definitions](#-kpi-definitions)
- [Report Pages](#-report-pages)
- [Tech Stack](#-tech-stack)
- [Limitations](#-limitations)
- [Roadmap](#-roadmap)
- [What I Learned](#-what-i-learned)
- [Disclaimer](#-disclaimer)

---

## 🔍 Overview

**GridCare** is a Power BI portfolio project exploring electricity distribution **reliability** and **maintenance priorities** using synthetic operational data.

Outage records and maintenance work orders normally answer different questions and live in different places. GridCare brings them into a single reporting model, connected by shared **feeder** and **date** dimensions, so customer impact can be read alongside outstanding maintenance.

> [!NOTE]
> **Status: 🚧 In development.** The semantic model and report visuals are built. Final report formatting and validation are ongoing, with machine learning and automation extensions planned.

---

## 🎯 Business Problem

Reviewing outages and maintenance separately makes it hard to see customer impact and outstanding work together. GridCare is built to answer:

| # | Question |
|---|----------|
| 1 | Which feeders contribute the most **customer interruption minutes**? |
| 2 | How **frequently** do customers experience interruptions? |
| 3 | How **long** do interruptions last? |
| 4 | Which feeders carry the largest **outstanding maintenance workload**? |
| 5 | Which **high-priority work orders** are overdue? |

---

## 🖥️ Dashboard Preview

### Page 1 — Reliability Overview
Customer interruption experience: SAIDI, SAIFI and CAIDI, monthly trend, outage causes and the top feeders by customer minutes.

<div align="center">
<img src="images/reliability-overview.png" alt="Reliability Overview page" width="95%">
</div>

<br>

### Page 2 — Maintenance Snapshot
Maintenance status at the fixed snapshot date: open and overdue work, high-priority overdue jobs and planned effort.

<div align="center">
<img src="images/maintenance-snapshot.png" alt="Maintenance Snapshot page" width="95%">
</div>

---

## 📊 Key Findings

Results for the full synthetic demo scope:

<div align="center">

| 🔌 Top feeder share | 🛠️ Unfinished orders | ⏰ Overdue | 🚨 High-priority overdue | ⏱️ Planned effort |
|:---:|:---:|:---:|:---:|:---:|
| **25.3%** | **74** | **65** | **21** | **930 hrs** |
| of interruption minutes from `DEMO-F10` | across all feeders | of the 74 unfinished | within the overdue set | estimated hours, unfinished work |

</div>

- **`DEMO-F10`** contributes approximately **25.3%** of customer interruption minutes and also has **five overdue maintenance orders** at the snapshot date.
- This makes it a sensible place to **start an investigation**, for example by asking a maintenance planner whether those jobs relate to the observed failures.

> [!IMPORTANT]
> These findings support investigation and maintenance planning. **They do not establish that overdue maintenance caused outages.**

---

## 🧩 Data Model

GridCare uses a **star schema** with two fact tables sharing conformed dimensions.

```mermaid
erDiagram
    DimDate    ||--o{ FactOutage    : "date"
    DimFeeder  ||--o{ FactOutage    : "feeder"
    DimCause   ||--o{ FactOutage    : "cause"
    DimDate    ||--o{ FactWorkOrder : "date"
    DimFeeder  ||--o{ FactWorkOrder : "feeder"

    DimDate {
        date Date
    }
    DimFeeder {
        string FeederName
    }
    DimCause {
        string Cause
    }
    FactOutage {
        grain "one row per outage event"
    }
    FactWorkOrder {
        grain "one row per work order"
    }
```

**Design decisions**

- ✅ **One-to-many** relationships from dimensions to facts.
- ✅ **Single-direction** filtering, from dimensions to facts.
- ✅ A **shared feeder dimension** gives consistent filtering across outage and maintenance reporting.
- ✅ **Separate fact tables** preserve each dataset's level of detail and avoid multiplying records through a direct fact-to-fact join.

---

## 🗂️ Dataset

All data is **fictional**, generated with Python for an electricity distribution reporting scenario.

| Table | Type | Contents |
|-------|:----:|----------|
| `DimDate` | Dimension | 365 calendar dates |
| `DimFeeder` | Dimension | 12 fictional electricity feeders |
| `DimCause` | Dimension | 6 outage cause classifications |
| `FactOutage` | Fact | 480 outage events |
| `FactWorkOrder` | Fact | 180 maintenance orders |

The maintenance dataset represents a **fixed snapshot as at 30 September 2026**.

---

## 📐 KPI Definitions

| KPI | Meaning | Reads as |
|-----|---------|----------|
| **SAIDI** | Average total interruption minutes per customer in the selected period | *How long, in total, does a typical customer lose supply?* |
| **SAIFI** | Average number of interruptions per customer in the selected period | *How often are customers interrupted?* |
| **CAIDI** | Average duration of a customer interruption (SAIDI ÷ SAIFI) | *When it happens, how long does it last?* |

Separating frequency (SAIFI) from duration (CAIDI) helps show whether a problem is **many short outages** or **fewer long ones**.

**Maintenance snapshot measures**

| Measure | Definition |
|---------|-----------|
| Open WOs | Work orders unfinished at the snapshot date |
| Overdue WOs | Open work orders past their due date (a subset of open) |
| High Priority Overdue | Overdue work orders flagged high priority |
| Open Estimated Hours | Planned hours for unfinished work |

> Snapshot KPI measures **remove calendar-date filters while retaining feeder filters**, keeping maintenance reporting consistent with the fixed snapshot date.

---

## 📄 Report Pages

### 1️⃣ Reliability Overview
- SAIDI, SAIFI, CAIDI and customer base cards
- Monthly SAIDI trend
- Customer minutes by outage cause
- Top six feeders ranked by customer interruption minutes
- "Investigate first" callout summarising the priority feeder

### 2️⃣ Maintenance Snapshot
- KPI cards: open, overdue, high-priority overdue and estimated hours
- Clustered bar chart comparing open and overdue orders by feeder (overdue is included within open, so the bars sit side by side)
- Work-order table, being refined into a **high-priority overdue review queue**

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| ![Power BI](https://img.shields.io/badge/-Power%20BI%20Desktop-F2C811?logo=powerbi&logoColor=black&style=flat-square) | Semantic modelling and interactive reporting |
| ![Power Query](https://img.shields.io/badge/-Power%20Query-217346?logo=microsoftexcel&logoColor=white&style=flat-square) | Data import and preparation |
| ![DAX](https://img.shields.io/badge/-DAX-008080?logo=microsoft&logoColor=white&style=flat-square) | Reliability KPIs and maintenance snapshot measures |
| ![Python](https://img.shields.io/badge/-Python-3776AB?logo=python&logoColor=white&style=flat-square) | Synthetic dataset generation and supporting analysis |
| ![CSV](https://img.shields.io/badge/-CSV-555555?style=flat-square) | Source datasets for the reporting model |

---

## ⚠️ Limitations

- The data is **synthetic** and does not reflect real-world operational performance.
- Maintenance records hold **one snapshot**, so historical backlog change cannot be shown.
- Estimated hours are **planned effort**, not actual time spent or hours saved.
- A feeder's outage impact and its overdue maintenance **do not prove causation**.
- Report formatting and final validation are still being completed.

---

## 🗺️ Roadmap

- [x] Star-schema semantic model
- [x] Reliability Overview page
- [x] Maintenance Snapshot page
- [ ] Complete report formatting, filter checks and KPI validation
- [ ] Python notebook to estimate **unplanned outage duration**
- [ ] Compare model performance against a **median-duration baseline** using a chronological split
- [ ] Automated **data-quality checks** and repeatable reporting outputs
- [ ] Daily maintenance snapshots or status-change records to analyse **backlog trends**

---

## 🎓 What I Learned

This project builds practical skills in:

- **Dimensional modelling** — choosing grain and keeping facts separate
- **DAX** — context-aware KPIs and snapshot logic
- **Operational reporting** — turning records into decisions
- **Communication** — explaining findings, assumptions and limits to non-technical stakeholders

The focus is a report whose calculations, assumptions and intended actions can be clearly explained.

---

## 📜 Disclaimer

> [!WARNING]
> This is an **independent portfolio project**. It uses **fictional data** and does **not** contain real operational data or represent the performance of any actual electricity network. Actual improvements in reliability, productivity or cost would require implementation and measurement in an operational environment.

<div align="center">

<br>

**⭐ If you found this useful, consider starring the repo.**

</div>
