# 1. はじめての Siv3D
Siv3D の最初のサンプルを改造して、Siv3D プログラミングを体験します。

## 1.1 最初のサンプルを改造する
Siv3D のインストール手順の最後に、次のようなサンプルプログラムの実行を確認しました。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/1.gif)

このサンプルプログラムを改造して、次のような Siv3D の機能を体験します。

1. 背景の色を変更する
2. 画面に絵文字や画像、文章、図形を表示する
3. キーボード入力を処理する（++left++ と ++right++ でプレイヤー 🦖 が移動します）

サンプルで改造する関数の詳細については、今後のチュートリアルで説明します。まずは主要な機能を実際にさわり、Siv3D プログラミングの雰囲気を感じてみましょう。

!!! warning "ビルド前にプログラムを終了する"
	Visual Studio や Xcode で変更したコードをビルドするとき、以前のプログラムが実行中のままだと、実行ファイルを更新できずビルドに失敗します。実行中の Siv3D プログラムを終了するには、ウィンドウを閉じるか、++escape++ を押します。

サンプルコード（下記）の中の :material-plus-circle: をクリックして説明を開き、手元でサンプルコードを改造しましょう。具体的な改造例をサンプルコードの下に用意しています。

```cpp title="最初から用意されているサンプルコード" hl_lines="6 12 39 42 61 77 85 93"
# include <Siv3D.hpp>

void Main()
{
	// 背景の色を設定する | Set the background color
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 }); // (1)!

	// 画像ファイルからテクスチャを作成する | Create a texture from an image file
	const Texture texture{ U"example/windmill.png" };

	// 絵文字からテクスチャを作成する | Create a texture from an emoji
	const Texture emoji{ U"🦖"_emoji }; // (2)!

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
		texture.draw(20, 20); // (3)!

		// テキストを描く | Draw text
		font(U"Hello, Siv3D!🎮").draw(64, Vec2{ 20, 340 }, ColorF{ 0.2, 0.4, 0.8 }); // (4)!

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
		Circle{ Cursor::Pos(), 40 }.draw(ColorF{ 1.0, 0.0, 0.0, 0.5 }); // (5)!

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
		if (KeyLeft.pressed()) // (6)!
		{
			// プレイヤーが左に移動する | Player moves left
			playerPosX = Max((playerPosX - speed * Scene::DeltaTime()), 60.0);
			isPlayerFacingRight = false;
		}

		// 右キーが押されていたら | If right key is pressed
		if (KeyRight.pressed()) // (7)!
		{
			// プレイヤーが右に移動する | Player moves right
			playerPosX = Min((playerPosX + speed * Scene::DeltaTime()), 740.0);
			isPlayerFacingRight = true;
		}

		// プレイヤーを描く | Draw the player
		emoji.scaled(0.75).mirrored(isPlayerFacingRight).drawAt(playerPosX, 540); // (8)!
	}
}
```

1. シーンの背景色を { R, G, B } = { 0.6, 0.8, 0.7 } に設定しています。数字を 0.0～1.0 の範囲で変更して、背景色を変えてみましょう。
2. 絵文字をロードして、テクスチャを作成しています。🦖 を 🐕 や 🐧, 🍔 に変えてみましょう。絵文字の前後に余分な空白があってはいけません。1 つのテクスチャでロードできる絵文字は 1 つまでです。
3. 画像ファイルから作成したテクスチャを、画面上の位置 (x, y) = (20, 20) に描画しています。数字を変えて、描かれる位置を変更してみましょう。
4. 「Hello, Siv3D!🎮」という文章を画面に表示しています。文章を書きかえてみましょう。`.draw()` の中の最初の数 `64` は文字の大きさです。文字を小さくしたり大きくしたりしてみましょう。
5. マウスカーソルに追随する円を、半径 40 ピクセル、色 { R, G, B, 不透明度 } = { 1.0, 0.0, 0.0, 0.5 } で描いています。円の半径を変更したり、RGB・不透明度を 0.0～1.0 の範囲で変更してみましょう。
6. ++left++ が押されていたらプレイヤーが左に移動するコードです。`KeyLeft` を `KeyA` に変えて、++a++ キーでプレイヤーを動かしてみましょう。
7. ++right++ が押されていたらプレイヤーが右に移動するコードです。`KeyRight` を `KeyD` に変えて、++d++ キーでプレイヤーを動かしてみましょう。
8. `.scaled(0.75)` で、絵文字のサイズを基本サイズから 75% に縮小しています。数字を `1.5` や `0.25` に変えて、絵文字のサイズを変更してみましょう。


## 1.2 改造例

??? note "シーンの背景色を変更する（6 行目）"
	シーンの背景色を { R, G, B } = { 0.6, 0.8, 0.7 } に設定しています。数字を 0.0～1.0 の範囲で変更して、背景色を変えてみましょう。

	=== "改造前"
		```cpp
		// 背景の色を設定する | Set the background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/0.png)

	=== "改造例"

		```cpp
		// 背景の色を設定する | Set the background color
		Scene::SetBackground(ColorF{ 0.2, 0.8, 1.0 });
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/1.png)


??? note "絵文字を変更する（12 行目）"
	絵文字をロードして、テクスチャを作成しています。🦖 を 🐕 や 🐧, 🍔 に変えてみましょう。絵文字の前後に余分な空白があってはいけません。1 つのテクスチャでロードできる絵文字は 1 つまでです。

	=== "改造前"
		```cpp
		// 絵文字からテクスチャを作成する | Create a texture from an emoji
		const Texture emoji{ U"🦖"_emoji };
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/0.png)

	=== "改造例"

		```cpp
		// 絵文字からテクスチャを作成する | Create a texture from an emoji
		const Texture emoji{ U"🐧"_emoji };
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/2.png)


??? note "画像を表示する位置を変更する（39 行目）"
	画像ファイルから作成したテクスチャを、画面上の位置 (x, y) = (20, 20) に描画しています。数字を変えて、描かれる位置を変更してみましょう。
	
	=== "改造前"
		```cpp
		// テクスチャを描く | Draw the texture
		texture.draw(20, 20);
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/0.png)

	=== "改造例"
		```cpp
		// テクスチャを描く | Draw the texture
		texture.draw(120, 30);
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/3.png)


??? note "文章を変更する（42 行目）"
	「Hello, Siv3D!🎮」という文章を画面に表示しています。文章を書きかえてみましょう。`.draw()` の中の最初の数 `64` は文字の大きさです。文字を小さくしたり大きくしたりしてみましょう。

	=== "改造前"
		```cpp
		// テキストを描く | Draw text
		font(U"Hello, Siv3D!🎮").draw(64, Vec2{ 20, 340 }, ColorF{ 0.2, 0.4, 0.8 });
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/0.png)

	=== "改造例"
		```cpp
		// テキストを描く | Draw text
		font(U"こんにちは、Siv3D!🤩").draw(40, Vec2{ 20, 340 }, ColorF{ 0.2, 0.4, 0.8 });
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/4.png)


??? note "円の半径や色、不透明度を変更する（61 行目）"
	マウスカーソルに追随する円を、半径 40 ピクセル、色 { R, G, B, 不透明度 } = { 1.0, 0.0, 0.0, 0.5 } で描いています。円の半径を変更したり、RGB・不透明度を 0.0～1.0 の範囲で変更してみましょう。

	=== "改造前"
		```cpp
		// 半透明の円を描く | Draw a semi-transparent circle
		Circle{ Cursor::Pos(), 40 }.draw(ColorF{ 1.0, 0.0, 0.0, 0.5 });
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/0.png)

	=== "改造例"
		```cpp
		// 半透明の円を描く | Draw a semi-transparent circle
		Circle{ Cursor::Pos(), 80 }.draw(ColorF{ 0.0, 1.0, 0.0, 0.8 });
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/5.png)


??? note "プレイヤーを操作するキーを変更する（77 行目, 85 行目）"
	++left++ が押されていたらプレイヤーが左に移動し、++right++ が押されていたらプレイヤーが右に移動するコードです。`KeyLeft` を `KeyA` に、`KeyRight` を `KeyD` に変えて、++a++ キーで左に、++d++ キーで右にプレイヤーが移動するようにしてみましょう。

	=== "改造前"
		```cpp
		if (KeyLeft.pressed())
		```
		```cpp
		if (KeyRight.pressed())
		```

	=== "改造例"
		```cpp
		if (KeyA.pressed())
		```
		```cpp
		if (KeyD.pressed())
		```


??? note "絵文字のサイズを変更する（93 行目）"
	`.scaled(0.75)` で、絵文字のサイズを基本サイズから 75% に縮小しています。数字を `1.5` や `0.25` に変えて、絵文字のサイズを変更してみましょう。

	=== "改造前"
		```cpp
		// プレイヤーを描く | Draw the player
		emoji.scaled(0.75).mirrored(isPlayerFacingRight).drawAt(playerPosX, 540);
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/0.png)

	=== "改造例"
		```cpp
		// プレイヤーを描く | Draw the player
		emoji.scaled(1.5).mirrored(isPlayerFacingRight).drawAt(playerPosX, 540);
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/7.png)


??? example "ホットリロード"
	Visual Studio では、いくつかの条件下で、プログラムを実行したままコードの変更を適用する「ホットリロード」を利用できます。ホットリロードの方法を知りたい場合は [ホットリロード](../tools/hot-reload.md) を参照してください。


## 振り返りチェックリスト
- [x] Siv3D プログラムを変更し、実行する方法を理解した
- [x] 背景の色を変更できた
- [x] 絵文字を変更できた
- [x] 画像が表示される位置を変更できた
- [x] 文章を変更できた
- [x] 円の半径や色、不透明度を変更できた
- [x] プレイヤーを操作するキーを変更できた
- [x] 絵文字のサイズを変更できた
