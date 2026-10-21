---
name: bee-rm-data-audit
description: Audit hotel revenue data quality (data governance) - Market Code, Source Code, Rate Code, miscoded guest segments, and sync checks between PMS, Channel Manager, and CRS. Use when the user asks to "check the data", "audit codes", "data governance", "system audit", "reconcile PMS and channel manager", "booking in the wrong segment", or when segment / channel figures look abnormal (vi - "kiểm tra dữ liệu", "audit mã", "đối chiếu PMS và channel manager", "booking sai phân khúc").
---

# Revenue Manager - Data Audit

Read and follow `${CLAUDE_PLUGIN_ROOT}/context/revenue-management.md` before starting.

"Garbage in, garbage out": the forecast and every pricing and cost decision are wrong if codes are entered wrong.

The assistant only detects and reports. Never fix data in the PMS / CM / CRS itself; if a tool can edit data, confirm each change with the user first.

## Preparation

1. Confirm the hotel and audit period (suggested default: bookings created / modified yesterday for the daily check, the whole month for the monthly check; the user must agree).
2. Get the **standard code list** (Market, Source, Rate Code, segments, and the mapping rules between them) from the Bee Intelligence Glossary / Workflows or a file from the user. If there is no standard list, stop the code checks, tell the user, and suggest adding the list to the Glossary; never invent rules.
3. Data sources: the booking list from the PMS; for sync checks, CM / CRS exports for the same period.

## Checks

1. **Missing / invalid codes**: bookings missing a Market / Source / Rate Code or using a code not in the list.
2. **Conflicting codes**: combinations that break the mapping rules (e.g. a group Rate Code with a transient Market Code; OTA Source with no commission; Corporate rate with no company attached).
3. **Anomalies**: bookings whose rate, LOS, or room count don't fit the assigned segment (e.g. 10 rooms under one name on the same dates tagged as Transient).
4. **PMS - CM - CRS sync** (if data is available): bookings present in one system but not another, differences in rate / dates / room type / status, channel rate codes not matching PMS codes.

## Output

1. Summary: bookings checked, errors by type, estimated impact (room nights / revenue misclassified by segment).
2. Detailed error table: booking ID, error, current value, correct value per the rules (if determinable), rationale.
3. Remediation: fix each booking (by someone with access), and if the same error recurs often, suggest retraining or adding the rule to Workflows.
