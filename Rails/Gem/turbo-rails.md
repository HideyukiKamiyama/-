# Turboとは


Turboとはページの一部の通信を非同期で行うための通信方法です。Turboは同じドメインへのリンクやフォームボタンを押した場合にAjax通信を行います。これにより画面の一部分だけを更新するため、素早くデータを取得することができます。

Turboには大きく３つの種類がありそれぞれ次のような違いがあります。

- Turbo Drive

HTMLのbodyタグのみを使って画面を更新します。headタグは読み込まずに既に読み込み済みのものを使うため通常よりも読み込みがはやくなります。これを利用するにはコードを何もいじる必要はなく導入するだけで使用することができる。

- Turbo Frames

画面上の一部分のみをAjax通信を使って更新します。Turbo Driveよりも小さい範囲を更新するためより早く読み込むことができます。

- Turbo Stream

こちらも画面の一部分のみを読み込む通信方法です。Turbo Framesとの違いは、Turbo Framesでは一箇所しか更新できなかったのに対し、Turbo Streamでは複数箇所更新することができます。

また、Turbo FramesがHTML要素の置換しかできないのに対し、こちらでは追加、更新、削除もできる。




# Turbo Frames


```html
<!-- app/views/posts/index.html.erb -->
<%= turbo_frame_tag "posts" do %>
  <%= render @posts %>
<% end %>

<!-- app/views/posts/_post.html.erb -->
<%= turbo_frame_tag dom_id(post) do %>
  <div class="post">
    <h2><%= post.title %></h2>
    <p><%= post.body %></p>
  </div>
<% end %>
```

上記のようにturbo_frame_tagで囲ってあげることでその部分を非同期で通信することができます。

また、上記のコードは`<turbo-frame id="posts">`のように変換されます。

- dom_idの参考サイト
    
    [Rails: Action Viewのdom_idヘルパーは実は有能（翻訳）｜TechRacho by BPS株式会社](https://techracho.bpsinc.jp/hachi8833/2022_07_06/119249)
    
    [ActionViewのdom_id便利そう - PIYO - Tech & Life -](https://blog.piyo.tech/posts/2014-09-01-200000/)
    



# Turbo Stream


画面の一部分のみ読み込むことで通信量を抑えることができる。Turbo Framesと比べて柔軟性が高い。

基本的な記述法としては`turbo_stream.アクション名`の後に`ターゲット名`を記述するのが一般的であり、このターゲット名で**どこのidの要素を変えたいかを指定して使う**のが基本となっている。




### Turbo Streamのメソッドと記述方法


Gemfileにturbo-railsを読み込むことでTurbo Streamのメソッドを使えるようになります。

メソッドは７つありますが代表的なもののみまとめます。

- append(対象の末尾に追加)
- prepend(対象の先頭に追加)
- replace(対象の要素を要素ごと置き換え）
- update(対象の要素のコンテンツを置き換え）
- remove(対象を削除)

これらのメソッドによる処理ではデータベースは変更していない。**あくまでブラウザ上の表示を部分的に変更し、通信量を削減するための処理**となる。

コントローラのアクション内でデータの追加（削除）などの処理を行った場合、画面上の変化はリストが一つ増える（減る）程度の場合が多く、そのような時に画面全てを再読込みしていたら通信量が無駄に多くなってしまう。

これを防ぐためにアクション実行後にredirect_toやrenderで`〇〇.html.erb`ファイルを読み込むのではなく、turbo-steramを実行する。

turbo-streamの実行方法は大きく二つあり、

1. コントローラ内でturbo-streamを実行する
2. アクション名.turbo-stream.erbを作成しそのファイルをrenderした後、ファイル内のturbo-streamを実行する

のどちらかで実行する。




### 省略記法


prependを例にしたこれらの記法は全て以下の記述と同じ処理を行う。

```html
<%# この4つは全て同じ結果になる %>

<%# 1. 省略形 %>
<%= turbo_stream.prepend "cats", @cat %>

<%# 2. renderを省略しない場合 %>
<%= turbo_stream.prepend "cats" do %>
  <%= render @cat %>
<% end %>

<%# 3. renderのオプションを省略しない場合 %>
<%= turbo_stream.prepend "cats" do %>
  <%= render partial: "cates/cat",
             locals: { cat: @cat } %>
<% end %>

<%# 4. renderのオプションをturbo_stream.prependのオプションとして使用する場合 %>
<%= turbo_stream.prepend "cats",
      partial: "cates/cat",
      locals: { cat: @cat } %>
```

上記の省略記法はディレクトリ構造によっては省略できない場合もあるため必要に応じて変更する。




### それぞれのメソッド毎の使い方


- append、prepend

```html
<%# turbo_stream.<アクション名> <ターゲットのid>, <追加するオブジェクト> %>
<%= turbo_stream.prepend "cats", @cat %>
<%= turbo_stream.append "cats", @cat %>
```

- replace、update

```html
<%# turbo_stream.<アクション名> <更新するオブジェクト> %>
<%= turbo_stream.replace @cat %>
<%= turbo_stream.update @cat %>
```

replaceとupdateは両方とも更新処理だが、replaceがターゲットの要素も含めて更新するのに対してupdateはターゲットのコンテンツのみを更新する。

updateメソッドはターゲットのコンテンツ（中身）を更新するメソッドであるため変更したい要素の外側のHTMLタグのid属性をターゲットに実行する。

- remove

```html
<%# turbo_stream.<アクション名> <削除するオブジェクト> %>
<%= turbo_stream.remove @cat %>
```

ディレクトリ構造の都合で省略記法が使えない場合はオブジェクトの代わりにターゲットのidを記述すれば良い。removeはただ削除するだけであるためオブジェクトを渡さなくてもどのidの要素を削除するかが分かれば画面上から削除することができる。

これらの記述は無理に省略記法を使うのではなく、ブロックを使った記法を用いると良い。




### 実例


turbo-streamの記述はコントローラに記述する方法とビューファイルに記述する方法がある。それぞれ記述方法が少し異なるため例を含めて解説する。

> ビューファイルに記述する場合
> 

以下のような`アクション名.turbo-stream.erb`という名前のファイルを作成しその中に記述します。そのアクションを実行した際に変更したい部分が複数ある場合はファイル内にturbo-streamの処理を複数記述することで実行するこができます。

create.turbo-stream.erb

```html
<%= turbo_stream.replace "bookmark-button-for-board-#{@board.id}" do %>
  <%= render 'bookmarks/unbookmark', board: @board %>
<% end %>
```

destroy.turbo-stream.erb

```html
<%= turbo_stream.replace "unbookmark-button-for-board-#{@board.id}" do %>
  <%= render 'bookmarks/bookmark', board: @board %>
<% end %>
```

上記のようにturbo-streamで対象の部分を挟むことによりその部分を非同期で通信することが可能になる。

上記のようにビューファイルに記述する場合、コントローラを以下のように記述しますが、今回のようにrenderやredirect_toの指定がない場合はレンダリングされるファイルはそのコントローラのアクション名と同じものになるため実行したアクションによって`create.turbo-stream.erb`や`destroy.turbo-stream.erb`を呼び出すことができる。

これは、userの新規登録でusersコントローラのnewアクションを踏んだ場合にusers/newに移動するのと同じ原理であり、Railsにはデフォルトでこのような機能が備わっている。

```ruby
class BookmarksController < ApplicationController
  def create
    @board = Board.find(params[:board_id])
    current_user.bookmark(@board)
  end

  def destroy
    @board = current_user.bookmarks.find(params[:id]).board
    current_user.unbookmark(@board)
  end
end
```

今回の場合は bookmarks/create.turbo_stream.erb と destroy.turbo-stream.erb を作成しているため

```html
createアクション　→　bookmarks/create.turbo_stream.erb 

dstroyアクション　→　destroy.turbo-stream.erb
```

がそれぞれ呼び出される。

> コントローラに記述する場合
> 

コントローラ内でのturbo-streamの記述はrespond_toを使う方法と使わない方法があります。（respond_toはturbo-streamで検索するとかなり出てくるため混乱しやすいと感じ、場合分けしてみました）

1. コントローラ内におけるturbo-streamの一般的な記述法

次のように記述することでコントローラからturbo-streamを実行することができます。

```ruby
if @post.save
  render turbo_stream: turbo_stream.prepend(
    "posts",
    partial: "posts/post",
    locals: { post: @post })
else
  render :new, status: :unprocessable_entity
end
```

今回の例ではprependを使って対象の先頭に要素を追加しています。第一引数の”posts”はturbo-streamで変更したい部分のidを指定しています。

1. respond_toを使う方法

```ruby
class PostsController < ApplicationController
  def create
    @post = Post.new(post_params)

    respond_to do |format|
      if @post.save
        format.turbo_stream { render turbo_stream: turbo_stream.prepend("posts", partial: "posts/post", locals: { post: @post }) }
        format.html { redirect_to @post, notice: "Post was successfully created." }
      else
        format.html { render :new, status: :unprocessable_entity }
      end
    end
  end

  # ...
end
```

respond_toは受け取ったフォーマットの種類によって処理を分けるインスタンスメソッドです。

format.〇〇でフォーマットを指定し、その後ろに対応する処理を記述します。これによりturbo-streamの処理と普通のhtmlの処理を分けることができます。（正直使い方はよく分かってない。疲れたので今度調べる）

- respond_to参考サイト
    
    [[Rails]respond_toについて勉強してみた！！[初心者] - Qiita](https://qiita.com/jackie0922youhei/items/b597e337feae1524cd8f)
    
    [[Rails]respond_toメソッドでリクエストフォーマットによってレンダリングを変える](https://zenn.dev/yusuke_docha/articles/3220613a756b71)
    



# 参考サイト


[Rails で JavaScript を利用する - Railsガイド](https://railsguides.jp/v7.0/working_with_javascript_in_rails.html#turbo)

[Turbo Reference](https://turbo.hotwired.dev/reference/streams)

[Turbo Handbook](https://turbo.hotwired.dev/handbook/streams)

[Turbo Streams（Fetch）｜猫でもわかるHotwire入門 Turbo編](https://zenn.dev/shita1112/books/cat-hotwire-turbo/viewer/turbo-streams-fetch)

[Rails 7での新機能: Turbo FramesとTurbo Streamsを活用した実践ガイド](https://blog.to-ko-s.com/turbo-frames-turbo-streams/)

[Hotwire（Turbo）を試す その2: Turbo Streamsでページの一部の差替・追加 - Qiita](https://qiita.com/kazutosato/items/74ab0a22d41cd8859fad)

[GitHub - hotwired/turbo-rails: Use Turbo in your Ruby on Rails app](https://github.com/hotwired/turbo-rails/tree/main)

[github turbo-stream](https://github.com/hotwired/turbo-rails/blob/main/app/models/turbo/streams/tag_builder.rb#L1)


- RUNTEQ課題ヒント
    
    # **ブックマークボタンのajax化の課題ヒント**
    
    # **ヒント**
    
    ## **同期通信とは**
    
    クライアントとサーバー間の通信においては、通常、**同期通信**と呼ばれる方法が用いられます。
    
    一瞬画面が白くなった後、画面が切り替わったり、ブラウザのタブのところにくるくるとしたアニメーションが出てくるような動作の特徴があります。
    
    !https://s3-ap-northeast-1.amazonaws.com/runteq-production/uploads/document_image/name/7/synchronous_communication.gif
    
    図からも分かるように、この同期通信の間、クライアントは他の作業を行うことができません。
    
    これは、クライアントからサーバーに対してページ全ての情報を返すようリクエストが送られているためであり、リクエストを送ったクライアントは、サーバーからの応答があるまでその結果を待機し、結果を受け取った後に画面全体を切り替える処理を行うのです。
    
    ## **非同期通信とは**
    
    同期通信の欠点を補うために生み出されたのが非同期通信です。
    
    非同期通信では、クライアントからのリクエスト送信後、サーバーの処理中にも、他の作業を行うことができます。
    
    また、ブラウザの一部の画面のみを更新するような使われ方をすることが多く全体を更新するよりも画面が描き変わるまでにかかる時間が短くなりやすいです。
    
    !https://s3-ap-northeast-1.amazonaws.com/runteq-production/uploads/document_image/name/8/asynchronous_communication.gif
    
    これは、クライアントからサーバーに対してページの一部の情報を返すようリクエストが送られているためであり、この技術により、ユーザーに処理待ちの時間を与えずに、裏では随時通信が行われている。　といったサービスの設計が可能となるのです。
    
    ## **Ajaxとは**
    
    **ajax**とは、Asynchronous JavaScript + XML の略で、**非同期通信**を可能にする通信方法のことを指します。
    
    ※Railsで使われる**turbo**はAjaxを使ってサーバーと通信しています
    
    ajaxを使用したサービス例: Google Map
    
    !https://s3-ap-northeast-1.amazonaws.com/runteq-production/uploads/document_image/name/9/google_map.gif
    
    ## **TurboDriveについて**
    
    Rails7系でturboを使っている場合、画面遷移は基本的にTurboDriveという機能を使って行われます。
    
    turboは同じドメインへのリンクやFormの送信ボタンを押すことによるリクエストを全てAjaxリクエストを使って行います。
    
    その後帰ってきたHTMLを使って画面を書き換えるのですが、この時書き換えるのはHTMLのbodyタグのみが対象になります。
    
    こうすることでheadタグなどに含まれるアプリ共通で使用するものを再度読み込みすることなく素早い画面遷移を実現しています。
    
    Ajaxリクエストを使ってはいますが、同期通信でページ全体を書き換えるものに近いようなイメージを持っていただくと理解しやすいかと思います。
    
    ## **TurboFlameについて**
    
    TurboDriveがbody全体を対象に更新するのに対してTurboFlameはturbo-frameタグで囲んだ一部のみを更新することが出来ます。
    
    これによってTurboDriveよりもより素早く画面を描き変え表示することが出来ます。
    
    ## **TurboStreamについて**
    
    TurboFlameがturbo-frameタグで囲んだ一部分のみしか対象に出来ないのに対して、TurboStreamは画面上の複数の要素を対象に描き変えることが出来ます。
    
    TurboFlameより柔軟に扱うことが出来る非同期通信方法のようなイメージで使い分けられると良いでしょう。
    
    ## **turbo_streamの使い方**
    
    turbo_streamは指定したidの値を持つHTMLに対して
    
    - append(対象の末尾に追加)
    - prepend(対象の先頭に追加)
    - replace(対象の要素を置き換え）
    - remove(対象を削除)
    
    などの操作を行います
    
    操作を行う際は行うコントローラーのアクションとアクション名に対応するViewファイルを作り、そのファイル内でturbo_streamを使ってどんなことを行いたいかを記述します
    
    例えば`id="runteq-member"`を持つ要素に対して要素の末尾に何か要素を追加したい場合は下記のような操作を行うことで実現できます
    
    リンクをクリックした際にAddMembersControllerのcreateアクションが実行される場合
    
    ```
    <%= link_to 'メンバー追加', add_members_path, data: { turbo_method: :post } %>
    <ul id="runteq-member">
      <li>ひさじゅ</li>
    </ul>
    
    ```
    
    リンクがクリックされたらAddMembersControllerのcreateアクションが実行される
    
    ```ruby
    class AddMembersController
      def create# アクション名に対応する`create.turbo_stream.erb`というViewファイルが呼び出される
      end
    end
    
    ```
    
    アクション名に対応する`create.turbo_stream.erb`がレンダリングされ、`id='runteq-member'`を持つ要素に対して`<li>らんてくん</li>`が追加される
    
    ```
    <%= turbo_stream.append 'runteq-member' do %>
      <li>らんてくん</li>
    <% end %>
    
    ```
    
    上記のながれを参考にブックマーク機能に置き換えてみましょう
    
