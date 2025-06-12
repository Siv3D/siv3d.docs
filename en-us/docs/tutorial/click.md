# 20. Click Game
Create a game where you click items using the content from Tutorials 3-19.

## 20.1 Item Drawing and Click Detection
- Prepare a target emoji and a circle for click detection
- When clicked, move to a random position

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/click/1.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Function that returns random coordinates
	Vec2 GetRandomPos()
	{
		return{ Random(60, 740), Random(60, 540) };
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Click target emoji
		const Texture targetEmoji{ U"🍎"_emoji };

		// Click target circle
		Circle targetCircle{ 400, 300, 60 };

		while (System::Update())
		{
			// If click target is clicked
			if (targetCircle.leftClicked())
			{
				// Change click target position to random position
				targetCircle.center = GetRandomPos();
			}

			// Draw click target
			targetEmoji.drawAt(targetCircle.center);
		}
	}
	```


## 20.2 Mouse Over Detection
- When the click target is being moused over, change the mouse cursor to a hand shape

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/click/2.png)

??? memo "Code"
	```cpp hl_lines="21-26"
	# include <Siv3D.hpp>

	// Function that returns random coordinates
	Vec2 GetRandomPos()
	{
		return{ Random(60, 740), Random(60, 540) };
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Click target emoji
		const Texture targetEmoji{ U"🍎"_emoji };

		// Click target circle
		Circle targetCircle{ 400, 300, 60 };

		while (System::Update())
		{
			// If click target overlaps with mouse cursor
			if (targetCircle.mouseOver())
			{
				// Change mouse cursor to hand shape
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// If click target is clicked
			if (targetCircle.leftClicked())
			{
				// Change click target position to random position
				targetCircle.center = GetRandomPos();
			}

			// Draw click target
			targetEmoji.drawAt(targetCircle.center);
		}
	}
	```


## 20.3 Score Display
- Add score when clicking items
- Display score on screen

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/click/3.png)

??? memo "Code"
	```cpp hl_lines="13 21-22 36 45-46"
	# include <Siv3D.hpp>

	// Function that returns random coordinates
	Vec2 GetRandomPos()
	{
		return{ Random(60, 740), Random(60, 540) };
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Click target emoji
		const Texture targetEmoji{ U"🍎"_emoji };

		// Click target circle
		Circle targetCircle{ 400, 300, 60 };

		// Score
		int32 score = 0;

		while (System::Update())
		{
			// If click target overlaps with mouse cursor
			if (targetCircle.mouseOver())
			{
				// Change mouse cursor to hand shape
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// If click target is clicked
			if (targetCircle.leftClicked())
			{
				score += 100;

				// Change click target position to random position
				targetCircle.center = GetRandomPos();
			}

			// Draw click target
			targetEmoji.drawAt(targetCircle.center);

			// Display score
			font(U"SCORE: {}"_fmt(score)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });
		}
	}
	```

## 20.4 Adding Obstacle Items
- Add obstacle items that reduce points when clicked

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/click/4.png)

??? memo "Code"
	```cpp hl_lines="18-19 24-25 33 43-44 47-53 58-59"
	# include <Siv3D.hpp>

	// Function that returns random coordinates
	Vec2 GetRandomPos()
	{
		return{ Random(60, 740), Random(60, 540) };
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Click target emoji
		const Texture targetEmoji{ U"🍎"_emoji };

		// Obstacle item emoji
		const Texture trapEmoji{ U"🌶"_emoji };

		// Click target circle
		Circle targetCircle{ 400, 300, 60 };

		// Obstacle item circle
		Circle trapCircle{ 200, 150, 60 };

		// Score
		int32 score = 0;

		while (System::Update())
		{
			// If click target overlaps with mouse cursor
			if (targetCircle.mouseOver() || trapCircle.mouseOver())
			{
				// Change mouse cursor to hand shape
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// If click target is clicked
			if (targetCircle.leftClicked())
			{
				score += 100;
				targetCircle.center = GetRandomPos();
				trapCircle.center = GetRandomPos();
			}

			// If obstacle item is clicked
			if (trapCircle.leftClicked())
			{
				score -= 200;
				targetCircle.center = GetRandomPos();
				trapCircle.center = GetRandomPos();
			}

			// Draw click target
			targetEmoji.drawAt(targetCircle.center);

			// Draw obstacle item
			trapEmoji.drawAt(trapCircle.center);

			// Display score
			font(U"SCORE: {}"_fmt(score)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });
		}
	}
	```


## 20.5 Remaining Time
- Set a time limit for the game and prevent item clicking when time runs out

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/click/5.png)

??? memo "Code"
	```cpp hl_lines="30-31 35 37-38 40-41 64 75-84"
	# include <Siv3D.hpp>

	// Function that returns random coordinates
	Vec2 GetRandomPos()
	{
		return{ Random(60, 740), Random(60, 540) };
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Click target emoji
		const Texture targetEmoji{ U"🍎"_emoji };

		// Obstacle item emoji
		const Texture trapEmoji{ U"🌶"_emoji };

		// Click target circle
		Circle targetCircle{ 400, 300, 60 };

		// Obstacle item circle
		Circle trapCircle{ 200, 150, 60 };

		// Score
		int32 score = 0;

		// Remaining time
		double remainingTime = 10.0;

		while (System::Update())
		{
			const double deltaTime = Scene::DeltaTime();

			// Reduce remaining time
			remainingTime -= deltaTime;

			if (0 < remainingTime) // If there's remaining time
			{
				// If click target overlaps with mouse cursor
				if (targetCircle.mouseOver() || trapCircle.mouseOver())
				{
					// Change mouse cursor to hand shape
					Cursor::RequestStyle(CursorStyle::Hand);
				}

				// If click target is clicked
				if (targetCircle.leftClicked())
				{
					score += 100;
					targetCircle.center = GetRandomPos();
					trapCircle.center = GetRandomPos();
				}

				// If obstacle item is clicked
				if (trapCircle.leftClicked())
				{
					score -= 200;
					targetCircle.center = GetRandomPos();
					trapCircle.center = GetRandomPos();
				}
			}

			// Draw click target
			targetEmoji.drawAt(targetCircle.center);

			// Draw obstacle item
			trapEmoji.drawAt(trapCircle.center);

			// Display score
			font(U"SCORE: {}"_fmt(score)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });

			if (0 < remainingTime) // If there's remaining time
			{
				// Display remaining time
				font(U"TIME: {:.1f}"_fmt(remainingTime)).draw(40, Arg::topRight(760, 40), ColorF{ 0.1 });
			}
			else // If time is up
			{
				// Display time's up message
				font(U"TIME'S UP!").drawAt(60, Vec2{ 400, 300 }, ColorF{ 0.1 });
			}
		}
	}
	```


## 20.6 【Complete】Game Reset
- Press ++enter++ on the game over screen to reset score and remaining time and replay the game

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/click/6.png)

??? memo "Code"
	```cpp hl_lines="65-74 94"
	# include <Siv3D.hpp>

	// Function that returns random coordinates
	Vec2 GetRandomPos()
	{
		return{ Random(60, 740), Random(60, 540) };
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Click target emoji
		const Texture targetEmoji{ U"🍎"_emoji };

		// Obstacle item emoji
		const Texture trapEmoji{ U"🌶"_emoji };

		// Click target circle
		Circle targetCircle{ 400, 300, 60 };

		// Obstacle item circle
		Circle trapCircle{ 200, 150, 60 };

		// Score
		int32 score = 0;

		// Remaining time
		double remainingTime = 10.0;

		while (System::Update())
		{
			const double deltaTime = Scene::DeltaTime();

			// Reduce remaining time
			remainingTime -= deltaTime;

			if (0 < remainingTime) // If there's remaining time
			{
				// If click target overlaps with mouse cursor
				if (targetCircle.mouseOver() || trapCircle.mouseOver())
				{
					// Change mouse cursor to hand shape
					Cursor::RequestStyle(CursorStyle::Hand);
				}

				// If click target is clicked
				if (targetCircle.leftClicked())
				{
					score += 100;
					targetCircle.center = GetRandomPos();
					trapCircle.center = GetRandomPos();
				}

				// If obstacle item is clicked
				if (trapCircle.leftClicked())
				{
					score -= 200;
					targetCircle.center = GetRandomPos();
					trapCircle.center = GetRandomPos();
				}
			}
			else // If time is up
			{
				if (KeyEnter.down())
				{
					score = 0;
					remainingTime = 15.0;
					targetCircle.center = GetRandomPos();
					trapCircle.center = GetRandomPos();
				}
			}

			// Draw click target
			targetEmoji.drawAt(targetCircle.center);

			// Draw obstacle item
			trapEmoji.drawAt(trapCircle.center);

			// Display score
			font(U"SCORE: {}"_fmt(score)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });

			if (0 < remainingTime) // If there's remaining time
			{
				// Display remaining time
				font(U"TIME: {:.1f}"_fmt(remainingTime)).draw(40, Arg::topRight(760, 40), ColorF{ 0.1 });
			}
			else // If time is up
			{
				// Display time's up message
				font(U"TIME'S UP!").drawAt(60, Vec2{ 400, 300 }, ColorF{ 0.1 });
				font(U"Press [Enter] to restart").drawAt(40, Vec2{ 400, 400 }, ColorF{ 0.1 });
			}
		}
	}
	```