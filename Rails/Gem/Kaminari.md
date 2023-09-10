# Kaminariとは


Kaminariはページネーションを実装するためのgemです。ページネーションとは投稿一覧などで多くの情報が表示される場合に画面の下部に表示されるページ一覧のような物です。詳しくは参考サイトを確認してください。




# 実装方法


1. gemをインストールします。今回は gem 'kaminari', "1.2.2” をGemfileに記述し以下のコマンドでインストールします。

```ruby
bundle install
```

1. インストール後サーバーを再起動して変更を反映させた後、

```ruby
rails g kaminari:config
```

コマンドで config/initializers/kaminari_config.rb というファイルを作成します。

1. kaminari_config.rb の編集を行います。kaminari_config.rbファイルはkaminariの設定を行うファイルで次のような内容が記述されています。

```ruby
# frozen_string_literal: true

Kaminari.configure do |config|
　#　一ページ辺りの表示件数
  config.default_per_page = 25

　#　一ページ辺りの最大表示件数
  config.max_per_page = nil

　#　現在のページを基準に左右何ページ分のページを表示するか
  config.window = 4

　#　最初と最後のページから何ページ分のページを表示するか
  config.outer_window = 0

　#　最初のページから何ページ分表示するか
  config.left = 0

　#　最後のページから何ページ分表示するか
  config.right = 0

　#　モデルに追加あれるページ番号を指定するスコープの名前（デフォルトは:page）
  config.page_method_name = :page
　
　#　ページ番号を渡すためのパラメータ名（デフォルトは:page）
  config.param_name = :page

　#　最大ページ数
  config.max_pages = nil

　#　最初のページでparamsを無視しない
  config.params_on_first_page = false
end
```

必要に応じて内容を書き換えます。

1. コントローラに次のように記述します。

board_controller.erb

```ruby
def index
    @boards = Board.all.includes(:user).page(params[:page])
    redirect_to login_path, danger: t('flash_message.require_login') unless logged_in?
end
```

通常の @boards = Board.all.includes(:user) の部分に .page(params[:page]) を付け加えます。

これにより@boardsにページネーションを実装することができます。 

1. ビューファイルのページネーションを表示したい部分に次の記述をします。

```ruby
<%= paginate @boards %>
```

これでページネーションの表示をすることができました。




# ページネーションへのBootstrapの反映方法


以下の手順でページネーションをBootstrapに反映することができます。

1. Gemfileに次の記述をし bundle install します。

```ruby
gem 'bootstrap5-kaminari-views'
```

この操作はkaminariのインストールと一緒に行って問題ありません。

1. 先ほどのビューファイルへの記述を次のように書き換えます。

```html
<%= paginate @boards, theme: 'bootstrap-5' %>
```

以上でbootstrapの反映は完了です。




# 参考サイト


### kaminari


[github kaminari](https://github.com/kaminari/kaminari)

[【Rails】Kaminariを用いたページネーション機能まとめ！導入手順、デザインの変更、日本語化についてまとめて解説｜TechTechMedia](https://techtechmedia.com/pagenation-demo-kaminari/)

[【Rails】kaminariのpageメソッドを用いて、配列のページャを作成する方法｜TechTechMedia](https://techtechmedia.com/page-for-array-kaminari/)

[【Rails】kaminariの使い方をざっくりまとめてみた](https://zenn.dev/yukihaga/articles/3d49208638e397)

[【Rails】kaminariとboostrap4を使ってページネーションをする - Qiita](https://qiita.com/mmaumtjgj/items/771deb2f3da3eecb4f54)

[Kaminariの使い方 まとめ - 猫Rails](https://nekorails.hatenablog.com/entry/2018/10/15/005146)



### kaminari（Bootstrapの適用）


[github bootstrap5-kaminari-ciews](https://github.com/felipecalvo/bootstrap5-kaminari-views)

[Rails｜kaminariの導入方法〜Bootstrapの適用](https://zenn.dev/airiin/articles/0a2d6df02dcfe0)

[kaminariを使用してbootstrap5でCSSを追加する - Qiita](https://qiita.com/mocomou_/items/c3cce91c241e08f9a50b)
