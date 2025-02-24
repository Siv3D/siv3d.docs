# 71. Web カメラ
パソコンに内蔵・接続された Web カメラから映像を取得し、プログラムで活用する方法を学びます。

## 71.1 接続されている Web カメラを列挙する
PC に接続されている Web カメラの一覧を `System::EnumerateWebcams()` で取得できます。結果は `Array<WebcamInfo>` 型で返されます。

`WebcamInfo` 型のメンバ変数は次の通りです。

| メンバ変数 | 説明 |
|--|--|
| `uint32 cameraIndex` | `Webcam` で使うデバイスインデックス |
| `String name` | 名前 |
| `String uniqueName` | ユニークな名前 |

```cpp
# include <Siv3D.hpp>

void Main()
{
	for (const auto& info : System::EnumerateWebcams())
	{
		Print << info.cameraIndex;
		Print << info.name;
		Print << info.uniqueName;
	}

	while (System::Update())
	{

	}
}
```


## 71.2 Web カメラの映像を取得する（メインスレッドで Web カメラを初期化）
`Webcam` クラスを通して Web カメラをメインスレッドで初期化し、撮影したフレームを `DyanmicTexture` に反映させて表示します。カメラの初期化には数秒以上かかることがあります。

:::message
macOS では初回の `Webcam` 作成時に、ユーザにカメラの使用権限を求めるダイアログを表示しつつ、`Webcam` の作成に失敗するため、再作成できる手段を用意する必要があります。
:::

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// デバイスインデックス 0 の Web カメラ、1280x720 かそれに近いサイズをリクエスト、即座に撮影開始
	Webcam webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes };

	DynamicTexture texture;

	while (System::Update())
	{
		// macOS では、ユーザがカメラ使用の権限を許可しないと Webcam の作成に失敗するため、再試行の手段を用意する
	# if SIV3D_PLATFORM(MACOS)
		if (not webcam)
		{
			if (SimpleGUI::Button(U"Retry", Vec2{ 20, 20 }))
			{
				webcam = Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes };
			}
		}
	# endif

		// 新しい画像が撮影された
		if (webcam.hasNewFrame())
		{
			// DynamicTexture 画像に転送
			webcam.getFrame(texture);
		}

		if (texture)
		{
			texture.draw();
		}
	}
}
```

## 71.3 Web カメラの映像を取得する（非同期で Web カメラを初期化）
カメラの初期化が完了するまでメインスレッドが停止することを避けるため、非同期で Web カメラを起動するサンプルです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 非同期タスクを開始
	AsyncTask<Webcam> task{ []() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };

	Webcam webcam;
	DynamicTexture texture;

	while (System::Update())
	{
		// macOS では、ユーザがカメラ使用の権限を許可しないと Webcam の作成に失敗する。再試行の手段を用意する
	# if SIV3D_PLATFORM(MACOS)
		if ((not webcam) && (not task.valid()))
		{
			if (SimpleGUI::Button(U"Retry", Vec2{ 20, 20 }))
			{
				task = AsyncTask{ []() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };
			}
		}
	# endif
		
		if (task.isReady())
		{
			// 起動が完了した Webcam をタスクから取得
			webcam = task.get();

			if (webcam)
			{
				Print << webcam.getResolution();
			}
		}

		if (webcam.hasNewFrame())
		{
			webcam.getFrame(texture);
		}

		// Webcam 作成待機中は円を表示
		if (not webcam)
		{
			Circle{ Scene::Center(), 40 }
				.drawArc(Scene::Time() * 180_deg, 300_deg, 5, 5);
		}

		if (texture)
		{
			texture.draw();
		}
	}
}
```


## 71.4 Web カメラの映像を画像処理して表示する
撮影した画像は `Image` として取得することもできます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 非同期タスクを開始
	AsyncTask<Webcam> task{ []() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };

	Webcam webcam;
	Image image;
	DynamicTexture texture;

	while (System::Update())
	{
		// macOS では、ユーザがカメラ使用の権限を許可しないと Webcam の作成に失敗する。再試行の手段を用意する
	# if SIV3D_PLATFORM(MACOS)
		if ((not webcam) && (not task.valid()))
		{
			if (SimpleGUI::Button(U"Retry", Vec2{ 20, 20 }))
			{
				task = AsyncTask{ []() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };
			}
		}
	# endif
		
		if (task.isReady())
		{
			// 起動が完了した Webcam をタスクから取得
			webcam = task.get();

			if (webcam)
			{
				Print << webcam.getResolution();
			}
		}

		if (webcam.hasNewFrame())
		{
			webcam.getFrame(image);

			// グレイスケール化
			image.grayscale();

			texture.fill(image);
		}

		// Webcam 作成待機中は円を表示
		if (not webcam)
		{
			Circle{ Scene::Center(), 40 }
				.drawArc(Scene::Time() * 180_deg, 300_deg, 5, 5);
		}

		if (texture)
		{
			texture.draw();
		}
	}
}
```


## 71.5 Web カメラの映像から顔を検出する
- Haar-Like 特徴量を用いた分類器 `CascadeClassifier` クラスを使うと、与えられた画像から特定のオブジェクトを検出できます
- 正面を向いた顔の検出には、`example/objdetect/haarcascade/frontal_face_alt2.xml` をモデルデータとして使用します
- Haar-Like 特徴量による顔検出は古典的な手法であり、深層学習ベースの手法と比べると検出精度はそこまで高くないことに注意してください

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 非同期タスクを開始
	AsyncTask<Webcam> task{ []() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };

	Webcam webcam;
	Image image;
	DynamicTexture texture;

	// 検出器。正面を向いた顔のモデルデータを使用して分類
	const CascadeClassifier faceDetector{ U"example/objdetect/haarcascade/frontal_face_alt2.xml" };

	// 検出された領域
	Array<Rect> rects;

	while (System::Update())
	{
		// macOS では、ユーザがカメラ使用の権限を許可しないと Webcam の作成に失敗する。再試行の手段を用意する
	# if SIV3D_PLATFORM(MACOS)
		if ((not webcam) && (not task.valid()))
		{
			if (SimpleGUI::Button(U"Retry", Vec2{ 20, 20 }))
			{
				task = AsyncTask{ []() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };
			}
		}
	# endif
		
		if (task.isReady())
		{
			// 起動が完了した Webcam をタスクから取得
			webcam = task.get();

			if (webcam)
			{
				Print << webcam.getResolution();
			}
		}

		if (webcam.hasNewFrame())
		{
			webcam.getFrame(image);

			// 画像から顔を検出。厳密さ: 3, 最小領域サイズ:　100x100
			rects = faceDetector.detectObjects(image, 3, Size{ 100, 100 });

			// 領域にモザイクエフェクトをかける
			for (const auto& rect : rects)
			{
				image(rect).mosaic(20);
			}

			texture.fill(image);
		}

		// Webcam 作成待機中は円を表示
		if (not webcam)
		{
			Circle{ Scene::Center(), 40 }
				.drawArc(Scene::Time() * 180_deg, 300_deg, 5, 5);
		}

		if (texture)
		{
			texture.draw();
		}

		for (const auto& rect : rects)
		{
			rect.drawFrame(4, Palette::Red);
		}
	}
}
```


## 71.6 Web カメラの映像から QR コードを検出する
`Image` から QR コードを読み取るには `QRScanner` クラスを使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 最大サイズ (Version 40) の QR コードの場合 1920x1080 が必要かもしれない。
	constexpr Size cameraResolution{ 1280, 720 };

	Window::Resize(cameraResolution);

	// 非同期タスクを開始
	AsyncTask<Webcam> task{ [=]() { return Webcam{ 0, cameraResolution, StartImmediately::Yes }; } };

	Webcam webcam;
	Image image;
	DynamicTexture texture;

	// QR コードスキャナ
	QRScanner qrScanner;

	// QR コードの内容
	Array<QRContent> contents;

	while (System::Update())
	{
		// macOS では、ユーザがカメラ使用の権限を許可しないと Webcam の作成に失敗する。再試行の手段を用意する
	# if SIV3D_PLATFORM(MACOS)
		if ((not webcam) && (not task.valid()))
		{
			if (SimpleGUI::Button(U"Retry", Vec2{ 20, 20 }))
			{
				task = AsyncTask{ []() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };
			}
		}
	# endif
		
		if (task.isReady())
		{
			// 起動が完了した Webcam をタスクから取得
			webcam = task.get();

			if (webcam)
			{
				Print << webcam.getResolution();
			}
		}

		if (webcam.hasNewFrame())
		{
			webcam.getFrame(image);

			texture.fill(image);

			// QR コードをスキャン
			contents = qrScanner.scan(image);
		}

		// Webcam 作成待機中は円を表示
		if (not webcam)
		{
			Circle{ Scene::Center(), 40 }
				.drawArc(Scene::Time() * 180_deg, 300_deg, 5, 5);
		}

		if (texture)
		{
			texture.draw();
		}

		// 検出された QR コードを可視化
		for (const auto& content : contents)
		{
			content.quad.drawFrame(4, Palette::Red);

			PutText(content.text, Arg::topLeft = content.quad.p0);
		}
	}
}
```
