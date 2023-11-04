# yarnとは

Facebookが作成したパッケージ管理ツールです。npmと互換性があり同じどちらも`package.json`を使用できます。


# 使い方

### package.jsonの作成

次のコマンドでpackage.jsonを作成します。

```ruby
yarn init
```

### パッケージのインストール

```ruby
yarn install
```

こちらのコマンドで`package.json`に記述されたパッケージをインストールします。
`yarn add`コマンドなどで`package.json`に追加されただけだとまだインストールされていないためこのコマンドを実行する必要があります。
`package.json`に追加しただけでの状態は`Gemfile`にgemを書いただけの状態と同じようなものです。

### パッケージの追加

次のコマンドで`package.json`にパッケージをインストールします。
このコマンドの実行後に`yarn install`コマンドを実行しなくてはいけないことに注意が必要です。

```ruby
yarn add [パッケージ名]
```

パッケージのアンインストール


```ruby
yarn remove
```

# 参考サイト

[【JavaScriptの応用】パッケージマネージャー -Yarn – ワードプレステーマTCD](https://tcd-theme.com/2021/10/javascript-yarn.html)

[npmとyarnについて解説](https://zenn.dev/hinoshin/articles/aadfdd7ba958a9)

[yarnとは - Qiita](https://qiita.com/akitaaa/items/c97ff951ca31298f3f24)

[バージョン管理ツール、パッケージ管理ツールの種類をまとめました - Qiita](https://qiita.com/akkey2475/items/5b2813e62303a9c75813)
