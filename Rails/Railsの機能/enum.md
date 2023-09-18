# enum


Railsではカラムと同名の列挙型をモデルに定義し、整数を割り当てることで整数を用いて列挙型に定義した文字列を呼び出すことができるようになります。

これにより文字列が変わったとしても数字で管理しているため管理が簡単になり、enumのメソッドを使えるようになります。

また、数字以外の値を登録しようとするとエラーになるため不正な値が登録されることを防ぐことができます。




# 定義方法


### カラムの追加


次のコマンドで対象のテーブルにカラムを追加します。

```ruby
rails generate migration add_role_to_users
```

次のようなマイグレーションファイルが作成されるため確認後、`rails db:migrate` します。

```ruby
class AddStatusToUsers < ActiveRecord::Migration
  def change
    add_column :users, :role, :integer, null: false, default: 0
  end
end
```

上記のマイグレーションファイルによりusersテーブルにデータ型が整数のroleカラムが作成されます。またデフォルトの値は0になっており、nullでは登録できない制約も付いています。




### モデル内の定義


enumの定義は次のようにモデル内で行います。

記述方法はハッシュを使った方法と配列を使った方法がありどちらで定義しても問題ありません。ハッシュを使った場合は一緒に定義した整数と値が連動し、配列を使った場合はインデックス番号と値が連動します。

```ruby
class User < ApplicationRecord
  # ハッシュ
  enum role: { general: 0, admin: 1 }

  # 配列その１
  enum role: [ :general, :admin  ]

  # 配列その２
  enum role: %i(general admin)
end
```

配列は値の順序が変わるとRailsアプリ全体のロジックが壊れるためハッシュが推奨されています。





# 便利なメソッド

### enumの動作


以下のようにカラム名を複数形にすることで、enumの全ての値をフェッチすることも、特定の値をフェッチすることもできます。

```ruby
User.roles
#=> { general: 0, admin: 1 }

User.roles[:admin]
#=> 1

User.roles["general"]
#=> 0
```


### ステータスがadminのuserの作成


```ruby
user = User.create(name: "太郎")
user.role #=> "general"

user = User.create(name: "太郎", role: :admin)
user.role #=> "admin"
```

マイグレーションファイルでデフォルトの値を設定しているのでroleカラムに値を渡さない場合はgeneralになる。



### ステータスの照合


enumが提供するヘルパーを使用すると次のように照合を行うことができます。

```ruby
# user = User.create(name: "太郎", role: :admin)

user.admin?
#=> true
```

enumを使わなかった場合、

```ruby
user.role == 1
```

のように照合を行うことになるため、1が何を意味しているかが分かりにくくなってしまいます。（マジックナンバー）


### ステータスの更新


enumではステータスの更新を行う場合 ! を使用することで更新を行うことができます。

```ruby
# user = User.create(name: "太郎", role: :admin)

user.general!

user.admin?
#=> false

user.general?
#=> true
```




# 参考サイト


[Active Record クエリインターフェイス - Railsガイド](https://railsguides.jp/active_record_querying.html#enum)

[【Rails】Enumってどんな子？使えるの？ - Qiita](https://qiita.com/ozackiee/items/17b91e26fad58e147f2e)

[Railsのenumを使いこなす方法（翻訳）｜TechRacho by BPS株式会社](https://techracho.bpsinc.jp/hachi8833/2022_02_18/115735)

[enumチュートリアル](https://pikawaka.com/rails/enum)

[【Rails】状態管理を容易に実装することができる機能「enum」の使い方まとめ｜TechTechMedia](https://techtechmedia.com/enum-rails/)

[【Rails】enumとは？メリット、使い方、カラムの追加方法について - Qiita](https://qiita.com/katsu105/items/b8de8c12b80cb1a92ed8)

[【Rails】モデルに列挙型（enum）を定義し、使いこなす方法](https://autovice.jp/articles/189)

[ActiveRecord::Enum (Ruby on Rails edge)](https://edgeapi.rubyonrails.org/classes/ActiveRecord/Enum.html)

