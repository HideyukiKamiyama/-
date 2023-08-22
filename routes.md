# ルーティング

# ルーティングとは

---

URLとアクションを紐付けるためのもの。/config/routes.rbファイル内に記述されている。

# resource

---

resourceメソッドはrailsで定義されている７つのルーティングを自動で設定してくれるメソッドです。

```jsx
resource :ルーティング名
```

を記述するだけでルーティングを設定することができます。設定されているルーティングは

```jsx
rails routes
```

コマンドで表示できます。以下に７つのルーティングをまとめます。

| アクション名 | 役割 |
| --- | --- |
| index | 一覧の表示 |
| show | 詳細の表示 |
| new | 投稿フォームを表示 |
| create | リソースを追加 |
| edit | 更新フォームを表示 |
| update | リソースを更新 |
| destroy | リソースを削除 |

これらのルーティングをresourceメソッドを使わずに記述すると以下ようになります。

```jsx
Rails.application.routes.draw do
    get 'tweets'     => 'tweets#index'
    get 'tweets/:id' => 'tweets#show'
    get 'tweets/new' => 'tweets#new'
    post 'tweets' => 'tweets#create'
    get 'tweets/:id/edit' => 'tweets#edit'
    patch 'tweets/:id'  => 'tweets#update'
    delete 'tweets/:id' => 'tweets#destroy'
end
```

また、複数のコントローラーのルーティングを設定したい場合は以下のように記述します。

```jsx
resources :コントローラー名, :コントローラー名
```

## onlyとexpect

---

`resourcesメソッド`を使用した際、indexアクションとshowアクションだけを指定したいときには下記のように記述します。

```jsx
resources :tweets, only: [:index, :snow]
```

もしくは以下のように記述します。

```jsx
resources :tweets, except: [:new, :create, :edit, :update, :destroy]
```

これにより使用するアクションを制限することができます。

[Rails のルーティング - Railsガイド](https://railsguides.jp/routing.html#crud、verb、アクション)

# resourceを使わないルーティングの設定

---

1. ルートパスへのルーティング

```ruby
root "コントローラ名#アクション名"
get "/", to: "コントローラ名#アクション名"
```

1. その他URLへのルーティング

```ruby
get "URL", to: "コントローラ名#アクション名"

# 次のようにも書ける
get "URL" => "コントローラ名#アクション名"
```

[Rails のルーティング - Railsガイド](https://railsguides.jp/routing.html)

# ルーティングのパスの一覧表示

---

ルーティングのパスを一覧で表示したい場合には以下のコマンドを使用する。

```html
bin/rails routes
```

[Rails のルーティング - Railsガイド](https://railsguides.jp/routing.html#既存のルールを一覧表示する)

[URLヘルパー](https://www.notion.so/URL-4c15e94c1673487aa6db12c28108d806?pvs=21)

## ネスト定義

ネスト定義する場合もresourceメソッドを使用することができます。

その際の記述は以下のようになります。

```jsx
resources :tweets do
  resources :comments
end
```

こうすることでidの後ろにcommentsを付け加えたURLを作成することができます。

[Rails のルーティング - Railsガイド](https://railsguides.jp/routing.html#ネストしたリソース)

[RailsのRoutingネストについて - Qiita](https://qiita.com/keisukegdk/items/beb5a62c17278c25c00d)

[Rails初学者がつまずきやすいルーティングのネストを徹底解説 | 目指せ、スーパーエンジニア](https://hirocorpblog.com/rails-routing-nest/)

# その他の用語

---

## Prefix

Prefixは接頭辞と呼ばれこの後ろに_pathヘルパーをつけることでルーティングを設定することができます。

パスにidが入っている場合はインスタンス変数にidの情報を含めて渡すことで指定することができます。

例えばusersコントローラーのshowアクションを動かすときは通常のパスだとusers/"ユーザーのid"になりますが、Prefixを使って書くとuser_path(@user)のような記述になります。

上の@userはコントローラーで@user = User.find(params[:id])などで記述してあげれば@userの中にはそのユーザーのidも含まれているのでuser_pathの引数として指定できます。
