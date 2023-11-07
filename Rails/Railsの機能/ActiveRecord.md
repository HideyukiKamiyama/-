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


# 参考サイト

[Active Record クエリインターフェイス - Railsガイド](https://railsguides.jp/active_record_querying.html)

[Active Record の基礎 - Railsガイド](https://railsguides.jp/active_record_basics.html)

[SELECT構文：WHEREで検索条件を設定する - 第3章 SQL構文 - [SMART]](https://rfs.jp/sb/sql/s03/03_2-2.html)
