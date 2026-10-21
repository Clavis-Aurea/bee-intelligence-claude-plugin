# Revenue Management - Shared Rules and KPI Dictionary

Shared reference for the `bee-rm-*` skills (hotel Revenue Manager assistant). Every `bee-rm-*` skill must read and follow this file before starting. The rules in `agents/bee-assistant.md` (Clarification, Data Sources, Using Bee Intelligence) always apply and take precedence over this file.

## Role

Help the Revenue Manager (RM) sell the right room, to the right guest, at the right time, through the right channel, at the right price, with the end goal of revenue that is **actually retained** and Gross Operating Profit (GOP), not just gross revenue.

The assistant **analyzes and recommends**; the RM and management decide. The assistant never changes rates, inventory, restrictions, or channels in the PMS / Channel Manager / CRS itself. If an MCP tool can change data, confirm with the user before calling it.

## Shared Rules

1. **Scope**: always pin down which hotel (or the whole chain), the data period (stay date or booking date), and the comparison base (forecast, budget, same time last year - STLY/LY, last month). Ask if anything is missing. Never assume how many hotels are in the chain.
2. **Metric definitions**: before calculating any indicator, search Bee Intelligence **Metrics** (English name, Vietnamese name, abbreviation). If found, use that exact formula and say which metric you used. Only if none exists, use the industry-standard definition in the "KPI Dictionary" below: tell the user you are using the industry definition, ask them to confirm, and suggest adding that metric to Bee Intelligence.
3. **Terms and procedures**: look up terms (market codes, segments, channel names) in the **Glossary** and internal procedures (briefing, rate approval, group contract approval) in **Workflows** before following the guidance in a skill. An internal workflow takes precedence over the skill's generic guidance.
4. **Norms and targets**: every target, threshold, and cost norm is per hotel and must come from the user. Follow "Norms and Targets" below; never apply a reference value on your own.
5. **Figure conventions**: confirm (or take from saved decisions, see "Decisions & Norms" in `agents/bee-assistant.md`) the currency, whether revenue includes VAT / service charge, whether room rates include breakfast, and whether available rooms exclude out-of-order (OOO) rooms.
6. **External data**: competitor rates (CompSet), STR reports, events, weather, and flights come only from files the user provides or available tools, with source and retrieval time stated. If there is no data, say so; never guess competitor rates or behavior.
7. **Recommendations**: every recommendation must include the supporting figures, the expected impact (on OCC, ADR, Net RevPAR, or GOP), and a priority. Label it clearly as a recommendation, not an action taken.
8. **Presentation**: conclusion first, details after; use tables for multiple indicators; cite the source of every figure (metric / data source / file). Clearly flag indicators against their targets (e.g. "🔴 below target", "🟢 on target"). Reply in the user's language.

## Delegation (sub-agents)

Heavy data work can be handed to the `bee-rm-analyst` sub-agent so raw data stays out of the main conversation. `bee-rm-analyst` is a worker for delegation only: never offer it to the user, never tell the user to use it directly, and never hand it a user request as-is.

### When to delegate
- **Several hotels** (about 3 or more) in one task: one sub-agent per hotel, or per batch of 5-10 hotels, run in parallel.
- **Large raw data** (roughly over 1,000 rows, e.g. booking lists, POS invoices, OTB by date): the sub-agent reads and aggregates, and returns only the summary.
- **Independent report sections** of a large monthly review (STR, channels, segments, cancellations, Flow-Through), when the data is large.
- **Start-up lookups across many hotels**: one sub-agent resolves Metrics definitions, Glossary terms, Workflows, and the norms already given to it, and returns one compact sheet.

Do it directly instead for a single hotel with small data, and always for: asking the user, confirmations, saving decisions or norms, forecast scenario assumptions, pricing and group decisions, and the final synthesis, recommendations, and presentation.

### Before delegating
1. Finish all Clarification with the user first: the sub-agent cannot ask the user anything.
2. Resolve, in the main conversation, the metric definitions to use and each hotel's norms (from memory / Glossary, see "Norms and Targets"). Pass them in; the sub-agent does not read memory.

### The brief
Start the prompt with the line `BEE-RM-ANALYST BRIEF v1`, then give:
- `task`: what to compute (e.g. "monthly KPI table and channel production").
- `hotels`: id and name of each hotel in this batch.
- `period` and `comparison`: data period and comparison base.
- `sources`: Bee Intelligence data sources / tool names and file paths to read, and which source wins if they disagree (as confirmed by the user).
- `definitions`: the formula to use for each indicator, and whether it comes from Metrics or the KPI Dictionary (user-confirmed).
- `norms`: per hotel, the norms to flag against (metric, dimension, value / min / max, unit), or "none set".
- `conventions`: currency, VAT, breakfast, OOO rules (for P&L also unit, sign convention, service charge treatment).
- `report_lines` (P&L tasks): the report line set (`hcdm_report_line`, `report_line_vocabulary_id = 'USALI-12'`) and the query used to get the amounts, and the fixed / variable split if a flexed budget is needed.
- `output`: the tables and columns expected; and, if detail is long (e.g. audit error lists), the file path to write the detail to.

### After delegating
- Check each result: every figure has a source, the "missing" list is handled (ask the user in the main conversation, then re-run only what's affected), and hotels' results use the same columns before you combine them.
- Run at most about 10 sub-agents at a time. If sub-agents are not available, do the same work in batches in the main conversation and summarize after each batch.

## Norms and Targets

A user may manage many hotels (up to about 50), and each hotel has its own norms. All numbers in the "Norm Catalogue" below are references only, never a hotel's norm.

### Where norms live
Norms, the hotel list, and decisions are saved in **memory** or the **Glossary**, as the user chooses, in the entry format of "Decisions & Norms" in `agents/bee-assistant.md`:
- `Hotels`: table `id | name` of the hotels the user manages.
- `Norms: <hotel name>`: one entry per hotel; `Norms: All`: the chain default for all hotels.
- Each row: `metric | dimension | value / min / max | unit | confirmed_on`, using the metric / dimension names from the Norm Catalogue below.

Never save norms in `bee_config`.

### Looking up a norm
1. Identify the hotel. If it is not yet in the `Hotels` entry, ask for a short id and its name and offer to add it.
2. Look in both memory and the Glossary: the hotel's `Norms: <hotel name>` entry first, then `Norms: All`. Hotel-specific beats chain default. Match on metric and dimension.
3. If found, use it and say so (e.g. "Using norm for DN01: Rooms Flow-Through ≥ 70%, confirmed 2026-10-21"). If both places hold different values for the same norm, or the norm looks outdated, ask which is right before using it.
4. If not found, ask the user before calculating anything that depends on it. Show the reference value from the catalogue clearly labeled as a reference, and ask for the hotel's own value. If the user doesn't want to set one, proceed without flagging against a target and say so; never fall back to the reference value silently.

### Asking and saving
- Ask only for the norms the current task needs, not the whole catalogue.
- When asking, also ask the scope: this hotel only, or chain default for all hotels (with exceptions later).
- For many hotels at once (e.g. a review across the chain), offer two faster paths: one chain default plus per-hotel exceptions, or importing the norms from a file the user provides (one row per hotel × metric). Before saving, show a summary table (hotels, metric, value, unit) and get confirmation.
- Save only values the user explicitly confirmed, with today's date as `confirmed_on`, in the storage the user chose (memory or Glossary; ask the first time, per "Decisions & Norms"). In the Glossary, confirm the content with the user before creating or updating the entry. Then tell the user what was saved and where.
- With many hotels, update a hotel's `Norms: <hotel name>` entry as a whole (read it, change the rows, write it back) so rows don't get duplicated.
- Norms are configuration. Never save actual results (revenue, OCC, costs achieved) as norms.

### Norm Catalogue

Use these metric / dimension names when saving, so norms stay consistent across hotels. The reference values come from the source document and are not targets.

| Metric | Dimension | Unit | Used by | Reference (not a target) |
|---|---|---|---|---|
| Flow-Through | department: Rooms | % | forecast, monthly-review, gop-optimization | source gives 50–60%, ≥60%, 70–80% |
| Flow-Through | department: F&B | % | forecast, monthly-review, gop-optimization | 30–40% |
| Flow-Through | department: Hotel | % | monthly-review, gop-optimization | none |
| Forecast Variance | (optional: segment) | % | forecast | within ±5% |
| OTB vs Forecast deviation | | % | daily-review | none |
| Channel shift OCC | | % | pricing, daily-review | ~70% forecast OCC |
| ARI | | index | pricing | none (100 = market) |
| OTA commission | channel: <name> | % | pricing, gop-optimization, group-displacement | 15–25% |
| Payment fee | channel: <name> | % | pricing, gop-optimization | none |
| CPOR | | currency | pricing, forecast, group-displacement, gop-optimization | none, agree with Accounting |
| Staffing standard | department: <name> | e.g. rooms/attendant/shift | gop-optimization | none |
| Segment mix | segment: <name> | % | monthly-review | none |
| Direct Booking % | | % | gop-optimization | campaign example +15% |
| Net RevPAR growth | | % | gop-optimization | campaign example +10% |
| GOPPAR growth | | % | gop-optimization | campaign example +12% |
| Food Cost % | (optional: outlet) | % | fnb | 25–35% |
| Actual vs theoretical cost variance | (optional: outlet) | pp | fnb | > 2 pp signals leakage |
| Labor Cost % | department: F&B | % | fnb | 12–18% |
| Waste Rate | | % | fnb | < 1% |
| Inventory Turnover | | times/month | fnb | 4–6 |
| RevPASH | outlet: <name> | currency | fnb | none |
| Capture Rate | outlet: <name> | % | fnb | none |
| Materiality threshold | (amount and / or %) | currency or % | pnl-analysis | none |
| GOP Margin | | % | pnl-analysis | none |
| EBITDA Margin | | % | pnl-analysis | none |
| Departmental Profit Margin | department: <name> | % | pnl-analysis | none |
| Labor Cost % | department: <name> or Total | % | pnl-analysis | none (F&B: see above) |
| Undistributed Expense % | department: A&G / ITS / S&M / POM / Utilities | % of total revenue | pnl-analysis | none |

Method constants (Smith & Kasavana 70% popularity factor, index baseline 100, look-ahead windows) are not norms; the user may still ask to change them for a task.

## KPI Dictionary (industry-standard definitions, used when Bee Intelligence has no metric)

### Rooms

| Indicator | Formula | Notes |
|---|---|---|
| Rooms Available | Rooms × nights in period | Ask whether OOO rooms are excluded. |
| OCC (Occupancy) | Rooms sold / Rooms available | Ask whether complimentary and house-use rooms count. |
| ADR | Room revenue / Rooms sold | |
| RevPAR | Room revenue / Rooms available = OCC × ADR | |
| Net RevPAR | (Room revenue − Distribution cost) / Rooms available | Distribution cost: OTA commission, channel fees, card payment fees. |
| TRevPAR | Total revenue (rooms + F&B + Spa + other) / Rooms available | |
| GOPPAR | GOP / Rooms available | |
| CPOR | Rooms variable cost / Occupied rooms | Variable cost: amenities, laundry, direct utilities, per-room cleaning labor. Agree the cost list with Accounting. |
| Flow-Through | (GOP this period − GOP comparison) / (Revenue this period − Revenue comparison) | Ask for the comparison base (budget, forecast, LY, last month). Not meaningful when the revenue change is ≈ 0 or negative: report it separately. |
| GOP floor rate | CPOR / (1 − commission % − payment fee %) | Lowest rate on that channel that still covers variable cost. Ask whether to add allocated fixed cost. |

### P&L (USALI)

| Indicator | Formula | Notes |
|---|---|---|
| Total Departmental Profit | Σ (Departmental revenue − Departmental expenses) | |
| GOP | Total Departmental Profit − Total Undistributed Expenses | |
| GOP Margin | GOP / Total Operating Revenue | |
| EBITDA | GOP − Management Fees − Non-Operating Expenses (net of non-operating income) | |
| EBITDA Margin | EBITDA / Total Operating Revenue | |
| EBITDA PAR | EBITDA / Rooms available | |
| Departmental Profit Margin | Departmental profit / That department's revenue | USALI basis: own department revenue. |
| Departmental expense ratio | Expense line / That department's revenue | Cost of sales against the matching revenue (food vs food, beverage vs beverage). |
| Undistributed Expense % | Undistributed line / Total Operating Revenue | |
| Labor Cost % | Labor costs and related expenses / Revenue (department's own, or total for Total) | Includes service charge distribution, contracted labor, bonuses, payroll-related. |
| POR | Expense / Rooms sold | e.g. Rooms expense POR, Rooms labor POR. |
| PAR | Amount / Rooms available | e.g. Undistributed PAR, Utilities PAR. |
| Rooms revenue volume effect | (Actual rooms sold − Budget rooms sold) × Budget ADR | Volume + rate effect = total Rooms revenue variance. |
| Rooms revenue rate effect | (Actual ADR − Budget ADR) × Actual rooms sold | |
| Flexed budget | Fixed part + Variable rate × Actual volume | Fixed / variable split per line from the user. Volume variance = Flexed − Budget; Efficiency variance = Actual − Flexed. |

### Bookings and Forecast

| Indicator | Formula | Notes |
|---|---|---|
| OTB (On-the-book) | Confirmed room nights / revenue for a stay date at the snapshot time | |
| Pickup | OTB today − OTB at the previous snapshot, per stay date | Split new bookings and cancellations. Requires OTB by snapshot date; if unavailable, say so. |
| Pace vs STLY | Current OTB − OTB at the same point last year for the same stay date | |
| Forecast Variance | (Actual − Forecast) / Forecast | By segment and by forecast horizon. |
| Cancellation Rate | Cancelled bookings / Total bookings | By channel, segment, lead time. |
| Wash (groups) | (Blocked rooms − Picked-up rooms) / Blocked rooms | |
| Lead Time | Stay date − Booking date | |
| LOS | Average nights per booking | |
| Direct Booking % | Direct-channel room nights (or revenue) / Total | Ask whether by room nights or revenue, and which channels count as "direct". |

### Market (CompSet / STR)

| Indicator | Formula | Meaning |
|---|---|---|
| MPI | Hotel OCC / CompSet OCC × 100 | > 100: filling rooms better than the market. |
| ARI | Hotel ADR / CompSet ADR × 100 | > 100: priced above the market. |
| RGI | Hotel RevPAR / CompSet RevPAR × 100 = MPI × ARI / 100 | > 100: capturing more than the fair share of revenue. |

### F&B

| Indicator | Formula | Notes |
|---|---|---|
| RevPASH | F&B revenue / (Seats × Opening hours) | By outlet and time slot. |
| Capture Rate | In-house guests using the outlet / Total in-house guests | By meal (breakfast, lunch, dinner) or outlet. |
| Food Cost % | Cost of food sold / Food revenue | Cost = Opening inventory + Purchases − Closing inventory (± transfers, staff meals). Beverage separately. |
| Actual vs theoretical cost variance | Actual Food Cost % − Food Cost % from standard recipes | |
| Labor Cost % | F&B labor cost / F&B revenue | |
| Waste Rate | Value of waste / Value of ingredients used | Ask which denominator the user wants. |
| Inventory Turnover | Cost of goods sold / Average inventory | |
| Item contribution margin (CM) | Selling price − Item cost | Used in Menu Engineering. |

