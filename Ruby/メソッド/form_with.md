# form_withとは




送信フォームを作成する際に使用するヘルパーメソッドです。似たようなヘルパーメソッドにform_forやform_tagがありましたが、現在はform_withに統合されform_withが推奨されています。

基本構文は次の通りです。

```html
<%= form_with(モデル or URLベース,[スコープ],[オプション]) do |f| %>
  フォームの中身
<% end %>
```

送信先の指定にはmodelオプションとurlオプションを使用します。




### **modelオプション**

- 渡されたオブジェクト（インスタンス）が新規で作成されたもの（中身がある）か、既存のものなのかを判定して、新規のオブジェクトならばcreateアクションへ、既存ならばupdateアクションへのパスを生成します。
- そのモデルと同名のコントローラーのアクションへと遷移するように自動でパスを生成します。
- モデルが親子関係にある場合は親と子の二つのオブジェクトを渡す必要があり、一つの場合と同様にオブジェクトの中身があるかないかでcreateアクションに行くか、updateアクションに行くかが決定されます。




### **urlオプション**

- 渡されたurlに対応するコントローラーのアクションへと遷移するようにパスを生成します。

### **modelオプションとurlオプションの併用**
- modelオプションで渡したインスタンスに対してフォームの変更を実行したいが、送信先はそのインスタンスに紐付かないところにしたい場合、modelオプションとurlオプションは併用することができる。例えばUserデータの一つである自分のデータを編集したいが編集自体はprofileコントローラで行いたい場合、modelオプションにはcurrent_userが入った@userを渡し、urlにprofileコントローラのcreateアクションなどへのパスを渡すことができる。

[超実践型オンラインエンジニア育成スクール | RUNTEQ(ランテック)](https://school.runteq.jp/v3/curriculums/rails_basic/chapters/3/hint)

[Rails：form_withのオプションや特徴について整理 - Qiita](https://qiita.com/yamaday0u/items/a3689bc48a7eff55929b)

[【Rails】form_with/form_forについて【入門】 - Qiita](https://qiita.com/snskOgata/items/44d32a06045e6a52d11c)



# フォーム要素を生成するヘルパー

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

form_withの特徴としてデフォルトでremote:trueが付与されていることが挙げられます。remote:tureを使用することでAjaxを使用することができるようになり、非同期通信によりWebページの一部を動的に変更することができるようになります。

[form_with | Railsドキュメント](https://railsdoc.com/page/form_with)

[remote: trueでフォーム送信をAjax実装する方法とは？](https://pikawaka.com/rails/remote-true)




# local: true

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


1. labelの表示

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


label要素のfor属性には関連付けができる要素のid属性を記述します。これを記述することにより関連づけたい要素とlabel属性を結び付けることができ、関連付けたい要素（form欄など）が何を示しているのかを説明できます。以下に例を示します。

```html
<label for="name">名前を入力してください: </label>
<input id="name" type="text" size="30" />
```

上記の例ではlabel要素のfor属性nameとinput要素のid属性nameが結びついており、input属性によって作成される入力フォームに「名前を入力してください」という説明書きを表示させることができます。

[<input>: 入力欄（フォーム入力）要素 - HTML: ハイパーテキストマークアップ言語 | MDN](https://developer.mozilla.org/ja/docs/Web/HTML/Element/input#追加機能)

[“for”属性｜“label”要素:フォームの内容のラベル｜HTML－havin' a coffee break｜珈琲とウェブデザイン](https://web.havincoffee.com/html/tag/label/for.html)




# 子要素の情報を送信する場合の記述方法

掲示板（board）とコメント（comment）がアソシエーションの関係にある場合、コメントをform_withで送信する際に送信先としてboardとcommentを両方指定する必要がある。

```html
<div>
  <%= form_with model: [@board, @comment], local: true do |f| %>
    <%= f.label :comment_body %>
    <%= f.textarea, :comment_body%>
    <%= f.subimt t('.post'), class: 'btn btn-primary' %>
  <% end %>
</div>
```

上記のようにmodelに親要素と子要素のインスタンス変数を両方指定することでコンパイルされた際のaction属性が`boards/board_id/comments`となる。

[ネストしているモデルに対するform_withの書き方 - Qiita](https://qiita.com/j-sunaga/items/29c5ff295798bc7884c7)

[【Rails】form_withについて（ネスト） -  bokuの学習記録](https://boku-boc.hatenablog.com/entry/2020/11/05/215744)




# submitのname

submitタグのname属性のcommitパラメータはデフォルトで付与されるようになっています。以下ソース。

[](https://github.com/rails/rails/blob/3-2-stable/actionpack/lib/action_view/helpers/form_tag_helper.rb#L428)

また、他の名前をつけたい場合は自分で記述することもできます。

[submit（送信ボタン）](http://w-d-l.net/html__tags__body__form__input__submit/)

[フォームの送信ボタンに名前（name属性）を付ける | フロントエンドBlog | ミツエーリンクス](https://www.mitsue.co.jp/knowledge/blog/frontend/201412/04_1400.html)




# その他のオプション

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

[Action View フォームヘルパー - Railsガイド](https://railsguides.jp/form_helpers.html)

[Ruby on Rails 5.1 リリースノート - Railsガイド](https://railsguides.jp/5_1_release_notes.html#form-forとform-tagのform-withへの統合)

[form_with | Railsドキュメント](https://railsdoc.com/page/form_with)

[form_withの使い方を徹底解説！](https://pikawaka.com/rails/form_with)

[form_withでのmodelとurlの指定の違いは？ - Qiita](https://qiita.com/kajikaji/items/5b5687fbb8ad287560fc)
