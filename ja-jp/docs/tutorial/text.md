# 15. テキストを表示する
色や位置を指定して、数値やテキストを画面に表示する方法を学びます。

## 15.1 数値を文字列に変換する（1）
- 変数の値を文字列に変換する際に最も便利なのは**フォーマット文字列**です
- `U"{}"_fmt(x)` と書くと、 `{}` には値 `x` を文字列にしたものが挿入されます
- 例えば `U"{} 月 {} 日"_fmt(month, day)` と書くと、 `month` と `day` の値が文字列に変換され、`U"12 月 31 日"` のような文字列が生成されます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/text/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 score = 12345;
	Print << U"スコア: {} 点"_fmt(score);

	int32 year = 2025;
	int32 month = 12;
	int32 day = 31;
	Print << U"{} 年 {} 月 {} 日"_fmt(year, month, day);

	while (System::Update())
	{
		
	}
}
```

- `Print` では次のように書くこともできますが、ひとまとまりの文字列として扱え、書式（**15.2** 参照）を制御できる `_fmt()` のほうが、今後のプログラムでは便利です

```cpp title="フォーマット文字列を使わない方法"
# include <Siv3D.hpp>

void Main()
{
	int32 score = 12345;
	Print << U"スコア: " << score << U" 点";

	int32 year = 2025;
	int32 month = 12;
	int32 day = 31;
	Print << year << U" 年 " << month << U" 月 " << day << U" 日";

	while (System::Update())
	{
		
	}
}
```


## 15.2 数値を文字列に変換する（2）
- フォーマット文字列には、さまざまな書式設定があります
- 浮動小数点数の値 `x` を、**小数点以下の桁数**を指定して変換する場合 `U"{:.2f}"_fmt(x)` のように書きます
	- この場合、小数点以下 2 桁まで（それ以降は四捨五入）の文字列が生成されます
	- 例えば、`U"{:.3f}"_fmt(3.141592)` は `U"3.142"` になります-
- 小数点以下を表示したくない場合は `U"{:.0f}"_fmt(x)` と書きます
	- 例えば、`U"{:.0f}"_fmt(3.141592)` は `U"3"` になります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/text/2.png)

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

- これ以外の書式設定については **チュートリアル 36** で詳しく説明します


## 15.3 フォントの作成
- `Print` を使えば簡単に文字列を画面に表示できますが、文字の位置や大きさ、色を変更することはできません
- より自由に文字列を描画するには、**フォント**（`Font` クラス）を使います
- フォントは、パソコン上のフォントファイルを読み込んで作成します
- フォントの最も簡単な作り方は、Siv3D に標準で同梱されているフォントファイルから作成することです
- 次のような簡単なコードで、同梱フォントファイルからフォントを作成できます

```cpp
Font font{ FontMethod::MSDF, 48 };
```

- 同梱フォントファイルのおかげで、Siv3D ではどのプラットフォーム（Windows, macOS, Linux, Web）でも同じ見た目の文字を描画できます


## 15.4 テキストを描く
- フォントを作成したら、`()` 演算子に文字列を渡し、次の方法で文字列を描画します
	- `.draw(フォントサイズ, pos, 色)` 
	- `.draw(フォントサイズ, x, y, 色)`
	- 座標は左上の位置です。色を省略した場合は白（`Palette::White`）が使われます
- 文字列には改行文字 `\n` を含めることができます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/text/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 同梱フォントファイルからフォントを作成する
	const Font font{ FontMethod::MSDF, 48 };

	while (System::Update())
	{
		font(U"Hello, Siv3D!").draw(80, Vec2{ 80, 100 }, ColorF{ 0.2 });

		font(U"C++\nProgramming").draw(60, Vec2{ 80, 300 });
	}
}
```

- `Font` のコンストラクタで与えている `48` はフォントの基本サイズ（詳細度）で、文字を大きく描くときの品質に影響します
- 実際の文字の大きさは `.draw()` の第一引数で指定している `80` や `60` です

!!! example "フォントの基本サイズと文字の品質"
	- `FontMethod::MSDF` 方式でフォントを作成するときの基本サイズ `48` は、フォントデータの詳細度を表します
	- この値は実行時性能とのトレードオフです
		- 詳細度を大きくすると、メモリ消費が増加して処理時間が増えます
		- 小さくすると、複雑な字形の文字で描画品質が低下する場合があります
	- 漢字の場合は `48` がバランスの取れた値です。英数字のみの場合は `32` でも十分です


## 15.5 太字のフォント
- Siv3D に同梱されているフォントは数種類あります
- とくに指定しない場合は `Typeface::Regular` が使われます
- `Typeface::Bold` を指定すると、**太字のフォント**を作成できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/text/5.png)

```cpp title="太字のフォントを作成する" hl_lines="8"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 同梱フォントファイルから太字のフォントを作成する
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		font(U"Hello, Siv3D!").draw(80, Vec2{ 80, 100 }, ColorF{ 0.2 });

		font(U"C++\nProgramming").draw(60, Vec2{ 80, 300 });
	}
}
```

- これ以外のフォントについては **チュートリアル 34** で詳しく説明します


## 15.6 中心位置を指定してテキストを描く
- 左上位置ではなく、**中心の座標**を指定してテキストを表示するには、次の方法を使います
	- `.drawAt(フォントサイズ, pos, color);`
	- `.drawAt(フォントサイズ, x, y, color);`
	- 中心座標が pos あるいは (x, y) になるようにテキストが表示されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/text/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	while (System::Update())
	{
		font(U"Hello").drawAt(60, Vec2{ 400, 300 }, ColorF{ 0.2 });

		font(U"Siv3D").drawAt(80, Cursor::Pos());
	}
}
```


## 15.7 それ以外の位置を基準にテキストを描く
- **右端の中心位置**を指定してテキストを表示するには、次の方法を使います
	- `.draw(フォントサイズ, Arg::rightCenter = pos, color);`
	- `.draw(フォントサイズ, Arg::rightCenter(x, y), color);`
	- 右端の中心座標が pos あるいは (x, y) になるようにテキストが表示されます
- 基準位置は全部で 9 種類あります

| 基準位置 | 説明 |
|---|---|
| `Arg::topLeft` | 左上。`.draw()` と同じ |
| `Arg::topCenter` | 上辺の中央 |
| `Arg::topRight` | 右上|
| `Arg::leftCenter` | 左辺の中央 |
| `Arg::center` | 中心。`.drawAt()` と同じ |
| `Arg::rightCenter` | 右辺の中央 |
| `Arg::bottomLeft` | 左下 |
| `Arg::bottomCenter` | 下辺の中央 |
| `Arg::bottomRight` | 右下 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/text/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	while (System::Update())
	{
		font(U"TopLeft").draw(40, Arg::topLeft(20, 20), ColorF{ 0.1 });
		font(U"TopRight").draw(40, Arg::topRight(780, 20), ColorF{ 0.1 });

		font(U"BottomLeft").draw(40, Arg::bottomLeft(20, 580), ColorF{ 0.1 });
		font(U"BottomRight").draw(40, Arg::bottomRight(780, 580), ColorF{ 0.1 });

		Rect{ 200, 100, 400, 200 }.draw(ColorF{ 0.8, 0.9, 1.0 });
		font(U"LeftCenter").draw(20, Arg::leftCenter(200, 200), ColorF{ 0.1 });
		font(U"RightCenter").draw(20, Arg::rightCenter(600, 200), ColorF{ 0.1 });

		// マウスカーソル位置を下辺中央とするようにテキストを描画する
		font(U"BottomCenter").draw(40, Arg::bottomCenter = Cursor::Pos(), ColorF{0.1});
	}
}
```


## 振り返りチェックリスト
- [x] フォーマット文字列 `U"{}"_fmt()` を使って、数値を文字列に変換する方法を学んだ
- [x] `U"{:.2f}"_fmt(x)` のように、小数点以下の桁数を指定して浮動小数点数を文字列に変換する方法を学んだ
- [x] Siv3D の同梱フォントファイルからフォントを作成する方法を学んだ
- [x] フォントの `.draw()` で文字列を画面に描画する方法を学んだ
- [x] `Typeface::Bold` を指定して太字のフォントを作成する方法を学んだ
- [x] `.drawAt()` で中心位置を指定して文字列を描画する方法を学んだ
- [x] `Arg::rightCenter` などを使って、9 種類の基準位置で文字列を描画する方法を学んだ

