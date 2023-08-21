# renderとredirect_to

---

# renderとredirect_toの違い

---

renderとredirect_toはどちらもページを移動する際に使用するメソッドですが異なる挙動を行います。

- rendr → controller → View
- redirect_to → URL → route → controller → View

つまりrenderは直接Viewファイルを呼び出しているのに対し、redirect_toはHTTPリクエストを実行しています。

# renderとredirect_toの使い分け

---

1. データの更新や削除などデータを送る時
2. ユーザーのログインなどの成否処理

上記のような場合にはredirect_toを使用します。つまりコントローラを使用して何かの処理を行いたい場合にredirect_toを使用します。

逆にrenderはただページを移動したい場合に使用されます。

# 参考サイト
