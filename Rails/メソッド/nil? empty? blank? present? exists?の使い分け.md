# nil? empty? blank? present? exists?の使い分け

存在を確認するメソッドに`empty?`、`blank?`、`present?`、`exists?`などがあります。
今回はこれらのメソッドの違いについてまとめようと思います。


## nil?

`nil?`メソッドはオブジェクトの中身が`nil`の場合のみ`true`を返します。
それ以外は`false`も空白もから配列も全て`false`です。


## empty?




## blank?


## present?


## exists?





# オブジェクトに対するメソッドの確認

今回のように対象のオブジェクトに対してそのメソッドが使用可能であるか`respond_to?`メソッドで確認することができます。
次のようにオブジェクトに対して`respond_to?`メソッドを使用し、引数に使用できるか確認したいメソッド名をシンボル又は文字列で渡すことで確認することができます。

```
hoge.respond_to? :empty?
# => true or false

hoge.respond_to?('empty?')
```

また、`methods`メソッドで対象のオブジェクトで使用することができるメソッドの一覧を表示することができます。

```
hoge.methods
```


# 参考サイト

[【Rails】nil?, empty?, blank?, present?, exists?の違いと使いどころ | Enjoy IT Life](https://nishinatoshiharu.com/rails-boolean-methods/)

[nil? empty? blank? present? の使い分け #Ruby - Qiita](https://qiita.com/somewhatgood@github/items/b74107480ee3821784e6)

[Rubyでメソッドがあるかをrespond_to?で調べました。 #Ruby - Qiita](https://qiita.com/pugiemonn/items/9667d44da06657603de1)

[Object#nil? (Ruby 3.2 リファレンスマニュアル)](https://docs.ruby-lang.org/ja/latest/method/Object/i/nil=3f.html)

[String#empty? (Ruby 3.2 リファレンスマニュアル)](https://docs.ruby-lang.org/ja/latest/method/String/i/empty=3f.html)

[Object#respond_to? (Ruby 3.2 リファレンスマニュアル)](https://docs.ruby-lang.org/ja/latest/method/Object/i/respond_to=3f.html)


