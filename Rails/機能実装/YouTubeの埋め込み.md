# YouTubeの埋め込み方法

RailsアプリでYouTubeを埋め込みたい場合の方法について紹介します。
YouTube動画には一つ一つに固有のIDが割り振られており、それがURLの１１文字に記されています。
この固有のIDを埋め込み用URLに渡すことで動画をアプリ内に埋め込むことができます。


## URLの種類

YouTubeにはURLが大きく２種類存在します。

- ブラウザで使用するURL

https://www.youtube.com/watch?v=M2bQzkg8R8A

- 共有用URL

https://youtu.be/M2bQzkg8R8A?si=tNcYxxKBOxii_mUw

こちらの`?si=tNcYxxKBOxii_mUw`の部分はクエリパラメータというもので追加情報をURLに含めたものです。

これらの二つのURLを観察するとどちらも`M2bQzkg8R8A`という１１文字の固有のIDが含まれていることが分かります。

このIDを埋め込み用URLである`https://www.youtube.com/embed/`の後ろに渡してあげることでRailsアプリ内にYouTube動画を埋め込むことができます。


# 参考サイト

[Rails Youtube 動画投稿・編集](https://zenn.dev/takahiro014124/articles/f6b815af0b8418)

[【rails入門】youtube埋め込み #Rails - Qiita](https://qiita.com/Naoya_pro/items/a8e992c40b69c88ed9e2)
