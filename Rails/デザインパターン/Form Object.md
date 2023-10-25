# Form Objectとは

`Form Object`とはフォームにActive Record以外のオブジェクトを渡したい場合に使用されるデザインパターンです。

RailsはMVCモデルを基本としたCRUD操作で成り立っているため、フォームはモデルに関連付いています。

そのため複数モデルに関連したフォームを作成したい場合にこの`Form Object`が必要になってきます。

また、複数のモデルやコントローラにフォームに関するロジックを記述する必要がある場合、それらを`Form Object`にまとめておくことでモデル、コントローラの肥大化や、可読性の低下を防ぐことができます。


# Form Objectのメリット

`Form Object`を使用するメリットは次の二つです。

- DBに保存しないフォームでもActiveRecordのバリデーションを使用可能
- フォームに関する記述を一箇所にまとめることができるため管理が容易


# Form Objectの使用方法

[こちら](https://qiita.com/ren0826jam/items/0effb716067a861e71f2)の記事が分かりやすかったのでこちらの記事のコードを参考にまとめていきます。


## Form Objectの作成

`app/forms/`配下に`Form Object`のファイルを作成します。`app/forms/`配下に作らなくてはいけないという決まりはないそうですが、こちらに記述することが推奨されているそうです。

今回は`sample_form.rb`というファイルを作成します。

作成した`sample_form.rb`にフォームに関する設定を記述していきます。

```ruby
class SampleForm
  include ActiveModel::Model # 通常のモデルのようにvalidationなどを使えるようにする
  include ActiveModel::Attributes # ActiveRecordのカラムのような属性を加えられるようにする

  attribute :first_name, :string
  attribute :last_name, :string
  attribute :email, :string
  attribute :body, :string

  validates :first_name, presence: true
  validates :last_name, presence: true
  validates :email, presence: true
  validates :body, presence: true

  def save
    処理
  end
end
```


## コントローラの編集

以下コントローラのサンプルです。

```ruby
class SamplesController < ApplicationController
...

  def new
    @sample = SampleForm.new
  end

  def create
    @sample = SampleForm.new(sample_params)
    if @sample.save
      redirect_to :samples, notice: 'サンプル情報を作成しました。'
    else
      render :new
    end
  end

  private

  def sample_params
    params.require(:sample).permit(:first_name, :last_name, :email, :body)
  end
end
```

newアクションとcreateアクションに注目してください。
これらのアクションでは`SampleForm.new`という形で`@sample`インスタンスを作成し、viewファイルへと渡しています。
これにより`sample_form.rb`の設定を反映させた`@sample`インスタンスを作成することができます。


## viewファイルの作成

最後にviewファイルのフォームの記述例です。以下のファイルは`_form.html.erb`を想定しています。

```html
<%= form_with model: sample, local: true do |form| %>
  <%= form.text_field :first_name %>
  <%= form.text_field :last_name %>
  <%= form.email_field :email %>
  <%= form.text_area :body %>
  <%= form.submit %>
<% end %>
```

こちらのパーシャルにはコントローラから渡された`@sample`を`sample`というローカル変数として渡しています。


# 参考サイト

[【備忘録】Formオブジェクトについて](https://zenn.dev/adverdest/articles/form_object_article)

[[Rails]大変便利なFormオブジェクトはご存知でしょうか？ #Ruby - Qiita](https://qiita.com/ren0826jam/items/0effb716067a861e71f2)

[Formオブジェクトパターン #Rails - Qiita](https://qiita.com/masakichi_eng/items/3ce819ac69c3f7b3692c)

[form objectを使ってみよう - メドピア開発者ブログ](https://tech.medpeer.co.jp/entry/2017/05/09/070758)

[Railsのデザインパターン: Formオブジェクト - applis](https://applis.io/posts/rails-design-pattern-form-objects)

[【Rails】Form Objectを使ってModelに依存しないFormを作成する ｜ もふもふ技術部](https://tech.mof-mof.co.jp/blog/rails-form-object/)

[【Rails】ロジックを1つに集約できるデザインパターン「Form Object」を簡易実装してみた｜TechTechMedia](https://techtechmedia.com/form-object-implementation/)

[【Rails】フォームオブジェクトの実装方法【超基本】｜Rubinistを目指す新米エンジニアのTECH BLOG](https://sakaishun.com/2021/04/20/form-object/)
