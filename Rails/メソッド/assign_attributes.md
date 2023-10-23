# assign_attributesとは？

`assign_attributes`メソッドとは属性の変更を行いたい場合に使用するメソッドです。
引数に属性のハッシュを渡すことで使用することができます。


# 使用例

以下は使用例です。（Railsドキュメントより引用）

> ```ruby
> class Cat
>   include ActiveModel::AttributeAssignment
>   attr_accessor :name, :status
> end
> cat = Cat.new
> cat.assign_attributes(name: "Gorby", status: "yawning")
> cat.name #=> 'Gorby'
> cat.status #=> 'yawning'
> cat.assign_attributes(status: "sleeping")
> cat.name #=> 'Gorby'
> cat.status #=> 'sleeping'
> ```

上記のコードから分かる通り`assign_attributes`メソッドは受け取った属性を後から上書きすることができます。

具体的な使用方法としては更新処理を行う際、受け取ったパラメータを必要に応じて変更してから保存する場合などに使用します。

また、`update`メソッドと異なり、保存処理は行わないため`assign_attributes`メソッドで上書きした後は`save`メソッドで保存する必要がある点には注意が必要です。


# 参考サイト

[assign_attributes | Railsドキュメント](https://railsdoc.com/page/assign_attributes)
