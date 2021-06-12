---
marp: true
---

# 今週の進捗

## 2021.06.12 モチ会 59 回

### tackman

---

# やったこと

## 北海道ボドゲ博のサークルカット

- 7.24に札幌で開催されるボドゲ即売会
- off-boxとJ.C.クリエイツが合同出展予定だよ

## Fine Art Datasetづくり

### ...のための準備

- 保存用バックエンド設計
  - Cloud Storage, Firestore
- 保存用ツール（Webフロントエンド）作成
- 保存処理のバックエンド実装

---

# 全体設計（概要）

1. WebページのURLリストを入力するWebフロントエンド
   1. Firestoreに保存
2. 保存されたWebページURLから、画像のURLを取得・画像を保存するFunctions
   1. 画像自体はCloud Storageへ

こまごまとハマった結果、微妙に当初構想とは違う形での実装になった

---

# ハマり① jsdom がブラウザで動かない

- jsdom: HTMLファイルをパースして要素取得できるライブラリ
- 当初はブラウザ上でWebページを取得、画像URLまで取得予定だった
- jsdom内部で利用しているHTTPまわりがnode.js専用だった模様

致し方ないのでCloud Functions上で実行することに

---

# ハマり② Functionsからのアクセス拒否をされる

- Functions上で動かしてみると、Webサイト側からブロックをされる
  - わかる（あり得るとは思っていた）
  - 同じ取得処理をローカルマシンからやると通ったので、IPアドレスあたりでブロックされていると推定

当座は手元のマシンで取得処理を動かすことに

- 一応自宅に休眠中サーバマシンはあるので、開発機に依存しない構成は可能
- 発熱があるのであまり夏場に動かしたくはない

---

# ハマり？③ Firebase Client SDKの謎バグ？を踏む

- FirestoreへのPUTに失敗する
  - エラーメッセージすら出さず死ぬ（Promiseがrejectされてない）
- 適当なGETをしてからPUTをすると成功する
  - 割と意味が分からない
  - issueはいま洗ってます
- 流石に意味不明過ぎるので継続調査予定

---

# 参考文献

- 北海道ボドゲ博 https://psykorokinesis.wixsite.com/hokbdgexpo
- Firebase クライアントSDKのissues
  - https://github.com/firebase/firebase-js-sdk/issues
- jsdom https://github.com/jsdom/jsdom