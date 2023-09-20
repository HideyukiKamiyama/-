# ****dartsass-railsとは****


Railsアプリケーションでsass(scss)を使用したい場合にプリコンパイルする際に使用するgemです。

[【Rails】Ruby on RailsとSass - Qiita](https://qiita.com/redrabbit1104/items/e0b2a89626a03aea671e)



# インストール方法


以下のコマンドでgemをインストールします。

```ruby
./bin/bundle add dartsass-rails
```

gemをインストールしたらdartsass-railsをRailsアプリケーションに統合し、config/initializers/dartsass.rbファイルを作成するために次のコマンドを実行します。

```ruby
./bin/rails dartsass:install
```



# config/initializers/dartsass.rbファイルの記述


config/initializers/dartsass.rbファイルに次のようにプリコンパイルしたいファイルを記述します。

このファイルには最初何も記述されていませんが、何も記述されていない状態で stylesheet/application.scss をプリコンパイルするように設定されています。

```ruby
Rails.application.config.dartsass.builds = {
  "application.scss" => "application.css",
  "admin/application.scss" => "admin/application.css"
  "admin/login.scss" => "admin/login.css"
}
```

こちらの設定をして再度サーバーを再起動すると app/assets/buildsファイルにプリコンパイルしたファイルが作成されます。

ここにファイルがプリコンパイルされることでアセットパイプラインにscssが読み込まれ、cssが適用されます。

[ざっくりわかる Sprocketsとは - Qiita](https://qiita.com/nichidai3_0514/items/99596113ffe33a29dde8)



# rails assets:precompileコマンド


プリコンパイルが上手く実行されず app/assets/buildsファイルにプリコンパイルされたファイルが作成されない場合次のコマンドを実行します。

```ruby
rails assets:precompile
```

このコマンドを実行することで config/initializers/dartsass.rbファイルに設定したファイルがプリコンパイルされます。

[rails assets:precompile | Railsドキュメント](https://railsdoc.com/page/rails_assets_precompile)

[【Tips】ローカルで `rails assets:precompile` を実行してしまい以後アセットの更新が反映されなくなってしまった場合の対応 - Qiita](https://qiita.com/GandT/items/4b3701a6ca51ff84370c)



# 参考サイト


[github dartsass-rails](https://github.com/rails/dartsass-rails)

[アセットパイプライン - Railsガイド](https://railsguides.jp/asset_pipeline.html#別のライブラリを使う)

[Rails7にdartsass-railsを導入する](https://engineer-daily.com/rails7-dartsass-rails/)

[dartsass-rails README（翻訳）｜TechRacho by BPS株式会社](https://techracho.bpsinc.jp/hachi8833/2022_11_14/122975)

[Rails 7 に Sass （DartSass）を導入する](https://zenn.dev/yama525/articles/0c3b9e3b75d9cc)
