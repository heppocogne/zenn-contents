---
title: "【Godot】NodePathで変数にアクセスしたい"
emoji: "👏"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: [Godot]
published: true
---

# NodePathについて
GodotのNodePathはノードやプロパティを指す文字列です。
ノードを指す場合:`Path/To/Node`
プロパティを指す場合:`Path/To/Sprite:offset:x`

ノードを指している場合は`get_node()`や`find_node()`でノードにアクセスすることができますが、プロパティを指している場合にはそうはいきません。


# NodePathで変数にアクセスして値を取得/変更したい
`$Path/To/Sprite.offset.x=100`のようにコード上で変数にアクセスすることができず、対象の変数のノードパスでアクセスしたくなりました。
自分が調べた限りでは`Path/To/Sprite:offset:x`のようなノードパスを使って変数に直接アクセスする方法は用意されていないようです。
しかし`get_node("Path/To/Sprite")`で対象ノードにはアクセスすることができ、`get_indexed()`,`set_indexed()`で対象の変数(`:offset:x`[^1])にアクセスすることができます。

例えば、`Path/To/Sprite:offset:x`の値は下記のように取得/変更することができます。
```python
get_node("Path/To/Sprite").get_indexed("offset:x")
get_node("Path/To/Sprite").set_indexed("offset:x", 100)
```

ノードパスを文字列処理で分解してやれば上の処理は関数にすることもできます。
```python
# 値を取得する
func get_node_property(path:String):
    var colon_index:int=path.find(":")
    var node:Node=get_node(path.left(colon_index))
    return node.get_indexed(path.right(colon_index))


# 値を変更する
func set_node_property(path:String, val):
    var colon_index:int=path.find(":")
    var node:Node=get_node(path.left(colon_index))
    node.set_indexed(path.right(colon_index), val)
```

ちなみに、`Path/To/Sprite:offset`を変更するだけなら普通に`set()`,`get()`を使う方が良いと思います。
```python
get_node("Path/To/Sprite").get("offset")
get_node("Path/To/Sprite").set("offset", Vector2(100,0))
```

[^1]: `:offset:x`でも`offset:x`でもアクセスできるようです。
