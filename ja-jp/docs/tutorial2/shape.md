# 26. 図形を描く
Siv3D に用意されているさまざまな 2D 図形の描画を学びます。

## 26.1 円
- 円は `Circle` クラスで表現します
- `Circle` は次のように作成できます
	- 半径が負の時の挙動は未規定です

| コード | 説明 |
|---|---|
| `Circle{ X 座標, Y 座標, 半径 }` | 中心座標と半径から円を作成します |
| `Circle{ 中心座標, 半径 }` | 中心座標と半径から円を作成します |
| `point.asCircle(半径)` | `Point` 型の値を中心座標として、半径を指定して円を作成します |
| `vec2.asCircle(半径)` | `Vec2` 型の値を中心座標として、半径を指定して円を作成します |

- 円をくには、`Circle` の `.draw()` を使います

| コード | 説明 |
|---|---|
| `.draw(色)` | 円を描きます |
| `.draw(内側の色, 外側の色)` | グラデーションする円を描きます |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Circle{ 150, 300, 40 }.draw(ColorF{ 0.8 });

		Circle{ Vec2{ 400, 300 }, 80 }.draw(ColorF{ 1.0 }, ColorF{ 1.0, 0.6, 0.4 });

		Cursor::Pos().asCircle(120).draw(ColorF{ 0.4 });
	}
}
```


## 26.2 円の枠
- 円の枠を描くには、`Circle` の `.drawFrame()` を使います
- 太さが正常な範囲にない場合（負など）の挙動は未規定です

| コード | 説明 |
|---|---|
| `.drawFrame(枠の太さ, 色)` | 円の枠を描きます |
| `.drawFrame(枠の太さ, 内側の色, 外側の色)` | グラデーションする円の枠を描きます |
| `.drawFrame(内側の太さ, 外側の太さ, 色)` | 円の枠を描きます |
| `.drawFrame(内側の太さ, 外側の太さ, 内側の色, 外側の色)` | グラデーションする円の枠を描きます |

- `.draw()` や `.drawFrame()` はその円自身の参照を返すため、`circle.draw().drawFrame()` のように連続して記述できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Circle{ 150, 300, 100 }.drawFrame(10, Palette::Seagreen);

		Circle{ 400, 300, 100 }.draw().drawFrame(2, 8, Palette::Seagreen);

		Circle{ 650, 300, 100 }.drawFrame(20, ColorF{ 0.0, 0.0 }, ColorF{ 0.0, 1.0 });
	}
}
```


## 26.3 扇形
- 扇形を描くには、`Circle` の `.drawPie()` を使います
- 開始角度は、12 時の方向を 0 とする、時計回りのラジアンで指定します
- 扇の角度は、時計回り方向の角度の大きさで指定します
	- 負の場合は反時計回り方向に扇が描かれます

| コード | 説明 |
|---|---|
| `.drawPie(開始角度, 扇の角度, 色)` | 扇型を描きます |
| `.drawPie(開始角度, 扇の角度, 内側の色, 外側の色)` | グラデーションする扇型を描きます |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Circle{ 150, 300, 120 }.drawPie(0_deg, 90_deg, ColorF{ 1.0 });

		Circle{ 400, 300, 120 }.drawPie(-30_deg, 60_deg, ColorF{ 0.25 });

		Circle{ 650, 300, 120 }.drawPie(120_deg, 120_deg, ColorF{ 0.1, 0.3, 0.1 }, ColorF{ 0.3, 1.0, 0.6 });
	}
}
```

## 26.4 円弧
- 円弧を描くには、`Circle` の `.drawArc()` を使います
- 開始角度は、12 時の方向を 0 とする、時計回りのラジアンで指定します
- 円弧の角度は、時計回り方向の角度の大きさで指定します
	- 負の場合は反時計回り方向に円弧が描かれます
- 太さが正常な範囲にない場合（負など）の挙動は未規定です

| コード | 説明 |
|---|---|
| `.drawArc(開始角度, 円弧の角度, 内側の太さ, 外側の太さ, 色)` | 円弧を描きます |
| `.drawArc(開始角度, 円弧の角度, 内側の太さ, 外側の太さ, 内側の色, 外側の色)` | グラデーションする円弧を描きます |
| `.drawArc(LineStyle::RoundCap, 開始角度, 円弧の角度, 内側の太さ, 外側の太さ, 色)` | 両端が丸い円弧を描きます |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Circle{ 150, 300, 120 }.drawArc(0_deg, 240_deg, 2, 2, ColorF{ 0.25 });

		Circle{ 400, 300, 120 }.drawArc(-30_deg, 60_deg, 20, 20, ColorF{ 0.0, 0.0 }, ColorF{ 0.0, 1.0 });

		Circle{ 650, 300, 120 }.drawArc(LineStyle::RoundCap, 120_deg, 120_deg, 30, 30, ColorF{ 0.1, 0.3, 0.1 });
	}
}
```


## 26.5 弓形
- 弓形を描くには、`Circle` の `.drawSegment()` または `.drawSegmentFromAngles()` を使います
- 角度はラジアンで指定します

| コード | 説明 |
|---|---|
| `.drawSegment(弧の中心の方向, 弧の角度, 色)` | 弓形を描きます |
| `.drawSegment(弧の開始角度, 弧の角度, 色)` | 弓形を描きます |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Circle{ 150, 200, 120 }.drawSegment(0_deg, 60, ColorF{ 0.9 });

		Circle{ 400, 200, 120 }.drawSegment(30_deg, 60, ColorF{ 0.9 });

		Circle{ 650, 200, 120 }.drawSegment(60_deg, 60, ColorF{ 0.9 });


		Circle{ 150, 400, 120 }.drawSegment(120_deg, 60, ColorF{ 0.25 });

		Circle{ 400, 400, 120 }.drawSegmentFromAngles(60_deg, 240_deg, ColorF{ 0.25 });

		Circle{ 650, 400, 120 }.drawSegmentFromAngles(90_deg, 120_deg, ColorF{ 0.25 });
	}
}
```

## 26.6 長方形
- 長方形は `Rect` または `RectF` で表現します
- `Rect` は座標やサイズを `int32` 型で、`RectF` は `double` 型で表現します
- サイズを表現する値のために、`Point` 型の別名である `Size` 型、`Vec2` 型の別名である `SizeF` 型を使うことができます
- 長方形は次のように作成できます
	- 幅と高さが負の時の挙動は未規定です

| コード | 説明 |
|---|---|
| `Rect{ 幅, 高さ }`<br>`RectF{ 幅, 高さ }` | 左上が (0, 0) の長方形を作成します |
| `Rect{ 幅と高さ }`<br>`RectF{ 幅と高さ }` | 左上が (0, 0) の長方形を作成します |
| `Rect{ 一辺の長さ }`<br>`RectF{ 一辺の長さ }` | 左上が (0, 0) の正方形を作成します |
| `Rect{ 左上の X 座標, 左上の Y 座標, 幅, 高さ }`<br>`RectF{ 左上の X 座標, 左上の Y 座標, 幅, 高さ }` | 長方形を作成します |
| `Rect{ 左上の X 座標, 左上の Y 座標, 幅と高さ }`<br>`RectF{ 左上の X 座標, 左上の Y 座標, 幅と高さ }` | 長方形を作成します |
| `Rect{ 左上の X 座標, 左上の Y 座標, 一辺の長さ }`<br>`RectF{ 左上の X 座標, 左上の Y 座標, 一辺の長さ }` | 正方形を作成します |
| `Rect{ 左上の座標, 幅, 高さ }`<br>`RectF{ 左上の座標, 幅, 高さ }`  | 長方形を作成します |
| `Rect{ 左上の座標, 幅と高さ }`<br>`RectF{ 左上の座標, 幅と高さ }`  | 長方形を作成します |
| `Rect{ 左上の座標, 一辺の長さ }`<br>`RectF{ 左上の座標, 一辺の長さ }` | 正方形を作成します |
| `Rect{ Arg::center(中心座標), 幅, 高さ }`<br>`RectF{ Arg::center(中心座標), 幅, 高さ }` | 中心座標を指定して長方形を作成します |
| `Rect{ Arg::center(中心座標), 幅と高さ }`<br>`RectF{ Arg::center(中心座標), 幅と高さ }` | 中心座標を指定して長方形を作成します |
| `Rect{ Arg::center(中心座標), 一辺の長さ }`<br>`RectF{ Arg::center(中心座標), 一辺の長さ }` | 中心座標を指定して正方形を作成します |
| `Rect::FromPoints(角の座標, その対角線上の角の座標)`<br>`RectF::FromPoints(角の座標, その対角線上の角の座標)` | 与えられた 2 点から長方形を作成します。<br>サイズが正の値である有効な長方形が作られます |

- 長方形を描くには、`Rect` または `RectF` の `.draw()` を使います

| コード | 説明 |
|---|---|
| `.draw(色)` | 長方形を描きます |
| `.draw(Arg::top = 上側の色, Arg::bottom = 下側の色)` | 上下にグラデーションする長方形を描きます |
| `.draw(Arg::left = 左側の色, Arg::right = 右側の色)` | 左右にグラデーションする長方形を描きます |
| `.draw(Arg::topLeft = 左上の色, Arg::bottomRight = 右下の色)` | 左上 → 右下にグラデーションする長方形を描きます |
| `.draw(Arg::topRight = 右上の色, Arg::bottomLeft = 左下の色)` | 右上 → 左下にグラデーションする長方形を描きます |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Rect{ 100, 100, 80 }.draw();

		RectF{ Vec2{ 200, 100 }, 80 }.draw(HSV{ 220, 0.3, 0.8 });

		Rect{ 300, 100, 80, 160 }.draw(ColorF{ 0.5 });

		Rect{ Point{ 400, 100 }, Size{ 80, 320 } }.draw(Arg::top(0.8, 0.9, 1.0), Arg::bottom = Palette::Seagreen);

		RectF{ 500, 100, SizeF{ 80.0, 320.5 } }.draw();

		Rect{ 600, 100, 80, 400 }.draw(Arg::topLeft(1.0), Arg::bottomRight(0.2));
	}
}
```

## 26.7 長方形の枠
- 長方形の枠を描くには、`Rect` または `RectF` の `.drawFrame()` を使います
- 太さが正常な範囲にない場合（負など）の挙動は未規定です

| コード | 説明 |
|---|---|
| `.drawFrame(太さ, 色)` | 長方形の枠を描きます |
| `.drawFrame(太さ, 内側の色, 外側の色)` | グラデーションする長方形の枠を描きます |
| `.drawFrame(内側の太さ, 外側の太さ, 色)` | 長方形の枠を描きます |
| `.drawFrame(内側の太さ, 外側の太さ, 内側の色, 外側の色)` | グラデーションする長方形の枠を描きます |
| `.drawFrame(太さ, Arg::top = 上側の色, Arg::bottom = 下側の色)` | 上下にグラデーションする長方形の枠を描きます |
| `.drawFrame(内側の太さ, 外側の太さ, Arg::top = 上側の色, Arg::bottom = 下側の色)` | 上下にグラデーションする長方形の枠を描きます |

- `.draw()` や `.drawFrame()` はその長方形自身の参照を返すため、`rect.draw().drawFrame()` のように連続して記述できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Rect{ 100, 100, 80 }.drawFrame(2);

		Rect{ 200, 100, 80 }
			.draw()
			.drawFrame(2, 8, ColorF{ 0.1 });

		Rect{ 300, 100, 80, 160 }.drawFrame(10, 0, ColorF{ 0.0, 0.0 }, ColorF{ 0.0, 1.0 });

		Rect{ 400, 100, 80, 160 }
			.draw(Arg::top(1.0, 0.8, 0.0), Arg::bottom = Palette::Red)
			.drawFrame(2, ColorF{ 0.1 });

		Rect{ 500, 100, 80, 320 }
			.drawFrame(3, 0)
			.drawFrame(0, 3, ColorF{ 0.1 });

		Rect{ 600, 100, 80, 320 }
			.drawFrame(3, 0, ColorF{ 0.1 })
			.drawFrame(0, 3, Arg::top(0.1), Arg::bottom(0.9));
	}
}
```


## 26.8 角丸長方形
- 角丸長方形は `RoundRect` で表現します
- `RoundRect` は次のように作成できます
	- サイズが負の値である場合、角の曲線の半径が不正な場合の挙動は未規定です

| コード | 説明 |
|---|---|
| `RoundRect{ 左上の X 座標, 左上の Y 座標, 幅, 高さ, 角の半径 }` | 角丸長方形を作成します |
| `RoundRect{ 左上の座標, 幅, 高さ, 角の半径 }` | 角丸長方形を作成します |
| `RoundRect{ 左上の座標, 幅と高さ, 角の半径 }` | 角丸長方形を作成します |
| `RoundRect{ Rect{ ... }, 角の半径 }` | 角丸長方形を作成します |
| `RoundRect{ RectF{ ... }, 角の半径 }` | 角丸長方形を作成します |
| `RoundRect{ Arg::center(中心座標), 幅, 高さ, 角の半径 }` | 中心座標を指定して角丸長方形を作成します |
| `RoundRect{ Arg::center(中心座標), 幅と高さ, 角の半径 }` | 中心座標を指定して角丸長方形を作成します |
| `rect.rounded(角の半径)` | 長方形（`Rect` または `RectF`）から角丸長方形を作成します |

- 角丸長方形を描くには、`RoundRect` の `.draw()` を使います

| コード | 説明 |
|---|---|
| `.draw(色)` | 角丸長方形を描きます |
| `.draw(Arg::top = 上側の色, Arg::bottom = 下側の色)` | 上下にグラデーションする角丸長方形を描きます |

- 角丸長方形の枠を描くには、`RoundRect` の `.drawFrame()` を使います

| コード | 説明 |
|---|---|
| `.drawFrame(太さ, 色)` | 角丸長方形の枠を描きます |
| `.drawFrame(内側の太さ, 外側の太さ, 色)` | 角丸長方形の枠を描きます |
| `.drawFrame(太さ, Arg::top = 上側の色, Arg::bottom = 下側の色)` | 上下にグラデーションする角丸長方形の枠を描きます |
| `.drawFrame(内側の太さ, 外側の太さ, Arg::top = 上側の色, Arg::bottom = 下側の色)` | 上下にグラデーションする角丸長方形の枠を描きます |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Rect rect{ 100, 300, 500, 200 };

	while (System::Update())
	{
		RoundRect{ 100, 100, 200, 100, 20 }.draw();

		RoundRect{ Arg::center(600, 150), 200, 80, 10 }.draw();

		rect.rounded(40).draw(ColorF{ 0.2 });
	}
}
```


## 26.9 一部の角が丸い長方形
- 一部の角が丸い長方形を表現する専用のクラスはなく、汎用的な多角形クラス `Polygon` を使って表現します
- 一部の角が丸い長方形の `Polygon` は次のように作成できます
	- 曲線半径が不正な場合の挙動は未規定です

| コード | 説明 |
|---|---|
| `rect.rounded(tl, tr, br, bl)` | 長方形（`Rect` または `RectF`）から一部の角が丸い長方形を作成します |

- `tl` は左上の角、`tr` は右上の角、`br` は右下の角、`bl` は左下の角の曲線の半径です
- 一部の角が丸い長方形を描くには、`Polygon` の `.draw()` を使います

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Rect{ 100, 100, 200, 100 }
			.rounded(40, 0, 0, 0).draw();

		Rect{ 400, 100, 200, 100 }
			.rounded(40, 40, 0, 0).draw();

		Rect{ 100, 300, 200, 200 }
			.rounded(40, 0, 40, 0).draw(ColorF{ 0.2 });

		Rect{ 400, 300, 200, 200 }
			.rounded(20, 40, 60, 80).draw(ColorF{ 0.2 });
	}
}
```


## 26.10 線分
- 線分は `Line` で表現します
- `Line` は次のように作成できます

| コード | 説明 |
|---|---|
| `Line{ 始点の X 座標, 始点の Y 座標, 終点の X 座標, 終点の Y 座標 }` | 線分を作成します |
| `Line{ 始点の座標, 終点の座標 }` | 線分を作成します |
| `Line{ 始点, 終点の X 座標, 終点の Y 座標 }` | 線分を作成します |
| `Line{ 始点の X 座標, 始点の Y 座標, 終点 }` | 線分を作成します |
| `Line{ 始点の X 座標, 始点の Y 座標, Arg::angle = 方向, 長さ }` | 線分を作成します |
| `Line{ 始点の座標, Arg::angle = 方向, 長さ }` | 線分を作成します |
| `Line{ 始点の X 座標, 始点の Y 座標, Arg::direction = 方向ベクトル }` | 線分を作成します |
| `Line{ 始点の座標, Arg::direction = 方向ベクトル }` | 線分を作成します |
| `rect.top()`, `rect.bottom()`, `rect.left()`, `rect.right()` | 長方形（`Rect` または `RectF`）の<br>上・下・左・右の辺を表す線分を作成します |

- 線分を描くには、`Line` の `.draw()` を使います

| コード | 説明 |
|---|---|
| `.draw(色)` | 線分を描きます |
| `.draw(始点の色, 終点の色)` | グラデーションする線分を描きます |
| `.draw(太さ, 色)` | 線分を描きます |
| `.draw(太さ, 始点の色, 終点の色)` | グラデーションする線分を描きます |
| `.draw(線のスタイル, 太さ, 色)` | 線分を描きます |
| `.draw(線のスタイル, 太さ, 始点の色, 終点の色)` | グラデーションする線分を描きます |

- 線のスタイルは次のいずれかを指定できます

| コード | 説明 |
|---|---|
| `LineStyle::SquareCap` | 両端が四角い線（デフォルト） |
| `LineStyle::Uncapped` | 両端が平らな線 |
| `LineStyle::RoundCap` | 両端が丸い線 |
| `LineStyle::SquareDot` | 四角いドットの線 |
| `LineStyle::RoundDot` | 丸いドットの線 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Line{ 100, 100, 400, 150 }.draw(4);

		Line{ 400, 300, Cursor::Pos() }.draw(10, Palette::Seagreen);

		Line{ 100, 400, 700, 400 }.draw(12, Palette::Orange);

		Line{ 100, 450, 700, 450 }.draw(LineStyle::RoundCap, 12, ColorF{ 0.2 });

		Line{ 100, 500, 700, 500 }.draw(LineStyle::SquareDot, 12,  ColorF{ 0.2 });

		Line{ 100, 550, 700, 550 }.draw(LineStyle::RoundDot, 12,  ColorF{ 0.2 });
	}
}
```

- 水平、垂直な線分を描きたい場合は、`Rect` や `RectF` を使うことも検討してください
	- 長方形として描画したほうが、品質や実行性能の面で有利です


## 26.11 矢印
- 矢印を表現する専用のクラスはなく、`Line` の `.drawArrow()` または `.drawDoubleHeadedArrow()` を使って描画します
- 単方向矢印は、`Line` の始点から終点方向に向かって描かれます

| コード | 説明 |
|---|---|
| `.drawArrow(線の幅, 三角形の幅と高さ, 色)` | 単方向の矢印を描きます |
| `.drawDoubleHeadedArrow(線の幅, 三角形の幅と高さ, 色)` | 両方向の矢印を描きます |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/11.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Line{ 50, 200, 200, 250 }
			.drawArrow(3, SizeF{ 20, 20 }, ColorF{ 0.2 });

		Line{ 350, 450, 450, 100 }
			.drawArrow(10, SizeF{ 40, 80 }, ColorF{ 0.2 });

		Line{ 600, 100, 700, 400 }
			.drawDoubleHeadedArrow(8, SizeF{ 30, 30 }, ColorF{ 0.2 });
	}
}
```


## 26.12 三角形
- 三角形は `Triangle` で表現します
- `Triangle` は次のように作成できます

| コード | 説明 |
|---|---|
| `Triangle{ 一辺の長さ }` | 正三角形の重心座標を (0, 0) として、一辺の長さを指定して正三角形を作成します |
| `Triangle{ 一辺の長さ, 回転角度 }` | 正三角形の重心座標を (0, 0) として、一辺の長さを指定して、回転した正三角形を作成します |
| `Triangle{ 重心の X 座標, 重心の Y 座標, 一辺の長さ }` | 重心座標と一辺の長さを指定して正三角形を作成します |
| `Triangle{ 重心の座標, 一辺の長さ }` | 重心座標と一辺の長さを指定して正三角形を作成します |
| `Triangle{ 重心の X 座標, 重心の Y 座標, 一辺の長さ, 回転角度 }` | 重心座標と一辺の長さを指定して、回転した正三角形を作成します |
| `Triangle{ 重心の座標, 一辺の長さ, 回転角度 }` | 重心座標と一辺の長さを指定して、回転した正三角形を作成します |
| `Triangle{ x0, y0, x1, y1, x2, y2 }` | 3 つの頂点座標を時計回りに指定して三角形を作成します |
| `Triangle{ pos0, pos1, pos2 }` | 3 つの頂点座標を時計回りに指定して三角形を作成します |
| `Triangle::FromPoints(pos0, pos1, pos2)` | 3 つの頂点座標を指定して三角形を作成します。<br>有効な三角形が作られるように頂点の順序が調整されます |

!!! warning "注意"
	- 反時計回りに頂点を指定した三角形は、描画はできますが、それ以外の機能（あたり判定など）が正しく動作しない場合があります

- 三角形を描くには、`Triangle` の `.draw()` を使います

| コード | 説明 |
|---|---|
| `.draw(色)` | 三角形を描きます |
| `.draw(色0, 色1, 色2)` | 3 つの頂点の色を指定して三角形を描きます |

- 三角形の枠を描くには、`Triangle` の `.drawFrame()` を使います

| コード | 説明 |
|---|---|
| `.drawFrame(太さ, 色)` | 三角形の枠を描きます |
| `.drawFrame(内側の太さ, 外側の太さ, 色)` | 三角形の枠を描きます |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/12.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		// 座標 (100, 100), (400, 300), (100, 300) で構成される三角形を描く
		Triangle{ 100, 100, 400, 300, 100, 300 }.draw();

		// 座標 (300, 100) を重心とする、1 辺が 80px の三角形を描く
		Triangle{ 300, 100, 80 }.draw(Palette::Orange);

		// 時計回りに 15° 回転させて描く
		Triangle{ 400, 100, 80, 15_deg }.draw(Palette::Seagreen);

		// 時計回りに 30° 回転させて描く
		Triangle{ 500, 100, 80, 30_deg }.draw(HSV{ 0 }, HSV{ 120 }, HSV{ 240 });

		// 3 つの頂点座標を Point や Vec2 型で指定する
		Triangle{ Cursor::Pos(), Vec2{ 700, 500 }, Vec2{ 100, 500 } }.draw(ColorF{ 0.2 });
	}
}
```



## 26.13 凸な四角形
- 4 つの頂点を持つ凸な四角形は `Quad` で表現します
	- 台形や平行四辺形は `Quad` で表現します
- `Rect` や `RectF` の辺は X 軸・Y 軸に平行ですが、`Quad` はその制約がありません
- `Quad` は次のように作成できます

| コード | 説明 |
|---|---|
| `Quad{ 頂点0, 頂点1, 頂点2, 頂点3 }` | 4 つの頂点座標を指定して四角形を作成します |
| `Quad{ x0, y0, x1, y1, x2, y2, x3, y3 }` | 4 つの頂点座標を指定して四角形を作成します |
| `Quad{ Rect{ ... } }` | 長方形から四角形を作成します |
| `Quad{ RectF{ ... } }` | 長方形から四角形を作成します |
| `rect.rotated(回転角度)` | 長方形（`Rect` または `RectF`）を回転させて四角形を作成します |
| `rect.rotatedAt(回転の中心座標, 回転角度)` | 長方形（`Rect` または `RectF`）を回転させて四角形を作成します |
| `rect.shearedX(スライド距離)` | 長方形（`Rect` または `RectF`）の上下の辺を X 軸方向にスライドさせた平行四辺形を作成します |
| `rect.shearedY(スライド距離)` | 長方形（`Rect` または `RectF`）の左右の辺を Y 軸方向にスライドさせた平行四辺形を作成します |
| `rect.skewedX(傾斜角度)` | 長方形（`Rect` または `RectF`）の左右の辺を傾斜させた平行四辺形を作成します |
| `rect.skewedY(傾斜角度)` | 長方形（`Rect` または `RectF`）の上下の辺を傾斜させた平行四辺形を作成します |

!!! warning "注意"
	- 4 つの頂点が反時計回りの場合や、凹角を持つ場合の挙動は未規定です

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/13-0.png)

- 四角形を描くには、`Quad` の `.draw()` を使います

| コード | 説明 |
|---|---|
| `.draw(色)` | 四角形を描きます |
| `.draw(色0, 色1, 色2, 色3)` | 4 つの頂点の色を指定して四角形を描きます |

- 四角形の枠を描くには、`Quad` の `.drawFrame()` を使います

| コード | 説明 |
|---|---|
| `.drawFrame(太さ, 色)` | 四角形の枠を描きます |
| `.drawFrame(内側の太さ, 外側の太さ, 色)` | 四角形の枠を描きます |

### 26.13.1 頂点を 4 つ指定
- 4 つの頂点座標を指定して `Quad` を作成します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/13-1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Quad{ Vec2{ 100, 100 }, Vec2{ 150, 100 }, Vec2{ 300, 300 }, Vec2{ 100, 300 } }.draw();

		Quad{ 300, 400, 500, 100, 600, 200, 500, 500 }.draw(ColorF{ 0.2 });
	}
}
```

### 26.13.2 長方形の回転
- 長方形を回転させて `Quad` を作成します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/13-2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Rect rect{ 150, 200, 400, 100 };

	double angle = 0.0_deg;

	while (System::Update())
	{
		angle += (Scene::DeltaTime() * 30_deg);

		rect.draw();

		// 長方形の中心が回転軸
		rect.rotated(angle).draw(Palette::Seagreen);

		// 長方形の左上が回転軸
		rect.rotatedAt(rect.pos, angle).draw(ColorF{ 0.2 });
	}
}
```

### 26.13.3 長方形のスライド・傾斜
- 長方形の辺をスライド・傾斜させて `Quad` を作成します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/13-3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		// 長方形の辺を X 軸方向に 30px ずつスライドさせた平行四辺形を描く
		Rect{ 100, 100, 200, 150 }.drawFrame(4, 0)
			.shearedX(30).draw(ColorF{ 0.2 });

		// 長方形の辺を Y 軸方向に -50px ずつスライドさせた平行四辺形を描く
		Rect{ 500, 100, 200, 150 }.drawFrame(4, 0)
			.shearedY(-50).draw(ColorF{ 0.2 });

		// 長方形の左右の辺を 30° 傾けた平行四辺形を描く
		Rect{ 100, 350, 200, 150 }.drawFrame(4, 0)
			.skewedX(30_deg).draw(Palette::Seagreen);

		// 長方形の上下の辺を -10° 傾けた平行四辺形を描く
		Rect{ 500, 350, 200, 150 }.drawFrame(4, 0)
			.skewedY(-10_deg).draw(Palette::Seagreen);
	}
}
```


## 26.14 楕円
- 楕円は `Ellipse` で表現します
	- 傾いた楕円は表現できません。
		- 傾いた楕円を描画したい場合は、**チュートリアル XX** の `Transformer2D` を使って描画するか、`Polygon` で近似することを検討してください
- `Ellipse` は次のように作成できます

| コード | 説明 |
|---|---|
| `Ellipse{ 中心の X 座標, 中心の Y 座標, X 軸方向の半径, Y 軸方向の半径 }` | 楕円を作成します |
| `Ellipse{ 中心の座標, X 軸方向の半径, Y 軸方向の半径 }` | 楕円を作成します |
| `Ellipse{ 中心の X 座標, 中心の Y 座標, X・Y 軸方向の半径 }` | 楕円を作成します |
| `Ellipse{ 中心の座標, X・Y 軸方向の半径 }` | 楕円を作成します |
| `Ellipse{ Circle{ ... } }` | 楕円を作成します |
| `Ellipse{ Rect{ ... } }` | 長方形に内接する楕円を作成します |
| `Ellipse{ RectF{ ... } }` | 長方形に内接する楕円を作成します |

- 楕円を描くには、`Ellipse` の `.draw()` を使います

| コード | 説明 |
|---|---|
| `.draw(色)` | 楕円を描きます |
| `.draw(内側の色, 外側の色)` | グラデーションする楕円を描きます |

- 楕円の枠を描くには、`Ellipse` の `.drawFrame()` を使います

| コード | 説明 |
|---|---|
| `.drawFrame(太さ, 色)` | 楕円の枠を描きます |
| `.drawFrame(内側の太さ, 外側の太さ, 色)` | 楕円の枠を描きます |


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/14.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Ellipse{ 300, 200, 200, 100 }.draw();

		Ellipse{ 600, 400, 50, 150 }.draw(ColorF{ 0.2 });
	}
}
```


## 26.15 よく使われる形状
- よく使われる形状を 1 行で描画できる関数が用意されています
- これらの関数は頂点配列を格納した `Shape2D` 型のオブジェクトを返します

| コード | 形状 |
|---|---|
| `Shape2D::Cross(半径, 太さ, 中心座標 = { 0, 0 }, 回転角度 = 0.0)` | ✖ マーク |
| `Shape2D::Plus(半径, 太さ, 中心座標 = { 0, 0 }, 回転角度 = 0.0)` | ＋マーク |
| `Shape2D::Pentagon(半径, 中心座標 = { 0, 0 }, 回転角度 = 0.0)` | 正五角形 |
| `Shape2D::Hexagon(半径, 中心座標 = { 0, 0 }, 回転角度 = 0.0)` | 正六角形 |
| `Shape2D::Ngon(辺の数, 半径, 中心座標 = { 0, 0 }, 回転角度 = 0.0)` | 正 N 角形 |
| `Shape2D::Star(半径, 中心座標 = { 0, 0 }, 回転角度 = 0.0)` | 五芒星 |
| `Shape2D::NStar(尖端の数, 外周の半径, 内周の半径, 中心座標 = { 0, 0 }, 回転角度 = 0.0)` | 星 |
| `Shape2D::Arrow(始点, 終点, 太さ, 三角形の幅と高さ)` | 矢印 |
| `Shape2D::Arrow(線分, 太さ, 三角形の幅と高さ)` | 矢印 |
| `Shape2D::DoubleHeadedArrow(始点, 終点, 太さ, 三角形の幅と高さ)` | 両方向矢印 |
| `Shape2D::DoubleHeadedArrow(線分, 太さ, 三角形の幅と高さ)` | 両方向矢印 |
| `Shape2D::Rhombus(幅, 高さ, 中心座標 = { 0, 0 }, 回転角度 = 0.0)` | ひし形 |
| `Shape2D::RectBalloon(長方形, ターゲット座標, 吹き出しの根元の比率 = 0.5)` | 長方形の吹き出し |
| `Shape2D::Stairs(基準座標, 幅, 高さ, 階段数, 右上に上がるか = true)` | 階段形 |
| `Shape2D::Heart(半径, 中心座標 = { 0, 0 }, 回転角度 = 0.0)` | ハート形 |
| `Shape2D::Squircle(半径, 中心座標, 品質)` | 正方形と円の中間 |
| `Shape2D::Astroid(中心座標 外接楕円の X 軸半径, 外接楕円の Y 軸半径, 回転角度 = 0.0)` | 星芒形 |

- これらの形を描画するには、`Shape2D` の `.draw()` を使います

| コード | 説明 |
|---|---|
| `.draw(色)` | 形を描きます |

- これらの形の枠を描画するには、`Shape2D` の `.drawFrame()` を使います

| コード | 説明 |
|---|---|
| `.drawFrame(太さ, 色)` | 形の枠を描きます |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/15.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウサイズを 1280x720 に変更する
	Window::Resize(1280, 720);

	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Shape2D::Cross(80, 10, Vec2{ 100, 100 }).draw();

		Shape2D::Plus(80, 10, Vec2{ 300, 100 }).draw();

		Shape2D::Pentagon(80, Vec2{ 500, 100 }).draw();

		Shape2D::Hexagon(80, Vec2{ 700, 100 }).draw();

		Shape2D::Hexagon(80, Vec2{ 900, 100 }, 30_deg).draw();

		Shape2D::Hexagon(80, Vec2{ 1100, 100 }, 30_deg).drawFrame(4, ColorF{ 0.2 });


		Shape2D::Ngon(10, 80, Vec2{ 100, 300 }).draw();

		Shape2D::Star(80, Vec2{ 300, 300 }).draw();

		Shape2D::NStar(10, 80, 60, Vec2{ 500, 300 }).draw();

		Shape2D::Arrow(Line{ 640, 340, 760, 260 }, 20, Vec2{ 40, 30 }).draw();

		Shape2D::DoubleHeadedArrow(Line{ 840, 340, 960, 260 }, 20, Vec2{ 40, 30 }).draw();

		Shape2D::Rhombus(160, 120, Vec2{ 1100, 300 }).draw();


		Shape2D::RectBalloon(RectF{ 20, 420, 160, 120 }, Vec2{ 20, 580 }, 0.5).draw();

		Shape2D::Stairs(Vec2{ 360, 560 }, 120, 120, 4).draw();

		Shape2D::Heart(80, Vec2{ 500, 500 }).draw();

		Shape2D::Squircle(60, Vec2{ 700, 500 }, 64).draw();

		Shape2D::Astroid(Vec2{ 900, 500 }, 60, 80).draw();
	}
}
```


## 26.16 自由な多角形
- 任意の多角形は `Polygon` で表現します
- `Polygon` は次のように作成できます
	- 頂点は 3 つ以上で、外周は時計回りに、穴は反時計回りに指定します

| コード | 説明 |
|---|---|
| `Polygon{ 頂点0, 頂点1, ... }` | 多角形を作成します |
| `Polygon{ Array<Vec2>{ ... } }` | 多角形を作成します |
| `Polygon{ Array<Vec2>{ ... }, Array<Array<Vec2>>{ ... } }` | 穴の開いた多角形を作成します |
| `Polygon{ Shape2D }` | `Shape2D` から多角形を作成します |
| `circle.asPolygon(品質)` | 円を多角形に変換します |
| `ellipse.asPolygon(品質)` | 楕円を多角形に変換します |
| `rect.asPolygon()` | 長方形（`Rect` または `RectF`）を多角形に変換します |
| `roundRect.asPolygon()` | 角丸長方形（`RoundRect`）を多角形に変換します |
| `triangle.asPolygon()` | 三角形（`Triangle`）を多角形に変換します |
| `quad.asPolygon()` | 四角形（`Quad`）を多角形に変換します |
| `shape2D.asPolygon()` | `Shape2D` を多角形に変換します |

- `Polygon` オブジェクトの作成には、メモリの確保や三角形分割の計算に実行時コストがかかります
	- とくに頂点数が多いものは、ループの内側で作成するのを避けるべきです。
- 多角形を描くには、`Polygon` の `.draw()` を使います

| コード | 説明 |
|---|---|
| `.draw(色)` | 多角形を描きます |
| `.draw(X 軸方向の移動量, Y 軸方向の移動量, 色)` | 多角形を描きます |
| `.draw(移動量, 色)` | 多角形を描きます |

- 多角形の枠を描くには、`Polygon` の `.drawFrame()` を使います

| コード | 説明 |
|---|---|
| `.drawFrame(太さ, 色)` | 多角形の枠を描きます |
| `.drawFrame(X 軸方向の移動量, Y 軸方向の移動量, 太さ, 色)` | 多角形の枠を描きます |
| `.drawFrame(移動量, 太さ, 色)` | 多角形の枠を描きます |


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/16.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 多角形
	const Polygon polygon1
	{
		Vec2{ 200, 100 }, Vec2{ 380, 300 }, Vec2{ 300, 500 }, Vec2{ 200, 400 }, Vec2{ 100, 500 }, Vec2{ 20, 300 }
	};

	// 穴のある多角形
	const Polygon polygon2
	{
		// 外周
		{ Vec2{ 600, 100 }, Vec2{ 780, 300 }, Vec2{ 700, 500 }, Vec2{ 600, 400 }, Vec2{ 500, 500 }, Vec2{ 420, 300 } },

		// 穴
		{ { Vec2{ 620, 250 }, Vec2{ 580, 250 }, Vec2{ 550, 350 }, Vec2{ 650, 350 } } }
	};

	while (System::Update())
	{
		polygon1.draw();
		polygon2.draw(ColorF{ 0.2 });
	}
}
```

- `Polygon` よりも少ない実行時コストで図形を描きたい場合は、`Shape2D` や `Buffer2D` など低レイヤのクラスを使います
- `Shape2D` では、頂点配列に加えてインデックス配列を自前で用意する必要があります
- `Buffer2D` では、さらにテクスチャをマッピングするための UV 座標も用意します
- いずれも本章では扱いません


## 26.17 連続する線分
- 連続した線分は `LineString` で表現します
- `LineString` は次のように作成できます

| コード | 説明 |
|---|---|
| `LineString{ 頂点0, 頂点1, ... }` | 連続した線分を作成します |
| `LineString{ Array<Point>{ ... } }` | 連続した線分を作成します |
| `LineString{ Array<Vec2>{ ... } }` | 連続した線分を作成します |

- 連続した線分を描くには、`LineString` の `.draw()` または `.drawClosed()` を使います
	- `.draw()` は終点と始点を結びません
	- `.drawClosed()` は終点と始点を結びます

| コード | 説明 |
|---|---|
| `.draw(太さ, 色)` | 連続した線分を描きます |
| `.draw(LineStyle::RoundCap, 太さ, 色)` | 両端が丸い、連続した線分を描きます |
| `.drawClosed(太さ, 色)` | 連続した線分を描きます（終点と始点を結びます） |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/17-1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const LineString ls1
	{
		Vec2{ 100, 60 }, Vec2{ 400, 140 },
		Vec2{ 100, 220 }, Vec2{ 400, 300 },
		Vec2{ 100, 380 }, Vec2{ 400, 460 },
		Vec2{ 100, 540 }
	};

	const LineString ls2
	{
		Vec2{ 500, 100 }, Vec2{ 700, 200 },
		Vec2{ 600, 500 },
	};

	while (System::Update())
	{
		ls1.draw(6);

		ls2.drawClosed(8, ColorF{ 0.2 });
	}
}
```

- `LineString` は実質的には `Array<Vec2>` です
- `Array` のような操作が可能です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/17-2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	LineString points;

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			// 点を追加する
			points << Cursor::Pos();
		}

		// 連続する線分を描く
		points.draw(8, ColorF{ 0.2 });

		// 各点について
		for (const auto& point : points)
		{
			// 円を描く
			point.asCircle(10).draw();
		}
	}
}
```


## 26.18 スプライン曲線
- Catmull-Rom スプライン曲線は `Spline2D` で表現します
- Catmull-Rom スプライン曲線は、指定した通過点を必ず通ります
- `Spline2D` は次のように作成できます
	- `CloseRing::Yes` を指定すると、終点と始点をつなげた輪になります

| コード | 説明 |
|---|---|
| `Spline2D{ Array<Vec2>{ ... } }` | Catmull-Rom スプライン曲線を作成します |
| `Spline2D{ LineString{ ... } }` | Catmull-Rom スプライン曲線を作成します |
| `Spline2D{ Array<Vec2>{ ... }, CloseRing::Yes }` | Catmull-Rom スプライン曲線（輪）を作成します |

- スプライン曲線を描くには、`Spline2D` の `.draw()` を使います

| コード | 説明 |
|---|---|
| `.draw(色, 品質 = 24)` | スプライン曲線を描きます |
| `.draw(太さ, 色, 品質 = 24)` | スプライン曲線を描きます |
| `.draw(LineStyle::RoundCap, 太さ, 色, 品質 = 24)` | 両端が丸い、スプライン曲線を描きます |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/18.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const LineString ls
	{
		Vec2{ 100, 60 }, Vec2{ 400, 140 },
		Vec2{ 100, 220 }, Vec2{ 400, 300 },
		Vec2{ 100, 380 }, Vec2{ 400, 460 },
		Vec2{ 100, 540 }
	};

	const Spline2D spline1{ ls };

	const Spline2D spline2
	{
		{ Vec2{ 500, 100 }, Vec2{ 700, 200 }, Vec2{ 600, 500 } },
		CloseRing::Yes
	};

	while (System::Update())
	{
		spline1.draw(6);

		spline2.draw(8, ColorF{ 0.2 });
	}
}
```


## 26.19 ベジェ曲線
- 2 次ベジェ曲線は `Bezier2` で、3 次ベジェ曲線は `Bezier3` で表現します
- ベジェ曲線は次のように作成できます

| コード | 説明 |
|---|---|
| `Bezier2{ 始点, 制御点, 終点 }` | 2 次ベジェ曲線を作成します |
| `Bezier3{ 始点, 制御点1, 制御点2, 終点 }` | 3 次ベジェ曲線を作成します |

- ベジェ曲線を描くには、`Bezier2` または `Bezier3` の `.draw()` を使います

| コード | 説明 |
|---|---|
| `.draw(色, 品質 = 24)` | ベジェ曲線を描きます |
| `.draw(太さ, 色, 品質 = 24)` | ベジェ曲線を描きます |
| `.draw(LineStyle::RoundCap, 太さ, 色, 品質 = 24)` | 両端が丸い、ベジェ曲線を描きます |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/19.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		// 2 次ベジェ曲線
		Bezier2{ Vec2{ 100, 400 }, Vec2{ 100, 250 }, Vec2{ 300, 100 } }.draw(6);

		// 3 次ベジェ曲線
		Bezier3{ Vec2{ 300, 400 }, Vec2{ 400, 400 }, Vec2{ 400, 100 }, Vec2{ 500, 100 }}.draw(8, ColorF{ 0.2 });
	}
}
```


## 26.20 図形の影
- `Circle`, `Rect`, `RectF`, `RoundRect` は、影を描画する `.drawShadow()` メンバ関数を持っています
	- 第 1 引数で影の位置のオフセット、第 2 引数でぼかしの大きさ、第 3 引数で影の大きさのオフセット、第 4 引数で影の色を指定できます
	- 影は図形で隠れて見えない部分も塗りつぶすため、影を描いたあとに図形を上から重ね書きする必要があります

| コード | 説明 |
|---|---|
| `.drawShadow(影の位置のオフセット, ぼかしの大きさ, 影の大きさのオフセット, 影の色)` | 図形（`Circle`, `Rect`, `RectF`, `RoundRect`）に影を描きます |

- `.drawShadow()` の戻り値が図形自身の参照であるため、つづけて `.draw()` を呼び出すことができます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/20.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Rect{ 100, 50, 150, 200 }
			.drawShadow(Vec2{ 2, 2 }, 8, 1)
			.draw();

		Rect{ 300, 50, 150, 200 }
			.drawShadow(Vec2{ 4, 4 }, 16, 1.5)
			.draw();

		Rect{ 500, 50, 150, 200 }
			.drawShadow(Vec2{ 6, 6 }, 24, 2)
			.draw();

		Circle{ 100, 400, 50 }
			.drawShadow(Vec2{ 0, 4 }, 10, 3)
			.draw(ColorF{ 0.6, 0.8, 0.7 });

		Circle{ 300, 400, 50 }
			.drawShadow(Vec2{ 0, -4 }, 10, 3)
			.draw(ColorF{ 0.5, 0.7, 0.6 });

		RoundRect{ 450, 350, 100, 100, 20 }
			.drawShadow(Vec2{ 2, 2 }, 8, 0)
			.draw();

		RoundRect{ 650, 350, 100, 100, 20 }
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw();
	}
}
```
