# 80. 効率的な描画
Siv3D における効率的な描画プログラムの書き方を学びます。

## 80.1 描画負荷の指標
- 簡単な 2D 描画では、非効率なプログラムが動作に影響を与えることは少ないですが、複雑なゲームで大量の描画を行う場合は、描画負荷を意識する必要があります
- 描画処理における CPU や GPU の描画負荷を減らすには、次の 3 つの指標の最小化を目指します

### 80.1.1 Draw call の数
- GPU に対して Siv3D エンジンが発行した描画命令の回数です
- `.draw()` の回数とは異なることに注意します
- プログラムが連続する `.draw()` を呼んだとき、使用するレンダー設定やテクスチャが共通であれば、1 つの Draw call にグループ化されます
- 直前のフレームの Draw call 回数は、`Profiler::GetStat().drawCalls` で取得可能です
- 複雑なゲームであっても、Draw call は数百以下にすることを目指しましょう

### 80.1.2 三角形の描画個数
- GPU が描画した三角形の数です
- 例えば `Rect` は 2 つの三角形、`Font` による文字描画は 1 文字あたり 2 つの三角形、`Circle` は大きさに応じて 10～200 個の三角形を描画します
- 直前のフレームの三角形の描画個数は、`Profiler::GetStat().triangleCount` で取得可能です
- 描画個数が数万を超える場合、より効率的な描画方法がないか検討しましょう

### 80.1.3 draw で塗った画面上のピクセル数の累積
- 1 回 1 回の `.draw()` で塗られた画面上のピクセル数の累積です
- 画面外部分は含みません。`Scene::SetBackground()` による背景塗りつぶはカウント対象外です
- 例えば次の描画は、画面外部分は含まないので 400 x 600 = 240,000 ピクセルです

```cpp
Rect{ -400, 0, 800, 600 }.draw();
```

- 次の描画は、最終的には最後に描いた長方形しか見えませんが、上書きして塗っているため 400 x 600 x 4 = 960,000 ピクセルです

```cpp
for (int32 i = 0; i < 4; ++i)
{
	Rect{ -400, 0, 800, 600 }.draw();
}
```

- プログラムで塗られたピクセル数の累積を取得する方法はありません


### サンプルコード

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"Draw call の数: " << Profiler::GetStat().drawCalls;
		Print << U"三角形の描画回数: " << Profiler::GetStat().triangleCount;
	}
}
```


## 80.2 Draw call を減らすコツ
- 図形描画とテクスチャ（文字）描画は別々のレンダー設定を用いるため、交互に `.draw()` すると Draw call のグループ化を阻害します
- 図形は図形で、テクスチャはテクスチャでグループ化して描画することで、Draw call を減らすことができます

### 方式 1. 図形描画と文字描画を交互に行った場合
- 1 つのセルごとに 2 回の Draw call が発行され、非効率です

| 指標 | 値 |
|--|--|
| Draw call | ❌ 202 |
| 三角形 | 461 |

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
		Print << U"Draw call の数: " << Profiler::GetStat().drawCalls;
		Print << U"三角形の描画回数: " << Profiler::GetStat().triangleCount;

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

### 方式 2. 図形描画と文字描画をそれぞれグループ化した場合
- すべての `Rect` の描画が 1 つの Draw call にまとめられます
- すべての文字描画が 1 つの Draw call にまとめられます

| 指標 | 値 |
|--|--|
| Draw call | ✅ 4 |
| 三角形 | 457 |

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
		Print << U"Draw call の数: " << Profiler::GetStat().drawCalls;
		Print << U"三角形の描画回数: " << Profiler::GetStat().triangleCount;

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


## 80.3 三角形の描画個数を減らすコツ
- グリッドの描画を例に、描画方法の工夫で三角形の個数を抑制する方法を考えます

### 方式 1. すべてのセルで Rect::drawFrame()
- `Rect::drawFrmae()` は 1 回につき 8 つの三角形を描画します

| 指標 | 値 |
|--|--|
| Draw call | 3 |
| 三角形 | ❌❌ 859 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/performance/3a.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		ClearPrint();
		Print << U"Draw call の数: " << Profiler::GetStat().drawCalls;
		Print << U"三角形の描画回数: " << Profiler::GetStat().triangleCount;

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

### 方式 2. 隙間をあけて Rect::draw()
- `Rect::draw()` は 2 つの三角形を描画します
- 三角形の描画回数を抑えることができましたが、塗りピクセル数は方式 1 に比べて倍になります

| 指標 | 値 |
|--|--|
| Draw call | 3 |
| 三角形 | ❌ 259 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/performance/3b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		ClearPrint();
		Print << U"Draw call の数: " << Profiler::GetStat().drawCalls;
		Print << U"三角形の描画回数: " << Profiler::GetStat().triangleCount;

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

### 方式 3. 縦横の線だけを描く
- 背景と縦横の線だけを描くことで、三角形の描画回数と塗りピクセル数を最小限に抑えられます

| 指標 | 値 |
|--|--|
| Draw call | 3 |
| 三角形 | ✅ 103 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/performance/3c.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		ClearPrint();
		Print << U"Draw call の数: " << Profiler::GetStat().drawCalls;
		Print << U"三角形の描画回数: " << Profiler::GetStat().triangleCount;

		Rect{ 400, 400 }.draw();

		for (int32 i = 0; i <= 10; ++i)
		{
			Rect{ -1, (-1 + (i * 40)), 402, 2 }.draw(ColorF{ 0.0 });
			Rect{ (-1 + (i * 40)), -1, 2, 402 }.draw(ColorF{ 0.0 });
		}
	}
}
```


## 80.4 色付き・数字付きのグリッドを低コストで描画する
- 色付きの `Rect` をたくさん並べて描く場合、`Image` + `Texture` の使用を検討してください
- 1 回の Draw call と 2 つの三角形だけですべてのセルを描画できます。
- 数字 0～N を描きたい場合は、0 の場合に描画を省略することで描画数を節約できます

| 指標 | 値 |
|--|--|
| Draw call | 5 |
| 三角形 | 255 |

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
		Print << U"Draw call の数: " << Profiler::GetStat().drawCalls;
		Print << U"三角形の描画回数: " << Profiler::GetStat().triangleCount;

		/*
		if (grid が更新されたら)
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


## 80.5 256x256 のグリッドを描く
- **80.4** をベースに、`Transformer2D` を用いて適切なスケーリングをしながら、画面に 256 x 256 セルのグリッドを描きます
- セルが小さいため文字は省略します
- 画面の物量に対して、非常に軽量な処理になっています

| 指標 | 値 |
|--|--|
| Draw call | 4 |
| 三角形 | 1089 |

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
		Print << U"Draw call の数: " << Profiler::GetStat().drawCalls;
		Print << U"三角形の描画回数: " << Profiler::GetStat().triangleCount;

		/*
		if (grid が更新されたら)
		{
			UpdateImageFromGrid(grid, image);
			texture.fill(image);
		}
		*/

		{
			// 描画座標に 0.07 倍のスケーリングを適用する	
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
