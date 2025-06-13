# 10. Drawing Circles
Learn how to draw circles on the screen.

## 10.1 Drawing Circles
- Write drawing commands **inside the main loop**
	- Since the screen is cleared with the background color every frame, if you want to keep displaying a circle, you need to draw the circle every frame
- To draw a circle, create a `Circle` class object and call its `.draw()` method
- `Circle` is created by specifying the center's X coordinate, Y coordinate, and radius like `Circle{ x, y, r }`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/circle/1.png)

```cpp title="Drawing Circles"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ 200, 300, 30 }.draw();

		Circle{ 600, 300, 100 }.draw();
	}
}
```


## 10.2 Drawing a Circle Following the Mouse
- You can create a `Circle` from two arguments like `Circle{ pos, r }` using `Point` or `Vec2` type values
- Combined with `Cursor::Pos()` which returns the current mouse cursor coordinates as a `Point` type, you can draw a circle that follows the mouse

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/circle/2.png)

```cpp title="Drawing a Circle Following the Mouse"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ Cursor::Pos(), 100 }.draw();
	}
}
```


## 10.3 Drawing Colored Circles
- To add color to shapes, pass a color as an argument to the `.draw()` function
- For color specification, use `Palette`, `ColorF`, or `HSV` as learned in [**Tutorial 8. Changing Background Color**](./background.md)
- When no color is specified, it defaults to white

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/circle/3.png)

```cpp title="Drawing Colored Circles"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ 100, 300, 30 }.draw(); // White when no color is specified

		Circle{ 200, 300, 30 }.draw(Palette::Green);

		Circle{ 300, 300, 30 }.draw(ColorF{ 1.0, 0.8, 0.0 });

		Circle{ 400, 300, 40 }.draw(ColorF{ 0.8 });

		Circle{ 500, 300, 40 }.draw(HSV{ 160.0, 0.5, 1.0 });

		Circle{ 600, 300, 40 }.draw(HSV{ 160.0 });
	}
}
```


## 10.4 Specifying Semi-transparent Colors
- With color specification using `ColorF` and `HSV`, you can set **opacity**
- Opacity ranges from 0.0 to 1.0
- 0.0 is completely transparent, 1.0 is completely opaque
- At 0.5, the background color and drawing color are mixed 50% each

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/circle/alpha.png)

- Opacity `a` (alpha) is specified as the last argument of `ColorF` or `HSV` like this:
	- `ColorF{ r, g, b, a }`, `ColorF{ gray, a }`
	- `HSV{ h, s, v, a }`, `HSV{ h, a }`
- When opacity is not specified like `ColorF{ 0.0, 0.6, 0.2 }`, `a` becomes 1.0
- When setting background color with `Scene::SetBackground()`, the opacity value has no meaning and is ignored

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/circle/4.png)

```cpp title="Drawing Semi-transparent Circles" hl_lines="11"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ 200, 300, 200 }.draw(ColorF{ 1.0 });
		Circle{ 600, 300, 200 }.draw(HSV{ 190.0, 0.5, 0.9 });

		// Draw a semi-transparent circle following the mouse cursor
		Circle{ Cursor::Pos(), 100 }.draw(ColorF{ 0.0, 0.6, 0.2, 0.5 });
	}
}
```


## 10.5 Drawing Circle Outlines
- When you want to draw only the outline of a circle, use `.drawFrame()` instead of `.draw()`
- `.drawFrame()` can be written in two ways:
	- `.drawFrame(thickness, color)`
	- `.drawFrame(inner thickness, outer thickness, color)`
- Inner and outer directions represent thickness toward the inside and outside from the reference circle, and the final thickness is the sum of both
- Specify values of 0.0 or greater for all thicknesses
- When `color` is omitted, it becomes white like with `.draw()`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/circle/5.png)

```cpp title="Drawing Circle Outlines"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ 100, 300, 60 }.drawFrame(8);

		Circle{ 300, 300, 60 }.drawFrame(4, 4); // Same as .drawFrame(8)

		Circle{ 500, 300, 80 }.drawFrame(10, 0, HSV{ 70.0, 0.8, 1.0 });

		Circle{ 700, 300, 80 }.drawFrame(0, 10, HSV{ 160.0, 0.8, 1.0 });
	}
}
```


## Review Checklist
- [x] Learned to create `Circle` and draw circles with `.draw()`
- [x] Learned how to draw colored circles by specifying color as an argument to `.draw()`
- [x] Learned how to draw semi-transparent circles by specifying opacity with `ColorF` or `HSV`
- [x] Learned how to draw circle outlines using `.drawFrame()`