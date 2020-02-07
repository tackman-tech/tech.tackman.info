---
marp: true
---

# React と Three で画像成分ビジュアリゼーション

## 2020.02.08 モチ会 第 4 回

### tackman

---

# 実物

http://tackman.info/imanalyze/

## 概要

- 画像の色の簡易分析ツールが欲しかったので作った
- HSV での分布を可視化してる簡単なもの
- フロントエンド・画像処理・インフラの 3 部構成でお届けします

## 技術スタック

- TypeScript
- フロント： React / react-three-fiber
- バックエンド：nodejs / opencv4node
- インフラ：Cloud Run

---

# Part1 フロントエンド

---

# TypeScript

- Microsoft 謹製 AltJS(死語)
  - AltJS 継承戦争の最終勝者として戴冠
- C#とか好きだった人には肌に合うと思う
- 最近はほとんどの npm パッケージに TS 向け型情報があるので色々楽

---

# React

- JavaScript での GUI フレームワーク
  - 「Web アプリ」の範疇に収まらない広がりがある
- 詳細説明略。無限にネットに資料があるので適当にぐぐって

---

# Three.js

- JavaScript での 3D 描画ライブラリのデファクトスタンダード
- 同じく有名どころなので詳細説明略

---

# react-three-fiber (1)

- React 的なコンポーネント志向で Three.js が使える！
- React 自体と合わせて使うので、React 利用アプリケーションとの相性 ◎

```jsx
<Canvas
  style={{
    background: 'black',
    width: 800,
    height: 600,
    marginLeft: 'auto',
    marginRight: 'auto'
  }}
  camera={{ position: [0, 0, 20], fov: 40 }}
>
  <ambientLight />
  <pointLight position={[-10, 10, 10]} />
  <HsvSphere rgb={state.imageData?.rgb} hsv={state.imageData?.hsv} />
</Canvas>
```

---

# react-three-fiber (2)

## パフォーマンスは？

- 原理的にはそこまで遅くならなそう、触って気になる点はなかった
  - 毎フレーム Update などは React 再描画ロジックの外側で処理できる
  - とは言えゴリゴリのチューニングに耐えるかは不明
- 素人の雑設計でぐちゃぐちゃのコードよりはだいたい速くなるのでは

---

# react-three-fiber(3)

## 使い勝手は？

- 個人的には最高、ごちゃごちゃしがちな 3D コードがすっきりした
  - 生の Three.js の init 相当部分が React コンポーネントと JSX で書ける
- Hooks 利用の今どきの React スタイル
  - Hooks は新しい機能だけど、昔の State まわりより学習コスト低い
  - オブジェクト指向覚えなくていい
    - クラス定義も継承も全く使わなかった

## 将来性は？

- 2020.02 現在活発に開発されている

---

# Part2 Web アプリで画像処理

---

# JavaScript での画像処理ライブラリ

## 定番がない！！！

- Python なら脳死で Pillow 選んでおけばいいんだけど…

## OpenCV

- 牛刀感はあるけど仕方ないので OpenCV のラッパーを導入
- 候補は 2 つ：
  - OpenCV.js
  - opencv4nodejs

---

# OpenCV.js

- OpenCV 公式謹製、ブラウザ上での実行可能なビルド
- 先生、型情報が、ないです・・・
- ドキュメントもかなり雑、一部定数がパッケージに入ってない etc
- 自前ビルドは結構大変そう
  - WebAssembly を扱うのはいろいろ大変

まとめるとあまりに辛く、見送り

---

# opencv4nodejs 　(1)

TS の型もあって使いやすい！最高

```ts
(im: cv.Mat): { rgb: number[][][]; hsv: number[][][] } => {
    const rgb = (im
      .cvtColor(cv.COLOR_BGR2RGB)
      .getDataAsArray() as any) as number[][][];
    const hsv = (im
      .cvtColor(cv.COLOR_BGR2HSV)
      .getDataAsArray() as any) as number[][][];
```

VSCode だとマウスオーバーで型情報が表示されるぞ！

---

# opencv4nodejs (2)

## しかしブラウザでは使えない

- Native build をしている仕組み上ブラウザ向けにやれないとのこと
- そもそもブラウザ向けでないって作者が言い切ってる
  - - https://github.com/justadudewhohacks/opencv4nodejs/issues/292

```text
What you are referring to is opencv.js, which is a completely different project.
```

---

# どうしよう？

## 妥協してブラウザでの完結を諦めた

- opencv4nodejs を使う部分だけを分離、API 化
- Part3 へ続く

---

# Part3 アーキテクチャとインフラ

---

# opencv4nodejs の分離でやること増えた

## フロント

- 普通にビルドして静的サイトにデプロイ
- 画像 URL を API に投げる →API が OpenCV で処理した結果を返してくれる

## バックエンド（API）

- 実行用 Docker イメージを作り、Cloud Run で実行

---

# Cloud Run

- GCP のコンテナ実行サービス
- Cloud Functions 的に実行時間だけ課金
  - EC2 や Compute Engine 立ち上げっぱなしより大幅にお得
- 人類には早すぎた Kubernetes からアプリケーション開発者を救済してくれる
- まだ β 取れてないっぽいけど気にするな！
- GCP Container Registry にイメージ push してボタンぽちぽちでデプロイ終了
  - もちろんデプロイのコード化をやってもいい

---

# このアーキテクチャのダメなところ

- 大容量画像処理結果を JSON レスポンスで返している
  - 帯域の無駄遣い、潜在的パケ死
  - 800x600 程度の画像でレスポンスサイズが 2 桁 MB とかになる
    - JPEG ならこのサイズの画像は数百 KB くらい
- 本来ブラウザ上で完結する処理でバックエンドを使っている
- バックエンド利用による開発効率低下
  - それでも他の手段よりは開発スピードはマシと判断しました

---

# 改善方法は？

- 気合いで OpenCV.js で書く
  - TS で軟弱になった身体ではやりたくない
- OpenCV.js に型をつける
  - opencv4nodejs の型定義情報を流用すればワンチャン・・・？
  - いずれにしろ分量がめちゃくちゃ多く、気合が必要

どちらにしろ今回のアプリのためにやるにはオーバーエンジニアリング

---

# ついでのアイディアメモ

- HSV でレスポンスを返したい時、みなし RGB にして PNG や JPEG で圧縮をかければ帯域節約できるのでは？
  - 未検証
- 今回のアプリではそもそもフロントで画像デコードどうするのか問題があるので、採用しても問題解決しないです
  - まともな画像デコーダがなかったのも OpenCV 採用理由の 1 つ

---

# 感想

- TypeScript の文明を全身で感じた
- react-three-fiber 最高、3D アプリ作るなら次も使いたい
- OpenCV を使うのは今後 TypeScript でいいのでは？
  - 少なくとも C++や Python でやるよりは楽

---

# 参考文献

- https://github.com/react-spring/react-three-fiber
- https://github.com/justadudewhohacks/opencv4nodejs
- https://docs.opencv.org/master/d5/d10/tutorial_js_root.html
- https://cloud.google.com/run/docs/
