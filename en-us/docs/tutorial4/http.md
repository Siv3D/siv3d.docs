# 62. HTTP Client
Learn how to make HTTP requests such as accessing web pages and downloading files.

## 62.1 URL
- When representing URLs in Siv3D code, using the `URL` type, which is an alias for the `String` type, makes the intent clearer

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


## 62.2 Checking Internet Connection
- `Network::IsConnected()` returns a `bool` indicating whether an internet connection is established

```cpp
# include <Siv3D.hpp>

void Main()
{
	if (Network::IsConnected())
	{
		Print << U"Connected";
	}
	else
	{
		Print << U"Not connected";
	}

	while (System::Update())
	{
		
	}
}
```


## 62.3 Opening URLs in Web Browser
- `System::LaunchBrowser(url)` opens the specified URL in a web browser
- Be careful when calling this function repeatedly in the main loop, as it will open a large number of pages

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Visit Website", Vec2{ 40, 40 }))
		{
			// Open web page in browser
			System::LaunchBrowser(U"https://siv3d.github.io/ja-jp/");
		}
	}
}
```


## 62.4 Opening Twitter Post Screen
- `Twitter::OpenTweetWindow(text)` opens a Twitter (X) post screen for posting a tweet with the specified text
- Be careful when calling this function repeatedly in the main loop, as it will open a large number of pages
- Due to the nature of the Twitter API, images cannot be automatically attached, but you can copy images to the clipboard (**Tutorial 65**) to make it easier for users to post images

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


## 62.5 File Download (Synchronous)
- To download a file from a specified URL, the simple way is to use `SimpleHTTP::Save(url, saveFilePath)`
- You can check the request result by examining the returned `HTTPResponse`. If `.isOK()` is `true`, it was successful

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Siv3D logo
	const URL url = U"https://raw.githubusercontent.com/Siv3D/siv3d.docs.images/master/logo/logo.png";

	// Save destination file path
	const FilePath saveFilePath = U"logo.png";

	Texture texture;

	// Download file synchronously
	// If status code is 200 (OK)
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


## 62.6 Response Visualization
- You can visualize the response status line and headers with code like this:

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Siv3D logo
	const URL url = U"https://raw.githubusercontent.com/Siv3D/siv3d.docs.images/master/logo/logo.png";

	// Save destination file path
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


## 62.7 File Download (Asynchronous)
- To avoid blocking the main thread during file download, use `SimpleHTTP::SaveAsync(url, saveFilePath)`
- This function starts an asynchronous file download task and returns an `AsyncHTTPTask` type object
- Query this object for task completion, and when the task is complete, examine the response
- Specifically, when `AsyncHTTPTask`'s `.isReady()` returns `true`, get the response with `.getResponse()`
- After getting the response, `.isReady()` returns `false`
- While the task is in progress (downloading), `.isDownloading()` returns `true`

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Siv3D logo
	const URL url = U"https://raw.githubusercontent.com/Siv3D/siv3d.docs.images/master/logo/logo.png";

	// Save destination file path
	const FilePath saveFilePath = U"logo2.png";

	Texture texture;

	// Start asynchronous file download
	AsyncHTTPTask task = SimpleHTTP::SaveAsync(url, saveFilePath);

	while (System::Update())
	{
		// Asynchronous task completed
		if (task.isReady())
		{
			// If response is 200 (OK)
			if (task.getResponse().isOK())
			{
				texture = Texture{ saveFilePath };
			}
			else
			{
				Print << U"Failed";
			}
		}

		// If downloading
		if (task.isDownloading())
		{
			// Draw spinning circle
			Circle{ 400, 300, 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}

		if (texture)
		{
			texture.draw();
		}
	}
}
```


## 62.8 File Download (Asynchronous, Progress Check, Cancel)
- To query download progress from an `AsyncHTTPTask` object, use `.getProgress()` to get progress as `HTTPProgress` type
- To cancel a download task, call `.cancel()` on the `AsyncHTTPTask`
- `HTTPProgress` has the following member variables:

| Code | Description |
|---|---|
| `HTTPAsyncStatus status` | Progress status |
| `int64 downloaded_bytes` | Downloaded size (bytes) |
| `int64 uploaded_bytes` | Uploaded size (bytes) |
| `Optional<int64> download_total_bytes` | Total size of file to download (bytes). none if unknown |
| `Optional<int64> upload_total_bytes` | Total size of file to upload (bytes). none if unknown |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/http/8.png)

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

	// Using HTTP communication test service (https://httpbin.org)
	// URL that downloads 1024 bytes of data over 4 seconds
	const URL url = U"https://httpbin.org/drip?duration=4&numbytes=1024&code=200&delay=0";

	// Save destination file path
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
			// Cancel the task
			task.cancel();
		}

		// Task progress
		const HTTPProgress progress = task.getProgress();

		font(U"status: {}"_fmt(ToString(progress.status))).draw(24, Vec2{ 20, 60 }, ColorF{ 0.1 });

		if (progress.status == HTTPAsyncStatus::Downloading)
		{
			// Downloaded size (bytes)
			const int64 downloaded = progress.downloaded_bytes;

			// File size to download (bytes). none if not available
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
		
		// If downloading
		if (task.isDownloading())
		{
			// Draw spinning circle
			Circle{ 400, 300, 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}
	}
}
```


## 62.9 GET (Synchronous)
- Sample GET request

```cpp
# include <Siv3D.hpp>

void Main()
{
	const URL url = U"https://httpbin.org/bearer";
	const HashTable<String, String> headers = { { U"Authorization", U"Bearer TOKEN123456abcdef" } };

	// Save destination file path
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


## 62.10 GET (Asynchronous)
- Asynchronous version of GET request

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const URL url = U"https://httpbin.org/bearer";
	const HashTable<String, String> headers = { { U"Authorization", U"Bearer TOKEN123456abcdef" } };

	// Save destination file path
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
		
		// If downloading
		if (task.isDownloading())
		{
			// Draw spinning circle
			Circle{ 400, 300, 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}
	}
}
```


## 62.11 POST (Synchronous)
- Sample POST request

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

	// Save destination file path
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


## 62.12 POST (Asynchronous)
- Asynchronous version of POST request

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

	// Save destination file path
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
		
		// If downloading
		if (task.isDownloading())
		{
			// Draw spinning circle
			Circle{ 400, 300, 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}
	}
}
```