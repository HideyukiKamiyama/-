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


# 参考サイト

[Active Record クエリインターフェイス - Railsガイド](https://railsguides.jp/active_record_querying.html)

[Active Record の基礎 - Railsガイド](https://railsguides.jp/active_record_basics.html)

[SELECT構文：WHEREで検索条件を設定する - 第3章 SQL構文 - [SMART]](https://rfs.jp/sb/sql/s03/03_2-2.html)
