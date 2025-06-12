# 78. QR Code
Learn how to create and read QR codes.

## 78.1 Creating QR Codes (String)
- There are functions to create QR codes from strings as follows
- The return value is a 2D array `Grid<bool>` that holds QR data
    - White cells are `false`, black cells are `true`

| Code | Description |
|---|---|
| `QR::EncodeNumber(s)` | Creates QR data from a string composed of numbers |
| `QR::EncodeAlnum(s)` | Creates QR data from a string composed of alphanumeric characters |
| `QR::EncodeText(s)` | Creates QR data from a string |

- To convert a QR code represented as `Grid<bool>` to `Image`, use `QR::MakeImage(qr, cellSize)`
    - `cellSize` is the side length (pixels) of one cell. If not specified, `16` is used
    - Appropriate margins are automatically added
- Running the following sample code will create a QR code from the string `Hello, Siv3D!` and save it as `qr.png`

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"Hello, Siv3D!";

	// Encode string to QR code and save as image
	QR::MakeImage(QR::EncodeText(s), 10).save(U"qr.png");

	while (System::Update())
	{

	}
}
```


## 78.2 Interactive QR Code Creation (String)
- This is a sample that converts text entered in a text box to a QR code and displays it
- The QR code is recreated only when there are changes to the string

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/qr/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Text to convert
	TextEditState textEdit{ U"abc" };

	String previous;

	// Dynamic texture for displaying QR code
	DynamicTexture texture;

	while (System::Update())
	{
		// Text input
		SimpleGUI::TextBox(textEdit, Vec2{ 20, 20 }, 1240);

		// Recreate QR code if text is updated
		if (const String current = textEdit.text;
			current != previous)
		{
			// Convert input text to QR code
			if (const auto qr = QR::EncodeText(current))
			{
				// Update dynamic texture with scaled image
				texture.fill(QR::MakeImage(qr).scaled(Size{ 500, 500 }, InterpolationAlgorithm::Nearest));
			}

			previous = current;
		}

		texture.drawAt(640, 400);
	}
}
```

## 78.3 Creating QR Codes (Binary)
- To create QR codes from binary data, use `QR::EncodeBinary(data, size)`
- `data` is a pointer to the beginning of the binary data, `size` is the size of the binary data (bytes)
- The return value is a 2D array `Grid<bool>` that holds QR data

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

	// Encode binary data to QR code and save as image
	QR::MakeImage(QR::EncodeBinary(&data, sizeof(data)), 10).save(U"qr.png");

	while (System::Update())
	{

	}
}
```



## 78.4 Reading QR Codes (String)
- `QRScanner` is a class for reading QR codes contained in `Image`
- `.scanOne(image)` reads up to one QR code contained in `image` and returns `QRContent`
- When the QR code encodes a string, the string is stored in `QRContent::text`

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
```txt title="Output"
Hello, Siv3D!
```


## 78.5 Reading QR Codes (Binary)
- When the QR code encodes binary data, the binary data is stored in `QRContent::binary`
- The size of the binary data can be obtained with `QRContent::binary.size()`
- The pointer to the beginning of the binary data can be obtained with `QRContent::binary.data()`
	
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
```txt title="Output"
1.23, 456, 789
```


## 78.6 Detecting QR Codes from Webcam Video
- The `.scan()` member function of `QRScanner` returns the contents of detected QR codes as `Array<QRContent>`
- See **Tutorial 71** for information about webcams

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

	// QR code scanner
	const QRScanner qrScanner;

	// QR code contents
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

			// Scan QR codes
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

		// Visualize detected QR code positions
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