# 27. 図形の操作
図形に対して、平行移動・拡大縮小・回転・太らせるなどの操作を行う方法を学びます。

## 27.1 図形を移動させる
- 一部の図形クラスは、自身を**指定したベクトルで平行移動した新しい図形**を作成して返す `.movedBy()` メンバ関数を持っています

| コード | 説明 |
|---|---|
| `.movedBy(X軸方向の移動量, Y軸方向の移動量)` | 指定したベクトルで平行移動した図形を作成して返します |
| `.movedBy(移動量)` | 指定したベクトルで平行移動した図形を作成して返します |

- 図形を直接移動させる場合は、`.moveBy()` メンバ関数を使います
	- 戻り値は `void` です

| コード | 説明 |
|---|---|
| `.moveBy(X軸方向の移動量, Y軸方向の移動量)` | 指定したベクトルで平行移動します |
| `.moveBy(移動量)` | 指定したベクトルで平行移動します |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Rect rect{ 100, 100, 300 };

	Circle circle{ 600, 100, 100 };

	while (System::Update())
	{
		circle.moveBy(0, (Scene::DeltaTime() * 100.0));

		rect.draw();

		rect.movedBy(40, 40).draw(Palette::Seagreen);

		circle.draw(ColorF{ 0.2 });
	}
}
```

## 27.2 図形を拡大縮小する（ピクセル指定）
- 一部の図形クラスは、自身の**幅や高さを変更した新しい図形**を作成して返す `.stretched()` メンバ関数を持っています

| コード | 説明 |
|---|---|
| `rect.stretched(上下左右)` | 長方形（`Rect` または `RectF`）の上下左右方向の拡大縮小量（ピクセル）を指定して新しい長方形を作成して返します |
| `rect.stretched(左右, 上下)` | 長方形（`Rect` または `RectF`）の左右方向と上下方向の拡大縮小量（ピクセル）を指定して新しい長方形を作成して返します |
| `rect.stretched(上, 右, 下, 左)` | 長方形（`Rect` または `RectF`）の上右下左方向の拡大縮小量（ピクセル）を指定して新しい長方形を作成して返します |
| `line.stretched(両端)` | 線分（`Line`）の始点と終点の拡大縮小量（ピクセル）を指定して新しい線分を作成して返します |
| `line.stretched(始点, 終点)` | 線分（`Line`）の始点と終点の拡大縮小量（ピクセル）を指定して新しい線分を作成して返します |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Rect rect{ 100, 100, 300 };

	const Line line{ 500, 100, 600, 500 };

	while (System::Update())
	{
		rect.draw();

		rect.stretched(-20).draw(ColorF{ 0.2 });

		line.stretched(40).draw(12);

		line.draw(4, ColorF{ 0.2 });
	}
}
```


## 27.3 図形を拡大縮小する（倍率指定）
- 一部の図形クラスは、自身を**指定した倍率で拡大縮小した新しい図形**を作成して返す `.scaled()`, `.scaledAt()` メンバ関数を持っています

| コード | 説明 |
|---|---|
| `.scaled(倍率)` | 図形を指定した倍率で拡大縮小した新しい図形を作成して返します |
| `.scaled(幅の倍率, 高さの倍率)` | 図形を指定した幅と高さの倍率で拡大縮小した新しい図形を作成して返します |
| `.scaledAt(拡大縮小の基準点, 倍率)` | 図形を指定した倍率で拡大縮小した新しい図形を作成して返します |
| `.scaledAt(拡大縮小の基準点, 幅の倍率, 高さの倍率)` | 図形を指定した幅と高さの倍率で拡大縮小した新しい図形を作成して返します |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Rect rect{ 100, 100, 300 };

	const Circle circle{ 600, 300, 100 };

	while (System::Update())
	{
		rect.draw();

		rect.scaled(0.5).draw(Palette::Seagreen);

		rect.scaledAt(rect.pos, 0.5).draw(ColorF{ 0.2 });

		circle.draw();

		circle.scaled(0.8).draw(ColorF{ 0.2 });
	}
}
```


## 27.4 図形を回転させる
- 一部の図形クラスは、自身を**指定した角度で回転した新しい図形**を作成して返す `.rotated()`, `.rotatedAt()` メンバ関数を持っています

| コード | 説明 |
|---|---|
| `.rotated(回転角度)` | 図形を指定した角度で回転した新しい図形を作成して返します |
| `.rotatedAt(回転の基準点, 回転角度)` | 図形を指定した角度で回転した新しい図形を作成して返します |

- 図形を直接回転させる場合は、`.rotate()`, `.rotateAt()` メンバ関数を使います
	- 戻り値は `void` です

| コード | 説明 |
|---|---|
| `.rotate(回転角度)` | 図形を指定した角度で回転します |
| `.rotateAt(回転の基準点, 回転角度)` | 図形を指定した角度で回転します |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Triangle triangle{ Vec2{ 200, 300 }, 200 };

	const Polygon polygon = Shape2D::Star(200, Vec2{ 600, 300 });

	double angle = 0_deg;

	while (System::Update())
	{
		angle += (Scene::DeltaTime() * 30_deg);

		triangle.rotated(angle).draw(ColorF{ 0.2 });

		polygon.rotatedAt(Vec2{ 600, 300 }, angle).draw(ColorF{ 0.2 });
	}
}
```


## 27.5 多角形を太らせる
- `Polygon` は、自身を**指定した太さで太らせた新しい多角形**を作成して返す `.calculateBuffer()`, `.calculateRoundBuffer()` メンバ関数を持っています
- この関数は計算コストが大きいため、ループ中での使用は避けるべきです

| コード | 説明 |
|---|---|
| `.calculateBuffer(太さ)` | 多角形を指定した太さで太らせた新しい多角形を作成して返します |
| `.calculateRoundBuffer(太さ)` | 多角形を指定した太さで太らせた新しい多角形を作成して返します（角を丸めます） |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Polygon polygon1 = Shape2D::Star(150, Vec2{ 200, 300 });
	const Polygon polygon2 = polygon1.calculateBuffer(20);

	const Polygon polygon3 = Shape2D::Star(150, Vec2{ 600, 300 });
	const Polygon polygon4 = polygon3.calculateRoundBuffer(20);

	while (System::Update())
	{
		polygon2.draw(ColorF{ 0.2 });
		polygon1.drawFrame(4);

		polygon4.draw(ColorF{ 0.2 });
		polygon3.drawFrame(4);
	}
}
```


## 27.6 長方形の構成要素を取得する
- `Rect`, `RectF` には、長方形の構成要素を取得する次のようなメンバ関数があります

| コード | 説明 |
|---|---|
| `.center()` | 長方形の中心座標を返します |
| `.tl()` | 長方形の左上の座標を返します |
| `.tr()` | 長方形の右上の座標を返します |
| `.br()` | 長方形の左下の座標を返します |
| `.bl()` | 長方形の右下の座標を返します |
| `.topCenter()` | 長方形の上辺の中心座標を返します |
| `.rightCenter()` | 長方形の右辺の中心座標を返します |
| `.bottomCenter()` | 長方形の下辺の中心座標を返します |
| `.leftCenter()` | 長方形の左辺の中心座標を返します |
| `.leftX()` | 長方形の左辺の X 座標を返します |
| `.rightX()` | 長方形の右辺の X 座標を返します |
| `.topY()` | 長方形の上辺の Y 座標を返します |
| `.bottomY()` | 長方形の下辺の Y 座標を返します |
| `.getRelativePoint(相対座標 X, 相対座標 Y)` | 長方形の相対座標を絶対座標に変換して返します。<br>左上を `(0,0)`, 右下を `(1,1)` とします |
| `.area()` | 長方形の面積を返します |


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	const Rect rect{ 100, 100, 600, 400 };

	while (System::Update())
	{
		rect.draw();

		rect.top().draw(4, HSV{ 0 });
		rect.right().draw(4, HSV{ 90 });
		rect.bottom().draw(4, HSV{ 180 });
		rect.left().draw(4, HSV{ 270 });

		font(U"TL").drawAt(40, rect.tl(), ColorF{ 0.2 });
		font(U"TC").drawAt(40, rect.topCenter(), ColorF{ 0.2 });
		font(U"TR").drawAt(40, rect.tr(), ColorF{ 0.2 });

		font(U"LC").drawAt(40, rect.leftCenter(), ColorF{ 0.2 });
		font(U"C").drawAt(40, rect.center(), ColorF{ 0.2 });
		font(U"RC").drawAt(40, rect.rightCenter(), ColorF{ 0.2 });

		font(U"BL").drawAt(40, rect.bl(), ColorF{ 0.2 });
		font(U"BC").drawAt(40, rect.bottomCenter(), ColorF{ 0.2 });
		font(U"BR").drawAt(40, rect.br(), ColorF{ 0.2 });

		font(U"(0.8, 0.2)").drawAt(40, rect.getRelativePoint(0.8, 0.2), ColorF{ 0.2 });
	}
}
```


## 27.7 円座標
- 円状に並ぶオブジェクトを扱う場合、X 軸 Y 軸を使った座標系の代わりに、基準点からの距離と角度で位置を表現する**円座標**を使うと便利です
- Siv3D では、円座標を扱うための `Circular` クラスと `OffsetCircular` クラスが用意されています
	- 角度は 12 時の方向を 0 とする、時計回りのラジアンで指定します

| コード | 説明 |
|---|---|
| `Circular{ r, theta }` | 原点を中心として半径 `r`, 角度 `theta` の位置を表します |
| `OffsetCircular{ offset, r, theta }` | `offset` を中心として半径 `r`, 角度 `theta` の位置を表します |

	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/7-1.png)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/7-2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 1.0, 0.98, 0.96 });

	double angle = 0_deg;

	while (System::Update())
	{
		angle += (Scene::DeltaTime() * 30_deg);

		for (int32 i = 0; i < 12; ++i)
		{
			const double theta = (i * 30_deg + angle);

			const Vec2 pos = OffsetCircular{ Vec2{ 400, 300 }, 200, theta };

			pos.asCircle(28)
				.drawShadow(Vec2{ 0, 4 }, 12, 4)
				.draw(HSV{ (i * 30), 0.8, 1.0 })
				.drawFrame(3, 2, ColorF{ 1.0 });
		}
	}
}
```
