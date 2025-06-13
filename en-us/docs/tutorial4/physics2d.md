# 69. 2D Physics Simulation
Learn how to use 2D physics simulation features.

## 69.1 Class Overview
The classes that appear in 2D physics simulation features are as follows:

| Class | Description |
| --- | --- |
| `P2World` | 2D physics simulation world. Usually only one is created |
| `P2Body` | A body existing in the world.<br>Composed of zero or more (usually one or more) parts `P2Shape` |
| `P2BodyID` | Unique ID assigned to body `P2Body` |
| `P2BodyType` | Enum representing whether a body is dynamic or static |
| `P2Shape` | Interface for parts that compose body `P2Body` |
| `P2ShapeType` | Enum representing the type of part `P2Shape` |
| `P2Circle` | One of the parts `P2Shape`, representing a circle |
| `P2Rect` | One of the parts `P2Shape`, representing a rectangle |
| `P2Triangle` | One of the parts `P2Shape`, representing a triangle |
| `P2Quad` | One of the parts `P2Shape`, representing a convex quadrilateral |
| `P2Polygon` | One of the parts `P2Shape`, representing a polygon |
| `P2Line` | One of the parts `P2Shape`, representing a line segment |
| `P2LineString` | One of the parts `P2Shape`, representing a collection of continuous line segments |
| `P2Material` | Represents the material (physical properties) of part `P2Shape` |
| `P2Filter` | Specifies category bit flags for part `P2Shape`.<br>Prevents interference with parts having specific bit flags |
| `P2Collision` | Information about all contacts acting on two bodies. Has up to 2 `P2Contact` |
| `P2Contact` | Information about contact acting on two bodies |
| `P2ContactPair` | A pair of `P2BodyID` when two bodies are in contact |
| `P2PivotJoint` | A type of joint that connects two bodies |
| `P2DistanceJoint` | A type of joint that connects two bodies |
| `P2SliderJoint` | A type of joint that connects two bodies |
| `P2WheelJoint` | A type of joint that connects two bodies |
| `P2MouseJoint` | A type of joint that connects two bodies |


## 69.2 World and Updates
- Create a virtual world `P2World` for physics simulation
- Update the world state with `.update()`
- The higher the update frequency, the more accurate the physics simulation, but the more calculations required
- Typically, updating 200 times per second is ideal
	- If the scene updates at 60 FPS, update the world 2 or more times per frame

??? memo "Code"
	```cpp hl_lines="7-14 18-22"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		// 2D physics simulation step time (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Advance the 2D physics world by StepTime seconds
				world.update(StepTime);
			}
		}
	}
	```


## 69.3 Dynamic Bodies
- `world.createCircle(type, center, r)` creates a body with a circle of radius `r` cm at position `center` cm in the world
- The return value is `P2Body`, through which you can get or change the body's state
- `type` represents the type of body. To create a dynamic body that is affected by forces, specify `P2Dynamic`. This time we specify `P2Dynamic` to be affected by gravity
- The gravity acceleration defaults to `Vec2{ 0, 980 }` cm/s^2, same as Earth
- The coordinate unit in the physics world is cm. As with drawing, the y-coordinate increases downward, so to create a body at a height of 300 cm, specify `Vec2{ 0, -300 }`
- Running the following code will show the body falling over time

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/3.png)

??? memo "Code"
	```cpp hl_lines="16-17 21-24"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		// 2D physics simulation step time (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		// Create a body (circle with radius 10cm) at height 300 cm
		P2Body body = world.createCircle(P2Dynamic, Vec2{ 0, -300 }, 10);

		while (System::Update())
		{
			ClearPrint();

			// Output the body's coordinates
			Print << U"{:.1f} cm"_fmt(body.getPos());

			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Advance the 2D physics world by StepTime seconds
				world.update(StepTime);
			}
		}
	}
	```


## 69.4 Body Removal (1)
- As more bodies exist in the world, CPU computation cost and memory usage increase
- Bodies that go outside the game area should be removed from the world
- Body state checking should ideally be done after each `world.update()` call, as in the following code
- You can remove a body from the world with `P2Body`'s `.release()`
- Removed bodies do not participate in subsequent world updates
- `P2Body` can be implicitly converted to `bool`. It becomes `true` if the body exists, `false` if it doesn't

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/4.png)

??? memo "Code"
	```cpp hl_lines="23-28 35-40"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		// 2D physics simulation step time (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		// Create a body (circle with radius 10cm) at height 300 cm
		P2Body body = world.createCircle(P2Dynamic, Vec2{ 0, -300 }, 10);

		while (System::Update())
		{
			ClearPrint();

			// If the body exists
			if (body)
			{
				// Output the body's coordinates
				Print << U"{:.1f} cm"_fmt(body.getPos());
			}

			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Advance the 2D physics world by StepTime seconds
				world.update(StepTime);

				// If the body exists and has fallen more than 500 cm below ground
				if (body && (500 < body.getPos().y))
				{
					// Remove the body from the world
					body.release();
				}
			}
		}
	}
	```


## 69.5 Body Removal (2)
- When handling multiple bodies, using `Array<P2Body>` is convenient
- `P2Body` removed from the array are automatically removed from the world
- Bodies have unique IDs assigned. You can get the ID with `.id()`
- Running the following code will show bodies being removed over time as they go outside the game area

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/5.png)

??? memo "Code"
	```cpp hl_lines="16-20 26-29 36-37"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		// 2D physics simulation step time (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		// Create 3 bodies (circles with radius 10cm)
		Array<P2Body> bodies;
		bodies << world.createCircle(P2Dynamic, Vec2{ -100, -300 }, 10);
		bodies << world.createCircle(P2Dynamic, Vec2{ 0, -600 }, 10);
		bodies << world.createCircle(P2Dynamic, Vec2{ 100, -900 }, 10);

		while (System::Update())
		{
			ClearPrint();

			for (const auto& body : bodies)
			{
				Print << U"ID: {}, {:.1f} cm"_fmt(body.id(), body.getPos());
			}

			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Advance the 2D physics world by StepTime seconds
				world.update(StepTime);

				// Remove bodies that have fallen more than 500 cm below ground
				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}
		}
	}
	```


## 69.6 Body Drawing and 2D Camera
- You can draw a body on screen based on its shape and state (position, etc.) by calling `.draw()` on `P2Body`
- Combined with a 2D camera (**Tutorial 49**), you can draw the world from various perspectives (center coordinates, zoom) which is convenient

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/6.png)

??? memo "Code"
	```cpp hl_lines="22-23 43-57"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		// 2D physics simulation step time (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		// Create 3 bodies (circles with radius 10cm)
		Array<P2Body> bodies;
		bodies << world.createCircle(P2Dynamic, Vec2{ -100, -300 }, 10);
		bodies << world.createCircle(P2Dynamic, Vec2{ 0, -600 }, 10);
		bodies << world.createCircle(P2Dynamic, Vec2{ 100, -900 }, 10);

		// 2D camera (center coordinates (0, -300), zoom 1.0)
		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			ClearPrint();

			for (const auto& body : bodies)
			{
				Print << U"ID: {}, {:.1f} cm"_fmt(body.id(), body.getPos());
			}

			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Advance the 2D physics world by StepTime seconds
				world.update(StepTime);

				// Remove bodies that have fallen more than 500 cm below ground
				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}

			// Update the 2D camera
			camera.update();
			{
				// Create Transformer2D from the 2D camera
				const auto t = camera.createTransformer();

				// Draw all bodies
				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);
		}
	}
	```


## 69.7 Static Bodies
- Create a fixed floor in the world
- `world.createRect(type, center, size);` creates a body with a rectangle of size `size` cm centered at `center` cm in the world
- `type` represents the type of body. To create floor or wall-like bodies that are always fixed and not affected by forces, specify `P2Static`. This time we specify `P2Static` to create a fixed floor
- Running the following code, the falling circle will stop at around -15.1 cm height from the origin
- The floor has a thickness of 5 cm upward from the origin, and the circle has a radius of 10 cm, so theoretically it should be at -15 cm position, but it becomes around -15.1 cm because small gaps are automatically inserted between bodies to stabilize the simulation

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/7.png)

??? memo "Code"
	```cpp hl_lines="16-17 52-53"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		// 2D physics simulation step time (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		// Ground (rectangle 1000 cm wide, 10 cm high)
		const P2Body ground = world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });

		// Create 3 bodies (circles with radius 10cm)
		Array<P2Body> bodies;
		bodies << world.createCircle(P2Dynamic, Vec2{ -100, -300 }, 10);
		bodies << world.createCircle(P2Dynamic, Vec2{ 0, -600 }, 10);
		bodies << world.createCircle(P2Dynamic, Vec2{ 100, -900 }, 10);

		// 2D camera (center coordinates (0, -300), zoom 1.0)
		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			ClearPrint();

			for (const auto& body : bodies)
			{
				Print << U"ID: {}, {:.1f} cm"_fmt(body.id(), body.getPos());
			}

			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Advance the 2D physics world by StepTime seconds
				world.update(StepTime);

				// Remove bodies that have fallen more than 500 cm below ground
				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}

			// Update the 2D camera
			camera.update();
			{
				// Create Transformer2D from the 2D camera
				const auto t = camera.createTransformer();

				// Draw the ground
				ground.draw(Palette::Gray);

				// Draw all bodies
				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);
		}
	}
	```


## 69.8 Various Shape Parts
- You can create bodies with `Circle`, `RectF`, `Triangle`, `Quad`, `Polygon` as parts
- Also, limited to `P2Static`, you can create bodies with `Line`, `LineString` shapes as parts

| Part Shape | Body Creation Function | Can be P2Dynamic? |
| --- | --- |:---:|
| Circle | `world.createCircle(type, center, r)` | ✅ |
| Rectangle | `world.createRect(type, center, size)` | ✅ |
| Triangle | `world.createTriangle(type, center, triangle)` | ✅ |
| Convex quadrilateral | `world.createQuad(type, center, quad)` | ✅ |
| Polygon | `world.createPolygon(type, center, polygon)` | ✅ |
| Line segment | `world.createLine(type, center, line)` |  |
| Collection of line segments | `world.createLineString(type, center, lineString)` |  |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/8.png)

??? memo "Code"
	```cpp hl_lines="16-22 29-30 37-38 47-57 63-87"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		// 2D physics simulation step time (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		// Ground
		Array<P2Body> grounds;
		grounds << world.createRect(P2Static, Vec2{ 0, -200 }, SizeF{ 600, 20 });
		grounds << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -500, -150, -300, -50 });
		grounds << world.createLineString(P2Static, Vec2{ 0, 0 }, LineString{ Vec2{ 100, -50 }, Vec2{ 200, -50 }, Vec2{ 600, -150 } });

		Array<P2Body> bodies;

		// 2D camera (center coordinates (0, -300), zoom 1.0)
		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			ClearPrint();
			Print << U"bodies.size(): " << bodies.size() << U"\n";

			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Advance the 2D physics world by StepTime seconds
				world.update(StepTime);

				// Remove bodies that have fallen more than 500 cm below ground
				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}

			// Update the 2D camera
			camera.update();
			{
				// Create Transformer2D from the 2D camera
				const auto t = camera.createTransformer();

				// Draw all ground
				for (const auto& ground : grounds)
				{
					ground.draw(Palette::Gray);
				}

				// Draw all bodies
				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Circle", Vec2{ 40, 80 }, 120))
			{
				bodies << world.createCircle(P2Dynamic, Vec2{ Random(-400, 400), -600 }, 20);
			}

			if (SimpleGUI::Button(U"Rect", Vec2{ 40, 120 }, 120))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ Random(-400, 400), -600}, Size{20, 60});
			}

			if (SimpleGUI::Button(U"Triangle", Vec2{ 40, 160 }, 120))
			{
				bodies << world.createTriangle(P2Dynamic, Vec2{ Random(-400, 400), -600 }, Triangle{ 40 });
			}

			if (SimpleGUI::Button(U"Quad", Vec2{ 40, 200 }, 120))
			{
				bodies << world.createQuad(P2Dynamic, Vec2{ Random(-400, 400), -600 }, RectF{ Arg::center(0, 0), 40 }.skewedX(45_deg) );
			}

			if (SimpleGUI::Button(U"Polygon", Vec2{ 40, 240 }, 120))
			{
				const Polygon polygon = Shape2D::NStar(5, 30, 20);
				bodies << world.createPolygon(P2Dynamic, Vec2{ Random(-400, 400), -600 }, polygon);
			}
		}
	}
	```


## 69.9 Getting 2D Shapes from Bodies
- Bodies are usually composed of one or more parts
- By getting a reference to a body's part with `body.shape(index)` and casting it to the appropriate part shape class, you can get the state of body parts existing in the world as 2D shapes like `Circle` or `Quad`

| Creation Function | P2ShapeType | Part Shape Class | Resulting 2D Shape |
| --- | --- | --- | --- |
| `world.createCircle(type, center, r)` | `P2ShapeType::Circle` | `P2Circle` | `Circle` |
| `world.createRect(type, center, size)` | `P2ShapeType::Rect` | `P2Rect` | `Quad` (due to rotation) |
| `world.createTriangle(type, center, triangle)` | `P2ShapeType::Triangle` | `P2Triangle` | `Triangle` |
| `world.createQuad(type, center, quad)` | `P2ShapeType::Quad` | `P2Quad` | `Quad` |
| `world.createPolygon(type, center, polygon)` | `P2ShapeType::Polygon` | `P2Polygon` | `Polygon` |
| `world.createLine(type, center, line)` | `P2ShapeType::Line` | `P2Line` | `Line` |
| `world.createLineString(type, center, lineString)` | `P2ShapeType::LineString` | `P2LineString` | `LineString` |

- The following code draws an outline on the part of the last added body, and changes the cursor to a hand shape when hovering over that part

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/9.png)

??? memo "Code"
	```cpp hl_lines="59-123"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		// 2D physics simulation step time (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		// Ground
		Array<P2Body> grounds;
		grounds << world.createRect(P2Static, Vec2{ 0, -200 }, SizeF{ 600, 20 });
		grounds << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -500, -150, -300, -50 });
		grounds << world.createLineString(P2Static, Vec2{ 0, 0 }, LineString{ Vec2{ 100, -50 }, Vec2{ 200, -50 }, Vec2{ 600, -150 } });

		Array<P2Body> bodies;

		// 2D camera (center coordinates (0, -300), zoom 1.0)
		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			ClearPrint();
			Print << U"bodies.size(): " << bodies.size() << U"\n";

			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Advance the 2D physics world by StepTime seconds
				world.update(StepTime);

				// Remove bodies that have fallen more than 500 cm below ground
				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}

			// Update the 2D camera
			camera.update();
			{
				// Create Transformer2D from the 2D camera
				const auto t = camera.createTransformer();

				// Draw all ground
				for (const auto& ground : grounds)
				{
					ground.draw(Palette::Gray);
				}

				// Draw all bodies
				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}

				if (bodies)
				{
					const auto& body = bodies.back();
					const P2Shape& shape = body.shape(0);
					shape.drawFrame(4, ColorF{ 1.0 });

					switch (shape.getShapeType())
					{
					case P2ShapeType::Circle:
						{
							const P2Circle& circleShape = static_cast<const P2Circle&>(shape);
							
							if (const Circle circle = circleShape.getCircle(); circle.mouseOver())
							{
								Cursor::RequestStyle(CursorStyle::Hand);
							}

							break;
						}
					case P2ShapeType::Rect:
						{
							const P2Rect& rectShape = static_cast<const P2Rect&>(shape);
							
							if (const Quad quad = rectShape.getQuad(); quad.mouseOver())
							{
								Cursor::RequestStyle(CursorStyle::Hand);
							}

							break;
						}
					case P2ShapeType::Triangle:
						{
							const P2Triangle& triangleShape = static_cast<const P2Triangle&>(shape);
							
							if (const Triangle triangle = triangleShape.getTriangle(); triangle.mouseOver())
							{
								Cursor::RequestStyle(CursorStyle::Hand);
							}

							break;
						}
					case P2ShapeType::Quad:
						{
							const P2Quad& quadShape = static_cast<const P2Quad&>(shape);
							
							if (const Quad quad = quadShape.getQuad(); quad.mouseOver())
							{
								Cursor::RequestStyle(CursorStyle::Hand);
							}

							break;
						}
					case P2ShapeType::Polygon:
						{
							const P2Polygon& polygonShape = static_cast<const P2Polygon&>(shape);
							
							if (const Polygon polygon = polygonShape.getPolygon(); polygon.mouseOver())
							{
								Cursor::RequestStyle(CursorStyle::Hand);
							}

							break;
						}
					}
				}
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Circle", Vec2{ 40, 80 }, 120))
			{
				bodies << world.createCircle(P2Dynamic, Vec2{ Random(-400, 400), -600 }, 20);
			}

			if (SimpleGUI::Button(U"Rect", Vec2{ 40, 120 }, 120))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ Random(-400, 400), -600}, Size{20, 60});
			}

			if (SimpleGUI::Button(U"Triangle", Vec2{ 40, 160 }, 120))
			{
				bodies << world.createTriangle(P2Dynamic, Vec2{ Random(-400, 400), -600 }, Triangle{ 40 });
			}

			if (SimpleGUI::Button(U"Quad", Vec2{ 40, 200 }, 120))
			{
				bodies << world.createQuad(P2Dynamic, Vec2{ Random(-400, 400), -600 }, RectF{ Arg::center(0, 0), 40 }.skewedX(45_deg) );
			}

			if (SimpleGUI::Button(U"Polygon", Vec2{ 40, 240 }, 120))
			{
				const Polygon polygon = Shape2D::NStar(5, 30, 20);
				bodies << world.createPolygon(P2Dynamic, Vec2{ Random(-400, 400), -600 }, polygon);
			}
		}
	}
	```


## 69.10 Part Materials
- When creating body parts, you can specify material with `P2Material`

| Parameter | Description | Default Value |
| --- | --- | --- |
| `density` | Part density (kg / m^2). Higher values mean more weight per area | `1.0` |
| `restitution` | Part restitution coefficient. Higher values mean more bounce. Usually in range [0.0, 1.0] | `0.1` |
| `friction` | Part friction coefficient. Higher values mean more friction. Usually in range [0.0, 1.0] | `0.2` |
| `restitutionThreshold` | Lower limit of velocity (m/s) for restitution to occur. Parts bounce when colliding faster than this | `1.0` |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/10.png)

??? memo "Code"
	```cpp hl_lines="22-31"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		// 2D physics simulation step time (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		// Ground
		Array<P2Body> grounds;
		grounds << world.createRect(P2Static, Vec2{ -200, 0 }, SizeF{ 600, 10 });
		grounds << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ 0, -150, 800, -250 });

		Array<P2Body> bodies;
		bodies << world.createCircle(P2Dynamic, Vec2{ -300, -600 }, 10, P2Material{ .restitution = 0.0 }); // No bounce
		bodies << world.createCircle(P2Dynamic, Vec2{ -200, -600 }, 10, P2Material{ .restitution = 0.5 }); // Little bounce
		bodies << world.createCircle(P2Dynamic, Vec2{ -100, -600 }, 10, P2Material{ .restitution = 0.9 }); // Bouncy

		bodies << world.createRect(P2Dynamic, Vec2{ 200, -600 }, SizeF{ 30, 20 }, P2Material{ .restitution = 0.1, .friction = 0.0 }); // No friction
		bodies << world.createRect(P2Dynamic, Vec2{ 300, -600 }, SizeF{ 30, 20 }, P2Material{ .restitution = 0.1, .friction = 0.3 }); // Little friction
		bodies << world.createRect(P2Dynamic, Vec2{ 400, -600 }, SizeF{ 30, 20 }, P2Material{ .restitution = 0.1, .friction = 0.9 }); // Friction

		bodies << world.createRect(P2Dynamic, Vec2{ -400, -600 }, SizeF{ 10, 80 }, P2Material{ .density = 10.0 }); // High density
		bodies << world.createRect(P2Dynamic, Vec2{ -350, -600 }, SizeF{ 10, 80 }, P2Material{ .density = 0.01 }); // Low density

		// 2D camera (center coordinates (0, -300), zoom 1.0)
		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Advance the 2D physics world by StepTime seconds
				world.update(StepTime);

				// Remove bodies that have fallen more than 500 cm below ground
				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}

			// Update the 2D camera
			camera.update();
			{
				// Create Transformer2D from the 2D camera
				const auto t = camera.createTransformer();

				// Draw all ground
				for (const auto& ground : grounds)
				{
					ground.draw(Palette::Gray);
				}

				// Draw all bodies
				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);
		}
	}
	```


## 69.11 Body Initial State (Initial Velocity, Rotation Angle, Angular Velocity)
- `P2Body` can set initial state with the following member functions

| Code | Description |
| --- | --- |
| `.setVelocity(velocity)` | Set body velocity (cm/s) |
| `.setAngle(angle)` | Set body rotation angle (rad) |
| `.setAngularVelocity(angularVelocity)` | Set body angular velocity (rad/s) |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/11.png)

??? memo "Code"
	```cpp hl_lines="53-81"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		// 2D physics simulation step time (seconds)
		constexpr double StepTime = (1.0 / 200.0);

		// 2D physics simulation accumulated time (seconds)
		double accumulatedTime = 0.0;

		// 2D physics world
		P2World world;

		const P2Body ground = world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });

		Array<P2Body> bodies;

		// 2D camera (center coordinates (0, -300), zoom 1.0)
		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// Advance the 2D physics world by StepTime seconds
				world.update(StepTime);

				// Remove bodies that have fallen more than 500 cm below ground
				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}

			// Update the 2D camera
			camera.update();
			{
				// Create Transformer2D from the 2D camera
				const auto t = camera.createTransformer();

				// Draw the ground
				ground.draw(Palette::Gray);

				// Draw all bodies
				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Normal Rectangle", Vec2{ 40, 40 }, 300))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ -250, -400 }, SizeF{ 40, 20 });
			}

			if (SimpleGUI::Button(U"Rectangle with Initial Velocity", Vec2{ 40, 80 }, 300))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ -250, -400 }, SizeF{ 40, 20 })
					.setVelocity(Vec2{ 300, -300 });
			}

			if (SimpleGUI::Button(U"Rectangle with Rotation Angle", Vec2{ 40, 120 }, 300))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ -250, -400 }, SizeF{ 40, 20 })
					.setAngle(30_deg);
			}

			if (SimpleGUI::Button(U"Rectangle with Angular Velocity", Vec2{ 40, 160 }, 300))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ -250, -400 }, SizeF{ 40, 20 })
					.setAngularVelocity(180_deg);
			}

			if (SimpleGUI::Button(U"Rectangle with Initial Velocity and Angular Velocity", Vec2{ 40, 200 }, 300))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ -250, -400 }, SizeF{ 40, 20 })
					.setVelocity(Vec2{ 300, -300 })
					.setAngularVelocity(180_deg);
			}
		}
	}
	```


## 69.12 Applying Forces to Bodies
- You can apply a force with vector `v` to `P2Body` using `.applyForce(v)`
- By continuously applying force, you can change the body's velocity

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/12.png)

??? memo "Code"
	```cpp hl_lines="27-45"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		constexpr double StepTime = (1.0 / 200.0);

		double accumulatedTime = 0.0;

		P2World world;

		const P2Body ground = world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });

		P2Body body = world.createRect(P2Dynamic, Vec2{ 0, -100 }, SizeF{ 50, 50 });

		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		size_t index = 2;

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);

				// Apply force to the body
				switch (index)
				{
				case 0:
					body.applyForce(Vec2{ -100, 0 });
					break;
				case 1:
					body.applyForce(Vec2{ -50, 0 });
					break;
				case 2:
					body.applyForce(Vec2{ 0, 0 });
					break;
				case 3:
					body.applyForce(Vec2{ 50, 0 });
					break;
				case 4:
					body.applyForce(Vec2{ 100, 0 });
					break;
				}
			}

			camera.update();
			{
				const auto t = camera.createTransformer();

				ground.draw(Palette::Gray);

				body.draw(ColorF{ 0.96 });
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);

			SimpleGUI::RadioButtons(index, { U"(-100, 0)", U"(-50, 0)", U"(0, 0)", U"(50, 0)", U"(100, 0)" }, Vec2{ 40, 40 });
		}
	}
	```


## 69.13 Applying Impulses to Bodies
- You can apply an impulse with vector `v` to `P2Body` using `.applyLinearImpulse(v)`
- Impulses instantly change the body's velocity

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/13.png)

??? memo "Code"
	```cpp hl_lines="38-51"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		constexpr double StepTime = (1.0 / 200.0);

		double accumulatedTime = 0.0;

		P2World world;

		const P2Body ground = world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });

		P2Body body = world.createRect(P2Dynamic, Vec2{ 0, -100 }, SizeF{ 50, 50 });

		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);
			}

			camera.update();
			{
				const auto t = camera.createTransformer();

				ground.draw(Palette::Gray);

				body.draw(ColorF{ 0.96 });
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Left", Vec2{ 40, 40 }, 120))
			{
				body.applyLinearImpulse(Vec2{ -100, 0 });
			}

			if (SimpleGUI::Button(U"Right", Vec2{ 40, 80 }, 120))
			{
				body.applyLinearImpulse(Vec2{ 100, 0 });
			}

			if (SimpleGUI::Button(U"Up", Vec2{ 40, 120 }, 120))
			{
				body.applyLinearImpulse(Vec2{ 0, -100 });
			}
		}
	}
	```


## 69.14 Body Sleep
- When bodies enter a stable state in the world, they enter a sleep state, skipping calculations to speed up simulation
- Sleeping bodies are automatically awakened when they collide with other bodies or have forces applied to them
- You can explicitly put bodies to sleep with `.setAwake(false)` to suppress interference between bodies, for example, to stabilize the initial state of a tower of stacked bodies
- The following code displays sleeping bodies in light colors. You can also confirm that a tower of bodies put to sleep remains stable

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/14.png)

??? memo "Code"
	```cpp hl_lines="15-28 47-58"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		constexpr double StepTime = (1.0 / 200.0);

		double accumulatedTime = 0.0;

		P2World world;

		const P2Body ground = world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });

		Array<P2Body> bodies;
		bodies << world.createRect(P2Dynamic, Vec2{ -400, -400 }, SizeF{ 60, 40 });
		bodies << world.createRect(P2Dynamic, Vec2{ -300, -600 }, SizeF{ 60, 40 });

		for (int32 i = 0; i < 10; ++i)
		{
			bodies << world.createRect(P2Dynamic, Vec2{ -100, (-30 - i * 60) }, SizeF{ 8, 60 });
		}

		for (int32 i = 0; i < 10; ++i)
		{
			// Explicitly put to sleep
			bodies << world.createRect(P2Dynamic, Vec2{ 300, (-30 - i * 60) }, SizeF{ 8, 60 }).setAwake(false);
		}

		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);

				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}

			camera.update();
			{
				const auto t = camera.createTransformer();

				ground.draw(Palette::Gray);

				for (const auto& body : bodies)
				{
					if (body.isAwake())
					{
						body.draw(HSV{ body.id() * 10.0 });
					}
					else
					{
						// Display sleeping bodies in light colors
						body.draw(HSV{ body.id() * 10.0, 0.2, 1.0 });
					}
				}
			}

			camera.draw(Palette::Orange);
		}
	}
	```


## 69.15 Gravity Settings
- You can set gravity with `P2World`'s `.setGravity(v)`
- Since sleeping bodies don't notice gravity changes, all bodies need to be awakened when gravity is changed

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/15.png)

??? memo "Code"
	```cpp hl_lines="3-10 55-77"
	# include <Siv3D.hpp>

	// Wake up all bodies
	void AwakeAll(Array<P2Body>& bodies)
	{
		for (auto& body : bodies)
		{
			body.setAwake(true);
		}
	}

	void Main()
	{
		Window::Resize(1280, 720);

		constexpr double StepTime = (1.0 / 200.0);

		double accumulatedTime = 0.0;

		P2World world;

		const P2Body ground = world.createClosedLineString(P2Static, Vec2{ 0, 0 },
			LineString{ Vec2{ -400, -600 }, Vec2{ 400, -600 }, Vec2{ 400, 0 }, Vec2{ -400, 0 } });

		Array<P2Body> bodies;
		bodies << world.createRect(P2Dynamic, Vec2{ -200, -200 }, SizeF{ 50, 50 });
		bodies << world.createRect(P2Dynamic, Vec2{ -100, -200 }, SizeF{ 50, 50 });
		bodies << world.createCircle(P2Dynamic, Vec2{ 100, -200 }, 20);
		bodies << world.createCircle(P2Dynamic, Vec2{ 200, -200 }, 20);

		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);
			}

			camera.update();
			{
				const auto t = camera.createTransformer();

				ground.draw(Palette::Gray);

				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}
			}

			// Draw 2D camera controls
			camera.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Left", Vec2{ 40, 40 }, 120))
			{
				world.setGravity(Vec2{ -980, 0 });
				AwakeAll(bodies);
			}

			if (SimpleGUI::Button(U"Right", Vec2{ 40, 80 }, 120))
			{
				world.setGravity(Vec2{ 980, 0 });
				AwakeAll(bodies);
			}

			if (SimpleGUI::Button(U"Up", Vec2{ 40, 120 }, 120))
			{
				world.setGravity(Vec2{ 0, -980 });
				AwakeAll(bodies);
			}

			if (SimpleGUI::Button(U"Down", Vec2{ 40, 160 }, 120))
			{
				world.setGravity(Vec2{ 0, 980 });
				AwakeAll(bodies);
			}

			Line{ Vec2{ 640, 360 }, (Vec2{ 640, 360 } + world.getGravity() * 0.1) }.drawArrow(20, SizeF{ 30, 30 });
		}
	}
	```


## 69.16 Collision Detection
- Each time the world is updated, collisions between bodies are detected
- You can get the latest list of collisions with `P2World`'s `.getCollisions()`
- The return value is `HashTable<P2ContactPair, P2Collision>`
- `P2ContactPair` is a pair of colliding bodies, with `.a` and `.b` storing the IDs of the colliding bodies
- The following code draws bodies in contact with the ground in white

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/16.png)

??? memo "Code"
	```cpp hl_lines="15-16 24-25 33-45 56-67"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		constexpr double StepTime = (1.0 / 200.0);

		double accumulatedTime = 0.0;

		P2World world;

		const P2Body ground = world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });

		// Ground ID
		const P2BodyID groundID = ground.id();

		Array<P2Body> bodies;

		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			// List of body IDs in contact with ground
			HashSet<P2BodyID> groundContacts;

			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);

				groundContacts.clear();

				// Iterate through collision list
				for (auto&& [pair, collision] : world.getCollisions())
				{
					// If one of the collision pair is the ground ID, the other is a body in contact with ground
					if (pair.a == groundID)
					{
						groundContacts.insert(pair.b);
					}
					else if (pair.b == groundID)
					{
						groundContacts.insert(pair.a);
					}
				}

				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}

			camera.update();
			{
				const auto t = camera.createTransformer();

				ground.draw(Palette::Gray);

				for (const auto& body : bodies)
				{
					// Draw bodies in contact with ground in white
					if (groundContacts.contains(body.id()))
					{
						body.draw(Palette::White);
					}
					else
					{
						body.draw(HSV{ body.id() * 10.0 });
					}
				}
			}

			camera.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Rect", Vec2{ 40, 40 }))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ Random(-200, 200), -300 }, SizeF{ 60, 40 });
			}
		}
	}
	```


## 69.17 Pivot Joint
- Pivot joint `P2PivotJoint` is a joint that connects two bodies with a single rotation axis (anchor)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/17.png)

??? memo "Code"
	```cpp hl_lines="18-31 55-56 71-76"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		constexpr double StepTime = (1.0 / 200.0);

		double accumulatedTime = 0.0;

		P2World world;

		Array<P2Body> grounds;
		grounds << world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });
		grounds << world.createRect(P2Static, Vec2{ 200, -150 }, SizeF{ 100, 20 });
		grounds << world.createRect(P2Static, Vec2{ -300, -550 }, SizeF{ 40, 40 });

		// Flipper
		const Vec2 flipperAnchor = Vec2{ 150, -150 };
		P2Body flipper = world.createRect(P2Dynamic, flipperAnchor, RectF{ -100, -5, 100, 10 });
		// Create pivot joint connecting flipper and grounds[1]
		const P2PivotJoint flipperJoint = world.createPivotJoint(grounds[1], flipper, flipperAnchor)
			.setLimits(-10_deg, 30_deg) // Set rotation limit angles
			.setLimitsEnabled(true); // Enable rotation limits

		// Pendulum
		const Vec2 pendulumAnchor = Vec2{ -300, -550 };
		const P2Body pendulum = world.createRect(P2Dynamic, pendulumAnchor, RectF{ -5, 0, 10, 200 })
			.setAngularDamping(0.2); // Parameter to dampen rotation
		// Create pivot joint connecting pendulum and grounds[2]
		const P2PivotJoint pendulumJoint = world.createPivotJoint(grounds[2], pendulum, pendulumAnchor);

		Array<P2Body> bodies;

		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);

				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}

			camera.update();
			{
				const auto t = camera.createTransformer();

				for (const auto& ground : grounds)
				{
					ground.draw(Palette::Gray);
				}

				flipper.draw();
				pendulum.draw();

				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}
			}

			camera.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Rect", Vec2{ 40, 40 }, 100))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ Random(20, 100), -600 }, SizeF{ 60, 40 }, P2Material{ .density = 0.1 });
			}

			// Flipper controls
			if (SimpleGUI::Button(U"Flipper", Vec2{ 40, 80 }, 100))
			{
				// Apply rotational impulse to flipper
				flipper.applyAngularImpulse(5000);
			}
		}
	}
	```



## 69.18 Distance Joint
- Distance joint `P2DistanceJoint` is a joint that keeps the anchors of two bodies at a constant distance, or within a constant distance range
- In the following code, the left pendulum maintains a distance of 200 cm from the ceiling anchor in the air, while the right pendulum maintains a distance range of 180-220 cm from the ceiling anchor in the air

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/18.png)

??? memo "Code"
	```cpp hl_lines="18-25 49-53 68-78"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		constexpr double StepTime = (1.0 / 200.0);

		double accumulatedTime = 0.0;

		P2World world;

		Array<P2Body> grounds;
		grounds << world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });
		grounds << world.createRect(P2Static, Vec2{ -300, -300 }, SizeF{ 40, 40 });
		grounds << world.createRect(P2Static, Vec2{ 300, -300 }, SizeF{ 40, 40 });

		// Left pendulum
		P2Body leftBall = world.createCircle(P2Dynamic, Vec2{ -300, -100 }, 20);
		const P2DistanceJoint leftJoint = world.createDistanceJoint(grounds[1], Vec2{ -300, -300 }, leftBall, Vec2{ -300, -100 }, 200);

		// Right pendulum
		P2Body rightBall = world.createCircle(P2Dynamic, Vec2{ 300, -100 }, 20);
		const P2DistanceJoint rightJoint = world.createDistanceJoint(grounds[2], Vec2{ 300, -300 }, rightBall, Vec2{ 300, -100 }, 200)
			.setMinLength(180).setMaxLength(220); // Set distance range 180-220

		Array<P2Body> bodies;

		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);

				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}

			camera.update();
			{
				const auto t = camera.createTransformer();

				for (const auto& ground : grounds)
				{
					ground.draw(Palette::Gray);
				}

				leftBall.draw();
				rightBall.draw();

				Line{ leftJoint.getAnchorPosA(), leftJoint.getAnchorPosB() }.draw(LineStyle::SquareDot, 4.0, Palette::Orange);
				Line{ rightJoint.getAnchorPosA(), rightJoint.getAnchorPosB() }.draw(LineStyle::SquareDot, 4.0, Palette::Orange);

				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}
			}

			camera.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Rect", Vec2{ 40, 40 }, 100))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ Random(-200, 200), -600 }, SizeF{ 40, 40 }, P2Material{ .density = 0.1 });
			}

			if (SimpleGUI::Button(U"Left", Vec2{ 40, 80 }, 100))
			{
				// Apply rightward impulse to left pendulum
				leftBall.applyLinearImpulse(Vec2{ 100, 0 });
			}

			if (SimpleGUI::Button(U"Right", Vec2{ 40, 120 }, 100))
			{
				// Apply leftward impulse to right pendulum
				rightBall.applyLinearImpulse(Vec2{ -100, 0 });
			}
		}
	}
	```


## 69.19 Slider Joint
- Slider joint `P2SliderJoint` is a joint that connects two bodies so that one can move along a straight line

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/19.png)

??? memo "Code"
	```cpp hl_lines="18-30 54-55 62-63 73-102"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		constexpr double StepTime = (1.0 / 200.0);

		double accumulatedTime = 0.0;

		P2World world;

		Array<P2Body> grounds;
		grounds << world.createRect(P2Static, Vec2{ -200, 0 }, SizeF{ 700, 10 });
		grounds << world.createCircle(P2Static, Vec2{ -500, -200 }, 20);
		grounds << world.createCircle(P2Static, Vec2{ 300, -400 }, 20);

		// Rightward slider
		const P2Body wall = world.createRect(P2Dynamic, Vec2{ -500, -200 }, SizeF{ 20, 320 });
		P2SliderJoint wallJoint = world.createSliderJoint(grounds[1], wall, Vec2{ -500, -200 }, Vec2{ 1, 0 })
			.setLimits(20, 400).setLimitEnabled(true) // Set movable range
			.setMaxMotorForce(1000) // Set maximum motor force. If this is too small, it may not move
			.setMotorEnabled(true); // Enable motor

		// Downward slider
		const P2Body floor = world.createRect(P2Dynamic, Vec2{ 300, -400 }, SizeF{ 250, 10 });
		P2SliderJoint floorJoint = world.createSliderJoint(grounds[2], floor, Vec2{ 300, -400 }, Vec2{ 0, 1 })
			.setLimits(100, 410).setLimitEnabled(true) // Set movable range
			.setMaxMotorForce(1000) // Set maximum motor force. If this is too small, it may not move
			.setMotorEnabled(true); // Enable motor

		Array<P2Body> bodies;

		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);

				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}

			camera.update();
			{
				const auto t = camera.createTransformer();

				for (const auto& ground : grounds)
				{
					ground.draw(Palette::Gray);
				}

				wall.draw();
				floor.draw();

				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}

				Line{ wallJoint.getAnchorPosA(), wallJoint.getAnchorPosB() }.draw(LineStyle::SquareDot, 4.0, Palette::Orange);
				Line{ floorJoint.getAnchorPosA(), floorJoint.getAnchorPosB() }.draw(LineStyle::SquareDot, 4.0, Palette::Orange);
			}

			camera.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Rect", Vec2{ 40, 40 }, 120))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ Random(-400, 200), -600 }, SizeF{ 40, 40 }, P2Material{ .density = 0.1 });
			}

			if (SimpleGUI::Button(U"Wall ←", Vec2{ 40, 80 }, 120))
			{
				// Set motor speed
				wallJoint.setMotorSpeed(-100);
			}

			if (SimpleGUI::Button(U"Wall Stop", Vec2{ 40, 120 }, 120))
			{
				wallJoint.setMotorSpeed(0);
			}

			if (SimpleGUI::Button(U"Wall →", Vec2{ 40, 160 }, 120))
			{
				wallJoint.setMotorSpeed(100);
			}

			if (SimpleGUI::Button(U"Floor ↑", Vec2{ 40, 200 }, 120))
			{
				floorJoint.setMotorSpeed(-100);
			}

			if (SimpleGUI::Button(U"Floor Stop", Vec2{ 40, 240 }, 120))
			{
				floorJoint.setMotorSpeed(0);
			}

			if (SimpleGUI::Button(U"Floor ↓", Vec2{ 40, 280 }, 120))
			{
				floorJoint.setMotorSpeed(100);
			}
		}
	}
	```



## 69.20 Wheel Joint
- Wheel joint `P2WheelJoint` is a joint that connects two bodies with a single rotation axis like a car wheel

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/20.png)

??? memo "Code"
	```cpp hl_lines="3-42 64 72 86-89 99-102 109-132"
	# include <Siv3D.hpp>

	struct Car
	{
		P2Body body;
		P2Body wheelL;
		P2Body wheelR;
		P2WheelJoint wheelJointL;
		P2WheelJoint wheelJointR;

		void draw() const
		{
			body.draw();
			wheelL.draw(ColorF{ 0.25 }).drawWireframe(2, Palette::Orange);
			wheelR.draw(ColorF{ 0.25 }).drawWireframe(2, Palette::Orange);
		}

		void setMotorSpeed(double speed)
		{
			wheelJointL.setMotorSpeed(speed);
			wheelJointR.setMotorSpeed(speed);
		}
	};

	Car CreateCar(P2World& world, const Vec2& pos, double dampingRatio)
	{
		Car car;
		car.body = world.createRect(P2Dynamic, pos, SizeF{ 200, 40 });
		car.wheelL = world.createCircle(P2Dynamic, pos + Vec2{ -50, 20 }, 30)
			.setAngularDamping(1.5); // Rotational damping
		car.wheelR = world.createCircle(P2Dynamic, pos + Vec2{ 50, 20 }, 30)
			.setAngularDamping(1.5); // Rotational damping
		car.wheelJointL = world.createWheelJoint(car.body, car.wheelL, car.wheelL.getPos(), Vec2{ 0, -1 })
			.setLinearStiffness(4.0, dampingRatio)
			.setLimits(-5, 5).setLimitsEnabled(true)
			.setMaxMotorTorque(1000).setMotorEnabled(true);
		car.wheelJointR = world.createWheelJoint(car.body, car.wheelR, car.wheelR.getPos(), Vec2{ 0, -1 })
			.setLinearStiffness(4.0, dampingRatio)
			.setLimits(-5, 5).setLimitsEnabled(true)
			.setMaxMotorTorque(1000).setMotorEnabled(true);
		return car;
	}

	void Main()
	{
		Window::Resize(1280, 720);

		constexpr double StepTime = (1.0 / 200.0);

		double accumulatedTime = 0.0;

		P2World world;

		Array<P2Body> grounds;
		grounds << world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });
		grounds << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -800, -200, -300, -100 });

		Array<Car> cars;

		Array<P2Body> bodies;

		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		double motorSpeed = 0.0;

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);

				cars.remove_if([](const Car& car) { return (500 < car.body.getPos().y); });

				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}

			camera.update();
			{
				const auto t = camera.createTransformer();

				for (const auto& ground : grounds)
				{
					ground.draw(Palette::Gray);
				}

				for (const auto& car : cars)
				{
					car.draw();
				}

				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}
			}

			camera.draw(Palette::Orange);

			for (auto& car : cars)
			{
				car.setMotorSpeed(motorSpeed);
			}

			if (SimpleGUI::Button(U"Rect", Vec2{ 40, 40 }, 240))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ Random(-200, 200), -600 }, SizeF{ 40, 40 }, P2Material{ .density = 0.1 });
			}

			if (SimpleGUI::Button(U"Car (low damping)", Vec2{ 40, 80 }, 240))
			{
				cars << CreateCar(world, Vec2{ Random(-700, 200), -600 }, 0.05);
			}

			if (SimpleGUI::Button(U"Car (high damping)", Vec2{ 40, 120 }, 240))
			{
				cars << CreateCar(world, Vec2{ Random(-700, 200), -600 }, 1.0);
			}

			if (SimpleGUI::Button(U"Motor (-500)", Vec2{ 40, 160 }, 240))
			{
				motorSpeed = -500;
			}

			if (SimpleGUI::Button(U"Motor (0)", Vec2{ 40, 200 }, 240))
			{
				motorSpeed = 0;
			}

			if (SimpleGUI::Button(U"Motor (500)", Vec2{ 40, 240 }, 240))
			{
				motorSpeed = 500;
			}

			if (SimpleGUI::Button(U"Reset", Vec2{ 40, 280 }, 240))
			{
				cars.clear();
				bodies.clear();
			}
		}
	}
	```


## 69.21 Mouse Joint
- Mouse joint `P2MouseJoint` is a joint for moving bodies using the mouse position as a target position

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/21.png)

??? memo "Code"
	```cpp hl_lines="21-22 47-64"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		constexpr double StepTime = (1.0 / 200.0);

		double accumulatedTime = 0.0;

		P2World world;

		Array<P2Body> grounds;
		grounds << world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 800, 10 });

		const P2Body box = world.createPolygon(P2Dynamic, Vec2{ 0, -200 },
			LineString{ Vec2{ -100, 0 }, Vec2{ -100, 100 }, Vec2{ 100, 100 }, { Vec2{ 100, 0 }} }.calculateBuffer(4));

		Array<P2Body> bodies;

		// Mouse joint
		P2MouseJoint mouseJoint;

		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		int32 stepCount = 0;

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);

				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });

				// Add circles at regular intervals
				if (++stepCount % 4 == 0)
				{
					bodies << world.createCircle(P2Dynamic, Vec2{ Random(-200, 200), -600 }, 5, P2Material{ .density = 0.1 });
				}
			}

			camera.update();
			{
				const auto t = camera.createTransformer();

				if (MouseL.down())
				{
					// Create mouse joint
					mouseJoint = world.createMouseJoint(box, Cursor::PosF())
						.setMaxForce(box.getMass() * 5000.0)
						.setLinearStiffness(2.0, 0.8);
				}
				else if (MouseL.pressed())
				{
					// Update mouse joint target position
					mouseJoint.setTargetPos(Cursor::PosF());
					Line{ mouseJoint.getAnchorPos(), mouseJoint.getTargetPos() }.draw(LineStyle::SquareDot, 4.0, Palette::Orange);
				}
				else if (MouseL.up())
				{
					// Destroy mouse joint
					mouseJoint.release();
				}

				for (const auto& ground : grounds)
				{
					ground.draw(Palette::Gray);
				}

				box.draw();

				for (const auto& body : bodies)
				{
					body.draw(HSV{ body.id() * 10.0 });
				}
			}

			camera.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Reset", Vec2{ 40, 40 }))
			{
				bodies.clear();
			}
		}
	}
	```


## 69.22 Linking Bodies with Textures
- To represent physics simulation results using textures, there are several methods:
	- Reflect information obtained from `P2Body` in texture drawing
	- Create `Polygon` or `MultiPolygon` following texture shapes and add them as `P2Body`
	- Create `Buffer2D` and use information from `P2Body` to create `Transformer2D` for drawing

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/22.png)

??? memo "Code"
	```cpp hl_lines="8-18 28-30 39 47-60 93-121 126"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Pattern 1: Replace circles with emoji
		const Texture appleTexture{ U"🍎"_emoji };

		// Pattern 2: Use polygons created from emoji
		const Texture penguinTexture{ U"🐧"_emoji };
		const Texture woodTexture{ U"example/texture/wood.jpg", TextureDesc::Mipped };
		const MultiPolygon penguinPolygon = Emoji::CreateImage(U"🐧").alphaToPolygonsCentered().simplified(2.0);

		// Pattern 3: Use Buffer2D
		const Polygon boxPolygon = LineString{ Vec2{ -100, 0 }, Vec2{ -100, 100 }, Vec2{ 100, 100 }, { Vec2{ 100, 0 }} }.calculateBuffer(8);
		const Buffer2D boxObject = boxPolygon.toBuffer2D(Arg::center(0, 50), SizeF{ 200, 200 });

		constexpr double StepTime = (1.0 / 200.0);
		double accumulatedTime = 0.0;

		P2World world;

		Array<P2Body> grounds;
		grounds << world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 800, 10 });

		Array<P2Body> apples;
		Array<P2Body> penguins;
		const P2Body box = world.createPolygon(P2Dynamic, Vec2{ 0, -200 }, boxPolygon);

		// Mouse joint
		P2MouseJoint mouseJoint;

		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		int32 stepCount = 0;

		bool showBodyOutline = true;

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);

				apples.remove_if([](const P2Body& apple) { return (500 < apple.getPos().y); });
				penguins.remove_if([](const P2Body& penguin) { return (500 < penguin.getPos().y); });

				// Add circles at regular intervals
				if (stepCount % 200 == 0)
				{
					apples << world.createCircle(P2Dynamic, Vec2{ Random(-300, -100), -600 }, 30, P2Material{ .density = 0.1 });
				}

				// Add penguins at regular intervals
				if (stepCount % 200 == 100)
				{
					penguins << world.createPolygons(P2Dynamic, Vec2{ Random(100, 300), -600 }, penguinPolygon, P2Material{ .density = 0.1 });
				}

				++stepCount;
			}

			camera.update();
			{
				const auto t = camera.createTransformer();

				if (MouseL.down())
				{
					// Create mouse joint
					mouseJoint = world.createMouseJoint(box, Cursor::PosF())
						.setMaxForce(box.getMass() * 5000.0)
						.setLinearStiffness(2.0, 0.8);
				}
				else if (MouseL.pressed())
				{
					// Update mouse joint target position
					mouseJoint.setTargetPos(Cursor::PosF());
					Line{ mouseJoint.getAnchorPos(), mouseJoint.getTargetPos() }.draw(LineStyle::SquareDot, 4.0, Palette::Orange);
				}
				else if (MouseL.up())
				{
					// Destroy mouse joint
					mouseJoint.release();
				}

				for (const auto& ground : grounds)
				{
					ground.draw(Palette::Gray);
				}

				{
					if (showBodyOutline)
					{
						box.drawFrame(2.0);
					}

					const Transformer2D t{ Mat3x2::Rotate(box.getAngle()).translated(box.getPos()) };
					boxObject.draw(woodTexture);
				}

				for (const auto& apple : apples)
				{
					appleTexture.resized(68).rotated(apple.getAngle()).drawAt(apple.getPos());

					if (showBodyOutline)
					{
						apple.drawFrame(2.0);
					}
				}

				for (const auto& penguin : penguins)
				{
					penguinTexture.rotated(penguin.getAngle()).drawAt(penguin.getPos());

					if (showBodyOutline)
					{
						penguin.drawFrame(2.0);
					}
				}
			}

			camera.draw(Palette::Orange);

			SimpleGUI::CheckBox(showBodyOutline, U"show outline", Vec2{ 40, 40 });

			if (SimpleGUI::Button(U"Reset", Vec2{ 40, 80 }))
			{
				apples.clear();
				penguins.clear();
			}
		}
	}
	```


## 69.23 Collision Filters
- Parts have collision filter `P2Filter`
- You can specify category bit flags that the part belongs to and set it not to interfere with other parts having specific bit flags
- When there are parts A and B, interference occurs only when `((A.maskBits & B.categoryBits) != 0) && ((B.maskBits & A.categoryBits) != 0)`
- By default, parts have `categoryBits = 0x0001` and `maskBits = 0xFFFF`, so all parts interfere with each other
- Additional condition setting via `groupIndex` is also possible, though not covered in the sample code
- The member variables of `P2Filter` are as follows:

| Code | Description |
|---|---|
| `uint16 categoryBits` | Bit flag representing the category this part belongs to |
| `uint16 maskBits` | Bit flag representing categories of other parts this part physically interferes with |
| `int16 groupIndex` | If either of two parts has `groupIndex` of `0`, interference is determined by `categoryBits` and `maskBits`.<br>If both parts have non-`0` `groupIndex` values that are different from each other, interference is determined by `categoryBits` and `maskBits`.<br>If two parts have `groupIndex` of `1` or higher and are equal to each other, they always interfere.<br>If two parts have `groupIndex` of `-1` or lower and are equal to each other, they never interfere |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/23.png)

??? memo "Code"
	```cpp hl_lines="12-19 25-27 48-49 61 66"
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		constexpr double StepTime = (1.0 / 200.0);
		double accumulatedTime = 0.0;

		P2World world;

		// Default collision filter
		constexpr P2Filter WallFilter{ .categoryBits = 0b0000'0000'0000'0001, .maskBits = 0b1111'1111'1111'1111 };

		// Team 1 collision filter (Team 1 members don't interfere with each other)
		constexpr P2Filter Team1Filter{ .categoryBits = 0b0000'0000'0000'0010, .maskBits = 0b0000'0000'0000'0101 };

		// Team 2 collision filter (Team 2 members don't interfere with each other)
		constexpr P2Filter Team2Filter{ .categoryBits = 0b0000'0000'0000'0100, .maskBits = 0b0000'0000'0000'0011 };

		constexpr ColorF Team1Color{ 0.4, 1.0, 0.2 };
		constexpr ColorF Team2Color{ 0.4, 0.2, 1.0 };

		Array<P2Body> grounds;
		grounds << world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 800, 10 });
		grounds << world.createRect(P2Static, Vec2{ -200, -200 }, SizeF{ 300, 10 }, {}, Team1Filter);
		grounds << world.createRect(P2Static, Vec2{ 200, -200 }, SizeF{ 300, 10 }, {}, Team2Filter);

		Array<P2Body> bodies;

		Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

		while (System::Update())
		{
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				world.update(StepTime);

				bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
			}

			camera.update();
			{
				const auto t = camera.createTransformer();

				for (const auto& body : bodies)
				{
					const bool isTeam1 = (body.shape(0).getFilter().categoryBits == Team1Filter.categoryBits);
					body.draw(isTeam1 ? Team1Color : Team2Color);
				}

				grounds[0].draw(Palette::Gray);
				grounds[1].draw(ColorF{ Team1Color, 0.75 });
				grounds[2].draw(ColorF{ Team2Color, 0.75 });
			}

			camera.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Team 1", Vec2{ 40, 40 }, 120))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ Random(-400, 400), -600 }, SizeF{ 40, 40 }, P2Material{ .density = 0.1 }, Team1Filter);
			}

			if (SimpleGUI::Button(U"Team 2", Vec2{ 40, 80 }, 120))
			{
				bodies << world.createRect(P2Dynamic, Vec2{ Random(-400, 400), -600 }, SizeF{ 40, 20 }, P2Material{ .density = 0.1 }, Team2Filter);
			}

			if (SimpleGUI::Button(U"Reset", Vec2{ 40, 120 }, 120))
			{
				bodies.clear();
			}
		}
	}
	```