# 25. アイテム集めゲーム
チュートリアル 3 ～ 24 の内容を使って、落下してくるアイテムを集めるゲームを作ります。

## 25.1 背景の描画
- 2 つの長方形を組み合わせて空と地面を描画します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/1.png)

```cpp
# include <Siv3D.hpp>

// 背景画面を描く関数
void DrawBackground()
{
	// 空を描く
	Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

	// 地面を描く
	Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
}

void Main()
{
	while (System::Update())
	{
		// 背景を描く
		DrawBackground();
	}
}
```


## 25.2 プレイヤーの実装
- プレイヤーの情報を管理する `Player` クラスを作ります
- プレイヤーの領域をメンバ変数 `Circle circle` で、プレイヤーの絵文字をメンバ変数 `Texture texture` で扱います
- メンバ関数 `.draw()` でプレイヤーを描画します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/2.png)

```cpp
# include <Siv3D.hpp>

// プレイヤークラス
struct Player
{
	Circle circle{ 400, 530, 30 };

	Texture texture{ U"😃"_emoji };

	// プレイヤーを描く関数
	void draw() const
	{
		texture.scaled(0.5).drawAt(circle.center);
	}
};

// 背景画面を描く関数
void DrawBackground()
{
	// 空を描く
	Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

	// 地面を描く
	Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
}

void Main()
{
	Player player;

	while (System::Update())
	{
		// 背景を描く
		DrawBackground();

		// プレイヤーを描く
		player.draw();
	}
}
```


## 25.3 プレイヤーの移動
- `Player` クラスにメンバ関数 `.update()` を追加し、プレイヤーの移動を実装します
- プレイヤーが画面外に出ないよう、移動できる X 座標の範囲を `Clamp` 関数を用いて制限します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/3.png)

```cpp
# include <Siv3D.hpp>

// プレイヤークラス
struct Player
{
	Circle circle{ 400, 530, 30 };

	Texture texture{ U"😃"_emoji };

	// プレイヤーの状態を更新する関数
	void update(double deltaTime)
	{
		const double speed = (deltaTime * 400.0);

		// [←] キーが押されたら左に移動
		if (KeyLeft.pressed())
		{
			circle.x -= speed;
		}

		// [→] キーが押されたら右に移動
		if (KeyRight.pressed())
		{
			circle.x += speed;
		}

		// プレイヤーが画面外に出ないようにする
		circle.x = Clamp(circle.x, 30.0, 770.0);
	}

	// プレイヤーを描く関数
	void draw() const
	{
		texture.scaled(0.5).drawAt(circle.center);
	}
};

// 背景画面を描く関数
void DrawBackground()
{
	// 空を描く
	Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

	// 地面を描く
	Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
}

void Main()
{
	Player player;

	while (System::Update())
	{
		/////////////////////////////////
		//
		//	更新
		//
		/////////////////////////////////

		const double deltaTime = Scene::DeltaTime();

		// プレイヤーの状態を更新する
		player.update(deltaTime);

		/////////////////////////////////
		//
		//	描画
		//
		/////////////////////////////////

		// 背景を描く
		DrawBackground();

		// プレイヤーを描く
		player.draw();
	}
}
```


## 25.4 アイテムクラスの実装
- 空から落ちてくるアイテムを表す `Item` クラスを作成します
- アイテムの領域をメンバ変数 `Circle circle` で、アイテムの種類をメンバ変数 `int32 type` で表します
- `type` が `0` のときはキャンディー、`1` のときはケーキを表すことにします
- メンバ関数 `.draw()` でアイテムを描画しますが、仮実装として `type` が `0` のときは赤い円、`1` のときは白い円を描画します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/4.png)

```cpp
# include <Siv3D.hpp>

// プレイヤークラス
struct Player
{
	Circle circle{ 400, 530, 30 };

	Texture texture{ U"😃"_emoji };

	// プレイヤーの状態を更新する関数
	void update(double deltaTime)
	{
		const double speed = (deltaTime * 400.0);

		// [←] キーが押されたら左に移動
		if (KeyLeft.pressed())
		{
			circle.x -= speed;
		}

		// [→] キーが押されたら右に移動
		if (KeyRight.pressed())
		{
			circle.x += speed;
		}

		// プレイヤーが画面外に出ないようにする
		circle.x = Clamp(circle.x, 30.0, 770.0);
	}

	// プレイヤーを描く関数
	void draw() const
	{
		texture.scaled(0.5).drawAt(circle.center);
	}
};

// アイテムクラス
struct Item
{
	Circle circle;

	// アイテムの種類（0: キャンディー, 1: ケーキ）
	int32 type;

	// アイテムを描く関数（仮実装）
	void draw() const
	{
		if (type == 0)
		{
			// キャンディーを描く
			circle.draw(Palette::Red);
		}
		else if (type == 1)
		{
			// ケーキを描く
			circle.draw(Palette::White);
		}
	}
};

// 背景画面を描く関数
void DrawBackground()
{
	// 空を描く
	Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

	// 地面を描く
	Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
}

// アイテムを描く関数
void DrawItems(const Array<Item>& items)
{
	for (const auto& item : items)
	{
		item.draw();
	}
}

void Main()
{
	Player player;

	// アイテムの配列
	Array<Item> items;
	items << Item{ Circle{ 200, 200, 30 }, 0 };
	items << Item{ Circle{ 600, 100, 30 }, 1 };

	while (System::Update())
	{
		/////////////////////////////////
		//
		//	更新
		//
		/////////////////////////////////

		const double deltaTime = Scene::DeltaTime();

		// プレイヤーの状態を更新する
		player.update(deltaTime);

		/////////////////////////////////
		//
		//	描画
		//
		/////////////////////////////////

		// 背景を描く
		DrawBackground();

		// プレイヤーを描く
		player.draw();

		// すべてのアイテムを描く
		DrawItems(items);
	}
}
```


## 25.5 テクスチャの効率的な管理（配列による共有）
- `Item` クラスには `Texture` メンバ変数を作りません
- たくさん登場するアイテム 1 つひとつに、毎回新しい `Texture` を用意するのは効率が悪いためです
- 必要最小限のテクスチャを事前に配列で用意し、それを参照して描画するようにします
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/5.png)

```cpp
# include <Siv3D.hpp>

// プレイヤークラス
struct Player
{
	Circle circle{ 400, 530, 30 };

	Texture texture{ U"😃"_emoji };

	// プレイヤーの状態を更新する関数
	void update(double deltaTime)
	{
		const double speed = (deltaTime * 400.0);

		// [←] キーが押されたら左に移動
		if (KeyLeft.pressed())
		{
			circle.x -= speed;
		}

		// [→] キーが押されたら右に移動
		if (KeyRight.pressed())
		{
			circle.x += speed;
		}

		// プレイヤーが画面外に出ないようにする
		circle.x = Clamp(circle.x, 30.0, 770.0);
	}

	// プレイヤーを描く関数
	void draw() const
	{
		texture.scaled(0.5).drawAt(circle.center);
	}
};

// アイテムクラス
struct Item
{
	Circle circle;

	// アイテムの種類（0: キャンディー, 1: ケーキ）
	int32 type;

	// アイテムを描く関数
	void draw(const Array<Texture>& itemTextures) const
	{
		// アイテムの種類に応じたテクスチャを描く
		itemTextures[type].scaled(0.5).drawAt(circle.center);
	}
};

// 背景画面を描く関数
void DrawBackground()
{
	// 空を描く
	Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

	// 地面を描く
	Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
}

// アイテムを描く関数
void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
{
	for (const auto& item : items)
	{
		item.draw(itemTextures);
	}
}

void Main()
{
	// アイテムのテクスチャ配列
	const Array<Texture> itemTextures =
	{
		Texture{ U"🍬"_emoji },
		Texture{ U"🍰"_emoji },
	};

	Player player;

	// アイテムの配列
	Array<Item> items;
	items << Item{ Circle{ 200, 200, 30 }, 0 };
	items << Item{ Circle{ 600, 100, 30 }, 1 };

	while (System::Update())
	{
		/////////////////////////////////
		//
		//	更新
		//
		/////////////////////////////////

		const double deltaTime = Scene::DeltaTime();

		// プレイヤーの状態を更新する
		player.update(deltaTime);

		/////////////////////////////////
		//
		//	描画
		//
		/////////////////////////////////

		// 背景を描く
		DrawBackground();

		// プレイヤーを描く
		player.draw();

		// すべてのアイテムを描く
		DrawItems(items, itemTextures);
	}
}
```


## 25.6 アイテムの落下と削除
- `Item` クラスにメンバ関数 `.update()` を追加し、アイテムを落下させます
- 地面に落下したアイテムは `.remove_if()` で削除します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/6.png)

```cpp
# include <Siv3D.hpp>

// プレイヤークラス
struct Player
{
	Circle circle{ 400, 530, 30 };

	Texture texture{ U"😃"_emoji };

	// プレイヤーの状態を更新する関数
	void update(double deltaTime)
	{
		const double speed = (deltaTime * 400.0);

		// [←] キーが押されたら左に移動
		if (KeyLeft.pressed())
		{
			circle.x -= speed;
		}

		// [→] キーが押されたら右に移動
		if (KeyRight.pressed())
		{
			circle.x += speed;
		}

		// プレイヤーが画面外に出ないようにする
		circle.x = Clamp(circle.x, 30.0, 770.0);
	}

	// プレイヤーを描く関数
	void draw() const
	{
		texture.scaled(0.5).drawAt(circle.center);
	}
};

// アイテムクラス
struct Item
{
	Circle circle;

	// アイテムの種類（0: キャンディー, 1: ケーキ）
	int32 type;

	void update(double deltaTime)
	{
		// アイテムを下に移動させる
		circle.y += (deltaTime * 200.0);
	}

	// アイテムを描く関数
	void draw(const Array<Texture>& itemTextures) const
	{
		// アイテムの種類に応じたテクスチャを描く
		itemTextures[type].scaled(0.5).drawAt(circle.center);
	}
};

void UpdateItems(Array<Item>& items, double deltaTime)
{
	// すべてのアイテムの状態を更新する
	for (auto& item : items)
	{
		item.update(deltaTime);
	}

	// 地面に落下したアイテムを削除する
	items.remove_if([](const Item& item) { return (580 < item.circle.y); });
}

// 背景画面を描く関数
void DrawBackground()
{
	// 空を描く
	Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

	// 地面を描く
	Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
}

// アイテムを描く関数
void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
{
	for (const auto& item : items)
	{
		item.draw(itemTextures);
	}
}

void Main()
{
	// アイテムのテクスチャ配列
	const Array<Texture> itemTextures =
	{
		Texture{ U"🍬"_emoji },
		Texture{ U"🍰"_emoji },
	};

	Player player;

	// アイテムの配列
	Array<Item> items;
	items << Item{ Circle{ 200, 200, 30 }, 0 };
	items << Item{ Circle{ 600, 100, 30 }, 1 };

	while (System::Update())
	{
		/////////////////////////////////
		//
		//	更新
		//
		/////////////////////////////////

		const double deltaTime = Scene::DeltaTime();

		// プレイヤーの状態を更新する
		player.update(deltaTime);

		// すべてのアイテムの状態を更新する
		UpdateItems(items, deltaTime);

		/////////////////////////////////
		//
		//	描画
		//
		/////////////////////////////////

		// 背景を描く
		DrawBackground();

		// プレイヤーを描く
		player.draw();

		// すべてのアイテムを描く
		DrawItems(items, itemTextures);
	}
}
```


## 25.7 アイテムの定期的な生成
- 0.8 秒ごとに、空中のランダムな位置にアイテムを出現させます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/7.png)

```cpp
# include <Siv3D.hpp>

// プレイヤークラス
struct Player
{
	Circle circle{ 400, 530, 30 };

	Texture texture{ U"😃"_emoji };

	// プレイヤーの状態を更新する関数
	void update(double deltaTime)
	{
		const double speed = (deltaTime * 400.0);

		// [←] キーが押されたら左に移動
		if (KeyLeft.pressed())
		{
			circle.x -= speed;
		}

		// [→] キーが押されたら右に移動
		if (KeyRight.pressed())
		{
			circle.x += speed;
		}

		// プレイヤーが画面外に出ないようにする
		circle.x = Clamp(circle.x, 30.0, 770.0);
	}

	// プレイヤーを描く関数
	void draw() const
	{
		texture.scaled(0.5).drawAt(circle.center);
	}
};

// アイテムクラス
struct Item
{
	Circle circle;

	// アイテムの種類（0: キャンディー, 1: ケーキ）
	int32 type;

	void update(double deltaTime)
	{
		// アイテムを下に移動させる
		circle.y += (deltaTime * 200.0);
	}

	// アイテムを描く関数
	void draw(const Array<Texture>& itemTextures) const
	{
		// アイテムの種類に応じたテクスチャを描く
		itemTextures[type].scaled(0.5).drawAt(circle.center);
	}
};

void UpdateItems(Array<Item>& items, double deltaTime)
{
	// すべてのアイテムの状態を更新する
	for (auto& item : items)
	{
		item.update(deltaTime);
	}

	// 地面に落下したアイテムを削除する
	items.remove_if([](const Item& item) { return (580 < item.circle.y); });
}

// 背景画面を描く関数
void DrawBackground()
{
	// 空を描く
	Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

	// 地面を描く
	Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
}

// アイテムを描く関数
void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
{
	for (const auto& item : items)
	{
		item.draw(itemTextures);
	}
}

void Main()
{
	// アイテムのテクスチャ配列
	const Array<Texture> itemTextures =
	{
		Texture{ U"🍬"_emoji },
		Texture{ U"🍰"_emoji },
	};

	Player player;

	// アイテムの配列
	Array<Item> items;
	items << Item{ Circle{ 200, 200, 30 }, 0 };
	items << Item{ Circle{ 600, 100, 30 }, 1 };

	// アイテムが出現する周期（秒）
	const double spawnInterval = 0.8;

	// 蓄積時間（秒）
	double accumulatedTime = 0.0;

	while (System::Update())
	{
		/////////////////////////////////
		//
		//	更新
		//
		/////////////////////////////////

		const double deltaTime = Scene::DeltaTime();

		// 蓄積時間を増やす
		accumulatedTime += deltaTime;

		// 蓄積時間が周期を超えたら
		if (spawnInterval < accumulatedTime)
		{
			// 新しいアイテムを追加する
			items << Item{ Circle{ Random(30.0, 770.0), -30, 30 }, Random(0, 1) };

			// 蓄積時間を周期分減らす
			accumulatedTime -= spawnInterval;
		}

		// プレイヤーの状態を更新する
		player.update(deltaTime);

		// すべてのアイテムの状態を更新する
		UpdateItems(items, deltaTime);

		/////////////////////////////////
		//
		//	描画
		//
		/////////////////////////////////

		// 背景を描く
		DrawBackground();

		// プレイヤーを描く
		player.draw();

		// すべてのアイテムを描く
		DrawItems(items, itemTextures);
	}
}
```


## 25.8 スコアシステムの導入
- アイテムをゲットしたときに増やすスコアを導入します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/8.png)

```cpp
# include <Siv3D.hpp>

// プレイヤークラス
struct Player
{
	Circle circle{ 400, 530, 30 };

	Texture texture{ U"😃"_emoji };

	// プレイヤーの状態を更新する関数
	void update(double deltaTime)
	{
		const double speed = (deltaTime * 400.0);

		// [←] キーが押されたら左に移動
		if (KeyLeft.pressed())
		{
			circle.x -= speed;
		}

		// [→] キーが押されたら右に移動
		if (KeyRight.pressed())
		{
			circle.x += speed;
		}

		// プレイヤーが画面外に出ないようにする
		circle.x = Clamp(circle.x, 30.0, 770.0);
	}

	// プレイヤーを描く関数
	void draw() const
	{
		texture.scaled(0.5).drawAt(circle.center);
	}
};

// アイテムクラス
struct Item
{
	Circle circle;

	// アイテムの種類（0: キャンディー, 1: ケーキ）
	int32 type;

	void update(double deltaTime)
	{
		// アイテムを下に移動させる
		circle.y += (deltaTime * 200.0);
	}

	// アイテムを描く関数
	void draw(const Array<Texture>& itemTextures) const
	{
		// アイテムの種類に応じたテクスチャを描く
		itemTextures[type].scaled(0.5).drawAt(circle.center);
	}
};

void UpdateItems(Array<Item>& items, double deltaTime)
{
	// すべてのアイテムの状態を更新する
	for (auto& item : items)
	{
		item.update(deltaTime);
	}

	// 地面に落下したアイテムを削除する
	items.remove_if([](const Item& item) { return (580 < item.circle.y); });
}

// 背景画面を描く関数
void DrawBackground()
{
	// 空を描く
	Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

	// 地面を描く
	Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
}

// アイテムを描く関数
void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
{
	for (const auto& item : items)
	{
		item.draw(itemTextures);
	}
}

// UI を描く関数
void DrawUI(int32 score, const Font& font)
{
	// スコアを描く
	font(U"SCORE: {}"_fmt(score)).draw(30, Vec2{ 20, 20 });
}

void Main()
{
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// アイテムのテクスチャ配列
	const Array<Texture> itemTextures =
	{
		Texture{ U"🍬"_emoji },
		Texture{ U"🍰"_emoji },
	};

	Player player;

	// アイテムの配列
	Array<Item> items;
	items << Item{ Circle{ 200, 200, 30 }, 0 };
	items << Item{ Circle{ 600, 100, 30 }, 1 };

	// アイテムが出現する周期（秒）
	const double spawnInterval = 0.8;

	// 蓄積時間（秒）
	double accumulatedTime = 0.0;

	// スコア
	int32 score = 0;

	while (System::Update())
	{
		/////////////////////////////////
		//
		//	更新
		//
		/////////////////////////////////

		const double deltaTime = Scene::DeltaTime();

		// 蓄積時間を増やす
		accumulatedTime += deltaTime;

		// 蓄積時間が周期を超えたら
		if (spawnInterval < accumulatedTime)
		{
			// 新しいアイテムを追加する
			items << Item{ Circle{ Random(30.0, 770.0), -30, 30 }, Random(0, 1) };

			// 蓄積時間を周期分減らす
			accumulatedTime -= spawnInterval;
		}

		// プレイヤーの状態を更新する
		player.update(deltaTime);

		// すべてのアイテムの状態を更新する
		UpdateItems(items, deltaTime);

		/////////////////////////////////
		//
		//	描画
		//
		/////////////////////////////////

		// 背景を描く
		DrawBackground();

		// プレイヤーを描く
		player.draw();

		// すべてのアイテムを描く
		DrawItems(items, itemTextures);

		// UI を描く
		DrawUI(score, font);
	}
}
```


## 25.9 アイテムとプレイヤーのあたり判定の実装
- 各アイテムとプレイヤーの円が重なっているかどうかを判定します
- 重なっている場合はアイテムを削除し、スコアを増やします
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/9.png)

```cpp
# include <Siv3D.hpp>

// プレイヤークラス
struct Player
{
	Circle circle{ 400, 530, 30 };

	Texture texture{ U"😃"_emoji };

	// プレイヤーの状態を更新する関数
	void update(double deltaTime)
	{
		const double speed = (deltaTime * 400.0);

		// [←] キーが押されたら左に移動
		if (KeyLeft.pressed())
		{
			circle.x -= speed;
		}

		// [→] キーが押されたら右に移動
		if (KeyRight.pressed())
		{
			circle.x += speed;
		}

		// プレイヤーが画面外に出ないようにする
		circle.x = Clamp(circle.x, 30.0, 770.0);
	}

	// プレイヤーを描く関数
	void draw() const
	{
		texture.scaled(0.5).drawAt(circle.center);
	}
};

// アイテムクラス
struct Item
{
	Circle circle;

	// アイテムの種類（0: キャンディー, 1: ケーキ）
	int32 type;

	void update(double deltaTime)
	{
		// アイテムを下に移動させる
		circle.y += (deltaTime * 200.0);
	}

	// アイテムを描く関数
	void draw(const Array<Texture>& itemTextures) const
	{
		// アイテムの種類に応じたテクスチャを描く
		itemTextures[type].scaled(0.5).drawAt(circle.center);
	}
};

void UpdateItems(Array<Item>& items, double deltaTime, const Player& player, int32& score)
{
	// すべてのアイテムの状態を更新する
	for (auto& item : items)
	{
		item.update(deltaTime);
	}

	// 各アイテムについて
	for (auto it = items.begin(); it != items.end();)
	{
		// プレイヤーとアイテムが交差したら
		if (player.circle.intersects(it->circle))
		{
			// スコアを加算する（キャンディー: 10点, ケーキ: 50点）
			score += ((it->type == 0) ? 10 : 50);

			// アイテムを削除する
			it = items.erase(it);
		}
		else
		{
			++it;
		}
	}

	// 地面に落下したアイテムを削除する
	items.remove_if([](const Item& item) { return (580 < item.circle.y); });
}

// 背景画面を描く関数
void DrawBackground()
{
	// 空を描く
	Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

	// 地面を描く
	Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
}

// アイテムを描く関数
void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
{
	for (const auto& item : items)
	{
		item.draw(itemTextures);
	}
}

// UI を描く関数
void DrawUI(int32 score, const Font& font)
{
	// スコアを描く
	font(U"SCORE: {}"_fmt(score)).draw(30, Vec2{ 20, 20 });
}

void Main()
{
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// アイテムのテクスチャ配列
	const Array<Texture> itemTextures =
	{
		Texture{ U"🍬"_emoji },
		Texture{ U"🍰"_emoji },
	};

	Player player;

	// アイテムの配列
	Array<Item> items;
	items << Item{ Circle{ 200, 200, 30 }, 0 };
	items << Item{ Circle{ 600, 100, 30 }, 1 };

	// アイテムが出現する周期（秒）
	const double spawnInterval = 0.8;

	// 蓄積時間（秒）
	double accumulatedTime = 0.0;

	// スコア
	int32 score = 0;

	while (System::Update())
	{
		/////////////////////////////////
		//
		//	更新
		//
		/////////////////////////////////

		const double deltaTime = Scene::DeltaTime();

		// 蓄積時間を増やす
		accumulatedTime += deltaTime;

		// 蓄積時間が周期を超えたら
		if (spawnInterval < accumulatedTime)
		{
			// 新しいアイテムを追加する
			items << Item{ Circle{ Random(30.0, 770.0), -30, 30 }, Random(0, 1) };

			// 蓄積時間を周期分減らす
			accumulatedTime -= spawnInterval;
		}

		// プレイヤーの状態を更新する
		player.update(deltaTime);

		// すべてのアイテムの状態を更新する
		UpdateItems(items, deltaTime, player, score);

		/////////////////////////////////
		//
		//	描画
		//
		/////////////////////////////////

		// 背景を描く
		DrawBackground();

		// プレイヤーを描く
		player.draw();

		// すべてのアイテムを描く
		DrawItems(items, itemTextures);

		// UI を描く
		DrawUI(score, font);
	}
}
```


## 25.10 残り時間の実装
- 残り時間を表す変数 `double remainingTime` を導入します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/10.png)

```cpp
# include <Siv3D.hpp>

// プレイヤークラス
struct Player
{
	Circle circle{ 400, 530, 30 };

	Texture texture{ U"😃"_emoji };

	// プレイヤーの状態を更新する関数
	void update(double deltaTime)
	{
		const double speed = (deltaTime * 400.0);

		// [←] キーが押されたら左に移動
		if (KeyLeft.pressed())
		{
			circle.x -= speed;
		}

		// [→] キーが押されたら右に移動
		if (KeyRight.pressed())
		{
			circle.x += speed;
		}

		// プレイヤーが画面外に出ないようにする
		circle.x = Clamp(circle.x, 30.0, 770.0);
	}

	// プレイヤーを描く関数
	void draw() const
	{
		texture.scaled(0.5).drawAt(circle.center);
	}
};

// アイテムクラス
struct Item
{
	Circle circle;

	// アイテムの種類（0: キャンディー, 1: ケーキ）
	int32 type;

	void update(double deltaTime)
	{
		// アイテムを下に移動させる
		circle.y += (deltaTime * 200.0);
	}

	// アイテムを描く関数
	void draw(const Array<Texture>& itemTextures) const
	{
		// アイテムの種類に応じたテクスチャを描く
		itemTextures[type].scaled(0.5).drawAt(circle.center);
	}
};

void UpdateItems(Array<Item>& items, double deltaTime, const Player& player, int32& score)
{
	// すべてのアイテムの状態を更新する
	for (auto& item : items)
	{
		item.update(deltaTime);
	}

	// 各アイテムについて
	for (auto it = items.begin(); it != items.end();)
	{
		// プレイヤーとアイテムが交差したら
		if (player.circle.intersects(it->circle))
		{
			// スコアを加算する（キャンディー: 10点, ケーキ: 50点）
			score += ((it->type == 0) ? 10 : 50);

			// アイテムを削除する
			it = items.erase(it);
		}
		else
		{
			++it;
		}
	}

	// 地面に落下したアイテムを削除する
	items.remove_if([](const Item& item) { return (580 < item.circle.y); });
}

// 背景画面を描く関数
void DrawBackground()
{
	// 空を描く
	Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

	// 地面を描く
	Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
}

// アイテムを描く関数
void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
{
	for (const auto& item : items)
	{
		item.draw(itemTextures);
	}
}

// UI を描く関数
void DrawUI(int32 score, double remainingTime, const Font& font)
{
	// スコアを描く
	font(U"SCORE: {}"_fmt(score)).draw(30, Vec2{ 20, 20 });

	// 残り時間を描く
	font(U"TIME: {:.0f}"_fmt(remainingTime)).draw(30, Arg::topRight(780, 20));

	if (remainingTime <= 0.0)
	{
		font(U"TIME'S UP!").drawAt(80, Vec2{ 400, 270 }, ColorF{ 0.3 });
	}
}

void Main()
{
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// アイテムのテクスチャ配列
	const Array<Texture> itemTextures =
	{
		Texture{ U"🍬"_emoji },
		Texture{ U"🍰"_emoji },
	};

	Player player;

	// アイテムの配列
	Array<Item> items;
	items << Item{ Circle{ 200, 200, 30 }, 0 };
	items << Item{ Circle{ 600, 100, 30 }, 1 };

	// アイテムが出現する周期（秒）
	const double spawnInterval = 0.8;

	// 蓄積時間（秒）
	double accumulatedTime = 0.0;

	// スコア
	int32 score = 0;

	// 残り時間（秒）
	double remainingTime = 20.0;

	while (System::Update())
	{
		/////////////////////////////////
		//
		//	更新
		//
		/////////////////////////////////

		const double deltaTime = Scene::DeltaTime();

		// 残り時間を減らす
		remainingTime = Max((remainingTime - deltaTime), 0.0);

		// 蓄積時間を増やす
		accumulatedTime += deltaTime;

		// 蓄積時間が周期を超えたら
		if (spawnInterval < accumulatedTime)
		{
			// 新しいアイテムを追加する
			items << Item{ Circle{ Random(30.0, 770.0), -30, 30 }, Random(0, 1) };

			// 蓄積時間を周期分減らす
			accumulatedTime -= spawnInterval;
		}

		// プレイヤーの状態を更新する
		player.update(deltaTime);

		// すべてのアイテムの状態を更新する
		UpdateItems(items, deltaTime, player, score);

		/////////////////////////////////
		//
		//	描画
		//
		/////////////////////////////////

		// 背景を描く
		DrawBackground();

		// プレイヤーを描く
		player.draw();

		// すべてのアイテムを描く
		DrawItems(items, itemTextures);

		// UI を描く
		DrawUI(score, remainingTime, font);
	}
}
```

## 25.11 【完成】ゲーム終了処理
- 残り時間が 0 になったら、アイテムをすべて消去し、プレイヤーの操作を受け付けないようにします
- これで、ゲームの完成です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/collect/11.png)

```cpp
# include <Siv3D.hpp>

// プレイヤークラス
struct Player
{
	Circle circle{ 400, 530, 30 };

	Texture texture{ U"😃"_emoji };

	// プレイヤーの状態を更新する関数
	void update(double deltaTime)
	{
		const double speed = (deltaTime * 400.0);

		// [←] キーが押されたら左に移動
		if (KeyLeft.pressed())
		{
			circle.x -= speed;
		}

		// [→] キーが押されたら右に移動
		if (KeyRight.pressed())
		{
			circle.x += speed;
		}

		// プレイヤーが画面外に出ないようにする
		circle.x = Clamp(circle.x, 30.0, 770.0);
	}

	// プレイヤーを描く関数
	void draw() const
	{
		texture.scaled(0.5).drawAt(circle.center);
	}
};

// アイテムクラス
struct Item
{
	Circle circle;

	// アイテムの種類（0: キャンディー, 1: ケーキ）
	int32 type;

	void update(double deltaTime)
	{
		// アイテムを下に移動させる
		circle.y += (deltaTime * 200.0);
	}

	// アイテムを描く関数
	void draw(const Array<Texture>& itemTextures) const
	{
		// アイテムの種類に応じたテクスチャを描く
		itemTextures[type].scaled(0.5).drawAt(circle.center);
	}
};

void UpdateItems(Array<Item>& items, double deltaTime, const Player& player, int32& score)
{
	// すべてのアイテムの状態を更新する
	for (auto& item : items)
	{
		item.update(deltaTime);
	}

	// 各アイテムについて
	for (auto it = items.begin(); it != items.end();)
	{
		// プレイヤーとアイテムが交差したら
		if (player.circle.intersects(it->circle))
		{
			// スコアを加算する（キャンディー: 10点, ケーキ: 50点）
			score += ((it->type == 0) ? 10 : 50);

			// アイテムを削除する
			it = items.erase(it);
		}
		else
		{
			++it;
		}
	}

	// 地面に落下したアイテムを削除する
	items.remove_if([](const Item& item) { return (580 < item.circle.y); });
}

// 背景画面を描く関数
void DrawBackground()
{
	// 空を描く
	Rect{ 0, 0, 800, 550 }.draw(Arg::top(0.3, 0.6, 1.0), Arg::bottom(0.6, 0.9, 1.0));

	// 地面を描く
	Rect{ 0, 550, 800, 50 }.draw(ColorF{ 0.3, 0.6, 0.3 });
}

// アイテムを描く関数
void DrawItems(const Array<Item>& items, const Array<Texture>& itemTextures)
{
	for (const auto& item : items)
	{
		item.draw(itemTextures);
	}
}

// UI を描く関数
void DrawUI(int32 score, double remainingTime, const Font& font)
{
	// スコアを描く
	font(U"SCORE: {}"_fmt(score)).draw(30, Vec2{ 20, 20 });

	// 残り時間を描く
	font(U"TIME: {:.0f}"_fmt(remainingTime)).draw(30, Arg::topRight(780, 20));

	if (remainingTime <= 0.0)
	{
		font(U"TIME'S UP!").drawAt(80, Vec2{ 400, 270 }, ColorF{ 0.3 });
	}
}

void Main()
{
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// アイテムのテクスチャ配列
	const Array<Texture> itemTextures =
	{
		Texture{ U"🍬"_emoji },
		Texture{ U"🍰"_emoji },
	};

	Player player;

	// アイテムの配列
	Array<Item> items;
	items << Item{ Circle{ 200, 200, 30 }, 0 };
	items << Item{ Circle{ 600, 100, 30 }, 1 };

	// アイテムが出現する周期（秒）
	const double spawnInterval = 0.8;

	// 蓄積時間（秒）
	double accumulatedTime = 0.0;

	// スコア
	int32 score = 0;

	// 残り時間（秒）
	double remainingTime = 20.0;

	while (System::Update())
	{
		/////////////////////////////////
		//
		//	更新
		//
		/////////////////////////////////

		const double deltaTime = Scene::DeltaTime();

		// 残り時間を減らす
		remainingTime = Max((remainingTime - deltaTime), 0.0);

		// ゲームが進行している場合
		if (0.0 < remainingTime)
		{
			// 蓄積時間を増やす
			accumulatedTime += deltaTime;

			// 蓄積時間が周期を超えたら
			if (spawnInterval < accumulatedTime)
			{
				// 新しいアイテムを追加する
				items << Item{ Circle{ Random(30.0, 770.0), -30, 30 }, Random(0, 1) };

				// 蓄積時間を周期分減らす
				accumulatedTime -= spawnInterval;
			}

			// プレイヤーの状態を更新する
			player.update(deltaTime);

			// すべてのアイテムの状態を更新する
			UpdateItems(items, deltaTime, player, score);
		}
		else
		{
			items.clear();
		}

		/////////////////////////////////
		//
		//	描画
		//
		/////////////////////////////////

		// 背景を描く
		DrawBackground();

		// プレイヤーを描く
		player.draw();

		// すべてのアイテムを描く
		DrawItems(items, itemTextures);

		// UI を描く
		DrawUI(score, remainingTime, font);
	}
}
```