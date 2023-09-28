# Factory_botとは


RSpecでテストファイルを実行する際にテストデータを作成してくれるgem。Fakerに近い働きをする。

# インストール


Gemfileのtestグループ内に以下の記述をし、 `bundle install` 。

Gemfile

```ruby
group :test do
  gem 'factory_bot_rails'
  gem 'database_cleaner'
end
```

gem 'database_cleaner'はテストが終了した場合に作成したデータを削除するためのgem。

# 使い方


## ファイル作成


次のコマンドでファイルを作成する。

```ruby
rails g factory_bot:model user
rails g factory_bot:model task
```

## spec/factory/〇〇.rbファイル


上記のコマンドで作成したファイル内にインスタンスを作成するためのコードを記述する。

spec/factory/user.rb

```ruby
FactoryBot.define do
  factory :user do
    sequence(:email) { |n| "user#{n}@example.com" }
    password {"password"}
    password_confirmation {"password"}
  end
end
```

spec/factory/task.rb

```ruby
FactoryBot.define do
  factory :task do
    association :user
    sequence(:title, "title_1")
    content {"test"}
    status {"todo"}
  end
end
```

## sequence


sequenceは連番を表しておりインスタンスを作成した数だけ１、２、３・・・と数字を増やしていくので一意のデータを作ることができる。

上記の例では `user.rb`と `task.rb` でsequenceの記述法が異なるがどちらも同じような働きをする。

`user.rb` の `{ |n| "user#{n}@example.com" }` はブロック変数 n に１〜順に数字が入っていくため

```ruby
user1.email = "user1@example.com"
user2.email = "user2@example.com"
user3.email = "user3@example.com"
               ・
               ・
```

のようなインスタンスを作成する。

`task.rb` も同様に値の”title_1”の１の部分に順に１〜数字が入るようになっているため、

```ruby
task1.title = "title_1"
task2.title = "title_2"
task3.title = "title_3"
         ・
         ・
```

のようにインスタンス変数を作成する。

[Rspec、FactoryBotで使う【sequence】って何？ - Qiita](https://qiita.com/mmaumtjgj/items/db1843ceba88893c1d07)

### ****create_listメソッド****


create_listはfactory_botで用意されているメソッドで、作成したインスタンスを配列として作成します。第一引数にファクトリ名、第二引数に作成するインスタンスの数を渡します。

```ruby
tasks = create_list(:task, 3)
```

上記のコードによりtaskインスタンスが３つ作成されます。

[[Rails] Rspecでcreate_listを使って複数のインスタンスを作成する方法](https://osamudaira.com/331/)

## アソシエーション


今回のuserとtaskにはアソシエーションの関係がありますが、以下のように記述することでアソシエーションの関係を設定することができます。

spec/factory/task.rb

```ruby
# 省略

association :user

# 省略
```

このようにアソシエーションを設定することでtaskインスタンスを作成した際にそれに連動したuserインスタンスを作成します。

## データの作成


上記のようにfactory_botの定義が完了すると、次のような記述でデータを作成することができる。

```ruby
# 生成と同時に保存しない
user = FactoryBot.build(:user)
user = FactoryBot.build(:user, first_name: nil)
# 生成と同時に保存する
user = FactoryBot.create(:user)
user = FactoryBot.create(:user, first_name: "Joe")
```

また、 `spec/rails_helper.rb`ファイルに以下の記述を追加することで省略記法を使うことができる。

```ruby
# spec/rails_helper.rb

config.include FactoryBot::Syntax::Methods
```

上記の記述をすることにより次のような省略をすることができる。

```ruby
user = FactoryBot.build(:user)
　↓
user = build(:user)
```

# 参考サイト


[github factory_bot](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md#callbacks)

[Factorybotを使ったテストデータの作成方法 - Qiita](https://qiita.com/napoano365/items/38e7796c683e785ccf3a)

[【RSpec初級編】FactoryBotを用いてテストコードを効率化する方法について解説｜TechTechMedia](https://techtechmedia.com/factory-bot-rails/)

[【RSpec】before(:suite)/before(:all)/before(:each)それぞれの違いについてまとめてみた｜TechTechMedia](https://techtechmedia.com/before-suite-all-each-rspec/)
