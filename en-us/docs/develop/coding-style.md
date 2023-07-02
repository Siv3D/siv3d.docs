# コーディングスタイル

Siv3D のコーディングスタイルについて説明します。

## 変数
- 変数は camelCase

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

- constexpr 定数は PascalCase

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

## 関数
- 関数名は PascalCase

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


## クラス
- クラス名は PascalCase
- `public`  メンバのみを持つ場合は `struct`, それ以外は `class` を使う
- `class` の場合、必要でない限り `public:` → `protected:` → `private:` の順に記述する
- 非静的 `privete` メンバ変数は `m_` から始めて camelCase で続ける
- 非静的メンバ関数は camelCase 
- 静的メンバ関数は PascalCase
- 静的メンバ定数は PascalCase 

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


## 列挙型
- 列挙型名は PascalCase
- 列挙子は PascalCase
- `enum` より `enum class` を使う


## インデント
- タブ空白を用いる


## 字下げスタイル
- オールマンスタイルを用いる

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


## 比較演算子
- とくに理由がない場合 `>` と `>=` は使わない


## 浮動小数点数リテラル
- つねに . の前後に数字を書く

```cpp
double x = 1.0; // OK
double y = 1.; // NG
double z = .1; // NG
```

## 括弧
- 積極的に使う


## 代替トークン
- 論理否定演算子 `!` は `not` に置き換える

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


## インクリメント・デクリメント演算子
- とくに理由がない場合、前置演算子を使う


## 配列名
- 複数形にする
- 複数形が無い語は `~List` とする


## include
- `# include <...>`
- `# include "..."`


## インクルードガード
- `# pragma once`


## ソースファイル名
- PascalCase
- ソースファイルは `.cpp`
- ヘッダファイルは `.hpp`
- ヘッダファイルでインクルードする詳細実装のヘッダファイルは `.ipp`


