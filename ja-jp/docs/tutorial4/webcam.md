# 71. Web カメラ
パソコンに内蔵・接続された Web カメラから映像を取得し、プログラムで活用する方法を学びます。

## 71.1 接続されている Web カメラを列挙する
- PC に接続されている Web カメラの一覧を `System::EnumerateWebcams()` で取得できます
- 結果は `Array<WebcamInfo>` 型で返されます
- `WebcamInfo` 型のメンバ変数は次のとおりです：

| コード | 説明 |
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
		Print << U"[{}] {} {}"_fmt(info.cameraIndex, info.name, info.uniqueName);
	}

	while (System::Update())
	{

	}
}
```


## 71.2 Web カメラの映像取得
- `Webcam{ デバイスインデックス, 解像度, 即座に撮影開始するか }` で Web カメラを初期化します
	- `デバイスインデックス` は `System::EnumerateWebcams()` で取得した `cameraIndex` です
	- `解像度` は `Size{ 幅, 高さ }` で指定します。カメラが指定の解像度をサポートしていない場合、近い解像度が選択されます
	- `即座に撮影開始するか` は `StartImmediately::Yes` または `StartImmediately::No` で指定します
		- No の場合、`Webcam` オブジェクトを作成したあと `.start()` メンバ関数を呼び出して撮影を開始します
- 環境によっては、カメラの初期化に数秒以上かかることがあります。非同期での初期化（**71.3**）も検討してください
- 撮影中は `.hasNewFrame()` で新しい画像が撮影されたかを確認し、`.getFrame()` に `DynamicTexture` を渡して画像を取得します

!!! warning "macOS での注意"
	- macOS では、初回の `Webcam` 作成時に、ユーザにカメラの使用権限を求めるダイアログを表示しつつ、`Webcam` の作成自体には必ず失敗するため、再作成の手段を用意する必要があります

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

## 71.3 Web カメラの映像取得（非同期で Web カメラを初期化）
- カメラの初期化完了までメインスレッドが停止することを避けたい場合、次のように非同期タスクを使ってカメラの初期化を行います
- 非同期タスクについては **チュートリアル 76** で詳しく説明します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 非同期タスクを作成
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

		if (not webcam)
		{
			Circle{ 640, 360, 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}

		if (texture)
		{
			texture.draw();
		}
	}
}
```


## 71.4 カメラの映像に対する画像処理
- 撮影した画像は `Image` で取得することもできます
- 次のサンプルコードでは、撮影した画像をグレースケール化してから `DynamicTexture` に転送しています

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	AsyncTask<Webcam> task{ []() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };
	Webcam webcam;
	Image image;
	DynamicTexture texture;

	while (System::Update())
	{
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
			webcam = task.get();

			if (webcam)
			{
				Print << webcam.getResolution();
			}
		}

		if (webcam.hasNewFrame())
		{
			webcam.getFrame(image);

			// グレースケール化
			image.grayscale();

			texture.fill(image);
		}

		if (not webcam)
		{
			Circle{ 640, 360, 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
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

	AsyncTask<Webcam> task{ []() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };
	Webcam webcam;
	Image image;
	DynamicTexture texture;

	// 検出器。正面を向いた顔のモデルデータ
	const CascadeClassifier faceDetector{ U"example/objdetect/haarcascade/frontal_face_alt2.xml" };

	// 検出された領域
	Array<Rect> rects;

	while (System::Update())
	{
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

			// 顔の領域にモザイクエフェクトをかける
			for (const auto& rect : rects)
			{
				image(rect).mosaic(20);
			}

			texture.fill(image);
		}

		if (not webcam)
		{
			Circle{ 640, 360, 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
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
- `Image` から QR コードを検出するには `QRScanner` クラスを使います
- 最大サイズ (Version 40) の QR コードを検出したい場合、1280 x 720 よりも大きな解像度の映像が必要です
- `QRScanner` の `.scan()` メンバ関数は、検出された QR コードの内容を `Array<QRContent>` で返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	const Font font{ FontMethod::MSDF, 48 };

	AsyncTask<Webcam> task{ [=]() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };
	Webcam webcam;
	Image image;
	DynamicTexture texture;

	// QR コードスキャナ
	const QRScanner qrScanner;

	// QR コードの内容
	Array<QRContent> contents;

	while (System::Update())
	{
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

		if (not webcam)
		{
			Circle{ 640, 360, 50 }.drawArc((Scene::Time() * 120_deg), 300_deg, 4, 4);
		}

		if (texture)
		{
			texture.draw();
		}

		// 検出された QR コードの位置を可視化する
		for (const auto& content : contents)
		{
			content.quad.drawFrame(4, Palette::Red);

			if (content.text)
			{
				const String& text = content.text;
				font(text).region(20, content.quad.p0).stretched(10).draw(ColorF{ 1.0, 0.8 });
				font(text).draw(20, content.quad.p0, ColorF{ 0.1 });
			}
		}
	}
}
```
