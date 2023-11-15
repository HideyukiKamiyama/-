# ActiveRecordとは？

ActiveRecordとはRailsアプリケーションからデータベースを操作するためのORMです。
この仕組みによりRubyの言語を使ってデータベースの操作を行うことができます。


# ActiveRecordのメソッド

## find

findメソッドは対象のモデルの中から特定のidを指定し、該当する一つのデータを取得します。

```ruby
Article.find(1)
```

このコードは次のSQLを発行します。

```
SELECT * FROM articles WHERE (articles.id = 1) LIMIT 1
```

## select

`select`メソッドはSQLのSELECTと同じ働きをするメソッドで、取得するデータのカラムを指定することができます。

```ruby
User.select(:name)
```

上記の例では`name`カラムを指定しているためUserクラスに`email`などの他のカラムがあったとしても`name`カラムのデータのみ取得します。


# 条件

## AND条件

AND条件は`where`メソッドをチェインすることで実行できます。

[Active Record クエリインターフェイス - Railsガイド](https://railsguides.jp/active_record_querying.html#and%E6%9D%A1%E4%BB%B6)

```ruby
Customer.where(last_name: 'Smith').where(orders_count: [1, 3, 5]))
```

## OR条件

OR条件は一つ目のリレーションで`or`メソッドを使用し、二つ目のリレーションをその引数に渡すことで実装できます。


[Active Record クエリインターフェイス - Railsガイド](https://railsguides.jp/active_record_querying.html#or%E6%9D%A1%E4%BB%B6)

```ruby
Customer.where(last_name: 'Smith').or(Customer.where(orders_count: [1, 3, 5]))
```

## NOT条件

NOT条件は`where.not`で実装することができます。

[Active Record クエリインターフェイス - Railsガイド](https://railsguides.jp/active_record_querying.html#not%E6%9D%A1%E4%BB%B6)

```ruby
Customer.where.not(orders_count: [1, 3, 5])
```


# 降順、昇順

並び順を変更したい場合は`order`メソッドを使用します。
`order`メソッドはデフォルトでは昇順になっているため次のように記述することで作成日時で昇順に並べたデータを取得します。
また引数はシンボル、または文字列を渡します。

```ruby
Book.order(:created_at)
# または
Book.order("created_at")
```

`order`メソッドは`asc`、`desc`を指定することができ、次のようにすることで昇順、降順を指定することができます。

```ruby
Book.order(created_at: :desc)
# または
Book.order(created_at: :asc)
# または
Book.order("created_at DESC")
# または
Book.order("created_at ASC")
```


# Limit、Offset

## Limit

`limit`メソッドはデータを上から何件取得するかを指定します。

```ruby
モデル名.limit(5)
```

## Offset

`offset`メソッドは`limit`メソッドとは対照的に引数に渡した数字分のデータを上からスキップし、それ以降のデータを取得します。

```ruby
モデル名.offset(5)
```

また、`offset`メソッドは`limit`メソッドと併用することができ、次の例では最初の10件をスキップした後、６件目から５件のデータを取得します。

```ruby
モデル名.limit(5).offset(10)
```


# Group

SQLで言うところの`GROUP BY`を実行したい場合は次のようになります。

```ruby
Order.select("created_at").group("created_at")
```

こちらの例では`Order`（注文）を`"created_at"`でグループ化したデータを取得しています。


# 参考サイト

[Active Record クエリインターフェイス - Railsガイド](https://railsguides.jp/active_record_querying.html)

[Active Record の基礎 - Railsガイド](https://railsguides.jp/active_record_basics.html)

[SELECT構文：WHEREで検索条件を設定する - 第3章 SQL構文 - [SMART]](https://rfs.jp/sb/sql/s03/03_2-2.html)


