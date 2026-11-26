# Cross-functional interview notes: inconsistent terminology and data silos (ontology design input)

- **Document ID**: `DOC-GLOSSARY`
- **Purpose**: Summarize business domains for the introduction of enterprise AI agents (Fabric Data Agent / Microsoft Foundry).
- **Interviewed functions**: Sales, Production Control, Manufacturing, Quality Assurance, and Equipment Maintenance (created: 2026-05-01)

---

## 1. How teams use the same word differently or different words for the same thing

### (A) Same name, different concepts: the "order" problem

- When **Sales or Supply Chain** says "order" or "customer order," it means an **ERP sales order** (`erp_sales_orders`, `SO-2026-xxx`).
- When **plant, manufacturing, or quality teams** say "order," "job," "work instruction," or "job number," they mean a **MES work order** released to the line (`mes_work_orders`, `WO-2026-xxxx`).
- Both tables use `order_id` as the primary key. If someone asks an AI agent to "show the problem orders," it may confuse sales and work orders or look at only one.
  - **Design proposal**: Use distinct namespaces for sales orders (`erp`) and manufacturing execution (`mes`), yielding `erp#Order` and `mes#Order`.

### (B) Terms that vary by team or operator (synonym candidates)

- **Equipment (`Equipment`)**: machine, unit, processing machine, furnace, equipment asset, asset.
- **Customer sales order (`erp#Order`)**: order, sales order, customer order, SO.
- **Work order (`mes#Order`)**: production instruction, work instruction, job, job number, work order sheet, WO, work order.
- **Material lot (`MaterialLot`)**: materials lot, magnet lot, incoming lot, batch, lot.
- **Quality inspection (`QualityInspection`)**: final inspection, in-process inspection, QC record, nonconformance inspection, inspection.
- **Supplier (`Supplier`)**: supplier, manufacturing partner, vendor, subcontractor.

### (C) Numerical codes and units are not self-explanatory (metadata candidates)

These columns are easy for an AI agent to misinterpret without context:

- `erp_sales_orders.order_status_code`: `0`=Cancelled, `1`=Confirmed, `2`=InProduction, `3`=AtRisk, `4`=Shipped.
- `mes_work_orders.wo_status_code`: `10`=Queued, `20`=Running, `30`=QualityHold, `40`=Completed.
- `mes_equipment.operating_state_code`: `1`=NormalRunning, `2`=DegradedWarning, `3`=MaintenanceHold.
- `mes_quality_inspections.disposition_code`: `1`=Pass, `2`=Rework, `3`=ScrapHold (hold for scrap disposition and segregation).
- `iot_equipment_alerts.severity_code`: `1`=Info, `2`=Warning, `3`=Critical.
- Sensor units: `chamber_or_spindle_temp_c` is temperature in `celsius`, `vibration_rms_mm_s` is RMS vibration velocity in `mm/s`, and `vacuum_pressure_pa` is absolute pressure in `Pa`. A higher pressure means a poorer vacuum and can indicate a leak.

### (D) Unwritten business rules (business rule candidates)

Some decision rules exist only in experienced staff members' knowledge or paper SOPs, not in the SQL schema:

1. **Vacuum brazing excursion quarantine**: Treat work orders being processed on a brazing furnace as `QualityHold` if an alert for temperature above `835 °C` or pressure above `5.0 Pa` lasts at least `15 minutes`.
2. **Overdue calibration guardrail**: Do not use equipment past its calibration cycle (`days_since_calibration > calibration_cycle_days`) for work orders tied to `Tier1` sales orders.
3. **Rare-earth magnet moisture limits**: `CRITICAL` magnet material `MAT-MAG-04` with `moisture_ppm` above `150 ppm` must not enter production. Above 175 ppm, do not recondition the lot; return it.
4. **Tier 1 delivery-risk escalation**: If a work order tied to a `Tier1` sales order is on `QualityHold (30)` or has a `ScrapHold (3)` inspection, treat it as a delivery risk even when ERP remains `2 (InProduction)`. Start approval for alternative equipment or emergency transport no later than five business days before the planned ship date.
