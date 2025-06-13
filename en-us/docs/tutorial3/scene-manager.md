# 58. Scene Management
Learn about features for "scene management" where individual scenes (like game title, gameplay, and results) are implemented in separate classes, and the overall flow is constructed by navigating between them.


## 58.1 Overview of Scene Management
- Using scene management allows you to efficiently develop complex applications (especially games)
- In scene management, individual scenes like game title, gameplay, and results are implemented in separate classes, and typically one of these scenes is executed
- Using the scene management feature `SceneManager`, you can easily write code to share data between scenes or specify destination scenes for smooth screen transitions

<div class="noshadow-90"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/1.png"></div>

!!! warning "About the term 'Scene'"
	- "Scene" in scene management refers to individual game situations or their implementation classes
	- This is a different concept from the scene representing the screen explained in **Tutorial 9** or the `Scene::` namespace features

## 58.2 Scene Management Basics
- First, decide the type for the value (state) that distinguishes individual scenes
	- If you choose `String` type, individual scenes are distinguished by `String` type values like `U"Title"` for the title scene and `U"Game"` for the game scene
	- You can also choose other types like `enum class` or `int32` depending on your policy
- Next, determine the scene manager class type with `using App = SceneManager<StateType>;` and name it `App`
- Implement each scene class by inheriting from `App::Scene`
	- Typically, you implement three member functions: constructor, `.update()`, and `.draw()`
- Create an `App` type object in the `Main()` function and register each scene with `.add()`
- Call `App::update()` every frame in the main loop, and the first registered scene will automatically be executed
	- The `.update()` and `.draw()` functions implemented in the scene are called here
- The following shows the simplest example with only one scene (title scene only)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/2.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// State type is String
	using App = SceneManager<String>;

	// Title scene
	class Title : public App::Scene
	{
	public:

		// Constructor (must be implemented)
		Title(const InitData& init)
			: IScene{ init }
		{

		}

		// Update function
		void update() override
		{

		}

		// Draw function
		void draw() const override
		{
			Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

			FontAsset(U"TitleFont")(U"My Game").drawAt(60, Vec2{ 400, 100 });

			Circle{ Cursor::Pos(), 50 }.draw(Palette::Seagreen);
		}
	};

	void Main()
	{
		FontAsset::Register(U"TitleFont", FontMethod::MSDF, 48, Typeface::Bold);

		// Create scene manager
		App manager;

		// Register title scene (named "Title")
		manager.add<Title>(U"Title");

		while (System::Update())
		{
			// Execute current scene
			// The scene's .update() and .draw() are executed
			if (not manager.update())
			{
				break;
			}
		}
	}
	```


## 58.3 Scene Transitions
- Add a new scene (game scene) to the sample from **58.2**
- When you want to transition to another scene during execution of a scene, call `.changeScene(next scene state)` in the scene's `.update()` function to specify the destination scene
- Scene transitions are performed with fade-in and fade-out effects lasting 2 seconds by default
	- The fade-in and fade-out duration and color can be customized (**58.4**)
- Each time a scene transitions, the old scene instance is destroyed and a new scene class is instantiated
- During fade-in and fade-out, the scene's `.update()` is not called, only `.draw()` is called

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/3.mp4?raw=true" autoplay loop muted playsinline></video>

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// State type is String
	using App = SceneManager<String>;

	// Title scene
	class Title : public App::Scene
	{
	public:

		// Constructor (must be implemented)
		Title(const InitData& init)
			: IScene{ init }
		{
			Print << U"Title::Title()";
		}

		~Title()
		{
			Print << U"Title::~Title()";
		}

		// Update function
		void update() override
		{
			// On left click
			if (MouseL.down())
			{
				// Transition to game scene
				changeScene(U"Game");
			}
		}

		// Draw function
		void draw() const override
		{
			Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

			FontAsset(U"TitleFont")(U"My Game").drawAt(60, Vec2{ 400, 100 });

			Circle{ Cursor::Pos(), 50 }.draw(Palette::Seagreen);
		}
	};

	// Game scene
	class Game : public App::Scene
	{
	public:

		Game(const InitData& init)
			: IScene{ init }
			, m_emoji{ U"🐥"_emoji }
		{
			Print << U"Game::Game()";
		}

		~Game()
		{
			Print << U"Game::~Game()";
		}

		void update() override
		{
			if (not m_stopwatch.isStarted())
			{
				m_stopwatch.start();
			}

			// On left click
			if (MouseL.down())
			{
				m_stopwatch.pause();

				// Transition to title scene
				changeScene(U"Title");
			}
		}

		void draw() const override
		{
			Scene::SetBackground(ColorF(0.0, 0.6, 0.4));

			const double t = m_stopwatch.sF();

			const Vec2 pos{ (400 + Periodic::Sine1_1(3s, t) * 300), 300 };

			m_emoji.drawAt(pos);
		}

	private:

		Texture m_emoji;

		Stopwatch m_stopwatch;
	};

	void Main()
	{
		FontAsset::Register(U"TitleFont", FontMethod::MSDF, 48, Typeface::Bold);

		// Create scene manager
		App manager;

		// Register each scene
		manager.add<Title>(U"Title");
		manager.add<Game>(U"Game");

		while (System::Update())
		{
			// Execute current scene
			// The scene's .update() and .draw() are executed
			if (not manager.update())
			{
				break;
			}
		}
	}
	```


## 58.4 Customizing Transition Effects (1)
- To change the screen color during fade-in and fade-out, call `.setFadeColor(color)` on the `SceneManager`
- To customize scene transition time, use `.changeScene(next scene state, transition time)` (default is 2 seconds)
- For the initial scene's fade-in, you can't use `.changeScene()`, so use `SceneManager`'s `.init(state, transition time)` instead

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/4.mp4?raw=true" autoplay loop muted playsinline></video>

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// State type is String
	using App = SceneManager<String>;

	// Title scene
	class Title : public App::Scene
	{
	public:

		// Constructor (must be implemented)
		Title(const InitData& init)
			: IScene{ init }
		{
			Print << U"Title::Title()";
		}

		~Title()
		{
			Print << U"Title::~Title()";
		}

		// Update function
		void update() override
		{
			// On left click
			if (MouseL.down())
			{
				// Transition to game scene
				changeScene(U"Game", 0.5s);
			}
		}

		// Draw function
		void draw() const override
		{
			Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

			FontAsset(U"TitleFont")(U"My Game").drawAt(60, Vec2{ 400, 100 });

			Circle{ Cursor::Pos(), 50 }.draw(Palette::Seagreen);
		}
	};

	// Game scene
	class Game : public App::Scene
	{
	public:

		Game(const InitData& init)
			: IScene{ init }
			, m_emoji{ U"🐥"_emoji }
		{
			Print << U"Game::Game()";
		}

		~Game()
		{
			Print << U"Game::~Game()";
		}

		void update() override
		{
			if (not m_stopwatch.isStarted())
			{
				m_stopwatch.start();
			}

			// On left click
			if (MouseL.down())
			{
				m_stopwatch.pause();

				// Transition to title scene
				changeScene(U"Title", 3s);
			}
		}

		void draw() const override
		{
			Scene::SetBackground(ColorF(0.0, 0.6, 0.4));

			const double t = m_stopwatch.sF();

			const Vec2 pos{ (400 + Periodic::Sine1_1(3s, t) * 300), 300 };

			m_emoji.drawAt(pos);
		}

	private:

		Texture m_emoji;

		Stopwatch m_stopwatch;
	};

	void Main()
	{
		FontAsset::Register(U"TitleFont", FontMethod::MSDF, 48, Typeface::Bold);

		// Create scene manager
		App manager;

		// Register each scene
		manager.add<Title>(U"Title");
		manager.add<Game>(U"Game");

		// Set fade-in and fade-out screen color
		manager.setFadeColor(ColorF{ 0.8, 0.9, 1.0 });

		// Explicitly specify initial scene and fade-in time
		manager.init(U"Title", 0.5s);

		while (System::Update())
		{
			// Execute current scene
			// The scene's .update() and .draw() are executed
			if (not manager.update())
			{
				break;
			}
		}
	}
	```


## 58.5 Customizing Transition Effects (2)
- To customize transition behavior in more detail, override the following member functions that are called only during transitions

| Code | Description | Default Implementation |
|---|---|---|
| `.updateFadeIn(double t)` | Update processing during fade-in | Does nothing |
| `.updateFadeOut(double t)` | Update processing during fade-out | Does nothing |
| `.drawFadeIn(double t)` | Draw processing during fade-in | Calls `.draw()` and draws fade-in color on top |
| `.drawFadeOut(double t)` | Draw processing during fade-out | Calls `.draw()` and draws fade-out color on top |

- The argument `t` is a value that increases from `0.0` at fade start to `1.0` at fade end (not in seconds)
- The following sample code overrides `.drawFadeIn()` and `.drawFadeOut()` to draw custom scene transition effects

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/5.mp4?raw=true" autoplay loop muted playsinline></video>

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// State type is String
	using App = SceneManager<String>;

	// Title scene
	class Title : public App::Scene
	{
	public:

		// Constructor (must be implemented)
		Title(const InitData& init)
			: IScene{ init }
		{
			Print << U"Title::Title()";
		}

		~Title()
		{
			Print << U"Title::~Title()";
		}

		// Update function
		void update() override
		{
			// On left click
			if (MouseL.down())
			{
				// Transition to game scene
				changeScene(U"Game", 1.5s);
			}
		}

		// Draw function
		void draw() const override
		{
			Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

			FontAsset(U"TitleFont")(U"My Game").drawAt(60, Vec2{ 400, 100 });

			Circle{ Cursor::Pos(), 50 }.draw(Palette::Seagreen);
		}

		void drawFadeIn(double t) const override
		{
			draw();

			Circle{ 400, 300, 600 }
				.drawFrame(((1 - t) * 600), 0, ColorF{ 0.2, 0.3, 0.4 });
		}

		void drawFadeOut(double t) const override
		{
			draw();

			for (int32 y = 0; y < 6; ++y)
			{
				RectF{ (800 + y * 100 - t * 1600), (y * 100), 1600, 100 }.draw(HSV{ (y * 20), 0.2, 1.0 });
			}
		}
	};

	// Game scene
	class Game : public App::Scene
	{
	public:

		Game(const InitData& init)
			: IScene{ init }
			, m_emoji{ U"🐥"_emoji }
		{
			Print << U"Game::Game()";
		}

		~Game()
		{
			Print << U"Game::~Game()";
		}

		void update() override
		{
			if (not m_stopwatch.isStarted())
			{
				m_stopwatch.start();
			}

			// On left click
			if (MouseL.down())
			{
				m_stopwatch.pause();

				// Transition to title scene
				changeScene(U"Title", 1.5s);
			}
		}

		void draw() const override
		{
			Scene::SetBackground(ColorF(0.0, 0.6, 0.4));

			const double t = m_stopwatch.sF();

			const Vec2 pos{ (400 + Periodic::Sine1_1(3s, t) * 300), 300 };

			m_emoji.drawAt(pos);
		}

		void drawFadeIn(double t) const override
		{
			draw();

			for (int32 y = 0; y < 6; ++y)
			{
				RectF{ (800 + y * 100 - (1 + t) * 1600), (y * 100), 1600, 100 }.draw(HSV{ (y * 20), 0.2, 1.0 });
			}
		}

		void drawFadeOut(double t) const override
		{
			draw();

			Circle{ 400, 300, 600 }
				.drawFrame((t * 600), 0, ColorF{ 0.2, 0.3, 0.4 });
		}

	private:

		Texture m_emoji;

		Stopwatch m_stopwatch;
	};

	void Main()
	{
		FontAsset::Register(U"TitleFont", FontMethod::MSDF, 48, Typeface::Bold);

		// Create scene manager
		App manager;

		// Register each scene
		manager.add<Title>(U"Title");
		manager.add<Game>(U"Game");

		// Set fade-in and fade-out screen color
		manager.setFadeColor(ColorF{ 0.8, 0.9, 1.0 });

		// Explicitly specify initial scene and fade-in time
		manager.init(U"Title", 0.75s);

		while (System::Update())
		{
			// Execute current scene
			// The scene's .update() and .draw() are executed
			if (not manager.update())
			{
				break;
			}
		}
	}
	```


## 58.6 Data Sharing Between Scenes
- When you have data like game scores that you want to share across scenes, add that data type as the second template argument of `SceneManager<>`
- This allows you to access that data from each scene's functions through `getData()`
- This data is initialized once when the scene manager is created and its contents are preserved even when transitioning scenes

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/6.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Shared data
	struct GameData
	{
		int32 lastScore = 0;

		int32 highScore = 0;
	};

	using App = SceneManager<String, GameData>;

	// Title scene
	class Title : public App::Scene
	{
	public:

		// Constructor (must be implemented)
		Title(const InitData& init)
			: IScene{ init } {}

		// Update function
		void update() override
		{
			// On left click
			if (MouseL.down())
			{
				// Transition to game scene
				changeScene(U"Game");
			}
		}

		// Draw function
		void draw() const override
		{
			Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

			const int32 lastScore = getData().lastScore;
			const int32 highScore = getData().highScore;

			const Font& font = FontAsset(U"TitleFont");
			font(U"My Game").drawAt(60, Vec2{ 400, 100 });

			font(U"Last Score: {}"_fmt(lastScore)).drawAt(40, Vec2{ 400, 400 }, ColorF{ 0.1 });
			font(U"High Score: {}"_fmt(highScore)).drawAt(40, Vec2{ 400, 480 }, ColorF{ 0.1 });

			Circle{ Cursor::Pos(), 50 }.draw(Palette::Seagreen);
		}
	};

	// Game scene
	class Game : public App::Scene
	{
	public:

		Game(const InitData& init)
			: IScene{ init }
			, m_emoji{ U"🐥"_emoji } {}

		void update() override
		{
			if (not m_stopwatch.isStarted())
			{
				m_stopwatch.start();
			}

			// On left click
			if (MouseL.down())
			{
				m_stopwatch.pause();

				const int32 score = m_stopwatch.ms();
				getData().lastScore = score;
				getData().highScore = Max(getData().highScore, score);

				// Transition to title scene
				changeScene(U"Title");
			}
		}

		void draw() const override
		{
			Scene::SetBackground(ColorF(0.0, 0.6, 0.4));

			const double t = m_stopwatch.sF();
			const Vec2 pos{ (400 + Periodic::Sine1_1(3s, t) * 300), 300 };
			m_emoji.drawAt(pos);

			const int32 currentScore = m_stopwatch.ms();
			const Font& font = FontAsset(U"TitleFont");
			font(U"Score: {}"_fmt(currentScore)).drawAt(40, pos.movedBy(0, 100), ColorF{ 0.1 });
		}

	private:

		Texture m_emoji;

		Stopwatch m_stopwatch;
	};

	void Main()
	{
		FontAsset::Register(U"TitleFont", FontMethod::MSDF, 48, Typeface::Bold);

		// Create scene manager
		App manager;

		// Register each scene
		manager.add<Title>(U"Title");
		manager.add<Game>(U"Game");

		while (System::Update())
		{
			// Execute current scene
			// The scene's .update() and .draw() are executed
			if (not manager.update())
			{
				break;
			}
		}
	}
	```


## 58.7 Scene Management in Practice (No File Splitting)
- A sample game consisting of three scenes: title scene, game scene, and ranking scene

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/7.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Scene states
	enum class State
	{
		Title,
		Game,
		Ranking,
	};

	// Shared data
	struct GameData
	{
		// Last game score
		int32 lastScore = 0;

		// High scores
		Array<int32> highScores = { 10, 8, 6, 4, 2 };
	};

	using App = SceneManager<State, GameData>;

	// Title scene
	class Title : public App::Scene
	{
	public:

		Title(const InitData& init)
			: IScene{ init } {}

		void update() override
		{
			// Button updates
			{
				m_startTransition.update(m_startButton.mouseOver());
				m_rankingTransition.update(m_rankingButton.mouseOver());
				m_exitTransition.update(m_exitButton.mouseOver());

				if (m_startButton.mouseOver() || m_rankingButton.mouseOver() || m_exitButton.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);
				}
			}

			// Button click handling
			if (m_startButton.leftClicked()) // To game
			{
				changeScene(State::Game);
			}
			else if (m_rankingButton.leftClicked()) // To ranking
			{
				changeScene(State::Ranking);
			}
			else if (m_exitButton.leftClicked()) // Exit
			{
				System::Exit();
			}
		}

		void draw() const override
		{
			Scene::SetBackground(ColorF{ 0.2, 0.8, 0.4 });

			// Title drawing
			FontAsset(U"TitleFont")(U"BREAKOUT")
				.drawAt(TextStyle::OutlineShadow(0.2, ColorF{ 0.2, 0.6, 0.2 }, Vec2{ 3, 3 }, ColorF{ 0.0, 0.5 }), 100, Vec2{ 400, 100 });

			// Button drawing
			{
				m_startButton.draw(ColorF{ 1.0, m_startTransition.value() }).drawFrame(2);
				m_rankingButton.draw(ColorF{ 1.0, m_rankingTransition.value() }).drawFrame(2);
				m_exitButton.draw(ColorF{ 1.0, m_exitTransition.value() }).drawFrame(2);

				const Font& boldFont = FontAsset(U"Bold");
				boldFont(U"PLAY").drawAt(36, m_startButton.center(), ColorF{ 0.1 });
				boldFont(U"RANKING").drawAt(36, m_rankingButton.center(), ColorF{ 0.1 });
				boldFont(U"EXIT").drawAt(36, m_exitButton.center(), ColorF{ 0.1 });
			}
		}

	private:

		RoundRect m_startButton{ Arg::center(400, 300), 300, 60, 8};
		RoundRect m_rankingButton{ Arg::center(400, 400), 300, 60, 8};
		RoundRect m_exitButton{ Arg::center(400, 500), 300, 60, 8 };

		Transition m_startTransition{ 0.4s, 0.2s };
		Transition m_rankingTransition{ 0.4s, 0.2s };
		Transition m_exitTransition{ 0.4s, 0.2s };
	};

	// Game scene
	class Game : public App::Scene
	{
	public:

		Game(const InitData& init)
			: IScene{ init }
		{
			for (int32 y = 0; y < 5; ++y)
			{
				for (int32 x = 0; x < (800 / BrickSize.x); ++x)
				{
					m_bricks << Rect{ (x * BrickSize.x), (60 + y * BrickSize.y), BrickSize };
				}
			}
		}

		void update() override
		{
			// Move ball
			m_ball.moveBy(m_ballVelocity * Scene::DeltaTime());

			// Check blocks in order
			for (auto it = m_bricks.begin(); it != m_bricks.end(); ++it)
			{
				// If block and ball intersect
				if (it->intersects(m_ball))
				{
					// If intersecting with top or bottom edge of block
					if (it->bottom().intersects(m_ball) || it->top().intersects(m_ball))
					{
						m_ballVelocity.y *= -1;
					}
					else // If intersecting with left or right edge of block
					{
						m_ballVelocity.x *= -1;
					}

					// Remove block from array (iterator becomes invalid)
					m_bricks.erase(it);

					m_brickSound.playOneShot(0.5);

					++m_score;

					break;
				}
			}

			// If hit ceiling
			if ((m_ball.y < 0) && (m_ballVelocity.y < 0))
			{
				m_ballVelocity.y *= -1;
			}

			// If hit left or right walls
			if (((m_ball.x < 0) && (m_ballVelocity.x < 0))
				|| ((800 < m_ball.x) && (0 < m_ballVelocity.x)))
			{
				m_ballVelocity.x *= -1;
			}

			// Bounce off paddle
			if (const Rect paddle = getPaddle();
				(0 < m_ballVelocity.y) && paddle.intersects(m_ball))
			{
				// Change bounce direction based on distance from paddle center
				m_ballVelocity = Vec2{ (m_ball.x - paddle.center().x) * 10, -m_ballVelocity.y }.setLength(BallSpeed);
			}

			// If goes off screen or no blocks left
			if ((600 < m_ball.y) || m_bricks.isEmpty())
			{
				// Go to ranking screen
				changeScene(State::Ranking);

				getData().lastScore = m_score;
			}
		}

		void draw() const override
		{
			Scene::SetBackground(ColorF{ 0.2 });

			// Draw all blocks
			for (const auto& brick : m_bricks)
			{
				brick.stretched(-1).draw(HSV{ brick.y - 40 });
			}

			// Draw ball
			m_ball.draw();

			// Draw paddle
			getPaddle().rounded(3).draw();

			// Hide mouse cursor
			Cursor::RequestStyle(CursorStyle::Hidden);

			// Draw score
			FontAsset(U"Bold")(m_score).draw(24, Vec2{ 400, 16 });
		}

	private:

		// Block size
		static constexpr Size BrickSize{ 40, 20 };

		// Ball speed
		static constexpr double BallSpeed = 480.0;

		// Ball velocity
		Vec2 m_ballVelocity{ 0, -BallSpeed };

		// Ball
		Circle m_ball{ 400, 400, 8 };

		// Block array
		Array<Rect> m_bricks;

		// Current game score
		int32 m_score = 0;

		// Sound effect when breaking blocks
		Audio m_brickSound{ GMInstrument::Woodblock, PianoKey::C5, 0.2s, 0.1s };

		Rect getPaddle() const
		{
			return{ Arg::center(Cursor::Pos().x, 500), 60, 10 };
		}
	};

	// Ranking scene
	class Ranking : public App::Scene
	{
	public:

		Ranking(const InitData& init)
			: IScene{ init }
		{
			auto& data = getData();

			if (data.lastScore)
			{
				// Reconstruct ranking
				data.highScores << data.lastScore;
				data.highScores.rsort();
				data.highScores.resize(RankingCount);

				// Set rank in m_rank if ranked
				for (int32 i = 0; i < RankingCount; ++i)
				{
					if (data.highScores[i] == data.lastScore)
					{
						m_rank = i;
						break;
					}
				}

				data.lastScore = 0;
			}
		}

		void update() override
		{
			if (MouseL.down())
			{
				// To title scene
				changeScene(State::Title);
			}
		}

		void draw() const override
		{
			Scene::SetBackground(ColorF{ 0.4, 0.6, 0.9 });
			const Font& boldFont = FontAsset(U"Bold");
			const auto& data = getData();

			boldFont(U"RANKING").drawAt(400, 60);

			// Display ranking
			for (int32 i = 0; i < RankingCount; ++i)
			{
				const RectF rect{ 100, (120 + i * 80), 600, 80 };

				rect.draw(ColorF{ 1.0, (1.0 - i * 0.2) });

				boldFont(data.highScores[i]).drawAt(rect.center(), ColorF{ 0.1 });

				// If ranked
				if (i == m_rank)
				{
					rect.drawFrame(2, 10, ColorF{ 1.0, 0.8, 0.2 });
				}
			}
		}

	private:

		static constexpr int32 RankingCount = 5;

		int32 m_rank = -1;
	};

	void Main()
	{
		FontAsset::Register(U"TitleFont", FontMethod::MSDF, 48, U"example/font/RocknRoll/RocknRollOne-Regular.ttf");
		FontAsset(U"TitleFont").setBufferThickness(4);
		
		FontAsset::Register(U"Bold", FontMethod::MSDF, 48, Typeface::Bold);

		App manager;
		manager.add<Title>(State::Title);
		manager.add<Game>(State::Game);
		manager.add<Ranking>(State::Ranking);

		while (System::Update())
		{
			if (not manager.update())
			{
				break;
			}
		}
	}
	```


## 58.8 Scene Management in Practice (File Splitting)
- An example configuration for splitting the program from **58.7** into files
- Split into a total of 8 files
	- Main.cpp
	- Common.hpp
	- Title.hpp
	- Title.cpp
	- Game.hpp
	- Game.cpp
	- Ranking.hpp
	- Ranking.cpp

!!! info "How to add project files without troubles (Windows)"
	1. Create 7 copies of `Main.cpp` in Explorer and rename each to their respective file names. This avoids character encoding troubles with the created source files
	2. Drag and drop the 7 files from Explorer to the project name section in Visual Studio's Solution Explorer. This makes the new source files participate in the project and become build targets

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/8.png)

??? memo "Main.cpp"
	```cpp
	# include "Common.hpp"
	# include "Title.hpp"
	# include "Game.hpp"
	# include "Ranking.hpp"

	void Main()
	{
		FontAsset::Register(U"TitleFont", FontMethod::MSDF, 48, U"example/font/RocknRoll/RocknRollOne-Regular.ttf");
		FontAsset(U"TitleFont").setBufferThickness(4);

		FontAsset::Register(U"Bold", FontMethod::MSDF, 48, Typeface::Bold);

		App manager;
		manager.add<Title>(State::Title);
		manager.add<Game>(State::Game);
		manager.add<Ranking>(State::Ranking);

		while (System::Update())
		{
			if (not manager.update())
			{
				break;
			}
		}
	}
	```

??? memo "Common.hpp"
	```cpp
	# pragma once
	# include <Siv3D.hpp>

	// Scene states
	enum class State
	{
		Title,
		Game,
		Ranking,
	};

	// Shared data
	struct GameData
	{
		// Last game score
		int32 lastScore = 0;

		// High scores
		Array<int32> highScores = { 10, 8, 6, 4, 2 };
	};

	using App = SceneManager<State, GameData>;
	```

??? memo "Title.hpp"
	```cpp
	# pragma once
	# include "Common.hpp"

	// Title scene
	class Title : public App::Scene
	{
	public:

		Title(const InitData& init);

		void update() override;

		void draw() const override;

	private:

		RoundRect m_startButton{ Arg::center(400, 300), 300, 60, 8 };
		RoundRect m_rankingButton{ Arg::center(400, 400), 300, 60, 8 };
		RoundRect m_exitButton{ Arg::center(400, 500), 300, 60, 8 };

		Transition m_startTransition{ 0.4s, 0.2s };
		Transition m_rankingTransition{ 0.4s, 0.2s };
		Transition m_exitTransition{ 0.4s, 0.2s };
	};
	```

??? memo "Title.cpp"
	```cpp
	# include "Title.hpp"

	Title::Title(const InitData& init)
		: IScene{ init }
	{

	}

	void Title::update()
	{
		// Button updates
		{
			m_startTransition.update(m_startButton.mouseOver());
			m_rankingTransition.update(m_rankingButton.mouseOver());
			m_exitTransition.update(m_exitButton.mouseOver());

			if (m_startButton.mouseOver() || m_rankingButton.mouseOver() || m_exitButton.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}
		}

		// Button click handling
		if (m_startButton.leftClicked()) // To game
		{
			changeScene(State::Game);
		}
		else if (m_rankingButton.leftClicked()) // To ranking
		{
			changeScene(State::Ranking);
		}
		else if (m_exitButton.leftClicked()) // Exit
		{
			System::Exit();
		}
	}

	void Title::draw() const
	{
		Scene::SetBackground(ColorF{ 0.2, 0.8, 0.4 });

		// Title drawing
		FontAsset(U"TitleFont")(U"BREAKOUT")
			.drawAt(TextStyle::OutlineShadow(0.2, ColorF{ 0.2, 0.6, 0.2 }, Vec2{ 3, 3 }, ColorF{ 0.0, 0.5 }), 100, Vec2{ 400, 100 });

		// Button drawing
		{
			m_startButton.draw(ColorF{ 1.0, m_startTransition.value() }).drawFrame(2);
			m_rankingButton.draw(ColorF{ 1.0, m_rankingTransition.value() }).drawFrame(2);
			m_exitButton.draw(ColorF{ 1.0, m_exitTransition.value() }).drawFrame(2);

			const Font& boldFont = FontAsset(U"Bold");
			boldFont(U"PLAY").drawAt(36, m_startButton.center(), ColorF{ 0.1 });
			boldFont(U"RANKING").drawAt(36, m_rankingButton.center(), ColorF{ 0.1 });
			boldFont(U"EXIT").drawAt(36, m_exitButton.center(), ColorF{ 0.1 });
		}
	}
	```

??? memo "Game.hpp"
	```cpp
	# pragma once
	# include "Common.hpp"

	// Game scene
	class Game : public App::Scene
	{
	public:

		Game(const InitData& init);

		void update() override;

		void draw() const override;

	private:

		// Block size
		static constexpr Size BrickSize{ 40, 20 };

		// Ball speed
		static constexpr double BallSpeed = 480.0;

		// Ball velocity
		Vec2 m_ballVelocity{ 0, -BallSpeed };

		// Ball
		Circle m_ball{ 400, 400, 8 };

		// Block array
		Array<Rect> m_bricks;

		// Current game score
		int32 m_score = 0;

		// Sound effect when breaking blocks
		Audio m_brickSound{ GMInstrument::Woodblock, PianoKey::C5, 0.2s, 0.1s };

		Rect getPaddle() const;
	};
	```

??? memo "Game.cpp"
	```cpp
	# include "Game.hpp"

	Game::Game(const InitData& init)
		: IScene{ init }
	{
		for (int32 y = 0; y < 5; ++y)
		{
			for (int32 x = 0; x < (800 / BrickSize.x); ++x)
			{
				m_bricks << Rect{ (x * BrickSize.x), (60 + y * BrickSize.y), BrickSize };
			}
		}
	}

	void Game::update()
	{
		// Move ball
		m_ball.moveBy(m_ballVelocity * Scene::DeltaTime());

		// Check blocks in order
		for (auto it = m_bricks.begin(); it != m_bricks.end(); ++it)
		{
			// If block and ball intersect
			if (it->intersects(m_ball))
			{
				// If intersecting with top or bottom edge of block
				if (it->bottom().intersects(m_ball) || it->top().intersects(m_ball))
				{
					m_ballVelocity.y *= -1;
				}
				else // If intersecting with left or right edge of block
				{
					m_ballVelocity.x *= -1;
				}

				// Remove block from array (iterator becomes invalid)
				m_bricks.erase(it);

				m_brickSound.playOneShot(0.5);

				++m_score;

				break;
			}
		}

		// If hit ceiling
		if ((m_ball.y < 0) && (m_ballVelocity.y < 0))
		{
			m_ballVelocity.y *= -1;
		}

		// If hit left or right walls
		if (((m_ball.x < 0) && (m_ballVelocity.x < 0))
			|| ((800 < m_ball.x) && (0 < m_ballVelocity.x)))
		{
			m_ballVelocity.x *= -1;
		}

		// Bounce off paddle
		if (const Rect paddle = getPaddle();
			(0 < m_ballVelocity.y) && paddle.intersects(m_ball))
		{
			// Change bounce direction based on distance from paddle center
			m_ballVelocity = Vec2{ (m_ball.x - paddle.center().x) * 10, -m_ballVelocity.y }.setLength(BallSpeed);
		}

		// If goes off screen or no blocks left
		if ((600 < m_ball.y) || m_bricks.isEmpty())
		{
			// Go to ranking screen
			changeScene(State::Ranking);

			getData().lastScore = m_score;
		}
	}

	void Game::draw() const
	{
		Scene::SetBackground(ColorF{ 0.2 });

		// Draw all blocks
		for (const auto& brick : m_bricks)
		{
			brick.stretched(-1).draw(HSV{ brick.y - 40 });
		}

		// Draw ball
		m_ball.draw();

		// Draw paddle
		getPaddle().rounded(3).draw();

		// Hide mouse cursor
		Cursor::RequestStyle(CursorStyle::Hidden);

		// Draw score
		FontAsset(U"Bold")(m_score).draw(24, Vec2{ 400, 16 });
	}

	Rect Game::getPaddle() const
	{
		return{ Arg::center(Cursor::Pos().x, 500), 60, 10 };
	}
	```

??? memo "Ranking.hpp"
	```cpp
	# pragma once
	# include "Common.hpp"

	// Ranking scene
	class Ranking : public App::Scene
	{
	public:

		Ranking(const InitData& init);

		void update() override;

		void draw() const override;

	private:

		static constexpr int32 RankingCount = 5;

		int32 m_rank = -1;
	};
	```

??? memo "Ranking.cpp"
	```cpp
	# include "Ranking.hpp"

	Ranking::Ranking(const InitData& init)
		: IScene{ init }
	{
		auto& data = getData();

		if (data.lastScore)
		{
			// Reconstruct ranking
			data.highScores << data.lastScore;
			data.highScores.rsort();
			data.highScores.resize(RankingCount);

			// Set rank in m_rank if ranked
			for (int32 i = 0; i < RankingCount; ++i)
			{
				if (data.highScores[i] == data.lastScore)
				{
					m_rank = i;
					break;
				}
			}

			data.lastScore = 0;
		}
	}

	void Ranking::update()
	{
		if (MouseL.down())
		{
			// To title scene
			changeScene(State::Title);
		}
	}

	void Ranking::draw() const
	{
		Scene::SetBackground(ColorF{ 0.4, 0.6, 0.9 });
		const Font& boldFont = FontAsset(U"Bold");
		const auto& data = getData();

		boldFont(U"RANKING").drawAt(400, 60);

		// Display ranking
		for (int32 i = 0; i < RankingCount; ++i)
		{
			const RectF rect{ 100, (120 + i * 80), 600, 80 };

			rect.draw(ColorF{ 1.0, (1.0 - i * 0.2) });

			boldFont(data.highScores[i]).drawAt(rect.center(), ColorF{ 0.1 });

			// If ranked
			if (i == m_rank)
			{
				rect.drawFrame(2, 10, ColorF{ 1.0, 0.8, 0.2 });
			}
		}
	}
	```