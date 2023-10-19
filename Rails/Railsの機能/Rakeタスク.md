# Rakeタスクとは？

RakeタスクとはRailsアプリケーションにおける処理（アプリケーションのビルド、テスト、データベースの操作など）をRakeタスクという形で作成し、必要に応じて実行する機能のこと。

`whenever`と組み合わせることで処理を自動化することができる。


# 作成方法

今回は"Hello World!"を返す簡単なRakeタスクを作成します。

```
rails g task hello_world
```

上記のコマンドを実行することで`lib/tasks/`の配下に以下のコードが記述された`hello_world.rake`というファイルが作成されます。

```ruby
namespace :greet do
end
```

上記のコードを次のように書き換えます。

```ruby
namespace :hello_world do
  desc "Hello Word!を返す"
  task :greeting do
    puts "Hello world!"
  end
end
```

上記のコードの意味は以下の通りです。

- namespace

タスクの名前空間です。このような名前空間を使用することでタスクの種類が増えてしまった場合にも、タスクをグループ化して整理することができます。

- descメソッド

descripton（説明）のこと。そのタスクが何をするタスクなのかを記述する。`rake -T`コマンドを実行するとそのアプリに定義されているRakeタスク一覧が表示されるが、その際にここで設定した`desc`が表示される。
タスクを実行する上で必須ではない（`rake -T`コマンド実行時に説明が表示されなくなるだけ）ため記述しなくても良い。

- taskメソッド

taskメソッドは実行するタスクを定義するためのメソッドです。上記の例では`:greetong`という名前がタスクに付けられており、タスクの処理として`puts "Hello World!"`を返します。






# 参考サイト

[【Rails】タスクを定義し実行することができるrake taskについて簡単にまとめてみる｜TechTechMedia](https://techtechmedia.com/rake-task-rails/)

[Rakeタスクの実装 #Ruby - Qiita](https://qiita.com/yoshito410kam/items/26c3c6e519d4990ed739)

[【Rails 】Rakeタスクとは #Rakeタスク - Qiita](https://qiita.com/mmaumtjgj/items/8384b6a26c97965bf047)
