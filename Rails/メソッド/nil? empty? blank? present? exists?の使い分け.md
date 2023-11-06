# nil? empty? blank? present? exists?の使い分け

存在を確認するメソッドに`empty?`、`blank?`、`present?`、`exists?`などがあります。

今回はこれらのメソッドの違いについてまとめようと思います。


## nil?

`nil?`メソッドはオブジェクトの中身が`nil`の場合のみ`true`を返します。

それ以外は`" "`（空白）も`[]`（空配列）も`{}`（空ハッシュ）も全て`false`です。


## empty?

`""`（空文字)、`[]`(空配列), `{}`(空ハッシュ)はtrueになり、`" "`（空白）のようにスペースが入っている場合は`false`になります。

nilや数字、真偽値に対して使うとNoMethodErrorが発生するので注意が必要です。


## blank?

`nil`, `false`, `""`（空文字）, `" "`(空白), `[]`(空配列), `{}`(空ハッシュ)の場合にtrue、それ以外の場合にfalseを返します。


## present?

`blank?`メソッドと逆の答えを返すメソッドです。つまり`!blank?`メソッドと同じ結果を返します。


## exists?

ActiveRecordでデータを検索し検索したデータが存在するかを確認する際に使用します。

同じようなことは`present?`メソッドでもできますがパフォーマンスの都合上こちらの方が良いとされています。

詳しくは[こちら](https://qiita.com/hirohero/items/bdf893b363274ae74a5e)を参照してください。


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


