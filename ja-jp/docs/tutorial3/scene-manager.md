# 58. シーン管理
ゲームのタイトル、ゲームプレイ、リザルトなど、個々の場面（シーン）を個別のクラスに実装し、それらを行き来することで全体の流れを構成する「シーン管理」のための機能を学びます。


## 58.1 シーン管理の概要
- シーン管理を使うと、複雑なアプリケーション（とくにゲーム）を効率よく開発できます
- シーン管理では、ゲームのタイトル、ゲームプレイ、リザルトなど、個々の場面（シーン）を個別のクラスに実装し、通常はそのうちの 1 つのシーンを実行します
- シーン管理機能 `SceneManager` を使うと、シーン間でデータを共有したり、遷移先のシーンを指定して滑らかに画面を切り替えたりする処理を簡単に記述できます

<div class="noshadow-90"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/1.png"></div>

!!! warning "「シーン」という言葉について"
	- シーン管理における「シーン」とは、個々のゲームの場面や、その実装クラスのことを指します
	- **チュートリアル 9** で説明した、画面のことを表すシーンや、`Scene::` 名前空間の機能とは異なる概念です


## 58.2 シーン管理の基本
- まずは、個々のシーンを区別する値（ステート）の型を決めます
	- `String` 型を選択した場合、タイトルシーンは `U"Title"`, ゲームシーンは `U"Game"` のように、`String` 型の値で個々のシーンを区別することになります
	- 方針に応じて、`enum class` や `int32` など、他の型を選択することもできます
- 次に、`using App = SceneManager<ステートの型>;` で、シーンマネージャークラスの型を決定し、`App` と名付けます
- 各シーンのクラスを `App::Scene` を継承して実装します
	- 通常は、コンストラクタ、`.update()`, `.draw()` の 3 つのメンバ関数を実装します
- `Main()` 関数に `App` 型のオブジェクトを作成し、各シーンを `.add()` で登録します
- メインループの中で `App::update()` を毎フレーム呼び出すと、最初に登録したシーンが自動的に実行されます
	- シーンに実装した `.update()` と `.draw()` 関数がここで呼ばれます
- 最も簡単な例として、シーンが 1 つだけ（タイトルシーンだけ）のサンプルを次に示します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/2.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// ステートの型は String
	using App = SceneManager<String>;

	// タイトルシーン
	class Title : public App::Scene
	{
	public:

		// コンストラクタ（必ず実装する）
		Title(const InitData& init)
			: IScene{ init }
		{

		}

		// 更新関数
		void update() override
		{

		}

		// 描画関数
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

		// シーンマネージャーを作成
		App manager;

		// タイトルシーン（名前は "Title"）を登録する
		manager.add<Title>(U"Title");

		while (System::Update())
		{
			// 現在のシーンを実行する
			// シーンに実装した .update() と .draw() が実行される
			if (not manager.update())
			{
				break;
			}
		}
	}
	```


## 58.3 シーン遷移
- **58.2** のサンプルに、新しいシーン（ゲームシーン）を追加します
- あるシーンの実行中に、別のシーンに遷移したい場合は、シーンの `.update()` 関数内で `.changeScene(次のシーンのステート)` を呼び、行きたい先のシーンを指定します
- シーンの遷移は、デフォルトでは 2 秒間のフェードイン・フェードアウト効果とともに行われます
	- フェードイン・フェードアウトの時間や色はカスタマイズできます（**58.4**）
- シーンを遷移するたび、古いシーンのインスタンスは破棄され、新しいシーンのクラスがインスタンス化されます
- フェードイン・フェードアウト中は、シーンの `.update()` は呼ばれず、`.draw()` だけが呼ばれます

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/3.mp4?raw=true" autoplay loop muted playsinline></video>

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// ステートの型は String
	using App = SceneManager<String>;

	// タイトルシーン
	class Title : public App::Scene
	{
	public:

		// コンストラクタ（必ず実装する）
		Title(const InitData& init)
			: IScene{ init }
		{
			Print << U"Title::Title()";
		}

		~Title()
		{
			Print << U"Title::~Title()";
		}

		// 更新関数
		void update() override
		{
			// 左クリックで
			if (MouseL.down())
			{
				// ゲームシーンに遷移
				changeScene(U"Game");
			}
		}

		// 描画関数
		void draw() const override
		{
			Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

			FontAsset(U"TitleFont")(U"My Game").drawAt(60, Vec2{ 400, 100 });

			Circle{ Cursor::Pos(), 50 }.draw(Palette::Seagreen);
		}
	};

	// ゲームシーン
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

			// 左クリックで
			if (MouseL.down())
			{
				m_stopwatch.pause();

				// タイトルシーンに遷移
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

		// シーンマネージャーを作成
		App manager;

		// 各シーンを登録する
		manager.add<Title>(U"Title");
		manager.add<Game>(U"Game");

		while (System::Update())
		{
			// 現在のシーンを実行する
			// シーンに実装した .update() と .draw() が実行される
			if (not manager.update())
			{
				break;
			}
		}
	}
	```


## 58.4 遷移演出のカスタマイズ（1）
- フェードイン・フェードアウト時の画面の色を変更するには `SceneManager` の `.setFadeColor(color)` を呼びます
- シーンの切り替え時間をカスタマイズするには、`.changeScene(次のシーンのステート, 遷移時間)` を使います（デフォルトでは 2 秒）
- 最初のシーンのフェードインについては `.changeScene()` が使えないため、代わりに `SceneManager` の `.init(state, 遷移時間)` を使います

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/4.mp4?raw=true" autoplay loop muted playsinline></video>

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// ステートの型は String
	using App = SceneManager<String>;

	// タイトルシーン
	class Title : public App::Scene
	{
	public:

		// コンストラクタ（必ず実装する）
		Title(const InitData& init)
			: IScene{ init }
		{
			Print << U"Title::Title()";
		}

		~Title()
		{
			Print << U"Title::~Title()";
		}

		// 更新関数
		void update() override
		{
			// 左クリックで
			if (MouseL.down())
			{
				// ゲームシーンに遷移
				changeScene(U"Game", 0.5s);
			}
		}

		// 描画関数
		void draw() const override
		{
			Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

			FontAsset(U"TitleFont")(U"My Game").drawAt(60, Vec2{ 400, 100 });

			Circle{ Cursor::Pos(), 50 }.draw(Palette::Seagreen);
		}
	};

	// ゲームシーン
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

			// 左クリックで
			if (MouseL.down())
			{
				m_stopwatch.pause();

				// タイトルシーンに遷移
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

		// シーンマネージャーを作成
		App manager;

		// 各シーンを登録する
		manager.add<Title>(U"Title");
		manager.add<Game>(U"Game");

		// フェードイン・フェードアウト時の画面の色を設定する
		manager.setFadeColor(ColorF{ 0.8, 0.9, 1.0 });

		// 最初のシーンとフェードイン時間を明示的に指定する
		manager.init(U"Title", 0.5s);

		while (System::Update())
		{
			// 現在のシーンを実行する
			// シーンに実装した .update() と .draw() が実行される
			if (not manager.update())
			{
				break;
			}
		}
	}
	```


## 58.5 遷移演出のカスタマイズ（2）
- 遷移中の挙動をより詳細にカスタマイズするには、遷移中限定で呼ばれる次のメンバ関数をオーバーライドします

| コード | 説明 | デフォルトの実装 |
|---|---|---|
| `.updateFadeIn(double t)` | フェードイン中の更新処理 | 何もしない |
| `.updateFadeOut(double t)` | フェードアウト中の更新処理 | 何もしない |
| `.drawFadeIn(double t)` | フェードイン中の描画処理 | `.draw()` を呼び、その上にフェードインの色を描画する |
| `.drawFadeOut(double t)` | フェードアウト中の描画処理 | `.draw()` を呼び、その上にフェードアウトの色を描画する |

- 引数 `t` は、フェード開始時に `0.0`, 終了時に `1.0` になるよう増えていく値です（単位は秒ではありません）
- 次のサンプルコードは、`.drawFadeIn()`, `.drawFadeOut()` をオーバーライドして、独自のシーン切り替えエフェクトを描画しています

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/5.mp4?raw=true" autoplay loop muted playsinline></video>

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// ステートの型は String
	using App = SceneManager<String>;

	// タイトルシーン
	class Title : public App::Scene
	{
	public:

		// コンストラクタ（必ず実装する）
		Title(const InitData& init)
			: IScene{ init }
		{
			Print << U"Title::Title()";
		}

		~Title()
		{
			Print << U"Title::~Title()";
		}

		// 更新関数
		void update() override
		{
			// 左クリックで
			if (MouseL.down())
			{
				// ゲームシーンに遷移
				changeScene(U"Game", 1.5s);
			}
		}

		// 描画関数
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

	// ゲームシーン
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

			// 左クリックで
			if (MouseL.down())
			{
				m_stopwatch.pause();

				// タイトルシーンに遷移
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

		// シーンマネージャーを作成
		App manager;

		// 各シーンを登録する
		manager.add<Title>(U"Title");
		manager.add<Game>(U"Game");

		// フェードイン・フェードアウト時の画面の色を設定する
		manager.setFadeColor(ColorF{ 0.8, 0.9, 1.0 });

		// 最初のシーンとフェードイン時間を明示的に指定する
		manager.init(U"Title", 0.75s);

		while (System::Update())
		{
			// 現在のシーンを実行する
			// シーンに実装した .update() と .draw() が実行される
			if (not manager.update())
			{
				break;
			}
		}
	}
	```


## 58.6 シーン間でのデータ共有
- ゲームのスコアの情報など、シーンをまたいで共有したいデータがある場合、そのデータ型を `SceneManager<>` の 2 つ目のテンプレート引数に追加します
- そうすることで、各シーンの関数から `getData()` を通してそのデータにアクセスできるようになります
- このデータはシーンマネージャーの作成時に 1 度だけ初期化され、シーン遷移をしても内容は保持されます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/6.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// 共有するデータ
	struct GameData
	{
		int32 lastScore = 0;

		int32 highScore = 0;
	};

	using App = SceneManager<String, GameData>;

	// タイトルシーン
	class Title : public App::Scene
	{
	public:

		// コンストラクタ（必ず実装する）
		Title(const InitData& init)
			: IScene{ init } {}

		// 更新関数
		void update() override
		{
			// 左クリックで
			if (MouseL.down())
			{
				// ゲームシーンに遷移
				changeScene(U"Game");
			}
		}

		// 描画関数
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

	// ゲームシーン
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

			// 左クリックで
			if (MouseL.down())
			{
				m_stopwatch.pause();

				const int32 score = m_stopwatch.ms();
				getData().lastScore = score;
				getData().highScore = Max(getData().highScore, score);

				// タイトルシーンに遷移
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

		// シーンマネージャーを作成
		App manager;

		// 各シーンを登録する
		manager.add<Title>(U"Title");
		manager.add<Game>(U"Game");

		while (System::Update())
		{
			// 現在のシーンを実行する
			// シーンに実装した .update() と .draw() が実行される
			if (not manager.update())
			{
				break;
			}
		}
	}
	```


## 58.7 シーン管理の実践（ファイル分割なし）
- タイトルシーン、ゲームシーン、ランキングシーンの 3 つのシーンで構成されるゲームのサンプルです

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene-manager/7.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// シーンのステート
	enum class State
	{
		Title,
		Game,
		Ranking,
	};

	// 共有するデータ
	struct GameData
	{
		// 直前のゲームのスコア
		int32 lastScore = 0;

		// ハイスコア
		Array<int32> highScores = { 10, 8, 6, 4, 2 };
	};

	using App = SceneManager<State, GameData>;

	// タイトルシーン
	class Title : public App::Scene
	{
	public:

		Title(const InitData& init)
			: IScene{ init } {}

		void update() override
		{
			// ボタンの更新
			{
				m_startTransition.update(m_startButton.mouseOver());
				m_rankingTransition.update(m_rankingButton.mouseOver());
				m_exitTransition.update(m_exitButton.mouseOver());

				if (m_startButton.mouseOver() || m_rankingButton.mouseOver() || m_exitButton.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);
				}
			}

			// ボタンのクリック処理
			if (m_startButton.leftClicked()) // ゲームへ
			{
				changeScene(State::Game);
			}
			else if (m_rankingButton.leftClicked()) // ランキングへ
			{
				changeScene(State::Ranking);
			}
			else if (m_exitButton.leftClicked()) // 終了
			{
				System::Exit();
			}
		}

		void draw() const override
		{
			Scene::SetBackground(ColorF{ 0.2, 0.8, 0.4 });

			// タイトル描画
			FontAsset(U"TitleFont")(U"BREAKOUT")
				.drawAt(TextStyle::OutlineShadow(0.2, ColorF{ 0.2, 0.6, 0.2 }, Vec2{ 3, 3 }, ColorF{ 0.0, 0.5 }), 100, Vec2{ 400, 100 });

			// ボタン描画
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

	// ゲームシーン
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
			// ボールを移動させる
			m_ball.moveBy(m_ballVelocity * Scene::DeltaTime());

			// ブロックを順にチェックする
			for (auto it = m_bricks.begin(); it != m_bricks.end(); ++it)
			{
				// ブロックとボールが交差していたら
				if (it->intersects(m_ball))
				{
					// ブロックの上辺、または底辺と交差していたら
					if (it->bottom().intersects(m_ball) || it->top().intersects(m_ball))
					{
						m_ballVelocity.y *= -1;
					}
					else // ブロックの左辺または右辺と交差していたら
					{
						m_ballVelocity.x *= -1;
					}

					// ブロックを配列から削除する（イテレータは無効になる）
					m_bricks.erase(it);

					m_brickSound.playOneShot(0.5);

					++m_score;

					break;
				}
			}

			// 天井にぶつかったら
			if ((m_ball.y < 0) && (m_ballVelocity.y < 0))
			{
				m_ballVelocity.y *= -1;
			}

			// 左右の壁にぶつかったら
			if (((m_ball.x < 0) && (m_ballVelocity.x < 0))
				|| ((800 < m_ball.x) && (0 < m_ballVelocity.x)))
			{
				m_ballVelocity.x *= -1;
			}

			// パドルにあたったらはね返る
			if (const Rect paddle = getPaddle();
				(0 < m_ballVelocity.y) && paddle.intersects(m_ball))
			{
				// パドルの中心からの距離に応じてはね返る方向を変える
				m_ballVelocity = Vec2{ (m_ball.x - paddle.center().x) * 10, -m_ballVelocity.y }.setLength(BallSpeed);
			}

			// 画面外に出るか、ブロックが無くなったら
			if ((600 < m_ball.y) || m_bricks.isEmpty())
			{
				// ランキング画面へ
				changeScene(State::Ranking);

				getData().lastScore = m_score;
			}
		}

		void draw() const override
		{
			Scene::SetBackground(ColorF{ 0.2 });

			// すべてのブロックを描画する
			for (const auto& brick : m_bricks)
			{
				brick.stretched(-1).draw(HSV{ brick.y - 40 });
			}

			// ボールを描く
			m_ball.draw();

			// パドルを描く
			getPaddle().rounded(3).draw();

			// マウスカーソルを非表示にする
			Cursor::RequestStyle(CursorStyle::Hidden);

			// スコアを描く
			FontAsset(U"Bold")(m_score).draw(24, Vec2{ 400, 16 });
		}

	private:

		// ブロックのサイズ
		static constexpr Size BrickSize{ 40, 20 };

		// ボールの速さ
		static constexpr double BallSpeed = 480.0;

		// ボールの速度
		Vec2 m_ballVelocity{ 0, -BallSpeed };

		// ボール
		Circle m_ball{ 400, 400, 8 };

		// ブロックの配列
		Array<Rect> m_bricks;

		// 現在のゲームのスコア
		int32 m_score = 0;

		// ブロックを壊したときの効果音
		Audio m_brickSound{ GMInstrument::Woodblock, PianoKey::C5, 0.2s, 0.1s };

		Rect getPaddle() const
		{
			return{ Arg::center(Cursor::Pos().x, 500), 60, 10 };
		}
	};

	// ランキングシーン
	class Ranking : public App::Scene
	{
	public:

		Ranking(const InitData& init)
			: IScene{ init }
		{
			auto& data = getData();

			if (data.lastScore)
			{
				// ランキングを再構成
				data.highScores << data.lastScore;
				data.highScores.rsort();
				data.highScores.resize(RankingCount);

				// ランクインしていたら m_rank に順位をセット
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
				// タイトルシーンへ
				changeScene(State::Title);
			}
		}

		void draw() const override
		{
			Scene::SetBackground(ColorF{ 0.4, 0.6, 0.9 });
			const Font& boldFont = FontAsset(U"Bold");
			const auto& data = getData();

			boldFont(U"RANKING").drawAt(400, 60);

			// ランキングを表示
			for (int32 i = 0; i < RankingCount; ++i)
			{
				const RectF rect{ 100, (120 + i * 80), 600, 80 };

				rect.draw(ColorF{ 1.0, (1.0 - i * 0.2) });

				boldFont(data.highScores[i]).drawAt(rect.center(), ColorF{ 0.1 });

				// ランクインしていたら
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


## 58.8 シーン管理の実践（ファイル分割）
- **58.7** のプログラムをファイル分割する場合の構成例です
- 全部で 8 つのファイルに分割します
	- Main.cpp
	- Common.hpp
	- Title.hpp
	- Title.cpp
	- Game.hpp
	- Game.cpp
	- Ranking.hpp
	- Ranking.cpp

!!! info "トラブルなくプロジェクトのファイルを増やす方法（Windows）"
	1. エクスプローラー上で `Main.cpp` のコピーを 7 つ作成し、それぞれのファイル名にリネームします。これにより、作成したソースファイルの文字コードのトラブルを回避できます
	2. Visual Studio のソリューションエクスプローラーのプロジェクト名の部分に、エクスプローラー から 7 つのファイルをドラッグ＆ドロップします。これにより、新しいソースファイルがプロジェクトに参加し、ビルド対象となります

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

	// シーンのステート
	enum class State
	{
		Title,
		Game,
		Ranking,
	};

	// 共有するデータ
	struct GameData
	{
		// 直前のゲームのスコア
		int32 lastScore = 0;

		// ハイスコア
		Array<int32> highScores = { 10, 8, 6, 4, 2 };
	};

	using App = SceneManager<State, GameData>;
	```

??? memo "Title.hpp"
	```cpp
	# pragma once
	# include "Common.hpp"

	// タイトルシーン
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
		// ボタンの更新
		{
			m_startTransition.update(m_startButton.mouseOver());
			m_rankingTransition.update(m_rankingButton.mouseOver());
			m_exitTransition.update(m_exitButton.mouseOver());

			if (m_startButton.mouseOver() || m_rankingButton.mouseOver() || m_exitButton.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}
		}

		// ボタンのクリック処理
		if (m_startButton.leftClicked()) // ゲームへ
		{
			changeScene(State::Game);
		}
		else if (m_rankingButton.leftClicked()) // ランキングへ
		{
			changeScene(State::Ranking);
		}
		else if (m_exitButton.leftClicked()) // 終了
		{
			System::Exit();
		}
	}

	void Title::draw() const
	{
		Scene::SetBackground(ColorF{ 0.2, 0.8, 0.4 });

		// タイトル描画
		FontAsset(U"TitleFont")(U"BREAKOUT")
			.drawAt(TextStyle::OutlineShadow(0.2, ColorF{ 0.2, 0.6, 0.2 }, Vec2{ 3, 3 }, ColorF{ 0.0, 0.5 }), 100, Vec2{ 400, 100 });

		// ボタン描画
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

	// ゲームシーン
	class Game : public App::Scene
	{
	public:

		Game(const InitData& init);

		void update() override;

		void draw() const override;

	private:

		// ブロックのサイズ
		static constexpr Size BrickSize{ 40, 20 };

		// ボールの速さ
		static constexpr double BallSpeed = 480.0;

		// ボールの速度
		Vec2 m_ballVelocity{ 0, -BallSpeed };

		// ボール
		Circle m_ball{ 400, 400, 8 };

		// ブロックの配列
		Array<Rect> m_bricks;

		// 現在のゲームのスコア
		int32 m_score = 0;

		// ブロックを壊したときの効果音
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
		// ボールを移動させる
		m_ball.moveBy(m_ballVelocity * Scene::DeltaTime());

		// ブロックを順にチェックする
		for (auto it = m_bricks.begin(); it != m_bricks.end(); ++it)
		{
			// ブロックとボールが交差していたら
			if (it->intersects(m_ball))
			{
				// ブロックの上辺、または底辺と交差していたら
				if (it->bottom().intersects(m_ball) || it->top().intersects(m_ball))
				{
					m_ballVelocity.y *= -1;
				}
				else // ブロックの左辺または右辺と交差していたら
				{
					m_ballVelocity.x *= -1;
				}

				// ブロックを配列から削除する（イテレータは無効になる）
				m_bricks.erase(it);

				m_brickSound.playOneShot(0.5);

				++m_score;

				break;
			}
		}

		// 天井にぶつかったら
		if ((m_ball.y < 0) && (m_ballVelocity.y < 0))
		{
			m_ballVelocity.y *= -1;
		}

		// 左右の壁にぶつかったら
		if (((m_ball.x < 0) && (m_ballVelocity.x < 0))
			|| ((800 < m_ball.x) && (0 < m_ballVelocity.x)))
		{
			m_ballVelocity.x *= -1;
		}

		// パドルにあたったらはね返る
		if (const Rect paddle = getPaddle();
			(0 < m_ballVelocity.y) && paddle.intersects(m_ball))
		{
			// パドルの中心からの距離に応じてはね返る方向を変える
			m_ballVelocity = Vec2{ (m_ball.x - paddle.center().x) * 10, -m_ballVelocity.y }.setLength(BallSpeed);
		}

		// 画面外に出るか、ブロックが無くなったら
		if ((600 < m_ball.y) || m_bricks.isEmpty())
		{
			// ランキング画面へ
			changeScene(State::Ranking);

			getData().lastScore = m_score;
		}
	}

	void Game::draw() const
	{
		Scene::SetBackground(ColorF{ 0.2 });

		// すべてのブロックを描画する
		for (const auto& brick : m_bricks)
		{
			brick.stretched(-1).draw(HSV{ brick.y - 40 });
		}

		// ボールを描く
		m_ball.draw();

		// パドルを描く
		getPaddle().rounded(3).draw();

		// マウスカーソルを非表示にする
		Cursor::RequestStyle(CursorStyle::Hidden);

		// スコアを描く
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

	// ランキングシーン
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
			// ランキングを再構成
			data.highScores << data.lastScore;
			data.highScores.rsort();
			data.highScores.resize(RankingCount);

			// ランクインしていたら m_rank に順位をセット
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
			// タイトルシーンへ
			changeScene(State::Title);
		}
	}

	void Ranking::draw() const
	{
		Scene::SetBackground(ColorF{ 0.4, 0.6, 0.9 });
		const Font& boldFont = FontAsset(U"Bold");
		const auto& data = getData();

		boldFont(U"RANKING").drawAt(400, 60);

		// ランキングを表示
		for (int32 i = 0; i < RankingCount; ++i)
		{
			const RectF rect{ 100, (120 + i * 80), 600, 80 };

			rect.draw(ColorF{ 1.0, (1.0 - i * 0.2) });

			boldFont(data.highScores[i]).drawAt(rect.center(), ColorF{ 0.1 });

			// ランクインしていたら
			if (i == m_rank)
			{
				rect.drawFrame(2, 10, ColorF{ 1.0, 0.8, 0.2 });
			}
		}
	}
	```
