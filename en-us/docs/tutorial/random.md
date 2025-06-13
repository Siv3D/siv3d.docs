# 18. Generating Random Numbers
Learn how to generate random numbers.

## 18.1 Generating Random Integers
- `Random(a, b)` randomly generates integers **from `a` to `b` inclusive**
	- `a` < `b` is required
- The pattern of generated random numbers is different each time

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/random/1.png)

```cpp title="Output 10 random numbers from 1 to 6" hl_lines="10"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	for (int32 i = 0; i < 10; ++i)
	{
		// Output random numbers from 1 to 6
		Print << Random(1, 6);
	}

	while (System::Update())
	{

	}
}
```


## 18.2 Fortune Telling Program
- By displaying different results based on random values, you can create a fortune telling app
- The following program randomly displays one of four emojis each time you click the mouse

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/random/2.png)

```cpp title="Display random emoji from 4 options each click" hl_lines="20"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji0{ U"😄"_emoji };
	const Texture emoji1{ U"😵‍💫"_emoji };
	const Texture emoji2{ U"😭"_emoji };
	const Texture emoji3{ U"😋"_emoji };

	// Emoji number
	int32 emojiIndex = 0;

	while (System::Update())
	{
		if (MouseL.down())
		{
			// Randomly select new emoji number
			emojiIndex = Random(0, 3);

			// Output emoji number
			Print << emojiIndex;
		}

		if (emojiIndex == 0)
		{
			emoji0.drawAt(400, 300);
		}
		else if (emojiIndex == 1)
		{
			emoji1.drawAt(400, 300);
		}
		else if (emojiIndex == 2)
		{
			emoji2.drawAt(400, 300);
		}
		else
		{
			emoji3.drawAt(400, 300);
		}
	}
}
```


## 18.3 Generating Random Floating Point Numbers
- When `a` and `b` in `Random(a, b)` are floating point numbers, it randomly generates floating point numbers **from `a` inclusive to `b` exclusive**
	- `a` < `b` is required
	- This differs from integers where it's "from `a` to `b` inclusive"
- The following code randomly changes the emoji's scale each time you click

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/random/3.png)

```cpp title="Randomly change emoji scale each click" hl_lines="17"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🎁"_emoji };

	// Scale factor
	double scale = 1.0;

	while (System::Update())
	{
		if (MouseL.down())
		{
			// Generate random value from 0.5 inclusive to 2.0 exclusive
			scale = Random(0.5, 2.0);

			// Output scale factor
			Print << scale;
		}

		emoji.scaled(scale).drawAt(400, 300);
	}
}
```


## 18.4 Moving to Random Coordinates
- Determine X and Y coordinates with random numbers to move the emoji to a random location

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/random/4.png)

```cpp title="Emoji moves to random location each click" hl_lines="15"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🛸"_emoji };

	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		if (MouseL.down())
		{
			pos = Vec2{ Random(100, 700), Random(100, 500) };
		}

		emoji.drawAt(pos);
	}
}
```


## 🧩 Practice
Try creating the following programs.

### Practice ① Rolling Dice
- Stop the rotating (numbers changing) dice by clicking
- Click again to start rotating again

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/random/5-1.mp4?raw=true" autoplay loop muted playsinline></video>

??? info "Hint"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Dice square area
		const Rect diceRect{ Arg::center(400, 300), 200 };

		// Dice result
		int32 result = 1;

		while (System::Update())
		{
			// If mouse cursor is over the dice
			if (diceRect.mouseOver())
			{
				// Change mouse cursor to hand icon
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// Draw dice square
			diceRect.draw();

			// Draw dice number
			font(U"{}"_fmt(result)).drawAt(120, Vec2{ 400, 300 }, ColorF{ 0.1 });
		}
	}
	```

??? success "Sample Solution"

	- `not isRolling` has the same meaning as `!isRolling`. Siv3D adopts the style of writing `!` as `not` for visibility

	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Dice square area
		const Rect diceRect{ Arg::center(400, 300), 200 };

		// Dice result
		int32 result = 1;

		// Whether rolling
		bool isRolling = true;

		while (System::Update())
		{
			// If rolling
			if (isRolling)
			{
				// Change dice result to random value
				result = Random(1, 6);
			}

			// If mouse cursor is over the dice
			if (diceRect.mouseOver())
			{
				// Change mouse cursor to hand icon
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// If dice is left-clicked
			if (diceRect.leftClicked())
			{
				// Toggle rolling state
				isRolling = (not isRolling);
			}

			// Draw dice square
			diceRect.draw();

			// Draw dice number
			font(U"{}"_fmt(result)).drawAt(120, Vec2{ 400, 300 }, ColorF{ 0.1 });
		}
	}
	```

## Review Checklist
- [x] Learned that `Random(a, b)` randomly generates integers from `a` to `b` inclusive
- [x] Learned that when `a` and `b` in `Random(a, b)` are floating point numbers, it randomly generates floating point numbers from `a` inclusive to `b` exclusive