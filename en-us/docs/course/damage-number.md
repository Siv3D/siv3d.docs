# Creating Original Damage Effects

| | | | |
|:--:|:--:|:--:|:--:|
| **Difficulty** | Intermediate | **Time** | 90 min+ |

In RPGs and action games, "damage numbers" pop out the instant an attack hits an enemy. There is a world of difference in player satisfaction between simply displaying a static number and crafting a polished effect with attention to motion and color.

In this course, you will programmatically control the movement, scaling, and color transitions of these numbers to create your very own "satisfying hit effects." If you feel your game looks a bit plain, let's solve that problem in just 90 minutes. Experience the joy of breathing life into your game screen through creative ideas and clever implementation.

???info "Reference Case: 'Xenoblade Chronicles 3'"
	<iframe width="100%" height="315" src="https://www.youtube.com/embed/0sB_VO-0gZ4?si=ODAqjSpp5_DJNxvF&rel=0&loop=1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

???info "Reference Case: 'Honkai: Star Rail'"
	<iframe width="100%" height="315" src="https://www.youtube.com/embed/sEheUxvfGDM?si=jfNaDs_FSoNpgkBK" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

???info "Reference Case: 'FINAL FANTASY XV'"
	<iframe width="100%" height="315" src="https://www.youtube.com/embed/JIYkBDHnPQw?si=bNdazqVbpEm9DFI-" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


## 1. Mockup Battle Scene
- First, we prepare a testing ground to experiment with the effects.
- We will draw a background, place an enemy character (Siv3D mascot "Siv3D-kun"), and implement a damage input pad where attack detection occurs upon clicking.
- At this stage, attacking only produces debug output in the top left of the screen; no damage is displayed over the enemy yet.
- We will use this as a base and gradually make the effects more impressive.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/1.png)

??? note "Code"
	```cpp
	# include <Siv3D.hpp>

	/// @brief Attack Attribute
	enum class AttackType
	{
		/// @brief Physical
		Physical,

		/// @brief Fire
		Fire,

		/// @brief Thunder
		Thunder,

		/// @brief Ice
		Ice,
	};

	/// @brief Information for a single hit
	struct Hit
	{
		/// @brief Attack Attribute
		AttackType type = AttackType::Physical;

		/// @brief Damage Amount
		int32 amount = 0;

		/// @brief Critical Hit
		bool isCritical = false;
	};

	/// @brief Damage Input Pad
	class DamagePad
	{
	public:

		/// @brief Pad Width (pixels)
		static constexpr int32 Width = 200;

		/// @brief Pad Height (pixels)
		static constexpr int32 Height = 150;

		/// @brief Constructor
		/// @param pos Position of the pad
		/// @param type Attack attribute of the pad
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief Update pad (issue hit on click) and draw
		/// @return Array of issued hit information. Empty array if none issued.
		Array<Hit> update() const
		{
			// Draw pad
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// Cursor position on the pad
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// Number of hits
			const int32 count = 1 + (cursorPos.x / 40);

			// Base damage amount
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// If cursor is over the pad
			if (m_rect.mouseOver())
			{
				// Hide cursor
				Cursor::RequestStyle(CursorStyle::Hidden);

				// Draw pointer UI on the pad
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// Array to store issued hit information
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // If left or right clicked
			{
				// Generate hit info for the number of hits and add to array
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // Only the first hit is critical on right click
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief Rectangular area of the pad
		Rect m_rect{ 0 };

		/// @brief Attack attribute of the pad
		AttackType m_type = AttackType::Physical;
	};

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Enemy texture
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// Damage input pads
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// Area where effects occur
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// Debug flags
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // Sky gradient
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // Ground gradient
			}

			// Draw enemy character
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// Input attack
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"Physical Damage \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"Fire Damage \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"Thunder Damage \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"Ice Damage \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// Debug UI
			{
				SimpleGUI::Slider(U"Effect Speed: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"Enemy Character", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"Target Range", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// Debug output hit list
				Print << hit.amount << (hit.isCritical ? U"(Critical)" : U"");
			}
		}
	}
	```


## 2. Attack and Damage Display
- We stop using the debug display and change it to draw numbers directly within the battle screen.
- Using the `Effect` feature, we spawn a damage amount effect at the location where the attack hit.
- Although it is still a simple appearance showing white numbers, the basic form of "results appearing over the enemy" is established.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/2.png)

??? note "Code"
	```cpp hl_lines="105-148 172-174 215-216 219-223"
	# include <Siv3D.hpp>

	/// @brief Attack Attribute
	enum class AttackType
	{
		/// @brief Physical
		Physical,

		/// @brief Fire
		Fire,

		/// @brief Thunder
		Thunder,

		/// @brief Ice
		Ice,
	};

	/// @brief Information for a single hit
	struct Hit
	{
		/// @brief Attack Attribute
		AttackType type = AttackType::Physical;

		/// @brief Damage Amount
		int32 amount = 0;

		/// @brief Critical Hit
		bool isCritical = false;
	};

	/// @brief Damage Input Pad
	class DamagePad
	{
	public:

		/// @brief Pad Width (pixels)
		static constexpr int32 Width = 200;

		/// @brief Pad Height (pixels)
		static constexpr int32 Height = 150;

		/// @brief Constructor
		/// @param pos Position of the pad
		/// @param type Attack attribute of the pad
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief Update pad (issue hit on click) and draw
		/// @return Array of issued hit information. Empty array if none issued.
		Array<Hit> update() const
		{
			// Draw pad
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// Cursor position on the pad
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// Number of hits
			const int32 count = 1 + (cursorPos.x / 40);

			// Base damage amount
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// If cursor is over the pad
			if (m_rect.mouseOver())
			{
				// Hide cursor
				Cursor::RequestStyle(CursorStyle::Hidden);

				// Draw pointer UI on the pad
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// Array to store issued hit information
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // If left or right clicked
			{
				// Generate hit info for the number of hits and add to array
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // Only the first hit is critical on right click
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief Rectangular area of the pad
		Rect m_rect{ 0 };

		/// @brief Attack attribute of the pad
		AttackType m_type = AttackType::Physical;
	};

	/// @brief Damage Display Effect
	struct DamageNumber : IEffect
	{
		/// @brief Effect lifetime (seconds)
		static constexpr double Lifetime = 0.8;

		/// @brief Hit information
		Hit m_hit;

		/// @brief Effect start position
		Vec2 m_start;

		/// @brief Font
		Font m_font;

		/// @brief Constructor
		/// @param hit Hit information
		/// @param start Effect start position
		/// @param font Font
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief Update and draw effect
		/// @param t Time elapsed since generation (seconds)
		/// @return true to continue effect, false to end
		bool update(const double t) override
		{
			// Base color
			ColorF baseColor{ 1.0, 1.0, 1.0 };

			// Text size
			double baseSize = 40.0;

			// Draw position
			Vec2 basePos = m_start;

			// Draw number
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Enemy texture
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// Damage input pads
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// Area where effects occur
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// Debug flags
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// Font for effect
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // Sky gradient
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // Ground gradient
			}

			// Draw enemy character
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// Input attack
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"Physical Damage \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"Fire Damage \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"Thunder Damage \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"Ice Damage \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// Debug UI
			{
				SimpleGUI::Slider(U"Effect Speed: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"Enemy Character", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"Target Range", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// Generate effect at a random position within the target range
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// Update and draw effects
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 3. Change Color by Attribute
- We change the color of the numbers based on the type of attack, such as "Red for Fire", "Blue for Ice".
- Organizing information by color is important for the player to intuitively understand "what attribute the current attack was." We add functional design to convey the game state, rather than just simple decoration.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/3.png)

??? note "Code"
	```cpp hl_lines="111-118 143-144"
	# include <Siv3D.hpp>

	/// @brief Attack Attribute
	enum class AttackType
	{
		/// @brief Physical
		Physical,

		/// @brief Fire
		Fire,

		/// @brief Thunder
		Thunder,

		/// @brief Ice
		Ice,
	};

	/// @brief Information for a single hit
	struct Hit
	{
		/// @brief Attack Attribute
		AttackType type = AttackType::Physical;

		/// @brief Damage Amount
		int32 amount = 0;

		/// @brief Critical Hit
		bool isCritical = false;
	};

	/// @brief Damage Input Pad
	class DamagePad
	{
	public:

		/// @brief Pad Width (pixels)
		static constexpr int32 Width = 200;

		/// @brief Pad Height (pixels)
		static constexpr int32 Height = 150;

		/// @brief Constructor
		/// @param pos Position of the pad
		/// @param type Attack attribute of the pad
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief Update pad (issue hit on click) and draw
		/// @return Array of issued hit information. Empty array if none issued.
		Array<Hit> update() const
		{
			// Draw pad
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// Cursor position on the pad
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// Number of hits
			const int32 count = 1 + (cursorPos.x / 40);

			// Base damage amount
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// If cursor is over the pad
			if (m_rect.mouseOver())
			{
				// Hide cursor
				Cursor::RequestStyle(CursorStyle::Hidden);

				// Draw pointer UI on the pad
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// Array to store issued hit information
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // If left or right clicked
			{
				// Generate hit info for the number of hits and add to array
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // Only the first hit is critical on right click
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief Rectangular area of the pad
		Rect m_rect{ 0 };

		/// @brief Attack attribute of the pad
		AttackType m_type = AttackType::Physical;
	};

	/// @brief Damage Display Effect
	struct DamageNumber : IEffect
	{
		/// @brief Effect lifetime (seconds)
		static constexpr double Lifetime = 0.8;

		/// @brief Base colors for each attribute
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief Hit information
		Hit m_hit;

		/// @brief Effect start position
		Vec2 m_start;

		/// @brief Font
		Font m_font;

		/// @brief Constructor
		/// @param hit Hit information
		/// @param start Effect start position
		/// @param font Font
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief Update and draw effect
		/// @param t Time elapsed since generation (seconds)
		/// @return true to continue effect, false to end
		bool update(const double t) override
		{
			// Base color according to attribute
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];

			// Text size
			double baseSize = 40.0;

			// Draw position
			Vec2 basePos = m_start;

			// Draw number
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Enemy texture
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// Damage input pads
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// Area where effects occur
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// Debug flags
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// Font for effect
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // Sky gradient
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // Ground gradient
			}

			// Draw enemy character
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// Input attack
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"Physical Damage \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"Fire Damage \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"Thunder Damage \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"Ice Damage \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// Debug UI
			{
				SimpleGUI::Slider(U"Effect Speed: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"Enemy Character", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"Target Range", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// Generate effect at a random position within the target range
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// Update and draw effects
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 4. Change Size by Damage Amount
- Having "weak attacks" and "special moves" look the same reduces the sense of satisfaction.
- We increase the font size of the numbers according to the number of digits in the damage (under 100, under 1000, or more).
- By creating a visual impact where "bigger numbers = something amazing happened," we reinforce the feedback when dealing massive damage.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/4.png)

??? note "Code"
	```cpp hl_lines="146-147"
	# include <Siv3D.hpp>

	/// @brief Attack Attribute
	enum class AttackType
	{
		/// @brief Physical
		Physical,

		/// @brief Fire
		Fire,

		/// @brief Thunder
		Thunder,

		/// @brief Ice
		Ice,
	};

	/// @brief Information for a single hit
	struct Hit
	{
		/// @brief Attack Attribute
		AttackType type = AttackType::Physical;

		/// @brief Damage Amount
		int32 amount = 0;

		/// @brief Critical Hit
		bool isCritical = false;
	};

	/// @brief Damage Input Pad
	class DamagePad
	{
	public:

		/// @brief Pad Width (pixels)
		static constexpr int32 Width = 200;

		/// @brief Pad Height (pixels)
		static constexpr int32 Height = 150;

		/// @brief Constructor
		/// @param pos Position of the pad
		/// @param type Attack attribute of the pad
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief Update pad (issue hit on click) and draw
		/// @return Array of issued hit information. Empty array if none issued.
		Array<Hit> update() const
		{
			// Draw pad
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// Cursor position on the pad
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// Number of hits
			const int32 count = 1 + (cursorPos.x / 40);

			// Base damage amount
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// If cursor is over the pad
			if (m_rect.mouseOver())
			{
				// Hide cursor
				Cursor::RequestStyle(CursorStyle::Hidden);

				// Draw pointer UI on the pad
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// Array to store issued hit information
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // If left or right clicked
			{
				// Generate hit info for the number of hits and add to array
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // Only the first hit is critical on right click
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief Rectangular area of the pad
		Rect m_rect{ 0 };

		/// @brief Attack attribute of the pad
		AttackType m_type = AttackType::Physical;
	};

	/// @brief Damage Display Effect
	struct DamageNumber : IEffect
	{
		/// @brief Effect lifetime (seconds)
		static constexpr double Lifetime = 0.8;

		/// @brief Base colors for each attribute
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief Hit information
		Hit m_hit;

		/// @brief Effect start position
		Vec2 m_start;

		/// @brief Font
		Font m_font;

		/// @brief Constructor
		/// @param hit Hit information
		/// @param start Effect start position
		/// @param font Font
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief Update and draw effect
		/// @param t Time elapsed since generation (seconds)
		/// @return true to continue effect, false to end
		bool update(const double t) override
		{
			// Base color according to attribute
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];

			// Base size according to damage amount
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;

			// Draw position
			Vec2 basePos = m_start;

			// Draw number
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Enemy texture
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// Damage input pads
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// Area where effects occur
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// Debug flags
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// Font for effect
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // Sky gradient
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // Ground gradient
			}

			// Draw enemy character
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// Input attack
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"Physical Damage \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"Fire Damage \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"Thunder Damage \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"Ice Damage \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// Debug UI
			{
				SimpleGUI::Slider(U"Effect Speed: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"Enemy Character", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"Target Range", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// Generate effect at a random position within the target range
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// Update and draw effects
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 5. Add Outline to Text
- In bright backgrounds or places where effects overlap, numbers can blend in and become hard to see.
- By drawing an outline around the numbers, we ensure they are clearly legible on any background. This not only improves visibility but also increases the text's presence, making the screen look more game-like.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/5.png)

??? note "Code"
	```cpp hl_lines="155-156"
	# include <Siv3D.hpp>

	/// @brief Attack Attribute
	enum class AttackType
	{
		/// @brief Physical
		Physical,

		/// @brief Fire
		Fire,

		/// @brief Thunder
		Thunder,

		/// @brief Ice
		Ice,
	};

	/// @brief Information for a single hit
	struct Hit
	{
		/// @brief Attack Attribute
		AttackType type = AttackType::Physical;

		/// @brief Damage Amount
		int32 amount = 0;

		/// @brief Critical Hit
		bool isCritical = false;
	};

	/// @brief Damage Input Pad
	class DamagePad
	{
	public:

		/// @brief Pad Width (pixels)
		static constexpr int32 Width = 200;

		/// @brief Pad Height (pixels)
		static constexpr int32 Height = 150;

		/// @brief Constructor
		/// @param pos Position of the pad
		/// @param type Attack attribute of the pad
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief Update pad (issue hit on click) and draw
		/// @return Array of issued hit information. Empty array if none issued.
		Array<Hit> update() const
		{
			// Draw pad
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// Cursor position on the pad
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// Number of hits
			const int32 count = 1 + (cursorPos.x / 40);

			// Base damage amount
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// If cursor is over the pad
			if (m_rect.mouseOver())
			{
				// Hide cursor
				Cursor::RequestStyle(CursorStyle::Hidden);

				// Draw pointer UI on the pad
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// Array to store issued hit information
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // If left or right clicked
			{
				// Generate hit info for the number of hits and add to array
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // Only the first hit is critical on right click
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief Rectangular area of the pad
		Rect m_rect{ 0 };

		/// @brief Attack attribute of the pad
		AttackType m_type = AttackType::Physical;
	};

	/// @brief Damage Display Effect
	struct DamageNumber : IEffect
	{
		/// @brief Effect lifetime (seconds)
		static constexpr double Lifetime = 0.8;

		/// @brief Base colors for each attribute
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief Hit information
		Hit m_hit;

		/// @brief Effect start position
		Vec2 m_start;

		/// @brief Font
		Font m_font;

		/// @brief Constructor
		/// @param hit Hit information
		/// @param start Effect start position
		/// @param font Font
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief Update and draw effect
		/// @param t Time elapsed since generation (seconds)
		/// @return true to continue effect, false to end
		bool update(const double t) override
		{
			// Base color according to attribute
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];

			// Base size according to damage amount
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;

			// Draw position
			Vec2 basePos = m_start;

			// Draw number
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			// Draw number outline
			m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Enemy texture
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// Damage input pads
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// Area where effects occur
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// Debug flags
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// Font for effect
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // Sky gradient
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // Ground gradient
			}

			// Draw enemy character
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// Input attack
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"Physical Damage \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"Fire Damage \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"Thunder Damage \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"Ice Damage \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// Debug UI
			{
				SimpleGUI::Slider(U"Effect Speed: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"Enemy Character", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"Target Range", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// Generate effect at a random position within the target range
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// Update and draw effects
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 6. Italicize Text
- As an option, change the font to italics.
- Slanted text conveys a sense of speed and momentum. This is a technique to give a dynamic impression to the screen in high-action games or speedy combat scenes.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/6.png)

??? note "Code"
	```cpp hl_lines="185"
	# include <Siv3D.hpp>

	/// @brief Attack Attribute
	enum class AttackType
	{
		/// @brief Physical
		Physical,

		/// @brief Fire
		Fire,

		/// @brief Thunder
		Thunder,

		/// @brief Ice
		Ice,
	};

	/// @brief Information for a single hit
	struct Hit
	{
		/// @brief Attack Attribute
		AttackType type = AttackType::Physical;

		/// @brief Damage Amount
		int32 amount = 0;

		/// @brief Critical Hit
		bool isCritical = false;
	};

	/// @brief Damage Input Pad
	class DamagePad
	{
	public:

		/// @brief Pad Width (pixels)
		static constexpr int32 Width = 200;

		/// @brief Pad Height (pixels)
		static constexpr int32 Height = 150;

		/// @brief Constructor
		/// @param pos Position of the pad
		/// @param type Attack attribute of the pad
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief Update pad (issue hit on click) and draw
		/// @return Array of issued hit information. Empty array if none issued.
		Array<Hit> update() const
		{
			// Draw pad
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// Cursor position on the pad
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// Number of hits
			const int32 count = 1 + (cursorPos.x / 40);

			// Base damage amount
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// If cursor is over the pad
			if (m_rect.mouseOver())
			{
				// Hide cursor
				Cursor::RequestStyle(CursorStyle::Hidden);

				// Draw pointer UI on the pad
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// Array to store issued hit information
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // If left or right clicked
			{
				// Generate hit info for the number of hits and add to array
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // Only the first hit is critical on right click
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief Rectangular area of the pad
		Rect m_rect{ 0 };

		/// @brief Attack attribute of the pad
		AttackType m_type = AttackType::Physical;
	};

	/// @brief Damage Display Effect
	struct DamageNumber : IEffect
	{
		/// @brief Effect lifetime (seconds)
		static constexpr double Lifetime = 0.8;

		/// @brief Base colors for each attribute
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief Hit information
		Hit m_hit;

		/// @brief Effect start position
		Vec2 m_start;

		/// @brief Font
		Font m_font;

		/// @brief Constructor
		/// @param hit Hit information
		/// @param start Effect start position
		/// @param font Font
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief Update and draw effect
		/// @param t Time elapsed since generation (seconds)
		/// @return true to continue effect, false to end
		bool update(const double t) override
		{
			// Base color according to attribute
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];

			// Base size according to damage amount
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;

			// Draw position
			Vec2 basePos = m_start;

			// Draw number
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			// Draw number outline
			m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Enemy texture
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// Damage input pads
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// Area where effects occur
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// Debug flags
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// Font for effect
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // Sky gradient
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // Ground gradient
			}

			// Draw enemy character
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// Input attack
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"Physical Damage \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"Fire Damage \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"Thunder Damage \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"Ice Damage \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// Debug UI
			{
				SimpleGUI::Slider(U"Effect Speed: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"Enemy Character", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"Target Range", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// Generate effect at a random position within the target range
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// Update and draw effects
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 7. Fade Out Effect
- We add animation so that numbers do not vanish abruptly but fade out gently.
- After some time has passed since appearance, we gradually increase transparency and slowly move it upwards.
- By letting it disappear with a lingering effect, abruptness is eliminated, resulting in a natural look.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/7.png)

??? note "Code"
	```cpp hl_lines="145-149 156-160"
	# include <Siv3D.hpp>

	/// @brief Attack Attribute
	enum class AttackType
	{
		/// @brief Physical
		Physical,

		/// @brief Fire
		Fire,

		/// @brief Thunder
		Thunder,

		/// @brief Ice
		Ice,
	};

	/// @brief Information for a single hit
	struct Hit
	{
		/// @brief Attack Attribute
		AttackType type = AttackType::Physical;

		/// @brief Damage Amount
		int32 amount = 0;

		/// @brief Critical Hit
		bool isCritical = false;
	};

	/// @brief Damage Input Pad
	class DamagePad
	{
	public:

		/// @brief Pad Width (pixels)
		static constexpr int32 Width = 200;

		/// @brief Pad Height (pixels)
		static constexpr int32 Height = 150;

		/// @brief Constructor
		/// @param pos Position of the pad
		/// @param type Attack attribute of the pad
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief Update pad (issue hit on click) and draw
		/// @return Array of issued hit information. Empty array if none issued.
		Array<Hit> update() const
		{
			// Draw pad
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// Cursor position on the pad
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// Number of hits
			const int32 count = 1 + (cursorPos.x / 40);

			// Base damage amount
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// If cursor is over the pad
			if (m_rect.mouseOver())
			{
				// Hide cursor
				Cursor::RequestStyle(CursorStyle::Hidden);

				// Draw pointer UI on the pad
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// Array to store issued hit information
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // If left or right clicked
			{
				// Generate hit info for the number of hits and add to array
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // Only the first hit is critical on right click
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief Rectangular area of the pad
		Rect m_rect{ 0 };

		/// @brief Attack attribute of the pad
		AttackType m_type = AttackType::Physical;
	};

	/// @brief Damage Display Effect
	struct DamageNumber : IEffect
	{
		/// @brief Effect lifetime (seconds)
		static constexpr double Lifetime = 0.8;

		/// @brief Base colors for each attribute
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief Hit information
		Hit m_hit;

		/// @brief Effect start position
		Vec2 m_start;

		/// @brief Font
		Font m_font;

		/// @brief Constructor
		/// @param hit Hit information
		/// @param start Effect start position
		/// @param font Font
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief Update and draw effect
		/// @param t Time elapsed since generation (seconds)
		/// @return true to continue effect, false to end
		bool update(const double t) override
		{
			// Base color according to attribute
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];
			// Fade out at the end
			{
				const double t2 = Max((t - (Lifetime - 0.2)), 0.0) / 0.2; // Change 0.0 -> 1.0 from 0.2s before end to Lifetime
				baseColor.a *= (1.0 - t2 * t2);
			}

			// Base size according to damage amount
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;

			// Draw position
			Vec2 basePos = m_start;
			// Rise from midway
			{
				const double t2 = Max((t - 0.2), 0.0); // Time elapsed since 0.2s
				basePos.y -= (120.0 * t2 * t2);
			}

			// Draw number
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			// Draw number outline
			m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Enemy texture
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// Damage input pads
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// Area where effects occur
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// Debug flags
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// Font for effect
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // Sky gradient
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // Ground gradient
			}

			// Draw enemy character
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// Input attack
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"Physical Damage \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"Fire Damage \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"Thunder Damage \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"Ice Damage \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// Debug UI
			{
				SimpleGUI::Slider(U"Effect Speed: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"Enemy Character", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"Target Range", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// Generate effect at a random position within the target range
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// Update and draw effects
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```


## 8. Appearance Effect
- We strengthen the impact at the moment the number appears.
- Immediately after appearing, we make the size slightly larger and saturate (glow) the color, adding motion that immediately returns to its original size and color.
- This creates a sense of impact upon hitting, improving the feedback when pressing the attack command.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/8.png)

??? note "Code"
	```cpp hl_lines="145-150 159-165"
	# include <Siv3D.hpp>

	/// @brief Attack Attribute
	enum class AttackType
	{
		/// @brief Physical
		Physical,

		/// @brief Fire
		Fire,

		/// @brief Thunder
		Thunder,

		/// @brief Ice
		Ice,
	};

	/// @brief Information for a single hit
	struct Hit
	{
		/// @brief Attack Attribute
		AttackType type = AttackType::Physical;

		/// @brief Damage Amount
		int32 amount = 0;

		/// @brief Critical Hit
		bool isCritical = false;
	};

	/// @brief Damage Input Pad
	class DamagePad
	{
	public:

		/// @brief Pad Width (pixels)
		static constexpr int32 Width = 200;

		/// @brief Pad Height (pixels)
		static constexpr int32 Height = 150;

		/// @brief Constructor
		/// @param pos Position of the pad
		/// @param type Attack attribute of the pad
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief Update pad (issue hit on click) and draw
		/// @return Array of issued hit information. Empty array if none issued.
		Array<Hit> update() const
		{
			// Draw pad
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// Cursor position on the pad
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// Number of hits
			const int32 count = 1 + (cursorPos.x / 40);

			// Base damage amount
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// If cursor is over the pad
			if (m_rect.mouseOver())
			{
				// Hide cursor
				Cursor::RequestStyle(CursorStyle::Hidden);

				// Draw pointer UI on the pad
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// Array to store issued hit information
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // If left or right clicked
			{
				// Generate hit info for the number of hits and add to array
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // Only the first hit is critical on right click
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief Rectangular area of the pad
		Rect m_rect{ 0 };

		/// @brief Attack attribute of the pad
		AttackType m_type = AttackType::Physical;
	};

	/// @brief Damage Display Effect
	struct DamageNumber : IEffect
	{
		/// @brief Effect lifetime (seconds)
		static constexpr double Lifetime = 0.8;

		/// @brief Base colors for each attribute
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief Hit information
		Hit m_hit;

		/// @brief Effect start position
		Vec2 m_start;

		/// @brief Font
		Font m_font;

		/// @brief Constructor
		/// @param hit Hit information
		/// @param start Effect start position
		/// @param font Font
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief Update and draw effect
		/// @param t Time elapsed since generation (seconds)
		/// @return true to continue effect, false to end
		bool update(const double t) override
		{
			// Base color according to attribute
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];
			// Saturation of color upon appearance
			{
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // Change 1.0 -> 0.0 from 0.0s to 0.1s
				const double add = (0.5 * t2 * t2);
				baseColor += ColorF{ add, add, add }; // Bright immediately after appearing, gradually converges to base color
			}
			// Fade out at the end
			{
				const double t2 = Max((t - (Lifetime - 0.2)), 0.0) / 0.2; // Change 0.0 -> 1.0 from 0.2s before end to Lifetime
				baseColor.a *= (1.0 - t2 * t2);
			}

			// Base size according to damage amount
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;
			// Size change upon appearance
			{
				// Extra size based on digits
				const double extraSize = (m_hit.amount < 100) ? 20.0 : (m_hit.amount < 1000) ? 40.0 : 80.0;
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // Change 1.0 -> 0.0 from 0.0s to 0.1s
				baseSize += (extraSize * t2 * t2); // Large immediately after appearing, gradually converges to base size
			}

			// Draw position
			Vec2 basePos = m_start;
			// Rise from midway
			{
				const double t2 = Max((t - 0.2), 0.0); // Time elapsed since 0.2s
				basePos.y -= (120.0 * t2 * t2);
			}

			// Draw number
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			// Draw number outline
			m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Enemy texture
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// Damage input pads
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// Area where effects occur
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// Debug flags
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// Font for effect
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(6);
		Effect effectManager;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // Sky gradient
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // Ground gradient
			}

			// Draw enemy character
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// Input attack
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"Physical Damage \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"Fire Damage \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"Thunder Damage \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"Ice Damage \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// Debug UI
			{
				SimpleGUI::Slider(U"Effect Speed: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"Enemy Character", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"Target Range", Vec2{ 260, 660 }, 200);
			}

			for (const Hit& hit : hits)
			{
				// Generate effect at a random position within the target range
				effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// Update and draw effects
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 9. Display Simultaneous Damage with Delay
- We prevent numbers from overlapping and crushing each other when a large number of hits occur at once.
- Damage information is temporarily stored in a queue and displayed sequentially at fixed intervals (e.g., every 0.04 seconds).
- This visualizes the rhythm of continuous hits and creates a sense of exhilaration in combos.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/9.png)

??? note "Code"
	```cpp hl_lines="211-216 255 258-261 264-282"
	# include <Siv3D.hpp>

	/// @brief Attack Attribute
	enum class AttackType
	{
		/// @brief Physical
		Physical,

		/// @brief Fire
		Fire,

		/// @brief Thunder
		Thunder,

		/// @brief Ice
		Ice,
	};

	/// @brief Information for a single hit
	struct Hit
	{
		/// @brief Attack Attribute
		AttackType type = AttackType::Physical;

		/// @brief Damage Amount
		int32 amount = 0;

		/// @brief Critical Hit
		bool isCritical = false;
	};

	/// @brief Damage Input Pad
	class DamagePad
	{
	public:

		/// @brief Pad Width (pixels)
		static constexpr int32 Width = 200;

		/// @brief Pad Height (pixels)
		static constexpr int32 Height = 150;

		/// @brief Constructor
		/// @param pos Position of the pad
		/// @param type Attack attribute of the pad
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief Update pad (issue hit on click) and draw
		/// @return Array of issued hit information. Empty array if none issued.
		Array<Hit> update() const
		{
			// Draw pad
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// Cursor position on the pad
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// Number of hits
			const int32 count = 1 + (cursorPos.x / 40);

			// Base damage amount
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// If cursor is over the pad
			if (m_rect.mouseOver())
			{
				// Hide cursor
				Cursor::RequestStyle(CursorStyle::Hidden);

				// Draw pointer UI on the pad
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// Array to store issued hit information
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // If left or right clicked
			{
				// Generate hit info for the number of hits and add to array
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // Only the first hit is critical on right click
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief Rectangular area of the pad
		Rect m_rect{ 0 };

		/// @brief Attack attribute of the pad
		AttackType m_type = AttackType::Physical;
	};

	/// @brief Damage Display Effect
	struct DamageNumber : IEffect
	{
		/// @brief Effect lifetime (seconds)
		static constexpr double Lifetime = 0.8;

		/// @brief Base colors for each attribute
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief Hit information
		Hit m_hit;

		/// @brief Effect start position
		Vec2 m_start;

		/// @brief Font
		Font m_font;

		/// @brief Constructor
		/// @param hit Hit information
		/// @param start Effect start position
		/// @param font Font
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief Update and draw effect
		/// @param t Time elapsed since generation (seconds)
		/// @return true to continue effect, false to end
		bool update(const double t) override
		{
			// Base color according to attribute
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];
			// Saturation of color upon appearance
			{
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // Change 1.0 -> 0.0 from 0.0s to 0.1s
				const double add = (0.5 * t2 * t2);
				baseColor += ColorF{ add, add, add }; // Bright immediately after appearing, gradually converges to base color
			}
			// Fade out at the end
			{
				const double t2 = Max((t - (Lifetime - 0.2)), 0.0) / 0.2; // Change 0.0 -> 1.0 from 0.2s before end to Lifetime
				baseColor.a *= (1.0 - t2 * t2);
			}

			// Base size according to damage amount
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;
			// Size change upon appearance
			{
				// Extra size based on digits
				const double extraSize = (m_hit.amount < 100) ? 20.0 : (m_hit.amount < 1000) ? 40.0 : 80.0;
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // Change 1.0 -> 0.0 from 0.0s to 0.1s
				baseSize += (extraSize * t2 * t2); // Large immediately after appearing, gradually converges to base size
			}

			// Draw position
			Vec2 basePos = m_start;
			// Rise from midway
			{
				const double t2 = Max((t - 0.2), 0.0); // Time elapsed since 0.2s
				basePos.y -= (120.0 * t2 * t2);
			}

			// Draw number
			m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);

			// Draw number outline
			m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });

			return (t < Lifetime);
		}
	};

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Enemy texture
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// Damage input pads
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// Area where effects occur
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// Debug flags
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// Font for effect
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(6);
		Effect effectManager;

		// Hit info queue
		std::queue<Hit> hitQueue;
		// Minimum interval for issuing hit effects (seconds)
		constexpr double hitInterval = 0.04;
		// Time elapsed since the last hit effect was issued (seconds)
		double accumulatedTime = 0.0;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // Sky gradient
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // Ground gradient
			}

			// Draw enemy character
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// Input attack
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"Physical Damage \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"Fire Damage \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"Thunder Damage \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"Ice Damage \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// Debug UI
			{
				SimpleGUI::Slider(U"Effect Speed: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"Enemy Character", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"Target Range", Vec2{ 260, 660 }, 200);
			}

			// Add issued hit info to queue
			for (const Hit& hit : hits)
			{
				hitQueue.push(hit);
				accumulatedTime = hitInterval; // Pad time so new hits trigger effect immediately
				// Generate effect at a random position within the target range
				//effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// Process queue and issue effects
			{
				// Accumulate time
				accumulatedTime += (Scene::DeltaTime() * effectSpeed);

				// Issue effects from the queue at fixed intervals
				if (hitInterval <= accumulatedTime)
				{
					if (not hitQueue.empty())
					{
						// Get hit info from front of queue
						const Hit hit = hitQueue.front(); hitQueue.pop();
						// Generate effect at a random position within the target range
						effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
						// Reduce accumulated time
						accumulatedTime -= hitInterval;
					}
				}
			}

			// Update and draw effects
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 10. Add Gradients
- We stop using solid color fills and apply a gradient where the top of the text is bright and the bottom is darker.
- By customizing the text drawing process and coloring each character carefully, we create a rich texture like a professional game.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/10.png)

??? note "Code"
	```cpp hl_lines="175-176 178-179 182-184 187-189 194-216"
	# include <Siv3D.hpp>

	/// @brief Attack Attribute
	enum class AttackType
	{
		/// @brief Physical
		Physical,

		/// @brief Fire
		Fire,

		/// @brief Thunder
		Thunder,

		/// @brief Ice
		Ice,
	};

	/// @brief Information for a single hit
	struct Hit
	{
		/// @brief Attack Attribute
		AttackType type = AttackType::Physical;

		/// @brief Damage Amount
		int32 amount = 0;

		/// @brief Critical Hit
		bool isCritical = false;
	};

	/// @brief Damage Input Pad
	class DamagePad
	{
	public:

		/// @brief Pad Width (pixels)
		static constexpr int32 Width = 200;

		/// @brief Pad Height (pixels)
		static constexpr int32 Height = 150;

		/// @brief Constructor
		/// @param pos Position of the pad
		/// @param type Attack attribute of the pad
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief Update pad (issue hit on click) and draw
		/// @return Array of issued hit information. Empty array if none issued.
		Array<Hit> update() const
		{
			// Draw pad
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// Cursor position on the pad
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// Number of hits
			const int32 count = 1 + (cursorPos.x / 40);

			// Base damage amount
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// If cursor is over the pad
			if (m_rect.mouseOver())
			{
				// Hide cursor
				Cursor::RequestStyle(CursorStyle::Hidden);

				// Draw pointer UI on the pad
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// Array to store issued hit information
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // If left or right clicked
			{
				// Generate hit info for the number of hits and add to array
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // Only the first hit is critical on right click
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief Rectangular area of the pad
		Rect m_rect{ 0 };

		/// @brief Attack attribute of the pad
		AttackType m_type = AttackType::Physical;
	};

	/// @brief Damage Display Effect
	struct DamageNumber : IEffect
	{
		/// @brief Effect lifetime (seconds)
		static constexpr double Lifetime = 0.8;

		/// @brief Base colors for each attribute
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief Hit information
		Hit m_hit;

		/// @brief Effect start position
		Vec2 m_start;

		/// @brief Font
		Font m_font;

		/// @brief Constructor
		/// @param hit Hit information
		/// @param start Effect start position
		/// @param font Font
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief Update and draw effect
		/// @param t Time elapsed since generation (seconds)
		/// @return true to continue effect, false to end
		bool update(const double t) override
		{
			// Base color according to attribute
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];
			// Saturation of color upon appearance
			{
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // Change 1.0 -> 0.0 from 0.0s to 0.1s
				const double add = (0.5 * t2 * t2);
				baseColor += ColorF{ add, add, add }; // Bright immediately after appearing, gradually converges to base color
			}
			// Fade out at the end
			{
				const double t2 = Max((t - (Lifetime - 0.2)), 0.0) / 0.2; // Change 0.0 -> 1.0 from 0.2s before end to Lifetime
				baseColor.a *= (1.0 - t2 * t2);
			}

			// Base size according to damage amount
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;
			// Size change upon appearance
			{
				// Extra size based on digits
				const double extraSize = (m_hit.amount < 100) ? 20.0 : (m_hit.amount < 1000) ? 40.0 : 80.0;
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // Change 1.0 -> 0.0 from 0.0s to 0.1s
				baseSize += (extraSize * t2 * t2); // Large immediately after appearing, gradually converges to base size
			}

			// Draw position
			Vec2 basePos = m_start;
			// Rise from midway
			{
				const double t2 = Max((t - 0.2), 0.0); // Time elapsed since 0.2s
				basePos.y -= (120.0 * t2 * t2);
			}

			// Convert number to string
			const String text = Format(m_hit.amount);

			// String position offset (for centering)
			const Vec2 offset = m_font(text).region(baseSize).size / 2;

			// Draw number
			//m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);
			// Draw with vertical gradient: Intense White -> baseColor
			DrawGlyphs(m_font, TextStyle::Default(), text, baseSize, (basePos - offset), ColorF{ 1.2, baseColor.a }, baseColor, t);

			// Draw number outline
			//m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });
			// Draw outline without gradient
			DrawGlyphs(m_font, TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), text, baseSize, (basePos - offset), ColorF{ 0.0, 0.0 }, ColorF{ 0.0, 0.0 }, t);

			return (t < Lifetime);
		}

		// Low-level text drawing: draws character textures one by one
		static void DrawGlyphs(const Font& font, const TextStyle& textStyle, const String& text, const double fontSize, const Vec2& basePos,
			const ColorF& topColor, const ColorF& bottomColor, const double t)
		{
			// Various settings
			const Array<Glyph> glyphs = font.getGlyphs(text);
			const double scale = (fontSize / font.fontSize());
			const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method(), textStyle.type) };
			Graphics2D::SetMSDFParameters(textStyle);

			Vec2 penPos{ basePos };

			// Loop to draw character textures
			for (const auto& glyph : glyphs)
			{
				// Draw character texture
				glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale)),
					Arg::top = topColor, Arg::bottom = bottomColor); // Draw with vertical gradient

				// Advance pen X position by character width
				penPos.x += (glyph.xAdvance * scale);
			}
		}
	};

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Enemy texture
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// Damage input pads
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// Area where effects occur
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// Debug flags
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// Font for effect
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(6);
		Effect effectManager;

		// Hit info queue
		std::queue<Hit> hitQueue;
		// Minimum interval for issuing hit effects (seconds)
		constexpr double hitInterval = 0.04;
		// Time elapsed since the last hit effect was issued (seconds)
		double accumulatedTime = 0.0;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // Sky gradient
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // Ground gradient
			}

			// Draw enemy character
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// Input attack
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"Physical Damage \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"Fire Damage \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"Thunder Damage \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"Ice Damage \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// Debug UI
			{
				SimpleGUI::Slider(U"Effect Speed: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"Enemy Character", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"Target Range", Vec2{ 260, 660 }, 200);
			}

			// Add issued hit info to queue
			for (const Hit& hit : hits)
			{
				hitQueue.push(hit);
				accumulatedTime = hitInterval; // Pad time so new hits trigger effect immediately
				// Generate effect at a random position within the target range
				//effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// Process queue and issue effects
			{
				// Accumulate time
				accumulatedTime += (Scene::DeltaTime() * effectSpeed);

				// Issue effects from the queue at fixed intervals
				if (hitInterval <= accumulatedTime)
				{
					if (not hitQueue.empty())
					{
						// Get hit info from front of queue
						const Hit hit = hitQueue.front(); hitQueue.pop();
						// Generate effect at a random position within the target range
						effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
						// Reduce accumulated time
						accumulatedTime -= hitInterval;
					}
				}
			}

			// Update and draw effects
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 11. Character-level Motion
- When massive damage (3 digits or more) occurs, we apply motion to each character individually.
- By adding a wave-like motion with staggered timing for each character, numbers with many digits will appear to move dynamically on the screen.
- This conveys to the player through the intensity of the movement that special damage has occurred.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/study/2025/23/11.png)

??? note "Code"
	```cpp hl_lines="207 209-214 217 222"
	# include <Siv3D.hpp>

	/// @brief Attack Attribute
	enum class AttackType
	{
		/// @brief Physical
		Physical,

		/// @brief Fire
		Fire,

		/// @brief Thunder
		Thunder,

		/// @brief Ice
		Ice,
	};

	/// @brief Information for a single hit
	struct Hit
	{
		/// @brief Attack Attribute
		AttackType type = AttackType::Physical;

		/// @brief Damage Amount
		int32 amount = 0;

		/// @brief Critical Hit
		bool isCritical = false;
	};

	/// @brief Damage Input Pad
	class DamagePad
	{
	public:

		/// @brief Pad Width (pixels)
		static constexpr int32 Width = 200;

		/// @brief Pad Height (pixels)
		static constexpr int32 Height = 150;

		/// @brief Constructor
		/// @param pos Position of the pad
		/// @param type Attack attribute of the pad
		DamagePad(const Point& pos, AttackType type)
			: m_rect{ pos, Width, Height }
			, m_type{ type } {}

		/// @brief Update pad (issue hit on click) and draw
		/// @return Array of issued hit information. Empty array if none issued.
		Array<Hit> update() const
		{
			// Draw pad
			m_rect.draw(ColorF{ 0.0, 0.3 }).drawFrame(1.5, ColorF{ 0.6 });

			// Cursor position on the pad
			const Point cursorPos = (Cursor::Pos() - m_rect.pos);

			// Number of hits
			const int32 count = 1 + (cursorPos.x / 40);

			// Base damage amount
			const double baseDamage = Math::Pow(10, (1.0 + cursorPos.y / 50.0));

			// If cursor is over the pad
			if (m_rect.mouseOver())
			{
				// Hide cursor
				Cursor::RequestStyle(CursorStyle::Hidden);

				// Draw pointer UI on the pad
				for (int32 i = 0; i < count; ++i)
				{
					Cursor::Pos().asCircle(3 + i * 3).drawFrame(1.5, ColorF{ 1.0, 0.5, 0.0 });
				}
			}

			// Array to store issued hit information
			Array<Hit> hits;

			if (m_rect.leftClicked() || m_rect.rightClicked()) // If left or right clicked
			{
				// Generate hit info for the number of hits and add to array
				for (int32 i = 0; i < count; ++i)
				{
					const bool isCritical = (m_rect.rightClicked()) && (i == 0); // Only the first hit is critical on right click
					const int32 damage = static_cast<int32>(baseDamage * Random(0.9, 1.1) * (isCritical ? 3 : 1));
					hits << Hit{ m_type, damage, isCritical };
				}
			}

			return hits;
		}

	private:

		/// @brief Rectangular area of the pad
		Rect m_rect{ 0 };

		/// @brief Attack attribute of the pad
		AttackType m_type = AttackType::Physical;
	};

	/// @brief Damage Display Effect
	struct DamageNumber : IEffect
	{
		/// @brief Effect lifetime (seconds)
		static constexpr double Lifetime = 0.8;

		/// @brief Base colors for each attribute
		static constexpr ColorF BaseColors[4] =
		{
			ColorF{ 1.0, 1.0, 1.0 }, // Physical
			ColorF{ 0.9, 0.2, 0.0 }, // Fire
			ColorF{ 0.9, 0.4, 0.9 }, // Thunder
			ColorF{ 0.5, 0.8, 1.0 }, // Ice
		};

		/// @brief Hit information
		Hit m_hit;

		/// @brief Effect start position
		Vec2 m_start;

		/// @brief Font
		Font m_font;

		/// @brief Constructor
		/// @param hit Hit information
		/// @param start Effect start position
		/// @param font Font
		DamageNumber(const Hit& hit, const Vec2& start, const Font& font)
			: m_hit{ hit }
			, m_start{ start }
			, m_font{ font } {}

		/// @brief Update and draw effect
		/// @param t Time elapsed since generation (seconds)
		/// @return true to continue effect, false to end
		bool update(const double t) override
		{
			// Base color according to attribute
			ColorF baseColor = BaseColors[FromEnum(m_hit.type)];
			// Saturation of color upon appearance
			{
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // Change 1.0 -> 0.0 from 0.0s to 0.1s
				const double add = (0.5 * t2 * t2);
				baseColor += ColorF{ add, add, add }; // Bright immediately after appearing, gradually converges to base color
			}
			// Fade out at the end
			{
				const double t2 = Max((t - (Lifetime - 0.2)), 0.0) / 0.2; // Change 0.0 -> 1.0 from 0.2s before end to Lifetime
				baseColor.a *= (1.0 - t2 * t2);
			}

			// Base size according to damage amount
			double baseSize = (m_hit.amount < 100) ? 40 : (m_hit.amount < 1000) ? 48 : 56;
			// Size change upon appearance
			{
				// Extra size based on digits
				const double extraSize = (m_hit.amount < 100) ? 20.0 : (m_hit.amount < 1000) ? 40.0 : 80.0;
				const double t2 = Max((0.1 - t), 0.0) / 0.1; // Change 1.0 -> 0.0 from 0.0s to 0.1s
				baseSize += (extraSize * t2 * t2); // Large immediately after appearing, gradually converges to base size
			}

			// Draw position
			Vec2 basePos = m_start;
			// Rise from midway
			{
				const double t2 = Max((t - 0.2), 0.0); // Time elapsed since 0.2s
				basePos.y -= (120.0 * t2 * t2);
			}

			// Convert number to string
			const String text = Format(m_hit.amount);

			// String position offset (for centering)
			const Vec2 offset = m_font(text).region(baseSize).size / 2;

			// Draw number
			//m_font(m_hit.amount).drawAt(baseSize, basePos, baseColor);
			// Draw with vertical gradient: Intense White -> baseColor
			DrawGlyphs(m_font, TextStyle::Default(), text, baseSize, (basePos - offset), ColorF{ 1.2, baseColor.a }, baseColor, t);

			// Draw number outline
			//m_font(m_hit.amount).drawAt(TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), baseSize, basePos, ColorF{ 0.0, 0.0 });
			// Draw outline without gradient
			DrawGlyphs(m_font, TextStyle::Outline(0.0, 0.16, ColorF{ 0.0, baseColor.a }), text, baseSize, (basePos - offset), ColorF{ 0.0, 0.0 }, ColorF{ 0.0, 0.0 }, t);

			return (t < Lifetime);
		}

		// Low-level text drawing: draws character textures one by one
		static void DrawGlyphs(const Font& font, const TextStyle& textStyle, const String& text, const double fontSize, const Vec2& basePos,
			const ColorF& topColor, const ColorF& bottomColor, const double t)
		{
			// Various settings
			const Array<Glyph> glyphs = font.getGlyphs(text);
			const double scale = (fontSize / font.fontSize());
			const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method(), textStyle.type) };
			Graphics2D::SetMSDFParameters(textStyle);

			Vec2 penPos{ basePos };

			// Loop to draw character textures
			for (int32 index = 0; const auto& glyph : glyphs) // index is the character index
			{
				const double waveIndex = t / 0.05 - 0.5; // Progress of wave (which character index is at the peak)
				Vec2 offset{ 0,0 };
				if (2 < text.size()) // If massive damage (3 digits or more), apply wave animation
				{
					offset.y = Max(1.0 - AbsDiff<double>(waveIndex, index), 0.0) * -6.0 * scale; // Closer to peak wave means higher Y position
				}

				// Draw character texture
				glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale) + offset),
					Arg::top = topColor, Arg::bottom = bottomColor); // Draw with vertical gradient

				// Advance pen X position by character width
				penPos.x += (glyph.xAdvance * scale);
				++index;
			}
		}
	};

	void Main()
	{
		// Set window size
		Window::Resize(1280, 720);

		// Enemy texture
		const Texture enemyTexture{ U"example/siv3d-kun.png" };

		// Damage input pads
		const DamagePad PhysicalButton{ Point{ 40, 80 }, AttackType::Physical };
		const DamagePad FireButton{ Point{ 260, 80 }, AttackType::Fire };
		const DamagePad ThunderButton{ Point{ 40, 280 }, AttackType::Thunder };
		const DamagePad IceButton{ Point{ 260, 280 }, AttackType::Ice };

		// Area where effects occur
		const Quad targetQuad{ 850, 120, 930, 120, 1100, 560, 750, 460 };

		// Debug flags
		bool showEnemy = true;
		bool showTargetQuad = false;
		double effectSpeed = 1.0;

		// Font for effect
		const Font font = Font{ FontMethod::MSDF, 36, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(6);
		Effect effectManager;

		// Hit info queue
		std::queue<Hit> hitQueue;
		// Minimum interval for issuing hit effects (seconds)
		constexpr double hitInterval = 0.04;
		// Time elapsed since the last hit effect was issued (seconds)
		double accumulatedTime = 0.0;

		while (System::Update())
		{
			// Draw background
			{
				Rect{ 1280, 500 }.draw(Arg::top(0.1, 0.3, 0.8), Arg::bottom(0.8, 0.3, 0.1)); // Sky gradient
				Rect{ 0, 500, 1280, 220 }.draw(Arg::top(0.4, 0.3, 0.2), Arg::bottom(0.3, 0.5, 0.4)); // Ground gradient
			}

			// Draw enemy character
			{
				if (showEnemy) enemyTexture.drawAt(Vec2{ 920, 360 });
				if (showTargetQuad) targetQuad.draw(ColorF{ 1.0, 0.4 }).drawFrame(2);
			}

			// Input attack
			Array<Hit> hits;
			{
				if (SimpleGUI::Button(U"Physical Damage \U000F04E5", Vec2{ 40, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Physical, Random<int32>(10, 100), false);
				hits.append(PhysicalButton.update());

				if (SimpleGUI::Button(U"Fire Damage \U000F0238", Vec2{ 260, 40 }, DamagePad::Width)) hits.emplace_back(AttackType::Fire, Random<int32>(20, 120), false);
				hits.append(FireButton.update());

				if (SimpleGUI::Button(U"Thunder Damage \U000F140C", Vec2{ 40, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Thunder, Random<int32>(30, 130), false);
				hits.append(ThunderButton.update());

				if (SimpleGUI::Button(U"Ice Damage \U000F0717", Vec2{ 260, 240 }, DamagePad::Width)) hits.emplace_back(AttackType::Ice, Random<int32>(40, 140), false);
				hits.append(IceButton.update());
			}

			// Debug UI
			{
				SimpleGUI::Slider(U"Effect Speed: x{:.2f}"_fmt(effectSpeed), effectSpeed, 0.1, 1.0, Vec2{ 40, 620 }, 220, 200);
				SimpleGUI::CheckBox(showEnemy, U"Enemy Character", Vec2{ 40, 660 }, 200);
				SimpleGUI::CheckBox(showTargetQuad, U"Target Range", Vec2{ 260, 660 }, 200);
			}

			// Add issued hit info to queue
			for (const Hit& hit : hits)
			{
				hitQueue.push(hit);
				accumulatedTime = hitInterval; // Pad time so new hits trigger effect immediately
				// Generate effect at a random position within the target range
				//effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
			}

			// Process queue and issue effects
			{
				// Accumulate time
				accumulatedTime += (Scene::DeltaTime() * effectSpeed);

				// Issue effects from the queue at fixed intervals
				if (hitInterval <= accumulatedTime)
				{
					if (not hitQueue.empty())
					{
						// Get hit info from front of queue
						const Hit hit = hitQueue.front(); hitQueue.pop();
						// Generate effect at a random position within the target range
						effectManager.add<DamageNumber>(hit, RandomVec2(targetQuad), font);
						// Reduce accumulated time
						accumulatedTime -= hitInterval;
					}
				}
			}

			// Update and draw effects
			{
				effectManager.setSpeed(effectSpeed);
				effectManager.update();
			}
		}
	}
	```

## 12. Useful Tutorials
- [Tutorial 26. Drawing Shapes](../tutorial2/shape.md){:target="_blank"}
- [Tutorial 30. Time and Motion](../tutorial2/time.md){:target="_blank"}
	- Explains how to handle time and easing functions.
- [Tutorial 34. Drawing Text](../tutorial2/font.md){:target="_blank"}
- [Tutorial 39. Random](../tutorial2/random.md){:target="_blank"}
- [Tutorial 48. 2D Render States](../tutorial3/2d-render-state.md){:target="_blank"}
- [Tutorial 51. Effects](../tutorial3/effect.md){:target="_blank"}
	- Explains effects in general, including non-text effects. Can be added or applied to damage animations.