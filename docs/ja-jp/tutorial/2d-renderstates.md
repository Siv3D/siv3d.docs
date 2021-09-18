description: OpenSiv3D のチュートリアル

!!! warning "このページよりも新しいドキュメントがあります"
	このドキュメントは古い OpenSiv3D v0.4.3 向けです。2021 年9 月 18 日に最新の OpenSiv3D v0.6.0 がリリースされました。最新のドキュメントは [Siv3D リファレンス v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation) です。

# 15. 2D レンダーステート

この章では、2D 描画の設定をカスタマイズして、表現の幅を広げる方法を学びます。

## 15.1 加算ブレンド
`ScopedRenderStates2D` オブジェクトのコンストラクタに `BlendState::Additive` を渡すと、そのオブジェクトのスコープが有効な間、図形や画像が加算ブレンドで描画されます。加算ブレンドでは、背景色に RGB 成分を加算するように描画されるので、重ねて描画した部分が明るくなります。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/15/1-0.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	Array<Vec2> points;

	for (int32 i = 0; i < 400; ++i)
	{
		points << RandomVec2(Scene::Rect());
	}

	bool enabled = true;

	while (System::Update())
	{
		if (enabled)
		{
			// 加算ブレンド有効
			const ScopedRenderStates2D state(BlendState::Additive);

			for (const auto& point : points)
			{
				Circle(point, 20).draw(HSV(point.y * 100 + point.x * 100, 0.5));
			}
		}
		else
		{
			// 通常のブレンドモード

			for (const auto& point : points)
			{
				Circle(point, 20).draw(HSV(point.y * 100 + point.x * 100, 0.5));
			}
		}

		SimpleGUI::CheckBox(enabled, U"AdditiveBlend", Vec2(20, 20));
	}
}
```


## 15.2 描画時に色を加算
画像や図形を描くときに、本来の色に RGBA 成分を加算して描画するには、`ScopedColorAdd2D` オブジェクトのコンストラクタに、加算したい値を設定します。そのオブジェクトのスコープが有効な間、描画の RGBA 値が加算されます。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/15/2-0.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture textureWindmill(U"example/windmill.png");
	const Texture textureSiv3DKun(U"example/siv3d-kun.png");
	ColorF add(0.0, 0.0, 0.0, 0.0);

	while (System::Update())
	{
		{
			// 描画時に色を加算
			const ScopedColorAdd2D state(add);

			textureWindmill.draw(40, 20);
			textureSiv3DKun.draw(400, 100);
		}

		SimpleGUI::Slider(U"R", add.r, Vec2(620, 20), 40);
		SimpleGUI::Slider(U"G", add.g, Vec2(620, 60), 40);
		SimpleGUI::Slider(U"B", add.b, Vec2(620, 100), 40);
	}
}
```


## 15.3 描画時に色を乗算
画像や図形を描くときに、本来の色に RGBA 成分を乗算して描画するには、`ScopedColorMul2D` オブジェクトのコンストラクタに、乗算したい値を設定します。そのオブジェクトのスコープが有効な間、描画の RGBA 値が乗算されます。

なお、`.draw()` に色を渡すことで、個別に乗算の色を設定することもできます（チュートリアル 5.10 参照）。`ScopedColorMul2D` はその設定を一括して適用できるものです。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/15/3-0.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture textureWindmill(U"example/windmill.png");
	const Texture textureSiv3DKun(U"example/siv3d-kun.png");
	ColorF add(1.0, 1.0, 1.0, 1.0);

	while (System::Update())
	{
		{
			// 描画時に色を乗算
			const ScopedColorMul2D state(add);

			textureWindmill.draw(40, 20);
			textureSiv3DKun.draw(400, 100);
		}

		SimpleGUI::Slider(U"R", add.r, Vec2(620, 20), 40);
		SimpleGUI::Slider(U"G", add.g, Vec2(620, 60), 40);
		SimpleGUI::Slider(U"B", add.b, Vec2(620, 100), 40);
	}
}
```


## 15.4 テクスチャサンプリングのフィルタ
テクスチャを拡大縮小して描画する際に、デフォルトでは線形補間によって色が滑らかに補間されます。ドット感を保ったまま拡大したいときにはサンプラーステート `SamplerState::ClampNearest` を `ScopedRenderStates2D` で設定します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/15/4-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture cat(Emoji(U"🐈"));
	bool linear = true;
	double scale = 1.0;

	while (System::Update())
	{
		if (linear)
		{
			// 通常のサンプラーモード
			cat.scaled(scale).drawAt(Scene::Center());
		}
		else
		{
			// 線形補間なし
			const ScopedRenderStates2D state(SamplerState::ClampNearest);
			cat.scaled(scale).drawAt(Scene::Center());
		}

		SimpleGUI::Slider(scale, 0.1, 8.0, Vec2(20, 20), 200);
		SimpleGUI::CheckBox(linear, U"Linear", Vec2(20, 60));
	}
}
```


## 15.5 ビューポート
`ScopedViewport2D` オブジェクトを作成すると、シーン内に仮想のシーンを作り、新しい長方形の描画領域を定義できます。描画時にはビューポートの長方形の左上が (0, 0) の描画座標になり、長方形の範囲外にはみ出たものは描画されなくなります。ビューポートは描画の座標にしか影響を及ぼさないので、マウスカーソルの座標も同様に移動させたい場合には、後述する `Transformer2D` と組み合わせます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/15/5-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture cat(Emoji(U"🐈"));

	// ビューポートの領域
	Rect viewportRect(200, 200, 400, 200);
	bool grab = false;

	while (System::Update())
	{
		if (grab)
		{
			viewportRect.pos.moveBy(Cursor::Delta());
		}

		// ミニウィンドウのタイトルバー
		const Rect bar(viewportRect.pos.movedBy(0, -40), viewportRect.w, 40);

		// タイトルバーをつかんで移動
		if (bar.leftClicked())
		{
			grab = true;
		}
		else if (MouseL.up())
		{
			grab = false;
		}

		bar.stretched(2, 2, 0, 2).draw(Palette::Seagreen);
		viewportRect.drawFrame(0, 2, Palette::Seagreen);

		{
			// ビューポートの適用
			const ScopedViewport2D viewport(viewportRect);

			Rect(0, 0, viewportRect.size).draw(ColorF(0.8, 0.9, 1.0));

			Circle(200, 100, 150).draw();

			cat.drawAt(20, 20);
		}
	}
}
```


## 15.6 座標変換
`Transformer2D` は、描画やマウスカーソル座標に対して、一括で回転・拡大縮小、座標移動などの座標変換を適用できる、非常に強力な機能です。

座標変換行列を `Mat3x2` によって定義し、`Transformer2D` オブジェクトのコンストラクタに値を設定します。オブジェクトのスコープが有効な間、その行列による座標変換が描画やマウスカーソルに適用されます。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/15/6-0.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture textureWindmill(U"example/windmill.png");
	const Texture textureSiv3DKun(U"example/siv3d-kun.png");
	constexpr Circle circle(200, 400, 60);

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
			// シーンの中心を基準に 1.5 倍拡大
			mat = Mat3x2::Scale(1.5, Scene::Center());
		}
		else if (index == 2)
		{
			// (50, 50) 移動
			mat = Mat3x2::Translate(50, 50);
		}
		else if (index == 3)
		{
			// シーンの中心を回転の軸にして 30° 回転
			mat = Mat3x2::Rotate(30_deg, Scene::Center());
		}
		else if (index == 4)
		{
			// シーンの中心を回転の軸にして徐々に回転しながら拡大
			mat = Mat3x2::Rotate(Scene::Time() * 5_deg, Scene::Center())
				.scaled(1.0 + Scene::Time() * 0.03, Scene::Center());
		}

		{
			// マウスカーソルの座標も描画と同様に変換
			constexpr bool transformCursorPos = true;

			// 座標変換行列を適用
			const Transformer2D t(mat, transformCursorPos);

			textureWindmill.draw(0, 0);

			textureSiv3DKun.draw(360, 100);

			circle.draw(circle.mouseOver() ? Palette::Red : Palette::Yellow);
		}

		SimpleGUI::RadioButtons(index, { U"Identity", U"Scale", U"Translate", U"Rotate", U"Roatate * Scale" }, Vec2(600, 20));
	}
}
```

### マウスカーソルのみ座標変換
ビューポートを使ってミニウィンドウを作成した際など、描画の座標変換は不要でマウスカーソルの座標変換だけ行いたい場合があります。そのようなときは、`Transformer2D` の第 1 引数に `Mat3x2:Identity()` を、第 2 引数にマウスカーソル用の座標変換行列を設定します。

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture cat(Emoji(U"🐈"));

	// ビューポートの領域
	Rect viewportRect(200, 200, 400, 200);
	bool grab = false;

	while (System::Update())
	{
		ClearPrint();

		if (grab)
		{
			viewportRect.pos.moveBy(Cursor::Delta());
		}

		// ミニウィンドウのタイトルバー
		const Rect bar(viewportRect.pos.movedBy(0, -40), viewportRect.w, 40);

		// タイトルバーをつかんで移動
		if (bar.leftClicked())
		{
			grab = true;
		}
		else if (MouseL.up())
		{
			grab = false;
		}

		bar.stretched(2, 2, 0, 2).draw(Palette::Seagreen);
		viewportRect.drawFrame(0, 2, Palette::Seagreen);

		{
			// ビューポートの適用
			const ScopedViewport2D viewport(viewportRect);
			
			// 描画は座標変換せず、マウスカーソル座標だけ変換
			const Transformer2D transform(Mat3x2::Identity(), Mat3x2::Translate(viewportRect.pos));

			Rect(0, 0, viewportRect.size).draw(ColorF(0.8, 0.9, 1.0));

			Circle(200, 100, 150).draw();

			cat.drawAt(20, 20);

			// マウスカーソル座標が変換されている
			Print << Cursor::PosF();
			Circle(Cursor::PosF(), 20).draw(ColorF(1.0, 0.0, 0.0, 0.5));
		}
	}
}
```

### Transformer2D の効果の乗算
`Transformer2D` の効果が適用されているときに新しい `Transformer2D` を作成すると、座標変換の効果が乗算されます。次のプログラムでは、重ねがけによって複雑な動きを実現しています。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/15/6-1.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double t = Scene::Time() * -30_deg;

		const Transformer2D t0(Mat3x2::Translate(Scene::Center()));

		Circle(0, 0, 40).draw(Palette::Orange);

		Circle(0, 0, 160).drawFrame();

		const Transformer2D t1(Mat3x2::Translate(160, 0).rotated(t));

		Circle(0, 0, 20).draw(Palette::Skyblue);

		Circle(0, 0, 40).drawFrame();

		const Transformer2D t2(Mat3x2::Translate(40, 0).rotated(t * 4));

		Circle(0, 0, 10).draw(Palette::White);
	}
}
```


## 15.7 2D カメラ
`Camera2D` を使うと、マウスやキーボードを使った直感的な操作で `Transformer2D` を作成、更新できるようになります。

`Camera2D::update()` では W/A/S/D キーで上下左右移動、↑/↓ キーで拡大縮小、マウス右クリックで自由移動、マウスホイールで拡大縮小の操作を行います。キー操作を無効にしたい場合は `Camera2D` コンストラクタに `Camera2DParameters::MouseOnly()` を渡します。カメラの挙動は `Camera2DParameters` によってカスタマイズできます。

`Camera2D::draw()` ではマウスでのカメラ操作を補助する矢印 UI を表示します。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/15/7-0.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.6, 0.8, 0.7));

	const Texture cat(Emoji(U"🐈"));

	// 2D カメラ
	// 中心が (0, 0), 拡大率 1.0 になるようなカメラ
	Camera2D camera(Vec2(0, 0), 1.0);

	while (System::Update())
	{
		// 2D カメラを更新
		camera.update();
		{
			// 2D カメラの設定から Transformer2D を作成
			const auto t = camera.createTransformer();

			for (int32 i = 0; i < 8; ++i)
			{
				Circle(0, 0, 50 + i * 50).drawFrame(2);
			}

			cat.drawAt(0, 0);

			Shape2D::Star(50, Vec2(200, 200)).draw(Palette::Yellow);
		}

		if (SimpleGUI::Button(U"Reset", Vec2(20, 20)))
		{
			// 2D カメラのパラメータをリセット
			camera.setCenter(Vec2(0, 0));
			camera.setTargetCenter(Vec2(0, 0));
			camera.setScale(1.0);
			camera.setTargetScale(1.0);
		}

		// 2D カメラ操作の UI を表示
		camera.draw(Palette::Orange);
	}
}

```


## 15.8 ワイヤフレームモードで描画
`ScopedRenderStates2D` オブジェクトのコンストラクタに `RasterizerState::WireframeCullNone` を渡すと、図形や画像を構成するポリゴンのワイヤフレームのみが描画されるようになります。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/15/8-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture textureWindmill(U"example/windmill.png");

	while (System::Update())
	{
		// ワイヤフレーム表示モードに
		const ScopedRenderStates2D rasterizer(RasterizerState::WireframeCullNone);

		textureWindmill.draw(20, 20);

		Circle(Scene::Center(), 100).draw();

		Shape2D::Star(100, Vec2(150, 400)).draw(Palette::Yellow);
	}
}
```


## 15.9 テクスチャのくり返し
`ScopedRenderStates2D` オブジェクトのコンストラクタにサンプラーステートを渡すことで、テクスチャ描画時に UV 座標が 0.0～1.0 の範囲を超えたときの処理の方法をカスタマイズできます。

`Texture::mapped()` によって、指定したサイズだけテクスチャをくり返しマッピングするような `TextureRegion` を作成できます。それをサンプラーステート `SamplerState::RepeatLinear` が適用されている状態で `.draw()` すると、テクスチャの内容がくり返しマッピングされて描画されます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/15/9-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));

	const Texture tree(Emoji(U"🌲"));

	while (System::Update())
	{
		// UV 座標が 0.0～1.0 の範囲を超えたとき、くり返しマッピング
		const ScopedRenderStates2D rasterizer(SamplerState::RepeatLinear);

		// シーンのサイズぴったりにマッピングして描画
		tree.mapped(Scene::Size()).draw();
	}
}
```


## 15.10 レンダーステートのプログラムあれこれ

###　レンダーステートのパラメータ
`ScopedRenderStates2D` は、コンストラクタの引数に `BlendState`, `SamplerState`, `RasterizerState` の 3 つを一度に設定できます。`BlendState`, `SamplerState`, `RasterizerState` は、この章で紹介した以外にも多くのパラメータを持ち、様々なカスタマイズが可能です。2D 描画におけるデフォルト値は `BlendState::Default`, `SamplerState::Default2D`, `RasterizerState::Default2D` で定義されています。

### Scoped～ のはたらき
`Scoped～` 系のオブジェクトや `Transformer2D` は、ソースコード上では何も働いていないように見えます。しかし、実際はコンストラクタでレンダーステートを設定し、自身が破棄されるとき（スコープが終了するとき）デストラクタでレンダーステートを最初の状態に戻す処理を行っています。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/15/10-1.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture cat(Emoji(U"🐈"));

	while (System::Update())
	{
		{
			// 線形補間を無効にするレンダーステートを有効化
			const ScopedRenderStates2D state(SamplerState::ClampNearest);

			cat.scaled(4).drawAt(200, 300);
		
		} // ここで state のデストラクタが呼び出され、レンダーステートが初期状態に

		cat.scaled(4).drawAt(600, 300);
	}
}
```

