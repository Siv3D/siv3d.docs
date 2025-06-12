# 14. Moving Shapes and Emojis
Learn how to create motion by changing variable values over time to make shapes and emojis move.

## 14.1 Motion Basics
- You can express motion by changing the position, size, angle, etc. of shapes and emojis over time
- Specifically, prepare variables that manage the motion state and change the variable values over time

### Problems with Fixed Value Addition Motion
- **Adding fixed values every frame** as in the following code causes the motion speed to vary depending on the main loop execution frequency ([**Tutorial 4.3**](./main.md))

```cpp title="Program that moves 1 pixel per frame" hl_lines="10"
# include <Siv3D.hpp>

void Main()
{
	// Circle's X coordinate
	double x = 0.0;

	while (System::Update())
	{
		x += 1.0;

		Circle{ x, 300, 50 }.draw();
	}
}
```

- In a program that moves 1 pixel per frame:
	- In a 60 FPS environment, it moves 60 pixels per second
	- In a 120 FPS environment, it moves 120 pixels per second
- This causes **unintended animation results on different computers and changes in game difficulty**

### Creating Time-based Motion
- Motion that isn't affected by frame rate uses **elapsed time from the previous frame**
- `Scene::DeltaTime()` returns the elapsed time from the previous frame (in seconds) as a `double` type
	- At 60 FPS, it's 1/60 second (about 0.016 seconds) per frame
	- At 120 FPS, it's 1/120 second (about 0.008 seconds) per frame
- By multiplying this value by "pixels to move per second," you can get the appropriate addition value for the frame rate
- For example, to move 100 pixels per second, do the following:

```cpp title="Program that moves 100 pixels per second" hl_lines="10"
# include <Siv3D.hpp>

void Main()
{
	// Circle's X coordinate
	double x = 0.0;

	while (System::Update())
	{
		x += (Scene::DeltaTime() * 100.0);

		Circle{ x, 300, 50 }.draw();
	}
}
```

- This program **stably moves at 100 pixels per second** whether the main loop runs at 60 FPS or 120 FPS


## 14.2 Moving Emojis
- Use a `Vec2` type variable to move emojis
- The emoji moves to the right at 100 pixels per second

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/motion/2.png)

```cpp title="Program where emoji moves right at 100 pixels per second" hl_lines="10 15"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🐥"_emoji };

	// Position to draw the emoji
	Vec2 pos{ 100, 300 };

	while (System::Update())
	{
		// Move position to the right at 100 pixels per second
		pos.x += (Scene::DeltaTime() * 100.0);

		// Draw emoji at current position
		emoji.drawAt(pos);
	}
}
```


## 14.3 Back and Forth Movement
- Extend the program from **14.2** so the emoji moves in the opposite direction when it reaches the screen edge
- Introduce a variable `double velocity` representing movement speed: positive `velocity` for rightward movement, negative for leftward
- When moving right and reaching the right edge of the screen (800 pixels minus 60 pixels considering emoji size), change `velocity` to negative
- Similarly, when moving left and reaching the left edge (60 pixels), change `velocity` back to positive

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/motion/3.png)

```cpp title="Program where emoji moves back and forth" hl_lines="13"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🐥"_emoji };

	// Position to draw the emoji
	Vec2 pos{ 100, 300 };

	// Emoji movement speed
	double velocity = 100.0;

	while (System::Update())
	{
		// Update position
		pos.x += (Scene::DeltaTime() * velocity);

		if (((0.0 < velocity) && (740 < pos.x)) // Reaches right edge or
			|| ((velocity < 0.0) && (pos.x < 60))) // reaches left edge
		{
			// Reverse velocity
			velocity *= -1;
		}

		emoji.drawAt(pos);
	}
}
```


## 14.4 Diagonal Movement + Bouncing
- Further extend the program from **14.3** to implement diagonal movement and bouncing
- Introduce a variable `Vec2 velocity` representing movement speed in each direction
	- Store horizontal speed in the x component and vertical speed in the y component
- `Vec2` can be operated on with operators like `+=` and `*` to manipulate x and y components together
- You can write `pos += (Scene::DeltaTime() * velocity);`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/motion/4.png)

```cpp title="Program where emoji bounces" hl_lines="13 18"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🐥"_emoji };

	// Position to draw the emoji
	Vec2 pos{ 100, 300 };

	// Emoji movement speed
	Vec2 velocity{ 100.0, 100.0 };

	while (System::Update())
	{
		// Update position
		pos += (Scene::DeltaTime() * velocity);

		if (((0.0 < velocity.x) && (740 < pos.x)) // Reaches right edge or
			|| ((velocity.x < 0.0) && (pos.x < 60))) // reaches left edge
		{
			// Reverse x-direction velocity
			velocity.x *= -1;
		}

		if (((0.0 < velocity.y) && (540 < pos.y)) // Reaches bottom edge or
			|| ((velocity.y < 0.0) && (pos.y < 60))) // reaches top edge
		{
			// Reverse y-direction velocity
			velocity.y *= -1;
		}

		emoji.drawAt(pos);
	}
}
```


## 14.5 Rotation
- Introduce a variable `double angle` representing rotation angle and rotate at 180 degrees per second

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/motion/5.png)

```cpp title="Program where emoji rotates at 180 degrees per second" hl_lines="10"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🍣"_emoji };

	// Emoji rotation angle
	double angle = 0_deg;

	while (System::Update())
	{
		// Update angle
		angle += (Scene::DeltaTime() * 180_deg);

		// Draw emoji at current position and angle
		emoji.rotated(angle).drawAt(400, 300);
	}
}
```


## 14.6 Member Variables of Shape Classes

### `Circle` Class
- The `Circle` class has the following member variables:

```cpp
struct Circle
{
	union
	{
		// Center coordinates
		Vec2 center;
		struct { double x, y; };
	};

	// Radius
	double r;
};
```

- The center X coordinate of circle `circle` is the same whether accessed as `circle.center.x` or `circle.x`

### `Rect` Class
- The `Rect` class has the following member variables:

```cpp
struct Rect
{
	union
	{
		// Top-left coordinates
		Point pos;
		struct { int32 x, y; };
	};

	union
	{
		// Width and height
		Point size;
		struct { int32 w, h; };
	};
};
```

- The top-left X coordinate of rectangle `rect` is the same whether accessed as `rect.pos.x` or `rect.x`
- The top-left Y coordinate of rectangle `rect` is the same whether accessed as `rect.pos.y` or `rect.y`
- The width of rectangle `rect` is the same whether accessed as `rect.size.x` or `rect.w`
- The height of rectangle `rect` is the same whether accessed as `rect.size.y` or `rect.h`

### `RectF` Class
- The `RectF` class has the following member variables:

```cpp
struct RectF
{
	union
	{
		// Top-left coordinates
		Vec2 pos;
		struct { double x, y; };
	};

	union
	{
		// Width and height
		Vec2 size;
		struct { double w, h; };
	};
};
```

- The `RectF` class has similar member variables to the `Rect` class, but handles coordinates and sizes with `double` type instead of `int32`


## 14.7 Moving Shapes
- Use the member variables explained in **14.6** to move shapes

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/motion/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Circle circle{ 400, 300, 10 };
	RectF rect{ 100, 200, 100, 200 };

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();
		circle.r += (deltaTime * 40.0);
		rect.x += (deltaTime * 100.0);

		circle.draw();
		rect.draw(ColorF{ 0.8, 0.9, 1.0 });
	}
}
```


## Review Checklist
- [x] Learned about the problems with fixed value addition motion
- [x] Learned how to create time-based motion
- [x] Learned how to move emojis
- [x] Learned how to implement back and forth movement and bouncing
- [x] Learned how to create rotating motion
- [x] Learned how to move shapes using member variables of shape classes