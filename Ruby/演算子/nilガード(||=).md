# nilガードとは

nilガードとは`||=`のように記述する自己代入演算子です。

この演算子は変数に値が入っている場合には代入を行わず、`nil`もしくは`false`の時だけ代入を行います。

```ruby
a = nil
a ||= "hoge"
puts a # => "hoge"

a = false
a ||= "hoge"
puts a # => "hoge"

a = "huga"
a ||= "hoge"
puts a # => "huga"
```

変数に`nil`が入ってしまう可能性が想定される場合に初期値を設定するために使用されることが多いです。


# ||=について

## || 演算子

||演算子は`or`を意味する論理演算子です。（論理演算子とは`true`、`false`のどちらかを返す演算子のこと）
この演算子は左右に渡された値を左から順に判定し、どちらか片方でもtrueの場合、trueを返します。
つまり以下のような式の場合、最初に`hoge`が`false`であることを判定し、次に`huga`が`true`であることを判定して`true`を返します。

```ruby
hoge = false
huga = true

a = hoge || huga
```


## ||=演算子

`||=`は`+=`と同じような自己代入演算子です。つまり`a ||= b`は`a = a || b`と同じ式となります。
先ほどの`||`演算子を考えるとRubyにおいて`nil`は`false`に相当するため`a = a || b`は`a`が`nil`の場合、`false`を返し、値が入っている（`true`である）`b`を代入します。


# 参考サイト

[nilガードについて - インゲージ開発者ブログ](https://blog.ingage.jp/entry/2023/03/20/110000)

[Rubyのnilガードについて調べてみた #Ruby - Qiita](https://qiita.com/_kt15_/items/9057d89aa9095696deeb)

[Rubyで使われる記号の意味（正規表現の複雑な記号は除く） (Ruby 3.2 リファレンスマニュアル)](https://docs.ruby-lang.org/ja/latest/doc/symref.html#or)
