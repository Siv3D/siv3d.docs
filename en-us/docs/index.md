# Siv3D: A C++ Framework for Creative Coding
<div class="logo"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/logo/logo.png" width="1450" height="450"></div>

**Siv3D** is an open-source framework that allows for **fun and easy programming of games and applications using modern C++ code**, incorporating sound, images, and AI. A wealth of tutorials are available, and you can easily ask questions or seek advice in the online user community. The operating environment is Windows / macOS / Linux / Web.

#### Download Siv3D | v0.6.11

[Windows :material-microsoft-windows:](download/windows){ .md-button .md-button--primary }[macOS (Intel / Rosetta) :material-apple:](download/macos){ .md-button .md-button--primary }[Ubuntu :material-ubuntu:](download/ubuntu){ .md-button .md-button--primary }

<small>Apple Silicon (M1 / M2) will be natively supported from Siv3D v0.8.0, which is currently under development.</small>

#### Download Siv3D for Web (Unofficial)

[for Web (Windows + Visual Studio) :material-microsoft-visual-studio:](download/web){ .md-button .md-button--primary }[for Web (Visual Studio Code) :material-microsoft-visual-studio-code:](download/web){ .md-button .md-button--primary }

## Exceptional Features for Efficient Game and App Development
Convenient classes and functions are provided for efficient development of 2D / 3D games, media art, visualizers, simulators, etc.

- 2D / 3D Graphics (shapes, images, text, icons, videos, 3D models, etc.)
- Audio (BGM, sound effects, text-to-speech, audio filters, etc.)
- Input Devices (mouse, keyboard, webcam, microphone, gamepad, etc.)
- Window, file system, network
- Image processing, audio processing, physics calculations, pathfinding, geometry, etc.
- AI (Access to OpenAI API)

[Learn more](./features/){ .md-button }


## The Shortest Path to Your Work's Completion
With Siv3D, you can use **C++ syntax** to get the world moving with just a few lines of code. Let's realize a lot of dreams and ideas with C++.

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
		food.drawAt(Scene::Center()); // Draw the texture at the center of the screen
		chick.drawAt(Cursor::Pos()); // Draw the texture at the mouse cursor position
	}
}
```

??? summary "See the result"
    ![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/demo/chick.gif)


## 7 Reasons to Use Siv3D

??? success "1. Extremely Short Code"
	Siv3D is equipped with a wealth of convenient functions and classes for implementing graphics and I/O, allowing you to complete a simple application with just one .cpp file. Share your source code on code sharing sites like [GitHub :material-open-in-new:](https://github.com/){:target="_blank"} or [GitHub Gist :material-open-in-new:](https://gist.github.com/){:target="_blank"}, and exchange technologies and learn together with Siv3D users around the world.

??? success "2. Learn the Latest C++"
	Siv3D's APIs and samples are written in the latest C++ standard, "C++20". Just by using Siv3D, you will naturally pick up a modern style of writing C++. The author of Siv3D has given [a lecture on the use of the latest C++ :material-open-in-new:](https://speakerdeck.com/cpp/cedec2020){:target="_blank"} at CEDEC, Japan's largest game development conference, and has also created a [C++ information portal :material-open-in-new:](https://cppmap.github.io/){:target="_blank"}. Let's proceed with both game development and C++ learning at the same time.

??? success "3. Small Learning, Great Power"
	Behind the scenes, Siv3D is a large-scale engine composed of 2,200 source code files and 90 third-party software. Users can freely handle its powerful features just by learning the user-friendly and consistent API of Siv3D. Keep your learning costs to a minimum and focus on game development.

??? success "4. Open Source"
	Siv3D is developed as [an open source :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D){:target="_blank"} under the MIT license, so you can always check the internal code and modify it. There are no conditions, including third-party libraries, that hinder commercial use. You can keep 100% of the profits from the games or applications you develop.

??? success "5. Lightweight and Quick Start"
	The OpenSiv3D SDK installer to start Siv3D programming is just 120 MB (for Windows). Installation is completed in a few clicks, and when you start Visual Studio, you will find an item for a Siv3D project in the menu, and you can start programming immediately.

??? success "6. Friendly Community"
	If you have any problems with Siv3D, the Siv3D [online community](community/community/) such as Discord can be of help. For students interested in open source software development, we offer a support program every year that uses Siv3D as a training ground. Let's create better works with your peers.

??? success "7. Running in a Web Browser"
	By using the unofficial Web version ([OpenSiv3D for Web :material-open-in-new:](https://siv3d.kamenokosoft.com/docs/en/){:target="_blank"}), you can port C++ applications made with Siv3D to run as web applications in a browser. Target smartphones and tablets to deliver your work to more people than ever before.


## Corporate Sponsor
<div class="sponsor"><a href="https://www.bandainamcostudios.com/" target="_blank"><img src="https://siv3d.jp/sponsors/バンダイナムコスタジオ.png" alt="Bandai Namco Studios Inc."></a></div>

## Individual Sponsors

#### Gold Sponsor 
- [TOMOAKI12345](https://github.com/TOMOAKI12345)
- [CubeSoft, Inc.](https://www.cube-soft.jp/)

#### Silver Sponsor
- [sknjpn](https://twitter.com/sknjpn)

#### Bronze Sponsor
アゲハマ, Fuyutsubaki, 😊, 🐝, 野菜ジュース, jacking75, Chris Ohk, qppon, ysaito, おおやま, 🍵, lamuda, 🌻, fal_rnd, As Project, 🍑, IZUNA

[Become a Siv3D Sponsor :material-github:](https://github.com/sponsors/Reputeless){:target="_blank" .md-button} [Sponsorship Opportunities](./sponsorship/corporate-sponsor){ .md-button } 
