
# 16. Effect

この章では、ちょっとしたアニメーションやエフェクトの演出に便利な `Effect` クラスの使い方を学びます。

## 16.1 Effect の基本
エフェクト機能を使うには、エフェクトを管理する `Effect` オブジェクトを作成し、`Effect::add<EffectType>()` で個々のエフェクトのパラメータを追加してエフェクトを発生させます。ここでいう `EffectType` は、`IEffect` を継承したクラスです。このクラスに必要な実装は `bool update(double t) override` メンバ関数です。エフェクトが発生してからの経過時間 `t` を受け取り、そのエフェクトの描画を行い、最後にエフェクトを次のフレームも継続させるかを `bool` 値で返します。例えば `return t < 3.0;` とすれば、エフェクトは 3 秒間継続できます。

次のプログラムは、クリックした場所に、時間とともに大きくなる輪を発生させるエフェクトを実装したものです。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/16/1-0.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

struct RingEffect : IEffect
{
	Vec2 m_pos;

	// このコンストラクタ引数が、Effect::add<RingEffect>() の引数になる
	RingEffect(const Vec2& pos)
		: m_pos(pos) {}

	bool update(double t) override
	{
		// 時間に応じて大きくなる輪
		Circle(m_pos, t * 100).drawFrame();

		// 1 秒以上経過したら消滅
		return t < 1.0;
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
			// エフェクトを発生
			effect.add<RingEffect>(Cursor::Pos());
		}

		// アクティブなエフェクトのプログラム IEffect::update() を実行
		effect.update();
	}
}
```

### ラムダ式での実装
`IEffect` 継承クラスの代わりに、引数が `double`, 戻り値が `bool` 型のラムダ式でエフェクトを記述することもできます。数行で書ける単純なエフェクトで、わざわざクラスを定義するのが面倒な場合はこの方法が便利です。

```C++
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
			// エフェクトを発生
			effect.add([pos = Cursor::Pos()](double t)
			{
				// 時間に応じて大きくなる輪
				Circle(pos, t * 100).drawFrame();
				// 1 秒以上経過したら消滅
				return t < 1.0;
			});
		}

		// アクティブなエフェクトのプログラム IEffect::update() を実行
		effect.update();
	}
}
```


## 16.2 少しこだわったエフェクト
イージングと組み合わせることもできます。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/16/2-0.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

struct RingEffect : IEffect
{
	Vec2 m_pos;

	ColorF m_color;

	RingEffect(const Vec2& pos)
		: m_pos(pos)
		, m_color(RandomColor()) {}

	bool update(double t) override
	{
		// イージング
		const double e = EaseOutExpo(t);

		Circle(m_pos, e * 100).drawFrame(20.0 * (1.0 - e), m_color);

		return t < 1.0;
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

		{
			// 加算ブレンドを有効に
			const ScopedRenderStates2D additiveBlend(BlendState::Additive);

			// アクティブなエフェクトのプログラム IEffect::update() を実行
			effect.update();
		}
	}
}
```


## 16.3 上昇する文字
フォントを使ったエフェクトの例です。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/16/3-0.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

struct NumberEffect : IEffect
{
	Vec2 m_start;

	int32 m_number;

	Font m_font;

	NumberEffect(const Vec2& start, int32 num, const Font& font)
		: m_start(start)
		, m_number(num)
		, m_font(font) {}

	bool update(double t) override
	{
		const HSV color(180 - m_number * 1.8, 1.0 - (t * 2.0));

		m_font(m_number).drawAt(m_start.movedBy(0, t * -120), color);

		// 0.5 秒以上経過で消滅
		return t < 0.5;
	}
};

void Main()
{
	const Font font(50, Typeface::Heavy);

	Effect effect;

	while (System::Update())
	{
		if (MouseL.down())
		{
			effect.add<NumberEffect>(Cursor::Pos(), Random(0, 100), font);
		}

		effect.update();
	}
}
```


## 16.4 飛び散る破片
一つのエフェクトで複数の図形を描く例です。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/16/4-0.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

struct Particle
{
	Vec2 start;

	Vec2 velocity;
};

struct Spark : IEffect
{
	Array<Particle> m_particles;

	Spark(const Vec2& start)
		: m_particles(50)
	{
		for (auto& particle : m_particles)
		{
			particle.start = start + RandomVec2(10.0);

			particle.velocity = RandomVec2(1.0) * Random(80.0);
		}
	}

	bool update(double t) override
	{
		for (const auto& particle : m_particles)
		{
			const Vec2 pos = particle.start
				+ particle.velocity * t + 0.5 * t * t * Vec2(0, 240);
			
			Triangle(pos, 16.0, pos.x * 5_deg).draw(HSV(pos.y - 40).toColorF(1.0 - t));
		}

		return t < 1.0;
	}
};

void Main()
{
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


## 16.5 エフェクトの一時停止と消去
`Effect::pause()` でエフェクトの更新を一時停止、`Effect::resume()` でエフェクトの更新を再開、`Effect::clear()` でエフェクトをすべて消去します。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/16/5-0.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

struct RingEffect : IEffect
{
	Vec2 m_pos;

	RingEffect(const Vec2& pos)
		: m_pos(pos) {}

	bool update(double t) override
	{
		const double e = EaseOutExpo(t);

		Circle(m_pos, e * 80).drawFrame(20.0 * (1.0 - e));

		return t < 1.0;
	}
};

void Main()
{
	Effect effect;

	// エフェクト発生させる間隔
	constexpr double effectSpawnTime = 0.15;
	double time = 0.0;

	while (System::Update())
	{
		if (!effect.isPaused())
		{
			time += Scene::DeltaTime();
		}

		ClearPrint();

		Print << U"Active effects: {}"_fmt(effect.num_effects());

		// effectSpawnTime が経過するたびにエフェクトが発生
		if (time >= effectSpawnTime)
		{
			time -= effectSpawnTime;

			effect.add<RingEffect>(Cursor::Pos());
		}

		effect.update();

		if (effect.isPaused())
		{
			if (SimpleGUI::Button(U"Resume", Vec2(600, 20), 100))
			{
				// エフェクトの更新を再開
				effect.resume();
			}
		}
		else
		{
			if (SimpleGUI::Button(U"Pause", Vec2(600, 20), 100))
			{
				// エフェクトの更新を一時停止
				effect.pause();
			}
		}

		if (SimpleGUI::Button(U"Clear", Vec2(600, 60), 100))
		{
			// 発生中のエフェクトをすべて消去
			effect.clear();
		}
	}
}
```


この章では扱いませんが、より大量のパーティクルを効率的に制御したい場合は、`ParticleSystem2D` を使うと便利です。
