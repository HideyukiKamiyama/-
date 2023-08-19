# Strong Parameters

---

# Strong Parametersとは

---

Strong Parametersとはデータベースに登録できる内容に制限をかけて不正なデータの登録を防ぐための手段のこと。ホワイトリスト形式で受け取り可能なデータを登録しておくことで予期せぬ値を受け取らないようにしている。

コードは以下のように記述する。

```ruby
private

def person_params
  params.require(:person).permit(:name, :age)
end
```

上記のコードのように`params.require(:クラス名).permit(:カラム名１, :カラム名２)`と記述することでpermit渡したカラムの値のみを保存できるようになる。また、Strong Parametersはprivateに記述することが好ましい。

# マスアサインメント脆弱性

---

マスアサインメントとはユーザーからのリクエストをハッシュ形にして送信し、複数のデータを一度に登録することができるRailsの機能のこと。この機能を悪用し、本来であれば登録不可能なデータを登録されてしまうことがある。

# strong parametersの値の利用方法

---

以下のようにstrong parametersのアクション名を引数として渡すことで利用できます。

```ruby
def create
  @post = Post.new(post_params) # 定義したメソッドを使用
  if @post.save
    redirect_to @post
  else
    render 'new'
  end
end
```

上記の例ではpost_params内でstrong parametersを使用しています。

# 参考サイト
