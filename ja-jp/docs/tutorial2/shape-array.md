# 23. 図形の配列
`Circle` や `Rect` などの図形クラスを配列で扱う方法を学びます。

## 23.1 Circle の配列を作る
- `Array<Circle>` は `Circle` の配列です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/shape-array/1.png)

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


## 23.2 すべての Circle を描画する
- 範囲 for 文（const 参照）を使って、すべての `Circle` を描画します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/shape-array/1.png)

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
		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.3 すべての Circle を動かす
- 範囲 for 文（参照）を使って、すべての `Circle` を動かします

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/shape-array/1.png)

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

		for (auto& circle : circles)
		{
			circle.y += (deltaTime * 50.0);
		}

		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.4 クリックで Circle を追加する
- クリックした位置に、ランダムな半径の `Circle` を追加します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/shape-array/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	
	Array<Circle> circles;

	while (System::Update())
	{
		if (MouseL.down())
		{
			circles << Circle{ Cursor::Pos(), Random(5.0, 40.0) };
		}

		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.5 クリックで Circle を削除する（`.remove_if()` 方式）
- `.remove_if()` を使って、左クリックされた `Circle` を削除します
    - `.remove_if()` については [**チュートリアル 22.17**](./array.md) 参照
- 「その円が左クリックされた」かを判定するラムダ式を `.remove_if()` に渡します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/shape-array/1.png)

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

		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


## 23.6 クリックで Circle を削除する（イテレータ方式）
- イテレータ方式で、左クリックされた `Circle` を削除します
    - イテレータ方式については [**チュートリアル 22.16**](./array.md) 参照
- `.remove_if()` 方式に比べ、削除に合わせた追加の処理（得点の加算、これ以上の削除の打ち切りなど）を書きやすい特徴があります
- 要素へのアクセスは、`.erase()` による削除を行う前に行う必要があります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/shape-array/1.png)

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
		for (auto it = circles.begin(); it != circles.end();)
		{
			if (it->leftClicked())
			{
				// 半径 * 10 をスコアに加算
				score += (it->r * 10); // 要素へのアクセスは削除前に行う

				it = circles.erase(it);
			}
			else
			{
				++it;
			}
		}

		for (const auto& circle : circles)
		{
			circle.draw();
		}

		font(U"Score: {}"_fmt(score)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });
	}
}
```



## 23.7 条件を満たす Circle を削除する
- 

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/shape-array/1.png)

```cpp

```


## 23.8 一定時間ごとに Circle を追加する
- 

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/shape-array/1.png)

```cpp

```


## 23.9 自作クラスの配列を作る
- 

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/shape-array/1.png)

```cpp

```
