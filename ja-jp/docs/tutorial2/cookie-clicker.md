# 29. クッキークリッカー
チュートリアル 3 ～ 28 の内容を使って、クッキークリッカー風のゲームを作ります。

!!! info "クッキークリッカーとは"
    - クッキークリッカーは、クッキーをクリックしてクッキーの数を増やしていくゲームです
    - 増やしたクッキーを使って、クッキーを増やすための生産設備を購入できます
    - 2013 年にオリジナルのゲームが公開されて人気を博し、その後、様々な派生ゲームが作られています

    - [Cookie Clicker 公式ページ :material-open-in-new:](https://orteil.dashnet.org/cookieclicker/){:target="_blank"}
    - [Wikipedia: Cookie Clicker :material-open-in-new:](https://ja.wikipedia.org/wiki/%E3%82%AF%E3%83%83%E3%82%AD%E3%83%BC%E3%82%AF%E3%83%AA%E3%83%83%E3%82%AB%E3%83%BC){:target="_blank"}


## 29.1 基本実装
- ゲームの進行状況を表すクラス `GameInfo` を用意します
	- この先、0.1 秒単位でクッキーの枚数を増やす計算ができるよう、`int32` の代わりに `double` 型でクッキーの数を管理します
	- クッキーの生産設備である「農場」と「工場」の数を、`int32` で表現します
- ゲーム画面の背景を、画面全体を覆うグラデーションの長方形で描画します
- `Main` 関数が複雑になりすぎないよう、処理は適宜関数に分割していきます
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/1.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// ゲームの進行状況
	struct GameInfo
	{
		// クッキーの数
		double cookies = 0.0;

		// 農場の数
		int32 farmCount = 0;

		// 工場の数
		int32 factoryCount = 0;
	};

	// 背景を描く関数
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	void Main()
	{
		// ゲームの進行状況
		GameInfo game;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	更新
			//
			/////////////////////////////////

			/////////////////////////////////
			//
			//	描画
			//
			/////////////////////////////////

			// 背景を描く
			DrawBackground();
		}
	}
	```


## 29.2 クッキーの描画
- クッキーの絵文字からテクスチャを作成し、画面に大きく描画します
- クッキーの大きさにモーションをつけられるよう、クッキーの表示サイズを変数 `cookieScale` で管理します
- クッキーのクリック判定用の円 `cookieCircle` を用意し、クリックされたらクッキーの数を増やします
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/2.png)

??? memo "コード"
	```cpp hl_lines="24-27 32-33 43-47 58-59"
	# include <Siv3D.hpp>

	// ゲームの進行状況
	struct GameInfo
	{
		// クッキーの数
		double cookies = 0.0;

		// 農場の数
		int32 farmCount = 0;

		// 工場の数
		int32 factoryCount = 0;
	};

	// 背景を描く関数
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };

		// クッキーのクリック円
		const Circle cookieCircle{ 170, 300, 90 };

		// ゲームの進行状況
		GameInfo game;

		// クッキーの表示サイズ（倍率）
		double cookieScale = 1.5;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	更新
			//
			/////////////////////////////////

			// クッキー円が左クリックされたら
			if (cookieCircle.leftClicked())
			{
				game.cookies += 1;
			}

			/////////////////////////////////
			//
			//	描画
			//
			/////////////////////////////////

			// 背景を描く
			DrawBackground();

			// クッキーを描画する
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);
		}
	}
	```


## 29.3 クッキーの枚数の表示
- クッキーの枚数を整数で表示します
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/3.png)

??? memo "コード"
	```cpp hl_lines="22-27 33 67-68"
	# include <Siv3D.hpp>

	// ゲームの進行状況
	struct GameInfo
	{
		// クッキーの数
		double cookies = 0.0;

		// 農場の数
		int32 farmCount = 0;

		// 工場の数
		int32 factoryCount = 0;
	};

	// 背景を描く関数
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	// クッキーの枚数を表示する関数
	void DrawCookieCount(const GameInfo& game, const Font& font)
	{
		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(game.cookies)).drawAt(60, Vec2{ 170, 100 });
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// クッキーのクリック円
		const Circle cookieCircle{ 170, 300, 90 };

		// ゲームの進行状況
		GameInfo game;

		// クッキーの表示サイズ（倍率）
		double cookieScale = 1.5;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	更新
			//
			/////////////////////////////////

			// クッキー円が左クリックされたら
			if (cookieCircle.leftClicked())
			{
				game.cookies += 1;
			}

			/////////////////////////////////
			//
			//	描画
			//
			/////////////////////////////////

			// 背景を描く
			DrawBackground();

			// クッキーの数を表示する
			DrawCookieCount(game, font);

			// クッキーを描画する
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);
		}
	}
	```


## 29.4 クリック体験の向上
- `cookieCircle` にマウスカーソルが重なっているとき、マウスカーソルを手の形に変更します
- クッキーをクリックしたときに、`cookieScale` を一時的に小さくします
	- `cookieScale` は時間の経過とともに `1.5` に戻ります
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/4.png)

??? memo "コード"
	```cpp hl_lines="52-58 63 68-69"
	# include <Siv3D.hpp>

	// ゲームの進行状況
	struct GameInfo
	{
		// クッキーの数
		double cookies = 0.0;

		// 農場の数
		int32 farmCount = 0;

		// 工場の数
		int32 factoryCount = 0;
	};

	// 背景を描く関数
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	// クッキーの枚数を表示する関数
	void DrawCookieCount(const GameInfo& game, const Font& font)
	{
		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(game.cookies)).drawAt(60, Vec2{ 170, 100 });
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// クッキーのクリック円
		const Circle cookieCircle{ 170, 300, 90 };

		// ゲームの進行状況
		GameInfo game;

		// クッキーの表示サイズ（倍率）
		double cookieScale = 1.5;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	更新
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// クッキー円上にマウスカーソルがあれば
			if (cookieCircle.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// クッキー円が左クリックされたら
			if (cookieCircle.leftClicked())
			{
				cookieScale = 1.3;

				game.cookies += 1;
			}

			// クッキーの表示サイズを回復する
			cookieScale = Min((cookieScale + deltaTime), 1.5);

			/////////////////////////////////
			//
			//	描画
			//
			/////////////////////////////////

			// 背景を描く
			DrawBackground();

			// クッキーの数を表示する
			DrawCookieCount(game, font);

			// クッキーを描画する
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);
		}
	}
	```


## 29.5 設備購入ボタンの追加
- **チュートリアル 28** を参考に、クッキーの生産設備を購入するためのボタンを追加します
- ボタンが押されたら、設備の数を増やします
- 引数 `enabled` を使って、「ボタンが押せない」という状態も表現できるようにします
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/5.png)

??? memo "コード"
	```cpp hl_lines="3-49 80-81 136-146"
	# include <Siv3D.hpp>

	// ボタンを描画する関数
	bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
	{
		// マウスカーソルがボタンの上にある場合
		if (enabled && rect.mouseOver())
		{
			// マウスカーソルを手の形にする
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// テキストの色
		const ColorF textColor{ 0.4, 0.3, 0.2 };

		// ボタンの背景の角丸四角形
		const RoundRect roundRect = rect.rounded(6);

		// 影と背景を描く
		roundRect
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw(ColorF{ 0.9, 0.8, 0.6 });

		// 枠を描く
		rect.stretched(-3).rounded(3)
			.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

		// 絵文字を描く
		texture.scaled(0.5).drawAt(rect.leftCenter().movedBy(50, 0));

		// 設備の名前を描く
		font(name).draw(30, rect.pos.movedBy(100, 15), textColor);

		// 設備の説明を描く
		font(desc).draw(18, rect.pos.movedBy(102, 60), textColor);

		// 所有数を描く
		font(count).draw(50, Arg::rightCenter = rect.tr().movedBy(-20, 50), textColor);

		// 無効の場合は
		if (not enabled)
		{
			// グレーの半透明を重ねる
			roundRect.draw(ColorF{ 0.8, 0.6 });
		}

		// ボタンが押されたら true を返す
		return (enabled && rect.leftClicked());
	}

	// ゲームの進行状況
	struct GameInfo
	{
		// クッキーの数
		double cookies = 0.0;

		// 農場の数
		int32 farmCount = 0;

		// 工場の数
		int32 factoryCount = 0;
	};

	// 背景を描く関数
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	// クッキーの枚数を表示する関数
	void DrawCookieCount(const GameInfo& game, const Font& font)
	{
		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(game.cookies)).drawAt(60, Vec2{ 170, 100 });
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };
		const Texture farmEmoji{ U"🌾"_emoji };
		const Texture factoryEmoji{ U"🏭"_emoji };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// クッキーのクリック円
		const Circle cookieCircle{ 170, 300, 90 };

		// ゲームの進行状況
		GameInfo game;

		// クッキーの表示サイズ（倍率）
		double cookieScale = 1.5;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	更新
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// クッキー円上にマウスカーソルがあれば
			if (cookieCircle.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// クッキー円が左クリックされたら
			if (cookieCircle.leftClicked())
			{
				cookieScale = 1.3;

				game.cookies += 1;
			}

			// クッキーの表示サイズを回復する
			cookieScale = Min((cookieScale + deltaTime), 1.5);

			/////////////////////////////////
			//
			//	描画
			//
			/////////////////////////////////

			// 背景を描く
			DrawBackground();

			// クッキーの数を表示する
			DrawCookieCount(game, font);

			// クッキーを描画する
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);

			// 農場ボタン
			if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"クッキー農場", U"1 CPS", game.farmCount, true))
			{
				++game.farmCount;
			}

			// 工場ボタン
			if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"クッキー工場", U"10 CPS", game.factoryCount, false))
			{
				++game.factoryCount;
			}
		}
	}
	```


## 29.6 クッキー生産量の計算
- 購入した設備の種類と数に応じて、毎秒のクッキー生産量（CPS: Cookies Per Second）を計算し、表示します
	- 実際のクッキーの枚数にはまだ反映しません
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/6.png)

??? memo "コード"
	```cpp hl_lines="63-67 76 82-83 139"
	# include <Siv3D.hpp>

	// ボタンを描画する関数
	bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
	{
		// マウスカーソルがボタンの上にある場合
		if (enabled && rect.mouseOver())
		{
			// マウスカーソルを手の形にする
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// テキストの色
		const ColorF textColor{ 0.4, 0.3, 0.2 };

		// ボタンの背景の角丸四角形
		const RoundRect roundRect = rect.rounded(6);

		// 影と背景を描く
		roundRect
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw(ColorF{ 0.9, 0.8, 0.6 });

		// 枠を描く
		rect.stretched(-3).rounded(3)
			.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

		// 絵文字を描く
		texture.scaled(0.5).drawAt(rect.leftCenter().movedBy(50, 0));

		// 設備の名前を描く
		font(name).draw(30, rect.pos.movedBy(100, 15), textColor);

		// 設備の説明を描く
		font(desc).draw(18, rect.pos.movedBy(102, 60), textColor);

		// 所有数を描く
		font(count).draw(50, Arg::rightCenter = rect.tr().movedBy(-20, 50), textColor);

		// 無効の場合は
		if (not enabled)
		{
			// グレーの半透明を重ねる
			roundRect.draw(ColorF{ 0.8, 0.6 });
		}

		// ボタンが押されたら true を返す
		return (enabled && rect.leftClicked());
	}

	// ゲームの進行状況
	struct GameInfo
	{
		// クッキーの数
		double cookies = 0.0;

		// 農場の数
		int32 farmCount = 0;

		// 工場の数
		int32 factoryCount = 0;

		// 毎秒のクッキー生産量 (cookies per second)
		int32 getCPS() const
		{
			return (farmCount + factoryCount * 10);
		}
	};

	// 背景を描く関数
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	// クッキーの枚数と CPS を表示する関数
	void DrawCookieCount(const GameInfo& game, const Font& font)
	{
		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(game.cookies)).drawAt(60, Vec2{ 170, 100 });

		// クッキーの生産量を表示する
		font(U"{} CPS"_fmt(game.getCPS())).drawAt(24, Vec2{ 170, 160 });
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };
		const Texture farmEmoji{ U"🌾"_emoji };
		const Texture factoryEmoji{ U"🏭"_emoji };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// クッキーのクリック円
		const Circle cookieCircle{ 170, 300, 90 };

		// ゲームの進行状況
		GameInfo game;

		// クッキーの表示サイズ（倍率）
		double cookieScale = 1.5;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	更新
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// クッキー円上にマウスカーソルがあれば
			if (cookieCircle.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// クッキー円が左クリックされたら
			if (cookieCircle.leftClicked())
			{
				cookieScale = 1.3;

				game.cookies += 1;
			}

			// クッキーの表示サイズを回復する
			cookieScale = Min((cookieScale + deltaTime), 1.5);

			/////////////////////////////////
			//
			//	描画
			//
			/////////////////////////////////

			// 背景を描く
			DrawBackground();

			// クッキーの数と CPS を表示する
			DrawCookieCount(game, font);

			// クッキーを描画する
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);

			// 農場ボタン
			if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"クッキー農場", U"1 CPS", game.farmCount, true))
			{
				++game.farmCount;
			}

			// 工場ボタン
			if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"クッキー工場", U"10 CPS", game.factoryCount, false))
			{
				++game.factoryCount;
			}
		}
	}
	```


## 29.7 設備購入費用の実装
- 農場と工場の購入費用を計算し、表示します
	- 購入数に応じて価格が上昇します
- 現在のクッキーの枚数が購入費用を下回っている場合は、ボタンを押せないようにします
- 設備を購入したときに、クッキーの枚数から購入費用を引くようにします
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/7.png)

??? memo "コード"
	```cpp hl_lines="69-79 158 160 165 167"
	# include <Siv3D.hpp>

	// ボタンを描画する関数
	bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
	{
		// マウスカーソルがボタンの上にある場合
		if (enabled && rect.mouseOver())
		{
			// マウスカーソルを手の形にする
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// テキストの色
		const ColorF textColor{ 0.4, 0.3, 0.2 };

		// ボタンの背景の角丸四角形
		const RoundRect roundRect = rect.rounded(6);

		// 影と背景を描く
		roundRect
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw(ColorF{ 0.9, 0.8, 0.6 });

		// 枠を描く
		rect.stretched(-3).rounded(3)
			.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

		// 絵文字を描く
		texture.scaled(0.5).drawAt(rect.leftCenter().movedBy(50, 0));

		// 設備の名前を描く
		font(name).draw(30, rect.pos.movedBy(100, 15), textColor);

		// 設備の説明を描く
		font(desc).draw(18, rect.pos.movedBy(102, 60), textColor);

		// 所有数を描く
		font(count).draw(50, Arg::rightCenter = rect.tr().movedBy(-20, 50), textColor);

		// 無効の場合は
		if (not enabled)
		{
			// グレーの半透明を重ねる
			roundRect.draw(ColorF{ 0.8, 0.6 });
		}

		// ボタンが押されたら true を返す
		return (enabled && rect.leftClicked());
	}

	// ゲームの進行状況
	struct GameInfo
	{
		// クッキーの数
		double cookies = 0.0;

		// 農場の数
		int32 farmCount = 0;

		// 工場の数
		int32 factoryCount = 0;

		// 毎秒のクッキー生産量 (cookies per second)
		int32 getCPS() const
		{
			return (farmCount + factoryCount * 10);
		}

		// 農場の価格
		int32 getFarmCost() const
		{
			return (10 + farmCount * 10);
		}

		// 工場の価格
		int32 getFactoryCost() const
		{
			return (100 + factoryCount * 100);
		}
	};

	// 背景を描く関数
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	// クッキーの枚数と CPS を表示する関数
	void DrawCookieCount(const GameInfo& game, const Font& font)
	{
		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(game.cookies)).drawAt(60, Vec2{ 170, 100 });

		// クッキーの生産量を表示する
		font(U"{} CPS"_fmt(game.getCPS())).drawAt(24, Vec2{ 170, 160 });
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };
		const Texture farmEmoji{ U"🌾"_emoji };
		const Texture factoryEmoji{ U"🏭"_emoji };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// クッキーのクリック円
		const Circle cookieCircle{ 170, 300, 90 };

		// ゲームの進行状況
		GameInfo game;

		// クッキーの表示サイズ（倍率）
		double cookieScale = 1.5;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	更新
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// クッキー円上にマウスカーソルがあれば
			if (cookieCircle.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// クッキー円が左クリックされたら
			if (cookieCircle.leftClicked())
			{
				cookieScale = 1.3;

				game.cookies += 1;
			}

			// クッキーの表示サイズを回復する
			cookieScale = Min((cookieScale + deltaTime), 1.5);

			/////////////////////////////////
			//
			//	描画
			//
			/////////////////////////////////

			// 背景を描く
			DrawBackground();

			// クッキーの数と CPS を表示する
			DrawCookieCount(game, font);

			// クッキーを描画する
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);

			// 農場ボタン
			if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"クッキー農場", U"C{} / 1 CPS"_fmt(game.getFarmCost()), game.farmCount, (game.getFarmCost() <= game.cookies)))
			{
				game.cookies -= game.getFarmCost();
				++game.farmCount;
			}

			// 工場ボタン
			if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"クッキー工場", U"C{} / 10 CPS"_fmt(game.getFactoryCost()), game.factoryCount, (game.getFactoryCost() <= game.cookies)))
			{
				game.cookies -= game.getFactoryCost();
				++game.factoryCount;
			}
		}
	}
	```


## 29.8 【完成】クッキー生産の反映
- 購入した設備の数に応じて、0.1 秒ごとにクッキーの枚数を増やします
	- 0.1 秒ごとに、CPS × 0.1 のクッキーを追加します
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/8.png)

??? memo "コード"
	```cpp hl_lines="115-116 128-138"
	# include <Siv3D.hpp>

	// ボタンを描画する関数
	bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
	{
		// マウスカーソルがボタンの上にある場合
		if (enabled && rect.mouseOver())
		{
			// マウスカーソルを手の形にする
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// テキストの色
		const ColorF textColor{ 0.4, 0.3, 0.2 };

		// ボタンの背景の角丸四角形
		const RoundRect roundRect = rect.rounded(6);

		// 影と背景を描く
		roundRect
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw(ColorF{ 0.9, 0.8, 0.6 });

		// 枠を描く
		rect.stretched(-3).rounded(3)
			.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

		// 絵文字を描く
		texture.scaled(0.5).drawAt(rect.leftCenter().movedBy(50, 0));

		// 設備の名前を描く
		font(name).draw(30, rect.pos.movedBy(100, 15), textColor);

		// 設備の説明を描く
		font(desc).draw(18, rect.pos.movedBy(102, 60), textColor);

		// 所有数を描く
		font(count).draw(50, Arg::rightCenter = rect.tr().movedBy(-20, 50), textColor);

		// 無効の場合は
		if (not enabled)
		{
			// グレーの半透明を重ねる
			roundRect.draw(ColorF{ 0.8, 0.6 });
		}

		// ボタンが押されたら true を返す
		return (enabled && rect.leftClicked());
	}

	// ゲームの進行状況
	struct GameInfo
	{
		// クッキーの数
		double cookies = 0.0;

		// 農場の数
		int32 farmCount = 0;

		// 工場の数
		int32 factoryCount = 0;

		// 毎秒のクッキー生産量 (cookies per second)
		int32 getCPS() const
		{
			return (farmCount + factoryCount * 10);
		}

		// 農場の価格
		int32 getFarmCost() const
		{
			return (10 + farmCount * 10);
		}

		// 工場の価格
		int32 getFactoryCost() const
		{
			return (100 + factoryCount * 100);
		}
	};

	// 背景を描く関数
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	// クッキーの枚数と CPS を表示する関数
	void DrawCookieCount(const GameInfo& game, const Font& font)
	{
		// クッキーの数を整数で表示する
		font(U"{:.0f}"_fmt(game.cookies)).drawAt(60, Vec2{ 170, 100 });

		// クッキーの生産量を表示する
		font(U"{} CPS"_fmt(game.getCPS())).drawAt(24, Vec2{ 170, 160 });
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };
		const Texture farmEmoji{ U"🌾"_emoji };
		const Texture factoryEmoji{ U"🏭"_emoji };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// クッキーのクリック円
		const Circle cookieCircle{ 170, 300, 90 };

		// ゲームの進行状況
		GameInfo game;

		// クッキーの表示サイズ（倍率）
		double cookieScale = 1.5;

		// ゲームの経過時間の蓄積（秒）
		double accumulatedTime = 0.0;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	更新
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// ゲームの経過時間を加算する
			accumulatedTime += deltaTime;

			// 0.1 秒以上蓄積していたら
			if (0.1 <= accumulatedTime)
			{
				// 0.1 秒分のクッキー生産を加算する
				game.cookies += (game.getCPS() * 0.1);

				accumulatedTime -= 0.1;
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

				game.cookies += 1;
			}

			// クッキーの表示サイズを回復する
			cookieScale = Min((cookieScale + deltaTime), 1.5);

			/////////////////////////////////
			//
			//	描画
			//
			/////////////////////////////////

			// 背景を描く
			DrawBackground();

			// クッキーの数と CPS を表示する
			DrawCookieCount(game, font);

			// クッキーを描画する
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);

			// 農場ボタン
			if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"クッキー農場", U"C{} / 1 CPS"_fmt(game.getFarmCost()), game.farmCount, (game.getFarmCost() <= game.cookies)))
			{
				game.cookies -= game.getFarmCost();
				++game.farmCount;
			}

			// 工場ボタン
			if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"クッキー工場", U"C{} / 10 CPS"_fmt(game.getFactoryCost()), game.factoryCount, (game.getFactoryCost() <= game.cookies)))
			{
				game.cookies -= game.getFactoryCost();
				++game.factoryCount;
			}
		}
	}
	```
