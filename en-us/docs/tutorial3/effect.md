# 51. Effects
Learn how to use the `Effect` and `IEffect` classes, which are convenient for creating small motions and effects.

## 51.1 Effect Basics
- Effects allow you to efficiently create time-based short motion expressions
- To implement an effect, first create a class that inherits from the `IEffect` class and override the member function `bool update(double t)`
	- `t` is the elapsed time (in seconds) since the effect occurred
	- Draw according to the elapsed time and return a `bool` value indicating whether the effect continues to exist
	- For example, `return (t < 3.0);` will make the effect continue for 3 seconds before ending
		- To control runtime load, effects that continue for more than 10 seconds are automatically terminated
- The `Effect` class manages multiple `IEffect` derived classes and updates them all at once
	- Add effects with `.add<DerivedClassName>(constructor arguments)`
	- Execute `.update()` on active effects with `IEffect::update()`
	- `.num_effects()` returns the number of active effects
- The following sample code generates an expanding ring at the clicked location
	- This effect continues for 1 second due to `return (t < 1.0);`

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/effect/1.mp4?raw=true" autoplay loop muted playsinline></video>

```cpp
# include <Siv3D.hpp>

struct RingEffect : IEffect
{
	Vec2 m_pos;

	ColorF m_color;

	// This constructor's arguments become the arguments for .add<RingEffect>()
	explicit RingEffect(const Vec2& pos)
		: m_pos{ pos }
		, m_color{ RandomColorF() } {}

	bool update(double t) override
	{
		// Draw a ring that grows with time
		Circle{ m_pos, (t * 100) }.drawFrame(4, m_color);

		// Continue if less than 1 second
		return (t < 1.0);
	}
};

void Main()
{
	Effect effect;

	while (System::Update())
	{
		ClearPrint();

		// Number of active effects
		Print << U"Active effects: {}"_fmt(effect.num_effects());

		if (MouseL.down())
		{
			// Add an effect
			effect.add<RingEffect>(Cursor::Pos());
		}

		// Execute IEffect::update() on all managed effects
		effect.update();
	}
}
```


## 51.2 Effect Implementation with Lambda Expressions
- Instead of creating a derived class of `IEffect`, you can also describe effects using lambda expressions
- This is particularly convenient for simple effects that can be written in a few lines
- The argument is of type `double`, representing elapsed time (in seconds)
- The return value is of type `bool`, indicating whether the effect continues
	- `return (t < 1.0);` will make the effect continue for 1 second
- The same effect as **51.1** written with a lambda expression:

```cpp
# include <Siv3D.hpp>

void Main()
{
	Effect effect;

	while (System::Update())
	{
		ClearPrint();

		// Number of active effects
		Print << U"Active effects: {}"_fmt(effect.num_effects());

		if (MouseL.down())
		{
			// Add an effect
			effect.add([pos = Cursor::Pos(), color = RandomColorF()](double t)
			{
				// Draw a ring that grows with time
				Circle{ pos, (t * 100) }.drawFrame(4, color);

				// Continue if less than 1 second
				return (t < 1.0);
			});
		}

		// Execute IEffect::update() on all managed effects
		effect.update();
	}
}
```


## 51.3 Using Easing
- Using easing (**Tutorial 30**) can change the impression of motion

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
		// Easing
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


## 51.4 Pausing, Speed Control, and Clearing Effects
- `Effect` can control the managed effects using the following member functions:
- Speed changes are implemented by increasing or decreasing the rate of increase of `t` passed to each `.update()`

| Code | Description |
|---|---|
| `.pause()` | Pause effect updates |
| `.resume()` | Resume effect updates |
| `.setSpeed(double)` | Change effect speed |
| `.clear()` | Clear all active effects |

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

	// Spawn interval (seconds)
	constexpr double SpawnInterval = 0.15;

	// Accumulated time (seconds)
	double accumulatedTime = 0.0;

	// Ball position
	Vec2 pos{ 200, 300 };

	// Ball velocity
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

			// Reflect when ball hits wall
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

		// When accumulated time exceeds spawn interval
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
				// Resume effect updates
				effect.resume();
			}
		}
		else
		{
			if (SimpleGUI::Button(U"Pause", Vec2{ 600, 20 }, 100))
			{
				// Pause effect updates
				effect.pause();
			}
		}

		if (SimpleGUI::Button(U"x2.0", Vec2{ 600, 60 }, 100))
		{
			// Set to 2.0x speed
			effect.setSpeed(2.0);
		}

		if (SimpleGUI::Button(U"x1.0", Vec2{ 600, 100 }, 100))
		{
			// Set to 1.0x speed
			effect.setSpeed(1.0);
		}

		if (SimpleGUI::Button(U"x0.5", Vec2{ 600, 140 }, 100))
		{
			// Set to 0.5x speed
			effect.setSpeed(0.5);
		}

		if (SimpleGUI::Button(U"Clear", Vec2{ 600, 180 }, 100))
		{
			// Clear all active effects
			effect.clear();
		}
	}
}
```

- While not covered in this chapter, `ParticleSystem2D` is useful for efficiently controlling large amounts of particles


## 51.5 (Sample) Rising Text
- An example of effects using fonts
- Random numbers rise from the clicked position
- The color of the numbers changes according to the score

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

## 51.6 (Sample) Scattering Fragments
- An example of drawing multiple shapes with one effect
- Draws triangles scattering in random directions from the clicked position
- The triangle colors change according to the Y coordinate

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


## 51.7 (Sample) Scattering Stars
- Creates star-shaped effects centered at the clicked position

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


## 51.8 (Sample) Bubble-like Effect
- An example of an effect that controls the appearance of shapes with time delays
- Render state settings are more efficient when applied to `Effect::update()` rather than within individual effect `.update()`

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
				.startTime = Random(-0.3, 0.1), // Time delay for appearance
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


## 51.9 (Sample) Click Effect
- An example of performing many drawings with one effect

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
			const Vec2 velocoty = RandomVec2(28.0);
			Particle particle{
				.velocity = velocoty,
				.start = velocoty,
				.r = Random(6.0, 12.0),
				.angle = Random(360_deg),
				.cw = RandomBool(),
				.color = HSV{ Random(50.0, 70.0), 0.4, 1.0 },
			};
			m_particles << particle;
		}

		for (int32 i = 0; i < 8; ++i)
		{
			const Vec2 velocoty = RandomVec2(28.0);
			Star star{
				.velocity = velocoty,
				.start = (velocoty + RandomVec2(2.0)),
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


## 51.10 (Sample) Effect Recursion
- An example of generating new effects within an effect
- To prevent infinite growth, generations that can generate new effects are limited

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/effect/10.mp4?raw=true" autoplay loop muted playsinline></video>

```cpp
# include <Siv3D.hpp>

// Spark state
struct Fire
{
	// Initial velocity
	Vec2 v0;

	// Hue offset
	double hueOffset;

	// Scaling
	double scale;

	// Time until explosion
	double nextFireSec;

	// Whether a child effect has been created after explosion
	bool hasChild = false;

	// Gravitational acceleration
	static constexpr Vec2 Gravity{ 0, 240 };
};

// Spark effect
struct Firework : IEffect
{
	// Number of sparks
	static constexpr int32 FireCount = 12;

	// To avoid circular reference, use reference or pointer when holding Effect in IEffect
	const Effect& m_parent;

	// Firework center coordinates
	Vec2 m_center;

	// Fire states
	std::array<Fire, FireCount> m_fires;

	// Generation number [0, 1, 2]
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

		if (m_n < 2) // If generation 0 or 1
		{
			for (auto& fire : m_fires)
			{
				if (!fire.hasChild && (fire.nextFireSec <= t))
				{
					// Create child effect
					const Vec2 pos = (m_center + fire.v0 * t + 0.5 * t * t * Fire::Gravity);
					m_parent.add<Firework>(m_parent, pos, (m_n + 1), fire.v0 + (t * Fire::Gravity));
					fire.hasChild = true;
				}
			}
		}

		return (t < 1.5);
	}
};

// Launch effect
struct FirstFirework : IEffect
{
	// To avoid circular reference, use reference or pointer when holding Effect in IEffect
	const Effect& m_parent;

	// Launch position
	Vec2 m_start;

	// Launch initial velocity
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
			// Create child effect when ending
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