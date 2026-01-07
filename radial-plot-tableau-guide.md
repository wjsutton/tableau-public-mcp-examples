# Building Radial Plots in Tableau

A comprehensive guide based on analysis of real Tableau Public workbooks.

---

## Overview

Radial plots (also called circular charts or polar charts) display data arranged around a central point. They're excellent for showing cyclical patterns like seasonal trends, hourly distributions, or year-over-year comparisons.

---

## 1. Data Structure Requirements

### Your Current Data
Your `events.csv` contains daily events from 2022-2024 — perfect for radial visualization showing patterns over time.

### Data Densification

Radial plots require **data densification** to create smooth curves. You need to join your data with a "scaffold" table.

**Simple Scaffold (for spikes/bars):**

| Path |
|------|
| 0 |
| 1 |

**Smooth Curve Scaffold (for continuous lines):**

| Path |
|------|
| 0 |
| 1 |
| 2 |
| ... |
| 100 |

Create this in Excel or as a separate data source, then use a cross-join (join on a calculated field = 1 on both sides).

---

## 2. Derived Fields from Your Date

Create these calculated fields from your date column:

```
// Year
Year: YEAR([date])

// Day of Year (1-365/366)
Day of Year: DATEPART('dayofyear', [date])

// Month Number
Month: MONTH([date])

// Week of Year
Week: DATEPART('week', [date])

// Day of Week
Day of Week: DATEPART('weekday', [date])
```

---

## 3. Core Calculated Fields

### Constants

```
// Inner circle radius (adjust to taste)
InnerC: 
50

// Outer circle radius (adjust to taste)
OuterC: 
100

// Angle per segment - full circle divided by number of data points
// For daily data (365 days):
PartSizeRad: 
2 * PI() / WINDOW_MAX(MAX([Day of Year]))

// For weekly data (52 weeks):
PartSizeRad: 
2 * PI() / 52

// For monthly data (12 months):
PartSizeRad: 
2 * PI() / 12
```

### Position Calculations

```
// Rank - determines position around the circle
Rank: 
RANK_UNIQUE(MAX([Day of Year]), 'asc')

// Normalize value to 0-1 range
ValuePart: 
ZN(SUM([events]) / WINDOW_MAX(MAX([events])))

// Angle for each data point (PI/2 offset starts at top)
Angle: 
[PartSizeRad] * [Rank] + PI()/2
```

### X and Y Coordinates

```
// X coordinate
X: 
COS([Angle]) * ([InnerC] + ([OuterC] - [InnerC]) * [ValuePart])

// Y coordinate
Y: 
SIN([Angle]) * ([InnerC] + ([OuterC] - [InnerC]) * [ValuePart])
```

### For Line Charts with Path (connecting to center)

```
// X with Path (0 = inner circle, 1 = value position)
X: 
IF [Path] = 0 
THEN COS([Angle]) * [InnerC]
ELSE COS([Angle]) * ([InnerC] + ([OuterC] - [InnerC]) * [ValuePart])
END

// Y with Path
Y: 
IF [Path] = 0 
THEN SIN([Angle]) * [InnerC]
ELSE SIN([Angle]) * ([InnerC] + ([OuterC] - [InnerC]) * [ValuePart])
END
```

---

## 4. Worksheet Setup

### Basic Configuration

| Setting | Value |
|---------|-------|
| Mark Type | Line (or Polygon for filled) |
| Columns | X |
| Rows | Y |

### Shelf Configuration

| Shelf | Field | Purpose |
|-------|-------|---------|
| Columns | X | Horizontal position |
| Rows | Y | Vertical position |
| Path | Path (bin) or Path field | Connects points in order |
| Detail | Day of Year or Date | Creates individual marks |
| Color | Year or Month | Distinguishes categories |

### Axis Settings

1. Right-click X axis → Edit Axis → Set range to symmetric (e.g., -120 to 120)
2. Right-click Y axis → Edit Axis → Set range to symmetric (e.g., -120 to 120)
3. Ensure both axes have the same range for a circular shape
4. Hide headers and gridlines for clean appearance

---

## 5. Table Calculation Settings

**Critical Step** — Your X and Y calculations must compute correctly:

1. Right-click on X pill → **Compute Using** → Select your path/date field
2. Right-click on Y pill → **Compute Using** → Select your path/date field

Or use **Edit Table Calculation**:
- Specific Dimensions: Check the fields that define your path
- Addressing: The dimension you're computing across
- Partitioning: The dimension you're computing within

### Common Compute Using Options

| Chart Type | Compute Using |
|------------|---------------|
| Daily spiral | Day of Year |
| Multi-year comparison | Day of Year (partition by Year) |
| Weekly pattern | Week |
| Hourly pattern | Hour |

---

## 6. Visual Variations

### Radial Line Chart
- Mark type: Line
- Shows continuous trend around circle
- Good for: Time series, temperature patterns

### Radial Bar Chart
- Mark type: Polygon or Line with Path
- Bars extend from center outward
- Good for: Comparing magnitudes

### Radial Area Chart
- Mark type: Polygon
- Filled area from inner circle to value
- Good for: Emphasizing volume/quantity

### Multi-Ring Spiral
- Each year/category as separate ring
- Use Year on Color
- Adjust radius calculation to offset rings:
```
Radius Offset:
[InnerC] + (RANK_UNIQUE(MAX([Year]), 'asc') - 1) * 20
```

---

## 7. For Your Events Data

### Recommended Approach: Year-over-Year Comparison

Show all three years (2022-2024) as overlapping lines:

1. Use Day of Year for position around circle
2. Use Year for color
3. Each year traces the same 365-day path
4. Easy to spot seasonal patterns

### Alternative: Spiral Timeline

Show all data as one continuous spiral:

1. Create a sequential index across all dates
2. Each revolution = one year
3. Spiral expands outward over time

---

## 8. Reference Workbooks

Download these from Tableau Public to see working examples:

| Workbook | Author | Type | Link |
|----------|--------|------|------|
| Arctic Sea Ice Radial | Andy Kriebel | Multi-year line | [View](https://public.tableau.com/app/profile/andy.kriebel/viz/ArticSeaIceRadialChart/RadialChart) |
| Templates of Radial Charts | Martynas | Multiple types | [View](https://public.tableau.com/app/profile/martynasjocys/viz/Radialgraphsinboringblackandwhite/RRB) |
| Radial Chart in Tableau | Gurpreet Singh | Tutorial with parameters | [View](https://public.tableau.com/app/profile/gurpreet.singh2669/viz/RadialChartinTableau/HowtomakeRadialChart) |
| MLB Franchise History | Bo McCready | Multi-ring | [View](https://public.tableau.com/app/profile/bo.mccready8742/viz/MajorLeagueBaseballFranchiseHistoryRadialCharts/AllTeams) |
| Multi-Layered Radial | Ludovic Tavernier | Layered rings | [View](https://public.tableau.com/app/profile/ludovic.tavernier/viz/greatified_com-Multi-LayeredRadialChart/multi-layeredradialchart) |

---

## 9. Troubleshooting

### Chart appears as straight line
- Check table calculation compute using settings
- Ensure Path field is on the Path shelf

### Chart is elliptical, not circular
- Make X and Y axis ranges identical
- Format → Set axis range to fixed symmetric values

### Points not connecting properly
- Verify Path field is ordered correctly
- Check that Rank calculation is working

### Values not scaling correctly
- Ensure WINDOW_MAX is computing across all marks
- Check ValuePart calculation returns 0-1 range

### Missing data points
- ZN() function handles nulls
- Check for null values in source data

---

## 10. Quick Start Checklist

- [ ] Create scaffold table (Path: 0, 1)
- [ ] Join scaffold to data (cross-join)
- [ ] Create date-derived fields (Year, Day of Year, etc.)
- [ ] Create constants (InnerC, OuterC, PartSizeRad)
- [ ] Create Rank calculation
- [ ] Create ValuePart (normalized 0-1)
- [ ] Create Angle calculation
- [ ] Create X and Y calculations
- [ ] Set up worksheet with Line mark type
- [ ] Configure table calculations (Compute Using)
- [ ] Set symmetric axis ranges
- [ ] Add Color/Detail for categorization
- [ ] Format: hide axes, add center label if desired
