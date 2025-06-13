# 80. Efficient Rendering
Learn how to write efficient rendering programs in Siv3D.

## 80.1 Rendering Load Indicators
- For simple 2D rendering, inefficient programs rarely affect performance, but when performing large-scale rendering in complex games, you need to be mindful of rendering load
- To reduce CPU and GPU rendering load in drawing processes, aim to minimize the following three indicators

### 80.1.1 Draw Call Count
- The number of drawing commands issued by the Siv3D engine to the GPU
- Note that this is different from the number of `.draw()` calls
- When the program calls consecutive `.draw()` methods, if they use common render settings or textures, they are grouped into a single draw call
- The draw call count from the previous frame can be obtained with `Profiler::GetStat().drawCalls`
- Even for complex games, aim to keep draw calls below a few hundred

### 80.1.2 Triangle Count
- The number of triangles drawn by the GPU
- For example, `Rect` draws 2 triangles, `Font` text rendering draws 2 triangles per character, and `Circle` draws 10-200 triangles depending on size
- The triangle count from the previous frame can be obtained with `Profiler::GetStat().triangleCount`
- If the draw count exceeds tens of thousands, consider whether there are more efficient drawing methods

### 80.1.3 Cumulative Pixels Drawn
- The cumulative number of pixels painted on screen by each `.draw()` call
- Off-screen areas are not included. Background fills by `Scene::SetBackground()` are not counted
- For example, the following drawing paints 400 x 600 = 240,000 pixels (excluding off-screen areas)

```cpp
Rect{ -400, 0, 800, 600 }.draw();
```

- The following drawing ultimately shows only the last rectangle drawn, but since it paints over itself, it totals 400 x 600 x 4 = 960,000 pixels

```cpp
for (int32 i = 0; i < 4; ++i)
{
	Rect{ -400, 0, 800, 600 }.draw();
}
```

- There is no way to get the cumulative pixel count painted by the program


### Sample Code

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"Draw call count: " << Profiler::GetStat().drawCalls;
		Print << U"Triangle count: " << Profiler::GetStat().triangleCount;
	}
}
```


## 80.2 Tips for Reducing Draw Calls
- Shape drawing and texture (text) drawing use different render settings, so alternating `.draw()` calls between them inhibits draw call grouping
- Draw calls can be reduced by grouping shapes together and textures together

### Method 1. Alternating Shape and Text Drawing
- Two draw calls are issued per cell, which is inefficient

| Indicator | Value |
|--|--|
| Draw calls | ❌ 202 |
| Triangles | 461 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/performance/2a.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Heavy };

	while (System::Update())
	{
		ClearPrint();
		Print << U"Draw call count: " << Profiler::GetStat().drawCalls;
		Print << U"Triangle count: " << Profiler::GetStat().triangleCount;

		for (int32 y = 0; y < 10; ++y)
		{
			for (int32 x = 0; x < 10; ++x)
			{
				Rect{ (x * 40), (y * 40), 38 }.draw();
				font(U"0").drawAt(20, Vec2{ (x * 40) + 20, (y * 40) + 20 }, ColorF{ 0.8 });
			}
		}
	}
}
```

### Method 2. Grouping Shape and Text Drawing Separately
- All `Rect` draws are combined into one draw call
- All text draws are combined into one draw call

| Indicator | Value |
|--|--|
| Draw calls | ✅ 4 |
| Triangles | 457 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/performance/2b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Heavy };

	while (System::Update())
	{
		ClearPrint();
		Print << U"Draw call count: " << Profiler::GetStat().drawCalls;
		Print << U"Triangle count: " << Profiler::GetStat().triangleCount;

		for (int32 y = 0; y < 10; ++y)
		{
			for (int32 x = 0; x < 10; ++x)
			{
				Rect{ (x * 40), (y * 40), 38 }.draw();
			}
		}

		for (int32 y = 0; y < 10; ++y)
		{
			for (int32 x = 0; x < 10; ++x)
			{
				font(U"0").drawAt(20, Vec2{ (x * 40) + 20, (y * 40) + 20 }, ColorF{ 0.8 });
			}
		}
	}
}
```


## 80.3 Tips for Reducing Triangle Count
- Using grid drawing as an example, let's consider ways to reduce triangle count through drawing method improvements

### Method 1. Using Rect::drawFrame() for All Cells
- `Rect::drawFrame()` draws 8 triangles per call

| Indicator | Value |
|--|--|
| Draw calls | 3 |
| Triangles | ❌❌ 859 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/performance/3a.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		ClearPrint();
		Print << U"Draw call count: " << Profiler::GetStat().drawCalls;
		Print << U"Triangle count: " << Profiler::GetStat().triangleCount;

		Rect{ 400, 400 }.draw();

		for (int32 y = 0; y < 10; ++y)
		{
			for (int32 x = 0; x < 10; ++x)
			{
				Rect{ (x * 40), (y * 40), 40 }.drawFrame(1, 0, ColorF{ 0.0 });
			}
		}
	}
}
```

### Method 2. Using Rect::draw() with Gaps
- `Rect::draw()` draws 2 triangles
- Triangle count is reduced compared to method 1, but pixel fill count doubles

| Indicator | Value |
|--|--|
| Draw calls | 3 |
| Triangles | ❌ 259 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/performance/3b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		ClearPrint();
		Print << U"Draw call count: " << Profiler::GetStat().drawCalls;
		Print << U"Triangle count: " << Profiler::GetStat().triangleCount;

		Rect{ 400, 400 }.draw(ColorF{ 0.0 });

		for (int32 y = 0; y < 10; ++y)
		{
			for (int32 x = 0; x < 10; ++x)
			{
				Rect{ (x * 40), (y * 40), 40 }.stretched(-1).draw();
			}
		}
	}
}
```

### Method 3. Drawing Only Vertical and Horizontal Lines
- By drawing only the background and vertical/horizontal lines, triangle count and pixel fill count are minimized

| Indicator | Value |
|--|--|
| Draw calls | 3 |
| Triangles | ✅ 103 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/performance/3c.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		ClearPrint();
		Print << U"Draw call count: " << Profiler::GetStat().drawCalls;
		Print << U"Triangle count: " << Profiler::GetStat().triangleCount;

		Rect{ 400, 400 }.draw();

		for (int32 i = 0; i <= 10; ++i)
		{
			Rect{ -1, (-1 + (i * 40)), 402, 2 }.draw(ColorF{ 0.0 });
			Rect{ (-1 + (i * 40)), -1, 2, 402 }.draw(ColorF{ 0.0 });
		}
	}
}
```


## 80.4 Drawing Colored and Numbered Grids Efficiently
- When drawing many colored `Rect` objects in a grid, consider using `Image` + `Texture`
- All cells can be drawn with just one draw call and 2 triangles
- For numbers 0 to N, drawing performance can be saved by skipping rendering when the value is 0

| Indicator | Value |
|--|--|
| Draw calls | 5 |
| Triangles | 255 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/performance/4.png)

```cpp
# include <Siv3D.hpp>

Color ToColor(int32 n)
{
	static const std::array<Color, 4> palettes = { Colormap01(0), Colormap01(0.33), Colormap01(0.67), Colormap01(1.0) };
	return palettes[n];
}

void UpdateImageFromGrid(const Grid<int32>& grid, Image& image)
{
	assert(grid.size() == image.size());

	for (int32 y = 0; y < grid.height(); ++y)
	{
		for (int32 x = 0; x < grid.width(); ++x)
		{
			image[y][x] = ToColor(grid[y][x]);
		}
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Heavy };
	const TextStyle textStyle = TextStyle::OutlineShadow(0.2, ColorF{ 0.1 }, Vec2{ 2, 2 }, ColorF{ 0.1 });

	Grid<int32> grid(10, 10, 0);
	for (size_t y = 0; y < grid.height(); ++y)
	{
		for (size_t x = 0; x < grid.width(); ++x)
		{
			grid[y][x] = Random(0, 3);
		}
	}

	Image image{ grid.size() };
	UpdateImageFromGrid(grid, image);
	DynamicTexture texture{ image };

	while (System::Update())
	{
		ClearPrint();
		Print << U"Draw call count: " << Profiler::GetStat().drawCalls;
		Print << U"Triangle count: " << Profiler::GetStat().triangleCount;

		/*
		if (grid was updated)
		{
			UpdateImageFromGrid(grid, image);
			texture.fill(image);
		}
		*/

		{
			const ScopedRenderStates2D sampler{ SamplerState::ClampNearest };
			texture.resized(grid.size() * 40).draw();
		}

		for (size_t i = 0; i <= grid.width(); ++i)
		{
			Rect{ -1, (-1 + (i * 40)), (grid.width() * 40 + 2), 2}.draw();
			Rect{ (-1 + (i * 40)), -1, 2, (grid.height() * 40 + 2) }.draw();
		}

		for (int32 y = 0; y < grid.height(); ++y)
		{
			for (int32 x = 0; x < grid.width(); ++x)
			{
				if (const int32 n = grid[y][x])
				{
					const Vec2 pos{ (x * 40 + 20), (y * 40 + 20) };
					font(n).drawAt(textStyle, 25, pos);
				}
			}
		}
	}
}
```


## 80.5 Drawing a 256x256 Grid
- Based on **80.4**, this uses `Transformer2D` to apply appropriate scaling while drawing a 256 x 256 cell grid to the screen
- Text is omitted since cells are small
- This achieves very lightweight rendering for the amount of information displayed

| Indicator | Value |
|--|--|
| Draw calls | 4 |
| Triangles | 1089 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/performance/5.png)

```cpp
# include <Siv3D.hpp>

Color ToColor(int32 n)
{
	static const std::array<Color, 4> palettes = { Colormap01(0), Colormap01(0.33), Colormap01(0.67), Colormap01(1.0) };
	return palettes[n];
}

void UpdateImageFromGrid(const Grid<int32>& grid, Image& image)
{
	assert(grid.size() == image.size());

	for (int32 y = 0; y < grid.height(); ++y)
	{
		for (int32 x = 0; x < grid.width(); ++x)
		{
			image[y][x] = ToColor(grid[y][x]);
		}
	}
}

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Grid<int32> grid(256, 256, 0);
	for (size_t y = 0; y < grid.height(); ++y)
	{
		for (size_t x = 0; x < grid.width(); ++x)
		{
			grid[y][x] = Random(0, 3);
		}
	}

	Image image{ grid.size() };
	UpdateImageFromGrid(grid, image);
	DynamicTexture texture{ image };

	while (System::Update())
	{
		ClearPrint();
		Print << U"Draw call count: " << Profiler::GetStat().drawCalls;
		Print << U"Triangle count: " << Profiler::GetStat().triangleCount;

		/*
		if (grid was updated)
		{
			UpdateImageFromGrid(grid, image);
			texture.fill(image);
		}
		*/

		{
			// Apply 0.07x scaling to drawing coordinates	
			const Transformer2D tr{ Mat3x2::Scale(0.07) };

			{
				const ScopedRenderStates2D sampler{ SamplerState::ClampNearest };
				texture.resized(grid.size() * 40).draw();
			}

			for (size_t i = 0; i <= grid.width(); ++i)
			{
				Rect{ -1, (-1 + (i * 40)), (grid.width() * 40 + 2), 2 }.draw();
				Rect{ (-1 + (i * 40)), -1, 2, (grid.height() * 40 + 2) }.draw();
			}
		}
	}
}
```