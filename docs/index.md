
```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));

	const Font font(60);

	const Texture cat(Emoji(U"🐈"));

	Vec2 catPos(650, 450);

	while (System::Update())
	{
		font(U"Hello, Siv3D!🐣").drawAt(Scene::Center(), Palette::Black);

		font(Cursor::Pos()).draw(20, 500, ColorF(0.6));

		cat.resized(80).drawAt(catPos);

		Circle(Cursor::Pos(), 40).draw(ColorF(1, 0, 0, 0.5));

		if (KeyA.down())
		{
			Print << U"Hello!";
		}

		if (SimpleGUI::Button(U"Move the cat", Vec2(600, 20)))
		{
			catPos = RandomVec2(Scene::Rect());
		}
	}
}
```

# Getting started
## Requirements
### Windows
- Windows 7 SP1 / 8.1 / 10 (64-bit)
- **Visual Studio 2019 version 16.1**
    - Install **Desktop development with C++** from the Visual Studio Installer

### macOS
- macOS High Sierra v10.13 or newer
- Xcode 10.1 or newer

## Installing OpenSiv3D SDK
### Windows
1. Download **[OpenSiv3D Installer for Windows Desktop](#)** and run the installer.

!!! note
    Use the Control Panel to uninstall OpenSiv3D SDK.

### macOS
1. Download **[OpenSiv3D Project Templates for macOS](#)** and extract its contents.


## Building an OpenSiv3D Application
### Windows
1. Lanuch Visual Studio 2019 and open a **New Project Dialog** by clicking **Create a new project**.
2. Select **OpenSiv3D(X.X.X)** project and then click **Next**.
3. Type a name for the project.
4. Click **OK** to create the project.
5. On the **Build** menu, click **Build Solution**.
6. On the **Debug** menu, click **Start Debugging**.

### macOS
1. Open the project file (siv3d_vX.X.X_macOS/examples/empty/empty.xcodeproj) in Xcode.
2. Click **Run button ▶️** to build and run the application.
