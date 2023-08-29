# Own?(object)

---

# own?(object)について

---

```ruby
  def own?(object)
    object.user_id == self.id
  end

# selfは省略可能
```

userモデルなどに定義して使用することができるインスタンスメソッド。

`objecit.user_id`の部分は引数として渡されたオブジェクトに紐づいているusr_idを取得している。

`self.id`はこのインスタンスメソッドのレシーバのidを取得している。

つまりこのインスタンスメソッドはレシーバのidと引数として渡したobjectのuser_idを比較し一致していればture、一致していなければfalseを返す。

主な使用方法としては`current_user`をレシーバにする事で引数のオブジェクトが`current_user`のものであるかを判定するのに使われる。

# 具体的な使用例

---

```ruby
<%= render 'crud_menus', board: board if current_user.own?(board) %>
```

上記の例では`board`が`current_user`のものであった場合に`crud_menus`を表示するプログラムとなっている。

# 参考サイト
