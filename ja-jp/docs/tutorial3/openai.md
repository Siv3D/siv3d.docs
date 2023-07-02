# 59. OpenAI API
OpenAI API と連携する生成 AI の機能を学びます。

## 59.1 OpenAI API の概要

### 59.1.1 OpenAI API とは
OpenAI API は、OpenAI が提供する AI 生成モデルを利用するための API です。OpenAI API を利用することで、AI 生成モデルを簡単に利用することができます。

### 59.1.2 利用の流れ
OpenAI API の利用は、次のような流れになります。

1. データ + API キーからなるリクエストを OpenAI サーバに送る。
2. OpenAI サーバが JSON で結果を返答する（内容によっては時間がかかる）
3. 返された JSON から必要な部分を抽出する

Siv3D では `OpenAI::～` に用意された関数を使うことで、一連の処理を簡単にプログラムできます。

### 59.1.3 Siv3D で利用できる OpenAI API

- **Chat:** 一連の会話に続くメッセージを回答する
- **Image:** 英語での説明に基づいた画像を返す
- **Embedding:** 単語や文章を意味に基づいた埋め込みベクトルに変換する

### 59.1.4 OpenAI API の利用料金
OpenAI が返答するとき、入出力のトークン数に応じて、API の利用料金が発生します。詳しくは [OpenAI | Pricing :material-open-in-new:](https://openai.com/pricing){:target="_blank"} を参照してください。

API の利用料金が高額になることが心配な場合は、Usage limits（毎月の上限）を設定できます。デフォルトでは毎月 120 ドルです。


## 59.2 準備 | OpenAI API キーの発行と管理

### 59.2.1 OpenAI API キーの発行
OpenAI の API キーは、"sk-" で始まる数十文字の文字列です。

OpenAI アカウントにサインインし、支払い手段の登録を済ませた状態で [https://platform.openai.com/account/api-keys :material-open-in-new:](https://platform.openai.com/account/api-keys){:target="_blank"} の「Create new secret key」から OpenAI API キーを発行できます（一度しか表示されません）。

### 59.2.2 API キーを安全に保存する
コードをコミット・公開したときに API キーが流出しないよう、開発中は API キーを環境変数に保存し、環境変数を読み取るコードで API キーを取得するようにします。

```cpp
// 環境変数 "MY_OPENAI_API_KEY" から API キーを取得する
const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");
```

### 59.2.3 環境変数の設定方法

??? info "Windows での環境変数の設定方法"
    システムのプロパティから環境変数を設定します。

    ![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/2a.png)

    ユーザー環境変数に "MY_OPENAI_API_KEY" という名前で API キーを設定します。システムに完全に適用させるためには、再起動が必要な場合があります。

    ![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/2b.png)


??? info "macOS での環境変数の設定方法"
    ターミナルで次のようなコマンドを入力します。

    `launchctl setenv <環境変数のキー> "<環境変数の値>"`

    ```
    launchctl setenv MY_OPENAI_API_KEY "sk-12345689abcdefghi..."
    ```

    再起動すると設定は失われます。


### 59.2.4 環境変数が設定されたかの確認

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

## 59.3 Chat の基本
`OpenAI::Chat::Complete()` は、OpenAI の Chat API を利用して、一連の会話に続く回答を取得する関数です。OpenAI からのレスポンスが返ってくるまで関数は制御を返さない（待ちが発生する）点に注意します。待っている間に別のことをしたい場合は 59.5 で扱う非同期版の関数を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 環境変数から API キーを取得する
	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	// 回答を String で得る
	const String answer = OpenAI::Chat::Complete(API_KEY, U"日本で一番高い山は？");

	Print << answer;

	while (System::Update())
	{

	}
}
```


## 59.4 テキストボックスからの入力を Chat に送る


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// 環境変数から API キーを取得する
	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	// テキストボックスの中身
	TextEditState textEditState;

	// 回答を格納する変数
	String answer;

	while (System::Update())
	{
		// テキストボックスを表示する
		SimpleGUI::TextBox(textEditState, Vec2{ 40, 40 }, 600);

		if (SimpleGUI::Button(U"送信", Vec2{ 660, 40 }, 80,
			(not textEditState.text.isEmpty()))) // テキストボックスが空でないときだけボタンを有効にする
		{
			// 質問文
			const String input = textEditState.text;

			// 回答文
			answer = OpenAI::Chat::Complete(API_KEY, input);
		}

		// 回答がある場合
		if (answer)
		{
			font(answer).draw(20, Rect{ 40, 100, 720, 600 }, ColorF{ 0.11 });
		}
	}
}
```


## 59.5 非同期で Chat の回答を取得する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// 環境変数から API キーを取得する
	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	// テキストボックスの中身
	TextEditState textEditState;

	// 非同期タスク
	AsyncHTTPTask task;

	// 回答を格納する変数
	String answer;

	while (System::Update())
	{
		// テキストボックスを表示する
		SimpleGUI::TextBox(textEditState, Vec2{ 40, 40 }, 600);

		if (SimpleGUI::Button(U"送信", Vec2{ 660, 40 }, 80,
			((not textEditState.text.isEmpty()) // テキストボックスが空でなく
				&& (not task.isDownloading())))) // タスクの実行中でないときだけボタンを有効にする
		{
			// 前回の回答を消去する
			answer.clear();

			// 質問文
			const String input = textEditState.text;

			// タスクを作成する
			task = OpenAI::Chat::CompleteAsync(API_KEY, input);
		}

		// ChatGPT の応答を待つ間はローディング画面を表示する
		if (task.isDownloading())
		{
			Circle{ Scene::Center(), 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}

		// 非同期処理が完了し、正常なレスポンスである場合
		if (task.isReady() && task.getResponse().isOK())
		{
			// 非同期処理の結果を取得する
			answer = OpenAI::Chat::GetContent(task.getAsJSON());
		}

		// 回答がある場合
		if (answer)
		{
			font(answer).draw(20, Rect{ 40, 100, 720, 600 }, ColorF{ 0.25 });
		}
	}
}
```


## 59.6 入力 UI の工夫

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// 環境変数から API キーを取得する
	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	// テキストボックスの中身
	TextEditState textEditState;

	// 非同期タスク
	AsyncHTTPTask task;

	// 回答を格納する変数
	String answer;

	while (System::Update())
	{
		// テキストボックスを表示する
		SimpleGUI::TextBox(textEditState, Vec2{ 40, 40 }, 240);

		if (SimpleGUI::Button(U"に登場する敵モンスターを生成", Vec2{ 300, 40 }, 360,
			((not textEditState.text.isEmpty()) // テキストボックスが空でなく
				&& (not task.isDownloading())))) // タスクの実行中でないときだけボタンを有効にする
		{
			// 前回の回答を消去する
			answer.clear();

			// 質問文
			const String input = (U"RPG ゲームで" + textEditState.text + U"に登場する敵モンスターを 1 種類考えてください。");

			// タスクを作成する
			task = OpenAI::Chat::CompleteAsync(API_KEY, input);
		}

		// ChatGPT の応答を待つ間はローディング画面を表示する
		if (task.isDownloading())
		{
			Circle{ Scene::Center(), 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}

		// 非同期処理が完了し、正常なレスポンスである場合
		if (task.isReady() && task.getResponse().isOK())
		{
			// 非同期処理の結果を取得する
			answer = OpenAI::Chat::GetContent(task.getAsJSON());
		}

		// 回答がある場合
		if (answer)
		{
			font(answer).draw(20, Rect{ 40, 100, 720, 600 }, ColorF{ 0.25 });
		}
	}
}
```


## 59.7 回答を JSON 形式で得る

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// 環境変数から API キーを取得する
	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	// テキストボックスの中身
	TextEditState textEditState;

	// 非同期タスク
	AsyncHTTPTask task;

	// 回答を格納する変数
	String answer;

	while (System::Update())
	{
		// テキストボックスを表示する
		SimpleGUI::TextBox(textEditState, Vec2{ 40, 40 }, 240);

		if (SimpleGUI::Button(U"に登場する敵モンスターを生成", Vec2{ 300, 40 }, 360,
			((not textEditState.text.isEmpty()) // テキストボックスが空でなく
				&& (not task.isDownloading())))) // タスクの実行中でないときだけボタンを有効にする
		{
			// 前回の回答を消去する
			answer.clear();

			// 質問文
			String input = (U"RPG ゲームで" + textEditState.text + U"に登場する敵モンスターを 1 種類考えてください。\n");
			input += U"出力は次のような JSON 形式で、日本語で出力してください。回答に JSON データ以外を含まないで下さい。\n";
			input += UR"({ "name": "敵の名前", "desc" : "説明" })"; // UR"()" は生文字列リテラル https://cpprefjp.github.io/lang/cpp11/raw_string_literals.html

			// タスクを作成する
			task = OpenAI::Chat::CompleteAsync(API_KEY, input);
		}

		// ChatGPT の応答を待つ間はローディング画面を表示する
		if (task.isDownloading())
		{
			Circle{ Scene::Center(), 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}

		// 非同期処理が完了し、正常なレスポンスである場合
		if (task.isReady() && task.getResponse().isOK())
		{
			// 非同期処理の結果を取得する
			answer = OpenAI::Chat::GetContent(task.getAsJSON());
		}

		// 回答がある場合
		if (answer)
		{
			font(answer).draw(20, Rect{ 40, 100, 720, 600 }, ColorF{ 0.25 });
		}
	}
}
```


## 59.8 複数のメッセージからなる会話から回答を得る
role と message をペアにした `Array<std::pair<String, String>` を渡すことで、複数のメッセージからなる会話に対して回答を得ることができます。

| role | 説明 |
|--|--|
| system | 前提条件や役割などを AI に指示 |
| user | ユーザの発言 |
| assistant | AI の発言 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 環境変数から API キーを取得する
	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	Print << OpenAI::Chat::Complete(API_KEY, {
		{ U"system", U"回答はできる限り簡潔にして、末尾に！を付けてください。" },
		{ U"user", U"白い食べ物は？" },
		{ U"assistant", U"豆腐！" },
		{ U"user", U"では黒いのは？" } });

	while (System::Update())
	{
	
	}
}
```


## 59.9 プロンプトから画像を生成する（同期）

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(512, 512);

	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	const Texture texture{ OpenAI::Image::Create(API_KEY,
		U"There are tall mountains in the distance. The sky is clear.",
		OpenAI::ImageSize512) };

	while (System::Update())
	{
		texture.draw();
	}
}
```


## 59.10 プロンプトから画像を生成する（非同期）

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	Array<Texture> textures;

	AsyncTask task = OpenAI::Image::CreateAsync(API_KEY, U"Mount Fuji has erupted, and volcanic ash is falling on Tokyo.", 4, OpenAI::ImageSize256);

	while (System::Update())
	{
		if (task.isValid())
		{
			Circle{ Scene::Center(), 50 }.drawArc(Scene::Time() * 120_deg, 300_deg, 4, 4);
		}

		if (task.isReady())
		{
			for (const auto& image : task.get())
			{
				textures << Texture{ image };
			}
		}

		for (size_t i = 0; i < textures.size(); ++i)
		{
			const double x = (i % 2) * 256.0;
			const double y = (i / 2) * 256.0;
			textures[i].draw(x, y);
		}
	}
}
```


## 59.11 Embedding を用いて類似する文章を検索する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/11.png)

```cpp
# include <Siv3D.hpp>

struct Text
{
	String text;

	Array<float> embedding;

	float cosineSimilarity = 0.0f;
};

bool Init(const String API_KEY, Array<Text>& texts)
{
	for (auto& text : texts)
	{
		String error;

		// OpenAI Embeddings API で文章の埋め込みベクトルを取得
		text.embedding = OpenAI::Embedding::Create(API_KEY, text.text, error);

		if (not text.embedding)
		{
			Print << error;

			return false;
		}
	}

	return true;
}

void Main()
{
	Window::Resize(1280, 720);

	Scene::SetBackground(ColorF{ 0.92 });

	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	Array<Text> texts =
	{
		{ U"公園。市街地などに設けられた公共施設としての庭園や遊園地。" },
		{ U"天気。ある場所の、ある時刻の気象状態。気温・湿度・風・雲量などを総合した状態。" },
		{ U"会議。関係者が集まって相談をし、物事を決定すること。" },
		{ U"水泳。スポーツや娯楽として水中を泳ぐこと。" },
		{ U"寿司。酢飯に生鮮魚介の切り身を乗せた料理。" },
		{ U"携帯電話。無線を用いて長距離通信のできる小型の移動電話。" },
		{ U"医者。病人の診察・治療を職業とする人。" },
		{ U"電車。駆動用電動機を装置し、架線あるいは軌道から得る電気を動力源として走行する鉄道車両。" },
		{ U"森林。樹木、特に高木が群生して大きな面積を占めている所。" },
		{ U"火曜日。月曜日と水曜日の間にある週の1日。" },
		{ U"大使館。特命全権大使が駐在国において公務を執行する公館。" },
		{ U"窃盗。人の物をぬすむこと。また、その人。" },
		{ U"地震。地球内部の急激な変動による振動が四方に伝わり大地が揺れる現象。" },
		{ U"手紙。用事などを記して、人に送る文書。" },
		{ U"睡眠。ねむること。ねむり。" },
	};

	AsyncTask initTask = Async(Init, String{ API_KEY }, std::ref(texts));

	TextEditState textEditState;

	float maxCosineSimilarity = 0.0f, minCosineSimilarity = 1.0f;

	AsyncHTTPTask task;

	while (System::Update())
	{
		if (initTask.isValid())
		{
			Circle{ Scene::Center(), 40 }.drawArc(Scene::Time() * 90_deg, 270_deg, 5);

			font(U"テキストの埋め込みベクトルを計算しています。事前に計算しておくことで実行時の処理を省略できます。").drawAt(22, Scene::Center().movedBy(0, 100), ColorF{ 0.11 });

			if (initTask.isReady())
			{
				if (not initTask.get())
				{
					Print << U"埋め込みベクトルの計算に失敗。";
				}
			}

			continue;
		}

		SimpleGUI::TextBox(textEditState, Vec2{ 40, 40 }, 1000);

		if (SimpleGUI::Button(U"検索", Vec2{ 1060, 40 }, 80, (not textEditState.text.isEmpty()) && (not task.isDownloading())))
		{
			task = OpenAI::Embedding::CreateAsync(API_KEY, textEditState.text);
		}

		if (task.isReady() && task.getResponse().isOK())
		{
			const Array<float> inputEmbedding = OpenAI::Embedding::GetVector(task.getAsJSON());

			maxCosineSimilarity = 0.0f; minCosineSimilarity = 1.0f;

			for (auto& text : texts)
			{
				text.cosineSimilarity = OpenAI::Embedding::CosineSimilarity(inputEmbedding, text.embedding);
				maxCosineSimilarity = Max(maxCosineSimilarity, text.cosineSimilarity);
				minCosineSimilarity = Min(minCosineSimilarity, text.cosineSimilarity);
			}
		}

		if (not task.isDownloading())
		{
			for (int32 i = 0; i < texts.size(); ++i)
			{
				const float cosineSimilarity = texts[i].cosineSimilarity;

				const Rect rect{ 40, (100 + i * 38), 1180, 36 };

				// 最も類似度が高いものを強調表示
				rect.draw((cosineSimilarity == maxCosineSimilarity) ? ColorF{ 1.0, 1.0, 0.75 } : ColorF{ 1.0 });

				// コサイン類似度を 0.0 ～ 1.0 に変換
				const double t = Math::Map(cosineSimilarity, minCosineSimilarity, maxCosineSimilarity, 0.0, 1.0);

				RectF{ rect.pos, (50 * t), rect.h }.stretched(0, -2).draw(Colormap01F(t, ColormapType::Turbo));

				// 文章とコサイン類似度を表示
				font(texts[i].text).draw(22, Arg::leftCenter = rect.leftCenter().movedBy(80, 0), ColorF{ 0.11 });
				font(cosineSimilarity).draw(18, Arg::leftCenter = rect.leftCenter().movedBy(1080, 0), ColorF{ 0.11 });
			}
		}
	}

	if (initTask.isValid())
	{
		initTask.wait();
	}
}
```
