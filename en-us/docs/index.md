# Siv3D: A C++ Framework for Creative Coding
<div class="logo"><img src="https://siv3d.jp/logo.png" width="1450" height="450"></div>

**Siv3D** is an open-source framework for creating games, media art, and other applications easily and enjoyably with modern C++. It comes with extensive sample code and tutorials, and an online user community where you can easily ask questions and seek advice.


## Download Siv3D

[Windows :material-microsoft-windows:](download/windows.md){ .md-button .md-button--primary }[macOS :material-apple:](download/macos.md){ .md-button .md-button--primary }[Ubuntu :material-ubuntu:](download/ubuntu.md){ .md-button .md-button--primary }[Web * :material-web:](download/web.md){ .md-button .md-button--primary }

<small>* The Web version is a community-driven, unofficial extension. It requires a more complex setup and is recommended for intermediate or advanced Siv3D users.</small>

<div class="grid cards" markdown>

-   :material-laptop:{ .lg .middle }　__Platform Support__

    ---

    Windows / macOS / Ubuntu / Web

-   :material-share-outline:{ .lg .middle }　__License__

    ---

    MIT License

-  :material-application-parentheses-outline:{ .lg .middle }　__Development Languages__

    ---

    C++20 / HLSL / GLSL

-   :material-update:{ .lg .middle }　__Version__

    ---

    0.6.16 (2025-04-07) / 0.8.0 (in development)

</div>

## Community

| Date | Event | Audience |
| --- | --- | --- |
| 2025-09-29 | [Siv3D Implementation Meetup at the University of Electro-Communications 2025 :material-open-in-new:](https://connpass.com/event/362266/){:target="_blank"} | UEC students and faculty |
| 2025-07-25 | Siv3D Lecture at NPCA Summer Camp 2025 | Nada High School students |
| 2025-06-21 | [Siv3D Implementation Meetup in Osaka 2025 :material-open-in-new:](https://connpass.com/event/357822/){:target="_blank"} | Everyone |
| 2024-12-15 | [Siv3D Study Session in Kyoto 2024 :material-open-in-new:](https://connpass.com/event/334897/){:target="_blank"} | Everyone |
| 2024-12-14 | [Siv3D Implementation Meetup in Kyoto 2024 :material-open-in-new:](https://connpass.com/event/332833/){:target="_blank"} | Everyone |
| 2024-12-01～25 | [Siv3D Advent Calendar 2024 :material-open-in-new:](https://qiita.com/advent-calendar/2024/siv3d){:target="_blank"} | Online・Everyone |

[Past Events](community/history.md){:target="_blank" .md-button } [:fontawesome-brands-discord: Join Siv3D Discord Server](https://discord.gg/mzevvsY){:target="_blank" .md-button .md-button--primary }


## Streamlining Game and Application Development with C++
Siv3D provides a rich set of classes and functions for 2D/3D graphics, audio, input handling, physics, image processing, AI, networking, and all the essentials for developing practical software.

Unlike commercial game engines such as Unity and Unreal Engine, Siv3D doesn't require proprietary editors or scripts, allowing you to complete games and applications using pure C++ code only.

For programmers who want to leverage their C++ skills or those who want to learn C++ development, Siv3D is a compelling choice.

=== "Source Code Example"

	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Set background color to light blue
		Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

		// Create texture from emoji
		const Texture texture{ U"🐥"_emoji };

		// Position to draw the texture
		Vec2 pos{ 400, 300 };

		// Main loop
		while (System::Update())
		{
			// If left mouse button is clicked
			if (MouseL.down())
			{
				// Change drawing position to current mouse cursor position
				pos = Cursor::Pos();
			}

			// Draw the texture
			texture.drawAt(pos);
		}
	}
	```

=== "Execution Result"

    ![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2024/index/emoji_cursor.gif)

## Applications of Siv3D

??? success "1. Learning Modern C++ (Click for details)"
	The creator of Siv3D is engaged in activities to promote cutting-edge C++, including giving lectures on modern C++ utilization at CEDEC, Japan's largest game developer conference. Siv3D's API and samples are also written utilizing the latest C++ standards, so using Siv3D helps you learn modern C++ coding practices. Advance your project development and C++ learning simultaneously.

	<script async class="docswell-embed" src="https://www.docswell.com/assets/libs/docswell-embed/docswell-embed.min.js" data-src="https://www.docswell.com/slide/5XEY92/embed" data-aspect="0.5625"></script>

??? success "2. Indie and Commercial Game Development (Click for details)"
	You can develop full-fledged games using Siv3D and release them on game distribution platforms like Steam. Here are some examples of games created with Siv3D.

	<iframe src="https://store.steampowered.com/widget/2487390/" frameborder="0" width="646" height="190"></iframe>
	<iframe src="https://store.steampowered.com/widget/2770160/" frameborder="0" width="646" height="190"></iframe>
	<iframe src="https://store.steampowered.com/widget/3147480/" frameborder="0" width="646" height="190"></iframe>
	<iframe src="https://store.steampowered.com/widget/2492380/" frameborder="0" width="646" height="190"></iframe>
	<iframe src="https://store.steampowered.com/widget/3382090/" frameborder="0" width="646" height="190"></iframe>
	<iframe src="https://store.steampowered.com/widget/2943760/" frameborder="0" width="646" height="190"></iframe>
	<iframe src="https://store.steampowered.com/widget/3328960/" frameborder="0" width="646" height="190"></iframe>

??? success "3. Information Visualization and GUI in Programming Contests (Click for details)"
	When solving problems in C++, you can use Siv3D to create information visualization and GUI to improve problem-solving efficiency. At the 34th National Institute of Technology Programming Contest (Kosen Programming Contest) in 2023, teams using Siv3D monopolized the 1st, 2nd, 3rd places, and special award in the competition division. The winning teams of the 33rd and 29th contests also used Siv3D. In recent contests, more than one-third of participating schools use Siv3D.

??? success "4. Software Development for Research (Click for details)"
	You can use Siv3D to create experimental applications or express simulation results as animations. Siv3D serves as a powerful tool for researchers to bring their ideas to life. Here are examples of Siv3D usage by university students and researchers.

	<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/Zk99iQE-TxE?si=EoWZOfbDDoCRRxFS" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

	<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/AAbWaGPMjg0?si=RC7OVc9PtSoLUi7x" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

??? success "5. Contributing to Open Source (Click for details)"
	You can leverage your programming experience and implementation skills to participate in open source activities that improve the Siv3D development experience, such as developing the Siv3D engine itself, reporting bugs, creating sample code, and writing articles. So far, more than 60 people, including middle school students, have committed source code to the Siv3D engine.

??? success "6. Web Browser Distribution (Click for details)"
	Using the Web version provided by volunteer users ([OpenSiv3D for Web :material-open-in-new:](download/web.md){:target="_blank"}), you can port C++ applications created with Siv3D to Web applications that run in browsers with minimal changes. By targeting smartphones and tablets, you can deliver your work to more people. Here are examples of Siv3D-made Web applications.

	<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">ゲーム「選挙で勝とう 2024」を作成しました！<br><br>舞台は日本の衆議院選挙。ある党の党首のつもりになって、12 日間の選挙を戦う全く新しいゲームです。ぜひ遊んでみてください！！！<br><br>URL: <a href="https://t.co/tnIySuWA4A">https://t.co/tnIySuWA4A</a> <a href="https://t.co/ZcNHVy61ex">pic.twitter.com/ZcNHVy61ex</a></p>&mdash; E869120 (@e869120) <a href="https://twitter.com/e869120/status/1849277131513053548?ref_src=twsrc%5Etfw">October 24, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

	<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">大学の講義で教えるために、浮動小数点数 (float 型) の仕組みを学べるアプリを Siv3D で作りました。<a href="https://t.co/BNSwkqAHzt">https://t.co/BNSwkqAHzt</a><br>(PC でのアクセス推奨) <a href="https://twitter.com/hashtag/Siv3D?src=hash&amp;ref_src=twsrc%5Etfw">#Siv3D</a> <a href="https://twitter.com/hashtag/OpenSiv3D?src=hash&amp;ref_src=twsrc%5Etfw">#OpenSiv3D</a> <a href="https://t.co/OAmZfo5R9D">pic.twitter.com/OAmZfo5R9D</a></p>&mdash; Ryo Suzuki (@Reputeless) <a href="https://twitter.com/Reputeless/status/1542160459658416129?ref_src=twsrc%5Etfw">June 29, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


## Reasons to Choose Siv3D

<div class="grid cards" markdown>

-   __1. Open Source and Trustworthy__

    ---

    Siv3D is [open source :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D){:target="_blank"}. Anyone can examine or modify the internal code. Including third-party libraries, there are no conditions that prevent commercial use. Developers can retain 100% of the revenue from games and applications they develop.

-   __2. Quick to Get Started__

    ---

    The Siv3D SDK installer for Windows is only 120 MB. Installation is completed with just a few clicks and appears in the Visual Studio menu. All information needed for development is available in the official tutorials, eliminating the need to search for books or introductory articles.

-   __3. Concise Code__

    ---

    Abundant convenient functions and classes for rendering and input/output are provided, allowing you to complete simple applications in a single .cpp file. Share your source code instantly on GitHub or GitHub Gist to exchange techniques with Siv3D users worldwide.

-  __4. Low Learning Curve, High Power__

    ---

    Siv3D is a large-scale engine consisting of 2,200 source files and 90 third-party software packages. You can freely handle its powerful features by learning only the consistent Siv3D API. Learning costs are minimized, allowing you to focus on project development.

-  __5. Friendly & Helpful Community__

    ---

    If you encounter difficulties with Siv3D, the [**online community**](community/community.md) on Discord will help. We also conduct [**free school visit study sessions**](community/community.md). For students interested in open source development, we provide support programs using Siv3D as a practice ground.

-   __6. Runs in Web Browsers__

    ---

    C++ programs created with Siv3D can be ported to Web applications running in browsers with almost no changes. By targeting smartphones and tablets, you can deliver your work to more people.
</div>


## Examples of Games Made with Siv3D

| Mutable 50 \| sashi | Maxwell's Puzzling Demon \| muratsubo Games |
| --- | --- |
| <iframe src="https://www.youtube-nocookie.com/embed/UAZB_YyMgmQ?si=j50IwGk5kx2cTI7Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe> | <iframe src="https://www.youtube-nocookie.com/embed/mGzAnk1hgNU?si=BVSVHigjTNrtKj2J" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe> |

| For the GHOSTs \| Circle Gensankoku | One week, My room \| Circle Jōyatō |
| --- | --- |
| <iframe src="https://www.youtube-nocookie.com/embed/3ns8QqGWry4?si=v6JKWmueG9RVxVL0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe> | <iframe src="https://www.youtube-nocookie.com/embed/S--QI455r3Q?si=jEabVpHLloiLO_gy" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe> |

[View More](./steam.md){:target="_blank" .md-button .md-button}

## Corporate Sponsors
<div class="sponsor"><a href="https://www.bandainamcostudios.com/" target="_blank"><img src="https://siv3d.jp/sponsors/バンダイナムコスタジオ.png" alt="Bandai Namco Studios"></a></div>

### Past Events
[Bandai Namco Studios Cup Siv3D Game Jam | Results Page](event/gamejam2023.md){:target="_blank" .md-button }


## Individual Sponsors

#### Gold Sponsor 
- [TOMOAKI12345](https://github.com/TOMOAKI12345){:target="_blank"}
- [CubeSoft, Inc.](https://www.cube-soft.jp/){:target="_blank"}

#### Silver Sponsor
- [sknjpn](https://x.com/sknjpn){:target="_blank"}
- 野菜ジュース
- [kagamiz](https://github.com/kagamiz){:target="_blank"}
- [kt2763](https://github.com/kt2763){:target="_blank"}

#### Bronze Sponsor
アゲハマ, Fuyutsubaki, 😊, 🐝, jacking75, Chris Ohk, ysaito, おおやま, ShivAlley, lamuda, fal_rnd, As Project, IZUNA, nasatame, sashi, 🌶️, 💯, PlumRice, 緑獺おがめ

---

[Corporate Sponsorship Information](sponsorship/corporate-sponsor.md){ .md-button } [Become an Individual Sponsor of Siv3D :material-github:](https://github.com/sponsors/Reputeless){:target="_blank" .md-button} 