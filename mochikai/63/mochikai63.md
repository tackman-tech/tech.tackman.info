---
marp: true
---

# 今週の進捗

## 2021.07.17 モチ会 63 回

### tackman

---

# 今週(までに)やったこと

## 画像変換実験（CUT）

- Contrastive Unpaired Translation
  - https://github.com/taesungp/contrastive-unpaired-translation
- この中に1枚画像でやれるSinCUTという手法があったので回してる途中

---

# CUTについて

- image-to-image synthesis手法の一種
  - 例：ドメインA（ウマ）の画像を、ドメインB（シマウマ）に変換するなど
- やれることはこの分野で有名なCycleGANなどと基本的に一緒
  - CUTの方が新しくて、性能面で上（と主張されている）
- 画像1枚×画像1枚に適用できるSinCUTという手法があるのも特徴
  - 通常数千〜数万の画像群同士で変換するので（CycleGAN等）

2つ目のドメイン画像データセットが用意不要なので、とりあえずSinCUTから

---

# 深層学習 on GCP

- 当初ローカルでやろうとしたが、画像生成の段階でGPUメモリ不足が判明
- 学習が手元マシンで回ってしまったので割と予想外だった
  - 大抵の深層学習アルゴリズムは学習時の方が計算資源を食う
- T4搭載のインスタンスを作って回してる
  - 一月使い続けると3万円くらいかかるので、腰が重くなりがち・・・
  - ちなみにGPUインスタンスは使用前にGPU割当申請が必要
    - 経験上ほぼ10分以内に許可されますが
  - 自宅にNVIDIA Teslaのグラフィックカード欲しい

結果は来週以降

---

# NVIDIA Tesla T4

https://www.nvidia.com/en-us/data-center/tesla-t4/

- 新し目の世代のGPU計算用グラフィックカード
- GDDR 16GB搭載
  - GeForce 3070でメモリは8GBくらい
- GCPだとなぜか古いGPUより安めなのでありがたく使わせてもらってる

GeForce 3090が高騰していたので売却したら欲しくなる、人生

---

# その他

- 7/24 北海道ボドゲ博3.0に参加します
  - https://psykorokinesis.wixsite.com/hokbdgexpo
  - 荷造りとかしてました