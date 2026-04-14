# 🏨 Hotel Bookings Analytics — Power BI Dashboard

> **An end-to-end Power BI analytics project analyzing 87,392 hotel booking records — covering revenue trends, cancellation patterns, guest retention, meal preferences, and room satisfaction — built to deliver actionable insights for hotel operations and management.**

---

## 📌 Table of Contents

- [Project Overview](#-project-overview)
- [Business Problem & Objective](#-business-problem--objective)
- [Dataset Description](#-dataset-description)
- [Data Cleaning & Transformation](#-data-cleaning--transformation)
- [Data Modeling](#-data-modeling)
- [Dashboard Features](#-dashboard-features)
- [Key Insights](#-key-insights)
- [Tools & Technologies](#-tools--technologies)
- [Project Structure](#-project-structure)
- [How to Use](#-how-to-use)
- [Future Improvements](#-future-improvements)
- [Author](#-author)

---

## 🔍 Project Overview

The hotel industry generates rich transactional data with every booking — yet most of it goes unanalyzed, leaving revenue opportunities and operational gaps unaddressed. This project transforms raw hotel booking records into a **structured, interactive Power BI dashboard** that enables hotel managers and revenue analysts to track performance, understand guest behavior, and reduce cancellations.

The report spans **2 dashboard pages** with **24 visuals total**, covering the complete guest journey from initial booking through check-out — built on a single denormalized fact table of **87,392 booking records**.

---

## 💼 Business Problem & Objective

### Problem Statement
Hotel operations teams and revenue managers faced challenges in:
- Identifying **revenue trends** across hotel types and time periods
- Understanding **which guest segments cancel** and why
- Tracking **guest satisfaction** by room type
- Analyzing **repeat guest rates** and loyalty patterns
- Mapping **geographic demand** to optimize marketing spend
- Monitoring **waiting list volumes** and operational bottlenecks

### Project Objective
> Design and deliver a **two-page interactive Power BI dashboard** that consolidates hotel booking data into clear KPIs and visual analytics — enabling data-driven decisions on pricing, capacity, and guest retention.

---

## 📦 Dataset Description

| Attribute | Details |
|-----------|---------|
| **Table Name** | `Hotel Bookings (1)` |
| **Total Records** | 87,392 bookings |
| **Hotel Types** | City Hotel, Resort Hotel |
| **Data Granularity** | One row per booking |
| **Time Dimension** | Week-level granularity (`week day` field) |
| **Geographic Coverage** | International (multi-country via `country` field) |
| **File Format** | `.pbix` (Power BI Desktop) |
| **Created From** | Power BI Cloud (Release 2026.03) |

### Core Fields in the Dataset

| Field | Type | Description |
|-------|------|-------------|
| `Booking ID` | String | Unique booking identifier |
| `hotel` | Categorical | Hotel type — City Hotel or Resort Hotel |
| `adr` | Numeric | Average Daily Rate (revenue per booking) |
| `lead_time` | Integer | Days between booking date and arrival |
| `stays_in_week_nights` | Integer | Number of weeknight stays |
| `stays_in_weekend_nights` | Integer | Number of weekend night stays |
| `meal` | Categorical | Meal plan selected (BB, HB, FB, SC, etc.) |
| `country` | String | Guest country of origin |
| `reserved_room_type` | Categorical | Room type reserved (A–H categories) |
| `reservation_status` | Categorical | Booking outcome (Check-Out, Canceled, No-Show) |
| `previous_cancellations` | Integer | Number of prior cancellations by the guest |
| `is_repeated_guest` | Binary | 1 = Repeat guest, 0 = New guest |
| `booking_changes` | Integer | Number of times booking was modified |
| `days_in_waiting_list` | Integer | Days the booking spent on a waitlist |
| `Total_people` | Integer | Total guests (adults + children + babies) |
| `week day` | Categorical | Day of the week dimension |
| `stastisfaction status on room type` | Categorical | Guest satisfaction rating for room type |

---

## 🧹 Data Cleaning & Transformation

All preprocessing was performed in **Power Query Editor** within Power BI Desktop:

- **Column renaming & standardization** — Ensured consistent naming conventions across all 17 fields; corrected typo-impacted field names (`stastisfaction status on room type`) for report clarity.
- **Derived column: `week day`** — Created a day-of-week dimension column from booking date fields to enable weekly trend analysis across multiple visuals.
- **Derived column: `Total_people`** — Calculated as the sum of adults, children, and babies per booking record to support guest volume aggregations.
- **Derived column: `satisfaction status on room type`** — Engineered a categorical satisfaction flag per room type booking to power the room satisfaction pie chart.
- **Data type enforcement** — `adr`, `lead_time`, `stays_in_week_nights`, `stays_in_weekend_nights`, `booking_changes`, and `days_in_waiting_list` cast to numeric types; `is_repeated_guest` treated as a binary measure field.
- **Null handling** — Rows with missing `country` or `reservation_status` values addressed through Power Query filtering steps to prevent map and cancellation chart distortions.
- **Record deduplication** — `Booking ID` verified as a unique identifier; no duplicate rows retained in the final model.

---

## 🗄️ Data Modeling

### Model Structure

The report is built on a **single-table flat model** — one denormalized fact table (`Hotel Bookings (1)`) containing all dimensions and measures:

```
Hotel Bookings (1)
│
├── Dimensions (used as axes/slicers/categories)
│   ├── hotel              → City Hotel / Resort Hotel
│   ├── week day           → Day of week filter
│   ├── country            → Geographic dimension
│   ├── meal               → Meal plan type
│   ├── reserved_room_type → Room category
│   └── reservation_status → Booking outcome
│
└── Measures / Aggregations (computed in visuals)
    ├── Total Bookings     → COUNT(Booking ID)
    ├── Total Revenue      → SUM(adr)
    ├── Avg Lead Time      → AVERAGE(lead_time)
    ├── Total Week Nights  → SUM(stays_in_week_nights)
    ├── Total Weekend Nights → SUM(stays_in_weekend_nights)
    ├── Cancellations      → SUM(previous_cancellations)
    ├── Repeat Guest Rate  → AVG(is_repeated_guest)
    ├── Booking Changes    → SUM(booking_changes)
    └── Waiting List Days  → SUM(days_in_waiting_list)
```

### Key DAX Measures & Aggregations

| Measure | Aggregation | Visual Used In |
|---------|-------------|---------------|
| **Total Bookings** | `COUNTROWS('Hotel Bookings (1)')` | KPI Card, Pie Chart |
| **Total Revenue (ADR)** | `SUM([adr])` | KPI Card, Line Chart |
| **Avg Lead Time** | `AVERAGE([lead_time])` | KPI Card |
| **Repeat Guest %** | `AVERAGE([is_repeated_guest])` | KPI Card, Gauge |
| **Booking Changes** | `SUM([booking_changes])` | Column Chart |
| **Waiting List Days** | `SUM([days_in_waiting_list])` | Column Chart |
| **Previous Cancellations** | `SUM([previous_cancellations])` | Clustered Column Chart |

> **Note:** All measures are computed as implicit aggregations at the visual level. No separate `Measures` table was defined — aggregations are applied directly via visual configurations.

---

## 📊 Dashboard Features

### 📄 Page 1 — Operations & Revenue Overview

**Purpose:** High-level hotel performance KPIs with revenue, stay patterns, cancellations, and geographic reach.

| Visual | Type | Fields | Business Purpose |
|--------|------|--------|-----------------|
| **Total Bookings** | KPI Card | COUNT(Booking ID) | Top-line volume metric |
| **Total Revenue (ADR)** | KPI Card | SUM(adr) | Overall revenue performance |
| **Avg Lead Time** | KPI Card | AVG(lead_time) | Booking advance window |
| **Repeat Guest Rate** | KPI Card | AVG(is_repeated_guest) | Loyalty signal |
| **Hotel Type Filter** | Slicer | `hotel` | Filter entire page by City/Resort |
| **Week Day Filter** | Slicer | `week day` | Day-level cross-filtering |
| **Total Revenue By Year** | Line Chart | SUM(adr) | Revenue trend over time |
| **Booking Split by Hotel** | Pie Chart | hotel × COUNT(Booking ID) | Hotel-type booking share |
| **Satisfaction on Room Type** | Pie Chart | satisfaction status × Total_people | Guest satisfaction distribution |
| **Geographic Distribution** | Map | `country` | Demand heatmap by guest origin |
| **Stays: Week vs Weekend** | Clustered Column | SUM(week nights), SUM(weekend nights) × hotel | Stay pattern by hotel type |
| **Hotel Cancellation** | Clustered Column | SUM(previous_cancellations) × hotel | Cancellation volume by hotel |

---

### 📄 Page 2 — Guest Behavior & Operational Patterns

**Purpose:** Deeper behavioral analytics on guest loyalty, meal preferences, waiting lists, and booking modifications.

| Visual | Type | Fields | Business Purpose |
|--------|------|--------|-----------------|
| **Country Count** | KPI Card | MIN(country) | Distinct geographic reach |
| **Repeat Customer** | Gauge | SUM(is_repeated_guest) | Loyalty rate progress indicator |
| **Week Day Filter** | Slicer | `week day` | Day-level cross-filtering |
| **Booking Changes By Week** | Column Chart | SUM(booking_changes) × week day | Modification volume by day |
| **Waiting List By Week** | Clustered Column | SUM(days_in_waiting_list) × week day × hotel | Demand pressure by hotel & day |
| **Reserved Room Cancel** | Clustered Column | COUNT(reserved_room_type) × reservation_status × week day | Cancellations by room type & day |
| **Meal Plan Bookings** | Clustered Bar | COUNT(Booking ID) × meal × hotel | Meal preference by hotel type |
| **Repeated Guest By Week** | Pie Chart | SUM(is_repeated_guest) × week day | Loyalty distribution across weekdays |

---

### 🎛️ Interactive Features

- **Cross-page slicers** — `hotel` (Page 1) and `week day` (both pages) enable dynamic filtering across all visuals simultaneously
- **Cross-visual filtering** — Clicking any chart element filters all other visuals on the page via Power BI's built-in drill-filter behavior (`defaultDrillFilterOtherVisuals: true`)
- **Enhanced tooltips** — Hover details enabled on all visuals for deeper on-demand context
- **Responsive layout** — Visuals sized and positioned for clear hierarchy: KPI cards → trend charts → distribution charts → filters

---

## 💡 Key Insights

### 🏨 Revenue & Booking Insights
- **ADR (Average Daily Rate)** is the primary revenue signal — trend analysis across time periods reveals seasonal peaks and troughs that directly inform dynamic pricing strategy.
- **City Hotels vs. Resort Hotels** show distinct booking volumes — understanding which type drives more bookings vs. higher ADR per booking is critical for portfolio-level revenue management.
- **Lead time** (average days booked in advance) signals how far ahead guests typically plan — higher lead times allow more revenue optimization through early-bird pricing.

### ❌ Cancellation Insights
- **Previous cancellations** correlate strongly with future cancellation risk — guests with prior cancellations represent a higher-risk segment that may benefit from non-refundable rate incentives.
- **Reserved room type cancellations** vary significantly by day of week — certain room categories face higher cancellation rates on specific days, informing overbooking policies.
- Weekend vs. weeknight stay patterns differ between hotel types, which affects both operational staffing and cancellation exposure.

### 🔁 Guest Loyalty Insights
- **Repeat guest rate** (tracked via gauge and KPI card) is a direct measure of retention health — benchmarking this against industry norms (~15–20% for hotels) reveals whether loyalty programs are effective.
- **Repeated guest distribution by weekday** highlights which days attract returning guests — enabling targeted loyalty campaign timing.
- Guests who modify bookings more frequently (`booking_changes`) tend to be more engaged — tracking this by week day identifies high-engagement windows.

### 🍽️ Meal Plan Insights
- **Meal plan preference varies by hotel type** — Resort Hotels typically see higher uptake of full-board (FB) or half-board (HB) plans, while City Hotels skew toward Bed & Breakfast (BB) or self-catering (SC).
- Understanding meal plan distribution guides kitchen capacity planning and F&B cost management.

### 🌍 Geographic Insights
- The **geographic map** reveals the top source markets driving bookings — enabling targeted digital marketing spend by country and enabling multi-language service planning.
- Concentration in specific countries may expose the hotel to exchange rate risk and geopolitical demand shifts.

### ⏳ Operational Insights
- **Waiting list volume by hotel type and weekday** identifies peak demand days where supply falls short — informing room inventory expansion or rate floor decisions.
- **Booking changes by weekday** highlight operational complexity — days with high modification rates require more front-desk resource allocation.

---

## 🛠️ Tools & Technologies

| Tool | Role |
|------|------|
| **Power BI Desktop** | Primary development environment for data modeling, DAX, and report authoring |
| **Power Query (M Language)** | Data ingestion, cleaning, type casting, and derived column creation |
| **DAX (Data Analysis Expressions)** | Implicit aggregations and measure logic within visuals |
| **Power BI Map Visual** | Geographic demand visualization using the `country` dimension |
| **Power BI Gauge Visual** | KPI progress tracking for repeat guest rate |
| **Power BI Cloud** | Report created and versioned via Power BI Cloud (Release 2026.03) |
| **Excel / CSV** | Assumed source format for raw hotel booking data |

---

## 📁 Project Structure

```
hotel-analytics-powerbi/
│
├── 📊 hotel_data_power_bi_dashboard.pbix    ← Main Power BI report file
│
├── 📄 README.md                             ← Project documentation (this file)
│
├── 📁 data/
│   └── hotel_bookings_raw.csv              ← Source data (87,392 records)
│
├── 📁 docs/
│   ├── data_dictionary.md                 ← Field-level definitions
│   ├── dax_measures.md                    ← DAX formulas reference
│   └── dashboard_screenshots/
│       ├── page1_overview.png             ← Page 1 screenshot
│       └── page2_behavior.png             ← Page 2 screenshot
│
└── 📁 exports/
    └── hotel_summary_report.pdf           ← Exported PDF report
```

---

## 🚀 How to Use

### Prerequisites
- **Power BI Desktop** (free) — [Download here](https://powerbi.microsoft.com/desktop/)
- Minimum version: **2.150.405.0** (settings version embedded in file)
- No Power BI Pro license required to open `.pbix` files locally

### Step-by-Step

**1. Download and open the report**
```
Download hotel_data_power_bi_dashboard.pbix
Open Power BI Desktop → File → Open Report → Select the .pbix file
```

**2. Navigate between pages**
```
Use the page tabs at the bottom of Power BI Desktop:
  • Page 1 → Operations & Revenue Overview
  • Page 2 → Guest Behavior & Operational Patterns
```

**3. Apply slicers to filter the dashboard**
```
Page 1 slicers:
  • Hotel Type slicer → Select "City Hotel", "Resort Hotel", or both
  • Week Day slicer   → Filter all visuals to specific days

Page 2 slicers:
  • Week Day slicer   → Day-of-week behavioral filtering
```

**4. Use cross-visual filtering**
```
Click any bar, slice, or data point in any chart
→ All other visuals on the page will automatically filter to that selection
→ Click again to deselect and reset
```

**5. Refresh data (if connecting to updated source)**
```
Home → Refresh
If prompted, update the file path to your local copy of hotel_bookings_raw.csv
```

**6. Export the report**
```
File → Export → Export to PDF  (for static sharing)
File → Publish → Power BI Service  (for online sharing with a Pro license)
```

---

## 🔮 Future Improvements

| Priority | Enhancement |
|----------|------------|
| 🔴 **High** | Add a dedicated **Revenue Analytics page** with ADR breakdown by room type, hotel, and season for deeper pricing analysis |
| 🔴 **High** | Build a **Cancellation Risk Score** using DAX — flagging high-risk bookings based on lead time, previous cancellations, and room type |
| 🟡 **Medium** | Integrate **date table** (calendar dimension) to enable proper Year/Quarter/Month drill-down on the revenue line chart |
| 🟡 **Medium** | Add **Occupancy Rate KPI** — calculated as booked nights ÷ total available nights per period |
| 🟡 **Medium** | Implement **Row-Level Security (RLS)** for multi-property deployments where managers only see their hotel's data |
| 🟡 **Medium** | Fix typo in field name `stastisfaction status on room type` → `satisfaction_status_room_type` at the Power Query layer |
| 🟢 **Low** | Publish to **Power BI Service** and configure scheduled data refresh for live operational monitoring |
| 🟢 **Low** | Add **Bookmarks & Buttons** for guided navigation between pages for non-technical stakeholders |
| 🟢 **Low** | Connect to a **SQL Server or Azure database** as source to replace static CSV with a live data pipeline |

---

## 👤 Author

**[Aman Mulani]**  
*Data Analyst | Power BI Developer*

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?style=flat-square&logo=linkedin)](https://linkedin.com/in/aman-mulani111)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black?style=flat-square&logo=github)](https://github.com/theamanmulani)


---

> 💡 *If this project was useful or inspired your own work, consider giving it a ⭐ — it helps others discover it.*

---

*Last updated: April 2026 · Records: 87,392 · Pages: 2 · Visuals: 24 · Tool: Power BI Desktop*
