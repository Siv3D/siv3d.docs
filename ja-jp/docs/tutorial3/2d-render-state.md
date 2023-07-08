# 39. 2D レンダーステート
2D 描画の設定（レンダーステート）をカスタマイズして、さまざまな効果を得る方法を学びます。

!!! info "Scoped～ のはたらき"
	この章で学ぶ `Scoped～` や `Transformer2D` などの変数は、ソースコード上では何も働いていないように見えますが、実際はコンストラクタの中では、渡されたレンダーステートをエンジンに設定し、自身が破棄されるとき（スコープが終了するとき）に、デストラクタで設定を元の状態に戻す処理を行っています。

	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		while (System::Update())
		{
			{
				// レンダーステートを変更する（元の状態も保存）
				const ScopedRenderStates2D rasterizer{ RasterizerState::WireframeCullNone };

				Circle{ 200, 300, 150 }.draw();

			} // ここで rasterizer のデストラクタが呼び出され、レンダーステートを元の状態に戻す

			Circle{ 600, 300, 150 }.draw();
		}
	}
	```


## 39.1 加算ブレンドで描く
`ScopedRenderStates2D` オブジェクトのコンストラクタに `BlendState::Additive` を渡すと、そのオブジェクトのスコープが有効な間、図形や画像が加算ブレンドで描画されます。加算ブレンドでは、背景色に RGB 成分を加算するように描画されるので、重ねて描画した部分が明るくなります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<Vec2> points;

	for (int32 i = 0; i < 400; ++i)
	{
		points << RandomVec2(Scene::Rect());
	}

	// 加算ブレンドを有効にするか
	bool enabled = true;

	while (System::Update())
	{
		if (enabled)
		{
			// 加算ブレンド有効
			const ScopedRenderStates2D blend{ BlendState::Additive };

			for (const auto& point : points)
			{
				Circle{ point, 20 }.draw(HSV{ (point.y * 100 + point.x * 100), 0.5 });
			}
		}
		else
		{
			// 通常のブレンドモード

			for (const auto& point : points)
			{
				Circle{ point, 20 }.draw(HSV{ (point.y * 100 + point.x * 100), 0.5 });
			}
		}

		SimpleGUI::CheckBox(enabled, U"AdditiveBlend", Vec2{ 40, 40 });
	}
}
```


## 39.2 描画色を加算する
画像や図形を描くときに、本来の色に RGBA 成分を加算して描画するには、`ScopedColorAdd2D` オブジェクトのコンストラクタに、加算したい値を設定します。そのオブジェクトのスコープが有効な間、描画の RGBA 値が加算されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture textureWindmill{ U"example/windmill.png" };
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };
	ColorF color{ 0.0, 0.0, 0.0, 0.0 };

	while (System::Update())
	{
		{
			// 描画時に色を加算するように設定する
			const ScopedColorAdd2D colorAdd{ color };

			textureWindmill.draw(40, 40);
			textureSiv3DKun.draw(400, 100);
		}

		SimpleGUI::Slider(U"R", color.r, Vec2{ 620, 40 }, 40);
		SimpleGUI::Slider(U"G", color.g, Vec2{ 620, 80 }, 40);
		SimpleGUI::Slider(U"B", color.b, Vec2{ 620, 120 }, 40);
	}
}
```


## 39.3 描画色を乗算する
画像や図形を描くときに、本来の色に RGBA 成分を乗算して描画するには、`ScopedColorMul2D` オブジェクトのコンストラクタに、乗算したい値を設定します。そのオブジェクトのスコープが有効な間、描画の RGBA 値が乗算されます。

`Texture` の `.draw()` に色を渡すことで乗算の色を設定することもできます（チュートリアル 25.9 参照）。`ScopedColorMul2D` はその設定を一括して行えるものです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture textureWindmill{ U"example/windmill.png" };
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };
	ColorF color{ 1.0, 1.0, 1.0, 1.0 };

	while (System::Update())
	{
		{
			// 描画時に色を乗算するように設定する
			const ScopedColorMul2D colorMul{ color };

			textureWindmill.draw(40, 40);
			textureSiv3DKun.draw(400, 100);
		}

		SimpleGUI::Slider(U"R", color.r, Vec2{ 620, 40 }, 40);
		SimpleGUI::Slider(U"G", color.g, Vec2{ 620, 80 }, 40);
		SimpleGUI::Slider(U"B", color.b, Vec2{ 620, 120 }, 40);
		SimpleGUI::Slider(U"A", color.a, Vec2{ 620, 160 }, 40);
	}
}
```


## 39.4 テクスチャ拡大縮小時のフィルタを変える
テクスチャを拡大縮小して描画する際に、デフォルトではバイリニア補間によって色が補間されます。ドット感を保ったまま拡大したいときにはサンプラーステート `SamplerState::ClampNearest` を `ScopedRenderStates2D` に設定します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"🐈"_emoji };
	bool bilinear = true;
	double scale = 1.0;

	while (System::Update())
	{
		if (bilinear)
		{
			// バイリニア補間（デフォルト）
			texture.scaled(scale).drawAt(Scene::Center());
		}
		else
		{
			// 最近傍補間
			const ScopedRenderStates2D sampler{ SamplerState::ClampNearest };
			texture.scaled(scale).drawAt(Scene::Center());
		}

		SimpleGUI::Slider(scale, 0.5, 12.0, Vec2{ 40, 40 }, 200);
		SimpleGUI::CheckBox(bilinear, U"Bilinear", Vec2{ 40, 80 });
	}
}
```


## 39.5 ワイヤフレームモードで描画する
`ScopedRenderStates2D` オブジェクトのコンストラクタに `RasterizerState::WireframeCullNone` を渡すと、図形や画像を構成する三角形のワイヤフレームのみが描画されるようになります。

ワイヤフレームモードは Web 版では利用できません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture textureWindmill{ U"example/windmill.png" };

	while (System::Update())
	{
		{
			// ワイヤフレーム表示モードに設定する
			const ScopedRenderStates2D rasterizer{ RasterizerState::WireframeCullNone };

			textureWindmill.draw(40, 40);

			Circle{ Scene::Center(), 100 }.draw();

			Shape2D::Star(100, Vec2{ 160, 400 }).draw(Palette::Yellow);
		}
	}
}
```


## 39.6 テクスチャをくり返して描画する
`ScopedRenderStates2D` オブジェクトのコンストラクタにサンプラーステートを渡すことで、テクスチャ描画時に UV 座標が 0.0～1.0 の範囲を超えたときの処理の方法をカスタマイズできます。

デフォルトでは、UV 座標が 0.0～1.0 の範囲を超えたとき、テクスチャの端の色がそのまま描画されます。テクスチャアドレスモードが Repeat のときは、UV 座標が 0.0～1.0 の範囲を超えたとき、テクスチャの端の色が反対側の端から続くように描画されます。

`Texture::mapped()` についてはチュートリアル 25.18 を参照してください。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/6.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture tree{ U"🌲"_emoji };

	while (System::Update())
	{
		{
			// UV 座標が 0.0～1.0 の範囲を超えたとき、くり返しマッピングするように設定する
			const ScopedRenderStates2D sampler{ SamplerState::RepeatLinear };

			// シーンのサイズにマッピングして描画する
			tree.mapped(Scene::Size()).draw();
		}
	}
}
```


## 39.7 シザー矩形を設定する
シザー矩形を使うと、指定した長方形領域以外への描画を行わないようにできます。

`Graphics2D::SetScissorRect()` でシザー矩形の領域を登録し、`.scissorEnable` を `true` にした `RasterizerState` を `ScopedRenderStates2D` で適用すると、シザー矩形が有効になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/7.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture textureWindmill{ U"example/windmill.png" };
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };

	// シザー矩形の範囲を登録する
	Graphics2D::SetScissorRect(Rect{ 100, 100, 300, 200 });

	while (System::Update())
	{
		{
			RasterizerState rs = RasterizerState::Default2D;
			rs.scissorEnable = true;

			// シザー矩形を有効化する
			const ScopedRenderStates2D rasterizer{ rs };

			textureWindmill.draw(40, 40);
			textureSiv3DKun.draw(160, 100);
		}
	}
}
```


## 39.8 ビューポートを設定する
`ScopedViewport2D` オブジェクトを作成すると、シーン内に仮想のシーンを作り、新しい描画領域を定義できます。描画時にはビューポートの長方形の左上が (0, 0) の描画座標になり、長方形の範囲外にはみ出たものは描画されなくなります。

ビューポートは描画の座標にしか影響を及ぼしません。マウスカーソルの座標も同様に移動させたい場合には、後述する `Transformer2D` と組み合わせます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture cat{ U"🐈"_emoji };

	while (System::Update())
	{
		{
			// ビューポートを適用する
			const ScopedViewport2D viewport{ 400, 300, 400, 300 };

			Circle{ 200, 150, 200 }.draw();

			cat.draw(0, 0);
		}
	}
}
```


## 39.9 座標変換行列を適用する
`Transformer2D` は、描画やマウスカーソル座標に対して、回転・拡大縮小、座標移動などの座標変換（アフィン変換）を適用できる、非常に強力で便利な機能です。

座標変換行列を `Mat3x2` によって定義し、`Transformer2D` オブジェクトのコンストラクタに値を設定します。オブジェクトのスコープが有効な間、その行列による座標変換が描画やマウスカーソルに適用されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture textureWindmill{ U"example/windmill.png", TextureDesc::Mipped };
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png", TextureDesc::Mipped };
	const Circle circle{ 200, 400, 60 };

	size_t index = 0;

	while (System::Update())
	{
		// 何もしない行列
		Mat3x2 mat = Mat3x2::Identity();

		if (index == 0)
		{

		}
		else if (index == 1)
		{
			// シーンの中心を基準に 1.5 倍拡大する行列
			mat = Mat3x2::Scale(1.5, Scene::Center());
		}
		else if (index == 2)
		{
			// (50, 50) 並行移動する行列
			mat = Mat3x2::Translate(50, 50);
		}
		else if (index == 3)
		{
			// シーンの中心を回転の軸にして 30° 回転する行列
			mat = Mat3x2::Rotate(30_deg, Scene::Center());
		}
		else if (index == 4)
		{
			// シーンの中心を回転の軸にして徐々に回転しながら拡大する行列
			mat = Mat3x2::Rotate(Scene::Time() * 5_deg, Scene::Center())
				.scaled(0.5 + Scene::Time() * 0.03, Scene::Center());
		}

		{
			// 座標変換行列を適用する（マウスカーソルにも適用）
			const Transformer2D transformer{ mat, TransformCursor::Yes };

			textureWindmill.draw(0, 0);

			textureSiv3DKun.draw(360, 100);

			circle.draw(circle.mouseOver() ? Palette::Red : Palette::Yellow);
		}

		SimpleGUI::RadioButtons(index, { U"Identity", U"Scale", U"Translate", U"Rotate", U"Rotate * Scale" }, Vec2{ 600, 40 });
	}
}
```


## 39.10 マウスカーソルだけに座標変換行列を適用する
ビューポートを使ってミニウィンドウを作成した際など、描画の座標変換は不要でマウスカーソルの座標変換だけ行いたい場合があります。そのようなときは、`Transformer2D` の第 1 引数に `Mat3x2:Identity()` を、第 2 引数にマウスカーソル用の座標変換行列を設定します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		{
			const ScopedViewport2D viewport{ 400, 300, 400, 300 };

			// マウスカーソル座標だけ (400, 300) 並行移動させる
			const Transformer2D transformer{ Mat3x2::Identity(), Mat3x2::Translate(400, 300) };

			Circle{ 200, 150, 200 }.draw();
			Circle{ Cursor::PosF(), 40 }.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Button", Vec2{ 20,20 }))
			{
				Print << U"Pushed";
			}
		}
	}
}
```


## 39.11 座標変換行列を重ねて適用する
`Transformer2D` の効果が適用されているときに新しい `Transformer2D` を作成すると、座標変換の効果が乗算されます。次のプログラムでは、行列の乗算によって複雑な動きを実現しています。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/11.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double t = (Scene::Time() * -30_deg);

		{
			const Transformer2D t0{ Mat3x2::Translate(Scene::Center()) };
			Circle{ 0, 0, 40 }.draw(Palette::Orange);
			Circle{ 0, 0, 160 }.drawFrame(2);

			{
				const Transformer2D t1{ Mat3x2::Translate(160, 0).rotated(t) };
				Circle{ 0, 0, 20 }.draw(Palette::Skyblue);
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


## 39.12 2D カメラ
`Camera2D` を使うと、マウスやキーボードを使った直感的な操作で `Transformer2D` を作成、更新できるようになります。

`Camera2D::update()` では ++w++ / ++a++ / ++s++ / ++d++ キーで上下左右移動、++up++ / ++down++ キーで拡大縮小、マウス右クリックで自由移動、マウスホイールで拡大縮小の操作を行います。キー操作を無効にしたい場合は `Camera2D` コンストラクタに `CameraControl::Mouse` を渡します。キー操作もマウス操作も無効にしたい場合は `CameraControl::None_` を渡します。

カメラの詳細な挙動は `Camera2DParameters` によってカスタマイズできます。

`Camera2D` の主なメンバ関数は次の通りです。

| 関数 | 説明 |
|--|--|
|`.createTransformer()`| 現在のカメラの設定から `Transformer2D` を作成する |
|`.setTargetCenter(Vec2)`| カメラの中心座標の目標を設定する |
|`.setTargetScale(double)`| カメラのズームアップ倍率の目標を設定する |
|`.jumpTo(Vec2, double)`| カメラの中心座標およびズームアップ倍率を即座に変更する |
|`.update()` | カメラの操作や、目標値への移動を行う |
|`.draw(const ColorF&)`| マウスでのカメラ操作を補助する矢印 UI を表示する |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/12.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture cat{ U"🐈"_emoji };

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

			cat.drawAt(0, 0);

			Shape2D::Star(50, Vec2{ 200, 200 }).draw(Palette::Yellow);
		}

		if (SimpleGUI::Button(U"Jump to center", Vec2{ 20, 20 }))
		{
			// 中心とズームアップ倍率を即座に変更する
			camera.jumpTo(Vec2{ 0, 0 }, 1.0);
		}

		if (SimpleGUI::Button(U"Move to center", Vec2{ 20, 60 }))
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


## 39.13 プログラムで制御する 2D カメラ
`CameraControl::None_` を設定した 2D カメラはプログラムで制御します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/13.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture playerTexture{ U"🙂"_emoji };
	const Texture treeTexture{ U"🌳"_emoji };

	// プレイヤーの X 座標
	double playerPosX = 400;

	// 木の X 座標
	Array<int32> trees = { 100, 300, 500, 700, 900 };

	// (400, 300) を中心とする, 拡大率 1.0 倍の, (マウスやキーではなく）プログラムで動かすカメラ
	Camera2D camera{ Scene::Center(), 1.0, CameraControl::None_ };

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

		if (KeyRight.pressed())
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
				// カメラの中心 X 座標と差が 400 ピクセルの物だけを描く（画面外のものを描かない）
				if (Abs(cameraPosX - tree) < 400.0)
				{
					treeTexture.drawAt(tree, 400);
				}
			}

			playerTexture.drawAt(playerPosX, 400);
		}
	}
}
```


## 39.14 ゲームやアプリの高解像度・高精細化
800x600 などの低いシーン解像度で開発したゲームやアプリケーションを、簡単に高解像度・高精細化する方法を説明します。

まず、大きな解像度のウィンドウにシーンを描画するために、`Transformer2D` を用いて描画やマウス座標をスケールアップ・移動できます。さらに、OS 設定の拡大縮小（150% など）を無視してシーンをドットバイドットで表示するために、`ResizeMode::Actual` を設定してシーンを高精細化できます（チュートリアル 32.1 参照）。

デフォルトの `ResizeMode::Virtual` では、例えば 4K 解像度、150% 拡大のノート PC では、フルスクリーン時のシーン解像度が 2560x1440 ですが、`ResizeMode::Actual` では 3840x2160 になります。シーンの解像度が大きいと描画負荷が大きくなることに注意してください。

下記のサンプルでは、800x600 のシーン解像度を想定して開発されたゲームの入力や描画の処理について、個々の描画や入力のコードに変更を加えずに、対応解像度を 800x600 よりも大きくしています。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/2d-render-state/14.png)

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
	const Circle circle{ 600, 260, 100 };

	Rect{ baseSize }.draw(ColorF{ 0.15, 0.6, 0.4 });

	Rect{ 40, 100, 400, 400 }.rounded(15).drawFrame(5);

	font(U"Hello, Siv3D").drawAt(40, Vec2{ 600, 120 });

	circle.draw(circle.mouseOver() ? ColorF{ 1.0 } : ColorF{ 0.75 });

	for (int32 i = 0; i < 8; ++i)
	{
		font(i + 1).drawAt(20, Vec2{ 20, (125 + 50 * i) }, ColorF{ 0.25 });
		font(char32{ U'a' + i }).drawAt(20, Vec2{ (65 + 50 * i), 80 }, ColorF{ 0.25 });
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
		Print << U"オリジナルのシーン解像度: " << BaseSceneSize;
		Print << U"シーン解像度: " << Scene::Size();
		Print << U"シーンの拡大倍率 = " << scale;
		Print << U"オフセット = " << offset;

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
							Window::SetFullscreen(!Window::GetState().fullscreen);
						}
					}
					WindowModeButton.drawFrame(2);
					font(Window::GetState().fullscreen ? U"ウィンドウにする" : U"フルスクリーンにする").drawAt(16, WindowModeButton.center(), ColorF{ 0.25 });
				}

				// DotByDot ボタン
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
					font((Scene::GetResizeMode() == ResizeMode::Actual) ? U"OS スケールに合わせる" : U"Dot by Dot にする").drawAt(16, DotByDotButton.center(), ColorF{ 0.25 });
				}
			}
		}
	}
}
```
