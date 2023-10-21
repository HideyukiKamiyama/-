# cronについて

`whenever`の前にまず`cron`について説明します。
`cron`はUNIX系のOSに標準で搭載されている仕組みで、「⚪︎時になったら〇〇のコマンドを実行」といった形で自動でプログラムを実行します。

`cron`を操作するにはcrontabというコマンドを使用します。
以下はコマンドの一例です。
```
# 現在設定中の定期実行タスクの一覧を表示
crontab -l

# cronの削除
crontab -r
```

# wheneverとは？

先ほどのcronをRubyで操作できるようにするためのgemです。
このgemを使うことでRakeタスクのようなプログラムを指定したタイミングや時間毎に実行することができます。


# インストール方法

Gemfileに次のコードを記述し、`bundle install`します。

```ruby
gem 'whenever', require: false
```

`require: false`はgemを自動読み込みをさせないようにするための記述です。
デフォルトでgemは自動で読み込まれるように設定されているためこの記述をしないとアプリを起動した際に自動で読み込まれてしまい、処理が遅くなってしまいます。
`whenever`はコマンドで実行することからアプリで呼び出す必要がないため`require: false`を記述すると良いです。


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


# `config/schedule.rb`ファイルの実行、取り消し

`config/schedule.rb`ファイルに定期実行の設定を記述したら次のコマンドでファイルの内容を読み込ませます。

```
$ bundle exec whenever --update-crontab
```

このコマンドにより`config/schedule.rb`ファイル内に記述した内容が読み込まれ、定期実行されるようになります。

また、この定期実行を止めたい場合は以下のコマンドで止めることができます。

```
$ bundle exec whenever --clear-crontab
```




# 参考サイト

[github whenever](https://github.com/javan/whenever)

[【Rails】定期実行をしてみよう！（whenever, cron） | ISSEN](https://blog.to-ko-s.com/rails-periodic-execution/)

[未経験からWebエンジニアを目指す [Rails] Rakeタスク、cron、wheneverを使って一時間ごとにタスクを実行](https://osamudaira.com/358/)
