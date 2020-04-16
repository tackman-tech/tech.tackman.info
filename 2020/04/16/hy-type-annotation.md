# Hy の Type annotation に関するメモ

## PythonのAnnotationを利用可能にする議論

- https://github.com/hylang/hy/issues/640
  - 古い時期の議論。一旦沙汰止みになったみたい
- https://github.com/hylang/hy/issues/179
  - 構文に関する議論
- https://github.com/hylang/hy/pull/1810
  - Pythonの type annotation に乗る形で実装された
  
## 公式ドキュメント

```text
The ^ symbol is used to denote annotations in three different contexts:

Standalone variable annotations.
Variable annotations in a setv call.
Function argument annotations.
They implement PEP 526 and PEP 3107.
```

https://docs.hylang.org/en/stable/language/api.html#id1 相当より引用

- 2020.04.16段階でのstable版。v0.18.0相当
