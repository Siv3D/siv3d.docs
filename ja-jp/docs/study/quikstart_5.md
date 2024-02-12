# 勉強会 5 コマ目（テキストの表示）

## 1. 文字列のフォーマット

### 1.1 文字列の中に変数の値をいれる
`U"{}"_fmt(x)` と書くと、`{}` には値 `x` を文字列にしたものが入ります。

`U"{} 月 {} 日"_fmt(12, 31)` は `U"12 月 31 日"` という文字列になります。

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


### 1.2 小数点以下の桁数を指定して変換する
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


## 2. テキストを表示する

### 2.1 テキストの自由な表示
好きな位置に好きな色でテキストを表示したい場合は、`Font` クラスを使います。まず、メインループの前に `Font 変数名{ FontMethod::MSDF, 48 };` でフォントを作成します。フォントの作成はコストがかかるため、**メインループの前**で行います。

作成したフォント `font` を使って、

- `font(テキスト).draw(サイズ, x, y, color);`
- `font(テキスト).draw(サイズ, pos, color);`
	- `pos` は `Vec2{ x, y }` で、`Vec2` は 2 次元の座標を表すクラスです。

のようにして、テキストを、サイズ、位置、色を指定して表示します。`color` を省略すると白色になります。「テキスト」の部分は、文字列以外に数値などもそのまま渡せます。

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


### 2.2 太文字のテキスト
太文字のフォントは `Font 変数名{ FontMethod::MSDF, 48, Typeface::Bold };` で作成します。2.1 のような通常のフォントでは `Typeface::Regular` が指定されています。

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


### 2.3 テキストの基準位置
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


### 2.4 テキストを指定した長方形内に描く
`Font::draw()` に、座標の代わりに `Rect` または `RectF` を渡すと、テキストをその長方形の中に収まるように描画します。テキストのすべての文字が長方形内に収まった場合、関数は `true` を返します。一方、テキストがあふれる場合、最後の文字が `…` に置き換えられ、関数は `false` を返します。

`Rect` の `.stretched(t)` を使うと、長方形の上下左右をそれぞれ t ピクセル拡大（負の場合は縮小）した長方形を得ることができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	const String text = U"Siv3D (シブスリーディー) は、ゲームやアプリを楽しく簡単な C++ コードで開発できるフレームワークです。";

	const Rect rect1{ 100, 100, 400, 160 };

	const Rect rect2{ 100, 300, 400, 160 };

	while (System::Update())
	{
		rect1.draw();

		font(text).draw(18, rect1, Palette::Black);

		rect2.draw();

		// rect2.stretched(-20) は rect2 を上下左右 20 ピクセル縮めた Rect
		rect2.stretched(-20).draw(ColorF{ 0.9 });

		font(text).draw(18, rect2.stretched(-20), Palette::Black);
	}
}
```

### 2.5 文字に輪郭や影をつける
文字に影や輪郭をつけるには、次のいずれかのテキストスタイルを使います。輪郭スケールの単位はピクセルではありません。最大でも 0.2 が目安です。

- `TextStyle::Outline(輪郭スケール, 輪郭の色)`
- `TextStyle::Shadow(影のオフセット, 影の色)`
- `TextStyle::OutlineShadow(輪郭スケール, 輪郭の色, 影のオフセット, 影の色)`

影のオフセットが大きい場合、影が途切れてしまうことがあります。それを防ぐには `Font` のメンバ関数 `.setBufferThickness(影のための余裕サイズ)` で、影の領域を大きめに確保しておきます。デフォルトは 2 ですが、4 にすると安全です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });

	const Font font{ FontMethod::SDF, 48, Typeface::Bold };

	// 文字の影のための余白を大きめに確保
	font.setBufferThickness(4);

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		// 輪郭
		font(text).draw(TextStyle::Outline(0.2, ColorF{ 0.1 }), 60, Vec2{ 40, 100 });

		// 影
		font(text).draw(TextStyle::Shadow(Vec2{ 1.5, 1.5 }, ColorF{ 0.1 }), 60, Vec2{ 40, 200 });

		// 輪郭＋影
		font(text).draw(TextStyle::OutlineShadow(0.2, ColorF{ 0.1 }, Vec2{ 1.5, 1.5 }, ColorF{ 0.1 }), 60, Vec2{ 40, 300 });
	}
}
```






