# オリジナルのダメージ演出を作る

| | | | |
|:--:|:--:|:--:|:--:|
| **難易度** | 中級 | **時間** | 90 分～ |

RPG やアクションゲームで、敵に攻撃がヒットした瞬間に飛び出す「ダメージ数」。ただ数字を表示するだけの演出と、動きや色彩にこだわった演出とでは、プレイヤーが得られる爽快感がまるで違います。

このコースでは、数字の動き、大きさの変化、色の移り変わりをプログラムで制御して、自分だけの「気持ちいいヒット演出」を作り上げます。「自分のゲームの見た目が地味」と感じているなら、この 90 分でその悩みを解決しましょう。アイデアと実装の工夫で、ゲーム画面に命を吹き込む楽しさを体験できます。

???info "参考事例『ゼノブレイド3』より"
	<iframe width="100%" height="315" src="https://www.youtube.com/embed/0sB_VO-0gZ4?si=ODAqjSpp5_DJNxvF&rel=0&loop=1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

???info "参考事例『崩壊:スターレイル』より"
	<iframe width="100%" height="315" src="https://www.youtube.com/embed/sEheUxvfGDM?si=jfNaDs_FSoNpgkBK" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

???info "参考事例『FINAL FANTASY XV』より"
	<iframe width="100%" height="315" src="https://www.youtube.com/embed/JIYkBDHnPQw?si=bNdazqVbpEm9DFI-" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


## 1. モックアップの戦闘シーン
- まずは、演出を試すための実験場を用意します
- 背景を描き、敵キャラクター（Siv3D マスコットキャラクター「Siv3D くん」）を配置し、クリックで攻撃判定が発生するダメージ入力パッドを実装します
- この段階では、攻撃しても画面左上にデバッグ表示が出るだけで、敵の上にはダメージが表示されません
- これをベースに、少しずつ演出を豪華にしていきます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/1.png)

??? note "コード"
	```cpp
	# include <Siv3D.hpp>

	/// @brief 攻撃の属性
	enum class AttackType
	{
		/// @brief 物理
		Physical,

		/// @brief 炎
		Fire,

		/// @brief 雷
		Thunder,

		/// @brief 氷
		Ice,
	};

	/// @brief 1 ヒット分の情報
	struct Hit
	{
		/// @brief 攻撃の属性
		AttackType type = AttackType::Physical;

		/// @brief ダメージ量
		int32 amount = 0;

		/// @brief クリティカルヒット
		bool isCritical = false;
	};

	/// @brief ダメージ入力パッド
	class DamagePad
	{
	public:

		/// @brief パッドの幅（ピクセル）
		static constexpr int32 Width = 200;

		/// @brief パッドの高さ（ピクセル）
		static constexpr int32 Height = 150;

		/// @brief コンストラクタ
		/// @param pos パッドの位置
		/// @param type パッドの攻撃属性
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief パッドの更新（クリックによるヒットの発行）・描画
		/// @return 発行されたヒット情報の配列。発行がなければ空の配列
		Array<Hit> update() const
		{
			// パッドの描画
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// パッド上のカーソル位置
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// ヒット数
			const int32 count = 1 + (cursorPos.x / 40);

			// 基本ダメージ量
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// パッド上にカーソルがある場合
			if (m_rect.mouseOver())
			{
				// カーソル非表示
				Cursor::RequestStyle(CursorStyle::Hidden);

				// パッド上のポインタ UI の描画
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// 発行されたヒット情報を格納する配列
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // 左 or 右クリックされた場合
			{
				// ヒット数分のヒット情報を生成して配列に追加
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // 右クリック時、最初のヒットのみクリティカルヒットに
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief パッドの矩形領域
		Rect m_rect{ 0 };

		/// @brief パッドの攻撃属性
		AttackType m_type = AttackType::Physical;
	};

	void Main()
	{
		// 画面サイズの設定
		Window::Resize(1280, 720);

		// 敵のテクスチャ
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// ダメージ入力パッド
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// エフェクトが発生する範囲
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// デバッグ用フラグ
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		while (System::Update())
		{
			// 背景の描画
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // 空のグラデーション
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // 地面のグラデーション
			}

			// 敵キャラクターの描画
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// 攻撃の入力
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"物理ダメージ \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"炎ダメージ \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"雷ダメージ \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"氷ダメージ \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// デバッグ用 UI
			{
				SimpleGUI::Slider(U"エフェクト速度: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"敵キャラクター", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"ターゲット範囲", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// ヒット一覧をデバッグ出力
				Print << hit.amount << (hit.isCritical ? U"(Critical)" : U"");
			}
		}
	}
	```


## 2. 攻撃とダメージ表示
- デバッグ表示をやめ、戦闘画面の中に数字を直接描画するように変更します
- `Effect` 機能を利用し、攻撃がヒットした場所にダメージ量エフェクトを発生させます
- まだ白い数字が表示されるだけの簡素な見た目ですが、「敵の上に結果が出る」という基本形が出来上がります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/2.png)

??? note "コード"
	```cpp hl_lines="105-148 172-174 215-216 219-223"
	# include <Siv3D.hpp>

	/// @brief 攻撃の属性
	enum class AttackType
	{
		/// @brief 物理
		Physical,

		/// @brief 炎
		Fire,

		/// @brief 雷
		Thunder,

		/// @brief 氷
		Ice,
	};

	/// @brief 1 ヒット分の情報
	struct Hit
	{
		/// @brief 攻撃の属性
		AttackType type = AttackType::Physical;

		/// @brief ダメージ量
		int32 amount = 0;

		/// @brief クリティカルヒット
		bool isCritical = false;
	};

	/// @brief ダメージ入力パッド
	class DamagePad
	{
	public:

		/// @brief パッドの幅（ピクセル）
		static constexpr int32 Width = 200;

		/// @brief パッドの高さ（ピクセル）
		static constexpr int32 Height = 150;

		/// @brief コンストラクタ
		/// @param pos パッドの位置
		/// @param type パッドの攻撃属性
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief パッドの更新（クリックによるヒットの発行）・描画
		/// @return 発行されたヒット情報の配列。発行がなければ空の配列
		Array<Hit> update() const
		{
			// パッドの描画
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// パッド上のカーソル位置
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// ヒット数
			const int32 count = 1 + (cursorPos.x / 40);

			// 基本ダメージ量
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// パッド上にカーソルがある場合
			if (m_rect.mouseOver())
			{
				// カーソル非表示
				Cursor::RequestStyle(CursorStyle::Hidden);

				// パッド上のポインタ UI の描画
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// 発行されたヒット情報を格納する配列
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // 左 or 右クリックされた場合
			{
				// ヒット数分のヒット情報を生成して配列に追加
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // 右クリック時、最初のヒットのみクリティカルヒットに
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief パッドの矩形領域
		Rect m_rect{ 0 };

		/// @brief パッドの攻撃属性
		AttackType m_type = AttackType::Physical;
	};

	/// @brief ダメージ表示エフェクト
	struct DamageNumber : IEffect
	{
		/// @brief エフェクトの寿命（秒）
		static constexpr double Lifetime = 0.8;

		/// @brief ヒット情報
		Hit m_hit;

		/// @brief エフェクト発生位置
		Vec2 m_start;

		/// @brief フォント
		Font m_font;

		/// @brief コンストラクタ
		/// @param hit ヒット情報
		/// @param start エフェクト発生位置
		/// @param font フォント
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief エフェクトの更新・描画
		/// @param t 発生からの経過時間（秒）
		/// @return エフェクト継続なら true, 終了なら false
		bool update(const double t) override
		{
			// 基本色
			ColorF baseColor{ 1.0, 1.0, 1.0 };

			// 文字のサイズ
			double baseSize = 40.0;

			// 描画位置
			Vec2 basePos = m_start;

			// 数字を描く
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// 画面サイズの設定
		Window::Resize(1280, 720);

		// 敵のテクスチャ
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// ダメージ入力パッド
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// エフェクトが発生する範囲
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// デバッグ用フラグ
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// エフェクト用フォント
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// 背景の描画
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // 空のグラデーション
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // 地面のグラデーション
			}

			// 敵キャラクターの描画
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// 攻撃の入力
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"物理ダメージ \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"炎ダメージ \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"雷ダメージ \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"氷ダメージ \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// デバッグ用 UI
			{
				SimpleGUI::Slider(U"エフェクト速度: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"敵キャラクター", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"ターゲット範囲", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// ターゲット範囲内のランダムな位置にエフェクトを発生させる
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// エフェクトの更新と描画
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 3. 属性に応じて色を変える
- 「炎属性なら赤」「氷属性なら青」といったように、攻撃の種類によって数字の色を変化させます
- 色による情報の整理は、プレイヤーが「今の攻撃は何属性だったか」を直感的に理解するために重要です。単なる装飾ではなく、ゲームの状況を伝えるための機能的なデザインを追加します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/3.png)

??? note "コード"
	```cpp hl_lines="111-118 143-144"
	# include <Siv3D.hpp>

	/// @brief 攻撃の属性
	enum class AttackType
	{
		/// @brief 物理
		Physical,

		/// @brief 炎
		Fire,

		/// @brief 雷
		Thunder,

		/// @brief 氷
		Ice,
	};

	/// @brief 1 ヒット分の情報
	struct Hit
	{
		/// @brief 攻撃の属性
		AttackType type = AttackType::Physical;

		/// @brief ダメージ量
		int32 amount = 0;

		/// @brief クリティカルヒット
		bool isCritical = false;
	};

	/// @brief ダメージ入力パッド
	class DamagePad
	{
	public:

		/// @brief パッドの幅（ピクセル）
		static constexpr int32 Width = 200;

		/// @brief パッドの高さ（ピクセル）
		static constexpr int32 Height = 150;

		/// @brief コンストラクタ
		/// @param pos パッドの位置
		/// @param type パッドの攻撃属性
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief パッドの更新（クリックによるヒットの発行）・描画
		/// @return 発行されたヒット情報の配列。発行がなければ空の配列
		Array<Hit> update() const
		{
			// パッドの描画
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// パッド上のカーソル位置
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// ヒット数
			const int32 count = 1 + (cursorPos.x / 40);

			// 基本ダメージ量
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// パッド上にカーソルがある場合
			if (m_rect.mouseOver())
			{
				// カーソル非表示
				Cursor::RequestStyle(CursorStyle::Hidden);

				// パッド上のポインタ UI の描画
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// 発行されたヒット情報を格納する配列
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // 左 or 右クリックされた場合
			{
				// ヒット数分のヒット情報を生成して配列に追加
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // 右クリック時、最初のヒットのみクリティカルヒットに
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief パッドの矩形領域
		Rect m_rect{ 0 };

		/// @brief パッドの攻撃属性
		AttackType m_type = AttackType::Physical;
	};

	/// @brief ダメージ表示エフェクト
	struct DamageNumber : IEffect
	{
		/// @brief エフェクトの寿命（秒）
		static constexpr double Lifetime = 0.8;

		/// @brief 属性ごとの基本色
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief ヒット情報
		Hit m_hit;

		/// @brief エフェクト発生位置
		Vec2 m_start;

		/// @brief フォント
		Font m_font;

		/// @brief コンストラクタ
		/// @param hit ヒット情報
		/// @param start エフェクト発生位置
		/// @param font フォント
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief エフェクトの更新・描画
		/// @param t 発生からの経過時間（秒）
		/// @return エフェクト継続なら true, 終了なら false
		bool update(const double t) override
		{
			// 属性に応じた基本色
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];

			// 文字のサイズ
			double baseSize = 40.0;

			// 描画位置
			Vec2 basePos = m_start;

			// 数字を描く
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// 画面サイズの設定
		Window::Resize(1280, 720);

		// 敵のテクスチャ
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// ダメージ入力パッド
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// エフェクトが発生する範囲
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// デバッグ用フラグ
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// エフェクト用フォント
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// 背景の描画
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // 空のグラデーション
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // 地面のグラデーション
			}

			// 敵キャラクターの描画
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// 攻撃の入力
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"物理ダメージ \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"炎ダメージ \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"雷ダメージ \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"氷ダメージ \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// デバッグ用 UI
			{
				SimpleGUI::Slider(U"エフェクト速度: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"敵キャラクター", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"ターゲット範囲", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// ターゲット範囲内のランダムな位置にエフェクトを発生させる
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// エフェクトの更新と描画
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 4. ダメージ量に応じて大きさを変える
- 「弱攻撃」と「必殺技」が同じ見た目では、爽快感が半減してしまいます
- ダメージの数値の桁数（100 未満、1000 未満、それ以上）に応じて、数字のフォントサイズを大きくします
- 「大きい数字＝凄いことが起きた」という視覚的なインパクトを与えることで、大ダメージを与えた時の手応えを強化します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/4.png)

??? note "コード"
	```cpp hl_lines="146-147"
	# include <Siv3D.hpp>

	/// @brief 攻撃の属性
	enum class AttackType
	{
		/// @brief 物理
		Physical,

		/// @brief 炎
		Fire,

		/// @brief 雷
		Thunder,

		/// @brief 氷
		Ice,
	};

	/// @brief 1 ヒット分の情報
	struct Hit
	{
		/// @brief 攻撃の属性
		AttackType type = AttackType::Physical;

		/// @brief ダメージ量
		int32 amount = 0;

		/// @brief クリティカルヒット
		bool isCritical = false;
	};

	/// @brief ダメージ入力パッド
	class DamagePad
	{
	public:

		/// @brief パッドの幅（ピクセル）
		static constexpr int32 Width = 200;

		/// @brief パッドの高さ（ピクセル）
		static constexpr int32 Height = 150;

		/// @brief コンストラクタ
		/// @param pos パッドの位置
		/// @param type パッドの攻撃属性
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief パッドの更新（クリックによるヒットの発行）・描画
		/// @return 発行されたヒット情報の配列。発行がなければ空の配列
		Array<Hit> update() const
		{
			// パッドの描画
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// パッド上のカーソル位置
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// ヒット数
			const int32 count = 1 + (cursorPos.x / 40);

			// 基本ダメージ量
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// パッド上にカーソルがある場合
			if (m_rect.mouseOver())
			{
				// カーソル非表示
				Cursor::RequestStyle(CursorStyle::Hidden);

				// パッド上のポインタ UI の描画
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// 発行されたヒット情報を格納する配列
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // 左 or 右クリックされた場合
			{
				// ヒット数分のヒット情報を生成して配列に追加
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // 右クリック時、最初のヒットのみクリティカルヒットに
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief パッドの矩形領域
		Rect m_rect{ 0 };

		/// @brief パッドの攻撃属性
		AttackType m_type = AttackType::Physical;
	};

	/// @brief ダメージ表示エフェクト
	struct DamageNumber : IEffect
	{
		/// @brief エフェクトの寿命（秒）
		static constexpr double Lifetime = 0.8;

		/// @brief 属性ごとの基本色
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief ヒット情報
		Hit m_hit;

		/// @brief エフェクト発生位置
		Vec2 m_start;

		/// @brief フォント
		Font m_font;

		/// @brief コンストラクタ
		/// @param hit ヒット情報
		/// @param start エフェクト発生位置
		/// @param font フォント
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief エフェクトの更新・描画
		/// @param t 発生からの経過時間（秒）
		/// @return エフェクト継続なら true, 終了なら false
		bool update(const double t) override
		{
			// 属性に応じた基本色
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];

			// ダメージ量に応じた基本サイズ
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;

			// 描画位置
			Vec2 basePos = m_start;

			// 数字を描く
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// 画面サイズの設定
		Window::Resize(1280, 720);

		// 敵のテクスチャ
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// ダメージ入力パッド
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// エフェクトが発生する範囲
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// デバッグ用フラグ
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// エフェクト用フォント
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// 背景の描画
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // 空のグラデーション
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // 地面のグラデーション
			}

			// 敵キャラクターの描画
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// 攻撃の入力
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"物理ダメージ \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"炎ダメージ \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"雷ダメージ \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"氷ダメージ \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// デバッグ用 UI
			{
				SimpleGUI::Slider(U"エフェクト速度: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"敵キャラクター", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"ターゲット範囲", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// ターゲット範囲内のランダムな位置にエフェクトを発生させる
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// エフェクトの更新と描画
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 5. 文字に枠を付ける
- 背景が明るい場所や、エフェクトが重なった場所では、文字が背景に溶け込んで見えにくくなることがあります
- 数字の周囲に枠線を描画することで、どんな背景の上でもくっきりと数字が読めるようにします。視認性が上がるだけでなく、文字としての存在感が増し、よりゲームらしい画面になります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/5.png)

??? note "コード"
	```cpp hl_lines="155-156"
	# include <Siv3D.hpp>

	/// @brief 攻撃の属性
	enum class AttackType
	{
		/// @brief 物理
		Physical,

		/// @brief 炎
		Fire,

		/// @brief 雷
		Thunder,

		/// @brief 氷
		Ice,
	};

	/// @brief 1 ヒット分の情報
	struct Hit
	{
		/// @brief 攻撃の属性
		AttackType type = AttackType::Physical;

		/// @brief ダメージ量
		int32 amount = 0;

		/// @brief クリティカルヒット
		bool isCritical = false;
	};

	/// @brief ダメージ入力パッド
	class DamagePad
	{
	public:

		/// @brief パッドの幅（ピクセル）
		static constexpr int32 Width = 200;

		/// @brief パッドの高さ（ピクセル）
		static constexpr int32 Height = 150;

		/// @brief コンストラクタ
		/// @param pos パッドの位置
		/// @param type パッドの攻撃属性
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief パッドの更新（クリックによるヒットの発行）・描画
		/// @return 発行されたヒット情報の配列。発行がなければ空の配列
		Array<Hit> update() const
		{
			// パッドの描画
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// パッド上のカーソル位置
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// ヒット数
			const int32 count = 1 + (cursorPos.x / 40);

			// 基本ダメージ量
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// パッド上にカーソルがある場合
			if (m_rect.mouseOver())
			{
				// カーソル非表示
				Cursor::RequestStyle(CursorStyle::Hidden);

				// パッド上のポインタ UI の描画
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// 発行されたヒット情報を格納する配列
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // 左 or 右クリックされた場合
			{
				// ヒット数分のヒット情報を生成して配列に追加
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // 右クリック時、最初のヒットのみクリティカルヒットに
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief パッドの矩形領域
		Rect m_rect{ 0 };

		/// @brief パッドの攻撃属性
		AttackType m_type = AttackType::Physical;
	};

	/// @brief ダメージ表示エフェクト
	struct DamageNumber : IEffect
	{
		/// @brief エフェクトの寿命（秒）
		static constexpr double Lifetime = 0.8;

		/// @brief 属性ごとの基本色
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief ヒット情報
		Hit m_hit;

		/// @brief エフェクト発生位置
		Vec2 m_start;

		/// @brief フォント
		Font m_font;

		/// @brief コンストラクタ
		/// @param hit ヒット情報
		/// @param start エフェクト発生位置
		/// @param font フォント
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief エフェクトの更新・描画
		/// @param t 発生からの経過時間（秒）
		/// @return エフェクト継続なら true, 終了なら false
		bool update(const double t) override
		{
			// 属性に応じた基本色
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];

			// ダメージ量に応じた基本サイズ
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;

			// 描画位置
			Vec2 basePos = m_start;

			// 数字を描く
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			// 数字の枠を描く
			m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// 画面サイズの設定
		Window::Resize(1280, 720);

		// 敵のテクスチャ
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// ダメージ入力パッド
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// エフェクトが発生する範囲
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// デバッグ用フラグ
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// エフェクト用フォント
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// 背景の描画
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // 空のグラデーション
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // 地面のグラデーション
			}

			// 敵キャラクターの描画
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// 攻撃の入力
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"物理ダメージ \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"炎ダメージ \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"雷ダメージ \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"氷ダメージ \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// デバッグ用 UI
			{
				SimpleGUI::Slider(U"エフェクト速度: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"敵キャラクター", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"ターゲット範囲", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// ターゲット範囲内のランダムな位置にエフェクトを発生させる
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// エフェクトの更新と描画
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 6. 文字をイタリックにする
- お好みで、フォントを斜体（イタリック）に変更します
- 斜めの文字はスピード感や勢いを感じさせます。アクション性の高いゲームや、スピーディーな戦闘シーンにおいて、画面に動的な印象を与えるための工夫です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/6.png)

??? note "コード"
	```cpp hl_lines="185"
	# include <Siv3D.hpp>

	/// @brief 攻撃の属性
	enum class AttackType
	{
		/// @brief 物理
		Physical,

		/// @brief 炎
		Fire,

		/// @brief 雷
		Thunder,

		/// @brief 氷
		Ice,
	};

	/// @brief 1 ヒット分の情報
	struct Hit
	{
		/// @brief 攻撃の属性
		AttackType type = AttackType::Physical;

		/// @brief ダメージ量
		int32 amount = 0;

		/// @brief クリティカルヒット
		bool isCritical = false;
	};

	/// @brief ダメージ入力パッド
	class DamagePad
	{
	public:

		/// @brief パッドの幅（ピクセル）
		static constexpr int32 Width = 200;

		/// @brief パッドの高さ（ピクセル）
		static constexpr int32 Height = 150;

		/// @brief コンストラクタ
		/// @param pos パッドの位置
		/// @param type パッドの攻撃属性
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief パッドの更新（クリックによるヒットの発行）・描画
		/// @return 発行されたヒット情報の配列。発行がなければ空の配列
		Array<Hit> update() const
		{
			// パッドの描画
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// パッド上のカーソル位置
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// ヒット数
			const int32 count = 1 + (cursorPos.x / 40);

			// 基本ダメージ量
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// パッド上にカーソルがある場合
			if (m_rect.mouseOver())
			{
				// カーソル非表示
				Cursor::RequestStyle(CursorStyle::Hidden);

				// パッド上のポインタ UI の描画
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// 発行されたヒット情報を格納する配列
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // 左 or 右クリックされた場合
			{
				// ヒット数分のヒット情報を生成して配列に追加
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // 右クリック時、最初のヒットのみクリティカルヒットに
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief パッドの矩形領域
		Rect m_rect{ 0 };

		/// @brief パッドの攻撃属性
		AttackType m_type = AttackType::Physical;
	};

	/// @brief ダメージ表示エフェクト
	struct DamageNumber : IEffect
	{
		/// @brief エフェクトの寿命（秒）
		static constexpr double Lifetime = 0.8;

		/// @brief 属性ごとの基本色
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief ヒット情報
		Hit m_hit;

		/// @brief エフェクト発生位置
		Vec2 m_start;

		/// @brief フォント
		Font m_font;

		/// @brief コンストラクタ
		/// @param hit ヒット情報
		/// @param start エフェクト発生位置
		/// @param font フォント
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief エフェクトの更新・描画
		/// @param t 発生からの経過時間（秒）
		/// @return エフェクト継続なら true, 終了なら false
		bool update(const double t) override
		{
			// 属性に応じた基本色
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];

			// ダメージ量に応じた基本サイズ
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;

			// 描画位置
			Vec2 basePos = m_start;

			// 数字を描く
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			// 数字の枠を描く
			m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// 画面サイズの設定
		Window::Resize(1280, 720);

		// 敵のテクスチャ
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// ダメージ入力パッド
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// エフェクトが発生する範囲
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// デバッグ用フラグ
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// エフェクト用フォント
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// 背景の描画
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // 空のグラデーション
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // 地面のグラデーション
			}

			// 敵キャラクターの描画
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// 攻撃の入力
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"物理ダメージ \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"炎ダメージ \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"雷ダメージ \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"氷ダメージ \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// デバッグ用 UI
			{
				SimpleGUI::Slider(U"エフェクト速度: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"敵キャラクター", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"ターゲット範囲", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// ターゲット範囲内のランダムな位置にエフェクトを発生させる
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// エフェクトの更新と描画
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 7. フェードアウトのエフェクト
- 数字がパッと消えるのではなく、ふわっと消えるようにアニメーションを付けます
- 出現してから少し時間が経ったら、徐々に透明度を上げつつ、上方向へゆっくり移動させます
- 余韻を残しながら消滅させることで、唐突さがなくなり、自然な見た目になります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/7.png)

??? note "コード"
	```cpp hl_lines="145-149 156-160"
	# include <Siv3D.hpp>

	/// @brief 攻撃の属性
	enum class AttackType
	{
		/// @brief 物理
		Physical,

		/// @brief 炎
		Fire,

		/// @brief 雷
		Thunder,

		/// @brief 氷
		Ice,
	};

	/// @brief 1 ヒット分の情報
	struct Hit
	{
		/// @brief 攻撃の属性
		AttackType type = AttackType::Physical;

		/// @brief ダメージ量
		int32 amount = 0;

		/// @brief クリティカルヒット
		bool isCritical = false;
	};

	/// @brief ダメージ入力パッド
	class DamagePad
	{
	public:

		/// @brief パッドの幅（ピクセル）
		static constexpr int32 Width = 200;

		/// @brief パッドの高さ（ピクセル）
		static constexpr int32 Height = 150;

		/// @brief コンストラクタ
		/// @param pos パッドの位置
		/// @param type パッドの攻撃属性
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief パッドの更新（クリックによるヒットの発行）・描画
		/// @return 発行されたヒット情報の配列。発行がなければ空の配列
		Array<Hit> update() const
		{
			// パッドの描画
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// パッド上のカーソル位置
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// ヒット数
			const int32 count = 1 + (cursorPos.x / 40);

			// 基本ダメージ量
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// パッド上にカーソルがある場合
			if (m_rect.mouseOver())
			{
				// カーソル非表示
				Cursor::RequestStyle(CursorStyle::Hidden);

				// パッド上のポインタ UI の描画
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// 発行されたヒット情報を格納する配列
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // 左 or 右クリックされた場合
			{
				// ヒット数分のヒット情報を生成して配列に追加
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // 右クリック時、最初のヒットのみクリティカルヒットに
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief パッドの矩形領域
		Rect m_rect{ 0 };

		/// @brief パッドの攻撃属性
		AttackType m_type = AttackType::Physical;
	};

	/// @brief ダメージ表示エフェクト
	struct DamageNumber : IEffect
	{
		/// @brief エフェクトの寿命（秒）
		static constexpr double Lifetime = 0.8;

		/// @brief 属性ごとの基本色
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief ヒット情報
		Hit m_hit;

		/// @brief エフェクト発生位置
		Vec2 m_start;

		/// @brief フォント
		Font m_font;

		/// @brief コンストラクタ
		/// @param hit ヒット情報
		/// @param start エフェクト発生位置
		/// @param font フォント
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief エフェクトの更新・描画
		/// @param t 発生からの経過時間（秒）
		/// @return エフェクト継続なら true, 終了なら false
		bool update(const double t) override
		{
			// 属性に応じた基本色
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];
			// 終盤のフェードアウト
			{
				const double t2 = Max((t - (Lifetime - 0.2)), 0.0) / 0.2; // 0.2 秒から LifeTime にかけて 0.0 → 1.0 に変化
				baseColor.a *= (1.0 - t2 * t2);
			}

			// ダメージ量に応じた基本サイズ
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;

			// 描画位置
			Vec2 basePos = m_start;
			// 途中からの上昇
			{
				const double t2 = Max((t - 0.2), 0.0); // 0.2 秒からの経過時間
				basePos.y -= (120.0 * t2 * t2);
			}

			// 数字を描く
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			// 数字の枠を描く
			m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// 画面サイズの設定
		Window::Resize(1280, 720);

		// 敵のテクスチャ
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// ダメージ入力パッド
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// エフェクトが発生する範囲
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// デバッグ用フラグ
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// エフェクト用フォント
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// 背景の描画
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // 空のグラデーション
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // 地面のグラデーション
			}

			// 敵キャラクターの描画
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// 攻撃の入力
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"物理ダメージ \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"炎ダメージ \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"雷ダメージ \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"氷ダメージ \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// デバッグ用 UI
			{
				SimpleGUI::Slider(U"エフェクト速度: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"敵キャラクター", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"ターゲット範囲", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// ターゲット範囲内のランダムな位置にエフェクトを発生させる
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// エフェクトの更新と描画
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```


## 8. 出現時のエフェクト
- 数字が出た瞬間のインパクトを強化します
- 出現直後はサイズを少し大きく、色を飽和（発光）させ、直ちに本来のサイズと色に戻る動きを加えます
- これにより、ヒットした瞬間の打撃感が生まれ、攻撃コマンドを押した時の手応えが向上します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/8.png)

??? note "コード"
	```cpp hl_lines="145-150 159-165"
	# include <Siv3D.hpp>

	/// @brief 攻撃の属性
	enum class AttackType
	{
		/// @brief 物理
		Physical,

		/// @brief 炎
		Fire,

		/// @brief 雷
		Thunder,

		/// @brief 氷
		Ice,
	};

	/// @brief 1 ヒット分の情報
	struct Hit
	{
		/// @brief 攻撃の属性
		AttackType type = AttackType::Physical;

		/// @brief ダメージ量
		int32 amount = 0;

		/// @brief クリティカルヒット
		bool isCritical = false;
	};

	/// @brief ダメージ入力パッド
	class DamagePad
	{
	public:

		/// @brief パッドの幅（ピクセル）
		static constexpr int32 Width = 200;

		/// @brief パッドの高さ（ピクセル）
		static constexpr int32 Height = 150;

		/// @brief コンストラクタ
		/// @param pos パッドの位置
		/// @param type パッドの攻撃属性
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief パッドの更新（クリックによるヒットの発行）・描画
		/// @return 発行されたヒット情報の配列。発行がなければ空の配列
		Array<Hit> update() const
		{
			// パッドの描画
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// パッド上のカーソル位置
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// ヒット数
			const int32 count = 1 + (cursorPos.x / 40);

			// 基本ダメージ量
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// パッド上にカーソルがある場合
			if (m_rect.mouseOver())
			{
				// カーソル非表示
				Cursor::RequestStyle(CursorStyle::Hidden);

				// パッド上のポインタ UI の描画
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// 発行されたヒット情報を格納する配列
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // 左 or 右クリックされた場合
			{
				// ヒット数分のヒット情報を生成して配列に追加
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // 右クリック時、最初のヒットのみクリティカルヒットに
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief パッドの矩形領域
		Rect m_rect{ 0 };

		/// @brief パッドの攻撃属性
		AttackType m_type = AttackType::Physical;
	};

	/// @brief ダメージ表示エフェクト
	struct DamageNumber : IEffect
	{
		/// @brief エフェクトの寿命（秒）
		static constexpr double Lifetime = 0.8;

		/// @brief 属性ごとの基本色
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief ヒット情報
		Hit m_hit;

		/// @brief エフェクト発生位置
		Vec2 m_start;

		/// @brief フォント
		Font m_font;

		/// @brief コンストラクタ
		/// @param hit ヒット情報
		/// @param start エフェクト発生位置
		/// @param font フォント
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief エフェクトの更新・描画
		/// @param t 発生からの経過時間（秒）
		/// @return エフェクト継続なら true, 終了なら false
		bool update(const double t) override
		{
			// 属性に応じた基本色
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];
			// 出現時の色の飽和
			{
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // 0.0 秒から 0.1 秒にかけて 1.0 → 0.0 に変化
				const double add = (0.5 * t2 * t2);
				baseColor += ColorF{ add, add, add }; // 出現直後に明るく、徐々に基本色に収束
			}
			// 終盤のフェードアウト
			{
				const double t2 = Max((t - (Lifetime - 0.2)), 0.0) / 0.2; // 0.2 秒から LifeTime にかけて 0.0 → 1.0 に変化
				baseColor.a *= (1.0 - t2 * t2);
			}

			// ダメージ量に応じた基本サイズ
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;
			// 出現時のサイズ変化
			{
				// ダメージ桁数に応じた追加サイズ
				const double extraSize = (m_hit.amount < 100) ? 20.0 : (m_hit.amount < 1000) ? 40.0 : 80.0;
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // 0.0 秒から 0.1 秒にかけて 1.0 → 0.0 に変化
				baseSize += (extraSize * t2 * t2); // 出現直後に大きく、徐々に基本サイズに収束
			}

			// 描画位置
			Vec2 basePos = m_start;
			// 途中からの上昇
			{
				const double t2 = Max((t - 0.2), 0.0); // 0.2 秒からの経過時間
				basePos.y -= (120.0 * t2 * t2);
			}

			// 数字を描く
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			// 数字の枠を描く
			m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// 画面サイズの設定
		Window::Resize(1280, 720);

		// 敵のテクスチャ
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// ダメージ入力パッド
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// エフェクトが発生する範囲
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// デバッグ用フラグ
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// エフェクト用フォント
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// 背景の描画
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // 空のグラデーション
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // 地面のグラデーション
			}

			// 敵キャラクターの描画
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// 攻撃の入力
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"物理ダメージ \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"炎ダメージ \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"雷ダメージ \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"氷ダメージ \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// デバッグ用 UI
			{
				SimpleGUI::Slider(U"エフェクト速度: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"敵キャラクター", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"ターゲット範囲", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// ターゲット範囲内のランダムな位置にエフェクトを発生させる
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// エフェクトの更新と描画
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 9. 同時ダメージを時間差で表示する
- 一度に大量のヒットが発生した際、数字が重なって潰れてしまうのを防ぎます
- 発生したダメージ情報を一旦キューに溜め込み、一定の間隔（0.04 秒ごとなど）を守って順番に表示させます
- 連続ヒットのリズムが可視化され、連撃の爽快感を演出できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/9.png)

??? note "コード"
	```cpp hl_lines="211-216 255 258-261 264-282"
	# include <Siv3D.hpp>

	/// @brief 攻撃の属性
	enum class AttackType
	{
		/// @brief 物理
		Physical,

		/// @brief 炎
		Fire,

		/// @brief 雷
		Thunder,

		/// @brief 氷
		Ice,
	};

	/// @brief 1 ヒット分の情報
	struct Hit
	{
		/// @brief 攻撃の属性
		AttackType type = AttackType::Physical;

		/// @brief ダメージ量
		int32 amount = 0;

		/// @brief クリティカルヒット
		bool isCritical = false;
	};

	/// @brief ダメージ入力パッド
	class DamagePad
	{
	public:

		/// @brief パッドの幅（ピクセル）
		static constexpr int32 Width = 200;

		/// @brief パッドの高さ（ピクセル）
		static constexpr int32 Height = 150;

		/// @brief コンストラクタ
		/// @param pos パッドの位置
		/// @param type パッドの攻撃属性
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief パッドの更新（クリックによるヒットの発行）・描画
		/// @return 発行されたヒット情報の配列。発行がなければ空の配列
		Array<Hit> update() const
		{
			// パッドの描画
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// パッド上のカーソル位置
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// ヒット数
			const int32 count = 1 + (cursorPos.x / 40);

			// 基本ダメージ量
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// パッド上にカーソルがある場合
			if (m_rect.mouseOver())
			{
				// カーソル非表示
				Cursor::RequestStyle(CursorStyle::Hidden);

				// パッド上のポインタ UI の描画
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// 発行されたヒット情報を格納する配列
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // 左 or 右クリックされた場合
			{
				// ヒット数分のヒット情報を生成して配列に追加
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // 右クリック時、最初のヒットのみクリティカルヒットに
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief パッドの矩形領域
		Rect m_rect{ 0 };

		/// @brief パッドの攻撃属性
		AttackType m_type = AttackType::Physical;
	};

	/// @brief ダメージ表示エフェクト
	struct DamageNumber : IEffect
	{
		/// @brief エフェクトの寿命（秒）
		static constexpr double Lifetime = 0.8;

		/// @brief 属性ごとの基本色
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief ヒット情報
		Hit m_hit;

		/// @brief エフェクト発生位置
		Vec2 m_start;

		/// @brief フォント
		Font m_font;

		/// @brief コンストラクタ
		/// @param hit ヒット情報
		/// @param start エフェクト発生位置
		/// @param font フォント
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief エフェクトの更新・描画
		/// @param t 発生からの経過時間（秒）
		/// @return エフェクト継続なら true, 終了なら false
		bool update(const double t) override
		{
			// 属性に応じた基本色
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];
			// 出現時の色の飽和
			{
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // 0.0 秒から 0.1 秒にかけて 1.0 → 0.0 に変化
				const double add = (0.5 * t2 * t2);
				baseColor += ColorF{ add, add, add }; // 出現直後に明るく、徐々に基本色に収束
			}
			// 終盤のフェードアウト
			{
				const double t2 = Max((t - (Lifetime - 0.2)), 0.0) / 0.2; // 0.2 秒から LifeTime にかけて 0.0 → 1.0 に変化
				baseColor.a *= (1.0 - t2 * t2);
			}

			// ダメージ量に応じた基本サイズ
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;
			// 出現時のサイズ変化
			{
				// ダメージ桁数に応じた追加サイズ
				const double extraSize = (m_hit.amount < 100) ? 20.0 : (m_hit.amount < 1000) ? 40.0 : 80.0;
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // 0.0 秒から 0.1 秒にかけて 1.0 → 0.0 に変化
				baseSize += (extraSize * t2 * t2); // 出現直後に大きく、徐々に基本サイズに収束
			}

			// 描画位置
			Vec2 basePos = m_start;
			// 途中からの上昇
			{
				const double t2 = Max((t - 0.2), 0.0); // 0.2 秒からの経過時間
				basePos.y -= (120.0 * t2 * t2);
			}

			// 数字を描く
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			// 数字の枠を描く
			m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// 画面サイズの設定
		Window::Resize(1280, 720);

		// 敵のテクスチャ
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// ダメージ入力パッド
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// エフェクトが発生する範囲
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// デバッグ用フラグ
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// エフェクト用フォント
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(6);
		Effect effectManager;

		// ヒット情報キュー
		std::queue<Hit> hitQueue;
		// 最小ヒットエフェクト発行間隔（秒）
		constexpr double hitInterval = 0.04;
		// 最後にヒットエフェクトを発行してからの経過時間（秒）
		double accumulatedTime = 0.0;

		while (System::Update())
		{
			// 背景の描画
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // 空のグラデーション
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // 地面のグラデーション
			}

			// 敵キャラクターの描画
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// 攻撃の入力
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"物理ダメージ \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"炎ダメージ \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"雷ダメージ \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"氷ダメージ \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// デバッグ用 UI
			{
				SimpleGUI::Slider(U"エフェクト速度: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"敵キャラクター", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"ターゲット範囲", Vec2{ 260, 660 }, 200);
			}

			// 発行されたヒット情報をキューに追加
			for (const Hit& hit : hits)
			{
				hitQueue.push(hit);
				accumulatedTime = hitInterval; // 新しいヒットに対して、すぐエフェクトを発行するよう、蓄積時間に下駄を履かせる
				// ターゲット範囲内のランダムな位置にエフェクトを発生させる
				//effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// キューの消化とエフェクト発行
			{
				// 蓄積時間の加算
				accumulatedTime += (Scene::DeltaTime() * effectSpeed);

				// 一定間隔ごとに、キューの先頭のヒット情報を取り出して、エフェクトを発行
				if (hitInterval <= accumulatedTime)
				{
					if (not hitQueue.empty())
					{
						// キューの先頭のヒット情報を取り出す
						const Hit hit = hitQueue.front(); hitQueue.pop();
						// ターゲット範囲内のランダムな位置にエフェクトを発生させる
						effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
						// 蓄積時間を減らす
						accumulatedTime -= hitInterval;
					}
				}
			}

			// エフェクトの更新と描画
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 10. グラデーションを付ける
- 単色の塗りつぶしをやめ、文字の上部を明るく、下部を濃くするグラデーションを適用します
- 文字描画の処理をカスタマイズし、1 文字ずつ丁寧に色を塗ることで、プロのゲームのようなリッチな質感を出します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/10.png)

??? note "コード"
	```cpp hl_lines="175-176 178-179 182-184 187-189 194-216"
	# include <Siv3D.hpp>

	/// @brief 攻撃の属性
	enum class AttackType
	{
		/// @brief 物理
		Physical,

		/// @brief 炎
		Fire,

		/// @brief 雷
		Thunder,

		/// @brief 氷
		Ice,
	};

	/// @brief 1 ヒット分の情報
	struct Hit
	{
		/// @brief 攻撃の属性
		AttackType type = AttackType::Physical;

		/// @brief ダメージ量
		int32 amount = 0;

		/// @brief クリティカルヒット
		bool isCritical = false;
	};

	/// @brief ダメージ入力パッド
	class DamagePad
	{
	public:

		/// @brief パッドの幅（ピクセル）
		static constexpr int32 Width = 200;

		/// @brief パッドの高さ（ピクセル）
		static constexpr int32 Height = 150;

		/// @brief コンストラクタ
		/// @param pos パッドの位置
		/// @param type パッドの攻撃属性
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief パッドの更新（クリックによるヒットの発行）・描画
		/// @return 発行されたヒット情報の配列。発行がなければ空の配列
		Array<Hit> update() const
		{
			// パッドの描画
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// パッド上のカーソル位置
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// ヒット数
			const int32 count = 1 + (cursorPos.x / 40);

			// 基本ダメージ量
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// パッド上にカーソルがある場合
			if (m_rect.mouseOver())
			{
				// カーソル非表示
				Cursor::RequestStyle(CursorStyle::Hidden);

				// パッド上のポインタ UI の描画
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// 発行されたヒット情報を格納する配列
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // 左 or 右クリックされた場合
			{
				// ヒット数分のヒット情報を生成して配列に追加
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // 右クリック時、最初のヒットのみクリティカルヒットに
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief パッドの矩形領域
		Rect m_rect{ 0 };

		/// @brief パッドの攻撃属性
		AttackType m_type = AttackType::Physical;
	};

	/// @brief ダメージ表示エフェクト
	struct DamageNumber : IEffect
	{
		/// @brief エフェクトの寿命（秒）
		static constexpr double Lifetime = 0.8;

		/// @brief 属性ごとの基本色
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief ヒット情報
		Hit m_hit;

		/// @brief エフェクト発生位置
		Vec2 m_start;

		/// @brief フォント
		Font m_font;

		/// @brief コンストラクタ
		/// @param hit ヒット情報
		/// @param start エフェクト発生位置
		/// @param font フォント
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief エフェクトの更新・描画
		/// @param t 発生からの経過時間（秒）
		/// @return エフェクト継続なら true, 終了なら false
		bool update(const double t) override
		{
			// 属性に応じた基本色
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];
			// 出現時の色の飽和
			{
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // 0.0 秒から 0.1 秒にかけて 1.0 → 0.0 に変化
				const double add = (0.5 * t2 * t2);
				baseColor += ColorF{ add, add, add }; // 出現直後に明るく、徐々に基本色に収束
			}
			// 終盤のフェードアウト
			{
				const double t2 = Max((t - (Lifetime - 0.2)), 0.0) / 0.2; // 0.2 秒から LifeTime にかけて 0.0 → 1.0 に変化
				baseColor.a *= (1.0 - t2 * t2);
			}

			// ダメージ量に応じた基本サイズ
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;
			// 出現時のサイズ変化
			{
				// ダメージ桁数に応じた追加サイズ
				const double extraSize = (m_hit.amount < 100) ? 20.0 : (m_hit.amount < 1000) ? 40.0 : 80.0;
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // 0.0 秒から 0.1 秒にかけて 1.0 → 0.0 に変化
				baseSize += (extraSize * t2 * t2); // 出現直後に大きく、徐々に基本サイズに収束
			}

			// 描画位置
			Vec2 basePos = m_start;
			// 途中からの上昇
			{
				const double t2 = Max((t - 0.2), 0.0); // 0.2 秒からの経過時間
				basePos.y -= (120.0 * t2 * t2);
			}

			// 数字を文字列に変換
			const String text = Format(m_hit.amount);

			// 文字列の位置オフセット（中央揃え用）
			const Vec2 offset = m_font(text).region(baseSize).size / 2;

			// 数字を描く
			//m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);
			// 強めの白 → baseColor の上下グラデーションで描く
			DrawGlyphs(m_font, TextStyle::Default(), text, baseSize, (basePos - offset), ColorF{ 1.2, baseColor.a }, baseColor, t);

			// 数字の枠を描く
			//m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });
			// グラデーション無しで枠を描く
			DrawGlyphs(m_font, TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), text, baseSize, (basePos - offset), ColorF{ 0.0, 0.0 }, ColorF{ 0.0, 0.0 }, t);

			return (t < Lifetime);
		}

		// 1 文字ずつ文字テクスチャを描く、低レベルのテキスト描画
		static void DrawGlyphs(const Font& font, const TextStyle& textStyle, const String& text, const double fontSize, const Vec2& basePos,
			const ColorF& topColor, const ColorF& bottomColor, const double t)
		{
			// 諸々の設定
			const Array<Glyph> glyphs = font.getGlyphs(text);
			const double scale = (fontSize / font.fontSize());
			const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method(), textStyle.type) };
			Graphics2D::SetMSDFParameters(textStyle);

			Vec2 penPos{ basePos };

			// 文字テクスチャを描くループ
			for (const auto& glyph : glyphs)
			{
				// 文字のテクスチャを描画する
				glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale)),
					Arg::top = topColor, Arg::bottom = bottomColor); // 上下グラデーションで描画

				// ペンの X 座標を文字の幅の分進める
				penPos.x += (glyph.xAdvance * scale);
			}
		}
	};

	void Main()
	{
		// 画面サイズの設定
		Window::Resize(1280, 720);

		// 敵のテクスチャ
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// ダメージ入力パッド
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// エフェクトが発生する範囲
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// デバッグ用フラグ
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// エフェクト用フォント
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(6);
		Effect effectManager;

		// ヒット情報キュー
		std::queue<Hit> hitQueue;
		// 最小ヒットエフェクト発行間隔（秒）
		constexpr double hitInterval = 0.04;
		// 最後にヒットエフェクトを発行してからの経過時間（秒）
		double accumulatedTime = 0.0;

		while (System::Update())
		{
			// 背景の描画
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // 空のグラデーション
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // 地面のグラデーション
			}

			// 敵キャラクターの描画
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// 攻撃の入力
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"物理ダメージ \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"炎ダメージ \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"雷ダメージ \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"氷ダメージ \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// デバッグ用 UI
			{
				SimpleGUI::Slider(U"エフェクト速度: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"敵キャラクター", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"ターゲット範囲", Vec2{ 260, 660 }, 200);
			}

			// 発行されたヒット情報をキューに追加
			for (const Hit& hit : hits)
			{
				hitQueue.push(hit);
				accumulatedTime = hitInterval; // 新しいヒットに対して、すぐエフェクトを発行するよう、蓄積時間に下駄を履かせる
				// ターゲット範囲内のランダムな位置にエフェクトを発生させる
				//effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// キューの消化とエフェクト発行
			{
				// 蓄積時間の加算
				accumulatedTime += (Scene::DeltaTime() * effectSpeed);

				// 一定間隔ごとに、キューの先頭のヒット情報を取り出して、エフェクトを発行
				if (hitInterval <= accumulatedTime)
				{
					if (not hitQueue.empty())
					{
						// キューの先頭のヒット情報を取り出す
						const Hit hit = hitQueue.front(); hitQueue.pop();
						// ターゲット範囲内のランダムな位置にエフェクトを発生させる
						effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
						// 蓄積時間を減らす
						accumulatedTime -= hitInterval;
					}
				}
			}

			// エフェクトの更新と描画
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 11. 文字単位のモーション
- 大ダメージ（3 桁以上）が出た時に、1 文字ごとに動きを付けます。
- 文字ごとにタイミングをずらして波打つような動きを加えることで、桁数の多い数字が画面内で躍動するような表現になります
- 特別なダメージが出たことを、動きの激しさでもプレイヤーに伝えます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/11.png)

??? note "コード"
	```cpp hl_lines="207 209-214 217 222"
	# include <Siv3D.hpp>

	/// @brief 攻撃の属性
	enum class AttackType
	{
		/// @brief 物理
		Physical,

		/// @brief 炎
		Fire,

		/// @brief 雷
		Thunder,

		/// @brief 氷
		Ice,
	};

	/// @brief 1 ヒット分の情報
	struct Hit
	{
		/// @brief 攻撃の属性
		AttackType type = AttackType::Physical;

		/// @brief ダメージ量
		int32 amount = 0;

		/// @brief クリティカルヒット
		bool isCritical = false;
	};

	/// @brief ダメージ入力パッド
	class DamagePad
	{
	public:

		/// @brief パッドの幅（ピクセル）
		static constexpr int32 Width = 200;

		/// @brief パッドの高さ（ピクセル）
		static constexpr int32 Height = 150;

		/// @brief コンストラクタ
		/// @param pos パッドの位置
		/// @param type パッドの攻撃属性
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief パッドの更新（クリックによるヒットの発行）・描画
		/// @return 発行されたヒット情報の配列。発行がなければ空の配列
		Array<Hit> update() const
		{
			// パッドの描画
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// パッド上のカーソル位置
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// ヒット数
			const int32 count = 1 + (cursorPos.x / 40);

			// 基本ダメージ量
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// パッド上にカーソルがある場合
			if (m_rect.mouseOver())
			{
				// カーソル非表示
				Cursor::RequestStyle(CursorStyle::Hidden);

				// パッド上のポインタ UI の描画
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// 発行されたヒット情報を格納する配列
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // 左 or 右クリックされた場合
			{
				// ヒット数分のヒット情報を生成して配列に追加
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // 右クリック時、最初のヒットのみクリティカルヒットに
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief パッドの矩形領域
		Rect m_rect{ 0 };

		/// @brief パッドの攻撃属性
		AttackType m_type = AttackType::Physical;
	};

	/// @brief ダメージ表示エフェクト
	struct DamageNumber : IEffect
	{
		/// @brief エフェクトの寿命（秒）
		static constexpr double Lifetime = 0.8;

		/// @brief 属性ごとの基本色
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief ヒット情報
		Hit m_hit;

		/// @brief エフェクト発生位置
		Vec2 m_start;

		/// @brief フォント
		Font m_font;

		/// @brief コンストラクタ
		/// @param hit ヒット情報
		/// @param start エフェクト発生位置
		/// @param font フォント
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief エフェクトの更新・描画
		/// @param t 発生からの経過時間（秒）
		/// @return エフェクト継続なら true, 終了なら false
		bool update(const double t) override
		{
			// 属性に応じた基本色
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];
			// 出現時の色の飽和
			{
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // 0.0 秒から 0.1 秒にかけて 1.0 → 0.0 に変化
				const double add = (0.5 * t2 * t2);
				baseColor += ColorF{ add, add, add }; // 出現直後に明るく、徐々に基本色に収束
			}
			// 終盤のフェードアウト
			{
				const double t2 = Max((t - (Lifetime - 0.2)), 0.0) / 0.2; // 0.2 秒から LifeTime にかけて 0.0 → 1.0 に変化
				baseColor.a *= (1.0 - t2 * t2);
			}

			// ダメージ量に応じた基本サイズ
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;
			// 出現時のサイズ変化
			{
				// ダメージ桁数に応じた追加サイズ
				const double extraSize = (m_hit.amount < 100) ? 20.0 : (m_hit.amount < 1000) ? 40.0 : 80.0;
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // 0.0 秒から 0.1 秒にかけて 1.0 → 0.0 に変化
				baseSize += (extraSize * t2 * t2); // 出現直後に大きく、徐々に基本サイズに収束
			}

			// 描画位置
			Vec2 basePos = m_start;
			// 途中からの上昇
			{
				const double t2 = Max((t - 0.2), 0.0); // 0.2 秒からの経過時間
				basePos.y -= (120.0 * t2 * t2);
			}

			// 数字を文字列に変換
			const String text = Format(m_hit.amount);

			// 文字列の位置オフセット（中央揃え用）
			const Vec2 offset = m_font(text).region(baseSize).size / 2;

			// 数字を描く
			//m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);
			// 強めの白 → baseColor の上下グラデーションで描く
			DrawGlyphs(m_font, TextStyle::Default(), text, baseSize, (basePos - offset), ColorF{ 1.2, baseColor.a }, baseColor, t);

			// 数字の枠を描く
			//m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });
			// グラデーション無しで枠を描く
			DrawGlyphs(m_font, TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), text, baseSize, (basePos - offset), ColorF{ 0.0, 0.0 }, ColorF{ 0.0, 0.0 }, t);

			return (t < Lifetime);
		}

		// 1 文字ずつ文字テクスチャを描く、低レベルのテキスト描画
		static void DrawGlyphs(const Font& font, const TextStyle& textStyle, const String& text, const double fontSize, const Vec2& basePos,
			const ColorF& topColor, const ColorF& bottomColor, const double t)
		{
			// 諸々の設定
			const Array<Glyph> glyphs = font.getGlyphs(text);
			const double scale = (fontSize / font.fontSize());
			const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method(), textStyle.type) };
			Graphics2D::SetMSDFParameters(textStyle);

			Vec2 penPos{ basePos };

			// 文字テクスチャを描くループ
			for (int32 index = 0; const auto& glyph : glyphs) // index は何文字目であるかのインデックス
			{
				const double waveIndex = t / 0.05 - 0.5; // 波の進行度合い（何文字目に波のピークがあるか）
				Vec2 offset{ 0,0 };
				if (2 < text.size()) // 大ダメージ（数字が 3 桁以上）の場合、波状のアニメーション
				{
					offset.y = Max(1.0 - AbsDiff<double>(waveIndex, index), 0.0) * -6.0 * scale; // 波のピークに近いほど Y 座標が上に移動
				}

				// 文字のテクスチャを描画する
				glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale) + offset),
					Arg::top = topColor, Arg::bottom = bottomColor); // 上下グラデーションで描画

				// ペンの X 座標を文字の幅の分進める
				penPos.x += (glyph.xAdvance * scale);
				++index;
			}
		}
	};

	void Main()
	{
		// 画面サイズの設定
		Window::Resize(1280, 720);

		// 敵のテクスチャ
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// ダメージ入力パッド
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// エフェクトが発生する範囲
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// デバッグ用フラグ
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// エフェクト用フォント
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(6);
		Effect effectManager;

		// ヒット情報キュー
		std::queue<Hit> hitQueue;
		// 最小ヒットエフェクト発行間隔（秒）
		constexpr double hitInterval = 0.04;
		// 最後にヒットエフェクトを発行してからの経過時間（秒）
		double accumulatedTime = 0.0;

		while (System::Update())
		{
			// 背景の描画
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // 空のグラデーション
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // 地面のグラデーション
			}

			// 敵キャラクターの描画
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// 攻撃の入力
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"物理ダメージ \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"炎ダメージ \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"雷ダメージ \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"氷ダメージ \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// デバッグ用 UI
			{
				SimpleGUI::Slider(U"エフェクト速度: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"敵キャラクター", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"ターゲット範囲", Vec2{ 260, 660 }, 200);
			}

			// 発行されたヒット情報をキューに追加
			for (const Hit& hit : hits)
			{
				hitQueue.push(hit);
				accumulatedTime = hitInterval; // 新しいヒットに対して、すぐエフェクトを発行するよう、蓄積時間に下駄を履かせる
				// ターゲット範囲内のランダムな位置にエフェクトを発生させる
				//effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// キューの消化とエフェクト発行
			{
				// 蓄積時間の加算
				accumulatedTime += (Scene::DeltaTime() * effectSpeed);

				// 一定間隔ごとに、キューの先頭のヒット情報を取り出して、エフェクトを発行
				if (hitInterval <= accumulatedTime)
				{
					if (not hitQueue.empty())
					{
						// キューの先頭のヒット情報を取り出す
						const Hit hit = hitQueue.front(); hitQueue.pop();
						// ターゲット範囲内のランダムな位置にエフェクトを発生させる
						effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
						// 蓄積時間を減らす
						accumulatedTime -= hitInterval;
					}
				}
			}

			// エフェクトの更新と描画
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 12. 参考になるチュートリアル
- [チュートリアル 26. 図形を描く](../../tutorial2/shape/){:target="_blank"}
- [チュートリアル 30. 時間と動き](../../tutorial2/time/){:target="_blank"}
	- 時間の扱いやイージングに関する機能の説明があります
- [チュートリアル 34. 文字を描く](../../tutorial2/font/){:target="_blank"}
- [チュートリアル 39. ランダム](../../tutorial2/random/){:target="_blank"}
- [チュートリアル 48. 2D レンダーステート](../../tutorial3/2d-render-state/){:target="_blank"}
- [チュートリアル 51. エフェクト](../../tutorial3/effect/){:target="_blank"}
	- 文字以外のエフェクトも含め、エフェクト全般の説明があります。ダメージ演出にも追加・応用できます
