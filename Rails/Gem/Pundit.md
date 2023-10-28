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

## policyファイルの作成

次のコマンドを実行して`app/policies/application_policy.rb`ファイルを作成します。

```
rails g pundit:install
```

このファイルを継承してコントローラ毎の`policy`ファイルを作成していくためベースとなる設定をこのファイルに書き込んでいきます。
以下は一例です。

```
class ApplicationPolicy
  attr_reader :user, :record # ここで読み取りの属性を定義しています。

  def initialize(user, record)
    @user = user
    @record = record
  end

  def index?
    false
  end

  〜 省略 〜

  def scope
    Pundit.policy_scope!(user, record.class)
  end

  class Scope
    attr_reader :user, :scope

    def initialize(user, scope)
      @user = user
      @scope = scope
    end

    def resolve
      scope
    end
  end
end
```

重要な部分について解説します。

```
attr_reader :user, :record

def initialize(user, record)
  @user = user
  @record = record
end
```

`attr_reader`メソッドで`user`と`record`インスタンスの読み取りができるようにしています。
この記述がないと読み取り専用のメソッドを作る必要がありますが、この記述をすることによりそれらを書かなくて良くなるため記述量を減らすことができます。

また、`initialize`メソッドは特別なメソッドでインスタンス生成時に一度だけ実行されます。

`Pundit`では第一引数の`user`には`current_user`が代入されるようになっており、第二引数の`record`には対応するモデルのオブジェクトが渡されます。

これによりログインしているユーザーの情報と対象のモデルのオブジェクトに関する情報を取得できるようになります。

## `policy`ファイルの作成

それぞれのコントローラに対応した`policy`ファイルを作成します。
以下は例になります。

```ruby
# app/policies/author_policy.rbファイル

class AuthorPolicy < TaxonomyPolicy
  def index?
    user.admin? || user.editor?
  end

　　　　〜　省略　〜

end
```

上記の例では`author`コントローラの`index`アクションにアクセスする際に`admin`もしくは`editor`でないとアクセスできなくするための記述です。
このように`アクション名?`のメソッドをファイル内に記述することで特定のコントローラの特定のアクションに対する認可の設定をすることができます。

## コントローラの編集

`policy`ファイルの設定をコントローラ側から呼び出します。

```ruby
# app/controller/author_controller.rb

def index
  authorize(Author) # ここでpolicyファイルの設定を呼び出している
　　　　〜　省略　〜
end
```

この`authorize`メソッドで先ほどの`index?`メソッドが実行されています。
また`authorize`メソッドの引数は対象のモデルのインスタンスが存在するかどうか（どのアクションか）で渡す引数を変えます。

- index、createなど特定のインスタンスが存在しない場合
```
authorize(Author) # モデルを直接渡す
```

- show、update、destroyなど特定のインスタンスが存在する場合
```
authorize(@author) # インスタンスを渡す
```


# 参考サイト

[github pundit](https://github.com/varvet/pundit)

[[Rails]gem Punditによる権限管理 (認可)](https://zenn.dev/yusuke_docha/articles/7b4b2f3f1bb203)

[Pundit + Railsで認可の仕組みをシンプルに作る #Ruby - Qiita](https://qiita.com/zaru/items/8bf7b41b33f3f55bd27d)

[【Rails】authorizeとは？[Pundit] / 403エラー画面の作成有り #pundit - Qiita](https://qiita.com/mmaumtjgj/items/c7fc40619a15cce5ccfc)

