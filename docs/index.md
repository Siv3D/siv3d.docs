# Siv3D: A C++ Framework for Creative Coding
<div class="noshadow-76"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/logo/logo.png"></div>

**Siv3D** is a framework for developing games and apps with **fun and easy C++ code**, distributed under the MIT license and running on Windows, macOS, Linux, and Web.

#### Download Siv3D | v0.6.4

[Windows :material-microsoft-windows:](download/windows){ .md-button .md-button--primary }[macOS :material-apple:](download/macos){ .md-button .md-button--primary }[Ubuntu :material-ubuntu:](download/ubuntu){ .md-button .md-button--primary }

#### Download Siv3D for Web (experimental) | v0.6.4

[for Web (Windows + Visual Studio) :material-microsoft-visual-studio:](download/web-vs){ .md-button .md-button--primary }[for Web (Visual Studio Code) :material-microsoft-visual-studio-code:](download/web-vscode){ .md-button .md-button--primary }

## Powerful features to streamline game and app development

- 2D / 3D graphics (Shapes, images, text, icons, videos, 3D models, etc.)
- Audio (background music, sound effects, text-to-speech, audio filters, etc.)
- Input devices (Mouse, keyboard, webcam, microphone, gamepad, etc.)
- Window, filesystem, networking
- Image processing, sound processing, physics, path finding, geometry, and other calculations

With Siv3D, you can combine a rich set of classes and functions to efficiently develop applications such as 2D / 3D games, media art, visualizers, and simulators with short code.

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

<div class="full"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/demo/chick.gif"></div>


## 7 reasons to use Siv3D

###  1. ⚡ Very short code
Siv3D のコードは最短 2 行です。描画や入出力を実現するための便利な関数とクラスが揃っているため、アプリケーションのほとんどは 1 つの .cpp ファイルだけで完成します。あなたのアイデアを、[GitHub :material-open-in-new:](https://github.com/) や [GitHub Gist :material-open-in-new:](https://gist.github.com/) などのコード共有サイトを使って手軽に保存・シェアして、世界中の Siv3D ユーザと技術を交換し、学び合いましょう。

### 2. 🛸 Latest C++
Siv3D のサンプルとライブラリ API は、最新の C++20 スタイルで書かれているため、Siv3D を使っているだけで、モダンな C++ の書き方やテクニックが自然と身に付きます。Siv3D の作者は、日本最大のゲーム開発カンファレンス CEDEC で [最新 C++ の活用に関する講演 :material-open-in-new:](https://speakerdeck.com/cpp/cedec2020) をしたり、[C++ の情報ポータル :material-open-in-new:](https://cppmap.github.io/) を作成したりするなど、最先端の C++ の普及活動に努めています。

### 3. 🏬 Small learning, big power
Siv3D は 2,200 ファイルのソースコードと 90 のサードパーティ・ソフトウェアによって構成されている大規模なエンジンですが、利用者はそのパワフルな機能を、使いやすく一貫した Siv3D の API を覚えるだけで自在に扱うことができます。勉強のコストを減らし、自分の作りたいアプリの開発に専念できます。

### 4. ⛰️ Open-source
Siv3D は MIT ライセンスのもと [GitHub 上で開発 :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D) されているため、いつでも内部コードを調べたり、改造したりできます。サードパーティ・ライブラリを含め、商用利用を妨げる条件はありません。開発したゲームやアプリケーションの収益は 100% 開発者が獲得できます。

### 5. 🛩️ Lightweight and quick start
Siv3D プログラミングを始めるための OpenSiv3D SDK インストーラはわずか 120 MB です（Windows 版）。インストールは数クリックで完了し、Visual Studio を起動すればメニューに Siv3D プロジェクトの項目が追加されているので、すぐにプログラミングを始められます。

### 6. 💗 Friendly community
Siv3D で困ったことがあれば、[Siv3D のコミュニティ](community/community/) が役に立ちます。また、全国の学校や地域コミュニティへの [無料出張勉強会](community/study-meeting/) も行っています。オープンソースソフトウェア開発に興味のある学生には、Siv3D を練習場にしたサポートプログラムを毎年提供しています。

### 7. 🌐 Runs in a Web browser
現在試験的に提供している Web 版（[OpenSiv3D for Web :material-open-in-new:](https://siv3d.kamenokosoft.com/ja/index)）を使うと、Siv3D で作った C++ アプリをブラウザ上で実行できる Web アプリに変換できます。スマホやタブレットでも動作するため、これまでよりもたくさんの人にあなたの作品を届けることができます。


## Sponsoring Siv3D
If you like Siv3D's vision and wish to support its development, please consider becoming an individual or corporate sponsor of Siv3D. Several benefits are also available.

!!! summary "Siv3D Sponsors"

	#### Platinum Sponsor 
	<a href="https://github.com/Kyle873" target="_blank"><figure><img src="https://avatars.githubusercontent.com/u/1127511?v=4" width="120" alt="Kyle873"><figcaption>Kyle Belanger</figcaption></figure></a>

	#### Gold Sponsor 
	- [TOMOAKI12345](https://github.com/TOMOAKI12345)
	- [CubeSoft, Inc.](https://www.cube-soft.jp/)

	#### Silver Sponsor
	- [sknjpn](https://twitter.com/sknjpn)

	#### Bronze Sponsor
	アゲハマ, 😀, minachun, Fuyutsubaki, 😊, 🐝, 🐠, 野菜ジュース, MawkishWaffle, jacking75, Chris Ohk, IZUNA, qppon, k-sunako, ysaito, totono, おおやま, tumf, 🍵, lamuda

	<small>（*匿名の方は絵文字）</small>

[Become a Siv3D Sponsor :material-github:](https://github.com/sponsors/Reputeless){ .md-button }
