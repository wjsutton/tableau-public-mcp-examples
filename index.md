---
layout: default
title: "Tableau Public MCP Examples"
description: "Prompt templates, examples, and guides for using Claude with the Tableau Public MCP Server"
---

# Tableau Public MCP Examples

> Real-world examples, prompt templates, and guides for using Claude with the Tableau Public MCP Server

## What is This?

The Tableau Public MCP Server enables Claude to analyse Tableau Public visualisations, extract data structures, and generate dashboards, galleries, and guides. This repository showcases practical examples you can adapt for your own projects.

## Prerequisites

- Tableau Public MCP Server installed and configured
- A Tableau Public account (optional for viewing examples)

---

## Examples

### 🖼️ Gallery Page Generator

![HTML](https://img.shields.io/badge/Output-HTML-orange?style=flat-square) ![Complexity](https://img.shields.io/badge/Complexity-Easy-green?style=flat-square)

**What it does:** Automatically creates a responsive HTML gallery page displaying all visualisations from a Tableau Public profile with thumbnails and clickable links.

**Use case:** Perfect for portfolio websites, personal showcase pages, or embedding your complete visualisation collection. The gallery is self-contained (single HTML file) and mobile-responsive.

**Example prompt:**
```
Can you take every viz from my tableau profile: wjsutton and create a html page
that is a gallery of images of every viz with a clickable link to the dashboard.
```

**Prompt template:**
```
Can you take every viz from my Tableau Public profile: [your-username]
and create a [output-type] page that is a gallery of images of every viz
with a clickable link to the dashboard.

Optional additions:
* Include viz titles and view counts
* Use a [layout-style] layout (grid, masonry, carousel)
* Add [filtering-options] (by date, views, favourites)
* Style with [colour-scheme] colours
```

**Placeholders to customise:**

| Placeholder | Description | Example |
|-------------|-------------|---------|
| `[your-username]` | Your Tableau Public username | wjsutton |
| `[output-type]` | File format | HTML, Markdown |
| `[layout-style]` | Grid arrangement | responsive grid, masonry |
| `[filtering-options]` | Interactive features | sort by views, filter by date |
| `[colour-scheme]` | Visual styling | dark theme, brand colours |

**Output:** [View the gallery →](./gallery.html)

<details>
<summary>🔍 Preview the gallery (click to expand)</summary>

<iframe src="./gallery.html" width="100%" height="600px" style="border: 1px solid #e5e7eb; border-radius: 8px; margin-top: 1rem;"></iframe>

</details>

---

### 💼 Job Application Portfolio

![HTML](https://img.shields.io/badge/Output-HTML-orange?style=flat-square) ![Complexity](https://img.shields.io/badge/Complexity-Advanced-red?style=flat-square)

**What it does:** Generates a custom-themed portfolio website that matches specific job requirements, combining your Tableau Public visualisations with professional experience.

**Use case:** Ideal for job applications where you want to demonstrate how your skills align with specific role requirements. Creates a targeted, professional portfolio that stands out.

**Example prompt:**
```
Hey Claude, for my job application to Jagex, please, can you build me a html
portfolio website to showcase the relevant content I have to meet the job
requirements of a Senior Marketing Analyst.

* embed thumbnails of relevant marketing dashboards from my Tableau Public account user: wjsutton
* Please see my previous analyst and marketing experience from LinkedIn attached.
* For technical skills, match the requirements of the job ad to projects & dashboards I have completed.
```

**Prompt template:**
```
Hey Claude, for my job application to [company-name], please build me an HTML
portfolio website to showcase relevant content for the role of [job-title].

Requirements:
* Embed thumbnails of relevant [domain-area] dashboards from my Tableau Public account: [your-username]
* Include my previous [relevant-experience-type] experience (attached/linked)
* Match technical skills from the job requirements to my completed projects
* Use a [theme-style] design aesthetic that reflects [company-culture]

Optional:
* Include specific projects: [project-list]
* Highlight achievements: [key-achievements]
* Add contact information: [contact-details]
```

**Placeholders to customise:**

| Placeholder | Description | Example |
|-------------|-------------|---------|
| `[company-name]` | Target company | Jagex, Epic Games |
| `[job-title]` | Position applying for | Senior Marketing Analyst |
| `[domain-area]` | Industry focus | marketing, gaming, finance |
| `[your-username]` | Tableau Public username | wjsutton |
| `[theme-style]` | Visual design | gaming-themed, corporate |
| `[company-culture]` | Cultural fit | innovative, data-driven |

**Output:** [View the portfolio →](./jagex.html)

<details>
<summary>🔍 Preview the portfolio (click to expand)</summary>

<iframe src="./jagex.html" width="100%" height="600px" style="border: 1px solid #e5e7eb; border-radius: 8px; margin-top: 1rem;"></iframe>

</details>

---

### 📊 Marketing Dashboard Build Guide

![Markdown](https://img.shields.io/badge/Output-Markdown-blue?style=flat-square) ![Complexity](https://img.shields.io/badge/Complexity-Medium-yellow?style=flat-square)

**What it does:** Analyses an existing Tableau marketing dashboard and produces a comprehensive step-by-step guide for recreating it with your own data.

**Use case:** Learn dashboard techniques by reverse-engineering professional examples. Perfect for understanding complex calculations, LODs, and layout strategies used in real-world marketing analytics.

**Example prompt:**
```
Can you take a look at https://public.tableau.com/app/profile/gbolahan.adebayo/viz/MarketingCampaignPerformanceDashboard_17164464702070/InsightsOverview
and tell me:
* how it was made,
* what the data structure is,
* any nuances to working with campaign data,
* any calculations or LODs I should watch out for.

Then can I have a guide on how to make a similar type of view with a dataset from Kaggle
```

**Prompt template:**
```
Can you analyse this Tableau Public dashboard: [viz-url] and provide:

* How it was built (worksheet structure, calculations)
* Required data structure and fields
* Key calculations and LOD expressions used
* Any domain-specific nuances (e.g., [domain-area] data considerations)
* Common pitfalls to avoid

Then create a step-by-step guide for building a similar dashboard using [data-source].

Optional specifications:
* Focus on these metrics: [specific-metrics]
* Include adaptations for [industry/use-case]
* Provide sample calculated field formulas
```

**Placeholders to customise:**

| Placeholder | Description | Example |
|-------------|-------------|---------|
| `[viz-url]` | Tableau Public dashboard URL | Full URL to any public viz |
| `[domain-area]` | Industry/data type | marketing, sales, finance |
| `[data-source]` | Where you'll get data | Kaggle, own dataset |
| `[specific-metrics]` | KPIs to focus on | ROI, conversion rate, CPC |
| `[industry/use-case]` | Adaptation context | gaming, e-commerce |

**Output:** [Read the guide →](./marketing-dashboard-guide.md)

**Preview:**
> This comprehensive guide covers:
> - **Phase 1-2:** Data preparation and parameter setup
> - **Phase 3-4:** Core calculated fields and dynamic metrics
> - **Phase 5:** LOD expressions for campaign-level analysis
> - **Phase 6-7:** Worksheet creation and dashboard assembly
> - **Phase 8:** Polish, formatting, and testing
> - **Bonus:** Gaming industry adaptations

---

### 📈 Radial Plot Build Guide

![Markdown](https://img.shields.io/badge/Output-Markdown-blue?style=flat-square) ![Complexity](https://img.shields.io/badge/Complexity-Advanced-red?style=flat-square)

**What it does:** Searches Tableau Public for radial plot examples and creates a detailed technical guide for building one with your specific dataset.

**Use case:** Master advanced Tableau techniques for creating circular/polar charts. Ideal for cyclical data like seasonal trends, hourly patterns, or year-over-year comparisons.

**Example prompt:**
```
How would I build a radial plot with this data set in Tableau?
Look at examples from Tableau Public and tell me what data structure,
calculations and sheets I need to make a radial plot.
```

**Prompt template:**
```
How would I build a radial plot with [dataset-description] in Tableau?

Please:
* Search Tableau Public for radial plot examples showing [data-pattern-type]
* Explain the required data structure for my [time-period] data
* Provide all necessary calculated fields with explanations
* Include table calculation setup instructions
* Show variations: [chart-variations]

My data details:
* Date range: [date-range]
* Metrics: [metric-list]
* Grouping: [grouping-dimension]
```

**Placeholders to customise:**

| Placeholder | Description | Example |
|-------------|-------------|---------|
| `[dataset-description]` | Brief data summary | daily events from 2022-2024 |
| `[data-pattern-type]` | What you're visualising | seasonal trends, daily patterns |
| `[time-period]` | Granularity | daily, weekly, monthly, hourly |
| `[chart-variations]` | Specific styles | line, bar, area, multi-ring |
| `[date-range]` | Timeframe | 2022-2024, last 365 days |
| `[metric-list]` | Values to plot | events, revenue, conversions |
| `[grouping-dimension]` | Category splits | year, product, region |

**Output:** [Read the guide →](./radial-plot-tableau-guide.md)

**Preview:**
> This advanced guide includes:
> - **Section 1-2:** Data structure requirements and densification
> - **Section 3:** Core trigonometric calculated fields (X, Y coordinates)
> - **Section 4-5:** Worksheet setup and table calculation configuration
> - **Section 6:** Visual variations (line, bar, area, spiral)
> - **Section 7-8:** Practical examples and reference workbooks
> - **Section 9-10:** Troubleshooting and quick start checklist

---

## How to Use These Templates

1. **Choose an example** that matches your use case
2. **Copy the prompt template** from the example above
3. **Replace `[placeholders]`** with your specific information (see the placeholder tables)
4. **Run in Claude** with the Tableau Public MCP Server enabled
5. **Iterate and refine** based on the output

## Tips for Best Results

- Be specific about output format (HTML, Markdown, etc.)
- Include examples when describing style preferences
- Mention any technical constraints upfront
- Reference specific Tableau Public URLs when analysing existing vizzes
- For portfolio projects, attach relevant documents (CV, job descriptions)

## Additional Resources

- [Tableau Public: wjsutton](https://public.tableau.com/app/profile/wjsutton)
- [Model Context Protocol Overview](https://modelcontextprotocol.io)
- [GitHub Pages Setup Guide](./gh-pages-guide.md)

## Contributing

Found a creative use case? Submit a PR with your example and prompt template!

---

*Last updated: 2026-01-07*
