# Simple_formとは

Railsで簡単にフォームを作成することができるgemです。

フォームの作成は`form_with`を使うことでも可能ですが、このgemを使うことでより簡単にフォームを作ることができます。


# 導入方法

Gemfileに次の記述をして`bundle install`。

```ruby
gem 'simple_form'
```

続いて`generate`コマンドを実行します。

```
rails generate simple_form:install

# bootstrapを適用させたい場合は次のオプションをつけます。
rails g simple_form:install --bootstrap
```

# フォームの作成

## フォーム部分の作成

以下にフォームの例を示します。

```erb
<%= simple_form_for @user do |f| %>
  <%= f.input :username %>
  <%= f.input :password %>
  <%= f.button :submit %>
<% end %>
```
```slim
= simple_form_for @user do |f|
  = f.input :username
  = f.input :password
  = f.button :submit
```

このように`f.input`とするだけでフォームの入力欄を作成することができます。
この記述だけで`label`も用意してくれるため別で`label`タグを作成する必要はありません。

## オプション

### labelの変更

```
label: "ラベルの名前"
```

labelの値を上書きしたい場合に使用します。また`f.input`はデフォルトでカラム名がlabelとして扱われるため、labelを無効にしたい場合は`label: false`と記述します。

### ヒントをつける

```
hint: "ヒントや注意書き
```

このオプションを使用すると入力欄の下にヒントを表示することができます。

### エラーメッセージ

```
error: "表示したいエラーメッセージ"
```

入力に失敗しエラーが発生した場合のメッセージを入力欄の近くに表示できます。

### プレースホルダー

```
placeholder: "プレースホルダーとして表示したい文字"
```

### カレンダーで日付を指定できるようにする

```
:published_at, as: :date_time_picker
```

### HTML属性をそのまま渡す

`input_html`オプションを使うとhtml属性をそのまま渡すことができます。
例えば`multiple: true`を渡したい場合は次のように記述します。

```
input_html: { multiple: true }
```

### submitボタンのボタン名変更

```
f.button :submit, "ボタン名"
```


## 入力欄の種類

`as`オプションを使用することでhtmlのtype属性を指定することができます。

### チェックボックス

```
= f.input :name, as: :boolean
```

### ファイル

```
= f.input :image, as: :file
```

### ラジオボタン

```
= f.input_field :カラム名, as: :radio_buttons
```

ラジオボタンの場合は`input`ではなく`input_field`としないと表示されません。
また、ラジオボタンの選択肢を`enum`で作成している場合、`enum_help`を使って翻訳することで`simple_form`が自動で読み取り翻訳されたものが表示されます。


# 参考サイト

[github simple_form](https://github.com/heartcombo/simple_form)

[github simple_form input_type](https://github.com/heartcombo/simple_form#available-input-types-and-defaults-for-each-column-type)

[simple_form - tanalog](https://tanakanoblogdesu.hatenablog.com/entry/2021/07/14/182432)

[【Rails】simple_form使い方基礎 #Rails - Qiita](https://qiita.com/A__Matsuda/items/dbf4da62ab9951b67aa9)

[[Rails] Simple_form gem](https://zenn.dev/yusuke_docha/articles/1fa77e0cfd54d9#%E5%B0%8E%E5%85%A5)
