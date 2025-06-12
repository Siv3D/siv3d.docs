# 71. Webcam
Learn how to get video from built-in or connected webcams on your computer and use it in programs.

## 71.1 Enumerating Connected Webcams
- You can get a list of webcams connected to your PC with `System::EnumerateWebcams()`
- The result is returned as `Array<WebcamInfo>` type
- The member variables of `WebcamInfo` type are as follows:

| Code | Description |
|--|--|
| `uint32 cameraIndex` | Device index for use with `Webcam` |
| `String name` | Name |
| `String uniqueName` | Unique name |

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


## 71.2 Webcam Video Capture
- Initialize a webcam with `Webcam{ device index, resolution, start recording immediately }`
	- `Device index` is the `cameraIndex` obtained from `System::EnumerateWebcams()`
	- `Resolution` is specified as `Size{ width, height }`. If the camera doesn't support the specified resolution, a close resolution will be selected
	- `Start recording immediately` is specified as `StartImmediately::Yes` or `StartImmediately::No`
		- If No, call the `.start()` member function after creating the `Webcam` object to start recording
- Depending on the environment, camera initialization may take several seconds or more. Consider asynchronous initialization (**71.3**)
- While recording, check if a new image has been taken with `.hasNewFrame()` and get the image by passing a `DynamicTexture` to `.getFrame()`

!!! warning "Notes for macOS"
	- On macOS, when creating `Webcam` for the first time, a dialog asking for camera usage permission is displayed to the user, but the `Webcam` creation itself will always fail, so you need to provide a means of recreation

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// Device index 0 webcam, request 1280x720 or close size, start recording immediately
	Webcam webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes };

	DynamicTexture texture;

	while (System::Update())
	{
		// On macOS, provide retry means since Webcam creation fails if user doesn't allow camera usage permission
	# if SIV3D_PLATFORM(MACOS)
		if (not webcam)
		{
			if (SimpleGUI::Button(U"Retry", Vec2{ 20, 20 }))
			{
				webcam = Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes };
			}
		}
	# endif

		// New image was taken
		if (webcam.hasNewFrame())
		{
			// Transfer to DynamicTexture image
			webcam.getFrame(texture);
		}

		if (texture)
		{
			texture.draw();
		}
	}
}
```

## 71.3 Webcam Video Capture (Asynchronous Webcam Initialization)
- If you want to avoid the main thread stopping until camera initialization is complete, use asynchronous tasks to initialize the camera as follows
- Asynchronous tasks are explained in detail in **Tutorial 76**

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// Create asynchronous task
	AsyncTask<Webcam> task{ []() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };

	Webcam webcam;
	DynamicTexture texture;

	while (System::Update())
	{
		// On macOS, Webcam creation fails if user doesn't allow camera usage permission. Provide retry means
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
			// Get the launched Webcam from the task
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


## 71.4 Image Processing on Camera Video
- Captured images can also be obtained as `Image`
- The following sample code converts the captured image to grayscale before transferring it to `DynamicTexture`

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

			// Convert to grayscale
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


## 71.5 Face Detection from Webcam Video
- Using the `CascadeClassifier` class that uses Haar-like features, you can detect specific objects from given images
- For detecting front-facing faces, use `example/objdetect/haarcascade/frontal_face_alt2.xml` as model data
- Note that Haar-like feature-based face detection is a classical method and detection accuracy is not as high compared to deep learning-based methods

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	AsyncTask<Webcam> task{ []() { return Webcam{ 0, Size{ 1280, 720 }, StartImmediately::Yes }; } };
	Webcam webcam;
	Image image;
	DynamicTexture texture;

	// Detector. Model data for front-facing faces
	const CascadeClassifier faceDetector{ U"example/objdetect/haarcascade/frontal_face_alt2.xml" };

	// Detected regions
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

			// Detect faces from image. Strictness: 3, minimum region size: 100x100
			rects = faceDetector.detectObjects(image, 3, Size{ 100, 100 });

			// Apply mosaic effect to face regions
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


## 71.6 QR Code Detection from Webcam Video
- Use the `QRScanner` class to detect QR codes from `Image`
- To detect maximum size (Version 40) QR codes, video resolution larger than 1280 x 720 is required
- The `.scan()` member function of `QRScanner` returns the contents of detected QR codes as `Array<QRContent>`

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