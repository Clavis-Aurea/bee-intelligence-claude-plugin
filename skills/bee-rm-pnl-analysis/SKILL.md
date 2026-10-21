---
name: bee-rm-pnl-analysis
description: Analyze a hotel P&L under USALI (Uniform System of Accounts for the Lodging Industry) from the USALI-12 mapped data in Bee Intelligence - check consistency, compute departmental profit, undistributed expenses, GOP, EBITDA, USALI ratios (% of revenue, POR, PAR), line-by-line variance vs budget / last year / last month, revenue and cost variance drivers (volume vs rate, flexed budget), Flow-Through, and compare hotels in a chain. Use when the user provides or mentions a "P&L", "profit and loss", "operating statement", "USALI", "income statement", "budget variance", "cost analysis", "EBITDA" (vi - "báo cáo kết quả kinh doanh", "phân tích P&L", "phân tích chi phí", "so với budget", "lãi lỗ").
---

# Revenue Manager - P&L Analysis (USALI)

Read and follow `${CLAUDE_PLUGIN_ROOT}/context/revenue-management.md` before starting.

Goal: turn a monthly P&L into a clear view of where profit came from and where it leaked, in the USALI structure, so management can act.

## Preparation

Confirm, and ask for anything missing:
- Hotel(s), reporting month, and comparison bases (budget, forecast, last year, last month). Ask whether the file has month and YTD columns and which to analyze.
- Source: the P&L data in Bee Intelligence, already mapped to USALI 12th edition (see "USALI-12 Report Lines"); and the operating statistics for the same period (rooms available, rooms sold, covers), needed for POR / PAR ratios and variance drivers. If statistics are missing, the analysis still runs, but say which parts can't be computed. If the user only has a P&L file that is not in Bee Intelligence, say this skill works from the USALI-12 data in Bee Intelligence and ask how to proceed; don't map the file yourself.
- Conventions (or from saved decisions): currency and unit (units, thousands, millions), sign convention for expenses, VAT excluded.
- Norms for this hotel (see "Norms and Targets"): `Materiality threshold`, and any margin / ratio targets the user wants to flag.

For several hotels, after Preparation and step 1, delegate steps 2-5 per hotel to `bee-rm-analyst` (see "Delegation" in the shared rules), passing the report line set and the query used; do steps 6-7 in the main conversation.

## USALI-12 Report Lines

P&L data in Bee Intelligence is already mapped to USALI 12th edition. The report line structure is in `hcdm_report_line` with `report_line_vocabulary_id = 'USALI-12'`. There is no mapping step: never re-map P&L lines yourself, and never replace these lines with a structure from memory.

- Use these report lines, in their own order and hierarchy, for every table in the output (sections, departments, subtotals such as Total Departmental Profit, GOP, EBITDA).
- Classify lines (operated department, undistributed, management fees, non-operating) by the vocabulary's own grouping, which also sets the ratio bases in step 3.
- If the data model behind the figures (which table holds the amounts by hotel and period, how it links to `hcdm_report_line`) is not clear from the available tools, the Glossary, or Workflows, ask the user; don't guess table or column names.

## Steps

### 1. Query the USALI-12 data
- With the available Bee Intelligence query tools, get the `USALI-12` report lines and the hotel's amounts for each line for the reporting period and each comparison base.
- Say which report line set and data source you used. If the tools can't reach this data with the user's permissions, say so (see Tools & Permissions in `agents/bee-assistant.md`) and stop.

### 2. Check consistency
- Sub-totals and totals add up along the `USALI-12` hierarchy; GOP = Total Departmental Profit − Total Undistributed Expenses; EBITDA follows from GOP.
- No amounts sit outside the `USALI-12` report lines (unmapped amounts); if some do, report them with the total.
- Revenue by department matches the operating statistics where possible (e.g. Rooms revenue ≈ rooms sold × ADR).
- Signs and units are consistent across columns.
- Report every mismatch with the amount; never correct the user's figures yourself. If a mismatch blocks the analysis, ask how to proceed.

### 3. Compute the statement and ratios
Use the USALI ratio bases (see the KPI Dictionary):
- Departmental expenses and departmental profit: % of **that department's** revenue. Cost of sales: % of the matching revenue (food cost vs food revenue, beverage cost vs beverage revenue).
- Undistributed expenses, GOP, management fees, EBITDA: % of **Total Operating Revenue**.
- Per occupied room (POR): Rooms expenses, Rooms labor, CPOR. Per available room (PAR): RevPAR, TRevPAR, undistributed expenses, GOPPAR, EBITDA PAR.
- Labor cost % by department and in total; revenue mix by department.

### 4. Variance analysis
- For each line: actual vs each comparison base, in amount and %, for the month (and YTD if asked).
- Flag only variances above the hotel's `Materiality threshold` (amount and / or %). If no threshold is set, ask; if the user doesn't want one, list the 10 largest absolute variances and say so.
- Show favorable / unfavorable correctly: higher revenue or profit is favorable; higher expense is unfavorable.

### 5. Variance drivers
Only where the statistics are available:
- **Rooms revenue**: volume effect = (actual rooms sold − budget rooms sold) × budget ADR; rate effect = (actual ADR − budget ADR) × actual rooms sold.
- **F&B revenue**: covers effect and average check effect, the same way.
- **Expenses, flexed budget**: flexed budget = fixed part + variable rate × actual volume (e.g. per occupied room or per cover). Volume variance = flexed budget − original budget; efficiency variance = actual − flexed budget. The fixed / variable split of each line must come from the user or a saved decision (`Cost behavior: <hotel name>`); never assume it. Without it, skip the flexed analysis and say so.
- **Flow-Through**: ΔGOP / ΔTotal Operating Revenue against each comparison base, by department where possible, checked against the hotel's `Flow-Through` norms.

### 6. Findings
Pick the 5-10 most important points: the biggest material variances and their drivers, departments with margin erosion, undistributed lines growing faster than revenue, weak Flow-Through. For each: figure, cause visible in the data, and which skill can dig deeper (`bee-rm-gop-optimization` for cost and Flow-Through, `bee-rm-fnb` for F&B cost, `bee-rm-pricing` for rate, `bee-rm-monthly-review` for channel and segment). Never invent causes that aren't in the data; if the cause is unknown, say what information would explain it.

### 7. Chain comparison (several hotels)
Same `USALI-12` report lines for every hotel; a comparison table of key ratios (GOP margin, departmental margins, labor cost %, undistributed % of revenue, GOPPAR, EBITDA margin) with each hotel against its own norms, and a ranking by the measure the user chooses. Note hotels whose data is incomplete.

## Output

1. Executive summary (3-5 lines): total revenue, GOP, GOP margin, EBITDA vs budget and last year, the main reason for the gap.
2. Summary operating statement in the `USALI-12` report lines: columns Actual | Budget | Var | Var % | LY | Var | Var % | % of revenue (USALI basis), with POR / PAR where computable.
3. Departmental schedules for the departments with material variances.
4. Variance drivers and Flow-Through.
5. Findings (step 6) and, for a chain, the comparison (step 7).
6. Data notes: report line set and data source used, consistency issues, parts not computed and why.

Ask whether the user wants it as a document, spreadsheet, or slides.
