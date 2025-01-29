# 28. ボタン
チュートリアル 3 ～ 27 の内容を使って、見た目にこだわったボタンを作成します。

## 28.1 基本の関数
- 与えられた長方形をボタンとして描画し、ボタンが押されたかどうかを返す関数 `Button` を作成します
- デザイン性のために、角を少し丸めた長方形として描画します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/button/1.png)

```cpp
# include <Siv3D.hpp>

bool Button(const Rect& rect)
{
	const RoundRect roundRect = rect.rounded(6);

	// 背景を描く
	roundRect.draw(ColorF{ 0.9, 0.8, 0.6 });

	// ボタンが押されたら true を返す
	return rect.leftClicked();
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (Button(Rect{ 80, 300, 300, 80 }))
		{
			Print << U"A";
		}

		if (Button(Rect{ 420, 300, 300, 80 }))
		{
			Print << U"B";
		}
	}
}
```


## 28.2 装飾の追加
- ボタンの見た目を整えるために、影や枠を追加します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/button/2.png)

```cpp
# include <Siv3D.hpp>

bool Button(const Rect& rect)
{
	const RoundRect roundRect = rect.rounded(6);

	// 影と背景を描く
	roundRect
		.drawShadow(Vec2{ 2, 2 }, 12, 0)
		.draw(ColorF{ 0.9, 0.8, 0.6 });

	// 枠を描く
	rect.stretched(-3).rounded(3)
		.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

	// ボタンが押されたら true を返す
	return rect.leftClicked();
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (Button(Rect{ 80, 300, 300, 80 }))
		{
			Print << U"A";
		}

		if (Button(Rect{ 420, 300, 300, 80 }))
		{
			Print << U"B";
		}
	}
}
```


## 28.3 テキストの追加
- フォントと文字列を渡し、ボタンにテキストを描画します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/button/3.png)

```cpp
# include <Siv3D.hpp>

bool Button(const Rect& rect, const Font& font, const String& text)
{
	const RoundRect roundRect = rect.rounded(6);

	// 影と背景を描く
	roundRect
		.drawShadow(Vec2{ 2, 2 }, 12, 0)
		.draw(ColorF{ 0.9, 0.8, 0.6 });

	// 枠を描く
	rect.stretched(-3).rounded(3)
		.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

	// テキストを描く
	font(text).drawAt(40, rect.center(), ColorF{ 0.4, 0.3, 0.2 });

	// ボタンが押されたら true を返す
	return rect.leftClicked();
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		if (Button(Rect{ 80, 300, 300, 80 }, font, U"Bread"))
		{
			Print << U"Bread";
		}

		if (Button(Rect{ 420, 300, 300, 80 }, font, U"Rice"))
		{
			Print << U"Rice";
		}
	}
}

```


## 28.4 マウスカーソルの変更
- マウスカーソルがボタンの上にある場合、手の形に変更します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/button/4.png)

```cpp
# include <Siv3D.hpp>

bool Button(const Rect& rect, const Font& font, const String& text)
{
	// マウスカーソルがボタンの上にある場合
	if (rect.mouseOver())
	{
		// マウスカーソルを手の形にする
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	const RoundRect roundRect = rect.rounded(6);

	// 影と背景を描く
	roundRect
		.drawShadow(Vec2{ 2, 2 }, 12, 0)
		.draw(ColorF{ 0.9, 0.8, 0.6 });

	// 枠を描く
	rect.stretched(-3).rounded(3)
		.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

	// テキストを描く
	font(text).drawAt(40, rect.center(), ColorF{ 0.4, 0.3, 0.2 });

	// ボタンが押されたら true を返す
	return rect.leftClicked();
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		if (Button(Rect{ 80, 300, 300, 80 }, font, U"Bread"))
		{
			Print << U"Bread";
		}

		if (Button(Rect{ 420, 300, 300, 80 }, font, U"Rice"))
		{
			Print << U"Rice";
		}
	}
}
```


## 28.5 無効状態の追加
- ボタンが無効状態のとき、グレーの半透明を重ね、操作も無効にします
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/button/5.png)

```cpp
# include <Siv3D.hpp>

bool Button(const Rect& rect, const Font& font, const String& text, bool enabled)
{
	// マウスカーソルがボタンの上にある場合
	if (enabled && rect.mouseOver())
	{
		// マウスカーソルを手の形にする
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	const RoundRect roundRect = rect.rounded(6);

	// 影と背景を描く
	roundRect
		.drawShadow(Vec2{ 2, 2 }, 12, 0)
		.draw(ColorF{ 0.9, 0.8, 0.6 });

	// 枠を描く
	rect.stretched(-3).rounded(3)
		.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

	// テキストを描く
	font(text).drawAt(40, rect.center(), ColorF{ 0.4, 0.3, 0.2 });

	// 無効の場合は
	if (not enabled)
	{
		// グレーの半透明を重ねる
		roundRect.draw(ColorF{ 0.8, 0.8 });
	}

	// ボタンが押されたら true を返す
	return (enabled && rect.leftClicked());
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		if (Button(Rect{ 80, 300, 300, 80 }, font, U"Bread", true))
		{
			Print << U"Bread";
		}

		if (Button(Rect{ 420, 300, 300, 80 }, font, U"Rice", true))
		{
			Print << U"Rice";
		}
	}
}
```


## 28.6 【完成】絵文字の追加
- ボタンに絵文字を追加します
- 絵文字の表示のために、テキストの位置を調整します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/button/6.png)

```cpp
# include <Siv3D.hpp>

bool Button(const Rect& rect, const Texture& emoji, const Font& font, const String& text, bool enabled)
{
	// マウスカーソルがボタンの上にある場合
	if (enabled && rect.mouseOver())
	{
		// マウスカーソルを手の形にする
		Cursor::RequestStyle(CursorStyle::Hand);
	}

	const RoundRect roundRect = rect.rounded(6);

	// 影と背景を描く
	roundRect
		.drawShadow(Vec2{ 2, 2 }, 12, 0)
		.draw(ColorF{ 0.9, 0.8, 0.6 });

	// 枠を描く
	rect.stretched(-3).rounded(3)
		.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

	// 絵文字を描く
	emoji.scaled(0.4).drawAt((rect.x + 60), rect.center().y);

	// テキストを描く
	font(text).drawAt(40, rect.center().movedBy(30, 0), ColorF{ 0.4, 0.3, 0.2 });

	// 無効の場合は
	if (not enabled)
	{
		// グレーの半透明を重ねる
		roundRect.draw(ColorF{ 0.8, 0.8 });
	}

	// ボタンが押されたら true を返す
	return (enabled && rect.leftClicked());
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emojiBread{ U"🍞"_emoji };

	const Texture emojiRice{ U"🍚"_emoji };

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		if (Button(Rect{ 80, 300, 300, 80 }, emojiBread, font, U"Bread", true))
		{
			Print << U"Bread";
		}

		if (Button(Rect{ 420, 300, 300, 80 }, emojiRice, font, U"Rice", true))
		{
			Print << U"Rice";
		}
	}
}
```

