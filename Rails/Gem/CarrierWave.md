# CarrierWaveとは


carrierwaveとはrailsにおける画像アップロード用のライブラリ(gem)です。

似たようなライブラリにPaperclipがあります。両者の大きな違いとしてcarrierwaveはアップロードクラスを持つという点が挙げられます。

Paperclipはアップロードクラスを持たないので画像のサイズやフォーマットなどをそれぞれのアップロード先のモデルに設定します。そのためモデル毎に毎回設定をしなくてはなりません。

それに対し、carrierwaveの場合はアップロードクラスを持っており、このクラスに設定を記述することで一つの設定を複数のクラスのアップロードに対して適用することができます。

[Paperclip と CarrierWave を結構マジメに比較してみた - おいちゃんと呼ばれています](https://inouetakuya.hatenablog.com/entry/20131014/1381749488)




# インストール方法


Gemfileに

```ruby
gem 'carrierwave', '2.2.2'
```

と記述（上記の例はバージョン2.2.2の場合）し、`bundle install`する。インストール後はサーバーを再起動しないとインストールが反映されない可能性があることに注意する。




# アップローダークラスの作成


CarrierWaveインストール後、以下のコマンドでアップローダークラスを作成できる。BoardImageはアップローダークラスの名前。

```ruby
rails generate uploader BoardImage
```

上記のコマンドを実行すると`app/uploaders/board_image_uploader.rb`が作成されます。

作成されたアップローダークラスに次のような記述をします。

```ruby
class BoardImageUploader < CarrierWave::Uploader::Base
  # ファイルがデフォルトでpublic/配下に保存される
  storage :file

  # アップロードしたファイルの保存先を指定
  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end

  # デフォルトの画像ファイル
  def default_url
    'board_placeholder.png'
  end

  # 拡張子の制限
  def extension_whitelist
    %w[jpg jpeg gif png]
  end
end
```

> 保存先の指定
> 

`storage :file`はデフォルトで設定されており、この記述があることで`public/`配下に保存されるようになります。また、次の記述によりさらに細かい保存先が指定されています。

```ruby
def store_dir
  "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
end
```

一つ一つ解説します。

- model.class.to_s.underscore

オブジェクトのクラス名を取得し、アンダースコアで区切った文字列に変換するコードです。つまりモデル名を表します。

- mounted_as

ファイルのカラム名がここに入ります。

- model.id

インスタンスのidを取得してここに入れます。

つまり、`storage :file`と合わせて`public/uploads/モデル名/画像のカラム名/id`に保存されることになります。

> デフォルト画像の設定
> 

次の部分でデフォルトの画像の設定を行っています。これにより画像がアップロードされなかった場合に表示される画像を指定することができます。また、この画像は`app/assets/images/`配下に保存しておきます。

```ruby
  def default_url
    'board_placeholder.png'
  end
```

> 拡張子の制限
> 

以下の記述で保存する拡張子を制限しています。

```ruby
  def extension_whitelist
    %w[jpg jpeg gif png]
  end
```




# .gitignoreの設定


.gitignoreファイルに以下の記述をすることで保存される画像をGitHubにアップロードされないようにできます。

```ruby
/public/uploads
```

これにより``/public/uploads``に保存された画像がGitHubにアップロードされなくなります。




# アップロード画像のカラムを追加


アップロード画像のファイル名を保存するカラムを追加する。DBサーバーの容量を圧迫しないようにするため、この時保存するのは画像のファイル名のみであり、画像そのものではない。実際の画像はパスとファイル名で取得する。

1. カラムを追加するためのマイグレーションファイルを作成する

```ruby
rails g migration AddBoardImageToBoards board_image:string

# 上記のコマンドは次の意味を示しています。
rails g migration Add[追加するカラム名]To[追加先のテーブル名] [追加するカラム名]:[データ型]
```

上記のコマンドでマイグレーションファイルを作成する。

1. マイグレーションファイルの確認

```ruby
class AddBoardImageToBoards < ActiveRecord::Migration[7.0]
  def change
    add_column :boards, :board_image, :string
  end
end
```

マイグレーションファイルを確認し、必要であれば編集を加える。

1. rails db:migrate

rails db:migrateコマンドで先ほどのマイグレーションファイルを適用させることでカラムを追加します。




# ****アップローダークラスとカラムの紐付け****


```ruby
class Board < ApplicationRecord
  mount_uploader :board_image, BoardImageUploader
  belongs_to :user

  validates :title, presence: true, length: { maximum: 255 }
  validates :body, presence: true, length: { maximum: 65_535 }
end
```

上記のように`mount_uploader :board_image, BoardImageUploader`を記述することでアップローダークラス（BoardImageUploader）とカラム（board_image）を紐付けています。




# ストロングパラメータに追記する


```ruby
def board_params
  params.require(:board).permit(:title, :body, :board_image, :board_image_cache)
end
```

ストロングパラメータに追記することで追加したカラムをDBに保存できるように設定する。

board_image_cacheはバリデーションによりデータの保存に失敗した際に画像が添付されたままにするためのもの。これがあることにより、保存に失敗した際に改めて画像を添付する必要がなくなる。

また、この機能を使用するためにはフォーム側にもboard_image_cacheを送信する設定をする必要がある。




# フォームの作成


```html
<div class="container">
  <div class="row">
    <div class="col-lg-8 offset-lg-2">
      <h1>掲示板作成</h1>
      <%= form_with model: @board, class: "new_board", local: true do |f| %>
        <%= render 'shared/flash_error_message', model: @board %>
        <div class="mb-3">
          <%= f.label :title %>
          <%= f.text_field :title, class: "form-control" %>
        </div>
        <div class="mb-3">
          <%= f.label :body %>
          <%= f.text_area :body, class: "form-control", rows: 10, height: "200px" %>
        </div>
        <div class="mb-3">
          <%= f.label :board_image %>
          <%= f.file_field :board_image, class: "form-control", accept: 'image/*' %>
          <%= f.hidden_field :board_image_cache %>
        </div>
        <%= f.submit class: "btn btn-primary", "data-disable-with": "登録" %>
      <% end %>
    </div>
  </div>
</div>
```

上記のコードのうち

```html
<div class="mb-3">
  <%= f.label :board_image %>
  <%= f.file_field :board_image, class: "form-control", accept: 'image/*' %>
  <%= f.hidden_field :board_image_cache %>
</div>
```

が画像を保存するためのフォーム。

`accept: 'image/*'`はサーバーが保存するデータを制限する記述。これにより画像データのみを受け取れるようになる。

[HTML 属性: accept - HTML: ハイパーテキストマークアップ言語 | MDN](https://developer.mozilla.org/ja/docs/Web/HTML/Attributes/accept)

[accept属性 ≪ input要素 ≪ フォーム ≪ 要素 ≪ HTML5入門](http://html5.cyberlab.info/elements/forms/input-accept.html)

また、`<%= f.hidden_field :board_image_cache %>`の部分で`board_image_cache`を送信しており、`f.hidden_field`となっているためブラウザ上では確認できなくなっている。




# 画像の表示


画像を表示したいビューファイルに

```html
<%= image_tag board.board_image.url, class: 'card-img-top', size: '300x200' %>
```

と記述する。ここではアップローダークラスのurlメソッドを使用してファイルのurlを取得している。




# アップローダークラスにより使えるメソッド


アップローダークラスを作成することにより次のメソッドが使えるようになります。

1. url

上でも説明しましたが、urlメソッドを使用することでアップローダークラスが所有している画像ファイルのURLを取得できます。public配下に置く画像ファイルは、Railsの仕様でブラウザに直接送信されるので、urlメソッドとimage_tagを使って画像を表示できます。

1.  avatar_identifier

avatar_identifierメソッドを使うと、アップローダークラスのオブジェクトが所有している画像ファイル名を取得できます。




# 参考サイト


[github carrierwave](https://github.com/carrierwaveuploader/carrierwave)

[github paperclip](https://github.com/thoughtbot/paperclip)

[[Rails] CarrierWaveを使った画像アップロード](https://osamudaira.com/177/)

[CarrierWaveチュートリアル](https://pikawaka.com/rails/carrierwave)

[【Rails】Carrierwaveで画像アップロード - ぺんぎんのRails日記](https://penguintech.hatenablog.com/entry/2021/03/30/235035)

[【Rails】CarrierWaveの使い方をざっくりまとめてみた](https://zenn.dev/yukihaga/articles/e63a224431bc96)
