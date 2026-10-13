# Logistics Operations Analytics — Power BI Capstone Project

[![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black)](#)
[![Excel](https://img.shields.io/badge/Excel-217346?style=flat&logo=microsoftexcel&logoColor=white)](#)
[![DAX](https://img.shields.io/badge/DAX-2E5B8A?style=flat)](#)
[![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)](#)

> A 3-dashboard Power BI analytics suite built from a 14-table, 300,000+ row logistics operations database — covering revenue performance, service-quality, and fleet/safety operations for a U.S. trucking and freight business.

**Author:** Uthman B. Adedeji, ACA | Chartered Accountant & Data Analyst
📧 uthmanbolaji@gmail.com

---

## 📌 Project Overview

This capstone project analyzes three years (2022–2024) of operational data from a logistics and freight-trucking company to answer three core business questions:

1. **Where does revenue come from, and is the business growing?**
2. **How reliable is our delivery service — and where exactly does it break down?**
3. **Is the fleet being used efficiently, what's driving cost, and how safe are our operations?**

The end-to-end workflow — data cleaning, relational modeling, DAX measure design, and dashboard development — was built entirely in **Power BI Desktop**, backed by a cleaned, analysis-ready Excel data source.

---

## 🗂️ The Dataset

A 14-table relational database (`.csv`/`.xlsx`), modeled as a star-ish schema with `loads` and `trips` as the central fact tables:

| Table | Rows | Description |
|---|---|---|
| `loads` | 85,410 | Shipment-level records: revenue, weight, booking type |
| `trips` | 85,410 | Actual trip execution: distance, duration, fuel, MPG |
| `delivery_events` | 170,820 | Pickup/delivery timestamps, on-time flags, detention |
| `fuel_purchases` | 196,442 | Fuel transactions: gallons, price, cost, location |
| `maintenance_records` | 2,920 | Service history: cost, downtime, maintenance type |
| `safety_incidents` | 170 | Accidents, violations, claims, preventability |
| `customers` | 200 | Customer accounts, contract type, revenue potential |
| `drivers` | 150 | Driver demographics, tenure, license, employment status |
| `trucks` / `trailers` | 120 / 180 | Fleet asset registry |
| `routes` / `facilities` | 58 / 50 | Lane and terminal reference data |
| `driver_monthly_metrics` / `truck_utilization_metrics` | 4,464 / 3,312 | Pre-aggregated monthly performance summaries |

**Data cleaning performed (Python/pandas):**
- Parsed all date/timestamp fields to proper datetime types
- Converted `detention_minutes` → `detention_hours`; derived `delay_hours` (actual vs. scheduled)
- Standardized all revenue/cost fields to numeric USD, rounded to 2 decimals
- Derived `driving_time_hours` and `idle_pct_of_trip` from raw trip duration data
- Identified and excluded an incomplete trailing data month (Jan 2025 held ~2% of a normal month's volume) from all time-series visuals to avoid a misleading trend artifact

---

## 🧮 Data Model & DAX

Built a relational model in Power BI with 13 relationships connecting all 14 tables to the `loads`/`trips` fact pair, plus a dedicated `Calendar` date table (marked as the official Date Table) to support time-intelligence functions.

**Sample measures (DAX):**

```DAX
On-Time % =
DIVIDE(
    CALCULATE(COUNTROWS(delivery_events), delivery_events[on_time_flag] = TRUE()),
    COUNTROWS(delivery_events)
)

Revenue YoY % =
VAR CurrYear = [Total Revenue]
VAR PrevYear = CALCULATE([Total Revenue], SAMEPERIODLASTYEAR('Calendar'[Date]))
RETURN DIVIDE(CurrYear - PrevYear, PrevYear)

Preventable Incident % =
DIVIDE(
    CALCULATE(COUNTROWS(safety_incidents), safety_incidents[preventable_flag] = TRUE()),
    COUNTROWS(safety_incidents)
)
```

17 measures in total across revenue, service-quality, and fleet/safety domains — covering aggregation, ratio (`DIVIDE`), filtered (`CALCULATE`), and time-intelligence (`SAMEPERIODLASTYEAR`) DAX patterns.

---

## 📊 Dashboard 1 — Revenue & Customer Performance

![Revenue & Customer Performance dashboard](images/dashboard1_revenue_customers.png)

**Key findings:**
- **$262.5M** total revenue across **85,410 loads** ($3,074 avg/load), but revenue is **flat year-over-year** (+0.9%, 2024 vs. 2023) — the business is stable, not growing organically.
- Revenue is **well-diversified**: the top 10 customers account for only **19.8%** of total revenue — low concentration risk.
- Booking type (Contract/Dedicated/Spot) and freight type (Dry Van/Refrigerated) contribute near-identical revenue shares — lane and customer selection drive performance more than freight mix.
- **Recommendation:** pursue rate growth or new-lane development on top-performing corridors (e.g., Philadelphia→Seattle, Charlotte→Portland) to break the flat-growth plateau.

## 📊 Dashboard 2 — On-Time Delivery & Service Quality

![On-Time Delivery & Service Quality dashboard](images/dashboard2_ontime_service.png)

**Key findings:**
- Overall on-time performance is **55.7%** — the single biggest operational weakness identified in the data.
- **Deliveries (44.6% on-time) underperform Pickups (66.7%) significantly** — the bottleneck is concentrated at the destination end.
- Average delivery detention (1.78 hrs) exceeds pickup detention (1.27 hrs), consistent with the on-time gap.
- The shortfall is consistent across all facility types (54–56%), pointing to a **systemic scheduling/dispatch issue** rather than a single problem location.
- **Recommendation:** root-cause review of the 10 lowest-performing facilities; renegotiate delivery appointment windows and build in scheduling buffer.

## 📊 Dashboard 3 — Fleet, Fuel & Safety Operations

![Fleet, Fuel & Safety Operations dashboard](images/dashboard3_fleet_fuel_safety.png)

**Key findings:**
- Average truck utilization: **83.0%** — healthy, with identifiable headroom at specific terminals.
- Fuel cost fell from **$34.3M (2022) to $30.1M (2024)** despite flat consumption (~8.2M gallons/year) — driven by price-per-gallon dropping $4.20 → $3.65, **not** an efficiency gain (fleet MPG held flat at 6.50).
- **$5.73M** total maintenance cost, spread evenly across Preventive, Repair, Tire, Brake, Engine, and Transmission categories — no single cost driver to target.
- **170 safety incidents, 37.6% marked preventable**, totaling **$2.65M in claims**; DOT Violations are the most frequent incident type.
- **Recommendation:** targeted driver coaching/compliance program focused on DOT-violation-prone behavior, given over a third of incidents were avoidable.

---

## 🛠️ Tools & Skills Demonstrated

| Category | Tools / Techniques |
|---|---|
| Data Cleaning & Prep | Python (pandas), Excel |
| Data Modeling | Power BI (star schema, 13 relationships, Date table) |
| Analysis Language | DAX (aggregation, CALCULATE, DIVIDE, time intelligence) |
| Visualization | Power BI (bar, donut, line charts, KPI cards, slicers, cross-filtering) |
| Business Analysis | KPI definition, trend analysis, root-cause framing, actionable recommendations |

---

## 📁 Repository Contents

```
├── README.md                                   ← this file
├── Logistics_Operations_Dashboards.pbix        ← full Power BI report (3 pages, live measures)
├── Logistics_Operations_Database_Cleaned.xlsx  ← cleaned source data (14 tables)
├── images/                                     ← dashboard preview images
└── data/                                       ← raw source CSVs (optional, for reproducibility)
```

---

## ⚠️ Note on the preview images

The three dashboard images above are polished **design mockups** built from this project's real, calculated figures — they illustrate the intended final layout and color system. Before publishing this repo, replace them with **actual screenshots exported from the `.pbix` file** (File → Export → "Export to PDF/Image," or a screen capture of each report page) so the README shows the real, working Power BI report. This keeps the portfolio piece fully honest for anyone reviewing it — including a hiring manager who opens the `.pbix` file directly.

---

## 📄 License

This project uses a synthetic/sample logistics dataset for educational and portfolio purposes.
