# 40. Creating Interactive UI
Create interactive programs using the content from Tutorials 3-39.

## 40.1 Placing Circles at Clicked Locations

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/1.gif)

```cpp
# include <Siv3D.hpp>

void DrawCircles(const Array<Circle>& circles)
{
	for (const auto& circle : circles)
	{
		circle.draw(HSV{ circle.center.x, 0.8, 0.9 });
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	Array<Circle> circles;

	while (System::Update())
	{
		if (MouseL.down())
		{
			// Add a circle with radius 10-30 at clicked position
			circles << Circle{ Cursor::Pos(), Random(10.0, 30.0) };
		}

		DrawCircles(circles);
	}
}
```


## 40.2 Grid Tile Coloring

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/2.gif)

```cpp
# include <Siv3D.hpp>

void UpdateGrid(Grid<int32>& grid)
{
	// Do nothing if not clicked
	if (not MouseL.down())
	{
		return;
	}

	for (int32 y = 0; y < grid.height(); ++y)
	{
		for (int32 x = 0; x < grid.width(); ++x)
		{
			const RectF rect{ (x * 100), (y * 100), 100 };

			if (rect.mouseOver())
			{
				// Change element on each click: 0 → 1 → 2 → 3 → 0 → 1 → ...
				++grid[y][x] %= 4;
			}
		}
	}
}

void DrawGrid(const Grid<int32>& grid)
{
	for (int32 y = 0; y < grid.height(); ++y)
	{
		for (int32 x = 0; x < grid.width(); ++x)
		{
			const RectF rect{ (x * 100), (y * 100), 100 };
			const ColorF color{ (3 - grid[y][x]) / 3.0 };
			rect.stretched(-1).draw(color);
		}
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// Create 8x6 2D array and initialize all elements to 0
	Grid<int32> grid(8, 6);

	while (System::Update())
	{
		UpdateGrid(grid);

		DrawGrid(grid);
	}
}
```


## 40.3 Multiple Bouncing Balls

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/3.png)

```cpp
# include <Siv3D.hpp>

struct Ball
{
	Vec2 pos;

	Vec2 velocity;
};

void UpdateBalls(Array<Ball>& balls, double ballRadius)
{
	const Size sceneSize{ 800, 600 };

	for (auto& ball : balls)
	{
		ball.pos += (ball.velocity * Scene::DeltaTime());

		if ((ball.pos.x <= ballRadius) || (sceneSize.x <= (ball.pos.x + ballRadius)))
		{
			ball.velocity.x *= -1.0;
		}

		if ((ball.pos.y <= ballRadius) || (sceneSize.y <= (ball.pos.y + ballRadius)))
		{
			ball.velocity.y *= -1.0;
		}
	}
}

void DrawBalls(const Array<Ball>& balls, double ballRadius)
{
	for (const auto& ball : balls)
	{
		Circle{ ball.pos, ballRadius }.draw().drawFrame(2, 0, ColorF{ 0.2 });
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const double ballRadius = 20.0;

	Array<Ball> balls;

	for (int32 i = 0; i < 5; ++i)
	{
		balls << Ball{ RandomVec2(Scene::Rect().stretched(-ballRadius)), RandomVec2(200) };
	}

	while (System::Update())
	{
		UpdateBalls(balls, ballRadius);

		DrawBalls(balls, ballRadius);
	}
}
```


## 40.4 Lottery

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/4.png)

```cpp
# include <Siv3D.hpp>

void DrawBox(const Rect& rect, const Font& font, const String& text)
{
	rect.rounded(6).draw();

	rect.stretched(-3).rounded(3).drawFrame(2, ColorF{ 0.75 });

	font(text).drawAt(60, rect.center(), ColorF{ 0.2 });
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	const Array<String> options = { U"New York", U"London", U"Paris", U"Tokyo", U"Sydney", U"Berlin" };

	const Rect rect{ Arg::center(400, 240), 400, 100 };

	// Empty string while drawing
	String result;

	while (System::Update())
	{
		if (result)
		{
			DrawBox(rect, font, result);

			if (SimpleGUI::Button(U"Start", Vec2{ 340, 340 }, 120))
			{
				result.clear();
			}
		}
		else
		{
			DrawBox(rect, font, options.choice());

			if (SimpleGUI::Button(U"Stop", Vec2{ 340, 340 }, 120))
			{
				result = options.choice();
			}
		}
	}
}
```


## 40.5 Bullet Hell Shooting

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/5.png)

```cpp
# include <Siv3D.hpp>

struct Bullet
{
	// Position
	Vec2 pos;

	// Velocity
	Vec2 velocity;
};

// Enemy state
struct EnemyState
{
	// Bullet firing interval (seconds)
	double fireInterval = 0.08;

	// Accumulated time (seconds)
	double accumulatedTime = 0.0;

	// Bullet firing direction
	double bulletLaunchAngle = 0_deg;

	// Position
	Vec2 pos;

	// Bullet array
	Array<Bullet> bullets;

	void update(double deltaTime)
	{
		pos = Vec2{ (400 + Periodic::Sine1_1(4s) * 200.0), 200 };

		accumulatedTime += deltaTime;

		// Fire new bullet when accumulated time exceeds interval
		if (fireInterval <= accumulatedTime)
		{
			const Vec2 velocity = Circular{ 120, bulletLaunchAngle };

			bullets << Bullet{ pos, velocity };

			bulletLaunchAngle += 15_deg;

			accumulatedTime -= fireInterval;
		}
	}
};

void UpdateBullets(Array<Bullet>& bullets, double deltaTime)
{
	// Move bullets
	for (auto& bullet : bullets)
	{
		bullet.pos += (bullet.velocity * deltaTime);
	}

	// Remove bullets that went off screen
	const Rect sceneRect{ 800, 600 };
	bullets.remove_if([&](const Bullet& bullet) { return (not bullet.pos.intersects(sceneRect)); });
}

void DrawBullets(const Array<Bullet>& bullets)
{
	for (const auto& bullet : bullets)
	{
		Circle{ bullet.pos, 8 }.draw().drawFrame(2, 0, ColorF{ 0.2 });
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture textureEnemy{ U"🛸"_emoji };

	// Enemy state
	EnemyState enemyState;

	while (System::Update())
	{
		/////////////////////////////////
		//
		//	Update
		//
		/////////////////////////////////

		const double deltaTime = Scene::DeltaTime();
		
		// Update enemy state
		enemyState.update(deltaTime);
		
		// Update bullet state
		UpdateBullets(enemyState.bullets, deltaTime);

		/////////////////////////////////
		//
		//	Draw
		//
		/////////////////////////////////
	
		// Draw enemy
		textureEnemy.drawAt(enemyState.pos);
		
		// Draw bullets
		DrawBullets(enemyState.bullets);
	}
}
```


## 40.6 Placing Multiple Emoji with Mouse

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/6.png)

```cpp
# include <Siv3D.hpp>

using ItemID = uint32;

struct Item
{
	// Update time (last clicked time)
	uint64 updateTime = 0;

	// Center coordinates
	Vec2 pos{ 0, 0 };

	// ID
	ItemID id = 0;

	// Emoji type
	int32 type = 0;
};

Array<Item> GenerateItems()
{
	Array<Item> items;

	for (int32 i = 0; i < 12; ++i)
	{
		const uint64 updateTime = Time::GetMillisec();
		const Vec2 pos = RandomVec2(Scene::Rect().stretched(-50));
		const ItemID id = (i + 1);
		const int32 type = Random(0, 5);
		items << Item{ updateTime, pos, id, type };
	}

	return items;
}

void MoveItem(Array<Item>& items, ItemID selectedItemID)
{
	for (auto& item : items)
	{
		if (item.id == selectedItemID)
		{
			const Vec2 move = Cursor::DeltaF(); // Mouse movement from previous frame
			item.pos.moveBy(move);
			return;
		}
	}
}

void SelectItem(Array<Item>& items,
	Optional<ItemID>& mouseOverItemID, Optional<ItemID>& selectedItemID)
{
	for (auto& item : items)
	{
		if (Circle{ item.pos, 50 }.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
			mouseOverItemID = item.id;

			if (MouseL.down())
			{
				item.updateTime = Time::GetMillisec();
				selectedItemID = item.id;
			}

			return;
		}
	}
}

void SortByUpdateTime(Array<Item>& items)
{
	items.sort_by([](const Item& a, const Item& b) { return a.updateTime > b.updateTime; });
}

void DrawItems(const Array<Item>& items, const Array<Texture>& emojis,
	const Optional<ItemID>& mouseOverItemID, const Optional<ItemID>& selectedItemID)
{
	// Draw in order of oldest update time
	for (int32 i = (static_cast<int32>(items.size()) - 1); 0 <= i; --i)
	{
		const auto& item = items[i];

		// Draw items being mouse-overed or selected slightly larger
		const bool mouseOver = ((item.id == mouseOverItemID) || (item.id == selectedItemID));

		emojis[item.type].scaled(mouseOver ? 1.1 : 1.0).drawAt(item.pos);
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Array<Texture> emojis =
	{
		Texture{ U"🍔"_emoji },
		Texture{ U"🍅"_emoji },
		Texture{ U"🥗"_emoji },
		Texture{ U"🍣"_emoji },
		Texture{ U"🍩"_emoji },
		Texture{ U"🍙"_emoji },
	};

	Array<Item> items = GenerateItems();

	// ID of selected item
	Optional<ItemID> selectedItemID;

	while (System::Update())
	{
		/////////////////////////////////
		//
		//	Update
		//
		/////////////////////////////////

		if (MouseL.up())
		{
			// Deselect item
			selectedItemID.reset();
		}

		// ID of mouse-overed item
		Optional<ItemID> mouseOverItemID;

		if (selectedItemID)
		{
			// Move selected item with mouse
			MoveItem(items, *selectedItemID);
		}
		else
		{
			// Select item
			SelectItem(items, mouseOverItemID, selectedItemID);
		}

		// Sort by newest update time
		SortByUpdateTime(items);

		/////////////////////////////////
		//
		//	Draw
		//
		/////////////////////////////////

		// Draw items
		DrawItems(items, emojis, mouseOverItemID, selectedItemID);
	}
}
```


## 40.7 Movement with Inertia

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/7.gif)

```cpp
# include <Siv3D.hpp>

struct SmoothedVec2
{
	Vec2 current{ 400, 300 };

	Vec2 target = current;

	Vec2 velocity{ 0, 0 };

	void update()
	{
		current = Math::SmoothDamp(current, target, velocity, 0.3);
	}
};

void Main()
{
	Scene::SetBackground(Palette::White);

	const double speed = 300.0;

	SmoothedVec2 pos;

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		if (KeyLeft.pressed())
		{
			pos.target.x -= (speed * deltaTime);
		}

		if (KeyRight.pressed())
		{
			pos.target.x += (speed * deltaTime);
		}

		if (KeyUp.pressed())
		{
			pos.target.y -= (speed * deltaTime);
		}

		if (KeyDown.pressed())
		{
			pos.target.y += (speed * deltaTime);
		}

		pos.update();

		RectF{ Arg::center = pos.current, 120, 80 }.draw(ColorF{ 0.2, 0.6, 0.9 });
	}
}
```


## 40.8 Game Message Box

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/8.gif)

```cpp
# include <Siv3D.hpp>

struct DialogBox
{
	Rect rect{ 40, 440, 720, 120 };

	// Message array
	Array<String> messages;

	// Current message index
	size_t messageIndex = 0;

	// Stopwatch for measuring message display time
	Stopwatch stopwatch;

	bool isFinished() const
	{
		// Number of characters to display
		const int32 count = Max(((stopwatch.ms() - 200) / 24), 0);

		// Whether current message is fully displayed
		return (static_cast<int32>(messages[messageIndex].length()) <= count);
	}

	void update()
	{
		if (isFinished() && (rect.leftClicked() || KeySpace.down()))
		{
			// Switch to next message
			++messageIndex %= messages.size();

			// Reset stopwatch
			stopwatch.restart();
		}
	}

	void draw(const Font& font) const
	{
		// Number of characters to display
		const int32 count = Max(((stopwatch.ms() - 200) / 24), 0);

		// Draw dialog box
		rect.rounded(10).drawShadow(Vec2{ 1, 1 }, 8).draw().drawFrame(2, ColorF{ 0.4 });

		// Draw conversation
		font(messages[messageIndex].substr(0, count)).draw(28, rect.stretched(-36, -20), ColorF{ 0.2 });

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		if (isFinished())
		{
			// Draw ▼ when message display is finished
			Triangle{ rect.br().movedBy(-30, -30), 20, 180_deg }.draw(ColorF{ 0.2, Periodic::Sine0_1(2.0s) });
		}
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Medium };

	DialogBox dialogBox;
	dialogBox.messages =
	{
		U"Twinkle, twinkle, little star,\nHow I wonder what you are!",
		U"Up above the world so high,\nLike a diamond in the sky.",
		U"When the blazing sun is gone,\nWhen he nothing shines upon,",
		U"Then you show your little light,\nTwinkle, twinkle, all the night.",
	};
	dialogBox.stopwatch.start();

	while (System::Update())
	{
		dialogBox.update();
		dialogBox.draw(font);
	}
}
```


## 40.9 Message Log

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/9.gif)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	TextEditState textEditState;

	ListBoxState listBoxState;

	while (System::Update())
	{
		SimpleGUI::ListBox(listBoxState, Vec2{ 40, 40 }, 720, 220);

		bool enter = false;
		{
			const bool previous = textEditState.active;

			SimpleGUI::TextBox(textEditState, Vec2{ 40, 280 }, 600);

			// Check if Enter key was pressed and text box became inactive
			enter = (previous && (textEditState.active == false) && textEditState.enterKey);
		}

		if (SimpleGUI::Button(U"Submit", Vec2{ 660, 280 }, 100, (not textEditState.text.isEmpty()))
			|| enter)
		{
			// Add text to list box
			listBoxState.items << textEditState.text;

			// Set scroll position to maximum so added text is visible (will be corrected to proper value in next SimpleGUI::ListBox())
			listBoxState.scroll = Largest<int32>;

			// Clear text box
			textEditState.clear();

			// If Enter key made text box inactive, make it active again
			if (enter)
			{
				textEditState.active = true;
			}
		}
	}
}
```


## 40.10 Gradually Changing Numbers

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/10.gif)

```cpp
# include <Siv3D.hpp>

struct SmoothedInt
{
	double current = 0.0;

	int32 target = 0;

	double velocity = 0.0;

	void update()
	{
		current = Math::SmoothDamp(current, target, velocity, 0.3);
	}

	int32 rounded() const
	{
		return static_cast<int32>(Math::Round(current));
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	SmoothedInt value;

	while (System::Update())
	{
		value.update();

		if (SimpleGUI::Button(U"+1", Vec2{ 200, 100 }, 80))
		{
			++value.target;
		}

		if (SimpleGUI::Button(U"-1", Vec2{ 300, 100 }, 80))
		{
			--value.target;
		}

		if (SimpleGUI::Button(U"+10", Vec2{ 400, 100 }, 80))
		{
			value.target += 10;
		}

		if (SimpleGUI::Button(U"-10", Vec2{ 500, 100 }, 80))
		{
			value.target -= 10;
		}

		if (SimpleGUI::Button(U"+100", Vec2{ 600, 100 }, 80))
		{
			value.target += 100;
		}

		if (SimpleGUI::Button(U"-100", Vec2{ 700, 100 }, 80))
		{
			value.target -= 100;
		}

		font(value.rounded()).draw(40, Arg::topRight(160, 90), ColorF{ 0.2 });
	}
}
```


## 40.11 List Reordering

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/11.gif)

```cpp
# include <Siv3D.hpp>

struct Item
{
	int32 id = 0;

	String text;

	// Draw item
	void draw(const Vec2& basePos, const Font& font, int32 order) const
	{
		drawImpl(getRect(basePos, order), font, false);
	}

	// Draw grabbed item
	void drawGrabbed(const Vec2& basePos, const Font& font, const Vec2& offset, int32 order) const
	{
		drawImpl(getRect(basePos, order).movedBy(Cursor::PosF() - offset), font, true);
	}

	// Return item rectangle
	RectF getRect(const Vec2& basePos, int32 order) const
	{
		return{ basePos.movedBy(0, (80 * order)), 400, 70 };
	}

private:

	void drawImpl(const RectF& rect, const Font& font, bool shadow) const
	{
		if (shadow)
		{
			rect.rounded(8).drawShadow(Vec2{ 2, 2 }, 16, 2).draw();
		}
		else
		{
			rect.rounded(8).draw();
		}

		font(text).draw(30, Arg::leftCenter = rect.leftCenter().movedBy(30, 0), ColorF{ 0.1 });
	}
};

// Information about grabbed item
struct GrabbedItem
{
	int32 id = 0;

	int32 oldOrder = 0;

	Vec2 offset{ 0, 0 };
};

Optional<GrabbedItem> GrabItem(const Array<Item>& items, const Point& basePos)
{
	for (int32 order = 0; auto & item : items)
	{
		// Item rectangle
		const RectF rect = item.getRect(basePos, order);

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);

			if (rect.leftClicked())
			{
				// Grab item
				return GrabbedItem{ item.id, order, Cursor::PosF() };
			}

			break;
		}

		++order;
	}

	return none;
}

void RearrangeItems(Array<Item>& items, int32 targetOrder, const GrabbedItem& grabbedItem)
{
	// If different from previous list order
	if (targetOrder != grabbedItem.oldOrder)
	{
		// Copy item temporarily
		auto tmp = std::move(items[grabbedItem.oldOrder]);

		// Remove item from previous position
		items.erase(items.begin() + grabbedItem.oldOrder);

		// Insert item at new position
		items.insert((items.begin() + targetOrder), std::move(tmp));
	}
}

void DrawItems(const Array<Item>& items, const Point& basePos, const Font& font,
	const Optional<int32>& targetOrder, const Optional<GrabbedItem>& grabbedItem)
{
	for (int32 order = 0; const auto & item : items)
	{
		// Skip position if trying to insert there
		if (targetOrder == order)
		{
			++order;
		}

		// Don't draw currently grabbed item at its original position
		if (grabbedItem && (grabbedItem->id == item.id))
		{
			continue;
		}

		item.draw(basePos, font, order);

		++order;
	}
}

void DrawGrabbedItem(const Array<Item>& items, const Point& basePos, const Font& font, const GrabbedItem& grabbedItem)
{
	// Draw only the grabbed item
	for (const auto& item : items)
	{
		if (grabbedItem.id == item.id)
		{
			item.drawGrabbed(basePos, font, grabbedItem.offset, grabbedItem.oldOrder);

			return;
		}
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	Array<Item> items =
	{
		{ 111, U"Apple" },
		{ 222, U"Bird" },
		{ 333, U"Cat" },
		{ 444, U"Dog" },
		{ 555, U"Elephant" },
	};

	const Point basePos{ 80, 80 };

	Optional<GrabbedItem> grabbedItem;

	while (System::Update())
	{
		// Draw list background
		RectF{ basePos, 400, 600 }.stretched(24).rounded(8).draw(ColorF{ 0.9 });

		// Item grabbing process
		if (not grabbedItem)
		{
			grabbedItem = GrabItem(items, basePos);
		}

		// List order directly under grabbed item. None if no item grabbed
		Optional<int32> targetOrder;

		if (grabbedItem)
		{
			targetOrder = Clamp(((Cursor::Pos().y - basePos.y) / 80), 0, (static_cast<int32>(items.size()) - 1));

			// Item dropping process
			if (MouseL.up())
			{
				// Change item order
				RearrangeItems(items, *targetOrder, *grabbedItem);

				// Stop grabbing item
				grabbedItem.reset();
				targetOrder.reset();
			}

			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// Draw items on list
		DrawItems(items, basePos, font, targetOrder, grabbedItem);

		// Draw grabbed item
		if (grabbedItem)
		{
			DrawGrabbedItem(items, basePos, font, *grabbedItem);
		}
	}
}
```