<!-- code-to-doc-findings
{
  "schemaVersion": 1,
  "scope": "受注 CSV の取り込み（バッチ入口から取込サービスまで。画面は対象外）",
  "sourceCommit": "88a9088c407c3c4f7f06838a85126f9e9999edea"
}
-->
# Findings: 受注 CSV の取り込み

Agents add rows with Decision `pending`. Only people fill Decision (`adopted` or `rejected`), Reason and Decided by.

| ID | Claim | Evidence | Type | Decision | Reason | Decided by |
|---|---|---|---|---|---|---|
| F-001 | バッチ入口は認証処理の後に `BatchOrchestrator.execute` を呼び出す。 | [[source:wholesale-batch/src/main/java/jp/co/tsubame/wholesale/batch/BatchMain.java#L30-L54]] | fact | adopted | | |
| F-002 | 連続する同じ `external_key` の CSV レコードは1つの受注グループにまとめられる。 | [[source:wholesale-batch/src/main/java/jp/co/tsubame/wholesale/batch/OrderCsv.java#L38-L54]] [[source:wholesale-batch/src/main/java/jp/co/tsubame/wholesale/batch/OrderGroup.java#L108-L109]] | fact | adopted | | |
| F-003 | 行処理の `import-orders` 分岐は受注グループを `OrderImportService.importDraft` に渡す。 | [[source:wholesale-batch/src/main/java/jp/co/tsubame/wholesale/batch/BatchOrchestrator.java#L68-L78]] [[source:wholesale-batch/src/main/java/jp/co/tsubame/wholesale/batch/BatchOrchestrator.java#L136-L138]] [[source:wholesale-batch/src/main/java/jp/co/tsubame/wholesale/batch/RowTask.java#L28-L30]] [[source:wholesale-batch/src/main/java/jp/co/tsubame/wholesale/batch/service/BatchRowService.java#L45-L63]] | fact | adopted | | |
| F-004 | 同じ商品コードが受注グループ内に複数回出現した場合、そのグループは却下される。 | [[source:wholesale-batch/src/main/java/jp/co/tsubame/wholesale/batch/OrderGroup.java#L66-L83]] [[source:wholesale-batch/src/main/java/jp/co/tsubame/wholesale/batch/service/OrderImportService.java#L30-L34]] [[source:wholesale-batch/src/main/java/jp/co/tsubame/wholesale/batch/BatchOrchestrator.java#L136-L148]] [[source:wholesale-batch/src/main/java/jp/co/tsubame/wholesale/batch/service/BatchRunService.java#L72-L80]] | fact | adopted | | |
| F-005 | 取込サービスは新規受注の下書き保存を受注サービスに依頼する。 | [[source:wholesale-batch/src/main/java/jp/co/tsubame/wholesale/batch/service/OrderImportService.java#L60-L74]] | fact | adopted | | |
| F-006 | 商品コードが重複するグループの実行時の結果コードは未観察。許可を得た後、隔離されたデータベースで該当 CSV を投入し、`row-results` の結果コードを観察する。 | [[source:wholesale-batch/src/main/java/jp/co/tsubame/wholesale/batch/OrderGroup.java#L81-L82]] | unknown | adopted | | |
