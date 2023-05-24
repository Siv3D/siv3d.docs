# Siv3D: A C++ Framework for Creative Coding
<div class="noshadow-76"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/logo/logo.png"></div>

**Siv3D** is a framework for developing games and apps with **fun and easy C++ code**, distributed under the MIT license and running on Windows, macOS, Linux, and Web.

#### Download Siv3D | v0.6.10

[Windows :material-microsoft-windows:](download/windows){ .md-button .md-button--primary }[macOS :material-apple:](download/macos){ .md-button .md-button--primary }[Ubuntu :material-ubuntu:](download/ubuntu){ .md-button .md-button--primary }

#### Download Siv3D for Web (experimental) | v0.6.6

[for Web (Windows + Visual Studio) :material-microsoft-visual-studio:](download/web-vs){ .md-button .md-button--primary }[for Web (Visual Studio Code) :material-microsoft-visual-studio-code:](download/web-vscode){ .md-button .md-button--primary }

## Powerful features to streamline game and app development
With Siv3D, you can combine a rich set of classes and functions to efficiently develop applications such as 2D / 3D games, media art, visualizers, and simulators with short code.

- 2D / 3D graphics (Shapes, images, text, icons, videos, 3D models, etc.)
- Audio (background music, sound effects, text-to-speech, audio filters, etc.)
- Input devices (Mouse, keyboard, webcam, microphone, gamepad, etc.)
- Window, filesystem, networking
- Image processing, sound processing, physics, path finding, geometry, and other calculations

[Learn more](./features/){ .md-button }


## The best way to complete your application work using only C++
Program your app using a combination of standard C++ syntax and cleverly designed Siv3D convenience types and functions. With the following concise code, the world begins to move.

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 }); // Set the background color
	const Texture food{ U"🍿"_emoji }; // Create a texture from an emoji
	const Texture chick{ U"🐥"_emoji };	// Create a texture from an emoji

	while (System::Update()) // Main loop
	{
		Circle{ Scene::Center(), 100 }.draw(); // Draw a circle in the center of the scene
		food.drawAt(Scene::Center()); // Draw the texture in the center of the scene
		chick.drawAt(Cursor::Pos()); // Draw the texture at the mouse cursor position
	}
}
```

??? summary "See the result"
	<div class="full"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/demo/chick.gif"></div>


## 7 Reasons to Use Siv3D

??? success "1. Extremely Short Code"
	The code required to use Siv3D is as short as 2 lines. With the convenient functions and classes for rendering and input/output, most of your application can be completed in just one .cpp file. Share and save your ideas on code sharing sites such as [GitHub :material-open-in-new:](https://github.com/) and [GitHub Gist :material-open-in-new:](https://gist.github.com/), and exchange and learn with Siv3D users around the world.

??? success "2. Learn the Latest C++"
	The samples and library API of Siv3D are written in the latest C++20 style, so simply by using Siv3D, you will naturally learn modern C++ writing techniques. The author of Siv3D is actively promoting the spread of cutting-edge C++ by giving lectures on the latest C++ at CEDEC, the largest game development conference in Japan, and creating a [C++ information portal :material-open-in-new:](https://cppmap.github.io/).

??? success "3. Small Learning, Great Power"
	Although Siv3D is a large engine comprised of 2,200 files of source code and 90 third-party software, users can freely handle its powerful functions by just learning the easy-to-use and consistent Siv3D API. Reduce the cost of learning and concentrate on developing your desired application.

??? success "4. Open Source"
	Siv3D is developed on [GitHub :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D) under the MIT license, so you can always check or modify the internal code. There are no conditions that hinder commercial use, including third-party libraries. The developer can earn 100% of the revenue from the developed game or application.

??? success "5. Lightweight and Quick Start"
	The OpenSiv3D SDK installer to start Siv3D programming is only 120 MB (Windows version). The installation is completed with just a few clicks, and when you launch Visual Studio, a Siv3D project item will be added to the menu, allowing you to start programming immediately.

??? success "6. Friendly Community"
	If you are having trouble with Siv3D, the friendly community will be happy to help you. Ask questions on the official [Siv3D Discord channel :material-open-in-new:](https://discord.gg/mzevvsY). Get advice from experienced Siv3D users and developers and make the most of Siv3D.

??? success "7. Running in a Web Browser"
	The Web version ([OpenSiv3D for Web :material-open-in-new:](https://siv3d.kamenokosoft.com/docs/en/)) which is currently provided on a trial basis, allows you to convert C++ applications created with Siv3D into web applications that run in a web browser. Since it also works on smartphones and tablets, you can reach even more people with your creations.

## Corporate Sponsor
<div class="sponsor"><a href="https://www.bandainamcostudios.com/" target="_blank"><img src="https://siv3d.jp/sponsors/バンダイナムコスタジオ.png" alt="Bandai Namco Studios Inc."></a></div>

## Individual Sponsors

#### Gold Sponsor 
- [TOMOAKI12345](https://github.com/TOMOAKI12345)
- [CubeSoft, Inc.](https://www.cube-soft.jp/)

#### Silver Sponsor
- [sknjpn](https://twitter.com/sknjpn)

#### Bronze Sponsor
アゲハマ, Fuyutsubaki, 😊, 🐝, 野菜ジュース, jacking75, Chris Ohk, qppon, ysaito, おおやま, 🍵, lamuda, 🌻, fal_rnd

If you like Siv3D's vision and wish to support its development, please consider becoming an individual or corporate sponsor of Siv3D. Several benefits are also available.

[Become a Siv3D Sponsor :material-github:](https://github.com/sponsors/Reputeless){ .md-button }
