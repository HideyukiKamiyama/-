# RSpec


# テストコードの記述


## テストコードのグループ化


- describe

テスト対象を記述する。

複数記述することやdexcribeはネストさせることができ、テストケースを細かく書き分けることができる。

例えばuserクラスのfindメソッドをテストする場合は次のように記述する。

```ruby
describe 'User' do
  describe '#find' do

  end
end
```

- context

状況や条件を記述する。

dexcribeブロック内に記述するのが一般的です。

- example

アウトプットの内容を記述。

- it

テストケースの説明。

一つのitブロックは一つのテストケースを表します。

[【Ruby on Rails】RSpecのdescribe/context/itの意味を理解した件 - Qiita](https://qiita.com/hawkcookie/items/d7965be4adbeb2490229)

[rspecを読みやすくメンテしやすく書くために](https://zenn.dev/yuji_developer/articles/52cc0e356b3748)


# マッチャー

マッチャーとは期待値と実際の値を比較して一致したか、一致しなかったかを返すオブジェクトです。
RSpecではこのマッチャを使用してアプリが想定通りに動いているかを判定します。


## within

withinマッチャーは検査する対象をidやclassを使用して指定することが出来ます。

例えば２つの`削除`ボタンが存在しているページで

```ruby
click_on '削除'
```

とした場合、

```
Ambiguous match, found 2 elements matching visible link or button "削除"
```

というエラーが発生してしまいます。
これは条件に当てはまる要素が二つ存在しているためにこのようなエラーが発生しています。

このような時に便利なのが`within`です。
このマッチャーを使用することでクリックの対象をidやclassで指定することができるため上記のエラーを解消することが出来ます。

使用方法は以下の通りです。

```ruby
before do
  within '#user_1' do
    click_on '削除'
  end
end
```

上記のコードには`within '#user_1'`と記述されている事からidがuser_1である削除ボタンを指定してクリックするテストを実行しています。


# 処理の共通化（ログイン）


### module


supportディレクトリ配下にフォルダを作成することでmoduleを作ることができます。

以下がログイン処理をまとめたmoduleです。

spec/support/login_macros.rb

```ruby
module LoginMacros
  def login(user)
    visit login_path
    fill_in 'メールアドレス', with: user.email
    fill_in 'パスワード', with: user.password
    click_on 'ログイン'
  end
end
```

supportディレクトリはデフォルトでは読み込まれないので下記「spec/support配下のファイルの読み込み設定」に記述されているように読み込み設定をする必要があります。

ここで定義したmoduleは利用するファイルで読み込み設定をする必要があります。その方法を二つ説明します。

1. テストファイル内で直接読み込む方法

```ruby
RSpec.describe 'hogehoge', type: :system do
  include LoginMacros

  it 'ログインした後に...' do
    user = User.create(email: 'runteq@example.com', password: 'password')
    login(user)
    # ログイン後のテストを書く
  end
end
```

上記のように `include LoginMacros` と記述することでmoduleをファイル内で読み込むことができます。

1. rails_helper.rb内で読み込む方法

それぞれのテストファイル内で `require 'rails_helper'` を記述し `rails_helper.rb`を読み込んでいる場合、 `rails_helper.rb` ファイル内でmoduleの読み込み設定をすることができます。

```ruby
RSpec.configure do |config|
  # 中略
  config.include LoginMacros
end
```

[【Rspec】ログイン機能をモジュールで共通化しテストコードをDRYにする - Qiita](https://qiita.com/mmaumtjgj/items/f39cdfcf2fd80af75253)

これらのどちらかの読み込みを行うことで `login(user)` メソッドが使えるようになります。


### before


それぞれのテストケースに対して共通で行いたい処理がある場合にbeforeを利用して処理の共通化をすることができます。

```ruby
before { login(user) }
```

上記の例ではloginモジュールによるログイン処理を対象のテスト前に実行します。


# 機能別テスト


### ユーザー新規登録


```ruby
RSpec.describe "Users", type: :system do
  let(:user) { create(:user) }
  context 'ログイン前' do
    context 'ユーザー新規登録' do
      it 'フォームの値が正しい場合、登録に成功すること' do
        visit root_path
        expect(page).to have_content('SignUp')
        visit new_user_path
        fill_in "email", with: "example@test.com"
        fill_in "password", with: "password"
        fill_in "password_confirmation", with: "password"
        expect { click_on('submit')}.to change { User.count }.by(1)
        expect(page).to have_content('Mypage')
        expect(page).to have_content('Logout')
      end
    end
  end
end
```

1. let(:user) { create(:user) }

Factory_botを使用してuserインスタンスを作成。 `create(:user)` は `FactoryBot.create(:user)` の省略形。

また、let(:user)ではインスタンス変数を定義している。

1. visit root_path

トップ画面へ移動。

1. expect(page).to have_content('SignUp')

移動したトップ画面に `Signup` のボタンがあるかを確認。

1. visit new_user_path

ユーザー登録画面へ移動

1. fill_in 〜

フォーム内に入力する値を設定。上記の例ではそれぞれemailに `example@test.com` 、passwordに `password` 、password_confirmationに `password` を入力したことを想定している。

1. expect { click_on('submit')}.to change { User.count }.by(1)

`expect { click_on('submit')}` はsubmitボタンをクリックした場合の処理を表している。今回はexpectの後ろのカッコが{}となっているがこれはブロック付きのメソッドを渡す場合に使用される。

[RSpec expect の () と {} の違い - Garbage in, gospel out](https://libitte.hatenablog.jp/entry/20141129/1417251425)

また、 `.to change { User.count }.by(1)` はUserデータが１つ増えたことを表しているため、このコードはsubmitボタンをクリックすることでUserデータが１つ増えたかを確認している。

1. expect(page).to have_content('Mypage')、expect(page).to have_content('Logout')

新規登録後に遷移した画面内に `Mypage` と `Logout` の文字が存在しているか（画面遷移が正しく行えているか）を確認している。


### ログアウト


```ruby
context 'ログイン後' do
    before { login(user) }

    it 'ログアウトボタンをクリックするとログアウトできること' do
      click_button "Logout"
      expect(current_path).to eq logout_path
      expect(session[:user_id]).to eq nil
    end
  end
```

1. before { login(user) }

moduleで設定したloginメソッドでログイン状態にする。

1. click_button “Logout”

“Logout”ボタンを押す。

1. expect(current_path).to eq logout_path

“Logout”ボタンを押した後に遷移するパスを確かめている

1. expect(session[:user_id]).to eq nil

ログアウトの処理によりsession[:user_id]がnilになっていることを確認する。

[Railsチュートリアルの第４版をRSpecでテスト-3 - Qiita](https://qiita.com/nanamemo_net/items/319615e6e5fdcd7d844a)


# capybaraの設定


今回の課題では以下のファイルに次の記述をすることで設定しました。詳細は今度調べたいと思います。

spec/support/capybara.rb

```ruby
Capybara.register_driver :remote_chrome do |app|
    options = Selenium::WebDriver::Chrome::Options.new
    options.add_argument('no-sandbox')
    options.add_argument('headless')
    options.add_argument('disable-gpu')
    options.add_argument('window-size=1680,1050')
    Capybara::Selenium::Driver.new(app, browser: :remote, url: ENV['SELENIUM_DRIVER_URL'], capabilities: options)
  end
```


# その他


### -format documentationオプション


RSpecには表示の出力をきれいにする-format documentationオプションがある。

このオプションはRSpec実行時にコマンドの最後に記述する `bundle exec rspec --format documentation`か `.rspec` ファイル内に `-format documentation` を記述することで使用できる。

[RSpecには表示の出力をキレイにする --format documentation というオプションがある - コード日進月歩](https://shinkufencer.hateblo.jp/entry/2019/01/21/233000)


### spec/support配下のファイルの読み込み設定


spec/support配下に作成されたファイルはデフォルトでは読み込まれません。そのため `spec/rails_helper.rb` ファイル内のコメントアウトを外して次の記述をしてあげる必要があります。

```ruby
Dir[Rails.root.join('spec', 'support', '**', '*.rb')].sort.each { |f| require f }
```

[RSpec 設定](https://zenn.dev/atelier_mirai/articles/16d7f9a8e95fd5)


### system RSpecファイルの作成


直接作成することも可能ですが、次のコマンドでsystem RSpecファイルを作成することができます。

```ruby
bundle exec rails g rspec:system ファイル名
```

このコマンドによりspecディレクトリ配下にsystemディレクトリが作成され、その配下にファイルが作成されます。


### 特定のテストのみ実行する方法


RSpecでテストを行う際に一部のテストを試すために全てのテストを実行していると時間がかかってしまいます。そのような時に特定のテストのみを実行する方法を紹介します。

spec/spec_helper.rb

```ruby
RSpec.configure do |config|
------------一部省略-----------------
=begin
  # This allows you to limit a spec run to individual examples or groups
  # you care about by tagging them with `:focus` metadata. When nothing
  # is tagged with `:focus`, all examples get run. RSpec also provides
  # aliases for `it`, `describe`, and `context` that include `:focus`
  # metadata: `fit`, `fdescribe` and `fcontext`, respectively.
  config.filter_run_when_matching :focus

  # Allows RSpec to persist some state between runs in order to support
-------------一部省略---------------------------
  Kernel.srand config.seed
=end
end
```

こちらのファイル内に`=begin` でコメントアウトされている部分があると思いますが、こちらの位置を `config.filter_run_when_matching :focus` の下の行までずらして一部のコメントアウトを外します。

```ruby
RSpec.configure do |config|
------------一部省略-----------------
  # This allows you to limit a spec run to individual examples or groups
  # you care about by tagging them with `:focus` metadata. When nothing
  # is tagged with `:focus`, all examples get run. RSpec also provides
  # aliases for `it`, `describe`, and `context` that include `:focus`
  # metadata: `fit`, `fdescribe` and `fcontext`, respectively.
  config.filter_run_when_matching :focus
=begin
  # Allows RSpec to persist some state between runs in order to support
-------------一部省略---------------------------
  Kernel.srand config.seed
=end
end
```

このコメントアウトを外すだけでdescribe、content、itなどの頭にfをつけるだけでそのテストのみを実行することができます。

[[Rspec] 実行するテストケースを限定する方法](https://osamudaira.com/490/)


# 参考サイト


[RSpec](https://github.com/rspec)

[使えるRSpec入門・その1「RSpecの基本的な構文や便利な機能を理解する」 - Qiita](https://qiita.com/jnchito/items/42193d066bd61c740612)

[使えるRSpec入門・その2「使用頻度の高いマッチャを使いこなす」 - Qiita](https://qiita.com/jnchito/items/2e79a1abe7cd8214caa5)

[使えるRSpec入門・その3「ゼロからわかるモック（mock）を使ったテストの書き方」 - Qiita](https://qiita.com/jnchito/items/640f17e124ab263a54dd)

[使えるRSpec入門・その4「どんなブラウザ操作も自由自在！逆引きCapybara大辞典」 - Qiita](https://qiita.com/jnchito/items/607f956263c38a5fec24)

[RailsにRSpecを導入する方法とやさしい解説 - Qiita](https://qiita.com/_kanacan_/items/59d05a605ef3ed992b03)

[【Ruby on Rails】RSpecのModel（モデル）テスト書き方サンプル - にょけんのボックス](https://nyoken.com/rspec-model)

[【Rails7】RSpecとは？ 導入から使い方まで【初学者向け】](https://blog.to-ko-s.com/rspec-introduction/)

[RSpecを使ったバリデーションテスト - higmonta blog](https://higmonta.hatenablog.com/entry/2021/07/17/224147)

[rspec マッチャー](https://rspec.info/features/3-12/rspec-expectations/built-in-matchers/)

[[Rails]rspecのセットアップから基本の書き方について](https://zenn.dev/redheadchloe/articles/20c3375b328e09)
