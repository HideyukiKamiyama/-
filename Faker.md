# Faker

---

# Fakerとは

---

ダミーデータを作成するためのgemです。データベースのテストを行う際にたくさんのデータが必要になることがありますが、これを簡単に作成することができます。このようなデータをseedデータと呼びます。

どのようなデータを作成できるかは下記のリンク内で確認することができます。

https://github.com/faker-ruby/faker

# seeds.rbについて

---

seeds.rbとはテーブルに大量のデータを作成する際に使用するファイルです。seeds.rbに登録したいデータとcreate!メソッドを記述し、`rails db:seed`を実行することでseeds.rb内に書いたデータが作成されます。

create!メソッドはcreateメソッドでバリデーションに引っかかった場合に例外を発生させる時に使用する。例外とはrailsにおける赤いエラー画面のこと。（理解浅め）

[【Rails/ActiveRecord】createとcreate!の違いについて - Qiita](https://qiita.com/saitok7/items/20adc17112d6d0bcf9d5)

このseeds.rbとFakerを組み合わせることで大量のダミーデータを作成することができます。

# 実行方法

---

1. いつも通りGemfileにFakerを記述し、bundle installする。
2. db/seeds.rbに次のようなコードを記述する。（以下はUserのデータの場合）

```ruby
20.times do |n|
  User.create!(
    last_name: Faker::Name.unique.last_name,
    first_name: Faker::Name.unique.first_name,
    email: Faker::Internet.unique.email,
    password: "3150test",
    password_confirmation: "3150test"
  )
end
```

1. `rails db:seed`で実行

## 注意点

---

```ruby
last_name: Faker::Name.unique.last_name,
```

の部分は`unique`を記述しないと一意のデータが作成されない。（デフォルトでは重複したデータを作成してしまう可能性がある)
