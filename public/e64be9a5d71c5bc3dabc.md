---
title: ChromeのDevTools（F12）で日常的に使うテクニック7選
tags:
  - HTML
  - CSS
  - JavaScript
  - Chrome
  - debug
private: false
updated_at: '2025-07-08T21:08:27+09:00'
id: e64be9a5d71c5bc3dabc
organization_url_name: null
slide: false
ignorePublish: false
---
# 概要

ChromeのDevTools（F12）の機能の中で私が日常的に使うテクニックを7つ紹介したいと思います。

フロントエンドの開発・デバッグ以外にも、他人が作成したWEBページの内容を確認したり、デザイナーが画面UIの検討などに役立ちます。


:::note warn
スクショの日付が違うため、説明用の画像のUIに差異があると思われますが、ご自身の環境と見比べて確認していただければと思います。
:::

# 起動方法

「**F12**キー」または「ブラウザ上で右クリックし**検証**」で下記のような画面が開きます。これが**DevTools**です。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/9bbaa41b-2642-5566-ddd0-b11cafe864aa.png)

# よく使うテクニック


## 1. HTMLを変更

DevToolsの*要素タブ*を利用し、「開いたWEBページのHTMLが構成内容を確認」や「画面表示しているHTMLを操作」が可能です。

Qiitaのヘッダーの「投稿する」ボタンをもとに「更新する」と「削除する」のボタンに変えてみました。（一時的な変更なので画面をリロード（F5）すると元に戻ります）

![Animation4.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/8d8cd1d8-0e67-31e8-b444-89cf3c4b5b98.gif)

一時的に修正してスクショをしたり、UIの検討などに役立ちます。

## 2. スタイルの変更

画面のスタイルについても同様に修正することができます。

`elememt.style`はインライン、idやclassといった`セレクタ`にスタイルを定義することも可能です。`:hov`から擬似クラスのスタイルを検証することもできます。

![Animation4.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/d217e849-26f0-5d1e-22ad-b7537c154ff8.gif)

## 3. スマホのエミュレータ

スマホやタブレットがなくても画面サイズごとの確認ができます。

![Animation.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/3e37d27c-1689-f896-8410-108c8f8b4096.gif)

## 4. JavaScriptの実行

画面上で定義されているメソッドを実行することが可能ですが、私の場合は、高性能なエディタとして使っています。

![Animation.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/8a0f95c9-eecd-fd66-0f10-3380a08405c3.gif)

## 5. Javascript変数の監視

目のアイコンから変数の中身を監視することができます。画像では、コンソール上で変数を操作していますが、画面上を操作したときに正常に値が入っているかを確認するときに役立ちます。


![Animation.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/74928e38-2d31-297f-be84-f72bd2a385aa.gif)

## 6. 読み込んだファイルの確認

Networkタブから、画面上で読み込んだファイルを確認できます。「ステータスコード」や「ファイルの中身」や「ファイルのURL」をよく確認します。

![Animation.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/22d8712b-b9de-7765-89f8-6bac7defa2e6.gif)

## 7. 画面のスクショ取得

全画面のスクショを取るには以下の操作をします。

- 「Ctrl] + 「Shift」+ 「p」でコマンドパレットを開く
- 「Capture full screenshot」を選択しスクショを保存

![Animation.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/660fce8a-80f4-47d1-4fdb-7820a046f51a.gif)

「全画面」スクショのほかに「範囲指定」や「画面表示されてる部分」のスクショも選べます。

# 終わりに

今回は、ChormeのDevTools（F12）で私が日常的に使うテクニックを7つに絞って紹介しました。

紹介した機能以外にも、便利な機能が沢山あるので機会があればまた紹介したいと思います。

ここまで読んでいただきありがとうございました。
