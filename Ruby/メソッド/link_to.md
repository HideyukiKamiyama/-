# link_toメソッドとは？


ActionView::Helpers::UrlHelperクラスで定義されているhelperメソッドです。

link_toメソッドを使用するとHTMLにおける<a>タグのリンクを作ることができます。以下が基本的な書き方です。

```jsx
<%= link_to '表示する文字列', 'パス' %>
```

第一引数には表示する文字列を、第二引数にはパスを渡すことでリンクを作ることができます。




## パスの書き方

1. URLを使った方法

```jsx
<%= link_to 'Googleへ', 'https://google.com' %>
```

このように直接URLを記述できます。

1. パスを使った方法

```jsx
<%= link_to 'トップページ', root_path %>
```

ルーティングが設定されている場合は_pathヘルパーメソッドを使うことでパスを作成することができます。

ルーティングはターミナルに以下のコマンドを入力することで調べることができます。

```jsx
rake routes
```

これを入力して表示されるルーティングの最後に_pathを追加することでパスを作成できます。





# link_toタグ内に画像を埋め込む


link_toタグ内に画像を埋め込む方法は二つあります。

1. 下記のように表示する文字列の部分に`image_tag`で画像を読み込めば良い。`image_tag`の　（）内にclass名などの属性を追加できる。

```html
<%= link_to image_tag("img_logo.png"), "/" %>
```

[【Rails】「link_to」でaタグ内に画像を設置したい - Qiita](https://qiita.com/hacchi56/items/c357c07481564066c2f1)

1. <%= link_to  〇〇 do %> ~  <% end %>で挟む

以下サンプルです。

```html
<%= link_to '/', class: 'navbar-brand' do %>
  <%= image_tag 'logo.png' %>
<% end %>
```

doを忘れないように注意!!!!

# link_toメソッドの中にタグを含める


link_toタグの中にタグを含めることで上記の画像の方法のようにアイコンなどをリンクにすることができます。

```ruby
<%= link_to users_path do %>
  <i class="fa fa-square"></i> リンク
<% end %>
```

[【Rails】link_toメソッドの中にタグを含める](https://blog.yuhiisk.com/archive/2018/05/02/rails-link-to-in-tags.html)




# その他の記述

第三引数以降に記述することのあるコードをまとめます。




### 1. class

classオプションを指定することでCSSで使用するclass名をつけることができます。Userクラスなどのクラスではないので注意してください。

```jsx
<%= link_to '表示する文字列', 'パス', class: 'クラス名' %>
```

このように記述すると<a>タグで以下のように記述したのと同じ状態になります。

```jsx
<a class="クラス名" href="パス">表示する文字列</a>
```




### 2. style(非推奨)

style属性を指定することができます。

```jsx
<%= link_to '表示する文字列', 'パス', style: 'background-color: blue;' %>
```

これによって以下のような<a>タグが生成されます。

```jsx
<a style="background-color: blue;" href="">表示文字列</a>
```

基本的にCSSはCSSファイルにまとめて記述する事が好まれるためHTMLファイルに個別に記述することは避けた方が良い。




### 3. date-turbo-method

link_toメソッドを使用した場合のデフォルトのHTTPリクエストはGETになりますが、date-turbo-method属性を使用することでPOSTやDELETEなどの他のリクエストを送ることができるようになります。

```html
<%= link_to '表示する文字列', date: {turbo_method: 'post'} %>
```

[Rails で JavaScript を利用する - Railsガイド](https://railsguides.jp/working_with_javascript_in_rails.html#httpメソッド)

[Tips詰め合わせ｜猫でもわかるHotwire入門 Turbo編](https://zenn.dev/shita1112/books/cat-hotwire-turbo/viewer/tips)




### 4. **date-turbo-confirmメソッド**

date-turbo-confirmメソッドを使用することでリンクを押した際に確認ダイアログを出す事ができます。

```html
<%= link_to '表示する文字列', date: {turbo_confirm: '本当に削除しますか'} %>
```

[Rails で JavaScript を利用する - Railsガイド](https://railsguides.jp/working_with_javascript_in_rails.html#確認ダイアログ)

[Abillyz](https://abillyz.com/moco/studies/647)

[Rails7におけるdata-confirm](https://hazm.jp/archives/180)

この確認ダイアログの見た目を変えたい場合は[github data-confirm-modal](https://github.com/ifad/data-confirm-modal)のリンクにある`data-confirm-model`を使うと良い




## link_to_unless_currentメソッド


link_to_unless_currentメソッドはlink_toメソッドと似たようなメソッドです。link_toメソッドとの違いは現在表示しているベージとリンク先のページが同じページであった場合、リンクではなくただの文字を表示することです。

```jsx
<%= link_to_unless_current 'TOP', 'https://home/top' %>
```

このリンクはトップページへのリンクとして使用することができますが、自分が今トップページにいる場合にはリンクではなくただの文字列を表示します。




# 参考サイト


[Rails で JavaScript を利用する - Railsガイド](https://railsguides.jp/working_with_javascript_in_rails.html#httpメソッド)

[link_toはRailsの基本！これであなたも必ずlink_toが…｜Udemy メディア](https://udemy.benesse.co.jp/development/system/link-to.html)

[rake routesコマンドの使い方をマスターしよう！](https://pikawaka.com/rails/rake_routes)

[link_to によるリンクの作り方を徹底解説【Ruby on Rails】](https://ichigick.com/rails-link-to/)

[link_toにclass属性の書き方 - Qiita](https://qiita.com/s_tatsuki/items/a445e950efe974cac210)
