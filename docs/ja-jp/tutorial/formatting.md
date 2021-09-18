description: OpenSiv3D のチュートリアル

!!! warning "このページよりも新しいドキュメントがあります"
	このドキュメントは古い OpenSiv3D v0.4.3 向けです。2021 年9 月 18 日に最新の OpenSiv3D v0.6.0 がリリースされました。最新のドキュメントは [Siv3D リファレンス v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation) です。

# 7. 文字列と数値の変換

この章では、数値データを文字列に変換する方法と、文字列を数値データに変換する方法を学びます。

## 7.1 数値から文字列への変換
`Format()` を使うと、文字列への変換に対応したあらゆるデータを `String` に変換できます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/7/1-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const String a = Format(12345);

	const String b = Format(true);

	const String c = Format(1.23456789);

	const String d = Format(Vec2(11, 22));

	const Array<int32> values = { 3, 4, 5, 6 };
	const String e = Format(values);

	const std::array<ColorF, 3> colors =
	{
		ColorF(1.0 , 0.0, 0.0),
		ColorF(0.0 , 1.0, 0.0),
		ColorF(0.0 , 0.0, 1.0),
	};
	const String f = Format(colors);

	const String g = Format(Rect(30, 50, 100, 50));

	Print << a;
	Print << b;
	Print << c;
	Print << d;
	Print << e;
	Print << f;
	Print << g;

	// ただし、Print では、わざわざ String にする必要はない
	Print << 12345;
	Print << colors;
	Print << Rect(30, 50, 100, 50);

	while (System::Update())
	{

	}
}
```


## 7.2 フォーマット指定子を使った、数値から文字列への変換
文字列リテラルのあとに `_fmt()` サフィックスを付けると、文字列リテラル内に記述した `{}` という箇所に、`( )` 内に記述した引数が文字列化されて挿入されます。

### 基本

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/7/2-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Print << U"Siv{}D"_fmt(3);

	Print << U"{}/{}/{}"_fmt(2020, 12, 31);

	Print << U"Hello, {}!"_fmt(U"Siv3D");

	Print << U"position: {}, color: {}"_fmt(Point(23, 45), ColorF(0.7, 0.8, 0.9));

	while (System::Update())
	{

	}
}
```

### インデックスの指定
`{0}`, `{1}` のように、`_fmt()` の引数のインデックスを指定できます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/7/2-1.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Print << U"{2}/{1}/{0}"_fmt(2020, 12, 31);

	Print << U"{0}/{1}/{2}"_fmt(2020, 12, 31);

	Print << U"C{0}{0}"_fmt(U'+');

	Print << U"{0} - {1} - {0}"_fmt(U"Tokyo", U"Osaka");

	while (System::Update())
	{

	}
}
```

### 小数点以下の桁数
小数点以下 N 桁を変換したい場合は `{:.Nf}` と記述します。指定しなかった場合は有効桁数 6 桁で変換されます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/7/2-2.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Print << U"{}"_fmt(Math::Pi);

	Print << U"{:.3f}"_fmt(Math::Pi);

	// インデックス指定と組み合わせる場合、インデックス値は : の左
	Print << U"{1} ≒ {0:.6f}"_fmt(Math::Pi, U"π"); 

	Print << U"{}"_fmt(12345.678);

	Print << U"{:.3f}"_fmt(12345.678);

	Print << U"{:.6f}"_fmt(12345.678);

	Print << U"{}"_fmt(9876543.21);

	Print << U"{:.0f}"_fmt(9876543.21);

	while (System::Update())
	{

	}
}
```

### パディング
N 文字の幅になるよう、文字の左にパティング文字 c を挿入したい場合は `{:c>N}` 、右に挿入したい場合は `{:c<N}`, 左右に均等に挿入したい場合は `{:c^N}` と記述します。c を省略した場合半角スペースとみなされます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/7/2-3.png?raw=true)

```C++
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

### 基数
`{:X}` は大文字の十六進数、`{:x}` は小文字の十六進数、`{:o}` は八進数、`{:b}` は二進数に変換します。`#` を付けるとプレフィックスが付きます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/7/2-4.png?raw=true)

```C++
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

	while (System::Update())
	{

	}
}
```

### 符号
`{:+}` は正の値に + 記号を付加し、`{: }` は正の値に半角空白を付加します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/7/2-5.png?raw=true)

```C++
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

変換指定子についてより詳細に知りたい場合は [{fmt} Format String Syntax](https://fmt.dev/latest/syntax.html) を参照してください。

## 7.3 文字列から数値への変換
`Parse<Type>()` 関数テンプレートは、引数の文字列を `Type` 型の値に変換し、失敗した場合は例外 `ParseError` を投げます。`ParseOr<Type>()` 関数テンプレートは、変換に失敗した場合例外を投げる代わりに第二引数の値を返します。

### Parse の基本

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/7/3-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const int32 a = Parse<int32>(U"123");

	const double b = Parse<double>(U"-3.14159");

	const Point c = Parse<Point>(U"(10, 20)");

	Print << a;
	Print << b;
	Print << c;

	const int32 d = ParseOr<int32>(U"???", -1);

	const ColorF e = ParseOr<ColorF>(U"123", ColorF(0.0, 0.0));

	const Circle f = ParseOr<Circle>(U"", Circle(0, 0, 0));

	Print << d;
	Print << e;
	Print << f;

	while (System::Update())
	{

	}
}
```

### ParseError の捕捉
複数の `Parse` を使う場合、変換の失敗の検出には例外を使うと便利です。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/7/3-1.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	try
	{
		const Point a = Parse<Point>(U"(0,0)");
		const Point b = Parse<Point>(U"(20, 40)");
		const Point c = Parse<Point>(U"123");
	}
	catch (const ParseError& e)
	{
		// 例外の詳細を取得して表示
		Print << e.what();
	}

	while (System::Update())
	{

	}
}
```

このほかにも、変換の成否を `Optional<Type>` で返す `ParseOpt<Type>()` 関数テンプレートがありますが、このチュートリアルでは詳しく説明しません。
