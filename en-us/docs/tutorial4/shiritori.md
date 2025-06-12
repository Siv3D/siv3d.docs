# 68. AI Drawing Word Chain Game
Create a word chain game using drawings by leveraging OpenAI's Vision API. The AI judges the drawn illustrations.

!!! info "OpenAI API Key Required"
	- Completing the program in this chapter requires the OpenAI API key obtained in **Tutorial 67**

## 68.1 Game Rules
- Think of a word that starts with the specified alphabet (e.g., A) and draw a picture
- If the AI can understand the drawn picture, it's OK. Use the last letter of that word to think of the next word

### Completed Image (Click to Play)

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">今日の <a href="https://twitter.com/hashtag/cppmix?src=hash&amp;ref_src=twsrc%5Etfw">#cppmix</a> で発表した AI 絵しりとり！<br>描いた絵を AI が判定して、AI がわからなかったらゲームオーバー。<a href="https://twitter.com/hashtag/Siv3D?src=hash&amp;ref_src=twsrc%5Etfw">#Siv3D</a> <a href="https://t.co/3IGEbZj9A4">pic.twitter.com/3IGEbZj9A4</a></p>&mdash; Ryo Suzuki (@Reputeless) <a href="https://twitter.com/Reputeless/status/1801640858263163192?ref_src=twsrc%5Etfw">June 14, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


## 68.2 Screen Size and Background
- Set the screen size and background color

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/2.png)

??? note "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		while (System::Update())
		{

		}
	}
	```

## 68.3 Background Checkerboard Pattern
- Draw a background checkerboard pattern by arranging squares

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/3.png)

??? note "Code"
	```cpp hl_lines="3-20 32-33"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// Number of horizontal and vertical cells
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// Draw square only when (x + y) is even
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		while (System::Update())
		{
			// Draw background checkerboard pattern
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });
		}
	}
	```

## 68.4 Paint Image
- Prepare editable image data `Image` for painting
- Prepare `DynamicTexture` for drawing that image to the scene

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/4.png)

??? note "Code"
	```cpp hl_lines="22-25 35-42 49-50"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// Number of horizontal and vertical cells
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// Draw square only when (x + y) is even
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture)
	{
		texture.draw();
	}

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Canvas size
		const Size canvasSize{ 512, 512 };

		// Paint image
		Image image{ canvasSize, Palette::White };

		// Create texture from paint image
		DynamicTexture texture{ image };

		while (System::Update())
		{
			// Draw background checkerboard pattern
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// Draw canvas
			DrawCanvas(texture);
		}
	}
	```


## 68.5 Canvas
- Use the `RoundRect` class to prepare a rounded rectangle and draw the painted texture along it
- Use `RoundRect`'s `.drawFrame(inner thickness, outer thickness, color)` to draw the canvas frame

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/5.png)

??? note "Code"
	```cpp hl_lines="22 24-31 42-43 60"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// Number of horizontal and vertical cells
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// Draw square only when (x + y) is even
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// Rounded rectangle
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// Draw paint result along the rounded rectangle
		rrect(texture).draw();

		// Draw rounded rectangle frame
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Canvas top-left position
		const Point canvasPos{ 100, 60 };

		// Canvas size
		const Size canvasSize{ 512, 512 };

		// Paint image
		Image image{ canvasSize, Palette::White };

		// Create texture from paint image
		DynamicTexture texture{ image };

		while (System::Update())
		{
			// Draw background checkerboard pattern
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// Draw canvas
			DrawCanvas(texture, canvasPos);
		}
	}
	```


## 68.6 Painting
- While the left mouse button is pressed, draw lines on the image
- `Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);` draws lines on the image
- `.movedBy(-canvasPos)` is processing to align the canvas position with the actual image position
- `texture.fill(image);` updates the `DynamicTexture` content with the new image

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/6.png)

??? note "Code"
	```cpp hl_lines="34-45 69-70"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// Number of horizontal and vertical cells
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// Draw square only when (x + y) is even
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// Rounded rectangle
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// Draw paint result along the rounded rectangle
		rrect(texture).draw();

		// Draw rounded rectangle frame
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// Update texture content
			texture.fill(image);
		}
	}

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Canvas top-left position
		const Point canvasPos{ 100, 60 };

		// Canvas size
		const Size canvasSize{ 512, 512 };

		// Paint image
		Image image{ canvasSize, Palette::White };

		// Create texture from paint image
		DynamicTexture texture{ image };

		while (System::Update())
		{
			// Perform painting
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// Draw background checkerboard pattern
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// Draw canvas
			DrawCanvas(texture, canvasPos);
		}
	}
	```


## 68.7 Canvas Clear
- Create an image clear button using SimpleGUI
- Clear the canvas when the clear button is pressed
- `image.fill(color);` fills the image with the specified color

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/7.png)

??? note "Code"
	```cpp hl_lines="47-53 83-88"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// Number of horizontal and vertical cells
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// Draw square only when (x + y) is even
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// Rounded rectangle
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// Draw paint result along the rounded rectangle
		rrect(texture).draw();

		// Draw rounded rectangle frame
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// Update texture content
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// Update texture content
		texture.fill(image);
	}

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Canvas top-left position
		const Point canvasPos{ 100, 60 };

		// Canvas size
		const Size canvasSize{ 512, 512 };

		// Paint image
		Image image{ canvasSize, Palette::White };

		// Create texture from paint image
		DynamicTexture texture{ image };

		while (System::Update())
		{
			// Perform painting
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// Draw background checkerboard pattern
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// When clear button is pressed
			if (SimpleGUI::Button(U"Clear", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// Clear canvas
				ClearCanvas(image, texture, Palette::White);
			}

			// Draw canvas
			DrawCanvas(texture, canvasPos);
		}
	}
	```


## 68.8 Topic Character
- Prepare a variable `targetChar` to represent the topic character
- Prepare a font to use for drawing characters
- Use `font(character or text).drawAt(size, center position, color);` to draw characters

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/8.png)

??? note "Code"
	```cpp hl_lines="34-46 77-78 92-93 113-114"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// Number of horizontal and vertical cells
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// Draw square only when (x + y) is even
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// Rounded rectangle
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// Draw paint result along the rounded rectangle
		rrect(texture).draw();

		// Draw rounded rectangle frame
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// Circle for topic display
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// Draw circle
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// Draw topic character
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// Update texture content
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// Update texture content
		texture.fill(image);
	}

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Prepare font
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

		// Canvas top-left position
		const Point canvasPos{ 100, 60 };

		// Canvas size
		const Size canvasSize{ 512, 512 };

		// Paint image
		Image image{ canvasSize, Palette::White };

		// Create texture from paint image
		DynamicTexture texture{ image };

		// Topic character
		char32 targetChar = U'C';

		while (System::Update())
		{
			// Perform painting
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// Draw background checkerboard pattern
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// When clear button is pressed
			if (SimpleGUI::Button(U"Clear", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// Clear canvas
				ClearCanvas(image, texture, Palette::White);
			}

			// Draw canvas
			DrawCanvas(texture, canvasPos);

			// Draw topic character
			DrawTargetCharacter(targetChar, canvasPos, font);
		}
	}
	```


## 68.9 Communication Setup
- Prepare `AsyncHTTPTask` class for asynchronous communication with OpenAI server
- Place a "Judge" button for having the AI judge the drawing
- Make the judge button disabled during communication

??? note "Code"
	```cpp hl_lines="95-96 106-111"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// Number of horizontal and vertical cells
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// Draw square only when (x + y) is even
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// Rounded rectangle
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// Draw paint result along the rounded rectangle
		rrect(texture).draw();

		// Draw rounded rectangle frame
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// Circle for topic display
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// Draw circle
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// Draw topic character
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// Update texture content
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// Update texture content
		texture.fill(image);
	}

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Prepare font
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

		// Canvas top-left position
		const Point canvasPos{ 100, 60 };

		// Canvas size
		const Size canvasSize{ 512, 512 };

		// Paint image
		Image image{ canvasSize, Palette::White };

		// Create texture from paint image
		DynamicTexture texture{ image };

		// Topic character
		char32 targetChar = U'C';

		// Asynchronous task
		AsyncHTTPTask task;

		while (System::Update())
		{
			// Perform painting
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// Draw background checkerboard pattern
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// When send button is pressed
			if (SimpleGUI::Button(U"Judge", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // Enable button when not waiting for judgment result
			{

			}

			// When clear button is pressed
			if (SimpleGUI::Button(U"Clear", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// Clear canvas
				ClearCanvas(image, texture, Palette::White);
			}

			// Draw canvas
			DrawCanvas(texture, canvasPos);

			// Draw topic character
			DrawTargetCharacter(targetChar, canvasPos, font);
		}
	}
	```


## 68.10 Creating Requests
- Create a request `OpenAI::Vision::Request` to send to OpenAI's Vision API
- Add images to the array `.images`
- Set the question text about the image in `.prompt`

```txt title="Prompt Japanese Translation"
What is drawn in the image? The answer starts with the letter "{}".
Write only the answer. Commas and periods are prohibited. If you don't know, output only a question mark.
```

??? note "Code"
	```cpp hl_lines="110-123"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// Number of horizontal and vertical cells
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// Draw square only when (x + y) is even
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// Rounded rectangle
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// Draw paint result along the rounded rectangle
		rrect(texture).draw();

		// Draw rounded rectangle frame
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// Circle for topic display
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// Draw circle
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// Draw topic character
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// Update texture content
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// Update texture content
		texture.fill(image);
	}

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Prepare font
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

		// Canvas top-left position
		const Point canvasPos{ 100, 60 };

		// Canvas size
		const Size canvasSize{ 512, 512 };

		// Paint image
		Image image{ canvasSize, Palette::White };

		// Create texture from paint image
		DynamicTexture texture{ image };

		// Topic character
		char32 targetChar = U'C';

		// Asynchronous task
		AsyncHTTPTask task;

		while (System::Update())
		{
			// Perform painting
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// Draw background checkerboard pattern
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// When send button is pressed
			if (SimpleGUI::Button(U"Judge", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // Enable button when not waiting for judgment result
			{
				// Prompt
				String prompt = U"What is drawn in the image? The answer starts with the letter {}. "_fmt(targetChar);
				prompt += U"Write only the answer. Commas and periods are prohibited. If you don't know, output only a question mark.";

				// Request
				OpenAI::Vision::Request request;

				// Set prompt to request
				request.questions = prompt;

				// Attach image to request
				request.images << OpenAI::Vision::ImageData::Base64FromImage(image);


			}

			// When clear button is pressed
			if (SimpleGUI::Button(U"Clear", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// Clear canvas
				ClearCanvas(image, texture, Palette::White);
			}

			// Draw canvas
			DrawCanvas(texture, canvasPos);

			// Draw topic character
			DrawTargetCharacter(targetChar, canvasPos, font);
		}
	}
	```


## 68.11 AI Interaction
- Create an asynchronous task with `OpenAI::Vision::CompleteAsync`
- When the task completes successfully, get the result in uppercase

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/11.png)

??? note "Code"
	```cpp hl_lines="77-78 126-127 137-145"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// Number of horizontal and vertical cells
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// Draw square only when (x + y) is even
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// Rounded rectangle
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// Draw paint result along the rounded rectangle
		rrect(texture).draw();

		// Draw rounded rectangle frame
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// Circle for topic display
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// Draw circle
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// Draw topic character
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// Update texture content
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// Update texture content
		texture.fill(image);
	}

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// OpenAI API key
		const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

		// Prepare font
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

		// Canvas top-left position
		const Point canvasPos{ 100, 60 };

		// Canvas size
		const Size canvasSize{ 512, 512 };

		// Paint image
		Image image{ canvasSize, Palette::White };

		// Create texture from paint image
		DynamicTexture texture{ image };

		// Topic character
		char32 targetChar = U'C';

		// Asynchronous task
		AsyncHTTPTask task;

		while (System::Update())
		{
			// Perform painting
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// Draw background checkerboard pattern
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// When send button is pressed
			if (SimpleGUI::Button(U"Judge", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // Enable button when not waiting for judgment result
			{
				// Prompt
				String prompt = U"What is drawn in this image? The answer starts with the letter {}. "_fmt(targetChar);
				prompt += U"Write only the answer. Commas and periods are prohibited. If you don't know, output only a question mark.";

				// Request
				OpenAI::Vision::Request request;

				// Set prompt to request
				request.questions = prompt;

				// Attach image to request
				request.images << OpenAI::Vision::ImageData::Base64FromImage(image);

				// Create task
				task = OpenAI::Vision::CompleteAsync(API_KEY, request);
			}

			// When clear button is pressed
			if (SimpleGUI::Button(U"Clear", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// Clear canvas
				ClearCanvas(image, texture, Palette::White);
			}

			// When asynchronous processing is complete and response is OK
			if (task.isReady() && task.getResponse().isOK())
			{
				// Get result
				const String answer = OpenAI::Vision::GetContent(task.getAsJSON()).uppercase();

				// Simple display of result
				Print << answer;
			}

			// Draw canvas
			DrawCanvas(texture, canvasPos);

			// Draw topic character
			DrawTargetCharacter(targetChar, canvasPos, font);
		}
	}
	```


## 68.12 Game Progress
- Record and display recent word chain history
- Proceed to the next character when correct

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/12.png)

??? note "Code"
	```cpp hl_lines="48-54 109-110 154-171 180-181"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// Number of horizontal and vertical cells
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// Draw square only when (x + y) is even
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// Rounded rectangle
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// Draw paint result along the rounded rectangle
		rrect(texture).draw();

		// Draw rounded rectangle frame
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// Circle for topic display
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// Draw circle
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// Draw topic character
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void DrawRecentHistory(const Array<String>& recentWords, const Font& font)
	{
		for (auto&& [i, answer] : Indexed(recentWords))
		{
			font(answer).draw(46, Vec2{ 736, (47 + i * 80) }, ColorF{ 0.1 });
		}
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// Update texture content
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// Update texture content
		texture.fill(image);
	}

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// OpenAI API key
		const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

		// Prepare font
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

		// Canvas top-left position
		const Point canvasPos{ 100, 60 };

		// Canvas size
		const Size canvasSize{ 512, 512 };

		// Paint image
		Image image{ canvasSize, Palette::White };

		// Create texture from paint image
		DynamicTexture texture{ image };

		// Topic character
		char32 targetChar = U'C';

		// Asynchronous task
		AsyncHTTPTask task;

		// Array to store recent word chain history
		Array<String> recentWords = { String(1, targetChar) };

		while (System::Update())
		{
			// Perform painting
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// Draw background checkerboard pattern
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// When send button is pressed
			if (SimpleGUI::Button(U"Judge", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // Enable button when not waiting for judgment result
			{
				// Prompt
				String prompt = U"What is drawn in this image? The answer starts with the letter {}. "_fmt(targetChar);
				prompt += U"Write only the answer. Commas and periods are prohibited. If you don't know, output only a question mark.";

				// Request
				OpenAI::Vision::Request request;

				// Set prompt to request
				request.questions = prompt;

				// Attach image to request
				request.images << OpenAI::Vision::ImageData::Base64FromImage(image);

				// Create task
				task = OpenAI::Vision::CompleteAsync(API_KEY, request);
			}

			// When clear button is pressed
			if (SimpleGUI::Button(U"Clear", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// Clear canvas
				ClearCanvas(image, texture, Palette::White);
			}

			// When asynchronous processing is complete and response is OK
			if (task.isReady() && task.getResponse().isOK())
			{
				// Get result
				const String answer = OpenAI::Vision::GetContent(task.getAsJSON()).uppercase();

				// Update end of history
				recentWords.back() = answer;

				// If correct
				if (answer != U"?")
				{
					targetChar = answer.back();
				}

				// Add next item to history
				recentWords << String(1, targetChar);

				// If history contains more than 8 items
				if (8 < recentWords.size())
				{
					// Remove first item
					recentWords.pop_front();
				}
			}

			// Draw canvas
			DrawCanvas(texture, canvasPos);

			// Draw topic character
			DrawTargetCharacter(targetChar, canvasPos, font);

			// Draw word chain history
			DrawRecentHistory(recentWords, font);
		}
	}
	```


## 68.13 Improving History Display
- Emphasize the first character
- When history is full, let the first item go off screen

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/13.png)

??? note "Code"
	```cpp hl_lines="50-51 55-61"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// Number of horizontal and vertical cells
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// Draw square only when (x + y) is even
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// Rounded rectangle
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// Draw paint result along the rounded rectangle
		rrect(texture).draw();

		// Draw rounded rectangle frame
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// Circle for topic display
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// Draw circle
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// Draw topic character
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void DrawRecentHistory(const Array<String>& recentWords, const Font& font)
	{
		// Overflow handling when history is full
		const double yOffset = (recentWords.size() < 8) ? 0 : -70;

		for (auto&& [i, answer] : Indexed(recentWords))
		{
			// First character
			const Vec2 pos{ 700, (80 + i * 80 + yOffset) };
			Circle{ pos, 32 }.draw(ColorF{ 0.8, 0.9, 1.0 });
			font(answer.front()).drawAt(46, pos, ColorF{ 0.1 });

			// Characters after first
			font(answer.substr(1)).draw(46, Vec2{ 736, (47 + i * 80 + yOffset) }, ColorF{ 0.1 });
		}
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// Update texture content
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// Update texture content
		texture.fill(image);
	}

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// OpenAI API key
		const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

		// Prepare font
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

		// Canvas top-left position
		const Point canvasPos{ 100, 60 };

		// Canvas size
		const Size canvasSize{ 512, 512 };

		// Paint image
		Image image{ canvasSize, Palette::White };

		// Create texture from paint image
		DynamicTexture texture{ image };

		// Topic character
		char32 targetChar = U'C';

		// Asynchronous task
		AsyncHTTPTask task;

		// Array to store recent word chain history
		Array<String> recentWords = { String(1, targetChar) };

		while (System::Update())
		{
			// Perform painting
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// Draw background checkerboard pattern
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// When send button is pressed
			if (SimpleGUI::Button(U"Judge", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // Enable button when not waiting for judgment result
			{
				// Prompt
				String prompt = U"What is drawn in this image? The answer starts with the letter {}. "_fmt(targetChar);
				prompt += U"Write only the answer. Commas and periods are prohibited. If you don't know, output only a question mark.";

				// Request
				OpenAI::Vision::Request request;

				// Set prompt to request
				request.questions = prompt;

				// Attach image to request
				request.images << OpenAI::Vision::ImageData::Base64FromImage(image);

				// Create task
				task = OpenAI::Vision::CompleteAsync(API_KEY, request);
			}

			// When clear button is pressed
			if (SimpleGUI::Button(U"Clear", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// Clear canvas
				ClearCanvas(image, texture, Palette::White);
			}

			// When asynchronous processing is complete and response is OK
			if (task.isReady() && task.getResponse().isOK())
			{
				// Get result
				const String answer = OpenAI::Vision::GetContent(task.getAsJSON()).uppercase();

				// Update end of history
				recentWords.back() = answer;

				// If correct
				if (answer != U"?")
				{
					targetChar = answer.back();
				}

				// Add next item to history
				recentWords << String(1, targetChar);

				// If history contains more than 8 items
				if (8 < recentWords.size())
				{
					// Remove first item
					recentWords.pop_front();
				}
			}

			// Draw canvas
			DrawCanvas(texture, canvasPos);

			// Draw topic character
			DrawTargetCharacter(targetChar, canvasPos, font);

			// Draw word chain history
			DrawRecentHistory(recentWords, font);
		}
	}
	```


## 68.14 Score
- Record and display score

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/14.png)

??? note "Code"
	```cpp hl_lines="65-70 106 129-130 181 204-205"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// Number of horizontal and vertical cells
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// Draw square only when (x + y) is even
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// Rounded rectangle
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// Draw paint result along the rounded rectangle
		rrect(texture).draw();

		// Draw rounded rectangle frame
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// Circle for topic display
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// Draw circle
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// Draw topic character
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void DrawRecentHistory(const Array<String>& recentWords, const Font& font)
	{
		// Overflow handling when history is full
		const double yOffset = (recentWords.size() < 8) ? 0 : -70;

		for (auto&& [i, answer] : Indexed(recentWords))
		{
			// First character
			const Vec2 pos{ 700, (80 + i * 80 + yOffset) };
			Circle{ pos, 32 }.draw(ColorF{ 0.8, 0.9, 1.0 });
			font(answer.front()).drawAt(46, pos, ColorF{ 0.1 });

			// Characters after first
			font(answer.substr(1)).draw(46, Vec2{ 736, (47 + i * 80 + yOffset) }, ColorF{ 0.1 });
		}
	}

	void DrawScore(int32 score, const Font& font)
	{
		const Vec2 center = font(score).region(140, Arg::topRight(1185, 15)).center();
		font(score).draw(TextStyle::OutlineShadow(0.2, ColorF{ 1.0 }, Vec2{ 2, 2 }, ColorF{ 0.0, 0.5 }), 140,
			Arg::topRight(1185, 15), ColorF{ 1.0, 0.6, 0.1 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// Update texture content
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// Update texture content
		texture.fill(image);
	}

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// OpenAI API key
		const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

		// Prepare font
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };
		const Font font2 = Font{ FontMethod::MSDF, 40, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(4);

		// Canvas top-left position
		const Point canvasPos{ 100, 60 };

		// Canvas size
		const Size canvasSize{ 512, 512 };

		// Paint image
		Image image{ canvasSize, Palette::White };

		// Create texture from paint image
		DynamicTexture texture{ image };

		// Topic character
		char32 targetChar = U'C';

		// Asynchronous task
		AsyncHTTPTask task;

		// Array to store recent word chain history
		Array<String> recentWords = { String(1, targetChar) };

		// Score
		int32 score = 0;

		while (System::Update())
		{
			// Perform painting
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// Draw background checkerboard pattern
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// When send button is pressed
			if (SimpleGUI::Button(U"Judge", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // Enable button when not waiting for judgment result
			{
				// Prompt
				String prompt = U"What is drawn in this image? The answer starts with the letter {}. "_fmt(targetChar);
				prompt += U"Write only the answer. Commas and periods are prohibited. If you don't know, output only a question mark.";

				// Request
				OpenAI::Vision::Request request;

				// Set prompt to request
				request.questions = prompt;

				// Attach image to request
				request.images << OpenAI::Vision::ImageData::Base64FromImage(image);

				// Create task
				task = OpenAI::Vision::CompleteAsync(API_KEY, request);
			}

			// When clear button is pressed
			if (SimpleGUI::Button(U"Clear", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// Clear canvas
				ClearCanvas(image, texture, Palette::White);
			}

			// When asynchronous processing is complete and response is OK
			if (task.isReady() && task.getResponse().isOK())
			{
				// Get result
				const String answer = OpenAI::Vision::GetContent(task.getAsJSON()).uppercase();

				// Update end of history
				recentWords.back() = answer;

				// If correct
				if (answer != U"?")
				{
					targetChar = answer.back();
					++score;
				}

				// Add next item to history
				recentWords << String(1, targetChar);

				// If history contains more than 8 items
				if (8 < recentWords.size())
				{
					// Remove first item
					recentWords.pop_front();
				}
			}

			// Draw canvas
			DrawCanvas(texture, canvasPos);

			// Draw topic character
			DrawTargetCharacter(targetChar, canvasPos, font);

			// Draw word chain history
			DrawRecentHistory(recentWords, font);

			// Draw score
			DrawScore(score, font2);
		}
	}
	```


## 68.15 Minor Improvements
- Make the first topic character randomly selected
- Display a rotating ring while waiting for AI response

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/15.png)

??? note "Code"
	```cpp hl_lines="48 63-71 131 212"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// Number of horizontal and vertical cells
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// Draw square only when (x + y) is even
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// Rounded rectangle
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// Draw paint result along the rounded rectangle
		rrect(texture).draw();

		// Draw rounded rectangle frame
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// Circle for topic display
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// Draw circle
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// Draw topic character
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void DrawRecentHistory(const Array<String>& recentWords, const Font& font, bool isWaiting)
	{
		// Overflow handling when history is full
		const double yOffset = (recentWords.size() < 8) ? 0 : -70;

		for (auto&& [i, answer] : Indexed(recentWords))
		{
			// First character
			const Vec2 pos{ 700, (80 + i * 80 + yOffset) };
			Circle{ pos, 32 }.draw(ColorF{ 0.8, 0.9, 1.0 });
			font(answer.front()).drawAt(46, pos, ColorF{ 0.1 });

			// Characters after first
			font(answer.substr(1)).draw(46, Vec2{ 736, (47 + i * 80 + yOffset) }, ColorF{ 0.1 });

			// When waiting for response
			if (isWaiting)
			{
				// Draw rotating ring around the last character
				if (i == recentWords.size() - 1)
				{
					Circle{ pos, 42 }.drawArc((Scene::Time() * 240_deg), 300_deg, 5, 2, ColorF{ 0.8, 0.9, 1.0 });
				}
			}
		}
	}

	void DrawScore(int32 score, const Font& font)
	{
		const Vec2 center = font(score).region(140, Arg::topRight(1185, 15)).center();
		font(score).draw(TextStyle::OutlineShadow(0.2, ColorF{ 1.0 }, Vec2{ 2, 2 }, ColorF{ 0.0, 0.5 }), 140,
			Arg::topRight(1185, 15), ColorF{ 1.0, 0.6, 0.1 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// Update texture content
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// Update texture content
		texture.fill(image);
	}

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// OpenAI API key
		const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

		// Prepare font
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };
		const Font font2 = Font{ FontMethod::MSDF, 40, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(4);

		// Canvas top-left position
		const Point canvasPos{ 100, 60 };

		// Canvas size
		const Size canvasSize{ 512, 512 };

		// Paint image
		Image image{ canvasSize, Palette::White };

		// Create texture from paint image
		DynamicTexture texture{ image };

		// Topic character
		char32 targetChar = Random(U'A', U'Z');

		// Asynchronous task
		AsyncHTTPTask task;

		// Array to store recent word chain history
		Array<String> recentWords = { String(1, targetChar) };

		// Score
		int32 score = 0;

		while (System::Update())
		{
			// Perform painting
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// Draw background checkerboard pattern
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// When send button is pressed
			if (SimpleGUI::Button(U"Judge", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // Enable button when not waiting for judgment result
			{
				// Prompt
				String prompt = U"What is drawn in this image? The answer starts with the letter {}. "_fmt(targetChar);
				prompt += U"Write only the answer. Commas and periods are prohibited. If you don't know, output only a question mark.";

				// Request
				OpenAI::Vision::Request request;

				// Set prompt to request
				request.questions = prompt;

				// Attach image to request
				request.images << OpenAI::Vision::ImageData::Base64FromImage(image);

				// Create task
				task = OpenAI::Vision::CompleteAsync(API_KEY, request);
			}

			// When clear button is pressed
			if (SimpleGUI::Button(U"Clear", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// Clear canvas
				ClearCanvas(image, texture, Palette::White);
			}

			// When asynchronous processing is complete and response is OK
			if (task.isReady() && task.getResponse().isOK())
			{
				// Get result
				const String answer = OpenAI::Vision::GetContent(task.getAsJSON()).uppercase();

				// Update end of history
				recentWords.back() = answer;

				// If correct
				if (answer != U"?")
				{
					targetChar = answer.back();
					++score;
				}

				// Add next item to history
				recentWords << String(1, targetChar);

				// If history contains more than 8 items
				if (8 < recentWords.size())
				{
					// Remove first item
					recentWords.pop_front();
				}
			}

			// Draw canvas
			DrawCanvas(texture, canvasPos);

			// Draw topic character
			DrawTargetCharacter(targetChar, canvasPos, font);

			// Draw word chain history
			DrawRecentHistory(recentWords, font, task.isDownloading());

			// Draw score
			DrawScore(score, font2);
		}
	}
	```