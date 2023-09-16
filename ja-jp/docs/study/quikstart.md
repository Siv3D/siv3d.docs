# 高速チュートリアル

## 1. プログラムのビルドと実行

- [Windows](../download/windows.md){:target="_blank"}
- [macOS](../download/macos.md){:target="_blank"}
- [Linux](../download/ubuntu.md){:target="_blank"}

!!! success "振り返りチェックリスト"
	- [x] 新しいプロジェクトを作成した
	- [x] 新しいプロジェクトに最初から用意されているプログラムをビルドして実行した
	- [x] 実行したプログラムのウィンドウを閉じるとプログラムが終了することを確認した
	- [x] 実行したプログラムは ++esc++ キーでも終了できることを確認した


## 2. Siv3D の Hello World

#### 2.1 最初から用意されているプログラム

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/hello/1.gif)

??? summary "サンプルコード"
	```cpp hl_lines="6 12 39 42 61 77 85 93"
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

	1. (R, G, B) = (0.6, 0.8, 0.7) でシーンの背景色を設定しています。数字を 0.0～1.0 の範囲で変更して、背景色を変えてみましょう。
	2. 絵文字データをロードしています。🦖 を 🐕 や 🐧, 🍔 に変えてみましょう。絵文字の前後に余分な空白を挿入しないよう気を付けてください。
	3. 画像ファイルから作成したテクスチャを、画面上の位置 (x, y) = (20, 20) に描画しています。数字を変えて、描かれる位置を変更してみましょう。
	4. 「Hello, Siv3D!🎮」という文章を画面に表示しています。文章を書き換えてみましょう。`.draw()` の中の `64` は文字の大きさです。小さくしたり大きくしたりしてみましょう。
	5. マウスカーソルに追随する円を、半径 40 ピクセル、色 (R, G, B, 不透明度) = (1.0, 0.0, 0.0, 0.5) で描いています。円の半径や色、不透明度を変更してみましょう。
	6. ++left++ が押されていたらプレイヤーが左に移動するようにしています。`KeyLeft` を `KeyA` に変えて、++a++ キーでプレイヤーを動かしてみましょう。
	7. ++right++ が押されていたらプレイヤーが右に移動するようにしています。`KeyRight` を `KeyD` に変えて、++d++ キーでプレイヤーを動かしてみましょう。
	8. `.scaled(0.75)` で絵文字のサイズを 75% に縮小しています。数字を変えて、絵文字のサイズを変更してみましょう。


- 上記のサンプルコードをクリックで展開すると、8 箇所に (+) マークがあります。(+) をクリックするとカスタマイズのヒントが現れます。
- Visual Studio や Xcode で変更したコードをビルドするとき、以前のプログラムが実行中のままだと、ビルドに失敗します。実行中の Siv3D プログラムを終了するには、ウィンドウを閉じるか、++escape++ を押します。
- 絵文字は [emojipedia :material-open-in-new:](https://emojipedia.org/){:target="_blank"} で探すと便利です。
- Windows の場合は、++windows+period++ で出てくる絵文字入力メニューも使えます。
- Windows では、++alt+enter++ でウィンドウを全画面表示にすることができます。

#### 2.2 これ以外のサンプルを体験する
- 本サイトのメニューから「サンプル」を選んでください。
- ゲーム、アプリ、図形、物理演算など、テーマに分かれた 100 以上のサンプルが用意されています。

!!! success "振り返りチェックリスト"
	- [x] 新しいプログラムをビルドする前に、実行中の Siv3D プログラムを閉じることを理解した
	- [x] 背景の色を変更できた
	- [x] 絵文字を変更できた
	- [x] 画像が表示される位置を変更できた
	- [x] 文章を変更できた
	- [x] 円の半径や色、不透明度を変更できた
	- [x] プレイヤーを操作するキーを変更できた
	- [x] 絵文字のサイズを変更できた


## 3. Main() 関数とメインループ
`<Siv3D.hpp>` ヘッダをインクルードするだけで、Siv3D の関数やクラスを使ったプログラムを書けるようになります。`<iostream>` や `<vector>` などの C++ 標準ライブラリのインクルードは不要です。Siv3D のプログラムでは、`std::string` の代わりに `String`, `std::vector` の代わりに `Array` のような Siv3D の型を使うことが推奨されます。

Siv3D のプログラムでは、`int main()` ではなく `void Main()` という関数をメイン関数として使います。

`while (System::Update()) { }` というループが**メインループ**です。メインループは、プログラムを実行しているモニタ 🖥️ の表示周期（リフレッシュレート）に合わせて繰り返されます。一般に毎秒 60 回や 120 回です。

```C++ hl_lines="5-8"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update()) // System::Update() が false を返すまで繰り返す
	{

	}
}
```

`System::Update()` 関数は普段は `true` を返すため、半永久的にメインループが続きますが、ウィンドウを閉じたり ++esc++ を押したりするなど特定の操作をすると、それ以降は `false` を返すことでメインループを終了させ、そのまま `Main()` 関数の終端まで到達するとプログラムが終了します。

!!! success "振り返りチェックリスト"
	- [x] Siv3D の基本的なプログラムでは `<Siv3D.hpp>` のみをインクルードすることを学んだ
	- [x] Siv3D プログラムのメイン関数が `int main()` ではなく `void Main()` であることを学んだ
	- [x] `System::Update()` がメインループの頻度を制御していることを学んだ
	- [x] `System::Update()` の戻り値が `false` になるとアプリケーションが終了することを学んだ


## 4. Print と ClearPrint()
`Print` を使うと、画面に文字列や数値を簡易表示できます。Siv3D で文字列を扱うときは、ダブルクォーテーションの前に `U` を付けます。これは、文字列を Unicode (UTF-32) 文字列として扱うための C++ の記法です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/print/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"C++";

	Print << U"Hello, " << U"Siv3D"; // 複数に分けることもできる

	Print << 123;

	Print << 4.567;

	while (System::Update())
	{

	}
}
```

`ClearPrint()` を使うと、画面に残っている簡易表示をすべて消去できます。メインループの先頭で常に `ClearPrint()` することで、現在のフレーム内で出力した内容だけを画面に表示することができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/print/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	while (System::Update())
	{
		// 古い出力（以前のフレームの出力）を消去する
		ClearPrint();

		Print << count;

		++count;
	}
}
```

!!! success "振り返りチェックリスト"
	- [x] `Print` を使って画面に文字列や数値を簡易表示する方法を学んだ
	- [x] `ClearPrint()` を使って簡易表示を消去する方法を学んだ


## 5. 基本的なデータ型

#### 数値型
よく使う重要なものに ★ を付けています。

| 型名        | 説明                                                                      |
|-----------|-------------------------------------------------------------------------|
| bool      | ★ ブーリアン型（`false` または `true`）                                            |
| int8      | 符号付き 8-bit 整数型（-128 ～ 127）                                              |
| uint8     | 符号無し 8-bit 整数型（0 ～ 255）                                                 |
| int16     | 符号付き 16-bit 整数型（-32,768 ～ 32,767）                                       |
| uint16    | 符号無し 16-bit 整数型（0 ～ 65,535）                                             |
| int32     | ★ 符号付き 32-bit 整数型（-2,147,483,648 ～ 2,147,483,647）                       |
| uint32    | 符号無し 32-bit 整数型（0 ～ 4,294,967,295）                                    |
| int64     | 符号付き 64-bit 整数型（-9,223,372,036,854,775,808 ～ 9,223,372,036,854,775,807） |
| uint64    | 符号無し 64-bit 整数型（0 ～ 18,446,744,073,709,551,615）                         |
| float     | 単精度浮動小数点数型                                                              |
| double    | ★ 倍精度浮動小数点数型                                                            |
| size_t    | ★ オブジェクトのサイズを表現する符号無し 64-bit 整数型（0 ～ 18,446,744,073,709,551,615）        |

Siv3D で整数を扱うときは、`int`, `unsigned long long` のような型名の代わりに、`int32`, `uint64` のように明示的にサイズを表現した型名を使います。これにより、プラットフォーム間での移植性が高まり、一貫性のある読みやすいコードになります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 score = 100;

	Print << score;

	score = 200;

	Print << score;

	score += 50;

	Print << score;

	while (System::Update())
	{

	}
}
```

#### 文字と文字列

| 型名        | 説明                                                                      |
| ----------- | ------------------------------------------------------------------------- |
| char32       | ★ UTF-32 の 1 要素（`char32_t` の別名） |
| String       | ★ 文字列クラス。要素は `char32`           |
| StringView   | 文字列のビュークラス                      |
| FilePath     | ★ ファイルパス文字列（`String` の別名）       |
| FilePathView | ファイルパス文字列のビュー（`StringView` の別名） |

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Hello, Siv3D";

	Print << s;

	s += U"!";

	Print << s;

	// Siv3D を C++ に置き換える
	s.replace(U"Siv3D", U"C++");

	Print << s;

	while (System::Update())
	{

	}
}
```

#### 配列などのデータ構造

| 型名        | 説明                                                                      |
| ----------- | ------------------------------------------------------------------------- |
| Array&lt;Type, Allocator&gt;                              | ★ 動的配列（C++ 標準ライブラリの `std::vector` の置き換え）                   |
| Grid&lt;Type, Allocator&gt;                               | 動的な二次元配列                                                 |
| HashSet&lt;Type, Hash, Eq, Alloc&gt;                      | ハッシュテーブルによる Set（C++ 標準ライブラリの `std::unordered_set` の置き換え） |
| HashTable&lt;Key, Value, Hash, Eq, Alloc&gt;              | ハッシュテーブルによる Map（C++ 標準ライブラリの `std::unordered_map` の置き換え） |
| Optional&lt;Type&gt;                                      | ★ 無効値を表現できる型（C++ 標準ライブラリの `std::optional` の置き換え）           |
| std::array&lt;Type, size_t&gt;                            | 固定長配列                                                    |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> coins;

	// 配列の末尾に新しい要素を追加する
	coins << 10;
	coins << 5;
	coins << 1;
	coins << 100 << 500;

	Print << coins;

	// 要素をソートする
	coins.sort();

	Print << coins;

	while (System::Update())
	{

	}
}
```

!!! success "振り返りチェックリスト"
	- [x] Siv3D でよく使う型、`bool`, `int32`, `double`, `size_t`, `char32`, `String`, `Array` を理解した


## 6. 背景の色を変える
背景の色は `Scene::SetBackground(色)` で変更できます。  
色の指定方法はさまざまな方法があります。

| 色の指定方法 | 説明 |
| --- | --- |
| `Palette::色名` | Siv3D で定義されている色を指定する。<br>`Palette::White`, `Palette::Black`, `Palette::Skyblue` など |
| `ColorF{ 赤, 緑, 青 }` | 赤、緑、青成分を 0.0～1.0 の範囲で指定する |
| `ColorF{ グレースケール }` | グレースケール 0.0～1.0 の範囲で指定する |
| `HSV{ 色相, 彩度, 明度 }` | 色相を 0.0～360.0, 彩度、明度を 0.0～1.0 の範囲で指定する |
| `HSV{ 色相 }` | 色相を 0.0～360.0 の範囲で指定する |
| `ColorF{ U"#77FF00" }` | 16 進数で色を指定する |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景を白色にする
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{

	}
}
```

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景色を RGB で指定する
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{

	}
}
```

!!! success "振り返りチェックリスト"
	- [x] 背景色を指定するには `Scene::SetBackground()` を使うことを学んだ
	- [x] `Palette::***` で定義されている色を使うことができることを学んだ
	- [x] 色を RGB で指定するには `ColorF{ 赤, 緑, 青 }` または `ColorF{ グレースケール }` を使うことを学んだ
	- [x] 色を HSV で指定するには `HSV{ 色相, 彩度, 明度 }` または `HSV{ 色相 }` を使うことを学んだ

## 7. 図形を描く

#### 7.1 座標系
ウィンドウ内で、背景色を変えられる部分が **画面（シーン）**です。Siv3D はこの領域に文字や図形、画像を表示できます。

画面のサイズは、基本の状態では**幅 800 ピクセル**、**高さ 600 ピクセル**です。

画面上の位置を表す座標系は、最も左上のピクセルが「X 座標 0」「Y 座標 0」を表す (0, 0) です。右に進むと X 座標が大きくなり、下に進むと Y 座標が大きくなります。画面の最も右下のピクセルの座標は (799, 599) です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// 現在のマウスカーソル座標を表示する
		Print << Cursor::Pos();
	}
}
```

このコードを実行すると、マウスカーソルの座標が画面の左上に簡易表示されます。

#### 7.2 円を描く
Siv3D では、何かを描く命令はメインループの中に記述します。円を描くときは `Circle` を作成し、その `.draw()` を呼びます。`Circle` は `Circle{ x, y, r }` のように、中心の X 座標、Y 座標、半径を指定して作成します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 円を描く
		Circle{ 400, 300, 20 }.draw();
	}
}
```

!!! info "Circle の構造"
	`Circle` は次のようなクラスです。
	```cpp
	struct Circle
	{
		double x;
		double y;
		double r;
	};
	```


`Circle` は、二次元座標を表す `Point` 型や `Vec2` 型の値を使って、`Circle{ pos, r }` のように、2 つの引数から作成することもできます。`Cursor::Pos()` は現在のマウスカーソルの座標を `Point` 型で返す関数なので、これを使ってマウスに追随する円を描くことができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// マウスに追随する円を描く
		Circle{ Cursor::Pos(), 100 }.draw();
	}
}
```

!!! info "Point や Vec2 の構造"
	`Point` と `Vec2` は次のようなクラスです。
	```cpp
	struct Point
	{
		int32 x;
		int32 y;
	};

	struct Vec2
	{
		double x;
		double y;
	};
	```

図形に色を付けたいときは `.draw()` 関数の引数に色を渡します。色を指定しなかった場合は白色になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ 100, 200, 40 }.draw(); // 色を指定しない場合は白色

		Circle{ 200, 200, 40 }.draw(Palette::Green);

		Circle{ 300, 200, 40 }.draw(Palette::Skyblue);

		Circle{ 400, 200, 40 }.draw(ColorF{ 1.0, 0.8, 0.0 });

		Circle{ 500, 200, 40 }.draw(ColorF{ 0.8 });

		Circle{ 600, 200, 40 }.draw(HSV{ 160.0, 0.5, 1.0 });

		Circle{ 700, 200, 40 }.draw(HSV{ 160.0 });
	}
}
```


`ColorF` と `HSV` は、**不透明度**を指定できます。不透明度は 0.0 から 1.0 の範囲で指定します。

0.0 は完全に透明、1.0 は完全に不透明で、0.5 のときは背景色と描画色が 50% ずつ混ざった色になります。

不透明度 `a` （アルファ）は、`ColorF{ r, g, b, a }`, `ColorF{ gray, a }`, `HSV{ h, s, v, a }`, `HSV{ h, a }` のように、最後の引数に指定します。

不透明度を指定しない場合は、デフォルトで 1.0 になります。

`Scene::SetBackground()` に指定する色については、不透明度は無視されるため無意味です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 中央に白い円を描く
		Circle{ 400, 300, 200 }.draw();

		// 左に半透明の円を描く
		Circle{ 200, 300, 200 }.draw(ColorF{ 1.0, 0.0, 0.0, 0.9 });

		// 右に半透明の円を描く
		Circle{ 600, 300, 200 }.draw(HSV{ 240.0, 0.5, 1.0, 0.2 });

		// マウスカーソルの位置に半透明の円を描く
		Circle{ Cursor::Pos(), 100 }.draw(ColorF{ 0.0, 0.5 });
	}
}
```


#### 7.3 長方形を描く
長方形を描くときは `Rect` を作成して `.draw()` します。

`Rect{ x, y, w, h }` は、左上座標が (x, y), 幅が w, 高さが h の長方形です。

`Rect{ x, y, s }` は引数を省略したオーバーロードで、左上座標が (x, y), 幅と高さが s の正方形です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 長方形を描く 
		Rect{ 20, 40, 400, 100 }.draw();

		// 正方形を描く 
		Rect{ 100, 200, 80 }.draw(Palette::Orange);
	}
}
```

!!! info "Rect の構造"
	`Rect` は次のようなクラスです。
	```cpp
	struct Rect
	{
		int32 x;
		int32 y;
		int32 w;
		int32 h;
	};
	```

座標や大きさを `double` 型で扱いたい場合は、`Rect` の代わりに `RectF` 使います。

`Scene::Time()` は、ゲーム開始からの経過時間を秒 （`double` 型）で返します。

下記のコードでは、長方形の幅 `w` が毎秒 20.0 のペースで増えていきます。`double` 型である `w` に合わせるため、`Rect` ではなく `RectF` を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double w = (Scene::Time() * 20.0);

		// 長方形を描く 
		RectF{ 20, 40, w, 100 }.draw();
	}
}
```

!!! info "RectF の構造"
	`RectF` は次のようなクラスです。
	```cpp
	struct Rect
	{
		double x;
		double y;
		double w;
		double h;
	};
	```

#### 7.4 円や長方形の枠を描く
円や長方形の枠だけを描きたい場合、`.draw()` の代わりに `.drawFrame(innerThickness, outerThickness, color)` を使います。

`innerThickness` は内側方向への太さ、`outerThickness` は外側方向への太さを表す `double` 型の値です。`innerThickness` と `outerThickness` には、それぞれ 0.0 以上の値を指定します。

`color` を省略した場合、`.draw()` と同様に白色で描画されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 長方形の枠を描く
		Rect{ 100, 100, 100, 30 }
			.drawFrame(3, 0);

		// 長方形の枠を描く
		Rect{ 220, 100, 100, 30 }
			.drawFrame(0, 3);

		// 長方形の枠を描く
		Rect{ 200, 200, 400, 100 }
			.drawFrame(3, 3, Palette::Orange);

		// 円の枠を描く
		Circle{ Cursor::Pos(), 40 }
			.drawFrame(1, 1, Palette::Seagreen);
	}
}
```


#### 7.5 長方形のグラデーション
`Rect` や `RectF` を `.draw()` する際、`.draw(Arg::top = 色, Arg::bottom = 色)` のように書くことで、上下方向のグラデーションで長方形を描画できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// グラデーションで長方形を描く
		Rect{ 0, 0, 600, 500 }
			.draw(Arg::top = ColorF{ 0.5, 0.7, 0.9 }, Arg::bottom = ColorF{ 0.5, 0.9, 0.7 });
	}
}
```

`.draw(Arg::left = 色, Arg::right = 色)` のように書くことで、左右方向のグラデーションで長方形を描画できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// グラデーションで長方形を描く
		Rect{ 0, 0, 600, 500 }
			.draw(Arg::left = ColorF{ 0.5, 0.7, 0.9 }, Arg::right = ColorF{ 0.5, 0.9, 0.7 });
	}
}
```

!!! success "振り返りチェックリスト"
	- [x] 画面の座標系と基本の画面サイズ (800x600) を理解した
	- [x] `Circle` を使って円を描画する方法を学んだ
	- [x] 半透明の色の指定方法を学んだ
	- [x] `Rect` または `RectF` を使って長方形を描画する方法を学んだ
	- [x] 円や長方形の枠を描画する方法を学んだ
	- [x] グラデーションで長方形を描画する方法を学んだ

## 8. 模様を描く

ループと図形描画を組み合わせると、模様やパターンを簡単に描くことができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 i = 0; i < 5; ++i)
		{
			Circle{ (i * 100), 100, 30 }.draw(Palette::Skyblue);
		}

		for (int32 i = 0; i < 5; ++i)
		{
			Circle{ (50 + i * 100), 200, 30 }.draw(Palette::Seagreen);
		}
	}
}
```

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 y = 0; y < 4; ++y) // 縦方向
		{
			for (int32 x = 0; x < 6; ++x) // 横方向
			{
				Circle{ (x * 100), (y * 100), 30 }.draw(Palette::Skyblue);
			}
		}
	}
}
```

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 y = 0; y < 4; ++y)
		{
			for (int32 x = 0; x < 6; ++x)
			{
				if ((x + y) % 2 == 0)
				{
					Circle{ (x * 100), (y * 100), 10 }.draw(Palette::Skyblue);
				}
				else
				{
					Circle{ (x * 100), (y * 100), 30 }.draw(Palette::Skyblue);
				}
			}
		}
	}
}
```

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 6; ++x)
		{
			Rect{ (x * 100), 0, 80, 600 }.draw(ColorF{ 0.0, (x * 0.2), 1.0 });
		}
	}
}
```

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 10; ++x)
		{
			Rect{ (x * 80), 0, 80, 600 }.draw(HSV{ (x * 36), 0.5, 1.0 });
		}
	}
}
```

!!! success "振り返りチェックリスト"
	- [x] ループと図形描画を組み合わせて模様を描画する方法を学んだ


## 9. 絵文字を描く
絵文字を自由な場所に描くには、絵文字からテクスチャ（`Texture` クラス）を作成し、そのメンバ関数 `.drawAt()` を使います。

まず、`Texture 変数名{ U"絵文字"_emoji };` で絵文字のテクスチャを作成します。テクスチャの作成はコストがかかるため、**メインループの前**で行います。

作成したテクスチャを画面に表示するには、`.drawAt(x, y)` または `.drawAt(pos)` を使います。指定した座標を中心として絵文字が描かれます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/emoji/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji1{ U"🐈"_emoji };

	const Texture emoji2{ U"🍎"_emoji };

	while (System::Update())
	{
		emoji1.drawAt(100, 100);

		emoji2.drawAt(200, 300);

		emoji1.drawAt(400, 300);

		emoji2.drawAt(Cursor::Pos());
	}
}
```

!!! info "絵文字を探す"
    - 絵文字の種類は [emojipedia :material-open-in-new:](https://emojipedia.org/){:target="_blank"} で探すと便利です。全部で 3700 種類以上が用意されています。
    - Windows の場合は、++windows+period++ で出てくる、OS 標準の絵文字入力メニューも使えます。

デフォルトの絵文字の大きさは余白（透明部分）も含めて 136x128 ピクセルです。`.drawAt(x, y)` の前に `.scaled(s)` を挟むことで、テクスチャが `s` 倍拡大縮小されます。

例えば、`0.5` を指定すると、絵文字の大きさが 136x128 の半分の 68x64 で描かれます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/emoji/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji1{ U"🐈"_emoji };

	const Texture emoji2{ U"🍎"_emoji };

	while (System::Update())
	{
		emoji1.scaled(0.6).drawAt(100, 100);

		emoji2.scaled(0.3).drawAt(200, 300);

		emoji1.drawAt(400, 300);
	}
}
```


`.drawAt(x, y)` の前に `.rotated(angle)` を挟むと、テクスチャが時計回りに `angle` 度回転します。`angle` は 1 周 360° を 2π とするラジアンで指定します。`45_deg`, `90_deg` のように `_deg` を付けて表記すれば、度数法の角度をラジアンに変換してくれます。

| _deg 記法 | ラジアン |
| --- | --- |
| `0_deg` | 0.0 |
| `45_deg` | 0.78539816339 |
| `90_deg` | 1.57079632679 |
| `180_deg` | 3.14159265359 |
| `360_deg` | 6.28318530718 |


例えば、`10_deg` を指定すると、絵文字が時計回りに 10 度回転します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/emoji/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji1{ U"🐈"_emoji };

	const Texture emoji2{ U"🍎"_emoji };

	while (System::Update())
	{
		emoji1.rotated(10_deg).drawAt(100, 100);

		emoji2.rotated(180_deg).drawAt(200, 300);

		emoji1.rotated(45_deg).drawAt(400, 300);
	}
}
```

`.drawAt(x, y)` の前に `.mirrored(mirror)` を挟むと、`mirror` が `true` のときにテクスチャが左右反転されます。`false` の場合は元の向きが使われます。

次のコードは、マウスカーソルの X 座標を `Cursor::Pos().x` によって取得し、マウスカーソルが絵文字の右側にある場合、絵文字を左右反転して（猫を右に向けて）描画するサンプルです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/emoji/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji1{ U"🐈"_emoji };

	while (System::Update())
	{
		const int32 cursorX = Cursor::Pos().x;

		emoji1.mirrored(400 <= cursorX).drawAt(400, 300);
	}
}
```


!!! success "振り返りチェックリスト"
	- [x] 絵文字からテクスチャを作成する方法を学んだ
	- [x] テクスチャの作成はコストがかかるため、メインループの前で行うことを学んだ
	- [x] テクスチャを `.drawAt(x, y)`, `.drawAt(pos)` を使って指定した場所に描く方法を学んだ
	- [x] テクスチャを `.scaled(s)` を使って拡大縮小する方法を学んだ
	- [x] テクスチャを `.rotated(angle)` を使って回転させる方法を学んだ
	- [x] 度数法をラジアンに変換する `_deg` を使う方法を学んだ
	- [x] テクスチャを `.mirrored(mirrored)` を使って左右反転させる方法を学んだ


## 10. 文字列のフォーマット
`U"{}"_fmt(x)` と書くと、`{}` には値 `x` を文字列にしたものが入ります。

例えば `U"{} 月 {} 日"_fmt(12, 31)` は `U"12 月 31 日"` という文字列になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/text/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 score = 1234;

	Print << U"スコア: {}"_fmt(score);

	int32 month = 12;

	int32 day = 31;

	Print << U"今日は {} 月 {} 日"_fmt(month, day);

	while (System::Update())
	{

	}
}
```

`double` 型の値 `x` を、小数点以下の桁数を指定して変換する場合、`U"{:.2f}"_fmt(x)` のように書きます（この場合小数点以下 2 桁）。

小数点以下を表示しない場合は `U"{:.0f}"_fmt(x)` とします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/text/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	double x = 123.4567;

	Print << x;

	Print << U"{}"_fmt(x);

	Print << U"{:.2f}"_fmt(x);

	Print << U"{:.0f}"_fmt(x);

	while (System::Update())
	{

	}
}
```

!!! success "振り返りチェックリスト"
	- [x] `_fmt()` を使って数値を文字列に変換する方法を学んだ
	- [x] 小数点以下の桁数を指定して数値を文字列に変換する方法を学んだ


## 11. テキストを表示する
`Print` のような簡易表示ではなく、好きな位置に好きな色でテキストを表示したい場合は、`Font` クラスを使います。

まず、メインループの前に `Font 変数名{ FontMethod::MSDF, 48 };` でフォントを作成します。フォントの作成はコストがかかるため、**メインループの前**で行います。

作成したフォント `font` を使って、

- `font(テキスト).draw(サイズ, x, y, color);`
- `font(テキスト).draw(サイズ, pos, color);`

のようにして、テキストを、サイズ、位置、色を指定して表示します。`color` を省略すると白色になります。

`font(テキスト)` のテキストの部分は、文字列以外の値も記述できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/text/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	int32 count = 0;

	while (System::Update())
	{
		font(U"C++").draw(50, Vec2{ 100, 100 }, Palette::Black);

		font(U"Siv{}D"_fmt(count)).draw(80, Vec2{ 200, 200 }, ColorF{ 0.2, 0.6, 0.9 });

		font(U"こんにちは").draw(25, Vec2{ 100, 400 }, ColorF{ 0.4 });

		font(count).draw(50, Vec2{ 300, 500 });

		++count;
	}
}
```

??? example "フォントの品質"
	`FontMethod::MSDF` 方式でフォントを作成するときの `48` は、フォントデータの詳細度を表しています。この値は実行時性能とのトレードオフです。詳細度を大きくすると、メモリ消費が増加して処理時間が増えます。小さくすると、複雑な字形の文字の描画品質が低下する場合があります。漢字の場合は `48` がバランスの取れた値です。英数字のみの場合は `32` でも十分です。

太文字のフォントは `Font 変数名{ FontMethod::MSDF, 48, Typeface::Bold };` で作成できます。通常のフォントは `Typeface::Regular` ですが、これは省略できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/text/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font regularFont{ FontMethod::MSDF, 48 }; // Typeface::Regular

	// 太文字のフォント
	const Font boldFont{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		regularFont(U"Hello, Siv3D!").draw(50, Vec2{ 100, 100 }, ColorF{ 0.3 });

		boldFont(U"Hello, Siv3D!").draw(50, Vec2{ 100, 200 }, ColorF{ 0.3 });
	}
}
```

中心の座標を指定してテキストを表示するには `.drawAt(サイズ, x, y, color);` または  `.drawAt(サイズ, pos, color);` を呼びます。中心が (x, y), あるいは pos になるようにテキストが表示されます。

右端の中心の座標を指定してテキストを表示するには `.draw(サイズ, Arg::rightCenter(x, y), color);` を呼びます。右端の中心が (x, y) になるようにテキストが表示されます。

基準位置は全部で 9 種類用意されています。`Arg::rightCenter = Vec2{ x, y }` や `Arg::rightCenter(pos)` のように、`Vec2` で指定することもできます。

| 基準位置 | 説明 |
| --- | --- |
| `Arg::topLeft(x, y)` | 左上。`.draw()` と同じ。 |
| `Arg::topCenter(x, y)` | 上中央 |
| `Arg::topRight(x, y)` | 右上 |
| `Arg::leftCenter(x, y)` | 左中央 |
| `Arg::center(x, y)` | 中央。`.drawAt()` と同じ。 |
| `Arg::rightCenter(x, y)` | 右中央 |
| `Arg::bottomLeft(x, y)` | 左下 |
| `Arg::bottomCenter(x, y)` | 下中央 |
| `Arg::bottomRight(x, y)` | 右下 |


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/text/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	while (System::Update())
	{
		font(U"Hello").drawAt(50, Vec2{ 400, 100 }, ColorF{ 0.1 });

		font(U"Siv3D").draw(50, Arg::rightCenter(780, 300), ColorF{ 0.1 });

		font(U"Hello").draw(50, Arg::rightCenter(780, 400), ColorF{ 0.1 });

		font(U"programming").draw(50, Arg::bottomCenter(Cursor::Pos()), ColorF{ 0.1 });
	}
}
```

!!! success "振り返りチェックリスト"
	- [x] フォントを作成する方法を学んだ
	- [x] フォントの作成はコストがかかるため、メインループの前で行うことを学んだ
	- [x] フォントを使ってテキストを表示する方法を学んだ
	- [x] 太文字のフォントを作成する方法を学んだ
	- [x] テキストの基準位置を変更する方法を学んだ


## 12. キーボード入力
`if (キー名.down())` で、キーが押されたかを調べることができます。

??? info "主なキー名"
	- ++a++ , ++b++ , ++c++ , ... は `KeyA`, `KeyB`, `KeyC` , ...
	- ++1++ , ++2++ , ++3++ , ... は `Key1`, `Key2`, `Key3`, ...
	- ++f1++ , ++f2++ , ++f3++ , ... は `KeyF1`, `KeyF2`, `KeyF3`, ...
	- ++up++ , ++down++ , ++left++ , ++right++ は `KeyUp`, `KeyDown`, `KeyLeft`, `KeyRight`
	- ++space++ は `KeySpace`
	- ++enter++ は `KeyEnter`
	- ++backspace++ は `KeyBackspace`
	- ++tab++ キーは `KeyTab`
	- ++esc++ キーは `KeyEscape`
	- ++page-up++ , ++page-down++ は `KeyPageUp`, `KeyPageDown`
	- ++delete++ キーは `KeyDelete`
	- Numpad の ++num0++ , ++num1++ , ++num2++ , ... は `KeyNum0`, `KeyNum1`, `KeyNum2`, ...
	- ++shift++ は `KeyShift`
	- ++left-shift++ (左シフト), ++right-shift++ (右シフト) は `KeyLShift`, `KeyRShift`
	- ++control++ は `KeyControl`
	- (macOS) ++command++ は `KeyCommand`
	- ++comma++ , ++period++ , ++slash++ キーは `KeyComma`, `KeyPeriod`, `KeySlash`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/keyboard/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// A キーが押されたら
		if (KeyA.down())
		{
			Print << U"A";
		}

		// スペースキーが押されたら
		if (KeySpace.down())
		{
			Print << U"Space";
		}

		// 1 キーが押されたら
		if (Key1.down())
		{
			Print << U"1";
		}	
	}
}
```

`if (キー名.pressed())` で、キーが押されているかを調べることができます。`.down()` は押された瞬間のみ、`.pressed()` は押されている間ずっと `true` になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/keyboard/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// A キーが押されていたら
		if (KeyA.pressed())
		{
			Print << U"A";
		}

		// スペースキーが押されていたら
		if (KeySpace.pressed())
		{
			Print << U"Space";
		}

		// 1 キーが押されていたら
		if (Key1.pressed())
		{
			Print << U"1";
		}	
	}
}
```

矢印キーを使って絵文字を左右に移動させるには次のようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/keyboard/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

	// 移動の速さ（ピクセル / 秒）
	const double speed = 200;

	double x = 400;

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		// ← キーが押されていたら
		if (KeyLeft.pressed())
		{
			x -= (speed * deltaTime);
		}

		// → キーが押されていたら
		if (KeyRight.pressed())
		{
			x += (speed * deltaTime);
		}

		emoji.drawAt(x, 300);
	}
}
```

!!! success "振り返りチェックリスト"
	- [x] キーが押されたか調べるには `if (キー名.down())` を使うことを学んだ
	- [x] キーが押されているか調べるには `if (キー名.pressed())` を使うことを学んだ


## 13. マウス入力
`if (MouseL.down())` でマウスの左ボタンが押されたかを、`if (MouseR.down())` でマウスの右ボタンが押されたかを調べることができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/mouse/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			Print << U"左クリック";
		}

		// 右クリックされたら
		if (MouseR.down())
		{
			Print << U"右クリック";
		}
	}
}
```

マウスカーソルの座標を `Point` 型で得るには `Cursor::Pos()` を使います。`Point` 型の値は `Vec2` 型に変換できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/mouse/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			// 現在のマウスカーソルの座標を代入
			pos = Cursor::Pos();
		}

		emoji.drawAt(pos);
	}
}
```

X 座標、Y 座標をそれぞれ `Cursor::Pos().x`、`Cursor::Pos().y` で得ることもできます。前述のプログラムと次のプログラムは同じ動作をします。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

	double x = 400;

	double y = 300;

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			// 現在のマウスカーソルの X 座標を代入
			x = Cursor::Pos().x;

			// 現在のマウスカーソルの Y 座標を代入
			y = Cursor::Pos().y;
		}

		emoji.drawAt(x, y);
	}
}
```

`Circle` や `Rect`, `RectF` の `.leftClicked()` で、その図形が左クリックされたかを判定できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/mouse/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Circle circle{ 200, 200, 50 };

	const Rect rect{ 400, 400, 200, 40 };

	while (System::Update())
	{
		// 円を左クリックしたら
		if (circle.leftClicked())
		{
			Print << U"円をクリック";
		}

		// 長方形を左クリックしたら
		if (rect.leftClicked())
		{
			Print << U"長方形をクリック";
		}

		circle.draw(Palette::Orange);

		rect.draw();
	}
}
```

`Circle` や `Rect`, `RectF` の `.mouseOver()` で、マウスカーソルがその図形の上にあるかを判定できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/mouse/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Circle circle{ 200, 200, 50 };

	const Rect rect{ 400, 400, 200, 40 };

	while (System::Update())
	{
		ClearPrint();

		// 円の上にマウスカーソルがあれば
		if (circle.mouseOver())
		{
			Print << U"円の上にある";
		}

		// 長方形の上にマウスカーソルがあれば
		if (rect.mouseOver())
		{
			Print << U"長方形の上にある";
		}

		circle.draw(Palette::Orange);

		rect.draw();
	}
}
```

`Cursor::RequestStyle(CursorStyle::Hand);` を呼ぶと、そのフレームはマウスカーソルが手の形のアイコンで表示されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/mouse/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Circle circle{ 200, 200, 50 };

	while (System::Update())
	{
		// 円の上にマウスカーソルがあれば
		if (circle.mouseOver())
		{
			// マウスカーソルを手のアイコンにする
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		circle.draw(Palette::Orange);
	}
}
```

絵文字（テクスチャ）には `.leftClicked()` や `.mouseOver()` が無いため、代わりに近い大きさの円を使って判定します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/mouse/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"🍪"_emoji };

	const Circle circle{ 200, 200, 60 };

	while (System::Update())
	{
		// 円の上にマウスカーソルがあれば
		if (circle.mouseOver())
		{
			// マウスカーソルを手のアイコンにする
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// 円を左クリックしたら
		if (circle.leftClicked())
		{
			Print << U"クッキーをクリック";
		}

		// 円は描かない
		//circle.draw();

        // circle.center は Vec2{ circle.x, circle.y } と同じ
		emoji.drawAt(circle.center, Palette::Orange);
	}
}
```

!!! success "振り返りチェックリスト"
	- [x] `MouseL`, `MouseR` の `.down()` で、左クリック、右クリックされたかを調べることを学んだ
	- [x] `Cursor::Pos()` で、マウスカーソルの位置を得ることを学んだ
	- [x] `Circle` や `Rect`, `RectF` の `.leftClicked()` で、その図形が左クリックされたかを判定できることを学んだ
	- [x] `Circle` や `Rect`, `RectF` の `.mouseOver()` で、マウスカーソルがその図形の上にあるかを判定できることを学んだ
	- [x] `Cursor::RequestStyle(CursorStyle::Hand);` で、マウスカーソルを手の形にできることを学んだ
	- [x] 絵文字（テクスチャ）には `.leftClicked()` や `.mouseOver()` が無いため、代わりに近い大きさの円を使って判定するテクニックを学んだ


## 14. ボタンを作る

#### 14.1 関数の準備
ボタンの処理を行うための関数を作ります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/1.png)

```cpp hl_lines="3-6 12"
# include <Siv3D.hpp>

void Button()
{
	Rect{ 250, 300, 300, 80 }.draw(ColorF{ 0.3, 0.7, 1.0 });
}

void Main()
{
	while (System::Update())
	{
		Button();
	}
}
```


#### 14.2 領域を指定できるようにする
好きな場所に好きな大きさのボタンを作れるようにします。関数の引数は、`int32`, `bool`, `double` などの基本的な数値型以外はすべて **const 参照渡し** を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/2.png)

```cpp hl_lines="3 5 12 14"
# include <Siv3D.hpp>

void Button(const Rect& rect)
{
	rect.draw(ColorF{ 0.3, 0.7, 1.0 });
}

void Main()
{
	while (System::Update())
	{
		Button(Rect{ 250, 300, 300, 80 });

		Button(Rect{ 250, 400, 300, 80 });
	}
}
```


#### 14.3 テキストを指定できるようにする
ボタン内に表示するテキストを指定できるようにします。文字列は `String` 型です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/3.png)

```cpp hl_lines="3 7 12 16 18"
# include <Siv3D.hpp>

void Button(const Rect& rect, const Font& font, const String& text)
{
	rect.draw(ColorF{ 0.3, 0.7, 1.0 });

	font(text).drawAt(40, (rect.x + rect.w / 2), (rect.y + rect.h / 2));
}

void Main()
{
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		Button(Rect{ 250, 300, 300, 80 }, font, U"つづきから");

		Button(Rect{ 250, 400, 300, 80 }, font, U"最初から");
	}
}
```


#### 14.4 マウスカーソルを手のアイコンにする
ボタンの上にマウスカーソルを重ねると、マウスカーソルが手のアイコンに変わるようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/4.png)

```cpp hl_lines="5-8"
# include <Siv3D.hpp>

void Button(const Rect& rect, const Font& font, const String& text)
{
	if (rect.mouseOver())
	{
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	rect.draw(ColorF{ 0.3, 0.7, 1.0 });

	font(text).drawAt(40, (rect.x + rect.w / 2), (rect.y + rect.h / 2));
}

void Main()
{
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		Button(Rect{ 250, 300, 300, 80 }, font, U"つづきから");

		Button(Rect{ 250, 400, 300, 80 }, font, U"最初から");
	}
}
```

#### 14.5 押せるかどうかを指定できるようにする
押せないボタンを作ります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/5.png)

```cpp hl_lines="3 5 10-19 28 30"
# include <Siv3D.hpp>

void Button(const Rect& rect, const Font& font, const String& text, bool enabled)
{
	if (enabled && rect.mouseOver())
	{
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.7, 1.0 });
		font(text).drawAt(40, (rect.x + rect.w / 2), (rect.y + rect.h / 2));
	}
	else
	{
		rect.draw(ColorF{ 0.5 });
		font(text).drawAt(40, (rect.x + rect.w / 2), (rect.y + rect.h / 2), ColorF{ 0.7 });
	}
}

void Main()
{
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		Button(Rect{ 250, 300, 300, 80 }, font, U"つづきから", false);

		Button(Rect{ 250, 400, 300, 80 }, font, U"最初から", true);
	}
}
```

#### 14.6 押されたかどうかを返す
ボタンが押されたかを戻り値で返すようにします。押せないボタンは、押しても `false` を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/6.png)

```cpp hl_lines="3 21 30-38"
# include <Siv3D.hpp>

bool Button(const Rect& rect, const Font& font, const String& text, bool enabled)
{
	if (enabled && rect.mouseOver())
	{
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.7, 1.0 });
		font(text).drawAt(40, (rect.x + rect.w / 2), (rect.y + rect.h / 2));
	}
	else
	{
		rect.draw(ColorF{ 0.5 });
		font(text).drawAt(40, (rect.x + rect.w / 2), (rect.y + rect.h / 2), ColorF{ 0.7 });
	}

	return (enabled && rect.leftClicked());
}

void Main()
{
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		if (Button(Rect{ 250, 300, 300, 80 }, font, U"つづきから", false))
		{
			Print << U"つづきから";
		}

		if (Button(Rect{ 250, 400, 300, 80 }, font, U"最初から", true))
		{
			Print << U"最初から";
		}
	}
}
```

#### 14.7 絵文字を追加する
ボタンに絵文字を追加できるようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/7.png)

```cpp hl_lines="3 21 28-30 36 41"
# include <Siv3D.hpp>

bool Button(const Rect& rect, const Texture& emoji, const Font& font, const String& text, bool enabled)
{
	if (enabled && rect.mouseOver())
	{
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.7, 1.0 });
		font(text).drawAt(40, (rect.x + rect.w / 2 + 30), (rect.y + rect.h / 2));
	}
	else
	{
		rect.draw(ColorF{ 0.5 });
		font(text).drawAt(40, (rect.x + rect.w / 2 + 30), (rect.y + rect.h / 2), ColorF{ 0.7 });
	}

	emoji.scaled(0.5).drawAt(rect.x + 60, rect.y + 40);

	return (enabled && rect.leftClicked());
}

void Main()
{
	const Texture emojiBread{ U"🍞"_emoji };

	const Texture emojiRice{ U"🍚"_emoji };

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		if (Button(Rect{ 250, 300, 300, 80 }, emojiBread, font, U"パン", false))
		{
			Print << U"パン";
		}

		if (Button(Rect{ 250, 400, 300, 80 }, emojiRice, font, U"米", true))
		{
			Print << U"米";
		}
	}
}
```


## 15. クッキークリッカー
ここまで学んだことを使って、クッキークリッカー風のゲームを作ります。

!!! info "クッキークリッカーとは"
    クッキークリッカーとは、クッキーをクリックしてクッキーの数を増やしていくゲームです。増やしたクッキーは、クッキーを増やすためのアイテムを買うために使うことができます。2013 年にオリジナルのゲームが公開されて人気を博し、その後、様々なクッキークリッカー風のゲームが作られています。

    - [Cookie Clicker 公式ページ :material-open-in-new:](https://orteil.dashnet.org/cookieclicker/){:target="_blank"}
    - [Wikipedia: Cookie Clicker :material-open-in-new:](https://ja.wikipedia.org/wiki/%E3%82%AF%E3%83%83%E3%82%AD%E3%83%BC%E3%82%AF%E3%83%AA%E3%83%83%E3%82%AB%E3%83%BC){:target="_blank"}

#### 15.1 背景とクッキーを描く

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/1.png)

```cpp hl_lines="5-6 10-14"
# include <Siv3D.hpp>

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	while (System::Update())
	{
		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーを描画する
		texture.scaled(1.5).drawAt(170, 300);
	}
}
```


#### 15.2 クッキーの個数を表示する
この先のステップで、0.1 秒ごとにクッキーの枚数を更新するため、クッキーの枚数は整数ではなく小数で管理します。表示するときは `U"{:.0f}"` で小数以下は表示しないようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/2.png)

```cpp hl_lines="8-12 19-20"
# include <Siv3D.hpp>

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーの個数
	double cookies = 0;

	while (System::Update())
	{
		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーを描画する
		texture.scaled(1.5).drawAt(170, 300);
	}
}
```


#### 15.3 クッキーを押せるようにする
クッキーの領域に沿った `Circle` でマウス入力を処理します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/3.png)

```cpp hl_lines="11-12 19-29 38"
# include <Siv3D.hpp>

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの個数
	double cookies = 0;

	while (System::Update())
	{
		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			++cookies;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーを描画する
		texture.scaled(1.5).drawAt(cookieCircle.center);
	}
}
```


#### 15.4 クッキーを押したときのモーションを付ける
クッキーを左クリックしたときにクッキーのサイズを小さくし、時間の経過とともにサイズを元に戻します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/4.png)

```cpp hl_lines="14-15 31 35-41 50" 
# include <Siv3D.hpp>

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの表示サイズ（倍率）
	double cookieScale = 1.5;

	// クッキーの個数
	double cookies = 0;

	while (System::Update())
	{
		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			cookieScale = 1.3;
			++cookies;
		}

		// クッキーの表示サイズを回復する
		cookieScale += Scene::DeltaTime();

		if (1.5 < cookieScale)
		{
			cookieScale = 1.5;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーを描画する
		texture.scaled(cookieScale).drawAt(cookieCircle.center);
	}
}
```


#### 15.5 アイテムのボタンを作る (1)
アイテム用のボタンを処理する関数を作ります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/5.png)

```cpp hl_lines="3-31 38-42 88-92"
# include <Siv3D.hpp>

/// @brief アイテムのボタン
/// @param rect ボタンの領域
/// @param texture ボタンの絵文字
/// @param enabled ボタンを押せるか
/// @return ボタンが押された場合 true, それ以外の場合は false
bool Button(const Rect& rect, const Texture& texture, bool enabled)
{
	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.5, 0.9, 0.8 });

		rect.drawFrame(2, 2, ColorF{ 0.5, 0.7, 1.0 });

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}
	}
	else
	{
		rect.draw(ColorF{ 0.0, 0.4 });

		rect.drawFrame(2, 2, ColorF{ 0.5 });
	}

	texture.scaled(0.5).drawAt(rect.x + 50, rect.y + 50);

	return (enabled && rect.leftClicked());
}

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// 農場の絵文字
	const Texture farmEmoji{ U"🌾"_emoji };

	// 工場の絵文字
	const Texture factoryEmoji{ U"🏭"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの表示サイズ（倍率）
	double cookieScale = 1.5;

	// クッキーの個数
	double cookies = 0;

	while (System::Update())
	{
		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			cookieScale = 1.3;
			++cookies;
		}

		// クッキーの表示サイズを回復する
		cookieScale += Scene::DeltaTime();

		if (1.5 < cookieScale)
		{
			cookieScale = 1.5;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーを描画する
		texture.scaled(cookieScale).drawAt(cookieCircle.center);

		// 農場のボタン
		Button(Rect{ 340, 40, 420, 100 }, farmEmoji, true);

		// 工場のボタン
		Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, false);
	}
}
```


#### 15.6 アイテムのボタンを作る (2)
ボタンに仮の説明文と数字を加えます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/6.png)

```cpp hl_lines="6-9 12 34-38 98-102"
# include <Siv3D.hpp>

/// @brief アイテムのボタン
/// @param rect ボタンの領域
/// @param texture ボタンの絵文字
/// @param font 文字描画に使うフォント
/// @param name アイテムの名前
/// @param desc アイテムの説明
/// @param count アイテムの所持数
/// @param enabled ボタンを押せるか
/// @return ボタンが押された場合 true, それ以外の場合は false
bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
{
	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.5, 0.9, 0.8 });

		rect.drawFrame(2, 2, ColorF{ 0.5, 0.7, 1.0 });

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}
	}
	else
	{
		rect.draw(ColorF{ 0.0, 0.4 });

		rect.drawFrame(2, 2, ColorF{ 0.5 });
	}

	texture.scaled(0.5).drawAt(rect.x + 50, rect.y + 50);

	font(name).draw(30, rect.x + 100, rect.y + 15, Palette::White);

	font(desc).draw(18, rect.x + 102, rect.y + 60, Palette::White);

	font(count).draw(50, Arg::rightCenter((rect.x + rect.w - 20), (rect.y + 50)), Palette::White);

	return (enabled && rect.leftClicked());
}

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// 農場の絵文字
	const Texture farmEmoji{ U"🌾"_emoji };

	// 工場の絵文字
	const Texture factoryEmoji{ U"🏭"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの表示サイズ（倍率）
	double cookieScale = 1.5;

	// クッキーの個数
	double cookies = 0;

	while (System::Update())
	{
		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			cookieScale = 1.3;
			++cookies;
		}

		// クッキーの表示サイズを回復する
		cookieScale += Scene::DeltaTime();

		if (1.5 < cookieScale)
		{
			cookieScale = 1.5;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーを描画する
		texture.scaled(cookieScale).drawAt(cookieCircle.center);

		// 農場のボタン
		Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"クッキー農場", U"C10 / 1 CPS", 0, true);

		// 工場のボタン
		Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"クッキー工場", U"C100 / 10 CPS", 0, false);
	}
}
```


#### 15.7 変数とボタンの表示を連動させる
ゲームの状態とボタンの表示を連動させます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/7.png)

```cpp hl_lines="66-76 110-122"
# include <Siv3D.hpp>

/// @brief アイテムのボタン
/// @param rect ボタンの領域
/// @param texture ボタンの絵文字
/// @param font 文字描画に使うフォント
/// @param name アイテムの名前
/// @param desc アイテムの説明
/// @param count アイテムの所持数
/// @param enabled ボタンを押せるか
/// @return ボタンが押された場合 true, それ以外の場合は false
bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
{
	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.5, 0.9, 0.8 });

		rect.drawFrame(2, 2, ColorF{ 0.5, 0.7, 1.0 });

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}
	}
	else
	{
		rect.draw(ColorF{ 0.0, 0.4 });

		rect.drawFrame(2, 2, ColorF{ 0.5 });
	}

	texture.scaled(0.5).drawAt(rect.x + 50, rect.y + 50);

	font(name).draw(30, rect.x + 100, rect.y + 15, Palette::White);

	font(desc).draw(18, rect.x + 102, rect.y + 60, Palette::White);

	font(count).draw(50, Arg::rightCenter((rect.x + rect.w - 20), (rect.y + 50)), Palette::White);

	return (enabled && rect.leftClicked());
}

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// 農場の絵文字
	const Texture farmEmoji{ U"🌾"_emoji };

	// 工場の絵文字
	const Texture factoryEmoji{ U"🏭"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの表示サイズ（倍率）
	double cookieScale = 1.5;

	// クッキーの個数
	double cookies = 0;

	// 農場の所有数
	int32 farmCount = 0;

	// 工場の所有数
	int32 factoryCount = 0;

	// 農場の価格
	int32 farmCost = 10;

	// 工場の価格
	int32 factoryCost = 100;

	while (System::Update())
	{
		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			cookieScale = 1.3;
			++cookies;
		}

		// クッキーの表示サイズを回復する
		cookieScale += Scene::DeltaTime();

		if (1.5 < cookieScale)
		{
			cookieScale = 1.5;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーを描画する
		texture.scaled(cookieScale).drawAt(cookieCircle.center);

		// 農場ボタン
		if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"クッキー農場", U"C{} / 1 CPS"_fmt(farmCost), farmCount, (farmCost <= cookies)))
		{
			cookies -= farmCost;
			++farmCount;
		}

		// 工場ボタン
		if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"クッキー工場", U"C{} / 10 CPS"_fmt(factoryCost), factoryCount, (factoryCost <= cookies)))
		{
			cookies -= factoryCost;
			++factoryCount;
		}
	}
}
```


#### 15.8 クッキーの自動生産
アイテムの所有数に応じてクッキーが自動で増えるようにします。具体的には、0.1 秒ごとに、毎秒のクッキー生産量の 10% を獲得するようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/8.png)

```cpp hl_lines="78-79 83-96 125-126"
# include <Siv3D.hpp>

/// @brief アイテムのボタン
/// @param rect ボタンの領域
/// @param texture ボタンの絵文字
/// @param font 文字描画に使うフォント
/// @param name アイテムの名前
/// @param desc アイテムの説明
/// @param count アイテムの所持数
/// @param enabled ボタンを押せるか
/// @return ボタンが押された場合 true, それ以外の場合は false
bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
{
	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.5, 0.9, 0.8 });

		rect.drawFrame(2, 2, ColorF{ 0.5, 0.7, 1.0 });

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}
	}
	else
	{
		rect.draw(ColorF{ 0.0, 0.4 });

		rect.drawFrame(2, 2, ColorF{ 0.5 });
	}

	texture.scaled(0.5).drawAt(rect.x + 50, rect.y + 50);

	font(name).draw(30, rect.x + 100, rect.y + 15, Palette::White);

	font(desc).draw(18, rect.x + 102, rect.y + 60, Palette::White);

	font(count).draw(50, Arg::rightCenter((rect.x + rect.w - 20), (rect.y + 50)), Palette::White);

	return (enabled && rect.leftClicked());
}

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// 農場の絵文字
	const Texture farmEmoji{ U"🌾"_emoji };

	// 工場の絵文字
	const Texture factoryEmoji{ U"🏭"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの表示サイズ（倍率）
	double cookieScale = 1.5;

	// クッキーの個数
	double cookies = 0;

	// 農場の所有数
	int32 farmCount = 0;

	// 工場の所有数
	int32 factoryCount = 0;

	// 農場の価格
	int32 farmCost = 10;

	// 工場の価格
	int32 factoryCost = 100;

	// ゲームの経過時間の蓄積
	double accumulatedTime = 0.0;

	while (System::Update())
	{
		// クッキーの毎秒の生産量 (cookies per second) を計算する
		const int32 cps = (farmCount + factoryCount * 10);

		// ゲームの経過時間を加算する
		accumulatedTime += Scene::DeltaTime();

		// 0.1 秒以上蓄積していたら
		if (0.1 <= accumulatedTime)
		{
			accumulatedTime -= 0.1;

			// 0.1 秒分のクッキー生産を加算する
			cookies += (cps * 0.1);
		}

		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			cookieScale = 1.3;
			++cookies;
		}

		// クッキーの表示サイズを回復する
		cookieScale += Scene::DeltaTime();

		if (1.5 < cookieScale)
		{
			cookieScale = 1.5;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーの生産量を表示する
		font(U"毎秒: {}"_fmt(cps)).drawAt(24, 170, 160);

		// クッキーを描画する
		texture.scaled(cookieScale).drawAt(cookieCircle.center);

		// 農場ボタン
		if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"クッキー農場", U"C{} / 1 CPS"_fmt(farmCost), farmCount, (farmCost <= cookies)))
		{
			cookies -= farmCost;
			++farmCount;
		}

		// 工場ボタン
		if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"クッキー工場", U"C{} / 10 CPS"_fmt(factoryCost), factoryCount, (factoryCost <= cookies)))
		{
			cookies -= factoryCost;
			++factoryCount;
		}
	}
}
```


#### 15.9 （完成）アイテムのインフレを実装する
アイテムの所有数が増えたときに、アイテムの価格がインフレするようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/9.png)

```cpp hl_lines="98-102"
# include <Siv3D.hpp>

/// @brief アイテムのボタン
/// @param rect ボタンの領域
/// @param texture ボタンの絵文字
/// @param font 文字描画に使うフォント
/// @param name アイテムの名前
/// @param desc アイテムの説明
/// @param count アイテムの所持数
/// @param enabled ボタンを押せるか
/// @return ボタンが押された場合 true, それ以外の場合は false
bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
{
	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.5, 0.9, 0.8 });

		rect.drawFrame(2, 2, ColorF{ 0.5, 0.7, 1.0 });

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}
	}
	else
	{
		rect.draw(ColorF{ 0.0, 0.4 });

		rect.drawFrame(2, 2, ColorF{ 0.5 });
	}

	texture.scaled(0.5).drawAt(rect.x + 50, rect.y + 50);

	font(name).draw(30, rect.x + 100, rect.y + 15, Palette::White);

	font(desc).draw(18, rect.x + 102, rect.y + 60, Palette::White);

	font(count).draw(50, Arg::rightCenter((rect.x + rect.w - 20), (rect.y + 50)), Palette::White);

	return (enabled && rect.leftClicked());
}

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// 農場の絵文字
	const Texture farmEmoji{ U"🌾"_emoji };

	// 工場の絵文字
	const Texture factoryEmoji{ U"🏭"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの表示サイズ（倍率）
	double cookieScale = 1.5;

	// クッキーの個数
	double cookies = 0;

	// 農場の所有数
	int32 farmCount = 0;

	// 工場の所有数
	int32 factoryCount = 0;

	// 農場の価格
	int32 farmCost = 10;

	// 工場の価格
	int32 factoryCost = 100;

	// ゲームの経過時間の蓄積
	double accumulatedTime = 0.0;

	while (System::Update())
	{
		// クッキーの毎秒の生産量 (cookies per second) を計算する
		const int32 cps = (farmCount + factoryCount * 10);

		// ゲームの経過時間を加算する
		accumulatedTime += Scene::DeltaTime();

		// 0.1 秒以上蓄積していたら
		if (0.1 <= accumulatedTime)
		{
			accumulatedTime -= 0.1;

			// 0.1 秒分のクッキー生産を加算する
			cookies += (cps * 0.1);
		}

		// 農場の価格を計算する
		farmCost = 10 + (farmCount * 10);

		// 工場の価格を計算する
		factoryCost = 100 + (factoryCount * 100);

		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			cookieScale = 1.3;
			++cookies;
		}

		// クッキーの表示サイズを回復する
		cookieScale += Scene::DeltaTime();

		if (1.5 < cookieScale)
		{
			cookieScale = 1.5;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーの生産量を表示する
		font(U"毎秒: {}"_fmt(cps)).drawAt(24, 170, 160);

		// クッキーを描画する
		texture.scaled(cookieScale).drawAt(cookieCircle.center);

		// 農場ボタン
		if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"クッキー農場", U"C{} / 1 CPS"_fmt(farmCost), farmCount, (farmCost <= cookies)))
		{
			cookies -= farmCost;
			++farmCount;
		}

		// 工場ボタン
		if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"クッキー工場", U"C{} / 10 CPS"_fmt(factoryCost), factoryCount, (factoryCost <= cookies)))
		{
			cookies -= factoryCost;
			++factoryCount;
		}
	}
}
```

#### 15.10 さらに発展したクッキークリッカー
Siv3D の機能をより多く使って、クッキークリッカーをさらに発展させたサンプルです。

- [ゲームのサンプル | クッキークリッカー](https://siv3d.github.io/ja-jp/samples/games/#9-%E3%82%AF%E3%83%83%E3%82%AD%E3%83%BC%E3%82%AF%E3%83%AA%E3%83%83%E3%82%AB%E3%83%BC){:target="_blank"}
