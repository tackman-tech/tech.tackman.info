# Hy threading macro の紹介

http://docs.hylang.org/en/stable/language/api.html#id2

Hyにはthreading macro（->, ->>）が標準で用意されています。
これらを使うと、Pythonでは一時変数と再代入が必要だったコードがそれらなしできれいに書けることがあります。

## Threading (tail) macro とはどういうものか

公式ドキュメントには下記のように説明されています。

```text
The threading macro inserts each expression into the next expression’s first argument place.
(訳：スレッディングマクロは、[訳注：スレッド内の]それぞれの式を次の式の最初の引数に配置します。)
```

Threading tail macro (->>) は、式の配置先が最初の引数でなく最後の引数である点だけが違うところです。

文字通りの意味なのですが、具体例を出した方が分かりやすいので下記に概念的なマクロ展開例を出してみます。


```Hy
; (defn f [x y z] (+ x y z)) など何か適当な関数
(-> 10
  (f 1 2)
  (f 3 4))
  
(->> 10
  (f 1 2)
  (f 3 4))
  
↓↓↓

(f (f 10 1 2) 3 4)

(f 3 4 (f 1 2 10))
```

## 実用的な例

挙動は分かったとして、これだけだと有用性がピンと来ないかもしれません。ここでは私がPythonからHyに移植したコードを比較してみます。

```python
    def forward(self, cond_vector, truncation):
        z = self.gen_z(cond_vector)

        # We use this conversion step to be able to use TF weights:
        # TF convention on shape is [batch, height, width, channels]
        # PT convention on shape is [batch, channels, height, width]
        z = z.view(-1, 4, 4, 16 * self.config.channel_width)
        z = z.permute(0, 3, 1, 2).contiguous()

        for i, layer in enumerate(self.layers):
            if isinstance(layer, GenBlock):
                z = layer(z, cond_vector, truncation)
            else:
                z = layer(z)

        z = self.bn(z, truncation)
        z = self.relu(z)
        z = self.conv_to_rgb(z)
        z = z[:, :3, ...]
        z = self.tanh(z)
        return z
```
(from: https://github.com/huggingface/pytorch-pretrained-BigGAN/blob/6ae20a35a051816d66811d85597033623a8ac888/pytorch_pretrained_biggan/model.py#L228)

```Hy
(defn tensor-view [t &rest args] ((. t view) args))
(defn tensor-permute [t &rest args] ((. t permute) args))
(defn tensor-contiguous [t] ((. t contiguous)))

(defn forward [self z truncation]
    (with [(torch.no_grad)]
      (-> (self.G.gen_z z)
          (tensor-view -1 4 4 (* 16 self.G.config.channel_width))
          (tensor-permute 0 3 1 2)
          (tensor-contiguous)

          (layers-forward self.G.layers z truncation)

          (self.G.bn truncation)
          (self.G.relu)
          (self.G.conv_to_rgb)
          (get [(slice None None) (slice None 3) ,,,])
(self.G.tanh))))
```

Python版では z = … を毎行書いていたのが、Hy版では一時変数と代入がforward関数中からは撲滅できています
（かわりに引数位置を調整するための関数群が追加されてはいます）。
1引数関数の呼び出しでチェインする部分は特に見通しが良くなっているのではないでしょうか。

一時変数を使わないとネストが深くなってコードが見づらくなる場合には、-> や ->> の使用は検討の価値があると思います。

## 本文について

2019/5現在、Hy 0.16 についての記述です。
