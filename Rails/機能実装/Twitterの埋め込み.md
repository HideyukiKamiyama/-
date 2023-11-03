# Twitterの埋め込み方法

RailsアプリにTwitterを埋め込む方法についてまとめます。

Twitterの投稿の右上にある点が３つ並んだアイコンをクリックすると次のスクリーンショットの画像のように「</>投稿を埋め込む」というリンクが表示されます。

![image](https://github.com/HideyukiKamiyama/til/assets/137368610/c7a30ee5-f8d5-4288-9b48-c97686c95cfc)

ここをクリックすると次のようにこの投稿を埋め込むためのコードが表示されます。

```html
<blockquote class="twitter-tweet">
  <p lang="ja" dir="ltr">学習時間　3h(590h/1000h)<br>学習内容　Rails応用８途中<br>明日の目標　Rails応用８〜９途中<br>一言<br>できれば日曜までにRails応用を終わらせたいけど終わるかな...。Rails応用はテーブル設計が分かりづらい気がするのでそこを一度整理した方が良いかな...？
    <a href="https://twitter.com/hashtag/RUNTEQ?src=hash&amp;ref_src=twsrc%5Etfw">#RUNTEQ</a>
    <a href="https://twitter.com/hashtag/1000%E6%99%82%E9%96%93%E3%83%81%E3%83%A3%E3%83%AC%E3%83%B3%E3%82%B8?src=hash&amp;ref_src=twsrc%5Etfw">#1000時間チャレンジ</a>
  </p>&mdash; かみ@RUNTEQ46期生Bクラス (@RUNTEQ46B151748)
  <a href="https://twitter.com/RUNTEQ46B151748/status/1720082121979768894?ref_src=twsrc%5Etfw">November 2, 2023</a>
</blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
```

このコードから必要ない部分を削除し、汎用的な形に修正します。

```html
<blockquote class="twitter-tweet">
  <a href="埋め込みたい投稿のURL"></a>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
```

これを埋め込みたい部分に記述したら完了です。

## slim

上記のコードをslimで記述した場合も載せておきます。

```slim
.embed-twitter
  blockquote.twitter-tweet
    a href="埋め込みたい投稿のURL"
  script async="" charset="utf-8" src="https://platform.twitter.com/widgets.js"
```


# 参考サイト

[TwitterのAPIを使わずに任意のツイートを埋め込む方法 - Sakura scope](https://www.nishishi.com/blog/2020/07/twitter_no_api.html)

[YoutubeとTwitterの埋め込み機能の修正と実装 - プログラミング学習　備忘録](https://yukimura907.hatenablog.com/entry/2021/02/14/191852)

[【Rails】APIを使わずにYoutubeとTwitterの埋め込み備忘録 #Twitter - Qiita](https://qiita.com/mmaumtjgj/items/de76cfbc14f8690eae63)

[[Rails] TwitterとYouTubeを埋め込む | 未経験からWebエンジニアを目指す](https://osamudaira.com/401/)
