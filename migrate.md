# マイグレーションファイル

---

# db/schema.rb

---

Railsの現在のDBの構造を表しています。`rails db:migrate`すると現在のDBの構造を自動で反映します。

[Active Record マイグレーション - Railsガイド](https://railsguides.jp/active_record_migrations.html#スキーマダンプの意義)

# 文字数のバリデーションの設定方法

---

次の例のように記述することで文字数を制限できます。

```ruby
class ChangePostsContentLimit50 < ActiveRecord::Migration[5.2]
  def up
    change_column :posts, :content, :text, limit: 50
  end
  def down
    change_column :posts, :content, :text
  end
end
```

バージョンを上げる際の処理をupに下げる際の処理をdownに記述します。

[【Rails】データのマイグレーションの理解と内容制限 - Qiita](https://qiita.com/super-kiricub/items/a6faa338de25bc329a0e)

[NOT NULLなどの制約の設定 - Ruby on Rails入門](https://www.javadrive.jp/rails/model/index9.html)

# 外部キーの設定

---

マイグレーションファイルで外部キーをテーブルに設定する場合、次のように記述します。

```ruby
t.references :user, foreign_key: true,  null: false
```

`t.references :user`と記述することで`user_id`カラムを自動で作成し、indexを付与してくれます。

また、これだけでは外部キーとして認識されないので`**foreign_key: true`**をつけます。

既に作成済みのテーブルに追加する場合は、追加用のマイグレーションファイルを作成し、

```ruby
class AddColumn < ActiveRecord::Migration[5.0]
 def change
   add_reference :tweets, :user, foreign_key: true
 end
end
```

と記述します。

また、次のように記述しても外部キー制約はつかないので注意が必要です。

```ruby
t.integer :user_id, foreign_key: true
```

# rails db:rollback

---

`rails db:migrate`で間違えたマイグレーションファイルを実行してしまったと気づいた場合、`rails db:rollback`で最新のマイグレーションファイルを`rails db:migrate`実行前の状態に戻すことができます。

どのマイグレーションファイルが最新のものかはマイグレーションファイルの日付を確認することで確認できます。

`rails db:rollback`で`rails db:migrate`実行前に戻したらマイグレーションファイルを修正し、もう一度`rails db:migrate`すれば修正完了です。
