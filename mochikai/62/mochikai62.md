---
marp: true
---

# 今週の進捗

## 2021.07.03 モチ会 62 回

### tackman

---

# やったこと

### プログレッシブJPEGクイズ用Webツールを作成

https://progressivejpegquiz.web.app/

- 古代ネット回線でちょっとずつ解像度が上がっていくさまを模した早押しクイズ
  - それが何か分かった段階で回答をする
- クイズ部ではこれをDiscord画面共有を使ってやっている

(実演)

---

# プ(略)クイズツールの構成

https://github.com/tackman/progressivejpegquiz

- バックエンドなし、ブラウザ上の単一ページのみ
- Canvas上で縮小→拡大をするシンプルな実装
- 一応Next.js利用
- photon-rs(Rust製画像処理ライブラリ、WebAssembly対応)利用

当初はRustで画像拡縮処理を書いて自分でWebAssembly化するつもりだったけれど、photon-rsがnpm上でWASMビルド済みパッケージを配布していたことが判明

- Rust + WebAssembly + webpack のビルドバトルをして少し詳しくなった
  - けれど実は必要なかったというオチ

---

# Fineart データセット クレンジング

- 目視で確認した結果、そこそこゴミデータが混ざっていた
- 明らかなゴミを目視で取り除くことにした
- 100枚あたり2分のペース、10K枚なので約3時間あれば作業完了見込み
- 進捗：ベンチマーク兼ねながらやった最初の300枚だけ・・・

3時間くらいやればできるけれどなかなか気が重くなる

---
# 雑記：今週末の重賞レース

春シーズン終わったし出資馬のデビューも遅そうなので秋までお馬さんはお休みしようかなと思っていたけど、カレンチャン(ウマ娘)にサマースプリントシリーズから目を話したらダメだぞって言われたのを思い出したのでこの夏もお馬さん見ます

### CBC賞（芝1200m, 小倉）

- ヨカヨカ（珍しい九州産、3歳牝馬）推しなのでここ軸で馬連流します
- 普通に一番人気っぽいし、出走馬の中で地力も実際高そう
- 今年のCBC賞は小倉開催なのでこれはもうヨカヨカでよか

### ラジオNikkei賞

- 3歳馬なにもわからん
- ロードカナロア子を信じるくらいしか方針が立たない
