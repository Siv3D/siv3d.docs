# Getting Started with Siv3D on Ubuntu

## 1. System Requirements
The necessary development environment for Siv3D programming on Ubuntu is as follows:

|  |  |
|--|--|
| OS | Ubuntu 20.04 LTS / Ubuntu 22.04 LTS |
| CPU | Intel / AMD CPU |
| GPU | OpenGL 4.1 compatible hardware |
| Output Devices | Monitors |
| Compilers | GCC 9.3.0 (+ Boost 1.71.0) / GCC 11.2 (+ Boost 1.74.0) / Clang 14.0.0 (+ Boost 1.74.0) |

## 2. Obtaining the latest code of Siv3D

[`main` branch of the OpenSiv3D official repository :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D) is the latest stable version. You can clone the repository from "Code" or download the source code as a ZIP file ("Download ZIP").

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/ubuntu/repo.png)

## 3. BUilding Siv3D
1. Install necessary tools and dependency packages.
[https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ubuntu.yml#L22-L23](https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ubuntu.yml#L22-L23)
2. Build the Siv3D library and create `libSiv3D.a`.
[https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ubuntu.yml#L25-L34](https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ubuntu.yml#L25-L34)
3. Install Siv3D.
[https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ubuntu.yml#L36-L39](https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ubuntu.yml#L36-L39)
4. Build a Siv3D application.
[https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ubuntu.yml#L41-L50](https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ubuntu.yml#L41-L50)

## 4. Sample Program

The default [code of Main.cpp](https://github.com/Siv3D/OpenSiv3D/blob/main/Linux/App/Main.cpp) for the Linux version is a simple program that outputs to the standard output and then terminates immediately. Please overwrite it with the following sample code by changing the comment-out range, etc. The running program will terminate when you press ++esc++ or close the window.

??? summary "sample code"
    ```cpp
    # include <Siv3D.hpp>

    void Main()
    {
        // 背景の色を設定する | Set the background color
        Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

        // 画像ファイルからテクスチャを作成する | Create a texture from an image file
        const Texture texture{ U"example/windmill.png" };

        // 絵文字からテクスチャを作成する | Create a texture from an emoji
        const Texture emoji{ U"🦖"_emoji };

        // 太文字のフォントを作成する | Create a bold font with MSDF method
        const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

        // テキストに含まれる絵文字のためのフォントを作成し、font に追加する | Create a font for emojis in text and add it to font as a fallback
        const Font emojiFont{ 48, Typeface::ColorEmoji };
        font.addFallback(emojiFont);

        // ボタンを押した回数 | Number of button presses
        int32 count = 0;

        // チェックボックスの状態 | Checkbox state
        bool checked = false;

        // プレイヤーの移動スピード | Player's movement speed
        double speed = 200.0;

        // プレイヤーの X 座標 | Player's X position
        double playerPosX = 400;

        // プレイヤーが右を向いているか | Whether player is facing right
        bool isPlayerFacingRight = true;

        while (System::Update())
        {
            // テクスチャを描く | Draw the texture
            texture.draw(20, 20);

            // テキストを描く | Draw text
            font(U"Hello, Siv3D!🎮").draw(64, Vec2{ 20, 340 }, ColorF{ 0.2, 0.4, 0.8 });

            // 指定した範囲内にテキストを描く | Draw text within a specified area
            font(U"Siv3D (シブスリーディー) は、ゲームやアプリを楽しく簡単な C++ コードで開発できるフレームワークです。")
                .draw(18, Rect{ 20, 430, 480, 200 }, Palette::Black);

            // 長方形を描く | Draw a rectangle
            Rect{ 540, 20, 80, 80 }.draw();

            // 角丸長方形を描く | Draw a rounded rectangle
            RoundRect{ 680, 20, 80, 200, 20 }.draw(ColorF{ 0.0, 0.4, 0.6 });

            // 円を描く | Draw a circle
            Circle{ 580, 180, 40 }.draw(Palette::Seagreen);

            // 矢印を描く | Draw an arrow
            Line{ 540, 330, 760, 260 }.drawArrow(8, SizeF{ 20, 20 }, ColorF{ 0.4 });

            // 半透明の円を描く | Draw a semi-transparent circle
            Circle{ Cursor::Pos(), 40 }.draw(ColorF{ 1.0, 0.0, 0.0, 0.5 });

            // ボタン | Button
            if (SimpleGUI::Button(U"count: {}"_fmt(count), Vec2{ 520, 370 }, 120, (checked == false)))
            {
                // カウントを増やす | Increase the count
                ++count;
            }

            // チェックボックス | Checkbox
            SimpleGUI::CheckBox(checked, U"Lock \U000F033E", Vec2{ 660, 370 }, 120);

            // スライダー | Slider
            SimpleGUI::Slider(U"speed: {:.1f}"_fmt(speed), speed, 100, 400, Vec2{ 520, 420 }, 140, 120);

            // 左キーが押されていたら | If left key is pressed
            if (KeyLeft.pressed())
            {
                // プレイヤーが左に移動する | Player moves left
                playerPosX = Max((playerPosX - speed * Scene::DeltaTime()), 60.0);
                isPlayerFacingRight = false;
            }

            // 右キーが押されていたら | If right key is pressed
            if (KeyRight.pressed())
            {
                // プレイヤーが右に移動する | Player moves right
                playerPosX = Min((playerPosX + speed * Scene::DeltaTime()), 740.0);
                isPlayerFacingRight = true;
            }

            // プレイヤーを描く | Draw the player
            emoji.scaled(0.75).mirrored(isPlayerFacingRight).drawAt(playerPosX, 540);
        }
    }
    ```