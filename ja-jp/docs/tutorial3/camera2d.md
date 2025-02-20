# 49. 2D 座標変換とカメラ
描画座標やマウスカーソル座標に対して、移動・拡大縮小・回転などの座標変換を適用する機能を学びます。

## 49.1 描画座標へのオフセット適用（Vec2 の加算）
- 複数の図形やテクスチャを組み合わせて何かを描画するとき、まとめて移動させたい場合があります
- 原始的な方法として、それぞれ描画する座標を `Point` や `Vec2` で指定する際、移動量を加算する方法があります
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2D/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	while (System::Update())
	{
		const Vec2 offset = Cursor::Pos();

		for (int32 y = 0; y < 6; ++y)
		{
			for (int32 x = 0; x < 6; ++x)
			{
				if (IsEven(x + y))
				{
					RectF{ (x * 40), (y * 40), 40 }.movedBy(offset).draw();
				}
			}
		}

		emoji.drawAt(Vec2{ 120, 100 } + offset);

		font(U"APPLE").drawAt(50, (Vec2{ 120, 200 } + offset), ColorF{ 0.1 });
	}
}
```


## 49.2 描画座標へのオフセット適用（Transformer2D）
- **49.1** よりも便利な方法として、`Transformer2D` を使う方法があります
- `Transformer2D` を使うと、描画やマウスカーソル座標に対して、一括で移動・拡大縮小・回転などの座標変換（アフィン変換）を適用できます
- 描画座標の移動を `Mat3x2::Translate(x, y)` または `Mat3x2::Translate(Vec2{ x, y })` で表現し、`Transformer2D` のコンストラクタに渡します
- `Transformer2D` オブジェクトが有効な間、その座標変換が 2D 描画に適用されます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2D/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	while (System::Update())
	{
		{
			const Vec2 offset = Cursor::Pos();
			const Transformer2D t{ Mat3x2::Translate(offset) };

			for (int32 y = 0; y < 6; ++y)
			{
				for (int32 x = 0; x < 6; ++x)
				{
					if (IsEven(x + y))
					{
						RectF{ (x * 40), (y * 40), 40 }.draw();
					}
				}
			}

			emoji.drawAt(Vec2{ 120, 100 });

			font(U"APPLE").drawAt(50, Vec2{ 120, 200 }, ColorF{ 0.1 });
		}

		// Transformer2D のスコープ範囲外には影響しない
		emoji.drawAt(600, 400);
	}
}
```


## 49.3 描画座標のスケーリング
- `Mat3x2::Scale(x, y, center)` または `Mat3x2::Scale(Vec2{ x, y }, center)` で、描画座標のスケーリングを行います
- `center` はスケーリングの中心座標です。省略した場合は `Vec2{ 0, 0 }` が使われます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2D/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	while (System::Update())
	{
		{
			const double scale = (1.0 + Periodic::Sine0_1(4s));
			const Transformer2D t{ Mat3x2::Scale(scale) };

			for (int32 y = 0; y < 6; ++y)
			{
				for (int32 x = 0; x < 6; ++x)
				{
					if (IsEven(x + y))
					{
						RectF{ (x * 40), (y * 40), 40 }.draw();
					}
				}
			}

			emoji.drawAt(Vec2{ 120, 100 });

			font(U"APPLE").drawAt(50, Vec2{ 120, 200 }, ColorF{ 0.1 });
		}

		// Transformer2D のスコープ範囲外には影響しない
		emoji.drawAt(600, 400);
	}
}
```


## 49.4 描画座標の回転
- `Mat3x2::Rotate(angle, center)` で、描画座標の回転を行います
- `angle` は時計回りの回転角度（ラジアン）です
- `center` は回転の軸となる座標です。省略した場合は `Vec2{ 0, 0 }` が使われます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2D/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	while (System::Update())
	{
		{
			const double angle = (Scene::Time() * 30_deg);
			const Transformer2D t{ Mat3x2::Rotate(angle, Vec2{ 120, 120 }) };

			for (int32 y = 0; y < 6; ++y)
			{
				for (int32 x = 0; x < 6; ++x)
				{
					if (IsEven(x + y))
					{
						RectF{ (x * 40), (y * 40), 40 }.draw();
					}
				}
			}

			emoji.drawAt(Vec2{ 120, 100 });

			font(U"APPLE").drawAt(50, Vec2{ 120, 200 }, ColorF{ 0.1 });
		}

		// Transformer2D のスコープ範囲外には影響しない
		emoji.drawAt(600, 400);
	}
}
```


## 49.5 座標変換行列の乗算
- `Mat3x2` は、次のようなメンバ関数を使って、座標変換を乗算できます
- これにより、回転・拡大縮小・移動を 1 つの行列にまとめて適用できます

| コード | 説明 |
|--|--|
|`.translated(x, y)`| 移動 |
|`.translated(Vec2{ x, y })`| 移動 |
|`.scaled(x, y, center)`| 拡大縮小 |
|`.scaled(Vec2{ x, y }, center)`| 拡大縮小 |
|`.rotated(angle, center)`| 回転 |

- `center` を省略した場合 `Vec2{ 0, 0 }` が使われます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2D/5.png)

```cpp
# include <Siv3D.hpp>

void Draw(const Font& font, const Texture& emoji)
{
	for (int32 y = -3; y < 3; ++y)
	{
		for (int32 x = -3; x < 3; ++x)
		{
			if (IsEven(x + y))
			{
				RectF{ (x * 40), (y * 40), 40 }.draw();
			}
		}
	}

	emoji.drawAt(Vec2{ 0, -20 });

	font(U"APPLE").drawAt(50, Vec2{ 0, 80 }, ColorF{ 0.1 });
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	while (System::Update())
	{
		{
			const Mat3x2 mat = Mat3x2::Scale(0.5 + Periodic::Sine0_1(4s))
				.translated(200, 160);
			const Transformer2D t{ mat };
			Draw(font, emoji);
		}

		{
			const Mat3x2 mat = Mat3x2::Rotate(Scene::Time() * 30_deg)
				.translated(600, 160);
			const Transformer2D t{ mat };
			Draw(font, emoji);
		}

		{
			const Mat3x2 mat = Mat3x2::Rotate(Scene::Time() * 30_deg)
				.scaled(0.5 + Periodic::Sine0_1(4s))
				.translated(Cursor::Pos());
			const Transformer2D t{ mat };
			Draw(font, emoji);
		}
	}
}
```


## 49.6 Transformer2D の重ねがけ
- `Transformer2D` の効果が適用されているときに新しい `Transformer2D` を有効化すると、座標変換の効果が乗算されます
- 次のコードでは、行列の乗算によって複雑な動きを実現しています
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2D/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		const double t = (Scene::Time() * -30_deg);

		{
			const Transformer2D t0{ Mat3x2::Translate(400, 300) };
			Circle{ 0, 0, 40 }.draw(Palette::Orangered);
			Circle{ 0, 0, 160 }.drawFrame(2);

			{
				const Transformer2D t1{ Mat3x2::Translate(160, 0).rotated(t) };
				Circle{ 0, 0, 20 }.draw(Palette::Seagreen);
				Circle{ 0, 0, 40 }.drawFrame(2);

				{
					const Transformer2D t2{ Mat3x2::Translate(40, 0).rotated(t * 4) };
					Circle{ 0, 0, 10 }.draw(Palette::Yellow);
				}
			}
		}
	}
}
```


## 49.7 マウスカーソル座標への座標変換
- `Transformer2D` のコンストラクタの第 2 引数に `TransformCursor::Yes` を渡すと、マウスカーソル座標にも座標変換が適用されます
- UI 要素に対して座標変換を適用する際に便利です
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2D/7.png)

- 次のサンプルコードでは、各アイテム上にマウスカーソルがあるかどうかを、回転・拡大縮小・移動された座標系で判定しています

```cpp
# include <Siv3D.hpp>

void Draw(const Font& font, const Texture& emoji)
{
	const Rect region{ -120, -120, 240 };

	for (int32 y = -3; y < 3; ++y)
	{
		for (int32 x = -3; x < 3; ++x)
		{
			if (IsEven(x + y))
			{
				RectF{ (x * 40), (y * 40), 40 }.draw();
			}
		}
	}

	emoji.drawAt(Vec2{ 0, -20 });

	font(U"APPLE").drawAt(50, Vec2{ 0, 80 }, ColorF{ 0.1 });

	if (region.mouseOver())
	{
		region.drawFrame(0, 6, Palette::Seagreen);
		Cursor::RequestStyle(CursorStyle::Hand);
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	while (System::Update())
	{
		{
			const Mat3x2 mat = Mat3x2::Scale(0.5 + Periodic::Sine0_1(4s))
				.translated(200, 300);
			const Transformer2D t{ mat, TransformCursor::Yes };
			Draw(font, emoji);
		}

		{
			const Mat3x2 mat = Mat3x2::Rotate(Scene::Time() * 30_deg)
				.translated(600, 300);
			const Transformer2D t{ mat, TransformCursor::Yes };
			Draw(font, emoji);
		}
	}
}
```


## 49.8 マウスカーソルのみ座標変換
- ビューポートを使ってミニウィンドウを作成した際など、描画の座標変換は不要でマウスカーソルの座標変換だけを行いたい場合があります
- そのようなときは、`Transformer2D` の第 1 引数に単位行列（何も変更しない行列） `Mat3x2::Identity()` を、第 2 引数にマウスカーソル用の座標変換行列を設定します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2D/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		const Point topLeft = Vec2{ (Periodic::Sine0_1(8s) * 400), (Periodic::Sine0_1(6s) * 300) }.asPoint();
		const Rect viewportRect{ topLeft, 360, 240};

		{
			const ScopedViewport2D viewport{ viewportRect };

			// マウスカーソル座標だけ平行移動させる
			const Transformer2D t{ Mat3x2::Identity(), Mat3x2::Translate(topLeft) };

			Circle{ 200, 150, 200 }.draw();
			Circle{ Cursor::PosF(), 40 }.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Button", Vec2{ 20, 20 }))
			{
				Print << U"Pushed";
			}
		}

		viewportRect.drawFrame(0, 2, Palette::Seagreen);
	}
}
```


## 49.9 2D カメラ
- `Camera2D` を使うと、マウスやキーボードを使った直感的な操作で `Transformer2D` を作成・制御できます
- `Camera2D::update()` では ++w++ / ++a++ / ++s++ / ++d++ キーで上下左右移動、++up++ / ++down++ キーで拡大縮小、マウス右クリックで自由移動、マウスホイールで拡大縮小の操作を行います
- キー操作を無効にしたい場合は `Camera2D` コンストラクタに `CameraControl::Mouse` を渡します
- キー操作もマウス操作も無効にしたい場合は `CameraControl::None_` を渡します
- カメラの詳細な挙動は `Camera2DParameters` によってカスタマイズできます。
- `Camera2D` の主なメンバ関数は次のとおりです：

| 関数 | 説明 |
|--|--|
|`.createTransformer()`| 現在のカメラの設定から `Transformer2D` を作成する |
|`.setTargetCenter(Vec2)`| カメラの中心座標の目標を設定する |
|`.setTargetScale(double)`| カメラのズームアップ倍率の目標を設定する |
|`.jumpTo(Vec2, double)`| カメラの中心座標およびズームアップ倍率を即座に変更する |
|`.update()` | カメラの操作や、目標値への移動を行う |
|`.draw(const ColorF&)`| マウスでのカメラ操作を補助する矢印 UI を表示する |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2D/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	// 2D カメラ
	// 初期設定: 中心 (0, 0), ズームアップ倍率 1.0
	Camera2D camera{ Vec2{ 0, 0 }, 1.0 };
	//Camera2D camera{ Vec2{ 0, 0 }, 1.0, CameraControl::Mouse }; // マウス操作のみの場合

	while (System::Update())
	{
		// 2D カメラを更新
		camera.update();
		{
			// 2D カメラの設定から Transformer2D を作成する
			const auto t = camera.createTransformer();

			for (int32 i = 0; i < 8; ++i)
			{
				Circle{ 0, 0, (50 + i * 50) }.drawFrame(2);
			}

			emoji.drawAt(0, 0);
			Shape2D::Star(100, Vec2{ 200, 200 }).draw(Palette::Seagreen);
			font(U"Siv3D").drawAt(50, Vec2{ -200, -100 }, ColorF{ 0.1 });
		}

		if (SimpleGUI::Button(U"Jump to center", Vec2{ 40, 40 }, 200))
		{
			// 中心とズームアップ倍率を即座に変更する
			camera.jumpTo(Vec2{ 0, 0 }, 1.0);
		}

		if (SimpleGUI::Button(U"Move to center", Vec2{ 40, 80 }, 200))
		{
			// 中心とズームアップ倍率の目標値をセットして、時間をかけて変更する
			camera.setTargetCenter(Vec2{ 0, 0 });
			camera.setTargetScale(1.0);
		}

		// 2D カメラ操作の UI を表示する
		camera.draw(Palette::Orange);
	}
}
```


## 49.10 2D カメラのプログラム制御
- `CameraControl::None_` を設定した 2D カメラはプログラムで制御します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2D/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture playerTexture{ U"🚙"_emoji };
	const Texture treeTexture{ U"🌳"_emoji };

	// プレイヤーの X 座標
	double playerPosX = 400;

	// 木の X 座標
	Array<double> trees = { 100, 300, 500, 700, 900 };

	// (400, 300) を中心とする, 拡大率 1.0 倍の, (マウスやキーではなく）プログラムで動かすカメラ
	Camera2D camera{ Vec2{ 400, 300 }, 1.0, CameraControl::None_ };

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		// カメラの X 座標
		const double cameraPosX = camera.getCenter().x;

		ClearPrint();
		Print << U"playerPosX: {:.1f}"_fmt(playerPosX);
		Print << U"cameraPosX: {:.1f}"_fmt(cameraPosX);

		// 左右キーで移動
		if (KeyLeft.pressed())
		{
			playerPosX -= (200 * deltaTime);
		}
		else if (KeyRight.pressed())
		{
			playerPosX += (200 * deltaTime);
		}

		// カメラの目標中心座標を設定する
		camera.setTargetCenter(Vec2{ playerPosX, 300 });

		// カメラを更新する
		camera.update();
		{
			// カメラによる座標変換を適用する
			const auto tr = camera.createTransformer();

			for (const auto& tree : trees)
			{
				// カメラの中心 X 座標と差が 500 ピクセルの物だけを描く（画面外のものを描かない）
				if (AbsDiff(cameraPosX, tree) < 500.0)
				{
					treeTexture.drawAt(tree, 400);
				}
			}

			playerTexture.drawAt(playerPosX, 410);
		}
	}
}
```

- 車が前進するときは前の視界を大きく、後進するときはうしろの視界を大きく確保したい場合、次のような改良ができます

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture playerTexture{ U"🚙"_emoji };
	const Texture treeTexture{ U"🌳"_emoji };

	// プレイヤーの X 座標
	double playerPosX = 400;

	// 木の X 座標
	Array<double> trees = { 100, 300, 500, 700, 900 };

	// (400, 300) を中心とする, 拡大率 1.0 倍の, (マウスやキーではなく）プログラムで動かすカメラ
	Camera2D camera{ Vec2{ 400, 300 }, 1.0, CameraControl::None_ };

	double cameraCenterOffset = 0.0;
	double cameraCenterOffsetVelocity = 0.0;

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		// カメラの X 座標
		const double cameraPosX = camera.getCenter().x;

		ClearPrint();
		Print << U"playerPosX: {:.1f}"_fmt(playerPosX);
		Print << U"cameraPosX: {:.1f}"_fmt(cameraPosX);

		// 左右キーで移動
		if (KeyLeft.pressed())
		{
			playerPosX -= (200 * deltaTime);
			cameraCenterOffset = Math::SmoothDamp(cameraCenterOffset, -150.0, cameraCenterOffsetVelocity, 0.8);
		}
		else if (KeyRight.pressed())
		{
			playerPosX += (200 * deltaTime);
			cameraCenterOffset = Math::SmoothDamp(cameraCenterOffset, 150.0, cameraCenterOffsetVelocity, 0.8);
		}

		// カメラの目標中心座標を設定する
		camera.setTargetCenter(Vec2{ (playerPosX + cameraCenterOffset), 300 });

		// カメラを更新する
		camera.update();
		{
			// カメラによる座標変換を適用する
			const auto tr = camera.createTransformer();

			for (const auto& tree : trees)
			{
				// カメラの中心 X 座標と差が 500 ピクセルの物だけを描く（画面外のものを描かない）
				if (AbsDiff(cameraPosX, tree) < 500.0)
				{
					treeTexture.drawAt(tree, 400);
				}
			}

			playerTexture.drawAt(playerPosX, 410);
		}
	}
}
```


## 49.11 シーンの高解像度・高精細化
- `Transformer2D` を使うことで、低解像度で開発したゲームやアプリを簡単に高解像度・高精細化できます

!!! warning "この方法を適用する場合の注意"
	- この方法を適用する場合は、既存のコードから `Scene::Width()`, `Scene::Height()`, `Scene::Size()`, `Scene::Rect()`, `Scene::Center()` 等の関数を除去してください
	- 上記の関数は、シーンのリサイズによって、自動的に大きい解像度の値を返してしまうため、この方法との相性が悪いです

- 大きな解像度のウィンドウにシーンを描画するために、`Transformer2D` を用いて描画やマウス座標をスケールアップ・移動できます
- OS の設定による拡大縮小を無視して、シーンをドットバイドットで表示するために、シーンのリサイズモードに `ResizeMode::Actual` を設定します（**チュートリアル 44**）
	- デフォルトの `ResizeMode::Virtual` では、例えば 4K 解像度、150 % 拡大のノート PC では、フルスクリーン時のシーン解像度が 2560x1440 である一方、`ResizeMode::Actual` では 3840x2160 になります。シーンの解像度が大きいと描画負荷が大きくなることに注意してください
- 次のサンプルでは、800 x 600 を想定して開発されたゲームの描画・入力処理について、ゲームのコード（`Game()` 関数）に変更を加えず、解像度の変更に対応しています

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2D/11.png)

```cpp
# include <Siv3D.hpp>

// オリジナルのシーンを何倍すればよいかを返す関数
double CalculateScale(const Vec2& baseSize, const Vec2& currentSize)
{
	return Min((currentSize.x / baseSize.x), (currentSize.y / baseSize.y));
}

// 画面の中央に配置するためのオフセットを返す関数
Vec2 CalculateOffset(const Vec2& baseSize, const Vec2& currentSize)
{
	return ((currentSize - baseSize * CalculateScale(baseSize, currentSize)) / 2.0);
}

void Game(const Size& baseSize, const Font& font)
{
	Rect{ baseSize }.draw(ColorF{ 0.15, 0.6, 0.4 });
	Rect{ 40, 100, 400, 400 }.rounded(15).drawFrame(5);

	const Circle circle{ 600, 260, 100 };
	circle.draw(circle.mouseOver() ? ColorF{ 1.0 } : ColorF{ 0.8 });

	if (circle.mouseOver())
	{
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	font(U"Hello, Siv3D").drawAt(40, Vec2{ 600, 120 });

	for (int32 i = 0; i < 8; ++i)
	{
		font(i + 1).drawAt(20, Vec2{ 20, (125 + 50 * i) }, ColorF{ 0.1 });
		font(char32{ U'a' + i }).drawAt(20, Vec2{ (65 + 50 * i), 80 }, ColorF{ 0.1 });
	}
}

void Main()
{
	// オリジナルのシーン解像度
	const Size BaseSceneSize{ 800, 600 };
	Scene::Resize(BaseSceneSize);

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	constexpr Rect MenuRect{ 0, 0, 700, 32 };
	constexpr Rect WindowModeButton{ 300, 0, 200, 32 };
	constexpr Rect DotByDotButton{ 500, 0, 200, 32 };

	while (System::Update())
	{
		// シーンの拡大倍率を計算する
		const double scale = CalculateScale(BaseSceneSize, Scene::Size());
		const Vec2 offset = CalculateOffset(BaseSceneSize, Scene::Size());

		ClearPrint();
		Print << U"Original scene resolution: " << BaseSceneSize;
		Print << U"Current scene resolution: " << Scene::Size();
		Print << U"Scene scale factor = " << scale;
		Print << U"Offset = " << offset;

		{
			// draw() とマウス座標にスケーリングを適用
			const Transformer2D screenScaling{ Mat3x2::Scale(scale).translated(offset), TransformCursor::Yes };

			Game(BaseSceneSize, font);

			{
				MenuRect.draw(ColorF{ 0.75 });

				// ウィンドウ ⇔ フルスクリーンボタン
				{
					if (WindowModeButton.mouseOver())
					{
						WindowModeButton.draw(ColorF{ 0.85 });
						Cursor::RequestStyle(CursorStyle::Hand);

						if (WindowModeButton.leftClicked())
						{
							// ウィンドウ ⇔ フルスクリーンを切り替える
							Window::SetFullscreen(not Window::GetState().fullscreen);
						}
					}
					WindowModeButton.drawFrame(2);
					font(Window::GetState().fullscreen ? U"Switch to Window" : U"Switch to Fullscreen").drawAt(16, WindowModeButton.center(), ColorF{ 0.25 });
				}

				// Dot by Dot ボタン
				{
					if (DotByDotButton.mouseOver())
					{
						DotByDotButton.draw(ColorF{ 0.85 });
						Cursor::RequestStyle(CursorStyle::Hand);

						if (DotByDotButton.leftClicked())
						{
							if (Scene::GetResizeMode() == ResizeMode::Virtual)
							{
								Scene::SetResizeMode(ResizeMode::Actual);
							}
							else
							{
								Scene::SetResizeMode(ResizeMode::Virtual);
							}
						}
					}
					DotByDotButton.drawFrame(2);
					font((Scene::GetResizeMode() == ResizeMode::Actual) ? U"Match OS Scale" : U"Switch to Dot by Dot").drawAt(16, DotByDotButton.center(), ColorF{ 0.25 });
				}
			}
		}
	}
}
```
