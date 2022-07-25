# Getting Started with Siv3D on Ubuntu

## 1. System requirements
Ubuntu で OpenSiv3D v0.6.4 プログラミングをするのに必要な開発環境です。

|  |  |
|--|--|
| OS | Ubuntu 20.04 LTS / Ubuntu 22.04 LTS |
| CPU | Intel / AMD CPU |
| GPU | OpenGL 4.1 compatible hardware |
| Output Devices | Monitors |
| Compilers | GCC 9.3.0 (+ Boost 1.71.0) / GCC 11.2 (+ Boost 1.74.0) / Clang 14.0.0 (+ Boost 1.74.0) |

- 非公式の ARM 対応版があります。詳しくは Siv3D ユーザコミュニティ Slack の `#linux` チャンネルをご覧ください

## 2. Getting the latest source code from the official OpenSiv3D repository

[OpenSiv3D 公式リポジトリの main ブランチ :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D) が最新安定版です。「Code」からリポジトリをクローンするか、ZIP ファイルでソースコードをダウンロードします（「Download ZIP」）。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/ubuntu/repo.png)

## 3. Building the Siv3D library and the sample application
1. 次を参考に、必要なツールや依存パッケージをインストールします
[https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L26-L49](https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L26-L49)
2. 次を参考に Siv3D ライブラリをビルドし、`libSiv3D.a` を作成します 
[https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L51-L60](https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L51-L60)
3. 次を参考に Siv3D アプリをビルドします 
[https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L62-L71](https://github.com/Siv3D/OpenSiv3D/blob/main/.github/workflows/ci.yml#L62-L71)

デフォルトの Main.cpp に用意されているプログラムは「すぐ終了する空のプログラム」なので、何も面白いものは表示されません。次のようなサンプルコードで上書きしてください。実行中のプログラムは、++esc++ を押すか、ウィンドウを閉じると終了します

??? summary "サンプルコード"
    ```cpp
    # include <Siv3D.hpp> // OpenSiv3D v0.6.4

    void Main()
    {
        // 背景の色を設定 | Set background color
        Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

        // 通常のフォントを作成 | Create a new font
        const Font font{ 60 };

        // 絵文字用フォントを作成 | Create a new emoji font
        const Font emojiFont{ 60, Typeface::ColorEmoji };

        // `font` が絵文字用フォントも使えるようにする | Set emojiFont as a fallback
        font.addFallback(emojiFont);

        // 画像ファイルからテクスチャを作成 | Create a texture from an image file
        const Texture texture{ U"example/windmill.png" };

        // 絵文字からテクスチャを作成 | Create a texture from an emoji
        const Texture emoji{ U"🐈"_emoji };

        // 絵文字を描画する座標 | Coordinates of the emoji
        Vec2 emojiPos{ 300, 150 };

        // テキストを画面にデバッグ出力 | Print a text
        Print << U"Push [A] key";

        while (System::Update())
        {
            // テクスチャを描く | Draw a texture
            texture.draw(200, 200);

            // テキストを画面の中心に描く | Put a text in the middle of the screen
            font(U"Hello, Siv3D!🚀").drawAt(Scene::Center(), Palette::Black);

            // サイズをアニメーションさせて絵文字を描く | Draw a texture with animated size
            emoji.resized(100 + Periodic::Sine0_1(1s) * 20).drawAt(emojiPos);

            // マウスカーソルに追随する半透明な円を描く | Draw a red transparent circle that follows the mouse cursor
            Circle{ Cursor::Pos(), 40 }.draw(ColorF{ 1, 0, 0, 0.5 });

            // もし [A] キーが押されたら | When [A] key is down
            if (KeyA.down())
            {
                // 選択肢からランダムに選ばれたメッセージをデバッグ表示 | Print a randomly selected text
                Print << Sample({ U"Hello!", U"こんにちは", U"你好", U"안녕하세요?" });
            }

            // もし [Button] が押されたら | When [Button] is pushed
            if (SimpleGUI::Button(U"Button", Vec2{ 640, 40 }))
            {
                // 画面内のランダムな場所に座標を移動
                // Move the coordinates to a random position in the screen
                emojiPos = RandomVec2(Scene::Rect());
            }
        }
    }
    ```