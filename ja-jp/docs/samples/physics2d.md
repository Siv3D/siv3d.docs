# 2D 物理演算のサンプル

## 1. 2D 物理演算のテンプレート

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/1.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 2D 物理演算のシミュレーションステップ（秒）
		constexpr double StepTime = (1.0 / 200.0);

		// 2D 物理演算のシミュレーション蓄積時間（秒）
		double accumulatedTime = 0.0;

		// 重力加速度 (cm/s^2)
		constexpr double Gravity = 980;

		// 2D 物理演算のワールド
		P2World world{ Gravity };

		// [_] 地面 (幅 1200 cm の床）
		const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -600, 0, 600, 0 });

		// 物体
		Array<P2Body> bodies;

		// 2D カメラ
		Camera2D camera{ Vec2{ 0, -300 } };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// 2D 物理演算のワールドを更新する
				world.update(StepTime);
			}

			// 地面より下に落ちた物体は削除する
			bodies.remove_if([](const P2Body& b) { return (200 < b.getPos().y); });

			// 2D カメラを更新する
			camera.update();
			{
				// 2D カメラから Transformer2D を作成する
				const auto t = camera.createTransformer();

				// 左クリックしたら
				if (MouseL.down())
				{
					// クリックした場所に半径 10 cm のボールを作成する
					bodies << world.createCircle(P2Dynamic, Cursor::PosF(), 10);
				}

				// すべてのボディを描画する
				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}

				// 地面を描画する
				ground.draw(Palette::Skyblue);
			}

			// 2D カメラの操作を描画する
			camera.draw(Palette::Orange);
		}
	}
	```


## 2. 鉄球による破壊

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/2.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.4, 0.7, 1.0 });

		// 2D 物理演算のシミュレーションステップ（秒）
		constexpr double StepTime = (1.0 / 200.0);

		// 2D 物理演算のシミュレーション蓄積時間（秒）
		double accumulatedTime = 0.0;

		// 2D 物理演算のワールド
		P2World world;

		// [_] 地面
		const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -1600, 0, 1600, 0 });

		// [■] 箱 (Sleep させておく)
		Array<P2Body> boxes;
		{
			for (auto y : Range(0, 12))
			{
				for (auto x : Range(0, 20))
				{
					boxes << world.createRect(P2Dynamic, Vec2{ x * 50, -50 - y * 100 },
						SizeF{ 50, 100 }, P2Material{ .density = 0.02, .restitution = 0.0, .friction = 1.0 })
						.setAwake(false);
				}
			}
		}

		// 振り子の軸の座標
		constexpr Vec2 PivotPos{ 0, -2400 };

		// チェーンを構成するリンク 1 つの長さ
		constexpr double LinkLength = 100.0;

		// チェーンを構成するリンクの数
		constexpr int32 LinkCount = 16;

		// チェーンの長さ
		constexpr double ChainLength = (LinkLength * LinkCount);

		// 鉄球の半径
		constexpr double BallRadius = 200;

		// 鉄球の初期座標
		constexpr Vec2 BallCenter = PivotPos.movedBy(-ChainLength - BallRadius, 0);

		// [●] 鉄球
		const P2Body ball = world.createCircle(P2BodyType::Dynamic, BallCenter, BallRadius,
			P2Material{ .density = 0.5, .restitution = 0.0, .friction = 1.0 });

		// [ ] 振り子の軸（実体がないプレースホルダー）
		const P2Body pivot = world.createPlaceholder(P2BodyType::Static, PivotPos);

		// [-] チェーンを構成するリンク
		Array<P2Body> links;

		// リンクどうしやリンクと鉄球をつなぐジョイント
		Array<P2PivotJoint> joints;
		{
			for (auto i : step(LinkCount))
			{
				// リンクの長方形（隣接するリンクと重なるよう少し大きめに）
				const RectF rect{ Arg::rightCenter = PivotPos.movedBy(i * -LinkLength, 0), LinkLength * 1.2, 20 };

				// categoryBits を 0 にすることで、箱など他の物体と干渉しないようにする
				links << world.createRect(P2Dynamic, rect.center(), rect.size,
					P2Material{ .density = 0.1, .restitution = 0.0, .friction = 1.0 }, P2Filter{ .categoryBits = 0 });

				if (i == 0)
				{
					// 振り子の軸と最初のリンクをつなぐジョイント
					joints << world.createPivotJoint(pivot, links.back(), rect.rightCenter().movedBy(-LinkLength * 0.1, 0));
				}
				else
				{
					// 新しいリンクと、一つ前のリンクをつなぐジョイント
					joints << world.createPivotJoint(links[links.size() - 2], links.back(), rect.rightCenter().movedBy(-LinkLength * 0.1, 0));
				}
			}

			// 最後のリンクと鉄球をつなぐジョイント
			joints << world.createPivotJoint(links.back(), ball, PivotPos.movedBy(-ChainLength, 0));
		}

		// [/] ストッパー
		P2Body stopper = world.createLine(P2Static, BallCenter.movedBy(0, 200), Line{ -400, 200, 400, 0 });

		// 2D カメラ
		Camera2D camera{ Vec2{ 0, -1200 }, 0.25 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// 2D 物理演算のワールドを更新する
				world.update(StepTime);

				// 落下した box は削除する
				boxes.remove_if([](const P2Body& body) { return (2000 < body.getPos().y); });
			}

			// 2D カメラを更新する
			camera.update();
			{
				// 2D カメラから Transformer2D を作成
				const auto t = camera.createTransformer();

				// 地面を描く
				ground.draw(ColorF{ 0.0, 0.5, 0.0 });

				// チェーンを描く
				for (const auto& link : links)
				{
					link.draw(ColorF{ 0.25 });
				}

				// 箱を描く
				for (const auto& box : boxes)
				{
					box.draw(ColorF{ 0.6, 0.4, 0.2 });
				}

				// ストッパーを描く
				stopper.draw(ColorF{ 0.25 });

				// 鉄球を描く
				ball.draw(ColorF{ 0.25 });
			}

			// ストッパーを無くす
			if (stopper && SimpleGUI::Button(U"Go", Vec2{ 1100, 20 }))
			{
				// ストッパーを破棄する
				stopper.release();
			}

			// 2D カメラの操作を描画
			camera.draw(Palette::Orange);
		}
	}
	```


## 3. Sketch to P2Body

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/3.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 2D 物理演算のシミュレーションステップ（秒）
		constexpr double StepTime = (1.0 / 200.0);

		// 2D 物理演算のシミュレーション蓄積時間（秒）
		double accumulatedTime = 0.0;

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
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// 2D 物理演算のワールドを更新する
				world.update(StepTime);
			}

			// 地面より下に落ちた物体は削除
			bodies.remove_if([](const P2Body& b) { return (200 < b.getPos().y); });

			// 2D カメラを更新する
			camera.update();
			{
				// 2D カメラから Transformer2D を作成する
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

				// すべてのボディを描画する
				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}

				// 地面を描画する
				ground.draw(Palette::Skyblue);

				points.draw(3);
			}

			// 2D カメラの操作を描画する
			camera.draw(Palette::Orange);
		}
	}
	```


## 4. 台車

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/4.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.4, 0.7, 1.0 });

		// 2D 物理演算のシミュレーションステップ（秒）
		constexpr double StepTime = (1.0 / 200.0);

		// 2D 物理演算のシミュレーション蓄積時間（秒）
		double accumulatedTime = 0.0;

		// 2D 物理演算のワールド
		P2World world;

		// [_] 地面
		Array<P2Body> floors;
		{
			floors << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -1600, 0, 1600, 0 });

			for (auto i : Range(1, 5))
			{
				if (IsEven(i))
				{
					floors << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ 0, -i * 200, 1600, -i * 200 - 300 });
				}
				else
				{
					floors << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -1600,  -i * 200 - 300, 0, -i * 200 });
				}
			}
		}

		// [🚙] 車
		const P2Body carBody = world.createRect(P2Dynamic, Vec2{ -1500, -1450 }, SizeF{ 200, 40 });
		const P2Body wheelL = world.createCircle(P2Dynamic, Vec2{ -1550, -1430 }, 30);
		const P2Body wheelR = world.createCircle(P2Dynamic, Vec2{ -1450, -1430 }, 30);
		const P2WheelJoint wheelJointL = world.createWheelJoint(carBody, wheelL, wheelL.getPos(), Vec2{ 0, -1 })
			.setLinearStiffness(4.0, 0.7)
			.setLimits(-5, 5).setLimitsEnabled(true);
		const P2WheelJoint wheelJointR = world.createWheelJoint(carBody, wheelR, wheelR.getPos(), Vec2{ 0, -1 })
			.setLinearStiffness(4.0, 0.7)
			.setLimits(-5, 5).setLimitsEnabled(true);

		// マウスジョイント
		P2MouseJoint mouseJoint;

		// 2D カメラ
		Camera2D camera{ Vec2{ 0, -1200 }, 0.25 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);
			}

			// 2D カメラを更新する
			camera.update();
			{
				// 2D カメラから Transformer2D を作成する
				const auto t = camera.createTransformer();

				if (MouseL.down())
				{
					mouseJoint = world.createMouseJoint(carBody, Cursor::PosF())
						.setMaxForce(carBody.getMass() * 5000.0)
						.setLinearStiffness(2.0, 0.7);
				}
				else if (MouseL.pressed())
				{
					mouseJoint.setTargetPos(Cursor::PosF());
				}
				else if (MouseL.up())
				{
					mouseJoint.release();
				}

				// 地面を描く
				for (const auto& floor : floors)
				{
					floor.draw(ColorF{ 0.0, 0.5, 0.0 });
				}

				carBody.draw(Palette::Gray);
				wheelL.draw(Palette::Gray).drawWireframe(1, Palette::Yellow);
				wheelR.draw(Palette::Gray).drawWireframe(1, Palette::Yellow);

				mouseJoint.draw();
				wheelJointL.draw();
				wheelJointR.draw();
			}

			// 2D カメラの操作を描画する
			camera.draw(Palette::Orange);
		}
	}
	```


## 5. 滑車の付いたかご

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/5.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.2 });

		// 2D 物理演算のシミュレーションステップ（秒）
		constexpr double StepTime = (1.0 / 200.0);

		// 2D 物理演算のシミュレーション蓄積時間（秒）
		double accumulatedTime = 0.0;

		// 2D 物理演算のワールド
		P2World world;

		const P2Body rail = world.createLineString(P2Static, Vec2{ 0, -400 }, { Vec2{-400, -40}, Vec2{-400, 0}, Vec2{400, 0}, {Vec2{400, -40}} });
		const P2Body wheel = world.createCircle(P2Dynamic, Vec2{ 0, -420 }, 20);
		const P2Body car = world.createCircle(P2Dynamic, Vec2{ 0, -380 }, 10).setFixedRotation(true);

		// ホイールジョイント
		const P2WheelJoint wheelJoint = world.createWheelJoint(car, wheel, wheel.getPos(), Vec2{ 0, 1 })
			.setLimitsEnabled(true);

		const P2Body box = world.createPolygon(P2Dynamic, Vec2{ 0, 0 }, LineString{ Vec2{-100, 0}, Vec2{-100, 100}, Vec2{100, 100}, {Vec2{100, 0}} }.calculateBuffer(5), P2Material{ .friction = 0.0 });

		// 距離ジョイント
		const P2DistanceJoint distanceJointL = world.createDistanceJoint(car, car.getPos(), box, Vec2{ -100, 0 }, 400);
		const P2DistanceJoint distanceJointR = world.createDistanceJoint(car, car.getPos(), box, Vec2{ 100, 0 }, 400);

		Array<P2Body> balls;

		// マウスジョイント
		P2MouseJoint mouseJoint;

		// 2D カメラ
		Camera2D camera{ Vec2{ 0, -150 } };

		Print << U"[space]: make balls";

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);
			}

			// こぼれたボールを削除する
			balls.remove_if([](const P2Body& b) { return (600 < b.getPos().y); });

			// 2D カメラを更新する
			camera.update();
			{
				// 2D カメラから Transformer2D を作成する
				const auto t = camera.createTransformer();

				// マウスジョイントによる干渉
				if (MouseL.down())
				{
					mouseJoint = world.createMouseJoint(box, Cursor::PosF())
						.setMaxForce(box.getMass() * 5000.0)
						.setLinearStiffness(2.0, 0.7);
				}
				else if (MouseL.pressed())
				{
					mouseJoint.setTargetPos(Cursor::PosF());
				}
				else if (MouseL.up())
				{
					mouseJoint.release();
				}

				if (KeySpace.pressed())
				{
					// ボールを追加する
					balls << world.createCircle(P2Dynamic, Cursor::PosF(), Random(2.0, 4.0), P2Material{ .density = 0.001, .restitution = 0.5, .friction = 0.0 });
				}

				rail.draw(Palette::Gray);
				wheel.draw(Palette::Gray).drawWireframe(1, Palette::Yellow);
				car.draw(ColorF{ 0.3, 0.8, 0.5 });
				box.draw(ColorF{ 0.3, 0.8, 0.5 });

				for (const auto& ball : balls)
				{
					ball.draw(Palette::Skyblue);
				}

				distanceJointL.draw();
				distanceJointR.draw();

				mouseJoint.draw();
			}

			// 2D カメラの操作を描画する
			camera.draw(Palette::Orange);
		}
	}
	```


## 6. Text to P2Body

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/6.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		Scene::SetBackground(ColorF{ 0.94, 0.91, 0.86 });

		const Font font{ 100, Typeface::Bold };

		// 2D 物理演算のシミュレーションステップ（秒）
		constexpr double StepTime = (1.0 / 200.0);

		// 2D 物理演算のシミュレーション蓄積時間（秒）
		double accumulatedTime = 0.0;

		// 物理演算のワールド
		P2World world;

		// 床
		const P2Body line = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -1600, 0, 1600, 0 }, OneSided::Yes, P2Material{ 1.0, 0.1, 1.0 });

		// 文字のパーツ
		Array<P2Body> bodies;

		String text;
		int32 generation = 0;
		HashTable<P2BodyID, int32> table;

		// 2D カメラ
		Camera2D camera{ Vec2{ 0, -500 }, 0.38, Camera2DParameters::MouseOnly() };

		constexpr Vec2 textPos{ -400, -500 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// 2D 物理演算のワールドを更新する
				world.update(StepTime);
			}

			// テキストの入力を行う
			TextInput::UpdateText(text);

			// 2D カメラを更新する
			camera.update();
			{
				// 2D カメラを適用する Transformer2D を作成する
				const auto t = camera.createTransformer();

				// 世代に応じた色で Body を描画する
				for (const auto& body : bodies)
				{
					body.draw(HSV{ (table[body.id()] * 45 + 30), 0.8, 0.8 });
				}

				// 床を描画する
				line.draw(Palette::Green);

				const String currentText = (text + TextInput::GetEditingText());

				// 入力文字を描画する
				{
					const Transformer2D scaling{ Mat3x2::Scale(2.5) };

					font(currentText).draw(textPos, ColorF{ 0.5 });
				}

				// 改行文字が入力されたらテキストを P2Body にする
				if (currentText.includes(U'\n'))
				{
					// 入力文字を PolygonGlyph 化する
					const Array<PolygonGlyph> glyphs = font.renderPolygons(currentText.removed(U'\n'));

					// P2Body にする Polygon を得る
					Array<Polygon> polygons;
					{
						Vec2 penPos{ textPos };

						for (const auto& glyph : glyphs)
						{
							for (const auto& polygon : glyph.polygons)
							{
								polygons << polygon
									.movedBy(penPos + glyph.getOffset())
									.scaled(2.5)
									.simplified(2.0);
							}

							penPos.x += glyph.xAdvance;
						}
					}

					for (const auto& polygon : polygons)
					{
						bodies << world.createPolygon(P2Dynamic, Vec2{ 0, 0 }, polygon, P2Material{ 1, 0.0, 0.4 });

						// 現在の世代を保存する
						table[bodies.back().id()] = generation;
					}

					text.clear();

					// 世代を進める
					++generation;
				}

				// 2D カメラ、右クリック時の UI を表示する
				camera.draw(Palette::Orange);
			}

			// 消去ボタン
			if (SimpleGUI::Button(U"Clear", Vec2{ 1100, 40 }))
			{
				bodies.clear();
			}
		}
	}
	```


## 7. 力による物体の移動

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/7.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズ
		Window::Resize(1280, 720);

		// 2D 物理演算のシミュレーションステップ（秒）
		constexpr double StepTime = (1.0 / 200.0);

		// 2D 物理演算のシミュレーション蓄積時間（秒）
		double accumulatedTime = 0.0;

		// 重力加速度 (cm/s^2)
		constexpr double Gravity = 980;

		// 2D 物理演算のワールド
		P2World world{ Gravity };

		// [_] 地面 (幅 1200 cm の床）
		const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -600, 0, 600, 0 });

		// 物体
		P2Body box = world.createRect(P2Dynamic, Vec2{ -400, -100 }, SizeF{ 50, 100 })
			.setFixedRotation(true); // 回転しないようにする

		// 2D カメラ
		Camera2D camera{ Vec2{ 0, -300 }, 1.0, CameraControl::Mouse };

		while (System::Update())
		{
			ClearPrint();
			Print << box.getVelocity();

			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// [←] キーが押されていたら
				if (KeyLeft.pressed())
				{
					// ボディに右向きの力を加える
					box.applyForce(Vec2{ -60000, 0 } * StepTime);
				}

				// [→] キーが押されていたら
				if (KeyRight.pressed())
				{
					// ボディに右向きの力を加える
					box.applyForce(Vec2{ 60000, 0 } * StepTime);
				}

				// 2D 物理演算のワールドを更新する
				world.update(StepTime);
			}

			// [↑] キーが押されていたら
			if (KeyUp.down())
			{
				// ボディに上向きの力を加える
				box.applyLinearImpulse(Vec2{ 0, -300 });
			}

			// 2D カメラを更新する
			camera.update();
			{
				// 2D カメラから Transformer2D を作成する
				const auto t = camera.createTransformer();

				// すべてのボディを描画する
				box.draw();

				// 地面を描画する
				ground.draw(Palette::Skyblue);
			}

			// 2D カメラの操作を描画する
			camera.draw(Palette::Orange);
		}
	}
	```


## 8. 衝突の検出

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/8.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 2D 物理演算のシミュレーションステップ（秒）
		constexpr double StepTime = (1.0 / 200.0);

		// 2D 物理演算のシミュレーション蓄積時間（秒）
		double accumulatedTime = 0.0;

		// 重力加速度 (cm/s^2)
		constexpr double Gravity = 980;

		// 2D 物理演算のワールド
		P2World world{ Gravity };

		// [_] 地面 (幅 1200 cm の床）
		const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -600, 0, 600, 0 });

		// 物体
		Array<P2Body> bodies;

		// 2D カメラ
		Camera2D camera{ Vec2{ 0, -300 } };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				ClearPrint();

				// 2D 物理演算のワールドを更新する
				world.update(StepTime);

				// 接触が発生しているボディの ID を表示する
				for (auto&& [pair, collision] : world.getCollisions())
				{
					Print << pair.a << U" vs " << pair.b;
				}
			}

			// 地面より下に落ちた物体は削除する
			bodies.remove_if([](const P2Body& b) { return (200 < b.getPos().y); });

			// 2D カメラを更新する
			camera.update();
			{
				// 2D カメラから Transformer2D を作成する
				const auto t = camera.createTransformer();

				// 左クリックしたら
				if (MouseL.down())
				{
					// クリックした場所に半径 10 cm のボールを作成する
					bodies << world.createCircle(P2Dynamic, Cursor::PosF(), 10);
				}

				// すべてのボディを描画する
				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}

				// 地面を描画する
				ground.draw(Palette::Skyblue);
			}

			// 2D カメラの操作を描画する
			camera.draw(Palette::Orange);
		}
	}
	```

## 9. 衝撃の検出

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/9.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// 衝突エフェクト
	struct DamageEffect : IEffect
	{
		Vec2 m_center;

		double m_scale;

		Texture m_texture;

		DamageEffect(const Vec2& center, double scale, const Texture& texture)
			: m_center{ center }
			, m_scale{ scale }
			, m_texture{ texture } {}

		bool update(double t) override
		{
			const double scale = (m_scale * (t - 0.5));
			m_texture.scaled(scale).drawAt(m_center);
			return (t < 0.5);
		}
	};

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.94, 0.91, 0.86 });

		const Font font{ 80, Typeface::Bold };
		const Font damageFont{ FontMethod::MSDF, 48, Typeface::Heavy };

		const Texture face0{ U"😮‍💨"_emoji };
		const Texture face1{ U"🙁"_emoji };
		const Texture face2{ U"😣"_emoji };
		const Texture collisionTexture{ U"💥"_emoji };
		Effect effect;

		// 2D 物理演算のシミュレーションステップ（秒）
		constexpr double StepTime = (1.0 / 200.0);

		// 2D 物理演算のシミュレーション蓄積時間（秒）
		double accumulatedTime = 0.0;

		// 物理演算のワールド
		P2World world;

		// 顔のボディ
		const P2Body faceBody = world.createCircle(P2Static, Vec2{ 0, 0 }, 110, P2Material{ 1.0, 0.1, 1.0 });

		// 文字のパーツ
		Array<P2Body> bodies;

		String text;

		// ボディの ID と与えたダメージ量のテーブル
		HashTable<P2BodyID, int32> table;

		// 2D カメラ
		Camera2D camera{ Vec2{ 0, -180 }, 1.0, Camera2DParameters::NoControl() };

		constexpr Vec2 TextPos{ -120, -480 };

		// 痛みの量
		double pain = 0.0;
		double painVelocity = 0.0;

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// 2D 物理演算のワールドを更新する
				world.update(StepTime);

				// 接触が発生しているボディ
				for (auto&& [pair, collision] : world.getCollisions())
				{
					// 各接触について
					for (const auto& contact : collision)
					{
						// ダメージ量
						const int32 damage = (contact.normalImpulse / 4.0);

						// ダメージ量が 1.0 以上なら
						if (1.0 < damage)
						{
							// 接触相手が顔のボディなら
							if (pair.a == faceBody.id())
							{
								table[pair.b] += damage;
							}
							else if (pair.b == faceBody.id())
							{
								table[pair.a] += damage;
							}

							// 痛みの量を増やす
							pain += damage;

							// 衝突エフェクトを追加する
							effect.add<DamageEffect>(contact.point, (damage / 10.0), collisionTexture);
						}
					}
				}
			}

			// 下に落ちた物体は削除する
			bodies.remove_if([&](const P2Body& b)
				{
					if (200 < b.getPos().y)
					{
						table.erase(b.id());
						return true;
					}

					return false;
				});

			// 痛みを減衰させる
			pain = Math::SmoothDamp(pain, 0.0, painVelocity, 0.5);

			// テキストの入力を行う
			TextInput::UpdateText(text);

			// 2D カメラを更新する
			camera.update();
			{
				Scene::Rect().draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

				// 2D カメラを適用する Transformer2D を作成する
				const auto t = camera.createTransformer();

				// 痛みの量に応じて顔の表情を変える
				((pain < 10.0) ? face0 : (pain < 100.0) ? face1 : face2)
					.scaled(2.0)
					.drawAt(0, 0);

				// 落下する文字を描画する
				for (const auto& body : bodies)
				{
					body.draw(ColorF{ 0.11 });
				}

				// 文字の累積ダメージを描画する
				for (const auto& body : bodies)
				{
					damageFont(table[body.id()]).drawAt(28, body.getPos().movedBy(0, -50), ColorF{ 0.1, 0.5, 0.2 });
				}

				// 衝突エフェクトを描画する
				effect.update();

				// 入力文字を描画する
				const String currentText = (text + TextInput::GetEditingText());
				font(currentText).draw(TextPos, ColorF{ 0.11 });

				// 改行文字が入力されたらテキストを P2Body にする
				if (currentText.includes(U'\n'))
				{
					// 入力文字を PolygonGlyph 化する
					const Array<PolygonGlyph> glyphs = font.renderPolygons(currentText.removed(U'\n'));

					// P2Body にする Polygon を得る
					Array<Polygon> polygons;
					{
						Vec2 penPos{ TextPos };

						for (const auto& glyph : glyphs)
						{
							for (const auto& polygon : glyph.polygons)
							{
								polygons << polygon
									.movedBy(penPos + glyph.getOffset());
							}

							penPos.x += glyph.xAdvance;
						}
					}

					for (auto& polygon : polygons)
					{
						const Vec2 offset = polygon.boundingRect().center();
						polygon.moveBy(-offset);

						bodies << world.createPolygon(P2Dynamic, offset, polygon, P2Material{ 1, 0.0, 0.4 });

						// その文字が与えたダメージの累積値
						table[bodies.back().id()] = 0;
					}

					text.clear();
				}
			}
		}
	}
	```

## 10. 見下ろし型 2D シューティング
2D 物理演算機能を使った、見下ろし型 2D シューティングゲームのサンプルです。

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/TopDownShooterP2/Screenshot/2.png)

[Siv3D-Sample | 見下ろし型 2D シューティング :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/blob/main/Samples/TopDownShooterP2){:target="_blank" .md-button}


## 11. 2D 物理演算による破壊ゲーム

![](https://raw.githubusercontent.com/Reputeless/games/main/games/005/B.png)

[ゲーム典型 | 2D 物理演算による破壊ゲーム :material-open-in-new:](https://github.com/Reputeless/games/blob/main/games/005/B.md){:target="_blank" .md-button}


