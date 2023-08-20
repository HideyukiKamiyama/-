# validation

---

# validationとは

---

validationとはデータベースにデータの登録を行う際、保存内容を検証する機能です。以下のようにモデルに記述します。

```ruby
class User < ApplicationRecord
  validates :nickname, presence: true
  validates :birthday, presence: true
end
```

上記のコードは以下のようにワンライナーで記述することも可能です。

```ruby
class User < ApplicationRecord
  validates :nickname, :birthday, presence: true
end
```

# valid?メソッド

---

valid?メソッドはバリデーションを実行し、バリデーションに引っ掛かるかを判別するメソッドです。このメソッドを実行し、エラーが発生しなければtrueを返し、発生したらfalseを返します。似たようなメソッドにvalidメソッドがありますが、こちらはその逆の結果を返します。

```ruby
class User < ApplicationRecord
  validates :nickname, presence: true
end
```

```ruby
@user = User.new(nickname: 'タロウ')
@user.valid?
> true
```

```ruby
@user = User.new(nickname: nil)
@user.valid?
> false
```

# errorsメソッド

---

1. errors

---

errorsメソッドはエラーメッセージをハッシュ形式で取得するコマンドです。

```ruby
class User < ApplicationRecord
  validates :nickname, presence: true
  validates :birthday, presence: true
end
```

```ruby
@user = User.new(nickname: nil, birthday: nil)
@user.valid?
@user.errors
=>{:nickname=>["can't be blank"], :birthday=>["can't be blank"]}
```

1. errors.full_messagesメソッド

---

errors.full_messagesメソッドは複数のエラーを配列型式で取得するメソッドです。eachやmapといった繰り返し処理を使って取得したエラーメッセージを表示します。

```ruby
class User < ApplicationRecord
  validates :nickname, presence: true
  validates :birthday, presence: true
end
```

```ruby
@user = User.new(nickname: nil, birthday: nil)
@user.valid?
@user.errors.full_messages
=> ["Nickname can't be blank", "Birthday can't be blank"]
```

# エラーメッセージの表示

---

エラーメッセージの表示は以下のように行います。

```html
<% if user.errors.any? %>
  <div class = "alert">
    <ul>
      <% user.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
    </ul>
  </div>
<% end %>
```

`if user.errors.any?`の部分でエラーが存在すればtureを返し内部の処理を実行します。

[Enumerable#any? (Ruby 3.2 リファレンスマニュアル)](https://docs.ruby-lang.org/ja/latest/method/Enumerable/i/any=3f.html)

また、エラーメッセージの表示部分をパーシャルにする場合次のようにすることもできます。

```html
<%= form_with model: @user, local: true do |f| %>
  <%= render 'shared/flash_error_message', model: @user %>
  <div class="mb-3">
    <%= f.label :last_name, class: "form-label" %>
    <%= f.text_field :last_name, class: "form-control" %>
  </div>
    ・
    ・
    ・
```

上記のようにform_withの下でrenderを使ってflash_error_messageのファイルを呼び出します。

renderの呼び出し部分に`model: @user`を記述することでパーシャル内で@userをmodelという変数として使用することができます。

[render](https://www.notion.so/render-e6ca6e81fa6d48f1b777c4ccee3e58a9?pvs=21) 

flash_error_messageファイル内は次のようになっています。以下のように変数にmodelを使用し、呼び出し元で先ほどのように`model: @user`のような記述を入れることで全てのモデルで使用することができるようになります。

例えばBoardモデルで同じようにエラーメッセージを表示したい場合は、`<%= render 'shared/flash_error_message', model: @board %>`と記述することで@boardをmodelという変数としてパーシャル内で使用することができるようになります。

```html
<% if model.errors.any? %>
  <div class="alert alert-danger">
    <ul>
      <% model.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
    </ul>
  </div>
<% end %>
```

# エラー発生時の挙動

---

```ruby
def create
  @user = User.new(user_params)
  if @user.save
    redirect_to ~~~(リダイレクト先のパス)
  else
    render :new
  end
end
```

次のような一般的なcreateアクションがあった場合、バリデーションによるエラーが発生するのは`@user.save`を行ったタイミングです。`@user = User.new(user_params)`を実行したタイミングではあくまで@userにデータを格納し、データを保存する準備をしただけであり、保存はしていないためバリデーションも実行されません。

`@user.save`が実行されるとバリデーションに引っかかった場合、railsがデフォルトで持っているエラーオブジェクトにハッシュ形式で格納されます。このエラーオブジェクトはerrorsメソッドで確認することができ、`user.errors`などで呼び出すことができます。

# validationの注意点

---

モデルのvalidationで設定した項目についてはデーターベース（マイグレーションファイル）でも設定しておく必要がある。validationはあくまでrailsアプリ（Webブラウザ）上から入力することができなくするための制約であり、SQLなどでデータベースを直接操作されてしまうと予期せぬデータを保存されてしまう恐れがある。マイグレーションファイルにも制約を付けておくことでこれらの問題を防ぐことができる。

# その他参考サイト
