
!!! warning "This is the documentation for an old version"
	This is the documentation for an old version of Siv3D (v0.4.3). See [Siv3D Reference v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation-en) for the latest version.

# 2D Geometry

## ハウスドルフ距離
![](https://github.com/Siv3D/siv3d.docs.images/blob/master/reference/2d-geometry/hausdorf.gif?raw=true)
```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(1.0, 0.96, 0.92));
	const Font font(40, Typeface::Heavy);

	const Polygon polygon = Shape2D::Star(240, Scene::Center());
	const LineString contour(polygon.outer());
	
	LineString contourClosed = contour;
	contourClosed << contour.front();
	
	// 精度を高めるため LineString を細かくする
	const LineString base = contourClosed.densified(10.0);

	LineString lines;
	double distance = Inf<double>;

	while (System::Update())
	{
		contour.drawClosed(12, ColorF(0.7));

		lines.draw(10, HSV(10, 1.0, 0.95));

		if (MouseL.pressed())
		{
			lines << Cursor::Pos();

			// ハウスドルフ距離を計算
			distance = Geometry2D::HausdorffDistance(base, lines);
		}

		if (MouseR.pressed())
		{
			lines.clear();
			distance = Inf<double>;
		}

		if (IsFinite(distance))
		{
			font(U"{:.2f}"_fmt(distance)).draw(20, 20, ColorF(0.25));
		}
	}
}
```


## 図形の重なる領域
![](https://github.com/Siv3D/siv3d.docs.images/blob/master/reference/2d-geometry/and.gif?raw=true)
```C++
# include <Siv3D.hpp>

void Main()
{
	const Polygon star = Shape2D::Star(200, Scene::Center());

	while (System::Update())
	{
		const Rect rect(Arg::center(Cursor::Pos()), 300, 100);

		star.drawFrame(2, Palette::Yellow);

		rect.draw(ColorF(1.0, 0.8));

		// star と rect の重なる領域を Polygon の配列で取得
		for (const auto& polygon : Geometry2D::And(star, rect.asPolygon()))
		{
			polygon.draw(ColorF(1.0, 0.0, 0.0, 0.5))
				.drawFrame(4, ColorF(1.0, 0.0, 0.0));
		}
	}
}
```

## 図形の引き算
![](https://github.com/Siv3D/siv3d.docs.images/blob/master/reference/2d-geometry/subtract.gif?raw=true)
```C++
# include <Siv3D.hpp>

void Main()
{
	const Polygon star = Shape2D::Star(200, Scene::Center());

	while (System::Update())
	{
		const Rect rect(Arg::center(Cursor::Pos()), 300, 100);

		star.drawFrame(2, Palette::Yellow);

		rect.drawFrame(2, ColorF(1.0, 0.8));

		// star から rect を引いた領域を Polygon の配列で取得
		for (const auto& polygon : Geometry2D::Subtract(star, rect.asPolygon()))
		{
			polygon.draw(ColorF(1.0, 0.0, 0.0, 0.5))
				.drawFrame(4, ColorF(1.0, 0.0, 0.0));
		}
	}
}
```


## 点群の凸包
![](https://github.com/Siv3D/siv3d.docs.images/blob/master/reference/2d-geometry/convexhull-points.gif?raw=true.gif)
```C++
# include <Siv3D.hpp>

void Main()
{
	Array<Vec2> points;

	Polygon convexHull;

	while (System::Update())
	{
		if (MouseL.down())
		{
            // 点を追加
			points << Cursor::Pos();

            // 凸包を計算
			convexHull = Geometry2D::ConvexHull(points);
		}

		convexHull.draw(Palette::Skyblue);

		for (const auto& point : points)
		{
			Circle(point, 5).draw(Palette::Seagreen);
		}
	}
}
```


## Polygon の凸包
![](https://github.com/Siv3D/siv3d.docs.images/blob/master/reference/2d-geometry/convexhull-polygon.png?raw=true)
```C++
# include <Siv3D.hpp>

void Main()
{
	const Polygon star = Shape2D::Star(200, Scene::Center(), 15_deg);
	
	// 凸包を計算
	const Polygon convexHull = star.calculateConvexHull();

	while (System::Update())
	{
		convexHull.draw(Palette::Gray);

		star.draw(Palette::Yellow);
	}
}
```


## Polygon の拡張
![](https://github.com/Siv3D/siv3d.docs.images/blob/master/reference/2d-geometry/append.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Polygon polygon;

	while (System::Update())
	{
		const Rect rect(Arg::center = Cursor::Pos(), 100);

		if (MouseL.down())
		{
            // polygon に rect を追加する
			// ただし、polygon と rect がつながらない場合は失敗して false を返す
			polygon.append(rect.asPolygon());
		}

		polygon
			.draw(Palette::Skyblue)
			.drawWireframe(1, Palette::White);

		rect.drawFrame(1, 0, Palette::Skyblue);
	}
}
```


## Polygon の太らせ、細らせ
![](https://github.com/Siv3D/siv3d.docs.images/blob/master/reference/2d-geometry/buffer.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.6));

	const Polygon starBase1(Shape2D::Star(100, Scene::Center().movedBy(-160, 0)));
	const Polygon starBase2(Shape2D::Star(100, Scene::Center().movedBy(160, 0)));

	Polygon star1 = starBase1;
	Polygon star2 = starBase2;

	double distance = 0;

	while (System::Update())
	{
		if (SimpleGUI::Slider(U"{:.1f}"_fmt(distance), distance, -30.0, 30.0, Vec2(20, 20)))
		{
			// Polygon を太らせ / 細らせる
			star1 = starBase1.calculateRoundBuffer(distance);
			star2 = starBase2.calculateBuffer(distance);
		}

		star1.draw(Palette::Darkblue);
		star2.draw(Palette::Darkblue);

		starBase1.drawFrame(2, Palette::Yellow);
		starBase2.drawFrame(2, Palette::Yellow);
	}
}
```

