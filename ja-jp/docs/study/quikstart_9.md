# 勉強会 9 コマ目（ゲーム開発）

### 1. カウンター
絵文字をクリックするとカウントが増えるアプリです。

- カウントをリセットする操作を追加してみましょう。
- 絵文字の上でマウスカーソルが手の形になるようにしてみましょう（6 コマ目 2.5 を参照）。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/13-8.1.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"🛎️"_emoji };

	const Font font{ FontMethod::MSDF, 48 };

    // 押した回数
	int32 count = 0;

	while (System::Update())
	{
		if (Circle{ 500, 300, 70 }.leftClicked())
		{
			++count;
		}

		font(U"{} 回"_fmt(count)).draw(50, Vec2{ 200, 300 }, Palette::Black);

		Circle{ 500, 300, 70 }.draw(ColorF{ 1.0, 1.0, 1.0, 0.5 });

		emoji.drawAt(500, 300);
	}
}
```


### 2. クッキークリック
クッキーをたくさんクリックするゲームです。

- クリックしてはいけないアイテムを追加で登場させてみましょう。
- 余裕のある方は、[16. クッキークリッカー風のゲームを作る](../tutorial/cookie-clicker.md){:target="_blank"} に挑戦してみましょう。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/13-8.2.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"🍪"_emoji };

	const Font font{ FontMethod::MSDF, 48 };

	int32 score = 0;

    // クッキーの座標
	Vec2 cookiePos{ 400, 300 };

	while (System::Update())
	{
		if (Circle{ cookiePos, 70 }.leftClicked())
		{
			score += 100;
			cookiePos = RandomVec2(Rect{ 50, 50, 700, 500 });
		}

		emoji.drawAt(cookiePos);

		font(U"スコア: {}"_fmt(score)).draw(50, Vec2{ 50, 50 }, Palette::Black);
	}
}
```

### 3. シューティングゲーム
- 次々と現れる敵を撃ち落とすシューティングゲームです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/study/9/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 自機
	Circle player{ 600, 520, 30 };

	// 自機の弾
	Array<Circle> bullets;

	// 敵
	Array<Circle> enemies;

	// これが 0.5 秒蓄積されたら弾を撃つ
	double accumulatedTime = 0.0;

	// これが 1.0 秒蓄積されたら敵を追加する
	double enemyAccumulatedTime = 0.0;

	while (System::Update())
	{
		//////////////////////////
		//
		// 更新
		//
		//////////////////////////

		// 前のフレームからの経過時間（秒）
		const double deltaTime = Scene::DeltaTime();
		accumulatedTime += deltaTime;
		enemyAccumulatedTime += deltaTime;

		if (KeyLeft.pressed()) // [←] キーが押されたら
		{
			// 自機を左に移動させる
			player.x -= (300 * deltaTime);
		}
		else if (KeyRight.pressed()) // [→] キーが押されたら
		{
			// 自機を右に移動させる
			player.x += (300 * deltaTime);
		}

		// 0.5 秒蓄積されたら弾を撃つ
		if (0.5 <= accumulatedTime)
		{
			// 弾を追加する
			bullets << Circle{ player.x, (player.y - 20), 8 };

			// 蓄積時間を減らす
			accumulatedTime -= 0.5;
		}

		// 1.0 秒蓄積されたら敵を追加する
		if (1.0 <= enemyAccumulatedTime)
		{
			// 敵を追加する
			enemies << Circle{ Random(60, 740), -30, 30 };

			// 蓄積時間を減らす
			enemyAccumulatedTime -= 1.0;
		}

		// 弾の移動
		for (auto& bullet : bullets) // すべての弾について
		{
			// 上に移動させる
			bullet.y -= (400 * deltaTime);
		}

		// 敵の移動
		for (auto& enemy : enemies) // すべての敵について
		{
			// 下に移動させる
			enemy.y += (120 * deltaTime);
		}

		// 弾と敵の衝突判定
		for (auto& enemy : enemies) // すべての敵について
		{
			// この敵が攻撃されたかどうか
			bool hit = false;

			for (auto& bullet : bullets) // すべての弾について
			{
				if (bullet.intersects(enemy)) // 弾と敵が衝突したら
				{
					bullet.y = -9999; // 弾を画面外に移動させる
					enemy.y = 9999; // 敵を画面外に移動させる
					hit = true;
				}

				// 攻撃された敵は残りの弾との判定をスキップする
				if (hit)
				{
					break;
				}
			}
		}

		// 画面外に出た弾を削除する
		bullets.remove_if([](const Circle& bullet) { return (bullet.y < -100); });

		// 画面外に出た敵を削除する
		enemies.remove_if([](const Circle& enemy) { return (1000 < enemy.y); });

		//////////////////////////
		//
		// 描画
		//
		//////////////////////////

		// 背景を描く
		Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.3, 0.7, 0.8 }, Arg::bottom = ColorF{ 0.5, 0.8, 0.9 });

		// 自機を描く
		player.draw();

		// 弾を描く
		for (const auto& bullet : bullets)
		{
			bullet.drawFrame(4, Palette::White);
			bullet.draw(Palette::Orange);
		}

		// 敵を描く
		for (const auto& enemy : enemies)
		{
			enemy.draw(ColorF{ 0.3 });
		}
	}
}
```


### 4. 落ちてくる食べ物を集めるゲーム

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/study/9/4.png)

```cpp
# include <Siv3D.hpp>

// 食べ物の種類
constexpr int32 FoodTypeCount = 3;

// 食べ物
struct Food
{
	// 位置
	Vec2 pos;

	// 種類（0: 🍬, 1: 🍩, 2: 🍰）
	int32 type = 0;
};

void Main()
{
	// プレイヤーのテクスチャ
	const Texture playerTexture{ U"😋"_emoji };

	// 各食べ物のテクスチャ
	const Array<Texture> foodTextures =
	{
		Texture{ U"🍬"_emoji },
		Texture{ U"🍩"_emoji },
		Texture{ U"🍰"_emoji },
	};

	// 各食べ物のスコア
	const Array<int32> foodScores =
	{
		10, 20, 100
	};

	// プレイヤーの速度（ピクセル/秒）
	double playerSpeed = 300.0;

	// 食べ物の落下速度（ピクセル/秒）
	double foodSpeed = 200.0;

	// 何秒ごとに食べ物が出現するか
	double foodSpawnTime = 1.0;

	// 前回の食べ物の出現から何秒経過したか
	double accumulatedTime = 0.0;

	// プレイヤーの位置
	Vec2 playerPos{ 400, 500 };

	// スコア
	int32 score = 0;

	// 食べ物
	Array<Food> foods;
	foods << Food{ .pos = Vec2{ 300, 100 }, .type = 2 };
	foods << Food{ .pos = Vec2{ 500, 200 }, .type = 1 };
	foods << Food{ .pos = Vec2{ 700, 300 }, .type = 0 };

	while (System::Update())
	{
		//////////////////////////
		//
		// 更新
		//
		//////////////////////////

		// 前のフレームからの経過時間（秒）
		const double deltaTime = Scene::DeltaTime();
		accumulatedTime += deltaTime;

		// プレイヤーの移動
		if (KeyLeft.pressed()) // [←] キーが押されていたら
		{
			playerPos.x -= (playerSpeed * deltaTime);
		}
		else if (KeyRight.pressed()) // [→] キーが押されていたら
		{
			playerPos.x += (playerSpeed * deltaTime);
		}

		// playerPos.x を 50 以上、750 以下に収める
		playerPos.x = Clamp(playerPos.x, 50.0, 750.0);

		// 食べ物を出現させる
		if (foodSpawnTime <= accumulatedTime)
		{
			foods << Food{ .pos = Vec2{ Random(50.0, 750.0), -50 },
				.type = Random(0, (FoodTypeCount - 1)) };

			// 蓄積時間を減らす
			accumulatedTime -= foodSpawnTime;
		}

		// 食べ物を落下させる
		for (auto& food : foods)
		{
			food.pos.y += (foodSpeed * deltaTime);
		}

		// プレイヤーによる食べ物の獲得
		{
			// プレイヤーのあたり判定円
			const Circle playerCircle{ playerPos, 60 };

			for (auto& food : foods)
			{
				// 食べ物のあたり判定円
				const Circle foodCircle{ food.pos, 30 };

				if (playerCircle.intersects(foodCircle)) // 交差していたら
				{
					// スコアを加算する
					score += foodScores[food.type];

					// 食べ物を画面外に移動させる
					food.pos.y = 9999;
				}
			}
		}

		// 画面外に落ちた食べ物を削除する
		foods.remove_if([](const Food& food) { return (700 < food.pos.y); });

		//////////////////////////
		//
		// 描画
		//
		//////////////////////////

		// デバッグ表示
		ClearPrint();
		Print << U"食べ物の個数: " << foods.size();
		Print << U"accumulatedTime: " << accumulatedTime;
		Print << U"スコア: " << score;

		// 空を描く（グラデーション）
		Rect{ 0, 0, 800, 520 }.draw(Arg::top = ColorF{ 0.1, 0.3, 0.6 }, Arg::bottom = ColorF{ 0.3, 0.7, 1.0 });

		// 地面を描く
		Rect{ 0, 520, 800, 80 }.draw(ColorF{ 0.2, 0.6, 0.3 });

		// プレイヤーを描く
		playerTexture.drawAt(playerPos);

		// 食べ物を描く
		for (const auto& food : foods)
		{
			foodTextures[food.type].scaled(0.5).drawAt(food.pos);
		}
	}
}
```


### 5. 落ちてくる食べ物を集めるゲーム（より楽しく）
- エフェクトや効果音を追加したバージョンです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/study/9/5.png)

```cpp hl_lines="16-38 83-84 86-87 89-90 148-149 151-152 187-189 192-193"
# include <Siv3D.hpp>

// 食べ物の種類
constexpr int32 FoodTypeCount = 3;

// 食べ物
struct Food
{
	// 位置
	Vec2 pos;

	// 種類（0: 🍬, 1: 🍩, 2: 🍰）
	int32 type = 0;
};

// 上昇する文字のエフェクト（公式チュートリアル 40.5）
struct ScoreEffect : IEffect
{
	Vec2 m_start;

	int32 m_score;

	Font m_font;

	ScoreEffect(const Vec2& start, int32 score, const Font& font)
		: m_start{ start }
		, m_score{ score }
		, m_font{ font } {}

	bool update(double t) override
	{
		const HSV color{ (180 - m_score * 1.8), 1.0 - (t * 2.0) };

		m_font(m_score).drawAt(m_start.movedBy(0, t * -120), color);

		return (t < 0.5);
	}
};

void Main()
{
	// プレイヤーのテクスチャ
	const Texture playerTexture{ U"😋"_emoji };

	// 各食べ物のテクスチャ
	const Array<Texture> foodTextures =
	{
		Texture{ U"🍬"_emoji },
		Texture{ U"🍩"_emoji },
		Texture{ U"🍰"_emoji },
	};

	// 各食べ物のスコア
	const Array<int32> foodScores =
	{
		10, 20, 100
	};

	// プレイヤーの速度（ピクセル/秒）
	double playerSpeed = 300.0;

	// 食べ物の落下速度（ピクセル/秒）
	double foodSpeed = 200.0;

	// 何秒ごとに食べ物が出現するか
	double foodSpawnTime = 1.0;

	// 前回の食べ物の出現から何秒経過したか
	double accumulatedTime = 0.0;

	// プレイヤーの位置
	Vec2 playerPos{ 400, 500 };

	// スコア
	int32 score = 0;

	// 食べ物
	Array<Food> foods;
	foods << Food{ .pos = Vec2{ 300, 100 }, .type = 2 };
	foods << Food{ .pos = Vec2{ 500, 200 }, .type = 1 };
	foods << Food{ .pos = Vec2{ 700, 300 }, .type = 0 };

	// 効果音
	const Audio sound{ Wave{ GMInstrument::Marimba, PianoKey::A5, 0.2s } };

	// エフェクト用のフォント
	const Font scoreFont{ FontMethod::MSDF, 48, Typeface::Heavy };

	// エフェクト管理
	Effect effect;

	while (System::Update())
	{
		//////////////////////////
		//
		// 更新
		//
		//////////////////////////

		// 前のフレームからの経過時間（秒）
		const double deltaTime = Scene::DeltaTime();
		accumulatedTime += deltaTime;

		// プレイヤーの移動
		if (KeyLeft.pressed()) // [←] キーが押されていたら
		{
			playerPos.x -= (playerSpeed * deltaTime);
		}
		else if (KeyRight.pressed()) // [→] キーが押されていたら
		{
			playerPos.x += (playerSpeed * deltaTime);
		}

		// playerPos.x を 50 以上、750 以下に収める
		playerPos.x = Clamp(playerPos.x, 50.0, 750.0);

		// 食べ物を出現させる
		if (foodSpawnTime <= accumulatedTime)
		{
			foods << Food{ .pos = Vec2{ Random(50.0, 750.0), -50 },
				.type = Random(0, (FoodTypeCount - 1)) };

			// 蓄積時間を減らす
			accumulatedTime -= foodSpawnTime;
		}

		// 食べ物を落下させる
		for (auto& food : foods)
		{
			food.pos.y += (foodSpeed * deltaTime);
		}

		// プレイヤーによる食べ物の獲得
		{
			// プレイヤーのあたり判定円
			const Circle playerCircle{ playerPos, 60 };

			for (auto& food : foods)
			{
				// 食べ物のあたり判定円
				const Circle foodCircle{ food.pos, 30 };

				if (playerCircle.intersects(foodCircle)) // 交差していたら
				{
					// スコアを加算する
					score += foodScores[food.type];

					// 音を鳴らす
					sound.playOneShot();

					// エフェクトを追加する
					effect.add<ScoreEffect>(food.pos, foodScores[food.type], scoreFont);

					// 食べ物を画面外に移動させる
					food.pos.y = 9999;
				}
			}
		}

		// 画面外に落ちた食べ物を削除する
		foods.remove_if([](const Food& food) { return (700 < food.pos.y); });

		//////////////////////////
		//
		// 描画
		//
		//////////////////////////

		// デバッグ表示
		ClearPrint();
		Print << U"食べ物の個数: " << foods.size();
		Print << U"accumulatedTime: " << accumulatedTime;
		Print << U"スコア: " << score;

		// 空を描く（グラデーション）
		Rect{ 0, 0, 800, 520 }.draw(Arg::top = ColorF{ 0.1, 0.3, 0.6 }, Arg::bottom = ColorF{ 0.3, 0.7, 1.0 });

		// 地面を描く
		Rect{ 0, 520, 800, 80 }.draw(ColorF{ 0.2, 0.6, 0.3 });

		// プレイヤーを描く
		playerTexture.drawAt(playerPos);

		// 食べ物を描く
		for (const auto& food : foods)
		{
			// 食べ物の回転角度
			const double angle = (food.pos.x + food.pos.y * 0.2_deg);
			foodTextures[food.type].scaled(0.5).rotated(angle).drawAt(food.pos);
		}

		// エフェクトを更新・描画する
		effect.update();
	}
}
```