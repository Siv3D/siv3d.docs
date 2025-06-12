# Integration of Custom Classes with Siv3D

## 1. Overview

This article explains how to integrate custom classes with various Siv3D features. Integration provides the following benefits:

- Can be used directly with `Print`, `Console`, etc.
- Can be used directly with `U"{}"_fmt()`
- Can be read from and written to configuration files like INI, JSON, CSV
- Can read and write binary data
- Can be used as keys in `HashSet` and `HashTable`


## 2. Format Support

Making a class "formattable" enables:

- Debug output to screen with `Print`
- Console output with `Console`
- Voice synthesis with `Say`
- Conversion to `String` with `Format()`
- Writing to `TextWriter`
- Writing to configuration files like `INI`, `JSON`, `CSV`
- `Font::operator()`

and more.


### 2.1 Method

Add the following function inside your class definition:

```cpp
friend void Formatter(FormatData& formatData, const CustomClass& value)
{

}
```

Inside this function, convert `value` to a string and add it to the `String` type member variable `.string` of `FormatData`.

```cpp title="Example"
friend void Formatter(FormatData& formatData, const RGB& value)
{
	formatData.string += U"({}, {}, {})"_fmt(value.r, value.g, value.b);
}
```

Now your custom class is "formattable".


### 2.2 Sample

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

	// Debug output to screen
	Print << rgb;

	// Console output
	Console << rgb;

	// Voice synthesis
	Say << rgb;

	// Conversion to String
	const String s = Format(rgb);

	// Writing to TextWriter
	{
		TextWriter writer{ U"test.txt" };
		writer << rgb;
	}

	// Writing to various configuration files like INI, JSON, CSV
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
		// Using with Font::operator()
		font(rgb).draw(100, 100);
	}
}
```


## 3. `_fmt` Support

Making a class compatible with `_fmt` allows you to stringify that class with `U"{}"_fmt()`.

### 3.1 Method

Define a specialization of `fmt::formatter` like the following in the global namespace. As an example, we'll make the previous `RGB` class compatible.

```cpp hl_lines="2 12 14" title="Example"
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


To also support special tags like `{:.2f}`, implement as follows:

```cpp hl_lines="14-23" title="Example"
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
		if (tag.empty()) // No special tag
		{
			return format_to(ctx.out(), U"({}, {}, {})", value.r, value.g, value.b);
		}
		else // Special tag present
		{
			const std::u32string format
				= (U"({:" + tag + U"}, {:" + tag + U"}, {:" + tag + U"})");
			return format_to(ctx.out(), format, value.r, value.g, value.b);
		}
	}
};
```

### 3.2 Sample

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
		if (tag.empty()) // No special tag
		{
			return format_to(ctx.out(), U"({}, {}, {})", value.r, value.g, value.b);
		}
		else // Special tag present
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


## 4. Parse Support

Making a class "parseable" enables:

- `Parse()`, `ParseOr()`, `ParseOpt()`
- Reading from configuration files like `INI`, `JSON`, `CSV`

and more.


### 4.1 Method

Add the following function inside your class definition:

```cpp
template <class CharType>
friend std::basic_istream<CharType>& operator >>(std::basic_istream<CharType>& input, CustomClass& value)
{

}
```

Inside this function, read values from the input stream `input`. It's desirable to have symmetric operations that can parse formatted strings.

```cpp title="Example"
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

Now your custom class is "parseable".


### 4.2 Sample

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

	// Reading from configuration files created in 2.3
	{
		INI ini{ U"test.ini" };
		const RGB x = Parse<RGB>(ini[U"aaa.color"]);
		Print << U"INI: " << x;
	}

	// Reading from configuration files created in 2.3
	{
		JSON json = JSON::Load(U"test.json");
		const RGB x = json[U"aaa"][U"color"].get<RGB>();
		Print << U"JSON: " << x;
	}

	// Reading from configuration files created in 2.3
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


## 5. Serialization Support

Making a class "serializable" enables use with:

- `Serializer`
- `Deserializer`


### 5.1 Method

Add the following member function inside your class definition:

```cpp
template <class Archive>
void SIV3D_SERIALIZE(Archive& archive)
{

}
```

Inside this function, pass each member variable as arguments to `archive()`. Each member variable must support serialization.

```cpp title="Example"
template <class Archive>
void SIV3D_SERIALIZE(Archive& archive)
{
	archive(r, g, b);
}
```


### 5.2 Sample

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
	// Save binary data to file
	{
		const RGB rgb{ 0.1f, 0.2f, 0.3f };
		const Array<RGB> colors = { RGB{ 0.2f, 0.3f, 0.4f }, RGB{ 0.5f, 0.6f, 0.7f } };

		Serializer<BinaryWriter> writer{ U"test.bin" };
		writer(rgb);
		writer(colors);
	}

	// Load binary data saved to file
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


## 6. Hash Support

Making a class hash-compatible allows it to be used as a key in `HashSet` and `HashTable`.

### 6.1 Method

Define the `==` operator and a specialization of `std::hash<>` for your class.

```cpp
friend bool operator ==(const CustomClass& a, const CustomClass& b) noexcept
{
	// Return true if a and b are equal
}

// Or

bool operator ==(const CustomClass&) const = default;
```

```cpp
template<>
struct std::hash<CustomClass>
{
	size_t operator()(const CustomClass& value) const noexcept
	{
		// Return hash value
	}
};
```

If the class is `Trivially Copyable`, you can use the `Hash::FNV1a()` function to generate hash values.


### 6.2 Sample

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
	table[Calendar{ 1, 1 }] = U"New Year's Day";
	table[Calendar{ 5, 5 }] = U"Children's Day";
	table[Calendar{ 11, 3 }] = U"Culture Day";

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