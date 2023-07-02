# 15. ボタンを作る
ここまで学んだことを使って、ボタンを作る練習をします。

## 15.1 関数の準備
ボタンの処理を行うための関数を作ります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/1.png)

```cpp hl_lines="3-6 12"
# include <Siv3D.hpp>

void Button()
{
	Rect{ 250, 300, 300, 80 }.draw(ColorF{ 0.3, 0.7, 1.0 });
}

void Main()
{
	while (System::Update())
	{
		Button();
	}
}
```


## 15.2 領域を指定できるようにする
好きな場所に好きな大きさのボタンを作れるようにします。関数の引数は、`int32`, `bool`, `double` などの基本的な数値型以外はすべて **const 参照渡し** を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/2.png)

```cpp hl_lines="3 5 12 14"
# include <Siv3D.hpp>

void Button(const Rect& rect)
{
	rect.draw(ColorF{ 0.3, 0.7, 1.0 });
}

void Main()
{
	while (System::Update())
	{
		Button(Rect{ 250, 300, 300, 80 });

		Button(Rect{ 250, 400, 300, 80 });
	}
}
```


## 15.3 テキストを指定できるようにする
ボタン内に表示するテキストを指定できるようにします。文字列は `String` 型です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/3.png)

```cpp hl_lines="3 7 12 16 18"
# include <Siv3D.hpp>

void Button(const Rect& rect, const Font& font, const String& text)
{
	rect.draw(ColorF{ 0.3, 0.7, 1.0 });

	font(text).drawAt(40, (rect.x + rect.w / 2), (rect.y + rect.h / 2));
}

void Main()
{
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		Button(Rect{ 250, 300, 300, 80 }, font, U"つづきから");

		Button(Rect{ 250, 400, 300, 80 }, font, U"最初から");
	}
}
```


## 15.4 マウスカーソルを手のアイコンにする
ボタンの上にマウスカーソルを重ねると、マウスカーソルが手のアイコンに変わるようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/4.png)

```cpp hl_lines="5-8"
# include <Siv3D.hpp>

void Button(const Rect& rect, const Font& font, const String& text)
{
	if (rect.mouseOver())
	{
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	rect.draw(ColorF{ 0.3, 0.7, 1.0 });

	font(text).drawAt(40, (rect.x + rect.w / 2), (rect.y + rect.h / 2));
}

void Main()
{
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		Button(Rect{ 250, 300, 300, 80 }, font, U"つづきから");

		Button(Rect{ 250, 400, 300, 80 }, font, U"最初から");
	}
}
```

## 15.5 押せるかどうかを指定できるようにする
押せないボタンを作ります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/5.png)

```cpp hl_lines="3 5 10-19 28 30"
# include <Siv3D.hpp>

void Button(const Rect& rect, const Font& font, const String& text, bool enabled)
{
	if (enabled && rect.mouseOver())
	{
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.7, 1.0 });
		font(text).drawAt(40, (rect.x + rect.w / 2), (rect.y + rect.h / 2));
	}
	else
	{
		rect.draw(ColorF{ 0.5 });
		font(text).drawAt(40, (rect.x + rect.w / 2), (rect.y + rect.h / 2), ColorF{ 0.7 });
	}
}

void Main()
{
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		Button(Rect{ 250, 300, 300, 80 }, font, U"つづきから", false);

		Button(Rect{ 250, 400, 300, 80 }, font, U"最初から", true);
	}
}
```

## 15.6 押されたかどうかを返す
ボタンが押されたかを戻り値で返すようにします。押せないボタンは、押しても `false` を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/6.png)

```cpp hl_lines="3 21 30-38"
# include <Siv3D.hpp>

bool Button(const Rect& rect, const Font& font, const String& text, bool enabled)
{
	if (enabled && rect.mouseOver())
	{
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.7, 1.0 });
		font(text).drawAt(40, (rect.x + rect.w / 2), (rect.y + rect.h / 2));
	}
	else
	{
		rect.draw(ColorF{ 0.5 });
		font(text).drawAt(40, (rect.x + rect.w / 2), (rect.y + rect.h / 2), ColorF{ 0.7 });
	}

	return (enabled && rect.leftClicked());
}

void Main()
{
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		if (Button(Rect{ 250, 300, 300, 80 }, font, U"つづきから", false))
		{
			Print << U"つづきから";
		}

		if (Button(Rect{ 250, 400, 300, 80 }, font, U"最初から", true))
		{
			Print << U"最初から";
		}
	}
}
```

## 15.7 絵文字を追加する
ボタンに絵文字を追加できるようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/button/7.png)

```cpp hl_lines="3 21 28-30 36 41"
# include <Siv3D.hpp>

bool Button(const Rect& rect, const Texture& emoji, const Font& font, const String& text, bool enabled)
{
	if (enabled && rect.mouseOver())
	{
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.7, 1.0 });
		font(text).drawAt(40, (rect.x + rect.w / 2 + 30), (rect.y + rect.h / 2));
	}
	else
	{
		rect.draw(ColorF{ 0.5 });
		font(text).drawAt(40, (rect.x + rect.w / 2 + 30), (rect.y + rect.h / 2), ColorF{ 0.7 });
	}

	emoji.scaled(0.5).drawAt(rect.x + 60, rect.y + 40);

	return (enabled && rect.leftClicked());
}

void Main()
{
	const Texture emojiBread{ U"🍞"_emoji };

	const Texture emojiRice{ U"🍚"_emoji };

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		if (Button(Rect{ 250, 300, 300, 80 }, emojiBread, font, U"パン", false))
		{
			Print << U"パン";
		}

		if (Button(Rect{ 250, 400, 300, 80 }, emojiRice, font, U"米", true))
		{
			Print << U"米";
		}
	}
}
```
