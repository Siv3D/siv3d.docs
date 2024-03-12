# 勉強会 3 コマ目（色と図形）

## 1. 背景の色を変える
背景の色は `Scene::SetBackground(色)` で変更できます。色の指定方法はさまざまな方法があります。

| 色の指定方法 | 説明 |
| --- | --- |
| `Palette::色名` | Siv3D で定義されている色を指定する。<br>`Palette::White`, `Palette::Black`, `Palette::Skyblue` など |
| `ColorF{ 赤, 緑, 青 }` | 赤、緑、青成分を 0.0～1.0 の範囲で指定する |
| `ColorF{ グレースケール }` | グレースケール 0.0～1.0 の範囲で指定する |
| `HSV{ 色相, 彩度, 明度 }` | 色相を 0.0～360.0（下図）、彩度、明度を 0.0～1.0 の範囲で指定する<br><br><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/6-2.png"> |
| `HSV{ 色相 }` | 色相を 0.0～360.0 の範囲で指定する |
| `ColorF{ U"#77FF00" }` | 16 進数で色を指定する |

??? info "HSV 表色系における色相"
	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/6-2.png)

### 1.1 Palette::色名 で背景色を指定する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景を白色にする
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{

	}
}
```

### 1.2 ColorF{ 赤, 緑, 青 } で背景色を指定する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景色を RGB で指定する
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{

	}
}
```

### 1.3 ColorF{ グレースケール } で背景色を指定する

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景色をグレースケールで指定する
	Scene::SetBackground(ColorF{ 0.8 }); // ColorF{ 0.8, 0.8, 0.8 } と同じ

	while (System::Update())
	{

	}
}
```

### 1.4 HSV{ 色相, 彩度, 明度 } で背景色を指定する

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景色を HSV で指定する
	Scene::SetBackground(HSV{ 240.0, 0.8, 0.8 });

	while (System::Update())
	{

	}
}
```


## 2. 座標系
ウィンドウ内で、背景色を変えられる部分が **画面（シーン）**です。Siv3D はこの領域に文字や図形、画像を表示できます。画面のサイズは、基本の状態では**幅 800 ピクセル**、**高さ 600 ピクセル**です。

画面上の位置を表す座標系は、最も左上のピクセルが「X 座標 0」「Y 座標 0」を表す (0, 0) です。右に進むと X 座標が大きくなり、下に進むと Y 座標が大きくなります。基本の状態の画面の中心ピクセルの座標は (400, 300) になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// 現在のマウスカーソル座標を表示する
		Print << Cursor::Pos();
	}
}
```


## 3. 図形を描く

### 3.1 円を描く
Siv3D では、何かを描く命令はメインループの中に記述します。円を描くときは `Circle` を作成し、その `.draw()` を呼びます。`Circle` は `Circle{ x, y, r }` のように、中心の X 座標、Y 座標、半径を指定して作成します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 円を描く
		Circle{ 400, 300, 20 }.draw();
	}
}
```

!!! info "Circle の構造"
	`Circle` は次のようなクラスです。
	```cpp
	struct Circle
	{
		double x;
		double y;
		double r;
	};
	```


`Circle` は、二次元座標を表す `Point` 型や `Vec2` 型の値を使って、`Circle{ pos, r }` のように、2 つの引数から作成することもできます。`Cursor::Pos()` は現在のマウスカーソルの座標を `Point` 型で返す関数なので、これを使ってマウスに追随する円を描くことができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// マウスに追随する円を描く
		Circle{ Cursor::Pos(), 100 }.draw();
	}
}
```

!!! info "Point や Vec2 の構造"
	`Point` と `Vec2` は次のようなクラスです。
	```cpp
	struct Point
	{
		int32 x;
		int32 y;
	};

	struct Vec2
	{
		double x;
		double y;
	};
	```

図形に色を付けたいときは `.draw()` 関数の引数に色を渡します。色を指定しなかった場合は白色になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ 100, 200, 40 }.draw(); // 色を指定しない場合は白色

		Circle{ 200, 200, 40 }.draw(Palette::Green);

		Circle{ 300, 200, 40 }.draw(Palette::Skyblue);

		Circle{ 400, 200, 40 }.draw(ColorF{ 1.0, 0.8, 0.0 });

		Circle{ 500, 200, 40 }.draw(ColorF{ 0.8 });

		Circle{ 600, 200, 40 }.draw(HSV{ 160.0, 0.5, 1.0 });

		Circle{ 700, 200, 40 }.draw(HSV{ 160.0 });
	}
}
```


`ColorF` と `HSV` は、**不透明度**を指定できます。不透明度は 0.0 から 1.0 の範囲で指定します。

0.0 は完全に透明、1.0 は完全に不透明で、0.5 のときは背景色と描画色が 50% ずつ混ざった色になります。

不透明度 `a` （アルファ）は、`ColorF{ r, g, b, a }`, `ColorF{ gray, a }`, `HSV{ h, s, v, a }`, `HSV{ h, a }` のように、最後の引数に指定します。不透明度を指定しなかった場合の不透明度は 1.0 です。

`Scene::SetBackground()` に指定する色については、不透明度は無視されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 中央に白い円を描く
		Circle{ 400, 300, 200 }.draw();

		// 左に半透明の円を描く
		Circle{ 200, 300, 200 }.draw(ColorF{ 1.0, 0.0, 0.0, 0.9 });

		// 右に半透明の円を描く
		Circle{ 600, 300, 200 }.draw(HSV{ 240.0, 0.5, 1.0, 0.2 });

		// マウスカーソルの位置に半透明の円を描く
		Circle{ Cursor::Pos(), 100 }.draw(ColorF{ 0.0, 0.5 });
	}
}
```


### 3.2 長方形を描く
長方形を描くときは `Rect` を作成して `.draw()` します。

`Rect{ x, y, w, h }` は、左上座標が (x, y), 幅が w, 高さが h の長方形です。

`Rect{ x, y, s }` は引数を省略したオーバーロードで、左上座標が (x, y), 幅と高さが s の正方形です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 長方形を描く 
		Rect{ 20, 40, 400, 100 }.draw();

		// 正方形を描く 
		Rect{ 100, 200, 80 }.draw(Palette::Orange);
	}
}
```

!!! info "Rect の構造"
	`Rect` は次のようなクラスです。
	```cpp
	struct Rect
	{
		int32 x;
		int32 y;
		int32 w;
		int32 h;
	};
	```

座標や大きさを `double` 型で扱いたい場合は、`Rect` の代わりに `RectF` 使います。

`Scene::Time()` は、プログラム開始からの経過時間を秒 （`double` 型）で返します。

下記のコードでは、長方形の幅 `w` が毎秒 20.0 のペースで増えていきます。`double` 型である `w` に合わせるため、`Rect` ではなく `RectF` を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double w = (Scene::Time() * 20.0);

		// 長方形を描く 
		RectF{ 20, 40, w, 100 }.draw();
	}
}
```

!!! info "RectF の構造"
	`RectF` は次のようなクラスです。
	```cpp
	struct RectF
	{
		double x;
		double y;
		double w;
		double h;
	};
	```

### 3.3 円や長方形の枠を描く
円や長方形の枠だけを描きたい場合、`.draw()` の代わりに `.drawFrame(innerThickness, outerThickness, color)` を使います。

`innerThickness` は内側方向への太さ、`outerThickness` は外側方向への太さを表す `double` 型の値です。`innerThickness` と `outerThickness` には、それぞれ 0.0 以上の値を指定します。

`color` を省略した場合、`.draw()` と同様に白色で描画されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 長方形の枠を描く
		Rect{ 100, 100, 100, 30 }
			.drawFrame(3, 0);

		// 長方形の枠を描く
		Rect{ 220, 100, 100, 30 }
			.drawFrame(0, 3);

		// 長方形の枠を描く
		Rect{ 200, 200, 400, 100 }
			.drawFrame(3, 3, Palette::Orange);

		// 円の枠を描く
		Circle{ Cursor::Pos(), 40 }
			.drawFrame(1, 1, Palette::Seagreen);
	}
}
```


### 3.4 長方形のグラデーション
`Rect` や `RectF` を `.draw()` する際、`.draw(Arg::top = 色, Arg::bottom = 色)` のように書くことで、上下方向のグラデーションで長方形を描画できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// グラデーションで長方形を描く
		Rect{ 0, 0, 600, 500 }
			.draw(Arg::top = ColorF{ 0.5, 0.7, 0.9 }, Arg::bottom = ColorF{ 0.5, 0.9, 0.7 });
	}
}
```

`.draw(Arg::left = 色, Arg::right = 色)` のように書くことで、左右方向のグラデーションで長方形を描画できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// グラデーションで長方形を描く
		Rect{ 0, 0, 600, 500 }
			.draw(Arg::left = ColorF{ 0.5, 0.7, 0.9 }, Arg::right = ColorF{ 0.5, 0.9, 0.7 });
	}
}
```


### 3.5 線分を描く
`Line{ x1, y1, x2, y2 }` で線分を作成できます。`Line` は `.draw(太さ, 色)` で描画できます。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-1.1.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// (100, 100) - (600, 200) を結ぶ太さ 2 の線分を描く
		Line{ 100, 100, 600, 200 }.draw(2, ColorF{ 0.3, 0.5, 0.7 });

		// (200, 200) - マウスカーソル を結ぶ太さ 10 の線分を描く
		Line{ 200, 200, Cursor::Pos() }.draw(10, ColorF{ 0.3, 0.5, 0.7 });
	}
}
```


### 3.6 三角形を描く
`Triangle{ x1, y1, x2, y2, x3, y3 }` に時計回りで座標を指定することで三角形を作成できます。`Triangle` は `.draw(色)` で描画できます。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-1.2.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// (100, 100) - (600, 100) - (200, 500) からなる三角形を描く
		Triangle{ 100, 100, 600, 100, 200, 500 }.draw(ColorF{ 0.3, 0.5, 0.7 });
	}
}
```


### 3.7 四角形を描く
`Quad{ x1, y1, x2, y2, x3, y3, x4, y4 }` に時計回りで座標を指定することで四角形を作成できます。ただし凹の角（180° より大きい角）があってはいけません。長方形を描く場合は `Rect` や `RectF` を使ったほうが便利です。`Quad` は `.draw(色)` で描画できます。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-1.3.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// (100, 100) - (600, 100) - (500, 500) - (200, 400) からなる三角形を描く
		Quad{ 100, 100, 600, 100, 500, 500, 200, 400 }.draw(ColorF{ 0.3, 0.5, 0.7 });
	}
}
```


### 3.8 これ以外の図形
Siv3D にはこれ以外にも多くの種類の図形を簡単に描画できます。詳しくは [17. 図形描画](../tutorial2/shape.md){:target="_blank"} を参照してください。


## 4. 模様を描く
ループと図形描画を組み合わせると、模様やパターンを簡単に描くことができます。

### 4.1 円を並べる

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 i = 0; i < 5; ++i)
		{
			Circle{ (i * 100), 100, 30 }.draw(Palette::Skyblue);
		}

		for (int32 i = 0; i < 5; ++i)
		{
			Circle{ (50 + i * 100), 200, 30 }.draw(Palette::Seagreen);
		}
	}
}
```

### 4.2 円を縦横に並べる（二重ループ）

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 y = 0; y < 4; ++y) // 縦方向
		{
			for (int32 x = 0; x < 6; ++x) // 横方向
			{
				Circle{ (x * 100), (y * 100), 30 }.draw(Palette::Skyblue);
			}
		}
	}
}
```

### 4.3 条件によって円の大きさを変える

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/3.png)

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
				if ((x + y) % 2 == 0)
				{
					Circle{ (x * 100), (y * 100), 10 }.draw(Palette::Skyblue);
				}
				else
				{
					Circle{ (x * 100), (y * 100), 30 }.draw(Palette::Skyblue);
				}
			}
		}
	}
}
```

### 4.4 色を変えながら長方形を並べる

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 6; ++x)
		{
			Rect{ (x * 100), 0, 80, 600 }.draw(ColorF{ 0.0, (x * 0.2), 1.0 });
		}
	}
}
```

### 4.5 色を変えながら長方形を並べる（HSV）

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 10; ++x)
		{
			Rect{ (x * 80), 0, 80, 600 }.draw(HSV{ (x * 36), 0.5, 1.0 });
		}
	}
}
```

