# 35. Optional Values
Learn the basic usage of the `Optional` type that can represent invalid values.

## 35.1 Optional
- `Optional<Type>` is a type equivalent to `std::optional<Type>`
- This type can hold all representations of the `Type` in addition to an "invalid value" that represents having no valid value
- Conceptually, it's like an `Array<Type>` with a size of either 0 or 1
	- When it has a valid value, the array size is 1 and you can access that value
	- When it's an invalid value, the size is 0 and you cannot access the value
- `Optional<Type>` values are initialized as invalid values when no initial value is given
- When an `Optional` value is output with `Print`, valid values are output as `(Optional)` followed by the value, and invalid values are output as `none`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Initialize with valid value
	Optional<Point> pos1 = Point{ 100, 200 };

	// Initialize with invalid value
	Optional<Point> pos2;

	Print << pos1;
	Print << pos2;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
(Optional)(100, 200)
none
```


## 35.2 Checking if a Valid Value is Held
- To check if an `Optional` value `opt` holds a valid value, use these methods:
	- `opt.has_value()` returns `true` if it holds a valid value
	- Check with `if (opt)` or `if (not opt)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Initialize with valid value
	Optional<Point> pos1 = Point{ 100, 200 };

	// Initialize with invalid value
	Optional<Point> pos2;

	Print << pos1.has_value();
	Print << pos2.has_value();

	if (pos1)
	{
		Print << U"pos1 has a value";
	}

	if (not pos2)
	{
		Print << U"pos2 does not have a value";
	}
	
	while (System::Update())
	{

	}
}
```
```txt title="Output"
true
false
pos1 has a value
pos2 does not have a value
```


## 35.3 Accessing Valid Values
- When an `Optional` value `opt` holds a valid value, you can access that value with `*opt`
- You can also access members using the `->` operator like `opt->x` or `opt->y`
- You must not access the value when it doesn't hold a valid value

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Initialize with valid value
	Optional<Point> pos = Point{ 100, 200 };

	if (pos)
	{
		Print << *pos;

		pos->x += 20;
		pos->y += 30;

		Print << *pos;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
(100, 200)
(120, 230)
```


## 35.4 Setting to Invalid Value
- `none` is a constant representing an invalid value for `Optional` types
- To assign an invalid value to an `Optional` value `opt`, use `opt = none`
- `opt.reset()` does the same thing

```cpp
# include <Siv3D.hpp>

void Main()
{
	Optional<Point> pos = Point{ 100, 200 };
	Print << pos;

	pos = none;
	Print << pos;

	pos = Point{ 300, 400 };
	Print << pos;

	pos.reset();
	Print << pos;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
(Optional)(100, 200)
none
(Optional)(300, 400)
none
```


## 35.5 Getting Valid Value or Alternative Value
- `.value_or(defaultValue)` returns the valid value if it holds one, or returns `defaultValue` if it's an invalid value

```cpp
# include <Siv3D.hpp>

void Main()
{
	Optional<Point> pos1 = Point{ 100, 200 };

	Optional<Point> pos2;

	// pos1 holds a valid value so returns Point{ 100, 200 }
	Print << pos1.value_or(Point{ 0, 0 });

	// pos2 doesn't hold a valid value so returns Point{ 0, 0 }
	Print << pos2.value_or(Point{ 0, 0 });

	while (System::Update())
	{

	}
}
```
```txt title="Output"
(100, 200)
(0, 0)
```


## 35.6 Combining with if
- By combining with `if` as follows, you can write concise code for processing when an `Optional` value holds a valid value

```cpp
# include <Siv3D.hpp>

Optional<int32> GetResult1()
{
	return 123;
}

Optional<int32> GetResult2()
{
	return none;
}

void Main()
{
	if (const auto result = GetResult1())
	{
		Print << *result;
	}

	if (const auto result = GetResult2())
	{
		Print << *result;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
123
```


## 35.7 Usage Example (1)
- This is a sample that creates arrows by dragging the mouse
- The position where the mouse left button was pressed is represented with `Optional<Point>`, and when it holds a valid value, an arrow is drawn from that position to the current mouse cursor position
- When the mouse left button is released, it's set to an invalid value
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/optional/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Position where mouse left button was pressed
	Optional<Point> start;

	while (System::Update())
	{
		ClearPrint();
		Print << start;

		if (MouseL.down()) // When mouse left button is pressed
		{
			// Assign mouse cursor position as valid value
			start = Cursor::Pos();
		}
		else if (MouseL.up()) // When mouse left button is released
		{
			// Set to invalid value
			start.reset();
		}

		// If it holds a valid value
		if (start)
		{
			// Draw circle centered at start point
			start->asCircle(10).draw(ColorF{ 0.2 });

			// Draw arrow to current mouse cursor position
			Line{ *start, Cursor::Pos() }.drawArrow(6, SizeF{ 20, 20 }, ColorF{ 0.2 });
		}
	}
}
```


## 35.8 Usage Example (2)
- This is a sample that moves items by dragging with the mouse
- The type of item being dragged is represented with `Optional<int32>`, and when it holds a valid value, that item is drawn at the mouse cursor position
- When dragging ends, it's set to an invalid value
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/optional/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture item1Texture{ U"🍌"_emoji };
	const Texture item2Texture{ U"🍎"_emoji };

	const Circle item1Circle{ 100, 200, 60 };
	const Circle item2Circle{ 100, 400, 60 };
	const Rect boxRect{ 500, 200, 200, 200 };

	Optional<int32> grabbedItem;

	while (System::Update())
	{
		ClearPrint();
		Print << grabbedItem;

		if (grabbedItem || item1Circle.mouseOver() || item2Circle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		if (item1Circle.leftClicked())
		{
			grabbedItem = 1;
		}
		else if (item2Circle.leftClicked())
		{
			grabbedItem = 2;
		}
		else if (MouseL.up())
		{
			grabbedItem.reset();
		}

		item1Texture.drawAt(item1Circle.center);
		item2Texture.drawAt(item2Circle.center);
		boxRect.draw();

		// Item is being grabbed
		if (grabbedItem)
		{
			// If cursor is over the box
			if (boxRect.mouseOver())
			{
				// Draw box frame in red
				boxRect.drawFrame(0, 20, ColorF{ 1.0, 0.5, 0.5 });
			}

			// Draw item
			if (grabbedItem == 1)
			{
				item1Texture.drawAt(Cursor::Pos());
			}
			else if (grabbedItem == 2)
			{
				item2Texture.drawAt(Cursor::Pos());
			}
		}
	}
}
```