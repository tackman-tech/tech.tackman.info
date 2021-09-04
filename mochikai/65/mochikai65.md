---
marp: true
---

# 今週の進捗

## 2021.08.14 モチ会 65 回

### tackman

---

# その① SinCUTで画風変換

SinCUTとは：単一画像×2枚でスタイル変換できるアルゴリズム

1. ドール写真→絵画風の変換
  - 今ひとつ上手くいっていない、思ったより画風変換先に引っ張られる
  - 変換元・先が構図レベルである程度近くないと厳しそうな印象
2. 風景写真→絵画風の変換
  - いまやってる
  - 人物系がダメでも、花や空などの背景ならいける仮説

---

# インスタンス費用を圧縮するための運用

- GCPのインスタンスはpoweroffコマンドで普通にインスタンスが終了する
  - これを利用して学習終了時自動的にインスタンスを落とすという発想
- ただし学習はコンテナ上で行っているので、ひとひねりが必要

```
# docker-comppose up -d でコンテナは立ち上がっている想定

ホスト側から
docker-compose exec ContainerName bash && sudo poweroff

コンテナ内で
python train.py && exit
```

---

# その②ドールのメイク仮完了

![height:350px](doll.png)

- 雰囲気を見てみたかったので、とりあえずアイをつけてボディに載せた
  - ボディが足りないので別の子の身体を借りてる
- 基本は問題なさそうだけど、いくらか要修正点あり
  - 眉をもうちょっときっちり描いてあげた方がよさそう
  - 銀髪前提なのでアイラインに白入れるのもアリ？