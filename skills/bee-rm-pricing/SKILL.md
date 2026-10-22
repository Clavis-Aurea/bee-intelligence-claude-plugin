---
name: bee-rm-pricing
description: Recommend hotel room pricing strategy - demand-based dynamic pricing (BAR), GOP floor rate and ceiling rate, seasonal rate grid, room category yielding, restrictions (MLOS, CTA, CTD), opening / closing room types by channel to optimize Net RevPAR, and promotions (early bird, last-minute, stay longer). Use when the user asks "what rate should we set", "raise / lower rates", "floor rate", "peak season pricing", "MLOS", "close OTA", "pricing by room type", "promotion" (vi - "nên đặt giá bao nhiêu", "tăng / giảm giá", "giá sàn", "giá mùa cao điểm", "đóng kênh OTA", "khuyến mãi").
---

# Revenue Manager - Pricing & Inventory

Read and follow `${CLAUDE_PLUGIN_ROOT}/context/revenue-management.md` before starting.

The assistant only **recommends** rates and settings. Never change them in the PMS / Channel Manager. If a tool can change rates, confirm each change with the user first.

## Preparation

Confirm: hotel, stay date range, room types, channels, current rates (BAR and related rate codes), OTB / forecast for those dates, CompSet rates (if available), internal pricing policy (search Workflows: rate approval procedure, allowed change limits).

## Types of Recommendation

### 1. GOP Floor Rate and Ceiling Rate
- Floor rate per channel = CPOR / (1 − commission % − payment fee %), using the hotel's `CPOR`, `OTA commission`, and `Payment fee` norms (see "Norms and Targets" in the shared rules). Never sell below the floor unless the user deliberately runs a "Loss Leader" and says so.
- Ceiling rate: what the market accepts (from CompSet data, historical highest ADR on similar dates); ask the user about internal rules.

### 2. Dynamic Pricing (daily BAR)
- Compare OTB / pickup with the forecast and historical booking curve. Dates filling faster than plan: recommend raising. Seriously slow dates: recommend a controlled decrease or last-minute deal (never below the floor).
- Consider same-date CompSet rates (the hotel's `ARI` norm, if set).
- Prefer raising ADR over raising OCC where possible: each extra room sold adds variable cost (CPOR), while a rate increase flows almost entirely into GOP. When recommending, compare the two options by expected GOP.

### 3. Seasonal Rate Grid (Seasonality)
- Define seasons (peak / shoulder / low) from historical OCC and ADR by week / month and the event calendar. For each season: floor rate, reference BAR, ceiling rate.

### 4. Room Category Yielding
- If the lower category always sells out early while the higher one stays empty: recommend narrowing the rate gap to steer guests to upgrade themselves, or use the higher category for special packages; include a front-desk upsell rate.

### 5. Channel Inventory
- When forecast OCC is high (above the hotel's `Channel shift OCC` norm): consider closing low-rate room types on high-commission channels, keeping them for the website / hotline. Show Net RevPAR before and after.
- Check rate parity across channels if data is available.

### 6. Restrictions
- MLOS for peak dates, weekends, holidays so long-stay guests aren't lost; CTA / CTD for shoulder dates around peaks. Lift restrictions on weak-demand dates.
- State the risk: overly tight restrictions lose one-night bookings.

### 7. Promotions
- Early Bird for long-lead-time markets; Last-minute for dates still empty close in; Stay Longer Save More (lower CPOR per night from fewer cleanings / less laundry). Check that promotions don't stack and don't push the net rate below the floor.

## Output

Recommendation table: date / room type / channel, current rate or setting, recommendation, rationale, expected impact (OCC, ADR, Net RevPAR, or GOP), priority. Add a list of assumptions the user needs to confirm.
