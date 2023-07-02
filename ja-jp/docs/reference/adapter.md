# 自作クラスと Siv3D の連係

## 1. 概要

本記事では、自作クラスを Siv3D の様々な機能と連係させる方法を説明します。連係によって以下のようなメリットが得られます。

- `Print` や `Console` などで直接扱えるようになる
- `U"{}"_fmt()` で直接扱えるようになる
- INI や JSON, CSV などの設定ファイルで読み書きできるようになる
- バイナリデータを読み書きできるようになる
- `HashSet` や `HashTable` のキーとして使えるようになる


## 2. フォーマット対応

クラスを「フォーマット可能」にすると、

- `Print` による画面へのデバッグ出力
- `Console` によるコンソール出力
- `Say` による音声読み上げ
- `Format()` による `String` への変換
- `TextWriter` への書き出し
- `INI`, `JSON`, `CSV` などの設定ファイルへの書き出し
- `Font::operator()`

などができるようになります。


### 2.1 方法

クラスの定義内に、次のような関数を追加します。

```cpp
friend void Formatter(FormatData& formatData, const 自作クラス& value)
{

}
```

この関数内で `value` を文字列化し、`FormatData` の `String` 型のメンバ変数 `.string` に追加します。

```cpp title="例"
friend void Formatter(FormatData& formatData, const RGB& value)
{
	formatData.string += U"({}, {}, {})"_fmt(value.r, value.g, value.b);
}
```

これで自作クラスが「フォーマット可能」になりました。


### 2.2 サンプル

```cpp
# include <Siv3D.hpp>

struct RGB
{
	float r, g, b;

	friend void Formatter(FormatData& formatData, const RGB& value)
	{
		formatData.string += U"({}, {}, {})"_fmt(value.r, value.g, value.b);
	}
};

void Main()
{
	const RGB rgb{ 0.1f, 0.2f, 0.3f };

	// 画面へのデバッグ出力
	Print << rgb;

	// コンソール出力
	Console << rgb;

	// 音声読み上げ
	Say << rgb;

	// String への変換
	const String s = Format(rgb);

	// TextWriter への書き込み
	{
		TextWriter writer{ U"test.txt" };
		writer << rgb;
	}

	// INI, JSON, CSV 等各種設定ファイルへの書き込み
	{
		INI ini;
		ini[U"aaa.color"] = rgb;
		ini.save(U"test.ini");

		JSON json;
		json[U"aaa"][U"color"] = rgb;
		json.save(U"test.json");

		CSV csv;
		csv.writeRow(U"item", U"color");
		csv.writeRow(U"aaa", rgb);
		csv.save(U"test.csv");
	}

	const Font font{ 64 };

	while (System::Update())
	{
		// Font::operator() での使用
		font(rgb).draw(100, 100);
	}
}
```


## 3. `_fmt` 対応

クラスを `_fmt` に対応させると、そのクラスを `U"{}"_fmt()` で文字列化できるようになります。

### 3.1 方法

グローバル名前空間に次のような `fmt::formatter` の特殊化を定義します。例として、先ほどの `RGB` クラスを対応させます。

```cpp hl_lines="2 12 14" title="例"
template <>
struct SIV3D_HIDDEN fmt::formatter<RGB, s3d::char32>
{
	std::u32string tag;

	auto parse(basic_format_parse_context<s3d::char32>& ctx)
	{
		return s3d::detail::GetFormatTag(tag, ctx);
	}

	template <class FormatContext>
	auto format(const RGB& value, FormatContext& ctx)
	{
		return format_to(ctx.out(), U"({}, {}, {})", value.r, value.g, value.b);
	}
};
```


`{:.2f}` のような特殊なタグにも対応させる場合は次のように実装します。

```cpp hl_lines="14-23" title="例"
template <>
struct SIV3D_HIDDEN fmt::formatter<RGB, s3d::char32>
{
	std::u32string tag;

	auto parse(basic_format_parse_context<s3d::char32>& ctx)
	{
		return s3d::detail::GetFormatTag(tag, ctx);
	}

	template <class FormatContext>
	auto format(const RGB& value, FormatContext& ctx)
	{
		if (tag.empty()) // 特殊タグが無い場合
		{
			return format_to(ctx.out(), U"({}, {}, {})", value.r, value.g, value.b);
		}
		else // 特殊タグがある場合
		{
			const std::u32string format
				= (U"({:" + tag + U"}, {:" + tag + U"}, {:" + tag + U"})");
			return format_to(ctx.out(), format, value.r, value.g, value.b);
		}
	}
};
```

### 3.2 サンプル

```cpp
# include <Siv3D.hpp>

struct RGB
{
	float r, g, b;
};

template <>
struct SIV3D_HIDDEN fmt::formatter<RGB, s3d::char32>
{
	std::u32string tag;

	auto parse(basic_format_parse_context<s3d::char32>& ctx)
	{
		return s3d::detail::GetFormatTag(tag, ctx);
	}

	template <class FormatContext>
	auto format(const RGB& value, FormatContext& ctx)
	{
		if (tag.empty()) // 特殊タグが無い場合
		{
			return format_to(ctx.out(), U"({}, {}, {})", value.r, value.g, value.b);
		}
		else // 特殊タグがある場合
		{
			const std::u32string format
				= (U"({:" + tag + U"}, {:" + tag + U"}, {:" + tag + U"})");
			return format_to(ctx.out(), format, value.r, value.g, value.b);
		}
	}
};

void Main()
{
	const RGB rgb{ 0.111f, 0.222f, 0.333f };

	Print << U"color: {}"_fmt(rgb);

	Print << U"color: {:.1f}"_fmt(rgb);

	while (System::Update())
	{

	}
}
```


## 4. パース対応

クラスを「パース可能」にすると、

- `Parse()`, `ParseOr()`, `ParseOpt()`
- `INI`, `JSON`, `CSV` などの設定ファイルからの読み込み

などができるようになります。


### 4.1 方法

クラスの定義内に、次のような関数を追加します。

```cpp
template <class CharType>
friend std::basic_istream<CharType>& operator >>(std::basic_istream<CharType>& input, 自作クラス& value)
{

}
```

この関数内で、入力ストリーム `input` から値を読み込みます。フォーマットした文字列をパースできるような対称的な操作になることが望ましいです。

```cpp title="例"
template <class CharType>
friend std::basic_istream<CharType>& operator >>(std::basic_istream<CharType>& input, RGB& value)
{
	CharType unused;
	return input >> unused
		>> value.r >> unused
		>> value.g >> unused
		>> value.b >> unused;
}
```

これで自作クラスが「パース可能」になりました。


### 4.2 サンプル

```cpp
# include <Siv3D.hpp>

struct RGB
{
	float r, g, b;

	friend void Formatter(FormatData& formatData, const RGB& value)
	{
		formatData.string += U"({}, {}, {})"_fmt(value.r, value.g, value.b);
	}

	template <class CharType>
	friend std::basic_istream<CharType>& operator >>(std::basic_istream<CharType>& input, RGB& value)
	{
		CharType unused;
		return input >> unused
			>> value.r >> unused
			>> value.g >> unused
			>> value.b >> unused;
	}
};


void Main()
{
	const RGB rgb{ 0.1f, 0.2f, 0.3f };

	const String s = Format(rgb);

	const RGB rgb2 = Parse<RGB>(s);

	Print << rgb2;

	// 2.3 で作成した設定ファイルから読み込む
	{
		INI ini{ U"test.ini" };
		const RGB x = Parse<RGB>(ini[U"aaa.color"]);
		Print << U"INI: " << x;
	}

	// 2.3 で作成した設定ファイルから読み込む
	{
		JSON json = JSON::Load(U"test.json");
		const RGB x = json[U"aaa"][U"color"].get<RGB>();
		Print << U"JSON: " << x;
	}

	// 2.3 で作成した設定ファイルから読み込む
	{
		CSV csv{ U"test.csv" };
		const RGB x = csv.get<RGB>(1, 1);
		Print << U"CSV: " << x;
	}

	while (System::Update())
	{

	}
}
```


## 5. シリアライズ対応

クラスを「シリアライズ可能」にすると、

- `Serializer`
- `Deserializer`

で使えるようになります。


### 5.1 方法

クラスの定義内に、次のようなメンバ関数を追加します。

```cpp
template <class Archive>
void SIV3D_SERIALIZE(Archive& archive)
{

}
```

この関数内で、各メンバ変数を `archive()` に引数として渡します。各メンバ変数はシリアライズ対応している必要があります。

```cpp title="例"
template <class Archive>
void SIV3D_SERIALIZE(Archive& archive)
{
	archive(r, g, b);
}
```


### 5.2 サンプル

```cpp
# include <Siv3D.hpp>

struct RGB
{
	float r, g, b;

	friend void Formatter(FormatData& formatData, const RGB& value)
	{
		formatData.string += U"({}, {}, {})"_fmt(value.r, value.g, value.b);
	}

	template <class Archive>
	void SIV3D_SERIALIZE(Archive& archive)
	{
		archive(r, g, b);
	}
};


void Main()
{
	// バイナリデータをファイルに保存
	{
		const RGB rgb{ 0.1f, 0.2f, 0.3f };
		const Array<RGB> colors = { RGB{ 0.2f, 0.3f, 0.4f }, RGB{ 0.5f, 0.6f, 0.7f } };

		Serializer<BinaryWriter> writer{ U"test.bin" };
		writer(rgb);
		writer(colors);
	}

	// ファイルに保存したバイナリデータを読み込む
	{
		RGB rgb;
		Array<RGB> colors;

		Deserializer<BinaryReader> reader{ U"test.bin" };
		reader(rgb);
		reader(colors);

		Print << rgb;
		Print << colors;
	}

	while (System::Update())
	{

	}
}
```


## 6. ハッシュ対応

クラスをハッシュ対応させると、`HashSet` や `HashTable` のキーとして使えるようになります。

### 6.1 方法

クラスに `==` 演算子と、`std::hash<>` の特殊化を定義します。

```cpp
friend bool operator ==(const 自作クラス& a, const 自作クラス& b) noexcept
{
	// a と b が等しい場合 true を返す
}

// または

bool operator ==(const 自作クラス&) const = default;
```

```cpp
template<>
struct std::hash<自作クラス>
{
	size_t operator()(const 自作クラス& value) const noexcept
	{
		// ハッシュ値を返す
	}
};
```

クラスが `Trivially Copyable` であれば、ハッシュ値の生成には `Hash::FNV1a()` 関数を使うことができます。


### 6.2 サンプル

```cpp
# include <Siv3D.hpp>

struct Calendar
{
	int16 month;

	int16 day;

	bool operator ==(const Calendar&) const = default;
};

template<>
struct std::hash<Calendar>
{
	size_t operator()(const Calendar& value) const noexcept
	{
		return Hash::FNV1a(value);
	}
};

void Main()
{
	HashTable<Calendar, String> table;
	table[Calendar{ 1, 1 }] = U"元旦";
	table[Calendar{ 5, 5 }] = U"こどもの日";
	table[Calendar{ 11, 3 }] = U"文化の日";

	const Calendar calendar{ 5, 5 };

	if (auto it = table.find(calendar); it != table.end())
	{
		Print << it->second;
	}

	while (System::Update())
	{

	}
}
```
