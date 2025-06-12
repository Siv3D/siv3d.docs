# Utilizing Polymorphism

| | | | |
|:--:|:--:|:--:|:--:|
| **Difficulty** | Intermediate | **Time** | 60 minutes~ |

## 1. Crab Class
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/polymorphism/1.png)

- First, create a normal class.

??? note "Code"
	```cpp
	# include <Siv3D.hpp>

	// Crab
	class Crab
	{
	public:

		Crab() = default;

		explicit Crab(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			TextureAsset(U"Crab").drawAt(m_pos);
		}

	private:

		// Current position
		Vec2 m_pos{ 0, 0 };

		// Target position
		Vec2 m_targetPos{ 0, 0 };

		// Current velocity
		Vec2 m_velocity{ 0,0 };

		// Remaining time until target change
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			// Keep Y coordinate movement moderate
			return{ Random(0, 1280), (m_pos.y + Random(-40, 40)) };
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		TextureAsset::Register(U"Crab", U"🦀"_emoji);

		Crab crab{ Vec2{ 600, 500 } };

		while (System::Update())
		{
			crab.update();

			crab.draw();
		}
	}
	```


## 2. Cat Class and Butterfly Class
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/polymorphism/2.png)

- Add two classes that have the same usage (member function interface) as the Crab class.

??? note "Code"
	```cpp hl_lines="59-114 116-171 179-180 183-185 190-192 195-197"
	# include <Siv3D.hpp>

	// Crab
	class Crab
	{
	public:

		Crab() = default;

		explicit Crab(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			TextureAsset(U"Crab").drawAt(m_pos);
		}

	private:

		// Current position
		Vec2 m_pos{ 0, 0 };

		// Target position
		Vec2 m_targetPos{ 0, 0 };

		// Current velocity
		Vec2 m_velocity{ 0,0 };

		// Remaining time until target change
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			// Keep Y coordinate movement moderate
			return{ Random(0, 1280), (m_pos.y + Random(-40, 40)) };
		}
	};

	// Cat
	class Cat
	{
	public:

		Cat() = default;

		explicit Cat(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			// Flip horizontally based on velocity
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Cat").mirrored(mirrored).drawAt(m_pos);
		}

	private:

		// Current position
		Vec2 m_pos{ 0, 0 };

		// Target position
		Vec2 m_targetPos{ 0, 0 };

		// Current velocity
		Vec2 m_velocity{ 0,0 };

		// Remaining time until target change
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// Butterfly
	class Butterfly
	{
	public:

		Butterfly() = default;

		explicit Butterfly(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			// Vertical swaying offset
			const double yOffset = (10.0 * Periodic::Sine1_1(0.5s));
			TextureAsset(U"Butterfly").drawAt(m_pos + Vec2{ 0, yOffset });
		}

	private:

		// Current position
		Vec2 m_pos{ 0, 0 };

		// Target position
		Vec2 m_targetPos{ 0, 0 };

		// Current velocity
		Vec2 m_velocity{ 0,0 };

		// Remaining time until target change
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		TextureAsset::Register(U"Crab", U"🦀"_emoji);
		TextureAsset::Register(U"Cat", U"🐈"_emoji);
		TextureAsset::Register(U"Butterfly", U"🦋"_emoji);

		Crab crab{ Vec2{ 600, 500 } };
		Cat cat1{ Vec2{ 300, 200 } };
		Cat cat2{ Vec2{ 1000, 100 } };
		Butterfly butterfly{ Vec2{ 700, 600 } };

		while (System::Update())
		{
			crab.update();
			cat1.update();
			cat2.update();
			butterfly.update();

			crab.draw();
			cat1.draw();
			cat2.draw();
			butterfly.draw();
		}
	}
	```

!!! info "Challenge"
	- Try adding new animal classes
		- 🐕 Dog: Moves toward the mouse cursor
		- 👻 Ghost: Becomes semi-transparent while moving
		- 🐢 Turtle: Long time until target change and slow movement speed

??? note "Challenge Implementation Example"
	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/polymorphism/2-2.png)

	```cpp hl_lines="173-228 230-292 294-349 359-361 367-369 377-379 385-387"
	# include <Siv3D.hpp>

	// Crab
	class Crab
	{
	public:

		Crab() = default;

		explicit Crab(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			TextureAsset(U"Crab").drawAt(m_pos);
		}

	private:

		// Current position
		Vec2 m_pos{ 0, 0 };

		// Target position
		Vec2 m_targetPos{ 0, 0 };

		// Current velocity
		Vec2 m_velocity{ 0,0 };

		// Remaining time until target change
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			// Keep Y coordinate movement moderate
			return{ Random(0, 1280), (m_pos.y + Random(-40, 40)) };
		}
	};

	// Cat
	class Cat
	{
	public:

		Cat() = default;

		explicit Cat(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			// Flip horizontally based on velocity
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Cat").mirrored(mirrored).drawAt(m_pos);
		}

	private:

		// Current position
		Vec2 m_pos{ 0, 0 };

		// Target position
		Vec2 m_targetPos{ 0, 0 };

		// Current velocity
		Vec2 m_velocity{ 0,0 };

		// Remaining time until target change
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// Butterfly
	class Butterfly
	{
	public:

		Butterfly() = default;

		explicit Butterfly(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			// Vertical swaying offset
			const double yOffset = (10.0 * Periodic::Sine1_1(0.5s));
			TextureAsset(U"Butterfly").drawAt(m_pos + Vec2{ 0, yOffset });
		}

	private:

		// Current position
		Vec2 m_pos{ 0, 0 };

		// Target position
		Vec2 m_targetPos{ 0, 0 };

		// Current velocity
		Vec2 m_velocity{ 0,0 };

		// Remaining time until target change
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// Dog
	class Dog
	{
	public:

		Dog() = default;

		explicit Dog(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = 0.5;

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 500.0);
		}

		void draw() const
		{
			// Flip horizontally based on velocity
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Dog").mirrored(mirrored).drawAt(m_pos);
		}

	private:

		// Current position
		Vec2 m_pos{ 0, 0 };

		// Target position
		Vec2 m_targetPos{ 0, 0 };

		// Current velocity
		Vec2 m_velocity{ 0,0 };

		// Remaining time until target change
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			return Cursor::Pos();
		}
	};

	// Ghost
	class Ghost
	{
	public:

		Ghost() = default;

		explicit Ghost(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			double alpha = 1.0;

			if (2.0 < m_velocity.length()) // Lower alpha when velocity is 2.0 or higher
			{
				alpha = 0.3;
			}

			// Flip horizontally based on velocity
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Ghost").mirrored(mirrored).drawAt(m_pos, ColorF{ 1.0, alpha });
		}

	private:

		// Current position
		Vec2 m_pos{ 0, 0 };

		// Target position
		Vec2 m_targetPos{ 0, 0 };

		// Current velocity
		Vec2 m_velocity{ 0,0 };

		// Remaining time until target change
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// Turtle
	class Turtle
	{
	public:

		Turtle() = default;

		explicit Turtle(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(8.0, 12.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.4, 50.0);
		}

		void draw() const
		{
			// Flip horizontally based on velocity
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Turtle").mirrored(mirrored).drawAt(m_pos);
		}

	private:

		// Current position
		Vec2 m_pos{ 0, 0 };

		// Target position
		Vec2 m_targetPos{ 0, 0 };

		// Current velocity
		Vec2 m_velocity{ 0,0 };

		// Remaining time until target change
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		TextureAsset::Register(U"Crab", U"🦀"_emoji);
		TextureAsset::Register(U"Cat", U"🐈"_emoji);
		TextureAsset::Register(U"Butterfly", U"🦋"_emoji);
		TextureAsset::Register(U"Dog", U"🐕"_emoji);
		TextureAsset::Register(U"Ghost", U"👻"_emoji);
		TextureAsset::Register(U"Turtle", U"🐢"_emoji);

		Crab crab{ Vec2{ 600, 500 } };
		Cat cat1{ Vec2{ 300, 200 } };
		Cat cat2{ Vec2{ 1000, 100 } };
		Butterfly butterfly{ Vec2{ 700, 600 } };
		Dog dog{ Vec2{ 600, 200 } };
		Ghost ghost{ Vec2{ 300, 500 } };
		Turtle turtle{ Vec2{ 800, 400 } };

		while (System::Update())
		{
			crab.update();
			cat1.update();
			cat2.update();
			butterfly.update();
			dog.update();
			ghost.update();
			turtle.update();

			crab.draw();
			cat1.draw();
			cat2.draw();
			butterfly.draw();
			dog.draw();
			ghost.draw();
			turtle.draw();
		}
	}
	```


## 3. Using Inheritance
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/polymorphism/3.png)

- Create a base class `BaseAnimal` and make the animal classes inherit from `BaseAnimal`.

??? note "Code"
	```cpp hl_lines="3-38 41 48 50 69 75 84 91 93 112 120 129 135 137 156 164"
	# include <Siv3D.hpp>

	// Animal base class
	class BaseAnimal
	{
	public:

		BaseAnimal() = default;

		explicit BaseAnimal(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		// Virtual destructor
		virtual ~BaseAnimal() = default;

		// By using virtual = 0, this becomes a pure virtual function
		// Classes inheriting from BaseAnimal must implement update()
		virtual void update() = 0;

		// By using virtual = 0, this becomes a pure virtual function
		// Classes inheriting from BaseAnimal must implement draw()
		virtual void draw() const = 0;

	protected: // Accessible from classes inheriting from BaseAnimal

		// Current position
		Vec2 m_pos{ 0, 0 };

		// Target position
		Vec2 m_targetPos{ 0, 0 };

		// Current velocity
		Vec2 m_velocity{ 0,0 };

		// Remaining time until target change
		double m_timer = 0.0;
	};

	// Crab
	class Crab : public BaseAnimal
	{
	public:

		Crab() = default;

		explicit Crab(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override // override explicitly indicates that this overrides BaseAnimal's update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override // override explicitly indicates that this overrides BaseAnimal's draw()
		{
			TextureAsset(U"Crab").drawAt(m_pos);
		}

	private:

		Vec2 getNextTarget() const
		{
			// Keep Y coordinate movement moderate
			return{ Random(0, 1280), (m_pos.y + Random(-40, 40)) };
		}
	};

	// Cat
	class Cat : public BaseAnimal
	{
	public:

		Cat() = default;

		explicit Cat(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override // override explicitly indicates that this overrides BaseAnimal's update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override // override explicitly indicates that this overrides BaseAnimal's draw()
		{
			// Flip horizontally based on velocity
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Cat").mirrored(mirrored).drawAt(m_pos);
		}

	private:

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// Butterfly
	class Butterfly : public BaseAnimal
	{
	public:

		Butterfly() = default;

		explicit Butterfly(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override
		{
			// Vertical swaying offset
			const double yOffset = (10.0 * Periodic::Sine1_1(0.5s));
			TextureAsset(U"Butterfly").drawAt(m_pos + Vec2{ 0, yOffset });
		}

	private:

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		TextureAsset::Register(U"Crab", U"🦀"_emoji);
		TextureAsset::Register(U"Cat", U"🐈"_emoji);
		TextureAsset::Register(U"Butterfly", U"🦋"_emoji);

		Crab crab{ Vec2{ 600, 500 } };
		Cat cat1{ Vec2{ 300, 200 } };
		Cat cat2{ Vec2{ 1000, 100 } };
		Butterfly butterfly{ Vec2{ 700, 600 } };

		while (System::Update())
		{
			crab.update();
			cat1.update();
			cat2.update();
			butterfly.update();

			crab.draw();
			cat1.draw();
			cat2.draw();
			butterfly.draw();
		}
	}
	```


## 4. Using Polymorphism
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/polymorphism/4.png)

- Derived classes can be handled collectively through pointers or references to the base class.
- When calling virtual functions, you can call derived class member functions (overridden functions) through base class pointers or references.

??? note "Code"
	```cpp hl_lines="180-185 189-192 194-197"
	# include <Siv3D.hpp>

	// Animal base class
	class BaseAnimal
	{
	public:

		BaseAnimal() = default;

		explicit BaseAnimal(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		// Virtual destructor
		virtual ~BaseAnimal() = default;

		// By using virtual = 0, this becomes a pure virtual function
		// Classes inheriting from BaseAnimal must implement update()
		virtual void update() = 0;

		// By using virtual = 0, this becomes a pure virtual function
		// Classes inheriting from BaseAnimal must implement draw()
		virtual void draw() const = 0;

	protected: // Accessible from classes inheriting from BaseAnimal

		// Current position
		Vec2 m_pos{ 0, 0 };

		// Target position
		Vec2 m_targetPos{ 0, 0 };

		// Current velocity
		Vec2 m_velocity{ 0,0 };

		// Remaining time until target change
		double m_timer = 0.0;
	};

	// Crab
	class Crab : public BaseAnimal
	{
	public:

		Crab() = default;

		explicit Crab(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override // override explicitly indicates that this overrides BaseAnimal's update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override // override explicitly indicates that this overrides BaseAnimal's draw()
		{
			TextureAsset(U"Crab").drawAt(m_pos);
		}

	private:

		Vec2 getNextTarget() const
		{
			// Keep Y coordinate movement moderate
			return{ Random(0, 1280), (m_pos.y + Random(-40, 40)) };
		}
	};

	// Cat
	class Cat : public BaseAnimal
	{
	public:

		Cat() = default;

		explicit Cat(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override // override explicitly indicates that this overrides BaseAnimal's update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override // override explicitly indicates that this overrides BaseAnimal's draw()
		{
			// Flip horizontally based on velocity
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Cat").mirrored(mirrored).drawAt(m_pos);
		}

	private:

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// Butterfly
	class Butterfly : public BaseAnimal
	{
	public:

		Butterfly() = default;

		explicit Butterfly(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override
		{
			// Vertical swaying offset
			const double yOffset = (10.0 * Periodic::Sine1_1(0.5s));
			TextureAsset(U"Butterfly").drawAt(m_pos + Vec2{ 0, yOffset });
		}

	private:

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		TextureAsset::Register(U"Crab", U"🦀"_emoji);
		TextureAsset::Register(U"Cat", U"🐈"_emoji);
		TextureAsset::Register(U"Butterfly", U"🦋"_emoji);

		// Array of BaseAnimal type pointers
		Array<std::unique_ptr<BaseAnimal>> animals;
		animals << std::make_unique<Crab>(Vec2{ 600, 500 });
		animals << std::make_unique<Cat>(Vec2{ 300, 200 });
		animals << std::make_unique<Cat>(Vec2{ 1000, 100 });
		animals << std::make_unique<Butterfly>(Vec2{ 700, 600 });

		while (System::Update())
		{
			for (auto& animal : animals)
			{
				animal->update();
			}

			for (const auto& animal : animals)
			{
				animal->draw();
			}
		}
	}
	```


## 5. Adding Virtual Member Functions
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/polymorphism/5.png)

- Add a member function that returns the animal's name.
- Add a member function that returns the animal's area (`Circle`).

??? note "Code"
	```cpp hl_lines="25-34 85-88 135-138 184-187 189-195 231"
	# include <Siv3D.hpp>

	// Animal base class
	class BaseAnimal
	{
	public:

		BaseAnimal() = default;

		explicit BaseAnimal(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		// Virtual destructor
		virtual ~BaseAnimal() = default;

		// By using virtual = 0, this becomes a pure virtual function
		// Classes inheriting from BaseAnimal must implement update()
		virtual void update() = 0;

		// By using virtual = 0, this becomes a pure virtual function
		// Classes inheriting from BaseAnimal must implement draw()
		virtual void draw() const = 0;

		// By using virtual = 0, this becomes a pure virtual function
		// Classes inheriting from BaseAnimal must implement getAnimalName()
		virtual String getAnimalName() const = 0;

		// The function is implemented (not = 0), so overriding is optional
		// If not overridden, BaseAnimal's getCircle() will be called
		virtual Circle getCircle() const
		{
			return Circle{ m_pos, 60 };
		}

	protected: // Accessible from classes inheriting from BaseAnimal

		// Current position
		Vec2 m_pos{ 0, 0 };

		// Target position
		Vec2 m_targetPos{ 0, 0 };

		// Current velocity
		Vec2 m_velocity{ 0,0 };

		// Remaining time until target change
		double m_timer = 0.0;
	};

	// Crab
	class Crab : public BaseAnimal
	{
	public:

		Crab() = default;

		explicit Crab(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override // override explicitly indicates that this overrides BaseAnimal's update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override // override explicitly indicates that this overrides BaseAnimal's draw()
		{
			TextureAsset(U"Crab").drawAt(m_pos);
		}

		String getAnimalName() const override // override explicitly indicates that this overrides BaseAnimal's getAnimalName()
		{
			return U"Crab";
		}

	private:

		Vec2 getNextTarget() const
		{
			// Keep Y coordinate movement moderate
			return{ Random(0, 1280), (m_pos.y + Random(-40, 40)) };
		}
	};

	// Cat
	class Cat : public BaseAnimal
	{
	public:

		Cat() = default;

		explicit Cat(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override // override explicitly indicates that this overrides BaseAnimal's update()
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override // override explicitly indicates that this overrides BaseAnimal's draw()
		{
			// Flip horizontally based on velocity
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Cat").mirrored(mirrored).drawAt(m_pos);
		}

		String getAnimalName() const override // override explicitly indicates that this overrides BaseAnimal's getAnimalName()
		{
			return U"Cat";
		}

	private:

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// Butterfly
	class Butterfly : public BaseAnimal
	{
	public:

		Butterfly() = default;

		explicit Butterfly(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override
		{
			// Decrease remaining time
			m_timer -= Scene::DeltaTime();

			// When remaining time becomes 0 or less
			if (m_timer <= 0.0)
			{
				// Reset remaining time
				m_timer = Random(3.0, 6.0);

				// Set next target position
				m_targetPos = getNextTarget();
			}

			// Move current position closer to target position
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override
		{
			// Vertical swaying offset
			const double yOffset = (10.0 * Periodic::Sine1_1(0.5s));
			TextureAsset(U"Butterfly").drawAt(m_pos + Vec2{ 0, yOffset });
		}

		String getAnimalName() const override
		{
			return U"Butterfly";
		}

		// Override because the default implementation is inaccurate
		Circle getCircle() const override
		{
			// Vertical swaying offset
			const double yOffset = (10.0 * Periodic::Sine1_1(0.5s));
			return{ (m_pos + Vec2{ 0, yOffset }), 60 };
		}

	private:

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		TextureAsset::Register(U"Crab", U"🦀"_emoji);
		TextureAsset::Register(U"Cat", U"🐈"_emoji);
		TextureAsset::Register(U"Butterfly", U"🦋"_emoji);

		// Array of BaseAnimal type pointers
		Array<std::unique_ptr<BaseAnimal>> animals;
		animals << std::make_unique<Crab>(Vec2{ 600, 500 });
		animals << std::make_unique<Cat>(Vec2{ 300, 200 });
		animals << std::make_unique<Cat>(Vec2{ 1000, 100 });
		animals << std::make_unique<Butterfly>(Vec2{ 700, 600 });

		while (System::Update())
		{
			for (auto& animal : animals)
			{
				animal->update();
			}

			for (const auto& animal : animals)
			{
				animal->draw();
				animal->getCircle().drawFrame(2, Palette::Black);
			}
		}
	}
	```


## Exercise
From here on, try adding and improving the program by thinking for yourself.