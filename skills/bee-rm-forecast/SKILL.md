---
name: bee-rm-forecast
description: Build and update a GOP-oriented hotel revenue forecast (30-60-90 day rolling forecast) by segment - based on OTB and pickup pace, net of cancellations / wash, with Net RevPAR, TRevPAR, expected GOP, three scenarios (Base / Best / Downside), forecast variance analysis, and preparation for presenting to the GM and HoDs. Use when the user asks for a "forecast", "rolling forecast", "update the forecast", "revenue scenarios", "variance analysis", "forecast accuracy", "present the forecast to the GM" (vi - "dự báo", "cập nhật forecast", "kịch bản doanh thu", "sai số dự báo").
---

# Revenue Manager - Forecast

Read and follow `${CLAUDE_PLUGIN_ROOT}/context/revenue-management.md` before starting.

Principle: don't forecast gross revenue, forecast **the cash actually retained** (Net Revenue, GOP).

Determine which task the user needs: (A) build / update a forecast, (B) variance analysis, (C) prepare a presentation. Ask if unclear.

## A. Build / Update a Forecast

### Preparation
Confirm: hotel, horizons (30 / 60 / 90 days), granularity (daily, weekly, or monthly), OTB source, history (at least same period last year), cost norms (`OTA commission` by channel, `CPOR`; see "Norms and Targets" in the shared rules) if GOP is needed. Ask whether there is an internal forecast workflow in Bee Intelligence; if there is, follow it.

### Steps
1. **Segments**: forecast Transient, Group, Corporate (and segments per the Glossary) separately. Each segment has its own booking window and cancellation rate.
2. **OTB base**: current OTB by stay date and segment.
3. **Remaining pickup**: estimate rooms still to be booked from the actual pickup pace of the last 14-30 days and the historical booking curve by lead time, not just "last year + x%". State the method used.
4. **Cancellations and wash**: deduct historical cancellation rates by channel (especially OTA) and group wash.
5. **Events**: adjust for holidays and local events the user provides; state which dates were adjusted and why.
6. **HoD input**: ask whether the user has input from the DOS (potential and tentative groups) and FOM (walk-ins); include it with the source.
7. **Revenue and profit**: compute room revenue from room nights and expected ADR; deduct distribution cost by channel for Net RevPAR; if service ratios are available, derive F&B (breakfast covers, restaurant revenue) and Spa for TRevPAR; if CPOR and fixed costs are available, compute expected GOP and GOPPAR.
8. **Flow-Through**: if the forecast is above plan, compute expected Flow-Through against the hotel's `Flow-Through` norms; if below the norm, flag 🔴 and name the costs to control.

### Three Scenarios
- **Base (most likely)**: from the steps above; used for official cost and staffing plans.
- **Best (optimistic)**: favorable assumptions the user agrees to (e.g. competitors selling out early, a major event, a marketing campaign outperforming); suggest upsell / ADR-raising options.
- **Downside (cautious)**: risk assumptions (bad weather, competitor discounting, rising cancellations); suggest immediate cost-cutting options for Accounting and Operations.

The user must confirm each scenario's assumptions; never invent assumption figures.

## B. Variance Analysis

1. Compare actuals with the locked forecast (ask which date the forecast was locked) by segment, channel, and forecast horizon.
2. Compute % variance and flag it against the hotel's `Forecast Variance` norm.
3. Identify the segments / channels with the largest variance, causes visible in the data, and method adjustments for next period.

## C. Prepare a Presentation for the GM / HoDs

1. Back each key figure with "evidence" (e.g. pickup over the last 7 days is x% faster than STLY, which room types competitors have closed).
2. Show the GOP / GOPPAR impact of each scenario, not just revenue.
3. Include the variance track record of previous months if available.
4. Run the pre-presentation checklist and report any item not met:
   - Have STR / CompSet rates been checked?
   - Have holidays and major events been reviewed?
   - Have cancellations (especially OTA) and wash been deducted?
   - Do the DOS and FOM agree with the numbers?

## Output

Forecast table (by period, segment, three scenarios) with OCC, ADR, RevPAR, Net RevPAR, TRevPAR, GOPPAR where computable; list of assumptions; alerts; and (if needed) a presentation summary.
