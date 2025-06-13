# Coding Style

This page explains Siv3D's coding style.

## Variables
- Variables use camelCase

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	const Texture texture{ U"example/windmill.png" };

	while (System::Update())
	{

	}
}
```

- constexpr constants use PascalCase

```cpp
# include <Siv3D.hpp>

void Main()
{
	constexpr Size SceneSize{ 640, 480 };

	constexpr ColorF BackgroundColor{ 0.8, 0.9, 1.0 };

	while (System::Update())
	{

	}
}
```

## Functions
- Function names use PascalCase

```cpp
# include <Siv3D.hpp>

[[nodiscard]]
constexpr int32 Add(const int32 a, const int32 b) noexcept
{
	return (a + b);
}

void Main()
{
	Print << Add(10, 20);

	while (System::Update())
	{

	}
}
```


## Classes
- Class names use PascalCase
- Use `struct` if it only has `public` members, otherwise use `class`
- For `class`, write in the order `public:` → `protected:` → `private:` unless necessary
- Non-static `private` member variables start with `m_` and continue with camelCase
- Non-static member functions use camelCase
- Static member functions use PascalCase
- Static member constants use PascalCase

```cpp
# include <Siv3D.hpp>

class Button
{
public:

	Button() = default;

	Button(const String& label, const Vec2& pos, const Font& font, double fontSize = 20.0)
		: m_label{ label }
		, m_pos{ pos }
		, m_font{ font }
		, m_fontSize{ fontSize }
		, m_width{ m_font(label).region(fontSize).w + Padding * 2 } {}

	[[nodiscard]]
	RectF getRect() const noexcept
	{
		if (isEmpty())
		{
			return Rect::Empty();
		}

		return{ m_pos, m_width, ButtonHeight };
	}

	[[nodiscard]]
	bool pushed() const noexcept
	{
		if (isEmpty())
		{
			return false;
		}

		return getRect().leftClicked();
	}

	void draw() const
	{
		if (isEmpty())
		{
			return;
		}

		const RectF rect = getRect();

		const bool mouseOver = rect.mouseOver();

		rect.rounded(ButtonRadius).draw(mouseOver ? ButtonMouseOverColor : ButtonColor);

		m_font(m_label).drawAt(m_fontSize, rect.center(), ButtonLabelColor);

		if (mouseOver)
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}
	}

	[[nodiscard]]
	bool isEmpty() const noexcept
	{
		return (m_width == EmptyWidth);
	}

private:

	String m_label;

	Vec2 m_pos{ 0, 0 };

	Font m_font;

	double m_fontSize = 0.0;

	double m_width = EmptyWidth;

	static constexpr double EmptyWidth = 0.0;

	static constexpr double Padding = 20.0;

	static constexpr double ButtonRadius = 4.0;

	static constexpr int32 ButtonHeight = 40;

	static constexpr ColorF ButtonColor{ 0.8, 0.9, 1.0 };

	static constexpr ColorF ButtonMouseOverColor{ 0.9, 0.95, 1.0 };

	static constexpr ColorF ButtonLabelColor{ 0.11 };
};

void Main()
{
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	const Button button{ U"OK", Vec2{ 100, 100 }, font };

	while (System::Update())
	{
		if (button.pushed())
		{
			Print << U"OK";
		}

		button.draw();
	}
}
```


## Enums
- Enum names use PascalCase
- Enumerators use PascalCase
- Use `enum class` rather than `enum`


## Indentation
- Use tab characters


## Brace Style
- Use Allman style

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (MouseL.down())
		{
			Print << U"MouseL.down()";
		}
	}
}
```


## Comparison Operators
- Don't use `>` and `>=` unless there's a specific reason


## Floating-Point Literals
- Always write digits before and after the decimal point

```cpp
double x = 1.0; // OK
double y = 1.; // NG
double z = .1; // NG
```

## Parentheses
- Use them actively


## Alternative Tokens
- Replace the logical NOT operator `!` with `not`

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"example/windmill.png" };

	if (not texture)
	{
		return;
	}

	while (System::Update())
	{

	}
}
```


## Increment/Decrement Operators
- Use prefix operators unless there's a specific reason


## Array Names
- Use plural forms
- For words without plural forms, use `~List`


## include
- `# include <...>`
- `# include "..."`


## Include Guards
- `# pragma once`


## Source File Names
- PascalCase
- Source files use `.cpp`
- Header files use `.hpp`
- Header files for detailed implementation included by header files use `.ipp`


