# scopeとは

`scope`とは`ActiveRecord`のクラスメソッドの一つで、モデルに条件式を定義することでその条件式をメソッドのように使用することができる機能のことです。

長い条件式を使用する場合や、同じ条件式を何度も使用する場合は`scope`を使用することで可読性の向上や、保守点検の簡易化が期待できます。


# 使用方法

次のようにクラス内に`scope`メソッドを書き、実行したいクエリを記述します。

```ruby
class Article < ApplicationRecord
  scope :published, -> { where(published: true) }
end
```

上記のように記述することでpublishedがメソッドのように使用することが出来るようになり、次のように記述することで`published`がtrueの`Article`を呼び出すことができます。

```ruby
Article.published
```


# scopeを使用するメリット

## 可読性

先ほどのコードを`scope`を使わないで呼び出すと次のような記述になり、`scope`を使用しない場合と比べて読みづらくなります。

```ruby
Article.where(published: true)
```


## 保守性




# 参考サイト

[Active Record クエリインターフェイス - Railsガイド](https://railsguides.jp/active_record_querying.html#%E3%82%B9%E3%82%B3%E3%83%BC%E3%83%97)

[[Rails]モデルのscopeメソッド](https://zenn.dev/yusuke_docha/articles/ca0637ccc8d01f)

[Railsでよく利用する、Scopeの使い方。 #Ruby - Qiita](https://qiita.com/ngron/items/14a39ce62c9d30bf3ac3)

[Railsのモデルのscopeを理解しよう #Ruby - Qiita](https://qiita.com/ozin/items/24d1b220a002004a6351)

[【Rails】モデルにスコープで定義する場合とクラスメソッドで定義する場合の違いについてまとめてみる｜TechTechMedia](https://techtechmedia.com/difference-between-scope-and-class-method-rails/)
