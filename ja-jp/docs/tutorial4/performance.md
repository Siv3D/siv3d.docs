# 80. 効率的な描画

描画負荷を減らすには、毎フレーム、次の指標の最小化を目指してください。

### 1.1 Draw call の数
GPU に対して Siv3D エンジンが発行した描画命令の回数。`.draw()` の回数とは異なることに注意。連続する `.draw()` を呼んだとき、使用するレンダー設定やテクスチャが共通であれば、1 つの Draw call にグループ化される。`Profiler::GetStat().drawCalls` で取得可能。

複雑なゲームであっても、Draw call は数百以下にすることが望ましい。

### 1.2 三角形の描画個数
GPU が描画した三角形の数。例えば `Rect` は 2 つの三角形、`Font` による文字描画は 1 文字あたり 2 つの三角形。`Circle` は大きさに応じて 10～200 個の三角形。`Profiler::GetStat().triangleCount` で取得可能。

数万を超える場合、より効率的な描画方法がないか検討したほうが良い。

### 1.3 `.draw()` によって塗った画面上のピクセル数の累積
例えば下記は画面外部分は含まないので 400x600 = 240,000 ピクセル。`Scene::SetBackground()` で塗りつぶした背景色はカウント対象外。

```cpp
Rect{ -400, 0, 800, 600 }.draw();
```

下記は最終的には最後に描いた長方形しか見えないが、上書きして塗っているため 400x600x4 = 960,000 ピクセル。

```cpp
for (int32 i = 0; i < 4; ++i)
{
	Rect{ -400, 0, 800, 600 }.draw();
}
```

プログラムでピクセル数を取得する手段はない。

なるべく小さくするよう努力する。例えば、全面塗りつぶしの代わりに `Scene::SetBackground()` を使えば大きな節約になる。

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

## 2. Draw call を減らすコツ
図形描画とテクスチャ（文字）描画は別々のレンダー設定を用いるため、交互に `.draw()` すると Draw call のグループ化を阻害します。

### 方式 1: 図形描画と文字描画を交互に行う
| 指標 | 値 |
|--|--|
| Draw call | ❌ 202 |
| 三角形 | 461 |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

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

### 方式 2: 図形描画と文字描画をそれぞれグループ化する
| 指標 | 値 |
|--|--|
| Draw call | ✅ 4 |
| 三角形 | 457 |

- すべての `Rect` の描画が 1 つの Draw call にまとめられている
- すべての文字描画が 1 つの Draw call にまとめられている

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

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

## 3. 三角形の描画個数を減らすコツ
グリッドの描画で大量の長方形を描く場合、描画方法の工夫で三角形の個数を抑制できます。

### 方式 1. すべてのセルで `Rect::drawFrame()` する
| 指標 | 値 |
|--|--|
| Draw call | 3 |
| 三角形 | ❌❌ 857 |

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

		for (int32 y = 0; y < 10; ++y)
		{
			for (int32 x = 0; x < 10; ++x)
			{
				Rect{ (x * 40), (y * 40), 40 }.drawFrame(1, 0);
			}
		}
	}
}
```

### 方式 2. 隙間をあけて `Rect::draw()` する
| 指標 | 値 |
|--|--|
| Draw call | 3 |
| 三角形 | ❌ 257 |

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
| 指標 | 値 |
|--|--|
| Draw call | 3 |
| 三角形 | ✅ 99 |

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

		for (int32 i = 0; i <= 10; ++i)
		{
			Rect{ -1, (-1 + (i * 40)), 402, 2 }.draw();
			Rect{ (-1 + (i * 40)), -1, 2, 402 }.draw();
		}
	}
}
```


## 4. 色付き・数字付きのグリッドを低コストで描画する
- 色付き `Rect` をたくさん描く必要がある場合、`Image` + `Texture` の使用を検討してください。1 回の Draw call と 2 つの三角形だけですべてのセルを描画できます。
- 数字 0～N を描きたい場合、0 の場合に描画を省略することで描画数を節約できます。

| 指標 | 値 |
|--|--|
| Draw call | 5 |
| 三角形 | 255 |

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
	const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };
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

## 5. 256x256 のグリッドを描く
- `Transformer2D` を用いて、4. から描画部分の変更なしで画面内に 256x256 のグリッドを描きます。文字は読めないので省略します。非常に軽量な処理になっています。

| 指標 | 値 |
|--|--|
| Draw call | 4 |
| 三角形 | 1089 |

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
