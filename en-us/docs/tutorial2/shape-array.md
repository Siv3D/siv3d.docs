# 23. Arrays of Shapes
Learn how to handle shape classes like `Circle` and `Rect` in arrays.

## 23.1 Creating an Array of Circles
- `Array<Circle>` is an array of `Circle`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<Circle> circles =
	{
		Circle{ 100, 300, 10 },
		Circle{ 200, 300, 20 },
		Circle{ 300, 300, 30 },
		Circle{ 400, 300, 40 },
	};

	Print << circles;

	while (System::Update())
	{

	}
}
```


## 23.2 Drawing All Circles
- Use a range-based for loop (const reference) to draw all `Circle`s

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	
	Array<Circle> circles =
	{
		Circle{ 100, 300, 10 },
		Circle{ 200, 300, 20 },
		Circle{ 300, 300, 30 },
		Circle{ 400, 300, 40 },
	};

	while (System::Update())
	{
		// Draw all circles
		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.3 Moving All Circles
- Use a range-based for loop (reference) to move all `Circle`s

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	
	Array<Circle> circles =
	{
		Circle{ 100, 300, 10 },
		Circle{ 200, 300, 20 },
		Circle{ 300, 300, 30 },
		Circle{ 400, 300, 40 },
	};

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		// Move all circles
		for (auto& circle : circles)
		{
			circle.y += (deltaTime * 50.0);
		}

		// Draw all circles
		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.4 Adding Circles by Clicking
- Add a `Circle` with a random radius at the clicked position

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	
	Array<Circle> circles;

	while (System::Update())
	{
		// If left-clicked
		if (MouseL.down())
		{
			// Add a circle with random radius at the clicked position
			circles << Circle{ Cursor::Pos(), Random(5.0, 40.0) };
		}

		// Draw all circles
		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.5 Removing Circles by Clicking (`.remove_if()` Method)
- Use `.remove_if()` to remove `Circle`s that are left-clicked
    - See [**Tutorial 22.17**](./array.md) for element removal using `.remove_if()`
- Pass a lambda expression to `.remove_if()` that determines "whether the circle was left-clicked"

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Array<Circle> circles =
	{
		Circle{ 100, 300, 10 },
		Circle{ 200, 300, 20 },
		Circle{ 300, 300, 30 },
		Circle{ 400, 300, 40 },
	};

	while (System::Update())
	{
		// Remove circles that were left-clicked
		circles.remove_if([](const Circle& circle) { return circle.leftClicked(); });

		// Draw all circles
		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.6 Removing Circles by Clicking (Iterator Method)
- Remove `Circle`s that are left-clicked using the iterator method
    - See [**Tutorial 22.16**](./array.md) for element removal using iterators
- Compared to the `.remove_if()` method, this approach makes it easier to write additional processing along with removal (such as adding points, stopping further removals, etc.)
- Access to elements must be done before removal with `.erase()`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	Array<Circle> circles =
	{
		Circle{ 100, 300, 10 },
		Circle{ 200, 300, 20 },
		Circle{ 300, 300, 30 },
		Circle{ 400, 300, 40 },
	};

	// Score
	double score = 0;

	while (System::Update())
	{
		// For each circle
		for (auto it = circles.begin(); it != circles.end();)
		{
			// If the circle was left-clicked
			if (it->leftClicked())
			{
				// Add radius * 10 to the score
				score += (it->r * 10); // Access element before deletion

				// Remove the circle
				it = circles.erase(it);
			}
			else
			{
				++it;
			}
		}

		// Draw all circles
		for (const auto& circle : circles)
		{
			circle.draw();
		}

		font(U"Score: {}"_fmt(score)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });
	}
}
```


## 23.7 Removing Circles that Reach a Certain Position
- Make `Circle`s move automatically and remove them when they reach a certain position
- Pass a lambda expression to `.remove_if()` that determines "whether the Y coordinate exceeded 500.0"

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Array<Circle> circles;

	for (int32 i = 0; i < 6; ++i)
	{
		circles << Circle{ (100 + i * 100), Random(0.0, 200.0), 30 };
	}

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		for (auto& circle : circles)
		{
			circle.y += (deltaTime * 100.0);
		}

		// Remove circles whose Y coordinate exceeded 500.0
		circles.remove_if([](const Circle& circle) { return (500.0 < circle.y); });

		// Draw all circles
		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.8 Adding Circles at Regular Intervals
- Apply the "doing something at regular intervals" from [**Tutorial 19.3**](../tutorial/time.md) to add a `Circle` every 0.5 seconds

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/8.png)

```cpp title="Add a circle every 0.5 seconds (maximum 10 circles)"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Array<Circle> circles;

	// Circle spawn interval (seconds)
	const double spawnInterval = 0.5;

	// Maximum number of circles
	const size_t maxCircles = 10;

	// Accumulated time (seconds)
	double accumulatedTime = 0.0;

	while (System::Update())
	{
		// Time elapsed since the previous frame (seconds)
		const double deltaTime = Scene::DeltaTime();

		// Increase accumulated time
		accumulatedTime += deltaTime;

		// If accumulated time exceeds the interval
		if (spawnInterval < accumulatedTime)
		{
			// If the number of circles hasn't reached the maximum
			if (circles.size() < maxCircles)
			{
				// Add a circle
				circles << Circle{ Random(100, 700), Random(100, 500), 30 };
			}

			// Reduce accumulated time by the interval
			accumulatedTime -= spawnInterval;
		}

		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.9 Arrays of Custom Classes (1)
- Create a `ColorCircle` class that has a `Circle` and a hue value, and handle them in an array

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/9.png)

```cpp
# include <Siv3D.hpp>

struct ColorCircle
{
	Circle circle;

	double hue;

	// Function to draw the circle
	void draw() const
	{
		circle.draw(HSV{ hue, 0.75, 0.9 });
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Array<ColorCircle> circles;

	while (System::Update())
	{
		// If left-clicked
		if (MouseL.down())
		{
			// Add a ColorCircle
			circles << ColorCircle{ Circle{ Cursor::Pos(), Random(5.0, 40.0) }, Random(0.0, 360.0) };
		}

		// Draw all ColorCircles
		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.10 Arrays of Custom Classes (2)
- Create a `RectCounter` class that keeps count of how many times it has been clicked, and handle them in an array
- `.center()` of `Rect` returns the center coordinates of the rectangle as a `Point` type

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/10.png)

```cpp
# include <Siv3D.hpp>

struct RectCounter
{
	Rect rect;

	int32 count = 0;

	// Function to update the counter
	void update()
	{
		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		if (rect.leftClicked())
		{
			++count;
		}
	}

	// Function to draw the counter
	void draw(const Font& font) const
	{
		rect.draw();
		rect.drawFrame(2, ColorF{ 0.1 });
		font(U"{}"_fmt(count)).drawAt(rect.center(), ColorF{ 0.1 });
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	Array<RectCounter> rectCounters;
	rectCounters << RectCounter{ Rect{ 100, 100, 100, 100 } };
	rectCounters << RectCounter{ Rect{ 300, 100, 100, 100 } };
	rectCounters << RectCounter{ Rect{ 500, 100, 100, 100 } };

	while (System::Update())
	{
		// Update all RectCounters
		for (auto& rectCounter : rectCounters)
		{
			rectCounter.update();
		}

		// Draw all RectCounters
		for (const auto& rectCounter : rectCounters)
		{
			rectCounter.draw(font);
		}
	}
}
```