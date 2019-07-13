
# 2D 物理演算

## 鉄球による破壊
<video src="../images/2d-physics-0.mp4" autoplay loop muted></video>
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
		world.update(useConstantDeltaTime ? (1.0 / 60.0) : Scene::DeltaTime(), 12, 4);

		// 落下した P2Body は削除
		bodies.remove_if([](const P2Body& body) { return body.getPos().y > 20; });

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