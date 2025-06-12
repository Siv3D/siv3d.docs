# 9. Screen and Coordinates

## 9.1 Screen
- The part within the window where the background color can be changed is called the **screen (scene)**
- Siv3D can display text, shapes, and images in this area
- The screen size is **800 pixels wide** and **600 pixels tall** by default
- The screen size may also be called "window size"
	- The window size including the frame around the screen, such as the title bar, is called "window area size" to distinguish it
- In normal Siv3D programming, it's sufficient to be aware of only the screen size (window size)


## 9.2 Changing Screen Size
- Change the screen size with `Window::Resize(width, height)`
- Once changed, that size is maintained. Usually set at the beginning before the main loop
- The following code changes the screen size to 1280 pixels wide and 720 pixels tall

```cpp title="Change Screen Size" hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// Resize screen to 1280x720
	Window::Resize(1280, 720);

	while (System::Update())
	{

	}
}
```


## 9.3 Coordinates
- When displaying shapes and images on the screen, you specify **coordinates** that represent pixel positions on the screen
- Coordinates are represented as `(X, Y)` using two values: X coordinate and Y coordinate
- The top-left pixel of the screen has "X coordinate: 0" and "Y coordinate: 0", which is `(0, 0)`
- Moving right increases the X coordinate, and moving down increases the Y coordinate
- When the screen size is 800 × 600, the bottom-right pixel of the screen has coordinates `(799, 599)`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/1.png)

- The following code displays the coordinates of the mouse cursor in the top-left of the screen

```cpp title="Display Mouse Cursor Coordinates"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// Display current mouse cursor coordinates
		Print << Cursor::Pos();
	}
}
```


## 9.4 Classes for Representing Coordinates
- The classes `Point` and `Vec2` are provided for representing coordinates
- `Point` represents coordinates with integers

```cpp
struct Point
{
	int32 x;
	int32 y;
};
```

- `Vec2` represents coordinates with floating point numbers

```cpp
struct Vec2
{
	double x;
	double y;
};
```

- `Cursor::Pos()` introduced in **9.3** returns the mouse cursor coordinates as a `Point` type

```cpp title="Display Mouse Cursor Coordinates" hl_lines="9 12-13"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		const Point pos = Cursor::Pos();

		// Display current mouse cursor coordinates separated by X and Y coordinates
		Print << U"X: " << pos.x;
		Print << U"Y: " << pos.y;
	}
}
```


## 9.5 Converting `Point` → `Vec2`
- `Point` → `Vec2` can be converted naturally (implicitly)

```cpp hl_lines="9"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		const Vec2 pos = Cursor::Pos();

		// Display current mouse cursor coordinates separated by X and Y coordinates
		Print << U"X: " << pos.x;
		Print << U"Y: " << pos.y;
	}
}
```


## 9.6 Converting `Vec2` → `Point`
- Converting `Vec2` → `Point` loses information, so you need to convert explicitly using `.asPoint()`
- Decimal information is truncated

```cpp hl_lines="9-10"
# include <Siv3D.hpp>

void Main()
{
	const Vec2 pos1{ 123.4, 567.8 };

	Print << pos1;

	// Convert Vec2 → Point
	const Point pos2 = pos1.asPoint();

	Print << pos2;

	while (System::Update())
	{

	}
}
```


## Review Checklist
- [x] Understood the default screen size (800x600)
- [x] Learned that screen size can be changed with `Window::Resize(width, height)`
- [x] Understood that coordinates are represented by two values, X and Y coordinates, with the top-left of the screen being `(0, 0)`
- [x] Learned that `Point` and `Vec2` are classes for representing coordinates, where `Point` uses integers and `Vec2` uses floating point numbers
- [x] Learned that `Point` → `Vec2` can be converted implicitly
- [x] Learned that `Vec2` → `Point` is converted explicitly using `.asPoint()`