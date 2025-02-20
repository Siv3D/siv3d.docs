# 40. インタラクションの実装
チュートリアル 3 ～ 39 の内容を使って、インタラクティブなプログラムを作成します。

## 40.1 クリックした場所への円の配置

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
			// クリックした位置に半径 10 ~ 30 の円を追加する
			circles << Circle{ Cursor::Pos(), Random(10.0, 30.0) };
		}

		DrawCircles(circles);
	}
}
```


## 40.2 グリッドのマスの色塗り

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/2.gif)

```cpp
# include <Siv3D.hpp>

void UpdateGrid(Grid<int32>& grid)
{
	// クリックされていない場合は何もしない
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
				// クリックのたびに要素を 0 → 1 → 2 → 3 → 0 → 1 → ... と変化させる
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

	// 8x6 の二次元配列を作成し、全ての要素を 0 で初期化する
	Grid<int32> grid(8, 6);

	while (System::Update())
	{
		UpdateGrid(grid);

		DrawGrid(grid);
	}
}
```


## 40.3 バウンドする複数のボール

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


## 40.4 抽選

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

	// 抽選中は空の文字列
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


## 40.5 弾幕の発射

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/5.png)

```cpp
# include <Siv3D.hpp>

struct Bullet
{
	// 位置
	Vec2 pos;

	// 速度
	Vec2 velocity;
};

// 敵の状態
struct EnemyState
{
	// 弾の発射周期（秒）
	double fireInterval = 0.08;

	// 蓄積時間（秒）
	double accumulatedTime = 0.0;

	// 弾の発射方向
	double bulletLaunchAngle = 0_deg;

	// 位置
	Vec2 pos;

	// 弾の配列
	Array<Bullet> bullets;

	void update(double deltaTime)
	{
		pos = Vec2{ (400 + Periodic::Sine1_1(4s) * 200.0), 200 };

		accumulatedTime += deltaTime;

		// 蓄積時間が周期を超えたら新しい弾を発射する
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
	// 弾を移動させる
	for (auto& bullet : bullets)
	{
		bullet.pos += (bullet.velocity * deltaTime);
	}

	// 画面外に出た弾を削除する
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

	// 敵の状態
	EnemyState enemyState;

	while (System::Update())
	{
		/////////////////////////////////
		//
		//	更新
		//
		/////////////////////////////////

		const double deltaTime = Scene::DeltaTime();
		
		// 敵の状態を更新する
		enemyState.update(deltaTime);
		
		// 弾の状態を更新する
		UpdateBullets(enemyState.bullets, deltaTime);

		/////////////////////////////////
		//
		//	描画
		//
		/////////////////////////////////
	
		// 敵を描く
		textureEnemy.drawAt(enemyState.pos);
		
		// 弾を描く
		DrawBullets(enemyState.bullets);
	}
}
```


## 40.6 複数の絵文字のマウスでの配置

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/6.png)

```cpp
# include <Siv3D.hpp>

using ItemID = uint32;

struct Item
{
	// 更新時刻（最後にクリックした時刻）
	uint64 updateTime = 0;

	// 中心座標
	Vec2 pos{ 0, 0 };

	// ID
	ItemID id = 0;

	// 絵文字の種類
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
			const Vec2 move = Cursor::DeltaF(); // 前フレームからのマウスの移動量
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
	// 更新時刻が古い順に描画する
	for (int32 i = (static_cast<int32>(items.size()) - 1); 0 <= i; --i)
	{
		const auto& item = items[i];

		// マウスオーバーしているか、選択されているアイテムは少し大きく描画する
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

	// 選択されているアイテムの ID
	Optional<ItemID> selectedItemID;

	while (System::Update())
	{
		/////////////////////////////////
		//
		//	更新
		//
		/////////////////////////////////

		if (MouseL.up())
		{
			// アイテムの選択を解除する
			selectedItemID.reset();
		}

		// マウスオーバーしているアイテムの ID
		Optional<ItemID> mouseOverItemID;

		if (selectedItemID)
		{
			// 選択中のアイテムをマウスで移動させる
			MoveItem(items, *selectedItemID);
		}
		else
		{
			// アイテムを選択する
			SelectItem(items, mouseOverItemID, selectedItemID);
		}

		// 更新時刻が新しい順に並び替える
		SortByUpdateTime(items);

		/////////////////////////////////
		//
		//	描画
		//
		/////////////////////////////////

		// アイテムを描画する
		DrawItems(items, emojis, mouseOverItemID, selectedItemID);
	}
}
```


## 40.7 慣性のある移動

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


## 40.8 ゲームのメッセージボックス

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/8.gif)

```cpp
# include <Siv3D.hpp>

struct DialogBox
{
	Rect rect{ 40, 440, 720, 120 };

	// メッセージの配列
	Array<String> messages;

	// 現在のメッセージのインデックス
	size_t messageIndex = 0;

	// メッセージの表示時間を計測するストップウォッチ
	Stopwatch stopwatch;

	bool isFinished() const
	{
		// 表示する文字数
		const int32 count = Max(((stopwatch.ms() - 200) / 24), 0);

		// 現在のメッセージがすべて表示されているか
		return (static_cast<int32>(messages[messageIndex].length()) <= count);
	}

	void update()
	{
		if (isFinished() && (rect.leftClicked() || KeySpace.down()))
		{
			// 次のメッセージに切り替える
			++messageIndex %= messages.size();

			// ストップウォッチをリセットする
			stopwatch.restart();
		}
	}

	void draw(const Font& font) const
	{
		// 表示する文字数
		const int32 count = Max(((stopwatch.ms() - 200) / 24), 0);

		// 会話ボックスを描画する
		rect.rounded(10).drawShadow(Vec2{ 1, 1 }, 8).draw().drawFrame(2, ColorF{ 0.4 });

		// 会話を描画する
		font(messages[messageIndex].substr(0, count)).draw(28, rect.stretched(-36, -20), ColorF{ 0.2 });

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		if (isFinished())
		{
			// メッセージの表示が終わっていたら、▼ を描画する
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


## 40.9 メッセージログ

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

			// エンターキーが押されてテキストボックスが非アクティブになったか
			enter = (previous && (textEditState.active == false) && textEditState.enterKey);
		}

		if (SimpleGUI::Button(U"Submit", Vec2{ 660, 280 }, 100, (not textEditState.text.isEmpty()))
			|| enter)
		{
			// リストボックスにテキストを追加する
			listBoxState.items << textEditState.text;

			// 追加したテキストが見えるようにスクロール位置を最大にする（次の SimpleGUI::ListBox() で適切な値に補正される）
			listBoxState.scroll = Largest<int32>;

			// テキストボックスをクリアする
			textEditState.clear();

			// エンターキーが押されてテキストボックスが非アクティブになった場合、再びアクティブにする
			if (enter)
			{
				textEditState.active = true;
			}
		}
	}
}
```


## 40.10 徐々に変化する数値

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


## 40.11 リストの並び替え

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/interaction/11.gif)

```cpp
# include <Siv3D.hpp>

struct Item
{
	int32 id = 0;

	String text;

	// アイテムの描画
	void draw(const Vec2& basePos, const Font& font, int32 order) const
	{
		drawImpl(getRect(basePos, order), font, false);
	}

	// 掴んでいるアイテムの描画
	void drawGrabbed(const Vec2& basePos, const Font& font, const Vec2& offset, int32 order) const
	{
		drawImpl(getRect(basePos, order).movedBy(Cursor::PosF() - offset), font, true);
	}

	// アイテムの長方形を返す
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

		font(text).draw(30, Arg::leftCenter = rect.leftCenter().movedBy(30, 0), ColorF{ 0.11 });
	}
};

// 掴んでいるアイテムの情報
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
		// アイテムの長方形
		const RectF rect = item.getRect(basePos, order);

		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);

			if (rect.leftClicked())
			{
				// アイテムを掴む
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
	// 以前と異なるリスト内順序の場合
	if (targetOrder != grabbedItem.oldOrder)
	{
		// アイテムを一旦コピー
		auto tmp = std::move(items[grabbedItem.oldOrder]);

		// 以前の場所にあったアイテムを削除する
		items.erase(items.begin() + grabbedItem.oldOrder);

		// 新しい場所にアイテムを挿入する
		items.insert((items.begin() + targetOrder), std::move(tmp));
	}
}

void DrawItems(const Array<Item>& items, const Point& basePos, const Font& font,
	const Optional<int32>& targetOrder, const Optional<GrabbedItem>& grabbedItem)
{
	for (int32 order = 0; const auto & item : items)
	{
		// そこに挿入しようとしていたら、その位置をスキップする
		if (targetOrder == order)
		{
			++order;
		}

		// 現在掴んでいるアイテムは、元あった場所には描画しない
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
	// 掴んでいるアイテムだけを描画する
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
		// リストの背景を描画する
		RectF{ basePos, 400, 600 }.stretched(24).rounded(8).draw(ColorF{ 0.9 });

		// アイテムを掴む処理
		if (not grabbedItem)
		{
			grabbedItem = GrabItem(items, basePos);
		}

		// 掴んでいるアイテムの真下のリスト内順序。アイテムを掴んでいない場合は none
		Optional<int32> targetOrder;

		if (grabbedItem)
		{
			targetOrder = Clamp(((Cursor::Pos().y - basePos.y) / 80), 0, (static_cast<int32>(items.size()) - 1));

			// 掴んでいるアイテムを置く処理
			if (MouseL.up())
			{
				// アイテムの並び順を変更する
				RearrangeItems(items, *targetOrder, *grabbedItem);

				// アイテムを掴んでいない状態にする
				grabbedItem.reset();
				targetOrder.reset();
			}

			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// リスト上のアイテムを描画する
		DrawItems(items, basePos, font, targetOrder, grabbedItem);

		// 掴んでいるアイテムを描画する
		if (grabbedItem)
		{
			DrawGrabbedItem(items, basePos, font, *grabbedItem);
		}
	}
}
```
