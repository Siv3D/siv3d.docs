# 幅優先探索の可視化

幅優先探索による迷路の探索を可視化するプログラムを作ります。


## 1. 迷路の表示

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景色を水色に
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// 距離の表示用フォント
	const Font font{ 18, Typeface::Bold };

	// 迷路を可視化するときのマスのサイズ（ピクセル）
	constexpr int32 CellSize = 40;

	// 二次元配列: 迷路 (0: 通行可能, 1: 壁)
	const Grid<int32> maze =
	{
		{ 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1 },
		{ 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1 },
		{ 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 1, 1 },
		{ 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1 },
		{ 1, 0, 1, 1, 1, 0, 1, 1, 1, 0, 1, 0, 1, 1 },
		{ 1, 0, 0, 0, 1, 0, 1, 0, 0, 0, 1, 0, 0, 1 },
		{ 1, 0, 1, 1, 1, 0, 1, 0, 1, 0, 0, 0, 1, 1 },
		{ 1, 0, 1, 0, 0, 0, 1, 0, 1, 0, 1, 0, 1, 1 },
		{ 1, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 1 },
		{ 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1 },
	};

	// 無限大を表現する数
	constexpr int32 INF = 10000;

	// 二次元配列: maze と同じサイズ、すべての要素を INF にセット
	Grid<int32> distances(maze.size(), INF);

	while (System::Update())
	{
		// 状態更新フラグ
		bool update = false;

		// 迷路の状態を可視化
		for (int32 y = 0; y < maze.height(); ++y)
		{
			for (int32 x = 0; x < maze.width(); ++x)
			{
				// マスの正方形
				const Rect rect = Rect{ (x * CellSize), (y * CellSize), CellSize }.stretched(-1);

				if (maze[y][x] == 1) // 壁のマス
				{
					// 黒で表示
					rect.draw(ColorF{ 0.25 });
				}
				else // 通行可能なマス
				{
					// 距離情報
					const int32 distance = distances[y][x];

					if (distance == INF)
					{
						// 灰色で表示
						rect.draw(ColorF{ 0.75 });

						font(U"∞").drawAt(rect.center(), ColorF{0.25});
					}
					else
					{
						// 白で表示
						rect.draw();

						font(distances[y][x]).drawAt(rect.center(), ColorF{ 0.25 });
					}
				}
			}
		}
	}
}
```

## 2. 探索アルゴリズムの可視化

```cpp hl_lines="35-50 54-84 122-127"
# include <Siv3D.hpp>

void Main()
{
	// 背景色を水色に
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// 距離の表示用フォント
	const Font font{ 18, Typeface::Bold };

	// 迷路を可視化するときのマスのサイズ（ピクセル）
	constexpr int32 CellSize = 40;

	// 二次元配列: 迷路 (0: 通行可能, 1: 壁)
	const Grid<int32> maze =
	{
		{ 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1 },
		{ 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1 },
		{ 1, 0, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 1, 1 },
		{ 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1 },
		{ 1, 0, 1, 1, 1, 0, 1, 1, 1, 0, 1, 0, 1, 1 },
		{ 1, 0, 0, 0, 1, 0, 1, 0, 0, 0, 1, 0, 0, 1 },
		{ 1, 0, 1, 1, 1, 0, 1, 0, 1, 0, 0, 0, 1, 1 },
		{ 1, 0, 1, 0, 0, 0, 1, 0, 1, 0, 1, 0, 1, 1 },
		{ 1, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0, 0, 1 },
		{ 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1 },
	};

	// 無限大を表現する数
	constexpr int32 INF = 10000;

	// 二次元配列: maze と同じサイズ、すべての要素を INF にセット
	Grid<int32> distances(maze.size(), INF);

	// 上、左、右、下のマスへのオフセット
	constexpr Point Offsets[4] = { Point{ 0, -1 }, Point{ -1, 0 }, Point{ 1, 0 }, Point{ 0, 1 } };

	// 全要素を確認できるように、std::queue の代わりに std::deque を使う
	std::deque<Point> q;

	// スタート位置
	const Point start{ 1, 1 };
	q.push_back(start);
	distances[start] = 0;

	// 更新の間隔（秒）
	constexpr double UpdateTime = 0.5;

	// 蓄積時間（秒）
	double accumulatedTime = 0.0;

	while (System::Update())
	{
		// 状態更新フラグ
		bool update = false;

		// 前フレームからの経過時間を加算
		accumulatedTime += Scene::DeltaTime();

		// 更新間隔を超えていたら
		if (UpdateTime <= accumulatedTime)
		{
			accumulatedTime -= UpdateTime;

			update = true;
		}

		// 幅優先探索
		if (update && (not q.empty()))
		{
			const Point currentPos = q.front(); q.pop_front();
			const int32 currentDistance = distances[currentPos];

			for (const auto& offset : Offsets)
			{
				const Point nextPos = (currentPos + offset);

				if ((maze[nextPos] == 0) && ((currentDistance + 1) < distances[nextPos]))
				{
					distances[nextPos] = (currentDistance + 1);
					q.push_back(nextPos);
				}
			}
		}

		// 迷路の状態を可視化
		for (int32 y = 0; y < maze.height(); ++y)
		{
			for (int32 x = 0; x < maze.width(); ++x)
			{
				// マスの正方形
				const Rect rect = Rect{ (x * CellSize), (y * CellSize), CellSize }.stretched(-1);

				if (maze[y][x] == 1) // 壁のマス
				{
					// 黒で表示
					rect.draw(ColorF{ 0.25 });
				}
				else // 通行可能なマス
				{
					// 距離情報
					const int32 distance = distances[y][x];

					if (distance == INF)
					{
						// 灰色で表示
						rect.draw(ColorF{ 0.75 });

						font(U"∞").drawAt(rect.center(), ColorF{0.25});
					}
					else
					{
						// 白で表示
						rect.draw();

						font(distances[y][x]).drawAt(rect.center(), ColorF{ 0.25 });
					}
				}
			}
		}

		// queue に入っているマスの可視化
		for (const auto& point : q)
		{
			// 赤い半透明の正方形を重ねる
			Rect{ (point * CellSize), CellSize }.draw(ColorF{ 1.0, 0.0, 0.0, 0.5 });
		}
	}
}
```