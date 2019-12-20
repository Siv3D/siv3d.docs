description: OpenSiv3D のチュートリアル

# 4. あたり判定

この章では、マウスカーソル vs 図形や、図形 vs 図形の交差判定を処理する方法を学びます。

## 4.1 マウスオーバー
ある図形 g の領域にマウスカーソルが重なっているかを、`g.mouseOver()` で調べられます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/4/1-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Circle circle(Scene::Center(), 100);

	while (System::Update())
	{
		if (circle.mouseOver())
		{
			// 円にマウスカーソルが重なっていれば水色
			circle.draw(Palette::Skyblue);
		}
		else
		{
			// 重なっていなければ灰色
			circle.draw(Palette::Gray);
		}
	}
}
```

このプログラムは条件演算子を使うとさらに短く書けます。

```C++ hl_lines="12"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Circle circle(Scene::Center(), 100);

	while (System::Update())
	{
		// 円にマウスカーソルが重なっていれば水色、そうでなければ灰色
		circle.draw(circle.mouseOver() ? Palette::Skyblue : Palette::Gray);
	}
}
```


## 4.2 図形のクリック

### クリック

ある図形 g が左クリック（またはタッチ）されたかを、`g.leftClicked()` で調べられます。`leftClicked()` は、接触のアクションのうち、最初に接触した瞬間のみを「クリックされた」と判定します。図形を押し続けていても反応はありません。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/4/2-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Circle circle(Scene::Center(), 100);

	int32 count = 0;

	while (System::Update())
	{
		ClearPrint();

		Print << count;

		// 円が左クリックされたら
		if (circle.leftClicked())
		{
			++count;
		}

		circle.draw(Palette::Gray);
	}
}
```

### 押されている

ある図形 g が左クリック（またはタッチ）で押されているかを、`g.leftPressed()` で調べられます。`leftClicked()` と異なり、押され続けていれば、最初の接触以降もつねに「押されている」と判定されます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/4/2-1.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Circle circle(Scene::Center(), 100);

	while (System::Update())
	{
		// 円が押されていれば水色、そうでなければ灰色
		circle.draw(circle.leftPressed() ? Palette::Skyblue : Palette::Gray);
	}
}
```


## 4.3 図形の交差

2 つの図形 g と h が交差しているかは、`g.intersects(h)` で調べられます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/4/3-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	constexpr Rect rect(100, 50, 200, 100);

	constexpr Circle circle(200, 400, 100);

	const Polygon star = Shape2D::Star(200, Vec2(550, 300));

	while (System::Update())
	{
		const Circle c(Cursor::Pos(), 30);

		rect.draw(rect.intersects(c) ? Palette::Skyblue : Palette::Gray);

		circle.draw(circle.intersects(c) ? Palette::Skyblue : Palette::Gray);

		star.draw(star.intersects(c) ? Palette::Skyblue : Palette::Gray);

		c.draw(Palette::Seagreen);
	}
}
```

!!! Info
    楕円どうしなど、一部の図形では交差判定が未実装の場合があります。実装状況は [Intersection.hpp](https://github.com/Siv3D/OpenSiv3D/blob/master/Siv3D/include/Siv3D/Intersection.hpp) で確認できます。

## 4.4 図形を内側に含む

ある図形 `g` が別の図形 `h` を完全に内側に含んでいるかは、`g.contains(h)` で調べられます。次のサンプルでは、マウスカーソルに追従する円が長方形や星などの図形の内部に完全に含まれているときに、その図形の色を変更します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/4/4-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	constexpr Rect rect(100, 50, 200, 100);

	constexpr Circle circle(200, 400, 100);

	const Polygon star = Shape2D::Star(200, Vec2(550, 300));

	while (System::Update())
	{
		const Circle c(Cursor::Pos(), 30);

		rect.draw(rect.contains(c) ? Palette::Skyblue : Palette::Gray);

		circle.draw(circle.contains(c) ? Palette::Skyblue : Palette::Gray);

		star.draw(star.contains(c) ? Palette::Skyblue : Palette::Gray);

		c.draw(Palette::Seagreen);
	}
}
```


## 4.5 線分と交差する点
ある図形 `g` と `h` の詳細な交差情報を `g.intersectsAt(h)` で取得できます。次のようなプログラムで、図形が交差する座標を取得できます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/4/5-0.gif?raw=true)

```C++ hl_lines="18 34 50"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	constexpr Rect rect(100, 50, 200, 100);

	constexpr Circle circle(200, 400, 100);

	constexpr Triangle triangle(Vec2(500, 100), Vec2(700, 500), Vec2(400, 400));

	while (System::Update())
	{
		const Line line(Scene::Center(), Cursor::Pos());

		// rect と line の交差情報を取得
		if (const auto points = rect.intersectsAt(line))
		{
			rect.draw(Palette::Skyblue);

			// 交差する座標に赤い円を表示
			for (const auto& point : points.value())
			{
				Circle(point, 4).draw(Palette::Red);
			}
		}
		else // 交差しない
		{
			rect.draw(Palette::Gray);
		}

		// circle と line の交差情報を取得
		if (const auto points = circle.intersectsAt(line))
		{
			circle.draw(Palette::Skyblue);

			// 交差する座標に赤い円を表示
			for (const auto& point : points.value())
			{
				Circle(point, 4).draw(Palette::Red);
			}
		}
		else // 交差しない
		{
			circle.draw(Palette::Gray);
		}

		// triangle と line の交差情報を取得
		if (const auto points = triangle.intersectsAt(line))
		{
			triangle.draw(Palette::Skyblue);

			// 交差する座標に赤い円を表示
			for (const auto& point : points.value())
			{
				Circle(point, 4).draw(Palette::Red);
			}
		}
		else // 交差しない
		{
			triangle.draw(Palette::Gray);
		}

		line.draw(2, Palette::Seagreen);
	}
}
```

!!! Info
    `.intersectsAt()` の戻り値 `points` は `Optional<Array<Vec2>>` 型です。交差する座標を取得する前に、上記サンプルのように `if (points)` で `Optional` が値を持つかをチェックする必要があります。2 つの図形が交差しない場合 `Optional` は値を持ちません。2 つの線分がぴったりオーバーラップするようなケースでは、`Optional` が値を持っていても、その値は空の配列になります。

## 4.6 マウスカーソルのスタイル
マウスカーソルのスタイルを変更したいときは、`Cursor::RequestStyle()` を通して、変更したいカーソルのスタイルをリクエストします。手の形にしたい場合は `CursorStyle::Hand` を、カーソルを非表示にしたい場合は `CursorStyle::Hidden` を指定します。マウスカーソルのリクエストは、そのフレームにのみ適用されるので、変更を維持したい場合は毎フレームにわたりリクエストをし続ける必要があります。

| CursorStyle                  | カーソルの形状   |
|------------------------------|-----------|
| CursorStyle::Arrow           | 矢印（通常）    |
| CursorStyle::Ibeam           | I マーク     |
| CursorStyle::Cross           | 十字のマーク    |
| CursorStyle::Hand            | 手のアイコン    |
| CursorStyle::NotAllowed      | 禁止のマーク    |
| CursorStyle::ResizeUpDown    | 上下のリサイズ   |
| CursorStyle::ResizeLeftRight | 左右のリサイズ   |
| CursorStyle::Hidden          | 非表示       |
| CursorStyle::Default         | Arrow と同じ |

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/4/6-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	constexpr ColorF buttonColor(0.2, 0.6, 1.0);
	constexpr Circle button(400, 300, 60);
	Transition press(0.05s, 0.05s);

	while (System::Update())
	{
		const bool mouseOver = button.mouseOver();

		// 円の上にマウスカーソルがあれば
		if (mouseOver)
		{
			// マウスカーソルを手の形に
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		press.update(button.leftPressed());

		const double t = press.value();

		button.movedBy(Vec2(0, 0).lerp(Vec2(0, 4), t))
			.drawShadow(Vec2(0, 6).lerp(Vec2(0, 1), t), 12 - t * 7, 5 - t * 4)
			.draw(buttonColor);
	}
}
```
