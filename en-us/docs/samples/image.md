# Image Samples

## 1. Sketch

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/1.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Canvas size
		constexpr Size CanvasSize{ 600, 600 };

		// Pen thickness
		constexpr int32 PenThickness = 8;

		// Pen color
		constexpr Color PenColor = Palette::Orange;

		// Prepare image data for drawing
		Image image{ CanvasSize, Palette::White };

		// Texture for display (DynamicTexture since content will be updated)
		DynamicTexture texture{ image };

		while (System::Update())
		{
			if (MouseL.pressed())
			{
				// Starting point of the line to draw is the mouse cursor position from the previous frame
				// (Use current mouse cursor position for the first time to prevent coordinate jumps during touch operations)
				const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());

				// Ending point of the line to draw is the current mouse cursor position
				const Point to = Cursor::Pos();

				// Draw line to image
				Line{ from, to }.overwrite(image, PenThickness, PenColor);

				// Update texture with the drawn image
				texture.fill(image);
			}

			// If the clear button is pressed
			if (SimpleGUI::Button(U"Clear", Vec2{ 640, 40 }, 120))
			{
				// Fill image with white
				image.fill(Palette::White);

				// Update texture with the filled image
				texture.fill(image);
			}

			// Display texture
			texture.draw();
		}
	}
	```

## 2. Kaleidoscope sketch

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/2.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Canvas size
		constexpr Size CanvasSize{ 600, 600 };

		// Number of divisions
		constexpr int32 N = 12;

		// Background color
		constexpr Color BackgroundColor{ 20, 40, 60 };

		// Resize window to canvas size
		Window::Resize(CanvasSize);

		// Image for drawing
		Image image{ CanvasSize, BackgroundColor };

		// Dynamic texture for displaying the image
		DynamicTexture texture{ image };

		while (System::Update())
		{
			if (MouseL.pressed())
			{
				// Move mouse cursor coordinates so that the center of the screen becomes (0, 0)
				const Vec2 begin = ((MouseL.down() ? Cursor::PosF() : Cursor::PreviousPosF()) - CanvasSize / 2);
				const Vec2 end = (Cursor::PosF() - CanvasSize / 2);

				// Change color according to time
				const ColorF color = HSV{ (Scene::Time() * 60.0), 0.5, 1.0 };

				for (int32 i = 0; i < N; ++i)
				{
					// Convert to polar coordinates
					std::array<Circular, 2> cs = { begin, end };

					for (auto& c : cs)
					{
						// Shift angle
						if (IsEven(i))
						{
							c.theta = (-c.theta - 2_pi / N * (i - 1));
						}
						else
						{
							c.theta = (c.theta + 2_pi / N * i);
						}
					}

					// Draw line to image based on shifted position
					Line{ cs[0], cs[1] }.moveBy(CanvasSize / 2)
						.overwrite(image, 2, color);
				}

				// Update texture with the drawn image
				texture.fillIfNotBusy(image);
			}

			if (MouseR.down()) // Reset with right click
			{
				// Fill image
				image.fill(BackgroundColor);

				// Update texture with filled image
				texture.fill(image);
			}

			// Draw texture
			texture.draw();
		}
	}
	```

## 3. Paint

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/3.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Canvas size
		constexpr Size CanvasSize{ 600, 600 };

		// Pen thickness
		double penThickness = 8;

		// Pen color
		HSV penColor = Palette::Orange;

		// Prepare image data for drawing
		Image image{ CanvasSize, Palette::White };

		// Texture for display (DynamicTexture since content will be updated)
		DynamicTexture texture{ image };

		const Array<String> modes = { U"Draw", U"Fill", U"Pick" };

		size_t modeIndex = 0;

		while (System::Update())
		{
			if (modeIndex == 0) // Pen
			{
				if (MouseL.pressed())
				{
					// Starting point of the line to draw is the mouse cursor position from the previous frame
					// (Use current mouse cursor position for the first time to prevent coordinate jumps during touch operations)
					const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());

					// Ending point of the line to draw is the current mouse cursor position
					const Point to = Cursor::Pos();

					// Draw line to image
					Line{ from, to }.overwrite(image, static_cast<int32>(penThickness), penColor, Antialiased::No);

					// Update texture with the drawn image
					texture.fill(image);
				}
				else if (MouseR.pressed())
				{
					const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
					const Point to = Cursor::Pos();
					Line{ from, to }.overwrite(image, static_cast<int32>(penThickness), Palette::White, Antialiased::No);
					texture.fill(image);
				}
			}
			else if (modeIndex == 1) // Fill
			{
				if (MouseL.down())
				{
					image.floodFill(Cursor::Pos(), penColor);
					texture.fill(image);
				}
				else if (MouseR.down())
				{
					image.floodFill(Cursor::Pos(), Palette::White);
					texture.fill(image);
				}
			}
			else // Picker
			{
				if (MouseL.down())
				{
					const Point cursorPos = Cursor::Pos();

					if (InRange(cursorPos.x, 0, (image.width() - 1))
						&& InRange(cursorPos.y, 0, (image.height() - 1)))
					{
						penColor = image[cursorPos];
					}
				}
			}

			if (SimpleGUI::Button(U"Save", Vec2{ 620, 40 }, 160))
			{
				image.saveWithDialog();
			}

			// If the clear button is pressed
			if (SimpleGUI::Button(U"Clear", Vec2{ 620, 100 }, 160))
			{
				// Fill image with white
				image.fill(Palette::White);

				// Update texture with the filled image
				texture.fill(image);
			}

			// Color selection
			SimpleGUI::ColorPicker(penColor, Vec2{ 620, 160 });

			// Pen thickness
			SimpleGUI::Slider(penThickness, 1.0, 30.0, Vec2{ 620, 300 }, 160);

			// Mode selection
			SimpleGUI::RadioButtons(modeIndex, modes, Vec2{ 620, 360 });

			// Display texture
			texture.draw();
		}
	}
	```


## 4. Image to Polygon

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/4.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Image to use
		const Image image{ U"example/siv3d-kun.png" };

		// Texture display position
		constexpr Vec2 BasePos{ 40, 80 };

		// Texture
		const Texture texture{ image };

		// Convert regions with alpha value 1 or higher to Polygon
		const Polygon polygon = image.alphaToPolygon(1, AllowHoles::No);

		// Tolerance distance for Polygon simplification (pixels)
		double maxDistance = 4.0;

		// Simplified Polygon
		Polygon simplifiedPolygon = polygon.simplified(maxDistance);

		while (System::Update())
		{
			// Display the number of triangles in the simplified Polygon
			ClearPrint();
			Print << U"{} triangles"_fmt(simplifiedPolygon.num_triangles());

			texture.draw(BasePos);

			// Display the simplified Polygon on the texture
			simplifiedPolygon.movedBy(BasePos)
				.draw(ColorF{ 1.0, 1.0, 0.0, 0.2 })
				.drawWireframe(2, Palette::Yellow);

			// Display the simplified Polygon next to the texture
			simplifiedPolygon.movedBy(BasePos.movedBy(320, 0))
				.draw(ColorF{ 0.5 });

			// Slider to set the tolerance distance for Polygon simplification
			if (SimpleGUI::Slider(U"{:.1f}"_fmt(maxDistance), maxDistance, 0, 50, Vec2{ 400, 40 }, 60, 240))
			{
				// If the slider changes, recreate the simplified Polygon with the new tolerance distance
				simplifiedPolygon = polygon.simplified(maxDistance);
			}
		}
	}
	```


## 5. JPEG Glitch

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/5.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Image
		const Image image{ U"example/windmill.png" };

		// Dynamic texture for display
		DynamicTexture texture{ image };

		// JPEG binary data
		const Blob originalBlob = image.encodeJPEG();

		// Number of data points to modify
		const size_t noiseCount = (image.num_pixels() / 4000);

		while (System::Update())
		{
			if (SimpleGUI::Button(U"Glitch", Vec2{ 40, 40 }))
			{
				// Create Array
				Blob modifiedBlob = originalBlob;

				for (size_t i = 0; i < noiseCount; ++i)
				{
					// Rewrite 1 byte at a random position to a random value.
					// Do not modify the header part (beginning).
					const size_t index = Random<size_t>(630, (modifiedBlob.size() - 1));

					modifiedBlob[index] = Byte{ RandomUint8() };
				}

				// Load as JPEG data to create image, transfer to dynamic texture
				texture.fill(Image{ MemoryReader{ modifiedBlob }, ImageFormat::JPEG });
			}

			texture.drawAt(Scene::Center());
		}
	}
	```


## 6. Tracing app

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/6.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Function to calculate distance between two images
	double Diff(const Image& a, const Image& b)
	{
		const Color* pA = a.data();
		const Color* pB = b.data();
		const Color* const pAEnd = (pA + a.num_pixels());
		double d = 0.0;

		// For all pixels
		while (pA != pAEnd)
		{
			d += (AbsDiff(pA->r, pB->r) + AbsDiff(pA->g, pB->g) + AbsDiff(pA->b, pB->b));
			++pA;
			++pB;
		}

		return d;
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Select target image through file dialog, resize to fit scene size
		const Image target = Dialog::OpenImage().fit(Scene::Size());

		// Current image
		Image image{ target.size(), Palette::White };

		// Previous image
		Image old = image;

		// Dynamic texture for displaying current image
		DynamicTexture texture{ image };

		// Distance to target
		double d1 = Diff(target, image);

		while (System::Update())
		{
			for (int32 i = 0; i < 100; ++i)
			{
				// Random coordinates
				const Point pos = RandomPoint(Rect{ image.size() });

				// Random color
				const ColorF color{ Random(), Random(), Random(), Random() };

				// Random radius
				const int32 size = Random(1, 10);

				// Draw circle to current image
				Circle{ pos, size }.paint(image, color);

				// Calculate distance to target
				const double d2 = Diff(target, image);

				if (d2 < d1) // Adopt if closer to target
				{
					d1 = d2;
					old = image;
				}
				else // Revert if not closer
				{
					image = old;
				}
			}

			// Update dynamic texture
			texture.fill(image);

			// Draw texture at center of screen
			texture.drawAt(Scene::Center());

			// Save button
			if (SimpleGUI::Button(U"Save", Vec2{ 660, 550 }))
			{
				// Save current image through file dialog
				image.saveWithDialog();
			}
		}
	}
	```


## 7. GrabCut background separation and Inpaint restoration

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/VfhFdJOdWw0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.8, 1.0, 0.9 });

		const Image image = Dialog::OpenImage().fit(Size{ 480, 320 });
		const Texture texture{ image };

		GrabCut grabcut{ image };
		Image mask{ image.size(), Color{0, 0} };
		Image background{ image.size(), Palette::Black };
		Image foreground{ image.size(), Palette::Black };
		Image inpaint;
		DynamicTexture maskTexture{ mask };
		Grid<GrabCutClass> result;
		DynamicTexture classTexture;
		DynamicTexture backgroundTexture{ background };
		DynamicTexture foregroundTexture{ foreground };
		DynamicTexture inpaintTexture{ foreground };

		constexpr Color BackgroundColor{ 0, 0, 255 };
		constexpr Color ForegroundColor{ 250, 100, 50 };

		while (System::Update())
		{
			if ((not classTexture) || MouseL.up() || MouseR.up())
			{
				grabcut.update(mask, ForegroundColor, BackgroundColor);
				grabcut.getResult(result);
				classTexture.fill(Image(result, [](GrabCutClass c) { return Color(80 * FromEnum(c)); }));

				for (auto p : step(image.size()))
				{
					const bool isBackground = (GrabCutClass::PossibleBackground <= result[p]);

					if (isBackground)
					{
						background[p] = image[p];
						foreground[p] = Color{ 0,0 };
					}
					else
					{
						foreground[p] = image[p];
						background[p] = Color{ 0,0 };
					}
				}

				ImageProcessing::Inpaint(background, background, Color{ 0, 0 }, inpaint);
				inpaint.gaussianBlur(3);

				foregroundTexture.fill(foreground);
				backgroundTexture.fill(background);
				inpaintTexture.fill(inpaint);
			}

			if (MouseL.pressed())
			{
				const Point from = MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos();
				const Point to = Cursor::Pos();
				Line{ from, to }.overwrite(mask, 4, ForegroundColor, Antialiased::No);
				maskTexture.fill(mask);
			}
			else if (MouseR.pressed())
			{
				const Point from = MouseR.down() ? Cursor::Pos() : Cursor::PreviousPos();
				const Point to = Cursor::Pos();
				Line{ from, to }.overwrite(mask, 4, BackgroundColor, Antialiased::No);
				maskTexture.fill(mask);
			}

			texture.draw();
			maskTexture.draw();
			classTexture.draw(600, 0);

			backgroundTexture.scaled(0.7).regionAt(200, 520).draw(ColorF{ 0 });
			backgroundTexture.scaled(0.7).drawAt(200, 520);

			foregroundTexture.scaled(0.7).regionAt(1080, 520).draw(ColorF{ 0 });
			foregroundTexture.scaled(0.7).drawAt(1080, 520);

			inpaintTexture.drawAt(640, 520);
			{
				const Transformer2D transformer{ Mat3x2::Scale(1.1, Vec2{640, 520}.movedBy(0, image.height() / 2)).translated((Scene::Center() - Cursor::Pos()) * 0.04) };
				foregroundTexture.drawAt(640, 520);
			}
		}
	}
	```


## 8. Face detection from dropped illustrations

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Texture texture;

		double scale = 1.0;

		// Detector. Classify using training data for front-facing faces
		const CascadeClassifier animeFaceDetector{ U"example/objdetect/haarcascade/face_anime.xml" };

		Array<Rect> detectedFaces;

		while (System::Update())
		{
			// File was dropped
			if (DragDrop::HasNewFilePaths())
			{
				// File could be loaded as an image
				if (const Image image{ DragDrop::GetDroppedFilePaths().front().path })
				{
					// Detect faces in the illustration
					detectedFaces = animeFaceDetector.detectObjects(image);

					// Scale image to fit screen size
					texture = Texture{ image.fitted(Scene::Size()) };

					// Image scaling ratio
					scale = (static_cast<double>(texture.width()) / image.width());
				}
			}

			if (texture)
			{
				texture.draw(0, 0);

				// Adjust face region coordinates for display
				const Transformer2D transformer{ Mat3x2::Scale(scale) };

				for (const auto& detectedFace : detectedFaces)
				{
					detectedFace.drawFrame((4 / scale), ColorF{ 1.0, 0.0, 0.0, Periodic::Sine0_1(1.5s) });
				}
			}
		}
	}
	```


## 9. Mandelbrot set

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/9.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	int32 Mandelbrot(double x, double y)
	{
		double a = 0.0, b = 0.0;

		for (int32 n = 0; n < 360; ++n)
		{
			const double t = (a * a - b * b + x);
			const double u = (2.0 * a * b + y);

			if (4.0 < (t * t + u * u))
			{
				return n;
			}

			a = t;
			b = u;
		}

		return 0;
	}

	void Main()
	{
		constexpr Size SceneSize{ 640, 480 };
		Window::Resize(SceneSize);

		Vec2 center(0, 0);
		double scale = -4.0;

		// Image to save results
		Image image{ SceneSize, Palette::Black };

		// Dynamic texture for drawing
		DynamicTexture texture(image);

		while (System::Update())
		{
			const double wheel = Mouse::Wheel();
			const bool clicked = (MouseL | MouseR).down();

			// Update only on first frame or when there's an operation
			if (wheel || clicked || (Scene::FrameCount() == 1))
			{
				scale -= wheel;

				const double s = Pow(1.25, scale);
				const double d = ((1.0 / s) / SceneSize.x);

				if (clicked)
				{
					center += (Cursor::PosF() - SceneSize / 2) * d;
				}

				const double xb = (center.x - d * (SceneSize.x * 0.5));
				const double yb = (center.y - d * (SceneSize.y * 0.5));

				for (int32 y = 0; y < SceneSize.y; ++y)
				{
					const double yPos = yb + (d * y);

					for (int32 x = 0; x < SceneSize.x; ++x)
					{
						const double xPos = xb + (d * x);

						if (const int32 m = Mandelbrot(xPos, yPos))
						{
							image[y][x] = HSV{ (240 - m), 0.8, 1.0 };
						}
						else
						{
							image[y][x] = Palette::Black;
						}
					}
				}

				// Update dynamic texture content with image
				texture.fill(image);
			}

			// Draw texture
			texture.draw();
		}
	}
	```

## 10. Kaleidoscope random walk

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/RandomWalkKaleidoscope/Screenshot/2.png)

[Siv3D-Sample | Kaleidoscope random walk :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/blob/main/Samples/RandomWalkKaleidoscope){:target="_blank" .md-button}
