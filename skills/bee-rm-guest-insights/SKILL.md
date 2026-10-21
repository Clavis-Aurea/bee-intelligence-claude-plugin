---
name: bee-rm-guest-insights
description: Analyze hotel guest profiles and behavior to drive pricing and distribution strategy - by nationality / source market (lead time, channel), age, length of stay (LOS), room type preference, use of other services (F&B, Spa), combined into guest personas, and measure TRevPAR / GOP contribution by segment. Use when the user asks for "guest analysis", "guest profile", "guest persona", "which nationalities", "lead time by market", "LOS", "which segment brings the most GOP", "design a package" (vi - "phân tích khách", "chân dung khách hàng", "khách nước nào", "phân khúc nào mang lại GOP cao nhất", "thiết kế gói combo").
---

# Revenue Manager - Guest Insights

Read and follow `${CLAUDE_PLUGIN_ROOT}/context/revenue-management.md` before starting.

Goal: turn guest data into pricing and distribution strategy, optimizing the guest's share of wallet, not just selling a room.

## Preparation

Confirm: hotel, data period, sources (booking / guest profile data from the PMS, POS invoices for services), and the user's main question. Ask which fields are available (nationality, age, channel, room type, LOS, ancillary spend); never infer guest attributes from data that isn't there (e.g. don't guess nationality from names or age from channel).

Analyze aggregated group data only. Don't list personal information about individual guests unless the user explicitly asks and has the right to it.

## Analysis Angles

Do the angles the user needs that have data:

| Angle | Analysis | Strategy ideas |
|---|---|---|
| Nationality / source market | Room nights, ADR, lead time, booking channel, cancellation rate by market | Early Bird for long-lead-time markets; open / close channels by target market; point-of-sale (POS) pricing if channels support it |
| Age / generation | Room type, channel (mobile / desktop), ancillary spend by age group | Packages by group (experience staycation, family retreat); allocate ads to each group's media channels |
| Length of stay (LOS) | LOS distribution by segment, day of week | Stay Longer Save More (lower CPOR per night); MLOS on peak dates |
| Room type | Occupancy by room type, segments willing to pay to upgrade | Yielding by room type, front-desk upsell script (`bee-rm-pricing`) |
| Other services | F&B / Spa / bar capture rate by segment, market, hour | Dynamic packaging (half-board, F&B credit), adjust menu / opening hours (`bee-rm-fnb`) |

## Personas and Profit Contribution

1. Combine the angles into the 2-5 most important guest personas (e.g. market × age × LOS × room type × preferred services), only from combinations with enough data; state the sample size of each persona.
2. For each persona / segment: room revenue, ancillary revenue, TRevPAR contribution, and GOP contribution if costs are available (use the method in `bee-rm-gop-optimization` part B).
3. Identify the segment contributing the most GOP and recommend how to attract more of it.

## Output

Tables for each angle analyzed, the guest personas, a strategic impact table (factor → affected indicator → RM action), and the data limitations (missing fields, small samples).
