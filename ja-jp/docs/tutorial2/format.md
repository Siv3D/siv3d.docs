# 36. 数値と文字列の変換
数値 → 文字列の変換と、文字列 → 数値の変換の方法を学びます。

## 36.1 数値から文字列への変換
- `Format()` を使うと、**フォーマット可能**な型の値を `String` に変換できます
- C++ の基本型や Siv3D の主要なクラスの多くがフォーマット可能です
- 自作クラスをフォーマット可能にする方法は **36.2** で説明します

```cpp
# include <Siv3D.hpp>

void Main()
{
	// int32 から String への変換
	const String a = Format(12345);
	Print << a;

	// bool から String への変換
	const String b = Format(true);
	Print << b;

	// double から String への変換
	const String c = Format(1.23456789);
	Print << c;

	// Vec2 から String への変換
	const String d = Format(Vec2{ 11, 22 });
	Print << d;

	// Array から String への変換
	const Array<int32> values = { 3, 4, 5, 6 };
	const String e = Format(values);
	Print << e;

	// ColorF の std::array から String への変換
	const std::array<ColorF, 3> colors =
	{
		ColorF{ 1.0 , 0.0, 0.0 },
		ColorF{ 0.0 , 1.0, 0.0 },
		ColorF{ 0.0 , 0.0, 1.0 },
	};
	const String f = Format(colors);
	Print << f;

	// Rect から String への変換
	const String g = Format(Rect{ 30, 50, 100, 50 });
	Print << g;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
12345
true
1.23457
(11, 22)
{3, 4, 5, 6}
{(1, 0, 0, 1), (0, 1, 0, 1), (0, 0, 1, 1)}
(30, 50, 100, 50)
```

- フォーマット可能な型は、直接 `Print` に送ることができるため、`String` が必要でない場合は `Format()` を使う必要はありません


## 36.2 自作クラスをフォーマット可能にする
- 自作クラスをフォーマット可能にするには、次のようなメンバ関数 `Format` を定義します
- 自作クラスをフォーマット可能にすると、`Format()` に限らず、`Print` など、様々な出力系機能でそのクラスの値を直接出力できるようになります

```cpp
# include <Siv3D.hpp>

struct MyInt
{
	int32 value;

	friend void Formatter(FormatData& formatData, const MyInt& value)
	{
		formatData.string.append(Format(value.value));
	}
};

struct RGB
{
	uint8 r, g, b;

	friend void Formatter(FormatData& formatData, const RGB& value)
	{
		formatData.string.append(U"({}, {}, {})"_fmt(value.r, value.g, value.b));
	}
};

void Main()
{
	RGB rgb{ 0x88, 0xCC, 0xFF };
	Print << rgb;
	Print << Format(rgb);

	MyInt myInt{ 123 };
	Print << myInt;
	Print << Format(myInt);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
(136, 204, 255)
(136, 204, 255)
123
123
```


## 36.3 桁区切り記号を使って数値を文字列に変換する 
- 数値を、桁区切り記号を挿入しながら `String` に変換するには `ThousandsSeparate()` を使います

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << ThousandsSeparate(123456);
	Print << ThousandsSeparate(3333.3333, 2); // 小数点以下 2 桁まで
	Print << ThousandsSeparate(3333.3333, 4); // 小数点以下 4 桁まで

	while (System::Update())
	{

	}
}
```
```txt title="出力"
123,456
3,333.33
3,333.3333
```


## 36.4 _fmt の基本
- 文字列リテラルに `_fmt()` サフィックスを付けると、文字列リテラル内に記述したフォーマット指定子 `{}` に、`( )` 内に記述した引数を文字列化して挿入できます
- 文字列内で `{` や `}` の文字を扱いたい場合は、`{{` や `}}` でエスケープします

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 n = 3;

	Print << U"Siv{}D"_fmt(n);

	Print << U"{}/{}/{}"_fmt(2025, 12, 31);

	Print << U"Hello, {}!"_fmt(U"Siv3D");

	Print << U"position: {}, color: {}"_fmt(Point{ 23, 45 }, ColorF{ 0.7, 0.8, 0.9 });

	// '{', '}' を使いたい場合は "{{", "}}" を使う 
	Print << U"{{abc}} {}"_fmt(123);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
Siv3D
2025/12/31
Hello, Siv3D!
position: (23, 45), color: (0.7, 0.8, 0.9, 1)
{abc} 123
```


## 36.5 _fmt のインデックス指定
- `{0}`, `{1}` のように、フォーマット指定子内にインデックスを記述すると、`_fmt()` 内の対応する引数を順序で指定できます

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"{2}/{1}/{0}"_fmt(2025, 12, 31);

	Print << U"{0}/{1}/{2}"_fmt(2025, 12, 31);

	Print << U"C{0}{0}"_fmt(U'+');

	Print << U"{0} - {1} - {0}"_fmt(U"Tokyo", U"Osaka");

	while (System::Update())
	{

	}
}
```
```txt title="出力"
31/12/2025
2025/12/31
C++
Tokyo - Osaka - Tokyo
```


## 36.6 String を _fmt のように使う
- 文字列リテラルの代わりに `String` をフォーマット文字列として使いたい場合は、`Fmt(s)` 関数を使います

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s1 = U"{2}/{1}/{0}";
	const String s2 = U"{0}/{1}/{2}";

	Print << Fmt(s1)(2025, 12, 31);
	Print << Fmt(s2)(2025, 12, 31);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
31/12/2025
2025/12/31
```


## 36.7 _fmt の小数点以下桁数の指定
- フォーマット文字列には、さまざまな書式設定があります
- 浮動小数点数の値 `x` を、**小数点以下の桁数**を指定して変換する場合 `U"{:.2f}"_fmt(x)` のように書きます
	- この場合、小数点以下 2 桁まで（それ以降は四捨五入）の文字列が生成されます
	- 例えば、`U"{:.3f}"_fmt(3.141592)` は `U"3.142"` になります-
- 小数点以下を表示したくない場合は `U"{:.0f}"_fmt(x)` と書きます
	- 例えば、`U"{:.0f}"_fmt(3.141592)` は `U"3"` になります
- 桁数を明示的に指定しなかった場合、値の情報が失われない最短の桁数になります

```cpp
# include <Siv3D.hpp>

void Main()
{
	double x = 3.14159265;

	Print << U"{}"_fmt(x);

	Print << U"{:.3f}"_fmt(x);

	// インデックス指定と組み合わせる場合、インデックスは : の左
	Print << U"{1} ≒ {0:.6f}"_fmt(x, U"π");

	Print << U"{}"_fmt(12345.678);

	Print << U"{:.3f}"_fmt(12345.678);

	Print << U"{:.6f}"_fmt(12345.678);

	Print << U"{}"_fmt(9876543.21);

	Print << U"{:.0f}"_fmt(9876543.21);

	// Vec2 型にも使える
	Print << U"{}"_fmt(Vec2{ 1.111, 2.222 });
	Print << U"{:.1f}"_fmt(Vec2{ 1.111, 2.222 });

	while (System::Update())
	{

	}
}
```
```txt title="出力"
3.14159265
3.142
π ≒ 3.141593
12345.678
12345.678
12345.678000
9876543.21
9876543
(1.111, 2.222)
(1.1, 2.2)
```


## 36.8 _fmt パディング指定
- 値の変換結果が最小 N 文字の幅になるよう、パティング文字を挿入する書式設定が可能です
- 変換結果の左にパティング文字 c を挿入したい場合は `{:c>N}`, 右に挿入したい場合は `{:c<N}`, 左右に均等に挿入したい場合は `{:c^N}` と記述します
- パティング文字を省略した場合は半角空白が使われます

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"{:0>5}"_fmt(3);
	Print << U"{:>5}"_fmt(3);

	Print << U"{:>6}"_fmt(100);
	Print << U"{:*>6}"_fmt(100);
	Print << U"{:<6}"_fmt(100);
	Print << U"{:*<6}"_fmt(100);
	Print << U"{:^6}"_fmt(100);
	Print << U"{:*^6}"_fmt(100);

	Print << U"{:?>6}"_fmt(U"aaa");
	Print << U"{:?>6}"_fmt(U"aaabbb");
	Print << U"{:?>6}"_fmt(U"aaabbbccc");

	while (System::Update())
	{

	}
}
```
```txt title="出力"
00003
    3
   100
***100
100
100***
 100
*100**
???aaa
aaabbb
aaabbbccc
```


## 36.9 _fmt 基数の指定
- 整数を 2 進数、8 進数、16 進数に変換する書式設定が可能です

| 書式設定 | 説明 |
|---|---|
| `{:X}` | 大文字の 16 進数 |
| `{:x}` | 小文字の 16 進数 |
| `{:o}` | 8 進数 |
| `{:b}` | 2 進数 |

- `#` を付けると基数に応じたプレフィックスが付きます

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"{:X}"_fmt(255);
	Print << U"{:x}"_fmt(255);
	Print << U"{:o}"_fmt(255);
	Print << U"{:b}"_fmt(255);
	Print << U"{:#X}"_fmt(255);
	Print << U"{:#x}"_fmt(255);
	Print << U"{:#o}"_fmt(255);
	Print << U"{:#b}"_fmt(255);
	Print << U"0x{:08X}"_fmt(255);
	Print << U"0x{:08x}"_fmt(255);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
FF
ff
377
11111111
0XFF
0xff
0377
0b11111111
0x000000FF
0x000000ff
```


## 36.10 _fmt 符号表示の指定
- 符号の表示を指定する書式設定が可能です
- `{:+}` は正の値に + 記号を付加し、`{: }` は正の値の前に半角空白を付加します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"{}/{}"_fmt(-123, 123);
	Print << U"{:+}/{:+}"_fmt(-123, 123);
	Print << U"{: }/{: }"_fmt(-123, 123);
	Print << U"{}/{}"_fmt(0.5, -0.5);
	Print << U"{:+}/{:+}"_fmt(0.5, -0.5);
	Print << U"{: }/{: }"_fmt(0.5, -0.5);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
-123/123
-123/+123
-123/ 123
0.5/-0.5
+0.5/-0.5
 0.5/-0.5
```


## 36.11 自作クラスの _fmt 対応
- 自作クラスを `_fmt()` でフォーマット可能にするには、次のサンプルを参考に `fmt::formatter` の特殊化を行います

```cpp
# include <Siv3D.hpp>

struct MyInt
{
	int32 value;
};

template <>
struct fmt::formatter<MyInt, char32>
{
	std::u32string tag;

	auto parse(basic_format_parse_context<char32>& ctx)
	{
		return s3d::detail::GetFormatTag(tag, ctx);
	}

	template <class FormatContext>
	auto format(const MyInt& value, FormatContext& ctx)
	{
		if (tag.empty())
		{
			return format_to(ctx.out(), U"{}", value.value);
		}
		else
		{
			const std::u32string format = (U"{:" + tag + U"}");
			return format_to(ctx.out(), format, value.value);
		}
	}
};

struct RGB
{
	uint8 r, g, b;
};

template <>
struct fmt::formatter<RGB, char32>
{
	std::u32string tag;

	auto parse(basic_format_parse_context<char32>& ctx)
	{
		return s3d::detail::GetFormatTag(tag, ctx);
	}

	template <class FormatContext>
	auto format(const RGB& value, FormatContext& ctx)
	{
		if (tag.empty())
		{
			return format_to(ctx.out(), U"({}, {}, {})", value.r, value.g, value.b);
		}
		else
		{
			const std::u32string format
				= (U"({:" + tag + U"}, {:" + tag + U"}, {:" + tag + U"})");
			return format_to(ctx.out(), format, value.r, value.g, value.b);
		}
	}
};

void Main()
{
	const MyInt a{ 127 };
	Print << U"{}"_fmt(a);
	Print << U"{:X}"_fmt(a);

	const RGB b{ 255, 127, 0 };
	Print << U"{}"_fmt(b);
	Print << U"{:X}"_fmt(b);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
127
7F
(255, 127, 0)
(FF, 7F, 0)
```


## 36.12 Parse
- `Parse` を使うと、文字列から**パース可能**な型の値に変換できます
- 配列のパースはサポートされていません
- `Parse<Type>(s)` は、文字列 `s` を `Type` 型の値に変換します
- 変換に失敗した場合は例外 `ParseError` を投げます

```cpp
# include <Siv3D.hpp>

void Main()
{
	const int32 a = Parse<int32>(U"123");
	const double b = Parse<double>(U"-3.14159");
	const Point c = Parse<Point>(U"(10, 20)");

	Print << a;
	Print << b;
	Print << c;

	try
	{
		const Point d = Parse<Point>(U"(0,0)");
		const Point e = Parse<Point>(U"(20, 40)");
		const Point f = Parse<Point>(U"123"); // 失敗して例外を投げる
	}
	catch (const ParseError& error)
	{
		// 例外の詳細を表示する
		Print << error;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
123
-3.14159
(10, 20)
[ParseError] Parse<struct s3d::Point>("123") failed
```


## 36.13 ParseOr
- `ParseOr<Type>(s, defaultValue)` は、文字列 `s` を `Type` 型の値に変換します
- 失敗した場合は `defaultValue` を返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	const int32 a = ParseOr<int32>(U"123", -1);
	const int32 b = ParseOr<int32>(U"???", -1); // 失敗して defaultValue を返す
	const ColorF c = ParseOr<ColorF>(U"123", ColorF{ 0.0, 0.0 }); // 失敗して defaultValue を返す
	const Circle d = ParseOr<Circle>(U"(400, 300, 100)", Circle{ 0, 0, 0 });

	Print << a;
	Print << b;
	Print << c;
	Print << d;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
123
-1
(0, 0, 0, 0)
(400, 300, 100)
```


## 36.14 ParseOpt
- `ParseOpt<Type>(s)` は、文字列 `s` を `Type` 型の値に変換し、`Optional<Type>` 型の値で返します
- 変換に失敗した場合は無効値を返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Optional<int32> a = ParseOpt<int32>(U"123");
	const Optional<int32> b = ParseOpt<int32>(U"???"); // 失敗して無効値を返す
	const Optional<ColorF> c = ParseOpt<ColorF>(U"123"); // 失敗して無効値を返す
	const Optional<Circle> d = ParseOpt<Circle>(U"(400, 300, 100)");

	if (a)
	{
		Print << U"a: " << *a;
	}

	if (b)
	{
		Print << U"b: " << *b;
	}

	if (c)
	{
		Print << U"c: " << *c;
	}

	if (d)
	{
		Print << U"d: " << *d;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
a: 123
d: (400, 300, 100)
```


## 36.15 自作クラスをパース可能にする
- 自作クラスをパース可能にするには、出力ストリームに対する `operator >>` をオーバーロードします

```cpp
# include <Siv3D.hpp>

struct MyInt
{
	int32 value;

	friend void Formatter(FormatData& formatData, const MyInt& value)
	{
		formatData.string.append(Format(value.value));
	}

	template <class CharType>
	friend std::basic_istream<CharType>& operator >>(std::basic_istream<CharType>& input, MyInt& value)
	{
		return (input >> value.value);
	}
};

struct RGB
{
	uint8 r, g, b;

	friend void Formatter(FormatData& formatData, const RGB& value)
	{
		formatData.string.append(U"({}, {}, {})"_fmt(value.r, value.g, value.b));
	}

	template <class CharType>
	friend std::basic_istream<CharType>& operator >>(std::basic_istream<CharType>& input, RGB& value)
	{
		CharType unused;
		uint32 rgb[3];

		// () や , を読み飛ばす
		input >> unused >> rgb[0] >> unused >> rgb[1] >> unused >> rgb[2] >> unused;

		value.r = static_cast<uint8>(rgb[0]);
		value.g = static_cast<uint8>(rgb[1]);
		value.b = static_cast<uint8>(rgb[2]);

		return input;
	}
};

void Main()
{
	const MyInt a = Parse<MyInt>(U"123");
	const RGB b = Parse<RGB>(U"(255, 127, 0)");

	Print << a;
	Print << b;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
123
(255, 127, 0)
```

