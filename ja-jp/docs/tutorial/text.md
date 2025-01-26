# 15. テキストを表示する
色や位置を指定して、数値やテキストを画面に表示する方法を学びます。

## 15.1 数値を文字列に変換する（1）
- 変数の値を文字列に変換する際に最も便利なのはフォーマット文字列です
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

```cpp title="_fmt() を使わない場合"
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
- フォーマット文字列 `_fmt()` には、さまざまな書式設定があります
- 浮動小数点数の値 `x` を、小数点以下の桁数を指定して変換する場合 `U"{:.2f}"_fmt(x)` のように書きます
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

- これ以外の書式設定については **チュートリアル ??** で詳しく説明します


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
- フォントを作成したら、`()` 演算子に文字列を渡し、`.draw(フォントサイズ, 左上の位置, 色)` で文字列を描画します
- 文字列には改行文字 `\n` を含めることができます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/text/4.png)

```cpp
#include <Siv3D.hpp>

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
- `Typeface::Bold` を指定すると、太字のフォントを作成できます

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

## 15.6 テキストの基準位置


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/text/6.png)

```cpp

```

## 振り返りチェックリスト



