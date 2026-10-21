---
name: bee-rm-group-displacement
description: Evaluate a hotel group or corporate contract / quote with Displacement Analysis - compare the group's net profit with the transient revenue it displaces, including operating cost, commission, and ancillary spend, and recommend accept / decline / renegotiate. Use when the user asks to "evaluate a group", "should we take this group", "displacement", "review the rate Sales proposed", "review a group contract", "corporate rate" (vi - "đánh giá đoàn", "có nên nhận đoàn này", "duyệt giá Sales đề xuất", "review hợp đồng đoàn").
---

# Revenue Manager - Group & Contract Displacement

Read and follow `${CLAUDE_PLUGIN_ROOT}/context/revenue-management.md` before starting.

The question to answer: does accepting this group / contract at the rate Sales proposes increase profit, or does it cost the chance to sell higher rates to transient guests?

## Required Information

Ask for anything missing; never fill it in yourself:
- Group: arrival / departure dates, rooms per night (by room type), proposed rate, terms (breakfast included, complimentary room ratio, agent commission), expected ancillary services (meetings, banquets, F&B), historical wash rate for this type of group.
- Transient for those dates: OTB, unconstrained transient forecast (transient demand without the group), expected ADR by channel, distribution cost.
- Costs: the hotel's `CPOR` and `OTA commission` norms (see "Norms and Targets" in the shared rules), plus the extra cost of groups (group breakfast, wear and tear), F&B cost from food cost %.
- Corporate contracts: also the committed annual volume, applicable seasons, blackout dates.

Search Workflows for an internal group contract approval procedure; if there is one, follow it.

## Calculation (per night, then summed over the stay)

1. **Rooms available for the group** = Total rooms − Transient OTB − Remaining transient forecast. Group rooms above this number **displace** transient guests.
2. **Group net profit** = Group room revenue (after wash) + Ancillary revenue − Commission − Variable cost (CPOR × rooms + cost of ancillary services).
3. **Lost transient profit** = For the displaced rooms: (Expected transient ADR − distribution cost − CPOR) × rooms, plus average transient ancillary spend if data is available.
4. **Adjacent nights**: if the group blocks nights so that long-stay transient guests spanning those nights are lost, state it (qualitatively if there is no data).
5. **Net benefit** = (2) − (3). Also compute the **break-even rate**: the lowest group room rate for which net benefit ≥ 0.

Use the formulas in Metrics if Bee Intelligence has them; state every assumption (wash, transient demand, ancillary spend).

## Conclusion

- **Accept** if net benefit is clearly positive (especially when the group fills quiet midweek nights).
- **Negotiate** if it loses money only on some peak nights: recommend a minimum rate per night, a date change, fewer rooms on peak nights, or added services (banquets, meetings).
- **Decline** if there is a net loss and no strategic benefit the user has stated.

This is a recommendation for the RM / DOS / GM to decide.

## Output

Per-night table (group rooms, displaced rooms, group profit, lost transient profit, net benefit), stay total, break-even rate, conclusion, and assumptions.
