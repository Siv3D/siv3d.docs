# 17. Handling Keyboard Input

## 17.1 Checking if a Key is Pressed
- `Key name.down()` returns `true` when a key is pressed
- The main key names are as shown in the following table

| Key | Key Name |
| --- | --- |
| A, B, C, ... | `KeyA`, `KeyB`, `KeyC`, ... |
| 1, 2, 3, ... | `Key1`, `Key2`, `Key3`, ... |
| F1, F2, F3, ... | `KeyF1`, `KeyF2`, `KeyF3`, ... |
| ↑, ↓, ←, → | `KeyUp`, `KeyDown`, `KeyLeft`, `KeyRight` |
| Space key | `KeySpace` |
| Enter key | `KeyEnter` |
| Backspace key | `KeyBackspace` |
| Tab key | `KeyTab` |
| Escape key | `KeyEscape` |
| Page up, Page down | `KeyPageUp`, `KeyPageDown` |
| Delete key | `KeyDelete` |
| Numpad 0, 1, 2, ... | `KeyNum0`, `KeyNum1`, `KeyNum2`, ... |
| Shift key | `KeyShift` |
| Left shift, Right shift | `KeyLShift`, `KeyRShift` |
| Ctrl key | `KeyControl` |
| (macOS) Command key | `KeyCommand` |
| Comma, Period, Slash | `KeyComma`, `KeyPeriod`, `KeySlash` |


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/keyboard/1.png)

```cpp title="Output the name of the pressed key"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		// If A key is pressed
		if (KeyA.down())
		{
			Print << U"A";
		}

		// If Space key is pressed
		if (KeySpace.down())
		{
			Print << U"Space";
		}

		// If 1 key is pressed
		if (Key1.down())
		{
			Print << U"1";
		}
	}
}
```

## 17.2 Checking if a Key is Being Pressed
- Unlike `.down()`, `.pressed()` returns `true` continuously while the key is being pressed

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/keyboard/2.png)

```cpp title="Output the name of the key being pressed"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		// If A key is being pressed
		if (KeyA.pressed())
		{
			Print << U"A";
		}

		// If Space key is being pressed
		if (KeySpace.pressed())
		{
			Print << U"Space";
		}

		// If 1 key is being pressed
		if (Key1.pressed())
		{
			Print << U"1";
		}
	}
}

```

## 17.3 Moving Emoji Left and Right with Keys
- Create a program that moves an emoji left and right using arrow keys

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/keyboard/3.png)

```cpp title="Emoji moves left and right with left and right arrow keys"
# include <Siv3D.hpp>

// Function to calculate movement amount for the current frame
Vec2 GetMovement(double speed)
{
	Vec2 move{ 0, 0 };

	if (KeyLeft.pressed()) // [←] key
	{
		move.x -= speed;
	}

	if (KeyRight.pressed()) // [→] key
	{
		move.x += speed;
	}

	return move;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🐥"_emoji };

	// Emoji position
	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		// Elapsed time from previous frame (seconds) * 200
		const double move = (Scene::DeltaTime() * 200);

		pos += GetMovement(move);

		emoji.drawAt(pos);
	}
}
```

## 🧩 Practice
Try creating the following programs.

### Practice ① Move emoji up, down, left, and right with keys
- ++left++ key moves emoji left
- ++right++ key moves emoji right  
- ++up++ key moves emoji up
- ++down++ key moves emoji down

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/keyboard/4-1.mp4?raw=true" autoplay loop muted playsinline></video>

??? info "Hint"
	- Extend the code from 17.3
	- ++up++ is `KeyUp`, ++down++ is `KeyDown`

??? success "Sample Solution"
	```cpp
	# include <Siv3D.hpp>

	// Function to calculate movement amount for the current frame
	Vec2 GetMovement(double speed)
	{
		Vec2 move{ 0, 0 };

		if (KeyLeft.pressed()) // [←] key
		{
			move.x -= speed;
		}

		if (KeyRight.pressed()) // [→] key
		{
			move.x += speed;
		}

		if (KeyUp.pressed()) // [↑] key
		{
			move.y -= speed;
		}

		if (KeyDown.pressed()) // [↓] key
		{
			move.y += speed;
		}

		return move;
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Texture emoji{ U"🐥"_emoji };

		// Emoji position
		Vec2 pos{ 400, 300 };

		while (System::Update())
		{
			// Elapsed time from previous frame (seconds) * 200
			const double move = (Scene::DeltaTime() * 200);

			pos += GetMovement(move);

			emoji.drawAt(pos);
		}
	}
	```


### Practice ② Switch between 4 options with keys
- ++left++ key moves selection left
- ++right++ key moves selection right
- Selection doesn't change when pressing ++left++ key while leftmost item is selected
- Selection doesn't change when pressing ++right++ key while rightmost item is selected

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/keyboard/4-2.mp4?raw=true" autoplay loop muted playsinline></video>

??? info "Hint"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Texture emoji0{ U"🍣"_emoji };
		const Texture emoji1{ U"🍜"_emoji };
		const Texture emoji2{ U"🍔"_emoji };
		const Texture emoji3{ U"🍛"_emoji };

		int32 itemIndex = 2;

		while (System::Update())
		{
			emoji0.drawAt(100, 200);
			emoji1.drawAt(300, 200);
			emoji2.drawAt(500, 200);
			emoji3.drawAt(700, 200);

			Rect{ Arg::center((100 + 200 * itemIndex), 200), 150 }
				.drawFrame(6, ColorF{ 0.2 });
		}
	}
	```

??? success "Sample Solution"
	```cpp
	# include <Siv3D.hpp>

	// Function to change selected item index based on key input
	int32 UpdateSelectIndex(int32 itemIndex, int32 maxIndex)
	{
		// If not at leftmost and [←] key is pressed, decrease index by 1
		if ((0 < itemIndex) && KeyLeft.down())
		{
			--itemIndex;
		}

		// If not at rightmost and [→] key is pressed, increase index by 1
		if ((itemIndex < maxIndex) && KeyRight.down())
		{
			++itemIndex;
		}

		// Return new index
		return itemIndex;
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Texture emoji0{ U"🍣"_emoji };
		const Texture emoji1{ U"🍜"_emoji };
		const Texture emoji2{ U"🍔"_emoji };
		const Texture emoji3{ U"🍛"_emoji };

		int32 itemIndex = 0;

		while (System::Update())
		{
			emoji0.drawAt(100, 200);
			emoji1.drawAt(300, 200);
			emoji2.drawAt(500, 200);
			emoji3.drawAt(700, 200);

			itemIndex = UpdateSelectIndex(itemIndex, 3);

			Rect{ Arg::center((100 + 200 * itemIndex), 200), 150 }
				.drawFrame(6, ColorF{ 0.2 });
		}
	}
	```

## Review Checklist
- [x] Learned the key names for major keys
- [x] Learned that `Key name.down()` returns `true` when that key is pressed
- [x] Learned that `Key name.pressed()` returns `true` continuously while that key is being pressed
- [x] Created a program that moves an emoji using key input
- [x] Created a program that switches between options using key input