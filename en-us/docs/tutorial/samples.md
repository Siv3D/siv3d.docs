# 2. Try Samples
Siv3D allows you to develop games and applications with short code. Let's experience some examples.


!!! info "Easy way to run samples"
	- Copy each sample's code and replace your editor's code, then rebuild
	- To copy the source code in the article to the clipboard, click the :material-content-copy: in the upper right corner of the code


## 2.1 Breakout
- A sample of breakout game

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/samples/1.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Size of one block
		constexpr Size BrickSize{ 40, 20 };

		// Ball speed (pixels / second)
		constexpr double BallSpeedPerSec = 480.0;

		// Ball velocity
		Vec2 ballVelocity{ 0, -BallSpeedPerSec };

		// Ball
		Circle ball{ 400, 400, 8 };

		// Array of blocks
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
			// Paddle
			const Rect paddle{ Arg::center(Cursor::Pos().x, 500), 60, 10 };

			// Move the ball
			ball.moveBy(ballVelocity * Scene::DeltaTime());

			// Check blocks in order
			for (auto it = bricks.begin(); it != bricks.end(); ++it)
			{
				// If the block and ball intersect
				if (it->intersects(ball))
				{
					// If intersecting with the top or bottom edge of the block
					if (it->bottom().intersects(ball) || it->top().intersects(ball))
					{
						// Reverse the sign of the Y component of the ball's velocity
						ballVelocity.y *= -1;
					}
					else // If intersecting with the left or right edge of the block
					{
						// Reverse the sign of the X component of the ball's velocity
						ballVelocity.x *= -1;
					}

					// Remove the block from the array (iterator becomes invalid)
					bricks.erase(it);

					// Don't check further
					break;
				}
			}

			// If hitting the ceiling
			if ((ball.y < 0) && (ballVelocity.y < 0))
			{
				// Reverse the sign of the Y component of the ball's velocity
				ballVelocity.y *= -1;
			}

			// If hitting the left or right walls
			if (((ball.x < 0) && (ballVelocity.x < 0))
				|| ((Scene::Width() < ball.x) && (0 < ballVelocity.x)))
			{
				// Reverse the sign of the X component of the ball's velocity
				ballVelocity.x *= -1;
			}

			// If hitting the paddle
			if ((0 < ballVelocity.y) && paddle.intersects(ball))
			{
				// Change the bounce direction (velocity vector) according to the distance from the center of the paddle
				ballVelocity = Vec2{ (ball.x - paddle.center().x) * 10, -ballVelocity.y }.withLength(BallSpeedPerSec);
			}

			// Draw all blocks
			for (const auto& brick : bricks)
			{
				// Change color according to the Y coordinate of the block
				brick.stretched(-1).draw(HSV{ brick.y - 40 });
			}

			// Hide the mouse cursor
			Cursor::RequestStyle(CursorStyle::Hidden);

			// Draw the ball
			ball.draw();

			// Draw the paddle
			paddle.rounded(3).draw();
		}
	}
	```

- When rewritten more compactly, it becomes as follows
- Among various game engines and game frameworks, only Siv3D can describe breakout in this short length (25 LoC)

??? memo "Compact code"
	```cpp
    # include <Siv3D.hpp>

    void Main()
    {
        Vec2 velocity{ 0, -480 };

        Circle ball{ 400, 400, 8 };

        Array<Rect> bricks;

        for (auto p : step(Size{ 20, 5 }))
        {
            bricks << Rect{ (p.x * 40), (60 + p.y * 20), 40, 20 };
        }

        while (System::Update())
        {
            const Rect paddle{ Arg::center(Cursor::Pos().x, 500), 60, 10 };

            ball.moveBy(velocity * Scene::DeltaTime());

            for (auto it = bricks.begin(); it != bricks.end(); ++it)
            {
                if (it->intersects(ball))
                {
                    ((it->bottom().intersects(ball) || it->top().intersects(ball)) ? velocity.y : velocity.x) *= -1;
                    bricks.erase(it);
                    break;
                }
            }

            if ((ball.y < 0) && (velocity.y < 0))
            {
                velocity.y *= -1;
            }

            if (((ball.x < 0) && (velocity.x < 0)) || ((Scene::Width() < ball.x) && (0 < velocity.x)))
            {
                velocity.x *= -1;
            }

            if ((0 < velocity.y) && paddle.intersects(ball))
            {
                velocity = Vec2{ (ball.x - paddle.center().x) * 10, -velocity.y }.withLength(480);
            }

            for (const auto& brick : bricks)
            {
                brick.stretched(-1).draw(HSV{ brick.y - 40 });
            }

            ball.draw();

            paddle.rounded(3).draw();
        }
    }
	```


## 2.2 Typing Game
- The basic functions of a typing game can be implemented as follows
- The characteristic is that what you want to do (text input update, drawing, state update) is directly and intuitively reflected in the code

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/samples/2.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// List of problem sentences
		const Array<String> texts =
		{
			U"Practice makes perfect.",
			U"Don't cry over spilt milk.",
			U"Faith will move mountains.",
			U"Nothing ventured, nothing gained.",
			U"Bad news travels fast.",
		};

		// Randomly select a problem sentence
		String target = texts.choice();

		// String being entered
		String input;

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		while (System::Update())
		{
			// Text input (TextInputMode::DenyControl: do not accept enter, tab, backspace)
			TextInput::UpdateText(input, TextInputMode::DenyControl);

			// If incorrect input is included, delete it
			while (not target.starts_with(input))
			{
				input.pop_back();
			}

			// If matched, move to the next problem
			if (input == target)
			{
				// Randomly select a problem sentence
				target = texts.choice();

				// Clear the input string	
				input.clear();
			}

			// Draw the problem sentence
			font(target).draw(40, Vec2{ 40, 80 }, ColorF{ 0.98 });

			// Draw the characters being entered
			font(input).draw(40, Vec2{ 40, 80 }, ColorF{ 0.12 });
		}
	}
	```


## 2.3 Emoji Tower
- Drop emojis by clicking to create a tower that physically stacks up
- A series of functions (polygon generation from images, physics simulation, camera operation, drawing) are completed with compact code

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/samples/3.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.2, 0.7, 1.0 });

		// Emojis that appear
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
				// Update the 2D physics world
				world.update(StepTime);

				accumulatedTime -= StepTime;
			}

			// Remove bodies that fell below the ground
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

			// Update the 2D camera
			camera.update();
			{
				// Create Transformer2D from 2D camera
				const auto t = camera.createTransformer();

				// If left-clicked
				if (MouseL.down())
				{
					// Add a body
					bodies << world.createPolygons(P2Dynamic, Cursor::PosF(), polygons[index], P2Material{ 0.1, 0.0, 1.0 });

					// Add the pair of body ID and emoji index to the correspondence table
					table.emplace(bodies.back().id(), std::exchange(index, Random(polygons.size() - 1)));
				}

				// Draw all bodies
				for (const auto& body : bodies)
				{
					textures[table[body.id()]].rotated(body.getAngle()).drawAt(body.getPos());
				}

				// Draw the ground
				ground.draw(Palette::Green);

				// Draw the currently controllable emoji
				textures[index].drawAt(Cursor::PosF(), ColorF{ 1.0, (0.5 + Periodic::Sine0_1(1s) * 0.5) });
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);
		}
	}
	```


## 2.4 Slot Machine
- A sample slot game operated with ++space++
- Sound effects are generated programmatically using the built-in soundfont
- By modifying the highlighted parts of the code, you can change settings such as symbols, appearance probability, and prize money

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/samples/4.png)

??? memo "Code"
	```cpp hl_lines="34-43 45-47"
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

		// List of symbols
		const Array<Symbol> symbols
		{
			{ Texture{ U"💎"_emoji }, 1000 },
			{ Texture{ U"7️⃣"_emoji }, 777 },
			{ Texture{ U"💰"_emoji }, 300 },
			{ Texture{ U"🃏"_emoji }, 100 },
			{ Texture{ U"🍇"_emoji }, 30 },
			{ Texture{ U"🍒"_emoji }, 10 },
		};

		// Basic list of symbols prepared for one reel
		const Array<int32> symbolListBase =
			{ 0, 1, 2, 3, 3, 4, 4, 4, 4, 5, 5, 5, 5, 5 };

		// List of symbols prepared for 3 reels (basic list shuffled)
		const std::array<Array<int32>, 3> symbolLists =
		{
			symbolListBase.shuffled(),
			symbolListBase.shuffled(),
			symbolListBase.shuffled()
		};

		// Draw positions of 3 reels
		const std::array<Rect, 3> reels
		{
			Rect{ 80, 100, 130, 300 },
			Rect{ 230, 100, 130, 300 },
			Rect{ 380, 100, 130, 300 },
		};

		// Draw position of money
		const RoundRect moneyRect{ 560, 440, 190, 60, 20 };

		// Rotation amount of 3 reels
		std::array<double, 3> rolls = { 0.0, 0.0, 0.0 };

		// Reel stop count in current game (result judgment at 3 times)
		int32 stopCount = 3;

		// Money possessed
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
						// Reduce money by 3
						money -= 3;

						// Reset reel stop count to 0
						stopCount = 0;

						// Play game start sound effect
						soundStart.playOneShot();
					}
				}
				else
				{
					// Stop the reel at integer position
					rolls[stopCount] = Math::Ceil(rolls[stopCount]);

					// Increase reel stop count
					++stopCount;

					// Play reel stop sound effect
					soundStop.playOneShot();

					// If all 3 reels are stopped
					if (stopCount == 3)
					{
						// Symbols of each reel
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

			// Draw 2 triangles pointing to the center
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
				// Blink while winning prize
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

## 2.5 Cookie Clicker
- A sample of a "Cookie Clicker" type game where you increase the number of items by clicking and purchasing production facilities
- Includes save data creation and loading, so you can resume from where you left off even after exiting the game

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/samples/5.png)

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
    /// @param font Font used for text drawing
    /// @param name Item name
    /// @param desc Item description
    /// @param count Item possession count
    /// @param enabled Whether the button can be pressed
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
            , m_texture{ texture } {
        }

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

        // Scale
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
            , m_texture{ texture } {
        }

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
            , m_font{ font } {
        }

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

        // Cost when purchasing the item for the first time
        int32 initialCost;

        // Item CPS
        int32 cps;

        // Returns the purchase cost when having count items
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
            // Add spring accumulated time
            m_accumulatedTime += deltaTime;

            while (0.005 <= m_accumulatedTime)
            {
                // Spring force (direction that cancels change)
                double force = (-0.02 * m_x);

                // Force that works when screen is pressed
                if (pressed)
                {
                    force += 0.004;
                }

                // Apply force to velocity (also apply damping)
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

    // Function to calculate CPS based on item possession count
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
            { Texture{ U"🌾"_emoji }, U"クッキー農場", 10, 1 },
            { Texture{ U"🏭"_emoji }, U"クッキー工場", 100, 10 },
            { Texture{ U"⚓"_emoji }, U"クッキー港", 1000, 100 },
        };

        // Possession count of each item
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

        // If save data is found, load it
        {
            // Open binary file
            Deserializer<BinaryReader> reader{ U"game.save" };

            if (reader) // If open succeeded
            {
                SaveData saveData;

                reader(saveData);

                cookies = saveData.cookies;

                for (size_t i = 0; i < Min(ItemTable.size(), saveData.itemCounts.size()); ++i)
                {
                    itemCounts[i] = saveData.itemCounts[i];
                }
            }
        }

        while (System::Update())
        {
            // Calculate cookie production per second
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
                // Calculate appropriate interval for background cookies to appear from cps (gradually gets smaller to avoid too many, with lower limit)
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

            // If cookie circle is left-clicked
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

            // Draw falling background cookies
            effectBackground.update();

            // Draw cookie halo
            DrawHalo(CookieCircle.center);

            // Display number of cookies as integer
            font(ThousandsSeparate((int32)cookies)).drawAt(60, 170, 100);

            // Display cookie production
            font(U"毎秒: {}"_fmt(cps)).drawAt(24, 170, 160);

            // Draw cookie
            texture.scaled(1.5 - cookieSpring.get()).drawAt(CookieCircle.center);

            // Draw effects
            effect.update();

            for (size_t i = 0; i < ItemTable.size(); ++i)
            {
                // Item possession count
                const int32 itemCount = itemCounts[i];

                // Current price of item
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

        // After main loop, save game on exit
        {
            // Open binary file
            Serializer<BinaryWriter> writer{ U"game.save" };

            // Write out data that supports serialization
            writer(SaveData{ cookies, itemCounts });
        }
    }
    ```


## 2.6 Minesweeper
- A sample of minesweeper. Left-click to open cells, right-click to place flags
- Avoid stepping on bombs while deducing bomb locations and opening cells

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/samples/6.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Game state
	enum class GameState
	{
		Game,		// Game in progress
		Failed,		// Game over
		Cleared,	// Game clear
	};

	// Cell state
	struct CellState
	{
		// Opened
		bool opened = false;

		// Flagged
		bool flagged = false;

		// Exploded
		bool exploded = false;

		// Island number (used when opening number-less squares at once)
		int32 groupIndex = 0;
	};

	// Offsets to surrounding squares
	constexpr Point Offsets[8] =
	{
		{ -1, -1 }, { 0, -1 }, { 1, -1 },
		{ -1,  0 }           , { 1,  0 },
		{ -1,  1 }, { 0,  1 }, { 1,  1 },
	};

	// Function to return the number of 💣 (-1) around specified square
	int32 GetBombCount(const Grid<int32>& grid, const Point& center)
	{
		// If self is 💣 (-1), return -1
		if (grid[center] == -1)
		{
			return -1;
		}

		// Number of 💣 (-1) found
		int32 bombCount = 0;

		for (const auto& offset : Offsets)
		{
			// Square to check
			const Point pos = (center + offset);

			// grid.fetch(pos, defaultValue) returns
			// grid[pos] if pos is within range, otherwise returns defaultValue
			if (grid.fetch(pos, 0) == -1) // If 💣 (-1)
			{
				++bombCount;
			}
		}

		return bombCount;
	}

	// Function to generate board
	Grid<int32> MakeGame(const Size& size, int32 bombs)
	{
		// Create 2D array for board
		Grid<int32> grid(size);

		// Place specified number of 💣 (-1)
		while (bombs)
		{
			// Random position on 2D array
			const Point pos = RandomPoint((size.x - 1), (size.y - 1));

			// If not yet placed
			if (grid[pos] == 0)
			{
				// Place 💣 (-1)
				grid[pos] = -1;

				// Reduce remaining number of 💣
				--bombs;
			}
		}

		// For all squares
		for (int32 y = 0; y < size.y; ++y)
		{
			for (int32 x = 0; x < size.x; ++x)
			{
				// Calculate numbers. However, 💣 squares remain -1
				grid[y][x] = GetBombCount(grid, Point{ x, y });
			}
		}

		return grid;
	}

	// Function to create board state
	Grid<CellState> MakeStates(const Grid<int32>& grid)
	{
		const Size size = grid.size();

		// 2D array same size as board
		Grid<CellState> states(size);

		// Data structure managing connection status of each square
		DisjointSet<int32> ds{ states.num_elements() };

		// For all squares
		for (int32 y = 0; y < size.y; ++y)
		{
			for (int32 x = 0; x < size.x; ++x)
			{
				// Index of own square
				const int32 index = static_cast<int32>(y * size.x + x);

				// If own is 0 square
				if (grid[y][x] == 0)
				{
					// If right square is 0
					if (int nx = (x + 1);
						(nx < size.x) && (grid[y][nx] == 0))
					{
						const int32 east = (index + 1); // Index of right square
						ds.merge(index, east); // Make right square same island
					}

					// If bottom square is 0
					if (int ny = (y + 1);
						(ny < size.y) && (grid[ny][x] == 0))
					{
						const int32 south = (index + size.x); // Index of bottom square
						ds.merge(index, south); // Make bottom square same island
					}
				}
			}
		}

		{
			// Square index
			int32 index = 0;

			// For all squares
			for (int32 y = 0; y < size.y; ++y)
			{
				for (int32 x = 0; x < size.x; ++x)
				{
					// Assign island number
					states[y][x].groupIndex = ds.find(index);

					++index;
				}
			}
		}

		return states;
	}

	// Function to draw unopened cell blocks
	void DrawBlock(const Rect& rect)
	{
		Triangle{ rect.tl(), rect.tr(), rect.bl() }.draw(ColorF{ 1.0 });
		Triangle{ rect.tr(), rect.br(), rect.bl() }.draw(ColorF{ 0.5 });
		rect.stretched(-5).draw(ColorF{ 0.75 });
	}

	// Function to draw board
	void DrawGame(const Grid<int32>& grid, const Grid<CellState>& states, const Font& font, const Texture& bombTexture, const Texture& flagTexture, const Point& gamePos, const Size& cellSize)
	{
		// Colors for numbers 0-8
		constexpr ColorF NumberColors[9] =
		{
			ColorF{ 0, 0, 0 }, ColorF{ 0, 0, 1 }, ColorF{ 0, 0.5, 0 }, ColorF{ 1, 0, 0 },
			ColorF{ 0, 0, 0.5 }, ColorF{ 0.5, 0, 0 }, ColorF{ 0.5, 0, 0 }, ColorF{ 0.5, 0, 0 }, ColorF{ 0.5, 0, 0 }
		};

		// For all squares
		for (int32 y = 0; y < grid.height(); ++y)
		{
			for (int32 x = 0; x < grid.width(); ++x)
			{
				const auto& state = states[y][x];

				// Top-left coordinate of cell
				const Point pos = (gamePos + (cellSize * Point{ x, y }));

				// Cell area
				const Rect cell{ pos, cellSize };

				if (state.opened) // Opened
				{
					// Draw background
					cell.stretched(-1).draw(ColorF{ 0.75 });

					if (const int32 n = grid[y][x];
						n == -1) // If 💣 (-1) square
					{
						// If explosion spot, make cell red
						if (state.exploded)
						{
							cell.stretched(-1).draw(ColorF{ 1, 0, 0 });
						}

						// Draw bomb
						bombTexture.resized(36).drawAt(cell.center());
					}
					else if (1 <= n) // If number square 1 or more
					{
						// Draw number
						font(n).drawAt(cell.center(), NumberColors[n]);
					}
				}
				else // Not opened
				{
					// Draw block
					DrawBlock(cell);

					// If flagged, draw flag
					if (state.flagged)
					{
						flagTexture.resized(30).drawAt(cell.center());
					}
				}
			}
		}
	}

	// Function to open squares with specified island number and squares adjacent to them
	void OpenGroup(const Grid<int32>& grid, Grid<CellState>& states, const int32 groupIndex)
	{
		// For all squares
		for (int32 y = 0; y < grid.height(); ++y)
		{
			for (int32 x = 0; x < grid.width(); ++x)
			{
				auto& state = states[y][x];

				// If square with specified island number
				if (state.groupIndex == groupIndex)
				{
					// Open
					state.opened = true;

					// For surrounding squares
					for (const auto& offset : Offsets)
					{
						const Point neighbor = (Point{ x, y } + offset);

						// If within board range and unopened, open that square also
						if (grid.inBounds(neighbor) && (not states[neighbor].opened))
						{
							states[neighbor].opened = true;

							// If it's a number-less square (0) with different island number, recursively open those too
							if ((grid[neighbor] == 0) && (groupIndex != states[neighbor].groupIndex))
							{
								OpenGroup(grid, states, states[neighbor].groupIndex);
							}
						}
					}
				}
			}
		}
	}

	// Function to update board
	void UpdateGame(GameState& gameState, const Grid<int32>& grid, Grid<CellState>& states, const int32 bombCount, const Point& gamePos, const Size& cellSize)
	{
		// Board area
		const Rect gameArea{ gamePos, (grid.size() * cellSize - Point{ 1, 1 }) };

		// Board was left-clicked
		const bool open = gameArea.leftClicked();

		// Board was right-clicked
		const bool flag = gameArea.rightClicked();

		if (open || flag)
		{
			// Position of clicked square
			const Point pos = ((Cursor::Pos() - gamePos) / cellSize);

			if (open && (not states[pos].opened) && (not states[pos].flagged)) // Unopened, unflagged square was left-clicked
			{
				// Open that square
				states[pos].opened = true;

				// If that square is a number-less square (0)
				if (grid[pos] == 0)
				{
					// Also open squares with same island number and squares adjacent to them
					OpenGroup(grid, states, states[pos].groupIndex);
				}

				// If that square is 💣 (-1)
				if (grid[pos] == -1)
				{
					// Game over
					gameState = GameState::Failed;

					// Set exploded flag
					states[pos].exploded = true;

					// Open all 💣 squares
					for (int32 y = 0; y < grid.height(); ++y)
					{
						for (int32 x = 0; x < grid.width(); ++x)
						{
							if (grid[y][x] == -1)
							{
								states[y][x].opened = true;
							}
						}
					}
				}
				else if (states.count_if([](const CellState& c) { return (not c.opened); }) == bombCount)
				{	// If number of unopened squares matches number of bombs
					// Game clear
					gameState = GameState::Cleared;
				}
			}
			else if (flag) // Right-clicked
			{
				// Toggle flag state
				states[pos].flagged = (not states[pos].flagged);
			}
		}
	}

	void Main()
	{
		// Set background color to slightly dark gray
		Scene::SetBackground(ColorF{ 0.5 });

		// Number of squares on board
		constexpr Size GameSize{ 20, 13 };

		// Number of 💣 to place
		constexpr int32 BombCount = 30;

		// Compile error if number of 💣 is 1/4 or more of squares
		static_assert(BombCount < (GameSize.area() / 4));

		// Cell size
		constexpr Size CellSize{ 40, 40 };

		// Board draw position
		constexpr Size GamePos{ 0, 80 };

		// Font for numbers
		const Font font{ FontMethod::MSDF, 32, Typeface::Bold };

		// Bomb emoji
		const Texture bombTexture{ U"💣"_emoji };

		// Flag emoji
		const Texture flagTexture{ U"🚩"_emoji };

		// Face emojis corresponding to GameState
		const std::array<Texture, 3> faceTextures = { Texture{ U"🙂"_emoji }, Texture{ U"😵"_emoji }, Texture{ U"😎"_emoji } };

		// Create board
		Grid<int32> grid = MakeGame(GameSize, BombCount);

		// Create state of each cell
		Grid<CellState> states = MakeStates(grid);

		// Game state
		GameState gameState = GameState::Game;

		// Face button area
		const Rect faceButton{ Arg::center(Scene::Width() / 2, 40), 72 };

		while (System::Update())
		{
			////////////////////////////////
			//
			//	State update
			//
			////////////////////////////////
			{
				// Update board if game in progress
				if (gameState == GameState::Game)
				{
					UpdateGame(gameState, grid, states, BombCount, GamePos, CellSize);
				}

				// Initialize state if face button is pressed
				if (faceButton.leftClicked())
				{
					grid = MakeGame(GameSize, BombCount);
					states = MakeStates(grid);
					gameState = GameState::Game;
				}
			}

			////////////////////////////////
			//
			//	Drawing
			//
			////////////////////////////////
			{
				// Draw board
				DrawGame(grid, states, font, bombTexture, flagTexture, GamePos, CellSize);

				// Draw UI area background
				Rect{ Scene::Width(), 80 }.draw(ColorF{ 0.75 });
				{
					// Draw face button
					DrawBlock(faceButton);

					// Draw face
					faceTextures[FromEnum(gameState)].resized(60).drawAt(faceButton.center());
				}
			}
		}
	}
	```

## 2.7 QR Code Creation
- A sample that converts text entered in a text box into a QR code and displays it
- Try reading it with your smartphone camera

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/samples/7.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Change window size
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Text to convert
		TextEditState textEdit{ U"abc" };

		String previous;

		// Dynamic texture for displaying QR code
		DynamicTexture texture;

		while (System::Update())
		{
			// Text input
			SimpleGUI::TextBox(textEdit, Vec2{ 20, 20 }, 1240);

			// If text is updated, recreate QR code
			if (const String current = textEdit.text;
				current != previous)
			{
				// Convert entered text to QR code
				if (const auto qr = QR::EncodeText(current))
				{
					// Update dynamic texture with enlarged image
					texture.fill(QR::MakeImage(qr).scaled(Size{ 500, 500 }, InterpolationAlgorithm::Nearest));
				}

				previous = current;
			}

			// Display QR code
			texture.drawAt(640, 400);
		}
	}
	```


## 2.8 Music Player
- Plays music files saved on your computer

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/samples/8.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Music
		Audio audio;

		// FFT results
		FFTResult fft;

		// Whether playback position has changed
		bool seeking = false;

		while (System::Update())
		{
			ClearPrint();

			// Playback and performance time
			const String time = (FormatTime(SecondsF{ audio.posSec() }, U"M:ss")
				+ U'/' + FormatTime(SecondsF{ audio.lengthSec() }, U"M:ss"));

			// Progress bar progress
			double progress = static_cast<double>(audio.posSample()) / audio.samples();

			if (audio.isPlaying())
			{
				// FFT analysis
				FFT::Analyze(fft, audio);

				// Visualize results
				for (auto i : step(Min(Scene::Width(), static_cast<int32>(fft.buffer.size()))))
				{
					const double size = Pow(fft.buffer[i], 0.6f) * 1000;
					RectF{ Arg::bottomLeft(i, 480), 1, size }.draw(HSV{ 240.0 - i });
				}

				// Display frequency at mouse cursor position
				Rect{ Cursor::Pos().x, 0, 1, Scene::Height() }.draw();
				Print << U"{:.2f} Hz"_fmt(Cursor::Pos().x * fft.resolution);
			}

			// Play
			if (SimpleGUI::Button(U"Play", Vec2{ 40, 500 }, 120, audio && !audio.isPlaying()))
			{
				// Play with 0.2 second fade-in time
				audio.play(0.2s);
			}

			// Pause
			if (SimpleGUI::Button(U"Pause", Vec2{ 170, 500 }, 120, audio.isPlaying()))
			{
				// Pause with 0.2 second fade-out time
				audio.pause(0.2s);
			}

			// Open music file from folder
			if (SimpleGUI::Button(U"Open", Vec2{ 300, 500 }, 120))
			{
				audio.stop(0.5s);
				audio = Dialog::OpenAudio();
				audio.play();
			}

			// Slider
			if (SimpleGUI::Slider(time, progress, Vec2{ 40, 540 }, 130, 590, (not audio.isEmpty())))
			{
				// Pause with 0.05 second fade-out time
				audio.pause(0.05s);

				// Wait until playback stops
				while (audio.isPlaying())
				{
					System::Sleep(0.01s);
				}

				// Change playback position
				audio.seekSamples(static_cast<size_t>(audio.samples() * progress));

				// Don't resume playback until slider is released to avoid noise
				seeking = true;
			}
			else if (seeking && MouseL.up())
			{
				// Resume playback
				audio.play(0.05s);
				seeking = false;
			}
		}

		// On exit, fade out volume if playing
		if (audio.isPlaying())
		{
			audio.fadeVolume(0.0, 0.3s);
			System::Sleep(0.3s);
		}
	}
	```


## 2.9 Simple 3D Drawing
- You can perform 3D drawing with short code

| Operation | Description |
| --- | --- |
| ++w++ ++s++ ++a++ ++d++ | Camera forward/backward/left/right movement |
| ++e++ ++x++ | Camera up/down movement |
| ++shift++ or ++ctrl++ pressed with movement keys | Change camera movement speed |
| ++up++ ++down++ ++left++ ++right++ | Camera viewpoint movement |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/samples/9.jpg)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Resize window and scene to 1280x720
		Window::Resize(1280, 720);

		// Background color (remove sRGB curve with removeSRGBCurve() for linear rendering)
		const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

		// UV check texture (using mipmaps. Specify as sRGB texture for correct handling during linear rendering)
		const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };

		// Multisample render texture for drawing 3D scenes
		// TextureFormat::R8G8B8A8_Unorm_SRGB for linear color space rendering
		// HasDepth::Yes to use depth buffer for depth comparison
		// resolve() required before using drawing content as it's a multisample render texture
		const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

		// Debug camera for 3D scene
		// 30° vertical field of view, camera position (10, 16, -32)
		// Forward/backward: [W][S], left/right: [A][D], up/down: [E][X], viewpoint: arrow keys, acceleration: [Shift][Ctrl]
		DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

		while (System::Update())
		{
			// Update debug camera (camera movement speed: 2.0)
			camera.update(2.0);

			// Set camera to 3D scene
			Graphics3D::SetCameraTransform(camera);

			// 3D drawing
			{
				// Fill renderTexture with background color,
				// set renderTexture as render target for 3D drawing
				const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

				// Draw floor
				Plane{ 64 }.draw(uvChecker);

				// Draw box
				Box{ -8,2,0,4 }.draw(ColorF{ 0.8, 0.6, 0.4 }.removeSRGBCurve());

				// Draw sphere
				Sphere{ 0,2,0,2 }.draw(ColorF{ 0.4, 0.8, 0.6 }.removeSRGBCurve());

				// Draw cylinder
				Cylinder{ 8, 2, 0, 2, 4 }.draw(ColorF{ 0.6, 0.4, 0.8 }.removeSRGBCurve());
			}

			// Draw 3D scene to 2D scene
			{
				// Execute 3D drawing before resolving renderTexture
				Graphics3D::Flush();

				// Resolve multisample texture
				renderTexture.resolve();

				// Transfer linear rendered renderTexture to scene
				Shader::LinearToScreen(renderTexture);
			}
		}
	}
	```


## 2.10 Terrain Editing
- You can edit terrain elevation by clicking on the height map in the upper left of the screen

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/samples/10.jpg)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		// Vertex shader for terrain
		const VertexShader vsTerrain = HLSL{ U"example/shader/hlsl/terrain_forward.hlsl", U"VS" }
			| GLSL{ U"example/shader/glsl/terrain_forward.vert", {{ U"VSPerView", 1 }, { U"VSPerObject", 2 }, { U"VSPerMaterial", 3 }} };

		// Pixel shader for terrain
		const PixelShader psTerrain = HLSL{ U"example/shader/hlsl/terrain_forward.hlsl", U"PS" }
			| GLSL{ U"example/shader/glsl/terrain_forward.frag", {{ U"PSPerFrame", 0 }, { U"PSPerView", 1 }, { U"PSPerMaterial", 3 }} };

		// Pixel shader for terrain normal calculation
		const PixelShader psNormal = HLSL{ U"example/shader/hlsl/terrain_normal.hlsl", U"PS" }
			| GLSL{ U"example/shader/glsl/terrain_normal.frag", {{U"PSConstants2D", 0}} };

		// Exit if shader loading failed
		if ((not vsTerrain) || (not psTerrain) || (not psNormal))
		{
			return;
		}

		// Sky color
		const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

		// Textures used for terrain
		const Texture terrainTexture{ U"example/texture/grass.jpg", TextureDesc::MippedSRGB };
		const Texture rockTexture{ U"example/texture/rock.jpg", TextureDesc::MippedSRGB };

		// Brush texture
		const Texture brushTexture{ U"example/particle.png" };

		// Render texture for 3D scene
		const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

		// Terrain mesh
		const Mesh gridMesh{ MeshData::Grid({ 128, 128 }, 128, 128) };

		// Debug camera
		DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

		// Height map and normal map
		RenderTexture heightmap{ Size{ 256, 256 }, ColorF{ 0.0 }, TextureFormat::R32_Float };
		RenderTexture normalmap{ Size{ 256, 256 }, ColorF{ 0.0, 0.0, 0.0 }, TextureFormat::R16G16_Float };

		while (System::Update())
		{
			camera.update(2.0);

			// 3D
			{
				Graphics3D::SetCameraTransform(camera);

				const ScopedCustomShader3D shader{ vsTerrain, psTerrain };
				const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
				const ScopedRenderStates3D ss{ { ShaderStage::Vertex, 0, SamplerState::ClampLinear} };
				Graphics3D::SetVSTexture(0, heightmap);
				Graphics3D::SetPSTexture(1, normalmap);
				Graphics3D::SetPSTexture(2, rockTexture);

				gridMesh.draw(terrainTexture);
			}

			// Draw RenderTexture to 2D scene
			{
				Graphics3D::Flush();
				renderTexture.resolve();
				Shader::LinearToScreen(renderTexture);
			}

			if (const bool gen = SimpleGUI::Button(U"Random", Vec2{ 270, 10 });
				(gen || (MouseL | MouseR).pressed())) // Edit terrain
			{
				// Edit height map
				if (gen)
				{
					const PerlinNoiseF perlin{ RandomUint64() };
					Grid<float> grid(256, 256);
					for (auto p : step(grid.size()))
					{
						grid[p] = (perlin.octave2D0_1(p / 256.0f, 5) * 16.0f);
					}
					const RenderTexture noise{ grid };
					const ScopedRenderTarget2D target{ heightmap };
					noise.draw();
				}
				else
				{
					const ScopedRenderTarget2D target{ heightmap };
					const ScopedRenderStates2D blend{ BlendState::Additive };
					brushTexture.scaled(1.0 + MouseL.pressed()).drawAt(Cursor::PosF(), ColorF{ Scene::DeltaTime() * 15.0 });
				}

				// Update normal map
				{
					const ScopedRenderTarget2D target{ normalmap };
					const ScopedCustomShader2D shader{ psNormal };
					const ScopedRenderStates2D blend{ BlendState::Opaque, SamplerState::ClampLinear };
					heightmap.draw();
				}
			}

			// Display height map and normal map on the left
			heightmap.draw(ColorF{ 0.1 });
			normalmap.draw(0, 260);
		}
	}
	```


## Review Checklist
- [x] Tried using Siv3D's basic functions such as drawing, keyboard input, mouse input, physics simulation, music playback, and 3D drawing