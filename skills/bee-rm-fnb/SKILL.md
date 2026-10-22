---
name: bee-rm-fnb
description: Optimize hotel F&B revenue and profit with a Revenue Management mindset - Menu Engineering (Stars, Plowhorses, Puzzles, Dogs), RevPASH by outlet and time slot, in-house guest capture rate, upselling / packages, and cost control (actual vs theoretical food cost, waste, labor cost, inventory, centralized purchasing). Use when the user asks for "menu analysis", "menu engineering", "RevPASH", "capture rate", "increase restaurant revenue", "food cost is high", "reduce waste", "F&B cost", "actual vs theoretical cost" (vi - "phân tích menu", "tăng doanh thu nhà hàng", "food cost cao", "giảm lãng phí", "chi phí F&B").
---

# Revenue Manager - F&B Revenue & Cost

Read and follow `${CLAUDE_PLUGIN_ROOT}/context/revenue-management.md` before starting.

Determine which task the user needs: A - Menu Engineering, B - RevPASH, C - Capture Rate and cross-selling, D - cost control. Ask if unclear. Always confirm the outlet (restaurant, bar, room service, banquet), data period, and source (POS, inventory, accounting).

With large POS / inventory data or several hotels, delegate the calculations of A (steps 1-3), B (step 1), C (step 1), and D (the indicators) to `bee-rm-analyst` (see "Delegation" in the shared rules); make the recommendations in the main conversation.

## A. Menu Engineering

Needed: items sold in the period with quantities, selling prices (before VAT, confirm), and item cost from standard recipes.

1. Per item: contribution margin (CM) = selling price − item cost; share of quantity sold.
2. Thresholds (Smith & Kasavana method, unless the user wants another):
   - High popularity if quantity share ≥ (1 / number of items) × 70%.
   - High CM if CM ≥ the CM weighted by quantity sold.
3. Classify and recommend:

| Group | Profile | Action |
|---|---|---|
| Stars | High CM, popular | Keep quality, place prominently on the menu |
| Plowhorses | Low CM, popular | Review portions / item cost, raise price slightly |
| Puzzles | High CM, slow-selling | Rename / restyle, train staff to recommend |
| Dogs | Low CM, slow-selling | Remove or replace |

Analyze food and beverage separately if their margins differ a lot. Point out item / drink pairs often ordered together (from invoice data) to suggest combos.

## B. RevPASH

Needed: revenue by hour (or time slot), seats, opening hours; if available, covers and seating time.

1. RevPASH by outlet × day of week × time slot; with seat occupancy and average spend per cover (if available) to tell whether low RevPASH comes from few guests or low spend.
2. Peak hours: recommend faster service / table turnover. Off-peak hours: recommend creating demand (happy hour, afternoon tea, small events, office lunch, delivery).

## C. Capture Rate and Cross-Selling

1. Capture rate by meal / outlet, by in-house segment or market (if the data can be joined with the PMS).
2. Recommend: vouchers in the check-in welcome message, front desk introducing / pre-selling dinner, half-board / F&B credit packages by room type, a chain-wide loyalty program. Estimate the extra revenue if capture rate rises x points (x chosen by the user).
3. Upselling: suggest guest conversation scripts and an incentive based on upsell revenue if the user wants.

## D. Cost Control

1. **Food Cost %** actual (from stock count: opening + purchases − closing, ± transfers, staff meals) vs theoretical (standard recipes × quantities sold). If the variance exceeds the hotel's `Actual vs theoretical cost variance` norm: identify the categories / items with the largest variance, suggest checking portions, receiving (weight loss on fresh goods), leakage.
2. **Waste**: summarize the waste log by cause (spoiled, expired, prep error, returned by guest), item / ingredient; suggest FIFO, portion adjustment, full ingredient use.
3. **Labor Cost %**: by outlet and day; suggest scheduling by forecast room occupancy and groups, cross-training for lean off-peak operation.
4. **Inventory**: inventory turnover, slow-moving items, par levels if the user wants.
5. **Centralized purchasing** (multi-hotel chain): items to consolidate into framework contracts based on chain-wide volume.

## Output

Analysis tables for the parts done, a KPI table (Food Cost %, Labor Cost %, Waste Rate, Inventory Turnover, RevPASH, Capture Rate) against the hotel's norms (see "Norms and Targets" in the shared rules), recommendations with estimated impact on F&B revenue / profit. If the user wants, build a spreadsheet template for tracking actual vs theoretical cost.
