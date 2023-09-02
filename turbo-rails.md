# turbo-rails

---

# Turboとは

---

Turboとはページの一部の通信を非同期で行うための通信方法です。Turboは同じドメインへのリンクやフォームボタンを押した場合にAjax通信を行います。これにより画面の一部分だけを更新するため、素早くデータを取得することができます。

Turboには大きく３つの種類がありそれぞれ次のような違いがあります。

- Turbo Drive

HTMLのbodyタグのみを使って画面を更新します。headタグは読み込まずに既に読み込み済みのものを使うため通常よりも読み込みがはやくなります。これを利用するにはコードを何もいじる必要はなく導入するだけで使用することができる。

- Turbo Frames

画面上の一部分のみをAjax通信を使って更新します。Turbo Driveよりも小さい範囲を更新するためより早く読み込むことができます。

- Turbo Stream

こちらも画面の一部分のみを読み込む通信方法です。Turbo Framesとの違いは、Turbo Framesでは一箇所しか更新できなかったのに対し、Turbo Streamでは複数箇所更新することができます。

また、Turbo FramesがHTML要素の置換しかできないのに対し、こちらでは追加、更新、削除もできる。

# Turbo Streamのメソッド

---

Gemfileにturbo-railsを読み込むことでTurbo Streamのメソッドを使えるようになります。

以下が代表的なメソッドです。

- append(対象の末尾に追加)
- prepend(対象の先頭に追加)
- replace(対象の要素を置き換え）
- remove(対象を削除)

# Turbo Frames

---

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

---

1. コントローラの記述

Turbo Streamを使う場合のコントローラの例を以下に示します。

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

format.〇〇でフォーマットを指定し、その後ろに対応する処理を記述します。

- respond_to参考サイト
    
    [[Rails]respond_toについて勉強してみた！！[初心者] - Qiita](https://qiita.com/jackie0922youhei/items/b597e337feae1524cd8f)
    
    [[Rails]respond_toメソッドでリクエストフォーマットによってレンダリングを変える](https://zenn.dev/yusuke_docha/articles/3220613a756b71)
    

また、フォーマットによって処理を分けない場合は次のようにrespond_toを使わずに記述します。

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

今回の例ではprependを使って対象の先頭に要素を追加しています。第一引数の”posts”はturbo_frame_tagのidを指定しており、対応するturbo_frame_tagを処理します。

今回のカリキュラムではコントローラを次のように記述しましたが、今回のように送信先の指定がない場合はレンダリングされるファイルはそのコントローラのアクション名と同じものになります。

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

今回の場合はbookmarks/create.turbo_stream.erbを作成しているためcreateアクションを踏んだ場合はこのファイルが呼び出されます。

1. ビューファイルの記述

```html
<%= turbo_frame_tag "bookmark-button" do %>
	<div class="ms-auto">
	  <% if current_user.bookmark?(board)  %>
	    <%= render 'bookmarks/unbookmark', board: board %>
	  <% else  %>
	    <%= render 'bookmarks/bookmark', board: board %>
	  <% end %>
	</div>
<% end %>
```

このように記述することでid=”bookmark-button”を対象とした変更を受け付けることができる。

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
    
- RUNTEQ復習ポイント
    
    # **ブックマークボタンのajax化の課題復習ポイント**
    
    # **復習ポイント**
    
    ## **アクション名に合わせて`アクション名.turbo_stream.erb`を作成していること**
    
    gem turbo-railsを使用するとturboを使っている時のリクエストが送られた時に自動的にアクション名と同名のturbo_stream.erbファイルを探して返します
    
    https://github.com/hotwired/turbo-rails/blob/main/app/models/turbo/streams/tag_builder.rb#L1
    
    create.turbo_stream.erb
    
    ```
    <%= turbo_stream.replace "bookmark-button-for-board-#{@board.id}" do %>
      <%= render 'boards/unbookmark', board: @board %>
    <% end %>
    
    ```
    
    ## **replaceメソッドを使っていること**
    
    replaceを使うと引数に渡された文字列と同名のIDをもつ要素を描き変えます。
    
    https://github.com/hotwired/turbo-rails/blob/main/app/models/turbo/streams/tag_builder.rb#L53
    
    app/views/boards/_unbookmark.html.erb
    
    ```
    <%= link_to bookmark_path(current_user.bookmarks.find_by(board_id: board.id)), id: "unbookmark-button-for-board-#{board.id}", data: { turbo_method: :delete } do %>
      <i class="bi bi-star-fill"></i>
    <% end %>
    
    ```
    
    destroy.turbo_stream.erb
    
    ```
    <%= turbo_stream.replace "unbookmark-button-for-board-#{@board.id}" do %>
      <%= render 'boards/bookmark', board: @board %>
    <% end %>
    ```
    

# 参考サイト
