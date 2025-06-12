# 44. Scene and Window
Learn how to customize Siv3D's scene and window.

## 44.1 Overview of Scene and Window
- In Siv3D, when you `.draw()` shapes, textures, text, etc., they are drawn to a virtual screen called a "scene"
- Then, `System::Update()` transfers the scene image to the window, allowing users to see the drawing results in the window

<div class="noshadow-90"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene/1.png"></div>

- These processes are performed automatically, so until now you've been able to view the results of `.draw()` in the window without being particularly aware of it
- This chapter delves deeper into the mechanisms of scenes and windows

## 44.2 "Three Sizes"
- To properly understand screen display in Siv3D programs, it's important to understand the following "three sizes":
	- ① Scene size
	- ② Virtual window size
	- ③ Actual window size (frame buffer size)

### ① Scene Size
- The size of an independent scene that serves as the basis for drawing with `.draw()` and mouse cursor coordinates with `Cursor::Pos()`
- By default, it's 800 × 600 and matches the "② virtual window size"
- When you take a screenshot using Siv3D's features, it's saved at this resolution
- Functions related to getting scene size are as follows:

| Code | Return Value | Description |
|--|--|--|
| `Scene::Size()` | `Size` | Returns scene size |
| `Scene::Width()` | `int32` | Returns scene width |
| `Scene::Height()` | `int32` | Returns scene height |
| `Scene::Center()` | `Point` | Returns scene center coordinates<br>Same as `Scene::Size() / 2` |
| `Scene::CenterF()` | `Vec2` | Returns scene center coordinates<br>Same as `Scene::Size() / 2.0` |
| `Scene::Rect()` | `Rect` | Returns scene rectangle<br>Same as `Rect{ 0, 0, Scene::Size() }` |

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene::Size(): " << Scene::Size();
		Print << U"Scene::Width(): " << Scene::Width();
		Print << U"Scene::Height(): " << Scene::Height();
		Print << U"Scene::Center(): " << Scene::Center();
		Print << U"Scene::CenterF(): " << Scene::CenterF();
		Print << U"Scene::Rect(): " << Scene::Rect();
	}
}
```

### ② Virtual Window Size
- The apparent size of the window's **client area** (the area where drawing occurs, excluding the title bar and frame) on the user's desktop
- By default, it's 800 × 600
- Can be obtained with `Window::GetState().virtualSize`
- Multiplying this virtual window size by the scaling factor set in the OS (150%, 200%, etc.) gives the "③ actual window size"

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
	}
}
```

!!! warning "Note for Linux version"
	- The current Siv3D Linux version doesn't support OS scaling factors, so ② virtual window size and ③ actual window size are equal

### ③ Actual Window Size (Frame Buffer Size)
- The size of the window's client area measured in actual pixels on the monitor
- By default, it's 800 × 600 multiplied by the OS scaling factor
- Can be obtained with `Window::GetState().frameBufferSize`

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;
	}
}
```

### OS Scaling Factor
- The current OS scaling factor for the monitor displaying the window can be obtained with `Window::GetState().scaling`
- `1.0` is 100%, `1.25` is 125%, `1.5` is 150%, `2.0` is 200%

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;
		Print << U"Scaling: " << Window::GetState().scaling;
	}
}
```

#### Relationship Between the Three Sizes
- Scene drawing results are scaled to fit the actual window size while maintaining aspect ratio and transferred to the window
- This automatically displays content at optimal size for the user's monitor environment
	- For example, in high DPI environments with 200% OS scaling, a scene drawn at 800 × 600 is enlarged to 1600 × 1200 for display, preventing problems like "text is too small to play the game"
- The following table summarizes the relationship between the three sizes at various OS scaling factors:

| OS Scaling Factor | ① Scene Size | ② Virtual Window Size | ③ Actual Window Size |
|--|--|--|--|
| 100% | 800x600 | 800x600 | 800x600 |
| 125% | 800x600 | 800x600 | 1000x750 |
| 150% | 800x600 | 800x600 | 1200x900 |
| 200% | 800x600 | 800x600 | 1600x1200 |

- On the other hand, in high DPI environments, you may want to draw scenes at correspondingly high resolution for high-definition rendering
- For example, when the OS scaling factor is 200%, you might want to draw at 1600 × 1200 to match the actual window size
- To match scene size to actual window size, use resize mode `ResizeMode::Actual` explained in **44.3**


## 44.3 Scene Resize Mode
- The following operations change the actual window size or virtual window size:
	- Moving the window to a different monitor
	- Calling window resize functions (**44.4**)
	- User resizing a resizable window (**44.5**) with the mouse
- With these size changes, the scene size also needs updating
- The scene **resize mode** determines the new scene size when this happens
- There are three resize modes:

| Resize Mode | Description |
|--|--|
|`ResizeMode::Virtual` | Use virtual window size as new scene size (default) |
|`ResizeMode::Actual` | Use actual window size as new scene size |
|`ResizeMode::Keep` | Don't change scene size |

- The current resize mode can be obtained with `Scene::GetResizeMode()`
- Set the resize mode with `Scene::SetResizeMode(ResizeMode)`
	- When set, the scene size is immediately updated to match the new resize mode
- The following sample changes the resize mode to `ResizeMode::Actual` immediately after program startup
- When the OS scaling factor is greater than 100%, the scene size becomes larger than 800 × 600, allowing higher-definition drawing at that size

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Actual);

	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene::Size(): " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;

		// 100 px checkerboard pattern to check scene size
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```


## 44.4 Window Resizing
- You can change the virtual window size with `Window::Resize(width, height)` or `Window::Resize(size)`
- With the virtual window size change, the actual window size changes and the scene size is updated according to the resize mode
- When using the default resize mode `ResizeMode::Virtual`, the new virtual window size specified by `Window::Resize()` becomes the new scene size, so it's not too complicated

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1000, 600);

	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;
		
		// 100 px checkerboard pattern to check scene size
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```

- When resize mode `ResizeMode::Keep` is set, the scene size doesn't change even with `Window::Resize()`
- When the aspect ratios of scene size and actual window size differ, **letterboxes** (margin areas) appear on the left/right or top/bottom of the client area

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Keep);
	Window::Resize(1000, 600);

	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;

		// 100 px checkerboard pattern to check scene size
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```


## 44.5 Manual Resizing
- To allow manual window resizing, set `Window::SetStyle(WindowStyle::Sizable)`
- When actual window size and virtual window size are changed by user operations, the scene size is also updated according to the resize mode
- When using the default resize mode `ResizeMode::Virtual`, the new virtual window size becomes the new scene size

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene/5-1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::SetStyle(WindowStyle::Sizable);

	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;

		// 100 px checkerboard pattern to check scene size
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```

- When resize mode `ResizeMode::Keep` is set, the scene size doesn't change even with manual window resizing
- When the aspect ratios of scene size and actual window size differ, letterboxes appear on the left/right or top/bottom of the client area

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene/5-2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Keep);
	Window::SetStyle(WindowStyle::Sizable);

	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;

		// 100 px checkerboard pattern to check scene size
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```


## 44.6 Changing Letterbox Color
- To change the letterbox color, specify a color with `Scene::SetLetterbox(color)`
- Like `Scene::SetBackground(color)`, once you change the letterbox color, it remains the same until changed again

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Change letterbox color
	Scene::SetLetterbox(ColorF{ 0.8, 0.9, 1.0 });

	Scene::SetResizeMode(ResizeMode::Keep);
	Window::SetStyle(WindowStyle::Sizable);

	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;

		// 100 px checkerboard pattern to check scene size
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```


## 44.7 Changing Only Scene Size
- When resize mode is set to `ResizeMode::Keep`, the scene size is not changed by window operations
- Instead, use `Scene::Resize(width, height)` or `Scene::Resize(size)` to change the scene size
- When scene and actual window sizes differ, use `Cursor::PosF()` instead of `Cursor::Pos()` to get mouse cursor coordinates as the more informative `Vec2` type

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Keep);

	// Resize scene to 1600x1200
	Scene::Resize(1600, 1200);

	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;

		// Get mouse cursor coordinates as Vec2 type
		Print << Cursor::PosF();

		// 100 px checkerboard pattern to check scene size
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```


## 44.8 Scene Scaling Filter
- When transferring a scene to the window, if the scene size and actual window size differ, the scene image is scaled to fit the client area while maintaining aspect ratio
- There are two texture filter options for scaling, which can be changed with `Scene::SetTextureFilter(texture filter)`
- By default, bilinear interpolation `TextureFilter::Linear` is used
	- This is an interpolation method that smoothly filters and scales images
- On the other hand, scaling low-resolution scenes with nearest-neighbor interpolation `TextureFilter::Nearest` filter can maintain the pixel feeling without filtering

| Texture Filter | Description |
|--|--|
|`TextureFilter::Linear`| Bilinear interpolation of images (default) |
|`TextureFilter::Nearest`| Nearest-neighbor interpolation of images |

=== "Bilinear (Default)"
	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene/8-1.png)


	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetResizeMode(ResizeMode::Keep);

		// Set scene size to 200x150
		Scene::Resize(200, 150);

		const Texture texture{ U"🐈"_emoji };

		while (System::Update())
		{
			Circle{ 120, 75, 50 }.draw();

			texture.draw();
		}
	}
	```


=== "Nearest-neighbor"
	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene/8-2.png)


	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetResizeMode(ResizeMode::Keep);

		// Set scene size to 200x150
		Scene::Resize(200, 150);

		// Set scene transfer scaling method to nearest-neighbor
		Scene::SetTextureFilter(TextureFilter::Nearest);

		const Texture texture{ U"🐈"_emoji };

		while (System::Update())
		{
			Circle{ 120, 75, 50 }.draw();

			texture.draw();
		}
	}
	```


## 44.9 Hiding Window Frame
- To hide the window frame, set `WindowStyle::Frameless` with `Window::SetStyle()`
- Manual window movement, resizing, and closing operations become unavailable

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Hide window frame
	Window::SetStyle(WindowStyle::Frameless);

	while (System::Update())
	{
		Circle{ Cursor::Pos(), 100 }.draw();
	}
}
```


## 44.10 Changing Title
- To change the window title, pass a string or value to `Window::SetTitle()`
- During debug builds, in addition to the title, information like `(Debug Build)`, frame rate, window size, and scene size is also displayed

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Change window title
	Window::SetTitle(U"My Game");

	while (System::Update())
	{

	}
}
```

!!! warning "Don't change title frequently during execution"
	- Changing the window title is a time-consuming operation
	- Avoid setting different titles with `Window::SetTitle()` every frame
	- If you pass the same title that's already set to `Window::SetTitle()`, nothing happens so no cost is incurred


## 44.11 Moving Window (1)
- `Window::Centering()` moves the window to the center of the current monitor's work area

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Center", Vec2{ 20, 20 }))
		{
			// Move window to center
			Window::Centering();
		}
	}
}
```


## 44.12 Moving Window (2)
- `Window::SetPos(x, y)` or `Window::SetPos(pos)` moves the window to a specified position
- The current window position can be obtained with `Window::GetPos()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// Display window position on screen
		Print << Window::GetPos();

		if (SimpleGUI::Button(U"(0, 0)", Vec2{ 200, 20 }))
		{
			// Move window to screen (0, 0)
			Window::SetPos(0, 0);
		}

		if (SimpleGUI::Button(U"(200, 200)", Vec2{ 300, 20 }))
		{
			// Move window to screen (200, 200)
			Window::SetPos(200, 200);
		}
	}
}
```


## 44.13 Minimize and Maximize
- To minimize a window programmatically, call `Window::Minimize()`
- To maximize, call `Window::Maximize()`
- To maximize a window, the window style must be `WindowStyle::Sizable`
- To restore a minimized/maximized window to its previous size, call `Window::Restore()`
- Whether a window is minimized can be obtained with `Window::GetState().minimized`
- Whether it's maximized can be obtained with `Window::GetState().maximized`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Enable window maximization
	Window::SetStyle(WindowStyle::Sizable);

	// Variable to count during minimization
	int32 count = 0;

	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;
		Print << U"Minimized Frame Count: " << count;
		Print << U"Maximized: " << Window::GetState().maximized;

		if (Window::GetState().minimized)
		{
			++count;
		}

		// 100 px checkerboard pattern to check scene size
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}

		if (SimpleGUI::Button(U"Minimize", Vec2{ 300, 40 }))
		{
			// Minimize window
			Window::Minimize();
		}

		if (SimpleGUI::Button(U"Maximize", Vec2{ 300, 80 }))
		{
			// Maximize window
			Window::Maximize();
		}

		if (SimpleGUI::Button(U"Restore", Vec2{ 300, 120 }))
		{
			// Restore minimized/maximized window to original size
			Window::Restore();
		}
	}
}
```


## 44.14 Monitor Information
- To get a list of information about connected monitors, use `System::EnumerateMonitors()`
- The result is obtained as `Array<MonitorInfo>` type
- The member variables of `MonitorInfo` type are as follows:

| Code | Description |
|--|--|
| `String name` | Display name |
| `String id` | Display ID |
| `String displayDeviceName` | Internal display name |
| `Rect displayRect` | Position and size of entire display |
| `Rect workArea` | Position and size of available area excluding taskbar, etc. |
| `Size fullscreenResolution` | Resolution in fullscreen mode |
| `bool isPrimary` | `true` if main display, `false` otherwise |
| `Optional<Size> sizeMillimeter` | Physical size (mm), `none` if unavailable |
| `Optional<double> scaling` | UI scaling factor. `none` if unavailable |
| `Optional<double> refreshRate` | Refresh rate. `none` if unavailable |

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Get list of connected monitor information
	const Array<MonitorInfo> monitors = System::EnumerateMonitors();

	for (const auto& monitor : monitors)
	{
		Print << U"name: " << monitor.name;
		Print << U"displayRect: " << monitor.displayRect << U" workArea: " << monitor.workArea;
		Print << U"fullscreenResolution: " << monitor.fullscreenResolution << U" sizeMillimeter: " << monitor.sizeMillimeter;
		Print << U"scaling: " << monitor.scaling << U" refreshRate: " << monitor.refreshRate;
		Print << U"isPrimary: " << monitor.isPrimary;
		Print << U"-----";
	}

	while (System::Update())
	{

	}
}
```

- To get the **monitor index** where the current program's window exists, use `System::GetCurrentMonitorIndex()`
- This index corresponds to the array returned by `System::EnumerateMonitors()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << System::GetCurrentMonitorIndex();
	}
}
```


## 44.15 Fullscreen Mode
- To put the application in **fullscreen mode**, call `Window::SetFullscreen(true)`
- To return to windowed mode, call `Window::SetFullscreen(false)`
- The second argument of `Window::SetFullscreen(true)` can specify the monitor index for fullscreen display
- Whether the application is in fullscreen mode can be obtained with `Window::GetState().fullscreen`

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"scene size: " << Scene::Size();
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;
		Print << U"fullscreen: " << Window::GetState().fullscreen;

		// 100px checkerboard pattern
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ x * 100, y * 100, 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}

		if (Window::GetState().fullscreen)
		{
			if (SimpleGUI::Button(U"Window mode", Vec2{ 300, 20 }))
			{
				// Switch to windowed mode
				Window::SetFullscreen(false);
			}
		}
		else
		{
			if (SimpleGUI::Button(U"Fullscreen mode", Vec2{ 300, 20 }))
			{
				// Switch to fullscreen mode
				Window::SetFullscreen(true);
			}
		}
	}
}
```


## 44.16 Full-screen Mode (Windows)
- On Windows, you can enter **full-screen mode** by pressing ++alt+enter++ while the application is running (**Tutorial 5.5**)
- The behavior is similar to fullscreen mode, but the scene resize mode is set to `Resize::Keep` and the scene size doesn't change
- This is useful when you want to run applications that don't support fullscreen mode or resolution changes in full-screen with large display
- To disable this key operation, call `Window::SetToggleFullscreenEnabled(false)`