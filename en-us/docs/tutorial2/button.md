# 28. Buttons
Using the content from tutorials 3-27, we'll create buttons that can be used in games and applications.

## 28.1 Basic Function
- Create a `Button` function that draws a rectangle as a button and returns whether the button was pressed
- For design purposes, draw it as a rectangle with slightly rounded corners
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/button/1.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	bool Button(const Rect& rect)
	{
		const RoundRect roundRect = rect.rounded(6);

		// Draw the background
		roundRect.draw(ColorF{ 0.9, 0.8, 0.6 });

		// Return true if the button is pressed
		return rect.leftClicked();
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		while (System::Update())
		{
			if (Button(Rect{ 80, 300, 300, 80 }))
			{
				Print << U"A";
			}

			if (Button(Rect{ 420, 300, 300, 80 }))
			{
				Print << U"B";
			}
		}
	}
	```


## 28.2 Adding Decorations
- To improve the button's appearance, add shadows and borders
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/button/2.png)

??? memo "Code"
	```cpp hl_lines="7-14"
	# include <Siv3D.hpp>

	bool Button(const Rect& rect)
	{
		const RoundRect roundRect = rect.rounded(6);

		// Draw shadow and background
		roundRect
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw(ColorF{ 0.9, 0.8, 0.6 });

		// Draw border
		rect.stretched(-3).rounded(3)
			.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

		// Return true if the button is pressed
		return rect.leftClicked();
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		while (System::Update())
		{
			if (Button(Rect{ 80, 300, 300, 80 }))
			{
				Print << U"A";
			}

			if (Button(Rect{ 420, 300, 300, 80 }))
			{
				Print << U"B";
			}
		}
	}
	```


## 28.3 Adding Text
- Pass font and string to draw text on the button
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/button/3.png)

??? memo "Code"
	```cpp hl_lines="3 16-17 27 31 33 36 38"
	# include <Siv3D.hpp>

	bool Button(const Rect& rect, const Font& font, const String& text)
	{
		const RoundRect roundRect = rect.rounded(6);

		// Draw shadow and background
		roundRect
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw(ColorF{ 0.9, 0.8, 0.6 });

		// Draw border
		rect.stretched(-3).rounded(3)
			.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

		// Draw text
		font(text).drawAt(40, rect.center(), ColorF{ 0.4, 0.3, 0.2 });

		// Return true if the button is pressed
		return rect.leftClicked();
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		while (System::Update())
		{
			if (Button(Rect{ 80, 300, 300, 80 }, font, U"Bread"))
			{
				Print << U"Bread";
			}

			if (Button(Rect{ 420, 300, 300, 80 }, font, U"Rice"))
			{
				Print << U"Rice";
			}
		}
	}
	```


## 28.4 Changing Mouse Cursor
- When the mouse cursor is over the button, change it to a hand shape
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/button/4.png)

??? memo "Code"
	```cpp hl_lines="5-10"
	# include <Siv3D.hpp>

	bool Button(const Rect& rect, const Font& font, const String& text)
	{
		// If the mouse cursor is over the button
		if (rect.mouseOver())
		{
			// Change the mouse cursor to a hand
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		const RoundRect roundRect = rect.rounded(6);

		// Draw shadow and background
		roundRect
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw(ColorF{ 0.9, 0.8, 0.6 });

		// Draw border
		rect.stretched(-3).rounded(3)
			.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

		// Draw text
		font(text).drawAt(40, rect.center(), ColorF{ 0.4, 0.3, 0.2 });

		// Return true if the button is pressed
		return rect.leftClicked();
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		while (System::Update())
		{
			if (Button(Rect{ 80, 300, 300, 80 }, font, U"Bread"))
			{
				Print << U"Bread";
			}

			if (Button(Rect{ 420, 300, 300, 80 }, font, U"Rice"))
			{
				Print << U"Rice";
			}
		}
	}
	```


## 28.5 Adding Disabled State
- When the button is disabled, overlay gray semi-transparent color and disable interaction
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/button/5.png)

??? memo "Code"
	```cpp hl_lines="3 6 26-31 45 50"
	# include <Siv3D.hpp>

	bool Button(const Rect& rect, const Font& font, const String& text, bool enabled)
	{
		// If the mouse cursor is over the button
		if (enabled && rect.mouseOver())
		{
			// Change the mouse cursor to a hand
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		const RoundRect roundRect = rect.rounded(6);

		// Draw shadow and background
		roundRect
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw(ColorF{ 0.9, 0.8, 0.6 });

		// Draw border
		rect.stretched(-3).rounded(3)
			.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

		// Draw text
		font(text).drawAt(40, rect.center(), ColorF{ 0.4, 0.3, 0.2 });

		// If disabled
		if (not enabled)
		{
			// Overlay gray semi-transparent color
			roundRect.draw(ColorF{ 0.8, 0.8 });
		}

		// Return true if the button is pressed
		return (enabled && rect.leftClicked());
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		while (System::Update())
		{
			if (Button(Rect{ 80, 300, 300, 80 }, font, U"Bread", true))
			{
				Print << U"Bread";
			}

			if (Button(Rect{ 420, 300, 300, 80 }, font, U"Rice", true))
			{
				Print << U"Rice";
			}
		}
	}
	```


## 28.6 【Complete】Adding Emojis
- Add emojis to the buttons
- Adjust text position for emoji display
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/button/6.png)

??? memo "Code"
	```cpp hl_lines="3 23-24 44-45 51 56"
	# include <Siv3D.hpp>

	bool Button(const Rect& rect, const Texture& emoji, const Font& font, const String& text, bool enabled)
	{
		// If the mouse cursor is over the button
		if (enabled && rect.mouseOver())
		{
			// Change the mouse cursor to a hand
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		const RoundRect roundRect = rect.rounded(6);

		// Draw shadow and background
		roundRect
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw(ColorF{ 0.9, 0.8, 0.6 });

		// Draw border
		rect.stretched(-3).rounded(3)
			.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

		// Draw emoji
		emoji.scaled(0.4).drawAt((rect.x + 60), rect.center().y);

		// Draw text
		font(text).drawAt(40, rect.center().movedBy(30, 0), ColorF{ 0.4, 0.3, 0.2 });

		// If disabled
		if (not enabled)
		{
			// Overlay gray semi-transparent color
			roundRect.draw(ColorF{ 0.8, 0.8 });
		}

		// Return true if the button is pressed
		return (enabled && rect.leftClicked());
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Texture breadEmoji{ U"🍞"_emoji };
		const Texture riceEmoji{ U"🍚"_emoji };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		while (System::Update())
		{
			if (Button(Rect{ 80, 300, 300, 80 }, breadEmoji, font, U"Bread", true))
			{
				Print << U"Bread";
			}

			if (Button(Rect{ 420, 300, 300, 80 }, riceEmoji, font, U"Rice", true))
			{
				Print << U"Rice";
			}
		}
	}
	```