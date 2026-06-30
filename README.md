# Nykaa Marketing Campaign Performance Analysis
### FY 2024-25 | Excel Analytics Portfolio Project | Currency: USD ($1 = ₹84)

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [The Brief](#2-the-brief)
3. [Dataset Description](#3-dataset-description)
4. [Data Cleaning — Challenges & Decisions](#4-data-cleaning--challenges--decisions)
5. [Workbook Structure](#5-workbook-structure)
6. [Key Findings](#6-key-findings)
7. [Business Recommendations for Q3](#7-business-recommendations-for-q3)
8. [Tools & Techniques Used](#8-tools--techniques-used)
9. [Known Limitations](#9-known-limitations)
10. [How to Use This Workbook](#10-how-to-use-this-workbook)

---

## 1. Project Overview

## Download the Workbook
The full Excel workbook is available here:
[Download EXCEL_PROJECT2.xlsx](https://docs.google.com/spreadsheets/d/14amwyFYG85vxCMeLIgDDJQouPI83PaHv/edit?usp=sharing&ouid=109714892173054151624&rtpof=true&sd=true)
This real-life simulation project is a full end-to-end marketing analytics workbook built for a fictional Nykaa's Marketing Analytics team. It covers **FY 2024-25 (July 2024 – June 2025)** and analyses the performance of **44,026 digital marketing campaigns** across five channels, five regions, six campaign objectives, and four audience segments.

The deliverable was commissioned by **Divya Nair, Head of Marketing Analytics**(fictional), for presentation to the **VP of Marketing and CFO**. Budget allocation decisions for Q3 FY 2025-26 depend directly on the findings in this workbook.

| Metric | Value |
|---|---|
| Total Revenue Generated | $270.7M |
| Total Budget Allocated | $133.5M |
| Total Budget Spent | $112.4M |
| Unspent Budget | $21.1M |
| Overall ROAS | 3.61x |
| Total Conversions | 44.3M |
| Campaigns Analysed | 44,026 |
| Analysis Period | Jul 2024 – Jun 2025 |
| Currency | USD ($1 = ₹84) |

---

## 2. The Brief

> *"We've been running campaigns across Social Media, Paid Ads, Influencer, Email and SEO for the past year. Leadership wants to know what's working and what isn't. We're overspending somewhere and underperforming somewhere else. I need you to find it, quantify it, and tell me what we should do about it. The CFO wants numbers. The VP wants a story. Give me both."*
>
> — Divya Nair, Head of Marketing Analytics

**Business questions this workbook answers:**

1. Which channels are driving ROI and which are draining budget?
2. Where exactly are we missing the targets set by the strategy team — and what does each miss cost us in dollars?
3. Is $21.1M in unspent budget a planning problem or a delivery problem?
4. Which regions should receive more or less investment in Q3?
5. Are we improving month-over-month, and what seasonal patterns should Q3 planning account for?
6. Does AI-generated content actually deliver better campaign outcomes?
7. Which audience segments and campaign objectives generate the most efficient returns?
8. Where is the funnel leaking — and is it a channel problem or a product problem?

---

## 3. Dataset Description

The source file contained four sheets:

| Sheet | Description | Rows | Columns |
|---|---|---|---|
| `RAW_Data` | Raw campaign records — messy, unclean, mixed formats | 56,355 | 26 |
| `Channel_Info` | Reference table with metadata for 10 marketing channels | 10 | 8 |
| `KPI_Targets` | Strategy team planning targets per channel (ROI, CTR, CVR, ROAS, CPA) | 5 | 11 |
| `AI_Scores` | ML model output — predicted conversion probability, AI targeting score, automation readiness | ~56K | 10 |

**Key columns in RAW_Data (post-cleaning):**

- `Campaign_ID` — unique campaign identifier (NY-CMP-XXXXX)
- `Campaign_Type_Cleaned` — standardised to: Social Media, Paid Ads, Influencer, Email, SEO
- `Region_Cluster_Cleaned` — standardised to: North India, South India, East India, West India, Pan India
- `Date` — campaign date (parsed from text to proper date type)
- `Revenue_USD` — revenue generated, converted from INR at ₹84/$
- `Budget_Allocated_USD` / `Budget_Spent_USD` — financial columns in USD
- `CTR_Pct`, `CVR_Pct`, `ROAS`, `CPA_USD` — calculated KPI columns added during cleaning
- `AI_Content_Flag` — standardised binary (Yes / No)
- `AI TIER` — AI targeting tier: Low (≤15), Mid (16–25), High (≤35), Elite (>35)

---

## 4. Data Cleaning — Challenges & Decisions

### What was found in the raw data

The RAW_Data sheet arrived with 56,355 rows and significant quality issues across nearly every column. The cleaning process was one of the most challenging parts of this project.

#### Issues identified and resolved

| Issue | Column | Rows Affected | Action Taken |
|---|---|---|---|
| Fully duplicate rows | All | 24 | Removed |
| Campaign Type — 30+ spelling variants | `Campaign_Type` | ~6,600 | Keyword mapping → 5 canonical values |
| Region — 31+ label variants | `Region_Cluster` | ~8,500 | Keyword mapping → 5 canonical regions |
| AI Flag — 14 mixed types (1/0/TRUE/y/Yes) | `AI_Content_Flag` | ~13,400 | Unified to Yes / No binary |
| Language typos (Englsh, Taml, Hndi) | `Language` | ~2,400 | Dictionary correction |
| Date stored as text (dd-mm-yyyy string) | `Date` | 56,355 | Parsed to proper date type |
| Clicks / Acquisition_Cost as text objects | Multiple | ~9 each | pd.to_numeric coercion |
| ROI outliers (values of 999 and -99) | `ROI` | ~14 | Flagged in ROI_Flag column; excluded from averages |
| Negative Revenue values | `Revenue` | ~11 | Flagged in Revenue_Flag; excluded from totals |
| Invalid campaign types (Display, PR, Offline, Unknown) | `Campaign_Type` | ~800 | Excluded from main analysis |
| Missing conversion values | `Conversions` | ~1,120 | Retained; excluded from CVR/CPA |
| Missing budget fields | `Budget_Allocated/Spent` | ~2,000 | Excluded from budget efficiency calculations |

### ⚠ The data loss issue — important note

**We started with 56,355 rows and ended with 44,026 analysed campaigns — a loss of approximately 12,329 rows.**

This reduction came from multiple sources:

1. **Duplicate removal:** 24 rows
2. **Invalid campaign types excluded** (Display, PR, Offline, Unknown): ~800 rows
3. **Rows with missing critical values** (no revenue, no conversions, no budget) that could not be reliably included in any KPI calculation: the remainder
4. **AI_Scores join** — not all Campaign_IDs in RAW_Data had a matching record in AI_Scores, so the joined sheet (`RAW_data_AI_score`) has fewer rows than the full cleaned dataset

**What this means for the analysis:** The 44,026 campaigns that form the analysis base are all rows with complete, valid data across the key financial and performance fields. Excluding incomplete records is the correct approach — including rows with null revenue or null conversions in channel averages would produce misleading KPIs. However, the volume of data lost (roughly 22% of the raw dataset) points to an upstream data pipeline problem that should be investigated before Q3. If the source system is not capturing revenue and conversion data for roughly 1 in 5 campaigns, we are likely underreporting performance across the board.

**Recommendation:** Flag this to the data engineering team. A 22% data loss rate at the point of analysis is a data governance issue, not just a cleaning issue.

### Columns added during cleaning

| New Column | Description |
|---|---|
| `Campaign_Type_Cleaned` | Standardised campaign type |
| `Region_Cluster_Cleaned` | Standardised region |
| `Month Name` | Calendar month for pivot grouping |
| `Quarter` | Q1–Q4 for seasonal analysis |
| `m/y` | Month-Year label (e.g. Jan 2025) |
| `Revenue_USD` | Revenue converted from INR at ₹84 |
| `Budget_Allocated_USD` | Allocated budget in USD |
| `Budget_Spent_USD` | Actual spend in USD |
| `Budget_Gap_USD` | Unspent budget (Allocated − Spent) |
| `Acquisition_Cost_USD` | Cost per acquisition in USD |
| `CTR_Pct` | Click-through rate (Clicks / Impressions × 100) |
| `CVR_Pct` | Conversion rate (Conversions / Clicks × 100) |
| `ROAS` | Return on Ad Spend (Revenue / Budget Spent) |
| `CPA_USD` | Cost per acquisition in USD |
| `AI TIER` | AI targeting tier bucket |

---

## 5. Workbook Structure

The final project file (`EXCEL_PROJECT2.xlsx`) contains 11 sheets:

| # | Sheet | What It Answers |
|---|---|---|
| 1 | `RAW_Data` | Cleaned source dataset — all analysis runs from here |
| 2 | `RAW_data_AI_score` | RAW_Data joined with AI_Scores on Campaign_ID |
| 3 | `KPI_Targets` | Strategy team targets (with USD CPA added) |
| 4 | `Channel_Info` | Channel reference metadata |
| 5 | `AI_Scores` | ML model scores (preserved) |
| 6 | `Channel-KPI` | Channel performance pivot + KPI scorecard vs targets |
| 7 | `Budget Efficiency` | Budget allocation vs spend vs return analysis |
| 8 | `Regional Performance` | Geographic performance breakdown |
| 9 | `Monthly Trends` | Time-series revenue, ROAS, and MoM analysis |
| 10 | `AI-Performance` | AI vs non-AI campaign comparison |
| 11 | `Audience_Objective` | Segment, objective, and device performance |
| 12 | `Funnel-Manager` | Funnel drop-off analysis + campaign manager scorecard |
| 13 | `DASHBOARD` | Executive one-page summary with KPI cards + recommendations |
| 14 | `WORKBOOK` | Supporting pivot data and chart references |

---

## 6. Key Findings

### Channel Performance

| Channel | Revenue | ROAS | Avg ROI | KPI Grade | Status |
|---|---|---|---|---|---|
| Influencer | $56.1M | 3.73x | 2.94 | **A ⭐** | Only channel hitting ROAS target |
| Social Media | $55.7M | 3.71x | 3.14 | C+ Average | Hitting CTR & CVR; missing ROI & ROAS |
| Email | $53.5M | 3.53x | 2.77 | **D ❌** | Missing 3 of 5 KPI targets |
| Paid Ads | $53.0M | 3.52x | 2.86 | C+ Average | 3 hits, 2 misses |
| SEO | $52.4M | 3.55x | 3.05 | **D ❌** | Missing 3 of 5 KPI targets |

**The portfolio's biggest KPI miss:** Email CTR is 8.47% against a 12.0% target — a 29% shortfall. This is an operational problem (subject lines, send times, list segmentation), not a budget problem.

**The portfolio's most expensive miss:** SEO CPA is $5.39 against a target of $1.08 — nearly 5× over target. Every conversion through SEO costs $4.31 more than the strategy team planned.

### Budget Efficiency

- **$21.1M in budget went unspent** — 15.8% of the total $133.5M allocation
- All five channels show near-identical utilisation rates (83.6% – 85.1%), which confirms this is **systemic over-planning**, not a channel-specific delivery failure
- At a portfolio ROAS of 3.61x, the $21.1M unspent budget represents approximately **$76M in foregone revenue**
- This is the single most actionable number in the entire workbook — it requires CFO attention and contract renegotiation before Q3

### Regional Performance

| Region | Revenue | ROAS | Action |
|---|---|---|---|
| North India | $55.7M | 3.67x | **INCREASE +15%** — highest ROAS; festive season priority |
| South India | $54.7M | 3.64x | MAINTAIN |
| Pan India | $54.5M | 3.58x | MAINTAIN — highest conversions |
| East India | $53.7M | 3.61x | MAINTAIN |
| West India | $52.2M | 3.54x | REVIEW — lowest revenue |

Social Media dominates North India. Paid Ads leads West India. Channel strategy should be **region-specific**, not uniform across all five geographies.

### Monthly Trends

| Month | Revenue | ROAS | Flag |
|---|---|---|---|
| August 2024 | $25.2M | 3.70x | PEAK — Rakhi festive campaigns |
| October 2024 | $25.0M | 3.51x | RECOVERY — Navratri / Dussehra |
| May 2025 | $24.6M | 3.71x | PEAK — Summer beauty season |
| February 2025 | $21.6M | 3.63x | HIGH ROAS — best efficiency on lowest spend |
| September 2024 | $22.3M | 3.53x | DIP — post-Onam lull |
| January 2025 | $23.1M | 3.50x | WEAK — New Year lag |

**Critical planning insight:** August and October are peak demand months. Festive season campaigns must be fully planned and budget-committed by July — any delay costs revenue that cannot be recovered within the quarter.

### AI Content Performance

| Metric | Non-AI | AI | Uplift |
|---|---|---|---|
| Avg ROI | 2.84 | 3.08 | **+8.75%** |
| Avg CVR % | 21.49% | 21.62% | +0.13pp |
| Avg Conversions/Campaign | 1,012.6 | 1,014.8 | +2.2 |
| Avg ROAS | 3.63x | 3.60x | −0.7% (neutral) |
| Avg CPA ($) | $5.49 | $5.52 | Slightly worse |

AI content delivers a consistent **+8.75% improvement in average ROI**. The biggest uplift is in Influencer campaigns (+20.8% ROI) and Paid Ads (+14.9% ROI). AI content adoption currently sits at approximately 50% of all campaigns — there is meaningful upside in scaling it.

### Funnel Analysis

All five channels show near-identical top-funnel performance (CTR: 8.44% – 8.57%), confirming that ad creative and targeting quality are consistent across the portfolio.

The problem is **mid-funnel**:
- 39% of clicks become leads — healthy
- Only 21.6% of leads convert to purchase — this means **approximately 50% of leads are lost before checkout**

This is not a channel problem. It is a **checkout experience, pricing, or offer problem** that affects the entire portfolio equally. Increasing any channel budget without fixing the post-click journey will not improve conversion rates.

### Audience Segments

| Segment | Revenue | ROI | CPA | Insight |
|---|---|---|---|---|
| Youth | $55.4M | 2.78 | $5.58 | Highest revenue → top segment |
| Premium Shoppers | $54.4M | 2.92 | $5.44 | Lowest CPA → most efficient to acquire |
| College Students | $54.2M | 2.92 | $5.48 | Highest CVR → strong converters |
| Working Women | $54.4M | 2.72 | $5.54 | Lowest ROI → review creative relevance |
| Tier 2 City Customers | $52.3M | **3.41** | $5.55 | **Highest ROI → underserved; highest Q3 growth potential** |

### Campaign Objectives

| Objective | Revenue | ROI | Verdict |
|---|---|---|---|
| App Install | $46.6M | 2.97 | Highest revenue → scale budget |
| Retargeting | $45.9M | **3.25** | Highest ROI → most efficient objective |
| Lead Generation | $45.0M | 2.68 | Lowest ROI — improve lead quality |
| Engagement | $44.6M | 2.98 | Most campaigns, lowest ROAS — trim 10% |
| Brand Awareness | $44.6M | 2.85 | Hard to measure — tie to lift testing |
| Conversion | $44.1M | 2.97 | Lowest revenue — fix landing pages first |

---

## 7. Business Recommendations for Q3

These recommendations are directly supported by the analysis and sized where possible.

---

### Recommendation 1 — Reallocate budget from SEO and Email to Influencer

**What:** Redirect 20% of SEO budget (~$4.4M) and freeze Email budget increases (~$4.0M). Deploy the freed capital to Influencer campaigns.

**Why:** Influencer is the only channel meeting its ROAS target (3.73x vs 3.0x minimum). SEO has the worst CPA in the portfolio at $5.39 — nearly 5× its $1.08 target. Email is failing three of five KPI targets with no budget fix available until CTR is resolved.

**Expected impact:** At Influencer's current ROAS of 3.73x, deploying an additional $8.4M generates an estimated **+$31.3M in incremental revenue** in Q3.

---

### Recommendation 2 — Fix Email CTR before any budget increase

**What:** Commission an A/B testing programme on Email subject lines, send times, and personalisation by audience segment. Target: close the CTR gap from 8.47% to at least 10.5% within Q3 month 2.

**Why:** Email CTR is 29% below its 12.0% target. This is an operational fix — it requires creative and strategic changes, not more money. The current Email CPA is $5.54 against a $1.44 target, meaning every Email conversion costs $4.10 more than planned. No budget increase is justified until this is addressed.

**Quick win:** AI subject line testing costs near-zero in marginal spend and has already shown an 11.2% ROI uplift in Influencer campaigns using the same approach.

---

### Recommendation 3 — Prioritise North India in Q3 festive season planning

**What:** Increase North India budget allocation by 15% ahead of the October–November festive window (Navratri, Dussehra, Diwali).

**Why:** North India delivers the highest ROAS in the portfolio (3.67x) and the highest revenue ($55.7M). October is already confirmed as a recovery peak month — concentrated investment before the festive season is the highest-confidence revenue lever available in Q3. Social Media is the dominant channel in North India and should receive the bulk of the increased allocation.

**Expected impact:** A 15% budget increase in North India (~$3.3M additional) generates an estimated **+$12.1M in Q3 revenue** at current ROAS.

---

### Recommendation 4 — Renegotiate Q3 media contracts for minimum 90% delivery

**What:** Present the $21.1M unspent budget finding to the CFO and instruct the media team to renegotiate all Q3 channel contracts with minimum delivery clauses of 90%+.

**Why:** $21.1M in unspent budget at 15.8% of total allocation is not a performance problem — it is a contract problem. Every channel is delivering at 83.6%–85.1% utilisation. This near-perfect uniformity across five different channels confirms the under-delivery is structural, not coincidental. At the portfolio ROAS of 3.61x, this unspent budget represents approximately $76M in foregone revenue — the single largest opportunity cost in the entire portfolio.

---

### Recommendation 5 — Scale AI content adoption from ~50% to 70%+ of campaigns

**What:** Set a Q3 target of 70% AI content adoption across all campaigns. Prioritise Influencer briefing templates and Email subject line AI tools where the ROI uplift is highest.

**Why:** AI campaigns deliver +8.75% better average ROI and +0.13 percentage points better CVR. With approximately 50% of campaigns currently using AI content, scaling to 70% adds meaningful efficiency without increasing total spend. Influencer AI uplift is the strongest in the portfolio (+20.8% ROI) and should be the primary focus.

---

### Recommendation 6 — Audit the post-click journey before increasing any channel budget

**What:** Commission a UX and checkout audit covering landing page performance, cart abandonment rates, and payment friction — before approving any channel budget increases for Q3.

**Why:** The funnel analysis shows that all five channels convert clicks to leads at identical rates (~39%), but approximately 50% of leads never complete a purchase. This is a product or checkout problem, not a marketing problem. Increasing any channel budget without fixing the post-click journey will deliver more leads to a leaky funnel — not more revenue.

---

### Recommendation 7 — Invest in Tier 2 City audience targeting

**What:** Build dedicated Q3 campaigns targeting Tier 2 City Customers, with budget drawn from working women campaigns where ROI is underperforming.

**Why:** Tier 2 City Customers have the highest ROI in the audience portfolio (3.41) but only the fourth-highest revenue ($52.3M) — indicating this audience is underserved relative to its efficiency. Working Women, by contrast, deliver the lowest ROI (2.72) and the highest budget allocation. The rebalancing opportunity is clear.

---

## 8. Tools & Techniques Used

| Tool / Feature | Where Used |
|---|---|
| **Power Query** | Data cleaning, type conversion, date parsing, column standardisation, USD conversion columns |
| **PivotTables** | Channel performance, regional breakdown, monthly trends, audience analysis, funnel analysis |
| **XLOOKUP** | KPI target comparisons — pulling strategy targets from KPI_Targets sheet |
| **GETPIVOTDATA** | Referencing pivot values in formula columns and KPI scorecard |
| **SUMIFS** | Verification cross-checks against pivot table totals |
| **Conditional Formatting** | Status column (HIT / MISS), gap direction coding, data bars on gap % column |
| **Named Ranges** | Exchange rate (FX_RATE), scenario inputs for what-if analysis |
| **Charts** | ROAS vs target (bar + line), revenue vs spend (clustered column), monthly trends (line), funnel (funnel chart), budget utilisation (bar) |
| **Slicers** | Region, Channel, Month — connected to multiple pivots simultaneously |
| **Python / pandas** | Automated data cleaning pipeline run before Power Query (deduplication, type standardisation, keyword mapping) |

---

## 9. Known Limitations

**1. Data loss during cleaning (~12,329 rows excluded)**
Approximately 22% of raw rows were excluded from the main analysis due to missing values in key financial fields (Revenue, Budget, Conversions). This means the analysis is based on the 78% of campaigns with complete records. Performance figures may understate actual totals. The root cause is a data pipeline issue and should be addressed upstream before Q3 reporting.

**2. June 2025 is a partial month**
The dataset cuts off on 24 June 2025. June figures are excluded from all month-on-month trend analysis and from any calculations that average across months. The Grand Total figures include June as a partial contribution to the annual total.

**3. Exchange rate is fixed at ₹84 = $1**
All INR-to-USD conversions use the FY 2024-25 average rate of ₹84. Actual daily rates fluctuated between approximately ₹82 and ₹87 during the period. Any restatement at a different rate requires updating the `FX_RATE` named cell in the workbook, which cascades through all USD columns automatically.

**4. AI Scores join is incomplete**
The `RAW_data_AI_score` sheet is a join between RAW_Data and AI_Scores on `Campaign_ID`. Not all Campaign_IDs in the cleaned dataset had a matching AI_Scores record, resulting in a smaller row count in the joined sheet. The AI performance analysis should be interpreted as directional rather than definitive until the join coverage is confirmed.

**5. Campaign Manager analysis has unassigned campaigns**
A portion of campaigns in the data carry `Campaign_Manager = Unassigned`. These are excluded from the manager performance scorecard but are included in all other analyses. The Unassigned bucket represents campaigns with no accountability assigned — a separate operational recommendation to address this is warranted.

**6. ROI values of 999 and -99 were excluded**
Fourteen rows with extreme ROI outliers (values of 999 or -99) were flagged and excluded from all ROI averages. These appear to be data entry errors or system default fill values. They do not affect revenue, ROAS, CTR, or CVR calculations.

---

## 10. How to Use This Workbook

### Starting point
Open `EXCEL_PROJECT2.xlsx`. The workbook opens on the `DASHBOARD` sheet by default. This is the executive view — read it in 90 seconds to get the full picture.

### For a deeper dive
Navigate to individual analysis sheets using the colour-coded tabs:
- **Red tab** → KPI vs Targets (Channel-KPI)
- **Green tab** → Budget Efficiency
- **Blue tab** → Regional Performance
- **Amber tab** → Monthly Trends
- **Purple tab** → AI Performance
- **Teal tab** → Audience & Objective
- **Grey tab** → Funnel & Manager

### Using the slicers
Three slicers on the Dashboard control the entire workbook view:
- **Channel** — filter all charts and tables to a single channel
- **Region** — isolate performance for a specific geography
- **Month** — restrict analysis to a specific time period

Each slicer is connected to all pivot tables on its sheet simultaneously. One click filters everything.

### Refreshing the data
If the source data is updated:
1. Go to the `RAW_Data` sheet
2. **Data → Refresh All**
3. All pivot tables, calculated columns, and charts update automatically
4. The exchange rate assumption lives in the named cell `FX_RATE` — update it there if the rate changes

### Extending the analysis
To add a new channel, region, or time period:
1. Add the new data to `RAW_Data` following the existing column structure
2. Ensure the Campaign_Type and Region_Cluster values match the five canonical values already in use
3. Refresh All — the analysis propagates automatically

   ## Download the Workbook
The full Excel workbook is available here:
[Download EXCEL_PROJECT2.xlsx](https://docs.google.com/spreadsheets/d/14amwyFYG85vxCMeLIgDDJQouPI83PaHv/edit?usp=sharing&ouid=109714892173054151624&rtpof=true&sd=true)

