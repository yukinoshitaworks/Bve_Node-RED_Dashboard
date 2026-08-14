# Bve_Node-RED_Dashboard

[Bve_MQTT_IO](https://github.com/yukinoshitaworks/Bve_MQTT_IO)(BVE Trainsim 用 MQTT 連携プラグイン)が発行する MQTT トピックを可視化・操作するための Node-RED ダッシュボードです。速度・距離程・時刻・パイロットランプ・ATS-P表示灯などをブラウザ上のダッシュボードで確認でき、力行/制動/レバーサをダッシュボードから逆にBVEへ送ることもできます。
開発中のため画面のレイアウト等荒削りな箇所があります。Pull RequestやIssue歓迎です。

## 対応関係

```
BVE (BveEX_20251119)  <--MQTT-->  Node-RED (本フロー)  -->  ブラウザダッシュボード
```

- BVE → MQTT → Node-RED: `bve/time` `bve/location` `bve/speed` `bve/pilot` `bve/panel` `bve/am` `bve/sound` を受信して表示
- Node-RED → MQTT → BVE: `bve/power` `bve/brake` `bve/reverser` をダッシュボードの操作に応じて送信

## 必要環境

- [Node.js](https://nodejs.org/)(Node-RED が動作するバージョン。LTS推奨)
- [Node-RED](https://nodered.org/) 本体
- MQTT ブローカー(例: [Mosquitto](https://mosquitto.org/))が、BVE側([Bve_MQTT_IO](https://github.com/yukinoshitaworks/Bve_MQTT_IO))と同じものに到達できること

## セットアップ手順

1. Node-RED をインストールし、ユーザーディレクトリ(既定 `~/.node-red`)を用意する

   ```
   npm install -g --unsafe-perm node-red
   ```

2. 本リポジトリの `package.json` を Node-RED のユーザーディレクトリにコピー(または依存内容をマージ)し、必要な追加ノードをインストールする

   ```
   cd ~/.node-red
   npm install
   ```

   `package.json` に含まれる主な依存ノード:

   | パッケージ | 用途 |
   |---|---|
   | `node-red-dashboard` | ダッシュボードUI(ゲージ・LED・スイッチなど)本体 |
   | `node-red-contrib-ui-led` / `ui-led2` | 表示灯(P電源・ATS-Pなど)のLED表示 |
   | `node-red-contrib-ui-level` | 速度計などのレベル表示 |
   | `node-red-contrib-ui-clock` / `ui-digital-clock` | 時刻表示 |
   | `node-red-contrib-mqtt-broker` | MQTTブローカー接続設定の補助 |
   | `node-red-contrib-uibuilder` | カスタムUI構築用(拡張したい場合) |
   | `node-red-contrib-web-worldmap` | 地図表示(本フローの主目的とは別の汎用ウィジェット) |
   | その他(`jma-weather` `openweathermap` `stoptimer` `timerswitch` など) | このNode-RED環境の他用途フロー向け。BVE連携には必須ではない |

3. Node-RED を起動する

   ```
   node-red
   ```

4. ブラウザで `http://localhost:1880` を開き、右上メニュー「読み込み」→ 本リポジトリの `flows.json` の内容を貼り付け(または読み込み)してインポートする
5. インポートした MQTT ノード(`mqtt in` / `mqtt out`)を開き、接続先ブローカーのホスト・ポートを自分の環境に合わせて設定する(BVE側 [Bve_MQTT_IO](https://github.com/yukinoshitaworks/Bve_MQTT_IO) と同じブローカーを指定)
6. 「デプロイ」を押し、`http://localhost:1880/ui` でダッシュボードを開く

## ダッシュボード表示内容(フロー内の主なウィジェット)

| ウィジェット | 対応トピック | 種類 |
|---|---|---|
| 距離程 | `bve/location` | テキスト表示 |
| km/h | `bve/speed` | テキスト/レベル表示 |
| 現在時刻 | `bve/time` | テキスト表示 |
| 知らせ灯 | `bve/pilot` | LED表示 |
| 力行 | `bve/power` | スイッチ/スライダー(BVEへ送信) |
| 制動 | `bve/brake` | スイッチ/スライダー(BVEへ送信) |
| レバーサ | `bve/reverser` | スイッチ(BVEへ送信) |
| P電源 / パターン接近 / ブレーキ動作 / ブレーキ開放 / ATS-P | `bve/panel` | LED表示(各パネル配列要素に対応) |

## 注意事項

- `flows_cred.json`(暗号化された認証情報)、`settings.js`(管理者認証・セキュリティ設定)、`node_modules` はこのリポジトリに含めていません。環境ごとに個別に用意してください
- MQTTブローカーのホスト/ポートはフロー内の `mqtt-broker` 設定ノードで管理されています。インポート後、必ず自分の環境に合わせて設定し直してください
- ダッシュボード内には本プロジェクト(BVE連携)と直接関係のない汎用ウィジェット(天気・世界地図・タイマーなど)も同居しています
