
# 22. Scene management

シーン管理（または **シーン遷移**）を使うと、複雑なアプリ（とくにゲーム）を効率よく開発できます。シーン管理では、ゲームのタイトル、ゲームプレイ、リザルトなど、個々の場面（シーン）を個別のクラスに実装し、それらを行き来することで全体の流れを設計します。Siv3D の `SceneManager` クラスを使うと、ある場面のクラスから別の場面のクラスにデータを引き継いだり、フェードイン・フェードアウトで滑らかに画面を切り替えたりする処理が自動的に提供されます。

!!! note
    シーン管理における「シーン」とは、個々の場面や、その実装クラスを指します。10 章で説明したシーンや `Scene::` 名前空間の機能とは別の概念です。

## 22.1 シーンの基本

個々のシーンを区別する値（ステート）の型を決めます。例えば `String` 型を使うと、タイトルシーンは `U"Title"`, ゲームシーンは `U"Game"` といった名前を付けて区別できます。以下のサンプルではクラス名と名前を一致させていますが、必ずしも従う必要はありません。

まず、ステートの型を使い `using App = SceneManager<String>;` のようにしてシーンマネージャークラスの型を決定します。次に、シーンを `App::Scene` を継承して実装します。通常はコンストラクタ、`update()`, `draw()` の 3 つのメンバ関数をオーバーライドします。

`Main()` 関数には `App` 型の変数を作り、用意したシーンクラスを `.add()` で登録します。あとはメインループの中で `App::update()` を毎フレーム呼び出せば、最初に登録したシーンが自動的に実行されます。シーンでは毎フレーム `update()` 関数が先に呼ばれ、その次に `draw()` 関数が呼ばれます。


```C++ hl_lines="3 6 11 18 24 39 42 47"
# include <Siv3D.hpp>

using App = SceneManager<String>;

// タイトルシーン
class Title : public App::Scene
{
public:

    // コンストラクタ（必ず実装）
	Title(const InitData& init)
		: IScene(init)
	{

	}

    // 更新関数
	void update() override
	{

	}

    // 描画関数 (const 修飾)
	void draw() const override
	{
		Scene::SetBackground(ColorF(0.3, 0.4, 0.5));

		FontAsset(U"TitleFont")(U"My Game").drawAt(400, 100);

		Circle(Cursor::Pos(), 50).draw(Palette::Orange);
	}
};

void Main()
{
	FontAsset::Register(U"TitleFont", 60, Typeface::Heavy);

    // シーンマネージャーを作成
	App manager;

    // タイトルシーン（名前は U"Title"）を登録
	manager.add<Title>(U"Title");

	while (System::Update())
	{
        // 現在のシーンを実行
		if (!manager.update())
		{
			break;
		}
	}
}
```

## 22.2 シーン遷移

先ほどのサンプルに新しいシーンを追加します。あるシーンの実行中に、別のシーンに遷移したい場合は、シーンの `update()` 関数内で `changeScene()` を呼び、行きたい先のシーン名を指定します。プログラムの実行中は、シーンを遷移するたび、古いシーンのクラスは破棄され、新しいシーンのクラスが作成されます。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/22/s1.mp4?raw=true" autoplay loop muted></video>

```C++ hl_lines="24 40 61 84"
# include <Siv3D.hpp>

using App = SceneManager<String>;

// タイトルシーン
class Title : public App::Scene
{
public:

	// コンストラクタ（必ず実装）
	Title(const InitData& init)
		: IScene(init)
	{

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

	// 描画関数 (const 修飾)
	void draw() const override
	{
		Scene::SetBackground(ColorF(0.3, 0.4, 0.5));

		FontAsset(U"TitleFont")(U"My Game").drawAt(400, 100);

		Circle(Cursor::Pos(), 50).draw(Palette::Orange);
	}
};

// ゲームシーン
class Game : public App::Scene
{
private:

	Texture m_texture;

public:

	Game(const InitData& init)
		: IScene(init)
		, m_texture(Emoji(U"🐈"))
	{

	}

	void update() override
	{
		// 左クリックで
		if (MouseL.down())
		{
			// タイトルシーンに遷移
			changeScene(U"Title");
		}
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF(0.2, 0.8, 0.6));

		m_texture.drawAt(Cursor::Pos());
	}
};

void Main()
{
	FontAsset::Register(U"TitleFont", 60, Typeface::Heavy);

	// シーンマネージャーを作成
	App manager;

	// タイトルシーン（名前は U"Title"）を登録
	manager.add<Title>(U"Title");

	// ゲームシーン（名前は U"Game"）を登録
	manager.add<Game>(U"Game");

	while (System::Update())
	{
		// 現在のシーンを実行
		if (!manager.update())
		{
			break;
		}
	}
}
```

## 22.3 シーン間でデータを共有

ゲームのスコアの情報など、シーンをまたいで共有したいデータがある場合、そのデータ型を `SceneManager<>` の 2 つ目のテンプレート引数に追加します。そうすることで、各シーンの関数から `geteData()` を通してそのデータにアクセスできるようになります。このデータはシーンマネージャーの作成時に 1 度だけ初期化されます。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/22/s2.mp4?raw=true" autoplay loop muted></video>

```C++ hl_lines="3 8 41 73 83"
# include <Siv3D.hpp>

struct GameData
{
	int32 score = 0;
};

using App = SceneManager<String, GameData>;

// タイトルシーン
class Title : public App::Scene
{
public:

	// コンストラクタ（必ず実装）
	Title(const InitData& init)
		: IScene(init)
	{

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

	// 描画関数 (const 修飾)
	void draw() const override
	{
		Scene::SetBackground(ColorF(0.3, 0.4, 0.5));

		FontAsset(U"TitleFont")(U"My Game").drawAt(400, 100);

		// 現在のスコアを表示
		FontAsset(U"ScoreFont")(U"Score: {}"_fmt(getData().score)).draw(520, 540);

		Circle(Cursor::Pos(), 50).draw(Palette::Orange);
	}
};

// ゲームシーン
class Game : public App::Scene
{
private:

	Texture m_texture;

public:

	Game(const InitData& init)
		: IScene(init)
		, m_texture(Emoji(U"🐈"))
	{

	}

	void update() override
	{
		// 左クリックで
		if (MouseL.down())
		{
			// タイトルシーンに遷移
			changeScene(U"Title");
		}

		// マウスカーソルの移動でスコアが増加
		getData().score += static_cast<int32>(Cursor::Delta().length() * 10);
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF(0.2, 0.8, 0.6));

		m_texture.drawAt(Cursor::Pos());

		// 現在のスコアを表示
		FontAsset(U"ScoreFont")(U"Score: {}"_fmt(getData().score)).draw(40, 40);
	}
};

void Main()
{
	FontAsset::Register(U"TitleFont", 60, Typeface::Heavy);
	FontAsset::Register(U"ScoreFont", 30, Typeface::Bold);

	// シーンマネージャーを作成
    // ここで GameData も初期化される
	App manager;

	// タイトルシーン（名前は U"Title"）を登録
	manager.add<Title>(U"Title");

	// ゲームシーン（名前は U"Game"）を登録
	manager.add<Game>(U"Game");

	while (System::Update())
	{
		// 現在のシーンを実行
		if (!manager.update())
		{
			break;
		}
	}
}
```

## 22.4 フェードイン・フェードアウトをカスタマイズする

フェードイン・フェードアウト時の画面の色を変更する場合は `SceneManager::setFadeColor()` で色を設定します。シーンの切り替えの時間をカスタマイズするには、`changeScene()` の第 2 引数に時間を指定します（デフォルトでは 1 秒）。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/22/s3.mp4?raw=true" autoplay loop muted></video>

```C++ hl_lines="29 69 102"
# include <Siv3D.hpp>

struct GameData
{
	int32 score = 0;
};

using App = SceneManager<String, GameData>;

// タイトルシーン
class Title : public App::Scene
{
public:

	// コンストラクタ（必ず実装）
	Title(const InitData& init)
		: IScene(init)
	{

	}

	// 更新関数
	void update() override
	{
		// 左クリックで
		if (MouseL.down())
		{
			// ゲームシーンに 0.3 秒かけて遷移
			changeScene(U"Game", 0.3s);
		}
	}

	// 描画関数 (const 修飾)
	void draw() const override
	{
		Scene::SetBackground(ColorF(0.3, 0.4, 0.5));

		FontAsset(U"TitleFont")(U"My Game").drawAt(400, 100);

		// 現在のスコアを表示
		FontAsset(U"ScoreFont")(U"Score: {}"_fmt(getData().score)).draw(520, 540);

		Circle(Cursor::Pos(), 50).draw(Palette::Orange);
	}
};

// ゲームシーン
class Game : public App::Scene
{
private:

	Texture m_texture;

public:

	Game(const InitData& init)
		: IScene(init)
		, m_texture(Emoji(U"🐈"))
	{

	}

	void update() override
	{
		// 左クリックで
		if (MouseL.down())
		{
			// タイトルシーンに 2 秒かけて遷移
			changeScene(U"Title", 2.0s);
		}

		// マウスカーソルの移動でスコアが増加
		getData().score += static_cast<int32>(Cursor::Delta().length() * 10);
	}

	void draw() const override
	{
		Scene::SetBackground(ColorF(0.2, 0.8, 0.6));

		m_texture.drawAt(Cursor::Pos());

		// 現在のスコアを表示
		FontAsset(U"ScoreFont")(U"Score: {}"_fmt(getData().score)).draw(40, 40);
	}
};

void Main()
{
	FontAsset::Register(U"TitleFont", 60, Typeface::Heavy);
	FontAsset::Register(U"ScoreFont", 30, Typeface::Bold);

	// シーンマネージャーを作成
	App manager;

	// タイトルシーン（名前は U"Title"）を登録
	manager.add<Title>(U"Title");

	// ゲームシーン（名前は U"Game"）を登録
	manager.add<Game>(U"Game");

	// フェードイン・フェードアウト時の画面の色
	manager.setFadeColor(Palette::Skyblue);

	while (System::Update())
	{
		// 現在のシーンを実行
		if (!manager.update())
		{
			break;
		}
	}
}
```


## 22.5 さらにシーン管理を学ぶ

より本格的なゲームをシーン管理で実装する方法は [Siv3Dゲーム開発のテンプレート/シーン遷移（1 ファイル版）](https://siv3d.github.io/ja-jp/sample/game-template/#1) を参照してください。また、シーンごとにソースファイルの分割をする場合は [ゲーム開発プロジェクト・テンプレート](https://siv3d.github.io/ja-jp/sample/game-template/#_2) の構造が参考になります。

`update()` や `draw()` のほかに、`updateFadeIn()`, `updateFadeOut()`, `drawFadeIn()`, `drawFadeOut()` メンバ関数をオーバーライドすることで、フェードイン・フェードアウトの最中の挙動をカスタマイズすることもできます。

- 参考: [シーン切り替え時のエフェクトをカスタマイズする](https://qiita.com/Reputeless/items/f5d48a152414356528dd)
