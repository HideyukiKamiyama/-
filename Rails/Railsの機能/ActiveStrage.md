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
  
### active_storage_attachments
  
モデルをblobsに接続するポリモーフィックjoinテーブルです。
  
### active_storage_variant_records
  
バリアントトラッキングが有効である場合に生成された各バリアントに関するレコードを保存します。



# 参考サイト

[Active Storage の概要 - Railsガイド](https://railsguides.jp/active_storage_overview.html)

[Active StorageのVariantの指定方法いろいろ #Ruby - Qiita](https://qiita.com/kazuomatz/items/3cdbd2c40576c2e9d89b)

[【Rails】ActiveStorageのvariantを使いこなす！便利な画像変換のメソッドやオプションを実例で解説（!, &gt;, &lt;, ^とは何か？）](https://prograshi.com/framework/rails/active-storage_variant/)

[[Rails]Active Storageを理解してない人は覗いてご覧なさい #Ruby - Qiita](https://qiita.com/ren0826jam/items/58bdbaff17581280ee5a)

[Railsの画像まわりのライブラリについて整理する(ActiveStorage, ImageMagick, ImageProcessing,,,) #Rails - Qiita](https://qiita.com/fgem28/items/54c5ca70753f16ef420c)
