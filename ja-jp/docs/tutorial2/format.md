# 36. 数値と文字列の変換

## 36.1 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/format/1.png)

```cpp

```


## 36.2 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/format/2.png)

```cpp

```


## 36.3 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/format/3.png)

```cpp

```


## 36.4 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/format/4.png)

```cpp

```


## 36.5 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/format/5.png)

```cpp

```


## 36.6 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/format/6.png)

```cpp

```


## 36.7 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/format/7.png)

```cpp

```


## 36.8 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/format/8.png)

```cpp

```


## 36.9 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/format/9.png)

```cpp

```


## 36.10 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/format/10.png)

```cpp

```


## 36.11 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/format/11.png)

```cpp

```


## 36.12 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/format/12.png)

```cpp

```






数値データを文字列に変換する方法と、文字列を数値データに変換する方法を学びます。

## 27.1 数値から文字列への変換
`Format()` を使うと、**フォーマット可能**な型の値を `String` に変換できます。

フォーマット可能とは、その型の値を文字列に変換する方法が定義されていることを意味します。C++ の基本型や Siv3D の主要なクラスの多くがフォーマット可能です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/1.png)

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

	// (復習) Print は String でなくても使える
	Print << 12345;
	Print << colors;
	Print << Rect{ 30, 50, 100, 50 };

	while (System::Update())
	{

	}
}
```


## 27.2 自作クラスをフォーマット可能にする
自作クラス `X` をフォーマット可能にするには、次のようなメンバ関数 `Format` を定義します。自作クラスをフォーマット可能にすると、`Format()` だけでなく、`Print` など、様々な関数でもそのクラスを扱えるようになります。

```cpp
struct X
{
	friend void Formatter(FormatData& formatData, const X& value)
	{
		const String s = /* value を String に変換する処理 */;

		formatData.string.append(s);
	}
};
```

次のサンプルコードでは、`MyInt` と `RGB` という自作クラスをフォーマット可能にしています。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/2.png)

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


## 27.3 桁区切り記号を使って数値を文字列に変換する 
`ThousandsSeparate()` を使うと、桁区切り記号を挿入しながら数値を `String` に変換できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/3.png)

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


## 27.4 フォーマット指定子による数値から文字列への変換
文字列リテラルのあとに `_fmt()` サフィックスを付けると、文字列リテラル内に記述したフォーマット指定子 `{}` に、`( )` 内に記述した引数を文字列化して挿入できます。

### 27.4.1 基本
`U"{}"_fmt(x)` と書くと、`{}` には値 `x` を文字列にしたものが入ります。

文字列内で `{` や `}` の文字を扱いたい場合は、`{{` や `}}` と書く必要があります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/4.1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 n = 3;

	Print << U"Siv{}D"_fmt(n);

	Print << U"{}/{}/{}"_fmt(2023, 12, 31);

	Print << U"Hello, {}!"_fmt(U"Siv3D");

	Print << U"position: {}, color: {}"_fmt(Point{ 23, 45 }, ColorF{ 0.7, 0.8, 0.9 });

	// '{', '}' を使いたい場合は "{{", "}}" を使う 
	Print << U"{{abc}} {}"_fmt(123);

	while (System::Update())
	{

	}
}
```


### 27.4.2 インデックスの指定 
`{0}`, `{1}` のようにインデックスを記述すると、`_fmt()` 内の対応する引数を順序で指定できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/4.2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"{2}/{1}/{0}"_fmt(2023, 12, 31);

	Print << U"{0}/{1}/{2}"_fmt(2023, 12, 31);

	Print << U"C{0}{0}"_fmt(U'+');

	Print << U"{0} - {1} - {0}"_fmt(U"Tokyo", U"Osaka");

	while (System::Update())
	{

	}
}
```

### 27.4.3 String を使う
`Fmt(s)` 関数を使うことで、文字列リテラルの代わりにフォーマット指定子が記述された `String` の文字列を用いてフォーマットを実行できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/4.3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s1 = U"{2}/{1}/{0}";

	const String s2 = U"{0}/{1}/{2}";

	Print << Fmt(s1)(2023, 12, 31);

	Print << Fmt(s2)(2023, 12, 31);

	while (System::Update())
	{

	}
}
```


### 27.4.4 小数点以下の桁数 
浮動小数点数型の値 `x` を、小数点以下の桁数を指定して変換する場合、`U"{:.2f}"_fmt(x)` のように書きます（この場合小数点以下 2 桁）。小数点以下を表示しない場合は `U"{:.0f}"_fmt(x)` とします。桁数を明示的に指定しない変換では、その値の精度が失われない最短の桁数になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/4.4.png)

```cpp

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


### 27.4.5 パディング 
値の変換結果が最小 N 文字の幅になるようパティング文字を挿入できます。文字の左にパティング文字 c を挿入したい場合は `{:c>N}`, 右に挿入したい場合は `{:c<N}`, 左右に均等に挿入したい場合は `{:c^N}` と記述します。`c` を省略した場合は半角スペースが使われます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/4.5.png)

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


### 27.4.6 基数の指定 
整数を変換するとき、`{:X}` は大文字の 16 進数、`{:x}` は小文字の 16 進数、`{:o}` は 8 進数、`{:b}` は 2 進数に変換します。`#` を付けると基数に応じたプレフィックスが付きます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/4.6.png)

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


### 27.4.7 符号
`{:+}` は正の値に + 記号を付加し、`{: }` は正の値の前に半角空白を付加します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/4.7.png)

```cpp

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


## 27.5 自作クラスを _fmt() でフォーマット可能にする
自作クラスを `_fmt()` でフォーマット可能にするには、次のサンプルを参考に `fmt::formatter` の特殊化を行ってください。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/5.png)

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


## 27.6 文字列から数値への変換
`Parse()`, `ParseOr()`, `ParseOpt()` を使うと、文字列を指定した型の値に変換できます。変換するにはその型が**パース可能**である必要があります。配列のパースはサポートされていません。

### 27.6.1 Parse
`Parse<Type>(s)` は、文字列 `s` を `Type` 型の値に変換し、失敗した場合は例外 `ParseError` を投げます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/6.1.png)

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

### 27.6.2 ParseOr
`ParseOr<Type>(s, defaultValue)` は、文字列 `s` を `Type` 型の値に変換し、失敗した場合は代わりに `defaultValue` を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/6.2.png)

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


### 27.6.3 ParseOpt
`ParseOpt<Type>(s)` は、文字列 `s` を `Type` 型の値に変換し、`Optional` でラップして返します。変換に失敗した場合は代わりに無効値を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/6.3.png)

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


## 27.7 自作クラスをパース可能にする
自作クラスを `_fmt()` でフォーマット可能にするには、次のサンプルを参考に `operator >>` をオーバーロードしてます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/format/7.png)

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