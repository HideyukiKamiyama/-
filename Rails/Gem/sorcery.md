# sorceryとは

sorceryとはrailsでログイン機能を実装する際に使用するgemです。次の方法でインストールできます。




# インストール方法

- Gemfile

```ruby
gem 'sorcery', "必要であればバージョンも"
```

Gemfileに上記のコードを記述。

- コマンド

```ruby
bundle install
```

bundle installによってGemfileに記述されたgemがインストールされる。




# sorceryの初期設定

sorceryでは以下のコマンドで基本的なファイルやモデルを作ることができる。

```bash
rails g sorcery:install
```

上記のコマンドで次のファイルを作成できる。

1. app/models/user.rb
2. db/migrate/XXXXXXXXX_sorcery_core.rb
3. config/initializers/sorcery.rb

この時マイグレーションファイルが作成されているので`rails db:migrate`コマンドでデーターベースに反映します。追加したいカラムがある場合はマイグレーションファイル次のように追加します。

```ruby
class SorceryCore < ActiveRecord::Migration[5.2]
 def change
   create_table :users do |t|
     t.string :email,            null: false
     t.string :crypted_password
     t.string :salt
     t.string :first_name,       null: false #追加
     t.string :last_name,        null: false #追加
     t.timestamps                null: false 
   end
   add_index :users, :email, unique: true
 end
end
```

この`null: false`はモデルに`validates :content, presence true`を記述しているのと同じバリデーションをかけていますが、セキィリティ面では上記の方が強いです。モデルにバリデーションをかける場合はrails上でしか入力を制限していないため直接SQLを操作されてしまうと変更されてしまいます。それに対し、上記の記法は直接入力を制限しているため安心です。

[migrationファイル内のnull: falseとは - Qiita](https://qiita.com/ryuuuuuuuuuu/items/2841cddf8b541c336ebd)

また、crypted_passwordはパスワードをハッシュ化して保存するためこちらもセキュリティが高くなります。saltカラムはパスワードの末尾にランダムな文字列を結合しパスワードの安全性を高めています。




# モデルのバリデーションの設定

userモデルのバリデーションを設定します。

```ruby
class User < ApplicationRecord
  authenticates_with_sorcery!    

  validates :password, length: { minimum: 3 }, if: -> { new_record? || changes[:crypted_password] }
  validates :password, confirmation: true, if: -> { new_record? || changes[:crypted_password] } 
  validates :password_confirmation, presence: true, if: -> { new_record? || changes[:crypted_password] } 
  validates :email, uniqueness: true, presence: true
  validates :last_name, presence: true, length: { maximum: 255 }
  validates :first_name, presence: true, length: { maximum: 255 }
end
```

> 上記コードの解説
> 
1. `authenticates_with_sorcery!`

このコードによりuserモデルがsorceryを使用しての認証機能を利用できるようになります。

1. `if: -> { new_record? || changes[:crypted_password] }`

ユーザーがパスワード以外のプロフィールを変更したい場合にパスワードの入力を省略できる。（正直あまり理解ができていない。次のサイトに比較的詳しく載っていた）

[Rails Diary](https://okmt-aya-26.hatenablog.com/)

[sorceryによるログイン機能｜tumu](https://note.com/tumu1632/n/n9f332de38563)

1. `validates :password, confirmation: true`と`validates :password_confirmation, presence: true`

これらのコードを二つ記述することでパスワードを2回入力し、その2回のパスワードが同じになっているかのバリデーションをかけることができる。

[Railsバリデーションまとめ - Qiita](https://qiita.com/h1kita/items/772b81a1cc066e67930e)

[Railsユーザーモデルのバリデーション設定(has_secure_password解説) - 独学プログラマ](https://blog.cloud-acct.com/posts/u-rails-user-validates/)




# コントローラ

userコントローラとuser_settionコントローラを作成するのが一般的。

1. userコントローラ

下記のコマンドでuserコントローラを作成する。（サイトによって違ったので注意）

```html
rails generate controller users new create
```

userコントローラを編集

```ruby
class UsersController < ApplicationController
  def new
    @user = User.new
  end
     
  def create
    @user = User.new(user_params)
    if @user.save
      redirect_to login_path
    else
      render :new
    end
  end
     
  private

  def user_params
    params.require(:user).permit(:email, :password, :password_confirmation, :last_name, :first_name)
  end
end
```

1. user_sessionコントローラ

下記のコマンドでuser_sessionコントローラを作成

```ruby
rails generate controller user_sessions
```

次にuser_sessionコントローラの例を示します。

```ruby
class UserSessionsController < ApplicationController
  skip_before_action :require_login

  def new
  end

  def create
    @user = login(params[:email], params[:password])

    if @user
      redirect_back_or_to root_path
    else
      render :new
    end
  end

  def destroy
    logout
    redirect_to root_path, status: :see_other
  end
end
```

ApplicationControllerは全てのコントローラで継承しているためApplicationControllerで定義した`before_action`を他のコントローラでスキップしたい場合に使用するのが`skip_before_action`です。

[before_actionの使い方とオプションについて](https://pikawaka.com/rails/before_action#skip_before_action)

rails7ではdestroyアクションに``status: :see_other``をつけないと予期せぬ挙動をする可能性があるため注意。

[Rails をはじめよう - Railsガイド](https://railsguides.jp/getting_started.html#記事を削除する)




# ルーティングの設定


ルーティングは次のように設定する。

```ruby
get 'login', to: 'user_sessions#new', as: :login
post 'login', to: 'user_sessions#create'
delete 'logout', to: 'user_sessions#destroy', as: :logout
resources :users, only: %i[new create]
```

`as: :login`、`as: :logout`はルーティングにloginとlogoutという名前をつけている。

```ruby
resources :users, only: %i[new create]
```

usersはnewとcreateしか使わないので上記の部分でルーティングを指定している。%iは要素がシンボルの配列を返す記法。




# Viewの作成


パターンが多すぎるため一度割愛




# data: { turbo_method: :delete }


最新railsのdeleteアクションでは`data: { turbo_method: :delete }`を付けないとlink_toメソッドがgetに向かってしまう。

[Rails 7でのTurboの落とし穴：deleteとformメッセージ更新](https://picolab.dev/2022/03/23/rails7-turbo/#toc3)




# sorceryのメソッド

1. logged_in?

ログインしているか確認し条件によって分岐処理をする。

1. login

ログインするためのメソッド。引数にemailとpasswordをとる

1. logout

ログアウトするためのメソッド

1. redirect_back_or_to(url, flash_hash = {})

redirect_back_or_toメソッドは、保存されたURLがある場合そのURLに、ない場合は指定されたURLにリダイレクトします。 例えば、User#editページに行く => 認証が必要 => ログインページに飛ぶ と言った挙動の場合User#editページが保存され、ログイン成功後にUser#editにリダイレクトすることができます。

1. not_authenticated

ログインしていなかった場合の処理を記述することができるメソッド。デフォルトではroot_pathに設定されているため遷移先を変更したい場合は以下のように書き換える必要がある。

1. require_login

require_loginはloginしているかを検証するメソッド。longinしていれば次に進もうとしていたページに遷移することができるが、loginしていなければnot_authenticatedメソッドが実行される。基本的にはアプリケーション全体に適用させたいため次のようにApplicationControllerに書くことが多い。

```ruby
class ApplicationController < ActionController::Base
  before_action :require_login

  private

  def not_authenticated
    redirect_to login_path, danger: "ログインしてください"
  end
end
```

また、require_loginをスキップしたいアクションがある場合は個別のコントローラに次のように記述する。

```ruby
skip_before_action :require_login, only: %i[new create]
```


# souceryのサブモジュール

以下のサイトにsouceryで使われるサブモジュールが記述されています。

[soucery](https://github.com/Sorcery/sorcery/wiki)




### 参考サイト

[github sorcery](https://github.com/Sorcery/sorcery)

[sorceryを使用して、ユーザー機能を作成｜Sho](https://note.com/sho_0021/n/n6eb4abd4d232)

[sorceryを使ってログイン機能を実装する - 学習記録](https://kimura34.hatenadiary.com/entry/2021/02/18/214240)

[Rails ログイン機能　[sorcery]　解説 - Qiita](https://qiita.com/oden-09/items/a2f1aa839f87fd244e19)

[【Rails】sorceryについて -  bokuの学習記録](https://boku-boc.hatenablog.com/entry/2020/10/10/213625)
