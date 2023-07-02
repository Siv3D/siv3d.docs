# 36. インタラクションの実装
ここまで学んだことを使って、様々なインタラクティブ要素を実装します。

## 36.1 クリックした場所に円を配置する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/interaction/1.gif)

```cpp
# include <Siv3D.hpp>

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

		for (const auto& circle : circles)
		{
			// x 座標に応じて色を変える
			circle.draw(HSV{ circle.center.x, 0.8, 0.9 });
		}
	}
}
```


## 36.2 グリッドのマスに色を塗る

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/interaction/2.gif)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// 8x6 の二次元配列を作成し、全ての要素を 0 で初期化する
	Grid<int32> grid(8, 6);

	while (System::Update())
	{
		for (int32 y = 0; y < grid.height(); ++y)
		{
			for (int32 x = 0; x < grid.width(); ++x)
			{
				const RectF rect{ (x * 100), (y * 100), 100 };

				if (rect.leftClicked())
				{
                    // クリックのたびに要素を 0 → 1 → 2 → 3 → 0 → 1 → ... と変化させる
					++grid[y][x] %= 4;
				}

				const ColorF color{ (3 - grid[y][x]) / 3.0 };

				rect.stretched(-1).draw(color);
			}
		}
	}
}
```


## 36.3 バウンドする複数のボール

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/interaction/3.png)

```cpp
# include <Siv3D.hpp>

struct Ball
{
	Vec2 pos;

	Vec2 velocity;
};

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
		for (auto& ball : balls)
		{
			ball.pos += (ball.velocity * Scene::DeltaTime());

			if ((ball.pos.x <= ballRadius) || (Scene::Width() <= (ball.pos.x + ballRadius)))
			{
				ball.velocity.x *= -1.0;
			}

			if ((ball.pos.y <= ballRadius) || (Scene::Height() <= (ball.pos.y + ballRadius)))
			{
				ball.velocity.y *= -1.0;
			}
		}

		for (const auto& ball : balls)
		{
			Circle{ ball.pos, ballRadius }.draw();
		}
	}
}
```


## 36.4 抽選

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/interaction/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	const Array<String> options = { U"New York", U"London", U"Paris", U"Tokyo", U"Sydney", U"Berlin" };

	const Rect optionRect{ Arg::center = Scene::Center().movedBy(0, -60), 400, 100 };

	String result;

	while (System::Update())
	{
		optionRect.draw();

		if (result)
		{
			font(result).drawAt(60, optionRect.center(), ColorF{ 0.11 });

			if (SimpleGUI::Button(U"Start", Scene::Center().movedBy(-60, 40), 120))
			{
				result.clear();
			}
		}
		else
		{
			font(options.choice()).drawAt(60, optionRect.center(), ColorF{ 0.11 });

			if (SimpleGUI::Button(U"Stop", Scene::Center().movedBy(-60, 40), 120))
			{
				result = options.choice();
			}
		}
	}
}
```


## 36.5 弾幕を発射する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/interaction/5.png)

```cpp
# include <Siv3D.hpp>

struct Bullet
{
	// 位置
	Vec2 pos;

	// 速度
	Vec2 velocity;
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture textureEnemy{ U"🛸"_emoji };

	// 発射周期（秒）
	const double fireInterval = 0.08;

	// 蓄積時間（秒）
	double accumulatedTime = 0.0;

	// 発射方向
	double angle = 0.0;

	Array<Bullet> bullets;

	while (System::Update())
	{
		ClearPrint();
		Print << bullets.size();

		accumulatedTime += Scene::DeltaTime();

		// 敵の位置
		const Vec2 enemyPos{ (400 + Periodic::Sine1_1(4s) * 200.0), 200 };

		// 蓄積時間が周期を超えたら
		if (fireInterval <= accumulatedTime)
		{
			const Vec2 velocity = Circular{ 120, angle };

			bullets << Bullet{ enemyPos, velocity };

			angle += 15_deg;

			accumulatedTime -= fireInterval;
		}

		// 弾の移動
		for (auto& bullet : bullets)
		{
			bullet.pos += (bullet.velocity * Scene::DeltaTime());
		}

		// 画面外に出た弾を削除
		bullets.remove_if([](const Bullet& bullet) { return (not bullet.pos.intersects(Scene::Rect())); });

		textureEnemy.drawAt(enemyPos);

		for (const auto& bullet : bullets)
		{
			Circle{ bullet.pos, 8 }.draw();
		}
	}
}
```


## 36.6 複数の絵文字をマウスで配置する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/interaction/6.png)

```cpp
# include <Siv3D.hpp>

using ItemID = uint32;

struct Item
{
	// 更新時刻
	uint64 updateTime = 0;

	// 中心座標
	Vec2 pos = Vec2{ 0, 0 };

	// ID
	ItemID id = 0;

	// 絵文字の種類
	int32 type = 0;
};

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

	// ID 発行用のカウンター
	ItemID idCounter = 0;

	Array<Item> items;

	for (int32 i = 0; i < 12; ++i)
	{
		items << Item{ Time::GetMillisec(), RandomVec2(Scene::Rect().stretched(-50)), ++idCounter, Random(0, static_cast<int32>(emojis.size() - 1))};
	}

	// 選択されているアイテムの ID
	Optional<ItemID> selectedItemID;

	while (System::Update())
	{
		// マウスオーバーしているアイテムの ID
		Optional<ItemID> mouseOverItemID;

		if (MouseL.up())
		{
			selectedItemID.reset();
		}

		if (selectedItemID)
		{
			for (auto& item : items)
			{
				if (item.id == *selectedItemID)
				{
					item.pos.moveBy(Cursor::DeltaF());
					break;
				}
			}
		}
		else
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

					break;
				}
			}
		}

		// 更新時刻が新しい順に並び替える
		items.sort_by([](const Item& a, const Item& b) { return a.updateTime > b.updateTime; });

		// 更新時刻が古い順に描画する
		for (int32 i = (static_cast<int32>(items.size()) - 1); 0 <= i; --i)
		{
			const auto& item = items[i];

			// マウスオーバーしているか、選択されているアイテムは少し大きく描画する
			const bool mouseOver = ((item.id == mouseOverItemID) || (item.id == selectedItemID));
			emojis[item.type].scaled(mouseOver ? 1.1 : 1.0).drawAt(item.pos);
		}
	}
}
```


## 36.7 慣性のある移動

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/interaction/7.gif)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const double speed = 300.0;

	Vec2 targetPos{ 400, 300 };

	Vec2 pos{ 400, 300 };

	Vec2 velocity{ 0, 0 };

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		if (KeyLeft.pressed())
		{
			targetPos.x -= (speed * deltaTime);
		}

		if (KeyRight.pressed())
		{
			targetPos.x += (speed * deltaTime);
		}

		if (KeyUp.pressed())
		{
			targetPos.y -= (speed * deltaTime);
		}

		if (KeyDown.pressed())
		{
			targetPos.y += (speed * deltaTime);
		}

		pos = Math::SmoothDamp(pos, targetPos, velocity, 0.3);

		RectF{ Arg::center = pos, 120, 80 }.draw(ColorF{ 0.2, 0.6, 0.9 });
	}
}
```


## 36.8 ゲームのメッセージボックス

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/interaction/8.gif)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Medium };

	// メッセージの配列
	const Array<String> messages =
	{
		U"Twinkle, twinkle, little star,\nHow I wonder what you are!",
		U"Up above the world so high,\nLike a diamond in the sky.",
		U"When the blazing sun is gone,\nWhen he nothing shines upon,",
		U"Then you show your little light,\nTwinkle, twinkle, all the night.",
	};

	// メッセージボックスの位置とサイズ
	const Rect messageBox{ 40, 440, 720, 120 };

	// メッセージのインデックス
	size_t messageIndex = 0;

	// メッセージの表示を開始してからの経過時間を測定するストップウォッチ
	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		// 表示する文字数
		int32 count = Max(((stopwatch.ms() - 200) / 24), 0);

		// 現在のメッセージがすべて表示されているか
		const bool finished = (static_cast<int32>(messages[messageIndex].length()) <= count);

		if (finished && (messageBox.leftClicked() || KeySpace.down()))
		{
			// 次のメッセージに切り替える
			++messageIndex %= messages.size();
			stopwatch.restart();
			count = 0;
		}

		// メッセージボックスを描画する
		messageBox.rounded(10).drawShadow(Vec2{ 1, 1 }, 8).draw().drawFrame(2, ColorF{ 0.4 });

		// メッセージを描画する
		font(messages[messageIndex].substr(0, count)).draw(28, messageBox.stretched(-36, -20), ColorF{ 0.11 });

		if (messageBox.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		if (finished)
		{
			// メッセージの表示が終わっていたら、▼ を描画する
			Triangle{ messageBox.br().movedBy(-30, -30), 20, 180_deg }.draw(ColorF{ 0.11, Periodic::Sine0_1(2.0s) });
		}
	}
}
```


## 36.9 メッセージログ

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/interaction/9.gif)

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


## 36.10 徐々に変化する数値

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/interaction/10.gif)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	int32 targetValue = 0;

	double currentValue = 0.0;

	double velocity = 0.0;

	while (System::Update())
	{
		ClearPrint();
		Print << U"targetValue: " << targetValue;

		currentValue = Math::SmoothDamp(currentValue, targetValue, velocity, 0.3);

		const int32 value = static_cast<int32>(Math::Round(currentValue));

		font(value).draw(40, Arg::topRight(160, 90), ColorF{ 0.11 });

		if (SimpleGUI::Button(U"+1", Vec2{ 200, 100 }, 80))
		{
			++targetValue;
		}

		if (SimpleGUI::Button(U"-1", Vec2{ 300, 100 }, 80))
		{
			--targetValue;
		}

		if (SimpleGUI::Button(U"+10", Vec2{ 400, 100 }, 80))
		{
			targetValue += 10;
		}

		if (SimpleGUI::Button(U"-10", Vec2{ 500, 100 }, 80))
		{
			targetValue -= 10;
		}

		if (SimpleGUI::Button(U"+100", Vec2{ 600, 100 }, 80))
		{
			targetValue += 100;
		}

		if (SimpleGUI::Button(U"-100", Vec2{ 700, 100 }, 80))
		{
			targetValue -= 100;
		}
	}
}
```


## 36.11 リストの並び替え

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/interaction/11.gif)

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
			// アイテムのリスト内順序
			int32 order = 0;

			for (auto& item : items)
			{
				// アイテムの長方形
				const RectF rect = item.getRect(basePos, order);

				if (rect.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);

					if (rect.leftClicked())
					{
						// アイテムを掴む
						grabbedItem = { item.id, order, Cursor::PosF() };
					}

					break;
				}

				++order;
			}
		}

		// 掴んでいるアイテムの真下のリスト内順序。アイテムを掴んでいない場合は -1
		int32 targetOrder = (grabbedItem ? Clamp(((Cursor::Pos().y - basePos.y) / 80), 0, (static_cast<int32>(items.size()) - 1)) : -1);

		// 掴んでいるアイテムを置く処理
		if (grabbedItem && MouseL.up())
		{
			// 以前と異なるリスト内順序の場合
			if (targetOrder != grabbedItem->oldOrder)
			{
				// アイテムを一旦コピー
				auto tmp = std::move(items[grabbedItem->oldOrder]);

				// 以前の場所にあったアイテムを削除する
				items.erase(items.begin() + grabbedItem->oldOrder);

				// 新しい場所にアイテムを挿入する
				items.insert((items.begin() + targetOrder), std::move(tmp));
			}

			// アイテムを掴んでいない状態にする
			grabbedItem.reset();
			targetOrder = -1;
		}

		if (grabbedItem)
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// リスト上のアイテムを描画する
		{
			int32 order = 0;

			for (const auto& item : items)
			{
				if (targetOrder == order)
				{
					++order;
				}

				// その位置に掴んでいるアイテムがある場合は描画しない
				if (grabbedItem && (grabbedItem->id == item.id))
				{
					continue;
				}

				item.draw(basePos, font, order);

				++order;
			}
		}

		// 掴んでいるアイテムを描画する
		if (grabbedItem)
		{
			for (const auto& item : items)
			{
				if (grabbedItem->id == item.id)
				{
					item.drawGrabbed(basePos, font, grabbedItem->offset, grabbedItem->oldOrder);
					break;
				}
			}
		}
	}
}
```
