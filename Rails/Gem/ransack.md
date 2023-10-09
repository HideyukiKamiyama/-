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

predicate（述語）一覧

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

# 日付の範囲選択


〇〇月〇〇日〜〇〇月〇〇日のような日付の範囲を選択して検索する機能を実装する方法について解説します。

先ほどのこちらの表を見ると

predicate（述語）一覧

| 検索方法 | 意味(英語) | 意味 |
| --- | --- | --- |
| *_eq | equal | 等しい |
| *_not_eq | not equal | 等しくない |
| *_lt | less than | より小さい |
| *_lteq | less than or equal | より小さい（等しいものも含む） |
| *_gt | grater than | より大きい |
| *_gteq | grater than or equal | より大きい（等しいものも含む） |
| *_cont | contains value | 部分一致（内容を含む） |

以降を表すのが `_gteq` 、以前を表すのが `_lteq` であることが分かります。

このpredicateを使用して〇〇月〇〇日以降〜〇〇月〇〇日以前という日付の範囲指定を行おうとしてもSQL上の指示は次のようになってしまいます。

```ruby
SELECT COUNT(DISTINCT "boards"."id") FROM "boards" WHERE ("boards"."created_at" >= '2022-01-01 00:00:00' AND "boards"."created_at" <= '2022-01-07 00:00:00')
```

そのため、上記の例では2022年の１月７日の０時以前しか含まれていないため１月７日がほとんど検索対象から外れてしまいます。

そのため以下のようなコードを書いても狙った範囲は選択できません。

```ruby
<div class="d-inline me-3">
  <%= f.date_field :created_at_gteq %>
  <span>~</span>
  <%= f.date_field :created_at_lteq %>
</div>
```

### predicate（述語）のカスタム


`config/initializers/ransack.rb`を作成し、次のように記述します。

config/initialilzers/ransack.rb

```ruby
Ransack.configure do |config|
  config.add_predicate 'lteq_end_of_day',
                  arel_predicate: 'lteq',
                  formatter: proc { |v| v.end_of_day }
end
```

- config.add_predicate

述語の名前を定義している

- arel_predicate:

カスタム元の述語を指定

- formatter:

arel_predicateで受け取った述語を実行する前に値を変換するための関数

- proc

ブロックをオブジェクト化する。これにより `{ |v| v.end_of_day }`の部分をオブジェクト化することができる。

[class Proc (Ruby 3.2 リファレンスマニュアル)](https://docs.ruby-lang.org/ja/latest/class/Proc.html)

- { |v| v.end_of_day }

フォームに入力された日付をvで受け取り、その日付の終わり（23:59:59）までに変換する。

end_of_dayはrailsに既に定義されているメソッド。

### フォームの作成


predicateのカスタムを行ったことにより `lteq_end_of_day` というメソッドを使用することができる。

このメソッドを使って作成したフォームが次のようになる。

```html
<div class="d-inline me-3">
  <%= f.date_field :created_at_gteq %>
  <span>~</span>
  <%= f.date_field :created_at_lteq_end_of_day %>
</div>
```

これにより入力した日付の終わり（23:59:59）までを検索対処に含めることができる。


# 複数のモデルに跨る検索


## 前置


今回は掲示板サイトで掲示板を「掲示板タイトル」、「掲示板に関連したコメント」、「掲示板の作成者」の３つの要素から検索したい。

これらのモデルの関連付けは

- Post : Comment = １ : 多
- Post : User = 多 : １
- Uaer : Profile = １ : １

の関係になっている。また、UserのnameカラムはProfileモデルに保存されている。

今回は掲示板をコメント、作成者で絞り込むためあくまで一覧で表示するのは掲示板となっている。

そのためフォームの送信先や検索処理はPostコントローラで行う。

## フォームの作成


検索フォームを次のように編集する。

```html
<%= search_form_for @q, url: search_posts_path, class: "d-flex" do |f| %>
  <%= f.search_field :title_or_body_cont, class: "form-control me-2", id: "search_field_of_post", placeholder: "投稿" %>
  <%= f.search_field :comments_body_cont, class: "form-control me-2", id: "search_field_of_comment", placeholder: "コメント" %>
  <%= f.search_field :user_profile_name_cont, class: "form-control me-2", id: "search_field_of_user", placeholder: "ユーザー" %>
  <button class="btn btn-outline-success" type="submit">Search</button>
<% end %>
```

今回は検索条件に `Postモデル`だけでなく、Postモデルに関連付けされている `Commentモデル` とUserモデルを経由した `Profileモデル` も検索対象に含める必要がある。

その場合は次のサイトのようにフォームタグの要素名に、 `関連するモデル名_関連するモデルのカラム名` を指定することで関連付けされたモデルを対象に検索することができる。

今回の例で言うとフォームの送信先がPostコントローラであるため

- 掲示板のタイトル、本文での検索は `title_or_body_cont`
- コメントでの検索は `comments_body_cont`

Commentモデルにカラム名である `body` を繋げている。Postに対してCommentは複数存在するのでcommentsのように複数形にする

- ユーザー名での検索は `user_profile_name_cont`

これはUserモデルに関連付いたProfileモデルのnameカラムを指定しているためこのような記述になっている。Postに対するUser、Userに対するProfileはそれぞれ一つしか存在しないため単数系で記述している。

[参考サイト１](https://qiita.com/sew_sou19/items/520d4348b2eaa7bf792c)

[参考サイト２](https://woshidan.hatenadiary.jp/entry/2021/04/05/205301)

## コントローラの編集


- searchアクションの作成、編集

今回は検索結果を `search.html.erb` で表示しようと思います。そのためsearchアクションを新しく作成し、次のように編集します。

posts_controller.rb

```ruby
def search
  @posts = @q.result(distinct: true).order(created_at: :desc).page(params[:page]).per(10)
end
```

## ルーティングの設定


検索結果のURLを `/posts/search` としたいため、postのルーティングにネストする形でcollectionのsearchのルーティングを作成します。

```ruby
resources :posts do
  resources :comments, module: :posts
  # 以下のcollectionブロックを追記し、/posts/search で検索できるようにする。
  collection do
    get 'search'
  end
end
```

## viewファイルの作成


ルーティングに対応した `posts/search.html.erb` ファイルを作成します。内容は `posts/index.html.erb` とほぼ同じですが検索結果が無かった場合に「検索結果は０件です」と表示するようif文を使って分岐処理を行います。

posts/search.html.erb

```ruby
<div class="container">
  <div class="row">
    <div class="col">
      <ul class="list-unstyled">
        <% if @posts.present? %>
          <%= render @posts %>
        <% else %>
          <p>検索結果は０件です</p>
        <% end %>
      </ul>
      <%= paginate @posts %>
    </div>
  </div>
</div>
```

以上で複数モデルに跨る検索機能を実装できました。

# タグを使った検索


掲示板にタグを設置し、そのタグをクリックすることでタグで検索を絞ります。

タグの表示は以下のようにeach文を使用して行うことを想定しています。

```ruby
<div>
  <% post.tags.each do |tag| %>
    <%= link_to tag.name, search_posts_path(q: {tags_name_cont: tag.name}), class: "badge rounded-pill bg-primary text-decoration-none text-white" %>
  <% end %>
</div>
```

`link_to tag.name, search_posts_path(q: {tags_name_cont: tag.name})` この部分ではlink_toの送信先にパラメータとして`(q: {tags_name_cont: tag.name})`を渡している。これにより検索条件に`{tags_name_cont: tag.name}` が指定された検索オブジェクト `q` を渡すことができ、そのタグの名前で検索した結果を表示することができる。

[参考サイト](https://qiita.com/DaichiA/items/d6da57578a2923e7bac3)


# 参考サイト


[github ransack](https://github.com/activerecord-hackery/ransack)

[【Ransack翻訳】Railsで検索機能を簡単に実装できるgem「Ransack」のREADMEを翻訳してみた｜TechTechMedia](https://techtechmedia.com/ransack-readme-translation/)

[Simple mode | Ransack documentation](https://activerecord-hackery.github.io/ransack/getting-started/simple-mode/#default-search-options)

[[Rails] ransack掲示板の検索機能を実装](https://osamudaira.com/235/)

[Ransackで簡単に検索フォームを作る73のレシピ - 猫Rails](https://nekorails.hatenablog.com/entry/2017/05/31/173925)

[ransackを使って検索機能がついたアプリを作ろう！](https://pikawaka.com/rails/ransack)

[GitHub - activerecord-hackery/ransack: Object-based searching.](https://github.com/activerecord-hackery/ransack#search-matchers)

[【ransack】日付選択検索とプルダウン選択 - Qiita](https://qiita.com/mmaumtjgj/items/34117cd07e9b7aa72585)

[ransackを使って日付検索＆プルダウン選択する - Programming Journal](https://study-diary.hatenadiary.jp/entry/2020/09/06/123853)
