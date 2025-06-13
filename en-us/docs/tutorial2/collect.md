# 25. Project: Item Collection Game
Using the content from tutorials 3-24, we'll create a game where you collect falling items.

## 25.1 Drawing the Background
- Draw the sky and ground by combining two rectangles
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/1.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Function to draw the background
	void DrawBackground()
	{
		// Draw the sky
		Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

		// Draw the ground
		Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
	}

	void Main()
	{
		while (System::Update())
		{
			// Draw the background
			DrawBackground();
		}
	}
	```


## 25.2 Implementing the Player
- Create a `Player` class to manage player information
- Use member variable `Circle circle` for the player's area and member variable `Texture texture` for the player's emoji
- Draw the player using the member function `.draw()`
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/2.png)

??? memo "Code"
	```cpp hl_lines="3-15 29 36-37"
	# include <Siv3D.hpp>

	// Player class
	struct Player
	{
		Circle circle{ 400, 530, 30 };

		Texture texture{ U"😃"_emoji };

		// Function to draw the player
		void draw() const
		{
			texture.scaled(0.5).drawAt(circle.center);
		}
	};

	// Function to draw the background
	void DrawBackground()
	{
		// Draw the sky
		Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

		// Draw the ground
		Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
	}

	void Main()
	{
		Player player;

		while (System::Update())
		{
			// Draw the background
			DrawBackground();

			// Draw the player
			player.draw();
		}
	}
	```


## 25.3 Player Movement
- Add member function `.update()` to the `Player` class to implement player movement
- Use the `Clamp` function to restrict the range of X coordinates where the player can move, preventing them from going off-screen
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/3.png)

??? memo "Code"
	```cpp hl_lines="10-29 54-69"
	# include <Siv3D.hpp>

	// Player class
	struct Player
	{
		Circle circle{ 400, 530, 30 };

		Texture texture{ U"😃"_emoji };

		// Function to update the player's state
		void update(double deltaTime)
		{
			const double speed = (deltaTime * 400.0);

			// Move left when [←] key is pressed
			if (KeyLeft.pressed())
			{
				circle.x -= speed;
			}

			// Move right when [→] key is pressed
			if (KeyRight.pressed())
			{
				circle.x += speed;
			}

			// Keep the player from going off-screen
			circle.x = Clamp(circle.x, 30.0, 770.0);
		}

		// Function to draw the player
		void draw() const
		{
			texture.scaled(0.5).drawAt(circle.center);
		}
	};

	// Function to draw the background
	void DrawBackground()
	{
		// Draw the sky
		Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

		// Draw the ground
		Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
	}

	void Main()
	{
		Player player;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// Update the player's state
			player.update(deltaTime);

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Draw the player
			player.draw();
		}
	}
	```


## 25.4 Implementing the Item Class
- Create an `Item` class to represent items falling from the sky
- Use member variable `Circle circle` for the item's area and member variable `int32 type` for the item type
- `type` of `0` represents candy, and `1` represents cake
- Draw items using member function `.draw()`, but as a temporary implementation, draw a red circle when `type` is `0` and a white circle when `type` is `1`
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/4.png)

??? memo "Code"
	```cpp hl_lines="38-60 72-79 85-88 115-116"
	# include <Siv3D.hpp>

	// Player class
	struct Player
	{
		Circle circle{ 400, 530, 30 };

		Texture texture{ U"😃"_emoji };

		// Function to update the player's state
		void update(double deltaTime)
		{
			const double speed = (deltaTime * 400.0);

			// Move left when [←] key is pressed
			if (KeyLeft.pressed())
			{
				circle.x -= speed;
			}

			// Move right when [→] key is pressed
			if (KeyRight.pressed())
			{
				circle.x += speed;
			}

			// Keep the player from going off-screen
			circle.x = Clamp(circle.x, 30.0, 770.0);
		}

		// Function to draw the player
		void draw() const
		{
			texture.scaled(0.5).drawAt(circle.center);
		}
	};

	// Item class
	struct Item
	{
		Circle circle;

		// Item type (0: candy, 1: cake)
		int32 type;

		// Function to draw the item (temporary implementation)
		void draw() const
		{
			if (type == 0)
			{
				// Draw candy
				circle.draw(Palette::Red);
			}
			else if (type == 1)
			{
				// Draw cake
				circle.draw(Palette::White);
			}
		}
	};

	// Function to draw the background
	void DrawBackground()
	{
		// Draw the sky
		Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

		// Draw the ground
		Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
	}

	// Function to draw items
	void DrawItems(const Array<Item>& items)
	{
		for (const auto& item : items)
		{
			item.draw();
		}
	}

	void Main()
	{
		Player player;

		// Array of items
		Array<Item> items;
		items << Item{ Circle{ 200, 200, 30 }, 0 };
		items << Item{ Circle{ 600, 100, 30 }, 1 };

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// Update the player's state
			player.update(deltaTime);

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Draw the player
			player.draw();

			// Draw all items
			DrawItems(items);
		}
	}
	```


## 25.5 Efficient Texture Management (Sharing via Arrays)
- We don't create a `Texture` member variable in the `Item` class
- Creating a new `Texture` for each of many items is inefficient
- We prepare the minimum necessary textures in advance as an array and reference them when drawing
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/5.png)

??? memo "Code"
	```cpp hl_lines="46-47 49-50 65 69 75-80 115"
	# include <Siv3D.hpp>

	// Player class
	struct Player
	{
		Circle circle{ 400, 530, 30 };

		Texture texture{ U"😃"_emoji };

		// Function to update the player's state
		void update(double deltaTime)
		{
			const double speed = (deltaTime * 400.0);

			// Move left when [←] key is pressed
			if (KeyLeft.pressed())
			{
				circle.x -= speed;
			}

			// Move right when [→] key is pressed
			if (KeyRight.pressed())
			{
				circle.x += speed;
			}

			// Keep the player from going off-screen
			circle.x = Clamp(circle.x, 30.0, 770.0);
		}

		// Function to draw the player
		void draw() const
		{
			texture.scaled(0.5).drawAt(circle.center);
		}
	};

	// Item class
	struct Item
	{
		Circle circle;

		// Item type (0: candy, 1: cake)
		int32 type;

		// Function to draw the item
		void draw(const Array<Texture>& itemTextures) const
		{
			// Draw texture according to item type
			itemTextures[type].scaled(0.5).drawAt(circle.center);
		}
	};

	// Function to draw the background
	void DrawBackground()
	{
		// Draw the sky
		Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

		// Draw the ground
		Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
	}

	// Function to draw items
	void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
	{
		for (const auto& item : items)
		{
			item.draw(itemTextures);
		}
	}

	void Main()
	{
		// Array of item textures
		const Array<Texture> itemTextures =
		{
			Texture{ U"🍬"_emoji },
			Texture{ U"🍰"_emoji },
		};

		Player player;

		// Array of items
		Array<Item> items;
		items << Item{ Circle{ 200, 200, 30 }, 0 };
		items << Item{ Circle{ 600, 100, 30 }, 1 };

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// Update the player's state
			player.update(deltaTime);

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Draw the player
			player.draw();

			// Draw all items
			DrawItems(items, itemTextures);
		}
	}
	```


## 25.6 Item Falling and Removal
- Add member function `.update()` to the `Item` class to make items fall
- Remove items that have fallen to the ground using `.remove_if()`
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/6.png)

??? memo "Code"
	```cpp hl_lines="46-50 60-70 120-121"
	# include <Siv3D.hpp>

	// Player class
	struct Player
	{
		Circle circle{ 400, 530, 30 };

		Texture texture{ U"😃"_emoji };

		// Function to update the player's state
		void update(double deltaTime)
		{
			const double speed = (deltaTime * 400.0);

			// Move left when [←] key is pressed
			if (KeyLeft.pressed())
			{
				circle.x -= speed;
			}

			// Move right when [→] key is pressed
			if (KeyRight.pressed())
			{
				circle.x += speed;
			}

			// Keep the player from going off-screen
			circle.x = Clamp(circle.x, 30.0, 770.0);
		}

		// Function to draw the player
		void draw() const
		{
			texture.scaled(0.5).drawAt(circle.center);
		}
	};

	// Item class
	struct Item
	{
		Circle circle;

		// Item type (0: candy, 1: cake)
		int32 type;

		void update(double deltaTime)
		{
			// Move the item downward
			circle.y += (deltaTime * 200.0);
		}

		// Function to draw the item
		void draw(const Array<Texture>& itemTextures) const
		{
			// Draw texture according to item type
			itemTextures[type].scaled(0.5).drawAt(circle.center);
		}
	};

	void UpdateItems(Array<Item>& items, double deltaTime)
	{
		// Update all items
		for (auto& item : items)
		{
			item.update(deltaTime);
		}

		// Remove items that have fallen to the ground
		items.remove_if([](const Item& item) { return (580 < item.circle.y); });
	}

	// Function to draw the background
	void DrawBackground()
	{
		// Draw the sky
		Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

		// Draw the ground
		Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
	}

	// Function to draw items
	void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
	{
		for (const auto& item : items)
		{
			item.draw(itemTextures);
		}
	}

	void Main()
	{
		// Array of item textures
		const Array<Texture> itemTextures =
		{
			Texture{ U"🍬"_emoji },
			Texture{ U"🍰"_emoji },
		};

		Player player;

		// Array of items
		Array<Item> items;
		items << Item{ Circle{ 200, 200, 30 }, 0 };
		items << Item{ Circle{ 600, 100, 30 }, 1 };

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// Update the player's state
			player.update(deltaTime);

			// Update all items
			UpdateItems(items, deltaTime);

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Draw the player
			player.draw();

			// Draw all items
			DrawItems(items, itemTextures);
		}
	}
	```


## 25.7 Periodic Item Generation
- Make items appear at random positions in the air every 0.8 seconds
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/7.png)

??? memo "Code"
	```cpp hl_lines="107-111 123-134"
	# include <Siv3D.hpp>

	// Player class
	struct Player
	{
		Circle circle{ 400, 530, 30 };

		Texture texture{ U"😃"_emoji };

		// Function to update the player's state
		void update(double deltaTime)
		{
			const double speed = (deltaTime * 400.0);

			// Move left when [←] key is pressed
			if (KeyLeft.pressed())
			{
				circle.x -= speed;
			}

			// Move right when [→] key is pressed
			if (KeyRight.pressed())
			{
				circle.x += speed;
			}

			// Keep the player from going off-screen
			circle.x = Clamp(circle.x, 30.0, 770.0);
		}

		// Function to draw the player
		void draw() const
		{
			texture.scaled(0.5).drawAt(circle.center);
		}
	};

	// Item class
	struct Item
	{
		Circle circle;

		// Item type (0: candy, 1: cake)
		int32 type;

		void update(double deltaTime)
		{
			// Move the item downward
			circle.y += (deltaTime * 200.0);
		}

		// Function to draw the item
		void draw(const Array<Texture>& itemTextures) const
		{
			// Draw texture according to item type
			itemTextures[type].scaled(0.5).drawAt(circle.center);
		}
	};

	void UpdateItems(Array<Item>& items, double deltaTime)
	{
		// Update all items
		for (auto& item : items)
		{
			item.update(deltaTime);
		}

		// Remove items that have fallen to the ground
		items.remove_if([](const Item& item) { return (580 < item.circle.y); });
	}

	// Function to draw the background
	void DrawBackground()
	{
		// Draw the sky
		Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

		// Draw the ground
		Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
	}

	// Function to draw items
	void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
	{
		for (const auto& item : items)
		{
			item.draw(itemTextures);
		}
	}

	void Main()
	{
		// Array of item textures
		const Array<Texture> itemTextures =
		{
			Texture{ U"🍬"_emoji },
			Texture{ U"🍰"_emoji },
		};

		Player player;

		// Array of items
		Array<Item> items;
		items << Item{ Circle{ 200, 200, 30 }, 0 };
		items << Item{ Circle{ 600, 100, 30 }, 1 };

		// Item spawn interval (seconds)
		const double spawnInterval = 0.8;

		// Accumulated time (seconds)
		double accumulatedTime = 0.0;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// Increase accumulated time
			accumulatedTime += deltaTime;

			// If accumulated time exceeds the interval
			if (spawnInterval < accumulatedTime)
			{
				// Add a new item
				items << Item{ Circle{ Random(30.0, 770.0), -30, 30 }, Random(0, 1) };

				// Reduce accumulated time by the interval
				accumulatedTime -= spawnInterval;
			}

			// Update the player's state
			player.update(deltaTime);

			// Update all items
			UpdateItems(items, deltaTime);

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Draw the player
			player.draw();

			// Draw all items
			DrawItems(items, itemTextures);
		}
	}
	```


## 25.8 Introducing the Score System
- Introduce a score that increases when items are collected
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/8.png)

??? memo "Code"
	```cpp hl_lines="91-96 122-123 169-170"
	# include <Siv3D.hpp>

	// Player class
	struct Player
	{
		Circle circle{ 400, 530, 30 };

		Texture texture{ U"😃"_emoji };

		// Function to update the player's state
		void update(double deltaTime)
		{
			const double speed = (deltaTime * 400.0);

			// Move left when [←] key is pressed
			if (KeyLeft.pressed())
			{
				circle.x -= speed;
			}

			// Move right when [→] key is pressed
			if (KeyRight.pressed())
			{
				circle.x += speed;
			}

			// Keep the player from going off-screen
			circle.x = Clamp(circle.x, 30.0, 770.0);
		}

		// Function to draw the player
		void draw() const
		{
			texture.scaled(0.5).drawAt(circle.center);
		}
	};

	// Item class
	struct Item
	{
		Circle circle;

		// Item type (0: candy, 1: cake)
		int32 type;

		void update(double deltaTime)
		{
			// Move the item downward
			circle.y += (deltaTime * 200.0);
		}

		// Function to draw the item
		void draw(const Array<Texture>& itemTextures) const
		{
			// Draw texture according to item type
			itemTextures[type].scaled(0.5).drawAt(circle.center);
		}
	};

	void UpdateItems(Array<Item>& items, double deltaTime)
	{
		// Update all items
		for (auto& item : items)
		{
			item.update(deltaTime);
		}

		// Remove items that have fallen to the ground
		items.remove_if([](const Item& item) { return (580 < item.circle.y); });
	}

	// Function to draw the background
	void DrawBackground()
	{
		// Draw the sky
		Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

		// Draw the ground
		Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
	}

	// Function to draw items
	void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
	{
		for (const auto& item : items)
		{
			item.draw(itemTextures);
		}
	}

	// Function to draw UI
	void DrawUI(int32 score, const Font& font)
	{
		// Draw the score
		font(U"SCORE: {}"_fmt(score)).draw(30, Vec2{ 20, 20 });
	}

	void Main()
	{
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Array of item textures
		const Array<Texture> itemTextures =
		{
			Texture{ U"🍬"_emoji },
			Texture{ U"🍰"_emoji },
		};

		Player player;

		// Array of items
		Array<Item> items;
		items << Item{ Circle{ 200, 200, 30 }, 0 };
		items << Item{ Circle{ 600, 100, 30 }, 1 };

		// Item spawn interval (seconds)
		const double spawnInterval = 0.8;

		// Accumulated time (seconds)
		double accumulatedTime = 0.0;

		// Score
		int32 score = 0;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// Increase accumulated time
			accumulatedTime += deltaTime;

			// If accumulated time exceeds the interval
			if (spawnInterval < accumulatedTime)
			{
				// Add a new item
				items << Item{ Circle{ Random(30.0, 770.0), -30, 30 }, Random(0, 1) };

				// Reduce accumulated time by the interval
				accumulatedTime -= spawnInterval;
			}

			// Update the player's state
			player.update(deltaTime);

			// Update all items
			UpdateItems(items, deltaTime);

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Draw the player
			player.draw();

			// Draw all items
			DrawItems(items, itemTextures);

			// Draw UI
			DrawUI(score, font);
		}
	}
	```


## 25.9 Implementing Item-Player Collision Detection
- Check whether each item's circle overlaps with the player's circle
- If they overlap, remove the item and increase the score
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/9.png)

??? memo "Code"
	```cpp hl_lines="60 68-84 170"
	# include <Siv3D.hpp>

	// Player class
	struct Player
	{
		Circle circle{ 400, 530, 30 };

		Texture texture{ U"😃"_emoji };

		// Function to update the player's state
		void update(double deltaTime)
		{
			const double speed = (deltaTime * 400.0);

			// Move left when [←] key is pressed
			if (KeyLeft.pressed())
			{
				circle.x -= speed;
			}

			// Move right when [→] key is pressed
			if (KeyRight.pressed())
			{
				circle.x += speed;
			}

			// Keep the player from going off-screen
			circle.x = Clamp(circle.x, 30.0, 770.0);
		}

		// Function to draw the player
		void draw() const
		{
			texture.scaled(0.5).drawAt(circle.center);
		}
	};

	// Item class
	struct Item
	{
		Circle circle;

		// Item type (0: candy, 1: cake)
		int32 type;

		void update(double deltaTime)
		{
			// Move the item downward
			circle.y += (deltaTime * 200.0);
		}

		// Function to draw the item
		void draw(const Array<Texture>& itemTextures) const
		{
			// Draw texture according to item type
			itemTextures[type].scaled(0.5).drawAt(circle.center);
		}
	};

	void UpdateItems(Array<Item>& items, double deltaTime, const Player& player, int32& score)
	{
		// Update all items
		for (auto& item : items)
		{
			item.update(deltaTime);
		}

		// For each item
		for (auto it = items.begin(); it != items.end();)
		{
			// If the player and item intersect
			if (player.circle.intersects(it->circle))
			{
				// Add to score (candy: 10 points, cake: 50 points)
				score += ((it->type == 0) ? 10 : 50);

				// Remove the item
				it = items.erase(it);
			}
			else
			{
				++it;
			}
		}

		// Remove items that have fallen to the ground
		items.remove_if([](const Item& item) { return (580 < item.circle.y); });
	}

	// Function to draw the background
	void DrawBackground()
	{
		// Draw the sky
		Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

		// Draw the ground
		Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
	}

	// Function to draw items
	void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
	{
		for (const auto& item : items)
		{
			item.draw(itemTextures);
		}
	}

	// Function to draw UI
	void DrawUI(int32 score, const Font& font)
	{
		// Draw the score
		font(U"SCORE: {}"_fmt(score)).draw(30, Vec2{ 20, 20 });
	}

	void Main()
	{
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Array of item textures
		const Array<Texture> itemTextures =
		{
			Texture{ U"🍬"_emoji },
			Texture{ U"🍰"_emoji },
		};

		Player player;

		// Array of items
		Array<Item> items;
		items << Item{ Circle{ 200, 200, 30 }, 0 };
		items << Item{ Circle{ 600, 100, 30 }, 1 };

		// Item spawn interval (seconds)
		const double spawnInterval = 0.8;

		// Accumulated time (seconds)
		double accumulatedTime = 0.0;

		// Score
		int32 score = 0;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// Increase accumulated time
			accumulatedTime += deltaTime;

			// If accumulated time exceeds the interval
			if (spawnInterval < accumulatedTime)
			{
				// Add a new item
				items << Item{ Circle{ Random(30.0, 770.0), -30, 30 }, Random(0, 1) };

				// Reduce accumulated time by the interval
				accumulatedTime -= spawnInterval;
			}

			// Update the player's state
			player.update(deltaTime);

			// Update all items
			UpdateItems(items, deltaTime, player, score);

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Draw the player
			player.draw();

			// Draw all items
			DrawItems(items, itemTextures);

			// Draw UI
			DrawUI(score, font);
		}
	}
	```


## 25.10 Implementing Remaining Time
- Introduce a variable `double remainingTime` to represent the remaining time

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/10.png)

??? memo "Code"
	```cpp hl_lines="110 115-121 151-152 164-165 202"
	# include <Siv3D.hpp>

	// Player class
	struct Player
	{
		Circle circle{ 400, 530, 30 };

		Texture texture{ U"😃"_emoji };

		// Function to update the player's state
		void update(double deltaTime)
		{
			const double speed = (deltaTime * 400.0);

			// Move left when [←] key is pressed
			if (KeyLeft.pressed())
			{
				circle.x -= speed;
			}

			// Move right when [→] key is pressed
			if (KeyRight.pressed())
			{
				circle.x += speed;
			}

			// Keep the player from going off-screen
			circle.x = Clamp(circle.x, 30.0, 770.0);
		}

		// Function to draw the player
		void draw() const
		{
			texture.scaled(0.5).drawAt(circle.center);
		}
	};

	// Item class
	struct Item
	{
		Circle circle;

		// Item type (0: candy, 1: cake)
		int32 type;

		void update(double deltaTime)
		{
			// Move the item downward
			circle.y += (deltaTime * 200.0);
		}

		// Function to draw the item
		void draw(const Array<Texture>& itemTextures) const
		{
			// Draw texture according to item type
			itemTextures[type].scaled(0.5).drawAt(circle.center);
		}
	};

	void UpdateItems(Array<Item>& items, double deltaTime, const Player& player, int32& score)
	{
		// Update all items
		for (auto& item : items)
		{
			item.update(deltaTime);
		}

		// For each item
		for (auto it = items.begin(); it != items.end();)
		{
			// If the player and item intersect
			if (player.circle.intersects(it->circle))
			{
				// Add to score (candy: 10 points, cake: 50 points)
				score += ((it->type == 0) ? 10 : 50);

				// Remove the item
				it = items.erase(it);
			}
			else
			{
				++it;
			}
		}

		// Remove items that have fallen to the ground
		items.remove_if([](const Item& item) { return (580 < item.circle.y); });
	}

	// Function to draw the background
	void DrawBackground()
	{
		// Draw the sky
		Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

		// Draw the ground
		Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
	}

	// Function to draw items
	void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
	{
		for (const auto& item : items)
		{
			item.draw(itemTextures);
		}
	}

	// Function to draw UI
	void DrawUI(int32 score, double remainingTime, const Font& font)
	{
		// Draw the score
		font(U"SCORE: {}"_fmt(score)).draw(30, Vec2{ 20, 20 });

		// Draw the remaining time
		font(U"TIME: {:.0f}"_fmt(remainingTime)).draw(30, Arg::topRight(780, 20));

		if (remainingTime <= 0.0)
		{
			font(U"TIME'S UP!").drawAt(80, Vec2{ 400, 270 }, ColorF{ 0.3 });
		}
	}

	void Main()
	{
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Array of item textures
		const Array<Texture> itemTextures =
		{
			Texture{ U"🍬"_emoji },
			Texture{ U"🍰"_emoji },
		};

		Player player;

		// Array of items
		Array<Item> items;
		items << Item{ Circle{ 200, 200, 30 }, 0 };
		items << Item{ Circle{ 600, 100, 30 }, 1 };

		// Item spawn interval (seconds)
		const double spawnInterval = 0.8;

		// Accumulated time (seconds)
		double accumulatedTime = 0.0;

		// Score
		int32 score = 0;

		// Remaining time (seconds)
		double remainingTime = 20.0;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// Decrease remaining time
			remainingTime = Max((remainingTime - deltaTime), 0.0);

			// Increase accumulated time
			accumulatedTime += deltaTime;

			// If accumulated time exceeds the interval
			if (spawnInterval < accumulatedTime)
			{
				// Add a new item
				items << Item{ Circle{ Random(30.0, 770.0), -30, 30 }, Random(0, 1) };

				// Reduce accumulated time by the interval
				accumulatedTime -= spawnInterval;
			}

			// Update the player's state
			player.update(deltaTime);

			// Update all items
			UpdateItems(items, deltaTime, player, score);

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Draw the player
			player.draw();

			// Draw all items
			DrawItems(items, itemTextures);

			// Draw UI
			DrawUI(score, remainingTime, font);
		}
	}
	```

## 25.11 【Complete】Item Rotation and Game Over Processing
- Make items rotate according to their Y coordinate while falling
- When the remaining time reaches 0, clear all items and stop accepting player controls
- The game is now complete

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/11.png)

??? memo "Code"
	```cpp hl_lines="56 167-169 188-192"
	# include <Siv3D.hpp>

	// Player class
	struct Player
	{
		Circle circle{ 400, 530, 30 };

		Texture texture{ U"😃"_emoji };

		// Function to update the player's state
		void update(double deltaTime)
		{
			const double speed = (deltaTime * 400.0);

			// Move left when [←] key is pressed
			if (KeyLeft.pressed())
			{
				circle.x -= speed;
			}

			// Move right when [→] key is pressed
			if (KeyRight.pressed())
			{
				circle.x += speed;
			}

			// Keep the player from going off-screen
			circle.x = Clamp(circle.x, 30.0, 770.0);
		}

		// Function to draw the player
		void draw() const
		{
			texture.scaled(0.5).drawAt(circle.center);
		}
	};

	// Item class
	struct Item
	{
		Circle circle;

		// Item type (0: candy, 1: cake)
		int32 type;

		void update(double deltaTime)
		{
			// Move the item downward
			circle.y += (deltaTime * 200.0);
		}

		// Function to draw the item
		void draw(const Array<Texture>& itemTextures) const
		{
			// Draw texture according to item type
			itemTextures[type].scaled(0.5).rotated(circle.y * 0.3_deg).drawAt(circle.center);
		}
	};

	void UpdateItems(Array<Item>& items, double deltaTime, const Player& player, int32& score)
	{
		// Update all items
		for (auto& item : items)
		{
			item.update(deltaTime);
		}

		// For each item
		for (auto it = items.begin(); it != items.end();)
		{
			// If the player and item intersect
			if (player.circle.intersects(it->circle))
			{
				// Add to score (candy: 10 points, cake: 50 points)
				score += ((it->type == 0) ? 10 : 50);

				// Remove the item
				it = items.erase(it);
			}
			else
			{
				++it;
			}
		}

		// Remove items that have fallen to the ground
		items.remove_if([](const Item& item) { return (580 < item.circle.y); });
	}

	// Function to draw the background
	void DrawBackground()
	{
		// Draw the sky
		Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

		// Draw the ground
		Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
	}

	// Function to draw items
	void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
	{
		for (const auto& item : items)
		{
			item.draw(itemTextures);
		}
	}

	// Function to draw UI
	void DrawUI(int32 score, double remainingTime, const Font& font)
	{
		// Draw the score
		font(U"SCORE: {}"_fmt(score)).draw(30, Vec2{ 20, 20 });

		// Draw the remaining time
		font(U"TIME: {:.0f}"_fmt(remainingTime)).draw(30, Arg::topRight(780, 20));

		if (remainingTime <= 0.0)
		{
			font(U"TIME'S UP!").drawAt(80, Vec2{ 400, 270 }, ColorF{ 0.3 });
		}
	}

	void Main()
	{
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Array of item textures
		const Array<Texture> itemTextures =
		{
			Texture{ U"🍬"_emoji },
			Texture{ U"🍰"_emoji },
		};

		Player player;

		// Array of items
		Array<Item> items;
		items << Item{ Circle{ 200, 200, 30 }, 0 };
		items << Item{ Circle{ 600, 100, 30 }, 1 };

		// Item spawn interval (seconds)
		const double spawnInterval = 0.8;

		// Accumulated time (seconds)
		double accumulatedTime = 0.0;

		// Score
		int32 score = 0;

		// Remaining time (seconds)
		double remainingTime = 20.0;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// Decrease remaining time
			remainingTime = Max((remainingTime - deltaTime), 0.0);

			// If the game is still in progress
			if (0.0 < remainingTime)
			{
				// Increase accumulated time
				accumulatedTime += deltaTime;

				// If accumulated time exceeds the interval
				if (spawnInterval < accumulatedTime)
				{
					// Add a new item
					items << Item{ Circle{ Random(30.0, 770.0), -30, 30 }, Random(0, 1) };

					// Reduce accumulated time by the interval
					accumulatedTime -= spawnInterval;
				}

				// Update the player's state
				player.update(deltaTime);

				// Update all items
				UpdateItems(items, deltaTime, player, score);
			}
			else
			{
				items.clear();
			}

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Draw the player
			player.draw();

			// Draw all items
			DrawItems(items, itemTextures);

			// Draw UI
			DrawUI(score, remainingTime, font);
		}
	}
	```