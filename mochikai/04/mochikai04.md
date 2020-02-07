---
marp: true
---

# ReactとThreeで画像成分ビジュアリゼーション

## 2020.02.08 モチ会 第4回 

### tackman

---

# 実物

http://tackman.info/imanalyze/

## 概要

- 画像の色の簡易分析ツールが欲しかったので作った
- HSVでの分布を可視化してる簡単なもの

## 技術スタック

- TypeScript
- フロント： React / react-three-fiber
- バックエンド：nodejs / opencv4node
- インフラ：Cloud Run

---

# Part1 フロントエンド

---

# TypeScript

- Microsoft謹製AltJS(死語)
  - AltJS継承戦争の最終勝者として戴冠
- C#とか好きだった人には肌に合うと思う
- 最近はほとんどのnpmパッケージにTS向け型情報があるので色々楽

---

# React

- JavaScriptでのGUIフレームワーク
  - 「Webアプリ」の範疇に収まらない広がりがある
- 詳細説明略。無限にネットに資料があるので適当にぐぐって

---

# Three.js

- JavaScriptでの3D描画ライブラリのデファクトスタンダード
- 同じく有名どころなので詳細説明略

---

# react-three-fiber (1)

- React的なコンポーネント志向でThree.jsが使える！
- React自体と合わせて使うので、React利用アプリケーションとの相性◎

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

---
# react-three-fiber (2)

## パフォーマンスは？

- 原理的にはそこまで遅くならなそう、触って気になる点はなかった
  - 毎フレームUpdateなどはReact再描画ロジックの外側で処理できる
  - とは言えゴリゴリのチューニングに耐えるかは不明
- 素人の雑設計でぐちゃぐちゃのコードよりはだいたい速くなるのでは

## 使い勝手は？

- 個人的には最高、ごちゃごちゃしがちな3Dコードがすっきりした
- Three.jsとfiberの書き方の対応関係に身体を慣らす必要はあり

## 将来性は？

2020.02現在活発に開発されている

---

# Part2 Webアプリで画像処理

---

# JavaScriptでの画像処理ライブラリ

## 定番がない！！！

- Pythonなら脳死でPillow選んでおけばいいんだけど…

## OpenCV

- 牛刀感はあるけど仕方ないのでOpenCVのラッパーを導入
- 候補は2つ：
  - OpenCV.js
  - opencv4nodejs

---

# OpenCV.js

- OpenCV公式謹製、ブラウザ上での実行可能なビルド
- 先生、型情報が、ないです・・・
- ドキュメントもかなり雑、一部定数がパッケージに入ってない etc
- 自前ビルドは結構大変そう
  - WebAssemblyを扱うのはいろいろ大変

まとめるとあまりに辛く、見送り

---

# opencv4nodejs　(1)

TSの型もあって使いやすい！最高

```
(im: cv.Mat): { rgb: number[][][]; hsv: number[][][] } => {
    const rgb = (im
      .cvtColor(cv.COLOR_BGR2RGB)
      .getDataAsArray() as any) as number[][][];
    const hsv = (im
      .cvtColor(cv.COLOR_BGR2HSV)
      .getDataAsArray() as any) as number[][][];
```

VSCodeだとマウスオーバーで型情報が表示されるぞ！

---

# opencv4nodejs (2)

## しかしブラウザでは使えない

- Native buildをしている仕組み上ブラウザ向けにやれないとのこと
- そもそもブラウザ向けでないって作者が言い切ってる
  - - https://github.com/justadudewhohacks/opencv4nodejs/issues/292

```
What you are referring to is opencv.js, which is a completely different project.
```

---

# どうしよう？

## 妥協してブラウザでの完結を諦めた

- opencv4nodejsを使う部分だけを分離、API化
- Part3へ続く

---

# Part3 アーキテクチャとインフラ

---

# opencv4nodejsの分離でやること増えた

## フロント

- 普通にビルドして静的サイトにデプロイ
- 画像URLをAPIに投げる→APIがOpenCVで処理した結果を返してくれる

## バックエンド（API）

- 実行用Dockerイメージを作り、Cloud Runで実行

---

# Cloud Run

- GCPのコンテナ実行サービス
- Cloud Functions的に実行時間だけ課金
  - EC2やCompute Engine立ち上げっぱなしより大幅にお得
- 人類には早すぎたKubernetesからアプリケーション開発者を救済してくれる
- まだβ取れてないっぽいけど気にするな！
- GCP Container Registryにイメージpushしてボタンぽちぽちでデプロイ終了
  - もちろんデプロイのコード化をやってもいい

---

# このアーキテクチャのダメなところ

- 大容量画像処理結果をJSONレスポンスで返している
  - 帯域の無駄遣い、潜在的パケ死
  - 800x600程度の画像でレスポンスサイズが2桁MBとかになる
    - JPEGならこのサイズの画像は数百KBくらい
- 本来ブラウザ上で完結する処理でバックエンドを使っている
- バックエンド利用による開発効率低下
  - それでも他の手段よりは開発スピードはマシと判断しました

---

# 改善方法は？

- 気合いでOpenCV.jsで書く
  - TSで軟弱になった身体ではやりたくない
- OpenCV.jsに型をつける
  - opencv4nodejsの型定義情報を流用すればワンチャン・・・？
  - いずれにしろ分量がめちゃくちゃ多く、気合が必要

どちらにしろ今回のアプリのためにやるにはオーバーエンジニアリング

---

# 感想

- TypeScriptの文明を全身で感じた
- react-three-fiber最高、3Dアプリ作るなら次も使いたい
- OpenCVを使うのは今後TypeScriptでいいのでは？
  - 少なくともC++やPythonでやるよりは楽

---

# 参考文献

- https://github.com/react-spring/react-three-fiber
- https://github.com/justadudewhohacks/opencv4nodejs
- https://docs.opencv.org/master/d5/d10/tutorial_js_root.html
- https://cloud.google.com/run/docs/
