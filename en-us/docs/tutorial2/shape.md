# 17. 図形を描く
Siv3D に用意されているさまざまな 2D 図形の描画機能を学びます。

## 17.1 円を描く
`Circle` は次のように作成できます。半径が負の時の挙動は未規定です。

| コード | 説明 |
|---|---|
| `Circle{ X 座標, Y 座標, 半径 }` | 中心座標と半径から円を作成します。 |
| `Circle{ 中心座標, 半径 }` | 中心座標と半径から円を作成します。 |
| `point.asCircle(半径)` | `Point` 型の値を中心座標として、半径を指定して円を作成します。 |
| `vec2.asCircle(半径)` | `Vec2` 型の値を中心座標として、半径を指定して円を作成します。 |

作成した円は `.draw()` で描画できます。

| コード | 説明 |
|---|---|
| `.draw(色)` | 円を描きます。 |
| `.draw(内側の色, 外側の色)` | グラデーションする円を描きます。 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		// 中心が (200, 300)、半径が 80 の円を描く
		Circle{ 200, 300, 80 }.draw(ColorF{ 0.8 });

		// 中心が白、外側がオレンジの円を描く
		Circle{ Vec2{ 400, 300 }, 60 }.draw(ColorF{ 1.0 }, ColorF{ 1.0, 0.6, 0.4 });

		// マウスカーソルの位置を中心とする半径 40 の円を描く
		Cursor::Pos().asCircle(40).draw(ColorF{ 0.4 });
	}
}
```


## 17.2 円の枠を描く
`Circle` の `.drawFrame()` を使うと、円の枠を描くことができます。太さが正常な範囲にない場合の挙動は未規定です。

`.draw()` や `.drawFrame()` の戻り値はその円自身であるため、`circle.draw().drawFrame()` のように関数を続けて書くこともできます。

| コード | 説明 |
|---|---|
| `.drawFrame(枠の太さ, 色)` | 円の枠を描きます。 |
| `.drawFrame(枠の太さ, 内側の色, 外側の色)` | グラデーションする円の枠を描きます。 |
| `.drawFrame(内側の太さ, 外側の太さ, 色)` |円の枠を描きます。 |
| `.drawFrame(内側の太さ, 外側の太さ, 内側の色, 外側の色)` | グラデーションする円の枠を描きます。 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Circle{ 200, 300, 80 }.drawFrame(10, Palette::Seagreen);

		Circle{ 400, 300, 80 }.drawFrame(10, 10, Palette::Seagreen);

		Circle{ 600, 300, 80 }.drawFrame(40, ColorF{ 0.2 }, ColorF{ 1.0 });
	}
}
```

## 17.3 扇型を描く
`Circle` の `.drawPie()` を使うと、扇型を描くことができます。角度はラジアンで指定します。

| コード | 説明 |
|---|---|
| `.drawPie(開始角度, 扇の角度, 色)` | 扇型を描きます。 |
| `.drawPie(開始角度, 扇の角度, 内側の色, 外側の色)` | グラデーションする扇型を描きます。 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Circle{ 200, 300, 100 }.drawPie(0_deg, 90_deg, ColorF{ 1.0 });

		Circle{ 400, 300, 200 }.drawPie(-30_deg, 60_deg, ColorF{ 0.25 });

		Circle{ 600, 300, 100 }.drawPie(120_deg, 120_deg, ColorF{ 0.1, 0.3, 0.1 }, ColorF{ 0.3, 1.0, 0.6 });
	}
}
```

## 17.4 円弧を描く
`Circle` の `.drawArc()` を使うと、円弧を描くことができます。角度はラジアンで指定します。太さが正常な範囲にない場合の挙動は未規定です。

| コード | 説明 |
|---|---|
| `.drawArc(開始角度, 円弧の角度, 内側の太さ, 外側の太さ, 色)` | 円弧を描きます。 |
| `.drawArc(開始角度, 円弧の角度, 内側の太さ, 外側の太さ, 内側の色, 外側の色)` | グラデーションする円弧を描きます。 |
| `.drawArc(LineStyle::RoundCap, 開始角度, 円弧の角度, 内側の太さ, 外側の太さ, 色)` | 両端が丸い円弧を描きます。 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Circle{ 200, 300, 100 }.drawArc(0_deg, 240_deg, 2, 2, ColorF{ 0.25 });

		Circle{ 400, 300, 200 }.drawArc(-30_deg, 60_deg, 20, 20, ColorF{ 0.25 }, ColorF{ 1.0, 1.0, 0.0 });

		Circle{ 600, 300, 100 }.drawArc(LineStyle::RoundCap, 120_deg, 120_deg, 30, 30, ColorF{ 0.1, 0.3, 0.1 });
	}
}
```

## 17.5 弓形を描く
`Circle` の `.drawSegment()` または `.drawSegmentFromAngles()` を使うと、弓形を描くことができます。角度はラジアンで指定します。

| コード | 説明 |
|---|---|
| `.drawSegment(弧の中心の方向, 弧の角度, 色)` | 弓形を描きます。 |
| `.drawSegment(弧の開始角度, 弧の角度, 色)` | 弓形を描きます。 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Circle{ 150, 200, 100 }
			.drawSegment(0_deg, 60, ColorF{ 0.9 });

		Circle{ 400, 200, 100 }
			.drawSegment(30_deg, 60, ColorF{ 0.9 });

		Circle{ 650, 200, 100 }
			.drawSegment(60_deg, 60, ColorF{ 0.9 });


		Circle{ 150, 400, 100 }
			.drawSegment(120_deg, 60, ColorF{ 0.25 });

		Circle{ 400, 400, 100 }
			.drawSegmentFromAngles(60_deg, 240_deg, ColorF{ 0.25 });

		Circle{ 650, 400, 100 }
			.drawSegmentFromAngles(90_deg, 120_deg, ColorF{ 0.25 });
	}
}
```

## 17.6 長方形を描く
`Rect` および `RectF` は次のように作成できます。幅と高さが負の時の挙動は未規定です。「幅と高さ」で使える `Size`, `SizeF` は、`Point`, `Vec2` の別名です。

| コード | 説明 |
|---|---|
| `Rect{ 左上の X 座標, 左上の Y 座標, 幅, 高さ }`<br>`RectF{ 左上の X 座標, 左上の Y 座標, 幅, 高さ }` | 長方形を作成します。 |
| `Rect{ 左上の X 座標, 左上の Y 座標, 幅と高さ }`<br>`RectF{ 左上の X 座標, 左上の Y 座標, 幅と高さ }` | 長方形を作成します。 |
| `Rect{ 左上の X 座標, 左上の Y 座標, 一辺の長さ }`<br>`RectF{ 左上の X 座標, 左上の Y 座標, 一辺の長さ }` | 正方形を作成します。 |
| `Rect{ 左上の座標, 幅, 高さ }`<br>`RectF{ 左上の座標, 幅, 高さ }`  | 長方形を作成します。 |
| `Rect{ 左上の座標, 幅と高さ }`<br>`RectF{ 左上の座標, 幅と高さ }`  | 長方形を作成します。 |
| `Rect{ 左上の座標, 一辺の長さ }`<br>`RectF{ 左上の座標, 一辺の長さ }` | 正方形を作成します。 |
| `Rect{ Arg::center(中心座標), 幅, 高さ }`<br>`RectF{ Arg::center(中心座標), 幅, 高さ }` | 長方形を作成します。 |
| `Rect{ Arg::center(中心座標), 幅と高さ }`<br>`RectF{ Arg::center(中心座標), 幅と高さ }` | 長方形を作成します。 |
| `Rect{ Arg::center(中心座標), 一辺の長さ }`<br>`RectF{ Arg::center(中心座標), 一辺の長さ }` | 正方形を作成します。 |
| `Rect::FromPoints(角の座標, その対角線上の角の座標)`<br>`RectF::FromPoints(角の座標, その対角線上の角の座標)` | 長方形を作成します。 |

作成した長方形は `.draw()` で描画できます。

| コード | 説明 |
|---|---|
| `.draw(色)` | 長方形を描きます。 |
| `.draw(Arg::top = 上側の色, Arg::bottom = 下側の色)` | グラデーションする長方形を描きます。 |
| `.draw(Arg::left = 左側の色, Arg::right = 右側の色)` | グラデーションする長方形を描きます。 |
| `.draw(Arg::topLeft = 左上の色, Arg::bottomRight = 右下の色)` | グラデーションする長方形を描きます。 |
| `.draw(Arg::topRight = 右上の色, Arg::bottomLeft = 左下の色)` | グラデーションする長方形を描きます。 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Rect{ 100, 20, 400, 80 }.draw();

		RectF{ Vec2{ 100, 120 }, 80 }.draw(ColorF{ 0.5 });

		Rect{ Point{ 100, 220 }, Size{ 300, 80 } }.draw(Arg::top = Palette::Yellow, Arg::bottom = Palette::Red);

		RectF{ 100, 320, SizeF{ 80.5, 80.0 } }.draw();

		Rect{ 100, 420, 400, 80 }.draw(Arg::topLeft(1.0), Arg::bottomRight(0.5));
	}
}
```

## 17.7 長方形の枠を描く
`Rect`, `RectF` の `.drawFrame()` を使うと、長方形の枠を描くことができます。太さが正常な範囲にない場合の挙動は未規定です。

`.draw()` や `.drawFrame()` の戻り値はその長方形自身であるため、`rect.draw().drawFrame()` のように関数を続けて書くこともできます。

| コード | 説明 |
|---|---|
| `.drawFrame(太さ, 色)` | 長方形の枠を描きます。 |
| `.drawFrame(太さ, 内側の色, 外側の色)` | グラデーションする長方形の枠を描きます。 |
| `.drawFrame(内側の太さ, 外側の太さ, 色)` | 長方形の枠を描きます。 |
| `.drawFrame(内側の太さ, 外側の太さ, 内側の色, 外側の色)` | グラデーションする長方形の枠を描きます。 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		Rect{ 100, 20, 400, 80 }.drawFrame(2);

		RectF{ Vec2{ 100, 120 }, 80 }.drawFrame(5, 5, ColorF{ 0.5 });

		Rect{ Point{ 100, 220 }, Size{ 300, 80 } }.drawFrame(10, Palette::Yellow, Palette::Green);

		RectF{ 100, 320, SizeF{ 80.5, 80.0 } }.drawFrame(10, 10);
	}
}
```


## 17.8 線分を描く
`Line` は次のように作成できます。

| コード | 説明 |
|---|---|
| `Line{ 始点の X 座標, 始点の Y 座標, 終点の X 座標, 終点の Y 座標 }` | 線分を作成します。 |
| `Line{ 始点の座標, 終点の座標 }` | 線分を作成します。 |
| `Line{ 始点, 終点の X 座標, 終点の Y 座標 }` | 線分を作成します。 |
| `Line{ 始点の X 座標, 始点の Y 座標, 終点 }` | 線分を作成します。 |
| `Line{ 始点の X 座標, 始点の Y 座標, Arg::angle = 方向, 長さ }` | 線分を作成します。 |
| `Line{ 始点の座標, Arg::angle = 方向, 長さ }` | 線分を作成します。 |
| `Line{ 始点の X 座標, 始点の Y 座標, Arg::direction = 方向ベクトル }` | 線分を作成します。 |
| `Line{ 始点の座標, Arg::direction = 方向ベクトル }` | 線分を作成します。 |

作成した `Line` は、`.draw()` で描画できます。

| コード | 説明 |
|---|---|
| `.draw(色)` | 線分を描きます。 |
| `.draw(始点の色, 終点の色)` | グラデーションする線分を描きます。 |
| `.draw(太さ, 色)` | 線分を描きます。 |
| `.draw(太さ, 始点の色, 終点の色)` | グラデーションする線分を描きます。 |
| `.draw(線のスタイル, 太さ, 色)` | 線分を描きます。 |
| `.draw(線のスタイル, 太さ, 始点の色, 終点の色)` | グラデーションする線分を描きます。 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 座標 (100, 100) から (400, 150) まで太さ 4px の線分を描く
		Line{ 100, 100, 400, 150 }.draw(4, Palette::Yellow);

		// 座標 (400, 300) からマウスカーソルの座標まで太さ 10px の線分を描く
		Line{ 400, 300, Cursor::Pos() }.draw(10, Palette::Skyblue);

		// 通常の線
		Line{ 100, 400, 700, 400 }.draw(12, Palette::Orange);

		// 両端が丸い線
		Line{ 100, 450, 700, 450 }.draw(LineStyle::RoundCap, 12, Palette::Orange);

		// 四角いドットの線
		Line{ 100, 500, 700, 500 }.draw(LineStyle::SquareDot, 12, Palette::Orange);

		// 丸いドットの線
		Line{ 100, 550, 700, 550 }.draw(LineStyle::RoundDot, 12, Palette::Orange);
	}
}
```


## 17.9 三角形を描く
三角形を描くには、`Triangle` を作成して `.draw()` します。`Triangle` を作成するには次のような方法があります。

| コード | 説明 |
|---|---|
| `Triangle{ 一辺の長さ }` | 正三角形の重心座標を (0, 0) として、一辺の長さを指定して正三角形を作成します。 |
| `Triangle{ 一辺の長さ, 回転角度 }` | 正三角形の重心座標を (0, 0) として、一辺の長さを指定して、回転した正三角形を作成します。 |
| `Triangle{ 重心の X 座標, 重心の Y 座標, 一辺の長さ }` | 重心座標と一辺の長さを指定して正三角形を作成します。 |
| `Triangle{ 重心の座標, 一辺の長さ }` | 重心座標と一辺の長さを指定して正三角形を作成します。 |
| `Triangle{ 重心の X 座標, 重心の Y 座標, 一辺の長さ, 回転角度 }` | 重心座標と一辺の長さを指定して、回転した正三角形を作成します。 |
| `Triangle{ 重心の座標, 一辺の長さ, 回転角度 }` | 重心座標と一辺の長さを指定して、回転した正三角形を作成します。 |
| `Triangle{ x0, y0, x1, y1, x2, y2 }` | 3 つの頂点座標を時計回りに指定して三角形を作成します。 |
| `Triangle{ pos0, pos1, pos2 }` | 3 つの頂点座標を時計回りに指定して三角形を作成します。 |

作成した `Triangle` は、`.draw()` で描画できます。

| コード | 説明 |
|---|---|
| `.draw(色)` | 三角形を描きます。 |
| `.draw(色, 色, 色)` | 3 つの頂点の色を指定して三角形を描きます。 |

反時計回りに頂点を指定した三角形は、描画はできますが、それ以外の機能（あたり判定など）は正しく動作しない場合があります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
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
		Triangle{ Cursor::Pos(), Vec2{ 700, 500 }, Vec2{ 100, 500 } }.draw(Palette::Skyblue);
	}
}
```


## 17.10 凸な四角形を描く
`Rect` や `RectF` では、各辺が X 軸、Y 軸に平行な長方形しか定義できませんでしたが、`Quad` を使うと 4 つの頂点座標を時計回りに指定して四角形を定義できます。ただし、`Quad` で定義される四角形は 180° 以上の内角を含まない形状（すべての角が凸）である必要があります。凹角を含む四角形を定義したい場合はのちにで出てくる `Polygon` 型を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/10a.png)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/10b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 4 つの頂点座標を指定して四角形を描く
		Quad{ Vec2{ 100, 100 }, Vec2{ 150, 100 }, Vec2{ 300, 300 }, Vec2{ 100, 300 } }.draw();

		Quad{ Vec2{ 300, 400 }, Vec2{ 500, 100 }, Vec2{ 600, 200 }, Vec2{ 500, 500 } }.draw(Palette::Skyblue);
	}
}
```

`Rect` や `RectF` を作成し、`.rotated()` または `.rotatedAt()` を使うと、長方形を回転させて `Quad` を作成できます。その `Quad` を `.draw()` する一連の操作を次のように 1 行で書けます。`Rect::pos` は `Rect` の左上の座標を `Point` 型で、`RectF::pos` は `RectF` の左上の座標を `Vec2` 型で表すメンバ変数です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/10c.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Rect rect{ 150, 200, 400, 100 };

	while (System::Update())
	{
		rect.draw();

		// 時計回りに 45° 回転した長方形を描く
		rect.rotated(45_deg).draw(Palette::Orange);

		// 長方形の左上の座標を回転の軸として時計回りに 60° 回転した長方形を描く
		rect.rotatedAt(rect.pos, 60_deg).draw(Palette::Skyblue);
	}
}
```

`Rect` や `RectF` を作成し、`.shearedX()` または `.shearedY()` を使うと、長方形の辺を X 軸または Y 軸に沿ってスライドさせた平行四辺形を `Quad` 型として作成できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/10d.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 長方形の辺を X 軸方向に 30px ずつスライドさせた平行四辺形を描く
		Rect{ 100, 50, 200, 100 }.drawFrame(1, 0)
			.shearedX(30).draw(Palette::Skyblue);

		// 長方形の辺を Y 軸方向に -50px ずつスライドさせた平行四辺形を描く
		Rect{ 400, 150, 300, 200 }.drawFrame(1, 0)
			.shearedY(-50).draw(Palette::Orange);
	}
}
```


## 17.11 楕円を描く
楕円を描くときは `Ellipse` を作成して `.draw()` します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/11.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 中心 (300, 200), X 軸方向の半径 200, Y 軸方向の半径　100 の楕円を描く
		Ellipse{ 300, 200, 200, 100 }.draw(Palette::Skyblue);

		// 中心 (600, 400), X 軸方向の半径 50, Y 軸方向の半径　150 の楕円を描く
		Ellipse{ 600, 400, 50, 150 }.draw(Palette::Orange);
	}
}
```


## 17.12 角丸長方形を描く
角が丸い長方形を描くには、`RoundRect` を作成して `.draw()` します。`RectF` と同じパラメータに加えて、最後に角の曲線の半径を指定します。`Rect` や `RectF` の `.rounded()` メンバ関数を使って、`Rect` や `RectF` から `RoundRect` を作成することもできます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/12.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Rect rect{ 100, 350, 500, 200 };

	while (System::Update())
	{
		// RectF{ 100, 100, 200, 100 } の角を 10px 丸めた角丸長方形を描く
		RoundRect{ 100, 100, 200, 100, 10 }.draw();

		// RectF{ Arg::center(400, 300), 200, 80 } の角を 5px 丸めた角丸長方形を描く
		RoundRect{ Arg::center(400, 300), 200, 80, 5 }.draw(Palette::Skyblue);

		// 長方形 rect の角を 40px 丸めた角丸長方形を描く
		rect.rounded(40).draw(Palette::Orange);
	}
}
```


## 17.13 一部の角が丸い長方形を描く
`Rect` と `RectF` の `.rounded(tl, tr, br, bl)` メンバ関数を使うと、4 つの角をそれぞれ異なるサイズで丸めた形状（`Polygon` 型）を作成できます。`tl` は左上の角、`tr` は右上の角、`br` は右下の角、`bl` は左下の角の曲線の半径を指定します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/13.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Rect{ 100, 100, 200, 200 }
			.rounded(40, 0, 0, 0).draw(ColorF{ 0.8, 0.9, 1.0 });

		Rect{ 400, 100, 200, 200 }
			.rounded(40, 40, 0, 0).draw(ColorF{ 0.8, 0.9, 1.0 });

		Rect{ 100, 350, 200, 200 }
			.rounded(40, 0, 40, 0).draw(ColorF{ 0.8, 0.9, 1.0 });

		Rect{ 400, 350, 200, 200 }
			.rounded(20, 40, 60, 80).draw(ColorF{ 0.8, 0.9, 1.0 });
	}
}
```


## 17.14 多角形を描く
複雑な図形を簡単に作成できるいくつかの関数が用意されています。これらの関数の戻り値である `Shape2D` 型のオブジェクトを `.draw()`, `.drawFrame()` することで図形を描けます。関数のうち、引数に `double angle` をとるものは、時計回りの回転の角度を指定できます。

| 関数名                  | 形状       | 引数                                                                                    |
|----------------------|----------|---------------------------------------------------------------------------------------------|
| Shape2D::Cross       | ✖ マーク    | `double r, double width, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`          |
| Shape2D::Plus        | ＋マーク     | `double r, double width, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`          |
| Shape2D::Pentagon    | 正五角形     | `double r, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`                     |
| Shape2D::Hexagon     | 正六角形     | `double r, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`                         |
| Shape2D::Ngon        | 正 N 角形   | `uint32 n, double r, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`                |
| Shape2D::Star        | 五芒星      | `double r, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`                             |
| Shape2D::Nstar       | 星        | `uint32 n, double rOuter, double rInner, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0` |
| Shape2D::Arrow       | 矢印       | `const Vec2& from, const Vec2& to, double width, const Vec2& headSize`       |
| Shape2D::Arrow       | 矢印       | `const Line& line, double width, const Vec2& headSize`                        |
| Shape2D::DoubleHeadedArrow | 両方向矢印 | `const Vec2& from, const Vec2& to, double width, const Vec2& headSize`  |
| Shape2D::DoubleHeadedArrow | 両方向矢印 | `const Line& line, double width, const Vec2& headSize`  |
| Shape2D::Rhombus     | ひし形      | `double w, double h, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`    |
| Shape2D::RectBalloon | 長方形の吹き出し | `const RectF& rect, const Vec2& target, double pointingRootRatio = 0.5`   |
| Shape2D::Stairs      | 階段形      | `const Vec2& base, double w, double h, uint32 steps, bool upStairs = true`  |
| Shape2D::Heart      | ハート形   | `double r, const Vec2& center = Vec2{ 0, 0 }, double angle = 0.0`  |
| Shape2D::Squircle      | 四角と円の中間形 | `double r, const Vec2& center, uint32 quality`   |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/14.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウおよびシーンを 1000x600 にリサイズ
	Window::Resize(1000, 600);

	while (System::Update())
	{
		Shape2D::Cross(80, 10, Vec2{ 100, 100 }).draw(Palette::Skyblue);

		Shape2D::Plus(80, 10, Vec2{ 300, 100 }).draw(Palette::Skyblue);

		Shape2D::Pentagon(80, Vec2{ 500, 100 }).draw(Palette::Skyblue);

		Shape2D::Hexagon(80, Vec2{ 700, 100 }).draw(Palette::Skyblue);

		// 30° 回転させる
		Shape2D::Hexagon(80, Vec2{ 900, 100 }, 30_deg).draw(Palette::Skyblue);


		// 正十角形
		Shape2D::Ngon(10, 80, Vec2{ 100, 300 }).draw(Palette::Skyblue);

		Shape2D::Star(80, Vec2{ 300, 300 }).draw(Palette::Skyblue);

		// rOuter は外周の半径、rInner は内周の半径
		Shape2D::NStar(10, 80, 60, Vec2{ 500, 300 }).draw(Palette::Skyblue);

		// headSize は三角形の幅と高さ
		Shape2D::Arrow(Line{ 640, 340, 760, 260 }, 20, Vec2{ 40, 30 }).draw(Palette::Skyblue);

		Shape2D::DoubleHeadedArrow(Line{ 840, 340, 960, 260 }, 20, Vec2{ 40, 30 }).draw(Palette::Skyblue);


		Shape2D::Rhombus(160, 120, Vec2{ 100, 500 }).draw(Palette::Skyblue);

		// 吹き出しの長方形と、三角形の頂点の置を指定。三角形のサイズは pointingRootRatio で決まる
		Shape2D::RectBalloon(RectF{ 220, 420, 160, 120 }, Vec2{ 220, 580 }).draw(Palette::Skyblue);

		// base には階段の最も高い段の底の端の座標を指定。steps は段数、upStairs を false にすると下りの階段に
		Shape2D::Stairs(Vec2{ 560, 560 }, 120, 120, 4).draw(Palette::Skyblue);

		Shape2D::Heart(80, Vec2{ 700, 500 }).draw(Palette::Skyblue);

		// 第 3 引数は角の丸の分割品質
		Shape2D::Squircle(60, Vec2{ 900, 500 }, 64).draw(Palette::Skyblue);
	}
}
```


## 17.15 自由に多角形を描く
`Shape2D` では表現できない多角形を描くには `Polygon` を作成して `.draw()` します。`Polygon` を作成するときは、各頂点の座標を時計回りに指定します。`Polygon` オブジェクトの作成には、メモリの確保や三角形分割の計算に実行時コストがかかるため、とくに頂点数が多いものはループの内側で作成するのを避けるべきです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/15.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Polygon polygon
	{
		Vec2{ 400, 100 }, Vec2{ 600, 300 }, Vec2{ 500, 500 },
		Vec2{ 400, 400 }, Vec2{ 300, 500 }, Vec2{ 200, 300 }
	};

	while (System::Update())
	{
		polygon.draw(Palette::Skyblue);
	}
}
```

`Polygon` よりも少ない実行時コストで図形を描きたい場合は、`Shape2D` や `Buffer2D` クラスの低レイヤ操作を使います。`Shape2D` では、頂点配列のほかにインデックス配列を自前で用意する必要があります。`Buffer2D` ではさらにテクスチャをマッピングするための UV 座標も必要になるため、プログラムが複雑になります。今回のチュートリアルでは扱いません。


## 17.16 穴の開いた角形を描く
穴の開いた `Polygon` を作るには、外周の時計回りの頂点座標リスト (`Array<Vec2>` 型) と、穴の形状の「反時計回り」の頂点座標リストの配列 (`Array<Array<Vec2>>` 型) から `Polygon` を作成します。

既存の `Polygon` に対して `.addHole()` で穴を追加することもできます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/16.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Polygon polygon{
		{ Vec2{ 400, 100 }, Vec2{ 600, 300 }, Vec2{ 500, 500 },
		  Vec2{ 400, 400 }, Vec2{ 300, 500 }, Vec2{ 200, 300 } },
		{ { Vec2{ 450, 250 }, Vec2{ 350, 250 }, Vec2{ 350, 350 }, Vec2{ 450, 350 } } }
	};

	while (System::Update())
	{
		polygon.draw(Palette::Skyblue);
	}
}
```


## 17.17 連続する線分を描く
連続した線分を描くには、`Vec2` 型の頂点の配列から `LineString` を作成して `.draw()` します。`.drawClosed()` では終点と始点を結んだ線も描画されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/17.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
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
		// 太さ 8px で描く
		ls1.draw(8, Palette::Skyblue);

		// 太さ 4px で描く（終点と始点も結ぶ）
		ls2.drawClosed(4, Palette::Orange);
	}
}
```

`Array<Vec2>` から `LineString` を作成することもできます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<Vec2> points
	{
		Vec2{ 100, 60 }, Vec2{ 400, 140 },
		Vec2{ 100, 220 }, Vec2{ 400, 300 },
		Vec2{ 100, 380 }, Vec2{ 400, 460 },
		Vec2{ 100, 540 }
	};

	const LineString ls{ points };

	while (System::Update())
	{
		// 太さ 8px で描く
		ls.draw(8, Palette::Skyblue);
	}
}
```

`LineString` は `Array<Vec2>` のようなメンバ関数を持つため、点の配列として操作することもできます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	LineString ls;

	while (System::Update())
	{
		if (MouseL.down()) // クリックしたら
		{
			ls << Cursor::Pos(); // 点を追加
		}

		ls.draw(8, Palette::Skyblue);
	}
}
```


## 17.18 Catmull-Rom スプライン曲線を描く
指定した通過点を必ず通る Catmull-Rom スプライン曲線を描くには、 `Spline2D` を作成して `.draw()` します。`Spline2D` は `Vec2` の配列または `LineString` から作成できます。コンストラクタの第 2 引数に `Close::Ring` を指定することで、終点と始点がつながっているスプライン曲線を作成できます。

サンプルプログラムでは示していませんが、`.draw()` には曲線計算時の品質（分割数）を指定する引数も用意されていて、デフォルトでは `24` になっています。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/18.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Spline2D splineA
	{ {
		Vec2{ 100, 60 }, Vec2{ 400, 140 },
		Vec2{ 100, 220 }, Vec2{ 400, 300 },
		Vec2{ 100, 380 }, Vec2{ 400, 460 },
		Vec2{ 100, 540 }
	} };

	// CloseRing::Yes -> 終点から始点も結ぶ
	const Spline2D splineB
	{ {
		Vec2{ 500, 100 }, Vec2{ 700, 200 },
		Vec2{ 600, 500 },
	}, CloseRing::Yes };

	while (System::Update())
	{
		// 太さ 8px で描く
		splineA.draw(8, Palette::Skyblue);

		// 太さ 4px で描く
		splineB.draw(4, Palette::Orange);
	}
}
```


## 17.19 ベジェ曲線を描く
2 次ベジェ曲線を描きたいときは `Bezier2`, 3 次ベジェ曲線を描きたいときは `Bezier3` を作成して `.draw()` します。

サンプルプログラムでは示していませんが、`.draw()` には曲線計算時の品質（分割数）を指定する引数も用意されていて、デフォルトでは `24` になっています。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/19.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 2 次ベジェ曲線
		Bezier2{ Vec2{ 100, 400 }, Vec2{ 100, 250 }, Vec2{ 300, 100 } }
			.draw(4, Palette::Skyblue);

		// 3 次ベジェ曲線
		Bezier3{ Vec2{ 300, 400 }, Vec2{ 400, 400 }, Vec2{ 400, 100 }, Vec2{ 500, 100 }}
			.draw(4, Palette::Orange);
	}
}
```


## 17.20 矢印を描く
`Line` には単方向の矢印を描く `.drawArrow()` と、両方向の矢印を描く `.drawDoubleHeadedArrow()` メンバ関数があります。いずれも第 1 引数には線の幅、第 2 引数には三角形の幅と高さを指定します。単方向矢印は、`Line` の始点から終点方向を向きます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/20.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 線の幅 3px, 三角の幅 20px, 高さ 20px の単方向矢印を描く
		Line{ 50, 200, 200, 250 }
			.drawArrow(3, SizeF{ 20, 20 }, Palette::Skyblue);

		// 線の幅 10px, 三角の幅 40px, 高さ 80px の単方向矢印を描く
		Line{ 350, 450, 450, 100 }
			.drawArrow(10, SizeF{ 40, 80 }, Palette::Orange);

		// 線の幅 8px, 三角の幅 30px, 高さ 30px の両方向矢印を描く
		Line{ 600, 100, 700, 400 }
			.drawDoubleHeadedArrow(8, SizeF{ 30, 30 }, Palette::Limegreen);
	}
}
```


## 17.21 影を描く
`Circle`, `Rect`, `RectF`, `RoundRect` は、影を描画する `.drawShadow()` メンバ関数を持っています。第 1 引数で影の位置のオフセット、第 2 引数でぼかしの大きさ、第 3 引数で影の大きさのオフセット、第 4 引数で影の色を指定できます。影は図形で隠れて見えない部分も塗りつぶされるため、影を描いたあとに図形を上から重ね書きする必要があります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/21.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

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
			.drawShadow(Vec2{ 0, 3 }, 8, 1)
			.draw();

		Circle{ 300, 400, 50 }
			.drawShadow(Vec2{ 3, 0 }, 8, 1)
			.draw();

		RoundRect{ 450, 350, 100, 100, 20 }
			.drawShadow(Vec2{ 2, 2 }, 8, 0)
			.draw();

		RoundRect{ 650, 350, 100, 100, 20 }
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw();
	}
}
```


## 17.22 図形の操作
多くの図形クラスが `.movedBy()` メンバ関数を持ち、自身の座標を指定したベクトルで平行移動した図形を作成して返します。また、`Rect` や `Circle`, `Line` など一部の図形クラスは `.stretched()` メンバ関数を持ち、自身の幅や高さを変更した図形を作成して返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/22a.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Circle circle{ 100, 100, 60 };

	const Rect rect{ 400, 300, 200 };

	while (System::Update())
	{
		circle.draw();

		// (200, 0) の方向に平行移動した円を描画する
		circle.movedBy(200, 0).draw(Palette::Skyblue);

		// (0, 200) の方向に平行移動した円を描画する
		circle.movedBy(0, 200).draw(Palette::Orange);


		rect.drawFrame(2, 2);

		// 上下左右を 10px 縮小した長方形を描画する
		rect.stretched(-10).drawFrame(2, 2, Palette::Skyblue);

		// 左右を 40px 拡大、上下を 20px 縮小した長方形を描画する
		rect.stretched(40, -20).drawFrame(2, 2, Palette::Orange);
	}
}
```

`Polygon` は自身を拡大縮小した新しい `Polygon` を返す `.scaled()` や、回転した `Polygon` を返す `.rotated()`, `.rotatedAt()` などのメンバ関数を持ちます。また、`Shape2D` は `Polygon` に変換可能です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/22b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Polygon star = Shape2D::Star(150, Vec2{ 0, 0 });

	while (System::Update())
	{
		star.scaled(1.2).movedBy(200, 200).draw(ColorF{ 0.6 });

		star.movedBy(200, 200).draw(ColorF{ 0.8 });

		star.scaled(0.8).movedBy(200, 200).draw(ColorF{ 1.0 });


		star.rotated(-30_deg).movedBy(600, 400).draw(ColorF{ 0.6 });

		star.movedBy(600, 400).draw(ColorF{ 0.8 });

		star.rotated(30_deg).movedBy(600, 400).draw(ColorF{ 1.0 });
	}
}
```

## 17.23 円座標
座標の指定においては、X 軸 Y 軸を使った座標系ではなく、基準点からの角度と距離に基づいた円座標を使うと便利な場合があります。以下は `Vec2` 型に変換可能な円座標クラスです。

| クラス | 説明 |
|---|---|
| `Circular{ r, theta }` | 原点を中心とする半径 `double r` の円を考え、その円周上で 12 時の方向を 0° として時計回りに `double theta` （ラジアン）の位置を表します。`Vec2` に変換できます。 |
| `OffsetCircular{ offset, r, theta }` | `Vec2 offset` を中心とする半径 `double r` の円を考え、その円周上で 12 時の方向を 0° として時計回りに `double theta` （ラジアン）の位置を表します。`Vec2` に変換できます。 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/23a.png)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/shape/23b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		const double t = Scene::Time();

		for (int32 i = 0; i < 12; ++i)
		{
			const double theta = (i * 30_deg);

			// (400, 300) を中心とする半径 200 の円周上で、角度 theta の位置にある点の座標を計算する
			const Vec2 pos = OffsetCircular{ Vec2{ 400, 300 }, 200, theta };

			Circle{ pos, 20 }.draw(HSV{ i * 30 });
		}
	}
}
```