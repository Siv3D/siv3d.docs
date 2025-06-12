# 24. Intersection between Shapes

## 24.1 Intersection between Shapes
- To determine if shape A and shape B intersect, use `A.intersects(B)`
- Returns `true` if the two shapes intersect
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/intersection/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	// Enemy circle
	Circle enemyCircle{ 400, 200, 100 };

	while (System::Update())
	{
		// Player circle
		const Circle playerCircle{ Cursor::Pos(), 20 };

		// If the two circles intersect
		if (playerCircle.intersects(enemyCircle))
		{
			font(U"Hit!").draw(30, Vec2{ 20, 20 }, ColorF{ 0.1 });
		}

		// Draw the enemy circle
		enemyCircle.draw(ColorF{ 0.5 });

		// Draw the player circle
		playerCircle.draw(ColorF{ 0.0, 0.6, 1.0 });
	}
}
```


## 24.2 Intersection with Multiple Shapes
- This sample demonstrates intersection testing with multiple shapes stored in an array
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/intersection/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	// Array of enemy circles
	Array<Circle> enemyCircles = {
		Circle{ 200, 200, 60 },
		Circle{ 400, 200, 60 },
		Circle{ 600, 200, 60 },
		Circle{ 300, 400, 60 },
		Circle{ 500, 400, 60 },
	};

	while (System::Update())
	{
		// Player circle
		const Circle playerCircle{ Cursor::Pos(), 20 };

		// Check intersection with each enemy circle
		for (const auto& enemyCircle : enemyCircles)
		{
			// If the two circles intersect
			if (playerCircle.intersects(enemyCircle))
			{
				font(U"Hit!").draw(30, Vec2{ 20, 20 }, ColorF{ 0.1 });
			}
		}

		// Draw all enemy circles
		for (const auto& enemyCircle : enemyCircles)
		{
			enemyCircle.draw(ColorF{ 0.5 });
		}

		// Draw the player circle
		playerCircle.draw(ColorF{ 0.0, 0.6, 1.0 });
	}
}
```


## 24.3 Removing Intersecting Shapes (`.remove_if()` Method)
- This sample demonstrates intersection testing with multiple shapes stored in an array and removing those that intersect
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/intersection/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Array of enemy circles
	Array<Circle> enemyCircles = {
		Circle{ 200, 200, 60 },
		Circle{ 400, 200, 60 },
		Circle{ 600, 200, 60 },
		Circle{ 300, 400, 60 },
		Circle{ 500, 400, 60 },
	};

	while (System::Update())
	{
		// Player circle
		const Circle playerCircle{ Cursor::Pos(), 20 };

		// Remove elements that intersect with the player circle from the array
		enemyCircles.remove_if([&](const Circle& enemyCircle)
			{
				return playerCircle.intersects(enemyCircle);
			});

		// Draw all enemy circles
		for (const auto& enemyCircle : enemyCircles)
		{
			enemyCircle.draw(ColorF{ 0.5 });
		}

		// Draw the player circle
		playerCircle.draw(ColorF{ 0.0, 0.6, 1.0 });
	}
}
```


## 24.4 Removing Intersecting Shapes (Iterator Method)
- This sample demonstrates intersection testing with multiple shapes stored in an array and removing those that intersect
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/intersection/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Array of enemy circles
	Array<Circle> enemyCircles = {
		Circle{ 200, 200, 60 },
		Circle{ 400, 200, 60 },
		Circle{ 600, 200, 60 },
		Circle{ 300, 400, 60 },
		Circle{ 500, 400, 60 },
	};

	while (System::Update())
	{
		// Player circle
		const Circle playerCircle{ Cursor::Pos(), 20 };

		for (auto it = enemyCircles.begin(); it != enemyCircles.end();)
		{
			// If the player circle and enemy circle intersect
			if (playerCircle.intersects(*it))
			{
				Print << U"Hit!";
			
				// Remove the circle
				it = enemyCircles.erase(it);
			}
			else
			{
				++it;
			}
		}

		// Draw all enemy circles
		for (const auto& enemyCircle : enemyCircles)
		{
			enemyCircle.draw(ColorF{ 0.5 });
		}

		// Draw the player circle
		playerCircle.draw(ColorF{ 0.0, 0.6, 1.0 });
	}
}
```