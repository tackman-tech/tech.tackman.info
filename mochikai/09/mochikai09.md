---
marp: true
---

# StyleGAN実装 進捗報告

## 2020.03.14 モチ会 9回

### tackman

---

# 目次

- StyleGAN(2) について
- 進捗
- 実装のTips（頭出し）

---

# StyleGAN

- オリジナルはちょっと古い（2018年）けれど、2020年3月現在これより抜本的に新しいGANの手法は出ていないと言っていい
  - 画像生成勢の一部には「StyleGANでゲームセットだね」という雰囲気すらある
- 派生型は色々出ているので、基礎として抑える唯一の手法

---

# StyleGAN2

- StyleGANチーム謹製・StyleGANをいろいろ改善したやつ
- やれることもコンセプトもおおむねStyleGANと一緒と思っていい

---

# 今週やった進捗

- StyleGAN2の再現実装中
- 環境整えてモデルのコア部分を6割くらい書いたところ

## なんでそれやってるのか

- 公式実装は商業利用不可
- 野良実装もあるけど、GPL…
- 手を動かして理解も兼ねようと思った

---

# 実装Tips集

---

# PyTorch

- 深層学習ライブラリ、最大手の1つ
  - Chainerからフォークして最終的にChainerを吸収した
- TensorFlowとPyTorchでだいたい二分される世界
  - 研究者人気はPyTorchの方が高いと思う
  - 某プロジェクトで3人ほどディープ実装者が集まったところ、全員PyTorch派だったので流れでPyTorchがプロジェクトのデファクトになった

---

# PyTorch Lightning

まだそこまで使い込めてないので、現時点での感想です

- PyTorch向けフレームワーク
- 昨年出てきて急激に人気に
  - PyTorch公式のIgniteのクセが強すぎたせいな気がしてる
- ボイラープレート部分を吸収してくれるので、正しく「フレームワーク」

---

# VSCode Remote-Container

![](./architecture-containers.png "https://code.visualstudio.com/docs/remote/containersより引用")

- ネ申
- Microsoftの開発ツールを信じろ
- DXはだいたいMSとGoogleが強いよね

---

# 参考文献

- StyleGAN https://github.com/NVlabs/stylegan
- StyleGAN2 https://github.com/NVlabs/stylegan2
- PyTorch三国志（Ignite・Catalyst・Lightning） https://qiita.com/fam_taro/items/c32e0a21cec5704d9a92
- Developing inside a Container https://code.visualstudio.com/docs/remote/containers