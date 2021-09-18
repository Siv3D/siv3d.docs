
!!! warning "This is the documentation for an old version"
	This is the documentation for an old version of Siv3D (v0.4.3). See [Siv3D Reference v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation-en) for the latest version.

# 2D particle system

## パラメータ
<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/reference/2d-particle/parameter.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

// Siv3D-kun の形の Polygon を作成
Polygon CreatePolygon()
{
	return Image(U"example/siv3d-kun.png")
		.alphaToPolygonCentered().simplified(1).scaled(1.3);
}

void Main()
{
	Window::Resize(1280, 720);
	const Texture textureSiv3D(U"example/siv3d-kun.png");
	const std::array<BlendState, 4> blends = {
		BlendState::Default, BlendState::Additive, BlendState::Opaque, BlendState::Subtractive
	};

	// パーティクル用のテクスチャ
	const Texture texture(U"example/particle.png", TextureDesc::Mipped);

	// デバッグモードのパーティクル描画
	bool debugMode = false;

	// 背景を白に
	bool whiteBackground = false;

	// Siv3D-kun を表示
	bool drawTexture = false;

	Vec2 position(300, 340);
	Vec2 force(0.0, 0.0);
	HSV startColor = ColorF(1.0);
	size_t blendIndex = 1;
	size_t emitterIndex = 1;

	CircleEmitter2D circleEmitter;
	ArcEmitter2D arcEmitter;
	RectEmitter2D rectEmitter;
	PolygonEmitter2D polygonEmitter(CreatePolygon());
	ParticleSystem2DParameters parameters;

	ParticleSystem2D particleSystem(position, force);
	particleSystem.setEmitter(arcEmitter);
	particleSystem.setTexture(texture);
	particleSystem.setParameters(parameters);
	particleSystem.prewarm();

	while (System::Update())
	{
		// パーティクルの個数を表示
		ClearPrint();
		Print << U"{} particles"_fmt(particleSystem.num_particles());

		// パーティクル更新
		particleSystem.update();

		if (debugMode)
		{
			// デバッグモードでパーティクル描画
			particleSystem.drawDebug();
		}
		else
		{
			// パーティクル描画
			particleSystem.draw();
		}

		if (emitterIndex == 3 && drawTexture)
		{
			// Siv3D-kun を表示
			textureSiv3D.scaled(1.3).drawAt(position);
		}

		if (MouseR.pressed())
		{
			// パーティクルの発生位置を移動
			particleSystem.setPosition(position = Cursor::Pos());
		}

		SimpleGUI::CheckBox(debugMode, U"Debug", Vec2(80, 660), 140);
		SimpleGUI::CheckBox(whiteBackground, U"White", Vec2(240, 660), 140);
		SimpleGUI::CheckBox(drawTexture, U"Texture", Vec2(400, 660), 140, (emitterIndex == 3));
		Scene::SetBackground(whiteBackground ? Color(250, 252, 255) : Palette::DefaultBackground);

		const int32 x0 = 560, x1 = 900;
		{
			SimpleGUI::Slider(U"Rate", parameters.rate, 1.0, 500.0, Vec2(x0, 20), 120, 200);
			SimpleGUI::Slider(U"Max", parameters.maxParticles, 50.0, 2500.0, Vec2(x0, 60), 120, 200);
			SimpleGUI::Slider(U"LifeTime", parameters.startLifeTime, 0.0, 5.0, Vec2(x0, 100), 120, 200);
			SimpleGUI::Slider(U"Speed", parameters.startSpeed, 0.0, 320.0, Vec2(x0, 140), 120, 200);
			SimpleGUI::Slider(U"Color H", startColor.h, 0.0, 360.0, Vec2(x0, 180), 120, 200);
			SimpleGUI::Slider(U"Color S", startColor.s, 0.0, 1.0, Vec2(x0, 220), 120, 200);
			SimpleGUI::Slider(U"Color V", startColor.v, 0.0, 1.0, Vec2(x0, 260), 120, 200);
			SimpleGUI::Slider(U"Color A", startColor.a, 0.0, 1.0, Vec2(x0, 300), 120, 200);
			SimpleGUI::Slider(U"Size", parameters.startSize, 0.0, 150.0, Vec2(x0, 340), 120, 200);
			SimpleGUI::Slider(U"Rotation", parameters.startRotationDeg, -180, 180, Vec2(x0, 380), 120, 200);
			SimpleGUI::Slider(U"AngularVel", parameters.startAngularVelocityDeg, -720, 720, Vec2(x0, 420), 120, 200);
			SimpleGUI::Slider(U"Force X", force.x, -320.0, 320.0, Vec2(x0, 460), 120, 200);
			SimpleGUI::Slider(U"Force Y", force.y, -320, 320.0, Vec2(x0, 500), 120, 200);
			SimpleGUI::RadioButtons(blendIndex, { U"Default", U"Additive", U"Opaque", U"Subtractive" }, Vec2(x0, 540), 320);

			parameters.blendState = blends[blendIndex];
			parameters.startColor = startColor;
			particleSystem.setParameters(parameters);
			particleSystem.setForce(force);
		}

		{
			bool changed = false;
			changed |= SimpleGUI::RadioButtons(emitterIndex, { U"Circle", U"Arc", U"Rect", U"Polygon" }, Vec2(x1, 20), 360);

			if (emitterIndex == 0)
			{
				changed |= SimpleGUI::Slider(U"Source Radius", circleEmitter.sourceRadius, 0.0, 40.0, Vec2(x1, 180), 160, 200);
				changed |= SimpleGUI::Slider(U"R", circleEmitter.r, 0.0, 320.0, Vec2(x1, 220), 160, 200);
				changed |= SimpleGUI::CheckBox(circleEmitter.randomDirection, U"Random Direction", Vec2(x1, 260), 360);
				changed |= SimpleGUI::CheckBox(circleEmitter.fromShell, U"From Shell", Vec2(x1, 300), 300);

				if (changed) // setEmitter は重い処理なので、変更があった時だけ
				{
					particleSystem.setEmitter(circleEmitter);
				}
			}
			else if (emitterIndex == 1)
			{
				changed |= SimpleGUI::Slider(U"Source Radius", arcEmitter.sourceRadius, 0.0, 40.0, Vec2(x1, 180), 160, 200);
				changed |= SimpleGUI::Slider(U"R", arcEmitter.r, 0.0, 320.0, Vec2(x1, 220), 160, 200);
				changed |= SimpleGUI::Slider(U"Direction", arcEmitter.direction, -180, 180, Vec2(x1, 260), 160, 200);
				changed |= SimpleGUI::Slider(U"Angle", arcEmitter.angle, 0.0, 360, Vec2(x1, 300), 160, 200);
				changed |= SimpleGUI::CheckBox(arcEmitter.randomDirection, U"Random Direction", Vec2(x1, 340), 360);
				changed |= SimpleGUI::CheckBox(arcEmitter.fromShell, U"From Shell", Vec2(x1, 380), 360);

				if (changed) // setEmitter は重い処理なので、変更があった時だけ
				{
					particleSystem.setEmitter(arcEmitter);
				}
			}
			else if (emitterIndex == 2)
			{
				changed |= SimpleGUI::Slider(U"Source Radius", rectEmitter.sourceRadius, 0.0, 40.0, Vec2(x1, 180), 160, 200);
				changed |= SimpleGUI::Slider(U"Width", rectEmitter.width, 0, 720, Vec2(x1, 220), 160, 200);
				changed |= SimpleGUI::Slider(U"Height", rectEmitter.height, 0, 720, Vec2(x1, 260), 160, 200);
				changed |= SimpleGUI::CheckBox(rectEmitter.randomDirection, U"Random Direction", Vec2(x1, 300), 360);
				changed |= SimpleGUI::CheckBox(rectEmitter.fromShell, U"From Shell", Vec2(x1, 340), 360);

				if (changed) // setEmitter は重い処理なので、変更があった時だけ
				{
					particleSystem.setEmitter(rectEmitter);
				}
			}
			else if (emitterIndex == 3)
			{
				if (changed) // setEmitter は重い処理なので、変更があった時だけ
				{
					particleSystem.setEmitter(polygonEmitter);
				}
			}
		}
	}
}
```


## ゲームへの組み込み
<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/reference/2d-particle/breakout.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

// ボール用のパーティクルクラス
class BallParticleSystem
{
private:

	// 2D パーティクルシステム
	ParticleSystem2D m_system;

	// パーティクル用のテクスチャ
	Texture m_texture;

public:

	BallParticleSystem(const Vec2& pos, const Vec2& force)
		: m_system(pos, force)
		, m_texture(U"example/particle.png", TextureDesc::Mipped)
	{
		m_system.setTexture(m_texture);

		// パラメータ
		ParticleSystem2DParameters parameters;
		parameters.rate = 300.0;
		parameters.startSpeed = 50.0;
		parameters.startColor = HSV(10.0, 0.8, 0.25);
		parameters.startSize = 60.0;
		parameters.blendState = BlendState::Additive;
		m_system.setParameters(parameters);

		// エミッター
		CircleEmitter2D circleEmitter;
		circleEmitter.r = 12.0;
		m_system.setEmitter(circleEmitter);
		
		m_system.prewarm();
	}

	void setPos(const Vec2& pos)
	{
		m_system.setPosition(pos);
	}

	void update()
	{
		m_system.update();
	}

	void draw() const
	{
		m_system.draw();
	}
};

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

	// ボール用のパーティクルクラス
	BallParticleSystem particleSystem(ball.center, Vec2(0.0, -120));

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

		// パーティクル更新
		particleSystem.setPos(ball.center);
		particleSystem.update();

		// ボールを描く
		ball.draw();

		// パドルを描く
		paddle.draw();

		// パーティクル描画
		particleSystem.draw();
	}
}
```
