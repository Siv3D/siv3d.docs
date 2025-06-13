# 8. Changing the Background Color
Learn how to change the background color of the screen.

## 8.1 Making the Background White
- To change the background color, use `Scene::SetBackground(color)`
- White color is `Palette::White`
- Once you change the background color, it remains unchanged until you change it again

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/1.png)

```cpp hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// Make the background white
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{

	}
}
```


## 8.2 Making the Background Black
- Black color is `Palette::Black`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/2.png)

```cpp hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// Make the background black
	Scene::SetBackground(Palette::Black);

	while (System::Update())
	{

	}
}
```


## 8.3 Making the Background Other Colors
- With `Palette::***` you can use [HTML color :material-open-in-new:](https://voidproc.github.io/siv3d-palette-browser/){:target="_blank"} names

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/3.png)

```cpp hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// Specify background color with HTML color
	Scene::SetBackground(Palette::Aliceblue);

	while (System::Update())
	{

	}
}
```


## 8.4 Specifying Color with RGB (1)
- To specify color with RGB, use `ColorF{ r, g, b }`
- `r`, `g`, `b` are values in the range 0.0 to 1.0, representing red, green, and blue components respectively
- For example, light blue is `ColorF{ 0.8, 0.9, 1.0 }` (r: 80%, g: 90%, b: 100%)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/4.png)

```cpp hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// Specify background color with RGB
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{

	}
}
```


## 8.5 Specifying Color with RGB (2)
- Colors where all RGB components are equal, like `ColorF{ 0.6, 0.6, 0.6 }`, become grayscale colors (white ~ gray ~ black)
- Such colors can be written short as `ColorF{ gray }`
- For example, `ColorF{ 0.8 }` is the same as `ColorF{ 0.8, 0.8, 0.8 }`
- White is `ColorF{ 1.0 }`, gray is `ColorF{ 0.5 }`, black is `ColorF{ 0.0 }`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/5.png)

```cpp hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// Specify background color with RGB
	Scene::SetBackground(ColorF{ 0.8 });

	while (System::Update())
	{

	}
}
```


## 8.6 Specifying Color with HSV (1)
- You can also use the **HSV color system** which specifies colors with three elements: **hue**, **saturation**, and **value**
- To specify color with the HSV color system, use `HSV{ h, s, v }`

### Hue
- `h` is hue, representing color with an angle from 0.0° to 360.0°
- You can intuitively handle color families (red, yellow, green, blue, purple, etc.), and by rotating the angle (addition/subtraction), smooth color changes along the color wheel (diagram below) are possible
- Like angles, 370.0° represents the same color as 10.0°. -10.0° represents the same color as 350.0°

<div class="noshadow-90"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/hue.png"></div>

### Saturation
- `s` is saturation, expressing color vividness in the range from most pale 0.0 to most vivid 1.0
- As it approaches 0.0, it becomes more whitish (pale) color

<div class="noshadow-90"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/saturation.png"></div>


### Value

- `v` is value, controlling color brightness in the range from darkest 0.0 to brightest 1.0
- As it approaches 0.0, it becomes more blackish (dark) color

<div class="noshadow-90"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/value.png"></div>


| Component | Value Range | Description |
| --- | --- | --- |
| h | 0.0 to 360.0 (outside range also possible) | **Hue**: represents color corresponding to the color wheel |
| s | 0.0 to 1.0 | **Saturation**: smaller values become more whitish (pale) color |
| v | 0.0 to 1.0 | **Value**: smaller values become more blackish (dark) color |

- For example, `HSV{ 220.0, 0.4, 1.0 }` (h: 220.0°, s: 0.4, v: 1.0) becomes light blue

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/6.png)

```cpp hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// Specify background color with HSV
	Scene::SetBackground(HSV{ 220.0, 0.4, 1.0 });

	while (System::Update())
	{

	}
}
```

## 8.7 Specifying Color with HSV (2)
- When written short as `HSV{ h }`, it's the same as `HSV{ h, 1.0, 1.0 }`
- For example, `HSV{ 220.0 }` is `HSV{ 220.0, 1.0, 1.0 }`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/7.png)

```cpp hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// Specify background color with HSV
	Scene::SetBackground(HSV{ 220.0 });

	while (System::Update())
	{

	}
}
```

## 8.8 Changing Background Color Over Time
- Changing the background color is a lightweight operation. There's no problem writing `Scene::SetBackground()` inside the main loop
- In the following code, the elapsed time (in seconds) since the application started is obtained with `Scene::Time()`, and the value multiplied by 60 is used as the hue of the background color
	- In other words, it's a background color animation where the color makes one full cycle over 6 seconds

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/8.png)

```cpp hl_lines="7 10"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double hue = (Scene::Time() * 60);

		// Specify background color with HSV
		Scene::SetBackground(HSV{ hue });
	}
}
```




## Review Checklist
- [x] Learned to change the background color using `Scene::SetBackground()`
- [x] Learned to specify colors by name using `Palette::***`
- [x] Learned to specify colors with RGB using `ColorF{ r, g, b }` or `ColorF{ gray }`
- [x] Learned to specify colors with HSV using `HSV{ h, s, v }` or `HSV{ h }`
- [x] Learned that `Scene::SetBackground()` is lightweight and can be used inside the main loop