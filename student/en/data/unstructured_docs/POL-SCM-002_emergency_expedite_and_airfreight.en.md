# Supply chain emergency response policy: alternative sourcing, interplant transfers, charter transport, and air freight

- **Document ID**: `DOC-POL-SCM002`
- **Scope**: Emergency response when `Tier1` sales orders (`erp#Order`) face a delivery delay risk (`AtRisk` or impact from an in-process `ScrapHold`).
- **Responsible functions**: Supply Chain and Corporate Planning (revised: 2025-11-01)

---

## 1. Escalation criteria

If either condition applies, convene an emergency review with Supply Chain, Manufacturing, Quality, and Sales **no later than five business days before `promised_ship_date`**. Decide whether to use alternative production or emergency transport.

1. A `Tier1` sales order has ERP status `AtRisk` (code `3`).
2. Even if an ERP sales order remains `InProduction` (code `2`), its linked MES work order (`mes#Order`) is on `QualityHold` (code `30`) or its inspection disposition is `ScrapHold` (code `3`), and the good quantity (`good_qty`) is below the order quantity (`order_qty`).

## 2. Priority of emergency response options

1. **First priority: Interplant line transfer.** Reserve capacity on healthy equipment within the company (for example, Nagoya's `EQ-VBF-205` when Hamamatsu's `EQ-VBF-204` is down). Allocate a qualified, conforming lot already in inventory (for example, `LOT-MAG-091`) and issue an additional emergency work order.
2. **Second priority: Emergency purchase from a qualified backup supplier.** If the primary supplier is on quality hold, request an emergency spot purchase or an earlier delivery for an in-transit purchase order from a qualified supplier with `is_qualified_backup = true`. For `MAT-MAG-04`, an example is `SUP-007`, Shinshu Magnet Materials.
3. **Third priority: Emergency charter or air freight.** If regular truck transport cannot meet the delivery date, arrange a dedicated charter or domestic air freight.

## 3. Approval authority for emergency costs and special handling

Obtain the following approval based on the **total value of affected sales orders (`order_amount_jpy`)** and the **estimated incremental emergency cost**.

| Affected order value or incremental cost | Final approver | Required evidence |
| --- | --- | --- |
| Affected order value **below JPY 30,000,000** and incremental cost **below JPY 1,000,000** | **Plant Manager** | Work order delay details and the alternative line schedule |
| Affected order value **at least JPY 30,000,000 but below JPY 90,000,000**, and incremental cost **below JPY 3,000,000** | **VP of Supply Chain** | Above, plus a financial comparison against SLA delay penalties |
| Affected order value **at least JPY 90,000,000** or incremental cost **at least JPY 3,000,000** | **Chief Operating Officer (COO)** | Above, plus the full list of affected customers, initial 8D draft, and Quality Assurance Director sign-off |

> **Cost-benefit rule**: As a general rule, approve emergency handling if the combined daily customer SLA delay penalties (`sla_penalty_jpy_per_day`) multiplied by the expected days of delay exceed the additional charter and overtime costs.
