# ブックマーク機能

---

# 中間テーブルの作成

---

今回はuserモデルとboardモデルの二つのモデル間でブックマークができるように実装していきます。そのためにまずはBookmarkモデルという中間テーブルを作成します。

まずは次のコマンドを実行しBookmarkモデルを作成します。

```ruby
rails g model Bookmark user:references board:references
```

`user:references board:references`を記述することによりuse_idとboard_idという二つの外部キーを作成することができます。

このコマンドを実行するとマイグレーションファイルができるため次のように編集し、`rails db:migrate`します。

```ruby
class CreateBookmarks < ActiveRecord::Migration[7.0]
  def change
    create_table :bookmarks do |t|
      t.references :user,       foreign_key: true
      t.references :board,      foreign_key: true

      t.timestamps
    end
    # user_idとboard_idのインデックスを作成している。uniqe: trueをつけることで一意の制約をつけている。
    add_index :bookmarks, [:user_id, :board_id], unique: :true
  end
end
```

`add_index :bookmarks, [:user_id, :board_id], unique: :true`を記述することによりuser_idとboard_idのインデックスを作成し、一意の制約をつけています。これによりbookmarksテーブルに対して特定のユーザーと特定のboardの組み合わせを迅速に検索しやすくするためのユニークなインデックスを作成しています。

[migrationファイルのadd_indexは何なのか - Qiita](https://qiita.com/mmaumtjgj/items/9f4fca618d2f395f985e)

# アソシエーションの設定

---

ブックマーク機能に関連する３つのモデル（user、board、bookmark）にアソシエーションを設定する。今回の関係はuserモデルとboardモデルがbookmarkモデルを通じて多対多の関係を構築しているためhas_many :throughを使用する。

それぞれの記述は次のようになる

1. Boardモデル

掲示板とブックマークは一対多であり、掲示板が削除された場合にブックマークも削除したいため次のように記述する。

```ruby
class Board < ApplicationRecord
  belongs_to :user
  has_many :bookmarks, dependent: :destroy
end
```

1. Userモデル

ユーザーとブックマークは一対多であるため`has_many :bookmarks`と記述し、ユーザーが削除された際にブックマークも削除するため dependent: :destroy`と記述。

また、bookmarksを通じたboardとの関連を示すために`has_many :bookmark_boards, through: :bookmarks, source: :board`と記述。

```ruby
class User < ApplicationRecord
  has_many :boards, dependent: :destroy
  has_many :bookmarks, dependent: :destroy
  has_many :bookmark_boards, through: :bookmarks, source: :board
end
```

- sourceオプションについて
    
    sourceオプションは関連付け元と関連付け名が異なる場合に関連付け元を示すために使用します。本来、
    
    ```ruby
    has_many :bookmark_boards, through: :bookmarks, source: :board
    ```
    
    の部分は
    
    ```ruby
    has_many :board, through: :bookmarks
    ```
    
    と同じ意味を表していますが、この記述だとbookmarkクラスを通さない関連付け（has_many :boards, dependent: :destroy）との区別ができなくなってしまうため
    
    ```ruby
    current_user.board
    ```
    
    と記述した際に「自分の投稿」なのか「自分がブックマークした投稿」なのかが分からなくなってしまいます。このような時に関連付けのメソッド名を変えてあげる必要があり、その際に使用するのがsourceオプションです。このオプションを使用することにより関連付け名を変えつつどのクラスとの関連を表しでいるのかを示す事ができます。
    
    つまり
    
    ```ruby
    has_many :bookmark_boards, through: :bookmarks, source: :board
    ```
    
    と記述することでブックマークした投稿を次のように取得することができるようになり、
    
    ```ruby
    current_user.bookmark_boards
    ```
    
    と
    
    ```ruby
    current_user.board
    ```
    
    を区別することができるようになります。
    
    [Active Record の関連付け - Railsガイド](https://railsguides.jp/association_basics.html#has-many関連付けの詳細な参照)
    
    [【Rails】class_nameとsourceオプションで分かりやすいアソシエーション名をつけよう｜Rubinistを目指す新米エンジニアのTECH BLOG](https://sakaishun.com/2021/03/20/classname-source/)
    

1. Bookmarkクラス

belongs_toオプションを使用してuserモデルとboardモデルとの関連を記述する。

```ruby
class Bookmark < ApplicationRecord
  belongs_to :user
  belongs_to :board
  # 一人のユーザーが一つのboardに対して一回しかブックマークできないように設定している
  validates :user_id, uniqueness: { scope: :board_id }
end
```

[Rails4のhas_many throughで多対多のリレーションを実装する - Qiita](https://qiita.com/samurai_runner/items/cbd91bb9e3f8b0433b99)

# バリデーションの設定

---

bookmarkモデルにバリデーションの設定を行う。

```ruby
class Bookmark < ApplicationRecord
  belongs_to :user
  belongs_to :board
  # 一人のユーザーが一つのboardに対して一回しかブックマークできないように設定している
  validates :user_id, uniqueness: { scope: :board_id }
end
```

上記の`validates :user_id, uniqueness: { scope: :board_id }`は次のように記述することもできる。

```ruby
validates_uniqueness_of :board_id, scope: :user_id
```

また、**belongs_toオプションでアソシエーションの設定をしている場合、`presence: true`は自動で設定されるため記載不要。**記載するとコード量が増え可読性が下がってしまう他、余計なクエリを発行してしまうため処理速度も下がってしまう。

```ruby
# このように書いてはいけない！！！
`validates :user_id, presence: true, uniqueness: { scope: :board_id }`
```

- uniqunessのscopeオプション
    
    user_idとboard_idに一意の制約をつけたい場合はBookmarkモデルに次のように記述します。
    
    ```ruby
    class Bookmark < ApplicationRecord
      belongs_to :user
      belongs_to :board
      # 一人のユーザーが一つのboardに対して一回しかブックマークできないように設定している
      validates :user_id, uniqueness: { scope: :board_id }
    end
    ```
    
    `validates :user_id, uniqueness: { scope: :board_id }`の記述があることによってuser_idとboard_idの組み合わせが一意なものになるように設定しています。つまり一人のユーザーが一つのboardに対して一度しかBookmarkする事ができなくなります。
    
    もしscopeオプションを使わなかった場合次のようになります。
    
    ```ruby
    class Bookmark < ApplicationRecord
      belongs_to :user
      belongs_to :board
      validates :user_id, uniqueness: true
    end
    ```
    
    この記述法ではuser_idがBookmarkモデルに対して一意になっているためBookmarkモデル内に同じuser_idが一つしか存在できない状態になっています。つまり一人のユーザーはどれか一つのboardにしかブックマークができなくなってしまいます。
    
    また、scopeオプションは複数のカラムに対して設定することもできます。
    
    [Active Record バリデーション - Railsガイド](https://railsguides.jp/active_record_validations.html#uniqueness)
    
    [[Rails] 掲示板にブックマーク機能の実装方法](https://osamudaira.com/217/)
    
    [[Rails]uniqueness: scopeを使ったバリデーション](https://osamudaira.com/515/)
    
    [【Rails】複数のカラムを使ったユニーク制約の方法【uniqueness: scope】 – なえのメモ帳](https://310nae.com/rails-uniqueness/)
    

# ルーティング

---

```ruby
Rails.application.routes.draw do
  root "static_pages#top"

  resources :users, only: %i[new create]
  resources :boards do
    resources :comments, only: %i[create], shallow: true
    collection do # <= 追加
			get :bookmarks # <= 追加
		end # <= 追加
  end
  resources :bookmarks, only: %i[create destroy] # <= 追加

  get "login", to: "user_sessions#new"
  post "login", to: "user_sessions#create"
  delete "logout", to: "user_sessions#destroy"
end
```

> コレクション、メンバーについて
> 

通常Restfulなルーティングはデフォルトで７つ（new、index、create、show、destroy、edit、uptade）があり、new、index、createのようなidを持たないアクションをコレクション、show、destroy、edit、updateのようなidを持つアクションをメンバーと呼びます。

これらのアクションは必ず７つでなくてはないわけではなく、自分で追加することができます。上記の記述法では自作したコレクションであるbookmarksをboardsにネストする形で作成しており、これにより`bookmarks_boards_path`のようなパスを使用する事ができるようになります。

また、このようにネストする方法は作成するURLを`/boards/bookmarks`とすることができるためURLから何をしているのかを推測しやすくするメリットがあります。

[Rails のルーティング - Railsガイド](https://railsguides.jp/routing.html#restfulなアクションをさらに追加する)

今回はブックマークした掲示板の一覧を表示したいため、idを伴わないことからコレクションを使用しています。また、次のように:on オプションを使用することで一行で記述することもできます。

```ruby
get 'bookmarks', on: :collection
```

# コントローラ

---

コントローラを作成する前にコントローラ内で使用するメソッドをUserモデルに記述します。このようにモデルにメソッドを記述する方法は良く使うので覚えておくこと。

```ruby
def bookmark?(board)
  bookmark_boards.include?(board)
end

def bookmark(board)
  bookmark_boards << board
end

def unbookmark(board)
  bookmark_boards.destroy(board)
end
```

1. bookmark?メソッド

```ruby
def bookmark?(board)
  bookmark_boards.include?(board)
end
```

このメソッドはinclude?メソッドを使用することでブックマークされているかを確認するのに使用します。include?メソッドは引数に渡された値（今回はその中のid）がレシーバに入っているかを確認し、入っていればtrue、入っていなければfalseを返します。

こちらのメソッドは`Bookmark.where(user_id: id, board_id: board.id).exists?`と同じ処理を行いますが、可読性の観点から上記の方が好ましい。

1. bookmarkメソッド

```ruby
def bookmark(board)
  bookmark_boards << board
end
```

bookmark_board内から引数で渡したboardのboard_idを探し、無ければ追加する。これは`bookmarks.create(board_id: board.id)`と同じような処理になります。

[Array#<< (Ruby 3.2 リファレンスマニュアル)](https://docs.ruby-lang.org/ja/latest/method/Array/i/=3c=3c.html)

1. unbookmarkメソッド

```ruby
def unbookmark(board)
  bookmark_boards.destroy(board)
end
```

引数で渡したboardのboard_idを探し存在すれば削除する。これは`bookmarks.destroy(bookmarks.find_by(board_id: board.id))`

と同じような処理になります。

[validation](https://www.notion.so/validation-56363bb81a154db9868de1242a5d5429?pvs=21)
