# Rails7.1.2 + Ruby3.2.2 + PostgreSQL環境構築


# アプリの作成〜GitHubとの連携まで

    
  ## 先にREADME.mdをプルリクする場合

  
  ### ディレクトリ作成〜プルリクまで
  
  
  1. ローカルに `mkdir` コマンドでアプリ名のフォルダを作成
  
  2. `cd` で作成したフォルダに移動
  
  3. GitHubのブラウザ上でアプリ名と同じリモートリポジトリを作成
  
  4. 作成したリポジトリ内に表示されている次のコマンドを順に実行していく
  
  ```
  echo "# test_pf" >> README.md # <= # test_pfを記述したREADME.mdを作成
    git init # <= カレントディレクトリをローカルリポジトリに変更
    git add README.md
    git commit -m "first commit"
    git branch -M main # <= masterブランチの名前をmainに変更
    git remote add origin git@github.com:HideyukiKamiyama/test_pf.git
    git push -u origin main
  ```
  
  5. ブラウザ上のGitHubの画面を更新し、READMEが作成されているのを確認
  
  6. ブラウザ上で新しくブランチを切る。もしくはターミナルで`git checkout -b ブランチ名` で新しいブランチを切る。
  
  7. [README.md](http://README.md) を編集してプルリクまで
  
  8. LGTMが出たら merge して `git checkout main` 、`git pull origin main`



  ### rails new によるアプリの作成
  
  
  1. `git checkout -b ブランチ名` で新しいブランチを切る
  
  2. `cd ..` コマンドで親ディレクトリに移動
  
  3. `rails new アプリ名 -d postgresql` コマンドで先ほどのアプリ名のディレクトリと同じ名前のアプリを作成
  
  `rails new` のアプリ名がディレクトリ名と同じだと勝手にさっきのディレクトリの配下にアプリが作られる。またこの時、先ほど作成した [`README.md`](http://README.md)を上書きするか確認されるが `n` で拒否してenter（ここで上書きを許可すると先ほど作成したREADMEが削除されるので注意）
  
  `-d postgresql`オプションはデータベースに何を使用するかを指定している
  
  4. 必要な編集を加えたらプルリク〜mergeまで
  
  5.  `git checkout main` 、`git pull origin main`



  
  ## アプリから作成する場合
  
  
  ### アプリの作成
  
  
  1. アプリを作成したいディレクトリに移動する
  
  2. `rails new sample_postgresql -d postgresql`コマンドを実行してアプリを作成する
  
  `-d postgresql`オプションはデータベースに何を使用するかを指定している

  
  ### GitHubのリモートリポジトリにアプリを紐づける
  
  
  1. アプリのディレクトリに移動する
  
  2. `git init`コマンドで初期化する
  
  このコマンドを実行するとアプリのディレクトリ内に`.git`ディレクトリが作成されます
  
  3. GitHub上で新しくリポジトリを作成
  
  4. 次の流れでコマンドを実行
  
  ```
  git add .
  git commit -m "コミットメッセージ"
  git remote add origin <GitHubリポジトリのURL>
  git push origin main
  ```
  
  `git remote add origin <GitHubリポジトリのURL>`の部分は新しく作成したリポジトリに書いてあります。
    





# アプリの実行


1. `brew ls`コマンドで事前にインストールしてあるPostgreSQLのバージョンを確認

```html
% brew ls
==> Formulae
autoconf	git		libcbor		libyaml		mysql		pcre2		protobuf@21	ruby		zlib
ca-certificates	icu4c		libevent	lz4		openssl@1.1	pkg-config	rbenv		ruby-build	zstd
gettext		krb5		libfido2	m4		openssl@3	postgresql@14	readline	xz
```

2. `brew services start postgresql@14`コマンドでDBサーバーを起動（今回はpostgresql@14だった）

```html
% brew services start postgresql@14      
==> Successfully started `postgresql@14` (label: homebrew.mxcl.postgresql@14)
```

3. 念の為`brew services list`コマンドでDBサーバーが実行されているかを確認する。

```
% brew services list

Name          Status  User             File
mysql         none                     
postgresql@14 started kamiyamahideyuki ~/Library/LaunchAgents/homebrew.mxcl.postgresql@14.plist
```

ここで`started`となっていれば大丈夫

4. `rails db:create`コマンドを実行してデータベースを作成する

これをしないとデータベースが無いため`ActiveRecord::NoDatabaseError` が発生してしまう。

5. `rails s`コマンドでサーバーを起動

6. localhost:3000で画面が表示されることを確認

7. Ctr + Cで一度停止

8. DBサーバーも止める場合は`brew services stop postgresql@14`コマンドを実行




# GitHubとの連携（一度行っていれば大丈夫）


### ssh接続

ssh接続ができてない場合は接続を行う

1. `cd ~/.ssh`コマンドで`.ssh`ディレクトリに移動

2. `ssh-keygen -t rsa`コマンドでキーを作成

とりあえず３回エンターを押す

3. `ls -l`コマンドで作成されたキーを確認

4. `id_rsa.pub`というのが公開鍵（`id_rsa`が秘密鍵）なので`pbcopy < id_rsa.pub`コマンドでクリップボードにコピーする

5. GitHub上からsshキーを作成する

https://github.com/settings/sshこちらから設定画面に行き、右上の「New SSH key」をクリックする。→「Add SSH key」をクリック

「Title」に公開鍵名、「key」に先ほどクリップボードにコピーした秘密鍵を貼り付ける

6. `ssh -T [git@github.com](mailto:git@github.com)`コマンドで接続を確認

```
Hi HideyukiKamiyama! You've successfully authenticated, but GitHub does not provide shell access.
```

と返ってきたら成功

https://qiita.com/tmyn470/items/338733077277eb8af8c4

https://zenn.dev/yoshi5490/articles/c7e6c9a1bc0502

https://qiita.com/shizuma/items/2b2f873a0034839e47ce



# 参考サイト


[【完全版】MacでRails環境構築する手順の全て - Qiita](https://qiita.com/kodai_0122/items/56168eaec28eb7b1b93b)

[Railsプロジェクトをgithubで管理する](https://zenn.dev/yoshi5490/articles/c7e6c9a1bc0502)

[rails newしてからGitHubにリポジトリを作成するまでの流れ - Qiita](https://qiita.com/tmyn470/items/338733077277eb8af8c4)
