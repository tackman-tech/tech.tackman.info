---
marp: true
---

# 今週の進捗

## 2021.02.20 モチ会 52 回

### tackman

---

# やったこと

- リモボド本向けにCI環境作った
  - GitHub Actions + Vivlistyle CLIでPDF自動生成
  - https://github.com/tackman/remobodo-book

今週は意識的に無をやったので進捗は少ない

---

# Vivliostyle CLI

- CSS組版ソフトウェアVivliostyleのCLI版
- Vivliostyle自体初めて触るので、使い勝手などは一冊書いてみてのお楽しみ

余談：準公式からD&D book風テーマが提供されている

https://www.npmjs.com/package/vivliostyle-theme-dnd-5e-phb

リンク先にサンプル画像があるので見てみてください

---

# GitHub ActionsでのPDF生成

- Vivliostyle CLIに関しては基本的に npm build するだけ
- なのだが、日本語フォントの面倒は見てくれないので自分で解決する必要あり
  - Vivliostyleはフォールバックでシステムフォントを利用する実装の模様
- ActionsのデフォルトのUbuntuには日本語フォントが入っていない→□□□

---

# フォントどうする問題

- さしあたりActions上で apt install IPAフォント をしている
- これで一応動きはするけれど、解決法が汚い＆出力が環境依存して嫌
  - 手元マシンだとadobe-source-han-sans-jpを使っており、別物になる

フォント、中でも日本語フォントは沼なので逃げられるなら逃げたい…