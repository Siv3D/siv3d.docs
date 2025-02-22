# 21. 便利な関数
Siv3D プログラミングを便利にする、いくつかの小さな関数を学びます。

## 21.1 最小値・最大値
- `Min(a, b)` は `a` と `b` のうち小さい方を返します
- `Max(a, b)` は `a` と `b` のうち大きい方を返します
- 2 つの引数の型は同じである必要があります
- 2 つの引数の型が異なる場合は `Min<size_t>(a, b)` のように明示的に型を指定します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << Min(10, 20);
	Print << Max(10, 20);

	Print << Min(12.3, 45.6);
	Print << Max(12.3, 45.6);

	String s = U"Hello";
	Print << Min<size_t>(s.size(), 4);
	Print << Max<size_t>(s.size(), 4);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
10
20
12.3
45.6
4
5
```


## 21.2 指定した範囲に収める
- `Clamp(value, min, max)` は `value` を `[min, max]` の範囲に収めた値を返します
	- `Clamp(-20, 0, 100)` は `0` を返します
	- `Clamp(50, 0, 100)` は `50` を返します
	- `Clamp(120, 0, 100)` は `100` を返します
- 3 つの引数の型は同じである必要があります

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << Clamp(-20, 0, 100);
	Print << Clamp(50, 0, 100);
	Print << Clamp(120, 0, 100);

	while (System::Update())
	{

	}
}
```
``` txt title="出力"
0
50
100
```


## 21.3 範囲内であるかを調べる
- `InRange(value, min, max)` は `value` が `[min, max]` の範囲内にあるかを `bool` 型で返します
	- `InRange(50, 0, 100)` は `true` を返します
	- `InRange(120, 0, 100)` は `false` を返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << InRange(50, 0, 100);
	Print << InRange(120, 0, 100);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
true
false
```


## 21.4 奇数・偶数の判定
- `IsOdd(n)` は整数 `n` が奇数であるかを `bool` 型で返します
- `IsEven(n)` は整数 `n` が偶数であるかを `bool` 型で返します
	
```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << IsOdd(3);
	Print << IsOdd(4);

	Print << IsEven(3);
	Print << IsEven(4);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
true
false
false
true
```


## 21.5 絶対値
- `Abs(value)` は `value` の絶対値を返します
	- `Abs(-10)` は `10` を返します
	- `Abs(10)` は `10` を返します
	- `Abs(-3.14)` は `3.14` を返します
	- `Abs(3.14)` は `3.14` を返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << Abs(-10);
	Print << Abs(10);
	Print << Abs(-3.14);
	Print << Abs(3.14);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
10
10
3.14
3.14
```


## 21.6 差の絶対値
- `AbsDiff(a, b)` は `a` と `b` の差の絶対値を返します
	- `AbsDiff(10, 20)` は `10` を返します
	- `AbsDiff(20, 10)` は `10` を返します
	- `AbsDiff(3.14, 2.71)` は `0.43` を返します
	- `AbsDiff(2.71, 3.14)` は `0.43` を返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << AbsDiff(10, 20);
	Print << AbsDiff(20, 10);
	Print << AbsDiff(3.14, 2.71);
	Print << AbsDiff(2.71, 3.14);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
10
10
0.43
0.43
```


## 21.7 インデックス付き範囲 for 文
- 範囲 for 文において、`Indexed()` を使うと、整数のインデックスと範囲の各要素の両方を同時に扱えます

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<String> animals = { U"cat", U"dog", U"bird" };

	for (auto&& [i, animal] : Indexed(animals))
	{
		Print << U"{}: {}"_fmt(i, animal);
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
0: cat
1: dog
2: bird
```


## 21.8 文字の性質を調べる
- 文字（おもに ASCII 文字）の性質を調べる次のような関数があります

| 関数 | 説明 |
|--|--|
|`bool IsASCII(char32)`| ASCII 文字であるかを返す |
|`bool IsDigit(char32)`| 10 進数の数字であるかを返す |
|`bool IsLower(char32)`| アルファベットの小文字であるかを返す |
|`bool IsUpper(char32)`| アルファベットの大文字であるかを返す |
|`bool IsAlpha(char32)`| 文字がアルファベットであるかを返す |
|`bool IsAlnum(char32)`| 文字がアルファベットもしくは数字であるかを返す |
|`bool IsXdigit(char32)`| 文字が 16 進数の数字であるかを返す |
|`bool IsControl(char32)`| 文字が制御文字であるかを返す |
|`bool IsBlank(char32)`| 文字が空白文字 (`' '`, `'\t'`, および全角空白) であるかを返す |
|`bool IsSpace(char32)`| 文字が空白類文字 (`' '`, `'\t'`, `'\n'`, `'\v'`, `'\f'`, `'\r'`, および全角空白) であるかを返す |
|`bool IsPrint(char32)`| 文字が印字可能文字であるかを返す |
|`char32 ToLower(char32)`| アルファベットの大文字を小文字にする |
|`char32 ToUpper(char32)`| アルファベットの小文字を大文字にする |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << IsASCII(U'A') << U' ' << IsASCII(U'あ');
	Print << IsUpper(U'A') << U' ' << IsUpper(U'a');
	Print << IsAlnum(U'4') << U' ' << IsAlnum(U'#');
	Print << IsSpace(U' ') << U' ' << IsSpace(U'-');
	Print << ToLower(U'A') << U' ' << ToLower(U'a');

	while (System::Update())
	{

	}
}
```
```txt title="出力"
true false
true false
true false
true false
a a
```


## 21.9 数学定数
- 次のような数学定数が用意されています

=== "double 型"
    | 名前 | 説明 | 値 (実際より高い精度の桁数で示しています) |
    |--|--|--|
    | `Math::E` | 自然対数の底 | 2.718281828459045235360287471352662498 |
    | `Math::Log2E` | 2 を底とする e の対数 | 1.442695040888963407359924681001892137 |
    | `Math::Log10E` | 10 を底とする e の対数 | 0.434294481903251827651128918916605082 |
    | `Math::Pi` | π（円周率） | 3.141592653589793238462643383279502884 |
    | `Math::QuarterPi` | π/4 | 0.785398163397448309615660845819875721 |
    | `Math::OneThirdPi` | π/3 | 1.047197551196597746154214461093167628 |
    | `Math::HalfPi` | π/2 | 1.570796326794896619231321691639751442 |
    | `Math::TwoPi` | 2π | 6.283185307179586476925286766559005768 |
    | `Math::Tau` | τ（2π） | 6.283185307179586476925286766559005768 |
    | `Math::InvTwoPi` | 1/(2π) | 0.159154943091895335768883763372514362 |
    | `Math::InvPi` | 1/π | 0.318309886183790671537767526745028724 |
    | `Math::InvSqrtPi` | 1/√π | 0.564189583547756286948079451560772586 |
    | `Math::Ln2` | 2 の自然対数 | 0.693147180559945309417232121458176568 |
    | `Math::Ln10` | 10 の自然対数 | 2.302585092994045684017991454684364208 |
    | `Math::Sqrt2` | √2 | 1.414213562373095048801688724209698078 |
    | `Math::Sqrt3` | √3 | 1.732050807568877293527446341505872366 |
    | `Math::InvSqrt2` | 1/√2 | 0.707106781186547524400844362104849039 |
    | `Math::InvSqrt3` | 1/√3 | 0.577350269189625764509148780501957456 |
    | `Math::EGamma` | オイラーの定数 | 0.577215664901532860606512090082402431 |
    | `Math::Phi` | 黄金数 (φ) | 1.618033988749894848204586834365638117 |
    | `Math::QNaN` | Quiet NaN | QNaN |
    | `Math::NaN` | Signaling NaN | SNaN |
    | `Math::Inf` | Inf | Inf |

=== "float 型"
    | 名前 | 説明 | 値 (実際より高い精度の桁数で示しています) |
    |--|--|--|
    | `Math::PiF` | π（円周率） | 3.141592653589793238462643383279502884 |
    | `Math::QuarterPiF` | π/4 | 0.785398163397448309615660845819875721 |
    | `Math::OneThirdPiF` | π/3 | 1.047197551196597746154214461093167628 |
    | `Math::HalfPiF` | π/2 | 1.570796326794896619231321691639751442 |
    | `Math::TwoPiF` | 2π | 6.283185307179586476925286766559005768 |
    | `Math::TauF` | τ（2π） | 6.283185307179586476925286766559005768 |
    | `Math::InvTwoPiF` | 1/(2π) | 0.159154943091895335768883763372514362 |
    | `Math::InvPiF` | 1/π | 0.318309886183790671537767526745028724 |
    | `Math::InvSqrtPiF` | 1/√π | 0.564189583547756286948079451560772586 |
    | `Math::QNaNF` | Quiet NaN | QNaN |
    | `Math::NaNF` | Signaling NaN | SNaN |
    | `Math::InfF` | Inf | Inf |

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 円周率
	Print << Math::Pi;

	// 黄金比
	Print << Math::Phi;

	// NaN
	Print << Math::QNaN;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
3.14159
1.61803
nan
```


## 21.10 角度の表現
- C++ や Siv3D の API は角度をラジアンで扱いますが、人にとっての読みやすさのため、表記だけを別の単位にすることができます
- `_deg` というサフィックスを用いると、度数法で角度を表現できます
	- 例えば `90_deg` は `(90 * Math::Pi / 180.0)` になり、90° をラジアンで表現した値になります
- `_pi` というサフィックスを用いることで、π とのかけ算を省略できます
	- 例えば `0.5_pi` は `(0.5 * Math::Pi)` と同じです
- 円の半径に対する周長の比として定義される定数 τ を用いて、角度を表現できます。サフィックスは `_tau` です
	- 例えば `0.5_tau` は `(0.5 * Math::TwoPi)` と同じで、180° をラジアンで表現した値になります

| サフィックス | 説明 | 数値に乗算する値 |
| --- | --- | --- |
| `_deg` | 度数法 | Math::Pi / 180.0 |
| `_pi` | π | Math::Pi |
| `_tau` | τ | Math::TwoPi |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"{}"_fmt(180_deg);
	Print << U"{}"_fmt(1_pi);
	Print << U"{}"_fmt(0.5_tau);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
3.141592653589793
3.141592653589793
3.141592653589793
```


## 21.11 角度の正規化
- 角度（ラジアン）を正規化するには `Math::NormalizeAngle(radian, cenetr = Pi)` を使います
- 第 2 引数は正規化の中心角度です。省略すると `Pi` が使われます
	- 中心角度が Pi の場合の戻り値の範囲は `[0, 2π)` です
	- 中心角度が 0 の場合の戻り値の範囲は `[-π, π)` です

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double a = (Scene::Time() * -90_deg);
		const double b = (Scene::Time() * 90_deg);

		ClearPrint();

		Print << a;
		// 角度を [0.0, 2π) の範囲に正規化した値を返す
		Print << Math::NormalizeAngle(a);
		// 角度を [-π, π) の範囲に正規化した値を返す
		Print << Math::NormalizeAngle(a, 0.0);

		Print << U"----";

		Print << b;
		// 角度を [0.0, 2π) の範囲に正規化した値を返す
		Print << Math::NormalizeAngle(b);
		// 角度を [-π, π) の範囲に正規化した値を返す
		Print << Math::NormalizeAngle(b, 0.0);
	}
}
```
```txt title="出力例"
-3.86462
2.41857
2.41857
----
3.86462
3.86462
-2.41857
```


## 21.12 ラジアンと度数法の変換
- ラジアンから度数法への変換には `Math::ToDegrees(radian)` を使います
- 度数法からラジアンへの変換には `Math::ToRadians(degrees)` を使います

```cpp
# include <Siv3D.hpp>

void Main()
{
	const double angle = 45_deg;

	Print << angle;
	Print << Math::ToDegrees(angle);
	Print << Math::ToRadians(Math::ToDegrees(angle));

	while (System::Update())
	{

	}
}
```
```txt title="出力"
0.7854
45
0.7854
```


## 21.13 列挙型から整数への変換
- `FromEnum(enum)` を使うと、列挙型の値を整数に変換できます

```cpp
# include <Siv3D.hpp>

enum class State
{
	Menu,
	Game,
	Result
};

void Main()
{
	State state = State::Result;
	const int32 n = FromEnum(state);
	Print << n;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
2
```


## 21.14 整数から列挙型への変換
- `ToEnum<Enum>(i)` を使うと、整数を列挙型に変換できます

```cpp
# include <Siv3D.hpp>

enum class State
{
	Menu,
	Game,
	Result
};

void Main()
{
	const int32 n = 2;
	State state = ToEnum<State>(n);
	Print << (state == State::Result);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
true
```


## 21.15 エラー
- Siv3D のプログラムでエラーを伝える例外を送出したい場合、`Error` クラスを使うと便利です
- この例外が捕捉されなかった場合、Siv3D エンジンはエラーメッセージの内容をメッセージボックスに表示してプログラムを終了します
- Windows 版（Visual Studio）において、例外の発生箇所を IDE 上で表示する方法は、[例外の発生箇所の表示](../tools/msvc-exception.md) を参照してください

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"aaa.png" };

	if (not texture)
	{
		// 例外を送出する
		throw Error{ U"Failed to load `aaa.png`" };
	}

	while (System::Update())
	{

	}
}
```


## 21.16 コマンドライン引数の取得
- プログラムの起動時に渡されたコマンドライン引数を取得するには、`System::GetCommandLineArgs()` を使います

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<String> commands = System::GetCommandLineArgs();

	for (const auto& command : commands)
	{
		Print << command;
	}

	while (System::Update())
	{

	}
}
```


## 21.17 コンソール出力
- `Print` では追いきれないほどの出力データを可視化したい場合や、出力データをクリップボードにコピーしたい場合はコンソール出力が便利です
- `Print` の代わりに `Console` に向かって出力することで、コンソール出力ができます
- Windows の場合はコマンドプロンプトに出力されます

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10,20,30,40,50 };

	Print << v;

	// コンソール出力
	Console << v;

	Console << U"Hello, Siv3D!";

	while (System::Update())
	{

	}
}
```


## 21.18 ログ出力
- `Print` の代わりに `Logger` に向かって出力することで、ログ出力ができます
- Windows の場合は Visual Studio の「出力」ウィンドウに出力されます（デバッグ実行時）

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10,20,30,40,50 };

	Print << v;

	// ログ出力
	Logger << v;

	Logger << U"Hello, Siv3D!";

	while (System::Update())
	{

	}
}
```
