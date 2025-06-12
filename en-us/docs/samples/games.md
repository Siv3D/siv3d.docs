# Game Samples

## 1. Block breaking game

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/1.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Size of a single block | Size of a single block
		constexpr Size BrickSize{ 40, 20 };

		// Ball speed (pixels / second) | Ball speed (pixels / second)
		constexpr double BallSpeedPerSec = 480.0;

		// Ball velocity | Ball velocity
		Vec2 ballVelocity{ 0, -BallSpeedPerSec };

		// Ball | Ball
		Circle ball{ 400, 400, 8 };

		// Array of bricks | Array of bricks
		Array<Rect> bricks;

		for (int32 y = 0; y < 5; ++y)
		{
			for (int32 x = 0; x < (Scene::Width() / BrickSize.x); ++x)
			{
				bricks << Rect{ (x * BrickSize.x), (60 + y * BrickSize.y), BrickSize };
			}
		}

		while (System::Update())
		{
			// Paddle | Paddle
			const Rect paddle{ Arg::center(Cursor::Pos().x, 500), 60, 10 };

			// Move the ball | Move the ball
			ball.moveBy(ballVelocity * Scene::DeltaTime());

			// Check bricks in sequence | Check bricks in sequence
			for (auto it = bricks.begin(); it != bricks.end(); ++it)
			{
				// If block and ball intersect | If block and ball intersect
				if (it->intersects(ball))
				{
					// If ball intersects with top or bottom of the block | If ball intersects with top or bottom of the block
					if (it->bottom().intersects(ball) || it->top().intersects(ball))
					{
						// Reverse the sign of the Y component of the ball's velocity | Reverse the sign of the Y component of the ball's velocity
						ballVelocity.y *= -1;
					}
					else // If intersecting with left or right side of the block
					{
						// Reverse the sign of the X component of the ball's velocity | Reverse the sign of the X component of the ball's velocity
						ballVelocity.x *= -1;
					}

					// Remove the block from the array (the iterator becomes invalid) | Remove the block from the array (the iterator becomes invalid)
					bricks.erase(it);

					// Do not check any more | Do not check any more
					break;
				}
			}

			// If the ball hits the ceiling | If the ball hits the ceiling
			if ((ball.y < 0) && (ballVelocity.y < 0))
			{
				// Reverse the sign of the Y component of the ball's velocity | Reverse the sign of the Y component of the ball's velocity
				ballVelocity.y *= -1;
			}

			// If the ball hits the left or right wall | If the ball hits the left or right wall
			if (((ball.x < 0) && (ballVelocity.x < 0))
				|| ((Scene::Width() < ball.x) && (0 < ballVelocity.x)))
			{
				// Reverse the sign of the X component of the ball's velocity | Reverse the sign of the X component of the ball's velocity
				ballVelocity.x *= -1;
			}

			// If the ball hits the left or right wall | If the ball hits the left or right wall
			if ((0 < ballVelocity.y) && paddle.intersects(ball))
			{
				// Change the direction (velocity vector) of the ball depending on the distance from the center of the paddle | Change the direction (velocity vector) of the ball depending on the distance from the center of the paddle
				ballVelocity = Vec2{ (ball.x - paddle.center().x) * 10, -ballVelocity.y }.setLength(BallSpeedPerSec);
			}

			// Draw all the bricks | Draw all the bricks
			for (const auto& brick : bricks)
			{
				// Change the color of the brick depending on the Y coordinate | Change the color of the brick depending on the Y coordinate
				brick.stretched(-1).draw(HSV{ brick.y - 40 });
			}

			// Hide the mouse cursor | Hide the mouse cursor
			Cursor::RequestStyle(CursorStyle::Hidden);

			// Draw the ball | Draw the ball
			ball.draw();

			// Draw the paddle | Draw the paddle
			paddle.rounded(3).draw();
		}
	}
	```

## 2. Collecting falling items game

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/10.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Player class
	struct Player
	{
		Circle circle{ 400, 530, 30 };

		Texture texture{ U"😃"_emoji };

		// Function to update player state
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

			// Keep player within screen bounds
			circle.x = Clamp(circle.x, 30.0, 770.0);
		}

		// Function to draw player
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
			// Move item downward
			circle.y += (deltaTime * 200.0);
		}

		// Function to draw item
		void draw(const Array<Texture>& itemTextures) const
		{
			// Draw texture based on item type
			itemTextures[type].scaled(0.5).rotated(circle.y * 0.3_deg).drawAt(circle.center);
		}
	};

	void UpdateItems(Array<Item>& items, double deltaTime, const Player& player, int32& score)
	{
		// Update all item states
		for (auto& item : items)
		{
			item.update(deltaTime);
		}

		// For each item
		for (auto it = items.begin(); it != items.end();)
		{
			// If player and item intersect
			if (player.circle.intersects(it->circle))
			{
				// Add score (candy: 10 points, cake: 50 points)
				score += ((it->type == 0) ? 10 : 50);

				// Remove item
				it = items.erase(it);
			}
			else
			{
				++it;
			}
		}

		// Remove items that fell to the ground
		items.remove_if([](const Item& item) { return (580 < item.circle.y); });
	}

	// Function to draw background
	void DrawBackground()
	{
		// Draw sky
		Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

		// Draw ground
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
		// Draw score
		font(U"SCORE: {}"_fmt(score)).draw(30, Vec2{ 20, 20 });

		// Draw remaining time
		font(U"TIME: {:.0f}"_fmt(remainingTime)).draw(30, Arg::topRight(780, 20));

		if (remainingTime <= 0.0)
		{
			font(U"TIME'S UP!").drawAt(80, Vec2{ 400, 270 }, ColorF{ 0.3 });
		}
	}

	void Main()
	{
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Item texture array
		const Array<Texture> itemTextures =
		{
			Texture{ U"🍬"_emoji },
			Texture{ U"🍰"_emoji },
		};

		Player player;

		// Item array
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

			// If game is still running
			if (0.0 < remainingTime)
			{
				// Increase accumulated time
				accumulatedTime += deltaTime;

				// If accumulated time exceeds interval
				if (spawnInterval < accumulatedTime)
				{
					// Add new item
					items << Item{ Circle{ Random(30.0, 770.0), -30, 30 }, Random(0, 1) };

					// Reduce accumulated time by interval
					accumulatedTime -= spawnInterval;
				}

				// Update player state
				player.update(deltaTime);

				// Update all item states
				UpdateItems(items, deltaTime, player, score);
			}
			else
			{
				items.clear();
			}

			/////////////////////////////////
			//
			//	Drawing
			//
			/////////////////////////////////

			// Draw background
			DrawBackground();

			// Draw player
			player.draw();

			// Draw all items
			DrawItems(items, itemTextures);

			// Draw UI
			DrawUI(score, remainingTime, font);
		}
	}
	```

## 3. 15 puzzle

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/3.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Check if two pieces are adjacent
	bool Swappable(int32 a, int32 b)
	{
		return ((a / 4 == b / 4) && (AbsDiff(a, b) == 1))
			|| ((a % 4 == b % 4) && (AbsDiff(a, b) == 4));
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

		// Piece size
		constexpr int32 CellSize = 100;

		// Position
		constexpr Point Offset{ 60, 40 };

		// Select image from dialog
		const Image image = Dialog::OpenImage();

		// Crop to square
		const Texture texture{ image.squareClipped(), TextureDesc::Mipped };

		// Shuffle puzzle with random operations
		Array<int32> pieces = Range(0, 15);
		{
			// Empty space position
			int32 blankPos = 15;

			for (int32 i = 0; i < 1000; ++i)
			{
				const int32 to = (blankPos + Sample({ -4, -1, 1, 4 }));

				if (InRange(to, 0, 15) && Swappable(blankPos, to))
				{
					std::swap(pieces[blankPos], pieces[to]);
					blankPos = to;
				}
			}
		}

		// Currently grabbed piece number
		Optional<int32> grabbed;

		while (System::Update())
		{
			Rect{ Offset, (CellSize * 4) }
				.drawShadow(Vec2{ 0, 2 }, 12, 8)
				.draw(ColorF{ 0.25 })
				.drawFrame(0, 8, ColorF{ 0.3, 0.5, 0.7 });

			if (not MouseL.pressed())
			{
				grabbed.reset();
			}

			for (int32 i = 0; i < 16; ++i)
			{
				const int32 pieceID = pieces[i];
				const Rect rect = Rect{ (CellSize * (i % 4)), (CellSize * (i / 4)), CellSize }.movedBy(Offset);

				if (pieceID == 15)
				{
					if (grabbed && rect.mouseOver() && Swappable(i, grabbed.value()))
					{
						std::swap(pieces[i], pieces[grabbed.value()]);
						grabbed = i;
					}

					continue;
				}

				if (rect.leftClicked())
				{
					grabbed = i;
				}

				rect(texture.uv((pieceID % 4 * 0.25), (pieceID / 4 * 0.25), 0.25, 0.25))
					.draw()
					.drawFrame(1, 0, ColorF{ 1.0, 0.75 });

				if (grabbed == i)
				{
					rect.draw(ColorF{ 1.0, 0.5, 0.0, 0.3 });
				}

				if (rect.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);
				}
			}

			// Draw reference image
			texture.resized(180)
				.draw((Offset.x + CellSize * 4 + 40), Offset.y)
				.drawFrame(0, 4, ColorF{ 0.3, 0.5, 0.7 });
		}
	}
	```


## 4. Number chain

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/4.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	struct Bubble
	{
		// Bubble circle radius
		static constexpr int32 Radius = 30;

		// Bubble circle
		Circle circle;

		// Bubble index
		int32 index;

		// True if connected
		bool connected = false;

		void draw(const Font& font) const
		{
			if (connected)
			{
				circle.drawShadow(Vec2{ 1, 2 }, 10, 3).draw()
					.drawFrame(2, 0, ColorF{ 0.3, 0.6, 1.0 });
			}
			else
			{
				circle.draw();
			}

			font(index + 1).drawAt(36, circle.center, ColorF{ 0.25 });
		}
	};

	// Check if bubbles overlap each other
	bool CheckBubbles(const Array<Bubble>& bubbles)
	{
		for (size_t i = 0; i < bubbles.size(); ++i)
		{
			for (size_t k = (i + 1); k < bubbles.size(); ++k)
			{
				// Overlapping
				if (bubbles[i].circle.stretched(5)
					.intersects(bubbles[k].circle.stretched(5)))
				{
					return false;
				}
			}
		}

		return true;
	}

	// Generate specified number of bubbles without overlap
	Array<Bubble> MakeBubbles(int32 count)
	{
		Array<Bubble> bubbles(count);

		do
		{
			for (int32 i = 0; i < count; ++i)
			{
				// Bubble index
				bubbles[i].index = i;

				// Bubble circle
				bubbles[i].circle.set(RandomVec2(Circle{ Scene::Center(), (Scene::Height() / 2 - Bubble::Radius) }), Bubble::Radius);
			}
		} while (not CheckBubbles(bubbles));

		return bubbles;
	}

	// Number of bubbles at specified level
	constexpr int32 GetBubbleCount(int32 level)
	{
		return Min(level, 15);
	}

	// Time limit at specified level (seconds)
	constexpr Duration GetTime(int32 level)
	{
		return Duration{ (level <= 15) ? 8.0 : 8.0 - Min((level - 15) * 0.05, 2.0) };
	}

	void Main()
	{
		Scene::SetBackground(Palette::White);

		const Font font{ FontMethod::MSDF, 48, Typeface::Medium };

		Effect effect;

		// Create sound effects
		const Array<PianoKey> keys = { PianoKey::C5,  PianoKey::D5, PianoKey::E5, PianoKey::F5, PianoKey::G5,
			PianoKey::A5, PianoKey::B5, PianoKey::C6, PianoKey::D6, PianoKey::E6,
			PianoKey::F6, PianoKey::G6, PianoKey::A6, PianoKey::B6, PianoKey::C7 };
		const Array<Audio> sounds = keys.map([](auto k) { return Audio{ GMInstrument::Glockenspiel, k, 0.3s }; });

		// High score
		int32 highScore = 0;

		// Current level
		int32 level = 1;

		// Connection count
		int32 connected = 0;

		// Remaining time timer
		Timer timer{ GetTime(level), StartImmediately::Yes };

		// Bubbles
		Array<Bubble> bubbles = MakeBubbles(GetBubbleCount(level));

		while (System::Update())
		{
			const double delta = Scene::DeltaTime();

			for (auto& bubble : bubbles)
			{
				if ((bubble.index == connected)
					&& (not bubble.connected)
					&& bubble.circle.stretched(10).mouseOver())
				{
					// Mark as connected
					bubble.connected = true;

					// Increase connection count
					++connected;

					// Add effect
					effect.add([pos = Cursor::Pos()](double t)
					{
						Circle{ pos, (Bubble::Radius + t * 200) }.drawFrame(2, 0, ColorF{ 0.2, 0.5, 1.0, (1.0 - t * 2.5) });
						return (t < 0.4);
					});

					// Play sound based on bubble number
					sounds[bubble.index].playOneShot(0.8);
				}

				// Move bubbles around circumference
				bubble.circle.center = OffsetCircular{ Scene::Center(), bubble.circle.center }
					.rotate((IsEven(bubble.index) ? 20_deg : -20_deg) * delta);
			}

			// When all bubbles are connected or time runs out
			if (const bool failed = timer.reachedZero();
				(connected == GetBubbleCount(level)) || failed)
			{
				// Update level
				level = (failed ? 1 : ++level);

				// Reset connection count
				connected = 0;

				// Reset time limit
				timer = Timer{ GetTime(level), StartImmediately::Yes };

				// Regenerate bubbles
				bubbles = MakeBubbles(GetBubbleCount(level));

				// Update high score
				highScore = Max(highScore, level);

				// Update title
				Window::SetTitle(U"Level {} (High score: {})"_fmt(level, highScore));
			}

			// Draw background representing time limit
			RectF{ Scene::Width(), (Scene::Height() * timer.progress0_1()) }.draw(HSV{ (level * 30), 0.3, 0.9 });

			// Draw lines connecting bubbles
			for (int32 i = 0; i < (connected - 1); ++i)
			{
				Line{ bubbles[i].circle.center, bubbles[i + 1].circle.center }.draw(3, Palette::Orange);
			}

			// Draw bubbles
			for (const auto& bubble : bubbles)
			{
				bubble.draw(font);
			}

			// Draw effects
			effect.update();
		}
	}
	```


## 5. Typing game

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/5.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// List of problem texts
		const Array<String> texts =
		{
			U"Practice makes perfect.",
			U"Don't cry over spilt milk.",
			U"Faith will move mountains.",
			U"Nothing ventured, nothing gained.",
			U"Bad news travels fast.",
		};

		// Randomly select problem text
		String target = texts.choice();

		// Input string
		String input;

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		while (System::Update())
		{
			// Text input (TextInputMode::DenyControl: don't accept enter, tab, backspace)
			TextInput::UpdateText(input, TextInputMode::DenyControl);

			// Delete incorrect input
			while (not target.starts_with(input))
			{
				input.pop_back();
			}

			// Move to next problem if matched
			if (input == target)
			{
				// Randomly select problem text
				target = texts.choice();

				// Clear input string	
				input.clear();
			}

			// Draw problem text
			font(target).draw(40, Vec2{ 40, 80 }, ColorF{ 0.98 });

			// Draw input text
			font(input).draw(40, Vec2{ 40, 80 }, ColorF{ 0.12 });
		}
	}
	```


## 6. Emoji tower

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/6.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.2, 0.7, 1.0 });

		// Appearing emojis
		const Array<String> emojis = { U"🐘", U"🐧", U"🐐", U"🐤" };

		Array<MultiPolygon> polygons;

		Array<Texture> textures;

		for (const auto& emoji : emojis)
		{
			// Create shape information from emoji image
			polygons << Emoji::CreateImage(emoji).alphaToPolygonsCentered().simplified(2.0);

			// Create texture from emoji image
			textures << Texture{ Emoji{ emoji } };
		}

		// 2D physics simulation step (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		// [_] Ground
		const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -300, 0, 300, 0 });

		// Animal bodies
		Array<P2Body> bodies;

		// Correspondence table between body ID and emoji index
		HashTable<P2BodyID, size_t> table;

		// Emoji index
		size_t index = Random(polygons.size() - 1);

		// 2D camera
		Camera2D camera{ Vec2{ 0, -200 } };

		while (System::Update())
		{
			accumulatedTime += Scene::DeltaTime();

			while (StepTime <= accumulatedTime)
			{
				// Update 2D physics world
				world.update(StepTime);

				accumulatedTime -= StepTime;
			}

			// Remove bodies that fell below ground
			for (auto it = bodies.begin(); it != bodies.end();)
			{
				if (100 < it->getPos().y)
				{
					// Also remove from correspondence table
					table.erase(it->id());

					it = bodies.erase(it);
				}
				else
				{
					++it;
				}
			}

			// Update 2D camera
			camera.update();
			{
				// Create Transformer2D from 2D camera
				const auto t = camera.createTransformer();

				// If left clicked
				if (MouseL.down())
				{
					// Add body
					bodies << world.createPolygons(P2Dynamic, Cursor::PosF(), polygons[index], P2Material{ 0.1, 0.0, 1.0 });

					// Add body ID and emoji index pair to correspondence table
					table.emplace(bodies.back().id(), std::exchange(index, Random(polygons.size() - 1)));
				}

				// Draw all bodies
				for (const auto& body : bodies)
				{
					textures[table[body.id()]].rotated(body.getAngle()).drawAt(body.getPos());
				}

				// Draw ground
				ground.draw(Palette::Green);

				// Draw currently controllable emoji
				textures[index].drawAt(Cursor::PosF(), ColorF{ 1.0, (0.5 + Periodic::Sine0_1(1s) * 0.5) });
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);
		}
	}
	```


## 7. Shooting game

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/7.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Function to create random enemy position
	Vec2 GenerateEnemy()
	{
		return RandomVec2({ 50, 750 }, -20);
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.1, 0.2, 0.7 });

		const Font font{ FontMethod::MSDF, 48 };

		// Player texture
		const Texture playerTexture{ U"🤖"_emoji };
		// Enemy texture
		const Texture enemyTexture{ U"👾"_emoji };

		// Player
		Vec2 playerPos{ 400, 500 };
		// Enemy
		Array<Vec2> enemies = { GenerateEnemy() };

		// Player shots
		Array<Vec2> playerBullets;
		// Enemy shots
		Array<Vec2> enemyBullets;

		// Player speed
		constexpr double PlayerSpeed = 550.0;
		// Player shot speed
		constexpr double PlayerBulletSpeed = 500.0;
		// Enemy speed
		constexpr double EnemySpeed = 100.0;
		// Enemy shot speed
		constexpr double EnemyBulletSpeed = 300.0;

		// Initial enemy spawn interval (seconds)
		constexpr double InitialEnemySpawnInterval = 2.0;
		// Enemy spawn interval (seconds)
		double enemySpawnTime = InitialEnemySpawnInterval;
		// Enemy spawn accumulated time (seconds)
		double enemyAccumulatedTime = 0.0;

		// Player shot cooltime (seconds)
		constexpr double PlayerShotCoolTime = 0.1;
		// Player shot cooltime timer (seconds)
		double playerShotTimer = 0.0;

		// Enemy shot cooltime (seconds)
		constexpr double EnemyShotCoolTime = 0.9;
		// Enemy shot cooltime timer (seconds)
		double enemyShotTimer = 0.0;

		Effect effect;

		// High score
		int32 highScore = 0;
		// Current score
		int32 score = 0;

		while (System::Update())
		{
			// Game over check
			bool gameover = false;

			const double deltaTime = Scene::DeltaTime();
			enemyAccumulatedTime += deltaTime;
			playerShotTimer = Min((playerShotTimer + deltaTime), PlayerShotCoolTime);
			enemyShotTimer += deltaTime;

			// Generate enemies
			while (enemySpawnTime <= enemyAccumulatedTime)
			{
				enemyAccumulatedTime -= enemySpawnTime;
				enemySpawnTime = Max(enemySpawnTime * 0.95, 0.3);
				enemies << GenerateEnemy();
			}

			// Player movement
			const Vec2 move = Vec2{ (KeyRight.pressed() - KeyLeft.pressed()), (KeyDown.pressed() - KeyUp.pressed()) }
				.setLength(deltaTime * PlayerSpeed * (KeyShift.pressed() ? 0.5 : 1.0));
			playerPos.moveBy(move).clamp(Scene::Rect());

			// Player shot firing
			if (PlayerShotCoolTime <= playerShotTimer)
			{
				playerShotTimer -= PlayerShotCoolTime;
				playerBullets << playerPos.movedBy(0, -50);
			}

			// Move player shots
			for (auto& playerBullet : playerBullets)
			{
				playerBullet.y += (deltaTime * -PlayerBulletSpeed);
			}
			// Remove player shots that went off screen
			playerBullets.remove_if([](const Vec2& b) { return (b.y < -40); });

			// Move enemies
			for (auto& enemy : enemies)
			{
				enemy.y += (deltaTime * EnemySpeed);
			}
			// Remove enemies that went off screen
			enemies.remove_if([&](const Vec2& e)
			{
				if (700 < e.y)
				{
					// Game over if enemy goes off screen
					gameover = true;
					return true;
				}
				else
				{
					return false;
				}
			});

			// Fire enemy shots
			if (EnemyShotCoolTime <= enemyShotTimer)
			{
				enemyShotTimer -= EnemyShotCoolTime;

				for (const auto& enemy : enemies)
				{
					enemyBullets << enemy;
				}
			}

			// Move enemy shots
			for (auto& enemyBullet : enemyBullets)
			{
				enemyBullet.y += (deltaTime * EnemyBulletSpeed);
			}
			// Remove player shots that went off screen
			enemyBullets.remove_if([](const Vec2& b) {return (700 < b.y); });

			////////////////////////////////
			//
			//	Hit detection
			//
			////////////////////////////////

			// Enemy vs player shot
			for (auto itEnemy = enemies.begin(); itEnemy != enemies.end();)
			{
				const Circle enemyCircle{ *itEnemy, 40 };
				bool skip = false;

				for (auto itBullet = playerBullets.begin(); itBullet != playerBullets.end();)
				{
					if (enemyCircle.intersects(*itBullet))
					{
						// Add explosion effect
						effect.add([pos = *itEnemy](double t)
						{
							const double t2 = ((0.5 - t) * 2.0);
							Circle{ pos, (10 + t * 280) }.drawFrame((20 * t2), ColorF{ 1.0, (t2 * 0.5) });
							return (t < 0.5);
						});

						itEnemy = enemies.erase(itEnemy);
						playerBullets.erase(itBullet);
						++score;
						skip = true;
						break;
					}

					++itBullet;
				}

				if (skip)
				{
					continue;
				}

				++itEnemy;
			}

			// Enemy shot vs player
			for (const auto& enemyBullet : enemyBullets)
			{
				// If enemy shot approaches within 20 pixels of playerPos
				if (enemyBullet.distanceFrom(playerPos) <= 20)
				{
					// Game over
					gameover = true;
					break;
				}
			}

			// Reset if game over
			if (gameover)
			{
				playerPos = Vec2{ 400, 500 };
				enemies.clear();
				playerBullets.clear();
				enemyBullets.clear();
				enemySpawnTime = InitialEnemySpawnInterval;
				highScore = Max(highScore, score);
				score = 0;
			}

			////////////////////////////////
			//
			//	Drawing
			//
			////////////////////////////////

			// Draw background animation
			for (int32 i = 0; i < 12; ++i)
			{
				const double a = Periodic::Sine0_1(2s, Scene::Time() - (2.0 / 12 * i));
				Rect{ 0, (i * 50), 800, 50 }.draw(ColorF(1.0, a * 0.2));
			}

			// Draw player
			playerTexture.resized(80).flipped().drawAt(playerPos);

			// Draw player shots
			for (const auto& playerBullet : playerBullets)
			{
				Circle{ playerBullet, 8 }.draw(Palette::Orange);
			}

			// Draw enemies
			for (const auto& enemy : enemies)
			{
				enemyTexture.resized(60).drawAt(enemy);
			}

			// Draw enemy shots
			for (const auto& enemyBullet : enemyBullets)
			{
				Circle{ enemyBullet, 4 }.draw(Palette::White);
			}

			// Draw explosion effects
			effect.update();

			// Draw score
			font(U"{} [{}]"_fmt(score, highScore)).draw(30, Arg::bottomRight(780, 580));
		}
	}
	```


## 8. Pinball

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/8.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Function to create frame vertex list
	LineString CreateFrame(const Vec2& leftAnchor, const Vec2& rightAnchor)
	{
		Array<Vec2> points = { leftAnchor, Vec2{ -70, -20 } };
		for (int32 i = -30; i <= 30; ++i)
		{
			points << OffsetCircular(Vec2{ 0.0, -120 }, 70, (i * 3_deg));
		}
		points << Vec2{ 70, -20 } << rightAnchor;
		return LineString{ points };
	}

	// Function to determine color based on contact
	ColorF GetColor(const P2Body& body, const HashSet<P2BodyID>& list)
	{
		return list.contains(body.id()) ? Palette::White : Palette::Orange;
	}

	void Main()
	{
		// Set background color
		Scene::SetBackground(ColorF(0.2, 0.3, 0.4));

		// 2D physics simulation step (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// Physics world
		P2World world{ 60.0 };

		// Left and right flipper axis coordinates
		constexpr Vec2 LeftFlipperAnchor{ -25, 10 }, RightFlipperAnchor{ 25, 10 };

		// Fixed frames
		Array<P2Body> frames;
		{
			// Perimeter
			frames << world.createLineString(P2Static, Vec2{ 0, 0 }, CreateFrame(LeftFlipperAnchor, RightFlipperAnchor));
			// Top left (
			frames << world.createLineString(P2Static, Vec2{ 0, 0 }, LineString{ Range(-25, -10).map([=](int32 i) { return OffsetCircular(Vec2{ 0.0, -120 }, 55, (i * 3_deg)).toVec2(); }) });
			// Top right )
			frames << world.createLineString(P2Static, Vec2{ 0, 0 }, LineString{ Range(10, 25).map([=](int32 i) { return OffsetCircular(Vec2{ 0.0, -120 }, 55, (i * 3_deg)).toVec2(); }) });
		}

		// Bumpers
		Array<P2Body> bumpers;
		{
			// ● x3
			{
				const P2Material material{ .restitution = 1.0, .restitutionThreshold = 0.1 };
				bumpers << world.createCircle(P2Static, Vec2{ 0, -170 }, 5, material);
				bumpers << world.createCircle(P2Static, Vec2{ -20, -150 }, 5, material);
				bumpers << world.createCircle(P2Static, Vec2{ 20, -150 }, 5, material);
			}
			// ▲ x2
			{
				const P2Material material{ .restitution = 0.8, .restitutionThreshold = 0.1 };
				bumpers << world.createTriangle(P2Static, Vec2{ 0, 0 }, Triangle{ -60, -50, -40, -15, -60, -30 }, material);
				bumpers << world.createTriangle(P2Static, Vec2{ 0, 0 }, Triangle{ 60, -50, 60, -30, 40, -15 }, material);
			}
		}

		const P2Material softMaterial{ .density = 0.1, .restitution = 0.0 };

		// Left flipper
		P2Body leftFlipper = world.createRect(P2Dynamic, LeftFlipperAnchor, RectF{ 0, 0.4, 21, 4.5 }, softMaterial);
		// Left flipper joint
		const P2PivotJoint leftJoint = world.createPivotJoint(frames[0], leftFlipper, LeftFlipperAnchor).setLimits(-20_deg, 25_deg).setLimitsEnabled(true);

		// Right flipper
		P2Body rightFlipper = world.createRect(P2Dynamic, RightFlipperAnchor, RectF{ -21, 0.4, 21, 4.5 }, softMaterial);
		// Right flipper joint
		const P2PivotJoint rightJoint = world.createPivotJoint(frames[0], rightFlipper, RightFlipperAnchor).setLimits(-25_deg, 20_deg).setLimitsEnabled(true);

		// Spinner ＋
		const P2Body spinner = world.createRect(P2Dynamic, Vec2{ -58, -120 }, SizeF{ 20, 1 }, softMaterial).addRect(RectF{ Arg::center(0, 0), 1, 20 }, P2Material{ 0.01, 0.0 });
		// Spinner joint
		P2PivotJoint spinnerJoint = world.createPivotJoint(frames[0], spinner, Vec2{ -58, -120 }).setMaxMotorTorque(0.05).setMotorSpeed(0).setMotorEnabled(true);

		// Windmill |
		frames << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -40, -60, -40, -40 });
		// Windmill wing ／
		const P2Body windmillWing = world.createRect(P2Dynamic, Vec2{ -40, -60 }, SizeF{ 30, 2 }, P2Material{ 0.1, 0.8 });
		// Windmill joint
		const P2PivotJoint windmillJoint = world.createPivotJoint(frames.back(), windmillWing, Vec2{ -40, -60 }).setMotorSpeed(240_deg).setMaxMotorTorque(10000.0).setMotorEnabled(true);

		// Pendulum axis
		const P2Body pendulumBase = world.createPlaceholder(P2Static, Vec2{ 0, -190 });
		// Pendulum ●
		P2Body pendulum = world.createCircle(P2Dynamic, Vec2{ 0, -120 }, 4, P2Material{ 0.1, 1.0 });
		// Pendulum joint
		const P2DistanceJoint pendulumJoint = world.createDistanceJoint(pendulumBase, Vec2{ 0, -190 }, pendulum, Vec2{ 0, -120 }, 70);

		// Elevator top ●
		const P2Body elevatorA = world.createCircle(P2Static, Vec2{ 40, -100 }, 3);
		// Elevator floor －
		const P2Body elevatorB = world.createRect(P2Dynamic, Vec2{ 40, -100 }, SizeF{ 20, 2 });
		// Elevator joint
		P2SliderJoint elevatorSliderJoint = world.createSliderJoint(elevatorA, elevatorB, Vec2{ 40, -100 }, Vec2::Down()).setLimits(5, 50).setLimitEnabled(true).setMaxMotorForce(10000).setMotorSpeed(-100);

		// Ball 〇
		const P2Body ball = world.createCircle(P2Dynamic, Vec2{ -40, -120 }, 4, P2Material{ 0.05, 0.0 });
		const P2BodyID ballID = ball.id();

		// Elevator animation stopwatch
		Stopwatch sliderStopwatch{ StartImmediately::Yes };

		// 2D camera
		const Camera2D camera{ Vec2{ 0, -80 }, 2.4 };

		while (System::Update())
		{
			////////////////////////////////
			//
			//	Update
			//
			////////////////////////////////

			if (4s < sliderStopwatch)
			{
				// Stop elevator lifting
				elevatorSliderJoint.setMotorEnabled(false);
				sliderStopwatch.restart();
			}
			else if (2s < sliderStopwatch)
			{
				// Elevator lifting
				elevatorSliderJoint.setMotorEnabled(true);
			}

			// IDs of bodies in contact with ball
			HashSet<P2BodyID> collidedIDs;

			// Update physics world
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Resistance to suppress pendulum oscillation
				pendulum.applyForce(Vec2{ (pendulum.getVelocity().x < 0.0) ? 0.0001 : -0.0001, 0.0 });

				// Left flipper control
				leftFlipper.applyTorque(KeyLeft.pressed() ? -80 : 40);

				// Right flipper control
				rightFlipper.applyTorque(KeyRight.pressed() ? 80 : -40);

				world.update(StepTime);

				// Store IDs of bodies in contact with ball
				for (auto&& [pair, collision] : world.getCollisions())
				{
					if (pair.a == ballID)
					{
						collidedIDs.emplace(pair.b);
					}
					else if (pair.b == ballID)
					{
						collidedIDs.emplace(pair.a);
					}
				}
			}

			////////////////////////////////
			//
			//	Drawing
			//
			////////////////////////////////

			// Drawing Transformer2D
			const auto transformer = camera.createTransformer();

			// Draw frames
			for (const auto& frame : frames)
			{
				frame.draw(Palette::Skyblue);
			}

			// Draw spinner
			spinner.draw(GetColor(spinner, collidedIDs));

			// Draw bumpers
			for (const auto& bumper : bumpers)
			{
				bumper.draw(GetColor(bumper, collidedIDs));
			}

			// Draw windmill
			windmillWing.draw(GetColor(windmillWing, collidedIDs));

			// Draw pendulum
			pendulum.draw(GetColor(pendulum, collidedIDs));

			// Draw elevator
			elevatorA.draw(GetColor(elevatorA, collidedIDs));
			elevatorB.draw(GetColor(elevatorB, collidedIDs));

			// Draw ball
			ball.draw(Palette::White);

			// Draw flippers
			leftFlipper.draw(Palette::Orange);
			rightFlipper.draw(Palette::Orange);

			// Visualize joints
			leftJoint.draw(Palette::Red);
			rightJoint.draw(Palette::Red);
			spinnerJoint.draw(Palette::Red);
			windmillJoint.draw(Palette::Red);
			pendulumJoint.draw(Palette::Red);
			elevatorSliderJoint.draw(Palette::Red);
		}
	}
	```


## 9. Cookie clicker

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/9.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Game save data
	struct SaveData
	{
		double cookies;

		Array<int32> itemCounts;

		// Define member function for serialization support
		template <class Archive>
		void SIV3D_SERIALIZE(Archive& archive)
		{
			archive(cookies, itemCounts);
		}
	};

	/// @brief Item button
	/// @param rect Button area
	/// @param texture Button emoji
	/// @param font Font for text drawing
	/// @param name Item name
	/// @param desc Item description
	/// @param count Item possession count
	/// @param enabled Whether button can be pressed
	/// @return true if button was pressed, false otherwise
	bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
	{
		if (enabled)
		{
			rect.draw(ColorF{ 0.3, 0.5, 0.9, 0.8 });

			rect.drawFrame(2, 2, ColorF{ 0.5, 0.7, 1.0 });

			if (rect.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}
		}
		else
		{
			rect.draw(ColorF{ 0.0, 0.4 });

			rect.drawFrame(2, 2, ColorF{ 0.5 });
		}

		texture.scaled(0.5).drawAt(rect.x + 50, rect.y + 50);

		font(name).draw(30, rect.x + 100, rect.y + 15, Palette::White);

		font(desc).draw(18, rect.x + 102, rect.y + 60, Palette::White);

		font(count).draw(50, Arg::rightCenter((rect.x + rect.w - 20), (rect.y + 50)), Palette::White);

		return (enabled && rect.leftClicked());
	}

	// Cookie falling effect
	struct CookieBackgroundEffect : IEffect
	{
		// Initial position
		Vec2 m_start;

		// Rotation angle
		double m_angle;

		// Texture
		Texture m_texture;

		CookieBackgroundEffect(const Vec2& start, const Texture& texture)
			: m_start{ start }
			, m_angle{ Random(2_pi) }
			, m_texture{ texture } {}

		bool update(double t) override
		{
			const Vec2 pos = m_start + 0.5 * t * t * Vec2{ 0, 120 };

			m_texture.scaled(0.3).rotated(m_angle).drawAt(pos, ColorF{ 1.0, (1.0 - t / 3.0) });

			return (t < 3.0);
		}
	};

	// Cookie dancing effect
	struct CookieEffect : IEffect
	{
		// Initial position
		Vec2 m_start;

		// Initial velocity
		Vec2 m_velocity;

		// Scale factor
		double m_scale;

		// Rotation angle
		double m_angle;

		// Texture
		Texture m_texture;

		CookieEffect(const Vec2& start, const Texture& texture)
			: m_start{ start }
			, m_velocity{ Circular{ 80, Random(-40_deg, 40_deg) } }
			, m_scale{ Random(0.2, 0.3) }
			, m_angle{ Random(2_pi) }
			, m_texture{ texture } {}

		bool update(double t) override
		{
			const Vec2 pos = m_start
				+ m_velocity * t + 0.5 * t * t * Vec2{ 0, 120 };

			m_texture.scaled(m_scale).rotated(m_angle).drawAt(pos, ColorF{ 1.0, (1.0 - t) });

			return (t < 1.0);
		}
	};

	// "+1" rising effect
	struct PlusOneEffect : IEffect
	{
		// Initial position
		Vec2 m_start;

		// Font
		Font m_font;

		PlusOneEffect(const Vec2& start, const Font& font)
			: m_start{ start }
			, m_font{ font } {}

		bool update(double t) override
		{
			m_font(U"+1").drawAt(24, m_start.movedBy(0, t * -120), ColorF{ 1.0, (1.0 - t) });

			return (t < 1.0);
		}
	};

	// Item data
	struct Item
	{
		// Item emoji
		Texture emoji;

		// Item name
		String name;

		// Cost when purchasing item for the first time
		int32 initialCost;

		// Item CPS
		int32 cps;

		// Returns purchase cost when owning count items
		int32 getCost(int32 count) const
		{
			return initialCost * (count + 1);
		}
	};

	// Cookie spring
	class CookieSpring
	{
	public:

		void update(double deltaTime, bool pressed)
		{
			// Add to spring accumulated time
			m_accumulatedTime += deltaTime;

			while (0.005 <= m_accumulatedTime)
			{
				// Spring force (direction to cancel change)
				double force = (-0.02 * m_x);

				// Force when screen is pressed
				if (pressed)
				{
					force += 0.004;
				}

				// Apply force to velocity (also dampen)
				m_velocity = (m_velocity + force) * 0.92;

				// Reflect in position
				m_x += m_velocity;

				m_accumulatedTime -= 0.005;
			}
		}

		double get() const
		{
			return m_x;
		}

	private:

		// Spring extension
		double m_x = 0.0;

		// Spring velocity
		double m_velocity = 0.0;

		// Spring accumulated time
		double m_accumulatedTime = 0.0;
	};

	// Function to draw cookie halo
	void DrawHalo(const Vec2& center)
	{
		for (int32 i = 0; i < 4; ++i)
		{
			double startAngle = Scene::Time() * 15_deg + i * 90_deg;
			Circle{ center, 180 }.drawPie(startAngle, 60_deg, ColorF{ 1.0, 0.3 }, ColorF{ 1.0, 0.0 });
		}

		for (int32 i = 0; i < 6; ++i)
		{
			double startAngle = Scene::Time() * -15_deg + i * 60_deg;
			Circle{ center, 180 }.drawPie(startAngle, 40_deg, ColorF{ 1.0, 0.3 }, ColorF{ 1.0, 0.0 });
		}
	}

	// Function to calculate CPS based on item ownership counts
	int32 CalculateCPS(const Array<Item>& ItemTable, const Array<int32>& itemCounts)
	{
		int32 cps = 0;

		for (size_t i = 0; i < ItemTable.size(); ++i)
		{
			cps += ItemTable[i].cps * itemCounts[i];
		}

		return cps;
	}

	void Main()
	{
		// Cookie emoji
		const Texture texture{ U"🍪"_emoji };

		// Item data
		const Array<Item> ItemTable = {
			{ Texture{ U"🌾"_emoji }, U"Cookie Farm", 10, 1 },
			{ Texture{ U"🏭"_emoji }, U"Cookie Factory", 100, 10 },
			{ Texture{ U"⚓"_emoji }, U"Cookie Port", 1000, 100 },
		};

		// Number of each item owned
		Array<int32> itemCounts(ItemTable.size()); // = { 0, 0, 0 }

		// Font
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Cookie click circle
		constexpr Circle CookieCircle{ 170, 300, 100 };

		// Effects
		Effect effectBackground, effect;

		// Cookie spring
		CookieSpring cookieSpring;

		// Number of cookies
		double cookies = 0;

		// Game elapsed time accumulation
		double accumulatedTime = 0.0;

		// Background cookie accumulated time
		double cookieBackgroundAccumulatedTime = 0.0;

		// Load save data if found
		{
			// Open binary file
			Deserializer<BinaryReader> reader{ U"game.save" };

			if (reader) // If successfully opened
			{
				SaveData saveData;

				reader(saveData);

				cookies = saveData.cookies;

				itemCounts = saveData.itemCounts;
			}
		}

		while (System::Update())
		{
			// Calculate cookies per second production
			const int32 cps = CalculateCPS(ItemTable, itemCounts);

			// Add game elapsed time
			accumulatedTime += Scene::DeltaTime();

			// If accumulated 0.1 seconds or more
			if (0.1 <= accumulatedTime)
			{
				accumulatedTime -= 0.1;

				// Add 0.1 seconds worth of cookie production
				cookies += (cps * 0.1);
			}

			// Background cookies
			{
				// Calculate appropriate interval for background cookie generation from cps (gradually decrease to prevent too many, with lower limit)
				const double cookieBackgroundSpawnTime = cps ? Max(1.0 / Math::Log2(cps * 2), 0.03) : Math::Inf;

				if (cps)
				{
					cookieBackgroundAccumulatedTime += Scene::DeltaTime();
				}

				while (cookieBackgroundSpawnTime <= cookieBackgroundAccumulatedTime)
				{
					effectBackground.add<CookieBackgroundEffect>(RandomVec2(Rect{ 0, -150, 800, 100 }), texture);

					cookieBackgroundAccumulatedTime -= cookieBackgroundSpawnTime;
				}
			}

			// Update cookie spring
			cookieSpring.update(Scene::DeltaTime(), CookieCircle.leftPressed());

			// If mouse cursor is over cookie circle
			if (CookieCircle.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// If cookie circle is left clicked
			if (CookieCircle.leftClicked())
			{
				++cookies;

				// Add cookie dancing effect
				effect.add<CookieEffect>(Cursor::Pos().movedBy(Random(-5, 5), Random(-5, 5)), texture);

				// Add "+1" rising effect
				effect.add<PlusOneEffect>(Cursor::Pos().movedBy(Random(-5, 5), Random(-15, -5)), font);

				// Add background cookie
				effectBackground.add<CookieBackgroundEffect>(RandomVec2(Rect{ 0, -150, 800, 100 }), texture);
			}

			// Draw background
			Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

			// Draw background falling cookies
			effectBackground.update();

			// Draw cookie halo
			DrawHalo(CookieCircle.center);

			// Display cookie count as integer
			font(ThousandsSeparate((int32)cookies)).drawAt(60, 170, 100);

			// Display cookie production rate
			font(U"Per second: {}"_fmt(cps)).drawAt(24, 170, 160);

			// Draw cookie
			texture.scaled(1.5 - cookieSpring.get()).drawAt(CookieCircle.center);

			// Draw effects
			effect.update();

			for (size_t i = 0; i < ItemTable.size(); ++i)
			{
				// Item ownership count
				const int32 itemCount = itemCounts[i];

				// Current item price
				const int32 itemCost = ItemTable[i].getCost(itemCount);

				// CPS per item
				const int32 itemCps = ItemTable[i].cps;

				// Button
				if (Button(Rect{ 340, (40 + 120 * i), 420, 100 }, ItemTable[i].emoji,
					font, ItemTable[i].name, U"C{} / {} CPS"_fmt(itemCost, itemCps), itemCount, (itemCost <= cookies)))
				{
					cookies -= itemCost;
					++itemCounts[i];
				}
			}
		}

		// Save game at exit after main loop
		{
			// Open binary file
			Serializer<BinaryWriter> writer{ U"game.save" };

			// Write serializable data
			writer(SaveData{ cookies, itemCounts });
		}
	}
	```


## 10. Drawing playing cards

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/10.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		Scene::SetBackground(Palette::Darkgreen);

		// Create card pack with 75 pixel card width and red back
		const PlayingCard::Pack pack{ 75, Palette::Red };

		// Number of jokers
		constexpr int32 NumJokers = 2;

		// Create deck including 52 cards + jokers
		Array<PlayingCard::Card> cards = PlayingCard::CreateDeck(NumJokers);

		while (System::Update())
		{
			for (size_t i = 0; i < cards.size(); ++i)
			{
				const Vec2 center{ (100 + i % 13 * 90), (100 + (i / 13) * 130) };

				if (pack.regionAt(center).mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);

					if (MouseL.down())
					{
						// Flip card
						cards[i].flip();
					}
				}

				// Draw card
				pack(cards[i]).drawAt(center);
			}
		}
	}
	```


## 11. Tic-tac-toe

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/11.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Function to check if 3 marks are connected
	bool CheckLine(const Grid<int32>& grid, const Point& cellA, const Point& cellB, const Point& cellC)
	{
		const int32 a = grid[cellA];
		const int32 b = grid[cellB];
		const int32 c = grid[cellC];
		return ((a != 0) && (a == b) && (b == c));
	}

	// Function to return list of connected lines
	Array<std::pair<Point, Point>> CheckLines(const Grid<int32>& grid)
	{
		Array<std::pair<Point, Point>> results;

		// Check 3 vertical columns
		for (int32 x = 0; x < 3; ++x)
		{
			if (CheckLine(grid, Point{ x, 0 }, Point{ x, 1 }, Point{ x, 2 }))
			{
				results.emplace_back(Point{ x, 0 }, Point{ x, 2 });
			}
		}

		// Check 3 horizontal rows
		for (int32 y = 0; y < 3; ++y)
		{
			if (CheckLine(grid, Point{ 0, y }, Point{ 1, y }, Point{ 2, y }))
			{
				results.emplace_back(Point{ 0, y }, Point{ 2, y });
			}
		}

		// Check diagonal (top-left -> bottom-right)
		if (CheckLine(grid, Point{ 0, 0 }, Point{ 1, 1 }, Point{ 2, 2 }))
		{
			results.emplace_back(Point{ 0, 0 }, Point{ 2, 2 });
		}

		// Check diagonal (top-right -> bottom-left)
		if (CheckLine(grid, Point{ 2, 0 }, Point{ 1, 1 }, Point{ 0, 2 }))
		{
			results.emplace_back(Point{ 2, 0 }, Point{ 0, 2 });
		}

		return results;
	}

	class GameBoard
	{
	public:

		// Cell size
		static constexpr int32 CellSize = 150;

		// O mark value
		static constexpr int32 O_Mark = 1;

		// X mark value
		static constexpr int32 X_Mark = 2;

		void update()
		{
			if (m_gameOver)
			{
				return;
			}

			// 3x3 cells
			for (auto p : step(Size{ 3, 3 }))
			{
				// Cell
				const Rect cell{ (p * CellSize), CellSize };

				// Cell mark
				const int32 mark = m_grid[p];

				// If cell is empty and clicked
				if ((mark == 0) && cell.leftClicked())
				{
					// Write current mark to cell
					m_grid[p] = m_currentMark;

					// Switch current mark
					m_currentMark = ((m_currentMark == O_Mark) ? X_Mark : O_Mark);

					// Look for connected lines
					m_lines = CheckLines(m_grid);

					// If empty cells become 0 or connected lines are found
					if (m_grid.count(0) == 0 || m_lines)
					{
						// Game over
						m_gameOver = true;
					}
				}
			}
		}

		// Reset game
		void reset()
		{
			m_currentMark = O_Mark;

			m_grid.fill(0);

			m_lines.clear();

			m_gameOver = false;
		}

		// Drawing
		void draw() const
		{
			drawGridLines();

			drawCells();

			drawResults();
		}

		// Returns whether game is over
		bool isGameOver() const
		{
			return m_gameOver;
		}

	private:

		// 3x3 2D array (initial value is 0 for all elements)
		Grid<int32> m_grid = Grid<int32>(3, 3);

		// Mark to be placed next
		int32 m_currentMark = O_Mark;

		// Game over flag
		bool m_gameOver = false;

		// List of 3 consecutive lines
		Array<std::pair<Point, Point>> m_lines;

		// Draw grid
		void drawGridLines() const
		{
			// Draw lines
			for (auto i : { 1, 2 })
			{
				Line{ (i * CellSize), 0, (i * CellSize), (3 * CellSize) }
					.draw(4, ColorF{ 0.25 });

				Line{ 0, (i * CellSize), (3 * CellSize), (i * CellSize) }
					.draw(4, ColorF{ 0.25 });
			}
		}

		// Draw cells
		void drawCells() const
		{
			// 3x3 cells
			for (auto p : step(Size{ 3, 3 }))
			{
				// Cell
				const Rect cell{ (p * CellSize), CellSize };

				// Cell mark
				const int32 mark = m_grid[p];

				// If X mark
				if (mark == X_Mark)
				{
					// Draw X mark
					Shape2D::Cross(CellSize * 0.4, 10, cell.center())
						.draw(ColorF{ 0.2 });

					// Don't process this cell further
					continue;
				}
				else if (mark == O_Mark) // If O mark
				{
					// Draw O mark
					Circle{ cell.center(), (CellSize * 0.4 - 10) }
					.drawFrame(10, 0, ColorF{ 0.2 });

					// Don't process this cell further
					continue;
				}

				// If cell is moused over
				if (!m_gameOver && cell.mouseOver())
				{
					// Change cursor to hand icon
					Cursor::RequestStyle(CursorStyle::Hand);

					// Draw semi-transparent white over cell
					cell.stretched(-2).draw(ColorF{ 1.0, 0.6 });
				}
			}
		}

		// Draw connected lines
		void drawResults() const
		{
			for (const auto& line : m_lines)
			{
				// Get start and end cells of connected line
				const Rect cellBegin{ line.first * CellSize, CellSize };
				const Rect cellEnd{ line.second * CellSize, CellSize };

				// Draw line
				Line{ cellBegin.center(), cellEnd.center() }
					.stretched(CellSize * 0.45)
					.draw(LineStyle::RoundCap, 5, ColorF{ 0.6 });
			}
		}
	};

	void Main()
	{
		// Background color
		Scene::SetBackground(ColorF{ 0.8, 1.0, 0.9 });

		constexpr Point Offset{ 175, 30 };

		GameBoard gameBoard;

		while (System::Update())
		{
			{
				// Move 2D drawing and mouse cursor coordinates
				const Transformer2D transform{ Mat3x2::Translate(Offset), TransformCursor::Yes };

				gameBoard.update();

				gameBoard.draw();
			}

			// If game is over
			if (gameBoard.isGameOver())
			{
				// Reset if Reset button is pressed
				if (SimpleGUI::ButtonAt(U"Reset", Vec2{ 400, 520 }))
				{
					gameBoard.reset();
				}
			}
		}
	}
	```

## 12. Rhythm game basics

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/12.png)

??? memo "Code"
	First, place a chart file `notes.txt` written as follows in the `App/` folder of your project.

	```txt title="notes.txt"
	2000 0
	2500 1
	3000 2
	3500 3
	4000 3
	4500 2
	5000 1
	5500 0
	6000 0
	6500 1
	7000 2
	7500 3
	8000 3
	8500 2
	9000 1
	9500 0
	```

	In actual games, elapsed time should be calculated from `.posSec()` or `.posSample()` of `Audio`, but this sample uses `Stopwatch` to determine elapsed time without using audio files.

	```cpp
	# include <Siv3D.hpp>

	// Note
	struct Note
	{
		// Note time
		int32 time;

		// Key index to press (0, 1, 2, 3)
		int32 key;

		// false when disappeared
		bool active = true;
	};

	// Function to load note information from chart file
	Array<Note> LoadNotes(const FilePath& path)
	{
		TextReader reader{ path };

		if (not reader)
		{
			throw Error{ U"Chart {} not found."_fmt(path) };
		}

		Array<Note> notes;

		String line;

		// Read line by line
		while (reader.readLine(line))
		{
			// Skip empty lines
			if (line.isEmpty())
			{
				continue;
			}

			// Split read line by half-width space
			const Array<String> params = line.split(U' ');

			// If split result is not 2 elements, it's an invalid chart
			if (params.size() != 2)
			{
				throw Error{ U"Invalid chart." };
			}

			// Convert split results to int32 type
			notes.emplace_back(Parse<int32>(params[0]), Parse<int32>(params[1]));
		}

		return notes;
	}

	// Function to calculate note position
	Vec2 GetNotePos(const Note& note, int32 time)
	{
		const double x = (250 + note.key * 100);
		const double y = (500 - (note.time - time) * 0.25);
		return{ x, y };
	}

	// Effect when note is hit
	struct NoteEffect : IEffect
	{
		Vec2 m_start;

		int32 m_score;

		Font m_font;

		NoteEffect(const Vec2& start, int32 score, const Font& font)
			: m_start{ start }
			, m_score{ score }
			, m_font{ font } {}

		bool update(double t) override
		{
			Circle{ m_start, (30 + t * 80) }.drawFrame(15 * (0.5 - t));

			if (m_score == 2)
			{
				m_font(U"Excellent").drawAt(32, m_start.movedBy(0, (-20 - t * 160)), Palette::Orange);
			}
			else if (m_score == 1)
			{
				m_font(U"Good").drawAt(32, m_start.movedBy(0, (-20 - t * 160)), Palette::Skyblue);
			}

			return (t < 0.5);
		}
	};

	void Main()
	{
		// Note array
		Array<Note> notes = LoadNotes(U"notes.txt");

		// Judgment keys
		const Array<Input> Keys = { KeyA, KeyS, KeyD, KeyF };

		// Key input effect transitions
		Array<Transition> keyTransitions(Keys.size(), Transition{ 0.0s, 0.2s });

		// Stopwatch for time measurement
		Stopwatch stopwatch{ StartImmediately::Yes };

		// Font
		const Font font{ FontMethod::MSDF, 48, Typeface::Heavy };

		// Effect management
		Effect effect;

		while (System::Update())
		{
			// Elapsed time (milliseconds)
			const int32 time = stopwatch.ms();

			ClearPrint();

			Print << time;

			////////////////////////////////
			//
			//	State update
			//
			////////////////////////////////

			for (size_t i = 0; i < Keys.size(); ++i)
			{
				keyTransitions[i].update(Keys[i].down());
			}

			for (auto& note : notes)
			{
				// Skip disappeared notes
				if (not note.active)
				{
					continue;
				}

				// Difference between current time and note time (milliseconds)
				const int32 diffMillisec = (time - note.time);

				// If absolute difference is less than 250 milliseconds
				if (Abs(diffMillisec) < 250)
				{
					// If key corresponding to note is pressed
					if (Keys[note.key].down())
					{
						// Remove note
						note.active = false;

						// Note position
						const Vec2 notePos = GetNotePos(note, time);

						// Add effect
						effect.add<NoteEffect>(Vec2{ notePos.x, 500 }, (Abs(diffMillisec) < 80 ? 2 : 1), font);
					}
				}

				// Delay of 250 milliseconds or more is a miss
				if (note.active && (250 <= diffMillisec))
				{
					// Remove note
					note.active = false;
				}
			}

			////////////////////////////////
			//
			//	Drawing
			//
			////////////////////////////////

			// Draw input
			for (int32 i = 0; i < 4; ++i)
			{
				const double x = (250 + i * 100);
				RectF{ Arg::bottomCenter(x, 600), 80, 600 }
					.draw(Arg::top = ColorF{ 1.0, 0.0 }, Arg::bottom = ColorF{ 1.0, keyTransitions[i].easeOut() * 0.5 });
			}

			// Draw rectangle
			Rect{ 0, 480, 800, 40 }.draw(ColorF{ 0.5 });

			// Draw key names
			for (int32 i = 0; i < 4; ++i)
			{
				const double x = (250 + i * 100);
				font(Keys[i].name()).drawAt(20, Vec2{ x, 500 }, ColorF{ 0.7 });
			}

			// Draw notes
			for (const auto& note : notes)
			{
				// Skip disappeared notes
				if (not note.active)
				{
					continue;
				}

				// Note position
				const Vec2 notePos = GetNotePos(note, time);

				// Only draw notes that are on screen
				if (-100.0 < notePos.y)
				{
					Circle{ notePos, 30 }.draw();
				}
			}

			// Draw effects
			effect.update();
		}
	}
	```

## 13. Minesweeper

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/Minesweeper/Screenshot/3.png)

[Siv3D-Sample | Minesweeper :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/tree/main/Samples/Minesweeper){:target="_blank" .md-button}


## 14. AI Othello

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/SimpleOthelloAI/Screenshot/2.png)

[Siv3D-Sample | AI Othello :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/tree/main/Samples/SimpleOthelloAI){:target="_blank" .md-button}


## 15. Klondike

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/Klondike/Screenshot/2.png)

[Siv3D-Sample | Klondike :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/tree/main/Samples/Klondike){:target="_blank" .md-button}


## 16. Memory game

![](https://raw.githubusercontent.com/Reputeless/games/main/games/003/A.png)

[Game Patterns | Memory game :material-open-in-new:](https://github.com/Reputeless/games/blob/main/games/003/A.md){:target="_blank" .md-button}


## 17. Tower of Hanoi

![](https://raw.githubusercontent.com/Reputeless/games/main/games/004/A.png)

[Game Patterns | Tower of Hanoi :material-open-in-new:](https://github.com/Reputeless/games/blob/main/games/004/A.md){:target="_blank" .md-button}


## 18. Wheel of Fortune (Roulette)

![](https://raw.githubusercontent.com/Reputeless/games/main/games/006/A.png)

[Game Patterns | Wheel of Fortune (Roulette) :material-open-in-new:](https://github.com/Reputeless/games/blob/main/games/006/A.md){:target="_blank" .md-button}


## 19. 2D RPG map and movement basics

![](https://raw.githubusercontent.com/Reputeless/games/main/games/007/A.png)

[Game Patterns | 2D RPG map and movement basics :material-open-in-new:](https://github.com/Reputeless/games/blob/main/games/007/A.md){:target="_blank" .md-button}


## 20. Auto tiles

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/AutoTiles/Screenshot/1.png)

[Siv3D-Sample | Auto tiles :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/tree/main/Samples/AutoTiles){:target="_blank" .md-button}

## 21. Board game basics

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/21.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	/// @brief Board game square types
	enum class SquareType
	{
		/// @brief Start
		Start,

		/// @brief Normal
		Normal,

		/// @brief Goal
		Goal,
	};

	/// @brief Board game square information
	struct SquareInfo
	{
		/// @brief Square position
		Point pos;

		/// @brief Square type
		SquareType type;
	};

	/// @brief Draws board game squares.
	/// @param squares Board game squares
	/// @param font Font
	void DrawSquares(const Array<SquareInfo>& squares, const Font& font)
	{
		// Draw lines between squares
		for (size_t i = 0; i < (squares.size() - 1); ++i)
		{
			Line{ squares[i].pos, squares[i + 1].pos }
				.draw(32, ColorF{ 1.0, 0.95, 0.9 });
		}

		// For each square
		for (const auto& square : squares)
		{
			if (square.type == SquareType::Start)
			{
				// Draw start square
				RoundRect{ Arg::center = square.pos, 144, 144, 24 }
					.draw(ColorF{ 0.5, 0.5, 0.8 }).drawFrame(4, ColorF{ 0.3 });

				// Draw start text
				font(U"START")
					.drawAt(36, square.pos);
			}
			else if (square.type == SquareType::Normal)
			{
				// Draw normal square
				RoundRect{ Arg::center = square.pos, 100, 100, 24 }
					.draw().drawFrame(4, ColorF{ 0.3 });
			}
			else if (square.type == SquareType::Goal)
			{
				// Draw goal square
				RoundRect{ Arg::center = square.pos, 144, 144, 24 }
					.draw(ColorF{ 0.8, 0.5, 0.5 }).drawFrame(4, ColorF{ 0.3 });

				// Draw goal text
				font(U"GOAL")
					.drawAt(36, square.pos);
			}
		}
	}

	void Main()
	{
		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font
		const Font font{ FontMethod::MSDF, 30, Typeface::Bold };

		// Player emoji
		const Texture playerEmoji{ U"🐥"_emoji };

		// Board game square information
		const Array<SquareInfo> squares = {
			{ {100, 500}, SquareType::Start },
			{ {300, 500}, SquareType::Normal },
			{ {500, 500}, SquareType::Normal },
			{ {700, 500}, SquareType::Normal },
			{ {700, 350}, SquareType::Normal },
			{ {500, 350}, SquareType::Normal },
			{ {300, 350}, SquareType::Normal },
			{ {100, 350}, SquareType::Normal },
			{ {100, 200}, SquareType::Normal },
			{ {300, 200}, SquareType::Normal },
			{ {500, 200}, SquareType::Normal },
			{ {700, 200}, SquareType::Goal },
		};

		// Dice rotation timer
		Timer diceTimer{ 1s };

		// Player movement timer
		Timer walkTimer{ 0.5s };

		// Can roll dice
		bool canRollDice = true;

		// Player position
		size_t playerPos = 0;

		// Dice result
		int32 diceResult = 0;

		// Step count
		int32 walkCount = 0;

		while (System::Update())
		{
			// Roll dice button
			if (SimpleGUI::Button(U"Roll dice",
				Vec2{ 40, 40 }, 200, canRollDice))
			{
				// Start dice rotation
				diceTimer.start();

				// Disable dice rolling
				canRollDice = false;
			}

			// Dice is rotating
			if (diceTimer.isRunning())
			{
				// Draw dice face
				Circle{ 300, 60, 40 }.draw();
				font(U"0/{}"_fmt(Random(1, 6)))
					.drawAt(30, Vec2{ 300, 60 }, ColorF{ 0.11 });
			}

			// Finalize dice result
			if (diceTimer.reachedZero())
			{
				// Determine dice result
				diceResult = Random(1, 6);

				// Stop dice rotation
				diceTimer.reset();

				// Start player movement
				walkTimer.restart();
			}

			// Display dice result
			if (diceResult)
			{
				// Draw dice face and step count
				Circle{ 300, 60, 40 }.draw();
				font(U"{}/{}"_fmt(walkCount, diceResult))
					.drawAt(30, Vec2{ 300, 60 }, ColorF{ 0.11 });
			}

			// Player movement
			if ((walkCount != diceResult) && walkTimer.reachedZero())
			{
				// Advance step count
				++walkCount;

				// Don't advance beyond goal
				playerPos = Min((playerPos + 1), (squares.size() - 1));

				// Restart movement timer
				walkTimer.restart();
			}

			// When movement is complete
			if ((diceResult == walkCount) && walkTimer.reachedZero())
			{
				// Reset dice result
				diceResult = 0;

				// Reset step count
				walkCount = 0;

				// Reset movement timer
				walkTimer.reset();

				// Enable dice rolling
				canRollDice = true;
			}

			// Draw board game squares
			DrawSquares(squares, font);

			// Draw player shadow
			Ellipse{ squares[playerPos].pos.movedBy(0, 30), 40, 10 }
				.draw(ColorF{ 0.0, 0.2 });

			// Draw player
			playerEmoji.drawAt(squares[playerPos].pos.movedBy(0, -24));
		}
	}
	```


## 22. Slot machine

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/22.png)

Control with ++space++.

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	/// @brief Slot game symbol
	struct Symbol
	{
		/// @brief Symbol
		Texture symbol;

		/// @brief Prize money
		int32 score;
	};

	void Main()
	{
		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font
		const Font font{ FontMethod::MSDF, 48,
			U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };

		// Game start sound effect
		const Audio soundStart{ Wave{ GMInstrument::Agogo,
			PianoKey::A3, 0.3s, 0.2s } };

		// Reel stop sound effect
		const Audio soundStop{ Wave{ GMInstrument::SteelDrums,
			PianoKey::A3, 0.3s, 0.2s } };

		// Prize winning sound effect (loop playback)
		const Audio soundGet{ Wave{ GMInstrument::TinkleBell,
			PianoKey::A6, 0.1s, 0.0s }, Loop::Yes };

		// Symbol list
		const Array<Symbol> symbols
		{
			{ Texture{ U"💎"_emoji }, 1000 },
			{ Texture{ U"7️⃣"_emoji }, 777 },
			{ Texture{ U"💰"_emoji }, 300 },
			{ Texture{ U"🃏"_emoji }, 100 },
			{ Texture{ U"🍇"_emoji }, 30 },
			{ Texture{ U"🍒"_emoji }, 10 },
		};

		// Basic symbol list for one reel
		const Array<int32> symbolListBase =
			{ 0, 1, 2, 3, 3, 4, 4, 4, 4, 5, 5, 5, 5, 5 };

		// Symbol lists for 3 reels (shuffled basic list)
		const std::array<Array<int32>, 3> symbolLists =
		{
			symbolListBase.shuffled(),
			symbolListBase.shuffled(),
			symbolListBase.shuffled()
		};

		// Drawing positions for 3 reels
		const std::array<Rect, 3> reels
		{
			Rect{ 80, 100, 130, 300 },
			Rect{ 230, 100, 130, 300 },
			Rect{ 380, 100, 130, 300 },
		};

		// Money display position
		const RoundRect moneyRect{ 560, 440, 190, 60, 20 };

		// Rotation amounts for 3 reels
		std::array<double, 3> rolls = { 0.0, 0.0, 0.0 };

		// Reel stop count for current game (result determination at 3)
		int32 stopCount = 3;

		// Money
		int32 money = 1000;

		while (System::Update())
		{
			// If space key is pressed
			if (KeySpace.down())
			{
				// If all 3 reels are stopped
				if (stopCount == 3)
				{
					// If money is 3 or more
					if (3 <= money)
					{
						// Subtract 3 from money
						money -= 3;

						// Reset reel stop count to 0
						stopCount = 0;

						// Play game start sound effect
						soundStart.playOneShot();
					}
				}
				else
				{
					// Stop reel at integer position
					rolls[stopCount] = Math::Ceil(rolls[stopCount]);

					// Increase reel stop count
					++stopCount;

					// Play reel stop sound effect
					soundStop.playOneShot();

					// If all 3 reels are stopped
					if (stopCount == 3)
					{
						// Symbols on each reel
						const int32 r0 = symbolLists[0][(
							static_cast<int32>(rolls[0] + 1) % symbolLists[0].size())];
						const int32 r1 = symbolLists[1][(
							static_cast<int32>(rolls[1] + 1) % symbolLists[1].size())];
						const int32 r2 = symbolLists[2][(
							static_cast<int32>(rolls[2] + 1) % symbolLists[2].size())];

						// If all 3 reel symbols are the same
						if ((r0 == r1) && (r1 == r2))
						{
							// Add prize money to money
							money += symbols[r0].score;

							// Play prize winning sound effect
							soundGet.play();

							// Stop prize winning sound effect after 1.5 seconds
							soundGet.stop(1.5s);
						}
					}
				}
			}

			// Reel rotation
			for (int32 i = 0; i < 3; ++i)
			{
				// Skip stopped reels
				if (i < stopCount)
				{
					continue;
				}

				// Increase reel rotation amount according to elapsed time from previous frame
				rolls[i] += (Scene::DeltaTime() * 12);
			}

			// Draw reels
			for (int32 k = 0; k < 3; ++k)
			{
				// Reel background
				reels[k].draw();

				// Draw reel symbols
				for (int32 i = 0; i < 4; ++i)
				{
					// Which element of the reel to point to (integer part of rotation amount)
					const int32 index = (static_cast<int32>(rolls[k] + i)
						% symbolLists[k].size());

					// Symbol index
					const int32 symbolIndex = symbolLists[k][index];

					// Symbol position correction (fractional part of rotation amount)
					const double t = Math::Fraction(rolls[k]);

					// Draw symbol
					symbols[symbolIndex].symbol.resized(90)
						.drawAt(reels[k].center().movedBy(0, 140 * (1 - i + t)));
				}
			}

			// Draw background color above and below reels to hide overflowing symbols
			Rect{ 80, 0, 430, 100 }.draw(Scene::GetBackground());
			Rect{ 80, 400, 430, 200 }.draw(Scene::GetBackground());

			// Draw reel shadows and frames
			for (const auto& reel : reels)
			{
				// Top shadow
				Rect{ reel.tl(), reel.w, 40 }.draw(Arg::top(0.0, 0.3), Arg::bottom(0.0, 0.0));

				// Bottom shadow
				Rect{ (reel.bl() - Point{ 0, 40 }), reel.w, 40 }.draw(Arg::top(0.0, 0.0), Arg::bottom(0.0, 0.3));

				// Frame
				reel.drawFrame(4, ColorF{ 0.5 });
			}

			// Draw 2 triangles pointing to center
			Triangle{ 60, 250, 36, 90_deg }.draw(ColorF{ 1.0, 0.2, 0.2 });
			Triangle{ 530, 250, 36, -90_deg }.draw(ColorF{ 1.0, 0.2, 0.2 });

			// Draw symbol list
			RoundRect{ 560, 100, 190, 300, 20 }.draw(ColorF{ 0.9, 0.95, 1.0 });

			for (size_t i = 0; i < symbols.size(); ++i)
			{
				// Draw symbol
				symbols[i].symbol.resized(32).draw(Vec2{ 586, (114 + i * 48) });

				// Draw prize money
				font(symbols[i].score).draw(TextStyle::OutlineShadow(0.2, ColorF{ 0.5, 0.3, 0.2 },
					Vec2{ 1.5, 1.5 }, ColorF{ 0.5, 0.3, 0.2 }),
					25, Arg::topRight(720, (109 + i * 48)), ColorF{ 1.0, 0.9, 0.1 });

				if (i != 0)
				{
					// Draw separator line between symbols
					Rect{ 570, (105 + i * 48), 170, 1 }.draw(ColorF{ 0.7 });
				}
			}

			// Draw money background
			if (soundGet.isPlaying())
			{
				// Flash during prize winning
				const ColorF color = Periodic::Sine0_1(0.3s) * ColorF { 0.5, 0.6, 0.7 };
				moneyRect.draw(color).drawFrame(1);
			}
			else
			{
				moneyRect.draw(ColorF{ 0.1, 0.2, 0.3 }).drawFrame(1);
			}

			// Draw money
			font(money).draw(30, Arg::rightCenter(moneyRect.rightCenter().movedBy(-30, 0)));
		}
	}
	```