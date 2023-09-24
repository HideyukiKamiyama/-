# enum_helpとは


enum_helpとはenumによって表示された文字をi18nに対応させるためのgemです。

# インストール


```ruby
gem 'enum_help'
```

上記のコードをGemfileに記載し、 `bundle install` する。

# ロケールファイルの編集


enumをi18nに対応させるためにロケールファイルを編集します。

今回は管理権限の有無を判定するためにenumを使用している場合を想定します。

userモデル内でenumを次のように定義します。

```ruby
enum role: { general: 0, admin: 1 }
```

次にロケールファイルに次の記述を追加します。

```ruby
enums:
    user:
      role:
        general: 一般
        admin: 管理者
```

こうすることでenumがi18nに対応するようになります。

# 利用方法


ここまでの準備を行うことで次のようなi18nのメソッドが使えるようになります。

```ruby
@user.roles # => admin

@user.roles_i18n # => 管理者
```

以上で実装は完了です。

# 参考サイト


[github enum_help](https://github.com/zmbacker/enum_help)

[enumとenum_helpの使い方【rails】 - Qiita](https://qiita.com/kikikikimorimori/items/353f69e31b42e85b9c29#enum_helpって何)

[[Rails] enum_helpよる多言語対応とransackを使ってプルダウン検索機能を実装](https://osamudaira.com/274/)
