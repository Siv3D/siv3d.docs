# 27. Shape Operations
Learn how to perform operations on shapes such as translation, scaling, rotation, and expanding.

## 27.1 Shape Translation
- Some shape classes have a `.movedBy()` member function that **creates and returns a new shape translated by a specified vector**

| Code | Description |
|---|---|
| `.movedBy(X movement, Y movement)` | Creates and returns a shape translated by the specified vector |
| `.movedBy(movement)` | Creates and returns a shape translated by the specified vector |

- To move a shape directly, use the `.moveBy()` member function
	- The return value is `void`

| Code | Description |
|---|---|
| `.moveBy(X movement, Y movement)` | Translates by the specified vector |
| `.moveBy(movement)` | Translates by the specified vector |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Rect rect{ 100, 100, 300 };

	Circle circle{ 600, 100, 100 };

	while (System::Update())
	{
		circle.moveBy(0, (Scene::DeltaTime() * 100.0));

		rect.draw();

		rect.movedBy(40, 40).draw(Palette::Seagreen);

		circle.draw(ColorF{ 0.2 });
	}
}
```

## 27.2 Shape Scaling (Pixel-based)
- Some shape classes have a `.stretched()` member function that **creates and returns a new shape with modified width or height**

| Code | Description |
|---|---|
| `rect.stretched(all directions)` | Creates and returns a new rectangle by specifying the scaling amount (pixels) in all directions for a rectangle (`Rect` or `RectF`) |
| `rect.stretched(horizontal, vertical)` | Creates and returns a new rectangle by specifying the scaling amount (pixels) horizontally and vertically for a rectangle (`Rect` or `RectF`) |
| `rect.stretched(top, right, bottom, left)` | Creates and returns a new rectangle by specifying the scaling amount (pixels) in top, right, bottom, and left directions for a rectangle (`Rect` or `RectF`) |
| `line.stretched(both ends)` | Creates and returns a new line segment by specifying the scaling amount (pixels) for both start and end points of a line segment (`Line`) |
| `line.stretched(start, end)` | Creates and returns a new line segment by specifying the scaling amount (pixels) for start and end points of a line segment (`Line`) |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Rect rect{ 100, 100, 300 };

	const Line line{ 500, 100, 600, 500 };

	while (System::Update())
	{
		rect.draw();

		rect.stretched(-20).draw(ColorF{ 0.2 });

		line.stretched(40).draw(12);

		line.draw(4, ColorF{ 0.2 });
	}
}
```


## 27.3 Shape Scaling (Scale Factor)
- Some shape classes have `.scaled()` and `.scaledAt()` member functions that **create and return a new shape scaled by a specified factor**

| Code | Description |
|---|---|
| `.scaled(scale)` | Creates and returns a new shape scaled by the specified factor |
| `.scaled(width scale, height scale)` | Creates and returns a new shape scaled by the specified width and height factors |
| `.scaledAt(scale reference point, scale)` | Creates and returns a new shape scaled by the specified factor |
| `.scaledAt(scale reference point, width scale, height scale)` | Creates and returns a new shape scaled by the specified width and height factors |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Rect rect{ 100, 100, 300 };

	const Circle circle{ 600, 300, 100 };

	while (System::Update())
	{
		rect.draw();

		rect.scaled(0.5).draw(Palette::Seagreen);

		rect.scaledAt(rect.pos, 0.5).draw(ColorF{ 0.2 });

		circle.draw();

		circle.scaled(0.8).draw(ColorF{ 0.2 });
	}
}
```


## 27.4 Shape Rotation
- Some shape classes have `.rotated()` and `.rotatedAt()` member functions that **create and return a new shape rotated by a specified angle**

| Code | Description |
|---|---|
| `.rotated(rotation angle)` | Creates and returns a new shape rotated by the specified angle |
| `.rotatedAt(rotation reference point, rotation angle)` | Creates and returns a new shape rotated by the specified angle |

- To rotate a shape directly, use the `.rotate()` and `.rotateAt()` member functions
	- The return value is `void`

| Code | Description |
|---|---|
| `.rotate(rotation angle)` | Rotates the shape by the specified angle |
| `.rotateAt(rotation reference point, rotation angle)` | Rotates the shape by the specified angle |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Triangle triangle{ Vec2{ 200, 300 }, 200 };

	const Polygon polygon = Shape2D::Star(200, Vec2{ 600, 300 });

	double angle = 0_deg;

	while (System::Update())
	{
		angle += (Scene::DeltaTime() * 30_deg);

		triangle.rotated(angle).draw(ColorF{ 0.2 });

		polygon.rotatedAt(Vec2{ 600, 300 }, angle).draw(ColorF{ 0.2 });
	}
}
```


## 27.5 Expanding Polygons
- `Polygon` has `.calculateBuffer()` and `.calculateRoundBuffer()` member functions that **create and return a new polygon expanded by a specified thickness**
- These functions have high computational cost and should be avoided inside loops

| Code | Description |
|---|---|
| `.calculateBuffer(thickness)` | Creates and returns a new polygon expanded by the specified thickness |
| `.calculateRoundBuffer(thickness)` | Creates and returns a new polygon expanded by the specified thickness (with rounded corners) |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Polygon polygon1 = Shape2D::Star(150, Vec2{ 200, 300 });
	const Polygon polygon2 = polygon1.calculateBuffer(20);

	const Polygon polygon3 = Shape2D::Star(150, Vec2{ 600, 300 });
	const Polygon polygon4 = polygon3.calculateRoundBuffer(20);

	while (System::Update())
	{
		polygon2.draw(ColorF{ 0.2 });
		polygon1.drawFrame(4);

		polygon4.draw(ColorF{ 0.2 });
		polygon3.drawFrame(4);
	}
}
```


## 27.6 Retrieving Rectangle Components
- `Rect` and `RectF` have the following member functions to retrieve rectangle components

| Code | Description |
|---|---|
| `.center()` | Returns the center coordinates of the rectangle |
| `.tl()` | Returns the top-left coordinates of the rectangle |
| `.tr()` | Returns the top-right coordinates of the rectangle |
| `.br()` | Returns the bottom-right coordinates of the rectangle |
| `.bl()` | Returns the bottom-left coordinates of the rectangle |
| `.topCenter()` | Returns the center coordinates of the top edge |
| `.rightCenter()` | Returns the center coordinates of the right edge |
| `.bottomCenter()` | Returns the center coordinates of the bottom edge |
| `.leftCenter()` | Returns the center coordinates of the left edge |
| `.leftX()` | Returns the X coordinate of the left edge |
| `.rightX()` | Returns the X coordinate of the right edge |
| `.topY()` | Returns the Y coordinate of the top edge |
| `.bottomY()` | Returns the Y coordinate of the bottom edge |
| `.getRelativePoint(relative X, relative Y)` | Converts relative coordinates to absolute coordinates.<br>Top-left is `(0,0)`, bottom-right is `(1,1)` |
| `.area()` | Returns the area of the rectangle |


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	const Rect rect{ 100, 100, 600, 400 };

	while (System::Update())
	{
		rect.draw();

		rect.top().draw(4, HSV{ 0 });
		rect.right().draw(4, HSV{ 90 });
		rect.bottom().draw(4, HSV{ 180 });
		rect.left().draw(4, HSV{ 270 });

		font(U"TL").drawAt(40, rect.tl(), ColorF{ 0.2 });
		font(U"TC").drawAt(40, rect.topCenter(), ColorF{ 0.2 });
		font(U"TR").drawAt(40, rect.tr(), ColorF{ 0.2 });

		font(U"LC").drawAt(40, rect.leftCenter(), ColorF{ 0.2 });
		font(U"C").drawAt(40, rect.center(), ColorF{ 0.2 });
		font(U"RC").drawAt(40, rect.rightCenter(), ColorF{ 0.2 });

		font(U"BL").drawAt(40, rect.bl(), ColorF{ 0.2 });
		font(U"BC").drawAt(40, rect.bottomCenter(), ColorF{ 0.2 });
		font(U"BR").drawAt(40, rect.br(), ColorF{ 0.2 });

		font(U"(0.8, 0.2)").drawAt(40, rect.getRelativePoint(0.8, 0.2), ColorF{ 0.2 });
	}
}
```


## 27.7 Circular Coordinates
- When dealing with objects arranged in a circle, it's convenient to use **circular coordinates** that express position by distance and angle from a reference point, instead of X-Y coordinates
- Siv3D provides `Circular` and `OffsetCircular` classes for handling circular coordinates
	- Angles are specified in clockwise radians with 12 o'clock as 0

| Code | Description |
|---|---|
| `Circular{ r, theta }` | Represents a position with radius `r` and angle `theta` centered at the origin |
| `OffsetCircular{ offset, r, theta }` | Represents a position with radius `r` and angle `theta` centered at `offset` |

	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/7-1.png)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/7-2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 1.0, 0.98, 0.96 });

	double angle = 0_deg;

	while (System::Update())
	{
		angle += (Scene::DeltaTime() * 30_deg);

		for (int32 i = 0; i < 12; ++i)
		{
			const double theta = (i * 30_deg + angle);

			const Vec2 pos = OffsetCircular{ Vec2{ 400, 300 }, 200, theta };

			pos.asCircle(28)
				.drawShadow(Vec2{ 0, 4 }, 12, 4)
				.draw(HSV{ (i * 30), 0.8, 1.0 })
				.drawFrame(3, 2, ColorF{ 1.0 });
		}
	}
}
```