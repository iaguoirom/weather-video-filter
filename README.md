# Weather-Based Video Filter

気象条件と動画ファイル名の撮影日時を照合し、条件に合う動画候補をCSVとして出力するためのJupyter Notebook集です。

固定カメラで大量に撮影した動画から、気温・風速・時間帯などの時系列データを使って、目視確認する候補を絞り込む用途を想定しています。

> English summary: Jupyter notebooks for filtering timestamped videos using weather conditions, and for exporting short review clips. They only read source videos and never copy, move, or delete them.

## 含まれるノートブック

| ファイル | 用途 |
|---|---|
| `weather_video_filter.ipynb` | 気象データ・日の出日没時刻・動画ファイル名を照合し、動画候補をCSVに出力する |
| `video_clipper.ipynb` | 指定した動画の必要な時間区間を、目視確認用の短い動画として書き出す |

## できること

`weather_video_filter.ipynb` は、次の順番で候補動画を抽出します。

1. 気象CSVから日時・気温・風速を読み込む。
2. 日の出・日没時刻を使い、夜間のデータを除外する。
3. 気温・風速の閾値で対象時刻を絞り込む。
4. 動画ファイル名から撮影日時を取得する。
5. 気象時刻と動画時刻を指定した許容時間内で照合する。
6. 該当動画の一覧をCSVに保存する。

動画ファイルのコピー・移動・削除は行いません。

## 動作環境

- Python 3.10 以降
- Jupyter Notebook または JupyterLab
- Windows / macOS / Linux

必要なライブラリは以下でインストールできます。

```bash
pip install -r requirements.txt
```

Jupyterをまだ使える状態にしていない場合は、次も実行してください。

```bash
pip install jupyterlab
jupyter lab
```

## 使い方

1. リポジトリをダウンロードまたはクローンする。
2. `weather_video_filter.ipynb` をJupyterで開く。
3. ノートブックの「設定を変更する」セルで、入力ファイル・動画フォルダ・抽出条件を書き換える。
4. 上から順にセルを実行する。
5. 出力されたCSVの動画候補を、必要に応じて `video_clipper.ipynb` で短く切り出して目視確認する。

## 必要な入力

### 気象CSV

デフォルト設定は、先頭6行が説明行で、以下の列を含むCSVを想定しています。

| 項目 | デフォルトの列番号（左端=0） |
|---|---:|
| 日時 | 0 |
| 気温 | 1 |
| 風速 | 4 |

CSVの形式が異なる場合は、ノートブック冒頭の `WEATHER_SKIP_ROWS`、`WEATHER_COLUMNS`、`WEATHER_ENCODING`、`WEATHER_DATETIME_FORMAT` を変更してください。

### 日の出・日没CSV（任意）

昼間だけに絞る場合、国立天文台の月別CSV形式を想定しています。最初の2行を見出しとして除き、1列目を日、2列目を日の出時刻、6列目を日の入り時刻として読みます。

時間帯で絞り込まない場合は、設定セルで `DAYTIME_ONLY = False` にしてください。

### 動画ファイル名

撮影日時を含む次の形式をファイル名から読み取れます。

```text
CAM1_20250427_085800.mp4
CAM1-20250427-085800.mp4
CAM1 20250427 085800.mp4
CAM1_20250427085800.mp4
```

## 出力

`weather_video_filter.ipynb` は、以下の列を含むCSVを出力します。

| 列 | 内容 |
|---|---|
| `video_path` | 動画フォルダからの相対パス |
| `filename` | 動画ファイル名 |
| `video_datetime` | ファイル名から取得した撮影日時 |
| `weather_datetime` | 対応づけられた気象時刻 |
| `temperature_c` | 気温 |
| `wind_speed_m_s` | 風速 |
| `time_difference_minutes` | 動画と気象時刻の差（分） |

## 注意点

- CSVの列構成・文字コード・日時形式は気象データ提供元によって異なります。最初に少量のデータで読み込み結果を確認してください。
- 気象条件に一致することは、特定の行動が起きたことを意味しません。出力結果は**動画を目視確認するための候補リスト**です。
- 施設名、撮影地点、個体情報、動画そのものなど、共有できない情報をリポジトリに追加しないでください。
- `video_clipper.ipynb` はOpenCVによる再エンコードで動画を出力し、音声トラックは保持しません。

## License

MIT License. See [LICENSE](LICENSE).
