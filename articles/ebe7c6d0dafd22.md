---
title: "Flutterで作ったアプリをGoogle Playにアップロードしてテストする"
emoji: "👻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Flutter, Android, Google_Play]
published: false
---
# 手順
1. アプリを署名付きでビルドする
この時、pubspec.yamlのバージョン情報(1.0.0+1など)がandroid/local.propertiesに反映されていているかを確認する。反映されていない場合は一度Androidでデバッグ実行するなどすると更新される。  
![](https://storage.googleapis.com/zenn-user-upload/ce10f6099a98-20251214.png)
![](https://storage.googleapis.com/zenn-user-upload/60bd209a1465-20251214.png)
Android Studioでandroidフォルダを開き、Build > Generate Signed App Bundle or APK...でappバンドルをビルドする。署名キーを作成していない場合はここで作成できる。
![](https://storage.googleapis.com/zenn-user-upload/19583273657a-20251214.png)

2. Google Playに作成したaabファイルアップロードする
「新しいリリースを作成」で今回作成したaabファイルをGoogle Playにアップロードする。  
一度でもアップロードしてしまうと公開しなくてもそのバージョンコードは使用できなくなるので注意。  
![](https://storage.googleapis.com/zenn-user-upload/4ca70545d5a4-20251214.png)
そのまま進めれば公開できる。

3. 内部テスト用URLを取得し、テストを行う
メーリングリストに必要なメールアドレスを入力し、テストを行う。
内部テスト用URLからGoogle Playのダウンロードページ(仮)に遷移できる。
![](https://storage.googleapis.com/zenn-user-upload/1323dc25f448-20251214.png)

終わりです。
