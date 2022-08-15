# 演習 A - アイテム集めゲーム

## 1. プレイヤーの移動

```cpp
# include <Siv3D.hpp>

void Main()
{
	// プレイヤーの絵文字テクスチャ
	const Texture playerTexture{ U"😃"_emoji };

	// プレイヤーのスピード（ピクセル / 秒)
	const double playerSpeed = 500.0;

	// プレイヤーの座標
	Vec2 playerPos{ 400, 500 };

	while (System::Update())
	{
		////////////////////////////////
		//
		//	状態更新
		//
		////////////////////////////////

		// 前のフレームからの経過時間 (秒)
		const double deltaTime = Scene::DeltaTime();

		// プレイヤーの移動に関する処理
		{
			if (KeyLeft.pressed()) // [←] キーが押されていたら
			{
				playerPos.x -= (playerSpeed * deltaTime);
			}
			else if (KeyRight.pressed()) // [→] キーが押されていたら
			{
				playerPos.x += (playerSpeed * deltaTime);
			}

			// 壁の外に出ないようにする
			// Clamp(x, min, max) は, x を min～max の範囲に収めた値を返す
			playerPos.x = Clamp(playerPos.x, 0.0, 800.0);
		}

		////////////////////////////////
		//
		//	描画
		//
		////////////////////////////////

		// 背景はグラデーションの Rect
		Scene::Rect()
			.draw(Arg::top = ColorF{ 0.1, 0.4, 0.8 }, Arg::bottom = ColorF{ 0.3, 0.7, 1.0 });

		// プレイヤーのテクスチャの描画
		playerTexture.drawAt(playerPos);
	}
}
```


## 2. 落ちてくるアイテム

```cpp hl_lines="3-11 24-37 41-43 70-96 111-115"
# include <Siv3D.hpp>

// アイテムの情報
struct Item
{
	// アイテムの現在位置
	Vec2 pos;

	// アイテムの種類を表す ID
	int32 type;
};

void Main()
{
	// プレイヤーの絵文字テクスチャ
	const Texture playerTexture{ U"😃"_emoji };

	// プレイヤーのスピード（ピクセル / 秒)
	const double playerSpeed = 500.0;

	// プレイヤーの座標
	Vec2 playerPos{ 400, 500 };

	// アイテムのテクスチャ
	const Texture itemTexture{ U"🍰"_emoji };

	// 現在画面上にあるアイテムの配列
	Array<Item> items;

	// アイテムが発生する時間間隔（秒）
	const double SpawnTime = 0.5;

	// 最後にアイテムが発生してからの経過時間（秒）
	double itemTimer = 0.0;

	// アイテムの落下スピード（ピクセル / 秒) 
	const double ItemSpeed = 200.0;

	while (System::Update())
	{
		// アイテムの個数の可視化
		ClearPrint();
		Print << U"ゲーム中のアイテムの個数: " << items.size();

		////////////////////////////////
		//
		//	状態更新
		//
		////////////////////////////////

		// 前のフレームからの経過時間 (秒)
		const double deltaTime = Scene::DeltaTime();

		// プレイヤーの移動に関する処理
		{
			if (KeyLeft.pressed()) // [←] キーが押されていたら
			{
				playerPos.x -= (playerSpeed * deltaTime);
			}
			else if (KeyRight.pressed()) // [→] キーが押されていたら
			{
				playerPos.x += (playerSpeed * deltaTime);
			}

			// 壁の外に出ないようにする
			// Clamp(x, min, max) は, x を min～max の範囲に収めた値を返す
			playerPos.x = Clamp(playerPos.x, 0.0, 800.0);
		}

		// アイテムの出現と移動と消滅に関する処理
		{
			itemTimer += deltaTime;

			// spawnTime が経過するごとに新しいアイテムを出現させる
			while (itemTimer >= SpawnTime)
			{
				Item item;
				item.pos.x = Random(100, 700); // アイテムの X 座標
				item.pos.y = -100; // アイテムの Y 座標
				item.type = 0; // アイテムの種類。 = Random(0, 3); とすれば 0～3 のランダムな数に

				// 配列に追加
				items << item;

				itemTimer -= SpawnTime;
			}

			// すべてのアイテムについて移動処理
			for (auto& item : items)
			{
				item.pos.y += deltaTime * ItemSpeed;
			}

			// 画面外に出たアイテムを消去する
			items.remove_if([](const Item& item) { return (item.pos.y > 700); });
		}

		////////////////////////////////
		//
		//	描画
		//
		////////////////////////////////

		// 背景はグラデーションの Rect
		Scene::Rect()
			.draw(Arg::top = ColorF{ 0.1, 0.4, 0.8 }, Arg::bottom = ColorF{ 0.3, 0.7, 1.0 });

		// プレイヤーのテクスチャの描画
		playerTexture.drawAt(playerPos);

		// アイテムの描画
		for (const auto& item : items)
		{
			itemTexture.drawAt(item.pos);
		}
	}
}
```


## 3. アイテムとの当たり判定

```cpp hl_lines="39-40 97-120 145-151"
# include <Siv3D.hpp>

// アイテムの情報
struct Item
{
	// アイテムの現在位置
	Vec2 pos;

	// アイテムの種類を表す ID
	int32 type;
};

void Main()
{
	// プレイヤーの絵文字テクスチャ
	const Texture playerTexture{ U"😃"_emoji };

	// プレイヤーのスピード（ピクセル / 秒)
	const double playerSpeed = 500.0;

	// プレイヤーの座標
	Vec2 playerPos{ 400, 500 };

	// アイテムのテクスチャ
	const Texture itemTexture{ U"🍰"_emoji };

	// 現在画面上にあるアイテムの配列
	Array<Item> items;

	// アイテムが発生する時間間隔（秒）
	const double SpawnTime = 0.5;

	// 最後にアイテムが発生してからの経過時間（秒）
	double itemTimer = 0.0;

	// アイテムの落下スピード（ピクセル / 秒) 
	const double ItemSpeed = 200.0;

	// スコア
	int32 score = 0;

	while (System::Update())
	{
		// アイテムの個数の可視化
		ClearPrint();
		Print << U"ゲーム中のアイテムの個数: " << items.size();

		////////////////////////////////
		//
		//	状態更新
		//
		////////////////////////////////

		// 前のフレームからの経過時間 (秒)
		const double deltaTime = Scene::DeltaTime();

		// プレイヤーの移動に関する処理
		{
			if (KeyLeft.pressed()) // [←] キーが押されていたら
			{
				playerPos.x -= (playerSpeed * deltaTime);
			}
			else if (KeyRight.pressed()) // [→] キーが押されていたら
			{
				playerPos.x += (playerSpeed * deltaTime);
			}

			// 壁の外に出ないようにする
			// Clamp(x, min, max) は, x を min～max の範囲に収めた値を返す
			playerPos.x = Clamp(playerPos.x, 0.0, 800.0);
		}

		// アイテムの出現と移動と消滅に関する処理
		{
			itemTimer += deltaTime;

			// spawnTime が経過するごとに新しいアイテムを出現させる
			while (itemTimer >= SpawnTime)
			{
				Item item;
				item.pos.x = Random(100, 700); // アイテムの X 座標
				item.pos.y = -100; // アイテムの Y 座標
				item.type = 0; // アイテムの種類。 = Random(0, 3); とすれば 0～3 のランダムな数に

				// 配列に追加
				items << item;

				itemTimer -= SpawnTime;
			}

			// すべてのアイテムについて移動処理
			for (auto& item : items)
			{
				item.pos.y += deltaTime * ItemSpeed;
			}

			// プレイヤーのあたり判定の円
			const Circle playerCirlce{ playerPos, 60 };

			// アイテムのあたり判定と回収したアイテムの削除
			for (auto it = items.begin(); it != items.end();)
			{
				// アイテムのあたり判定の円
				const Circle itemCircle{ it->pos, 60 };

				// 交差したらアイテムを削除
				if (playerCirlce.intersects(itemCircle))
				{
					// (削除する前に) スコアを加算
					score += 100;

					// アイテムを削除
					it = items.erase(it);
				}
				else
				{
					// イテレータを次のアイテムに進める
					++it;
				}
			}

			// 画面外に出たアイテムを消去する
			items.remove_if([](const Item& item) { return (item.pos.y > 700); });
		}

		////////////////////////////////
		//
		//	描画
		//
		////////////////////////////////

		// 背景はグラデーションの Rect
		Scene::Rect()
			.draw(Arg::top = ColorF{ 0.1, 0.4, 0.8 }, Arg::bottom = ColorF{ 0.3, 0.7, 1.0 });

		// プレイヤーのテクスチャの描画
		playerTexture.drawAt(playerPos);

		// アイテムの描画
		for (const auto& item : items)
		{
			itemTexture.drawAt(item.pos);
		}

		// 当たり判定の可視化（デバッグ用）
		Circle{ playerPos, 60 }.drawFrame(2, Palette::Red); // プレイヤーの当たり判定円
		for (const auto& item : items)
		{
			// アイテムの当たり判定円
			Circle{ item.pos, 60 }.drawFrame(2, Palette::Red);
		}
	}
}
```

