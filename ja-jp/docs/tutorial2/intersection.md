# 24. 図形どうしの交差判定

## 24.1 図形どうしの交差
- 図形 A と図形 B が交差しているかを判定するには、`A.intersects(B)` を使います
- 2 つの図形が交差している場合、`true` を返します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/intersection/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	// 敵の円
	Circle enemyCircle{ 400, 200, 100 };

	while (System::Update())
	{
		// プレイヤーの円
		const Circle playerCircle{ Cursor::Pos(), 20 };

		// もし 2 つの Circle が交差している場合
		if (playerCircle.intersects(enemyCircle))
		{
			font(U"Hit!").draw(30, Vec2{ 20, 20 }, ColorF{ 0.1 });
		}

		// 敵の円を描く
		enemyCircle.draw(ColorF{ 0.5 });

		// プレイヤーの円を描く
		playerCircle.draw(ColorF{ 0.0, 0.6, 1.0 });
	}
}
```


## 24.2 複数の図形との交差判定
- 配列に格納された複数の図形と交差判定を行うサンプルです
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/intersection/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	// 敵の円の配列
	Array<Circle> enemyCircles = {
		Circle{ 200, 200, 60 },
		Circle{ 400, 200, 60 },
		Circle{ 600, 200, 60 },
		Circle{ 300, 400, 60 },
		Circle{ 500, 400, 60 },
	};

	while (System::Update())
	{
		// プレイヤーの円
		const Circle playerCircle{ Cursor::Pos(), 20 };

		// 敵の各円との交差判定
		for (const auto& enemyCircle : enemyCircles)
		{
			// もし 2 つの Circle が交差している場合
			if (playerCircle.intersects(enemyCircle))
			{
				font(U"Hit!").draw(30, Vec2{ 20, 20 }, ColorF{ 0.1 });
			}
		}

		// すべての敵の円を描く
		for (const auto& enemyCircle : enemyCircles)
		{
			enemyCircle.draw(ColorF{ 0.5 });
		}

		// プレイヤーの円を描く
		playerCircle.draw(ColorF{ 0.0, 0.6, 1.0 });
	}
}
```


## 24.3 交差した図形を削除する（`.remove_if()` 方式）
- 配列に格納された複数の図形と交差判定を行い、交差したものを削除するサンプルです
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/intersection/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 敵の円の配列
	Array<Circle> enemyCircles = {
		Circle{ 200, 200, 60 },
		Circle{ 400, 200, 60 },
		Circle{ 600, 200, 60 },
		Circle{ 300, 400, 60 },
		Circle{ 500, 400, 60 },
	};

	while (System::Update())
	{
		// プレイヤーの円
		const Circle playerCircle{ Cursor::Pos(), 20 };

		// プレイヤーの円と交差している要素を配列から削除する
		enemyCircles.remove_if([&](const Circle& enemyCircle)
			{
				return playerCircle.intersects(enemyCircle);
			});

		// すべての敵の円を描く
		for (const auto& enemyCircle : enemyCircles)
		{
			enemyCircle.draw(ColorF{ 0.5 });
		}

		// プレイヤーの円を描く
		playerCircle.draw(ColorF{ 0.0, 0.6, 1.0 });
	}
}
```


## 24.4 交差した図形を削除する（イテレータ方式）
- 配列に格納された複数の図形と交差判定を行い、交差したものを削除するサンプルです
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/intersection/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 敵の円の配列
	Array<Circle> enemyCircles = {
		Circle{ 200, 200, 60 },
		Circle{ 400, 200, 60 },
		Circle{ 600, 200, 60 },
		Circle{ 300, 400, 60 },
		Circle{ 500, 400, 60 },
	};

	while (System::Update())
	{
		// プレイヤーの円
		const Circle playerCircle{ Cursor::Pos(), 20 };

		for (auto it = enemyCircles.begin(); it != enemyCircles.end();)
		{
			// プレイヤーの円と敵の円が交差している場合
			if (playerCircle.intersects(*it))
			{
				Print << U"Hit!";
			
				// その円を削除する
				it = enemyCircles.erase(it);
			}
			else
			{
				++it;
			}
		}

		// すべての敵の円を描く
		for (const auto& enemyCircle : enemyCircles)
		{
			enemyCircle.draw(ColorF{ 0.5 });
		}

		// プレイヤーの円を描く
		playerCircle.draw(ColorF{ 0.0, 0.6, 1.0 });
	}
}
```

