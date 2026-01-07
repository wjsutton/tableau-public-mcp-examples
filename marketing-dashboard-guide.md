# Marketing Campaign Performance Dashboard Build Guide

## Phase 1: Data Preparation

### Required Fields
Your dataset needs these columns (rename as needed):

| Field | Type | Example |
|-------|------|---------|
| Campaign_ID | String | "CAMP_001" |
| Activity_Date | Date | 2023-06-15 |
| Channel | String | Facebook, Google Ads, Instagram |
| Impressions | Integer | 50000 |
| Clicks | Integer | 1200 |
| Cost | Decimal | 450.00 |
| Conversions | Integer | 85 |
| Revenue | Decimal | 2500.00 |

### Calculated fields to add in Tableau (if missing from source)
```
// Conversion Rate
[Conversions] / [Clicks]

// CPC (Cost per Click)
[Cost] / [Clicks]

// Cost per Conversion
[Cost] / [Conversions]

// ROI
([Revenue] - [Cost]) / [Cost]

// Campaign Duration (if you have start/end dates)
DATEDIFF('day', [Start_Date], [End_Date])
```

---

## Phase 2: Parameters

Create these 4 parameters:

### 1. Year Parameter
- Data type: Integer
- Allowable values: List → 2022, 2023, 2024
- Current value: Most recent year

### 2. Week Parameter  
- Data type: Integer
- Allowable values: Range → 1 to 53
- Current value: Current week number

### 3. Metrics Selector
- Data type: String
- Allowable values: List
  - Impressions
  - Clicks
  - Cost per Conversion
  - Conversion Rate
  - Revenue
  - ROI

### 4. Channel Parameter
- Data type: String
- Allowable values: List of your channels

---

## Phase 3: Core Calculated Fields

### Current Week Metrics
```
// CW Impressions
SUM(
  IF DATEPART('week', [Activity_Date]) = [Week Parameter]
  AND DATEPART('year', [Activity_Date]) = [Year Parameter]
  THEN [Impressions] 
  END
)
```
Duplicate for: Clicks, Cost, Conversions, Revenue

### Previous Week Metrics (with year boundary handling)
```
// PW Impressions
SUM(
  IF (
    // Handle Week 1 → look at Week 53 of previous year
    ([Week Parameter] = 1 
     AND DATEPART('week', [Activity_Date]) = 53 
     AND DATEPART('year', [Activity_Date]) = [Year Parameter] - 1)
    OR
    // Normal case: previous week same year
    (DATEPART('week', [Activity_Date]) = [Week Parameter] - 1 
     AND DATEPART('year', [Activity_Date]) = [Year Parameter] 
     AND [Week Parameter] > 1)
  )
  THEN [Impressions] 
  END
)
```

### Ratio Metrics for Current Week
```
// CW CPC
SUM(IF [Current Week Filter] THEN [Cost] END)
/
SUM(IF [Current Week Filter] THEN [Conversions] END)

// CW Conversion Rate
AVG(IF [Current Week Filter] THEN [Conversion Rate] END)
```

### Week-over-Week Change
```
// WoW Impressions %
([CW Impressions] / [PW Impressions]) - 1
```

### Color Indicator (Boolean)
```
// WoW Positive?
[WoW Impressions] > 0
```

---

## Phase 4: Dynamic Metric Selector

### Selected Metric Value
```
CASE [Metrics Selector]
  WHEN 'Impressions' THEN SUM([Impressions])
  WHEN 'Clicks' THEN SUM([Clicks])
  WHEN 'Cost per Conversion' THEN SUM([Cost])/SUM([Conversions])
  WHEN 'Conversion Rate' THEN AVG([Conversion Rate])
  WHEN 'Revenue' THEN SUM([Revenue])
  WHEN 'ROI' THEN SUM([ROI])
END
```

### Selected Metric Label (formatted)
```
CASE [Metrics Selector]
  WHEN 'Impressions' THEN STR(SUM([Impressions]))
  WHEN 'Clicks' THEN STR(SUM([Clicks]))
  WHEN 'Cost per Conversion' THEN '$' + STR(ROUND(SUM([Cost])/SUM([Conversions]), 2))
  WHEN 'Conversion Rate' THEN STR(ROUND(AVG([Conversion Rate])*100, 1)) + '%'
  WHEN 'Revenue' THEN '$' + STR(ROUND(SUM([Revenue]), 0))
  WHEN 'ROI' THEN STR(ROUND(SUM([ROI])*100, 1)) + '%'
END
```

---

## Phase 5: LOD for Campaign-Level Metrics

```
// Campaign Duration (fixed at campaign level)
{FIXED [Campaign_ID]: MAX([Campaign_Duration])}

// Campaign Total Spend
{FIXED [Campaign_ID]: SUM([Cost])}

// Campaign Lifetime Value
{FIXED [Campaign_ID]: SUM([Revenue])}
```

---

## Phase 6: Build the Worksheets

### KPI Cards (6 total)
1. Create a sheet for each metric
2. Use Gantt Bar mark type (for precise positioning)
3. Show: CW value, PW value, WoW% change
4. Color WoW% by the boolean (green positive, red negative)
5. Use ▲ ▼ symbols in labels

### Channel Performance Bar Chart
- Rows: Channel
- Columns: [Selected Metric Value]
- Color: Channel
- Sort: Descending by metric

### Trend Line
- Columns: WEEK([Activity_Date])
- Rows: [Selected Metric Value]
- Filter: [Year Parameter]

### Bump Chart (Channel Ranking)
- Columns: WEEK([Activity_Date])
- Rows: RANK([Selected Metric Value])
- Color/Path: Channel
- Filter: `LAST() < 6` to show last 6 weeks

### Campaign Table
- Rows: Campaign_ID
- Columns: Multiple metrics
- Sort by: [Selected Metric Value] descending

---

## Phase 7: Dashboard Assembly

### Layout (1300 x 1200 pixels)
```
┌─────────────────────────────────────────────┐
│  Header: Title + Year/Week Selectors        │
├──────────┬──────────┬──────────┬────────────┤
│ KPI 1    │ KPI 2    │ KPI 3    │ Filters    │
│ Impress. │ Clicks   │ CPC      │            │
├──────────┼──────────┼──────────┤            │
│ KPI 4    │ KPI 5    │ KPI 6    │            │
│ Conv %   │ Revenue  │ ROI      │            │
├──────────┴──────────┴──────────┼────────────┤
│ Trend Line Chart               │ Channel    │
│                                │ Bar Chart  │
├────────────────────────────────┴────────────┤
│ Campaign Detail Table                       │
└─────────────────────────────────────────────┘
```

### Actions
1. **Filter Action**: Click Channel bar → filters all sheets
2. **Highlight Action**: Hover on trend line → highlights channel
3. **Parameter Action**: Click metric selector → updates [Metrics Selector]

---

## Phase 8: Polish

### Formatting
- Remove gridlines, row/column dividers
- Set background colors (dark theme: #1a1a2e)
- Use consistent font family (e.g., Tableau Regular)
- Add icons for visual hierarchy

### Tooltips
- Customize with context: "Week X vs Week X-1"
- Include all related metrics
- Add visual indicators

### Testing
- [ ] Week 1 boundary works (pulls Week 53 of previous year)
- [ ] All metrics switch correctly via parameter
- [ ] Filters cascade properly
- [ ] Mobile/tablet responsiveness

---

## Gaming Industry Adaptation

For your Jagex application, adapt this to player acquisition:

| Marketing Metric | Gaming Equivalent |
|------------------|-------------------|
| Impressions | Ad Impressions / Store Page Views |
| Clicks | Click-throughs / Downloads Started |
| Conversions | Installs / Account Creations |
| Revenue | First Purchase Value / LTV |
| Cost | User Acquisition Cost |
| ROI | ROAS (Return on Ad Spend) |

Add gaming-specific metrics:
- Day 1 / Day 7 / Day 30 Retention
- Tutorial Completion Rate
- First Session Duration
- Cohort Analysis by Acquisition Channel
