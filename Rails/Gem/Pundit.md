# Punditとは

Punditとはユーザーによって特定のviewファイルへのアクセス権限を与える（認可）ためのgemです。

このgemを使うことで管理者はアクセス可能、一般ユーザーはアクセス不可などの設定を簡単に行うことができます。

# 使い方

## インストール

いつも通りGemfileに`gem "pundit"`を記述し`bundle install`する。

## application_controller.rbへの記述

`application_controller.rb`内に以下の記述をすることにより`Pundit`の機能を継承先も含めたコントローラ内で使用できるようにします。

```
class ApplicationController < ActionController::Base
  include Pundit::Authorization
end
```
















# 参考サイト

[github pundit](https://github.com/varvet/pundit)

[[Rails]gem Punditによる権限管理 (認可)](https://zenn.dev/yusuke_docha/articles/7b4b2f3f1bb203)

[Pundit + Railsで認可の仕組みをシンプルに作る #Ruby - Qiita](https://qiita.com/zaru/items/8bf7b41b33f3f55bd27d)

[【Rails】authorizeとは？[Pundit] / 403エラー画面の作成有り #pundit - Qiita](https://qiita.com/mmaumtjgj/items/c7fc40619a15cce5ccfc)


