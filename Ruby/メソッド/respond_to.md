# respond_toメソッドとは

respond_toメソッドはリクエストで指定されたフォーマットによって処理を分けるメソッドです。

```jsx
#基本的な書き方

respond_to do |format|
  format.形式 { 処理 }
end
```

例えば下の例のようにformatの後にhtmlと記述すればリクエストされるフォーマットがhtml形式の場合の処理が実行される。

```jsx
class UsersController < ApplicationController
  def index
    respond_to do |format|
      format.html # リクエストされるフォーマットがHTML形式の場合
      format.json { 処理 } # リクエストされるフォーマットがJSON形式の場合
      format.js { 処理 } # リクエストされるフォーマットがJS形式の場合
    end
  end
end
```



# リクエストのフォーマットとは

URLの最後に.jsonのように記述することでフォーマットを指定することができます。何も記述しない場合はデフォルトでhtmlが指定されます。



# 参考サイト

[pikawaka respond_to](https://pikawaka.com/rails/respond_to)
