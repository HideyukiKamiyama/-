# Vimとは


# 書き込み権限がない状態でファイルの書き込みをしてしまった場合

書き込み権限がない状態でファイルを編集してしまった場合は次のコマンドで強制的に保存することができます。

```
:w !sudo tee %
```

上記のコードはそれぞれ次のことを表しています。

- w : vimにおけるファイルの保存コマンド
- !sudo tee : !にてコマンド`sudo tee`を指示
- % : 現在開いているファイルを表す

このコマンドで強制的に保存した後、`:q!`コマンドでVimを終了して完了です。


# 参考サイト

[【Vim使い方】初心者でもすぐに始められるVimの基本](https://original-game.com/vim-mac2/)

[初心者向けVimの使い方まとめ | エビワークス](https://ebi-works.com/vim/)

[vim :: readonly のファイルを sudo で強制的に保存する    [Tipsというかメモ]](https://tm.root-n.com/unix:command:vim:readlonly_write)

