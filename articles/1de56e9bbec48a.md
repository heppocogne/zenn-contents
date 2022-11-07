---
title: "【Godot】Webエクスポートをする際の注意点"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Godot, GodotEngine, gamedev]
published: true
---

# はじめに
Godotでwebアプリケーションを作った時に躓いた所を書いていきます。

## 出力された.htmlファイルを開いても実行できない
出力された.htmlファイルをダブルクリックで開いても`Failed to Fetch`と表示されて実行できません。
ブラウザが非対応とかそういうことではなく、エクスポートされたhtmlファイルはサーバー上で実行される事が前提になっているからです。([参考動画](https://www.youtube.com/watch?v=eMGxxwOA-6g&t=512s))
Project > ExportでHTML5エクスポートの設定を行うと右上にエクスポートされたhtmlをブラウザ上で実行するボタンが現れます。
![](https://storage.googleapis.com/zenn-user-upload/13159caf1a9a-20221107.png)

## `print()`、`print_debug()`が表示されない
上述の通りにブラウザ上で実行してもGodot Editor上には`print()`、`print_debug()`の内容はおろかエラーさえ出力されません。エラーが発生するとUIは動いているのに反応しない...という状態になります。
`print()`やエラーはブラウザの開発者コンソール上に表示されます。
![](https://storage.googleapis.com/zenn-user-upload/11645809ede8-20221107.png)

## ローカルファイルシステムがwebサーバーのものになっている
`access=FileDialog.ACCESS_FILESYSTEM`と設定してローカルに保存させようと思ったらwebサーバーのファイルシステムが表示されました。
![](https://storage.googleapis.com/zenn-user-upload/91162aad178d-20221107.png)
Godot内で作成したファイルをローカルに保存させるには`JavaScript.download_buffer(buffer:PoolByteArray, name:String, mime:String)`が使用できます。
ただしJavaScriptシングルトンはデスクトップ向けのビルドでは無効になっている(デフォルトの場合)為、`OS.has_feature("JavaScript")`で条件分岐させてJavaScriptシングルトンが有効な場合のみに実行させる必要があります。
- 例1: テキストファイルをDLさせる
    ```python
    var f:=File.new()
    f.open("temp.txt",File.WRITE)
    f.store_var("abc")
    f.close()
    f.open("temp.txt",File.READ)
    JavaScript.download_buffer(f.get_buffer(f.get_len()),"test.txt","text/plain")

    # もしくは
    JavaScript.download_buffer("abc".to_utf8(),"test.txt","text/plain")
    ```
- 例2: png画像をDLさせる
    ```python
    var img:Image=save_texture.get_data()
    var f:=File.new()
    img.save_png("temp.png")
    f.open("temp.png")
    JavaScript.download_buffer(f.get_buffer(f.get_len()),"test.png","image/png")

    # もしくは
    var img:Image=save_texture.get_data()
    JavaScript.download_buffer(img.save_png_to_buffer(),"test.png","image/png")
    ```

## html5向けのバイナリはWindowsではコンパイルできない
Windowsに[emsdk](https://github.com/emscripten-core/emsdk)を導入してもjavascriptにはコンパイルできないようです。
今回はカスタムテンプレートを使おうとしただけですが、GDNativeもコンパイルできないと困るかも。

## その他
何かあれば追記します。
