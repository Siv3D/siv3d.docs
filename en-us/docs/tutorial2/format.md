# 36. Number and String Conversion
Learn methods for converting numbers → strings and strings → numbers.

## 36.1 Converting Numbers to Strings
- Use `Format()` to convert values of **formattable** types to `String`
- Most basic C++ types and major Siv3D classes are formattable
- How to make custom classes formattable is explained in **36.2**

```cpp
# include <Siv3D.hpp>

void Main()
{
	// int32 to String conversion
	const String a = Format(12345);
	Print << a;

	// bool to String conversion
	const String b = Format(true);
	Print << b;

	// double to String conversion
	const String c = Format(1.23456789);
	Print << c;

	// Vec2 to String conversion
	const String d = Format(Vec2{ 11, 22 });
	Print << d;

	// Array to String conversion
	const Array<int32> values = { 3, 4, 5, 6 };
	const String e = Format(values);
	Print << e;

	// ColorF std::array to String conversion
	const std::array<ColorF, 3> colors =
	{
		ColorF{ 1.0 , 0.0, 0.0 },
		ColorF{ 0.0 , 1.0, 0.0 },
		ColorF{ 0.0 , 0.0, 1.0 },
	};
	const String f = Format(colors);
	Print << f;

	// Rect to String conversion
	const String g = Format(Rect{ 30, 50, 100, 50 });
	Print << g;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
12345
true
1.23457
(11, 22)
{3, 4, 5, 6}
{(1, 0, 0, 1), (0, 1, 0, 1), (0, 0, 1, 1)}
(30, 50, 100, 50)
```

- Formattable types can be sent directly to `Print`, so you don't need to use `Format()` when `String` is not required


## 36.2 Making Custom Classes Formattable
- To make custom classes formattable, define a member function `Format` as follows:
- Making custom classes formattable allows you to output class values directly with not only `Format()` but also `Print` and various other output functions

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
```txt title="Output"
(136, 204, 255)
(136, 204, 255)
123
123
```


## 36.3 Converting Numbers to Strings with Thousands Separators
- To convert numbers to `String` while inserting thousands separators, use `ThousandsSeparate()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << ThousandsSeparate(123456);
	Print << ThousandsSeparate(3333.3333, 2); // Up to 2 decimal places
	Print << ThousandsSeparate(3333.3333, 4); // Up to 4 decimal places

	while (System::Update())
	{

	}
}
```
```txt title="Output"
123,456
3,333.33
3,333.3333
```


## 36.4 _fmt Basics
- Adding the `_fmt()` suffix to string literals allows you to insert string-converted arguments from `( )` into format specifiers `{}` written in the string literal
- To use `{` or `}` characters in strings, escape them with `{{` or `}}`

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 n = 3;

	Print << U"Siv{}D"_fmt(n);

	Print << U"{}/{}/{}"_fmt(2025, 12, 31);

	Print << U"Hello, {}!"_fmt(U"Siv3D");

	Print << U"position: {}, color: {}"_fmt(Point{ 23, 45 }, ColorF{ 0.7, 0.8, 0.9 });

	// Use "{{", "}}" for '{', '}' characters
	Print << U"{{abc}} {}"_fmt(123);

	while (System::Update())
	{

	}
}
```
```txt title="Output"
Siv3D
2025/12/31
Hello, Siv3D!
position: (23, 45), color: (0.7, 0.8, 0.9, 1)
{abc} 123
```


## 36.5 _fmt Index Specification
- Writing indices like `{0}`, `{1}` in format specifiers allows you to specify corresponding arguments in `_fmt()` by order

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
```txt title="Output"
31/12/2025
2025/12/31
C++
Tokyo - Osaka - Tokyo
```


## 36.6 Using String Like _fmt
- To use `String` as a format string instead of string literals, use the `Fmt(s)` function

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
```txt title="Output"
31/12/2025
2025/12/31
```


## 36.7 _fmt Decimal Places Specification
- Format strings have various formatting options
- To convert floating-point value `x` with specified **decimal places**, write like `U"{:.2f}"_fmt(x)`
	- This generates a string with up to 2 decimal places (rounded beyond that)
	- For example, `U"{:.3f}"_fmt(3.141592)` becomes `U"3.142"`
- To not display decimal places, write `U"{:.0f}"_fmt(x)`
	- For example, `U"{:.0f}"_fmt(3.141592)` becomes `U"3"`
- When decimal places are not explicitly specified, the shortest number of digits that doesn't lose value information is used

```cpp
# include <Siv3D.hpp>

void Main()
{
	double x = 3.14159265;

	Print << U"{}"_fmt(x);

	Print << U"{:.3f}"_fmt(x);

	// When combining with index specification, the index goes left of :
	Print << U"{1} ≒ {0:.6f}"_fmt(x, U"π");

	Print << U"{}"_fmt(12345.678);

	Print << U"{:.3f}"_fmt(12345.678);

	Print << U"{:.6f}"_fmt(12345.678);

	Print << U"{}"_fmt(9876543.21);

	Print << U"{:.0f}"_fmt(9876543.21);

	// Also works with Vec2 type
	Print << U"{}"_fmt(Vec2{ 1.111, 2.222 });
	Print << U"{:.1f}"_fmt(Vec2{ 1.111, 2.222 });

	while (System::Update())
	{

	}
}
```
```txt title="Output"
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


## 36.8 _fmt Padding Specification
- Formatting can insert padding characters to ensure conversion results have a minimum width of N characters
- To insert padding character c to the left of conversion results use `{:c>N}`, to the right use `{:c<N}`, evenly to both sides use `{:c^N}`
- If padding character is omitted, half-width space is used

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
```txt title="Output"
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


## 36.9 _fmt Base Specification
- Formatting can convert integers to binary, octal, and hexadecimal

| Format Setting | Description |
|---|---|
| `{:X}` | Uppercase hexadecimal |
| `{:x}` | Lowercase hexadecimal |
| `{:o}` | Octal |
| `{:b}` | Binary |

- Adding `#` includes prefixes according to the base

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
```txt title="Output"
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


## 36.10 _fmt Sign Display Specification
- Formatting can specify sign display
- `{:+}` adds + symbol to positive values, `{: }` adds half-width space before positive values

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
```txt title="Output"
-123/123
-123/+123
-123/ 123
0.5/-0.5
+0.5/-0.5
 0.5/-0.5
```


## 36.11 _fmt Support for Custom Classes
- To make custom classes formattable with `_fmt()`, specialize `fmt::formatter` as shown in the following sample

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
```txt title="Output"
127
7F
(255, 127, 0)
(FF, 7F, 0)
```


## 36.12 Parse
- Use `Parse` to convert strings to values of **parseable** types
- Array parsing is not supported
- `Parse<Type>(s)` converts string `s` to a value of type `Type`
- Throws `ParseError` exception if conversion fails

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
		const Point f = Parse<Point>(U"123"); // Fails and throws exception
	}
	catch (const ParseError& error)
	{
		// Display exception details
		Print << error;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
123
-3.14159
(10, 20)
[ParseError] Parse<struct s3d::Point>("123") failed
```


## 36.13 ParseOr
- `ParseOr<Type>(s, defaultValue)` converts string `s` to a value of type `Type`
- Returns `defaultValue` if it fails

```cpp
# include <Siv3D.hpp>

void Main()
{
	const int32 a = ParseOr<int32>(U"123", -1);
	const int32 b = ParseOr<int32>(U"???", -1); // Fails and returns defaultValue
	const ColorF c = ParseOr<ColorF>(U"123", ColorF{ 0.0, 0.0 }); // Fails and returns defaultValue
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
```txt title="Output"
123
-1
(0, 0, 0, 0)
(400, 300, 100)
```


## 36.14 ParseOpt
- `ParseOpt<Type>(s)` converts string `s` to a value of type `Type` and returns an `Optional<Type>` value
- Returns an invalid value if conversion fails

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Optional<int32> a = ParseOpt<int32>(U"123");
	const Optional<int32> b = ParseOpt<int32>(U"???"); // Fails and returns invalid value
	const Optional<ColorF> c = ParseOpt<ColorF>(U"123"); // Fails and returns invalid value
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
```txt title="Output"
a: 123
d: (400, 300, 100)
```


## 36.15 Making Custom Classes Parseable
- To make custom classes parseable, overload `operator >>` for the output stream

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

		// Skip (), and ,
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
```txt title="Output"
123
(255, 127, 0)
```