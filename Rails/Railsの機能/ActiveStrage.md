# Active Strageとは

Rails5.2から導入されたファイルのアップロード機能です。
Active Strageを使うことで対象のテーブルにカラムを追加することなく画像や動画、音楽といったデータをアップロードすることができるようになります。


# 導入方法

## Active Strageのダウンロード方法

```
rails active_storage:install
rails db:migrate
```

上記を実行するとActive Strageで利用する３つのテーブルが作成されます。

### active_storage_blobs
  
アップロードされたファイルに関するデータ（ファイル名、Content-Typeなど）を保存します。
画像などのデータそのものではなくデータに関するメタデータ（ファイル名、ファイルの種類、ファイルサイズ、ストレージプロバイダの情報など）のみが保存されています。
  
### active_storage_attachments
  
モデルをblobsに接続するポリモーフィックjoinテーブルです。
  

# ファイルの保存先の設定

`active_storage_blobs`テーブルにはファイルのメタデータ（ファイル名、ファイルの種類、ファイルサイズ、ストレージプロバイダの情報など）のみが保存されており、データそのものは保存されていないため、実際のデータを保存するサービスを指定する必要があります。
その保存先は次のように`config/environments/development.rb`ファイルに記述します。
以下の例では`:local`に保存先を指定しています。

```ruby
# ファイルをローカルに保存する
config.active_storage.service = :local
```

指定した保存先に関する設定は`config/strage.yml`に記述します。
以下は一例です。

```ruby
local:
  service: Disk
  root: <%= Rails.root.join("storage") %>

test:
  service: Disk
  root: <%= Rails.root.join("tmp/storage") %>

amazon:
  service: S3
  access_key_id: ""
  secret_access_key: ""
  bucket: ""
  region: "" # 例: 'ap-northeast-1'
```

`service: Disk`は保存先がこのパソコンであることを示しており、`root`はその保存先へのパスを表しています。
また、`amazon`の`service: S3`はAmazon S3（Simple Storage Service）を表しておりその下の`access_key_id`や`secret_access_key`はAmazon S3へのアクセス方法に関する情報が記載されています。

先ほどの`config/environments/development.rb`ファイルに記述した以下の記述は上記の`:local`を保存先に指定することを表しています。

```ruby
# 保存先を`local`に指定している
config.active_storage.service = :local
```


# 保存したいデータとモデルの連携

## `has_one_attached`

データを連動させたいモデルに`has_one_attached`と記述すると対象のモデルに１対１でデータを紐付けることができます。

例えば各Userデータにアバター画像を紐付けたい場合は`User`モデルに次のような記述をします。

```ruby
class User < ApplicationRecord
  has_one_attached :avatar
end
```

## `has_many_attached`

こちらはレコードとファイルに１対多の関係を設定します。

例えば`Message`モデルが存在し、その一つ一つのレコードに複数の画像を添付できるようにしたい場合は次のようにします。

```ruby
class Message < ApplicationRecord
  has_many_attached :images
end
```


# 画像の加工方法

## image_processing

ActiveStrageを使って画像を保存する場合、`image_processing`というgemを使うことで画像を加工することができます。
似たようなgemに`mini_magick`がありますが`mini_magick`はRubyを使って`ImageMagick`という画像処理ツール扱うためのgemです。
この`mini_magick`は`image_processing`をインストールすると一緒にインストールされます。

## `variant`メソッド

`image_processing`をインストールすることで`Active Strage`で保存した画像を加工することができる`variant`メソッドを使用出来るようになります。
このメソッドは引数に処理を渡すことでサイズの調整や切り取りを行うことができます。

### resize

`resize`は画像のサイズを調整するのに使用します。
基本の使用方法は次の通りです。

```ruby
Blobオブジェクト.variant( resize: "横幅(x高さ!)")
```

`resize`オプションは値に横幅を渡すことでアスペクト比を保ったまま画像の横幅を調整することができます。
また高さの調整もしたい場合は`x高さ!`を横幅に並べて記述する必要がありますが、この時`!`をつけないと高さを指定していないのと同じになってしまうため注意が必要です。

その他にも使えるオプション（水彩画風にする、トリミングするなど）はたくさんあるので気になる場合は[こちら](https://prograshi.com/framework/rails/active-storage_variant/)を確認してください。

## `processed`メソッド

`variant`メソッドの後ろに`processed`メソッドをつけると同じ加工をした画像を確認し、存在する場合はそれを取得します。

```
Blobオブジェクト.variant( 処理 ).processed
```

# 参考サイト

[Active Storage の概要 - Railsガイド](https://railsguides.jp/active_storage_overview.html)

[Active StorageのVariantの指定方法いろいろ #Ruby - Qiita](https://qiita.com/kazuomatz/items/3cdbd2c40576c2e9d89b)

[【Rails】ActiveStorageのvariantを使いこなす！便利な画像変換のメソッドやオプションを実例で解説（!, &gt;, &lt;, ^とは何か？）](https://prograshi.com/framework/rails/active-storage_variant/)

[[Rails]Active Storageを理解してない人は覗いてご覧なさい #Ruby - Qiita](https://qiita.com/ren0826jam/items/58bdbaff17581280ee5a)

[Railsの画像まわりのライブラリについて整理する(ActiveStorage, ImageMagick, ImageProcessing,,,) #Rails - Qiita](https://qiita.com/fgem28/items/54c5ca70753f16ef420c)
