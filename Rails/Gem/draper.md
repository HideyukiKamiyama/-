# draperとは


ソフトウェアデザインパターンの一つです。RailsにおけるデザインパターンとはMVCモデルに置いて頻出するパターンを抽象化してまとめたものです。

このデザインパターンを使用することで効率良く設計をすることができます。

railsの設計においてよくある問題がViewに表示したいメソッドを追加したいけど、モデルに書くと肥大化してしまうという問題です。

これを解消するためにdecoratorを実装し、model → decorator → View とすることで上記の問題を解決しています。




# インストール方法


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

また上記のコードのfull_nameメソッドの内部のコードは下記のようにした方が短く読みやすいため好ましい。

```ruby
def full_name
    # BAD
    object.last_name + ' ' + object.first_name

    # GOOD
    "#{object.last_name} #{object.first_name}"
  end
```




# ビューファイルでの表示


表示したいビューファイル内で以下のように記述します。

```html
<%= current_user.decorate.full_name %>
```

current_userはrailsにおいてログインユーザーを表すヘルパーメソッド。

[Rails params[:id]とcurrent_user](https://zenn.dev/goldsaya/articles/14774cec2dea0b)




# 参考サイト


[github draper](https://github.com/drapergem/draper)

[[Rails] Draperを使ってDecoratorの導入する](https://osamudaira.com/150/)

[decoratorを導入して、viewの記述をすっきりさせ、modelの肥大化を回避する【Day 3/30 2nd】｜Kentarotawara](https://note.com/kentarotawara/n/n0c1ff2060cf7)

[Decoratorの役割とDraperについて - Qiita](https://qiita.com/jonson29/items/00077b54bb91ed74fdb8)

[gem draperを使用して、decoratorを導入する方法](https://zenn.dev/atsushi101011/articles/a72558af23e685)




# ****Active_Decorator****


また、似たようなデザインパターンにActive_Decoratorというものがあります。下記は公式リンクです。

[github active decorator](https://github.com/amatsuda/active_decorator)
