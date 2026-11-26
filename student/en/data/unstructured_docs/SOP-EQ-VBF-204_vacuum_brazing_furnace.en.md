# Standard operating procedure: vacuum brazing furnaces VBF-204 and VBF-205, abnormal conditions and maintenance

- **Document ID**: `DOC-SOP-VBF204`
- **Equipment**: `EQ-VBF-204` (Hamamatsu Precision Plant Line 2, `LINE-HMM-02`) and `EQ-VBF-205` (Nagoya Plant 1 Line 2, `LINE-NGO-02`)
- **Alert codes**: `ALT-VAC-LEAK` (degraded chamber vacuum), `ALT-TMP-HIGH` (brazing-zone overtemperature)
- **Responsible functions**: Production Engineering and Equipment Maintenance (revised: 2026-01-15)

---

## 1. Normal operating parameters and control limits

When vacuum brazing rotor cores for `PRD-ACT-900` (high-precision hollow servo actuator HA-900) and `PRD-ACT-650` (low-profile cleanroom actuator CA-650), observe these limits to prevent oxidation of the silver-copper braze rings (`MAT-BRZ-05`) and reduce voids.

| Parameter | Normal range | Warning threshold | Critical shutdown threshold |
| --- | --- | --- | --- |
| Chamber temperature (`chamber_or_spindle_temp_c`) | 790.0–815.0 °C | Above 820.0 °C | **Above 835.0 °C** |
| Chamber absolute pressure (`vacuum_pressure_pa`) | 0.8–2.5 Pa | Above 3.5 Pa | **Above 5.0 Pa** |
| Duration of continuous excursion (`duration_minutes`) | — | Under 10 minutes | **Quarantine the lot immediately at 15 minutes or more** |

## 2. Initial response to `ALT-VAC-LEAK` or `ALT-TMP-HIGH`

1. **Hold the interlock as soon as the batch finishes.** If the temperature remains above `835.0 °C` or pressure above `5.0 Pa` for **15 minutes or more** during heating, wait for nitrogen purge cooling to finish to avoid cracking the rotor by rapid cooling. Then set the equipment to `MaintenanceHold` (code `3`).
2. **Segregate in-process work orders.** Set work orders (`mes#Order`) in the affected batch and those processed during the preceding 2 hours to `QualityHold` (code `30`). Route them for ultrasonic flaw detection and X-ray void inspection.
3. **Identify and replace the responsible component.** Degradation of the fluororubber vacuum seal on the main chamber door flange accounts for 85% of cases where pressure exceeds `5.0 Pa`.
   - **Specified replacement part**: `PART-SEAL-KF40-VT` (high-vacuum Viton O-ring kit; spare-parts location `HMM-MNT-B12`).
   - **Check the thermocouple protection tube as well.** When `ALT-TMP-HIGH` also occurred, compare drift in the main control Type R thermocouple (`PART-TC-R850`) with a reference instrument.

## 3. Return to service and alternative equipment

- After replacing the seal, run a helium leak test with an empty furnace (leak rate `1.0 × 10^-8 Pa·m³/s` or less) and three-point temperature-distribution calibration. Do not resume production until **both the Equipment Maintenance Manager and the Quality Assurance Manager sign the return-to-service record**.
- If recovery will take **12 hours or more** and Tier 1 sales orders are affected, ask Production Control for an emergency interplant transfer to the healthy equivalent furnace **`EQ-VBF-205`** at Nagoya Plant 1. Verify that its calibration is current and its absolute pressure has been confirmed at `2.0 Pa` or lower.
