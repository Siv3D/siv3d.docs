# 11. Drawing Rectangles
Learn how to draw rectangles on the screen.

## 11.1 Drawing Rectangles
- To draw a rectangle, create a `Rect` class object and call its `.draw()` method
- `Rect` is created as follows:
	- Specify top-left coordinates (x, y), width w, height h as `Rect{ x, y, w, h }` (rectangle)
	- Specify top-left coordinates (x, y), side length s as `Rect{ x, y, s }` (square)
	- All values are specified as integers (`int32`)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/1.png)

```cpp title="Drawing Rectangles"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Rect{ 20, 40, 400, 100 }.draw();

		Rect{ 100, 200, 80 }.draw(Palette::Orange);

		Rect{ 400, 300, 360, 260 }.draw(ColorF{ 0.8, 0.9, 1.0 });
	}
}
```


## 11.2 Drawing Rectangles (Floating Point Numbers)
- When you want to handle coordinates and sizes with `double` type, use `RectF` instead of `Rect`
- `RectF` is created as follows:
	- Specify top-left coordinates (x, y), width w, height h as `RectF{ x, y, w, h }` (rectangle)
	- Specify top-left coordinates (x, y), side length s as `RectF{ x, y, s }` (square)
	- Values are specified as integers or floating point numbers (`double`)
- In the following sample, `Scene::Time()` returns the elapsed time (in seconds) since the application started as a `double` type. This value multiplied by 20.0 is used as the rectangle width for drawing

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/2.png)

```cpp title="Drawing a Rectangle with Width Changing Over Time"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double w = (Scene::Time() * 20.0);

		RectF{ 0, 250, w, 100 }.draw();
	}
}
```

- Since `w` is a `double` type, using `Rect` instead of `RectF` would cause a compile error


## 11.3 Creating Rectangles by Specifying Center
- When you want to create a rectangle by specifying the center coordinates instead of the top-left, do the following:
	- `Rect{ Arg::center(x, y), w, h }`
	- `Rect{ Arg::center(x, y), s }`
	- `Rect{ Arg::center = pos, w, h }`
	- `Rect{ Arg::center = pos, s }`
	- The same applies to `RectF`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/3.png)

```cpp title="Drawing Rectangles with Center Coordinate Specification"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Rect{ Arg::center(100, 100), 180 }.draw();

		Rect{ Arg::center(400, 300), 300, 100 }.draw(Palette::Orange);

		Rect{ Arg::center = Cursor::Pos(), 200 }.draw(ColorF{ 0.8, 0.9, 1.0, 0.8 });
	}
}
```


## 11.4 Drawing Rectangle Outlines
- When you want to draw only the outline of a rectangle, use `.drawFrame()` instead of `.draw()`
- `.drawFrame()` can be written in two ways:
	- `.drawFrame(thickness, color)`
	- `.drawFrame(inner thickness, outer thickness, color)`
- Inner and outer directions represent thickness toward the inside and outside from the reference rectangle, and the final thickness is the sum of both
- Specify values of 0.0 or greater for all thicknesses
- When `color` is omitted, it becomes white like with `.draw()`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/4.png)

```cpp title="Drawing Rectangle Outlines"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Rect{ Arg::center(100, 100), 180 }.drawFrame(10);

		Rect{ 400, 300, 200 }.drawFrame(8, 0, Palette::Seagreen);

		Rect{ 100, 300, 400, 200 }.drawFrame(0, 4, ColorF{ 0.8, 0.9, 1.0 });
	}
}
```


## 11.5 Vertical Gradients
- When you want to gradient the rectangle color vertically, use the following `.draw()`:
	- `.draw(Arg::top = top color, Arg::bottom = bottom color)`
	- You can also write `Arg::top(r, g, b)`, `Arg::top(gray)`, `Arg::top(r, g, b, a)`, `Arg::top(gray, a)`
- The order of `top` and `bottom` cannot be swapped

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/5.png)

```cpp title="Drawing Rectangles with Vertical Gradients"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Rect{ 0, 0, 600, 500 }
			.draw(Arg::top = ColorF{ 0.5, 0.7, 0.9 }, Arg::bottom = ColorF{ 0.5, 0.9, 0.7 });

		Rect{ 660, 40, 80, 520 }
			.draw(Arg::top(1.0), Arg::bottom(0.0));
	}
}
```


## 11.6 Horizontal Gradients
- When you want to gradient the rectangle color horizontally, use the following `.draw()`:
	- `.draw(Arg::left = left color, Arg::right = right color)`
	- You can also write `Arg::left(r, g, b)`, `Arg::left(gray)`, `Arg::left(r, g, b, a)`, `Arg::left(gray, a)`
- The order of `left` and `right` cannot be swapped

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/6.png)


```cpp title="Drawing Rectangles with Horizontal Gradients"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Rect{ 0, 0, 600, 500 }
			.draw(Arg::left = ColorF{ 0.5, 0.7, 0.9 }, Arg::right = ColorF{ 0.5, 0.9, 0.7 });

		Rect{ 660, 40, 80, 520 }
			.draw(Arg::left(1.0), Arg::right(0.0));
	}
}
```

## Review Checklist
- [x] Learned to create `Rect` and draw rectangles with `.draw()`
- [x] Learned to use `RectF` when handling position and size with `double` type
- [x] Learned how to create rectangles by specifying center coordinates
- [x] Learned how to draw rectangle outlines using `.drawFrame()`
- [x] Learned how to draw rectangles with vertical or horizontal gradients