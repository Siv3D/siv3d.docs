# Random Painting to Match a Reference

| | | | |
|:--:|:--:|:--:|:--:|
| **Difficulty** | Intermediate | **Time** | 60 min+ |

When we say "making a computer draw," we aren't talking about the currently trending generative AI. Instead, we use nothing but a simple program and random numbers.

The program starts by randomly drawing lines and placing shapes on a blank canvas. By keeping only the attempts that bring the result closer to the reference image, what begins as mere scribbles gradually—but surely—transforms into "expressive geometric art."

Let's enjoy this slightly mysterious and creative process together, watching the chaotic screen converge into a beautiful painting as the program strives to mimic the reference.

## 1. Open and Display an Image
- First, create a function to load the reference image that will be copied.
- Here, we will implement the ability to select an image file via a button, resize it to an appropriate size, and display it on the left side of the screen.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/24/1.png)

??? note "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Maximum canvas size (larger sizes take longer to process)
		static constexpr Size MaxCanvasSize{ 640, 720 };

		// Reference image (in main memory)
		Image targetImage;
		// Texture to draw the reference image (in VRAM)
		Texture targetTexture;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ MaxCanvasSize }.draw(Arg::top(0.7), Arg::bottom(0.2));
				Rect{ MaxCanvasSize.x, 0, MaxCanvasSize }.draw(Arg::top(0.7, 0.3, 0.4), Arg::bottom(0.6, 0.1, 0.4));
			}

			// Display reference image on the left
			if (targetTexture)
			{
				targetTexture.scaled(0.92).drawAt(MaxCanvasSize / 2);
			}

			// Button to open reference image
			if (SimpleGUI::Button(U"Open", Vec2{ 30, 30 }, 100))
			{
				if (Image image = Dialog::OpenImage()) // If image opened successfully
				{
					// Resize to fit max canvas size
					targetImage = image.fitted(MaxCanvasSize);
					// Ignore transparency and make opaque
					for (auto& pixel : targetImage)
					{
						pixel.a = 255;
					}

					// Create new reference texture
					targetTexture = Texture{ targetImage };
				}
			}
		}
	}
	```

## 2. Create and Display a Canvas
- Prepare a canvas for drawing next to the reference image.
- By placing the reference on the left and the canvas on the right, you can compare and see how the program reproduces the image.
- At this stage, the canvas is blank.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/24/2.png)

??? note "Code"
	```cpp hl_lines="16-19 35-39 57-61"
	# include <Siv3D.hpp>

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Maximum canvas size (larger sizes take longer to process)
		static constexpr Size MaxCanvasSize{ 640, 720 };

		// Reference image (in main memory)
		Image targetImage;
		// Texture to draw the reference image (in VRAM)
		Texture targetTexture;

		// Canvas image (in main memory)
		Image canvas;
		// Dynamic texture to draw canvas content (in VRAM)
		DynamicTexture canvasTexture;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ MaxCanvasSize }.draw(Arg::top(0.7), Arg::bottom(0.2));
				Rect{ MaxCanvasSize.x, 0, MaxCanvasSize }.draw(Arg::top(0.7, 0.3, 0.4), Arg::bottom(0.6, 0.1, 0.4));
			}

			// Display reference image on the left
			if (targetTexture)
			{
				targetTexture.scaled(0.92).drawAt(MaxCanvasSize / 2);
			}

			// Display canvas on the right
			if (canvasTexture)
			{
				canvasTexture.scaled(0.92).drawAt(MaxCanvasSize * Vec2{ 1.5, 0.5 });
			}

			// Button to open reference image
			if (SimpleGUI::Button(U"Open", Vec2{ 30, 30 }, 100))
			{
				if (Image image = Dialog::OpenImage()) // If image opened successfully
				{
					// Resize to fit max canvas size
					targetImage = image.fitted(MaxCanvasSize);
					// Ignore transparency and make opaque
					for (auto& pixel : targetImage)
					{
						pixel.a = 255;
					}

					// Create new reference texture
					targetTexture = Texture{ targetImage };

					// Create new canvas with same size as reference
					canvas = Image{ targetImage.size(), Color{ 255 } };

					// Create dynamic texture with new canvas size
					canvasTexture = DynamicTexture{ canvas };
				}
			}
		}
	}
	```

## 3. Draw Random Circles
- From here, we start the drawing process. First, simply draw a "circle" of a random size at a random position on the canvas.
- At this stage, we don't judge "similarity to the reference," so we just keep drawing unconditionally. The canvas will quickly be filled with gray circles.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/24/3.png)

??? note "Code"
	```cpp hl_lines="3-21 40-41 87-101"
	# include <Siv3D.hpp>

	/// @brief Draws a random circle on the canvas.
	/// @param target Reference image
	/// @param canvas Canvas image
	/// @param currentDistance Current distance
	void DrawRandomCircle(const Image& target, Image& canvas)
	{
		// Canvas size
		const Size canvasSize = canvas.size();
		// Randomly determine circle center
		const Point center{ Random(canvasSize.x - 1), Random(canvasSize.y - 1) };
		// Randomly determine circle radius
		const int32 radius = Random(5, 30);

		// Circle color
		Color color{ 64, 64, 64 };

		// Paint circle onto canvas
		Circle{ center, radius }.paint(canvas, color, Antialiased::Yes);
	}

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Maximum canvas size (larger sizes take longer to process)
		static constexpr Size MaxCanvasSize{ 640, 720 };

		// Reference image (in main memory)
		Image targetImage;
		// Texture to draw the reference image (in VRAM)
		Texture targetTexture;

		// Canvas image (in main memory)
		Image canvas;
		// Dynamic texture to draw canvas content (in VRAM)
		DynamicTexture canvasTexture;
		// Candidate image for new canvas state (in main memory)
		Image candidateImage;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ MaxCanvasSize }.draw(Arg::top(0.7), Arg::bottom(0.2));
				Rect{ MaxCanvasSize.x, 0, MaxCanvasSize }.draw(Arg::top(0.7, 0.3, 0.4), Arg::bottom(0.6, 0.1, 0.4));
			}

			// Display reference image on the left
			if (targetTexture)
			{
				targetTexture.scaled(0.92).drawAt(MaxCanvasSize / 2);
			}

			// Display canvas on the right
			if (canvasTexture)
			{
				canvasTexture.scaled(0.92).drawAt(MaxCanvasSize * Vec2{ 1.5, 0.5 });
			}

			// Button to open reference image
			if (SimpleGUI::Button(U"Open", Vec2{ 30, 30 }, 100))
			{
				if (Image image = Dialog::OpenImage()) // If image opened successfully
				{
					// Resize to fit max canvas size
					targetImage = image.fitted(MaxCanvasSize);
					// Ignore transparency and make opaque
					for (auto& pixel : targetImage)
					{
						pixel.a = 255;
					}

					// Create new reference texture
					targetTexture = Texture{ targetImage };

					// Create new canvas with same size as reference
					canvas = Image{ targetImage.size(), Color{ 255 } };

					// Create dynamic texture with new canvas size
					canvasTexture = DynamicTexture{ canvas };
				}
			}

			// Create next state
			if (targetImage && canvas)
			{
				// Match candidate image to current canvas state
				candidateImage = canvas;

				// Draw random circle on candidate image
				DrawRandomCircle(targetImage, candidateImage);

				// Accept unconditionally (will change to accept only if distance decreases later)
				canvas = candidateImage;

				// Update dynamic texture with new canvas content
				canvasTexture.fill(canvas);
			}
		}
	}
	```

## 4. Calculate Difference and Adopt Only If Improved
- This is the most critical part of this program.
- Calculate the color difference (distance) from the reference image for both the "current canvas" and the "canvas with a trial circle drawn".
- Only adopt the result if drawing the circle brings the canvas closer to the reference.
- By repeating this, what started as a collection of circles will gradually reveal the silhouette of the reference.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/24/4.png)

??? note "Code"
	```cpp hl_lines="3-49 55-56 71-72 95-97 137-138 148-149 155 157-163 165-170"
	# include <Siv3D.hpp>

	// Distance type (Sum of absolute differences of pixel colors, max 255 * 3 * pixel count)
	using DistanceType = int32;

	/// @brief Returns the distance between two images (sum of absolute color differences).
	/// @param a One image
	/// @param b The other image
	/// @return Sum of absolute color differences. Returns -1 if image sizes differ.
	DistanceType Distance(const Image& a, const Image& b)
	{
		if (a.size() != b.size())
		{
			return -1;
		}

		// Loop using pointers to avoid overhead
		const size_t pixelCount = a.num_pixels();
		const Color* pA = a.data();
		const Color* pAEnd = (pA + pixelCount);
		const Color* pB = b.data();

		DistanceType result = 0;
		while (pA != pAEnd)
		{
			result += Abs(static_cast<int32>(pA->r) - static_cast<int32>(pB->r));
			result += Abs(static_cast<int32>(pA->g) - static_cast<int32>(pB->g));
			result += Abs(static_cast<int32>(pA->b) - static_cast<int32>(pB->b));
			++pA;
			++pB;
		}

		/*
		// Simple double loop version (high overhead)
		for (int32 y = 0; y < a.height(); ++y)
		{
			for (int32 x = 0; x < a.width(); ++x)
			{
				const Color colorA = a[y][x];
				const Color colorB = b[y][x];
				result += Abs(static_cast<int32>(colorA.r) - static_cast<int32>(colorB.r));
				result += Abs(static_cast<int32>(colorA.g) - static_cast<int32>(colorB.g));
				result += Abs(static_cast<int32>(colorA.b) - static_cast<int32>(colorB.b));
			}
		}
		*/

		return result;
	}

	/// @brief Draws a random circle on the canvas.
	/// @param target Reference image
	/// @param canvas Canvas image
	/// @param currentDistance Current distance
	/// @return New distance
	DistanceType DrawRandomCircle(const Image& target, Image& canvas)
	{
		// Canvas size
		const Size canvasSize = canvas.size();
		// Randomly determine circle center
		const Point center{ Random(canvasSize.x - 1), Random(canvasSize.y - 1) };
		// Randomly determine circle radius
		const int32 radius = Random(5, 30);

		// Circle color
		Color color{ 64, 64, 64 };

		// Paint circle onto canvas
		Circle{ center, radius }.paint(canvas, color, Antialiased::Yes);

		// Return new distance after painting
		return Distance(target, canvas);
	}

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Maximum canvas size (larger sizes take longer to process)
		static constexpr Size MaxCanvasSize{ 640, 720 };

		// Reference image (in main memory)
		Image targetImage;
		// Texture to draw the reference image (in VRAM)
		Texture targetTexture;

		// Canvas image (in main memory)
		Image canvas;
		// Dynamic texture to draw canvas content (in VRAM)
		DynamicTexture canvasTexture;
		// Candidate image for new canvas state (in main memory)
		Image candidateImage;

		// Distance between two images
		DistanceType initialDistance = 0; // Initial distance
		DistanceType currentDistance = 0; // Current distance

		while (System::Update())
		{
			// Draw background
			{
				Rect{ MaxCanvasSize }.draw(Arg::top(0.7), Arg::bottom(0.2));
				Rect{ MaxCanvasSize.x, 0, MaxCanvasSize }.draw(Arg::top(0.7, 0.3, 0.4), Arg::bottom(0.6, 0.1, 0.4));
			}

			// Display reference image on the left
			if (targetTexture)
			{
				targetTexture.scaled(0.92).drawAt(MaxCanvasSize / 2);
			}

			// Display canvas on the right
			if (canvasTexture)
			{
				canvasTexture.scaled(0.92).drawAt(MaxCanvasSize * Vec2{ 1.5, 0.5 });
			}

			// Button to open reference image
			if (SimpleGUI::Button(U"Open", Vec2{ 30, 30 }, 100))
			{
				if (Image image = Dialog::OpenImage()) // If image opened successfully
				{
					// Resize to fit max canvas size
					targetImage = image.fitted(MaxCanvasSize);
					// Ignore transparency and make opaque
					for (auto& pixel : targetImage)
					{
						pixel.a = 255;
					}

					// Create new reference texture
					targetTexture = Texture{ targetImage };

					// Create new canvas with same size as reference
					canvas = Image{ targetImage.size(), Color{ 255 } };
					// Initialize distance
					initialDistance = currentDistance = Distance(targetImage, canvas);

					// Create dynamic texture with new canvas size
					canvasTexture = DynamicTexture{ canvas };
				}
			}

			// Create next state
			if (targetImage && canvas)
			{
				// Whether the canvas was updated in this trial
				bool updated = false;

				// Match candidate image to current canvas state
				candidateImage = canvas;

				// Draw random circle on candidate image
				const DistanceType newDistance = DrawRandomCircle(targetImage, candidateImage);

				// If candidate is closer, accept it
				if (newDistance < currentDistance)
				{
					canvas = candidateImage;
					currentDistance = newDistance;
					updated = true;
				}

				// If canvas was updated
				if (updated)
				{
					// Update dynamic texture with new canvas content
					canvasTexture.fill(canvas);
				}
			}
		}
	}
	```

## 5. Display Distance and Progress
- Although you can see the drawing change, it's more tangible if you can verify it numerically.
- Let's display the "Distance" indicating how far the current canvas is from the reference, and the "Progress Rate" indicating how much it has improved since the start.
- Just watching the numbers decrease provides a strange sense of accomplishment.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/24/5.png)

??? note "Code"
	```cpp hl_lines="80-81 176-183"
	# include <Siv3D.hpp>

	// Distance type (Sum of absolute differences of pixel colors, max 255 * 3 * pixel count)
	using DistanceType = int32;

	/// @brief Returns the distance between two images (sum of absolute color differences).
	/// @param a One image
	/// @param b The other image
	/// @return Sum of absolute color differences. Returns -1 if image sizes differ.
	DistanceType Distance(const Image& a, const Image& b)
	{
		if (a.size() != b.size())
		{
			return -1;
		}

		// Loop using pointers to avoid overhead
		const size_t pixelCount = a.num_pixels();
		const Color* pA = a.data();
		const Color* pAEnd = (pA + pixelCount);
		const Color* pB = b.data();

		DistanceType result = 0;
		while (pA != pAEnd)
		{
			result += Abs(static_cast<int32>(pA->r) - static_cast<int32>(pB->r));
			result += Abs(static_cast<int32>(pA->g) - static_cast<int32>(pB->g));
			result += Abs(static_cast<int32>(pA->b) - static_cast<int32>(pB->b));
			++pA;
			++pB;
		}

		/*
		// Simple double loop version (high overhead)
		for (int32 y = 0; y < a.height(); ++y)
		{
			for (int32 x = 0; x < a.width(); ++x)
			{
				const Color colorA = a[y][x];
				const Color colorB = b[y][x];
				result += Abs(static_cast<int32>(colorA.r) - static_cast<int32>(colorB.r));
				result += Abs(static_cast<int32>(colorA.g) - static_cast<int32>(colorB.g));
				result += Abs(static_cast<int32>(colorA.b) - static_cast<int32>(colorB.b));
			}
		}
		*/

		return result;
	}

	/// @brief Draws a random circle on the canvas.
	/// @param target Reference image
	/// @param canvas Canvas image
	/// @param currentDistance Current distance
	/// @return New distance
	DistanceType DrawRandomCircle(const Image& target, Image& canvas)
	{
		// Canvas size
		const Size canvasSize = canvas.size();
		// Randomly determine circle center
		const Point center{ Random(canvasSize.x - 1), Random(canvasSize.y - 1) };
		// Randomly determine circle radius
		const int32 radius = Random(5, 30);

		// Circle color
		Color color{ 64, 64, 64 };

		// Paint circle onto canvas
		Circle{ center, radius }.paint(canvas, color, Antialiased::Yes);

		// Return new distance after painting
		return Distance(target, canvas);
	}

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Font for displaying distance
		const Font font{ FontMethod::MSDF, 36, Typeface::Bold };

		// Maximum canvas size (larger sizes take longer to process)
		static constexpr Size MaxCanvasSize{ 640, 720 };

		// Reference image (in main memory)
		Image targetImage;
		// Texture to draw the reference image (in VRAM)
		Texture targetTexture;

		// Canvas image (in main memory)
		Image canvas;
		// Dynamic texture to draw canvas content (in VRAM)
		DynamicTexture canvasTexture;
		// Candidate image for new canvas state (in main memory)
		Image candidateImage;

		// Distance between two images
		DistanceType initialDistance = 0; // Initial distance
		DistanceType currentDistance = 0; // Current distance

		while (System::Update())
		{
			// Draw background
			{
				Rect{ MaxCanvasSize }.draw(Arg::top(0.7), Arg::bottom(0.2));
				Rect{ MaxCanvasSize.x, 0, MaxCanvasSize }.draw(Arg::top(0.7, 0.3, 0.4), Arg::bottom(0.6, 0.1, 0.4));
			}

			// Display reference image on the left
			if (targetTexture)
			{
				targetTexture.scaled(0.92).drawAt(MaxCanvasSize / 2);
			}

			// Display canvas on the right
			if (canvasTexture)
			{
				canvasTexture.scaled(0.92).drawAt(MaxCanvasSize * Vec2{ 1.5, 0.5 });
			}

			// Button to open reference image
			if (SimpleGUI::Button(U"Open", Vec2{ 30, 30 }, 100))
			{
				if (Image image = Dialog::OpenImage()) // If image opened successfully
				{
					// Resize to fit max canvas size
					targetImage = image.fitted(MaxCanvasSize);
					// Ignore transparency and make opaque
					for (auto& pixel : targetImage)
					{
						pixel.a = 255;
					}

					// Create new reference texture
					targetTexture = Texture{ targetImage };

					// Create new canvas with same size as reference
					canvas = Image{ targetImage.size(), Color{ 255 } };
					// Initialize distance
					initialDistance = currentDistance = Distance(targetImage, canvas);

					// Create dynamic texture with new canvas size
					canvasTexture = DynamicTexture{ canvas };
				}
			}

			// Create next state
			if (targetImage && canvas)
			{
				// Whether the canvas was updated in this trial
				bool updated = false;

				// Match candidate image to current canvas state
				candidateImage = canvas;

				// Draw random circle on candidate image
				const DistanceType newDistance = DrawRandomCircle(targetImage, candidateImage);

				// If candidate is closer, accept it
				if (newDistance < currentDistance)
				{
					canvas = candidateImage;
					currentDistance = newDistance;
					updated = true;
				}

				// If canvas was updated
				if (updated)
				{
					// Update dynamic texture with new canvas content
					canvasTexture.fill(canvas);
				}
			}

			// Display distance and progress
			{
				const double progress = initialDistance ? (1.0 - (static_cast<double>(currentDistance) / initialDistance)) : 0.0; // Progress rate
				const String currentText = ThousandsSeparate(currentDistance); // Current distance (comma separated)
				const String progressText = U" ({:.3f}%)"_fmt(progress * 100.0); // Progress rate (%)
				// Draw distance text
				font(currentText + progressText).draw(TextStyle::OutlineShadow(0.0, 0.15, ColorF{ 0.0 }, Vec2{ 0.5, 0.5 }, ColorF{ 0.0 }), 32, Vec2{ 200, 20 }, Palette::White);
			}
		}
	}
	```

## 6. Draw Colored Circles
- Up to this point, the circles were gray. Now let's extract colors from the reference image.
- We pick the reference color at the circle's center coordinates and then randomly vary the color tone and transparency slightly before painting.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/24/6.png)

??? note "Code"
	```cpp hl_lines="66-75"
	# include <Siv3D.hpp>

	// Distance type (Sum of absolute differences of pixel colors, max 255 * 3 * pixel count)
	using DistanceType = int32;

	/// @brief Returns the distance between two images (sum of absolute color differences).
	/// @param a One image
	/// @param b The other image
	/// @return Sum of absolute color differences. Returns -1 if image sizes differ.
	DistanceType Distance(const Image& a, const Image& b)
	{
		if (a.size() != b.size())
		{
			return -1;
		}

		// Loop using pointers to avoid overhead
		const size_t pixelCount = a.num_pixels();
		const Color* pA = a.data();
		const Color* pAEnd = (pA + pixelCount);
		const Color* pB = b.data();

		DistanceType result = 0;
		while (pA != pAEnd)
		{
			result += Abs(static_cast<int32>(pA->r) - static_cast<int32>(pB->r));
			result += Abs(static_cast<int32>(pA->g) - static_cast<int32>(pB->g));
			result += Abs(static_cast<int32>(pA->b) - static_cast<int32>(pB->b));
			++pA;
			++pB;
		}

		/*
		// Simple double loop version (high overhead)
		for (int32 y = 0; y < a.height(); ++y)
		{
			for (int32 x = 0; x < a.width(); ++x)
			{
				const Color colorA = a[y][x];
				const Color colorB = b[y][x];
				result += Abs(static_cast<int32>(colorA.r) - static_cast<int32>(colorB.r));
				result += Abs(static_cast<int32>(colorA.g) - static_cast<int32>(colorB.g));
				result += Abs(static_cast<int32>(colorA.b) - static_cast<int32>(colorB.b));
			}
		}
		*/

		return result;
	}

	/// @brief Draws a random circle on the canvas.
	/// @param target Reference image
	/// @param canvas Canvas image
	/// @param currentDistance Current distance
	/// @return New distance
	DistanceType DrawRandomCircle(const Image& target, Image& canvas)
	{
		// Canvas size
		const Size canvasSize = canvas.size();
		// Randomly determine circle center
		const Point center{ Random(canvasSize.x - 1), Random(canvasSize.y - 1) };
		// Randomly determine circle radius
		const int32 radius = Random(5, 30);

		// Get color from reference image
		Color color = target[center];
		// Slightly vary the color
		{
			HSV hsv{ color };
			hsv.h += Random(-10.0, 10.0); // Vary hue by ±10 degrees
			hsv.s = Clamp(hsv.s * Random(0.9, 1.1), 0.0, 1.0); // Vary saturation by 0.9 to 1.1x
			hsv.v = Clamp(hsv.v * Random(0.9, 1.1), 0.0, 1.0); // Vary value by 0.9 to 1.1x
			hsv.a = Random(0.4, 1.0); // Vary alpha randomly
			color = ColorF{ hsv };
		}

		// Paint circle onto canvas
		Circle{ center, radius }.paint(canvas, color, Antialiased::Yes);

		// Return new distance after painting
		return Distance(target, canvas);
	}

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Font for displaying distance
		const Font font{ FontMethod::MSDF, 36, Typeface::Bold };

		// Maximum canvas size (larger sizes take longer to process)
		static constexpr Size MaxCanvasSize{ 640, 720 };

		// Reference image (in main memory)
		Image targetImage;
		// Texture to draw the reference image (in VRAM)
		Texture targetTexture;

		// Canvas image (in main memory)
		Image canvas;
		// Dynamic texture to draw canvas content (in VRAM)
		DynamicTexture canvasTexture;
		// Candidate image for new canvas state (in main memory)
		Image candidateImage;

		// Distance between two images
		DistanceType initialDistance = 0; // Initial distance
		DistanceType currentDistance = 0; // Current distance

		while (System::Update())
		{
			// Draw background
			{
				Rect{ MaxCanvasSize }.draw(Arg::top(0.7), Arg::bottom(0.2));
				Rect{ MaxCanvasSize.x, 0, MaxCanvasSize }.draw(Arg::top(0.7, 0.3, 0.4), Arg::bottom(0.6, 0.1, 0.4));
			}

			// Display reference image on the left
			if (targetTexture)
			{
				targetTexture.scaled(0.92).drawAt(MaxCanvasSize / 2);
			}

			// Display canvas on the right
			if (canvasTexture)
			{
				canvasTexture.scaled(0.92).drawAt(MaxCanvasSize * Vec2{ 1.5, 0.5 });
			}

			// Button to open reference image
			if (SimpleGUI::Button(U"Open", Vec2{ 30, 30 }, 100))
			{
				if (Image image = Dialog::OpenImage()) // If image opened successfully
				{
					// Resize to fit max canvas size
					targetImage = image.fitted(MaxCanvasSize);
					// Ignore transparency and make opaque
					for (auto& pixel : targetImage)
					{
						pixel.a = 255;
					}

					// Create new reference texture
					targetTexture = Texture{ targetImage };

					// Create new canvas with same size as reference
					canvas = Image{ targetImage.size(), Color{ 255 } };
					// Initialize distance
					initialDistance = currentDistance = Distance(targetImage, canvas);

					// Create dynamic texture with new canvas size
					canvasTexture = DynamicTexture{ canvas };
				}
			}

			// Create next state
			if (targetImage && canvas)
			{
				// Whether the canvas was updated in this trial
				bool updated = false;

				// Match candidate image to current canvas state
				candidateImage = canvas;

				// Draw random circle on candidate image
				const DistanceType newDistance = DrawRandomCircle(targetImage, candidateImage);

				// If candidate is closer, accept it
				if (newDistance < currentDistance)
				{
					canvas = candidateImage;
					currentDistance = newDistance;
					updated = true;
				}

				// If canvas was updated
				if (updated)
				{
					// Update dynamic texture with new canvas content
					canvasTexture.fill(canvas);
				}
			}

			// Display distance and progress
			{
				const double progress = initialDistance ? (1.0 - (static_cast<double>(currentDistance) / initialDistance)) : 0.0; // Progress rate
				const String currentText = ThousandsSeparate(currentDistance); // Current distance (comma separated)
				const String progressText = U" ({:.3f}%)"_fmt(progress * 100.0); // Progress rate (%)
				// Draw distance text
				font(currentText + progressText).draw(TextStyle::OutlineShadow(0.0, 0.15, ColorF{ 0.0 }, Vec2{ 0.5, 0.5 }, ColorF{ 0.0 }), 32, Vec2{ 200, 20 }, Palette::White);
			}
		}
	}
	```


## 7. Try Multiple Times Per Frame
- Until now, we were only attempting to draw a circle once per frame, so it took time to complete the picture.
- Change the loop to run multiple times (here, 5 times) per frame for the "draw a circle, adopt if closer" process. This will speed up the drawing pace.
- Be careful not to increase it too much, or the frame rate will drop.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/24/7.png)

??? note "Code"
	```cpp hl_lines="163-165 179"
	# include <Siv3D.hpp>

	// Distance type (Sum of absolute differences of pixel colors, max 255 * 3 * pixel count)
	using DistanceType = int32;

	/// @brief Returns the distance between two images (sum of absolute color differences).
	/// @param a One image
	/// @param b The other image
	/// @return Sum of absolute color differences. Returns -1 if image sizes differ.
	DistanceType Distance(const Image& a, const Image& b)
	{
		if (a.size() != b.size())
		{
			return -1;
		}

		// Loop using pointers to avoid overhead
		const size_t pixelCount = a.num_pixels();
		const Color* pA = a.data();
		const Color* pAEnd = (pA + pixelCount);
		const Color* pB = b.data();

		DistanceType result = 0;
		while (pA != pAEnd)
		{
			result += Abs(static_cast<int32>(pA->r) - static_cast<int32>(pB->r));
			result += Abs(static_cast<int32>(pA->g) - static_cast<int32>(pB->g));
			result += Abs(static_cast<int32>(pA->b) - static_cast<int32>(pB->b));
			++pA;
			++pB;
		}

		/*
		// Simple double loop version (high overhead)
		for (int32 y = 0; y < a.height(); ++y)
		{
			for (int32 x = 0; x < a.width(); ++x)
			{
				const Color colorA = a[y][x];
				const Color colorB = b[y][x];
				result += Abs(static_cast<int32>(colorA.r) - static_cast<int32>(colorB.r));
				result += Abs(static_cast<int32>(colorA.g) - static_cast<int32>(colorB.g));
				result += Abs(static_cast<int32>(colorA.b) - static_cast<int32>(colorB.b));
			}
		}
		*/

		return result;
	}

	/// @brief Draws a random circle on the canvas.
	/// @param target Reference image
	/// @param canvas Canvas image
	/// @param currentDistance Current distance
	/// @return New distance
	DistanceType DrawRandomCircle(const Image& target, Image& canvas)
	{
		// Canvas size
		const Size canvasSize = canvas.size();
		// Randomly determine circle center
		const Point center{ Random(canvasSize.x - 1), Random(canvasSize.y - 1) };
		// Randomly determine circle radius
		const int32 radius = Random(5, 30);

		// Get color from reference image
		Color color = target[center];
		// Slightly vary the color
		{
			HSV hsv{ color };
			hsv.h += Random(-10.0, 10.0); // Vary hue by ±10 degrees
			hsv.s = Clamp(hsv.s * Random(0.9, 1.1), 0.0, 1.0); // Vary saturation by 0.9 to 1.1x
			hsv.v = Clamp(hsv.v * Random(0.9, 1.1), 0.0, 1.0); // Vary value by 0.9 to 1.1x
			hsv.a = Random(0.4, 1.0); // Vary alpha randomly
			color = ColorF{ hsv };
		}

		// Paint circle onto canvas
		Circle{ center, radius }.paint(canvas, color, Antialiased::Yes);

		// Return new distance after painting
		return Distance(target, canvas);
	}

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Font for displaying distance
		const Font font{ FontMethod::MSDF, 36, Typeface::Bold };

		// Maximum canvas size (larger sizes take longer to process)
		static constexpr Size MaxCanvasSize{ 640, 720 };

		// Reference image (in main memory)
		Image targetImage;
		// Texture to draw the reference image (in VRAM)
		Texture targetTexture;

		// Canvas image (in main memory)
		Image canvas;
		// Dynamic texture to draw canvas content (in VRAM)
		DynamicTexture canvasTexture;
		// Candidate image for new canvas state (in main memory)
		Image candidateImage;

		// Distance between two images
		DistanceType initialDistance = 0; // Initial distance
		DistanceType currentDistance = 0; // Current distance

		while (System::Update())
		{
			// Draw background
			{
				Rect{ MaxCanvasSize }.draw(Arg::top(0.7), Arg::bottom(0.2));
				Rect{ MaxCanvasSize.x, 0, MaxCanvasSize }.draw(Arg::top(0.7, 0.3, 0.4), Arg::bottom(0.6, 0.1, 0.4));
			}

			// Display reference image on the left
			if (targetTexture)
			{
				targetTexture.scaled(0.92).drawAt(MaxCanvasSize / 2);
			}

			// Display canvas on the right
			if (canvasTexture)
			{
				canvasTexture.scaled(0.92).drawAt(MaxCanvasSize * Vec2{ 1.5, 0.5 });
			}

			// Button to open reference image
			if (SimpleGUI::Button(U"Open", Vec2{ 30, 30 }, 100))
			{
				if (Image image = Dialog::OpenImage()) // If image opened successfully
				{
					// Resize to fit max canvas size
					targetImage = image.fitted(MaxCanvasSize);
					// Ignore transparency and make opaque
					for (auto& pixel : targetImage)
					{
						pixel.a = 255;
					}

					// Create new reference texture
					targetTexture = Texture{ targetImage };

					// Create new canvas with same size as reference
					canvas = Image{ targetImage.size(), Color{ 255 } };
					// Initialize distance
					initialDistance = currentDistance = Distance(targetImage, canvas);

					// Create dynamic texture with new canvas size
					canvasTexture = DynamicTexture{ canvas };
				}
			}

			// Create next state
			if (targetImage && canvas)
			{
				// Whether the canvas was updated in this trial
				bool updated = false;

				// Try 5 times per frame
				for (int32 i = 0; i < 5; ++i)
				{
					// Match candidate image to current canvas state
					candidateImage = canvas;

					// Draw random circle on candidate image
					const DistanceType newDistance = DrawRandomCircle(targetImage, candidateImage);

					// If candidate is closer, accept it
					if (newDistance < currentDistance)
					{
						canvas = candidateImage;
						currentDistance = newDistance;
						updated = true;
					}
				}

				// If canvas was updated
				if (updated)
				{
					// Update dynamic texture with new canvas content
					canvasTexture.fill(canvas);
				}
			}

			// Display distance and progress
			{
				const double progress = initialDistance ? (1.0 - (static_cast<double>(currentDistance) / initialDistance)) : 0.0; // Progress rate
				const String currentText = ThousandsSeparate(currentDistance); // Current distance (comma separated)
				const String progressText = U" ({:.3f}%)"_fmt(progress * 100.0); // Progress rate (%)
				// Draw distance text
				font(currentText + progressText).draw(TextStyle::OutlineShadow(0.0, 0.15, ColorF{ 0.0 }, Vec2{ 0.5, 0.5 }, ColorF{ 0.0 }), 32, Vec2{ 200, 20 }, Palette::White);
			}
		}
	}
	```

## 8. Draw Lines
- Let's change the shape we draw from "circles" to "line segments".
- By constructing the image with lines of random angles and lengths, it changes to a touch that looks like a colored pencil sketch.
- The interesting part of this program is that the texture of the generated art changes significantly just by changing the type of shapes and parameters drawn.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/24/8.png)

??? note "Code"
	```cpp hl_lines="84-121 208-209"
	# include <Siv3D.hpp>

	// Distance type (Sum of absolute differences of pixel colors, max 255 * 3 * pixel count)
	using DistanceType = int32;

	/// @brief Returns the distance between two images (sum of absolute color differences).
	/// @param a One image
	/// @param b The other image
	/// @return Sum of absolute color differences. Returns -1 if image sizes differ.
	DistanceType Distance(const Image& a, const Image& b)
	{
		if (a.size() != b.size())
		{
			return -1;
		}

		// Loop using pointers to avoid overhead
		const size_t pixelCount = a.num_pixels();
		const Color* pA = a.data();
		const Color* pAEnd = (pA + pixelCount);
		const Color* pB = b.data();

		DistanceType result = 0;
		while (pA != pAEnd)
		{
			result += Abs(static_cast<int32>(pA->r) - static_cast<int32>(pB->r));
			result += Abs(static_cast<int32>(pA->g) - static_cast<int32>(pB->g));
			result += Abs(static_cast<int32>(pA->b) - static_cast<int32>(pB->b));
			++pA;
			++pB;
		}

		/*
		// Simple double loop version (high overhead)
		for (int32 y = 0; y < a.height(); ++y)
		{
			for (int32 x = 0; x < a.width(); ++x)
			{
				const Color colorA = a[y][x];
				const Color colorB = b[y][x];
				result += Abs(static_cast<int32>(colorA.r) - static_cast<int32>(colorB.r));
				result += Abs(static_cast<int32>(colorA.g) - static_cast<int32>(colorB.g));
				result += Abs(static_cast<int32>(colorA.b) - static_cast<int32>(colorB.b));
			}
		}
		*/

		return result;
	}

	/// @brief Draws a random circle on the canvas.
	/// @param target Reference image
	/// @param canvas Canvas image
	/// @param currentDistance Current distance
	/// @return New distance
	DistanceType DrawRandomCircle(const Image& target, Image& canvas)
	{
		// Canvas size
		const Size canvasSize = canvas.size();
		// Randomly determine circle center
		const Point center{ Random(canvasSize.x - 1), Random(canvasSize.y - 1) };
		// Randomly determine circle radius
		const int32 radius = Random(5, 30);

		// Get color from reference image
		Color color = target[center];
		// Slightly vary the color
		{
			HSV hsv{ color };
			hsv.h += Random(-10.0, 10.0); // Vary hue by ±10 degrees
			hsv.s = Clamp(hsv.s * Random(0.9, 1.1), 0.0, 1.0); // Vary saturation by 0.9 to 1.1x
			hsv.v = Clamp(hsv.v * Random(0.9, 1.1), 0.0, 1.0); // Vary value by 0.9 to 1.1x
			hsv.a = Random(0.4, 1.0); // Vary alpha randomly
			color = ColorF{ hsv };
		}

		// Paint circle onto canvas
		Circle{ center, radius }.paint(canvas, color, Antialiased::Yes);

		// Return new distance after painting
		return Distance(target, canvas);
	}

	/// @brief Draws a random line segment on the canvas.
	/// @param target Reference image
	/// @param canvas Canvas image
	/// @param currentDistance Current distance
	/// @return New distance
	DistanceType DrawRandomStroke(const Image& target, Image& canvas)
	{
		// Canvas size
		const Size canvasSize = canvas.size();
		// Randomly determine line center
		const Point center{ Random(canvasSize.x - 1), Random(canvasSize.y - 1) };
		// Randomly determine line length
		const int32 length = Random(10, 50);
		// Randomly determine line angle (0 deg is 12 o'clock, clockwise)
		const double angle = Random(225_deg, 235_deg);
		// Line thickness
		const int32 thickness = 2;

		// Get color from reference image
		Color color = target[center];
		// Slightly vary the color
		{
			HSV hsv{ color };
			hsv.h += Random(-10.0, 10.0); // Vary hue by ±10 degrees
			hsv.s = Clamp(hsv.s * Random(0.9, 1.1), 0.0, 1.0); // Vary saturation by 0.9 to 1.1x
			hsv.v = Clamp(hsv.v * Random(0.9, 1.1), 0.0, 1.0); // Vary value by 0.9 to 1.1x
			hsv.a = Random(0.4, 1.0); // Vary alpha randomly
			color = ColorF{ hsv };
		}

		// Paint line segment onto canvas
		const Vec2 p0 = center + Circular{ (length / 2.0), (angle - 180_deg) }.toVec2(); // Line start point
		const Vec2 p1 = center + Circular{ (length / 2.0), angle }.toVec2(); // Line end point
		Line{ p0, p1 }.paint(canvas, thickness, color);

		// Return new distance after painting
		return Distance(target, canvas);
	}

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Font for displaying distance
		const Font font{ FontMethod::MSDF, 36, Typeface::Bold };

		// Maximum canvas size (larger sizes take longer to process)
		static constexpr Size MaxCanvasSize{ 640, 720 };

		// Reference image (in main memory)
		Image targetImage;
		// Texture to draw the reference image (in VRAM)
		Texture targetTexture;

		// Canvas image (in main memory)
		Image canvas;
		// Dynamic texture to draw canvas content (in VRAM)
		DynamicTexture canvasTexture;
		// Candidate image for new canvas state (in main memory)
		Image candidateImage;

		// Distance between two images
		DistanceType initialDistance = 0; // Initial distance
		DistanceType currentDistance = 0; // Current distance

		while (System::Update())
		{
			// Draw background
			{
				Rect{ MaxCanvasSize }.draw(Arg::top(0.7), Arg::bottom(0.2));
				Rect{ MaxCanvasSize.x, 0, MaxCanvasSize }.draw(Arg::top(0.7, 0.3, 0.4), Arg::bottom(0.6, 0.1, 0.4));
			}

			// Display reference image on the left
			if (targetTexture)
			{
				targetTexture.scaled(0.92).drawAt(MaxCanvasSize / 2);
			}

			// Display canvas on the right
			if (canvasTexture)
			{
				canvasTexture.scaled(0.92).drawAt(MaxCanvasSize * Vec2{ 1.5, 0.5 });
			}

			// Button to open reference image
			if (SimpleGUI::Button(U"Open", Vec2{ 30, 30 }, 100))
			{
				if (Image image = Dialog::OpenImage()) // If image opened successfully
				{
					// Resize to fit max canvas size
					targetImage = image.fitted(MaxCanvasSize);
					// Ignore transparency and make opaque
					for (auto& pixel : targetImage)
					{
						pixel.a = 255;
					}

					// Create new reference texture
					targetTexture = Texture{ targetImage };

					// Create new canvas with same size as reference
					canvas = Image{ targetImage.size(), Color{ 255 } };
					// Initialize distance
					initialDistance = currentDistance = Distance(targetImage, canvas);

					// Create dynamic texture with new canvas size
					canvasTexture = DynamicTexture{ canvas };
				}
			}

			// Create next state
			if (targetImage && canvas)
			{
				// Whether the canvas was updated in this trial
				bool updated = false;

				// Try 5 times per frame
				for (int32 i = 0; i < 5; ++i)
				{
					// Match candidate image to current canvas state
					candidateImage = canvas;

					// Draw random line on candidate image
					const DistanceType newDistance = DrawRandomStroke(targetImage, candidateImage);

					// If candidate is closer, accept it
					if (newDistance < currentDistance)
					{
						canvas = candidateImage;
						currentDistance = newDistance;
						updated = true;
					}
				}

				// If canvas was updated
				if (updated)
				{
					// Update dynamic texture with new canvas content
					canvasTexture.fill(canvas);
				}
			}

			// Display distance and progress
			{
				const double progress = initialDistance ? (1.0 - (static_cast<double>(currentDistance) / initialDistance)) : 0.0; // Progress rate
				const String currentText = ThousandsSeparate(currentDistance); // Current distance (comma separated)
				const String progressText = U" ({:.3f}%)"_fmt(progress * 100.0); // Progress rate (%)
				// Draw distance text
				font(currentText + progressText).draw(TextStyle::OutlineShadow(0.0, 0.15, ColorF{ 0.0 }, Vec2{ 0.5, 0.5 }, ColorF{ 0.0 }), 32, Vec2{ 200, 20 }, Palette::White);
			}
		}
	}
	```

## 9. Save Canvas Image
- Add a "Save" button to allow exporting the current canvas state as a PNG file.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/24/9.png)

??? note "Code"
	```cpp hl_lines="196-201"
	# include <Siv3D.hpp>

	// Distance type (Sum of absolute differences of pixel colors, max 255 * 3 * pixel count)
	using DistanceType = int32;

	/// @brief Returns the distance between two images (sum of absolute color differences).
	/// @param a One image
	/// @param b The other image
	/// @return Sum of absolute color differences. Returns -1 if image sizes differ.
	DistanceType Distance(const Image& a, const Image& b)
	{
		if (a.size() != b.size())
		{
			return -1;
		}

		// Loop using pointers to avoid overhead
		const size_t pixelCount = a.num_pixels();
		const Color* pA = a.data();
		const Color* pAEnd = (pA + pixelCount);
		const Color* pB = b.data();

		DistanceType result = 0;
		while (pA != pAEnd)
		{
			result += Abs(static_cast<int32>(pA->r) - static_cast<int32>(pB->r));
			result += Abs(static_cast<int32>(pA->g) - static_cast<int32>(pB->g));
			result += Abs(static_cast<int32>(pA->b) - static_cast<int32>(pB->b));
			++pA;
			++pB;
		}

		/*
		// Simple double loop version (high overhead)
		for (int32 y = 0; y < a.height(); ++y)
		{
			for (int32 x = 0; x < a.width(); ++x)
			{
				const Color colorA = a[y][x];
				const Color colorB = b[y][x];
				result += Abs(static_cast<int32>(colorA.r) - static_cast<int32>(colorB.r));
				result += Abs(static_cast<int32>(colorA.g) - static_cast<int32>(colorB.g));
				result += Abs(static_cast<int32>(colorA.b) - static_cast<int32>(colorB.b));
			}
		}
		*/

		return result;
	}

	/// @brief Draws a random circle on the canvas.
	/// @param target Reference image
	/// @param canvas Canvas image
	/// @param currentDistance Current distance
	/// @return New distance
	DistanceType DrawRandomCircle(const Image& target, Image& canvas)
	{
		// Canvas size
		const Size canvasSize = canvas.size();
		// Randomly determine circle center
		const Point center{ Random(canvasSize.x - 1), Random(canvasSize.y - 1) };
		// Randomly determine circle radius
		const int32 radius = Random(5, 30);

		// Get color from reference image
		Color color = target[center];
		// Slightly vary the color
		{
			HSV hsv{ color };
			hsv.h += Random(-10.0, 10.0); // Vary hue by ±10 degrees
			hsv.s = Clamp(hsv.s * Random(0.9, 1.1), 0.0, 1.0); // Vary saturation by 0.9 to 1.1x
			hsv.v = Clamp(hsv.v * Random(0.9, 1.1), 0.0, 1.0); // Vary value by 0.9 to 1.1x
			hsv.a = Random(0.4, 1.0); // Vary alpha randomly
			color = ColorF{ hsv };
		}

		// Paint circle onto canvas
		Circle{ center, radius }.paint(canvas, color, Antialiased::Yes);

		// Return new distance after painting
		return Distance(target, canvas);
	}

	/// @brief Draws a random line segment on the canvas.
	/// @param target Reference image
	/// @param canvas Canvas image
	/// @param currentDistance Current distance
	/// @return New distance
	DistanceType DrawRandomStroke(const Image& target, Image& canvas)
	{
		// Canvas size
		const Size canvasSize = canvas.size();
		// Randomly determine line center
		const Point center{ Random(canvasSize.x - 1), Random(canvasSize.y - 1) };
		// Randomly determine line length
		const int32 length = Random(10, 50);
		// Randomly determine line angle (0 deg is 12 o'clock, clockwise)
		const double angle = Random(225_deg, 235_deg);
		// Line thickness
		const int32 thickness = 2;

		// Get color from reference image
		Color color = target[center];
		// Slightly vary the color
		{
			HSV hsv{ color };
			hsv.h += Random(-10.0, 10.0); // Vary hue by ±10 degrees
			hsv.s = Clamp(hsv.s * Random(0.9, 1.1), 0.0, 1.0); // Vary saturation by 0.9 to 1.1x
			hsv.v = Clamp(hsv.v * Random(0.9, 1.1), 0.0, 1.0); // Vary value by 0.9 to 1.1x
			hsv.a = Random(0.4, 1.0); // Vary alpha randomly
			color = ColorF{ hsv };
		}

		// Paint line segment onto canvas
		const Vec2 p0 = center + Circular{ (length / 2.0), (angle - 180_deg) }.toVec2(); // Line start point
		const Vec2 p1 = center + Circular{ (length / 2.0), angle }.toVec2(); // Line end point
		Line{ p0, p1 }.paint(canvas, thickness, color);

		// Return new distance after painting
		return Distance(target, canvas);
	}

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Font for displaying distance
		const Font font{ FontMethod::MSDF, 36, Typeface::Bold };

		// Maximum canvas size (larger sizes take longer to process)
		static constexpr Size MaxCanvasSize{ 640, 720 };

		// Reference image (in main memory)
		Image targetImage;
		// Texture to draw the reference image (in VRAM)
		Texture targetTexture;

		// Canvas image (in main memory)
		Image canvas;
		// Dynamic texture to draw canvas content (in VRAM)
		DynamicTexture canvasTexture;
		// Candidate image for new canvas state (in main memory)
		Image candidateImage;

		// Distance between two images
		DistanceType initialDistance = 0; // Initial distance
		DistanceType currentDistance = 0; // Current distance

		while (System::Update())
		{
			// Draw background
			{
				Rect{ MaxCanvasSize }.draw(Arg::top(0.7), Arg::bottom(0.2));
				Rect{ MaxCanvasSize.x, 0, MaxCanvasSize }.draw(Arg::top(0.7, 0.3, 0.4), Arg::bottom(0.6, 0.1, 0.4));
			}

			// Display reference image on the left
			if (targetTexture)
			{
				targetTexture.scaled(0.92).drawAt(MaxCanvasSize / 2);
			}

			// Display canvas on the right
			if (canvasTexture)
			{
				canvasTexture.scaled(0.92).drawAt(MaxCanvasSize * Vec2{ 1.5, 0.5 });
			}

			// Button to open reference image
			if (SimpleGUI::Button(U"Open", Vec2{ 30, 30 }, 100))
			{
				if (Image image = Dialog::OpenImage()) // If image opened successfully
				{
					// Resize to fit max canvas size
					targetImage = image.fitted(MaxCanvasSize);
					// Ignore transparency and make opaque
					for (auto& pixel : targetImage)
					{
						pixel.a = 255;
					}

					// Create new reference texture
					targetTexture = Texture{ targetImage };

					// Create new canvas with same size as reference
					canvas = Image{ targetImage.size(), Color{ 255 } };
					// Initialize distance
					initialDistance = currentDistance = Distance(targetImage, canvas);

					// Create dynamic texture with new canvas size
					canvasTexture = DynamicTexture{ canvas };
				}
			}

			// Button to save canvas
			if (canvas && SimpleGUI::Button(U"Save", Vec2{ 30, 70 }, 100))
			{
				// Save image using file dialog
				canvas.saveWithDialog();
			}

			// Create next state
			if (targetImage && canvas)
			{
				// Whether the canvas was updated in this trial
				bool updated = false;

				// Try 5 times per frame
				for (int32 i = 0; i < 5; ++i)
				{
					// Match candidate image to current canvas state
					candidateImage = canvas;

					// Draw random line on candidate image
					const DistanceType newDistance = DrawRandomStroke(targetImage, candidateImage);

					// If candidate is closer, accept it
					if (newDistance < currentDistance)
					{
						canvas = candidateImage;
						currentDistance = newDistance;
						updated = true;
					}
				}

				// If canvas was updated
				if (updated)
				{
					// Update dynamic texture with new canvas content
					canvasTexture.fill(canvas);
				}
			}

			// Display distance and progress
			{
				const double progress = initialDistance ? (1.0 - (static_cast<double>(currentDistance) / initialDistance)) : 0.0; // Progress rate
				const String currentText = ThousandsSeparate(currentDistance); // Current distance (comma separated)
				const String progressText = U" ({:.3f}%)"_fmt(progress * 100.0); // Progress rate (%)
				// Draw distance text
				font(currentText + progressText).draw(TextStyle::OutlineShadow(0.0, 0.15, ColorF{ 0.0 }, Vec2{ 0.5, 0.5 }, ColorF{ 0.0 }), 32, Vec2{ 200, 20 }, Palette::White);
			}
		}
	}
	```


## 10. Optimization Hints
- On Windows, running the Release build in Visual Studio will speed it up several times.
- The current code rescans "all pixels of the entire image" to recalculate the distance every time a small circle is drawn.
- You can significantly reduce the computational load by changing this to compare only the "rectangular area (bounding box) changed by drawing."
- Implementation hint: Pass `currentDistance` as an argument to `DrawRandomCircle` or `DrawRandomStroke`. Inside the function, instead of looking at the whole image, calculate the increase/decrease (difference) in distance only within the range of the drawn shape, and return the value reflected in `currentDistance`.


## 11. Useful Tutorials
- [Tutorial 8. Changing the Background Color](../tutorial/background.md){:target="_blank"}
	- Explains the HSV color system.
- [Tutorial 26. Drawing Shapes](../tutorial2/shape.md){:target="_blank"}
	- Various shape classes have a `.paint()` member function that allows you to draw directly onto an image.
- [Tutorial 38. GUI](../tutorial2/gui.md){:target="_blank"}
	- Explains how to use various GUI components like radio buttons, sliders, and color pickers.
- [Tutorial 39. Random](../tutorial2/random.md){:target="_blank"}
- [Tutorial 63. Image Processing](../tutorial4/image.md){:target="_blank"}