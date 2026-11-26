# 部門横断ヒアリングメモ: 現場用語のゆらぎとデータサイロ課題（オントロジー設計インプット資料）

- **文書ID**: `DOC-GLOSSARY`
- **作成目的**: 全社AIエージェント（Fabric Data Agent / Microsoft Foundry）導入に向けた業務ドメイン整理メモ
- **ヒアリング対象**: 営業部・生産管理部・製造課・品質保証部・設備保全課（作成日: 2026-05-01）

---

## 1. 現場で起きている「同じ言葉なのに違う意味」「違う言葉なのに同じ意味」問題

### (A) 同名概念の衝突：「オーダー（Order）」問題
- **営業・SCM部門**が「オーダー」「注文」と言うときは、顧客から受けた **ERP受注（`erp_sales_orders`、`SO-2026-xxx`）** を指す。
- **工場・製造・品質部門**が「オーダー」「ワーク」「指図」「工番」と言うときは、ラインへ流す **MES製造指図（`mes_work_orders`、`WO-2026-xxxx`）** を指す。
- しかも両方のテーブルの主キー列名が `order_id` になっているため、AIにただ「問題のあるオーダーを出して」と聞くと、受注と製造指図を混同したり片方しか見なかったりする。
  - **検討事項**: 受注ドメイン（`erp`）と製造実行ドメイン（`mes`）をサブオントロジー（Namespace）で分け、`erp#Order` と `mes#Order` として明確に区別したい。

### (B) 部門・担当者ごとの呼び方のゆらぎ（同義語 / Synonym 候補）
- **設備 (`Equipment`)**: 現場では「マシン」「号機」「加工機」「炉」「設備資産」「Asset」と呼ばれる。
- **顧客受注 (`erp#Order`)**: 「受注」「セールスオーダー」「顧客注文」「SO」「Sales Order」。
- **製造指図 (`mes#Order`)**: 「製造指図」「作業指示」「ワーク」「工番」「指図書」「WO」「Work Order」。
- **部材ロット (`MaterialLot`)**: 「資材ロット」「磁石ロット」「入荷ロット」「バッチ」「Lot」。
- **品質検査 (`QualityInspection`)**: 「出荷検査」「工程検査」「QC記録」「不適合検査」「Inspection」。
- **サプライヤー (`Supplier`)**: 「仕入先」「取引先メーカー」「ベンダー」「外注先」「Vendor」。

### (C) 数値コード・単位のブラックボックス問題（メタデータ / Metadata 候補）
テーブルのカラムだけではAIが意味を誤解しやすい項目：
- `erp_sales_orders.order_status_code`: `0`=キャンセル(Cancelled), `1`=受注確定(Confirmed), `2`=製造中(InProduction), `3`=納期リスク(AtRisk), `4`=出荷済(Shipped)
- `mes_work_orders.wo_status_code`: `10`=投入待ち(Queued), `20`=加工中(Running), `30`=品質保留(QualityHold), `40`=完了(Completed)
- `mes_equipment.operating_state_code`: `1`=正常稼働(NormalRunning), `2`=劣化注意(DegradedWarning), `3`=保全停止保持(MaintenanceHold)
- `mes_quality_inspections.disposition_code`: `1`=合格(Pass), `2`=手直し再加工(Rework), `3`=廃棄・隔離保留(ScrapHold)
- `iot_equipment_alerts.severity_code`: `1`=情報(Info), `2`=警告(Warning), `3`=重大異常(Critical)
- センサー計測値の単位: `chamber_or_spindle_temp_c` は摂氏温度（`celsius`）、`vibration_rms_mm_s` は振動速度実効値（`mm/s`）、`vacuum_pressure_pa` は絶対圧力パスカル（`Pa`、数値が大きいほど真空度が悪化＝リークしている点に注意！）。

### (D) 暗黙の業務ルール（Business Rules 候補）
ベテランの頭の中や紙のSOPにしかなく、SQLスキーマには書かれていない判断基準：
1. **真空ろう付け異常隔離ルール**: ろう付け炉で温度 `835℃` 超または真空度 `5.0 Pa` 超のアラートが `15分` 以上続いた設備で処理中の製造指図は、品質保留（`QualityHold`）として扱わなければならない。
2. **校正期限超過ガードレール**: 校正周期（`calibration_cycle_days`）を超過している設備（`days_since_calibration > calibration_cycle_days`）は、`Tier1` 顧客向け受注の製造指図に割り当ててはならない。
3. **希土類磁石の吸湿制限ルール**: 重要度 `CRITICAL` の磁石部材（`MAT-MAG-04`）で吸湿量 `moisture_ppm` が `150 ppm` を超えるロットは工程投入禁止であり、175 ppm 超は再生不可・返品対象である。
4. **Tier 1 納期リスクエスカレーションルール**: `Tier1` 顧客の受注に紐づく製造指図で `QualityHold (30)` または `ScrapHold (3)` が発生している場合、ERP側ステータスが `2 (InProduction)` のままでも実質納期リスク案件として扱い、出荷予定日の5営業日前までに代替設備振替または緊急輸送の決裁を開始しなければならない。
