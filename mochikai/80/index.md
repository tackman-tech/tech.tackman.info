---
marp: true
---

# 今回の進捗

## 2022.01.22 モチ会 80 回

### tackman

---

# コミティア140申し込み準備

- [done] サークル名決め
  - もうちょっと出オチしなくて長く使えるサークル名をつけたくなった
- [in review]] ジャンル決め
  - SFかイラストで検討中
- [WIP] サクカ提出
- [in review] 頒布予定物
  - 百合をやる舞台の本
    - タイトルとコンセプトは決めている
  - （ちょっとえっちなドールの写真集）（うちの子写真集を刷りたいだけ）

---

# 黒い画面でカタカタやるやつの環境整備

メイン開発機のLinuxデスクトップ機を立ち作業用にしているけれど、座って作業したい時もある → ssh経由で作業できるように(neo)vim環境を整えよう！

- Alacritty https://alacritty.org/
  - Win/Mac/Linuxそれぞれ版アリ、めっちゃきれいな画面になる
  - 画面が黒くなくなる
- Coc https://github.com/neoclide/coc.nvim
  - オールインLSPプラグイン
  - VSCodeでやれることはやりたいので、 "configured like VSCode, extensions work like in VSCode" のコンセプトが自分に合ってた
- coc-pyright(Pythonプラグイン)他入れたらVSCodeより開発しやすいレベルになった

---

# (Liquid) Haskellの準備

- ボードゲームの公理の実装に使ってみるか～ということで準備
- 静的検査・内部DSLをいい感じに書きたい → Haskellかなという流れ

---

# 参考リンク

- [Neovimでのフロントエンド開発環境 2021](https://zenn.dev/yano/articles/vim_frontend_development_2021)
- [Vimを支える技術: Alacritty, AquaSKK, tmux, Language Server… 高速ウェブ開発の世界](https://zenn.dev/tsukkee/articles/stmk_advent_calendar_vim)
