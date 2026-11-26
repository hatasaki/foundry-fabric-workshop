# Quality inspection standard: shipment and in-process nonconformance disposition for precision actuators

- **Document ID**: `DOC-QMS-004`
- **Products**: `PRD-ACT-900`, `PRD-ACT-650`, `PRD-ACT-400`, and all other servo actuator products
- **Defect codes**: `DEF-BOND-02`, `DEF-TRQ-04`, `DEF-DIM-01`
- **Responsible function**: Quality Assurance (revised: 2026-01-10)

---

## 1. Key inspection characteristics and acceptance criteria

| Inspection | Column | Pass (`DEF-00`) | Rework limit (`Rework=2`) | Immediate hold for scrap disposition (`ScrapHold=3`) |
| --- | --- | --- | --- | --- |
| Torque ripple | `torque_ripple_pct` | 2.0% or less | 2.1–3.0% | **Above 3.0% (`DEF-TRQ-04`)** |
| Brazed joint void area | `bond_void_area_pct` | 2.0% or less | Not permitted; the joint cannot be rebrazed | **Above 5.0% (`DEF-BOND-02`)** |
| Winding insulation resistance | `insulation_mohm` | At least 500 MΩ | 300–499 MΩ (dry and retest) | **Below 300 MΩ (`DEF-INS-01`)** |

## 2. Joint voids (`DEF-BOND-02`) and excessive torque ripple (`DEF-TRQ-04`) occurring together

- Do not rework a unit if `bond_void_area_pct` is **above 5.0%**; the magnet could detach at high speed. Move all such units to red-tag quarantine with `ScrapHold` (`disposition_code = 3`).
- If both `DEF-BOND-02` and `DEF-TRQ-04` are detected in work orders using the same equipment or magnet lot (`MAT-MAG-04`), treat it as a combined issue: vacuum or temperature excursions on the equipment side, and moisture or reduced coercivity on the lot side. Identify all in-process work orders using that same equipment and lot, **including sales orders still marked `InProduction=2` in ERP**, and block shipment.
- Do not normally request a concession for `Tier1` customers. Deliver only conforming lots remanufactured with alternative material lots and healthy equipment.
