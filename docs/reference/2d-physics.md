
# 2D physics

## ワールドテンプレート 1
クリックしたところにボールが発生するシンプルな物理演算ワールドです。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/reference/2d-physics/template1.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	// 物理演算のワールド更新に 60FPS の定数時間を使う場合は true, 実時間を使う場合 false
	constexpr bool useConstantDeltaTime = true;
	if (useConstantDeltaTime)
	{
		// フレームレート上限を 60 FPS に 
		Graphics::SetTargetFrameRateHz(60);
	}

	// 2D カメラ
	Camera2D camera(Vec2(0, -8), 20.0);

	// 物理演算の精度
	constexpr int32 velocityIterations = 12;
	constexpr int32 positionIterations = 4;

	// 物理演算用のワールド
	P2World world(9.8);
	// 床
	const P2Body line = world.createStaticLine(Vec2(0, 0), Line(-16, 0, 16, 0));
	// 物体
	Array<P2Body> bodies;

	while (System::Update())
	{
		ClearPrint();
		Print << U"Balls: {}"_fmt(bodies.size());

		// 2D カメラを更新
		camera.update();
		{
			// 2D カメラの設定から Transformer2D を作成・適用
			const auto t = camera.createTransformer();

			if (MouseL.down())
			{
				// クリックした場所にボールを作成
				bodies << world.createCircle(Cursor::PosF(), 0.5);
			}

			// (y > 10) まで落下した P2Body は削除
			bodies.remove_if([](const P2Body& body) { return body.getPos().y > 10; });

			// 物理演算のワールドを更新
			world.update(useConstantDeltaTime ? (1.0 / 60.0) : Scene::DeltaTime(), velocityIterations, positionIterations);

			// 床を描画
			line.draw(Palette::Skyblue);

			// 物体を描画
			for (const auto& body : bodies)
			{
				body.draw(HSV(body.id() * 10, 0.7, 0.9));
			}
		}

		// 2D カメラ操作の UI を表示
		camera.draw(Palette::Orange);
	}
}
```


## 鉄球による破壊
<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/reference/2d-physics/1.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF(0.4, 0.7, 1.0));

	constexpr bool useConstantDeltaTime = true;
	if (useConstantDeltaTime)
	{
		Graphics::SetTargetFrameRateHz(60);
	}

	P2World world(9.8);
	P2Body line = world.createStaticLine(Vec2(0, 0), Line(-16, 0, 16, 0));
	Array<P2Body> bodies;

	for (auto y : Range(0, 12))
	{
		for (auto x : Range(0, 20))
		{
			bodies << world.createDynamicRect(Vec2(x * 0.5, -0.5 - y * 1), SizeF(0.5, 1), P2Material(0.02, 0.0, 1.0));
		}
	}

	for (auto x : Range(0, 9))
	{
		bodies << world.createDynamicRect(Vec2(0.5 + x * 1.0, -13.5), SizeF(1, 1), P2Material(0.02, 0.0, 1.0));
	}

	// 振り子の軸
	constexpr Vec2 base(0, -24);	
	// チェーンの数
	constexpr int32 chainCount = 16;
	// 鉄球の半径
	constexpr double ballR = 2.0;
	// 鉄球の初期座標
	constexpr Vec2 ballCenter = base.movedBy(-chainCount - ballR, 0);

	// 鉄球
	const P2Body ball = world.createDynamicCircle(ballCenter, ballR);
	// 振り子の軸
	bodies << world.createStaticDummy(base);
	// 振り子のジョイント
	Array<P2PivotJoint> joints;

	for (auto i : step(chainCount))
	{
		const RectF rect(Arg::rightCenter = base.movedBy(0 - i , 0), 1.0, 0.1);
		bodies << world.createDynamicRect(rect.center(), rect.size);	
		joints << world.createPivotJoint(bodies[bodies.size() - 2], bodies.back(), rect.rightCenter());
	}

	joints << world.createPivotJoint(bodies.back(), ball, base.movedBy(-chainCount, 0));

	// ストッパー
	bool hasStopper = true;
	bodies << world.createStaticLine(ballCenter.movedBy(0, 2), Line(-4, 2, 4, 0));

	Camera2D camera(Vec2(0, -12), 24.0);

	while (System::Update())
	{
		// 落下した P2Body は削除
		bodies.remove_if([](const P2Body& body) { return body.getPos().y > 20; });

		world.update(useConstantDeltaTime ? (1.0 / 60.0) : Scene::DeltaTime(), 12, 4);

		camera.update();
		{
			const auto t = camera.createTransformer();

			line.draw(Palette::Skyblue);

			for (const auto& body : bodies)
			{
				body.draw(ColorF(0.6, 0.4, 0.2));
			}

			ball.draw(ColorF(0.25));
		}

		if (SimpleGUI::Button(U"Go", Vec2(1100, 20)) && hasStopper)
		{
			bodies.pop_back();
			hasStopper = false;
		}

		camera.draw(Palette::Orange);
	}

	// ジョイント（joints は 関連する Body より先に破棄されなければならない）
	joints.clear();
}
```