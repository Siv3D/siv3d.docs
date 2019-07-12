
# よくある間違い

## 初心者編

### Texture をポインタで管理する
`Texture`, `Font`, `Audio` クラスは、参照カウント方式でアセットデータを管理します。そのため、一度作成したオブジェクトのコピーは軽量です。

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture texture(U"example/windmill.png");

	// OK: コスト無しでコピー
	const Texture texture2 = texture;
	
	while (System::Update())
	{
		texture2.draw(0, 0);
	}
}
```

また、デストラクタによって参照カウンタが 0 になった時点で、画像や音声などのアセットデータを自動的にメモリから解放するので、次のプログラムのように破棄の処理を明示的に記述する必要もありません。Siv3D のクラスは、ポインタを使わずにそのまま扱うのが作法です。

```C++
# include <Siv3D.hpp>

void Main()
{
	// 非推奨: わざわざポインタで管理する必要はない
	Texture* pTexture = new Texture(U"example/windmill.png");

	while (System::Update())
	{
		// 非推奨: わざわざポインタで管理する必要はない
		pTexture->draw(0, 0);
	}

	// 非推奨: わざわざポインタで管理する必要はない
	delete pTexture;
}
```


### メインループ内で重いオブジェクトを作成する
`Texture`, `Font`, `Audio` といったアセットデータクラスの作成は、ファイルからの読み込みやメモリの確保に非常に大きな負荷がかかります。メインフレームの中で繰り返しこれらを作成することのないようにする必要があります。

次のようなプログラムを書くと、Siv3D は実行中に警告のメッセージを表示してアプリケーションを終了させます。

```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 誤り
		Texture(U"example/windmill.png").draw(0, 0);
	}
}
```

次のようにメインループの前に 1 度だけ作成するようにします。

```C++
# include <Siv3D.hpp>

void Main()
{
	// メインループの前に 1 度だけ作成
	const Texture texture(U"example/windmill.png");

	while (System::Update())
	{
		texture.draw(0, 0);
	}
}
```

アセット管理を利用するのも OK です。

```C++
# include <Siv3D.hpp>

void Main()
{
	// アセット管理を利用
	TextureAsset::Register(U"Windmill", U"example/windmill.png");

	while (System::Update())
	{
		TextureAsset(U"Windmill").draw(0, 0);
	}
}
```


### フレームレートに依存するアニメーションを作る
最近は 120Hz や 144Hz など、高リフレッシュレートのモニターが増えています。「毎フレーム 3 ピクセル移動」のようなプログラムは、60Hz のモニターでは毎秒 180 ピクセル、120Hz のモニターでは毎秒 360 ピクセルと、速度が大きく変わってしまうので、フレームレートに依存する書き方は避けなければいけません。

`Scene::Delta()` や `Scene::Time()`, `Stopwatch` を使って、時間ベースのアニメーションを実装するか、`Effect`, `Transition`, `Periodic` のように時間ベースで設計されているクラスや関数を使ってアニメーションを実装します。

次のプログラムはフレームレートに依存するコードになっているので、フレームレートが変化すると円の移動速度が変わってしまいます。

```C++
# include <Siv3D.hpp>

void Main()
{
	constexpr std::array<int32, 5> framerates = { 15, 30, 60, 120, 240 };
	size_t index = 2;
	Graphics::SetTargetFrameRateHz(framerates[index]);

	Vec2 pos = Scene::Center();

	while (System::Update())
	{
		// NG: フレームレートに依存する移動量
		const double speed = 3;

		// 上下左右キーで移動
		if (KeyLeft.pressed())
		{
			pos.x -= speed;
		}

		if (KeyRight.pressed())
		{
			pos.x += speed;
		}

		if (KeyUp.pressed())
		{
			pos.y -= speed;
		}

		if (KeyDown.pressed())
		{
			pos.y += speed;
		}

		Circle(pos, 50).draw(Palette::Skyblue);

		// フレームレートを変更
		if (SimpleGUI::RadioButtons(index, { U"15FPS", U"30FPS", U"60FPS", U"120FPS", U"240FPS" }, Vec2(600, 20)))
		{
			Graphics::SetTargetFrameRateHz(framerates[index]);
		}
	}
}
```

次のプログラムでは `Scene::Delta()` に応じて移動量を調整しているため、あらゆるフレームレートでも一定の速度を維持しています。

```C++
# include <Siv3D.hpp>

void Main()
{
	constexpr std::array<int32, 5> framerates = { 15, 30, 60, 120, 240 };
	size_t index = 2;
	Graphics::SetTargetFrameRateHz(framerates[index]);

	Vec2 pos = Scene::Center();

	while (System::Update())
	{
		// 毎秒 180 ピクセル
		const double speed = 180.0;
		// ok: フレームの経過時間に応じた移動量
		const double delta = speed * Scene::DeltaTime();

		// 上下左右キーで移動
		if (KeyLeft.pressed())
		{
			pos.x -= delta;
		}

		if (KeyRight.pressed())
		{
			pos.x += delta;
		}

		if (KeyUp.pressed())
		{
			pos.y -= delta;
		}

		if (KeyDown.pressed())
		{
			pos.y += delta;
		}

		Circle(pos, 50).draw(Palette::Skyblue);

		// フレームレートを変更
		if (SimpleGUI::RadioButtons(index, { U"15FPS", U"30FPS", U"60FPS", U"120FPS", U"240FPS" }, Vec2(600, 20)))
		{
			Graphics::SetTargetFrameRateHz(framerates[index]);
		}
	}
}
```


### コンパイラの警告を無視する
Siv3D の関数やクラスのいくつかは、間違った使い方をしたときにコンパイラが警告を発するように設計されています。些細な警告の無視を続けていると、こうした警告によって発見できるバグを見過ごしやすくなります。Siv3D では、ユーザのコードに由来する警告を一切出さずに、非常に大きなアプリケーションを開発できます。警告なしを目指してコードを書きましょう。

次のプログラムには誤りがありますが、コンパイラが警告を発するので容易に発見できます。

```C++
# include <Siv3D.hpp>

void Main()
{
	Circle circle(200, 300, 40);

	while (System::Update())
	{
		if (MouseL.down())
		{
			// moveBy と間違えて movedBy を使っている
			circle.movedBy(20, 0);
		}

		circle.draw();
	}
}
```


### 読み込みたいファイルを適切なディレクトリに配置しない
プログラムを実行しているときのカレントディレクトリは、実行ファイルが存在するフォルダで、プロジェクトの中では `App` フォルダです。サンプルで使われている `example/windmill.png` もここに用意されています。自分で用意した画像ファイルをプログラムで読み込ませたいときは、`App` フォルダに配置すると、ファイル名だけで読み込めます。

```C++
# include <Siv3D.hpp>

void Main()
{
	// App フォルダにmy-picture.png を配置した場合
	const Texture texture(U"my-picture.png");

	while (System::Update())
	{
		texture.draw(0, 0);
	}
}
```


## アセットデータ編

### `Texture` をグローバル変数や static 変数として使う
`Texture`, `Font`, `Audio` は、Siv3D の初期化よりもあとに作成され、Siv3D の終了処理よりも前に破棄される必要があります。`Main()` 関数の中や、そこで使われるクラスのメンバであれば問題ありませんが、グローバル変数や static 変数としてこれらを使うとは Siv3D の初期化よりも前に作成されたり、終了処理後に破棄が行われます。前者は実行時エラーの原因になります。アセット管理機能を使うことで、グローバル変数や static 変数の使用を避けましょう。

```C++
# include <Siv3D.hpp>

// NG: Siv3D の初期化よりも前に作成されるので実行時エラー
Texture global_texture(U"example/windmill.png");

void Draw()
{
	// 非推奨: Siv3D の終了処理よりもあとに破棄される
	static Texture static_texture(U"example/windmill.png");

	static_texture.draw(0, 0);
}

void Main()
{
	while (System::Update())
	{

	}
}
```

### 縮小表示する画像ファイルを `TextureDesc::Mipped` 指定しない

画像ファイルから `Texture` を作成した場合、デフォルトでは `TextureDesc::Mipped` が指定されていません。`TextureDesc::Mipped` を指定しないと、縮小描画したときに画質が低下します。なお、絵文字やアイコンから `Texture` を作る際にはデフォルトで `TextureDesc::Mipped` が設定されているので、記述は不要です。


## コード全般編

### 不必要な C++ 標準ライブラリヘッダをインクルードする
`<Siv3D.hpp>` は `<cmath>` や `<array>`, `<algorithm>` など、よく使われる C++ 標準ライブラリをすでに内部でインクルードしています。C++ 標準の関数を使って、コンパイルエラーになった時にだけ、対応する追加のインクルードを記述すれば OK です。例として `std::any` を使うには `<any>`, `std::variant` を使うには `<variant>` を追加でインクルードします。


### `std::vector` や `std::string` を使う
`std::vector` や `std::string` を使うと、Siv3D の関数やクラスと連係するときに変換が必要だったり、データの処理が簡単な関数で実現できないため非効率です。Siv3D のプログラムでは、動的配列に `Array`, 文字列には `String` を使いましょう。静的な配列には `std::array` を使います。


### 円周率を新しく定義する
Siv3D では、円周率は `Math::Pi`, 2π は `Math::TwoPi` で定義されています。`1_pi` や `2_pi`, `0.5_pi` のように `_pi` リテラルを使うのもよいでしょう。


## パフォーマンス編

### Siv3D の関数を不必要に頻繁に呼び出す
`Scene::Delta()` や `Cursor::Pos()` は同一フレーム内ではつねに同じ値を返します。軽量な関数なので、パフォーマンスに与える影響は大きくはありませんが、頻繁に呼び出すのは無駄です。こうした関数を複数回呼んでいる場合、メインループの先頭で値を変数に代入し、それを再利用するようにしましょう。関数の呼び出しを減らし、最大限の実行時性能を実現できます。

次のプログラムでは、`Scene::Time()` の呼び出しが不必要に行われています。

```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		for (int32 y : step(10))
		{
			for (int32 x : step(10))
			{
				// Scene::Time() が毎フレーム 100 回実行される
				Triangle(50 + x * 50, 50 + y * 50, 30, Scene::Time() * 30_deg).draw();
			}
		}
	}
}
```

次のように、変数に代入しておくと呼び出しの回数を減らせます。

```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// Scene::Time() の呼び出しは毎フレーム 1 回
		const double t = Scene::Time();

		for (int32 y : step(10))
		{
			for (int32 x : step(10))
			{
				Triangle(50 + x * 50, 50 + y * 50, 30, t * 30_deg).draw();
			}
		}
	}
}
```


## その他編

### プログラムで読み込むテキストファイルを Unicode 以外の形式で保存する
Unicode 形式以外の文字コードで保存すると、ユーザの環境によっては正しく内容が読み込めない場合があります。また Unicode への変換で実行時コストもかかります。UTF-8 など、Unicode 形式の文字コードで保存するようにしましょう。なお、Siv3D では BOM 無しの UTF-8 よりも BOM 付きの UTF-8 形式のほうが、読み込みの速度が早くなります。

