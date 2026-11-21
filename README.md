# org.worldbank.api

`api.worldbank.org` の World Development Indicators API から取得した、全世界のエネルギー指標 snapshot を保全する DataLad dataset です。統合用 datom は `etzhayyim/global-energy-datoms` が生成します。

各 raw JSON は API 応答をそのまま保存し、`raw/source-catalog.edn` に指標コード・観測年・SHA-256 を記録します。WDI 指標ごとに最新利用可能年が異なるため、消費側は `:energy/year` を必ず条件に含めます。

## 時系列 snapshot（2000–2023）

`raw/wdi-series/` は `:sources` の 8 指標に 10 指標を加えた計 18 指標の 2000–2023 年時系列です。各ファイルは `https://api.worldbank.org/v2/country/all/indicator/<CODE>?format=json&date=2000:2023&per_page=20000` の応答ボディを無改変で保存したもので、ファイル名は API が返す指標タイトル由来の短い kebab-case 名に `-2000-2023` を付けた命名です（全 18 件とも 1 ページに収まったため `.pN` ファイルはありません）。対応する catalog 行は `raw/source-catalog.edn` の `:series` にあり、`:sources` の行と `raw/wdi/` の単年 snapshot は不変です。

追加の 10 指標（`raw/wdi-series/` のファイル名つき）は次のとおりです。

- `EG.ELC.ACCS.ZS` — Access to electricity (% of population) — `access-to-electricity-2000-2023.json`
- `EG.USE.PCAP.KG.OE` — Energy use (kg of oil equivalent per capita) — `energy-use-per-capita-2000-2023.json`
- `NY.GDP.MKTP.CD` — GDP (current US$) — `gdp-current-usd-2000-2023.json`
- `SP.URB.TOTL.IN.ZS` — Urban population (% of total population) — `urban-population-share-2000-2023.json`
- `SP.DYN.TFRT.IN` — Fertility rate, total (births per woman) — `fertility-rate-2000-2023.json`
- `SH.DYN.MORT` — Mortality rate, under-5 (per 1,000 live births) — `under-5-mortality-rate-2000-2023.json`
- `SE.SEC.ENRR` — School enrollment, secondary (% gross) — `secondary-enrollment-gross-2000-2023.json`
- `SL.UEM.TOTL.ZS` — Unemployment, total (% of total labor force) (modeled ILO estimate) — `unemployment-total-2000-2023.json`
- `SH.XPD.CHEX.GD.ZS` — Current health expenditure (% of GDP) — `current-health-expenditure-share-2000-2023.json`
- `AG.LND.FRST.ZS` — Forest area (% of land area) — `forest-area-share-2000-2023.json`

## 単年 snapshot の 2026-11-21 更新セット

`:sources` の 8 指標は World Bank 側で改訂が続いているため、同じ 8 指標・同じ観測年を 2026-11-21 に再取得した dated set を `raw/wdi/2026-11-21/` に保存しました（ファイル名は `raw/wdi/` と同一、応答ボディ無改変）。既存ファイルは energy projection が参照しているため上書きせず、`raw/source-catalog.edn` の `:sources` と `:series` は不変です。新セットの catalog 行は `:snapshots` にあり、要求 URL・`:sha256`・`:bytes`・`:retrieved-at`・ページング（`:total` 266、`:pages` 1）を記録しています。`bin/verify-source-catalog.cljk` は新行の sha・バイト数・ページングを検証し、同ディレクトリの `DIFF.edn` の存在を要求します。

新旧の差分は `raw/wdi/2026-11-21/DIFF.edn` に実測で記録しています（キーは `country.id`。`countryiso3code` は所得グループ集計で空のため）。全 8 ファイルで新集計 `F2`（Fragility, Conflict, and Violence situations）が増え、265 → 266 レコードになりました。両方にいる 265 件のうち値が変わった件数は次のとおりです。

| 指標 | 観測年 | 値が変わった件数 |
| --- | --- | --- |
| `EG.ELC.RNEW.ZS`（renewable electricity share） | 2021 | 77 |
| `EG.ELC.FOSL.ZS`（fossil electricity share） | 2022 | 129 |
| `EG.ELC.NUCL.ZS`（nuclear electricity share） | 2022 | 44 |
| `EG.FEC.RNEW.ZS`（renewable final energy share） | 2022 | 0 |
| `SP.POP.TOTL`（population） | 2022 | 0 |
| `NY.GDP.PCAP.CD`（GDP per capita） | 2022 | 7 |
| `SP.DYN.LE00.IN`（life expectancy） | 2022 | 2 |
| `IT.NET.USER.ZS`（internet users） | 2022 | 0 |

null と非 null の間の変化はありませんでした。最大 20 件の新旧ペア例は `DIFF.edn` の `:examples` にあります。

このセットに切り替えるかどうかは owner の判断です。projection は引き続き `:sources` のファイルを参照しています。
