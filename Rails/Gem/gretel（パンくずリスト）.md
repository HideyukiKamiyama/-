# パンくずリストとは

パンくずリストとはユーザーがWebアプリ上のどこのページにいるのかを視覚的に分かりやすくするための表示です。

```
ホーム > ご利用案内 > お問い合わせ
```

のような表示のことを言う。


# インストール方法

Gemfileに以下の記述をし`bundle install`します。

```ruby
gem 'gretel'
```

次に以下のコマンドを実行して`config/breadcrumbs.rb`を作成します。

```
rails g gretel:install
```

`config/breadcrumbs.rb`が作成されていれば準備完了です。


# `config/breadcrumbs.rb`ファイルの編集

`config/breadcrumbs.rb`ファイルは`gretel`の設定を記述するファイルです。
このファイルには`gretel`の設定に関する情報を記述します。
ファイルの中身は次のようになっています。

```ruby
crumb :root do
  link "Home", root_path
end

crumb :admin_user do |user|
  link 'プロフィール', admin_user_path(user)
  parent :admin_users
end
```

上記のコードはそれぞれ以下のような内容を意味しており、これを繋げていくことでパンくずを表示します。

```ruby
crumb "ページ名" do
  link "ビューに表示される名前", "リンクされるURL"
  parent :親のページ名（現在の前のページ）
end
```

`parent`は親ページを持つ場合のみ記述するのでルートページには記述しません。
`crumb`の`ページ名`はこの設定の名前なので厳密にはページ名を記述する訳ではありません。
しかし、どのページの設定であるかが分かりやすくするためページ名を記述すると良いでしょう。

また`ビューに表示されてる名前`の部分には`Font Awesome`のような記号を含めることもできます。


# 












# 参考サイト

[【Rails】 gretelを使ってパンくずリストを作成しよう | Pikawaka](https://pikawaka.com/rails/gretel)
