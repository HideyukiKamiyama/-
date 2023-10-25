# scopeとは

`scope`とは`ActiveRecord`のクラスメソッドの一つで、モデルに条件式を定義することでその条件式をメソッドのように使用することができる機能のことです。

長い条件式を使用する場合や、同じ条件式を何度も使用する場合は`scope`を使用することで可読性の向上や、保守点検の簡易化が期待できます。


# 使用方法

`scope`の基本構文は次の通りです。

```ruby
class モデル名 < ApplicationRecord
  scope :スコープの名前, -> { 条件式 }
end
```

上記のようにクラス内に`scope`メソッドを書き、実行したいクエリを記述します。
以下が実際の使用例です。

```ruby
class Article < ApplicationRecord
  scope :published, -> { where(published: true) }
end
```

上記のように記述することでpublishedがメソッドのように使用することが出来るようになり、次のように記述することで`published`がtrueの`Article`を呼び出すことができます。

```ruby
Article.published
```


## 引数の渡し方

`scope`内の条件式に引数を渡したい場合次のように記述することで引数を渡すことができます。

```ruby
class Article < ApplicationRecord
  scope :published, -> (count){ where(published: true).limit(count) }
end
```

`scope`で定義された上記のような処理は通常のクラスメソッドと同じように呼び出すことができます。

```ruby
Article.published(5)
# => Article.where(published: true).limit(5)が実行される
```


## 条件文の使用

`scope`の中で`if`文などを使用して条件分岐をすることができます。

```ruby
class Order < ApplicationRecord
  scope :created_before, ->(time) { where(created_at: ...time) if time.present? }
end
```


## デフォルトスコープ

特定のモデルの全てのクエリに同いじ条件式を適用させたい場合、次のようにデフォルトスコープを使用します。

```ruby
class Article < ApplicationRecord
  default_scope :published, -> { where(published: true) }
end
```

この記述をしておくことにより`Article`モデルに対してクエリが発行されると次のように`WHERE (published: true)`が含まれたクエリになります。

```sql
SELECT * FROM articles WHERE (published: true)
```



# scopeを使用するメリット

## 可読性

先ほどのコード（`scope :published, -> { where(published: true) }`)を`scope`を使わないで呼び出すと次のような記述になり、`scope`を使用しない場合と比べて読みづらくなります。また、条件式が長くなってしまう場合に`scope`を使うことでコードを短くすることができます。

```ruby
Article.where(published: true)
```


## 保守性

繰り返し使用する条件式を`scope`を使って定義することで処理を共通化することができるため、修正が必要になった場合に一箇所修正するだけで全ての条件式を修正することができます。

例えば先ほどの処理に`limit(5)`という処理を追加する場合、先ほどの`scope`に追加するだけで修正が完了します。

```ruby
class Article < ApplicationRecord
  scope :published, -> { where(published: true).limit(5) }
end
```




# 参考サイト

[Active Record クエリインターフェイス - Railsガイド](https://railsguides.jp/active_record_querying.html#%E3%82%B9%E3%82%B3%E3%83%BC%E3%83%97)

[[Rails]モデルのscopeメソッド](https://zenn.dev/yusuke_docha/articles/ca0637ccc8d01f)

[Railsでよく利用する、Scopeの使い方。 #Ruby - Qiita](https://qiita.com/ngron/items/14a39ce62c9d30bf3ac3)

[Railsのモデルのscopeを理解しよう #Ruby - Qiita](https://qiita.com/ozin/items/24d1b220a002004a6351)

[【Rails】モデルにスコープで定義する場合とクラスメソッドで定義する場合の違いについてまとめてみる｜TechTechMedia](https://techtechmedia.com/difference-between-scope-and-class-method-rails/)
