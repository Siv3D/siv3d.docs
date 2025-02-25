# 23. 図形の配列
`Circle` や `Rect` などの図形クラスを配列で扱う方法を学びます。

## 23.1 Circle の配列の作成
- `Array<Circle>` は `Circle` の配列です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<Circle> circles =
	{
		Circle{ 100, 300, 10 },
		Circle{ 200, 300, 20 },
		Circle{ 300, 300, 30 },
		Circle{ 400, 300, 40 },
	};

	Print << circles;

	while (System::Update())
	{

	}
}
```


## 23.2 すべての Circle の描画
- 範囲 for 文（const 参照）を使って、すべての `Circle` を描画します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	
	Array<Circle> circles =
	{
		Circle{ 100, 300, 10 },
		Circle{ 200, 300, 20 },
		Circle{ 300, 300, 30 },
		Circle{ 400, 300, 40 },
	};

	while (System::Update())
	{
		// すべての円を描画する
		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.3 すべての Circle の移動
- 範囲 for 文（参照）を使って、すべての `Circle` を動かします

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	
	Array<Circle> circles =
	{
		Circle{ 100, 300, 10 },
		Circle{ 200, 300, 20 },
		Circle{ 300, 300, 30 },
		Circle{ 400, 300, 40 },
	};

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		// すべての円を動かす
		for (auto& circle : circles)
		{
			circle.y += (deltaTime * 50.0);
		}

		// すべての円を描画する
		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.4 クリックによる Circle 追加
- クリックした位置に、ランダムな半径の `Circle` を追加します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	
	Array<Circle> circles;

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			// クリックした位置に、ランダムな半径の円を追加する
			circles << Circle{ Cursor::Pos(), Random(5.0, 40.0) };
		}

		// すべての円を描画する
		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.5 クリックによる Circle 削除（`.remove_if()` 方式）
- `.remove_if()` を使って、左クリックされた `Circle` を削除します
    - `.remove_if()` での要素削除については [**チュートリアル 22.17**](./array.md) 参照
- 「その円が左クリックされたか」を判定するラムダ式を `.remove_if()` に渡します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Array<Circle> circles =
	{
		Circle{ 100, 300, 10 },
		Circle{ 200, 300, 20 },
		Circle{ 300, 300, 30 },
		Circle{ 400, 300, 40 },
	};

	while (System::Update())
	{
		// 左クリックされた円を削除する
		circles.remove_if([](const Circle& circle) { return circle.leftClicked(); });

		// すべての円を描画する
		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.6 クリックによる Circle 削除（イテレータ方式）
- イテレータ方式で、左クリックされた `Circle` を削除します
    - イテレータ方式での要素削除については [**チュートリアル 22.16**](./array.md) 参照
- `.remove_if()` 方式に比べ、削除に合わせた追加の処理（得点の加算、これ以上の削除の打ち切りなど）を書きやすい特徴があります
- 要素へのアクセスは、`.erase()` による削除を行う前に行う必要があります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	Array<Circle> circles =
	{
		Circle{ 100, 300, 10 },
		Circle{ 200, 300, 20 },
		Circle{ 300, 300, 30 },
		Circle{ 400, 300, 40 },
	};

	// スコア
	double score = 0;

	while (System::Update())
	{
		// 各円について
		for (auto it = circles.begin(); it != circles.end();)
		{
			// その円が左クリックされたていたら
			if (it->leftClicked())
			{
				// 半径 * 10 をスコアに加算する
				score += (it->r * 10); // 要素へのアクセスは削除前に行う

				// その円を削除する
				it = circles.erase(it);
			}
			else
			{
				++it;
			}
		}

		// すべての円を描画する
		for (const auto& circle : circles)
		{
			circle.draw();
		}

		font(U"Score: {}"_fmt(score)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });
	}
}
```


## 23.7 所定の位置に移動した Circle の削除
- `Circle` が自動的に移動し、一定の位置に到達したら削除するようにします
- 「Y 座標が 500.0 を超えたか」を判定するラムダ式を `.remove_if()` に渡します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Array<Circle> circles;

	for (int32 i = 0; i < 6; ++i)
	{
		circles << Circle{ (100 + i * 100), Random(0.0, 200.0), 30 };
	}

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		for (auto& circle : circles)
		{
			circle.y += (deltaTime * 100.0);
		}

		// Y 座標が 500.0 を超えた円を削除する
		circles.remove_if([](const Circle& circle) { return (500.0 < circle.y); });

		// すべての円を描画する
		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.8 一定時間ごとの Circle 追加
- [**チュートリアル 19.3**](../tutorial/time.md) の「一定時間おきに何かをする」を応用して、0.5 秒ごとに `Circle` を追加します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/8.png)

```cpp title="0.5 秒ごとに円を追加する（上限 10 個）"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Array<Circle> circles;

	// 円が出現する周期（秒）
	const double spawnInterval = 0.5;

	// 円の数の上限（個）
	const size_t maxCircles = 10;

	// 蓄積時間（秒）
	double accumulatedTime = 0.0;

	while (System::Update())
	{
		// 前フレームからの経過時間（秒）
		const double deltaTime = Scene::DeltaTime();

		// 蓄積時間を増やす
		accumulatedTime += deltaTime;

		// 蓄積時間が周期を超えたら
		if (spawnInterval < accumulatedTime)
		{
			// 円の数が上限数に達していなければ
			if (circles.size() < maxCircles)
			{
				// 円を追加する
				circles << Circle{ Random(100, 700), Random(100, 500), 30 };
			}

			// 蓄積時間を周期分減らす
			accumulatedTime -= spawnInterval;
		}

		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.9 自作クラスの配列（1）
- `Circle` と色相を持つ `ColorCircle` クラスを作成し、配列で扱います

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/9.png)

```cpp
# include <Siv3D.hpp>

struct ColorCircle
{
	Circle circle;

	double hue;

	// 円を描画する関数
	void draw() const
	{
		circle.draw(HSV{ hue, 0.75, 0.9 });
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Array<ColorCircle> circles;

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			// ColorCircle を追加する
			circles << ColorCircle{ Circle{ Cursor::Pos(), Random(5.0, 40.0) }, Random(0.0, 360.0) };
		}

		// すべての ColorCircle を描画する
		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.10 自作クラスの配列（2）
- 自身が何回押されたかのカウントを持つ `RectCounter` クラスを作成し、配列で扱います
- `Rect` の `.center()` は、長方形の中心座標を `Point` 型で返します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-array/10.png)

```cpp
# include <Siv3D.hpp>

struct RectCounter
{
	Rect rect;

	int32 count = 0;

	// カウンターを更新する関数
	void update()
	{
		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		if (rect.leftClicked())
		{
			++count;
		}
	}

	// カウンターを描画する関数
	void draw(const Font& font) const
	{
		rect.draw();
		rect.drawFrame(2, ColorF{ 0.1 });
		font(U"{}"_fmt(count)).drawAt(rect.center(), ColorF{ 0.1 });
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	Array<RectCounter> rectCounters;
	rectCounters << RectCounter{ Rect{ 100, 100, 100, 100 } };
	rectCounters << RectCounter{ Rect{ 300, 100, 100, 100 } };
	rectCounters << RectCounter{ Rect{ 500, 100, 100, 100 } };

	while (System::Update())
	{
		// すべての RectCounter を更新する
		for (auto& rectCounter : rectCounters)
		{
			rectCounter.update();
		}

		// すべての RectCounter を描画する
		for (const auto& rectCounter : rectCounters)
		{
			rectCounter.draw(font);
		}
	}
}
```

