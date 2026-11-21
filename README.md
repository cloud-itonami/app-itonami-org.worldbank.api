# org.worldbank.api

`api.worldbank.org` の World Development Indicators API から取得した、全世界のエネルギー指標 snapshot を保全する DataLad dataset です。統合用 datom は `etzhayyim/global-energy-datoms` が生成します。

各 raw JSON は API 応答をそのまま保存し、`raw/source-catalog.edn` に指標コード・観測年・SHA-256 を記録します。WDI 指標ごとに最新利用可能年が異なるため、消費側は `:energy/year` を必ず条件に含めます。

## 時系列 snapshot（2000–2023）

`raw/wdi-series/` は `:sources` と同じ 8 指標の 2000–2023 年時系列です。各ファイルは `https://api.worldbank.org/v2/country/all/indicator/<CODE>?format=json&date=2000:2023&per_page=20000` の応答ボディを無改変で保存したもので、既存ファイル名の末尾年を `-2000-2023` に置き換えた命名です（全 8 件とも 1 ページに収まったため `.pN` ファイルはありません）。対応する catalog 行は `raw/source-catalog.edn` の `:series` にあり、`:sources` の行と `raw/wdi/` の単年 snapshot は不変です。
