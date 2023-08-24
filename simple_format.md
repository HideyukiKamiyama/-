# simple_format

---

# simple_formatとは

---

simple_formatはRialsのヘルパーメソッドです。simple_formatを使うとviewでテキストを表示したい場合に改行が反映されなくなってしまう問題を解消することができます。

例えば、コントローラで

```ruby
@txt = "あいうえお\nかきくけこ\n\nさしすせそ"
```

と記述し、Viewで以下のように表示した場合

```ruby
<%= @txt %>
```

```ruby
あいうえお かきくけこ  さしすせそ
```

と改行文字が反映されずに表示されてしまいます。これを解消するのがsimple_formatです。

上記の例をsimple_formatを使用して表示した場合、次のようになります。

```ruby
<%= simple_format(@txt) %>
```

```ruby
あいうえお
かきくけこ

さしすせそ
```

# simple_formatで行っていること

---

simple_formatで囲った文字を次のように加工しています。

- 文字列を<p>で括る
- 改行は<br />を付与
- 連続した改行は、</p><p>を付与

# 参考サイト
