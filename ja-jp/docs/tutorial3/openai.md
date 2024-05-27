# 59. OpenAI API
OpenAI API と連携する生成 AI の機能を学びます。

## 59.1 OpenAI API の概要

### 59.1.1 OpenAI API とは
OpenAI API は、OpenAI が提供する生成 AI モデルを利用するための API です。OpenAI API を利用することで、生成 AI モデルを簡単に利用することができます。

### 59.1.2 利用の流れ
OpenAI API の利用は、次のような流れになります。

1. データ + API キーからなるリクエストを OpenAI サーバに送る。
2. OpenAI サーバが JSON で結果を返答する（内容によっては時間がかかる）
3. 返された JSON から必要な部分を抽出する

Siv3D では `OpenAI::～` に用意された関数を使うことで、一連の処理を簡単にプログラムできます。

### 59.1.3 Siv3D で利用できる OpenAI API

- **Chat:** 一連の会話に続くメッセージを回答する
- **Image:** プロンプトに基づいた画像を生成する
- **Embedding:** 単語や文章を、埋め込みベクトルに変換する
- **Vision:** 画像に関する質問に回答する
- **Speech:** テキストを音声に変換する

### 59.1.4 OpenAI API の利用料金
OpenAI が返答するとき、入力や出力の長さ（トークン数）に応じて、API の利用料金が発生します。詳しくは [OpenAI | Pricing :material-open-in-new:](https://openai.com/pricing){:target="_blank"} を参照してください。

API の利用料金が高額になることが心配な場合は、OpenAI アカウントのダッシュボードから Usage limits（毎月の上限）を設定できます。


## 59.2 準備 | OpenAI API キーの発行と管理

### 59.2.1 OpenAI API キーの発行
OpenAI の API キーは、"sk-" から始まる数十文字の文字列です。

OpenAI アカウントにサインインし、[https://platform.openai.com/api-keys :material-open-in-new:](https://platform.openai.com/api-keys){:target="_blank"} の「Create new secret key」から OpenAI API キーを発行できます（一度しか表示されません）。

### 59.2.2 API キーを安全に保存する
コードをコミット・公開したときに API キーが流出しないよう、開発中は API キーを環境変数に保存し、環境変数を読み取るコードで API キーを取得することが望ましいです。

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
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/t1.png)

`OpenAI::Chat::Complete()` は、OpenAI の Chat API を利用して、一連の会話に続く回答を取得する関数です。OpenAI からのレスポンスが返ってくるまで関数は制御を返さない（待ちが発生する）点に注意します。待っている間に別のことをしたい場合は 59.5 で扱う非同期版の関数を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 環境変数から API キーを取得する
	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	// プロンプト
	const String prompt = U"ファンタジーゲームの定番の敵キャラクターを 3 つ挙げて。";

	// 回答を String で得る
	const String answer = OpenAI::Chat::Complete(API_KEY, prompt);

	// 出力
	Print << answer;

	while (System::Update())
	{

	}
}
```


## 59.4 テキストボックスからの入力を Chat に送る
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/t2.png)


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
			// プロンプト
			const String prompt = textEditState.text;

			// 回答文
			answer = OpenAI::Chat::Complete(API_KEY, prompt);
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

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/t4.png)

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

			// プロンプト
			const String prompt = textEditState.text;

			// タスクを作成する
			task = OpenAI::Chat::CompleteAsync(API_KEY, prompt);
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

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/t3.png)

あらかじめ用意したプロンプトテンプレートに、ユーザーが入力したテキストを組み合わせることで、短い入力から回答を得ることができます。

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

			// プロンプト
			const String prompt = (U"RPG ゲームで" + textEditState.text + U"に登場する敵モンスターを 1 種類考えてください。");

			// タスクを作成する
			task = OpenAI::Chat::CompleteAsync(API_KEY, prompt);
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

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/t5.png)

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

			// プロンプト
			String prompt = (U"RPG ゲームで" + textEditState.text + U"に登場する敵モンスターを 1 種類考えてください。\n");
			prompt += U"出力は次のような JSON 形式で、日本語で出力してください。回答に JSON データ以外を含まないで下さい。\n";
			prompt += UR"({ "name": "敵の名前", "desc" : "説明" })"; // UR"()" は生文字列リテラル https://cpprefjp.github.io/lang/cpp11/raw_string_literals.html

			// タスクを作成する
			task = OpenAI::Chat::CompleteAsync(API_KEY, prompt);
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
ChatGPT API はリクエストごとに記憶はリセットされます。コンテキストを保持したまま連続した会話を行う場合は、ロールと履歴からなる一連の会話履歴を送信する必要があります。

| ロール | 説明 |
| --- | --- |
| `Rolu::System` | AI の監督者 |
| `Role::User` | 利用者 |
| `Role::Assistant` | AI |


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/t6.png)


```cpp
# include <Siv3D.hpp>

void Main()
{
	// 環境変数から API キーを取得する
	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	OpenAI::Chat::Request request;

	// 会話プロンプト（ロールとメッセージのペアの配列）を構築する
	request.messages.emplace_back(OpenAI::Chat::Role::System,
		U"あなたは 90% 猫、10% ChatGPT の「CatGPT」としてふるまいなさい。語尾に「ニャン」を付けなさい。");

	request.messages.emplace_back(OpenAI::Chat::Role::User,
		U"あなたの名前は？");

	request.messages.emplace_back(OpenAI::Chat::Role::Assistant,
		U"私は CatGPT だニャン");

	request.messages.emplace_back(OpenAI::Chat::Role::User,
		U"どのように過ごしていますか？"); // 最後は user で終わる必要がある

	// 回答を String で得る
	const String answer = OpenAI::Chat::Complete(API_KEY, request);

	// 出力
	Print << answer;

	while (System::Update())
	{

	}
}
```


## 59.9 プロンプトから画像を生成する（同期）

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/t7.png)



```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(Size{ 1792, 1024 } / 2);

	// 環境変数から API キーを取得する
	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	// リクエスト内容
	OpenAI::Image::RequestDALLE3 request;
	request.prompt = U"A peaceful fantasy landscape with rolling meadows leading to a range of majestic mountains in the distance. The scene is serene, with lush green grass, colorful wildflowers, and a clear blue sky. In the foreground, there are gentle hills, and a sparkling river winding through the meadow. The mountains are snow-capped and majestic, with forests at their base. The overall atmosphere is calm and enchanting, evoking a sense of wonder and tranquility.";
	request.imageSize = OpenAI::Image::RequestDALLE3::ImageSize1792x1024;

	const Texture texture{ OpenAI::Image::Create(API_KEY, request) };

	while (System::Update())
	{
		texture.scaled(0.5).draw();
	}
}
```


## 59.10 プロンプトから画像を生成する（非同期）

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/t8.png)



```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(Size{ 1792, 1024 } / 2);

	// 環境変数から API キーを取得する
	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	// リクエスト内容
	OpenAI::Image::RequestDALLE3 request;
	request.prompt = U"A dramatic scene of Mount Fuji erupting with lava and ash clouds, with volcanic ash falling over Tokyo. The sky is dark and filled with ash, creating an apocalyptic atmosphere. Tokyo's skyline is visible in the distance, covered in a layer of ash. The overall mood is intense and somber.";
	request.imageSize = OpenAI::Image::RequestDALLE3::ImageSize1792x1024;

	AsyncTask<Image> task = OpenAI::Image::CreateAsync(API_KEY, request);

	Texture texture;

	while (System::Update())
	{
		if (task.isValid())
		{
			Circle{ Scene::Center(), 50 }.drawArc(Scene::Time() * 120_deg, 300_deg, 4, 4);
		}

		if (task.isReady())
		{
			texture = Texture{ task.get() };
		}

		if (texture)
		{
			texture.scaled(0.5).draw();
		}
	}
}
```


## 59.11 Embedding を用いて類似する文章を検索する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/t9.png)


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

	// 環境変数から API キーを取得する
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


## 59.12 Vision を用いて画像に関する質問に回答する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/t10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// 環境変数から API キーを取得する
	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	const Texture texture{ U"example/windmill.png" };

	OpenAI::Vision::Request request;
	request.images << OpenAI::Vision::ImageData::Base64FromFile(U"example/windmill.png");
	request.questions = U"何が写っていますか？";

	// 非同期タスク
	AsyncHTTPTask task = OpenAI::Vision::CompleteAsync(API_KEY, request);

	// 回答を格納する変数
	String answer;

	while (System::Update())
	{
		// ChatGPT の応答を待つ間はローディング画面を表示する
		if (task.isDownloading())
		{
			Circle{ Scene::Center(), 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}

		// 非同期処理が完了し、正常なレスポンスである場合
		if (task.isReady() && task.getResponse().isOK())
		{
			// 非同期処理の結果を取得する
			answer = OpenAI::Vision::GetContent(task.getAsJSON());
		}

		texture.fitted(Size{ 400, 300 }).draw(40, 40);

		// 回答がある場合
		if (answer)
		{
			font(answer).draw(20, Rect{ 40, 340, 1200, 240 }, ColorF{ 0.25 });
		}
	}
}
```

## 59.13 Speech を用いてテキストを音声に変換する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/openai/t11.png)


```cpp
# include <Siv3D.hpp>

class Task
{
public:

	enum class State
	{
		Running,
		Failed,
		Completed,
	};

	Task() = default;

	void update()
	{
		if (m_isReady)
		{
			return;
		}

		if (m_task.isReady())
		{
			m_isReady = true;
			m_isFailed = (not m_task.get());
			m_completeTime = Time::GetMillisec();
		}
	}

	void play()
	{
		if (not m_isReady)
		{
			return;
		}

		if (not m_audio)
		{
			m_audio = Audio{ Audio::Stream, m_path };
		}

		m_audio.play();
	}

	const String& getText() const
	{
		return m_request.input;
	}

	const Audio& getAudio() const
	{
		return m_audio;
	}

	State getState() const
	{
		if (not m_isReady)
		{
			return State::Running;
		}
		else if (m_isFailed)
		{
			return State::Failed;
		}
		else
		{
			return State::Completed;
		}
	}

	static Task Create(const StringView apiKey, const OpenAI::Speech::Request& request, const FilePathView saveDirectory)
	{
		Task task;
		task.m_request = request;
		task.m_path = FileSystem::PathAppend(saveDirectory, U"{}.{}"_fmt(UUIDValue::Generate().str(), request.responseFormat));
		task.m_task = OpenAI::Speech::CreateAsync(apiKey, request, task.m_path);
		task.m_startTime = Time::GetMillisec();
		return task;
	}

	double getTime() const
	{
		if (not m_isReady)
		{
			return 0.0;
		}
		else
		{
			return (m_completeTime - m_startTime) / 1000.0;
		}
	}

private:

	OpenAI::Speech::Request m_request;

	FilePath m_path;

	AsyncTask<bool> m_task;

	uint64 m_startTime = 0;

	uint64 m_completeTime = 0;

	Audio m_audio;

	bool m_isReady = false;

	bool m_isFailed = false;
};

void DrawTaskShadow(int32 taskIndex)
{
	const Rect rect{ 40, (40 + taskIndex * 60), 1200, 56 };
	rect.drawShadow({ 0, 2 }, 6, 0.0, ColorF{ 0.0, 0.5 }, false);
}

void DrawTask(int32 taskIndex, Task& task, const Font& font)
{
	const double fontSize = 20.0;
	const Rect rect{ 40, (40 + taskIndex * 60), 1200, 56 };

	rect.draw();
	{
		const auto text = font(task.getText());
		const double textWidth = text.region(fontSize).w;
		const double overWidth = (textWidth - 846.0 + 100);
		const Audio& audio = task.getAudio();
		const Rect textRect = Rect{ rect.pos.movedBy(54, 14), 846, 30 };
		const ScopedViewport2D viewport{ textRect };

		if (0 < overWidth)
		{
			const double audioProgress = (audio.posSec() / audio.lengthSec());
			const double xOffset = (overWidth * audioProgress);
			text.draw(fontSize, Vec2{ 10 - xOffset, 0 }, ColorF{ 0.11 });
		}
		else
		{
			text.draw(fontSize, Vec2{ 10, 0 }, ColorF{ 0.11 });
		}
	}

	Rect{ rect.pos.movedBy(900, 00), 300, 56 }.draw(Arg::left(0.8, 0.9, 1.0), Arg::right(0.9, 0.95, 1.0));

	if (task.getState() == Task::State::Completed)
	{
		Rect{ rect.pos, 50, rect.h }.draw(ColorF{ 0.2, 0.7, 0.5 });
		font(U"#{}"_fmt(taskIndex)).draw(fontSize, Arg::leftCenter(rect.pos.movedBy(12, 28)), ColorF{ 1.0 });

		if (SimpleGUI::Button(U"\U000F040A", rect.pos.movedBy(920, 10)))
		{
			task.play();
		}

		font(U"生成時間 {:.2f} 秒"_fmt(task.getTime())).draw(fontSize, Arg::rightCenter(rect.pos.movedBy(1180, 28)), ColorF{ 0.1, 0.2, 0.5 });
	}
	else
	{
		font(U"#{}"_fmt(taskIndex)).draw(fontSize, Arg::leftCenter(rect.pos.movedBy(12, 28)), ColorF{ 0.11 });
	}
}

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Medium };

	// 環境変数から API キーを取得する
	const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

	Array<Task> tasks;

	TextAreaEditState textAreaEditState;
	const Array<String> voices = {
		U"Alloy", U"Echo", U"Fable", U"Onyx", U"Nova", U"Shimmer",
	};
	size_t voiceIndex = 0;
	size_t qualityIndex = 0;

	size_t randomTextIndex = 0;

	const Array<String> randomTexts = {
		U"The transcriptions API takes as input the audio file you want to transcribe and the desired output file format for the transcription of the audio. We currently support multiple input and output file formats.",
		U"Please note that our Usage Policies require you to provide a clear disclosure to end users that the TTS voice they are hearing is AI-generated and not a human voice.",
		U"Siv3D には 2D, 3D ゲーム、メディアアート、ビジュアライザ、シミュレータを効率的に開発するための、便利なクラスや関数が用意されています。",
		U"Siv3D の API とサンプルは、最新の C++ 規格「C++20」で書かれています。Siv3D を使っているだけで、現代的な C++ の書き方が身に付きます。",
	};

	while (System::Update())
	{
		for (auto& task : tasks)
		{
			task.update();
		}

		for (int32 i = 0; auto & task : tasks)
		{
			DrawTaskShadow(i++);
		}

		for (int32 i = 0; auto & task : tasks)
		{
			DrawTask(i++, task, font);
		}

		SimpleGUI::TextArea(textAreaEditState, Vec2{ 40, 560 }, SizeF{ 1000, 100 });

		if (SimpleGUI::Button(U"Random", Vec2{ 1060, 600 }, 140))
		{
			textAreaEditState = TextAreaEditState{ randomTexts[randomTextIndex] };
			++randomTextIndex %= randomTexts.size();
		}

		SimpleGUI::HorizontalRadioButtons(voiceIndex, voices, Vec2{ 40, 520 });

		SimpleGUI::HorizontalRadioButtons(qualityIndex, { U"速度", U"品質" }, Vec2{ 840, 520 });

		if (SimpleGUI::Button(U"Generate", Vec2{ 1060, 520 }, 140, (not textAreaEditState.text.isEmpty())))
		{
			OpenAI::Speech::Request request;
			request.model = (qualityIndex == 0) ? OpenAI::Speech::Model::TTS1 : OpenAI::Speech::Model::TTS1HD;
			request.input = textAreaEditState.text;
			request.voice = voices[voiceIndex].lowercased();
			tasks << Task::Create(API_KEY, request, U"speech/");

			textAreaEditState.clear();
		}
	}
}
```
