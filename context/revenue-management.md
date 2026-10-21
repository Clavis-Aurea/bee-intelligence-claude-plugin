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

