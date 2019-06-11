
# Game
## Breakout
![](images/game-breakout.gif)
```C++
# include <Siv3D.hpp>

void Main()
{
	// ブロックのサイズ
	constexpr Size blockSize(40, 20);

	// ブロックの配列
	Array<Rect> blocks;

	// 横 (Scene::Width() / blockSize.x) 個、縦 5 個のブロックを配列に追加する
	for (auto p : step(Size((Scene::Width() / blockSize.x), 5)))
	{
		blocks << Rect(p.x * blockSize.x, 60 + p.y * blockSize.y, blockSize);
	}

	// ボールの速さ
	constexpr double speed = 480.0;

	// ボールの速度
	Vec2 ballVelocity(0, -speed);

	// ボール
	Circle ball(400, 400, 8);

	while (System::Update())
	{
		// パドル
		const Rect paddle(Arg::center(Cursor::Pos().x, 500), 60, 10);

		// ボールを移動
		ball.moveBy(ballVelocity * Scene::DeltaTime());

		// ブロックを順にチェック
		for (auto it = blocks.begin(); it != blocks.end(); ++it)
		{
			// ボールとブロックが交差していたら
			if (it->intersects(ball))
			{
				// ボールの向きを反転する
				(it->bottom().intersects(ball) || it->top().intersects(ball) ? ballVelocity.y : ballVelocity.x) *= -1;
				
				// ブロックを配列から削除（イテレータが無効になるので注意）
				blocks.erase(it);

				// これ以上チェックしない	
				break;
			}
		}

		// 天井にぶつかったらはね返る
		if (ball.y < 0 && ballVelocity.y < 0)
		{
			ballVelocity.y *= -1;
		}

		// 左右の壁にぶつかったらはね返る
		if ((ball.x < 0 && ballVelocity.x < 0) || (Scene::Width() < ball.x && ballVelocity.x > 0))
		{
			ballVelocity.x *= -1;
		}

		// パドルにあたったらはね返る
		if (ballVelocity.y > 0 && paddle.intersects(ball))
		{
			// パドルの中心からの距離に応じてはね返る向きを変える
			ballVelocity = Vec2((ball.x - paddle.center().x) * 10, -ballVelocity.y).setLength(speed);
		}

		// すべてのブロックを描画する
		for (const auto& block : blocks)
		{
			block.stretched(-1).draw(HSV(block.y - 40));
		}

		// ボールを描く
		ball.draw();

		// パドルを描く
		paddle.draw();
	}
}
```

## Pinball
![](images/game-pinball.gif)
```C++
# include <Siv3D.hpp>

// 外周の枠の頂点リストを作成
Array<Vec2> CreateFrame(const Vec2& leftAnchor, const Vec2& rightAnchor)
{
	Array<Vec2> points = { leftAnchor, Vec2(-7, -2) };
	for (auto i : Range(-30, 30))
	{
		points << OffsetCircular(Vec2(0.0, -12.0), 7, i * 3_deg);
	}
	return points << Vec2(7, -2) << rightAnchor;
}

// 接触しているかに応じて色を決定
ColorF GetColor(const P2Body& body, const Array<P2BodyID>& list)
{
	return list.includes(body.id()) ? Palette::White : Palette::Orange;
}

void Main()
{
	// フレームレートを 60 に固定
	Graphics::SetTargetFrameRateHz(60);
	// フレームレートに依存しない、物理シミュレーションの更新
	constexpr double timeDelta = 1.0 / 60.0;

	// 背景色を設定
	Scene::SetBackground(ColorF(0.2, 0.3, 0.4));
	
	// 物理演算用のワールド
	P2World world(6.0);

	// 左右フリッパーの軸の座標
	constexpr Vec2 leftFlipperAnchor(-2.5, 1), rightFlipperAnchor(2.5, 1);

	// 固定の枠
	Array<P2Body> frames;
	// 外周
	frames << world.createStaticLineString(Vec2(0, 0), LineString(CreateFrame(leftFlipperAnchor, rightFlipperAnchor)));
	// 左上の (
	frames << world.createStaticLineString(Vec2(0, 0), LineString(Range(-25, -10).map([=](int32 i) { return OffsetCircular(Vec2(0.0, -12.0), 5.5, i * 3_deg).toVec2(); })));
	// 右上の )
	frames << world.createStaticLineString(Vec2(0, 0), LineString(Range(10, 25).map([=](int32 i) { return OffsetCircular(Vec2(0.0, -12.0), 5.5, i * 3_deg).toVec2(); })));

	// バンパー
	Array<P2Body> bumpers;
	// ● x3
	bumpers << world.createStaticCircle(Vec2(0, -17), 0.5, P2Material(1.0, 1.0));
	bumpers << world.createStaticCircle(Vec2(-2, -15), 0.5, P2Material(1.0, 1.0));
	bumpers << world.createStaticCircle(Vec2(2, -15), 0.5, P2Material(1.0, 1.0));
	// ▲ x2
	bumpers << world.createStaticTriangle(Vec2(0, 0), Triangle(-6, -5, -4, -1.5, -6, -3), P2Material(1.0, 0.8));
	bumpers << world.createStaticTriangle(Vec2(0, 0), Triangle(6, -5, 6, -3, 4, -1.5), P2Material(1.0, 0.8));

	// 左フリッパー
	P2Body leftFlipper = world.createDynamicRect(leftFlipperAnchor, RectF(0.0, 0.04, 2.1, 0.45), P2Material(0.1, 0.0));
	// 左フリッパーのジョイント
	const P2PivotJoint leftJoint = world.createPivotJoint(frames[0], leftFlipper, leftFlipperAnchor).setLimits(-20_deg, 25_deg).setLimitEnabled(true);

	// 右フリッパー
	P2Body rightFlipper = world.createDynamicRect(rightFlipperAnchor, RectF(-2.1, 0.04, 2.1, 0.45), P2Material(0.1, 0.0));
	// 右フリッパーのジョイント
	const P2PivotJoint rightJoint = world.createPivotJoint(frames[0], rightFlipper, rightFlipperAnchor).setLimits(-25_deg, 20_deg).setLimitEnabled(true);

	// スピナー ＋
	const P2Body spinner = world.createDynamicRect(Vec2(-5.8, -12), SizeF(2.0, 0.1), P2Material(0.1, 0.0)).addRect(SizeF(0.1, 2.0), P2Material(0.01, 0.0));
	// スピナーのジョイント
	P2PivotJoint spinnerJoint = world.createPivotJoint(frames[0], spinner, Vec2(-5.8, -12)).setMaxMotorTorque(0.05).setMotorSpeed(0).setMotorEnabled(true);

	// 風車の |
	frames << world.createStaticLine(Vec2(0, 0), Line(-4, -6, -4, -4));
	// 風車の羽 ／
	const P2Body windmillWing = world.createDynamicRect(Vec2(-4, -6), SizeF(3.0, 0.2), P2Material(0.1, 0.8));
	// 風車のジョイント
	const P2PivotJoint windmillJoint = world.createPivotJoint(frames.back(), windmillWing, Vec2(-4, -6)).setMotorSpeed(240_deg).setMaxMotorTorque(10000.0).setMotorEnabled(true);

	// 振り子の軸
	const P2Body pendulumbase = world.createStaticDummy(Vec2(0, -19));
	// 振り子 ●
	P2Body pendulum = world.createDynamicCircle(Vec2(0, -12), 0.4, P2Material(0.1, 1.0));
	// 振り子のジョイント
	const P2DistanceJoint pendulumJoint = world.createDistanceJoint(pendulumbase, Vec2(0, -19), pendulum, Vec2(0, -12), 7);

	// エレベーターの上部 ●
	const P2Body elevatorA = world.createStaticCircle(Vec2(4, -10), 0.3);
	// エレベーターの床 －
	const P2Body elevatorB = world.createRect(Vec2(4, -10), SizeF(2.0, 0.2));
	// エレベーターのジョイント
	P2SliderJoint elevatorSliderJoint = world.createSliderJoint(elevatorA, elevatorB, Vec2(4, -10), Vec2(0, 1)).setLimits(0.5, 5.0).setLimitEnabled(true).setMaxMotorForce(10000).setMotorSpeed(-10);

	// ボール 〇
	const P2Body ball = world.createDynamicCircle(Vec2(-4, -12), 0.4, P2Material(0.05, 0.0));
	const P2BodyID ballID = ball.id();

	// エレベーターのアニメーション用ストップウォッチ
	Stopwatch sliderStopwatch(true);

	// 2D カメラ
	const Camera2D camera(Vec2(0, -8), 24.0);

	while (System::Update())
	{
		/////////////////////////////////////////
		//
		// 更新
		//

		// 振り子の抵抗
		pendulum.applyForce(Vec2(pendulum.getVelocity().x < 0.0 ? 0.01 : -0.01, 0.0));

		if (sliderStopwatch > 4s)
		{
			// エレベーターの巻き上げを停止
			elevatorSliderJoint.setMotorEnabled(false);
			sliderStopwatch.restart();
		}
		else if (sliderStopwatch > 2s)
		{
			// エレベーターの巻き上げ
			elevatorSliderJoint.setMotorEnabled(true);
		}

		// 左フリッパーの操作
		leftFlipper.applyTorque(KeyLeft.pressed() ? -80 : 40);

		// 右フリッパーの操作
		rightFlipper.applyTorque(KeyRight.pressed() ? 80 : -40);

		// 物理演算ワールドの更新
		world.update(timeDelta, 24, 12);

		// ボールと接触しているボディの ID を取得
		Array<P2BodyID> collidedIDs;
		for (auto [pair, collision] : world.getCollisions())
		{
			if (pair.a == ballID)
			{
				collidedIDs << pair.b;
			}
			else if (pair.b == ballID)
			{
				collidedIDs << pair.a;
			}
		}

		/////////////////////////////////////////
		//
		// 描画
		//

		// 描画用の Transformer2D
		const auto transformer = camera.createTransformer();

		// 枠の描画
		for (const auto& frame : frames)
		{
			frame.draw(Palette::Skyblue);
		}

		// スピナーの描画
		spinner.draw(GetColor(spinner, collidedIDs));

		// バンパーの描画
		for (const auto& bumper : bumpers)
		{
			bumper.draw(GetColor(bumper, collidedIDs));
		}

		// 風車の描画
		windmillWing.draw(GetColor(windmillWing, collidedIDs));
		
		// 振り子の描画
		pendulum.draw(GetColor(pendulum, collidedIDs));
		
		// エレベーターの描画
		elevatorA.draw(GetColor(elevatorA, collidedIDs));
		elevatorB.draw(GetColor(elevatorB, collidedIDs));

		// ボールの描画
		ball.draw(Palette::White);

		// フリッパーの描画
		leftFlipper.draw(Palette::Orange);
		rightFlipper.draw(Palette::Orange);

		// ジョイントの可視化
		leftJoint.draw(Palette::Red);
		rightJoint.draw(Palette::Red);
		spinnerJoint.draw(Palette::Red);
		windmillJoint.draw(Palette::Red);
		pendulumJoint.draw(Palette::Red);
		elevatorSliderJoint.draw(Palette::Red);
	}
}
```

## Emoji Tower
<video src="../images/game-emoji-tower.mp4" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	// 背景色を設定
	Scene::SetBackground(ColorF(0.3, 0.6, 0.9));

	// 登場する絵文字
	const Array<String> emojis = { U"🐘", U"🐧", U"🐐", U"🐤" };

	// 画像のスケール
	constexpr double scale = 0.04;

	// 絵文字の形状情報とテクスチャを作成
	Array<MultiPolygon> polygons;
	Array<Texture> textures;
	for (const auto& emoji : emojis)
	{
		// 絵文字の画像から形状情報を作成
		polygons << Emoji::CreateImage(emoji).alphaToPolygonsCentered().simplified(0.8).scale(scale);
		
		// 絵文字の画像からテクスチャを作成
		textures << Texture(Emoji(emoji));
	}

	// 物理演算用のワールド
	P2World world;

	// 床 －
	const P2Body line = world.createStaticLine(Vec2(0, 0), Line(-12, 0, 12, 0), P2Material(1, 0.1, 1.0));
	
	// 登場した絵文字のボディ
	Array<P2Body> bodies;

	// ボディ ID と絵文字のインデックスの対応テーブル
	HashTable<P2BodyID, size_t> table;

	// 2D カメラ
	Camera2D camera(Vec2(0, -8), 20);

	// 絵文字のインデックス
	size_t index = Random(polygons.size() - 1);

	while (System::Update())
	{
		// 物理演算ワールドの更新
		world.update();

		// 2D カメラの操作と更新
		camera.update();

		// Transformer2D の作成
		auto t = camera.createTransformer();

		// 左クリックされたら
		if (MouseL.down())
		{
			// ボディを追加
			bodies << world.createPolygons(Cursor::PosF(), polygons[index], P2Material(0.1, 0.0, 1.0));
			
			// ボディ ID と絵文字のインデックスの対応を追加
			table.emplace(bodies.back().id(), std::exchange(index, Random(polygons.size() - 1)));
		}

		// すべてのボディを描画
		for (const auto& body : bodies)
		{
			textures[table[body.id()]].scaled(scale).rotated(body.getAngle()).drawAt(body.getPos());
		}

		// 床を描画
		line.draw(Palette::Green);
		
		// 現在操作できる絵文字を描画
		textures[index].scaled(scale).drawAt(Cursor::PosF(), AlphaF(0.5 + Periodic::Sine0_1(1s) * 0.5));

		// 2D カメラ操作のエフェクトを表示
		camera.draw(Palette::Orange);
	}
}
```
