---
name: bee-rm-monthly-review
description: Monthly revenue report and revenue meeting pack for a hotel - monthly KPIs (OCC, ADR, RevPAR, Net RevPAR, TRevPAR, GOPPAR), STR market indices (RGI, MPI, ARI), channel production, segment mix, cancellation rate, Flow-Through, and next month's action plan for the GM and owners. Use when the user asks for a "monthly report", "monthly review", "revenue meeting", "yield meeting", "STR analysis", "channel review", "segment review" (vi - "báo cáo tháng", "phân tích STR", "đánh giá kênh", "đánh giá phân khúc").
---

# Revenue Manager - Monthly Review

Read and follow `${CLAUDE_PLUGIN_ROOT}/context/revenue-management.md` before starting.

Goal: see the full picture of the past month, measure financial health and market position, then set direction for the next 3-6 months.

## Preparation

Confirm, and ask for anything missing:
- Hotel (or chain), reporting month.
- Comparison bases: budget, forecast, last month, same month last year (can be several).
- Sources: Bee Intelligence data sources, STR / CompSet reports, Accounting cost reports (for Net RevPAR, GOPPAR, Flow-Through).
- Purpose: internal RM report or a pack for the GM / owners meeting (affects level of detail).
- The norms each section is checked against, per hotel (see "Norms and Targets" in the shared rules). For a chain review, ask for missing norms in one batch (chain default plus exceptions, or a file import), not hotel by hotel.

## Analysis Sections

Do the sections that have data; for any section missing data, write "no data yet" rather than silently leaving it out.

For a chain review, delegate sections 1-6 per hotel (or batch) to `bee-rm-analyst`; for one hotel with large data, sections 2-5 can be delegated in parallel. See "Delegation" in the shared rules. The Action Plan and Output are always done in the main conversation.

1. **Headline KPIs**: OCC, ADR, RevPAR, Net RevPAR, TRevPAR, GOPPAR, room revenue and total revenue, against the comparison bases.
2. **Market (STR / CompSet)**: MPI, ARI, RGI for the month and the trend over recent months. Explain whether RGI moved because of rate (ARI) or occupancy (MPI).
3. **Channel Production**: per channel - room nights, revenue, ADR, distribution cost, Net ADR, share, cancellation rate. Rank by net revenue, not just revenue. Identify the channel bringing the most guests and the most expensive channel.
4. **Segment Performance**: Transient / Group / Corporate (and internal segments per the Glossary). Actual share vs the hotel's `Segment mix` norms, ADR, LOS per segment.
5. **Cancellations**: cancellation rate by channel, segment, lead time; the channel with the most cancellations and suggested policy changes (deposit, cancellation terms).
6. **Flow-Through**: against the comparison base the user chose, for Rooms and the whole hotel if GOP figures are available, against the hotel's `Flow-Through` norms. If below the norm, point to cost lines that rose abnormally (use `bee-rm-gop-optimization` for a deep dive).
7. **Data health**: suggest running `bee-rm-data-audit` if there are signs of miscoding (e.g. a segment with unusual ADR / LOS).

## Action Plan

From the findings, recommend for next month and the next 3-6 months: channel budget (increase / cut), segment mix adjustments, seasonal pricing policy (use `bee-rm-pricing`), forecast update (use `bee-rm-forecast`). Each action: owner (if the user says), deadline, measuring indicator.

## Output

- Internal report: executive summary, KPI tables, key findings, action plan.
- GM / owners meeting pack: a one-page summary (results vs targets, RGI, GOPPAR, Flow-Through), 3-5 key messages, next month's plan. Ask whether the user wants it as a document, spreadsheet, or slides.
