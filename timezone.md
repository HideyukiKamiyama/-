# timezone設定

---

# タイムゾーンとは

---

タイムゾーンとはUTC(協定世界時)によって定められている時差区分のこと。これにより日本では13:00の時ハワイでは18:00といった時差が生じる。

# タイムゾーンの設定

---

railsのタイムゾーンはconfig/application.rbで設定できます。

- config.time_zone

config.time_zoneはrailsのアプリケーション内で使用するタイムゾーンを設定します。TokyoやSydnyなどの都市名を設定することで設定することができます。またrailsの日付を扱うクラスであるTimeWithZoneに対応しています。

何も設定していないとデフォルトでUTCになります。

- config.active_record.default_zimezone

config.active_record.default_zimezoneはDBを読み書きする際にDBに記録されている時間をどのタイムゾーンで記録するかを指定します。:utc と :local のどちらかを設定することができ、何も設定していない場合は :utc となります。:localを設定した場合、DBサーバーのタイムゾーンが適用されます。

タイムゾーンは、コンソール上でTime.zoneやTime.currentなどの結果を見ることで確認できます。

config/application.rb内に次のように記述することで設定できます。

```ruby
config.time_zone = "Tokyo"
config.active_record.default_timezone = :local
```

# **TimeWithZoneクラスを使ったほうがいい理由**

---

> 国をまたいだ開発をする際、Aさんの環境変数TZはUTC、Bさんの環境変数TZはJSTとなっているとします。そうした場合に、AさんとBさんで表示される日時が異なってきます。`TimeWithZone`クラスを使えば`application.rb`の設定が適用されるので、環境による差はなくなります。なので、Railsを使って日付を扱う場合は、`TimeWithZone`クラスを使うのがよいでしょう。引用：https://sakaishun.com/2021/03/22/time-timewithzone/
>
