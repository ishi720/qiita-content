---
title: ゲーミングCSSを作ってQiitaを光らせてみた
tags:
  - CSS
  - animation
  - npm
  - gaming-css
private: false
updated_at: '2023-11-19T10:32:31+09:00'
id: d8a4ff56ad0defb8b87b
organization_url_name: null
slide: false
ignorePublish: false
---
# 概要

ゲーミングPC、ゲーミングマウス、ゲーミングキーボード、ゲーミングチェアーなど、色々ありますが、なぜか光っています。

なので、WEB画面も光らせてみようと、`gaming-css`というプラグイン作成しnpmに公開しました。

# セットアップ方法

下記のコマンドでインストールできます。

```bash
$ npm install gaming-css
```

詳細な使い方についてはREADMEを確認して頂ければと思います。

https://github.com/ishi720/gaming-css

# CSSを適用

## Qiitaのプロフィール画面を装飾しました！

### Before

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/c2a7dca2-6b89-7836-ea05-22dd00c3d1d0.png)

### After

![Animation4.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/fd6efbee-2e1d-de3c-ae5b-27d444d71976.gif)

とっても綺麗になりましたね！（Qiita運営さん！ゲーミングテーマはいかがでしょうか？）

ちなみに、CSSを適用するのに１時間くらいかかりました。


# 仕組み

仕組みは、簡単です。下記のように、線・文字・背景・画像などに対してアニメーションのスタイルを用意して適用しているだけです。

```css
/*背景色*/
.gaming-background-color {
  animation: gaming-background-color 5s linear infinite
}
@keyframes gaming-background-color {
  0% { background-color: Magenta; }
  33% { background-color: yellow; }
  66% { background-color: Cyan; }
  100% { background-color: Magenta; }
}
```

```css
/*画像の色*/
.gaming-filter {
  animation: gaming-filter 5s linear infinite;
}
@keyframes gaming-filter {
  0% { filter: hue-rotate(0deg); }
  33% { filter: hue-rotate(120deg); }
  66% { filter: hue-rotate(240deg); }
  100% { filter: hue-rotate(360deg); }
}
```

# おまけ

npmにパッケージ公開する時のメモです。

```bash
# 認証
$ npm adduser

# 自分が誰かを確認
$ npm whoami

# 登録
$ npm publish

# 削除
$ npm unpublish {パッケージ名}@{バージョン} --force
```


# おわりに

今回は、自作したnpmのパッケージの`gaming-css`の紹介をしました。

皆さんも、WEB画面を光らせてみてはどうでしょうか。

ここまで、読んでいただきありがとうございました。

