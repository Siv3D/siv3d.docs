# 1. Your First Siv3D Program
Experience Siv3D programming by modifying the first sample.

## 1.1 Modifying the First Sample
- When you create a new Siv3D project, the following sample program is provided:

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/1.gif)

- By modifying this sample program, you'll experience the following Siv3D features:
	1. Change the background color
	2. Display emojis, images, text, and shapes on the screen
	3. Handle keyboard input (++left++ and ++right++ move the player 🦖)

- The details of the functions modified in the sample will be explained in future tutorials
- First, let's explore the main features and get a feel for Siv3D programming

!!! warning "End the program before building"
	- When building modified code in Visual Studio or Xcode, if the previous program is still running, the executable file cannot be updated and the build will fail
	- To end a running Siv3D program, close the window or press ++escape++

- Click the :material-plus-circle: in the sample code below to open the explanations and modify the sample code yourself
- Specific modification examples are summarized in **1.2**

```cpp title="Sample code provided from the start" hl_lines="6 12 39 42 61 77 85 93"
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

1. Sets the scene background color to { R, G, B } = { 0.6, 0.8, 0.7 }. Try changing the numbers in the range 0.0 to 1.0 to change the background color
2. Loads an emoji and creates a texture. Try changing 🦖 to 🐕, 🐧, or 🍔. There should be no extra spaces before or after the emoji. Only one emoji can be loaded per texture
3. Draws the texture created from an image file at position (x, y) = (20, 20) on the screen. Try changing the numbers to change where it's drawn
4. Displays the text "Hello, Siv3D!🎮" on the screen. Try rewriting the text. The first number `64` in `.draw()` is the text size. Try making the text smaller or larger
5. Draws a circle that follows the mouse cursor with radius 40 pixels and color { R, G, B, opacity } = { 1.0, 0.0, 0.0, 0.5 }. Try changing the circle's radius or changing the RGB and opacity values in the range 0.0 to 1.0
6. Code that moves the player left when ++left++ is pressed. Try changing `KeyLeft` to `KeyA` to move the player with the ++a++ key
7. Code that moves the player right when ++right++ is pressed. Try changing `KeyRight` to `KeyD` to move the player with the ++d++ key
8. `.scaled(0.75)` scales the emoji to 75% of its base size. Try changing the number to `1.5` or `0.25` to change the emoji size


## 1.2 Modification Examples

??? note "Change the scene background color (line 6)"
	- Sets the scene background color to { R, G, B } = { 0.6, 0.8, 0.7 }
	- Try changing the numbers in the range 0.0 to 1.0 to change the background color

	=== "Before modification"
		```cpp
		// 背景の色を設定する | Set the background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/0.png)

	=== "Modification example"

		```cpp
		// 背景の色を設定する | Set the background color
		Scene::SetBackground(ColorF{ 0.2, 0.8, 1.0 });
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/1.png)


??? note "Change the emoji (line 12)"
	- Loads an emoji and creates a texture
	- Try changing 🦖 to 🐕, 🐧, or 🍔
	- There should be no extra spaces before or after the emoji
	- Only one emoji can be loaded per texture

	=== "Before modification"
		```cpp
		// 絵文字からテクスチャを作成する | Create a texture from an emoji
		const Texture emoji{ U"🦖"_emoji };
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/0.png)

	=== "Modification example"

		```cpp
		// 絵文字からテクスチャを作成する | Create a texture from an emoji
		const Texture emoji{ U"🐧"_emoji };
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/2.png)


??? note "Change the position where the image is displayed (line 39)"
	- Draws the texture created from an image file at position (x, y) = (20, 20) on the screen
	- Try changing the numbers to change where it's drawn
	
	=== "Before modification"
		```cpp
		// テクスチャを描く | Draw the texture
		texture.draw(20, 20);
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/0.png)

	=== "Modification example"
		```cpp
		// テクスチャを描く | Draw the texture
		texture.draw(120, 30);
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/3.png)


??? note "Change the text (line 42)"
	- Displays the text "Hello, Siv3D!🎮" on the screen. Try rewriting the text
	- The first number `64` in `.draw()` is the text size. Try making the text smaller or larger

	=== "Before modification"
		```cpp
		// テキストを描く | Draw text
		font(U"Hello, Siv3D!🎮").draw(64, Vec2{ 20, 340 }, ColorF{ 0.2, 0.4, 0.8 });
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/0.png)

	=== "Modification example"
		```cpp
		// テキストを描く | Draw text
		font(U"こんにちは、Siv3D!🤩").draw(40, Vec2{ 20, 340 }, ColorF{ 0.2, 0.4, 0.8 });
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/4.png)


??? note "Change the circle's radius, color, and opacity (line 61)"
	- Draws a circle that follows the mouse cursor with radius 40 pixels and color { R, G, B, opacity } = { 1.0, 0.0, 0.0, 0.5 }
	- Try changing the circle's radius or changing the RGB and opacity values in the range 0.0 to 1.0

	=== "Before modification"
		```cpp
		// 半透明の円を描く | Draw a semi-transparent circle
		Circle{ Cursor::Pos(), 40 }.draw(ColorF{ 1.0, 0.0, 0.0, 0.5 });
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/0.png)

	=== "Modification example"
		```cpp
		// 半透明の円を描く | Draw a semi-transparent circle
		Circle{ Cursor::Pos(), 80 }.draw(ColorF{ 0.0, 1.0, 0.0, 0.8 });
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/5.png)


??? note "Change the keys that control the player (line 77, line 85)"
	- Code that moves the player left when ++left++ is pressed and right when ++right++ is pressed
	- Try changing `KeyLeft` to `KeyA` and `KeyRight` to `KeyD` so the player moves left with ++a++ and right with ++d++

	=== "Before modification"
		```cpp
		if (KeyLeft.pressed())
		```
		```cpp
		if (KeyRight.pressed())
		```

	=== "Modification example"
		```cpp
		if (KeyA.pressed())
		```
		```cpp
		if (KeyD.pressed())
		```


??? note "Change the emoji size (line 93)"
	- `.scaled(0.75)` scales the emoji to 75% of its base size
	- Try changing the number to `1.5` or `0.25` to change the emoji size

	=== "Before modification"
		```cpp
		// プレイヤーを描く | Draw the player
		emoji.scaled(0.75).mirrored(isPlayerFacingRight).drawAt(playerPosX, 540);
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/0.png)

	=== "Modification example"
		```cpp
		// プレイヤーを描く | Draw the player
		emoji.scaled(1.5).mirrored(isPlayerFacingRight).drawAt(playerPosX, 540);
		```
		![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/hello/7.png)


??? example "Hot Reload"
	- In Visual Studio, under certain conditions, you can use "hot reload" to apply code changes while the program is running
	- If you want to know how to use hot reload, refer to [**Hot Reload**](../tools/hot-reload.md)


## Review Checklist
- [x] Understood how to modify and run Siv3D programs
- [x] Changed the background color
- [x] Changed the emoji
- [x] Changed the position where the image is displayed
- [x] Changed the text
- [x] Changed the circle's radius, color, and opacity
- [x] Changed the keys that control the player
- [x] Changed the emoji size