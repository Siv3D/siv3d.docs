
# Game
## Breakout
![](https://github.com/Siv3D/siv3d.docs.images/blob/master/sample/game/breakout.gif?raw=true)
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
![](https://github.com/Siv3D/siv3d.docs.images/blob/master/sample/game/pinball.gif?raw=true)
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
<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/sample/game/emoji-tower.mp4?raw=true" autoplay loop muted></video>

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


## Shooter
<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/sample/game/shooter.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

// 敵の位置をランダムに作成する関数
Vec2 GenerateEnemy()
{
	return RandomVec2({ 50, 750 }, -20);
}

void Main()
{
	Scene::SetBackground(ColorF(0.1, 0.2, 0.7));

	const Font font(30);

	// 自機テクスチャ
	const Texture playerTexture(Emoji(U"🤖"));
	// 敵テクスチャ
	const Texture enemyTexture(Emoji(U"👾"));

	// 自機
	Vec2 playerPos(400, 500);
	// 敵
	Array<Vec2> enemies = { GenerateEnemy() };

	// 自機ショット
	Array<Vec2> playerBullets;
	// 敵ショット
	Array<Vec2> enemyBullets;

	// 自機のスピード
	constexpr double playerSpeed = 550.0;
	// 自機ショットのスピード
	constexpr double playerBulletSpeed = 500.0;
	// 敵のスピード
	constexpr double enemySpeed = 100.0;
	// 敵ショットのスピード
	constexpr double enemyBulletSpeed = 300.0;

	// 敵の発生間隔の初期値（秒）
	double initialEnemySpawnTime = 2.0;
	// 敵の発生間隔（秒）
	double enemySpawnTime = initialEnemySpawnTime;
	// 敵の発生間隔タイマー
	double enemySpawnTimer = 0.0;

	// 自機ショットのクールタイム（秒）
	constexpr double playerShotCoolTime = 0.1;
	// 自機ショットのクールタイムタイマー
	double playerShotTimer = 0.0;

	// 敵ショットのクールタイム（秒）
	constexpr double enemyShotCoolTime = 0.90;
	// 敵ショットのクールタイムタイマー
	double enemyShotTimer = 0.0;

	Effect effect;

	// ハイスコア
	int32 highScore = 0;
	// 現在のスコア
	int32 score = 0;

	while (System::Update())
	{
		// ゲームオーバー判定
		bool gameover = false;

		const double deltaTime = Scene::DeltaTime();
		enemySpawnTimer += deltaTime;
		playerShotTimer = Min(playerShotTimer + deltaTime, playerShotCoolTime);
		enemyShotTimer += deltaTime;

		// 敵の発生
		while (enemySpawnTimer > enemySpawnTime)
		{
			enemySpawnTimer -= enemySpawnTime;
			enemySpawnTime = Max(enemySpawnTime * 0.95, 0.3);
			enemies << GenerateEnemy();
		}

		//-------------------
		//
		// 移動
		//

		// 自機の移動
		const Vec2 move = Vec2(KeyRight.pressed() - KeyLeft.pressed(), KeyDown.pressed() - KeyUp.pressed())
			.setLength(deltaTime * playerSpeed * (KeyShift.pressed() ? 0.5 : 1.0));
		playerPos.moveBy(move).clamp(Scene::Rect());

		// 自機ショットの発射
		if (playerShotTimer >= playerShotCoolTime)
		{
			playerShotTimer = 0.0;
			playerBullets << playerPos.movedBy(0, -50);
		}

		// 自機ショットの移動
		for (auto& playerBullet : playerBullets)
		{
			playerBullet.y += deltaTime * -playerBulletSpeed;
		}
		// 画面外に出た自機ショットは消滅
		playerBullets.remove_if([](const Vec2& b) { return b.y < -40; });

		// 敵の移動
		for (auto& enemy : enemies)
		{
			enemy.y += deltaTime * enemySpeed;
		}
		// 画面外に出た敵は消滅
		enemies.remove_if([&](const Vec2& e)
		{
			if (e.y > 700)
			{
				// 敵が画面外に出たらゲームオーバー
				gameover = true;
				return true;
			}
			else
			{
				return false;
			}
		});

		// 敵ショットの発射
		if (enemyShotTimer >= enemyShotCoolTime)
		{
			enemyShotTimer -= enemyShotCoolTime;

			for (const auto& enemy : enemies)
			{
				enemyBullets << enemy;
			}
		}

		// 敵ショットの移動
		for (auto& enemyBullet : enemyBullets)
		{
			enemyBullet.y += deltaTime * enemyBulletSpeed;
		}
		// 画面外に出た自機ショットは消滅
		enemyBullets.remove_if([](const Vec2& b) {return b.y > 700; });

		//-------------------
		//
		// 攻撃判定
		//

		// 敵 vs 自機ショット
		for (auto itEnemy = enemies.begin(); itEnemy != enemies.end();)
		{
			const Circle enemyCircle(*itEnemy, 40);
			bool skip = false;

			for (auto itBullet = playerBullets.begin(); itBullet != playerBullets.end();)
			{
				if (enemyCircle.intersects(*itBullet))
				{
					// 爆発エフェクトを追加
					effect.add([pos = *itEnemy](double t)
					{
						const double t2 = (1.0 - t);
						Circle(pos, 10 + t * 70).drawFrame(20 * t2, AlphaF(t2 * 0.5));
						return t < 1.0;
					});

					itEnemy = enemies.erase(itEnemy);
					playerBullets.erase(itBullet);
					++score;
					skip = true;
					break;
				}

				++itBullet;
			}

			if (skip)
			{
				continue;
			}

			++itEnemy;
		}

		// 敵ショット vs 自機
		for (const auto& enemyBullet : enemyBullets)
		{
			// 敵ショットが playerPos の 20 ピクセル以内に接近したら
			if (enemyBullet.distanceFrom(playerPos) <= 20)
			{
				// ゲームオーバーにする
				gameover = true;
				break;
			}
		}

		// ゲームオーバーならリセット
		if (gameover)
		{
			playerPos = Vec2(400, 500);
			enemies.clear();
			playerBullets.clear();
			enemyBullets.clear();
			enemySpawnTime = initialEnemySpawnTime;
			highScore = Max(highScore, score);
			score = 0;
		}

		//-------------------
		//
		// 描画
		//

		// 背景のアニメーション
		for (auto i : step(12))
		{
			const double a = Periodic::Sine0_1(2s, Scene::Time() - (2.0 / 12 * i));
			Rect(0, i * 50, 800, 50).draw(ColorF(1.0, a * 0.2));
		}

		// 自機の描画
		playerTexture.resized(80).flipped().drawAt(playerPos);

		// 自機ショットの描画
		for (const auto& playerBullet : playerBullets)
		{
			Circle(playerBullet, 8).draw(Palette::Orange);
		}

		// 敵の描画
		for (const auto& enemy : enemies)
		{
			enemyTexture.resized(60).drawAt(enemy);
		}

		// 敵ショットの描画
		for (const auto& enemyBullet : enemyBullets)
		{
			Circle(enemyBullet, 4).draw(Palette::White);
		}

		effect.update();

		// スコアの描画
		font(U"{} [{}]"_fmt(score, highScore)).draw(Arg::bottomRight(780, 580));
	}
}
```


## 15 puzzle
<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/sample/game/15puzzle.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

bool Swappable(int32 a, int32 b)
{
	return (a / 4 == b / 4 && Abs(a - b) == 1) || (a % 4 == b % 4 && Abs(a - b) == 4);
}

void Main()
{
	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));
	constexpr int32 cellSize = 100;
	constexpr Point offset(60, 40);

	// ダイアログから画像を選択
	const Image image = Dialog::OpenImage();

	// 正方形に切り抜く
	const Texture texture(image.squareClipped(), TextureDesc::Mipped);

	Optional<int32> grabbed;

	// ランダムな操作でパズルをシャッフル
	Array<int32> pieces = Range(0, 15);
	{
		int32 pos15 = 15;

		for (int32 i = 0; i < 1000; ++i)
		{
			const int32 to = pos15 + Sample({ -4, -1, 1, 4 });

			if (InRange(to, 0, 15) && Swappable(pos15, to))
			{
				std::swap(pieces[pos15], pieces[to]);
				pos15 = to;
			}
		}
	}

	while (System::Update())
	{
		Rect(offset, 4 * cellSize)
			.drawShadow(Vec2(0, 2), 12, 8)
			.draw(ColorF(0.25))
			.drawFrame(0, 8, ColorF(0.3, 0.5, 0.7));

		if (!MouseL.pressed())
		{
			grabbed = none;
		}

		for (auto i : step(16))
		{
			const int32 pieceID = pieces[i];
			const Rect rect = Rect(i % 4 * cellSize, i / 4 * cellSize, cellSize).movedBy(offset);

			if (pieceID == 15)
			{
				if (grabbed && rect.mouseOver() && Swappable(i, grabbed.value()))
				{
					std::swap(pieces[i], pieces[grabbed.value()]);
					grabbed = i;
				}

				continue;
			}

			if (rect.leftClicked())
			{
				grabbed = i;
			}

			rect(texture.uv(pieceID % 4 * 0.25, pieceID / 4 * 0.25, 0.25, 0.25))
				.draw()
				.drawFrame(1, 0, ColorF(1.0, 0.75));

			if (grabbed == i)
			{
				rect.draw(ColorF(1.0, 0.5, 0.0, 0.3));
			}

			if (rect.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}
		}

		texture.resized(180)
			.draw(offset.x + cellSize * 4 + 40, offset.y)
			.drawFrame(0, 4, ColorF(0.3, 0.5, 0.7));
	}
}
```


## Number chain
<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/sample/game/number-chain.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

struct Bubble
{
	// バブルの円の半径
	static constexpr int32 CircleR = 30;

	// バブルの円
	Circle circle;

	// バブルのインデックス
	int32 index;
	
	// 接続済みなら true に
	bool connected = false;

	void draw() const
	{
		if (connected)
		{
			circle.drawShadow(Vec2(1, 2), 10, 3).draw()
				.drawFrame(2, 0, ColorF(0.3, 0.6, 1.0));
		}
		else
		{
			circle.draw();
		}

		FontAsset(U"Bubble")(index + 1).drawAt(circle.center, ColorF(0.25));
	}
};

// バブルどうしが重なっていないかチェック
bool CheckBubbles(const Array<Bubble>& bubbles)
{
	for (auto i : step(bubbles.size()))
	{
		for (auto k : step(bubbles.size()))
		{
			// 重なっている
			if (i != k && bubbles[i].circle.stretched(5).intersects(bubbles[k].circle.stretched(5)))
			{
				return false;
			}
		}
	}

	return true;
}

// 指定した個数のバブルを重ならないように生成
Array<Bubble> MakeBubbles(int32 count)
{
	Array<Bubble> bubbles(count);

	do
	{
		for (auto i : step(count))
		{
			// バブルのインデックス
			bubbles[i].index = i;

			// バブルの円
			bubbles[i].circle.set(RandomVec2(Circle(Scene::Center(), Scene::Height() / 2 - Bubble::CircleR)), Bubble::CircleR);
		}
	} while (!CheckBubbles(bubbles));

	return bubbles;
}

// 指定したレベルにおけるバブルの個数
constexpr int32 GetBubbleCount(int32 level)
{
	return Min(level, 15);
}

// 指定したレベルにおける制限時間
constexpr double GetTime(int32 level)
{
	return (level <= 15) ? 8.0 : 8.0 - Min((level - 15) * 0.05, 2.0);
}

void Main()
{
	Scene::SetBackground(Palette::White);
	FontAsset::Register(U"Bubble", 36, Typeface::Medium);
	Effect effect;

	// 効果音を作成
	const Array<PianoKey> keys = { PianoKey::C5,  PianoKey::D5, PianoKey::E5, PianoKey::F5, PianoKey::G5,
		PianoKey::A5, PianoKey::B5, PianoKey::C6, PianoKey::D6, PianoKey::E6,
		PianoKey::F6, PianoKey::G6, PianoKey::A6, PianoKey::B6, PianoKey::C7 };
	const Array<Audio> sounds = keys.map([](auto k) { return Audio(GMInstrument::Glockenspiel, k, 0.3s); });

	// ハイスコア
	int32 highScore = 0;

	// 現在のレベル
	int32 level = 1;	

	// 接続数
	int32 connected = 0;

	// 残り時間のタイマー
	Timer timer(GetTime(level), true);

	// バブル
	Array<Bubble> bubbles = MakeBubbles(GetBubbleCount(level));

	while (System::Update())
	{
		const double delta = Scene::DeltaTime();

		// 制限時間を表す背景
		RectF(Scene::Size() * Vec2(1, timer.progress0_1())).draw(HSV(level * 30, 0.3, 0.9));

		for (auto& bubble : bubbles)
		{
			if ((bubble.index == connected) && !bubble.connected && bubble.circle.stretched(10).mouseOver())
			{
				// 接続済みに
				bubble.connected = true;
				
				// 接続数を増やす
				++connected;
				
				// エフェクトを追加
				effect.add([pos = Cursor::Pos()](double t)
				{
					Circle(pos, Bubble::CircleR + t * 200).drawFrame(2, 0, ColorF(0.2, 0.5, 1.0, 1.0 - t * 2.5));
					return t < 0.4;
				});

				// バブルの数字に応じて効果音を鳴らす
				sounds[bubble.index].playOneShot(0.8);
			}

			// バブルを円周に沿って移動
			bubble.circle.center = OffsetCircular(Scene::Center(), bubble.circle.center)
				.rotate((IsEven(bubble.index) ? 20_deg : -20_deg) * delta);
		}

		// バブルをすべてつなぐか、時間切れになったら
		if (const bool failed = timer.reachedZero(); (connected == GetBubbleCount(level)) || failed)
		{
			// レベルを更新
			level = failed ? 1 : ++level;

			// 接続数をリセット
			connected = 0;

			// 制限時間をリセット
			timer = Timer(GetTime(level), true);

			// バブルを再生成
			bubbles = MakeBubbles(GetBubbleCount(level));
			
			// ハイスコアを更新
			highScore = Max(highScore, level);
			
			// タイトルを更新
			Window::SetTitle(U"Level {} (High score: {})"_fmt(level, highScore));
		}

		// バブルをつなぐ線
		for (int32 i = 0; i < (connected - 1); ++i)
		{
			Line(bubbles[i].circle.center, bubbles[i + 1].circle.center).draw(3, Palette::Orange);
		}

		// バブルを描画
		for (const auto& bubble : bubbles)
		{
			bubble.draw();
		}

		effect.update();
	}
}
```


