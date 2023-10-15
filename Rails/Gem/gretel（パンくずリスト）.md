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


# viewファイルの編集

viewのパンくずを表示したい部分に以下の記述をします。

```html
<%= breadcrumbs separator: "区切り文字" %>
```

この「区切り文字」の部分は複数のパンくずリストを繋げるためのもので

```
ホーム > ご利用案内 > お問い合わせ
```

のように記述したい場合は

```html
<%= breadcrumbs separator: " > " %>
```

と記述しても良いです。

ただ、今回の`>`のような記号だとHTMLの閉じタグとして認識されてしまう可能性があるため次のように[特殊文字](https://www.htmq.com/text/)を記述すると良いでしょう。

```html
<%= breadcrumbs separator: " &rsaquo; " %>
```


## `application.html.erb`の編集

今回は全てのページにパンくずリストを表示したいため`application.html.erb`に先ほどのコード`<%= breadcrumbs separator: " &rsaquo; " %>`を記述します。


## それぞれのviewファイルでの呼び出し

`application.html.erb`で記述した位置にパンくずを呼び出すためそれぞれのviewファイル内に次のような記述をします。

```html
<% breadcrumb :設定したcrumb名 %>
```

- ユーザー新規登録ページの場合

```ruby
# config/breadcrumb.rb
crumb :user_new do
  link "ユーザー登録", new_user_path
  parent :root
end
```

であった場合、crumb名は`user_new`であるためユーザー新規登録ページへの記述は

```html
<% breadcrumb :user_new %>
```

のようになります。

- ユーザー詳細ページの場合

ユーザー詳細のようにそのページのユーザー名などを表示したい場合はパンくずの設定を次のように記述します。

```ruby
# config/breadcrumb.rb
crumb :user_show do |user|
  link "#{user.name}さんの詳細", user_path(user)
  parent :root
end
```

上記のように記述した場合、crumb呼び出し時にuserインスタンスを渡す必要があります。

```html
<% breadcrumb :user_edit, @user %>
```

このように記述することでパンくずの表示にユーザー名を含ませることができるようになります。


# 参考サイト

[【Rails】 gretelを使ってパンくずリストを作成しよう | Pikawaka](https://pikawaka.com/rails/gretel)
