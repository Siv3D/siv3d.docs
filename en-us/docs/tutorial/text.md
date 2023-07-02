# 11. テキストを表示する
色や位置を指定して数値やテキストを表示する方法を学びます。

## 11.1 数値を文字列に変換する (1)
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

`Print` では次のように書くこともできますが、ひとまとまりの文字列として扱える `_fmt()` を使うほうが今後のプログラムで便利です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 score = 1234;

	Print << U"スコア: " << score;

	int32 month = 12;

	int32 day = 31;

	Print << U"今日は " << month << U" 月 " << day << U" 日";

	while (System::Update())
	{

	}
}
```


## 11.2 数値を文字列に変換する (2)
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

## 11.3 テキストを表示する
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


## 11.4 太文字のテキストを表示する
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


## 11.5 テキストの基準位置を変更する
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


## 振り返りチェックリスト
- [x] `_fmt()` を使って数値を文字列に変換する方法を学んだ
- [x] 小数点以下の桁数を指定して数値を文字列に変換する方法を学んだ
- [x] フォントを作成する方法を学んだ
- [x] フォントの作成はコストがかかるため、メインループの前で行うことを学んだ
- [x] フォントを使ってテキストを表示する方法を学んだ
- [x] 太文字のフォントを作成する方法を学んだ
- [x] テキストの基準位置を変更する方法を学んだ
