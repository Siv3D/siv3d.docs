# 43. Mouse Input
Learn how to handle mouse input.

## 43.1 Mouse Cursor Position
- Mouse cursor coordinates can be obtained as `Point` type using `Cursor::Pos()`
- When the scene differs from the actual window size (**Tutorial 44**), you can use `Cursor::PosF()` to get coordinates as `Vec2` type including decimal places
- The mouse cursor coordinates obtained with `Cursor::Pos()` are from the time of the last `System::Update()` call
- Please note that there may be a slight delay compared to the latest mouse cursor position actually visible on screen

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/mouse/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		ClearPrint();
		Print << Cursor::Pos();
		Print << Cursor::PosF();
		Circle{ Cursor::PosF(), 50 }.draw(ColorF{ 0.2 });
	}
}
```


## 43.2 Mouse Cursor Movement
- The mouse cursor coordinates from one frame ago can be obtained with `Cursor::PreviousPos()` / `Cursor::PreviousPosF()`
- The amount of mouse cursor movement from one frame ago can be obtained with `Cursor::Delta()` / `Cursor::DeltaF()`
- `Cursor::Delta() == (Cursor::Pos() - Cursor::PreviousPos())`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/mouse/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Whether grabbing the circle
	bool grab = false;

	Circle circle{ 400, 300, 50 };

	while (System::Update())
	{
		if (grab)
		{
			// Move the circle by the amount of movement
			circle.moveBy(Cursor::Delta());
		}

		if (circle.leftClicked()) // If the circle is left-clicked
		{
			grab = true;
		}
		else if (MouseL.up()) // If the left mouse button is released
		{
			grab = false;
		}

		if (grab || circle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		circle.draw(ColorF{ 0.2 });
	}
}
```


## 43.3 Mouse Cursor Screen Coordinates
- To get mouse cursor coordinates based on screen coordinates, use `Cursor::ScreenPos()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// Mouse cursor coordinates in screen coordinates
		Print << Cursor::ScreenPos();
	}
}
```


## 43.4 Mouse Button Input State
- The following `Input` type constants are assigned to mouse buttons

| Constant | Corresponding Button |
|---------|---------|
| MouseL  | Left button    |
| MouseR  | Right button    |
| MouseM  | Middle button   |
| MouseX1 | Extended button 1 |
| MouseX2 | Extended button 2 |
| MouseX3 | Extended button 3 |
| MouseX4 | Extended button 4 |
| MouseX5 | Extended button 5 |

- Like the keyboard in **Tutorial 42**, you can check the input state of buttons through member functions of the `Input` type

| Code | Not pressed | Just pressed | Held down | Just released | Released |
|:--:|:--:|:--:|:--:|:--:|:--:|
| `.down()` | false | **✔ true** | false | false | false |
| `.pressed()` | false | **✔ true** | **✔ true** | false | false |
| `.up()` | false | false | false | **✔ true** | false |

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << MouseL.pressed();
		Print << MouseM.pressed();
		Print << MouseR.pressed();
	}
}
```

## 43.5 Canceling Mouse Button Input
- To disable subsequent mouse button input within the current frame, call the `.clearInput()` member function of `Input`
- This is used to prevent input from penetrating to UI hidden behind when buttons or other UI elements overlap on screen
- You can use `Mouse::ClearLRInput()` to cancel both `MouseL` and `MouseR` simultaneously

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/mouse/5.png)

```cpp
# include <Siv3D.hpp>

class MyButton
{
public:

	MyButton() = default;

	explicit MyButton(const Rect& rect)
		: m_rect{ rect } {
	}

	bool update() const
	{
		if (m_rect.leftClicked())
		{
			MouseL.clearInput();
			return true;
		}

		return false;
	}

	void draw() const
	{
		if (m_rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		m_rect.draw().drawFrame(1, 0, Palette::Black);
	}

private:

	Rect m_rect{ 0, 0, 0, 0 };
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const MyButton button0{ Rect{ 100, 100, 200, 100 } };
	const MyButton button1{ Rect{ 150, 150, 200, 100 } };

	while (System::Update())
	{
		if (button0.update())
		{
			Print << U"button0";
		}

		if (button1.update())
		{
			Print << U"button1";
		}

		button1.draw();
		button0.draw();
	}
}
```


## 43.6 Button Press Duration
- `.pressedDuration()` of `Input` returns the duration that the input has been pressed as a `Duration` type
- `.pressedDuration()` is valid until the frame when that key's `.up()` returns `true`

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << MouseL.pressedDuration();
		Print << MouseM.pressedDuration();
		Print << MouseR.pressedDuration();
	}
}
```


## 43.7 Double-Click Detection
- You can detect double-clicks by creating a class like the following

```cpp
# include <Siv3D.hpp>

class DoubleClick
{
public:

	void update()
	{
		if (m_step == 3)
		{
			m_step = 0;
		}

		if (MouseL.down())
		{
			if (m_step == 0)
			{
				m_step = 1;
			}
			else if (m_step == 2)
			{
				if (const uint64 d = (Time::GetMillisec() - m_previousTimeMillisec);
					d < DoubleClickThresholdMillisec)
				{
					m_step = 3;
				}
				else
				{
					m_step = 1;
				}
			}
		}

		if (m_step == 0)
		{
			return;
		}

		if (not Cursor::Delta().isZero())
		{
			m_step = 0;
		}

		if ((m_step == 1) && MouseL.up())
		{
			if (MouseL.pressedDuration() < Milliseconds{ MaxClickTimeMillisec })
			{
				m_step = 2;
				m_previousTimeMillisec = Time::GetMillisec();
			}
			else
			{
				m_step = 0;
			}
		}
	}

	[[nodiscard]]
	bool doubleClicked() const noexcept
	{
		return (m_step == 3);
	}

private:

	// Duration of the first click (milliseconds)
	static constexpr int32 MaxClickTimeMillisec = 500;

	// Maximum interval between first and second clicks (milliseconds)
	static constexpr int32 DoubleClickThresholdMillisec = 500;

	int32 m_step = 0;

	int64 m_previousTimeMillisec = 0;
};

void Main()
{
	DoubleClick dc;

	while (System::Update())
	{
		// Must be called once every frame
		dc.update();

		// When double-clicked
		if (dc.doubleClicked())
		{
			Print << U"double click";
		}
	}
}
```


## 43.8 Mouse Button Names
- `.name()` of `Input` returns the name of that key as a `String` type

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << MouseL.name();
	Print << MouseR.name();
	Print << MouseM.name();
	Print << MouseX1.name();
	Print << MouseX2.name();

	while (System::Update())
	{

	}
}
```
```txt title="Output"
MouseL
MouseR
MouseM
MouseX1
MouseX2
```


## 43.9 Getting All Mouse Button Inputs
- `Mouse::GetAllInputs()` returns a list of active mouse buttons as `Array<Input>` where `.down()`, `.pressed()`, or `.up()` is `true` in the current frame

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// Get list of mouse buttons where down() / pressed() / up() is true
		const Array<Input> buttons = Mouse::GetAllInputs();

		for (const auto& button : buttons)
		{
			Print << button.name() << (button.pressed() ? U" pressed" : U" up");
		}
	}
}
```


## 43.10 Mouse Wheel Rotation Amount
- `Mouse::Wheel()` returns the scroll amount of the mouse wheel from the previous frame as a `double` type
- `Mouse::WheelH()` returns the scroll amount of the mouse's horizontal wheel from the previous frame as a `double` type
- Mouse wheel scroll amount is independent of frame rate, so you don't need to adjust it with `Scene::DeltaTime()`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/mouse/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		ClearPrint();

		// Mouse wheel scroll amount
		Print << Mouse::Wheel();

		// Mouse horizontal wheel scroll amount
		Print << Mouse::WheelH();

		pos.y -= (Mouse::Wheel() * 10);
		pos.x += (Mouse::WheelH() * 10);

		RectF{ Arg::center = pos, 100 }.draw(ColorF{ 0.2 });
	}
}
```


## 43.11 Checking if Mouse Cursor is Over Client Area
- `Cursor::OnClientRect()` returns as `bool` type whether the mouse cursor is over the window's client area (scene)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// Display whether the mouse cursor is over the window's client area
		Print << Cursor::OnClientRect();

		if (Cursor::OnClientRect())
		{
			Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
		}
		else
		{
			Scene::SetBackground(ColorF{ 0.2 });
		}
	}
}
```


## 43.12 Moving the Mouse Cursor
- To move the mouse cursor to a specified position, use `Cursor::SetPos(pos)`
- When the scene differs from the actual window size (**Tutorial 44**), there may be an error of ± 1 pixel

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << Cursor::Pos();

		if (SimpleGUI::Button(U"center", Vec2{ 100, 20 }))
		{
			// Move the mouse cursor to the center of the scene
			Cursor::SetPos(Point{ 400, 300 });
		}
	}
}
```


## 43.13 Mouse Cursor Movement Restriction (Windows) 
- In the Windows version, calling `Cursor::ClipToWindow(true)` restricts the area where the mouse cursor can move to the window's client area
- To remove the restriction, call `Cursor::ClipToWindow(false)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	bool clip = false;

	while (System::Update())
	{
		ClearPrint();
		Print << Cursor::Pos();

		if (SimpleGUI::CheckBox(clip, U"clip", Vec2{ 100, 20 }))
		{
			if (clip)
			{
				// Restrict mouse cursor movement to the window's client area
				Cursor::ClipToWindow(true);
			}
			else
			{
				// Remove restriction
				Cursor::ClipToWindow(false);
			}
		}
	}
}
```


## 43.14 Mouse Cursor Style (Standard Styles)
- You can change the mouse cursor style for that frame with `Cursor::RequestStyle(style)`
- Specify one of the following `CursorStyle` for `style`:

| Code                  | Description   |
|------------------------------|-----------|
| CursorStyle::Arrow           | Arrow (default)    |
| CursorStyle::IBeam           | I-beam     |
| CursorStyle::Cross           | Cross mark    |
| CursorStyle::Hand            | Hand icon    |
| CursorStyle::NotAllowed      | Prohibition mark    |
| CursorStyle::ResizeUpDown    | Up-down resize   |
| CursorStyle::ResizeLeftRight | Left-right resize   |
| CursorStyle::ResizeNWSE      | Top-left - bottom-right resize   |
| CursorStyle::ResizeNESW      | Top-right - bottom-left resize   |
| CursorStyle::ResizeAll       | Up-down-left-right resize   |
| CursorStyle::Hidden          | Hidden       |
| CursorStyle::Default         | Same as Arrow |

`Cursor::RequestStyle()` is a change for that frame only and returns to the original style in the next frame. If you want to continuously change the mouse cursor style, you need to call `Cursor::RequestStyle()` every frame.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/mouse/14.gif)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const ColorF buttonColor{ 0.2, 0.6, 1.0 };
	const Circle button{ 400, 300, 60 };
	Transition press{ 0.05s, 0.05s };

	while (System::Update())
	{
		const bool mouseOver = button.mouseOver();

		// If the mouse cursor is over the circle
		if (mouseOver)
		{
			// Change the mouse cursor to hand shape
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		press.update(button.leftPressed());

		const double t = press.value();

		button.movedBy(Vec2{ 0, 0 }.lerp(Vec2{ 0, 4 }, t))
			.drawShadow(Vec2{ 0, 6 }.lerp(Vec2{ 0, 1 }, t), (12 - t * 7), (5 - t * 4))
			.draw(buttonColor);
	}
}
```


## 43.15 Mouse Cursor Style (Custom Image)
- You can use any image created with the `Image` class (**Tutorial 63**) as a mouse cursor
- Register the image with a name using `Cursor::RegisterCustomCursorStyle(name, image, hotSpot)`
	- You can register multiple custom cursors if they have different names
	- Specify the click position in the image for `hotSpot`
- To apply the registered custom cursor to the current frame, call `Cursor::RequestStyle(name)`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/mouse/15.gif)

```cpp
# include <Siv3D.hpp>

Image CreateCursorImage()
{
	Image image{ 32, 32, Palette::White };

	for (int32 y = 0; y < image.height(); ++y)
	{
		for (int32 x = 0; x < image.width(); ++x)
		{
			image[y][x] = ColorF{ (x / 31.0), (y / 31.0), 1.0 };
		}
	}

	return image;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Register custom cursor. (0, 0) in the image is the click position
	Cursor::RegisterCustomCursorStyle(U"MyCursor", CreateCursorImage(), Point{ 0, 0 });

	const Circle circle{ 400, 300, 100 };

	while (System::Update())
	{
		Cursor::RequestStyle(U"MyCursor");

		circle.draw(circle.mouseOver() ? Palette::Orange : Palette::White);
	}
}
```