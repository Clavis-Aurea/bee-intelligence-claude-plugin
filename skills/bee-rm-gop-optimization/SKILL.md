---
name: bee-rm-gop-optimization
description: Maximize hotel Gross Operating Profit (GOP, GOPPAR) with Profit-Oriented Revenue Management - channel audit by Net RevPAR, contribution margin by segment, Flow-Through audit with Accounting / Finance, variable budgeting by CPOR, labor flexing by OTB, and planning / tracking a 90-day GOP campaign. Use when the user asks to "increase GOP", about "GOPPAR", "flow-through", "revenue is up but profit isn't", "reduce OTA dependence", "direct booking", "Revenue and Accounting collaboration", "budget from the forecast", "90-day campaign" (vi - "tăng GOP", "doanh thu tăng mà lợi nhuận không tăng", "giảm phụ thuộc OTA", "phối hợp Revenue với Kế toán", "chiến dịch 90 ngày").
---

# Revenue Manager - GOP Optimization

Read and follow `${CLAUDE_PLUGIN_ROOT}/context/revenue-management.md` before starting.

Principle: focus on "how much money is retained" after each transaction. The ultimate indicator is GOPPAR, not RevPAR.

Determine which tasks the user needs (can be several): A - channels, B - segments, C - Flow-Through audit, D - budget and staffing from the forecast, E - 90-day campaign. Ask if unclear.

For several hotels or large data, delegate the calculations of A, B, and C (steps 1-3) per hotel to `bee-rm-analyst` (see "Delegation" in the shared rules). Recommendations, D, and E stay in the main conversation.

## A. Channel Mix Audit

1. Per channel: room nights, revenue, commission and fees (card fees, conversion fees), net revenue, Net ADR, Net RevPAR contribution.
2. Compare direct channels (website, hotline, Zalo / email) with OTAs: what rate does an OTA night need to match a direct night?
3. Recommend: push direct booking (direct-only perks cost less than commission), close low-rate room types on OTAs when forecast OCC is high, review OTA / Corporate contracts, stop overlapping promotions.

## B. Contribution Margin by Segment

Per segment (Transient, Group, Corporate...): room + ancillary revenue − distribution cost − segment-specific variable cost = contribution per room night. Identify the segments contributing the most and least GOP. For a specific group, use `bee-rm-group-displacement`.

## C. Flow-Through Audit (with Accounting / Finance)

1. Confirm the comparison base, and get the hotel's `Flow-Through` norms (Rooms, F&B, Hotel) as described in "Norms and Targets" in the shared rules.
2. Compute Flow-Through = ΔGOP / ΔRevenue. If revenue fell or barely changed, report it separately (the ratio is not meaningful) and analyze absolute costs.
3. Break it down: which cost lines (labor, utilities, laundry, amenities, commission...) the extra revenue "leaked" into. Separate fixed costs (must not rise with revenue) from variable costs (may rise only in proportion to rooms sold).
4. Flag 🔴 if operating costs grow faster than revenue.

## D. Variable Budget and Staffing from the Forecast

1. **Variable budget**: variable cost budget = the hotel's `CPOR` norm (agreed with Accounting) × forecast occupied rooms (from `bee-rm-forecast`). Low-forecast weeks: suggest freezing non-urgent purchases; peak weeks: suggest buying early to get prompt-payment discounts.
2. **Labor Flexing**: from forecast OCC / arrivals / departures per day, suggest staffing levels for FO, HK, F&B so department heads can adjust rosters. Use the hotel's `Staffing standard` norms (e.g. rooms per room attendant per shift); if missing, ask, and never invent standards.
3. **Energy**: on low-OCC days, suggest consolidating guests and closing floors (per the internal SOP if one is in Workflows).
4. **Information handoff**: Revenue provides OTB, pickup, ADR by segment; Accounting provides actual CPOR, commission cost, receivables; Finance provides Flow-Through and Net RevPAR. Point out which information is missing.

## E. 90-Day GOP Campaign

When the user wants a plan, build a 3-phase roadmap for one pilot hotel, then adapt it to the hotel's reality:
- **Days 1-30 - Diagnose and set foundations**: channel audit (A), set the GOP floor rate (`bee-rm-pricing`), standardize data (`bee-rm-data-audit`), review OTA / Corporate contracts, stop overlapping promotions.
- **Days 31-60 - Net revenue push**: "Direct First" campaign, front-desk upselling with incentives, room + F&B / Spa packages (`bee-rm-fnb`, `bee-rm-guest-insights`).
- **Days 61-90 - Operations and Flow-Through**: Flow-Through target, labor flexing, energy management, a review meeting on GOPPAR vs last month.

Tracking KPIs: Direct Booking %, Net RevPAR, Flow-Through, GOPPAR, against the pilot hotel's norms for these metrics (`Direct Booking %`, `Net RevPAR growth`, `Flow-Through`, `GOPPAR growth`; the campaign figures in the Norm Catalogue are only examples). When tracking the campaign, report each KPI against target and baseline; if revenue rises but GOPPAR stays flat, say so clearly so cost processes get reviewed. Once the pilot succeeds, the rollout to other hotels can be proposed; each hotel uses its own norms, so ask for any that are missing (chain default plus exceptions, or a file import).

## Output

Analysis tables for the parts done, key findings, recommendations with estimated impact on GOP / GOPPAR, and a list of missing data.
