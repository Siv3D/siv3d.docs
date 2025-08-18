# 2D Physics Samples

## 1. 2D physics template

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/1.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// 2D physics simulation step (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// Gravitational acceleration (cm/s^2)
		constexpr double Gravity = 980;

		// 2D physics world
		P2World world{ Gravity };

		// [_] Ground (floor with width 1200 cm)
		const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -600, 0, 600, 0 });

		// Bodies
		Array<P2Body> bodies;

		// 2D camera
		Camera2D camera{ Vec2{ 0, -300 } };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Update 2D physics world
				world.update(StepTime);
			}

			// Remove bodies that fell below ground
			bodies.remove_if([](const P2Body& b) { return (200 < b.getPos().y); });

			// Update 2D camera
			camera.update();
			{
				// Create Transformer2D from 2D camera
				const auto t = camera.createTransformer();

				// If left clicked
				if (MouseL.down())
				{
					// Create ball with radius 10 cm at clicked location
					bodies << world.createCircle(P2Dynamic, Cursor::PosF(), 10);
				}

				// Draw all bodies
				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}

				// Draw ground
				ground.draw(Palette::Skyblue);
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);
		}
	}
	```


## 2. Destruction by wrecking ball

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/2.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.4, 0.7, 1.0 });

		// 2D physics simulation step (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		// [_] Ground
		const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -1600, 0, 1600, 0 });

		// [■] Boxes (kept sleeping)
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

		// Pendulum axis coordinates
		constexpr Vec2 PivotPos{ 0, -2400 };

		// Length of one link in the chain
		constexpr double LinkLength = 100.0;

		// Number of links in the chain
		constexpr int32 LinkCount = 16;

		// Chain length
		constexpr double ChainLength = (LinkLength * LinkCount);

		// Wrecking ball radius
		constexpr double BallRadius = 200;

		// Initial coordinates of wrecking ball
		constexpr Vec2 BallCenter = PivotPos.movedBy(-ChainLength - BallRadius, 0);

		// [●] Wrecking ball
		const P2Body ball = world.createCircle(P2BodyType::Dynamic, BallCenter, BallRadius,
			P2Material{ .density = 0.5, .restitution = 0.0, .friction = 1.0 });

		// [ ] Pendulum axis (placeholder with no physical body)
		const P2Body pivot = world.createPlaceholder(P2BodyType::Static, PivotPos);

		// [-] Links composing the chain
		Array<P2Body> links;

		// Joints connecting links to each other and to the wrecking ball
		Array<P2PivotJoint> joints;
		{
			for (auto i : step(LinkCount))
			{
				// Link rectangle (slightly larger to overlap with adjacent links)
				const RectF rect{ Arg::rightCenter = PivotPos.movedBy(i * -LinkLength, 0), LinkLength * 1.2, 20 };

				// Set categoryBits to 0 to avoid interference with other objects like boxes
				links << world.createRect(P2Dynamic, rect.center(), rect.size,
					P2Material{ .density = 0.1, .restitution = 0.0, .friction = 1.0 }, P2Filter{ .categoryBits = 0 });

				if (i == 0)
				{
					// Joint connecting pendulum axis and first link
					joints << world.createPivotJoint(pivot, links.back(), rect.rightCenter().movedBy(-LinkLength * 0.1, 0));
				}
				else
				{
					// Joint connecting new link and previous link
					joints << world.createPivotJoint(links[links.size() - 2], links.back(), rect.rightCenter().movedBy(-LinkLength * 0.1, 0));
				}
			}

			// Joint connecting last link and wrecking ball
			joints << world.createPivotJoint(links.back(), ball, PivotPos.movedBy(-ChainLength, 0));
		}

		// [/] Stopper
		P2Body stopper = world.createLine(P2Static, BallCenter.movedBy(0, 200), Line{ -400, 200, 400, 0 });

		// 2D camera
		Camera2D camera{ Vec2{ 0, -1200 }, 0.25 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Update 2D physics world
				world.update(StepTime);

				// Remove fallen boxes
				boxes.remove_if([](const P2Body& body) { return (2000 < body.getPos().y); });
			}

			// Update 2D camera
			camera.update();
			{
				// Create Transformer2D from 2D camera
				const auto t = camera.createTransformer();

				// Draw ground
				ground.draw(ColorF{ 0.0, 0.5, 0.0 });

				// Draw chain
				for (const auto& link : links)
				{
					link.draw(ColorF{ 0.25 });
				}

				// Draw boxes
				for (const auto& box : boxes)
				{
					box.draw(ColorF{ 0.6, 0.4, 0.2 });
				}

				// Draw stopper
				stopper.draw(ColorF{ 0.25 });

				// Draw wrecking ball
				ball.draw(ColorF{ 0.25 });
			}

			// Remove stopper
			if (stopper && SimpleGUI::Button(U"Go", Vec2{ 1100, 20 }))
			{
				// Release stopper
				stopper.release();
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);
		}
	}
	```


## 3. Sketch to P2Body

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/3.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// 2D physics simulation step (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		// [_] Ground
		const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -600, 0, 600, 0 });

		// Bodies
		Array<P2Body> bodies;

		// 2D camera
		Camera2D camera{ Vec2{ 0, -300 } };

		LineString points;

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Update 2D physics world
				world.update(StepTime);
			}

			// Remove bodies that fell below ground
			bodies.remove_if([](const P2Body& b) { return (200 < b.getPos().y); });

			// Update 2D camera
			camera.update();
			{
				// Create Transformer2D from 2D camera
				const auto t = camera.createTransformer();

				// If left click or mouse movement while clicking occurs
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

				// Draw all bodies
				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}

				// Draw ground
				ground.draw(Palette::Skyblue);

				points.draw(3);
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);
		}
	}
	```


## 4. Cart

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/4.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.4, 0.7, 1.0 });

		// 2D physics simulation step (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		// [_] Ground
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

		// [🚙] Car
		const P2Body carBody = world.createRect(P2Dynamic, Vec2{ -1500, -1450 }, SizeF{ 200, 40 });
		const P2Body wheelL = world.createCircle(P2Dynamic, Vec2{ -1550, -1430 }, 30);
		const P2Body wheelR = world.createCircle(P2Dynamic, Vec2{ -1450, -1430 }, 30);
		const P2WheelJoint wheelJointL = world.createWheelJoint(carBody, wheelL, wheelL.getPos(), Vec2{ 0, -1 })
			.setLinearStiffness(4.0, 0.7)
			.setLimits(-5, 5).setLimitsEnabled(true);
		const P2WheelJoint wheelJointR = world.createWheelJoint(carBody, wheelR, wheelR.getPos(), Vec2{ 0, -1 })
			.setLinearStiffness(4.0, 0.7)
			.setLimits(-5, 5).setLimitsEnabled(true);

		// Mouse joint
		P2MouseJoint mouseJoint;

		// 2D camera
		Camera2D camera{ Vec2{ 0, -1200 }, 0.25 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);
			}

			// Update 2D camera
			camera.update();
			{
				// Create Transformer2D from 2D camera
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

				// Draw ground
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

			// Draw 2D camera controls
			camera.draw(Palette::Orange);
		}
	}
	```


## 5. Pulley system

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/5.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// Set background color
		Scene::SetBackground(ColorF{ 0.2 });

		// 2D physics simulation step (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		const P2Body rail = world.createLineString(P2Static, Vec2{ 0, -400 }, { Vec2{-400, -40}, Vec2{-400, 0}, Vec2{400, 0}, {Vec2{400, -40}} });
		const P2Body wheel = world.createCircle(P2Dynamic, Vec2{ 0, -420 }, 20);
		const P2Body car = world.createCircle(P2Dynamic, Vec2{ 0, -380 }, 10).setFixedRotation(true);

		// Wheel joint
		const P2WheelJoint wheelJoint = world.createWheelJoint(car, wheel, wheel.getPos(), Vec2{ 0, 1 })
			.setLimitsEnabled(true);

		const P2Body box = world.createPolygon(P2Dynamic, Vec2{ 0, 0 }, LineString{ Vec2{-100, 0}, Vec2{-100, 100}, Vec2{100, 100}, {Vec2{100, 0}} }.calculateBuffer(5), P2Material{ .friction = 0.0 });

		// Distance joints
		const P2DistanceJoint distanceJointL = world.createDistanceJoint(car, car.getPos(), box, Vec2{ -100, 0 }, 400);
		const P2DistanceJoint distanceJointR = world.createDistanceJoint(car, car.getPos(), box, Vec2{ 100, 0 }, 400);

		Array<P2Body> balls;

		// Mouse joint
		P2MouseJoint mouseJoint;

		// 2D camera
		Camera2D camera{ Vec2{ 0, -150 } };

		Print << U"[space]: make balls";

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);
			}

			// Remove spilled balls
			balls.remove_if([](const P2Body& b) { return (600 < b.getPos().y); });

			// Update 2D camera
			camera.update();
			{
				// Create Transformer2D from 2D camera
				const auto t = camera.createTransformer();

				// Mouse joint interaction
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
					// Add balls
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

			// Draw 2D camera controls
			camera.draw(Palette::Orange);
		}
	}
	```


## 6. Text to P2Body

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/6.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		Scene::SetBackground(ColorF{ 0.94, 0.91, 0.86 });

		const Font font{ 100, Typeface::Bold };

		// 2D physics simulation step (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// Physics world
		P2World world;

		// Floor
		const P2Body line = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -1600, 0, 1600, 0 }, OneSided::Yes, P2Material{ 1.0, 0.1, 1.0 });

		// Text parts
		Array<P2Body> bodies;

		String text;
		int32 generation = 0;
		HashTable<P2BodyID, int32> table;

		// 2D camera
		Camera2D camera{ Vec2{ 0, -500 }, 0.38, Camera2DParameters::MouseOnly() };

		constexpr Vec2 textPos{ -400, -500 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Update 2D physics world
				world.update(StepTime);
			}

			// Input text
			TextInput::UpdateText(text);

			// Update 2D camera
			camera.update();
			{
				// Create Transformer2D that applies 2D camera
				const auto t = camera.createTransformer();

				// Draw bodies with colors based on generation
				for (const auto& body : bodies)
				{
					body.draw(HSV{ (table[body.id()] * 45 + 30), 0.8, 0.8 });
				}

				// Draw floor
				line.draw(Palette::Green);

				const String currentText = (text + TextInput::GetEditingText());

				// Draw input text
				{
					const Transformer2D scaling{ Mat3x2::Scale(2.5) };

					font(currentText).draw(textPos, ColorF{ 0.5 });
				}

				// Convert text to P2Body when newline character is entered
				if (currentText.includes(U'\n'))
				{
					// Convert input text to PolygonGlyph
					const Array<PolygonGlyph> glyphs = font.renderPolygons(currentText.removed(U'\n'));

					// Get polygons to create P2Body
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

						// Save current generation
						table[bodies.back().id()] = generation;
					}

					text.clear();

					// Advance generation
					++generation;
				}

				// Display 2D camera and right-click UI
				camera.draw(Palette::Orange);
			}

			// Clear button
			if (SimpleGUI::Button(U"Clear", Vec2{ 1100, 40 }))
			{
				bodies.clear();
			}
		}
	}
	```


## 7. Force-based movement

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/7.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// 2D physics simulation step (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// Gravitational acceleration (cm/s^2)
		constexpr double Gravity = 980;

		// 2D physics world
		P2World world{ Gravity };

		// [_] Ground (floor with width 1200 cm)
		const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -600, 0, 600, 0 });

		// Body
		P2Body box = world.createRect(P2Dynamic, Vec2{ -400, -100 }, SizeF{ 50, 100 })
			.setFixedRotation(true); // Prevent rotation

		// 2D camera
		Camera2D camera{ Vec2{ 0, -300 }, 1.0, CameraControl::Mouse };

		while (System::Update())
		{
			ClearPrint();
			Print << box.getVelocity();

			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// If [←] key is pressed
				if (KeyLeft.pressed())
				{
					// Apply leftward force to body
					box.applyForce(Vec2{ -60000, 0 } * StepTime);
				}

				// If [→] key is pressed
				if (KeyRight.pressed())
				{
					// Apply rightward force to body
					box.applyForce(Vec2{ 60000, 0 } * StepTime);
				}

				// Update 2D physics world
				world.update(StepTime);
			}

			// If [↑] key is pressed
			if (KeyUp.down())
			{
				// Apply upward force to body
				box.applyLinearImpulse(Vec2{ 0, -300 });
			}

			// Update 2D camera
			camera.update();
			{
				// Create Transformer2D from 2D camera
				const auto t = camera.createTransformer();

				// Draw all bodies
				box.draw();

				// Draw ground
				ground.draw(Palette::Skyblue);
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);
		}
	}
	```


## 8. Collision detection

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/8.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Resize window to 1280x720
		Window::Resize(1280, 720);

		// 2D physics simulation step (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// Gravitational acceleration (cm/s^2)
		constexpr double Gravity = 980;

		// 2D physics world
		P2World world{ Gravity };

		// [_] Ground (floor with width 1200 cm)
		const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -600, 0, 600, 0 });

		// Bodies
		Array<P2Body> bodies;

		// 2D camera
		Camera2D camera{ Vec2{ 0, -300 } };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				ClearPrint();

				// Update 2D physics world
				world.update(StepTime);

				// Display IDs of bodies in contact
				for (auto&& [pair, collision] : world.getCollisions())
				{
					Print << pair.a << U" vs " << pair.b;
				}
			}

			// Remove bodies that fell below ground
			bodies.remove_if([](const P2Body& b) { return (200 < b.getPos().y); });

			// Update 2D camera
			camera.update();
			{
				// Create Transformer2D from 2D camera
				const auto t = camera.createTransformer();

				// If left clicked
				if (MouseL.down())
				{
					// Create ball with radius 10 cm at clicked location
					bodies << world.createCircle(P2Dynamic, Cursor::PosF(), 10);
				}

				// Draw all bodies
				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}

				// Draw ground
				ground.draw(Palette::Skyblue);
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);
		}
	}
	```

## 9. Impact detection

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/9.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Collision effect
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

		// 2D physics simulation step (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// Physics world
		P2World world;

		// Face body
		const P2Body faceBody = world.createCircle(P2Static, Vec2{ 0, 0 }, 110, P2Material{ 1.0, 0.1, 1.0 });

		// Text parts
		Array<P2Body> bodies;

		String text;

		// Table of body IDs and damage amounts dealt
		HashTable<P2BodyID, int32> table;

		// 2D camera
		Camera2D camera{ Vec2{ 0, -180 }, 1.0, Camera2DParameters::NoControl() };

		constexpr Vec2 TextPos{ -120, -480 };

		// Pain amount
		double pain = 0.0;
		double painVelocity = 0.0;

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Update 2D physics world
				world.update(StepTime);

				// Bodies in contact
				for (auto&& [pair, collision] : world.getCollisions())
				{
					// For each contact
					for (const auto& contact : collision)
					{
						// Damage amount
						const int32 damage = (contact.normalImpulse / 4.0);

						// If damage amount is 1.0 or more
						if (1.0 < damage)
						{
							// If contact partner is face body
							if (pair.a == faceBody.id())
							{
								table[pair.b] += damage;
							}
							else if (pair.b == faceBody.id())
							{
								table[pair.a] += damage;
							}

							// Increase pain amount
							pain += damage;

							// Add collision effect
							effect.add<DamageEffect>(contact.point, (damage / 10.0), collisionTexture);
						}
					}
				}
			}

			// Remove objects that fell down
			bodies.remove_if([&](const P2Body& b)
				{
					if (200 < b.getPos().y)
					{
						table.erase(b.id());
						return true;
					}

					return false;
				});

			// Decay pain
			pain = Math::SmoothDamp(pain, 0.0, painVelocity, 0.5);

			// Input text
			TextInput::UpdateText(text);

			// Update 2D camera
			camera.update();
			{
				Scene::Rect().draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

				// Create Transformer2D that applies 2D camera
				const auto t = camera.createTransformer();

				// Change face expression based on pain amount
				((pain < 10.0) ? face0 : (pain < 100.0) ? face1 : face2)
					.scaled(2.0)
					.drawAt(0, 0);

				// Draw falling text
				for (const auto& body : bodies)
				{
					body.draw(ColorF{ 0.11 });
				}

				// Draw cumulative damage for text
				for (const auto& body : bodies)
				{
					damageFont(table[body.id()]).drawAt(28, body.getPos().movedBy(0, -50), ColorF{ 0.1, 0.5, 0.2 });
				}

				// Draw collision effects
				effect.update();

				// Draw input text
				const String currentText = (text + TextInput::GetEditingText());
				font(currentText).draw(TextPos, ColorF{ 0.11 });

				// Convert text to P2Body when newline character is entered
				if (currentText.includes(U'\n'))
				{
					// Convert input text to PolygonGlyph
					const Array<PolygonGlyph> glyphs = font.renderPolygons(currentText.removed(U'\n'));

					// Get polygons to create P2Body
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

						// Cumulative damage value dealt by that character
						table[bodies.back().id()] = 0;
					}

					text.clear();
				}
			}
		}
	}
	```

## 10. Top-down 2D shooter
A sample top-down 2D shooting game using 2D physics features.

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/TopDownShooterP2/Screenshot/2.png)

[Siv3D-Sample | Top-down 2D shooter :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/blob/main/Samples/TopDownShooterP2){:target="_blank" .md-button}


## 11. 2D physics destruction game

![](https://raw.githubusercontent.com/Reputeless/games/main/games/005/B.png)

[Game Patterns | 2D physics destruction game :material-open-in-new:](https://github.com/Reputeless/games/blob/main/games/005/B.md){:target="_blank" .md-button}


## 12. 2D Platformer
- A sample 2D platformer that supports left/right movement, jumping, and dropping through floors.

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/p2/12.mp4?raw=true" autoplay loop muted playsinline></video>

??? memo "Code"
	```cpp
	# include <Siv3D.hpp> // Siv3D v0.6.16

	/// @brief Player state
	struct PlayerState
	{
		/// @brief Player size (distance from the center to the head, and from the center to the feet)
		static constexpr double PlayerSizeHalf = 30.0;

		/// @brief Tolerance (epsilon) for collision detection
		static constexpr double ContactEpsilon = 2.0;

		/// @brief Jump impulse magnitude
		static constexpr double JumpImpulse = 12.0;

		/// @brief Force for left/right movement
		static constexpr double WalkForce = 4000.0;

		/// @brief Strength of air resistance proportional to the player's horizontal speed
		static constexpr double LinearDrag = 16.0;

		/// @brief Player velocity
		Vec2 velocity{ 0, 0 };

		/// @brief Player position
		Vec2 position{ 0, 0 };

		/// @brief Time since the last jump
		double jumpTime = 0.0;

		/// @brief Flag indicating whether the player is standing on a floor (i.e., can jump)
		bool isStandingOnFloor = false;

		/// @brief Flag indicating whether the Down key is pressed
		bool downKeyPressed = false;

		/// @brief Returns whether the player is rising.
		/// @param epsilon Velocity threshold to be considered rising
		/// @return true if the player is rising, otherwise false
		bool isRising(double epsilon = 1.0) const
		{
			return (velocity.y < -epsilon);
		}

		/// @brief Returns the player's foot Y coordinate.
		/// @return The player's foot Y coordinate
		double footY() const
		{
			return (position.y + PlayerSizeHalf);
		}

		/// @brief Visualizes the player's state.
		void draw() const
		{
			if (isRising())
			{
				Line{ position.x, (position.y + 12), position.x, (position.y - 12) }
					.drawArrow(8, SizeF{ 18, 10 }, ColorF{ 0.1 });
			}
			else if (downKeyPressed)
			{
				Line{ position.x, (position.y - 12), position.x, (position.y + 12) }
					.drawArrow(8, SizeF{ 18, 10 }, ColorF{ 0.1 });
			}

			if (isStandingOnFloor)
			{
				position.withY(footY()).asCircle(6).draw(ColorF{ 0.1 });
			}
		}
	};

	/// @brief Updates the player's movement.
	/// @param playerBody Player body
	/// @param playerState Player state
	/// @param deltaTime Time delta used for the update
	void UpdatePlayer(P2Body& playerBody, PlayerState& playerState, double deltaTime = Scene::DeltaTime())
	{
		// Left/right movement
		{
			// Air resistance proportional to the current speed
			const double drag = (playerBody.getVelocity().x * -PlayerState::LinearDrag);

			if (KeyLeft.pressed())
			{
				// If the left key is pressed, apply force to the left
				playerBody.applyForce(Vec2{ ((-PlayerState::WalkForce + drag) * deltaTime), 0 });
			}
			else if (KeyRight.pressed())
			{
				// If the right key is pressed, apply force to the right
				playerBody.applyForce(Vec2{ ((PlayerState::WalkForce + drag) * deltaTime), 0 });
			}
		}

		// While descending, not standing on a floor
		if (playerBody.getVelocity().y > 0)
		{
			playerState.isStandingOnFloor = false;
		}

		// Jump
		{
			playerState.jumpTime += deltaTime;

			if (playerState.isStandingOnFloor && KeyUp.down())
			{
				// Apply an impulse upward
				playerBody.applyLinearImpulse(Vec2{ 0, -PlayerState::JumpImpulse });

				// Reset jump timer
				playerState.jumpTime = 0.0;

				// After jumping, the player is no longer standing on a floor
				playerState.isStandingOnFloor = false;
			}
		}

		// Update whether the Down key is pressed
		playerState.downKeyPressed = KeyDown.pressed();
	}

	/// @brief Determines whether a floor is passable.
	/// @param floor Floor body to test
	/// @param playerState Player state
	/// @return true if passable, otherwise false
	static bool IsPassable(const P2Body& floor, const PlayerState& playerState)
	{
		// Always passable when the Down key is pressed
		if (playerState.downKeyPressed)
		{
			return true;
		}

		// Always passable while the player is rising
		if (playerState.isRising())
		{
			return true;
		}

		// While descending, only floors above the player's feet (smaller Y) are passable
		return (floor.getPos().y < (playerState.footY() - PlayerState::ContactEpsilon));
	}

	/// @brief Determines whether the player has landed on a floor.
	/// @param world World
	/// @param playerBody Player body
	/// @param playerState Player state
	/// @return true if landed, otherwise false
	static bool HasLanded(const P2World& world, const P2Body& playerBody, const PlayerState& playerState)
	{
		for (auto&& [pair, collision] : world.getCollisions())
		{
			// Consider only collisions involving the player
			if ((pair.a == playerBody.id()) || (pair.b == playerBody.id()))
			{
				for (const auto& contact : collision)
				{
					// If the contact point is near the player's feet, treat as landed
					if (contact.point.y > (playerState.footY() - PlayerState::ContactEpsilon))
					{
						return true;
					}
				}
			}
		}

		return false;
	}

	/// @brief Draws a pass-through floor.
	/// @param floor Pass-through floor body
	/// @param playerState Player state
	void DrawFloor(const P2Body& floor, const PlayerState& playerState)
	{
		const auto pLine = std::dynamic_pointer_cast<P2Line>(floor.getPtr(0));

		if (IsPassable(floor, playerState))
		{
			// If passable, draw as a dotted line
			pLine->getLine().draw(LineStyle::SquareDot, 5);
		}
		else
		{
			// If not passable, draw as a normal line
			pLine->getLine().draw(5);
		}
	}

	void Main()
	{
		Window::Resize(1280, 720);

		constexpr double SimulationStepTime = (1.0 / 200.0);
		double simulationAccumulatedTime = 0.0;
		P2World world;

		// Default collision filter for walls
		constexpr P2Filter SolidFloorFilter{ .categoryBits = 0b0001, .maskBits = 0b1111 };

		// Collision filter for pass-through state
		constexpr P2Filter PassableFloorFilter{ .categoryBits = 0b0010, .maskBits = 0b1111 };

		// Player collision filter
		constexpr P2Filter NormalCharacterFilter{ .categoryBits = 0b0100, .maskBits = 0b1101 };

		// Enemy character collision filter
		constexpr P2Filter EnemyCharacterFilter{ .categoryBits = 0b1000, .maskBits = 0b1111 };

		// Ground body
		const P2Body groundFloor = world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 800, 10 }, P2Material{ .restitution = 0.0 }, SolidFloorFilter);

		// Pass-through floor bodies
		Array<P2Body> floors;
		floors << world.createLine(P2Static, Vec2{ -200, -100 }, Line{ -150, 0, 150, 0 }, OneSided::No, P2Material{ .restitution = 0.0 }, SolidFloorFilter);
		floors << world.createLine(P2Static, Vec2{ -200, -200 }, Line{ -150, 0, 150, 0 }, OneSided::No, P2Material{ .restitution = 0.0 }, SolidFloorFilter);
		floors << world.createLine(P2Static, Vec2{ -200, -300 }, Line{ -150, 0, 150, 0 }, OneSided::No, P2Material{ .restitution = 0.0 }, SolidFloorFilter);

		// Enemy character body
		const P2Body enemyBody = world.createRect(P2Dynamic, Vec2{ -200, -400 }, SizeF{ 30, 30 }, P2Material{ .density = 0.02, .restitution = 0.0, .friction = 1.0 }, EnemyCharacterFilter)
			.setFixedRotation(true);

		// Player body
		P2Body playerBody = world.createRect(P2Dynamic, Vec2{ 0, -300 }, SizeF{ 40, (PlayerState::PlayerSizeHalf * 2) }, P2Material{ .density = 0.1, .restitution = 0.0 }, NormalCharacterFilter)
			.setFixedRotation(true);

		// Fixed camera
		Camera2D fixedCamera{ Vec2{ 0, -200 }, 1.5, CameraControl::None_ };

		// Player state
		PlayerState playerState{
			.velocity = playerBody.getVelocity(),
			.position = playerBody.getPos()
		};

		while (System::Update())
		{
			// Move the player
			UpdatePlayer(playerBody, playerState);

			for (simulationAccumulatedTime += Scene::DeltaTime();
				(SimulationStepTime <= simulationAccumulatedTime); simulationAccumulatedTime -= SimulationStepTime)
			{
				// For each pass-through floor
				for (auto& floor : floors)
				{
					// Switch collision filter based on the player's state
					const bool isPassable = IsPassable(floor, playerState);
					floor.shape(0).setFilter(isPassable ? PassableFloorFilter : SolidFloorFilter);
				}

				// Step the world forward
				world.update(SimulationStepTime);

				// Update player state at each step
				{
					playerState.velocity = playerBody.getVelocity();
					playerState.position = playerBody.getPos();

					if ((0.1 <= playerState.jumpTime) && HasLanded(world, playerBody, playerState))
					{
						playerState.isStandingOnFloor = true;
					}
				}
			}

			fixedCamera.update();
			{
				const auto t = fixedCamera.createTransformer();

				// Draw background
				fixedCamera.getRegion().draw(Arg::top(0.2, 0.6, 0.9), Arg::bottom(0.2, 0.5, 0.4));

				// Draw ground
				groundFloor.draw(ColorF{ 0.6 });

				// Draw pass-through floors
				for (const auto& floor : floors)
				{
					DrawFloor(floor, playerState);
				}

				// Draw player
				playerBody.draw(ColorF{ 0.6, 0.8, 0.7 });
				playerState.draw();

				// Draw enemy character
				enemyBody.draw(ColorF{ 1.0, 0.6, 0.8 });
			}

			fixedCamera.draw(Palette::Orange);
		}
	}
	```

