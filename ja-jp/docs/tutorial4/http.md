# 62. HTTP クライアント
Web ページへのアクセスや、ファイルダウンロードなどの HTTP リクエストを行う方法を学びます。

## 62.1 URL
- URL を Siv3D のコードで表現するときは、`String` 型のエイリアス（別名）である `URL` 型を使うと意図が明確になります

```cpp
# include <Siv3D.hpp>

void Main()
{
	const URL url = U"https://example.com";

	while (System::Update())
	{

	}
}
```


## 62.2 Web ブラウザで URL を開く
- `System::LaunchBrowser(url)` は、指定した URL を Web ブラウザで開きます
- メインループの中でこの関数を繰り返し呼ぶと、大量にページが開かれるため、注意する必要があります

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Visit Website", Vec2{ 40, 40 }))
		{
			// Web ページをブラウザで開く
			System::LaunchBrowser(U"https://siv3d.github.io/ja-jp/");
		}
	}
}
```


## 62.3 Twitter の投稿画面を開く
- `Twitter::OpenTweetWindow(text)` は、指定したテキストを含むツイートを投稿するための Twitter（X）の投稿画面を開きます
- メインループの中でこの関数を繰り返し呼ぶと、大量にページが開かれるため、注意する必要があります
- Twitter API の性質上、自動で画像を添付することはできませんが、クリップボードに画像をコピー（**チュートリアル 65**）して、ユーザが画像の投稿をしやすくすることはできます

```cpp
# include <Siv3D.hpp>

void PostRsultTweet(int32 score)
{
	const String text = U"I got {} points in the game!\n#Siv3D\nhttps://siv3d.github.io/ja-jp/"_fmt(score);

	Twitter::OpenTweetWindow(text);
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Share Score", Vec2{ 40, 40 }))
		{
			PostRsultTweet(123);
		}
	}
}
```


## 62.4 ファイルダウンロード（同期）
- 指定した URL からファイルをダウンロードしたい場合は `SimpleHTTP::Save(url, saveFilePath)` を使うのが簡単です
- 戻り値の `HTTPResponse` を調べると、リクエストの結果を得られます。`.isOK()` が `true` であれば成功です

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Siv3D のロゴ
	const URL url = U"https://raw.githubusercontent.com/Siv3D/siv3d.docs.images/master/logo/logo.png";

	// 保存先のファイルパス
	const FilePath saveFilePath = U"logo.png";

	Texture texture;

	// ファイルを同期ダウンロード
	// ステータスコードが 200 (OK) なら
	if (SimpleHTTP::Save(url, saveFilePath).isOK())
	{
		texture = Texture{ saveFilePath };
	}
	else
	{
		Print << U"Failed";
	}

	while (System::Update())
	{
		if (texture)
		{
			texture.draw();
		}
	}
}
```


## 62.5 レスポンスの可視化
- 次のようなコードで、レスポンスのステータス行とヘッダーを可視化できます：

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Siv3D のロゴ
	const URL url = U"https://raw.githubusercontent.com/Siv3D/siv3d.docs.images/master/logo/logo.png";

	// 保存先のファイルパス
	const FilePath saveFilePath = U"logo.png";

	if (const auto response = SimpleHTTP::Save(url, saveFilePath))
	{
		Console << U"------";
		Console << response.getStatusLine().rtrimmed();
		Console << U"status code: " << FromEnum(response.getStatusCode());
		Console << U"------";
		Console << response.getHeader().rtrimmed();
		Console << U"------";
	}
	else
	{
		Print << U"Failed.";
	}

	Print << saveFilePath;
	const Texture texture{ saveFilePath };

	while (System::Update())
	{
		if (texture)
		{
			texture.draw();
		}
	}
}
```


## 62.6 ファイルダウンロード（非同期）
- ファイルのダウンロード中にメインスレッドの処理が止まるのを避けたい場合は、`SimpleHTTP::SaveAsync(url, saveFilePath)` を使います
- この関数は、ファイルの非同期ダウンロードタスクを開始し、`AsyncHTTPTask` 型のオブジェクトを返します
- このオブジェクトにタスクの完了を問い合わせ、タスクが完了していたらレスポンスを調べます
- 具体的には、`AsyncHTTPTask` の `.isReady()` が `true` になったら、`.getResponse()` でレスポンスを取得します
- レスポンスの取得以降は、`.isReady()` は `false` を返します
- タスクの進行中（ダウンロード中）は `.isDownloading()` が `true` を返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Siv3D のロゴ
	const URL url = U"https://raw.githubusercontent.com/Siv3D/siv3d.docs.images/master/logo/logo.png";

	// 保存先のファイルパス
	const FilePath saveFilePath = U"logo2.png";

	Texture texture;

	// ファイルの非同期ダウンロードを開始
	AsyncHTTPTask task = SimpleHTTP::SaveAsync(url, saveFilePath);

	while (System::Update())
	{
		// 非同期タスクが完了した
		if (task.isReady())
		{
			// レスポンスが 200 (OK) なら
			if (task.getResponse().isOK())
			{
				texture = Texture{ saveFilePath };
			}
			else
			{
				Print << U"Failed";
			}
		}

		// ダウンロード中の場合
		if (task.isDownloading())
		{
			// くるくる回る円を描く
			Circle{ 400, 300, 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}

		if (texture)
		{
			texture.draw();
		}
	}
}
```


## 62.7 ファイルダウンロード（非同期、進捗確認、キャンセル）
- `AsyncHTTPTask` オブジェクトに、ダウンロードの進捗を問い合わせたい場合、`.getProgress()` を使うと `HTTPProgress` 型で進捗を取得できます
- ダウンロードのタスクを取り消したい場合は、`AsyncHTTPTask` の `.cancel()` を呼びます
- `HTTPProgress` は次のようなメンバ変数を持ちます

| コード | 説明 |
|---|---|
| `HTTPAsyncStatus status` | 進行状況 |
| `int64 downloaded_bytes` | ダウンロードしたサイズ（バイト） |
| `int64 uploaded_bytes` | アップロードしたサイズ（バイト） |
| `Optional<int64> download_total_bytes` | ダウンロードするファイルの合計サイズ（バイト）。不明な場合 none |
| `Optional<int64> upload_total_bytes` | アップロードするファイルの合計サイズ（バイト）。不明な場合 none |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/http/7.png)

```cpp
# include <Siv3D.hpp>

String ToString(HTTPAsyncStatus status)
{
	switch (status)
	{
	case HTTPAsyncStatus::None_:
		return U"None_";
	case HTTPAsyncStatus::Downloading:
		return U"Downloading";
	case HTTPAsyncStatus::Failed:
		return U"Failed";
	case HTTPAsyncStatus::Canceled:
		return U"Canceled";
	case HTTPAsyncStatus::Succeeded:
		return U"Succeeded";
	default:
		return U"Unknown";
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// HTTP 通信のテスト用サービス (https://httpbin.org) を利用
	// 1024 バイトのデータを 4 秒かけてダウンロードする URL
	const URL url = U"https://httpbin.org/drip?duration=4&numbytes=1024&code=200&delay=0";

	// 保存先のファイルパス
	const FilePath saveFilePath = U"drip.txt";

	AsyncHTTPTask task;

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Download", Vec2{ 20, 20 }, 140, task.isEmpty()))
		{
			task = SimpleHTTP::SaveAsync(url, saveFilePath);
		}

		if (SimpleGUI::Button(U"Cancel", Vec2{ 180, 20 }, 140, (task.getStatus() == HTTPAsyncStatus::Downloading)))
		{
			// タスクをキャンセルする
			task.cancel();
		}

		// タスクの進捗
		const HTTPProgress progress = task.getProgress();

		font(U"status: {}"_fmt(ToString(progress.status))).draw(24, Vec2{ 20, 60 }, ColorF{ 0.1 });

		if (progress.status == HTTPAsyncStatus::Downloading)
		{
			// ダウンロード済みのサイズ（バイト）
			const int64 downloaded = progress.downloaded_bytes;

			// ダウンロードするファイルのサイズ（バイト）。取得できない場合は none
			if (const Optional<int64> total = progress.download_total_bytes)
			{
				font(U"downloaded: {} bytes / {} bytes"_fmt(downloaded, *total)).draw(24, Vec2{ 20, 100 }, ColorF{ 0.1 });

				const double progress0_1 = (static_cast<double>(downloaded) / *total);
				const RectF rect{ 20, 140, 500, 30 };
				rect.drawFrame(2, 0, ColorF{ 0.1 });
				RectF{ rect.pos, (rect.w * progress0_1), rect.h }.draw(ColorF{ 0.1 });
			}
			else
			{
				font(U"downloaded: {} bytes"_fmt(downloaded)).draw(24, Vec2{ 20, 100 }, ColorF{ 0.1 });
			}
		}

		if (task.isReady())
		{
			if (const auto& response = task.getResponse())
			{
				Console << U"------";
				Console << response.getStatusLine().rtrimmed();
				Console << U"status code: " << FromEnum(response.getStatusCode());
				Console << U"------";
				Console << response.getHeader().rtrimmed();
				Console << U"------";

				if (response.isOK())
				{
					Console << FileSystem::FileSize(saveFilePath) << U" bytes";
					Console << U"------";
				}
			}
			else
			{
				Print << U"Failed.";
			}
		}
		
		// ダウンロード中の場合
		if (task.isDownloading())
		{
			// くるくる回る円を描く
			Circle{ 400, 300, 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}
	}
}
```


## 62.8 GET（同期）
- GET リクエストのサンプルです

```cpp
# include <Siv3D.hpp>

void Main()
{
	const URL url = U"https://httpbin.org/bearer";
	const HashTable<String, String> headers = { { U"Authorization", U"Bearer TOKEN123456abcdef" } };

	// 保存先のファイルパス
	const FilePath saveFilePath = U"auth_result.json";

	if (SimpleHTTP::Get(url, headers, saveFilePath).isOK())
	{
		const JSON json = JSON::Load(saveFilePath);
		Print << U"authenticated: " << json[U"authenticated"].get<bool>();
		Print << U"token: " << json[U"token"].getString();
	}
	else
	{
		Print << U"Failed";
	}

	while (System::Update())
	{

	}
}
```


## 62.9 GET（非同期）
- GET リクエストの非同期版です

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const URL url = U"https://httpbin.org/bearer";
	const HashTable<String, String> headers = { { U"Authorization", U"Bearer TOKEN123456abcdef" } };

	// 保存先のファイルパス
	const FilePath saveFilePath = U"auth_result.json";

	AsyncHTTPTask task = SimpleHTTP::GetAsync(url, headers, saveFilePath);

	while (System::Update())
	{
		if (task.isReady())
		{
			if (task.getResponse().isOK())
			{
				const JSON json = JSON::Load(saveFilePath);
				Print << U"authenticated: " << json[U"authenticated"].get<bool>();
				Print << U"token: " << json[U"token"].getString();
			}
			else
			{
				Print << U"Failed.";
			}
		}
		
		// ダウンロード中の場合
		if (task.isDownloading())
		{
			// くるくる回る円を描く
			Circle{ 400, 300, 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}
	}
}
```


## 62.10 POST（同期）
- POST リクエストのサンプルです

```cpp
# include <Siv3D.hpp>

void Main()
{
	const URL url = U"https://httpbin.org/post";
	const HashTable<String, String> headers = { { U"Content-Type", U"application/json" } };
	const std::string data = JSON
	{
		{ U"body", U"Hello, Siv3D!" },
		{ U"date", DateTime::Now().format() },
	}.formatUTF8();

	// 保存先のファイルパス
	const FilePath saveFilePath = U"post_result.json";

	if (SimpleHTTP::Post(url, headers, data.data(), data.size(), saveFilePath).isOK())
	{
		const JSON json = JSON::Load(saveFilePath);
		Print << json.format();
	}
	else
	{
		Print << U"Failed";
	}

	while (System::Update())
	{

	}
}
```


## 62.11 POST（非同期）
- POST リクエストの非同期版です

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const URL url = U"https://httpbin.org/post";
	const HashTable<String, String> headers = { { U"Content-Type", U"application/json" } };
	const std::string data = JSON
	{
		{ U"body", U"Hello, Siv3D!" },
		{ U"date", DateTime::Now().format() },
	}.formatUTF8();

	// 保存先のファイルパス
	const FilePath saveFilePath = U"post_result.json";

	AsyncHTTPTask task = SimpleHTTP::PostAsync(url, headers, data.data(), data.size(), saveFilePath);

	while (System::Update())
	{
		if (task.isReady())
		{
			if (task.getResponse().isOK())
			{
				const JSON json = JSON::Load(saveFilePath);
				Print << json.format();
			}
			else
			{
				Print << U"Failed";
			}
		}
		
		// ダウンロード中の場合
		if (task.isDownloading())
		{
			// くるくる回る円を描く
			Circle{ 400, 300, 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}
	}
}
```
