# 1. はじめての Siv3D
この章では Siv3D プログラミングの雰囲気を体験します。   

## 1.1 最初のサンプルコード

Siv3D プロジェクトを作成すると、最初に次のようなサンプルコードが用意されています。  
このサンプルコードをいきなり全部理解する必要はありません。  
まずは動かして体験しましょう。  
サンプルコードを実行すると、次のようなプログラムが実行されます。

??? summary "用意されているサンプルコードを表示する"
	```cpp
	# include <Siv3D.hpp> // OpenSiv3D v0.6.5

	void Main()
	{
		// 背景の色を設定 | Set background color
		Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

		// 通常のフォントを作成 | Create a new font
		const Font font{ 60 };

		// 絵文字用フォントを作成 | Create a new emoji font
		const Font emojiFont{ 60, Typeface::ColorEmoji };

		// `font` が絵文字用フォントも使えるようにする | Set emojiFont as a fallback
		font.addFallback(emojiFont);

		// 画像ファイルからテクスチャを作成 | Create a texture from an image file
		const Texture texture{ U"example/windmill.png" };

		// 絵文字からテクスチャを作成 | Create a texture from an emoji
		const Texture emoji{ U"🐈"_emoji };

		// 絵文字を描画する座標 | Coordinates of the emoji
		Vec2 emojiPos{ 300, 150 };

		// テキストを画面にデバッグ出力 | Print a text
		Print << U"Push [A] key";

		while (System::Update())
		{
			// テクスチャを描く | Draw a texture
			texture.draw(200, 200);

			// テキストを画面の中心に描く | Put a text in the middle of the screen
			font(U"Hello, Siv3D!🚀").drawAt(Scene::Center(), Palette::Black);

			// サイズをアニメーションさせて絵文字を描く | Draw a texture with animated size
			emoji.resized(100 + Periodic::Sine0_1(1s) * 20).drawAt(emojiPos);

			// マウスカーソルに追随する半透明な円を描く | Draw a red transparent circle that follows the mouse cursor
			Circle{ Cursor::Pos(), 40 }.draw(ColorF{ 1, 0, 0, 0.5 });

			// もし [A] キーが押されたら | When [A] key is down
			if (KeyA.down())
			{
				// 選択肢からランダムに選ばれたメッセージをデバッグ表示 | Print a randomly selected text
				Print << Sample({ U"Hello!", U"こんにちは", U"你好", U"안녕하세요?" });
			}

			// もし [Button] が押されたら | When [Button] is pushed
			if (SimpleGUI::Button(U"Button", Vec2{ 640, 40 }))
			{
				// 画面内のランダムな場所に座標を移動
				// Move the coordinates to a random position in the screen
				emojiPos = RandomVec2(Scene::Rect());
			}
		}
	}
	```

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/1/1.gif)

- マウスカーソルを移動すると、半透明の赤い円が追随します
- 右上の「Button」と書かれたボタンを押すと、ネコの位置がランダムに変わります
- ++a++ キーを押すと、ランダムなメッセージが画面の左側に表示されます
- 実行中のプログラムは、++esc++ を押すか、ウィンドウを閉じると終了します

## 1.2 サンプルコードを改造する
Siv3D プログラミングの練習として、サンプルコードに登場する **数字** や **絵文字** をカスタマイズしてみましょう。  

Visual Studio や Xcode では、新しいプログラムをビルドするときに、古いプログラムが実行中のままだとビルドに失敗します。サンプルコードを改造する前に、++esc++ を押すか、ウィンドウを閉じて、**実行中のプログラムを終了** しましょう。

### 背景色を変える
```cpp
// 背景の色を設定 | Set background color
Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });
```
シーンの背景色の設定です。3 つの数字は左から順に R, G, B です。これらの数字を 0.0～1.0 の範囲で変更して、背景色を変えてみましょう。

### 文字の大きさを変える
```cpp
// 通常のフォントを作成 | Create a new font
const Font font{ 60 };
```
基本サイズを 60 に指定してフォントを作成しています。このフォントは画面中心のテキスト「Hello, Siv3D!🚀」の表示に使われています。数字をだいたい 10～200 の範囲で変更して、文字の大きさが変わることを確認しましょう。

### 絵文字を変える
```cpp
// 絵文字からテクスチャを作成 | Create a texture from an emoji
const Texture emoji{ U"🐈"_emoji };
```
絵文字 🐈 からテクスチャを作成しています。絵文字を 🐕 や 🐧, 🍔 に変えてみましょう。**絵文字の前後に余計な空白を付けると認識されない** ので気を付けてください。

- 「いぬ」と日本語で入力して変換することで犬の絵文字を得ることができます
- Windows では ++windows+period++ キーを押すと登場する絵文字入力メニューが便利です
- オンライン絵文字百科事典 [emojipedia](https://emojipedia.org/) から目的の絵文字を検索するのも良いでしょう

### 画像の描画位置を変える
```cpp
// テクスチャを描く | Draw a texture
texture.draw(200, 200);
```
画像ファイル `example/windmill.png` から作成したテクスチャを、画面上の位置 (x, y) = (200, 200) に描画しています。数字を変えて、位置を変更してみましょう。

### テキストを変える
```cpp
// テキストを画面の中心に描く | Put a text in the middle of the screen
font(U"Hello, Siv3D!🚀").drawAt(Scene::Center(), Palette::Black);
```
「Hello, Siv3D!🚀」というテキストを画面の中心に描画しています。  
このテキストを「こんにちは, Siv3D!🚀」に変えてみましょう。  
`"` の前にある `U` は UTF-32 文字コードを意味します。Siv3D はほぼすべての関数で UTF-32 文字コードを使うため、`U"` はそのままにしておいてください。

### マウスに追随する円を変える
```cpp
// マウスカーソルに追随する半透明な円を描く | Draw a red transparent circle that follows the mouse cursor
Circle{ Cursor::Pos(), 40 }.draw(ColorF{ 1, 0, 0, 0.5 });
```
円をマウスカーソルの位置に半径 40 ピクセル、(R, G, B, 不透明度) = (1.0, 0.0, 0.0, 0.5) で描いています。円の半径や色、不透明度を変更してみましょう。不透明度は 0.0～1.0 の範囲で指定し、0.0 で完全に透明になります。

## 1.3 遊べるサンプル
Siv3D の様々な機能を体験できるおすすめサンプルを紹介します。

この Web サイトのサンプルコードは、コードエリアの右上にある「Copy to Clipboard」アイコンを押すと、**コードをクリップボードにコピー** できます。これまで書いていたサンプルコードを新しいコードで上書きして実行してみましょう。  

どのサンプルも発展的な Siv3D の機能を使っているので、コードの意味を理解するのは難しいかもしれませんが、今はまだ気にしなくて大丈夫です。

### 1 | ブロックくずし

マウスでパドルを移動させてブロックを全部消しましょう。  
ただし、クリアしても何も起こりません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/1/s1.png)

??? summary "ブロックくずしのサンプルコードを表示する"
	```cpp
	# include <Siv3D.hpp>

	////////////////////////////////
	//
	//	定数
	//
	////////////////////////////////

	// 画面のサイズ (800x600)
	constexpr Size SceneSize = Scene::DefaultSceneSize;

	// 1 つのブロックのサイズ
	constexpr Size BrickSize{ 40, 20 };

	// 横に何個のブロックが並ぶか
	constexpr int32 BrickCountX = (SceneSize.x / BrickSize.x);

	// 縦に何個のブロックが並ぶか
	constexpr int32 BrickCountY = 5;

	// ブロックを並べ始める座標
	constexpr Point BrickStartPosition{ 0, 60 };

	// ボールの初期位置（ピクセル）
	constexpr Vec2 BallInitialPos{ (SceneSize.x * 0.5), (SceneSize.y * 0.75) };

	// ボールの速さ（ピクセル / 秒）
	constexpr double BallSpeedPerSec = 480.0;

	// パドルの Y 座標
	constexpr int32 PaddleY = 500;

	// パドルのサイズ
	constexpr Size PaddleSize{ 60, 10 };

	////////////////////////////////
	//
	//	初期状態を作る関数
	//
	////////////////////////////////

	// ブロック（brick: レンガ）の初期配列を作る関数
	Array<Rect> MakeBricks()
	{
		// ブロックの配列（1 つのブロックを Rect で表現）
		Array<Rect> bricks;

		for (int32 y = 0; y < BrickCountY; ++y)
		{
			for (int32 x = 0; x < BrickCountX; ++x)
			{
				// ブロックの左上の X 座標
				const int32 posX = (x * BrickSize.x + BrickStartPosition.x);

				// ブロックの左上の Y 座標
				const int32 posY = (y * BrickSize.y + BrickStartPosition.y);

				// Rect を追加
				bricks << Rect{ posX, posY, BrickSize };
			}
		}

		return bricks;
	}

	// 初期のボールを作成する関数
	Circle MakeBall()
	{
		return{ BallInitialPos, 8 };
	}

	// 初期のボールの速度を作成する関数
	Vec2 MakeBallVelocity()
	{
		return{ 0, -BallSpeedPerSec };
	}

	////////////////////////////////
	//
	//	Main
	//
	////////////////////////////////

	void Main()
	{
		// ブロックの配列
		Array<Rect> bricks = MakeBricks();

		// ボール（中心座標と半径）
		Circle ball = MakeBall();

		// ボールの速度
		Vec2 ballVelocity = MakeBallVelocity();

		while (System::Update())
		{
			////////////////////////////////
			//
			//	状態の更新
			//
			////////////////////////////////

			// パドル
			const Rect paddle{ Arg::center(Cursor::Pos().x, PaddleY), PaddleSize };

			// ボールを移動
			ball.moveBy(ballVelocity * Scene::DeltaTime());

			// ブロックを順にチェック
			for (auto it = bricks.begin(); it != bricks.end(); ++it)
			{
				// ブロックとボールが交差していたら
				if (it->intersects(ball))
				{
					// ブロックの上辺、または底辺と交差していたら
					if (it->bottom().intersects(ball)
						|| it->top().intersects(ball))
					{
						// ボールの速度の Y 成分を反転
						ballVelocity.y *= -1;
					}
					else
					{
						// ボールの速度の X 成分を反転
						ballVelocity.x *= -1;
					}

					// ブロックを配列から削除（イテレータは無効になるので注意）
					bricks.erase(it);

					// これ以上チェックしない
					break;
				}
			}

			// 天井にぶつかったら
			if ((ball.y < 0) && (ballVelocity.y < 0))
			{
				// ボールの速度の Y 成分を反転
				ballVelocity.y *= -1;
			}

			// 左右の壁にぶつかったら
			if (((ball.x < 0) && (ballVelocity.x < 0))
				|| ((SceneSize.x < ball.x) && (0 < ballVelocity.x)))
			{
				// ボールの速度の X 成分を反転
				ballVelocity.x *= -1;
			}

			// パドルにあたったら
			if ((0 < ballVelocity.y) && paddle.intersects(ball))
			{
				// パドルの中心からの距離に応じてはね返る方向（速度ベクトル）を変える
				ballVelocity = Vec2{ (ball.x - paddle.center().x) * 10, -ballVelocity.y }
				.setLength(BallSpeedPerSec); // ボールの速さが BallSpeedPerSec になるよう、ベクトルの長さを調整
			}

			// 画面の底を越えたら（ゲームオーバーになったら）
			if (SceneSize.y <= ball.y)
			{
				// ブロックの配列をリセット
				bricks = MakeBricks();

				// ボールをリセット
				ball = MakeBall();

				// ボールの速度をリセット
				ballVelocity = MakeBallVelocity();
			}

			////////////////////////////////
			//
			//	描画
			//
			////////////////////////////////

			// マウスカーソルを非表示にする
			Cursor::RequestStyle(CursorStyle::Hidden);

			// すべてのブロックを描画する
			for (const auto& brick : bricks)
			{
				brick.stretched(-1) // 1 px 縮ませることで境界線をわかりやすくする
					.draw(HSV{ brick.y - 40 }); // Y 座標に応じて色を変える
			}

			// ボールを描く
			ball.draw();

			// パドルを描く
			paddle.rounded(3) // 角を少し丸くする
				.draw();
		}
	}
	```


### 2 | 万華鏡ペイント

万華鏡のような模様を描けます。右クリックすると、書いたものをリセットします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/1/s2.png)

??? summary "万華鏡ペイントのサンプルコードを表示する"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// キャンバスのサイズ
		constexpr Size CanvasSize{ 600, 600 };

		// ウィンドウをキャンバスのサイズに
		Window::Resize(CanvasSize);

		// 分割数
		constexpr int32 N = 12;

		// 背景色
		constexpr Color BackgroundColor{ 20, 40, 60 };

		// 書き込み用の画像
		Image image{ CanvasSize, BackgroundColor };

		// 画像を表示するための動的テクスチャ
		DynamicTexture texture{ image };

		while (System::Update())
		{
			if (MouseL.pressed())
			{
				// 画面の中心が (0, 0) になるようにマウスカーソルの座標を移動
				const Vec2 begin = (MouseL.down() ? Cursor::PosF() : Cursor::PreviousPosF()) - Scene::Center();
				const Vec2 end = (Cursor::PosF() - Scene::Center());

				for (auto i : step(N))
				{
					// 円座標に変換
					std::array<Circular, 2> cs = { begin, end };

					for (auto& c : cs)
					{
						// 角度をずらす
						c.theta = IsEven(i) ? (-c.theta - 2_pi / N * (i - 1)) : (c.theta + 2_pi / N * i);
					}

					// ずらした位置をもとに、画像に線を書き込む
					Line{ cs[0], cs[1] }.moveBy(Scene::Center())
						.overwrite(image, 2, HSV{ (Scene::Time() * 60), 0.5, 1.0 });
				}

				// 書き込んだ画像でテクスチャを更新
				texture.fillIfNotBusy(image);
			}
			else if (MouseR.down()) // 右クリックされたら
			{
				// 画像を塗りつぶす
				image.fill(BackgroundColor);

				// 塗りつぶした画像でテクスチャを更新
				texture.fill(image);
			}

			// テクスチャを描く
			texture.draw();
		}
	}
	```


### 3 | QR コード生成

テキストボックスに入力したテキストを QR コードに変換します。  
スマートフォンのカメラで読み取ってみましょう。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/1/s3.png)

??? summary "QR コード生成のサンプルコードを表示する"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		// テキストボックスの中身
		TextEditState textEdit{ U"abc" };

		String previous;

		// QR コードを書き込む動的テクスチャ
		DynamicTexture texture;

		while (System::Update())
		{
			// テキスト入力
			SimpleGUI::TextBox(textEdit, Vec2{ 20,20 }, 1240);

			// テキストの更新があれば QR コードを再作成
			if (const String current = textEdit.text;
				current != previous)
			{
				// 入力したテキストを QR コードに変換
				if (const auto qr = QR::EncodeText(current))
				{
					// 枠を付けて拡大した画像で動的テクスチャを更新
					texture.fill(QR::MakeImage(qr).scaled(500, 500, InterpolationAlgorithm::Nearest));
				}

				previous = current;
			}

			texture.drawAt(640, 400);
		}
	}
	```


### 4 | 物理演算ワールド
四角や丸を描くと物体が生成されて物理演算をします。  
マウスホイールや右クリックで視点を移動できます。  

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/1/s4.png)

??? summary "物理演算ワールドのサンプルコードを表示する"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズ
		Window::Resize(1280, 720);

		// 2D 物理演算のシミュレーションステップ（秒）
		constexpr double StepSec = (1.0 / 200.0);

		// 2D 物理演算のシミュレーション蓄積時間（秒）
		double accumulatorSec = 0.0;

		// 2D 物理演算のワールド
		P2World world;

		// [_] 地面
		const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -600, 0, 600, 0 });

		// 物体
		Array<P2Body> bodies;

		// 2D カメラ
		Camera2D camera{ Vec2{ 0, -300 } };

		LineString points;

		while (System::Update())
		{
			for (accumulatorSec += Scene::DeltaTime(); StepSec <= accumulatorSec; accumulatorSec -= StepSec)
			{
				// 2D 物理演算のワールドを更新
				world.update(StepSec);
			}

			// 地面より下に落ちた物体は削除
			bodies.remove_if([](const P2Body& b) { return (200 < b.getPos().y); });

			// 2D カメラの更新
			camera.update();
			{
				// 2D カメラから Transformer2D を作成
				const auto t = camera.createTransformer();

				// 左クリックもしくはクリックしたままの移動が発生したら
				if (MouseL.down() ||
					(MouseL.pressed() && (not Cursor::DeltaF().isZero())))
				{
					points << Cursor::PosF();
				}
				else if (MouseL.up())
				{
					points = points.simplified(2.0);

					if (const Polygon polygon = Polygon::CorrectOne(points))
					{
						const Vec2 pos = polygon.centroid();

						bodies << world.createPolygon(P2Dynamic, pos, polygon.movedBy(-pos));
					}

					points.clear();
				}

				// すべてのボディを描画
				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}

				// 地面を描画
				ground.draw(Palette::Skyblue);

				points.draw(3);
			}

			// 2D カメラの操作を描画
			camera.draw(Palette::Orange);
		}
	}
	```


### 5 | kd-tree
kd-木は近くにあるユニットを高速に検索できるデータ構造です。  
シミュレーションゲームなどで役に立ちます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/1/s5.png)

??? summary "kd-tree のサンプルコードを表示する"
	```cpp
	# include <Siv3D.hpp>

	struct Unit
	{
		Circle circle;

		ColorF color;

		void draw() const
		{
			circle.draw(color);
		}
	};

	// Unit を KDTree で扱えるようにするためのアダプタ
	struct UnitAdapter : KDTreeAdapter<Array<Unit>, Vec2>
	{
		static const element_type* GetPointer(const point_type& point)
		{
			return point.getPointer();
		}

		static element_type GetElement(const dataset_type& dataset, size_t index, size_t dim)
		{
			return dataset[index].circle.center.elem(dim);
		}
	};

	void Main()
	{
		// 200 個の Unit を生成
		Array<Unit> units(200);

		for (auto& unit : units)
		{
			unit.circle = Circle{ RandomVec2(Scene::Rect()), 4 };
			unit.color = RandomColorF();
		}

		// kd-tree を構築
		KDTree<UnitAdapter> kdTree{ units };

		// radius search する際の探索距離
		constexpr double SearchDistance = 80.0;

		while (System::Update())
		{
			const Vec2 cursorPos = Cursor::PosF();

			Circle{ cursorPos, SearchDistance }.draw(ColorF{ 1.0, 0.2 });

			// SearchDistance 以内の距離にある Unit のインデックスを取得
			for (auto index : kdTree.radiusSearch(cursorPos, SearchDistance))
			{
				Line{ cursorPos, units[index].circle.center }.draw(4);
			}

			// ユニットを描画
			for (const auto& unit : units)
			{
				unit.draw();
			}
		}
	}
	```


### 6 | 音楽プレーヤー
パソコンに保存されている音楽ファイルを再生して、スペクトラムも表示します。
パソコンに再生できる音楽ファイルが無い場合、サンプル用の音楽ファイルが `App/example/test.mp3` にあります。フリーの BGM 素材 (MP3) をダウンロードして試すこともできます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/1/s6.png)

??? summary "音楽プレーヤーのサンプルコードを表示する"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// 音楽
		Audio audio;

		// FFT の結果
		FFTResult fft;

		// 再生位置の変更の有無
		bool seeking = false;

		while (System::Update())
		{
			ClearPrint();

			// 再生・演奏時間
			const String time = FormatTime(SecondsF{ audio.posSec() }, U"M:ss")
				+ U" / " + FormatTime(SecondsF{ audio.lengthSec() }, U"M:ss");

			// プログレスバーの進み具合
			double progress = (static_cast<double>(audio.posSample()) / audio.samples());

			if (audio.isPlaying())
			{
				// FFT 解析
				FFT::Analyze(fft, audio);

				// 結果を可視化
				for (auto i : step(Min(Scene::Width(), static_cast<int32>(fft.buffer.size()))))
				{
					const double size = (Pow(fft.buffer[i], 0.6f) * 1000);
					RectF{ Arg::bottomLeft(i, 480), 1, size }.draw(HSV{ 240.0 - i });
				}

				// 周波数表示
				Rect{ Cursor::Pos().x, 0, 1, 480 }.draw();
				Print << U"{:.2f} Hz"_fmt(Cursor::Pos().x * fft.resolution);
			}

			// フォルダから音楽ファイルを開く
			if (SimpleGUI::Button(U"Open", Vec2{ 40, 500 }, 120))
			{
				// 現在再生中のオーディオを 0.5 秒かけてフェードアウトさせて停止
				audio.stop(0.5s);

				// ファイルダイアログからオーディオを開く
				audio = Dialog::OpenAudio();

				// オーディオを再生
				audio.play();
			}

			// 再生
			if (SimpleGUI::Button(U"\U000F040A Play", Vec2{ 170, 500 }, 120, audio && (not audio.isPlaying())))
			{
				audio.play(0.2s);
			}

			// 一時停止
			if (SimpleGUI::Button(U"\U000F03E4 Pause", Vec2{ 300, 500 }, 120, audio.isPlaying()))
			{
				audio.pause(0.2s);
			}

			// スライダー
			if (SimpleGUI::Slider(time, progress, Vec2{ 40, 540 }, 120, 590, (not audio.isEmpty())))
			{
				audio.pause(0.05s);

				while (audio.isPlaying()) // 再生が止まるまで待機
				{
					System::Sleep(0.01s);
				}

				// 再生位置を変更
				audio.seekSamples(static_cast<size_t>(audio.samples() * progress));

				// ノイズを避けるため、スライダーから手を離すまで再生は再開しない
				seeking = true;
			}
			else if (seeking && MouseL.up())
			{
				// 再生を再開
				audio.play(0.05s);
				seeking = false;
			}
		}

		// 終了時に再生中の場合、音量をフェードアウト
		if (audio.isPlaying())
		{
			audio.fadeVolume(0.0, 0.3s);
			System::Sleep(0.3s);
		}
	}
	```


### 7 | ナビメッシュ
制御点をもとに道路を作り、始点から終点までの最短経路を求めます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/1/s7.png)

??? summary "ナビメッシュのサンプルコードを表示する"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		Scene::SetBackground(ColorF{ 0.8, 0.9, 0.8 });

		// 制御点
		Array<Vec2> points;

		// 道路用ポリゴン
		Polygon polygon;

		// 経路
		LineString path;

		// ナビメッシュ
		NavMesh navMesh;

		while (System::Update())
		{
			// 左クリックされたら
			if (MouseL.down())
			{
				// 制御点を追加
				points << Cursor::Pos();

				// スプライン曲線を作り丸く太らせて道路を作る
				polygon = Spline2D{ points }.calculateRoundBuffer(24, 8, 12);

				// ポリゴンからナビメッシュを構築（エージェントの半径 20 ピクセル）
				navMesh.build(polygon, { .agentRadius = 20.0 });

				// 制御点の先頭から終点までの経路を計算
				path = navMesh.query(points.front(), points.back());
			}

			// 道路を描画
			polygon.draw(ColorF{ 1.0 }).drawFrame(2, ColorF{ 0.7 });

			// 経路があれば
			if (path)
			{
				// 経路を描画
				path.draw(8, ColorF{ 0.1, 0.5, 0.9 });

				// スタート地点に円を描画
				path.front().asCircle(12).draw(ColorF{ 1.0, 0.3, 0.0 });

				// ゴール地点に円を描画
				path.back().asCircle(12).draw(ColorF{ 1.0, 0.3, 0.0 });
			}
		}
	}
	```


### 8 | ライフゲーム エディタ
ライフゲームを実行するプログラムです。  
ライフゲームとは: [ライフゲーム (Wikipedia)](https://ja.wikipedia.org/wiki/%E3%83%A9%E3%82%A4%E3%83%95%E3%82%B2%E3%83%BC%E3%83%A0)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/1/s8.png)

??? summary "ライフゲーム エディタのサンプルコードを表示する"
	```cpp
	# include <Siv3D.hpp>

	// 1 セルが 1 バイトになるよう、ビットフィールドを使用
	struct Cell
	{
		bool previous : 1 = 0;
		bool current : 1 = 0;
	};

	// フィールドをランダムなセル値で埋める関数
	void RandomFill(Grid<Cell>& grid)
	{
		grid.fill(Cell{});

		// 境界のセルを除いて更新
		for (auto y : Range(1, grid.height() - 2))
		{
			for (auto x : Range(1, grid.width() - 2))
			{
				grid[y][x] = Cell{ 0, RandomBool(0.5) };
			}
		}
	}

	// フィールドの状態を更新する関数
	void Update(Grid<Cell>& grid)
	{
		for (auto& cell : grid)
		{
			cell.previous = cell.current;
		}

		// 境界のセルを除いて更新
		for (auto y : Range(1, grid.height() - 2))
		{
			for (auto x : Range(1, grid.width() - 2))
			{
				const int32 c = grid[y][x].previous;

				int32 n = 0;
				n += grid[y - 1][x - 1].previous;
				n += grid[y - 1][x].previous;
				n += grid[y - 1][x + 1].previous;
				n += grid[y][x - 1].previous;
				n += grid[y][x + 1].previous;
				n += grid[y + 1][x - 1].previous;
				n += grid[y + 1][x].previous;
				n += grid[y + 1][x + 1].previous;

				// セルの状態の更新
				grid[y][x].current = (c == 0 && n == 3) || (c == 1 && (n == 2 || n == 3));
			}
		}
	}

	// フィールドの状態を画像化する関数
	void CopyToImage(const Grid<Cell>& grid, Image& image)
	{
		for (auto y : step(image.height()))
		{
			for (auto x : step(image.width()))
			{
				image[y][x] = grid[y + 1][x + 1].current
					? Color{ 0, 255, 0 } : Palette::Black;
			}
		}
	}

	void Main()
	{
		// フィールドのセルの数（横）
		constexpr int32 Width = 60;

		// フィールドのセルの数（縦）
		constexpr int32 Height = 60;

		// 計算をしない境界部分も含めたサイズで二次元配列を確保
		Grid<Cell> grid((Width + 2), (Height + 2), Cell{ 0,0 });

		// フィールドの状態を可視化するための画像
		Image image{ Width, Height, Palette::Black };

		// 動的テクスチャ
		DynamicTexture texture{ image };

		Stopwatch stopwatch{ StartImmediately::Yes };

		// 自動再生
		bool autoStep = false;

		// 更新頻度
		double speed = 0.5;

		// グリッドの表示
		bool showGrid = true;

		// 画像の更新の必要があるか
		bool updated = false;

		while (System::Update())
		{
			// フィールドをランダムな値で埋めるボタン
			if (SimpleGUI::ButtonAt(U"Random", Vec2{ 700, 40 }, 170))
			{
				RandomFill(grid);
				updated = true;
			}

			// フィールドのセルをすべてゼロにするボタン
			if (SimpleGUI::ButtonAt(U"Clear", Vec2{ 700, 80 }, 170))
			{
				grid.fill({ 0, 0 });
				updated = true;
			}

			// 一時停止 / 再生ボタン
			if (SimpleGUI::ButtonAt(autoStep ? U"Pause" : U"Run ▶", Vec2{ 700, 160 }, 170))
			{
				autoStep = !autoStep;
			}

			// 更新頻度変更スライダー
			SimpleGUI::SliderAt(U"Speed", speed, 1.0, 0.1, Vec2{ 700, 200 }, 70, 100);

			// 1 ステップ進めるボタン、または更新タイミングの確認
			if (SimpleGUI::ButtonAt(U"Step", Vec2{ 700, 240 }, 170)
				|| (autoStep && stopwatch.sF() >= (speed * speed)))
			{
				Update(grid);
				updated = true;
				stopwatch.restart();
			}

			// グリッド表示の有無を指定するチェックボックス
			SimpleGUI::CheckBoxAt(showGrid, U"Grid", Vec2{ 700, 320 }, 170);

			// フィールド上でのセルの編集
			if (Rect{ 0, 0, 599 }.mouseOver())
			{
				const Point target = (Cursor::Pos() / 10 + Point{ 1, 1 });

				if (MouseL.pressed())
				{
					grid[target].current = true;
					updated = true;
				}
				else if (MouseR.pressed())
				{
					grid[target].current = false;
					updated = true;
				}
			}

			// 画像の更新
			if (updated)
			{
				CopyToImage(grid, image);
				texture.fill(image);
				updated = false;
			}

			// 画像をフィルタなしで拡大して表示
			{
				const ScopedRenderStates2D sampler{ SamplerState::ClampNearest };
				texture.scaled(10).draw();
			}

			// グリッドの表示
			if (showGrid)
			{
				for (auto i : step(61))
				{
					Rect{ 0, i * 10, 600, 1 }.draw(ColorF{ 0.4 });
					Rect{ i * 10, 0, 1, 600 }.draw(ColorF{ 0.4 });
				}
			}

			// 盤面上ではマウスカーソルの代わりに選択セルを強調表示
			if (Rect{ 0, 0, 599 }.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hidden);
				Rect{ Cursor::Pos() / 10 * 10, 10 }.draw(Palette::Orange);
			}
		}
	}
	```


### 9 | 模写アプリ
真っ白な画像からスタートして、ランダムな色の円を重ねていくことで、目標の画像に近づけていくプログラムです。パソコンに適当な画像ファイルが無い場合、サンプル用の画像ファイルが `App/example/` フォルダにあります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/1/s9.png)

??? summary "ランダムな色の円で目的の絵を作るサンプルコードを表示する"
	```cpp
	# include <Siv3D.hpp>

	// 2 つの画像の距離を計算する関数
	double Diff(const Image& a, const Image& b)
	{
		const Color* pA = a.data();
		const Color* pB = b.data();
		const Color* const pAEnd = (pA + a.num_pixels());
		double d = 0.0;

		// すべてのピクセルに対して
		while (pA != pAEnd)
		{
			d += AbsDiff(pA->r, pB->r) + AbsDiff(pA->g, pB->g) + AbsDiff(pA->b, pB->b);
			++pA;
			++pB;
		}

		return d;
	}

	void Main()
	{
		// 目標とする画像をファイルダイアログで選択、シーンのサイズにフィットするようリサイズ
		const Image target = Dialog::OpenImage().fit(Scene::Size());

		// 現在の画像
		Image image{ target.size(), Palette::White };

		// 直前の画像
		Image old = image;

		// 現在の画像を表示するための動的テクスチャ
		DynamicTexture texture{ image };

		// 目標との距離
		double d1 = Diff(target, image);

		while (System::Update())
		{
			for (int32 i = 0; i < 100; ++i)
			{
				// ランダムな座標
				const Point pos = RandomPoint(Rect{ image.size() });

				// ランダムな色
				const ColorF color{ Random(), Random(), Random(), Random() };

				// ランダムな半径
				const int32 size = Random(1, 10);

				// 円を現在の画像に書き込む
				Circle{ pos, size }.paint(image, color);

				// 目標との距離を計算
				const double d2 = Diff(target, image);

				if (d2 < d1) // 目標に近づいていたら採用
				{
					d1 = d2;
					old = image;
				}
				else // 近づいていなかったら元に戻す
				{
					image = old;
				}
			}

			// 動的テクスチャを更新
			texture.fill(image);

			// テクスチャを画面の中心に描画
			texture.drawAt(Scene::Center());

			// 保存ボタン
			if (SimpleGUI::Button(U"Save", Vec2{ 660, 550 }))
			{
				// 現在の画像をファイルダイアログ経由で保存
				image.saveWithDialog();
			}
		}
	}
	```


### 10 | マイクで入力した音の周波数解析
マイクで入力した音声波形のスペクトラムをリアルタイムで表示します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/1/s10.png)

??? summary "マイクで入力した音の周波数解析のサンプルコードを表示する"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// マイクをセットアップ（ただちに録音をスタート）
		Microphone mic{ StartImmediately::Yes };

		if (not mic)
		{
			// マイクを利用できない場合、終了
			throw Error{ U"Microphone not available" };
		}

		FFTResult fft;

		while (System::Update())
		{
			// FFT の結果を取得
			mic.fft(fft);

			// 結果を可視化
			for (auto i : step(800))
			{
				const double size = (Pow(fft.buffer[i], 0.6f) * 1200);
				RectF{ Arg::bottomLeft(i, 600), 1, size }.draw(HSV{ 240 - i });
			}

			// 周波数表示
			Rect{ Cursor::Pos().x, 0, 1, Scene::Height() }.draw();

			ClearPrint();
			Print << U"{} Hz"_fmt(Cursor::Pos().x * fft.resolution);
		}
	}
	```


### 11 | ピアノ
キーボードを使ってピアノを演奏できるプログラムです。  
コードを書き換えて楽器の音を変更できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/1/s11.png)

??? summary "ピアノのサンプルコードを表示する"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// 白鍵の大きさ
		constexpr Size KeySize{ 55, 400 };

		// 楽器の種類
		constexpr GMInstrument Instrument = GMInstrument::Shamisen;

		// ウインドウをリサイズ
		Window::Resize((12 * KeySize.x), KeySize.y);

		// 鍵盤の数
		constexpr int32 NumKeys = 20;

		// 音を作成
		std::array<Audio, NumKeys> sounds;
		for (auto i : step(NumKeys))
		{
			sounds[i] = Audio{ Instrument, static_cast<uint8>(PianoKey::A3 + i), 0.5s };
		}

		// 対応するキー
		constexpr std::array<Input, NumKeys> Keys =
		{
			KeyTab, Key1, KeyQ,
			KeyW, Key3, KeyE, Key4, KeyR, KeyT, Key6, KeyY, Key7, KeyU, Key8, KeyI,
			KeyO, Key0, KeyP, KeyMinus, KeyEnter,
		};

		// 描画位置計算用のオフセット値
		constexpr std::array<int32, NumKeys> KeyPositions =
		{
			0, 1, 2, 4, 5, 6, 7, 8, 10, 11, 12, 13, 14, 15, 16, 18, 19, 20, 21, 22
		};

		while (System::Update())
		{
			// キーが押されたら対応する音を再生
			for (auto i : step(NumKeys))
			{
				if (Keys[i].down())
				{
					sounds[i].playOneShot(0.5);
				}
			}

			// 白鍵を描画
			for (auto i : step(NumKeys))
			{
				// オフセット値が偶数
				if (IsEven(KeyPositions[i]))
				{
					RectF{ (KeyPositions[i] / 2 * KeySize.x), 0, KeySize }
					.stretched(-1).draw(Keys[i].pressed() ? Palette::Pink : Palette::White);
				}
			}

			// 黒鍵を描画
			for (auto i : step(NumKeys))
			{
				// オフセット値が奇数
				if (IsOdd(KeyPositions[i]))
				{
					RectF{ (KeySize.x * 0.68 + KeyPositions[i] / 2 * KeySize.x), 0, (KeySize.x * 0.58), (KeySize.y * 0.62) }
					.draw(Keys[i].pressed() ? Palette::Pink : Color(24));
				}
			}
		}
	}
	```


### 12 | 3D 描画
3D 描画も扱えます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/1/s12.png)

??? summary "3D 描画のサンプルコードを表示する"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// ウインドウとシーンを 1280x720 にリサイズ
		Window::Resize(1280, 720);

		// 背景色 (リニアレンダリング用なので removeSRGBCurve() で sRGB カーブを除去）
		const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

		// UV チェック用テクスチャ (ミップマップ使用。リニアレンダリング時に正しく扱われるよう、sRGB テクスチャであると明示）
		const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };

		// 3D シーンを描く、マルチサンプリング対応レンダーテクスチャ
		// リニア色空間のレンダリング用に TextureFormat::R8G8B8A8_Unorm_SRGB
		// 奥行きの比較のための深度バッファも使うので HasDepth::Yes
		// マルチサンプル・レンダーテクスチャなので、描画内容を使う前に resolve() が必要
		const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

		// 3D シーンのデバッグ用カメラ
		// 縦方向の視野角 30°, カメラの位置 (10, 16, -32)
		// 前後移動: [W][S], 左右移動: [A][D], 上下移動: [E][X], 注視点移動: アローキー, 加速: [Shift][Ctrl]
		DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };

		while (System::Update())
		{
			// デバッグカメラの更新 (カメラの移動スピード: 2.0)
			camera.update(2.0);

			// 3D シーンにカメラを設定
			Graphics3D::SetCameraTransform(camera);

			// 3D 描画
			{
				// renderTexture を背景色で塗りつぶし、
				// renderTexture を 3D 描画のレンダーターゲットに
				const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

				// 床を描画
				Plane{ 64 }.draw(uvChecker);

				// ボックスを描画
				Box{ -8,2,0,4 }.draw(ColorF{ 0.8, 0.6, 0.4 }.removeSRGBCurve());

				// 球を描画
				Sphere{ 0,2,0,2 }.draw(ColorF{ 0.4, 0.8, 0.6 }.removeSRGBCurve());

				// 円柱を描画
				Cylinder{ 8, 2, 0, 2, 4 }.draw(ColorF{ 0.6, 0.4, 0.8 }.removeSRGBCurve());
			}

			// 3D シーンを 2D シーンに描画
			{
				// renderTexture を resolve する前に 3D 描画を実行する
				Graphics3D::Flush();

				// マルチサンプル・テクスチャのリゾルブ
				renderTexture.resolve();

				// リニアレンダリングされた renderTexture をシーンに転送
				Shader::LinearToScreen(renderTexture);
			}
		}
	}
	```
