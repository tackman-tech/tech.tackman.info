---
marp: true
---

# GCP上mastodonの最安構成を追求した話

## 2021.01.16 モチ会 48 回

### tackman

---

# 今週の進捗

### カード画像自動生成器

- CSV -> カード画像約60枚をnpm runで生成できる状態にした
- [モチ会第45回の発表参照](http://tech.tackman.info/mochikai/45/)

### TypeScript深層学習ライブラリ

- 名前決めた（重要なんですよ！！）

### 自鯖mastodon費用削減作業

- クーポン使い切ってから費用垂れ流し状態になっていた
- GCP上で、最小構成をどこまで安くできるかチャレンジ
- mastodon本体に1行も手を入れずに、どこまで安くあげられるか

---

# mastodonで動くもの

- PostgreSQL
- Redis
- Sidekiq
- Web server
  - Rails / HTTP server
  - Streaming server (node)

特にRailsまわりのコンピューティングリソースの使用効率はかなり悪く、つらい

---

# CloudSQL 最安インスタンスでPostgreSQL

- 月額$6くらい
- Webサーバ等と同居させる構成だともうちょっとケチれるけど流石に怖い
  - 最小プランですら本番環境で使うべきでないとGCP公式には言われている
- DBは最重要資源なので、ここで多少出費がかさむのはあきらめる

---

# Compute Engineで費用削減する方法

## Preemptive instance

- 通常価格の1/3程度で利用可能
- ただし頻繁に終了させられる
  - 試したところ、数時間〜半日に一回くらいはインスタンスが終了させられる

### Webサーバーとして使うための仕組み

- 邪道：定期的にインスタンスのstartスクリプトを別マシンから叩く
- 正道：Instance groupを作成して自動再起動可能にする

---

# Preemptive instanceを自動再起動

1. Instance templateを登録
2. Instance groupを作成
3. Managed stateful instanceとして1を登録
   1. 今回はスケールアウトしないので、インスタンス1台固定設定

Immutable Infrastructureの考え方を1台のインスタンスに適用するイメージ

---

# Preemptive instanceの可用性について

- 1台構成の場合、落ちるたびに1分程度サービスが止まる感じ
- お一人様インスタンスなので問題ないと判断

---

# Redisサーバー

- GCPのマネージドRedisがあるけど、最低価格が高いのでパス
- メモリ1GBのE2.microインスタンス
- Preemptiveで月に$2程度

---

# Sidekiqサーバー

- 典型的なmastodon安価構成だとWebサーバーと同居してるけど、分離した
- Preemptive E2.microインスタンス（$2/mon）
  - 本当はf1.microにしたかったけど、メモリが600MBだと不足する…

---

# Webサーバー

E2.microでHTTP+ストリーミングサーバーが乗る

## IPアドレス固定問題

- Redisやバックグラウンドジョブ（Sidekiq）と違い、外に晒すIPアドレスが必要
- Instance groupを使うとグローバルIPアドレスを固定できない
- 起動時にDNSサービスのAPIを叩いて、IPアドレス登録をすれば解決するが…
  - 未実装
  - 今回はCloudflareをDNS/CDNに使っていて、APIがあるのは確認済み

---

# ところでそれPleroma

- Pleromaとは：mastodon互換のElixir製サーバー
- PleromaではPostgreSQL以外は全部乗せで700MB程度のサーバーで動く、らしい
  - mastodonだとRedis込で2.5GBくらいメモリ食ってます

Pleromaにリプレースするだけで、サーバ費用が1/3程度になる可能性…？