# form_with

---

# form_withとは

---

送信フォームを作成する際に使用するヘルパーメソッドです。似たようなヘルパーメソッドにform_forやform_tagがありましたが、現在はform_withに統合されform_withが推奨されています。

基本構文は次の通りです。

```html
<%= form_with(モデル or URLベース,[スコープ],[オプション]) do |f| %>
  フォームの中身
<% end %>
```

送信先の指定にはmodelオプションとurlオプションを使用します。

### **modelオプション**

---

- 渡されたオブジェクトが新規で作成されたものか、既存のものなのかを判定して、
    
    新規のオブジェクトならばcreateアクションへ、既存ならばupdateアクションへのパスを生成します。
    
- そのモデルと同名のコントローラーのアクションへと遷移するように自動でパスを生成します。

### **urlオプション**

---

- 渡されたurlに対応するコントローラーのアクションへと遷移するようにパスを生成します。

[超実践型オンラインエンジニア育成スクール | RUNTEQ(ランテック)](https://school.runteq.jp/v3/curriculums/rails_basic/chapters/3/hint)

[Rails：form_withのオプションや特徴について整理 - Qiita](https://qiita.com/yamaday0u/items/a3689bc48a7eff55929b)

# フォーム要素を生成するヘルパー

---

form_withでは内部に要素を生成するヘルパーを使用することができます。ここには一部をまとめるので詳細はリンク先を参照。

```html
f.text_field  # 一行のテキストフォームを作成
f.email_field # email用のフォームを作成
f.password_field # パスワード用のフォームを作成
```

[Action View フォームヘルパー - Railsガイド](https://railsguides.jp/form_helpers.html#フォーム要素を生成するヘルパー)

1. placeholderオプション

text_fieldなどにオプションでplaceholderを設定できます。以下例です。

```ruby
<%= f.text_field :title, placeholder: "タイトル" %>
```

[text_field_tag | Railsドキュメント](https://railsdoc.com/page/text_field_tag)

# remote: true

---

form_withの特徴としてデフォルトでremote:trueが付与されていることが挙げられます。remote:tureを使用することでAjaxを使用することができるようになり、非同期通信によりWebページの一部を動的に変更することができるようになります。

[form_with | Railsドキュメント](https://railsdoc.com/page/form_with)

[remote: trueでフォーム送信をAjax実装する方法とは？](https://pikawaka.com/rails/remote-true)

# local: true

---

`form_with`はデフォルトで`remote: true`が付与されているため非同期通信になっている。これを同期通信にしたい場合に使用するのが`local: true`です。以下の例のように使用します。

```html
<%= form_with model: board, local: true do |f| %>
   <div class="form-group">
     <%= f.label :title %>
     <%= f.text_field :title, class: 'form-control' %>
   </div>
   <div class="form-group">
     <%= f.label :body %>
     <%= f.text_area :body, class: 'form-control', rows: 10 %>
   </div>
 
   <%= f.submit class: 'btn btn-primary' %>
 <% end %>
```

上記のように`local: true`をつけることで同期通信に変えることができます。

[備忘録16:Rails-form_with local: true｜さしみ](https://note.com/sashimi299/n/n6b9c46c63573)

# name属性、id属性をerbで表記する場合

---

- 送信先がモデルではない場合
    
    
    ```html
    <%= f.text_field :email %>
    ```
    
    送信先がモデルではない場合、上記のerbは次のようにコンパイルされます。
    
    ```html
    <input type="text" name="email" id="email" />
    ```
    
    つまり、`f.text_field`の右側に記述した`:〇〇`がそのままname属性とid属性になります。
    
- 送信先がモデルの場合
    
    
    ```html
    <%= f.text_field :email %>
    ```
    
    送信先をmodelで指定している場合(今回の例はuserモデル)は、上記のコードは次のようにコンパイルされます。
    
    ```html
    <input type="text" name="user[email]" id="user_email" />
    ```
    
    つまり送信先をuserモデルにしておくことで`<%= f.text_field :email %>`と記述することで`name="user[email]"`と`id="user_email"`を自動で作成してくれます。そのため、erbで:user[email]と記述するとエラーになってしまいます。
    
- name属性の付け方
    
    以下の例のようにname属性の付与は自分で設定するよりもform_withによる自動付与に任せた方が好ましいとされています。
    
    ```html
    # BAD f.labelのname属性を、name:で個別に指定している
    
    <%= form_with model: board do |f| %>
      <div class="form-group">
        <%= f.label :title, name: 'title' %>
        <%= f.text_field :title, class: 'form-control' %>
      </div>
    
    # GOOD f.labelのname属性を、form_withの自動付与に任せている
    
    <%= form_with model: board do |f| %>
      <div class="form-group">
        <%= f.label :title %>
        <%= f.text_field :title, class: 'form-control' %>
      </div>
    ```
    

# label要素

---

1. labelの表示

---

- 送信先がモデルではない場合
    
    erbで入力フォームにラベルを表示する場合は以下のように記述します。
    
    ```html
    <%= f.label :email, class: "form-label" %>
    ```
    
    上記のコードは次のようにコンパイルされます。
    
    ```html
    <label class="form-label" for="email">Email</label>
    ```
    
    つまり`f.label`の右側に`:〇〇`と記述するとfor属性に〇〇が設定され、〇〇の頭文字を大文字にしたものがブラウザ上に表示されます。
    
- 送信先がモデルの場合
    
    また、送信先にモデルを指定している場合の例は次のようになります。
    
    ```html
    <%= f.label :email, class: "form-label" %>
    ```
    
    上記のようにerbを記述した場合、次のようにコンパイルされます。
    
    ```html
    <label class="form-label" for="user_email">Email</label>
    ```
    
    つまり`f.label`の右側に`:〇〇`と記述した場合にそのモデル名がfor属性に追加され`for=”モデル名_〇〇”`となります。
    

1. label要素のfor属性

---

label要素のfor属性には関連付けができる要素のid属性を記述します。これを記述することにより関連づけたい要素とlabel属性を結び付けることができ、関連付けたい要素（form欄など）が何を示しているのかを説明できます。以下に例を示します。

```html
<label for="name">名前を入力してください: </label>
<input id="name" type="text" size="30" />
```

上記の例ではlabel要素のfor属性nameとinput要素のid属性nameが結びついており、input属性によって作成される入力フォームに「名前を入力してください」という説明書きを表示させることができます。

[<input>: 入力欄（フォーム入力）要素 - HTML: ハイパーテキストマークアップ言語 | MDN](https://developer.mozilla.org/ja/docs/Web/HTML/Element/input#追加機能)

[“for”属性｜“label”要素:フォームの内容のラベル｜HTML－havin' a coffee break｜珈琲とウェブデザイン](https://web.havincoffee.com/html/tag/label/for.html)

# submitのname

---

submitタグのname属性のcommitパラメータはデフォルトで付与されるようになっています。以下ソース。

[](https://github.com/rails/rails/blob/3-2-stable/actionpack/lib/action_view/helpers/form_tag_helper.rb#L428)

また、他の名前をつけたい場合は自分で記述することもできます。

[submit（送信ボタン）](http://w-d-l.net/html__tags__body__form__input__submit/)

[フォームの送信ボタンに名前（name属性）を付ける | フロントエンドBlog | ミツエーリンクス](https://www.mitsue.co.jp/knowledge/blog/frontend/201412/04_1400.html)

# その他のオプション

---

1. data-disable-with

data-disable-withを使うことでフォームを送信する際に入力フィールドを自動で無効にすることもできます。これによりユーザーの誤操作による2回クリックを無効にしHTTPリクエストの重複によるバックエンド側のエラーを防ぐことができます。

[Rails で JavaScript を使用する - Railsガイド](https://railsguides.jp/v6.0/working_with_javascript_in_rails.html#入力を自動で無効にする)

1. rows

フォームのテキストエリアの幅をrowsオプションで指定することができます。CSSは個別にHTMLで指定するより一つのファイルで管理することが好ましいので、入力幅を指定したい場合はstyle属性で指定するのではなくrowsオプションで指定するのが良いとされています。

```html
# BAD styleで高さを指定して、入力幅を設定している
<%= f.text_area :body, class: 'form-control', style: 'height: 200px', row: 10 %>

# GOOD styleを使用せずに、rows:オプションで入力幅を設定している
<%= f.text_area :body, class: 'form-control', rows: 10 %>
```

# その他参考サイト
