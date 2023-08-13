# フラッシュメッセージ

---

フラッシュメッセージを使うとログインやユーザー登録などを行った際、一時的にメッセージを表示することができます。

# 実装方法

---

表示を行いたいコントローラにハッシュのように記述します。

```ruby
# コントローラ
flash[:キー名] = "表示したいメッセージ"
```

キーは任意のものを付けることができます。

```html
# ビューファイル
<%= flash[:キー名] %>
```

- flashとflash.now

flashに似たものにflash.nowがあります。flashは次のリクエストの間まで残るのに対し、flash.nowは現在のリクエストの間のみ表示されます。また、呼び出し方法はflash、flash.now共に変わりません。

使い分け方法はredirect_toならflash、renderならflash.nowで設定する。

- noticeとalert

キーは任意のものを付けることができますが、デフォルトでnoticeとalertが用意されており、これらは省略して記述することができます。

```html
<%= notice %>
<%= alert %>
```

フラッシュメッセージ表示用のビューファイルは次のように記述する。flashはハッシュ形式のデータなので引数は二つ与える。下記の例ではmessageの部分に表示したい文字が入っているため`<%= message %>`と表示している。

```html
<% flash.each do |message_type, message| %>
  <div><%= message %></div>
<% end %>
```

Rails7系ではrenderメソッド実行時のflashメッセージやエラーメッセージは`status: :unprocessable_entity`をつけないとメッセージが表示されないので注意。

[Rails をはじめよう - Railsガイド](https://railsguides.jp/getting_started.html#記事を1件作成する)

# 記述の簡略化

---

application_controller.rb に以下の設定を追加すると、デフォルトの alert と notice 以外で、Bootstrapに用意されているスタイルのフラッシュを定義できます。

```ruby
add_flash_types :success, :info, :warning, :danger
```

[ActionController::Flash::ClassMethods](https://api.rubyonrails.org/classes/ActionController/Flash/ClassMethods.html)

[redirect_to使った時にBootstrap対応のフラッシュメッセージを表示させる - Qiita](https://qiita.com/Yama-to/items/4d19a714d8bf5bfbabdd)

また、上記の設定を行うことでControllerのredirectをフラッシュメッセージを含めて1行で記載できます。

add_flash_types を定義しなくても毎回flashを記載すればフラッシュメッセージの表示はできますが、記述量を減らすことができるので覚えておくと良い。」

```ruby
# BEFORE
redirect_to login_path, flash: { success: 'hoge' }

# AFTER
redirect_to login_path, success: 'hoge'
```

また、redirect_back_or_to では add_flash_types を定義していなくてもflashの記載を省略できます。

```ruby
# add_flash_typesを定義していなくても、flash: { } の記載を省略できる
redirect_back_or_to root_path, success: 'hoge'
```

# Bootstrap

---

Bootstrapを導入している場合は指定するキーを変えることでフラッシュメッセージにcssを適用することができます。現在公式では8種類キーが用意されているようですが今回は主要な４つについて以下に例を示します。
