---
title: Gemini APIを使って漢字をひらがなに変換する
tags:
  - Go
  - GeminiAPI
private: false
updated_at: '2024-12-10T09:00:18+09:00'
id: 25f77ff6ef8adeacc535
organization_url_name: null
slide: false
ignorePublish: false
---
# 概要

前回、Go言語の勉強のため「ぱ行で会話するための変換プログラムを作成」しました。（下記参照）

https://qiita.com/ishi720/items/3e747fbbc569494ce9d2

しかし「漢字をひらがなに変換する処理」が未実装だったので、Googleが開発した生成型人工知能チャットボットの**Gemini**のAPIを利用して実装していきたいと思います。

# 準備

- 下記URLからGemini APIのAPIキーを取得してください

https://ai.google.dev/gemini-api/docs?hl=ja

- GCPでGemini APIが有効になっているか確認してください


# プログラムコード

それでは、今回書いたプログラムです。
意外とコード量は少なく済みました。

```go
package main

import (
	"context"
	"fmt"

	"github.com/google/generative-ai-go/genai"
	"google.golang.org/api/option"
)

func main() {
	ctx := context.Background()
	text := "お寿司を食べる"

	// Gemini APIクライアントを作成する
	client, err := genai.NewClient(ctx, option.WithAPIKey("{YOUR API KEY}")) // 取得したAPIキーを指定
	if err != nil {
		fmt.Println("Geminiクライアントの作成に失敗しました")
	}
	defer client.Close()

	// モデルとプロンプトの準備
	model := client.GenerativeModel("gemini-1.5-flash") // Geminiのモデルを指定
	prompt := genai.Text("以下の漢字の部分をひらがなに変換してください: " + text)
	res, err := model.GenerateContent(ctx, prompt)
	if err != nil {
		fmt.Println("Gemini APIの呼び出しに失敗しました")
	}
	// 変換結果を出力
	fmt.Println(res.Candidates[0].Content.Parts[0])
}
```

ちなみに`以下の漢字の部分をひらがなに変換してください`の部分を変えてあげるだけで違うプログラムになります。なんか不思議な感覚です。


# 結果

そして変換結果です。なかなか制度が高いのではないでしょうか。

|変換前|変換後|
|--|--|
|お寿司を食べる|おすしをたべる|
|以心伝心|いしんでんしん|
|東京特許許可局|とうきょうとっきょきょかきょく|
|会議の最中に最中を食べたい|かいぎのさいちゅうに<font color="red">**さいちゅう**</font>をたべたい|
|祇園精舎の鐘の声、諸行無常の響きあり。娑羅双樹の花の色、盛者必衰の理をあらはす。|ぎおんしょうじゃのかねのこえ、しょぎょうむじょうのひびきあり。さらそうじゅのはなのいろ、じょうしゃひっすいの<font color="red">**り**</font>をあらわす。|

# 終わりに

今回は、**Go言語**と**Gemini API**を使って**ひらがな変換機能**を作りました。
精度は100％ではありませんが、質問の仕方を変えたり、変換後の文章をどのように使うかでカバーできそうです。

今度は[前回の記事](https://qiita.com/ishi720/items/3e747fbbc569494ce9d2)とプログラムと合体させて1つのアプリケーションを作成したいと思います。

以上、ここまで読んでいただきありがとうございました。
