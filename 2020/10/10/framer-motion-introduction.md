# React / Framer MotionでCSSアニメーション

ReactでCSSアニメーションをするライブラリ、Framer Motionの紹介です。
https://www.framer.com/motion/

## TL; DR

- 自由度を損なわずに宣言的にCSSアニメーションを記述できるライブラリだよ
- いきなり作り込みたくない勢でもヘルパー使うだけでそれっぽいのが作れるよ

## 本稿のターゲット
React及びHooksの知識がある読者にFramer Motionの概要の紹介をして、導入するかの判断の助けになることを狙っています。利用法についての解説はほぼしません。

## 特徴
サンプルコード[^1]を見るだけで、ライブラリの方向性はある程度分かると思います。

```tsx
import {
  motion,
  useMotionValue,
  useTransform,
} from "framer-motion"

export const MyComponent = () => {
  const x = useMotionValue(0)
  const background = useTransform(
    x,
    [-100, 0, 100],
    ["#ff008c", "#7700ff", "rgb(230, 255, 0)"]
  )

  return (
    <motion.div style={{ background }}>
      <motion.div
        drag="x"
        dragConstraints={{ left: 0, right: 0 }}
        style={{ x }}
      >
        <Icon x={x} />
      </motion.div>
    </motion.div>
  )
}
```

### JSXによる宣言的な記述

ぱっと見で目につく点として、JSXを利用した宣言的な記述が挙げられます。GUIのプログラミングで状態変数がごちゃつきがちだった時代はReactが終わらせつつありますが、React本体はアニメーションの面倒を見てくれません。Framer Motionは、Reactの流儀をCSSアニメーションに持ち込むものと言えます。公式サイトの謳い文句で "Production-ready declarative animations." とあるように、十分な柔軟性を備えた上で宣言的にCSSアニメーションを記述するためのライブラリがFramer Motionと言えるでしょう。

### Hooksによる状態管理

もう一つ特徴的なのが、useMotionValueやuseTransformを使ったHooksによる状態管理です。Hooksがいかにゲームチェンジャーだったかは、2020年も終わりが見えてきた今繰り返す必要はないでしょう。これによりアニメーションのコードもHooksの恩恵を受けることができます。

Framer Motionは紹介記事では書ききれないほどアニメーションの制御項目があり、その気になればかなりのカスタムアニメーションを作り込めます。より複雑なことをやろうとした時に、状態変数を生で触らずHooksを使える恩恵は大きくなってくるはずです。

## 基本事項の紹介

Framer Motionではdiv, imgといった通常のHTMLタグに対応するmotion.div, motion.imgが準備されています。コンポーネントをこれらmotion.xxxに包むことでアニメーションを記述します。motion.xxxのプロパティとして、
	
1. 移動変形などアニメーション自体に関する情報
2. タップなどの入力イベント処理
3. ライブラリで準備済みヘルパー属性
	
などがあります。

3の頻用アニメーション指定については、コンポーネントのドラッグを可能にするdragやマウスオーバーを検知するhoverなどがあります。あまりアニメーションを自前で作り込まない場合でも、例えば下サンプルのようにヘルパーを利用するだけでそれなりの見栄えが作れます。

```tsx
// マウスオーバーで画像をちょっとだけ拡大するアニメーション
<motion.img whileHover={{ scale: 1.2 }} src="some-image" />
```

## チラ裏

過去にGUIを含むアプリケーションを色々作ってきて、自前でアニメーションがあるコードを書くと途端にコードがごちゃついてくるなあという経験がありました。Framer Motion（とReact）はそういった過去の自分が解決できなかった問題を解決してくれるのでは、という期待が今のところ持てています。React界隈だとFramer Motion以外でも、[React Three Fiber](https://github.com/pmndrs/react-three-fiber)など個人的に筋がいいなあと思うライブラリがあって、文明の進歩に感動することが多いです。


[^1]: 公式サンプルより引用 https://www.framer.com/api/motion/examples/#motionvalues

