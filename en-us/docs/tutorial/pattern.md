# 12. Drawing Patterns
Learn how to arrange circles and rectangles using loops to draw patterns.

## 12.1 Arranging Circles
- By combining `for` loops with shape drawing, you can draw many shapes with short code

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 i = 0; i < 6; ++i)
		{
			Circle{ (i * 100), 100, 30 }.draw(Palette::Skyblue);
		}

		for (int32 i = 0; i < 6; ++i)
		{
			Circle{ (50 + i * 100), 200, 30 }.draw(Palette::Seagreen);
		}
	}
}
```


## 12.2 Arranging Circles with Nested Loops
- By wrapping a loop that arranges shapes horizontally with another loop that arranges them vertically, you can arrange shapes both horizontally and vertically
- It's clear to name the horizontal loop variable `x` and the vertical loop variable `y`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 y = 0; y < 4; ++y) // Vertical direction
		{
			for (int32 x = 0; x < 6; ++x) // Horizontal direction
			{
				Circle{ (x * 100), (y * 100), 30 }.draw(Palette::Skyblue);
			}
		}
	}
}
```


## 12.3 Making it More Complex
- By focusing on the loop variables `x` and `y` within the nested loop and changing the shapes based on whether their sum is odd or even, you can draw more complex patterns
- You can determine if an integer `n` is even with `(n % 2) == 0`
- There's also a function `IsEven(n)` that returns a `bool` indicating whether integer `n` is even

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 y = 0; y < 4; ++y)
		{
			for (int32 x = 0; x < 6; ++x)
			{
				if (IsEven(x + y)) // If x + y is even
				{
					// Draw a large circle
					Circle{ (x * 100), (y * 100), 30 }.draw(Palette::Skyblue);
				}
				else // If odd
				{
					// Draw a small circle
					Circle{ (x * 100), (y * 100), 10 }.draw(Palette::Skyblue);
				}
			}
		}
	}
}
```


## 12.4 Arranging Rectangles
- Arrange rectangles like pillars

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 6; ++x)
		{
			Rect{ (x * 100), 0, 80, 600 }.draw(Palette::Skyblue);
		}
	}
}
```


## 12.5 Gradually Changing Size
- Make the rectangle length gradually increase as the loop variable `x` increases

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 6; ++x)
		{
			Rect{ (x * 100), 0, 80, ((x + 1) * 100) }.draw(Palette::Skyblue);
		}
	}
}
```


## 12.6 Gradually Changing Color
- Make the color gradually change as the loop variable `x` increases

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 6; ++x)
		{
			Rect{ (x * 100), 0, 80, 600 }.draw(ColorF{ 0.0, (x * 0.15), (x * 0.15) });
		}
	}
}
```


## 12.7 Gradually Changing Hue
- Make the hue gradually change as the loop variable `x` increases
- The leftmost and rightmost rectangles have hues of 0 and 360 respectively, which are the same color

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 10; ++x)
		{
			Rect{ (x * 80), 0, 80, 600 }.draw(HSV{ (x * 40), 0.5, 1.0 });
		}
	}
}
```


## 🧩 Practice
Try drawing the following patterns

### Practice ① Ripples

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/8-1.png)

??? info "Hint"
	- Gradually change the radius of `Circle`

??? success "Sample Solution"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(Palette::White);

		while (System::Update())
		{
			for (int32 i = 0; i < 5; ++i)
			{
				Circle{ 400, 300, ((i + 1) * 50) }.drawFrame(4, Palette::Skyblue);
			}
		}
	}
	```


### Practice ② Checkerboard Pattern

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/8-2.png)

??? info "Hint"
	- Use nested loops
	- Change color based on the parity of `x` and `y`

??? success "Sample Solution"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(Palette::White);

		while (System::Update())
		{
			for (int32 x = 0; x < 8; ++x)
			{
				for (int32 y = 0; y < 6; ++y)
				{
					if (IsEven(x + y))
					{
						Rect{ (x * 100), (y * 100), 100 }.draw(Palette::Skyblue);
					}
				}
			}
		}
	}
	```


### Practice ③ Grid

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/8-3.png)

??? info "Hint"
	- Use nested loops
	- Draw frames with `Rect`'s `.drawFrame()`
	- Or fill the interior with `Rect`'s `.draw()`

??? success "Sample Solution 1"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(Palette::White);

		while (System::Update())
		{
			for (int32 x = 0; x < 8; ++x)
			{
				for (int32 y = 0; y < 6; ++y)
				{
					Rect{ (x * 100), (y * 100), 100 }.drawFrame(2, 0, Palette::Skyblue);
				}
			}
		}
	}
	```

??? success "Sample Solution 2"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(Palette::Skyblue);

		while (System::Update())
		{
			for (int32 x = 0; x < 8; ++x)
			{
				for (int32 y = 0; y < 6; ++y)
				{
					Rect{ (2 + x * 100), (2 + y * 100), 96 }.draw();
				}
			}
		}
	}
	```


## Review Checklist
- [x] Learned how to arrange and draw shapes by combining loops with shape drawing
- [x] Learned how to arrange shapes vertically and horizontally using nested loops
- [x] Learned how to create variations in size, color, etc. by utilizing loop variables