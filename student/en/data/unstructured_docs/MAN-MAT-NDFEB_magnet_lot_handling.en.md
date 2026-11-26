# Materials engineering standard: storage, moisture control, and alternative sourcing for sintered neodymium magnets (MAT-MAG-04)

- **Document ID**: `DOC-MAN-NDFEB`
- **Material**: `MAT-MAG-04` (high-temperature-resistant sintered neodymium magnet rotor core)
- **Suppliers**: Primary supplier `SUP-003` (Tokai Rare Earth Precision Co., Ltd.); qualified backup supplier `SUP-007` (Shinshu Magnet Materials Co., Ltd.)
- **Responsible functions**: Materials Engineering and Purchasing (revised: 2026-01-20)

---

## 1. Moisture limits at receipt and before production

If sintered neodymium magnets (NdFeB) have substantial surface oxidation or adsorbed moisture when loaded into a vacuum brazing furnace, the heat releases moisture and hydrogen. This can degrade the chamber vacuum, cause poor braze wetting (`DEF-BOND-02`), and worsen torque ripple through thermal demagnetization (`DEF-TRQ-04`).

- **Production entry limit**: The Karl Fischer moisture measurement (`moisture_ppm`) must be **150 ppm or less**.
- **Lots above 150 ppm**: Do not put a lot with `moisture_ppm > 150` (for example, a lot marked `QUARANTINE_SUSPECT`) directly into brazing or assembly under any circumstances.
  - **151–175 ppm**: Degas and dry in a vacuum baking furnace for `6 hours at 180 °C`. Remeasure afterward. The lot may enter production conditionally only if it measures 100 ppm or less.
  - **Above 175 ppm**: Intergranular corrosion may already have progressed. **Do not recondition by degassing. Return the entire lot to the supplier** and switch to a different lot immediately.

## 2. When to switch to the qualified backup supplier (`SUP-007`)

If the quality score (`quality_rating_score`) for primary supplier `SUP-003` (Tokai Rare Earth Precision) falls **below 80**, or a delivered lot has a moisture nonconformance **above 175 ppm**, take these actions under Article 14 of the master purchasing agreement:

1. Suspend new receipts from `SUP-003` and initiate a `ConditionalReview` audit.
2. For `Tier1` work, give priority to the qualified backup supplier `SUP-007` (Shinshu Magnet Materials Co., Ltd.; standard lead time: 7 days). The supplier is IATF16949-qualified for the same drawing. Prioritize its accepted on-hand lot `LOT-MAG-091` and the in-transit purchase order `PO-2026-315` / `LOT-MAG-095`.
