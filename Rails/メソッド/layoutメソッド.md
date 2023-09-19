# レイアウトファイルとは


レイアウトファイルとはブラウザ上で共通して表示される部分を一纏めにしたファイルです。

例えば、ページが変わったとしてもヘッダーやフッターのような共通して表示される部分がありますがこのような共通した部分をレイアウトファイルに一纏めにして記述することができます。

Railsにおいてはデフォルトで app/views/layouts/application.rb がレイアウトファイルとして使用されるようになっています。

# レイアウトファイルの使い方


```ruby
<!DOCTYPE html>
<html>
  <head>
    <title><%= page_title(yield(:title)) %></title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag "application" %> 
    <%= javascript_include_tag "application", "data-turbo-track": "reload", defer: true %>
  </head>

  <body>
    <% if logged_in? %>
      <%= render 'shared/header'%> 
    <% else %>
      <%= render 'shared/before_login_header'%>
    <% end %>
    <%= render 'shared/flash_message'%>
    <%= yield %>
    <%= render 'shared/footer'%>
  </body>
</html>
```

こちらがレイアウトファイルのサンプルです。

headタグ内でクロスサイトリクエストフォージェリ対策やCSSファイル、jsファイルの読み込みなどを指定しています。

このファイルをベースとして、読み込んだ new.html.erb や index.html.web などのファイルが <%= yield %> の部分で呼び出され、画面に表示されます。

# layout宣言


通常のユーザーが使用する画面と管理者用の画面でレイアウトを分けたい場合などにコントローラ内でlayoutメソッドを使用することで読み込むレイアウトファイルを指定することができます。

以下の例はログイン画面で layout/admin_login.html.erb をレイアウトファイルとして読み込みたい場合の例です。

Admin::UserSessionsコントローラ

```ruby
class Admin::UserSessionsController < Admin::BaseController
  skip_before_action :require_login, only: %i[new create]
  skip_before_action :check_admin, only: %i[new create]
  skip_before_action :verify_authenticity_token, only: %i[destroy]

  layout 'layouts/admin_login'

  def new; end

  def create
    @user = login(params[:email], params[:password])
    if @user
      redirect_to admin_root_path, success: 'ログインしました'
    else
      flash.now[:danger] = 'ログインに失敗しました'
      render :new
    end
  end

  def destroy
    logout
    redirect_to admin_login_path, status: :see_other, success: 'ログアウトしました'
  end
end
```

以下の部分によりAdmin::UserSessionsコントローラ内では layout/admin_login.html.erb がレイアウトファイルとして読み込まれるようになります。

```ruby
layout 'layouts/admin_login'
```

# 参考サイト


[layoutメソッドの使い方と使い所とは？](https://pikawaka.com/rails/layout)
