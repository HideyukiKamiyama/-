# ransackとは


ransackとはRailsで簡単に検索機能を実装するためのgemです。簡単に記述できるシンプルモードとより複雑な条件を設定できるアドバンスモードがあります。




# コントローラの記述


```html
def index
  @posts = Post.all.includes(:user).order(created_at: :desc)
end
```

上記のコードを次のように書き換えます。

```ruby
def index
  @q = Post.ransack(params[:q])
  @posts = @q.result(distinct: true).includes(:user).order(created_at: :desc)
end
```

上記のコードについての説明です。

- params[:q]は検索パラメータです。ransackのヘルパーメソッドである`serch_form_for`で送信される情報が入っています。
- ransackメソッドは渡されたparams[:q]のパラメータを元に該当するデータを探します。whereのrausack版と捉えて良いそうです。
- resultはransackで取得したデータ(Ransack::Searchオブジェクト)をActiveRecord_Relationオブジェクトに変換します。
- distinct: trueは重複を取り除く際に記述します。これは掲示板に対してコメントが投稿できる場合などに役立ちます。例えば掲示板Aに〇〇という文字が含まれたコメントが二つあった場合に〇〇で検索を行うと掲示板Aが二つ検索結果に表示されてしまいます。distinct: trueを記述することでこの重複を防ぐことができます。

この書き換えを行うことにより検索パラメータが渡された場合にその条件に該当するデータを絞り込むことができます。




# Viewの記述


Viewファイルではransackのヘルパーメソッドである`serch_from_for`を使用します。これは`from_with`のransack版のようなものです。以下のようにフォーム部分を部分テンプレートにすると再利用性が高くなります。

```html
# app/views/boards/_search_form.html.erb

<%= search_form_for q, url: url do |f| %>
  <div class="input-group mb-3">
    <%= f.search_field :title_or_body_cont, class: 'form-control', placeholder: '検索ワード' %>
    <div class="input-group-append">
      <%= f.submit '検索', class: 'btn btn-primary' %>
    </div>
  </div>
<% end %>
```

```html
# app/views/boards/index.html.erb

<%= render 'search_form', q: @q, url: boards_path %>
```

```html
# app/views/boards/bookmarks.html.erb

<%= render 'search_form', q: @q, url: bookmarks_boards_path %>
```

コードの解説です。

- <%= search_form_for q, url: url do |f| %>

search_form_forはransackをインストールすると使えるようになるヘルパーメソッドです。フォームデータを格納したqを渡しており、この値をアクション内のparams[:q]で受け取ります。

urlには送信先を指定しており、パスを記述することが多いです。今回は部分テンプレートとしての再利用性を高めるためにurlと記述しており、呼び出し元でurlにパスを渡しています。

- f.search_field

search_fieldは検索欄を作成します。HTMLタグのtype=”search”が記載されているフォームです。こちらは検索欄に文字を入力すると右端に✖️印が表示されこれをクリックすることで中身を削除することができるようになります。

- title_or_body_cont

title_or_body_countはtitleカラムまたはbodyカラムの内部から入力した文字列が含まれているか部分一致で検索します。title_countにしたらtitleカラムのみから探し、body_countにしたらbodyカラムから探すようになります。この*_countの部分は検索の方法を示しており（今回は部分一致）、検索方法ごとに種類が存在します。

| 検索方法 | 意味(英語) | 意味 |
| --- | --- | --- |
| *_eq | equal | 等しい |
| *_not_eq | not equal | 等しくない |
| *_lt | less than | より小さい |
| *_lteq | less than or equal | より小さい（等しいものも含む） |
| *_gt | grater than | より大きい |
| *_gteq | grater than or equal | より大きい（等しいものも含む） |
| *_cont | contains value | 部分一致（内容を含む） |

- <%= render 'search_form', q: @q, url: boards_path %>、<%= render 'search_form', q: @q, url: bookmarks_boards_path %>

これらはViewファイルからsearch_formの部分テンプレートを呼び出すためのコードです。

コントローラのアクション内で設定した@qオブジェクトを渡すことで検索条件を渡しています。

またurlにパスを渡すことで呼び出したフォームの送信先を設定しています。今回はフォーム内で

`<%= search_form_for q, url: url do |f| %>`という書き方をしているため、render内でurlにパスを渡すことで送信先を設定できます。このような記述をすることでパーシャルの再利用性が高くなっています。




# labelタグについて


labelタグは入力フォームに何を入力するかをユーザーが判別しやすくするためのタグです。

ransackについて検索するとlabelタグを記述しているパターンが多いが、検索フォームのように入力欄が一つしかなく、何を入力するかが明らかである場合にはlabelタグは記述しなくても問題ありません。




# serch_formの部分テンプレートを使う際の注意点


serch_formの部分テンプレートをを使用する際に部分テンプレート内でのURLの書き方を以下のようにしてしまうと部分テンプレートの再利用性が落ちてしまう。

```html
# app/views/boards/_search_form.html.erb

<%= search_form_for q, url: request.path do |f| %>
  <div class="input-group mb-3">
    <%= f.search_field :title_or_body_cont, class: 'form-control', placeholder: '検索ワード' %>
    <div class="input-group-append">
      <%= f.submit '検索', class: 'btn btn-primary' %>
    </div>
  </div>
<% end %>
```

- requestオブジェクト

requestオブジェクトはクライアントからのリクエストに関する情報が含まれています。request.pathとすることで現在のフォームを呼び出したパスを取得することができます。

そのため上記のようにrequest.pathとすることで現在のファイルを表示したpathを取得できるのですが、再利用性の観点から良くないとされています。

例えばshowアクションのようなidを必要とするページでこのテンプレートを使用したいと考えたとするとこのrequest.pathは`show/1`のようになってしまい、そのページにしか移動することができなくなってしまいます。（本来であれば検索後は一覧画面などに遷移したい）

そのため`search_form_for`の引数のurlに`request.path`を使用することは推奨されておらず、呼び出し元の`render`で検索結果をどのページで表示するのかを考えてパスを渡してあげる必要があります。

[Action Controller の概要 - Railsガイド](https://railsguides.jp/action_controller_overview.html#requestオブジェクト)




# 参考サイト


[github ransack](https://github.com/activerecord-hackery/ransack)

[【Ransack翻訳】Railsで検索機能を簡単に実装できるgem「Ransack」のREADMEを翻訳してみた｜TechTechMedia](https://techtechmedia.com/ransack-readme-translation/)

[Simple mode | Ransack documentation](https://activerecord-hackery.github.io/ransack/getting-started/simple-mode/#default-search-options)

[[Rails] ransack掲示板の検索機能を実装](https://osamudaira.com/235/)

[Ransackで簡単に検索フォームを作る73のレシピ - 猫Rails](https://nekorails.hatenablog.com/entry/2017/05/31/173925)

[ransackを使って検索機能がついたアプリを作ろう！](https://pikawaka.com/rails/ransack)
