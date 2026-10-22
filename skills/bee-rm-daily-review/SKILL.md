---
name: bee-rm-daily-review
description: Daily business review for a hotel Revenue Manager - yesterday's results vs forecast (OCC, ADR, RevPAR), pickup and cancellations for the next 14-30 days, OTB vs forecast, CompSet rates, recommended rate / inventory / restriction changes, and a briefing for Front Office and Housekeeping. Use when the user asks for a "daily report", "daily review", "pickup today", "night audit", "morning briefing", "how did yesterday perform" (vi - "báo cáo ngày", "pickup hôm nay", "briefing sáng", "đánh giá hiệu suất hôm qua").
---

# Revenue Manager - Daily Review

Read and follow `${CLAUDE_PLUGIN_ROOT}/context/revenue-management.md` before starting.

Goal: read the "pulse" of the last 24 hours and recommend immediate adjustments for the next 14-30 days.

## Preparation

Confirm (or take from saved decisions in memory or the Glossary), and ask for anything missing:
- Which hotel (or the whole chain, in which case report each hotel, then the total).
- Report date (default: yesterday for results, today for OTB; still state the dates used).
- Look-ahead window: 14 or 30 days.
- Data source: a Bee Intelligence data source, Night Audit / OTB / Pickup / rate shop files from the user, or both.

If the user wants only one part (e.g. pickup only), do only that part.

For several hotels, after Preparation, delegate parts 1, 2, and 4 per hotel (or batch) to `bee-rm-analyst` as described in "Delegation" in the shared rules; do parts 3, 5, and 6 in the main conversation from the returned tables.

## Part 1 - Yesterday's Performance (Night Audit)

1. Get yesterday's OCC, ADR, RevPAR, room revenue (and total revenue if available).
2. Compare with that day's forecast and budget (and STLY if available). Show absolute and % variance.
3. State the main causes if visible in the data (no-shows, late cancellations, walk-ins, upgrades, groups arriving short). Never speculate about causes not in the data.

## Part 2 - Pickup and Cancellations

1. For each stay date in the look-ahead window: current OTB, net 24h pickup, new bookings, cancellations, and OTB vs forecast.
2. Flag unusual dates: sudden large pickup, mass cancellations (name the channel / segment / group if available), or OTB deviating from forecast beyond the hotel's `OTB vs Forecast deviation` norm (see "Norms and Targets" in the shared rules; if none is set, ask).
3. If there is no OTB by snapshot date, say pickup cannot be calculated and ask whether the user has the previous day's snapshot file.

## Part 3 - External Events

Ask about, or use what the user has provided on, local events, weather, cancelled flights, etc. in the look-ahead window. List them with affected dates and source. Never invent events.

## Part 4 - CompSet and Distribution

1. If rate shop data is available: table of the hotel's rate vs each competitor by date (same room type, same conditions), the gaps, and dates where competitors closed out or dropped rates unusually (possibly a flash sale).
2. If there is no rate shop data, skip this part and say so.

## Part 5 - Recommended Actions

Based on parts 2-4, recommend by stay date (use the `bee-rm-pricing` skill for detailed rate calculation if needed):
- **Rate (BAR)**: raise for dates filling faster than forecast; lower or run a last-minute deal for dates with serious vacancy. Never recommend a rate below the agreed floor.
- **Channel inventory**: for dates above the hotel's `Channel shift OCC` norm, consider closing low-rate room types on high-commission channels to optimize Net RevPAR.
- **Restrictions**: MLOS / CTA / CTD for peak dates, weekends, holidays; lift restrictions on weak dates.

Each recommendation has: date, action, supporting figures, expected impact, priority.

## Part 6 - Operations Briefing (Daily Briefing)

A short briefing for Front Office (FO) and Housekeeping (HK) for today and tomorrow: arrivals / departures / stayovers, expected OCC, group arrivals / departures, VIP guests (only what is in the data), staffing notes. Ask whether there is a briefing workflow / template in Bee Intelligence and use it if there is.

## Output

1. A 3-5 line summary: whether yesterday met forecast, which dates need action, the top-priority recommendation.
2. The tables from parts 1, 2, 4.
3. The list of recommendations (part 5).
4. The briefing (part 6), kept separate so the user can send it right away.
