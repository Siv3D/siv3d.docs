# 57. 高度な図形処理
2D 図形の高度な機能を学びます。

## 57.1 xxxxxxxxxxxxx


## 57.2 xxxxxxxxxxxxxx


## 57.3 xxxxxxxxxxxxx


## 57.4 xxxxxxxxxxxxxx


## 57.5 xxxxxxxxxxxxx


## 57.6 xxxxxxxxxxxxxx


## 57.7 xxxxxxxxxxxxx


## 57.8 xxxxxxxxxxxxxx


## 57.9 xxxxxxxxxxxxx


## 57.10 xxxxxxxxxxxxxx


## 57.11 xxxxxxxxxxxxxx


## 57.12 xxxxxxxxxxxxxx


## 57.13 xxxxxxxxxxxxxx


## 57.14 xxxxxxxxxxxxxx
<!--
```cpp
# include <Siv3D.hpp>

void Main()
{
	LineString ls;
	double length = 0.0;
	double walkDistance = 0.0;

	while (System::Update())
	{
		if (length)
		{
			walkDistance += Scene::DeltaTime() * 100;

			if (length <= walkDistance)
			{
				walkDistance = 0.0;
			}
		}

		if (MouseL.down())
		{
			ls << Cursor::Pos();
			length = ls.calculateLength();
		}

		ls.draw(2, Palette::Orange);

		if (length)
		{
			ls.calculatePointFromOrigin(walkDistance).asCircle(5).draw();
		}

		for (size_t i = 0; i < ls.num_points(); ++i)
		{
			const Vec2 pos = ls[i];

			// 各点における進行方向左手
			const Vec2 normal = ls.normalAtPoint(i);

			Line{ pos, Arg::direction = normal * 10 }.drawArrow(4, { 5,5 }, Palette::Red);
		}

		for (size_t i = 0; i < ls.num_lines(); ++i)
		{
			const Vec2 pos = ls.line(i).center();

			// 各線における進行方向左手
			const Vec2 normal = ls.normalAtLine(i);

			Line{ pos, Arg::direction = (normal * 10) }.drawArrow(4, { 5,5 }, Palette::Yellow);
		}
	}
}
```
-->