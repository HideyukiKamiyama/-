# wheneverとは？

Railsのアプリケーションでプログラムを定期実行するためのgemです。
このgemを使うことでRakeタスクのようなプログラムを自動で指定したタイミングや間隔毎に実行することができます。


# インストール方法

Gemfileに次のコードを記述し、`bundle install`します。

```ruby
gem 'whenever', require: false
```

`require: false`はgemを自動読み込みをさせないようにするための記述。
デフォルトでgemは自動で読み込まれるように設定されているためこの記述をしないとアプリを起動した際に自動で読み込まれてしまい、処理が遅くなってしまう。
今回の`whenever`はコマンドで使用することからアプリで呼び出す必要がないため`require: false`を記述すると良い。


# 設定ファイルの作成

```
bundle exec wheneverize .
```

上記のコマンドを実行することで`config/schedule.rb`が作成されます。
このファイル内には以下の例のように定期実行したい処理や間隔を記述します。

```ruby
every 1.minutes do
  rake # （実行したいタスク）
end
```

上記の例では１分毎に対象のタスクを実行するよう設定されています。




# 参考サイト

[github whenever](https://github.com/javan/whenever)

[【Rails】定期実行をしてみよう！（whenever, cron） | ISSEN](https://blog.to-ko-s.com/rails-periodic-execution/)

[未経験からWebエンジニアを目指す [Rails] Rakeタスク、cron、wheneverを使って一時間ごとにタスクを実行](https://osamudaira.com/358/)
