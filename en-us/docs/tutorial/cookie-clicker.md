# 16. クッキークリッカー風のゲームを作る
ここまで学んだことを使って、クッキークリッカー風のゲームを作ります。

!!! info "クッキークリッカーとは"
    クッキークリッカーとは、クッキーをクリックしてクッキーの数を増やしていくゲームです。増やしたクッキーは、クッキーを増やすためのアイテムを買うために使うことができます。2013 年にオリジナルのゲームが公開されて人気を博し、その後、様々なクッキークリッカー風のゲームが作られています。

    - [Cookie Clicker 公式ページ :material-open-in-new:](https://orteil.dashnet.org/cookieclicker/){:target="_blank"}
    - [Wikipedia: Cookie Clicker :material-open-in-new:](https://ja.wikipedia.org/wiki/%E3%82%AF%E3%83%83%E3%82%AD%E3%83%BC%E3%82%AF%E3%83%AA%E3%83%83%E3%82%AB%E3%83%BC){:target="_blank"}

## 完成図

??? check "完成プログラム"
    ```cpp
    # include <Siv3D.hpp>

    /// @brief アイテムのボタン
    /// @param rect ボタンの領域
    /// @param texture ボタンの絵文字
    /// @param font 文字描画に使うフォント
    /// @param name アイテムの名前
    /// @param desc アイテムの説明
    /// @param count アイテムの所持数
    /// @param enabled ボタンを押せるか
    /// @return ボタンが押された場合 true, それ以外の場合は false
    bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
    {
        if (enabled)
        {
            rect.draw(ColorF{ 0.3, 0.5, 0.9, 0.8 });

            rect.drawFrame(2, 2, ColorF{ 0.5, 0.7, 1.0 });

            if (rect.mouseOver())
            {
                Cursor::RequestStyle(CursorStyle::Hand);
            }
        }
        else
        {
            rect.draw(ColorF{ 0.0, 0.4 });

            rect.drawFrame(2, 2, ColorF{ 0.5 });
        }

        texture.scaled(0.5).drawAt(rect.x + 50, rect.y + 50);

        font(name).draw(30, rect.x + 100, rect.y + 15, Palette::White);

        font(desc).draw(18, rect.x + 102, rect.y + 60, Palette::White);

        font(count).draw(50, Arg::rightCenter((rect.x + rect.w - 20), (rect.y + 50)), Palette::White);

        return (enabled && rect.leftClicked());
    }

    void Main()
    {
        // クッキーの絵文字
        const Texture texture{ U"🍪"_emoji };

        // 農場の絵文字
        const Texture farmEmoji{ U"🌾"_emoji };

        // 工場の絵文字
        const Texture factoryEmoji{ U"🏭"_emoji };

        // フォント
        const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

        // クッキーのクリック円
        const Circle cookieCircle{ 170, 300, 100 };

        // クッキーの表示サイズ（倍率）
        double cookieScale = 1.5;

        // クッキーの個数
        double cookies = 0;

        // 農場の所有数
        int32 farmCount = 0;

        // 工場の所有数
        int32 factoryCount = 0;

        // 農場の価格
        int32 farmCost = 10;

        // 工場の価格
        int32 factoryCost = 100;

        // ゲームの経過時間の蓄積
        double accumulatedTime = 0.0;

        while (System::Update())
        {
            // クッキーの毎秒の生産量 (cookies per second) を計算する
            const int32 cps = (farmCount + factoryCount * 10);

            // ゲームの経過時間を加算する
            accumulatedTime += Scene::DeltaTime();

            // 0.1 秒以上蓄積していたら
            if (0.1 <= accumulatedTime)
            {
                accumulatedTime -= 0.1;

                // 0.1 秒分のクッキー生産を加算する
                cookies += (cps * 0.1);
            }

            // 農場の価格を計算する
            farmCost = 10 + (farmCount * 10);

            // 工場の価格を計算する
            factoryCost = 100 + (factoryCount * 100);

            // クッキー円上にマウスカーソルがあれば
            if (cookieCircle.mouseOver())
            {
                Cursor::RequestStyle(CursorStyle::Hand);
            }

            // クッキー円が左クリックされたら
            if (cookieCircle.leftClicked())
            {
                cookieScale = 1.3;
                ++cookies;
            }

            // クッキーの表示サイズを回復する
            cookieScale += Scene::DeltaTime();

            if (1.5 < cookieScale)
            {
                cookieScale = 1.5;
            }

            // 背景を描く
            Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

            // クッキーの数を整数で表示する
            font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

            // クッキーの生産量を表示する
            font(U"毎秒: {}"_fmt(cps)).drawAt(24, 170, 160);

            // クッキーを描画する
            texture.scaled(cookieScale).drawAt(cookieCircle.center);

            // 農場ボタン
            if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"クッキー農場", U"C{} / 1 CPS"_fmt(farmCost), farmCount, (farmCost <= cookies)))
            {
                cookies -= farmCost;
                ++farmCount;
            }

            // 工場ボタン
            if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"クッキー工場", U"C{} / 10 CPS"_fmt(factoryCost), factoryCount, (factoryCost <= cookies)))
            {
                cookies -= factoryCost;
                ++factoryCount;
            }
        }
    }
    ```


## 16.1 背景とクッキーを描く

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/1.png)

```cpp hl_lines="5-6 10-14"
# include <Siv3D.hpp>

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	while (System::Update())
	{
		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーを描画する
		texture.scaled(1.5).drawAt(170, 300);
	}
}
```


## 16.2 クッキーの個数を表示する
この先のステップで、0.1 秒ごとにクッキーの枚数を更新するため、クッキーの枚数は整数ではなく小数で管理します。表示するときは `U"{:.0f}"` で小数以下は表示しないようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/2.png)

```cpp hl_lines="8-12 19-20"
# include <Siv3D.hpp>

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーの個数
	double cookies = 0;

	while (System::Update())
	{
		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーを描画する
		texture.scaled(1.5).drawAt(170, 300);
	}
}
```


## 16.3 クッキーを押せるようにする
クッキーの領域に沿った `Circle` でマウス入力を処理します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/3.png)

```cpp hl_lines="11-12 19-29 38"
# include <Siv3D.hpp>

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの個数
	double cookies = 0;

	while (System::Update())
	{
		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			++cookies;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーを描画する
		texture.scaled(1.5).drawAt(cookieCircle.center);
	}
}
```


## 16.4 クッキーを押したときのモーションを付ける
クッキーを左クリックしたときにクッキーのサイズを小さくし、時間の経過とともにサイズを元に戻します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/4.png)

```cpp hl_lines="14-15 31 35-41 50" 
# include <Siv3D.hpp>

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの表示サイズ（倍率）
	double cookieScale = 1.5;

	// クッキーの個数
	double cookies = 0;

	while (System::Update())
	{
		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			cookieScale = 1.3;
			++cookies;
		}

		// クッキーの表示サイズを回復する
		cookieScale += Scene::DeltaTime();

		if (1.5 < cookieScale)
		{
			cookieScale = 1.5;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーを描画する
		texture.scaled(cookieScale).drawAt(cookieCircle.center);
	}
}
```


## 16.5 アイテムのボタンを作る (1)
アイテム用のボタンを処理する関数を作ります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/5.png)

```cpp hl_lines="3-31 38-42 88-92"
# include <Siv3D.hpp>

/// @brief アイテムのボタン
/// @param rect ボタンの領域
/// @param texture ボタンの絵文字
/// @param enabled ボタンを押せるか
/// @return ボタンが押された場合 true, それ以外の場合は false
bool Button(const Rect& rect, const Texture& texture, bool enabled)
{
	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.5, 0.9, 0.8 });

		rect.drawFrame(2, 2, ColorF{ 0.5, 0.7, 1.0 });

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}
	}
	else
	{
		rect.draw(ColorF{ 0.0, 0.4 });

		rect.drawFrame(2, 2, ColorF{ 0.5 });
	}

	texture.scaled(0.5).drawAt(rect.x + 50, rect.y + 50);

	return (enabled && rect.leftClicked());
}

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// 農場の絵文字
	const Texture farmEmoji{ U"🌾"_emoji };

	// 工場の絵文字
	const Texture factoryEmoji{ U"🏭"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの表示サイズ（倍率）
	double cookieScale = 1.5;

	// クッキーの個数
	double cookies = 0;

	while (System::Update())
	{
		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			cookieScale = 1.3;
			++cookies;
		}

		// クッキーの表示サイズを回復する
		cookieScale += Scene::DeltaTime();

		if (1.5 < cookieScale)
		{
			cookieScale = 1.5;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーを描画する
		texture.scaled(cookieScale).drawAt(cookieCircle.center);

		// 農場のボタン
		Button(Rect{ 340, 40, 420, 100 }, farmEmoji, true);

		// 工場のボタン
		Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, false);
	}
}
```


## 16.6 アイテムのボタンを作る (2)
ボタンに仮の説明文と数字を加えます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/6.png)

```cpp hl_lines="6-9 12 34-38 98-102"
# include <Siv3D.hpp>

/// @brief アイテムのボタン
/// @param rect ボタンの領域
/// @param texture ボタンの絵文字
/// @param font 文字描画に使うフォント
/// @param name アイテムの名前
/// @param desc アイテムの説明
/// @param count アイテムの所持数
/// @param enabled ボタンを押せるか
/// @return ボタンが押された場合 true, それ以外の場合は false
bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
{
	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.5, 0.9, 0.8 });

		rect.drawFrame(2, 2, ColorF{ 0.5, 0.7, 1.0 });

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}
	}
	else
	{
		rect.draw(ColorF{ 0.0, 0.4 });

		rect.drawFrame(2, 2, ColorF{ 0.5 });
	}

	texture.scaled(0.5).drawAt(rect.x + 50, rect.y + 50);

	font(name).draw(30, rect.x + 100, rect.y + 15, Palette::White);

	font(desc).draw(18, rect.x + 102, rect.y + 60, Palette::White);

	font(count).draw(50, Arg::rightCenter((rect.x + rect.w - 20), (rect.y + 50)), Palette::White);

	return (enabled && rect.leftClicked());
}

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// 農場の絵文字
	const Texture farmEmoji{ U"🌾"_emoji };

	// 工場の絵文字
	const Texture factoryEmoji{ U"🏭"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの表示サイズ（倍率）
	double cookieScale = 1.5;

	// クッキーの個数
	double cookies = 0;

	while (System::Update())
	{
		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			cookieScale = 1.3;
			++cookies;
		}

		// クッキーの表示サイズを回復する
		cookieScale += Scene::DeltaTime();

		if (1.5 < cookieScale)
		{
			cookieScale = 1.5;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーを描画する
		texture.scaled(cookieScale).drawAt(cookieCircle.center);

		// 農場のボタン
		Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"クッキー農場", U"C10 / 1 CPS", 0, true);

		// 工場のボタン
		Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"クッキー工場", U"C100 / 10 CPS", 0, false);
	}
}
```


## 16.7 変数とボタンの表示を連動させる
ゲームの状態とボタンの表示を連動させます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/7.png)

```cpp hl_lines="66-76 110-122"
# include <Siv3D.hpp>

/// @brief アイテムのボタン
/// @param rect ボタンの領域
/// @param texture ボタンの絵文字
/// @param font 文字描画に使うフォント
/// @param name アイテムの名前
/// @param desc アイテムの説明
/// @param count アイテムの所持数
/// @param enabled ボタンを押せるか
/// @return ボタンが押された場合 true, それ以外の場合は false
bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
{
	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.5, 0.9, 0.8 });

		rect.drawFrame(2, 2, ColorF{ 0.5, 0.7, 1.0 });

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}
	}
	else
	{
		rect.draw(ColorF{ 0.0, 0.4 });

		rect.drawFrame(2, 2, ColorF{ 0.5 });
	}

	texture.scaled(0.5).drawAt(rect.x + 50, rect.y + 50);

	font(name).draw(30, rect.x + 100, rect.y + 15, Palette::White);

	font(desc).draw(18, rect.x + 102, rect.y + 60, Palette::White);

	font(count).draw(50, Arg::rightCenter((rect.x + rect.w - 20), (rect.y + 50)), Palette::White);

	return (enabled && rect.leftClicked());
}

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// 農場の絵文字
	const Texture farmEmoji{ U"🌾"_emoji };

	// 工場の絵文字
	const Texture factoryEmoji{ U"🏭"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの表示サイズ（倍率）
	double cookieScale = 1.5;

	// クッキーの個数
	double cookies = 0;

	// 農場の所有数
	int32 farmCount = 0;

	// 工場の所有数
	int32 factoryCount = 0;

	// 農場の価格
	int32 farmCost = 10;

	// 工場の価格
	int32 factoryCost = 100;

	while (System::Update())
	{
		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			cookieScale = 1.3;
			++cookies;
		}

		// クッキーの表示サイズを回復する
		cookieScale += Scene::DeltaTime();

		if (1.5 < cookieScale)
		{
			cookieScale = 1.5;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーを描画する
		texture.scaled(cookieScale).drawAt(cookieCircle.center);

		// 農場ボタン
		if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"クッキー農場", U"C{} / 1 CPS"_fmt(farmCost), farmCount, (farmCost <= cookies)))
		{
			cookies -= farmCost;
			++farmCount;
		}

		// 工場ボタン
		if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"クッキー工場", U"C{} / 10 CPS"_fmt(factoryCost), factoryCount, (factoryCost <= cookies)))
		{
			cookies -= factoryCost;
			++factoryCount;
		}
	}
}
```


## 16.8 クッキーの自動生産
アイテムの所有数に応じてクッキーが自動で増えるようにします。具体的には、0.1 秒ごとに、毎秒のクッキー生産量の 10% を獲得するようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/8.png)

```cpp hl_lines="78-79 83-96 125-126"
# include <Siv3D.hpp>

/// @brief アイテムのボタン
/// @param rect ボタンの領域
/// @param texture ボタンの絵文字
/// @param font 文字描画に使うフォント
/// @param name アイテムの名前
/// @param desc アイテムの説明
/// @param count アイテムの所持数
/// @param enabled ボタンを押せるか
/// @return ボタンが押された場合 true, それ以外の場合は false
bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
{
	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.5, 0.9, 0.8 });

		rect.drawFrame(2, 2, ColorF{ 0.5, 0.7, 1.0 });

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}
	}
	else
	{
		rect.draw(ColorF{ 0.0, 0.4 });

		rect.drawFrame(2, 2, ColorF{ 0.5 });
	}

	texture.scaled(0.5).drawAt(rect.x + 50, rect.y + 50);

	font(name).draw(30, rect.x + 100, rect.y + 15, Palette::White);

	font(desc).draw(18, rect.x + 102, rect.y + 60, Palette::White);

	font(count).draw(50, Arg::rightCenter((rect.x + rect.w - 20), (rect.y + 50)), Palette::White);

	return (enabled && rect.leftClicked());
}

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// 農場の絵文字
	const Texture farmEmoji{ U"🌾"_emoji };

	// 工場の絵文字
	const Texture factoryEmoji{ U"🏭"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの表示サイズ（倍率）
	double cookieScale = 1.5;

	// クッキーの個数
	double cookies = 0;

	// 農場の所有数
	int32 farmCount = 0;

	// 工場の所有数
	int32 factoryCount = 0;

	// 農場の価格
	int32 farmCost = 10;

	// 工場の価格
	int32 factoryCost = 100;

	// ゲームの経過時間の蓄積
	double accumulatedTime = 0.0;

	while (System::Update())
	{
		// クッキーの毎秒の生産量 (cookies per second) を計算する
		const int32 cps = (farmCount + factoryCount * 10);

		// ゲームの経過時間を加算する
		accumulatedTime += Scene::DeltaTime();

		// 0.1 秒以上蓄積していたら
		if (0.1 <= accumulatedTime)
		{
			accumulatedTime -= 0.1;

			// 0.1 秒分のクッキー生産を加算する
			cookies += (cps * 0.1);
		}

		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			cookieScale = 1.3;
			++cookies;
		}

		// クッキーの表示サイズを回復する
		cookieScale += Scene::DeltaTime();

		if (1.5 < cookieScale)
		{
			cookieScale = 1.5;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーの生産量を表示する
		font(U"毎秒: {}"_fmt(cps)).drawAt(24, 170, 160);

		// クッキーを描画する
		texture.scaled(cookieScale).drawAt(cookieCircle.center);

		// 農場ボタン
		if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"クッキー農場", U"C{} / 1 CPS"_fmt(farmCost), farmCount, (farmCost <= cookies)))
		{
			cookies -= farmCost;
			++farmCount;
		}

		// 工場ボタン
		if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"クッキー工場", U"C{} / 10 CPS"_fmt(factoryCost), factoryCount, (factoryCost <= cookies)))
		{
			cookies -= factoryCost;
			++factoryCount;
		}
	}
}
```


## 16.9 （完成）アイテムのインフレを実装する
アイテムの所有数が増えたときに、アイテムの価格がインフレするようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/cookie-clicker/9.png)

```cpp hl_lines="98-102"
# include <Siv3D.hpp>

/// @brief アイテムのボタン
/// @param rect ボタンの領域
/// @param texture ボタンの絵文字
/// @param font 文字描画に使うフォント
/// @param name アイテムの名前
/// @param desc アイテムの説明
/// @param count アイテムの所持数
/// @param enabled ボタンを押せるか
/// @return ボタンが押された場合 true, それ以外の場合は false
bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
{
	if (enabled)
	{
		rect.draw(ColorF{ 0.3, 0.5, 0.9, 0.8 });

		rect.drawFrame(2, 2, ColorF{ 0.5, 0.7, 1.0 });

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}
	}
	else
	{
		rect.draw(ColorF{ 0.0, 0.4 });

		rect.drawFrame(2, 2, ColorF{ 0.5 });
	}

	texture.scaled(0.5).drawAt(rect.x + 50, rect.y + 50);

	font(name).draw(30, rect.x + 100, rect.y + 15, Palette::White);

	font(desc).draw(18, rect.x + 102, rect.y + 60, Palette::White);

	font(count).draw(50, Arg::rightCenter((rect.x + rect.w - 20), (rect.y + 50)), Palette::White);

	return (enabled && rect.leftClicked());
}

void Main()
{
	// クッキーの絵文字
	const Texture texture{ U"🍪"_emoji };

	// 農場の絵文字
	const Texture farmEmoji{ U"🌾"_emoji };

	// 工場の絵文字
	const Texture factoryEmoji{ U"🏭"_emoji };

	// フォント
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クッキーのクリック円
	const Circle cookieCircle{ 170, 300, 100 };

	// クッキーの表示サイズ（倍率）
	double cookieScale = 1.5;

	// クッキーの個数
	double cookies = 0;

	// 農場の所有数
	int32 farmCount = 0;

	// 工場の所有数
	int32 factoryCount = 0;

	// 農場の価格
	int32 farmCost = 10;

	// 工場の価格
	int32 factoryCost = 100;

	// ゲームの経過時間の蓄積
	double accumulatedTime = 0.0;

	while (System::Update())
	{
		// クッキーの毎秒の生産量 (cookies per second) を計算する
		const int32 cps = (farmCount + factoryCount * 10);

		// ゲームの経過時間を加算する
		accumulatedTime += Scene::DeltaTime();

		// 0.1 秒以上蓄積していたら
		if (0.1 <= accumulatedTime)
		{
			accumulatedTime -= 0.1;

			// 0.1 秒分のクッキー生産を加算する
			cookies += (cps * 0.1);
		}

		// 農場の価格を計算する
		farmCost = 10 + (farmCount * 10);

		// 工場の価格を計算する
		factoryCost = 100 + (factoryCount * 100);

		// クッキー円上にマウスカーソルがあれば
		if (cookieCircle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クッキー円が左クリックされたら
		if (cookieCircle.leftClicked())
		{
			cookieScale = 1.3;
			++cookies;
		}

		// クッキーの表示サイズを回復する
		cookieScale += Scene::DeltaTime();

		if (1.5 < cookieScale)
		{
			cookieScale = 1.5;
		}

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(cookies)).drawAt(60, 170, 100);

		// クッキーの生産量を表示する
		font(U"毎秒: {}"_fmt(cps)).drawAt(24, 170, 160);

		// クッキーを描画する
		texture.scaled(cookieScale).drawAt(cookieCircle.center);

		// 農場ボタン
		if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"クッキー農場", U"C{} / 1 CPS"_fmt(farmCost), farmCount, (farmCost <= cookies)))
		{
			cookies -= farmCost;
			++farmCount;
		}

		// 工場ボタン
		if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"クッキー工場", U"C{} / 10 CPS"_fmt(factoryCost), factoryCount, (factoryCost <= cookies)))
		{
			cookies -= factoryCost;
			++factoryCount;
		}
	}
}
```


## 16.10 （参考）さらに発展したクッキークリッカー
今後学ぶ Siv3D の機能を使って、クッキークリッカーをさらに発展させたサンプルがあります。

- [ゲームのサンプル | クッキークリッカー](https://siv3d.github.io/ja-jp/samples/games/#9-%E3%82%AF%E3%83%83%E3%82%AD%E3%83%BC%E3%82%AF%E3%83%AA%E3%83%83%E3%82%AB%E3%83%BC){:target="_blank"}

