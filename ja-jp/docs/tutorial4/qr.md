# 78. QR コード
QR コードの作成と、読み取りの方法を学びます。

## 78.1 QR コードの作成（文字列）
- 文字列から QR コードを作成する次のような関数があります
- 戻り値は QR データを保持する二次元配列 `Grid<bool>` です
    - 白いセルが `false`、黒いセルが `true` です

| コード | 説明 |
|---|---|
| `QR::EncodeNumber(s)` | 数字から構成される文字列から QR データを作成します |
| `QR::EncodeAlnum(s)` | 英数字から構成される文字列から QR データを作成します |
| `QR::EncodeText(s)` | 文字列から QR データを作成します |

- `Grid<bool>` で表現される QR コードを `Image` に変換するには、`QR::MakeImage(qr, cellSize)` を使います
    - `cellSize` は 1 つのセルの辺の長さ（ピクセル）です。指定したなっかた場合は `16` が使われます
    - 適切な余白も自動的に追加されます
- 次のサンプルコードを実行すると、`Hello, Siv3D!` という文字列から QR コードを作成して `qr.png` として保存します

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"Hello, Siv3D!";

	// 文字列を QR コードにエンコードして画像として保存する
	QR::MakeImage(QR::EncodeText(s), 10).save(U"qr.png");

	while (System::Update())
	{

	}
}
```


## 78.2 QR コードのインタラクティブな作成（文字列）
- テキストボックスに入力した文字列を QR コードに変換して表示するサンプルです
- 文字列に変更があったときにのみ QR コードを再作成します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/qr/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 変換するテキスト
	TextEditState textEdit{ U"abc" };

	String previous;

	// QR コードを表示するための動的テクスチャ
	DynamicTexture texture;

	while (System::Update())
	{
		// テキスト入力
		SimpleGUI::TextBox(textEdit, Vec2{ 20, 20 }, 1240);

		// テキストの更新があれば QR コードを再作成する
		if (const String current = textEdit.text;
			current != previous)
		{
			// 入力したテキストを QR コードに変換する
			if (const auto qr = QR::EncodeText(current))
			{
				// 拡大した画像で動的テクスチャを更新する
				texture.fill(QR::MakeImage(qr).scaled(Size{ 500, 500 }, InterpolationAlgorithm::Nearest));
			}

			previous = current;
		}

		texture.drawAt(640, 400);
	}
}
```

## 78.2 QR コードの作成（バイナリ）
- バイナリデータから QR コードを作成するには、`QR::EncodeBinary(data, size)` を使います
- `data` はバイナリデータの先頭ポインタ、`size` はバイナリデータのサイズ（バイト）です
- 戻り値は QR データを保持する二次元配列 `Grid<bool>` です

```cpp
# include <Siv3D.hpp>

struct Data
{
	double a;
	int32 b;
	int32 c;
};

void Main()
{
	const Data data{ 1.23, 456, 789 };

	// バイナリデータを QR コードにエンコードして画像として保存する
	QR::MakeImage(QR::EncodeBinary(&data, sizeof(data)), 10).save(U"qr.png");

	while (System::Update())
	{

	}
}
```



## 78.3 QR コードの読み取り（文字列）
- `QRScanner` は `Image` に含まれる QR コードを読み取るためのクラスです
- `.scanOne(image)` は `image` に含まれる QR コードを最大 1 つ読み取り、`QRContent` を返します
- QR コードが文字列をエンコードしている場合、`QRContent::text` に文字列が格納されます

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		QR::MakeImage(QR::EncodeText(U"Hello, Siv3D!"), 10).save(U"qr.png");
	}

	{
		const QRScanner scanner;
		const QRContent content = scanner.scanOne(Image{ U"qr.png" });

		if (content)
		{
			Print << content.text;
		}
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
Hello, Siv3D!
```


## 78.4 QR コードの読み取り（バイナリ）
- QR コードがバイナリデータをエンコードしている場合、`QRContent::binary` にバイナリデータが格納されます
- バイナリデータのサイズは `QRContent::binary.size()` で取得できます
- バイナリデータの先頭ポインタは `QRContent::binary.data()` で取得できます
	
```cpp
# include <Siv3D.hpp>

struct Data
{
	double a;
	int32 b;
	int32 c;
};

void Main()
{
	{
		const Data data{ 1.23, 456, 789 };
		QR::MakeImage(QR::EncodeBinary(&data, sizeof(data)), 10).save(U"qr.png");
	}

	{
		const QRScanner scanner;
		const QRContent content = scanner.scanOne(Image{ U"qr.png" });

		if (content && (content.binary.size() == sizeof(Data)))
		{
			Data data;
			std::memcpy(&data, content.binary.data(), sizeof(data));
			Print << data.a << U", " << data.b << U", " << data.c;
		}
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
1.23, 456, 789
```


## 78.5 Web カメラの映像から QR コードを検出する
- `QRScanner` の `.scan()` メンバ関数は、検出された QR コードの内容を `Array<QRContent>` で返します
- Web カメラについては **チュートリアル 71** を参照してください

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
