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

# name属性をerbで表記する場合

---

```
<%= f.text_field :name %>
```

送信先をmodelで指定している場合は、上記のコードは次のようにコンパイルされます。

```
<input type="text" name="user[name]" id="user_name" />
```

つまりuserモデルのnameカラムに関する情報であることが分かるように自動で変換されるため、erbで:user[name]と記述するとエラーになってしまいます。

# remote: true

---

form_withの特徴としてデフォルトでremote:truegが付与されていることが挙げられます。remote:tureを使用することでAjaxを使用することができるようになり、非同期通信によりWebページの一部を動的に変更することができるようになります。

[form_with | Railsドキュメント](https://railsdoc.com/page/form_with)

[remote: trueでフォーム送信をAjax実装する方法とは？](https://pikawaka.com/rails/remote-true)

# label要素のfor属性

---

label要素のfor属性には関連付けができる要素のid属性を記述します。これを記述することにより関連づけたい要素とlabel属性を結び付けることができ、関連付けたい要素（form欄など）が何を示しているのかを説明できます。以下に例を示します。

```html
<label for="name">名前を入力してください: </label>
<input id="name" type="text" size="30" />
```

上記の例ではlabel要素のfor属性nameとinput要素のid属性nameが結びついており、input属性によって作成される入力フォームに「名前を入力してください」という説明書きを表示させることができます。
