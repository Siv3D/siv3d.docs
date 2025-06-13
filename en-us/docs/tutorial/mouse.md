# 16. Handling Mouse Input

## 16.1 Getting Mouse Cursor Position
- To get the current coordinates of the mouse cursor, use `Cursor::Pos()`
- `Cursor::Pos()` returns a `Point` type value

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/1.png)

```cpp title="Display emoji at mouse cursor position" hl_lines="13"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };
	
	const Texture texture{ U"🐥"_emoji };

	while (System::Update())
	{
		const Point cursorPos = Cursor::Pos();

		font(U"{}"_fmt(cursorPos)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });

		texture.drawAt(cursorPos);
	}
}
```


## 16.2 Checking if Mouse Button is Pressed
- When the left mouse button is pressed, `MouseL.down()` returns `true`
- When the right mouse button is pressed, `MouseR.down()` returns `true`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/2.png)

```cpp title="Output message when mouse button is pressed" hl_lines="10 16"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		// If left-clicked
		if (MouseL.down())
		{
			Print << U"Left Click";
		}

		// If right-clicked
		if (MouseR.down())
		{
			Print << U"Right Click";
		}
	}
}
```


## 16.3 Checking if Mouse Button is Being Pressed
- Unlike `.down()`, `.pressed()` returns `true` continuously while the button is being pressed

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/3.png)

```cpp title="Output message while mouse button is being pressed" hl_lines="10 16"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		// If left button is being pressed
		if (MouseL.pressed())
		{
			Print << U"Left Pressed";
		}

		// If right button is being pressed
		if (MouseR.pressed())
		{
			Print << U"Right Pressed";
		}
	}
}
```


## 16.4 Combining Mouse Inputs
- Sample code for moving an emoji using mouse input:
	- The emoji moves to where you left-click
	- Right-clicking returns it to the center of the screen

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture texture{ U"🐥"_emoji };

	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		// If left-clicked
		if (MouseL.down())
		{
			// Change emoji display position to mouse cursor position
			pos = Cursor::Pos();
		}

		// If right-clicked
		if (MouseR.down())
		{
			// Reset emoji display position to center of screen
			pos = Vec2{ 400, 300 };
		}

		texture.drawAt(pos);
	}
}
```


## 16.5 Checking if a Shape is Clicked
- To check if shapes like `Rect` or `Circle` are clicked, use the member function `.leftClicked()` of each shape class
- `.leftClicked()` returns `true` when that shape is left-clicked

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/5.png)

```cpp title="Output message when shape is clicked" hl_lines="14 20"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Circle circle{ 200, 150, 100 };

	const Rect rect{ 400, 300, 200, 100 };

	while (System::Update())
	{
		// If circle is left-clicked
		if (circle.leftClicked())
		{
			Print << U"Circle";
		}

		// If rectangle is left-clicked
		if (rect.leftClicked())
		{
			Print << U"Rect";
		}

		circle.draw(Palette::Seagreen);
		rect.draw(ColorF{ 0.4 });
	}
}
```


## 16.6 Click Detection is Independent of Drawing
- Whether the shape is actually drawn on screen does not affect the result of `.leftClicked()`
- The `rect` in the following code represents a rectangle covering the left half of the screen
- Although it's not drawn using `.draw()`, you can still check if that rectangular area was left-clicked using `.leftClicked()`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/6.png)

```cpp title="Output message when left half of screen is clicked"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Rectangle covering the left half of the screen
	const Rect rect{ 0, 0, 400, 600 };

	while (System::Update())
	{
		// If left half of screen is left-clicked
		if (rect.leftClicked())
		{
			Print << U"Click!";
		}
	}
}
```


## 16.7 Checking if Mouse Cursor is Over a Shape
- Using the member function `.mouseOver()` of each shape class, you can check if the mouse cursor is over that shape
- `.mouseOver()` returns `true` when the mouse cursor is over that shape
- Like click detection, this doesn't depend on whether the shape is actually drawn
- The following code uses the conditional operator `A ? B : C` to change the shape's color depending on whether the mouse cursor is over the shape

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/7.png)

```cpp title="Change shape color when mouse cursor is over the shape"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Circle circle{ 200, 150, 100 };

	const Rect rect{ 400, 300, 200, 100 };

	while (System::Update())
	{
		circle.draw(circle.mouseOver() ? Palette::Seagreen : Palette::White);

		rect.draw(rect.mouseOver() ? ColorF{ 0.8 } : ColorF{ 0.6 });
	}
}
```


## 16.8 Making Mouse Cursor Hand-shaped
- To inform users that an object can be operated with the mouse, you might change the mouse cursor to a hand shape
- Calling `Cursor::RequestStyle(CursorStyle::Hand);` makes the mouse cursor hand-shaped for that frame

```cpp title="Make cursor hand-shaped when mouse cursor is over shape" hl_lines="13"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Circle circle{ 200, 150, 100 };

	while (System::Update())
	{
		if (circle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		circle.draw();
	}
}
```


## 16.9 Checking if an Emoji is Clicked
- Emojis (textures) don't have `.leftClicked()` or `.mouseOver()`
- Instead, you can approximate with a shape of similar size for detection
	- For emojis, you can roughly approximate with a circle of radius `60`
- The following code is a sample for clicking an apple on the screen
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/9.png)

```cpp title="Output message when emoji is clicked" hl_lines="9 14 21"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🍎"_emoji };

	const Circle circle{ 200, 150, 60 };

	while (System::Update())
	{
		// If mouse cursor is over the circle
		if (circle.mouseOver())
		{
			// Make mouse cursor a hand icon
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// If circle is left-clicked
		if (circle.leftClicked())
		{
			Print << U"Apple";
		}

		emoji.drawAt(circle.center);

		// Don't draw the circle
		//circle.draw();
	}
}
```

## Review Checklist
- [x] Learned that `Cursor::Pos()` gets the mouse cursor position as a `Point` type
- [x] Learned that `MouseL.down()` returns `true` when the left mouse button is pressed
- [x] Learned that `MouseL.pressed()` returns `true` continuously while the left mouse button is being pressed
- [x] Learned that shape classes' `.leftClicked()` returns `true` when that shape is left-clicked
- [x] Learned that shape classes' `.mouseOver()` returns `true` when the mouse cursor is over that shape
- [x] Learned that shape mouse-related detection can be performed regardless of whether the shape is drawn
- [x] Learned that `Cursor::RequestStyle(CursorStyle::Hand);` makes the mouse cursor hand-shaped for that frame only
- [x] Learned to approximate with shapes for detection since emojis (textures) don't have `.leftClicked()` or `.mouseOver()`