# Ubuntu で Siv3D プログラミングを始める

## 1. システム要件
Ubuntu で Siv3D プログラミングをするのに必要な開発環境は次の通りです。

|  |  |
|--|--|
| OS | Ubuntu 20.04 LTS / Ubuntu 22.04 LTS |
| CPU | Intel または AMD 製の CPU |
| GPU | OpenGL 4.1 サポート |
| 映像出力 | モニタなど、何らかの映像出力装置があること |
| 開発環境 | GCC 9.3.0 (+ Boost 1.71.0) / GCC 11.2 (+ Boost 1.74.0) / Clang 14.0.0 (+ Boost 1.74.0) |

## 2. Siv3D の最新コードを入手する

[OpenSiv3D 公式リポジトリの main ブランチ :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D) が最新安定版です。「Code」からリポジトリをクローンするか、ZIP ファイルでソースコードをダウンロードします（「Download ZIP」）。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/ubuntu/repo.png)

## 3. Siv3D をビルドする
1. 必要なツールや依存パッケージをインストールします。
[https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L26-L49](https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L26-L49)
2. Siv3D ライブラリをビルドし、`libSiv3D.a` を作成します。 
[https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L51-L60](https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L51-L60)
3. Siv3D をインストールします。 
[https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L65](https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L65)
4. Siv3D アプリをビルドします。 
[https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L67-L76](https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L67-L76)

## 4. サンプルプログラム

Linux 版のデフォルトの [Main.cpp のコード](https://github.com/Siv3D/OpenSiv3D/blob/main/Linux/App/Main.cpp) は、標準出力をしてすぐに終了する単純なプログラムです。コメントアウト範囲を変更するなどして、次のようなサンプルコードで上書きしてください。実行中のプログラムは、++esc++ を押すか、ウィンドウを閉じると終了します。

??? summary "サンプルコード"
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