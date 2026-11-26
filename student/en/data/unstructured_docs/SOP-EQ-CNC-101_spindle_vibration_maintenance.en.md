# Standard operating procedure: spindle vibration and calibration for five-axis precision rotor grinder CNC-101

- **Document ID**: `DOC-SOP-CNC101`
- **Equipment**: `EQ-CNC-101` (Hamamatsu Precision Plant Line 1, `LINE-HMM-01`)
- **Alert codes**: `ALT-VIB-SPIKE` (abnormal spindle RMS vibration), `ALT-TMP-WARN` (rising spindle bearing temperature)
- **Related defect**: `DEF-DIM-01` (coaxiality or outside diameter out of tolerance)
- **Responsible function**: Equipment Maintenance (revised: 2026-02-01)

---

## 1. Calibration interval and assignment restrictions

Check positioning accuracy and spindle runout on the five-axis precision rotor grinder (`CNCGrinder`) using a laser interferometer every **90 days**.

- **If calibration is overdue**: When `days_since_calibration` exceeds **90 days**, manage the equipment as `DegradedWarning` (code `2`) or `MaintenanceHold` (code `3`).
- **Do not assign overdue equipment to Tier 1 work**: Do not assign equipment past its calibration interval to a work order linked to a `Tier1` customer order, including `Queued` work. If a queued work order is already assigned, switch immediately to `EQ-CNC-102` at Nagoya Plant 1 or `EQ-CNC-103` at Kitakyushu Plant, or complete calibration at least 48 hours before production starts.

## 2. Response to `ALT-VIB-SPIKE` (RMS vibration above 4.5 mm/s)

1. When the current grinding pass finishes, hold automatic feed at 0%. Inspect grinding wheel dressing and check for trapped chips on the toolholder taper.
2. Use field balancer `BAL-FIELD-02` to correct dynamic spindle imbalance at 12,000 rpm. Confirm that RMS vibration returns to **2.5 mm/s or less**.
3. Measure every unit processed during the incident with a coordinate measuring machine. If `DEF-DIM-01` is recorded, determine whether regrinding (`Rework`, `disposition_code = 2`) is permitted.
