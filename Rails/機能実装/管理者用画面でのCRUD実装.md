# 管理画面へのCRUDの導入


# 実装したいこと


管理画面でユーザーと掲示板に関するCRUD機能を実装します。

ユーザー、掲示板はそれぞれ検索可能にし、掲示板は日付で範囲検索できるようにします。

# コントローラの作成


Admin::Boardコントローラ

```ruby
class Admin::BoardsController < Admin::BaseController
  before_action :set_board, only: %i[show edit update destroy]

  def index
    @search = Board.ransack(params[:q])
    @boards = @search.result(distinct: true).includes(:user).order(created_at: :desc).page(params[:page])
  end

  def show; end

  def edit; end

  def update
    if @board.update(board_params)
      redirect_to admin_board_path(@board), success: t('flash_message.board_update')
    else
      flash.now[:danger] = t('flash_message.board_not_update')
      render :edit, status: :unprocessable_entity
    end
  end

  def destroy
    @board.destroy!
    redirect_to  admin_boards_path, status: :see_other, success: t('flash_message.board_delete')
  end

  private

  def set_board
    @board = Board.find(params[:id])
  end

  def board_params
    params.require(:board).permit(:title, :body, :board_image, :board_image_cache)
  end
end
```

Admin::Userコントローラ

```ruby
class Admin::UsersController < Admin::BaseController
  before_action :set_user, only: %i[show edit update destroy]
  
  def index
    @search = User.ransack(params[:q])
    @users = @search.result(distinct: true).order(created_at: :desc).page(params[:page])
  end
  
  def show; end

  def edit; end

  def update
    if @user.update(user_params)
      redirect_to admin_user_path, success: t('flash_message.user_update')
    else
      flash.now[:danger] = t('flash_message.user_not_update')
      render :edit, status: :unprocessable_entity
    end
  end

  def destroy
    @user.destroy!
    redirect_to admin_users_path, status: :see_other, success: t('flash_message.user_destroy')
  end

  private

  def set_user
    @user = User.find(params[:id])
  end

  def user_params
    params.require(:user).permit(:email, :last_name, :first_name, :avatar, :avatar_cache, :role)
  end
end
```

Admin::Boardコントローラ、Admin::Userコントローラ共に一覧ページで検索に対応させるためransackメソッドを使用しています。

destoryアクションでは `status: :see_other` をつけ忘れないように気をつけてください。

[Rails 7.0 + Ruby 3.1でゼロからアプリを作ってみたときにハマったところあれこれ - Qiita](https://qiita.com/jnchito/items/5c41a7031404c313da1f#destroyのレスポンスに-status-see_other-を付ける必要がある)

# ルーティング


ルーティングは次のように設定します。

adminディレクトリ配下にあるAdmin::Boardコントローラ、Admin::Userコントローラを使用するため `namespace :admin do` ブロック内に記述します。

```ruby
namespace :admin do
  root 'dashboards#index'
  get 'login', to: 'user_sessions#new'
  post 'login', to: 'user_sessions#create'
  delete 'logout', to: 'user_sessions#destroy'
  resources :users, only: %i[index show edit update destroy]
  resources :boards, only: %i[index show edit update destroy]
end
```

# viewファイルの作成


### admin/boards配下のファイル


index.html.erb

```ruby
<% content_for(:title, t('.title')) %>
<div class="container mb-5 pt-2">
  <h1><%= t('.title') %></h1>
  <div class="row">
    <div class="col-md-12 mb-3">
      <%= render 'search_form' %>
    </div>
  </div>
  <div class="row">
    <div class="col-md-12">
      <table class="table table-striped">
        <thead>
        <tr>
          <th scope="col"><%= Board.human_attribute_name(:id) %></th>
          <th scope="col"><%= Board.human_attribute_name(:title) %></th>
          <th scope="col"><%= Board.human_attribute_name(:full_name) %></th>
          <th scope="col"><%= Board.human_attribute_name(:created_at) %></th>
          <th scope="col"></th>
        </tr>
        </thead>
        <tbody>
        <%= render @boards %>
        </tbody>
      </table>
    </div>
  </div>
  <div class="row">
    <div class="col-md-12">
      <!-- ページネーション -->
      <%= paginate @boards %>
    </div>
  </div>
</div>
```

show.html.erb

```ruby
<% content_for(:title, @board.title) %>
<div class="container">
  <div class="row">
    <div class="col-md-10 offset-md-1 col-lg-8 offset-lg-2">
      <h1><%= t('.title') %></h1>
      <div class="text-right mb-3">
        <%= link_to t('defaults.edit'), edit_admin_board_path(@board), class: 'btn btn-success' %>
        <%= link_to t('defaults.delete'), admin_board_path(@board), data: { turbo_confirm: t('defaults.delete_confirm'), turbo_method: "delete" }, class: 'btn btn-danger' %>
      </div>
      <table class="table table-bordered bg-white">
        <tr>
          <th scope="row"><%= Board.human_attribute_name(:id) %></th>
          <td><%= @board.id %></td>
        </tr>
        <tr>
          <th scope="row"><%= Board.human_attribute_name(:title) %></th>
          <td><%= @board.title %></td>
        </tr>
        <tr>
          <th scope="row"><%= Board.human_attribute_name(:full_name) %></th>
          <td><%= @board.user.decorate.full_name %></td>
        </tr>
        <tr>
          <th scope="row"><%= Board.human_attribute_name(:body) %></th>
          <td><%= @board.body %></td>
        </tr>
        <tr>
          <th scope="row"><%= Board.human_attribute_name(:created_at) %></th>
          <td><%= l @board.created_at, format: :long %></td>
        </tr>
      </table>
    </div>
  </div>
</div>
```

edit.html.erb

```ruby
<% content_for(:title, @board.title) %>
<div class="container">
  <div class="row">
    <div class="col-md-10 offset-md-1 col-lg-8 offset-lg-2">
      <h1><%= t '.title' %></h1>
      <%= form_with model: @board, url: admin_board_path(@board), local: true do |f| %>
        <%= render 'shared/flash_error_message', model: @board %>
        <div class="form-group">
          <%= f.label :title %>
          <%= f.text_field :title, class: 'form-control' %>
        </div>
        <div class="form-group">
          <%= f.label :body %>
          <%= f.text_area :body, class: 'form-control', rows: 10, height: "200px" %>
        </div>
        <div class="form-group">
          <%= f.label :board_image %>
          <%= f.file_field :board_image, class: "form-control", accept: 'image/*' %>
          <%= f.hidden_field :board_image_cache %>
        </div>

        <div class='mt-3 mb-3'>
          <%= image_tag @board.board_image.url,
                        class: 'rounded-circle',
                        id: 'preview',
                        size: '100x100' %>
        </div>
        <%= f.submit class: 'btn btn-primary' %>
        <% end %>
      </div>
    </div>
  </div>
</div>
```

_board.html.erb

```ruby
<tr>
  <td>
    <%= board.id %>
  </td>
  <td>
  <%= link_to board.title, admin_board_path(board) %>
  </td>
  <td>
    <%= board.user.decorate.full_name %>
  </td>
  <td>
    <%= l board.created_at, format: :long %>
  </td>
  <td>
    <%= link_to t('defaults.show'), admin_board_path(board), class: 'btn btn-info' %>
    <%= link_to t('defaults.edit'), edit_admin_board_path(board), class: 'btn btn-success' %>
    <%= link_to t('defaults.delete'), admin_board_path(board), data: { turbo_confirm: t('defaults.delete_confirm'), turbo_method: "delete" }, class: 'btn btn-danger' %>
  </td>
</tr>
```

_search_form.html.erb

```ruby
<%= search_form_for @search, url: admin_boards_path do |f| %>
  <div class="row">
    <div class="d-flex align-items-center justify-content-center">
      <div class="d-inline me-3">
        <%= f.search_field :title_or_body_cont, class: 'form-control', placeholder: t('defaults.search_word') %>
      </div>
      <div class="d-inline me-3">
        <%= f.date_field :created_at_gteq %>
        <span>~</span>
        <%= f.date_field :created_at_lteq_end_of_day %>
      </div>
      <%= f.submit class: 'btn btn-primary' %>
    </div>
  </div>
<% end %>
```

### admin/user配下のファイル


index.html.erb

```ruby
<% content_for(:title, t('.title')) %>
<div class="container mb-5 pt-2">
  <h1><%= t('.title') %></h1>
  <div class="row">
    <div class="col-md-12 mb-3">
      <%= render 'search_form' %>
    </div>
  </div>
  <div class="row">
    <div class="col-md-12">
      <table class="table table-striped">
        <thead>
        <tr>
          <th scope="col"><%= User.human_attribute_name(:id) %></th>
          <th scope="col"><%= User.human_attribute_name(:full_name) %></th>
          <th scope="col"><%= User.human_attribute_name(:role) %></th>
          <th scope="col"><%= User.human_attribute_name(:created_at) %></th>
          <th scope="col"></th>
        </tr>
        </thead>
        <tbody>
        <%= render @users %>
        </tbody>
      </table>
    </div>
  </div>
  <div class="row">
    <div class="col-md-12">
      <!-- ページネーション -->
      <%= paginate @users %>
    </div>
  </div>
</div>
```

show.html.erb

```ruby
<% content_for(:title, t('.title')) %>
<div class="container">
  <div class="row">
    <div class="col-md-10 offset-md-1 col-lg-8 offset-lg-2">
      <h1><%= t('.title') %></h1>
      <div class="text-right mb-3">
        <%= link_to t('defaults.edit'), edit_admin_user_path(@user), class: 'btn btn-success' %>
        <%= link_to t('defaults.delete'), admin_user_path(@user), data: { turbo_confirm: t('defaults.delete_confirm'), turbo_method: "delete" }, class: 'btn btn-danger' %>
      </div>
      <table class="table table-bordered bg-white">
        <tr>
          <th scope="row"><%= User.human_attribute_name(:id) %></th>
          <td><%= @user.id %></td>
        </tr>
        <tr>
          <th scope="row"><%= User.human_attribute_name(:role) %></th>
          <td><%= @user.role_i18n %></td>
        </tr>
        <tr>
          <th scope="row"><%= User.human_attribute_name(:full_name) %></th>
          <td><%= @user.decorate.full_name %></td>
        </tr>
        <tr>
          <th scope="row"><%= User.human_attribute_name(:avatar) %></th>
          <td><%= image_tag @user.avatar.url %></td>
        </tr>
        <tr>
          <th scope="row"><%= User.human_attribute_name(:created_at) %></th>
          <td><%= l @user.created_at, format: :long %></td>
        </tr>
      </table>
    </div>
  </div>
</div>
```

edit.html.erb

```ruby
<% content_for(:title, t('.title')) %>
<div class="container">
  <div class="row">
    <div class="col-md-10 offset-md-1 col-lg-8 offset-lg-2">
      <h1><%= t '.title' %></h1>
      <%= form_with model: @user, url: admin_user_path(@user), local: true do |f| %>
        <%= render 'shared/flash_error_message', model: @user %>
        <div class="form-group">
          <%= f.label :email %>
          <%= f.email_field :email, class: 'form-control' %>
        </div>
        <div class="form-group">
          <%= f.label :last_name %>
          <%= f.text_field :last_name, class: 'form-control' %>
        </div>
        <div class="form-group">
          <%= f.label :first_name %>
          <%= f.text_field :first_name, class: 'form-control' %>
        </div>
        <div class="form-group">
          <%= f.label :avatar %>
          <%= f.file_field :avatar, onchange: 'previewFileWithId(preview)', class: 'form-control', accept: 'image/*' %>
          <%= f.hidden_field :avatar_cache %>
        </div>
        <div class='mt-3 mb-3'>
          <%= image_tag @user.avatar.url,
                        class: 'rounded-circle',
                        id: 'preview',
                        size: '100x100' %>
        </div>
        <div class="form-group">
          <%= f.label :role %>
          <%= f.select :role, User.roles_i18n.invert, {}, class: 'form-control' %>
        </div>
        <%= f.submit class: 'btn btn-primary' %>
        <% end %>
    </div>
  </div>
</div>
```

_user.html.erb

```ruby
<tr>
  <td>
    <%= user.id %>
  </td>
  <td>
    <%= user.decorate.full_name %>
  </td>
  <td>
    <%= user.role_i18n %>
  </td>
  <td>
    <%= l user.created_at, format: :long %>
  </td>
  <td>
    <%= link_to t('defaults.show'), admin_user_path(user), class: 'btn btn-info' %>
    <%= link_to t('defaults.edit'), edit_admin_user_path(user), class: 'btn btn-success' %>
    <%= link_to t('defaults.delete'), admin_user_path(user), data: { turbo_confirm: t('defaults.delete_confirm'), turbo_method: "delete" }, class: 'btn btn-danger' %>
  </td>
</tr>
```

_search_form.html.erb

```ruby
<%= search_form_for @search, url: admin_users_path do |f| %>
  <div class="row">
    <div class="d-flex align-items-center justify-content-center">
      <div class="d-inline me-3">
        <%= f.search_field :first_name_or_last_name_cont, class: 'form-control', placeholder: t('defaults.search_word') %>
      </div>
      <div class="d-inline me-3">
        <%= f.select :role_eq, User.roles_i18n.invert.map{|key, value| [key, User.roles[value]]}, { include_blank: t('defaults.unspecified') }, { class: 'form-control me-1' } %>
      </div>
      <%= f.submit class: 'btn btn-primary' %>
    </div>
  </div>
<% end %>
```

### その他


shared/_sidebar.html.erb

```ruby
<nav id="sidebarMenu" class="col-md-3 col-lg-2 d-md-block bg-light sidebar">
  <div class="position-sticky pt-3 sidebar-sticky">
    <ul class="nav flex-column">
      <li class="nav-item">
        <%= link_to admin_users_path, class: "nav-link #{active_if("admin/users")}" do %>
          <%= t('.users') %>
        <% end %>
      </li>
      <li class="nav-item">
        <%= link_to admin_boards_path, class: "nav-link #{active_if("admin/boards")}" do %>
          <%= t('.boards') %>
        <% end %>
      </li>
    </ul>
  </div>
</nav>
```

今回の実装にはransackでの範囲選択とenum_helpを使用したenumのi18n化も含まれているためこちらは他のページで詳細を解説します。

[enum_help](https://github.com/HideyukiKamiyama/til/blob/main/Rails/Gem/enum_help.md)

[ransack](https://github.com/HideyukiKamiyama/til/blob/main/Rails/Gem/ransack.md)

# 参考サイト


[[Rails] 管理画面にCRUD機能を作成する](https://osamudaira.com/271/#toc4)

[[Rails] 管理画面の投稿/ユーザのCRUD19/20](https://zenn.dev/redheadchloe/articles/89ada0233227f0#検索フォームのパーシャルを作成する-1)
