# 51. エフェクト
小さなモーションやエフェクトの演出に便利な `Effect` および `IEffect` クラスの使い方を学びます。

## 51.1 エフェクトの基本
- エフェクトを利用すると、時間ベースの短いモーション表現を効率的に作れます
- エフェクトを実装するには、まず `IEffect` クラスを継承したクラスを作成し、メンバ関数 `bool update(double t)` をオーバーライドします
	- `t` は、エフェクトが発生してからの経過時間（秒）です
	- 経過時間に応じた描画を行い、エフェクトが引き続き生存するかを `bool` 値で返します
	- 例えば `return (t < 3.0);` とすれば、エフェクトは 3 秒間継続してから終了します
		- 実行時の負荷を抑制するため、10 秒以上継続したエフェクトは自動的に終了されます
- `Effect` クラスは、複数の `IEffect` 派生クラスを管理し、一括して更新します
	- `.add<派生クラス名>(コンストラクタ引数)` でエフェクトを追加します
	- `.update()` でアクティブなエフェクトの `IEffect::update()` を実行します
	- `.num_effects()` はアクティブなエフェクトの数を返します
- 次のサンプルコードは、クリックした場所に、時間とともに大きくなる輪を発生させます
	- このエフェクトは `return (t < 1.0);` より、1 秒間継続します

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/effect/1.mp4?raw=true" autoplay loop muted playsinline></video>

```cpp
# include <Siv3D.hpp>

struct RingEffect : IEffect
{
	Vec2 m_pos;

	ColorF m_color;

	// このコンストラクタ引数が .add<RingEffect>() の引数になる
	explicit RingEffect(const Vec2& pos)
		: m_pos{ pos }
		, m_color{ RandomColorF() } {}

	bool update(double t) override
	{
		// 時間に応じて大きくなる輪を描く
		Circle{ m_pos, (t * 100) }.drawFrame(4, m_color);

		// 1 秒未満なら継続する
		return (t < 1.0);
	}
};

void Main()
{
	Effect effect;

	while (System::Update())
	{
		ClearPrint();

		// アクティブなエフェクトの数
		Print << U"Active effects: {}"_fmt(effect.num_effects());

		if (MouseL.down())
		{
			// エフェクトを追加する
			effect.add<RingEffect>(Cursor::Pos());
		}

		// 管理しているすべてのエフェクトの IEffect::update() を実行する
		effect.update();
	}
}
```


## 51.2 ラムダ式によるエフェクト実装
- `IEffect` の派生クラスの代わりに、ラムダ式でエフェクトを記述することもできます
- とくに、数行程度で書ける単純なエフェクトに便利です
- 引数は `double` 型で、経過時間（秒）を表します
- 戻り値は `bool` 型で、エフェクトが継続するかを表します
	- `return (t < 1.0);` とすれば、エフェクトは 1 秒間継続します
- **51.1** と同じエフェクトをラムダ式で書くと、次のようになります

```cpp
# include <Siv3D.hpp>

void Main()
{
	Effect effect;

	while (System::Update())
	{
		ClearPrint();

		// アクティブなエフェクトの数
		Print << U"Active effects: {}"_fmt(effect.num_effects());

		if (MouseL.down())
		{
			// エフェクトを追加する
			effect.add([pos = Cursor::Pos(), color = RandomColorF()](double t)
			{
				// 時間に応じて大きくなる輪を描く
				Circle{ pos, (t * 100) }.drawFrame(4, color);

				// 1 秒未満なら継続する
				return (t < 1.0);
			});
		}

		// 管理しているすべてのエフェクトの IEffect::update() を実行する
		effect.update();
	}
}
```


## 51.3 イージングの活用
- イージング（**チュートリアル 30**）を使うことで、動きの印象を変えることができます

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/effect/3.mp4?raw=true" autoplay loop muted playsinline></video>

```cpp
# include <Siv3D.hpp>

struct RingEffect : IEffect
{
	Vec2 m_pos;

	ColorF m_color;

	explicit RingEffect(const Vec2& pos)
		: m_pos{ pos }
		, m_color{ RandomColorF() } {}

	bool update(double t) override
	{
		// イージング
		const double e = EaseOutExpo(t);

		Circle{ m_pos, (e * 100) }.drawFrame((20.0 * (1.0 - e)), m_color);

		return (t < 1.0);
	}
};

void Main()
{
	Effect effect;

	while (System::Update())
	{
		ClearPrint();

		Print << U"Active effects: {}"_fmt(effect.num_effects());

		if (MouseL.down())
		{
			effect.add<RingEffect>(Cursor::Pos());
		}

		effect.update();
	}
}
```


## 51.4 エフェクトの一時停止と速度変更、消去
- `Effect` は次のようなメンバ関数を使って、管理しているエフェクトを制御できます：
- 速度の変更は、個々の `.update()` に渡される `t` の増加ペースを増減させることによって実現されます

| コード | 説明 |
|---|---|
| `.pause()` | エフェクトの更新を一時停止する |
| `.resume()` | エフェクトの更新を再開する |
| `.setSpeed(double)` | エフェクトの速度を変更する |
| `.clear()` | アクティブなエフェクトをすべて消去する |

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/effect/4.mp4?raw=true" autoplay loop muted playsinline></video>

```cpp
# include <Siv3D.hpp>

struct RingEffect : IEffect
{
	Vec2 m_pos;

	explicit RingEffect(const Vec2& pos)
		: m_pos{ pos } {
	}

	bool update(double t) override
	{
		Circle{ m_pos, (t * 120) }.drawFrame(4 * (1.0 - t));

		return (t < 1.0);
	}
};

void Main()
{
	Effect effect;

	// 出現間隔（秒）
	constexpr double SpawnInterval = 0.15;

	// 蓄積された時間（秒）
	double accumulatedTime = 0.0;

	// ボールの位置
	Vec2 pos{ 200, 300 };

	// ボールの速度
	Vec2 velocity{ 320, 360 };

	while (System::Update())
	{
		ClearPrint();
		Print << U"Active effects: {}"_fmt(effect.num_effects());
		Print << U"speed: {}"_fmt(effect.getSpeed());

		if (not effect.isPaused())
		{
			const double deltaTime = (Scene::DeltaTime() * effect.getSpeed());
			accumulatedTime += deltaTime;
			pos += (deltaTime * velocity);

			// ボールが壁にぶつかったら反射する
			if (((0 < velocity.x) && (800 < pos.x))
				|| ((velocity.x < 0) && (pos.x < 0)))
			{
				velocity.x = -velocity.x;
			}
			else if (((0 < velocity.y) && (600 < pos.y))
				|| ((velocity.y < 0) && (pos.y < 0)))
			{
				velocity.y = -velocity.y;
			}
		}

		// 蓄積時間が出現間隔を超えたら
		if (SpawnInterval <= accumulatedTime)
		{
			accumulatedTime -= SpawnInterval;
			effect.add<RingEffect>(pos);
		}

		pos.asCircle(10).draw();

		effect.update();

		if (effect.isPaused())
		{
			if (SimpleGUI::Button(U"Resume", Vec2{ 600, 20 }, 100))
			{
				// エフェクトの更新を再開する
				effect.resume();
			}
		}
		else
		{
			if (SimpleGUI::Button(U"Pause", Vec2{ 600, 20 }, 100))
			{
				// エフェクトの更新を一時停止する
				effect.pause();
			}
		}

		if (SimpleGUI::Button(U"x2.0", Vec2{ 600, 60 }, 100))
		{
			// 2.0 倍速にする
			effect.setSpeed(2.0);
		}

		if (SimpleGUI::Button(U"x1.0", Vec2{ 600, 100 }, 100))
		{
			// 1.0 倍速にする
			effect.setSpeed(1.0);
		}

		if (SimpleGUI::Button(U"x0.5", Vec2{ 600, 140 }, 100))
		{
			// 0.5 倍速にする
			effect.setSpeed(0.5);
		}

		if (SimpleGUI::Button(U"Clear", Vec2{ 600, 180 }, 100))
		{
			// 発生中のエフェクトをすべて消去する
			effect.clear();
		}
	}
}
```

- この章では扱いませんが、より大量のパーティクルを効率的に制御したい場合は、`ParticleSystem2D` を使うと便利です


## 51.5 （サンプル）上昇する文字
- フォントを使ったエフェクトの例です
- クリックした位置から、ランダムな数字が上昇していきます
- 数字の色は、スコアに応じて変化します

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/effect/5.mp4?raw=true" autoplay loop muted playsinline></video>

```cpp
# include <Siv3D.hpp>

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
		const HSV color{ (180 - m_score * 1.8), (1.0 - (t * 2.0)) };

		m_font(m_score).drawAt(TextStyle::Outline(0.2, ColorF{ 0.0, color.a }),
			60, m_start.movedBy(0, t * -120), color);

		return (t < 0.5);
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Heavy, FontStyle::Italic };

	Effect effect;

	while (System::Update())
	{
		if (MouseL.down())
		{
			effect.add<ScoreEffect>(Cursor::Pos(), Random(0, 100), font);
		}

		effect.update();
	}
}
```

## 51.6 （サンプル）飛び散る破片
- 1 つのエフェクトで複数の図形を描く例です
- クリックした位置を中心に、ランダムな方向に飛び散る三角形を描きます
- 三角形の色は、Y 座標に応じて変化します

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/effect/6.mp4?raw=true" autoplay loop muted playsinline></video>

```cpp
# include <Siv3D.hpp>

struct Particle
{
	Vec2 start;

	Vec2 velocity;
};

struct Spark : IEffect
{
	Array<Particle> m_particles;

	explicit Spark(const Vec2& start)
		: m_particles(50)
	{
		for (auto& particle : m_particles)
		{
			particle.start = (start + RandomVec2(12.0));
			particle.velocity = (RandomVec2(1.0) * Random(100.0));
		}
	}

	bool update(double t) override
	{
		for (const auto& particle : m_particles)
		{
			const Vec2 pos = (particle.start
				+ particle.velocity * t + 0.5 * t * t * Vec2{ 0, 240 });

			Triangle{ pos, (20.0 * (1.0 - t)), (pos.x * 10_deg) }.draw(HSV{ pos.y - 40 });
		}

		return (t < 1.0);
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Effect effect;

	while (System::Update())
	{
		if (MouseL.down())
		{
			effect.add<Spark>(Cursor::Pos());
		}

		effect.update();
	}
}
```


## 51.7 （サンプル）飛び散る星
- クリックした位置を中心に、星型のエフェクトを発生させます

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/effect/7.mp4?raw=true" autoplay loop muted playsinline></video>

```cpp
# include <Siv3D.hpp>

struct StarEffect : IEffect
{
	static constexpr Vec2 Gravity{ 0, 160 };

	struct Star
	{
		Vec2 start;
		Vec2 velocity;
		ColorF color;
	};

	Array<Star> m_stars;

	StarEffect(const Vec2& pos, double baseHue)
	{
		for (int32 i = 0; i < 6; ++i)
		{
			const Vec2 velocity = RandomVec2(Circle{ 60 });
			Star star{
				.start = (pos + velocity * 0.5),
				.velocity = velocity,
				.color = HSV{ baseHue + Random(-20.0, 20.0) },
			};
			m_stars << star;
		}
	}

	bool update(double t) override
	{
		t /= 0.4;

		for (auto& star : m_stars)
		{
			const Vec2 pos = (star.start
				+ star.velocity * t + 0.5 * t * t * Gravity);
			const double angle = (pos.x * 3_deg);

			Shape2D::Star((36 * (1.0 - t)), pos, angle).draw(star.color);
		}

		return (t < 1.0);
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Effect effect;
	Circle circle{ 400, 300, 30 };
	double baseHue = 180.0;

	while (System::Update())
	{
		if (circle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		if (circle.leftClicked())
		{
			effect.add<StarEffect>(Cursor::Pos(), baseHue);
			circle.center = RandomVec2(Scene::Rect().stretched(-80));
			baseHue = Random(0.0, 360.0);
		}

		circle.draw(HSV{ baseHue });
		effect.update();
	}
}
```


## 51.8 （サンプル）泡のようなエフェクト
- 時間差で図形を登場させる制御を行うエフェクトの例です
- レンダーステートの設定は、個別のエフェクトの `.update()` 内ではなく、`Effect::update()` に対して適用するほうが効率的です

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/effect/8.mp4?raw=true" autoplay loop muted playsinline></video>

```cpp
# include <Siv3D.hpp>

struct BubbleEffect : IEffect
{
	struct Bubble
	{
		Vec2 offset;
		double startTime;
		double scale;
		ColorF color;
	};

	Vec2 m_pos;

	Array<Bubble> m_bubbles;

	BubbleEffect(const Vec2& pos, double baseHue)
		: m_pos{ pos }
	{
		for (int32 i = 0; i < 8; ++i)
		{
			Bubble bubble{
				.offset = RandomVec2(Circle{30}),
				.startTime = Random(-0.3, 0.1), // 登場の時間差
				.scale = Random(0.1, 1.2),
				.color = HSV{ baseHue + Random(-30.0, 30.0) }
			};
			m_bubbles << bubble;
		}
	}

	bool update(double t) override
	{
		for (const auto& bubble : m_bubbles)
		{
			const double t2 = (bubble.startTime + t);

			if (not InRange(t2, 0.0, 1.0))
			{
				continue;
			}

			const double e = EaseOutExpo(t2);

			Circle{ (m_pos + bubble.offset + (bubble.offset * 4 * t)), (e * 40 * bubble.scale) }
				.draw(ColorF{ bubble.color, 0.15 })
				.drawFrame((30.0 * (1.0 - e) * bubble.scale), bubble.color);
		}

		return (t < 1.3);
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Effect effect;

	while (System::Update())
	{
		if (MouseL.down())
		{
			effect.add<BubbleEffect>(Cursor::Pos(), Random(0.0, 360.0));
		}

		{
			const ScopedRenderStates2D blend{ BlendState::Additive };
			effect.update();
		}
	}
}
```


## 51.9 （サンプル）クリック時のエフェクト
- 1 つのエフェクトでたくさんの描画を行う例です

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/effect/9.mp4?raw=true" autoplay loop muted playsinline></video>

```cpp
# include <Siv3D.hpp>

struct TouchEffect : IEffect
{
	struct Particle
	{
		Vec2 velocity;
		Vec2 start;
		double r;
		double angle;
		bool cw;
		ColorF color;
	};

	struct Star
	{
		Vec2 velocity;
		Vec2 start;
		double angle;
		double scale;
		ColorF color;
	};

	Vec2 m_pos;

	Array<Particle> m_particles;

	Array<Star> m_stars;

	explicit TouchEffect(const Vec2& pos)
		: m_pos{ pos }
	{
		for (int32 i = 0; i < 200; ++i)
		{
			const Vec2 velocity = RandomVec2(28.0);
			Particle particle{
				.velocity = velocity,
				.start = velocity,
				.r = Random(6.0, 12.0),
				.angle = Random(360_deg),
				.cw = RandomBool(),
				.color = HSV{ Random(50.0, 70.0), 0.4, 1.0 },
			};
			m_particles << particle;
		}

		for (int32 i = 0; i < 8; ++i)
		{
			const Vec2 velocity = RandomVec2(28.0);
			Star star{
				.velocity = velocity,
				.start = (velocity + RandomVec2(2.0)),
				.angle = Random(360_deg),
				.scale = Random(0.6, 1.4),
				.color = HSV{ Random(50.0, 70.0), 0.4, 1.0 },
			};
			m_stars << star;
		}
	}

	bool update(double t) override
	{
		t /= 0.45;

		const double r = (30 + t * 30);
		const ColorF outer = HSV{ 180, 0.8, 1.0, 0.0 };
		const ColorF inner = HSV{ 180, 0.8, 1.0, (0.5 * (1.0 - t)) };

		Circle{ m_pos, r }
			.drawFrame(10, 0, outer, inner)
			.drawFrame(0, 10, inner, outer);

		for (const auto& particle : m_particles)
		{
			const Vec2 pos = m_pos
				+ particle.start
				+ Circular(particle.r, particle.angle + t * 120_deg * (particle.cw ? 1 : -1))
				+ (particle.velocity * t - 0.5 * t * t * particle.velocity);
			const double rOuter = (1.0 * (1.0 - t) * 2);
			const double rInner = (0.8 * (1.0 - t) * 2);

			Shape2D::NStar(2, rOuter, rInner, pos, particle.angle)
				.draw(particle.color);
		}

		for (const auto& star : m_stars)
		{
			const Vec2 pos = m_pos
				+ star.start
				+ (star.velocity * t - 0.5 * t * t * star.velocity);
			const double rOuter = (12 * (1.0 - t) * star.scale);
			const double rInner = (4 * (1.0 - t) * star.scale);
			const double angle = (star.angle + t * 90_deg);

			Shape2D::NStar(4, rOuter, rInner, pos, angle)
				.draw(star.color);
		}

		return (t < 1.0);
	}
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Effect effect;

	while (System::Update())
	{
		if (MouseL.down())
		{
			effect.add<TouchEffect>(Cursor::Pos());
		}

		{
			const ScopedRenderStates2D blend{ BlendState::Additive };
			effect.update();
		}
	}
}
```


## 51.10 （サンプル）エフェクトの再帰
- エフェクトの中で新たなエフェクトを発生させる例です
- 無限に増えることのないよう、新たなエフェクトを発生させられる世代を制限しています

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/effect/10.mp4?raw=true" autoplay loop muted playsinline></video>

```cpp
# include <Siv3D.hpp>

// 火花の状態
struct Fire
{
	// 初速
	Vec2 v0;

	// 色相のオフセット
	double hueOffset;

	// スケーリング
	double scale;

	// 破裂するまでの時間
	double nextFireSec;

	// 破裂して子エフェクトを作成したか
	bool hasChild = false;

	// 重力加速度
	static constexpr Vec2 Gravity{ 0, 240 };
};

// 火花エフェクト
struct Firework : IEffect
{
	// 火花の個数
	static constexpr int32 FireCount = 12;

	// 循環参照を避けるため、IEffect の中で Effect を持つ場合、参照またはポインタにする
	const Effect& m_parent;

	// 花火の中心座標
	Vec2 m_center;

	// 火の状態
	std::array<Fire, FireCount> m_fires;

	// 何世代目？ [0, 1, 2]
	int32 m_n;

	Firework(const Effect& parent, const Vec2& center, int32 n, const Vec2& v0)
		: m_parent{ parent }
		, m_center{ center }
		, m_n{ n }
	{
		for (auto i : step(FireCount))
		{
			const double angle = (i * 30_deg + Random(-10_deg, 10_deg));
			const double speed = (60.0 - m_n * 15) * Random(0.9, 1.1) * (IsEven(i) ? 0.5 : 1.0);
			m_fires[i].v0 = Circular{ speed, angle } + v0;
			m_fires[i].hueOffset = Random(-10.0, 10.0) + (IsEven(i) ? 15 : 0);
			m_fires[i].scale = Random(0.8, 1.2);
			m_fires[i].nextFireSec = Random(0.7, 1.0);
		}
	}

	bool update(double t) override
	{
		for (const auto& fire : m_fires)
		{
			const Vec2 pos = (m_center + fire.v0 * t + 0.5 * t * t * Fire::Gravity);
			pos.asCircle((10 - (m_n * 3)) * ((1.5 - t) / 1.5) * fire.scale)
				.draw(HSV{ 10 + m_n * 120.0 + fire.hueOffset, 0.6, 1.0 - m_n * 0.2 });
		}

		if (m_n < 2) // 0, 1 世代目なら
		{
			for (auto& fire : m_fires)
			{
				if (!fire.hasChild && (fire.nextFireSec <= t))
				{
					// 子エフェクトを作成
					const Vec2 pos = (m_center + fire.v0 * t + 0.5 * t * t * Fire::Gravity);
					m_parent.add<Firework>(m_parent, pos, (m_n + 1), fire.v0 + (t * Fire::Gravity));
					fire.hasChild = true;
				}
			}
		}

		return (t < 1.5);
	}
};

// 打ち上げエフェクト
struct FirstFirework : IEffect
{
	// 循環参照を避けるため、IEffect の中で Effect を持つ場合、参照またはポインタにする
	const Effect& m_parent;

	// 打ち上げ位置
	Vec2 m_start;

	// 打ち上げ初速
	Vec2 m_v0;

	FirstFirework(const Effect& parent, const Vec2& start, const Vec2& v0)
		: m_parent{ parent }
		, m_start{ start }
		, m_v0{ v0 } {
	}

	bool update(double t) override
	{
		const Vec2 pos = (m_start + m_v0 * t + 0.5 * t * t * Fire::Gravity);
		Circle{ pos, 6 }.draw();
		Line{ m_start, pos }.draw(LineStyle::RoundCap, 8, ColorF{ 0.0 }, ColorF{ 1.0 - (t / 0.6) });

		if (t < 0.6)
		{
			return true;
		}
		else
		{
			// 終了時に子エフェクトを作成
			const Vec2 velocity = (m_v0 + t * Fire::Gravity);
			m_parent.add<Firework>(m_parent, pos, 0, velocity);
			return false;
		}
	}
};

void Main()
{
	Effect effect;

	while (System::Update())
	{
		Scene::Rect().draw(Arg::top(0.0), Arg::bottom(0.2, 0.1, 0.4));

		if (MouseL.down())
		{
			effect.add<FirstFirework>(effect, Cursor::Pos(), Vec2{ 0, -400 });
		}

		{
			const ScopedRenderStates2D blend{ BlendState::Additive };
			effect.update();
		}
	}
}
```
