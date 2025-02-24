# 67. OpenAI API
OpenAI が提供する生成 AI モデルを活用する方法を学びます。

## 67.1 OpenAI API の概要

### 67.1.1 OpenAI API とは
- OpenAI API は、OpenAI 社が提供する生成 AI モデルを利用するための API です
- OpenAI API を利用することで、ゲームやアプリに最新の生成 AI モデルを統合できます
- OpenAI API の一般的な利用方法は、次のような流れになります：
	- ① データ + API キーからなるリクエストを OpenAI サーバに送る。
	- ② OpenAI サーバが JSON で結果を返答する（内容によっては時間がかかる）
	- ③ 返された JSON から必要な部分を抽出する
- Siv3D では `OpenAI::～` に用意された関数を使うことで、①～③ の一連の処理を簡単にプログラムできます

### 67.1.2 Siv3D で利用できる OpenAI API
- Siv3D では、次の OpenAI API のための関数が用意されています：

| API の種類 | 説明 |
|--|--|
| Chat | 一連の会話に続くメッセージを回答する |
| Image | プロンプトに基づいた画像を生成する |
| Vision | 画像に関する質問に回答する |
| Speech | テキストを音声に変換する |
| Embedding | 単語や文章を、埋め込みベクトルに変換する |

### 67.1.3 OpenAI API の利用料金
- AI が結果を返すとき、入力や出力の長さ（トークン数）に応じて、API の利用料金が発生します
- 詳しくは [OpenAI | Pricing :material-open-in-new:](https://openai.com/ja-JP/api/pricing/){:target="_blank"} を参照してください。
- OpenAI API の利用料金は事前チャージ方式のため、使いすぎて請求額が高額になる心配はありません
	- 本章のサンプルを全部試しても、1 ドルに満たない程度です
- 料金の使用状況は、OpenAI アカウントのダッシュボードで確認できます

## 67.2 OpenAI API キー
### 67.2.1 OpenAI API キーの発行
- OpenAI API を利用するには、OpenAI アカウントを作成し、API キーを取得する必要があります
- API キーは、OpenAI アカウントのダッシュボードから発行できます
- API キーは `sk-` から始まる数十文字の英数文字列です
- この API キーによってアカウントの認証が行われ、API の利用が可能になります

### 67.2.2 OpenAI API キーを安全に保存する
- コードをコミット・公開したときに自身の API キーが流出しないよう、開発中は API キーを環境変数に保存し、環境変数を読み取るコード（**チュートリアル 66.2**）で API キーを取得することが推奨されます

```cpp
// 環境変数 "MY_OPENAI_API_KEY" から API キーを取得する
const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");
```

- 万が一 API キーが外部に漏れた場合は、キーを無効化して新しいキーを発行することができます

### 67.2.3 環境変数の設定方法
??? info "Windows での環境変数の設定方法"
	- システムのプロパティから環境変数を設定します。

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/openai/2a.png)

	- ユーザー環境変数に "MY_OPENAI_API_KEY" という名前で API キーを設定します

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/openai/2b.png)
	
	- システムに完全に適用させるためには、再起動が必要な場合があります。


??? info "macOS での環境変数の設定方法"
	- ターミナルで次のようなコマンドを入力します。
	- `launchctl setenv <環境変数のキー> "<環境変数の値>"`

	```
	launchctl setenv MY_OPENAI_API_KEY "sk-12345689abcdefghi..."
	```

	- 再起動すると設定は失われます。


### 67.2.4 環境変数が設定されたかの確認
- 次のコードで、環境変数が設定されているかを確認できます

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 環境変数から API キーを取得する
	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	// API キーを表示する
	Print << API_KEY;

	while (System::Update())
	{

	}
}
```

- 本章の以降のサンプルコードは、環境変数 `MY_OPENAI_API_KEY` に API キーが設定されていることを前提としています


## 67.3 Chat の基本
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/openai/3.png)

```cpp

```


## 67.4 Chat の基本（非同期）
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/openai/3.png)

```cpp

```


## 67.5 UI の工夫
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/openai/3.png)

```cpp

```


## 67.6 ロールと履歴
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/openai/3.png)

```cpp

```


## 67.7 ロールプレイングゲーム
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/openai/3.png)

```cpp

```


## 67.8 画像生成
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/openai/3.png)

```cpp

```


## 67.9 画像生成（非同期）
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/openai/3.png)

```cpp

```


## 67.10 画像に関する質問
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/openai/3.png)

```cpp

```


## 67.11 複数の画像に関する質問
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/openai/3.png)

```cpp

```


## 67.12 テキストの読み上げ
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/openai/3.png)

```cpp

```


## 67.13 Embedding
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/openai/3.png)

```cpp

```
