# draper

---

# draperとは

---

ソフトウェアデザインパターンの一つです。RailsにおけるデザインパターンとはMVCモデルに置いて頻出するパターンを抽象化してまとめたものです。

このデザインパターンを使用することで効率良く設計をすることができます。

railsの設計においてよくある問題がViewに表示したいメソッドを追加したいけど、モデルに書くと肥大化してしまうという問題です。

これを解消するためにdecoratorを実装し、model → decorator → View とすることで上記の問題を解決しています。

# インストール方法

---

Gemfileに以下の記述をし、`bundle install`をする。

```html
gem 'draper'
```

その後

```ruby
rails generate draper:install
```

を実行することでデコレーター層を実装することができる。これを実行することで`app/decorators/application_decorator.rb`を作成することができ、ApplicationDecoratorは全てのDecoratorの親クラスになる。

# デコレータの作成

---

上記の準備が終わると下記のコマンドが使えるようになります。

```ruby
rails g decorator 〇〇
```

Userモデルに関するデコレータを作成する場合、

```ruby
rails g decorator User
```

と実行することで`app/decorators/user_decorator.rb`が作成されます。

# デコレータにメソッドを追加

---

Userモデルでフルネームを呼び出すメソッドを作っていきます。

last_name と first_name は事前にUserモデルのカラムに追加してあります。

```ruby
class UserDecorator < Draper::Decorator
	delegate_all  # UserモデルのメソッドをUserDecoratorクラスでも使用できるようにするための記述
	
	def full_name
		"#{object.last_name} #{object.first_name}"
	end
end
```

delegate_all を記述することによってUserモデル内のメソッドをUserDecoratorクラスでも使用することができるようになります。これにより UserDecorator 内で`last_name`と`first_name`メソッドが使えるようになっています。

objectメソッドはデコレートしているモデルを参照しているメソッド？（ここは情報があいまい）

# ビューファイルでの表示

---

表示したいビューファイル内で以下のように記述します。

```html
<%= current_user.decorate.full_name %>
```

https://github.com/drapergem/draper
