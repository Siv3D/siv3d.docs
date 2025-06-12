# 49. 2D Coordinate Transformation and Camera
Learn how to apply coordinate transformations such as translation, scaling, and rotation to drawing coordinates and mouse cursor coordinates.

## 49.1 Applying Offset to Drawing Coordinates (Vec2 Addition)
- When drawing multiple shapes or textures that form a composite, you may want to move them all together
- A primitive approach is to add a translation amount when specifying coordinates with `Point` or `Vec2`
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2d/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	while (System::Update())
	{
		const Vec2 offset = Cursor::Pos();

		for (int32 y = 0; y < 6; ++y)
		{
			for (int32 x = 0; x < 6; ++x)
			{
				if (IsEven(x + y))
				{
					RectF{ (x * 40), (y * 40), 40 }.movedBy(offset).draw();
				}
			}
		}

		emoji.drawAt(Vec2{ 120, 100 } + offset);

		font(U"APPLE").drawAt(50, (Vec2{ 120, 200 } + offset), ColorF{ 0.1 });
	}
}
```


## 49.2 Applying Offset to Drawing Coordinates (Transformer2D)
- A more convenient method than **49.1** is to use `Transformer2D`
- `Transformer2D` allows you to apply coordinate transformations (affine transformations) such as translation, scaling, and rotation to drawing and mouse cursor coordinates all at once
- Express the translation of drawing coordinates with `Mat3x2::Translate(x, y)` or `Mat3x2::Translate(Vec2{ x, y })` and pass it to the `Transformer2D` constructor
- While a `Transformer2D` object is active, the coordinate transformation is applied to 2D drawing
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2d/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	while (System::Update())
	{
		{
			const Vec2 offset = Cursor::Pos();
			const Transformer2D t{ Mat3x2::Translate(offset) };

			for (int32 y = 0; y < 6; ++y)
			{
				for (int32 x = 0; x < 6; ++x)
				{
					if (IsEven(x + y))
					{
						RectF{ (x * 40), (y * 40), 40 }.draw();
					}
				}
			}

			emoji.drawAt(Vec2{ 120, 100 });

			font(U"APPLE").drawAt(50, Vec2{ 120, 200 }, ColorF{ 0.1 });
		}

		// Not affected outside the scope of Transformer2D
		emoji.drawAt(600, 400);
	}
}
```


## 49.3 Scaling Drawing Coordinates
- Use `Mat3x2::Scale(x, y, center)` or `Mat3x2::Scale(Vec2{ x, y }, center)` to scale drawing coordinates
- `center` is the center point of scaling. If omitted, `Vec2{ 0, 0 }` is used
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2d/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	while (System::Update())
	{
		{
			const double scale = (1.0 + Periodic::Sine0_1(4s));
			const Transformer2D t{ Mat3x2::Scale(scale) };

			for (int32 y = 0; y < 6; ++y)
			{
				for (int32 x = 0; x < 6; ++x)
				{
					if (IsEven(x + y))
					{
						RectF{ (x * 40), (y * 40), 40 }.draw();
					}
				}
			}

			emoji.drawAt(Vec2{ 120, 100 });

			font(U"APPLE").drawAt(50, Vec2{ 120, 200 }, ColorF{ 0.1 });
		}

		// Not affected outside the scope of Transformer2D
		emoji.drawAt(600, 400);
	}
}
```


## 49.4 Rotating Drawing Coordinates
- Use `Mat3x2::Rotate(angle, center)` to rotate drawing coordinates
- `angle` is the clockwise rotation angle (in radians)
- `center` is the center point of rotation. If omitted, `Vec2{ 0, 0 }` is used
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2d/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	while (System::Update())
	{
		{
			const double angle = (Scene::Time() * 30_deg);
			const Transformer2D t{ Mat3x2::Rotate(angle, Vec2{ 120, 120 }) };

			for (int32 y = 0; y < 6; ++y)
			{
				for (int32 x = 0; x < 6; ++x)
				{
					if (IsEven(x + y))
					{
						RectF{ (x * 40), (y * 40), 40 }.draw();
					}
				}
			}

			emoji.drawAt(Vec2{ 120, 100 });

			font(U"APPLE").drawAt(50, Vec2{ 120, 200 }, ColorF{ 0.1 });
		}

		// Not affected outside the scope of Transformer2D
		emoji.drawAt(600, 400);
	}
}
```


## 49.5 Matrix Multiplication for Coordinate Transformation
- `Mat3x2` can multiply coordinate transformations using the following member functions:
- This allows you to combine rotation, scaling, and translation into a single matrix

| Code | Description |
|--|--|
|`.translated(x, y)`| Translation |
|`.translated(Vec2{ x, y })`| Translation |
|`.scaled(x, y, center)`| Scaling |
|`.scaled(Vec2{ x, y }, center)`| Scaling |
|`.rotated(angle, center)`| Rotation |

- If `center` is omitted, `Vec2{ 0, 0 }` is used

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2d/5.png)

```cpp
# include <Siv3D.hpp>

void Draw(const Font& font, const Texture& emoji)
{
	for (int32 y = -3; y < 3; ++y)
	{
		for (int32 x = -3; x < 3; ++x)
		{
			if (IsEven(x + y))
			{
				RectF{ (x * 40), (y * 40), 40 }.draw();
			}
		}
	}

	emoji.drawAt(Vec2{ 0, -20 });

	font(U"APPLE").drawAt(50, Vec2{ 0, 80 }, ColorF{ 0.1 });
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	while (System::Update())
	{
		{
			const Mat3x2 mat = Mat3x2::Scale(0.5 + Periodic::Sine0_1(4s))
				.translated(200, 160);
			const Transformer2D t{ mat };
			Draw(font, emoji);
		}

		{
			const Mat3x2 mat = Mat3x2::Rotate(Scene::Time() * 30_deg)
				.translated(600, 160);
			const Transformer2D t{ mat };
			Draw(font, emoji);
		}

		{
			const Mat3x2 mat = Mat3x2::Rotate(Scene::Time() * 30_deg)
				.scaled(0.5 + Periodic::Sine0_1(4s))
				.translated(Cursor::Pos());
			const Transformer2D t{ mat };
			Draw(font, emoji);
		}
	}
}
```


## 49.6 Stacking Transformer2D
- When a new `Transformer2D` is activated while another `Transformer2D` effect is active, the coordinate transformations are multiplied
- The following code achieves complex motion through matrix multiplication
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2d/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		const double t = (Scene::Time() * -30_deg);

		{
			const Transformer2D t0{ Mat3x2::Translate(400, 300) };
			Circle{ 0, 0, 40 }.draw(Palette::Orangered);
			Circle{ 0, 0, 160 }.drawFrame(2);

			{
				const Transformer2D t1{ Mat3x2::Translate(160, 0).rotated(t) };
				Circle{ 0, 0, 20 }.draw(Palette::Seagreen);
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


## 49.7 Coordinate Transformation of Mouse Cursor
- Pass `TransformCursor::Yes` as the second argument to the `Transformer2D` constructor to apply coordinate transformation to the mouse cursor as well
- This is convenient when applying coordinate transformation to UI elements
- The following sample code determines whether the mouse cursor is over each item in the rotated, scaled, and translated coordinate system

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2d/7.png)

```cpp
# include <Siv3D.hpp>

void Draw(const Font& font, const Texture& emoji)
{
	const Rect region{ -120, -120, 240 };

	for (int32 y = -3; y < 3; ++y)
	{
		for (int32 x = -3; x < 3; ++x)
		{
			if (IsEven(x + y))
			{
				RectF{ (x * 40), (y * 40), 40 }.draw();
			}
		}
	}

	emoji.drawAt(Vec2{ 0, -20 });

	font(U"APPLE").drawAt(50, Vec2{ 0, 80 }, ColorF{ 0.1 });

	if (region.mouseOver())
	{
		region.drawFrame(0, 6, Palette::Seagreen);
		Cursor::RequestStyle(CursorStyle::Hand);
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	while (System::Update())
	{
		{
			const Mat3x2 mat = Mat3x2::Scale(0.5 + Periodic::Sine0_1(4s))
				.translated(200, 300);
			const Transformer2D t{ mat, TransformCursor::Yes };
			Draw(font, emoji);
		}

		{
			const Mat3x2 mat = Mat3x2::Rotate(Scene::Time() * 30_deg)
				.translated(600, 300);
			const Transformer2D t{ mat, TransformCursor::Yes };
			Draw(font, emoji);
		}
	}
}
```


## 49.8 Mouse Cursor Only Coordinate Transformation
- There are cases where you only want to transform the mouse cursor coordinates without transforming the drawing coordinates, such as when creating a mini-window using a viewport
- In such cases, set `Mat3x2::Identity()` (identity matrix that doesn't change anything) as the first argument of `Transformer2D`, and set the coordinate transformation matrix for the mouse cursor as the second argument

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2d/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		const Point topLeft = Vec2{ (Periodic::Sine0_1(8s) * 400), (Periodic::Sine0_1(6s) * 300) }.asPoint();
		const Rect viewportRect{ topLeft, 360, 240};

		{
			const ScopedViewport2D viewport{ viewportRect };

			// Translate only mouse cursor coordinates
			const Transformer2D t{ Mat3x2::Identity(), Mat3x2::Translate(topLeft) };

			Circle{ 200, 150, 200 }.draw();
			Circle{ Cursor::PosF(), 40 }.draw(Palette::Orange);

			if (SimpleGUI::Button(U"Button", Vec2{ 20, 20 }))
			{
				Print << U"Pushed";
			}
		}

		viewportRect.drawFrame(0, 2, Palette::Seagreen);
	}
}
```


## 49.9 2D Camera
- `Camera2D` allows you to create and control `Transformer2D` with intuitive mouse and keyboard operations
- `Camera2D::update()` allows movement with ++w++ / ++a++ / ++s++ / ++d++ keys for up/down/left/right, ++up++ / ++down++ keys for zoom in/out, right-click mouse for free movement, and mouse wheel for zoom in/out
- If you want to disable keyboard operations, pass `CameraControl::Mouse` to the `Camera2D` constructor
- To disable both keyboard and mouse operations, pass `CameraControl::None_`
- You can customize the camera behavior with `Camera2DParameters`
- The main member functions of `Camera2D` are:

| Code | Description |
|--|--|
|`.createTransformer()`| Create a `Transformer2D` from the current camera settings |
|`.setTargetCenter(Vec2)`| Set the target center coordinate of the camera |
|`.setTargetScale(double)`| Set the target zoom scale of the camera |
|`.jumpTo(Vec2, double)`| Immediately change the camera center coordinate and zoom scale |
|`.update()` | Handle camera operations and movement to target values |
|`.draw(const ColorF&)`| Display arrow UI to assist with mouse camera operations |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2d/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture emoji{ U"🍎"_emoji };

	// 2D camera
	// Initial settings: center (0, 0), zoom scale 1.0
	Camera2D camera{ Vec2{ 0, 0 }, 1.0 };
	//Camera2D camera{ Vec2{ 0, 0 }, 1.0, CameraControl::Mouse }; // For mouse control only

	while (System::Update())
	{
		// Update the 2D camera
		camera.update();
		{
			// Create Transformer2D from the 2D camera settings
			const auto t = camera.createTransformer();

			for (int32 i = 0; i < 8; ++i)
			{
				Circle{ 0, 0, (50 + i * 50) }.drawFrame(2);
			}

			emoji.drawAt(0, 0);
			Shape2D::Star(100, Vec2{ 200, 200 }).draw(Palette::Seagreen);
			font(U"Siv3D").drawAt(50, Vec2{ -200, -100 }, ColorF{ 0.1 });
		}

		if (SimpleGUI::Button(U"Jump to center", Vec2{ 40, 40 }, 200))
		{
			// Immediately change center and zoom scale
			camera.jumpTo(Vec2{ 0, 0 }, 1.0);
		}

		if (SimpleGUI::Button(U"Move to center", Vec2{ 40, 80 }, 200))
		{
			// Set target center and zoom scale, changing gradually over time
			camera.setTargetCenter(Vec2{ 0, 0 });
			camera.setTargetScale(1.0);
		}

		// Display the 2D camera control UI
		camera.draw(Palette::Orange);
	}
}
```


## 49.10 2D Camera Control
- A 2D camera set with `CameraControl::None_` is controlled programmatically
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2d/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture playerTexture{ U"🚙"_emoji };
	const Texture treeTexture{ U"🌳"_emoji };

	// Player's X position
	double playerPosX = 400;

	// Trees' X positions
	Array<double> trees = { 100, 300, 500, 700, 900 };

	// Camera centered at (400, 300), scale 1.0, controlled by program (not mouse/keyboard)
	Camera2D camera{ Vec2{ 400, 300 }, 1.0, CameraControl::None_ };

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		// Camera's X position
		const double cameraPosX = camera.getCenter().x;

		ClearPrint();
		Print << U"playerPosX: {:.1f}"_fmt(playerPosX);
		Print << U"cameraPosX: {:.1f}"_fmt(cameraPosX);

		// Move with left/right keys
		if (KeyLeft.pressed())
		{
			playerPosX -= (200 * deltaTime);
		}
		else if (KeyRight.pressed())
		{
			playerPosX += (200 * deltaTime);
		}

		// Set camera target center position
		camera.setTargetCenter(Vec2{ playerPosX, 300 });

		// Update camera
		camera.update();
		{
			// Apply camera coordinate transformation
			const auto tr = camera.createTransformer();

			for (const auto& tree : trees)
			{
				// Only draw objects within 500 pixels of camera center X (don't draw off-screen objects)
				if (AbsDiff(cameraPosX, tree) < 500.0)
				{
					treeTexture.drawAt(tree, 400);
				}
			}

			playerTexture.drawAt(playerPosX, 410);
		}
	}
}
```

- If you want to provide a larger view ahead when the car moves forward and a larger view behind when reversing, the following improvement can be made:

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture playerTexture{ U"🚙"_emoji };
	const Texture treeTexture{ U"🌳"_emoji };

	// Player's X position
	double playerPosX = 400;

	// Trees' X positions
	Array<double> trees = { 100, 300, 500, 700, 900 };

	// Camera centered at (400, 300), scale 1.0, controlled by program (not mouse/keyboard)
	Camera2D camera{ Vec2{ 400, 300 }, 1.0, CameraControl::None_ };

	double cameraCenterOffset = 0.0;
	double cameraCenterOffsetVelocity = 0.0;

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		// Camera's X position
		const double cameraPosX = camera.getCenter().x;

		ClearPrint();
		Print << U"playerPosX: {:.1f}"_fmt(playerPosX);
		Print << U"cameraPosX: {:.1f}"_fmt(cameraPosX);

		// Move with left/right keys
		if (KeyLeft.pressed())
		{
			playerPosX -= (200 * deltaTime);
			cameraCenterOffset = Math::SmoothDamp(cameraCenterOffset, -150.0, cameraCenterOffsetVelocity, 0.8);
		}
		else if (KeyRight.pressed())
		{
			playerPosX += (200 * deltaTime);
			cameraCenterOffset = Math::SmoothDamp(cameraCenterOffset, 150.0, cameraCenterOffsetVelocity, 0.8);
		}

		// Set camera target center position
		camera.setTargetCenter(Vec2{ (playerPosX + cameraCenterOffset), 300 });

		// Update camera
		camera.update();
		{
			// Apply camera coordinate transformation
			const auto tr = camera.createTransformer();

			for (const auto& tree : trees)
			{
				// Only draw objects within 500 pixels of camera center X (don't draw off-screen objects)
				if (AbsDiff(cameraPosX, tree) < 500.0)
				{
					treeTexture.drawAt(tree, 400);
				}
			}

			playerTexture.drawAt(playerPosX, 410);
		}
	}
}
```


## 49.11 High Resolution and High Definition Scene
- Using `Transformer2D`, you can easily make games and apps developed at low resolution high resolution and high definition

!!! warning "Notes when applying this method"
	- When applying this method, remove functions like `Scene::Width()`, `Scene::Height()`, `Scene::Size()`, `Scene::Rect()`, `Scene::Center()` from your existing code
	- These functions automatically return larger resolution values due to scene resizing, which is incompatible with this method

- To draw the scene in a larger resolution window, you can use `Transformer2D` to scale up and move the drawing and mouse coordinates
- To display the scene dot-by-dot ignoring OS scaling settings, set the scene resize mode to `ResizeMode::Actual` (**Tutorial 44**)
	- With the default `ResizeMode::Virtual`, for example, on a 4K resolution, 150% scaled laptop, the scene resolution when fullscreen is 2560x1440, while with `ResizeMode::Actual` it becomes 3840x2160. Note that larger scene resolution increases drawing load
- In the following sample, the drawing and input processing of a game developed for 800 x 600 resolution supports resolution changes without modifying the game code (`Game()` function)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/camera2d/11.png)

```cpp
# include <Siv3D.hpp>

// Function to calculate how much to scale the original scene
double CalculateScale(const Vec2& baseSize, const Vec2& currentSize)
{
	return Min((currentSize.x / baseSize.x), (currentSize.y / baseSize.y));
}

// Function to calculate offset for centering on screen
Vec2 CalculateOffset(const Vec2& baseSize, const Vec2& currentSize)
{
	return ((currentSize - baseSize * CalculateScale(baseSize, currentSize)) / 2.0);
}

void Game(const Size& baseSize, const Font& font)
{
	Rect{ baseSize }.draw(ColorF{ 0.15, 0.6, 0.4 });
	Rect{ 40, 100, 400, 400 }.rounded(15).drawFrame(5);

	const Circle circle{ 600, 260, 100 };
	circle.draw(circle.mouseOver() ? ColorF{ 1.0 } : ColorF{ 0.8 });

	if (circle.mouseOver())
	{
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	font(U"Hello, Siv3D").drawAt(40, Vec2{ 600, 120 });

	for (int32 i = 0; i < 8; ++i)
	{
		font(i + 1).drawAt(20, Vec2{ 20, (125 + 50 * i) }, ColorF{ 0.1 });
		font(char32{ U'a' + i }).drawAt(20, Vec2{ (65 + 50 * i), 80 }, ColorF{ 0.1 });
	}
}

void Main()
{
	// Original scene resolution
	const Size BaseSceneSize{ 800, 600 };
	Scene::Resize(BaseSceneSize);

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	constexpr Rect MenuRect{ 0, 0, 700, 32 };
	constexpr Rect WindowModeButton{ 300, 0, 200, 32 };
	constexpr Rect DotByDotButton{ 500, 0, 200, 32 };

	while (System::Update())
	{
		// Calculate scene scale factor
		const double scale = CalculateScale(BaseSceneSize, Scene::Size());
		const Vec2 offset = CalculateOffset(BaseSceneSize, Scene::Size());

		ClearPrint();
		Print << U"Original scene resolution: " << BaseSceneSize;
		Print << U"Current scene resolution: " << Scene::Size();
		Print << U"Scene scale factor = " << scale;
		Print << U"Offset = " << offset;

		{
			// Apply scaling to draw() and mouse coordinates
			const Transformer2D screenScaling{ Mat3x2::Scale(scale).translated(offset), TransformCursor::Yes };

			Game(BaseSceneSize, font);

			{
				MenuRect.draw(ColorF{ 0.75 });

				// Window ⇔ Fullscreen button
				{
					if (WindowModeButton.mouseOver())
					{
						WindowModeButton.draw(ColorF{ 0.85 });
						Cursor::RequestStyle(CursorStyle::Hand);

						if (WindowModeButton.leftClicked())
						{
							// Toggle Window ⇔ Fullscreen
							Window::SetFullscreen(not Window::GetState().fullscreen);
						}
					}
					WindowModeButton.drawFrame(2);
					font(Window::GetState().fullscreen ? U"Switch to Window" : U"Switch to Fullscreen").drawAt(16, WindowModeButton.center(), ColorF{ 0.25 });
				}

				// Dot by Dot button
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
					font((Scene::GetResizeMode() == ResizeMode::Actual) ? U"Match OS Scale" : U"Switch to Dot by Dot").drawAt(16, DotByDotButton.center(), ColorF{ 0.25 });
				}
			}
		}
	}
}
```