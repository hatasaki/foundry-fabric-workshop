# Customer SLA addendum summary: quality and delivery commitments for Tier 1 strategic customers

- **Document ID**: `DOC-SLA-TIER1`
- **Customers**: `CUS-001`, `CUS-002`, `CUS-003`, `CUS-004` (Tier 1 strategic accounts)
- **Responsible functions**: Enterprise Sales and Legal (revised: 2026-04-01)

---

## 1. Delivery penalties and initial 8D reporting by Tier 1 customer

| Customer ID | Customer name | Late-delivery penalty (`sla_penalty_jpy_per_day`) | Initial 8D deadline after a major process nonconformance or shipment hold is detected (`requires_8d_hours`) | Special terms |
| --- | --- | --- | --- | --- |
| `CUS-001` | Japan Robotics Systems Co., Ltd. | **JPY 800,000 per day** | **Within 48 hours of detection** | Transfer to qualified equivalent equipment `EQ-VBF-205` at Nagoya Plant 1 (`PLT-NGO`) requires prior notification only. |
| `CUS-002` | Tokai Mobility Electronics Co., Ltd. | **JPY 650,000 per day** | **Within 48 hours of detection** | Cleanroom product `PRD-ACT-650` is qualified for material from alternative magnet supplier `SUP-007`. |
| `CUS-003` | Kansai Semiconductor Equipment Co., Ltd. | **JPY 900,000 per day** | **Within 24 hours of detection (strict)** | Semiconductor lithography and handling lines have the strictest deadline. Start the clock when the work order enters `QualityHold` on the shop floor, even before ERP status is updated. |
| `CUS-004` | Capital Region Medical Robotics Co., Ltd. | **JPY 700,000 per day** | **Within 48 hours of detection** | The customer does not accept products processed on equipment with overdue calibration. |

## 2. Coordination between Sales and Production Control

- For `CUS-003` (Kansai Semiconductor Equipment), Article 9, paragraph 2 requires an initial containment report (D1–D3) **within 24 hours of the timestamp when `QualityHold` (code `30`) was recorded in MES**. Missing that deadline can incur an audit penalty even before delivery becomes late.
- Retrieve sales orders whose ERP `order_status_code` is `3 (AtRisk)` **and** orders still at `2 (InProduction)` that are linked through the graph to affected equipment or nonconforming material lots.
