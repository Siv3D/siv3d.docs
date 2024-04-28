# ゲームのサンプル

## 1. ブロックくずし

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/1.gif)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// 1 つのブロックのサイズ | Size of a single block
		constexpr Size BrickSize{ 40, 20 };

		// ボールの速さ（ピクセル / 秒） | Ball speed (pixels / second)
		constexpr double BallSpeedPerSec = 480.0;

		// ボールの速度 | Ball velocity
		Vec2 ballVelocity{ 0, -BallSpeedPerSec };

		// ボール | Ball
		Circle ball{ 400, 400, 8 };

		// ブロックの配列 | Array of bricks
		Array<Rect> bricks;

		for (int32 y = 0; y < 5; ++y)
		{
			for (int32 x = 0; x < (Scene::Width() / BrickSize.x); ++x)
			{
				bricks << Rect{ (x * BrickSize.x), (60 + y * BrickSize.y), BrickSize };
			}
		}

		while (System::Update())
		{
			// パドル | Paddle
			const Rect paddle{ Arg::center(Cursor::Pos().x, 500), 60, 10 };

			// ボールを移動させる | Move the ball
			ball.moveBy(ballVelocity * Scene::DeltaTime());

			// ブロックを順にチェックする | Check bricks in sequence
			for (auto it = bricks.begin(); it != bricks.end(); ++it)
			{
				// ブロックとボールが交差していたら | If block and ball intersect
				if (it->intersects(ball))
				{
					// ブロックの上辺、または底辺と交差していたら | If ball intersects with top or bottom of the block
					if (it->bottom().intersects(ball) || it->top().intersects(ball))
					{
						// ボールの速度の Y 成分の符号を反転する | Reverse the sign of the Y component of the ball's velocity
						ballVelocity.y *= -1;
					}
					else // ブロックの左辺または右辺と交差していたら
					{
						// ボールの速度の X 成分の符号を反転する | Reverse the sign of the X component of the ball's velocity
						ballVelocity.x *= -1;
					}

					// ブロックを配列から削除する（イテレータは無効になる） | Remove the block from the array (the iterator becomes invalid)
					bricks.erase(it);

					// これ以上チェックしない | Do not check any more
					break;
				}
			}

			// 天井にぶつかったら | If the ball hits the ceiling
			if ((ball.y < 0) && (ballVelocity.y < 0))
			{
				// ボールの速度の Y 成分の符号を反転する | Reverse the sign of the Y component of the ball's velocity
				ballVelocity.y *= -1;
			}

			// 左右の壁にぶつかったら | If the ball hits the left or right wall
			if (((ball.x < 0) && (ballVelocity.x < 0))
				|| ((Scene::Width() < ball.x) && (0 < ballVelocity.x)))
			{
				// ボールの速度の X 成分の符号を反転する | Reverse the sign of the X component of the ball's velocity
				ballVelocity.x *= -1;
			}

			// パドルにあたったら | If the ball hits the left or right wall
			if ((0 < ballVelocity.y) && paddle.intersects(ball))
			{
				// パドルの中心からの距離に応じてはね返る方向（速度ベクトル）を変える | Change the direction (velocity vector) of the ball depending on the distance from the center of the paddle
				ballVelocity = Vec2{ (ball.x - paddle.center().x) * 10, -ballVelocity.y }.setLength(BallSpeedPerSec);
			}

			// すべてのブロックを描画する | Draw all the bricks
			for (const auto& brick : bricks)
			{
				// ブロックの Y 座標に応じて色を変える | Change the color of the brick depending on the Y coordinate
				brick.stretched(-1).draw(HSV{ brick.y - 40 });
			}

			// マウスカーソルを非表示にする | Hide the mouse cursor
			Cursor::RequestStyle(CursorStyle::Hidden);

			// ボールを描く | Draw the ball
			ball.draw();

			// パドルを描く | Draw the paddle
			paddle.rounded(3).draw();
		}
	}
	```

## 2. 落ちてくるアイテムを拾うゲーム

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/2.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// アイテムの情報
	struct ItemInfo
	{
		// アイテムのテクスチャ
		Texture texture;

		// 落下速度（ピクセル / 秒）
		double speed;

		// 得点
		int32 score;
	};

	// フィールド上のアイテム
	struct Item
	{
		// アイテムの種類
		int32 type;

		// アイテムの現在位置
		Vec2 pos;
	};

	void Main()
	{
		// プレイヤーの絵文字テクスチャ
		const Texture playerTexture{ U"😃"_emoji };

		// スコア表示用のフォント
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// プレイヤーのスピード（ピクセル / 秒)
		constexpr double PlayerSpeed = 500.0;

		// アイテムが発生する時間間隔（秒）
		constexpr double ItemSpawnInterval = 0.5;

		// アイテムのあたり判定の円の半径（ピクセル）
		constexpr double ItemRadius = 40.0;

		// アイテムのテクスチャ
		const Array<ItemInfo> ItemInfos =
		{
			{ Texture{ U"🍩"_emoji }, 200.0, 100 },
			{ Texture{ U"🍰"_emoji }, 300.0, 500 },
		};

		// 最後にアイテムが発生してからの経過時間（秒）
		double itemSpawnAccumulatedTime = 0.0;

		// プレイヤーの座標
		Vec2 playerPos{ 400, 500 };

		// 現在画面上にあるアイテムの配列
		Array<Item> items;

		// スコア
		int32 score = 0;

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
					playerPos.x -= (PlayerSpeed * deltaTime);
				}
				else if (KeyRight.pressed()) // [→] キーが押されていたら
				{
					playerPos.x += (PlayerSpeed * deltaTime);
				}

				// 壁の外に出ないようにする
				// Clamp(x, min, max) は, x を min～max の範囲に収めた値を返す
				playerPos.x = Clamp(playerPos.x, 0.0, 800.0);
			}

			// アイテムの出現と移動と消滅に関する処理
			{
				itemSpawnAccumulatedTime += deltaTime;

				// spawnTime が経過するごとに新しいアイテムを出現させる
				while (ItemSpawnInterval <= itemSpawnAccumulatedTime)
				{
					// 新しく出現するアイテムを配列に追加する
					items << Item
					{
						.type = (RandomBool(0.9) ? 0 : 1), // アイテムの種類
						.pos = { Random(100, 700), -100 }, // アイテムの初期座標
					};

					itemSpawnAccumulatedTime -= ItemSpawnInterval;
				}

				// すべてのアイテムについて移動処理を行う
				for (auto& item : items)
				{
					item.pos.y += (ItemInfos[item.type].speed * deltaTime);
				}

				// プレイヤーのあたり判定の円
				const Circle playerCircle{ playerPos, 60 };

				// アイテムのあたり判定と回収したアイテムの削除
				for (auto it = items.begin(); it != items.end();)
				{
					// アイテムのあたり判定の円
					const Circle itemCircle{ it->pos, ItemRadius };

					// 交差したらアイテムを削除
					if (playerCircle.intersects(itemCircle))
					{
						// (削除する前に) スコアを加算する
						score += ItemInfos[it->type].score;

						// アイテムを削除する
						it = items.erase(it);
					}
					else
					{
						// イテレータを次のアイテムに進める
						++it;
					}
				}

				// 画面外に出たアイテムを消去する
				items.remove_if([](const Item& item) { return (700 < item.pos.y); });
			}

			////////////////////////////////
			//
			//	描画
			//
			////////////////////////////////

			// 背景を描画する
			Scene::Rect().draw(Arg::top = ColorF{ 0.1, 0.4, 0.8 }, Arg::bottom = ColorF{ 0.3, 0.7, 1.0 });

			// 地面を描画する
			Rect{ Arg::bottomLeft(0, Scene::Height()), Scene::Width(), 60 }.draw(ColorF{ 0.2, 0.6, 0.3 });

			// プレイヤーのテクスチャを描画する
			playerTexture.drawAt(playerPos);

			// アイテムを描画する
			for (const auto& item : items)
			{
				ItemInfos[item.type].texture.resized(ItemRadius * 2).drawAt(item.pos);
			}

			// スコアを描画する
			font(ThousandsSeparate(score)).draw(30, Vec2{ 20, 20 });
		}
	}
	```

## 3. 15 パズル

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/3.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// 2つ のピースが隣り合っているかを判定する
	bool Swappable(int32 a, int32 b)
	{
		return ((a / 4 == b / 4) && (AbsDiff(a, b) == 1))
			|| ((a % 4 == b % 4) && (AbsDiff(a, b) == 4));
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

		// ピースのサイズ
		constexpr int32 CellSize = 100;

		// 位置
		constexpr Point Offset{ 60, 40 };

		// ダイアログから画像を選択する
		const Image image = Dialog::OpenImage();

		// 正方形に切り抜く
		const Texture texture{ image.squareClipped(), TextureDesc::Mipped };

		// ランダムな操作でパズルをシャッフルする
		Array<int32> pieces = Range(0, 15);
		{
			// 空白の位置
			int32 blankPos = 15;

			for (int32 i = 0; i < 1000; ++i)
			{
				const int32 to = (blankPos + Sample({ -4, -1, 1, 4 }));

				if (InRange(to, 0, 15) && Swappable(blankPos, to))
				{
					std::swap(pieces[blankPos], pieces[to]);
					blankPos = to;
				}
			}
		}

		// 掴んでいるピースの番号
		Optional<int32> grabbed;

		while (System::Update())
		{
			Rect{ Offset, (CellSize * 4) }
				.drawShadow(Vec2{ 0, 2 }, 12, 8)
				.draw(ColorF{ 0.25 })
				.drawFrame(0, 8, ColorF{ 0.3, 0.5, 0.7 });

			if (not MouseL.pressed())
			{
				grabbed.reset();
			}

			for (int32 i = 0; i < 16; ++i)
			{
				const int32 pieceID = pieces[i];
				const Rect rect = Rect{ (CellSize * (i % 4)), (CellSize * (i / 4)), CellSize }.movedBy(Offset);

				if (pieceID == 15)
				{
					if (grabbed && rect.mouseOver() && Swappable(i, grabbed.value()))
					{
						std::swap(pieces[i], pieces[grabbed.value()]);
						grabbed = i;
					}

					continue;
				}

				if (rect.leftClicked())
				{
					grabbed = i;
				}

				rect(texture.uv((pieceID % 4 * 0.25), (pieceID / 4 * 0.25), 0.25, 0.25))
					.draw()
					.drawFrame(1, 0, ColorF{ 1.0, 0.75 });

				if (grabbed == i)
				{
					rect.draw(ColorF{ 1.0, 0.5, 0.0, 0.3 });
				}

				if (rect.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);
				}
			}

			// 見本を描く
			texture.resized(180)
				.draw((Offset.x + CellSize * 4 + 40), Offset.y)
				.drawFrame(0, 4, ColorF{ 0.3, 0.5, 0.7 });
		}
	}
	```


## 4. 数つなぎ

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/4.gif)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	struct Bubble
	{
		// バブルの円の半径
		static constexpr int32 Radius = 30;

		// バブルの円
		Circle circle;

		// バブルのインデックス
		int32 index;

		// 接続済みなら true に
		bool connected = false;

		void draw(const Font& font) const
		{
			if (connected)
			{
				circle.drawShadow(Vec2{ 1, 2 }, 10, 3).draw()
					.drawFrame(2, 0, ColorF{ 0.3, 0.6, 1.0 });
			}
			else
			{
				circle.draw();
			}

			font(index + 1).drawAt(36, circle.center, ColorF{ 0.25 });
		}
	};

	// バブルどうしが重なっていないかチェックする
	bool CheckBubbles(const Array<Bubble>& bubbles)
	{
		for (size_t i = 0; i < bubbles.size(); ++i)
		{
			for (size_t k = (i + 1); k < bubbles.size(); ++k)
			{
				// 重なっている
				if (bubbles[i].circle.stretched(5)
					.intersects(bubbles[k].circle.stretched(5)))
				{
					return false;
				}
			}
		}

		return true;
	}

	// 指定した個数のバブルを重ならないように生成する
	Array<Bubble> MakeBubbles(int32 count)
	{
		Array<Bubble> bubbles(count);

		do
		{
			for (int32 i = 0; i < count; ++i)
			{
				// バブルのインデックス
				bubbles[i].index = i;

				// バブルの円
				bubbles[i].circle.set(RandomVec2(Circle{ Scene::Center(), (Scene::Height() / 2 - Bubble::Radius) }), Bubble::Radius);
			}
		} while (not CheckBubbles(bubbles));

		return bubbles;
	}

	// 指定したレベルにおけるバブルの個数
	constexpr int32 GetBubbleCount(int32 level)
	{
		return Min(level, 15);
	}

	// 指定したレベルにおける制限時間（秒）
	constexpr Duration GetTime(int32 level)
	{
		return Duration{ (level <= 15) ? 8.0 : 8.0 - Min((level - 15) * 0.05, 2.0) };
	}

	void Main()
	{
		Scene::SetBackground(Palette::White);

		const Font font{ FontMethod::MSDF, 48, Typeface::Medium };

		Effect effect;

		// 効果音を作成する
		const Array<PianoKey> keys = { PianoKey::C5,  PianoKey::D5, PianoKey::E5, PianoKey::F5, PianoKey::G5,
			PianoKey::A5, PianoKey::B5, PianoKey::C6, PianoKey::D6, PianoKey::E6,
			PianoKey::F6, PianoKey::G6, PianoKey::A6, PianoKey::B6, PianoKey::C7 };
		const Array<Audio> sounds = keys.map([](auto k) { return Audio{ GMInstrument::Glockenspiel, k, 0.3s }; });

		// ハイスコア
		int32 highScore = 0;

		// 現在のレベル
		int32 level = 1;

		// 接続数
		int32 connected = 0;

		// 残り時間のタイマー
		Timer timer{ GetTime(level), StartImmediately::Yes };

		// バブル
		Array<Bubble> bubbles = MakeBubbles(GetBubbleCount(level));

		while (System::Update())
		{
			const double delta = Scene::DeltaTime();

			for (auto& bubble : bubbles)
			{
				if ((bubble.index == connected)
					&& (not bubble.connected)
					&& bubble.circle.stretched(10).mouseOver())
				{
					// 接続済みにする
					bubble.connected = true;

					// 接続数を増やす
					++connected;

					// エフェクトを追加する
					effect.add([pos = Cursor::Pos()](double t)
					{
						Circle{ pos, (Bubble::Radius + t * 200) }.drawFrame(2, 0, ColorF{ 0.2, 0.5, 1.0, (1.0 - t * 2.5) });
						return (t < 0.4);
					});

					// バブルの数字に応じて効果音を鳴らす
					sounds[bubble.index].playOneShot(0.8);
				}

				// バブルを円周に沿って移動させる
				bubble.circle.center = OffsetCircular{ Scene::Center(), bubble.circle.center }
					.rotate((IsEven(bubble.index) ? 20_deg : -20_deg) * delta);
			}

			// バブルをすべてつなぐか、時間切れになったら
			if (const bool failed = timer.reachedZero();
				(connected == GetBubbleCount(level)) || failed)
			{
				// レベルを更新する
				level = (failed ? 1 : ++level);

				// 接続数をリセットする
				connected = 0;

				// 制限時間をリセットする
				timer = Timer{ GetTime(level), StartImmediately::Yes };

				// バブルを再生成する
				bubbles = MakeBubbles(GetBubbleCount(level));

				// ハイスコアを更新する
				highScore = Max(highScore, level);

				// タイトルを更新する
				Window::SetTitle(U"Level {} (High score: {})"_fmt(level, highScore));
			}

			// 制限時間を表す背景を描画する
			RectF{ Scene::Width(), (Scene::Height() * timer.progress0_1()) }.draw(HSV{ (level * 30), 0.3, 0.9 });

			// バブルをつなぐ線を描画する
			for (int32 i = 0; i < (connected - 1); ++i)
			{
				Line{ bubbles[i].circle.center, bubbles[i + 1].circle.center }.draw(3, Palette::Orange);
			}

			// バブルを描画する
			for (const auto& bubble : bubbles)
			{
				bubble.draw(font);
			}

			// エフェクトを描画する
			effect.update();
		}
	}
	```


## 5. タイピングゲーム

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/5.gif)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// 問題文のリスト
		const Array<String> texts =
		{
			U"Practice makes perfect.",
			U"Don't cry over spilt milk.",
			U"Faith will move mountains.",
			U"Nothing ventured, nothing gained.",
			U"Bad news travels fast.",
		};

		// 問題文をランダムに選ぶ
		String target = texts.choice();

		// 入力中の文字列
		String input;

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		while (System::Update())
		{
			// テキスト入力（TextInputMode::DenyControl: エンターやタブ、バックスペースは受け付けない）
			TextInput::UpdateText(input, TextInputMode::DenyControl);

			// 誤った入力が含まれていたら削除する
			while (not target.starts_with(input))
			{
				input.pop_back();
			}

			// 一致したら次の問題へ移る
			if (input == target)
			{
				// 問題文をランダムに選ぶ
				target = texts.choice();

				// 入力文字列をクリアする	
				input.clear();
			}

			// 問題文を描画する
			font(target).draw(40, Vec2{ 40, 80 }, ColorF{ 0.98 });

			// 入力中の文字を描画する
			font(input).draw(40, Vec2{ 40, 80 }, ColorF{ 0.12 });
		}
	}
	```


## 6. 絵文字タワー

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/6.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズ
		Window::Resize(1280, 720);

		// 背景色を設定
		Scene::SetBackground(ColorF{ 0.2, 0.7, 1.0 });

		// 登場する絵文字
		const Array<String> emojis = { U"🐘", U"🐧", U"🐐", U"🐤" };

		Array<MultiPolygon> polygons;

		Array<Texture> textures;

		for (const auto& emoji : emojis)
		{
			// 絵文字の画像から形状情報を作成する
			polygons << Emoji::CreateImage(emoji).alphaToPolygonsCentered().simplified(2.0);

			// 絵文字の画像からテクスチャを作成する
			textures << Texture{ Emoji{ emoji } };
		}

		// 2D 物理演算のシミュレーションステップ（秒）
		constexpr double StepTime = (1.0 / 200.0);

		// 2D 物理演算のシミュレーション蓄積時間（秒）
		double accumulatedTime = 0.0;

		// 2D 物理演算のワールド
		P2World world;

		// [_] 地面
		const P2Body ground = world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -300, 0, 300, 0 });

		// 動物の物体
		Array<P2Body> bodies;

		// 物体の ID と絵文字のインデックスの対応テーブル
		HashTable<P2BodyID, size_t> table;

		// 絵文字のインデックス
		size_t index = Random(polygons.size() - 1);

		// 2D カメラ
		Camera2D camera{ Vec2{ 0, -200 } };

		while (System::Update())
		{
			accumulatedTime += Scene::DeltaTime();

			while (StepTime <= accumulatedTime)
			{
				// 2D 物理演算のワールドを更新する
				world.update(StepTime);

				accumulatedTime -= StepTime;
			}

			// 地面より下に落ちた物体は削除する
			for (auto it = bodies.begin(); it != bodies.end();)
			{
				if (100 < it->getPos().y)
				{
					// 対応テーブルからも削除
					table.erase(it->id());

					it = bodies.erase(it);
				}
				else
				{
					++it;
				}
			}

			// 2D カメラを更新する
			camera.update();
			{
				// 2D カメラから Transformer2D を作成する
				const auto t = camera.createTransformer();

				// 左クリックされたら
				if (MouseL.down())
				{
					// ボディを追加する
					bodies << world.createPolygons(P2Dynamic, Cursor::PosF(), polygons[index], P2Material{ 0.1, 0.0, 1.0 });

					// ボディ ID と絵文字のインデックスの組を対応テーブルに追加する
					table.emplace(bodies.back().id(), std::exchange(index, Random(polygons.size() - 1)));
				}

				// すべてのボディを描画する
				for (const auto& body : bodies)
				{
					textures[table[body.id()]].rotated(body.getAngle()).drawAt(body.getPos());
				}

				// 地面を描画する
				ground.draw(Palette::Green);

				// 現在操作できる絵文字を描画する
				textures[index].drawAt(Cursor::PosF(), AlphaF(0.5 + Periodic::Sine0_1(1s) * 0.5));
			}

			// 2D カメラの操作を描画する
			camera.draw(Palette::Orange);
		}
	}
	```


## 7. シューティングゲーム

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/7.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// 敵の位置をランダムに作成する関数
	Vec2 GenerateEnemy()
	{
		return RandomVec2({ 50, 750 }, -20);
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.1, 0.2, 0.7 });

		const Font font{ FontMethod::MSDF, 48 };

		// 自機テクスチャ
		const Texture playerTexture{ U"🤖"_emoji };
		// 敵テクスチャ
		const Texture enemyTexture{ U"👾"_emoji };

		// 自機
		Vec2 playerPos{ 400, 500 };
		// 敵
		Array<Vec2> enemies = { GenerateEnemy() };

		// 自機ショット
		Array<Vec2> playerBullets;
		// 敵ショット
		Array<Vec2> enemyBullets;

		// 自機のスピード
		constexpr double PlayerSpeed = 550.0;
		// 自機ショットのスピード
		constexpr double PlayerBulletSpeed = 500.0;
		// 敵のスピード
		constexpr double EnemySpeed = 100.0;
		// 敵ショットのスピード
		constexpr double EnemyBulletSpeed = 300.0;

		// 敵の発生間隔の初期値（秒）
		constexpr double InitialEnemySpawnInterval = 2.0;
		// 敵の発生間隔（秒）
		double enemySpawnTime = InitialEnemySpawnInterval;
		// 敵の発生の蓄積時間（秒）
		double enemyAccumulatedTime = 0.0;

		// 自機ショットのクールタイム（秒）
		constexpr double PlayerShotCoolTime = 0.1;
		// 自機ショットのクールタイムタイマー（秒）
		double playerShotTimer = 0.0;

		// 敵ショットのクールタイム（秒）
		constexpr double EnemyShotCoolTime = 0.9;
		// 敵ショットのクールタイムタイマー（秒）
		double enemyShotTimer = 0.0;

		Effect effect;

		// ハイスコア
		int32 highScore = 0;
		// 現在のスコア
		int32 score = 0;

		while (System::Update())
		{
			// ゲームオーバー判定
			bool gameover = false;

			const double deltaTime = Scene::DeltaTime();
			enemyAccumulatedTime += deltaTime;
			playerShotTimer = Min((playerShotTimer + deltaTime), PlayerShotCoolTime);
			enemyShotTimer += deltaTime;

			// 敵を発生させる
			while (enemySpawnTime <= enemyAccumulatedTime)
			{
				enemyAccumulatedTime -= enemySpawnTime;
				enemySpawnTime = Max(enemySpawnTime * 0.95, 0.3);
				enemies << GenerateEnemy();
			}

			// 自機の移動
			const Vec2 move = Vec2{ (KeyRight.pressed() - KeyLeft.pressed()), (KeyDown.pressed() - KeyUp.pressed()) }
				.setLength(deltaTime * PlayerSpeed * (KeyShift.pressed() ? 0.5 : 1.0));
			playerPos.moveBy(move).clamp(Scene::Rect());

			// 自機ショットの発射
			if (PlayerShotCoolTime <= playerShotTimer)
			{
				playerShotTimer -= PlayerShotCoolTime;
				playerBullets << playerPos.movedBy(0, -50);
			}

			// 自機ショットを移動させる
			for (auto& playerBullet : playerBullets)
			{
				playerBullet.y += (deltaTime * -PlayerBulletSpeed);
			}
			// 画面外に出た自機ショットを削除する
			playerBullets.remove_if([](const Vec2& b) { return (b.y < -40); });

			// 敵を移動させる
			for (auto& enemy : enemies)
			{
				enemy.y += (deltaTime * EnemySpeed);
			}
			// 画面外に出た敵を削除する
			enemies.remove_if([&](const Vec2& e)
			{
				if (700 < e.y)
				{
					// 敵が画面外に出たらゲームオーバー
					gameover = true;
					return true;
				}
				else
				{
					return false;
				}
			});

			// 敵ショットの発射
			if (EnemyShotCoolTime <= enemyShotTimer)
			{
				enemyShotTimer -= EnemyShotCoolTime;

				for (const auto& enemy : enemies)
				{
					enemyBullets << enemy;
				}
			}

			// 敵ショットを移動させる
			for (auto& enemyBullet : enemyBullets)
			{
				enemyBullet.y += (deltaTime * EnemyBulletSpeed);
			}
			// 画面外に出た自機ショットを削除する
			enemyBullets.remove_if([](const Vec2& b) {return (700 < b.y); });

			////////////////////////////////
			//
			//	攻撃判定
			//
			////////////////////////////////

			// 敵 vs 自機ショット
			for (auto itEnemy = enemies.begin(); itEnemy != enemies.end();)
			{
				const Circle enemyCircle{ *itEnemy, 40 };
				bool skip = false;

				for (auto itBullet = playerBullets.begin(); itBullet != playerBullets.end();)
				{
					if (enemyCircle.intersects(*itBullet))
					{
						// 爆発エフェクトを追加する
						effect.add([pos = *itEnemy](double t)
						{
							const double t2 = ((0.5 - t) * 2.0);
							Circle{ pos, (10 + t * 280) }.drawFrame((20 * t2), AlphaF(t2 * 0.5));
							return (t < 0.5);
						});

						itEnemy = enemies.erase(itEnemy);
						playerBullets.erase(itBullet);
						++score;
						skip = true;
						break;
					}

					++itBullet;
				}

				if (skip)
				{
					continue;
				}

				++itEnemy;
			}

			// 敵ショット vs 自機
			for (const auto& enemyBullet : enemyBullets)
			{
				// 敵ショットが playerPos の 20 ピクセル以内に接近したら
				if (enemyBullet.distanceFrom(playerPos) <= 20)
				{
					// ゲームオーバーにする
					gameover = true;
					break;
				}
			}

			// ゲームオーバーならリセットする
			if (gameover)
			{
				playerPos = Vec2{ 400, 500 };
				enemies.clear();
				playerBullets.clear();
				enemyBullets.clear();
				enemySpawnTime = InitialEnemySpawnInterval;
				highScore = Max(highScore, score);
				score = 0;
			}

			////////////////////////////////
			//
			//	描画
			//
			////////////////////////////////

			// 背景のアニメーションを描画する
			for (int32 i = 0; i < 12; ++i)
			{
				const double a = Periodic::Sine0_1(2s, Scene::Time() - (2.0 / 12 * i));
				Rect{ 0, (i * 50), 800, 50 }.draw(ColorF(1.0, a * 0.2));
			}

			// 自機を描画する
			playerTexture.resized(80).flipped().drawAt(playerPos);

			// 自機ショットを描画する
			for (const auto& playerBullet : playerBullets)
			{
				Circle{ playerBullet, 8 }.draw(Palette::Orange);
			}

			// 敵を描画する
			for (const auto& enemy : enemies)
			{
				enemyTexture.resized(60).drawAt(enemy);
			}

			// 敵ショットを描画する
			for (const auto& enemyBullet : enemyBullets)
			{
				Circle{ enemyBullet, 4 }.draw(Palette::White);
			}

			// 爆発エフェクトを描画する
			effect.update();

			// スコアを描画する
			font(U"{} [{}]"_fmt(score, highScore)).draw(30, Arg::bottomRight(780, 580));
		}
	}
	```


## 8. ピンボール

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/8.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// 外周の枠の頂点リストを作成する関数
	LineString CreateFrame(const Vec2& leftAnchor, const Vec2& rightAnchor)
	{
		Array<Vec2> points = { leftAnchor, Vec2{ -70, -20 } };
		for (int32 i = -30; i <= 30; ++i)
		{
			points << OffsetCircular(Vec2{ 0.0, -120 }, 70, (i * 3_deg));
		}
		points << Vec2{ 70, -20 } << rightAnchor;
		return LineString{ points };
	}

	// 接触しているかに応じて色を決定する関数
	ColorF GetColor(const P2Body& body, const HashSet<P2BodyID>& list)
	{
		return list.contains(body.id()) ? Palette::White : Palette::Orange;
	}

	void Main()
	{
		// 背景色を設定する
		Scene::SetBackground(ColorF(0.2, 0.3, 0.4));

		// 2D 物理演算のシミュレーションステップ（秒）
		constexpr double StepTime = (1.0 / 200.0);

		// 2D 物理演算のシミュレーション蓄積時間（秒）
		double accumulatedTime = 0.0;

		// 物理演算用のワールド
		P2World world{ 60.0 };

		// 左右フリッパーの軸の座標
		constexpr Vec2 LeftFlipperAnchor{ -25, 10 }, RightFlipperAnchor{ 25, 10 };

		// 固定の枠
		Array<P2Body> frames;
		{
			// 外周
			frames << world.createLineString(P2Static, Vec2{ 0, 0 }, CreateFrame(LeftFlipperAnchor, RightFlipperAnchor));
			// 左上の (
			frames << world.createLineString(P2Static, Vec2{ 0, 0 }, LineString{ Range(-25, -10).map([=](int32 i) { return OffsetCircular(Vec2{ 0.0, -120 }, 55, (i * 3_deg)).toVec2(); }) });
			// 右上の )
			frames << world.createLineString(P2Static, Vec2{ 0, 0 }, LineString{ Range(10, 25).map([=](int32 i) { return OffsetCircular(Vec2{ 0.0, -120 }, 55, (i * 3_deg)).toVec2(); }) });
		}

		// バンパー
		Array<P2Body> bumpers;
		{
			// ● x3
			{
				const P2Material material{ .restitution = 1.0, .restitutionThreshold = 0.1 };
				bumpers << world.createCircle(P2Static, Vec2{ 0, -170 }, 5, material);
				bumpers << world.createCircle(P2Static, Vec2{ -20, -150 }, 5, material);
				bumpers << world.createCircle(P2Static, Vec2{ 20, -150 }, 5, material);
			}
			// ▲ x2
			{
				const P2Material material{ .restitution = 0.8, .restitutionThreshold = 0.1 };
				bumpers << world.createTriangle(P2Static, Vec2{ 0, 0 }, Triangle{ -60, -50, -40, -15, -60, -30 }, material);
				bumpers << world.createTriangle(P2Static, Vec2{ 0, 0 }, Triangle{ 60, -50, 60, -30, 40, -15 }, material);
			}
		}

		const P2Material softMaterial{ .density = 0.1, .restitution = 0.0 };

		// 左フリッパー
		P2Body leftFlipper = world.createRect(P2Dynamic, LeftFlipperAnchor, RectF{ 0, 0.4, 21, 4.5 }, softMaterial);
		// 左フリッパーのジョイント
		const P2PivotJoint leftJoint = world.createPivotJoint(frames[0], leftFlipper, LeftFlipperAnchor).setLimits(-20_deg, 25_deg).setLimitsEnabled(true);

		// 右フリッパー
		P2Body rightFlipper = world.createRect(P2Dynamic, RightFlipperAnchor, RectF{ -21, 0.4, 21, 4.5 }, softMaterial);
		// 右フリッパーのジョイント
		const P2PivotJoint rightJoint = world.createPivotJoint(frames[0], rightFlipper, RightFlipperAnchor).setLimits(-25_deg, 20_deg).setLimitsEnabled(true);

		// スピナー ＋
		const P2Body spinner = world.createRect(P2Dynamic, Vec2{ -58, -120 }, SizeF{ 20, 1 }, softMaterial).addRect(RectF{ Arg::center(0, 0), 1, 20 }, P2Material{ 0.01, 0.0 });
		// スピナーのジョイント
		P2PivotJoint spinnerJoint = world.createPivotJoint(frames[0], spinner, Vec2{ -58, -120 }).setMaxMotorTorque(0.05).setMotorSpeed(0).setMotorEnabled(true);

		// 風車の |
		frames << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -40, -60, -40, -40 });
		// 風車の羽 ／
		const P2Body windmillWing = world.createRect(P2Dynamic, Vec2{ -40, -60 }, SizeF{ 30, 2 }, P2Material{ 0.1, 0.8 });
		// 風車のジョイント
		const P2PivotJoint windmillJoint = world.createPivotJoint(frames.back(), windmillWing, Vec2{ -40, -60 }).setMotorSpeed(240_deg).setMaxMotorTorque(10000.0).setMotorEnabled(true);

		// 振り子の軸
		const P2Body pendulumBase = world.createPlaceholder(P2Static, Vec2{ 0, -190 });
		// 振り子 ●
		P2Body pendulum = world.createCircle(P2Dynamic, Vec2{ 0, -120 }, 4, P2Material{ 0.1, 1.0 });
		// 振り子のジョイント
		const P2DistanceJoint pendulumJoint = world.createDistanceJoint(pendulumBase, Vec2{ 0, -190 }, pendulum, Vec2{ 0, -120 }, 70);

		// エレベーターの上部 ●
		const P2Body elevatorA = world.createCircle(P2Static, Vec2{ 40, -100 }, 3);
		// エレベーターの床 －
		const P2Body elevatorB = world.createRect(P2Dynamic, Vec2{ 40, -100 }, SizeF{ 20, 2 });
		// エレベーターのジョイント
		P2SliderJoint elevatorSliderJoint = world.createSliderJoint(elevatorA, elevatorB, Vec2{ 40, -100 }, Vec2::Down()).setLimits(5, 50).setLimitEnabled(true).setMaxMotorForce(10000).setMotorSpeed(-100);

		// ボール 〇
		const P2Body ball = world.createCircle(P2Dynamic, Vec2{ -40, -120 }, 4, P2Material{ 0.05, 0.0 });
		const P2BodyID ballID = ball.id();

		// エレベーターのアニメーション用ストップウォッチ
		Stopwatch sliderStopwatch{ StartImmediately::Yes };

		// 2D カメラ
		const Camera2D camera{ Vec2{ 0, -80 }, 2.4 };

		while (System::Update())
		{
			////////////////////////////////
			//
			//	更新
			//
			////////////////////////////////

			if (4s < sliderStopwatch)
			{
				// エレベーターの巻き上げを停止
				elevatorSliderJoint.setMotorEnabled(false);
				sliderStopwatch.restart();
			}
			else if (2s < sliderStopwatch)
			{
				// エレベーターの巻き上げ
				elevatorSliderJoint.setMotorEnabled(true);
			}

			// ボールと接触しているボディの ID
			HashSet<P2BodyID> collidedIDs;

			// 物理演算ワールドの更新
			for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
			{
				// 振り子の揺れをおさえる抵抗
				pendulum.applyForce(Vec2{ (pendulum.getVelocity().x < 0.0) ? 0.0001 : -0.0001, 0.0 });

				// 左フリッパーの操作
				leftFlipper.applyTorque(KeyLeft.pressed() ? -80 : 40);

				// 右フリッパーの操作
				rightFlipper.applyTorque(KeyRight.pressed() ? 80 : -40);

				world.update(StepTime);

				// ボールと接触しているボディの ID を格納
				for (auto&& [pair, collision] : world.getCollisions())
				{
					if (pair.a == ballID)
					{
						collidedIDs.emplace(pair.b);
					}
					else if (pair.b == ballID)
					{
						collidedIDs.emplace(pair.a);
					}
				}
			}

			////////////////////////////////
			//
			//	描画
			//
			////////////////////////////////

			// 描画用の Transformer2D
			const auto transformer = camera.createTransformer();

			// 枠の描画
			for (const auto& frame : frames)
			{
				frame.draw(Palette::Skyblue);
			}

			// スピナーの描画
			spinner.draw(GetColor(spinner, collidedIDs));

			// バンパーの描画
			for (const auto& bumper : bumpers)
			{
				bumper.draw(GetColor(bumper, collidedIDs));
			}

			// 風車の描画
			windmillWing.draw(GetColor(windmillWing, collidedIDs));

			// 振り子の描画
			pendulum.draw(GetColor(pendulum, collidedIDs));

			// エレベーターの描画
			elevatorA.draw(GetColor(elevatorA, collidedIDs));
			elevatorB.draw(GetColor(elevatorB, collidedIDs));

			// ボールの描画
			ball.draw(Palette::White);

			// フリッパーの描画
			leftFlipper.draw(Palette::Orange);
			rightFlipper.draw(Palette::Orange);

			// ジョイントの可視化
			leftJoint.draw(Palette::Red);
			rightJoint.draw(Palette::Red);
			spinnerJoint.draw(Palette::Red);
			windmillJoint.draw(Palette::Red);
			pendulumJoint.draw(Palette::Red);
			elevatorSliderJoint.draw(Palette::Red);
		}
	}
	```


## 9. クッキークリッカー

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/9.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	//ゲームのセーブデータ
	struct SaveData
	{
		double cookies;

		Array<int32> itemCounts;

		// シリアライズに対応させるためのメンバ関数を定義する
		template <class Archive>
		void SIV3D_SERIALIZE(Archive& archive)
		{
			archive(cookies, itemCounts);
		}
	};

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

	// クッキーが降るエフェクト
	struct CookieBackgroundEffect : IEffect
	{
		// 初期座標
		Vec2 m_start;

		// 回転角度
		double m_angle;

		// テクスチャ
		Texture m_texture;

		CookieBackgroundEffect(const Vec2& start, const Texture& texture)
			: m_start{ start }
			, m_angle{ Random(2_pi) }
			, m_texture{ texture } {}

		bool update(double t) override
		{
			const Vec2 pos = m_start + 0.5 * t * t * Vec2{ 0, 120 };

			m_texture.scaled(0.3).rotated(m_angle).drawAt(pos, ColorF{ 1.0, (1.0 - t / 3.0) });

			return (t < 3.0);
		}
	};

	// クッキーが舞うエフェクト
	struct CookieEffect : IEffect
	{
		// 初期座標
		Vec2 m_start;

		// 初速
		Vec2 m_velocity;

		// 拡大倍率
		double m_scale;

		// 回転角度
		double m_angle;

		// テクスチャ
		Texture m_texture;

		CookieEffect(const Vec2& start, const Texture& texture)
			: m_start{ start }
			, m_velocity{ Circular{ 80, Random(-40_deg, 40_deg) } }
			, m_scale{ Random(0.2, 0.3) }
			, m_angle{ Random(2_pi) }
			, m_texture{ texture } {}

		bool update(double t) override
		{
			const Vec2 pos = m_start
				+ m_velocity * t + 0.5 * t * t * Vec2{ 0, 120 };

			m_texture.scaled(m_scale).rotated(m_angle).drawAt(pos, ColorF{ 1.0, (1.0 - t) });

			return (t < 1.0);
		}
	};

	// 「+1」が上昇するエフェクト
	struct PlusOneEffect : IEffect
	{
		// 初期座標
		Vec2 m_start;

		// フォント
		Font m_font;

		PlusOneEffect(const Vec2& start, const Font& font)
			: m_start{ start }
			, m_font{ font } {}

		bool update(double t) override
		{
			m_font(U"+1").drawAt(24, m_start.movedBy(0, t * -120), ColorF{ 1.0, (1.0 - t) });

			return (t < 1.0);
		}
	};

	// アイテムのデータ
	struct Item
	{
		// アイテムの絵文字
		Texture emoji;

		// アイテムの名前
		String name;

		// アイテムを初めて購入するときのコスト
		int32 initialCost;

		// アイテムの CPS
		int32 cps;

		// アイテムを count 個持っているときの購入コストを返す
		int32 getCost(int32 count) const
		{
			return initialCost * (count + 1);
		}
	};

	// クッキーのばね
	class CookieSpring
	{
	public:

		void update(double deltaTime, bool pressed)
		{
			// ばねの蓄積時間を加算する
			m_accumulatedTime += deltaTime;

			while (0.005 <= m_accumulatedTime)
			{
				// ばねの力（変化を打ち消す方向）
				double force = (-0.02 * m_x);

				// 画面を押しているときに働く力
				if (pressed)
				{
					force += 0.004;
				}

				// 速度に力を適用（減衰もさせる）
				m_velocity = (m_velocity + force) * 0.92;

				// 位置に反映
				m_x += m_velocity;

				m_accumulatedTime -= 0.005;
			}
		}

		double get() const
		{
			return m_x;
		}

	private:

		// ばねの伸び
		double m_x = 0.0;

		// ばねの速度
		double m_velocity = 0.0;

		// ばねの蓄積時間
		double m_accumulatedTime = 0.0;
	};

	// クッキーの後光を描く関数
	void DrawHalo(const Vec2& center)
	{
		for (int32 i = 0; i < 4; ++i)
		{
			double startAngle = Scene::Time() * 15_deg + i * 90_deg;
			Circle{ center, 180 }.drawPie(startAngle, 60_deg, ColorF{ 1.0, 0.3 }, ColorF{ 1.0, 0.0 });
		}

		for (int32 i = 0; i < 6; ++i)
		{
			double startAngle = Scene::Time() * -15_deg + i * 60_deg;
			Circle{ center, 180 }.drawPie(startAngle, 40_deg, ColorF{ 1.0, 0.3 }, ColorF{ 1.0, 0.0 });
		}
	}

	// アイテムの所有数をもとに CPS を計算する関数
	int32 CalculateCPS(const Array<Item>& ItemTable, const Array<int32>& itemCounts)
	{
		int32 cps = 0;

		for (size_t i = 0; i < ItemTable.size(); ++i)
		{
			cps += ItemTable[i].cps * itemCounts[i];
		}

		return cps;
	}

	void Main()
	{
		// クッキーの絵文字
		const Texture texture{ U"🍪"_emoji };

		// アイテムのデータ
		const Array<Item> ItemTable = {
			{ Texture{ U"🌾"_emoji }, U"クッキー農場", 10, 1 },
			{ Texture{ U"🏭"_emoji }, U"クッキー工場", 100, 10 },
			{ Texture{ U"⚓"_emoji }, U"クッキー港", 1000, 100 },
		};

		// 各アイテムの所有数
		Array<int32> itemCounts(ItemTable.size()); // = { 0, 0, 0 }

		// フォント
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// クッキーのクリック円
		constexpr Circle CookieCircle{ 170, 300, 100 };

		// エフェクト
		Effect effectBackground, effect;

		// クッキーのばね
		CookieSpring cookieSpring;

		// クッキーの個数
		double cookies = 0;

		// ゲームの経過時間の蓄積
		double accumulatedTime = 0.0;

		// 背景のクッキーの蓄積時間
		double cookieBackgroundAccumulatedTime = 0.0;

		// セーブデータが見つかればそれを読み込む
		{
			// バイナリファイルをオープン
			Deserializer<BinaryReader> reader{ U"game.save" };

			if (reader) // もしオープンに成功したら
			{
				SaveData saveData;

				reader(saveData);

				cookies = saveData.cookies;

				itemCounts = saveData.itemCounts;
			}
		}

		while (System::Update())
		{
			// クッキーの毎秒の生産量を計算する
			const int32 cps = CalculateCPS(ItemTable, itemCounts);

			// ゲームの経過時間を加算する
			accumulatedTime += Scene::DeltaTime();

			// 0.1 秒以上蓄積していたら
			if (0.1 <= accumulatedTime)
			{
				accumulatedTime -= 0.1;

				// 0.1 秒分のクッキー生産を加算する
				cookies += (cps * 0.1);
			}

			// 背景のクッキー
			{
				// 背景のクッキーが発生する適当な間隔を cps から計算（多くなりすぎないよう緩やかに小さくなり、下限も設ける）
				const double cookieBackgroundSpawnTime = cps ? Max(1.0 / Math::Log2(cps * 2), 0.03) : Math::Inf;

				if (cps)
				{
					cookieBackgroundAccumulatedTime += Scene::DeltaTime();
				}

				while (cookieBackgroundSpawnTime <= cookieBackgroundAccumulatedTime)
				{
					effectBackground.add<CookieBackgroundEffect>(RandomVec2(Rect{ 0, -150, 800, 100 }), texture);

					cookieBackgroundAccumulatedTime -= cookieBackgroundSpawnTime;
				}
			}

			// クッキーのばねを更新する
			cookieSpring.update(Scene::DeltaTime(), CookieCircle.leftPressed());

			// クッキー円上にマウスカーソルがあれば
			if (CookieCircle.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// クッキー円が左クリックされたら
			if (CookieCircle.leftClicked())
			{
				++cookies;

				// クッキーが舞うエフェクトを追加する
				effect.add<CookieEffect>(Cursor::Pos().movedBy(Random(-5, 5), Random(-5, 5)), texture);

				// 「+1」が上昇するエフェクトを追加する
				effect.add<PlusOneEffect>(Cursor::Pos().movedBy(Random(-5, 5), Random(-15, -5)), font);

				// 背景のクッキーを追加する
				effectBackground.add<CookieBackgroundEffect>(RandomVec2(Rect{ 0, -150, 800, 100 }), texture);
			}

			// 背景を描く
			Rect{ 0, 0, 800, 600 }.draw(Arg::top = ColorF{ 0.6, 0.5, 0.3 }, Arg::bottom = ColorF{ 0.2, 0.5, 0.3 });

			// 背景で降り注ぐクッキーを描画する
			effectBackground.update();

			// クッキーの後光を描く
			DrawHalo(CookieCircle.center);

			// クッキーの数を整数で表示する
			font(ThousandsSeparate((int32)cookies)).drawAt(60, 170, 100);

			// クッキーの生産量を表示する
			font(U"毎秒: {}"_fmt(cps)).drawAt(24, 170, 160);

			// クッキーを描画する
			texture.scaled(1.5 - cookieSpring.get()).drawAt(CookieCircle.center);

			// エフェクトを描画する
			effect.update();

			for (size_t i = 0; i < ItemTable.size(); ++i)
			{
				// アイテムの所有数
				const int32 itemCount = itemCounts[i];

				// アイテムの現在の価格
				const int32 itemCost = ItemTable[i].getCost(itemCount);

				// アイテム 1 つあたりの CPS
				const int32 itemCps = ItemTable[i].cps;

				// ボタン
				if (Button(Rect{ 340, (40 + 120 * i), 420, 100 }, ItemTable[i].emoji,
					font, ItemTable[i].name, U"C{} / {} CPS"_fmt(itemCost, itemCps), itemCount, (itemCost <= cookies)))
				{
					cookies -= itemCost;
					++itemCounts[i];
				}
			}
		}

		// メインループの後、終了時にゲームをセーブ
		{
			// バイナリファイルをオープン
			Serializer<BinaryWriter> writer{ U"game.save" };

			// シリアライズに対応したデータを書き出す
			writer(SaveData{ cookies, itemCounts });
		}
	}
	```


## 10. トランプを描く

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/10.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		Scene::SetBackground(Palette::Darkgreen);

		// カードの幅が 75 ピクセルで裏面が赤色のカードパックを作成
		const PlayingCard::Pack pack{ 75, Palette::Red };

		// ジョーカーの枚数
		constexpr int32 NumJokers = 2;

		// 52 枚 + ジョーカーを含むカードを作成する
		Array<PlayingCard::Card> cards = PlayingCard::CreateDeck(NumJokers);

		while (System::Update())
		{
			for (size_t i = 0; i < cards.size(); ++i)
			{
				const Vec2 center{ (100 + i % 13 * 90), (100 + (i / 13) * 130) };

				if (pack.regionAt(center).mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);

					if (MouseL.down())
					{
						// カードをめくる
						cards[i].flip();
					}
				}

				// カードを描画する
				pack(cards[i]).drawAt(center);
			}
		}
	}
	```


## 11. 三目並べ

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/11.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// 3 つのマークがつながったかを返す関数
	bool CheckLine(const Grid<int32>& grid, const Point& cellA, const Point& cellB, const Point& cellC)
	{
		const int32 a = grid[cellA];
		const int32 b = grid[cellB];
		const int32 c = grid[cellC];
		return ((a != 0) && (a == b) && (b == c));
	}

	// マークがつながったラインの一覧を返す関数
	Array<std::pair<Point, Point>> CheckLines(const Grid<int32>& grid)
	{
		Array<std::pair<Point, Point>> results;

		// 縦 3 列を調べる
		for (int32 x = 0; x < 3; ++x)
		{
			if (CheckLine(grid, Point{ x, 0 }, Point{ x, 1 }, Point{ x, 2 }))
			{
				results.emplace_back(Point{ x, 0 }, Point{ x, 2 });
			}
		}

		// 横 3 行を調べる
		for (int32 y = 0; y < 3; ++y)
		{
			if (CheckLine(grid, Point{ 0, y }, Point{ 1, y }, Point{ 2, y }))
			{
				results.emplace_back(Point{ 0, y }, Point{ 2, y });
			}
		}

		// 斜め（左上 -> 右下) を調べる
		if (CheckLine(grid, Point{ 0, 0 }, Point{ 1, 1 }, Point{ 2, 2 }))
		{
			results.emplace_back(Point{ 0, 0 }, Point{ 2, 2 });
		}

		// 斜め（右上 -> 左下) を調べる
		if (CheckLine(grid, Point{ 2, 0 }, Point{ 1, 1 }, Point{ 0, 2 }))
		{
			results.emplace_back(Point{ 2, 0 }, Point{ 0, 2 });
		}

		return results;
	}

	class GameBoard
	{
	public:

		// セルの大きさ
		static constexpr int32 CellSize = 150;

		// O マークの値
		static constexpr int32 O_Mark = 1;

		// X マークの値
		static constexpr int32 X_Mark = 2;

		void update()
		{
			if (m_gameOver)
			{
				return;
			}

			// 3x3 のセル
			for (auto p : step(Size{ 3, 3 }))
			{
				// セル
				const Rect cell{ (p * CellSize), CellSize };

				// セルのマーク
				const int32 mark = m_grid[p];

				// セルが空白で、なおかつクリックされたら
				if ((mark == 0) && cell.leftClicked())
				{
					// セルに現在のマークを書き込む
					m_grid[p] = m_currentMark;

					// 現在のマークを入れ替える
					m_currentMark = ((m_currentMark == O_Mark) ? X_Mark : O_Mark);

					// つながったラインを探す
					m_lines = CheckLines(m_grid);

					// 空白セルが 0 になるか、つながったラインが見つかったら
					if (m_grid.count(0) == 0 || m_lines)
					{
						// ゲーム終了
						m_gameOver = true;
					}
				}
			}
		}

		// ゲームをリセット
		void reset()
		{
			m_currentMark = O_Mark;

			m_grid.fill(0);

			m_lines.clear();

			m_gameOver = false;
		}

		// 描画
		void draw() const
		{
			drawGridLines();

			drawCells();

			drawResults();
		}

		// ゲームが終了したかを返す
		bool isGameOver() const
		{
			return m_gameOver;
		}

	private:

		// 3x3 の二次元配列 (初期値は全要素 0)
		Grid<int32> m_grid = Grid<int32>(3, 3);

		// これから置くマーク
		int32 m_currentMark = O_Mark;

		// ゲーム終了フラグ
		bool m_gameOver = false;

		// 3 つ連続したラインの一覧
		Array<std::pair<Point, Point>> m_lines;

		// 格子を描く
		void drawGridLines() const
		{
			// 線を引く
			for (auto i : { 1, 2 })
			{
				Line{ (i * CellSize), 0, (i * CellSize), (3 * CellSize) }
					.draw(4, ColorF{ 0.25 });

				Line{ 0, (i * CellSize), (3 * CellSize), (i * CellSize) }
					.draw(4, ColorF{ 0.25 });
			}
		}

		// セルを描く
		void drawCells() const
		{
			// 3x3 のセル
			for (auto p : step(Size{ 3, 3 }))
			{
				// セル
				const Rect cell{ (p * CellSize), CellSize };

				// セルのマーク
				const int32 mark = m_grid[p];

				// X マークだったら
				if (mark == X_Mark)
				{
					// X マークを描く
					Shape2D::Cross(CellSize * 0.4, 10, cell.center())
						.draw(ColorF{ 0.2 });

					// このセルはこれ以上処理しない
					continue;
				}
				else if (mark == O_Mark) // O マークだったら
				{
					// 〇 マークを描く
					Circle{ cell.center(), (CellSize * 0.4 - 10) }
					.drawFrame(10, 0, ColorF{ 0.2 });

					// このセルはこれ以上処理しない
					continue;
				}

				// セルがマウスオーバーされたら
				if (!m_gameOver && cell.mouseOver())
				{
					// カーソルを手のアイコンにする
					Cursor::RequestStyle(CursorStyle::Hand);

					// セルの上に半透明の白を描く
					cell.stretched(-2).draw(ColorF{ 1.0, 0.6 });
				}
			}
		}

		// つながったラインを描く
		void drawResults() const
		{
			for (const auto& line : m_lines)
			{
				// つながったラインの始点と終点のセルを取得
				const Rect cellBegin{ line.first * CellSize, CellSize };
				const Rect cellEnd{ line.second * CellSize, CellSize };

				// 線を引く
				Line{ cellBegin.center(), cellEnd.center() }
					.stretched(CellSize * 0.45)
					.draw(LineStyle::RoundCap, 5, ColorF{ 0.6 });
			}
		}
	};

	void Main()
	{
		// 背景色
		Scene::SetBackground(ColorF{ 0.8, 1.0, 0.9 });

		constexpr Point Offset{ 175, 30 };

		GameBoard gameBoard;

		while (System::Update())
		{
			{
				// 2D 描画とマウスカーソル座標を移動
				const Transformer2D transform{ Mat3x2::Translate(Offset), TransformCursor::Yes };

				gameBoard.update();

				gameBoard.draw();
			}

			// ゲームが終了していたら
			if (gameBoard.isGameOver())
			{
				// Reset ボタンを押せばリセット
				if (SimpleGUI::ButtonAt(U"Reset", Vec2{ 400, 520 }))
				{
					gameBoard.reset();
				}
			}
		}
	}
	```

## 12. 音ゲー基礎

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/12.png)

??? memo "コード"
	あらかじめ次のように書かれた譜面ファイル `notes.txt` を、プロジェクトの `App/` フォルダ内に配置しておきます。

	```txt title="notes.txt"
	2000 0
	2500 1
	3000 2
	3500 3
	4000 3
	4500 2
	5000 1
	5500 0
	6000 0
	6500 1
	7000 2
	7500 3
	8000 3
	8500 2
	9000 1
	9500 0
	```

	実際のゲームでは `Audio` の `.posSec()` や `.posSample()` から経過時間を計算すべきですが、このサンプルでは音声ファイルを使わずに `Stopwatch` で経過時間を決めています。

	```cpp
	# include <Siv3D.hpp>

	// ノート
	struct Note
	{
		// ノートの時刻
		int32 time;

		// 押すべきキーのインデックス (0, 1, 2, 3)
		int32 key;

		// 消えたら false
		bool active = true;
	};

	// ノート情報を譜面ファイルからロードする関数
	Array<Note> LoadNotes(const FilePath& path)
	{
		TextReader reader{ path };

		if (not reader)
		{
			throw Error{ U"譜面 {} が見つかりません。"_fmt(path) };
		}

		Array<Note> notes;

		String line;

		// 1 行ずつ読み込む
		while (reader.readLine(line))
		{
			// 空白行はスキップ
			if (line.isEmpty())
			{
				continue;
			}

			// 読み込んだ行を半角スペースで分割
			const Array<String> params = line.split(U' ');

			// 分割した結果が 2 要素でない場合は不正な譜面
			if (params.size() != 2)
			{
				throw Error{ U"不正な譜面です。" };
			}

			// 分割した結果をそれぞれ int32 型に変換
			notes.emplace_back(Parse<int32>(params[0]), Parse<int32>(params[1]));
		}

		return notes;
	}

	// ノートの座標を計算する関数
	Vec2 GetNotePos(const Note& note, int32 time)
	{
		const double x = (250 + note.key * 100);
		const double y = (500 - (note.time - time) * 0.25);
		return{ x, y };
	}

	// ノートを押したときのエフェクト
	struct NoteEffect : IEffect
	{
		Vec2 m_start;

		int32 m_score;

		Font m_font;

		NoteEffect(const Vec2& start, int32 score, const Font& font)
			: m_start{ start }
			, m_score{ score }
			, m_font{ font } {}

		bool update(double t) override
		{
			Circle{ m_start, (30 + t * 80) }.drawFrame(15 * (0.5 - t));

			if (m_score == 2)
			{
				m_font(U"Excellent").drawAt(32, m_start.movedBy(0, (-20 - t * 160)), Palette::Orange);
			}
			else if (m_score == 1)
			{
				m_font(U"Good").drawAt(32, m_start.movedBy(0, (-20 - t * 160)), Palette::Skyblue);
			}

			return (t < 0.5);
		}
	};

	void Main()
	{
		// ノート配列
		Array<Note> notes = LoadNotes(U"notes.txt");

		// 判定キー
		const Array<Input> Keys = { KeyA, KeyS, KeyD, KeyF };

		// キー入力エフェクトのトランジション
		Array<Transition> keyTransitions(Keys.size(), Transition{ 0.0s, 0.2s });

		// 時間測定用ストップウォッチ
		Stopwatch stopwatch{ StartImmediately::Yes };

		// フォント
		const Font font{ FontMethod::MSDF, 48, Typeface::Heavy };

		// エフェクト管理
		Effect effect;

		while (System::Update())
		{
			// 経過時間（ミリ秒）
			const int32 time = stopwatch.ms();

			ClearPrint();

			Print << time;

			////////////////////////////////
			//
			//	状態更新
			//
			////////////////////////////////

			for (size_t i = 0; i < Keys.size(); ++i)
			{
				keyTransitions[i].update(Keys[i].down());
			}

			for (auto& note : notes)
			{
				// 消えているノートはスキップ
				if (not note.active)
				{
					continue;
				}

				// 現在のタイムとノートのタイムとの差（ミリ秒）
				const int32 diffMillisec = (time - note.time);

				// 差の絶対値が 250 ミリ秒未満なら
				if (Abs(diffMillisec) < 250)
				{
					// ノートに対応するキーが押されていたら
					if (Keys[note.key].down())
					{
						// ノートを消す
						note.active = false;

						// ノートの座標
						const Vec2 notePos = GetNotePos(note, time);

						// エフェクトを追加する
						effect.add<NoteEffect>(Vec2{ notePos.x, 500 }, (Abs(diffMillisec) < 80 ? 2 : 1), font);
					}
				}

				// 250 ミリ秒以上の遅れはミス
				if (note.active && (250 <= diffMillisec))
				{
					// ノートを消す
					note.active = false;
				}
			}

			////////////////////////////////
			//
			//	描画
			//
			////////////////////////////////

			// 入力を描画する
			for (int32 i = 0; i < 4; ++i)
			{
				const double x = (250 + i * 100);
				RectF{ Arg::bottomCenter(x, 600), 80, 600 }
					.draw(Arg::top = ColorF{ 1.0, 0.0 }, Arg::bottom = ColorF{ 1.0, keyTransitions[i].easeOut() * 0.5 });
			}

			// 長方形を描画する
			Rect{ 0, 480, 800, 40 }.draw(ColorF{ 0.5 });

			// キー名を描画する
			for (int32 i = 0; i < 4; ++i)
			{
				const double x = (250 + i * 100);
				font(Keys[i].name()).drawAt(20, Vec2{ x, 500 }, ColorF{ 0.7 });
			}

			// ノートを描画する
			for (const auto& note : notes)
			{
				// 消えているノートはスキップ
				if (not note.active)
				{
					continue;
				}

				// ノートの座標
				const Vec2 notePos = GetNotePos(note, time);

				// 画面内にあるノートのみ描画する
				if (-100.0 < notePos.y)
				{
					Circle{ notePos, 30 }.draw();
				}
			}

			// エフェクトの描画
			effect.update();
		}
	}
	```

## 13. マインスイーパー

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/Minesweeper/Screenshot/3.png)

[Siv3D-Sample | マインスイーパー :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/tree/main/Samples/Minesweeper){:target="_blank" .md-button}


## 14. AI オセロ

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/SimpleOthelloAI/Screenshot/2.png)

[Siv3D-Sample | AI オセロ :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/tree/main/Samples/SimpleOthelloAI){:target="_blank" .md-button}


## 15. クロンダイク

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/Klondike/Screenshot/2.png)

[Siv3D-Sample | クロンダイク :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/tree/main/Samples/Klondike){:target="_blank" .md-button}


## 16. 神経衰弱

![](https://raw.githubusercontent.com/Reputeless/games/main/games/003/A.png)

[ゲーム典型 | 神経衰弱 :material-open-in-new:](https://github.com/Reputeless/games/blob/main/games/003/A.md){:target="_blank" .md-button}


## 17. ハノイの塔

![](https://raw.githubusercontent.com/Reputeless/games/main/games/004/A.png)

[ゲーム典型 | ハノイの塔 :material-open-in-new:](https://github.com/Reputeless/games/blob/main/games/004/A.md){:target="_blank" .md-button}


## 18. Wheel of Fortune (ルーレット)

![](https://raw.githubusercontent.com/Reputeless/games/main/games/006/A.png)

[ゲーム典型 | Wheel of Fortune (ルーレット) :material-open-in-new:](https://github.com/Reputeless/games/blob/main/games/006/A.md){:target="_blank" .md-button}


## 19. 2D RPG のマップと移動の基本

![](https://raw.githubusercontent.com/Reputeless/games/main/games/007/A.png)

[ゲーム典型 | 2D RPG のマップと移動の基本 :material-open-in-new:](https://github.com/Reputeless/games/blob/main/games/007/A.md){:target="_blank" .md-button}


## 20. オートタイル

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/AutoTiles/Screenshot/1.png)

[Siv3D-Sample | オートタイル :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/tree/main/Samples/AutoTiles){:target="_blank" .md-button}

## 21. すごろくの基本

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/21.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	/// @brief すごろくのマスの種類
	enum class SquareType
	{
		/// @brief スタート
		Start,

		/// @brief 通常
		Normal,

		/// @brief ゴール
		Goal,
	};

	/// @brief すごろくのマスの情報
	struct SquareInfo
	{
		/// @brief マスの位置
		Point pos;

		/// @brief マスの種類
		SquareType type;
	};

	/// @brief すごろくのマスを描画します。
	/// @param squares すごろくのマス
	/// @param font フォント
	void DrawSquares(const Array<SquareInfo>& squares, const Font& font)
	{
		// マスの間の線を描画する
		for (size_t i = 0; i < (squares.size() - 1); ++i)
		{
			Line{ squares[i].pos, squares[i + 1].pos }
				.draw(32, ColorF{ 1.0, 0.95, 0.9 });
		}

		// 各マスについて
		for (const auto& square : squares)
		{
			if (square.type == SquareType::Start)
			{
				// スタートマスを描画する
				RoundRect{ Arg::center = square.pos, 144, 144, 24 }
					.draw(ColorF{ 0.5, 0.5, 0.8 }).drawFrame(4, ColorF{ 0.3 });

				// スタートの文字を描画する
				font(U"START")
					.drawAt(36, square.pos);
			}
			else if (square.type == SquareType::Normal)
			{
				// 通常マスを描画する
				RoundRect{ Arg::center = square.pos, 100, 100, 24 }
					.draw().drawFrame(4, ColorF{ 0.3 });
			}
			else if (square.type == SquareType::Goal)
			{
				// ゴールマスを描画する
				RoundRect{ Arg::center = square.pos, 144, 144, 24 }
					.draw(ColorF{ 0.8, 0.5, 0.5 }).drawFrame(4, ColorF{ 0.3 });

				// ゴールの文字を描画する
				font(U"GOAL")
					.drawAt(36, square.pos);
			}
		}
	}

	void Main()
	{
		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// フォント
		const Font font{ FontMethod::MSDF, 30, Typeface::Bold };

		// プレイヤーの絵文字
		const Texture playerEmoji{ U"🐥"_emoji };

		// すごろくのマスの情報
		const Array<SquareInfo> squares = {
			{ {100, 500}, SquareType::Start },
			{ {300, 500}, SquareType::Normal },
			{ {500, 500}, SquareType::Normal },
			{ {700, 500}, SquareType::Normal },
			{ {700, 350}, SquareType::Normal },
			{ {500, 350}, SquareType::Normal },
			{ {300, 350}, SquareType::Normal },
			{ {100, 350}, SquareType::Normal },
			{ {100, 200}, SquareType::Normal },
			{ {300, 200}, SquareType::Normal },
			{ {500, 200}, SquareType::Normal },
			{ {700, 200}, SquareType::Goal },
		};

		// サイコロの回転タイマー
		Timer diceTimer{ 1s };

		// プレイヤーの移動タイマー
		Timer walkTimer{ 0.5s };

		// サイコロをふれるか
		bool canRollDice = true;

		// プレイヤーの位置
		size_t playerPos = 0;

		// サイコロの結果
		int32 diceResult = 0;

		// 歩数
		int32 walkCount = 0;

		while (System::Update())
		{
			// サイコロをふるボタン
			if (SimpleGUI::Button(U"サイコロをふる",
				Vec2{ 40, 40 }, 200, canRollDice))
			{
				// サイコロの回転を開始する
				diceTimer.start();

				// サイコロをふれないようにする
				canRollDice = false;
			}

			// サイコロが回転中
			if (diceTimer.isRunning())
			{
				// サイコロの目を描画する
				Circle{ 300, 60, 40 }.draw();
				font(U"0/{}"_fmt(Random(1, 6)))
					.drawAt(30, Vec2{ 300, 60 }, ColorF{ 0.11 });
			}

			// サイコロの結果を確定させる
			if (diceTimer.reachedZero())
			{
				// サイコロの結果を決定する
				diceResult = Random(1, 6);

				// サイコロの回転を停止する
				diceTimer.reset();

				// プレイヤーの移動を開始する
				walkTimer.restart();
			}

			// サイコロの結果の表示
			if (diceResult)
			{
				// サイコロの目と歩数を描画する
				Circle{ 300, 60, 40 }.draw();
				font(U"{}/{}"_fmt(walkCount, diceResult))
					.drawAt(30, Vec2{ 300, 60 }, ColorF{ 0.11 });
			}

			// プレイヤーの移動
			if ((walkCount != diceResult) && walkTimer.reachedZero())
			{
				// 歩数を進める
				++walkCount;

				// ゴールの先に進まないようにする
				playerPos = Min((playerPos + 1), (squares.size() - 1));

				// 移動タイマーを再スタートさせる
				walkTimer.restart();
			}

			// 移動が完了したら
			if ((diceResult == walkCount) && walkTimer.reachedZero())
			{
				// サイコロの結果をリセットする
				diceResult = 0;

				// 歩数をリセットする
				walkCount = 0;

				// 移動タイマーをリセットする
				walkTimer.reset();

				// サイコロをふれるようにする
				canRollDice = true;
			}

			// すごろくのマスを描する
			DrawSquares(squares, font);

			// プレイヤーの丸影を描画する
			Ellipse{ squares[playerPos].pos.movedBy(0, 30), 40, 10 }
				.draw(ColorF{ 0.0, 0.2 });

			// プレイヤーを描画する
			playerEmoji.drawAt(squares[playerPos].pos.movedBy(0, -24));
		}
	}
	```


## 22. スロットマシン

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/games/22.png)

++space++ で操作します。

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	/// @brief スロットゲームの絵柄
	struct Symbol
	{
		/// @brief 絵柄
		Texture symbol;

		/// @brief 賞金
		int32 score;
	};

	void Main()
	{
		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// フォント
		const Font font{ FontMethod::MSDF, 48,
			U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };

		// ゲーム開始の効果音
		const Audio soundStart{ Wave{ GMInstrument::Agogo,
			PianoKey::A3, 0.3s, 0.2s } };

		// リール停止の効果音
		const Audio soundStop{ Wave{ GMInstrument::SteelDrums,
			PianoKey::A3, 0.3s, 0.2s } };

		// 賞金獲得の効果音（ループ再生）
		const Audio soundGet{ Wave{ GMInstrument::TinkleBell,
			PianoKey::A6, 0.1s, 0.0s }, Loop::Yes };

		// 絵柄のリスト
		const Array<Symbol> symbols
		{
			{ Texture{ U"💎"_emoji }, 1000 },
			{ Texture{ U"7️⃣"_emoji }, 777 },
			{ Texture{ U"💰"_emoji }, 300 },
			{ Texture{ U"🃏"_emoji }, 100 },
			{ Texture{ U"🍇"_emoji }, 30 },
			{ Texture{ U"🍒"_emoji }, 10 },
		};

		// 1 つのリールに用意される絵柄の基本リスト
		const Array<int32> symbolListBase =
			{ 0, 1, 2, 3, 3, 4, 4, 4, 4, 5, 5, 5, 5, 5 };

		// 3 つのリールに用意される絵柄のリスト（基本リストをシャッフル）
		const std::array<Array<int32>, 3> symbolLists =
		{
			symbolListBase.shuffled(),
			symbolListBase.shuffled(),
			symbolListBase.shuffled()
		};

		// 3 つのリールの描画位置
		const std::array<Rect, 3> reels
		{
			Rect{ 80, 100, 130, 300 },
			Rect{ 230, 100, 130, 300 },
			Rect{ 380, 100, 130, 300 },
		};

		// 所持金の描画位置
		const RoundRect moneyRect{ 560, 440, 190, 60, 20 };

		// 3 つのリールの回転量
		std::array<double, 3> rolls = { 0.0, 0.0, 0.0 };

		// 現在のゲームにおけるリール停止カウント（3 回で結果判定）
		int32 stopCount = 3;

		// 所持金
		int32 money = 1000;

		while (System::Update())
		{
			// スペースキーが押されたら
			if (KeySpace.down())
			{
				// 3 つのリールが停止している場合
				if (stopCount == 3)
				{
					// 所持金が 3 以上ある場合
					if (3 <= money)
					{
						// 所持金を 3 減らす
						money -= 3;

						// リール停止回数を 0 に戻す
						stopCount = 0;

						// ゲーム開始の効果音を再生する
						soundStart.playOneShot();
					}
				}
				else
				{
					// リールを整数位置で停止させる
					rolls[stopCount] = Math::Ceil(rolls[stopCount]);

					// リール停止カウントを増やす
					++stopCount;

					// リール停止の効果音を再生する
					soundStop.playOneShot();

					// 3 つのリールが停止した場合
					if (stopCount == 3)
					{
						// 各リールの絵柄
						const int32 r0 = symbolLists[0][(
							static_cast<int32>(rolls[0] + 1) % symbolLists[0].size())];
						const int32 r1 = symbolLists[1][(
							static_cast<int32>(rolls[1] + 1) % symbolLists[1].size())];
						const int32 r2 = symbolLists[2][(
							static_cast<int32>(rolls[2] + 1) % symbolLists[2].size())];

						// 3 つのリールの絵柄がすべて同じ場合
						if ((r0 == r1) && (r1 == r2))
						{
							// 所持金に賞金を加算する
							money += symbols[r0].score;

							// 賞金獲得の効果音を再生する
							soundGet.play();

							// 賞金獲得の効果音を 1.5 秒後に停止する
							soundGet.stop(1.5s);
						}
					}
				}
			}

			// リールの回転
			for (int32 i = 0; i < 3; ++i)
			{
				// 停止済みのリールはスキップ
				if (i < stopCount)
				{
					continue;
				}

				// 前フレームからの経過時間に応じてリールの回転量を増やす
				rolls[i] += (Scene::DeltaTime() * 12);
			}

			// リールの描画
			for (int32 k = 0; k < 3; ++k)
			{
				// リールの背景
				reels[k].draw();

				// リールの絵柄を描画
				for (int32 i = 0; i < 4; ++i)
				{
					// リールの何番目の要素を指すか（回転量の整数部分）
					const int32 index = (static_cast<int32>(rolls[k] + i)
						% symbolLists[k].size());

					// 絵柄のインデックス
					const int32 symbolIndex = symbolLists[k][index];

					// 絵柄の位置補正（回転量の小数部分）
					const double t = Math::Fraction(rolls[k]);

					// 絵柄の描画
					symbols[symbolIndex].symbol.resized(90)
						.drawAt(reels[k].center().movedBy(0, 140 * (1 - i + t)));
				}
			}

			// リールの上下に背景色を描くことで、はみ出した絵柄を隠す
			Rect{ 80, 0, 430, 100 }.draw(Scene::GetBackground());
			Rect{ 80, 400, 430, 200 }.draw(Scene::GetBackground());

			// リールの影と枠線の描画
			for (const auto& reel : reels)
			{
				// 上の影
				Rect{ reel.tl(), reel.w, 40 }.draw(Arg::top(0.0, 0.3), Arg::bottom(0.0, 0.0));

				// 下の影
				Rect{ (reel.bl() - Point{ 0, 40 }), reel.w, 40 }.draw(Arg::top(0.0, 0.0), Arg::bottom(0.0, 0.3));

				// 枠線
				reel.drawFrame(4, ColorF{ 0.5 });
			}

			// 中央を指す 2 つの三角形の描画
			Triangle{ 60, 250, 36, 90_deg }.draw(ColorF{ 1.0, 0.2, 0.2 });
			Triangle{ 530, 250, 36, -90_deg }.draw(ColorF{ 1.0, 0.2, 0.2 });

			// 絵柄リストを描く
			RoundRect{ 560, 100, 190, 300, 20 }.draw(ColorF{ 0.9, 0.95, 1.0 });

			for (size_t i = 0; i < symbols.size(); ++i)
			{
				// 絵柄を描く
				symbols[i].symbol.resized(32).draw(Vec2{ 586, (114 + i * 48) });

				// 賞金を描く
				font(symbols[i].score).draw(TextStyle::OutlineShadow(0.2, ColorF{ 0.5, 0.3, 0.2 },
					Vec2{ 1.5, 1.5 }, ColorF{ 0.5, 0.3, 0.2 }),
					25, Arg::topRight(720, (109 + i * 48)), ColorF{ 1.0, 0.9, 0.1 });

				if (i != 0)
				{
					// 絵柄の間に区切り線を描く
					Rect{ 570, (105 + i * 48), 170, 1 }.draw(ColorF{ 0.7 });
				}
			}

			// 所持金の背景の描画
			if (soundGet.isPlaying())
			{
				// 賞金獲得中は点滅させる
				const ColorF color = Periodic::Sine0_1(0.3s) * ColorF { 0.5, 0.6, 0.7 };
				moneyRect.draw(color).drawFrame(1);
			}
			else
			{
				moneyRect.draw(ColorF{ 0.1, 0.2, 0.3 }).drawFrame(1);
			}

			// 所持金の描画
			font(money).draw(30, Arg::rightCenter(moneyRect.rightCenter().movedBy(-30, 0)));
		}
	}
	```