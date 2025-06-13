# 29. Cookie Clicker
Using the content from tutorials 3-28, we'll create a cookie clicker-style game.

!!! info "What is Cookie Clicker"
    - Cookie Clicker is a game where you click cookies to increase the number of cookies
    - You can use the cookies you've accumulated to purchase production facilities that generate more cookies
    - The original game was released in 2013 and became popular, leading to various derivative games

    - [Cookie Clicker Official Page :material-open-in-new:](https://orteil.dashnet.org/cookieclicker/){:target="_blank"}
    - [Wikipedia: Cookie Clicker :material-open-in-new:](https://en.wikipedia.org/wiki/Cookie_Clicker){:target="_blank"}


## 29.1 Basic Implementation
- Create a `GameInfo` class to represent the game's progress
	- To enable calculations for increasing cookies every 0.1 seconds later, manage the number of cookies using `double` type instead of `int32`
	- Represent the number of cookie production facilities "farms" and "factories" using `int32`
- Draw the game screen background as a gradient rectangle covering the entire screen
- To prevent the `Main` function from becoming too complex, divide processes into functions as appropriate
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/1.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Game progress
	struct GameInfo
	{
		// Number of cookies
		double cookies = 0.0;

		// Number of farms
		int32 farmCount = 0;

		// Number of factories
		int32 factoryCount = 0;
	};

	// Function to draw the background
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	void Main()
	{
		// Game progress
		GameInfo game;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();
		}
	}
	```


## 29.2 Drawing Cookies
- Create a texture from a cookie emoji and draw it large on the screen
- Manage the cookie display size with a variable `cookieScale` to add motion effects to the cookie size
- Prepare a circle `cookieCircle` for cookie click detection, and increase the number of cookies when clicked
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/2.png)

??? memo "Code"
	```cpp hl_lines="24-27 32-33 43-47 58-59"
	# include <Siv3D.hpp>

	// Game progress
	struct GameInfo
	{
		// Number of cookies
		double cookies = 0.0;

		// Number of farms
		int32 farmCount = 0;

		// Number of factories
		int32 factoryCount = 0;
	};

	// Function to draw the background
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };

		// Cookie click circle
		const Circle cookieCircle{ 170, 300, 90 };

		// Game progress
		GameInfo game;

		// Cookie display size (scale)
		double cookieScale = 1.5;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			// If the cookie circle is left-clicked
			if (cookieCircle.leftClicked())
			{
				game.cookies += 1;
			}

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Draw the cookie
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);
		}
	}
	```


## 29.3 Displaying Cookie Count
- Display the number of cookies as an integer
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/3.png)

??? memo "Code"
	```cpp hl_lines="22-27 33 67-68"
	# include <Siv3D.hpp>

	// Game progress
	struct GameInfo
	{
		// Number of cookies
		double cookies = 0.0;

		// Number of farms
		int32 farmCount = 0;

		// Number of factories
		int32 factoryCount = 0;
	};

	// Function to draw the background
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	// Function to display cookie count
	void DrawCookieCount(const GameInfo& game, const Font& font)
	{
		// Display the number of cookies as an integer
		font(U"{:.0f}"_fmt(game.cookies)).drawAt(60, Vec2{ 170, 100 });
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Cookie click circle
		const Circle cookieCircle{ 170, 300, 90 };

		// Game progress
		GameInfo game;

		// Cookie display size (scale)
		double cookieScale = 1.5;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			// If the cookie circle is left-clicked
			if (cookieCircle.leftClicked())
			{
				game.cookies += 1;
			}

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Display the cookie count
			DrawCookieCount(game, font);

			// Draw the cookie
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);
		}
	}
	```


## 29.4 Improving Click Experience
- When the mouse cursor is over `cookieCircle`, change the mouse cursor to a hand shape
- When the cookie is clicked, temporarily reduce `cookieScale`
	- `cookieScale` returns to `1.5` over time
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/4.png)

??? memo "Code"
	```cpp hl_lines="52-58 63 68-69"
	# include <Siv3D.hpp>

	// Game progress
	struct GameInfo
	{
		// Number of cookies
		double cookies = 0.0;

		// Number of farms
		int32 farmCount = 0;

		// Number of factories
		int32 factoryCount = 0;
	};

	// Function to draw the background
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	// Function to display cookie count
	void DrawCookieCount(const GameInfo& game, const Font& font)
	{
		// Display the number of cookies as an integer
		font(U"{:.0f}"_fmt(game.cookies)).drawAt(60, Vec2{ 170, 100 });
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Cookie click circle
		const Circle cookieCircle{ 170, 300, 90 };

		// Game progress
		GameInfo game;

		// Cookie display size (scale)
		double cookieScale = 1.5;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// If the mouse cursor is over the cookie circle
			if (cookieCircle.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// If the cookie circle is left-clicked
			if (cookieCircle.leftClicked())
			{
				cookieScale = 1.3;

				game.cookies += 1;
			}

			// Restore the cookie display size
			cookieScale = Min((cookieScale + deltaTime), 1.5);

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Display the cookie count
			DrawCookieCount(game, font);

			// Draw the cookie
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);
		}
	}
	```


## 29.5 Adding Facility Purchase Buttons
- Referring to **Tutorial 28**, add buttons to purchase cookie production facilities
- When a button is pressed, increase the number of facilities
- Use the `enabled` argument to represent the state where "the button cannot be pressed"
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/5.png)

??? memo "Code"
	```cpp hl_lines="3-49 80-81 136-146"
	# include <Siv3D.hpp>

	// Function to draw buttons
	bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
	{
		// If the mouse cursor is over the button
		if (enabled && rect.mouseOver())
		{
			// Change the mouse cursor to a hand
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// Text color
		const ColorF textColor{ 0.4, 0.3, 0.2 };

		// Button background rounded rectangle
		const RoundRect roundRect = rect.rounded(6);

		// Draw shadow and background
		roundRect
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw(ColorF{ 0.9, 0.8, 0.6 });

		// Draw border
		rect.stretched(-3).rounded(3)
			.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

		// Draw emoji
		texture.scaled(0.5).drawAt(rect.leftCenter().movedBy(50, 0));

		// Draw facility name
		font(name).draw(30, rect.pos.movedBy(100, 15), textColor);

		// Draw facility description
		font(desc).draw(18, rect.pos.movedBy(102, 60), textColor);

		// Draw owned count
		font(count).draw(50, Arg::rightCenter = rect.tr().movedBy(-20, 50), textColor);

		// If disabled
		if (not enabled)
		{
			// Overlay gray semi-transparent
			roundRect.draw(ColorF{ 0.8, 0.6 });
		}

		// Return true if the button is pressed
		return (enabled && rect.leftClicked());
	}

	// Game progress
	struct GameInfo
	{
		// Number of cookies
		double cookies = 0.0;

		// Number of farms
		int32 farmCount = 0;

		// Number of factories
		int32 factoryCount = 0;
	};

	// Function to draw the background
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	// Function to display cookie count
	void DrawCookieCount(const GameInfo& game, const Font& font)
	{
		// Display the number of cookies as an integer
		font(U"{:.0f}"_fmt(game.cookies)).drawAt(60, Vec2{ 170, 100 });
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };
		const Texture farmEmoji{ U"🌾"_emoji };
		const Texture factoryEmoji{ U"🏭"_emoji };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Cookie click circle
		const Circle cookieCircle{ 170, 300, 90 };

		// Game progress
		GameInfo game;

		// Cookie display size (scale)
		double cookieScale = 1.5;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// If the mouse cursor is over the cookie circle
			if (cookieCircle.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// If the cookie circle is left-clicked
			if (cookieCircle.leftClicked())
			{
				cookieScale = 1.3;

				game.cookies += 1;
			}

			// Restore the cookie display size
			cookieScale = Min((cookieScale + deltaTime), 1.5);

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Display the cookie count
			DrawCookieCount(game, font);

			// Draw the cookie
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);

			// Farm button
			if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"Cookie Farm", U"1 CPS", game.farmCount, true))
			{
				++game.farmCount;
			}

			// Factory button
			if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"Cookie Factory", U"10 CPS", game.factoryCount, false))
			{
				++game.factoryCount;
			}
		}
	}
	```


## 29.6 Calculating Cookie Production
- Calculate and display the cookies per second (CPS) based on the type and number of purchased facilities
	- Don't reflect it in the actual cookie count yet
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/6.png)

??? memo "Code"
	```cpp hl_lines="63-67 76 82-83 139"
	# include <Siv3D.hpp>

	// Function to draw buttons
	bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
	{
		// If the mouse cursor is over the button
		if (enabled && rect.mouseOver())
		{
			// Change the mouse cursor to a hand
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// Text color
		const ColorF textColor{ 0.4, 0.3, 0.2 };

		// Button background rounded rectangle
		const RoundRect roundRect = rect.rounded(6);

		// Draw shadow and background
		roundRect
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw(ColorF{ 0.9, 0.8, 0.6 });

		// Draw border
		rect.stretched(-3).rounded(3)
			.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

		// Draw emoji
		texture.scaled(0.5).drawAt(rect.leftCenter().movedBy(50, 0));

		// Draw facility name
		font(name).draw(30, rect.pos.movedBy(100, 15), textColor);

		// Draw facility description
		font(desc).draw(18, rect.pos.movedBy(102, 60), textColor);

		// Draw owned count
		font(count).draw(50, Arg::rightCenter = rect.tr().movedBy(-20, 50), textColor);

		// If disabled
		if (not enabled)
		{
			// Overlay gray semi-transparent
			roundRect.draw(ColorF{ 0.8, 0.6 });
		}

		// Return true if the button is pressed
		return (enabled && rect.leftClicked());
	}

	// Game progress
	struct GameInfo
	{
		// Number of cookies
		double cookies = 0.0;

		// Number of farms
		int32 farmCount = 0;

		// Number of factories
		int32 factoryCount = 0;

		// Cookies per second production
		int32 getCPS() const
		{
			return (farmCount + factoryCount * 10);
		}
	};

	// Function to draw the background
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	// Function to display cookie count and CPS
	void DrawCookieCount(const GameInfo& game, const Font& font)
	{
		// Display the number of cookies as an integer
		font(U"{:.0f}"_fmt(game.cookies)).drawAt(60, Vec2{ 170, 100 });

		// Display cookie production rate
		font(U"{} CPS"_fmt(game.getCPS())).drawAt(24, Vec2{ 170, 160 });
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };
		const Texture farmEmoji{ U"🌾"_emoji };
		const Texture factoryEmoji{ U"🏭"_emoji };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Cookie click circle
		const Circle cookieCircle{ 170, 300, 90 };

		// Game progress
		GameInfo game;

		// Cookie display size (scale)
		double cookieScale = 1.5;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// If the mouse cursor is over the cookie circle
			if (cookieCircle.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// If the cookie circle is left-clicked
			if (cookieCircle.leftClicked())
			{
				cookieScale = 1.3;

				game.cookies += 1;
			}

			// Restore the cookie display size
			cookieScale = Min((cookieScale + deltaTime), 1.5);

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Display the cookie count and CPS
			DrawCookieCount(game, font);

			// Draw the cookie
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);

			// Farm button
			if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"Cookie Farm", U"1 CPS", game.farmCount, true))
			{
				++game.farmCount;
			}

			// Factory button
			if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"Cookie Factory", U"10 CPS", game.factoryCount, false))
			{
				++game.factoryCount;
			}
		}
	}
	```


## 29.7 Implementing Facility Purchase Costs
- Calculate and display the purchase costs for farms and factories
	- Prices increase based on the number purchased
- If the current cookie count is below the purchase cost, disable the button
- When facilities are purchased, subtract the purchase cost from the cookie count
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/7.png)

??? memo "Code"
	```cpp hl_lines="69-79 158 160 165 167"
	# include <Siv3D.hpp>

	// Function to draw buttons
	bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
	{
		// If the mouse cursor is over the button
		if (enabled && rect.mouseOver())
		{
			// Change the mouse cursor to a hand
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// Text color
		const ColorF textColor{ 0.4, 0.3, 0.2 };

		// Button background rounded rectangle
		const RoundRect roundRect = rect.rounded(6);

		// Draw shadow and background
		roundRect
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw(ColorF{ 0.9, 0.8, 0.6 });

		// Draw border
		rect.stretched(-3).rounded(3)
			.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

		// Draw emoji
		texture.scaled(0.5).drawAt(rect.leftCenter().movedBy(50, 0));

		// Draw facility name
		font(name).draw(30, rect.pos.movedBy(100, 15), textColor);

		// Draw facility description
		font(desc).draw(18, rect.pos.movedBy(102, 60), textColor);

		// Draw owned count
		font(count).draw(50, Arg::rightCenter = rect.tr().movedBy(-20, 50), textColor);

		// If disabled
		if (not enabled)
		{
			// Overlay gray semi-transparent
			roundRect.draw(ColorF{ 0.8, 0.6 });
		}

		// Return true if the button is pressed
		return (enabled && rect.leftClicked());
	}

	// Game progress
	struct GameInfo
	{
		// Number of cookies
		double cookies = 0.0;

		// Number of farms
		int32 farmCount = 0;

		// Number of factories
		int32 factoryCount = 0;

		// Cookies per second production
		int32 getCPS() const
		{
			return (farmCount + factoryCount * 10);
		}

		// Farm price
		int32 getFarmCost() const
		{
			return (10 + farmCount * 10);
		}

		// Factory price
		int32 getFactoryCost() const
		{
			return (100 + factoryCount * 100);
		}
	};

	// Function to draw the background
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	// Function to display cookie count and CPS
	void DrawCookieCount(const GameInfo& game, const Font& font)
	{
		// Display the number of cookies as an integer
		font(U"{:.0f}"_fmt(game.cookies)).drawAt(60, Vec2{ 170, 100 });

		// Display cookie production rate
		font(U"{} CPS"_fmt(game.getCPS())).drawAt(24, Vec2{ 170, 160 });
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };
		const Texture farmEmoji{ U"🌾"_emoji };
		const Texture factoryEmoji{ U"🏭"_emoji };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Cookie click circle
		const Circle cookieCircle{ 170, 300, 90 };

		// Game progress
		GameInfo game;

		// Cookie display size (scale)
		double cookieScale = 1.5;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// If the mouse cursor is over the cookie circle
			if (cookieCircle.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// If the cookie circle is left-clicked
			if (cookieCircle.leftClicked())
			{
				cookieScale = 1.3;

				game.cookies += 1;
			}

			// Restore the cookie display size
			cookieScale = Min((cookieScale + deltaTime), 1.5);

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Display the cookie count and CPS
			DrawCookieCount(game, font);

			// Draw the cookie
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);

			// Farm button
			if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"Cookie Farm", U"C{} / 1 CPS"_fmt(game.getFarmCost()), game.farmCount, (game.getFarmCost() <= game.cookies)))
			{
				game.cookies -= game.getFarmCost();
				++game.farmCount;
			}

			// Factory button
			if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"Cookie Factory", U"C{} / 10 CPS"_fmt(game.getFactoryCost()), game.factoryCount, (game.getFactoryCost() <= game.cookies)))
			{
				game.cookies -= game.getFactoryCost();
				++game.factoryCount;
			}
		}
	}
	```


## 29.8 【Complete】Implementing Cookie Production
- Increase the number of cookies every 0.1 seconds based on the number of purchased facilities
	- Every 0.1 seconds, add CPS × 0.1 cookies
		
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/cookie-clicker/8.png)

??? memo "Code"
	```cpp hl_lines="115-116 128-138"
	# include <Siv3D.hpp>

	// Function to draw buttons
	bool Button(const Rect& rect, const Texture& texture, const Font& font, const String& name, const String& desc, int32 count, bool enabled)
	{
		// If the mouse cursor is over the button
		if (enabled && rect.mouseOver())
		{
			// Change the mouse cursor to a hand
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// Text color
		const ColorF textColor{ 0.4, 0.3, 0.2 };

		// Button background rounded rectangle
		const RoundRect roundRect = rect.rounded(6);

		// Draw shadow and background
		roundRect
			.drawShadow(Vec2{ 2, 2 }, 12, 0)
			.draw(ColorF{ 0.9, 0.8, 0.6 });

		// Draw border
		rect.stretched(-3).rounded(3)
			.drawFrame(2, ColorF{ 0.4, 0.3, 0.2 });

		// Draw emoji
		texture.scaled(0.5).drawAt(rect.leftCenter().movedBy(50, 0));

		// Draw facility name
		font(name).draw(30, rect.pos.movedBy(100, 15), textColor);

		// Draw facility description
		font(desc).draw(18, rect.pos.movedBy(102, 60), textColor);

		// Draw owned count
		font(count).draw(50, Arg::rightCenter = rect.tr().movedBy(-20, 50), textColor);

		// If disabled
		if (not enabled)
		{
			// Overlay gray semi-transparent
			roundRect.draw(ColorF{ 0.8, 0.6 });
		}

		// Return true if the button is pressed
		return (enabled && rect.leftClicked());
	}

	// Game progress
	struct GameInfo
	{
		// Number of cookies
		double cookies = 0.0;

		// Number of farms
		int32 farmCount = 0;

		// Number of factories
		int32 factoryCount = 0;

		// Cookies per second production
		int32 getCPS() const
		{
			return (farmCount + factoryCount * 10);
		}

		// Farm price
		int32 getFarmCost() const
		{
			return (10 + farmCount * 10);
		}

		// Factory price
		int32 getFactoryCost() const
		{
			return (100 + factoryCount * 100);
		}
	};

	// Function to draw the background
	void DrawBackground()
	{
		Rect{ 800, 600 }.draw(Arg::top(0.6, 0.5, 0.3), Arg::bottom(0.2, 0.5, 0.3));
	}

	// Function to display cookie count and CPS
	void DrawCookieCount(const GameInfo& game, const Font& font)
	{
		// Display the number of cookies as an integer
		font(U"{:.0f}"_fmt(game.cookies)).drawAt(60, Vec2{ 170, 100 });

		// Display cookie production rate
		font(U"{} CPS"_fmt(game.getCPS())).drawAt(24, Vec2{ 170, 160 });
	}

	void Main()
	{
		const Texture cookieEmoji{ U"🍪"_emoji };
		const Texture farmEmoji{ U"🌾"_emoji };
		const Texture factoryEmoji{ U"🏭"_emoji };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Cookie click circle
		const Circle cookieCircle{ 170, 300, 90 };

		// Game progress
		GameInfo game;

		// Cookie display size (scale)
		double cookieScale = 1.5;

		// Accumulated game time (seconds)
		double accumulatedTime = 0.0;

		while (System::Update())
		{
			/////////////////////////////////
			//
			//	Update
			//
			/////////////////////////////////

			const double deltaTime = Scene::DeltaTime();

			// Add to the accumulated game time
			accumulatedTime += deltaTime;

			// If 0.1 seconds or more have accumulated
			if (0.1 <= accumulatedTime)
			{
				// Add 0.1 seconds worth of cookie production
				game.cookies += (game.getCPS() * 0.1);

				accumulatedTime -= 0.1;
			}

			// If the mouse cursor is over the cookie circle
			if (cookieCircle.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// If the cookie circle is left-clicked
			if (cookieCircle.leftClicked())
			{
				cookieScale = 1.3;

				game.cookies += 1;
			}

			// Restore the cookie display size
			cookieScale = Min((cookieScale + deltaTime), 1.5);

			/////////////////////////////////
			//
			//	Draw
			//
			/////////////////////////////////

			// Draw the background
			DrawBackground();

			// Display the cookie count and CPS
			DrawCookieCount(game, font);

			// Draw the cookie
			cookieEmoji.scaled(cookieScale).drawAt(cookieCircle.center);

			// Farm button
			if (Button(Rect{ 340, 40, 420, 100 }, farmEmoji, font, U"Cookie Farm", U"C{} / 1 CPS"_fmt(game.getFarmCost()), game.farmCount, (game.getFarmCost() <= game.cookies)))
			{
				game.cookies -= game.getFarmCost();
				++game.farmCount;
			}

			// Factory button
			if (Button(Rect{ 340, 160, 420, 100 }, factoryEmoji, font, U"Cookie Factory", U"C{} / 10 CPS"_fmt(game.getFactoryCost()), game.factoryCount, (game.getFactoryCost() <= game.cookies)))
			{
				game.cookies -= game.getFactoryCost();
				++game.factoryCount;
			}
		}
	}
	```