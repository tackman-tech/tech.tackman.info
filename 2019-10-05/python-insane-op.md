# Pythonのイカれた演算子について

次のPythonコードを見て欲しい。

```python
a = ('Some string' in [1, 2, 3]) in [0, 4, 5]
print(a)
```

このコードを実行すると、出力は

```python
True
```

になる。 'Some string' in [1, 2, 3] の評価結果は False で、PythonではFalseと0は等価に扱われる。
そのため 0 in [0, 4, 5] は True になり何もおかしくない、ように見える。

では次のコードはどうなるだろうか。

```python
b = 'Some string' in [1, 2, 3] in [0, 4, 5]
print(b)
```

最初のコードと何も変わらないように見える。しかし、実行結果は

```python
False
```

となる。こうなると「in演算子って右結合だっけ？」と思うのが人情なので、次のコードを実行してみると

```python
z = 'Some string' in ([1, 2, 3] in [0, 4, 5])
print(z)
```

結果は次のようになる。

```python
Traceback (most recent call last):
File "nanka-file-name.py", line 31, in <module>
z = s in (x in y)
TypeError: argument of type 'bool' is not iterable
```

[1, 2, 3] in [0, 4, 5] が先に評価されて、'Some string' in False のような形になると実行時エラーになる。そりゃそうだ。
in演算子は当たり前に左結合である。しかしそうなると、1番目と2番目のコードの差が謎になる。

挙動をより詳細に見るため、何が起こっているかを探ってみる。

```python
s = 'Some string'
x = [1, 2, 3]
y = [0, 4, 5]

def f():
    print('f() evaluated')
    return [1, 2, 3]

def g():
    print('g() evaluated')
    return [0, 4, 5]

c = s in f() in g()
print(c)
# f() evaluated
# False

d = (s in f()) in g()
print(d)
# f() evaluated
# g() evaluated
# True
```

カッコなしでin演算子をつなげた場合、s in f() in g() ではそもそもg()が評価されないようだ。
カッコをつけた場合はちゃんと評価がされる。
左結合同士のはずなのに、カッコの有無で挙動が違う謎減少になっている。

## 解答

@todesking 氏に教えていただけた。

```text
"Comparisons can be chained arbitrarily, e.g., x < y <= z is equivalent to x < y and y <= z, except that y is evaluated only once (but in both cases z is not evaluated at all when x < y is found to be false)." ナルホディ薄

https://docs.python.org/3/reference/expressions.html#comparisons

つまりs in x in y == s in x and x in y != (s in x) in y


```

[https://twitter.com/todesking/status/1178628914987466752](https://twitter.com/todesking/status/1178628914987466752)
[https://twitter.com/todesking/status/1178629615188725760](https://twitter.com/todesking/status/1178629615188725760)



- in演算子は比較演算子の一種である
- 比較演算子は a < b < c のようにつなげることができる。この時、左側が False だと以降は評価されない

この結果、s in f() in g() のような式は、s in f() が False の場合にin g()は評価されない。
なるほど確かに言語仕様通りですね！理解はしました、納得はできないけれど。

## その他の病的な例

Pythonの比較演算子は上記のように仕様自体が狂っているとしか思えないシロモノなので、
比較的素直そうな >, < といった演算子を使う下記のコードでもかなり直感に反する結果になることがある。

```python
def f():
    print('f() evaluated')
    return 2

def g():
    print('g() evaluated')
    return 3

def h():
    print('h() evaluated')
    return -2

print('1 > f() > h() series')
print((1 > f()) > h())
print(1 > f() > h())

# 結果

1 > f() > h() series
f() evaluated
h() evaluated
True
f() evaluated
False


def F():
    print('f() evaluated')
    return -3

def G():
    print('g() evaluated')
    return -1

def h():
    print('h() evaluated')
    return -5


a = -4 < F() > G()
print(a)

b = (-4 < F()) > G()
print(b)

# 結果
f() evaluated
g() evaluated
False
f() evaluated
g() evaluated
True
```

1番目例では、1 > f()は偽で f() > h() は真。
なのでPythonの言語仕様が期待する通り、1 > f() && f() > h() と評価されて結果はFalseになって欲しい。
しかしこれにカッコをつけると、 (1 > f())が先に評価されてFalseになり、False > h() は
0 > -2 と等価となって式全体ではTrueになってしまう。
期待される結合法則通りにカッコをつけると結果が変わる。

2番目の例に関しては、そもそもこんな形の式を書けて欲しくない。あまりに不健全な形をしている。
おまけにこいつもカッコの有無で結果が変わる。

## 感想

※このセクションは完全に感情の吐露であり、意味のある情報はありません

- in演算子が比較演算子の一種というのは正気とは思えない
- 比較演算子を連ねて書けるという仕様も正気とは思えない
  - zen of Pythonと言いながらこの書き方イケてるよね！というノリでひどい構文を入れるPythonはこのあたり本当に信用できない
- True/Falseが1/0にマップされるとか高級アセンブラこと昭和のC言語か？もう令和ですよ？
  - ここは後方互換性切っていいところだと思うんです
- 比較演算子でFalseがあった時に以降を評価しないという仕様、顧客が本当に必要だったものは失敗の伝搬（Maybe的なやつ）だったのでは
- 暗黙のキャストはやはり悪、エラーを吐いて死んで欲しい


## 結語

こんな狂った言語にいられるか！俺は[Hy](http://hyjp.org)にイカせてもらう！
