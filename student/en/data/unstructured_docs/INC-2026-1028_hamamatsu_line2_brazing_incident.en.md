# Historical incident investigation (8D record): VBF-204 vacuum seal degradation and joint voids on Hamamatsu Line 2

- **Document ID**: `DOC-INC-1028`
- **Incident date**: 2026-02-28 (permanent corrective action registered: 2026-03-05)
- **Site and equipment**: Hamamatsu Precision Plant, `LINE-HMM-02` / `EQ-VBF-204`
- **Responsible functions**: Quality Assurance and Hamamatsu Manufacturing

---

## 1. Incident summary and root cause

In late February 2026, `EQ-VBF-204` raised `ALT-VAC-LEAK` (peak pressure: 8.2 Pa). The shop floor reset the alert and kept the furnace running. A later lot showed a bond void area above 6% (`DEF-BOND-02`).

Disassembly identified two contributing factors:

1. More than 90 days had elapsed since calibration and seal inspection. The flange O-ring (`PART-SEAL-KF40-VT`) had cracks caused by heat hardening.
2. The magnet lot was relatively moist. Outgassing during heat-up exceeded the exhaust pump's capacity, driving chamber pressure above 5.0 Pa.

## 2. Lessons for the ontology and monitoring rules

- The sales order status (`erp_sales_orders`) stays at `InProduction (2)` until the overnight batch or a manual update by Production Control. **ERP alone cannot immediately show delivery risks caused by shop-floor `QualityHold (30)`.**
- Use graph relationships to trace equipment alerts (IoT) through equipment, work orders, and material lots (MES) to products and sales orders (ERP). Standardize the process so it identifies the full impact immediately, **including risks on sales orders that ERP has not yet flagged**.
