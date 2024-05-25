# 多態性の活用

| | | | |
|:--:|:--:|:--:|:--:|
| **難易度** | 中級 | **時間** | 60 分～ |

## 1. カニクラス
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/polymorphism/1.png)

- まずは普通のクラスを作成します。

??? note "コード"
	```cpp
	# include <Siv3D.hpp>

	// カニ
	class Crab
	{
	public:

		Crab() = default;

		explicit Crab(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			TextureAsset(U"Crab").drawAt(m_pos);
		}

	private:

		// 現在の位置
		Vec2 m_pos{ 0, 0 };

		// 目標の位置
		Vec2 m_targetPos{ 0, 0 };

		// 現在の速度
		Vec2 m_velocity{ 0,0 };

		// 目標変更までの残り時間
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			// Y 座標の移動量は抑えめにする
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


## 2. ネコクラスと蝶クラス
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/polymorphism/2.png)

- カニクラスと使い方（メンバ関数のインタフェース）が同じクラスを 2 つ追加します。

??? note "コード"
	```cpp hl_lines="59-114 116-171 179-180 183-185 190-192 195-197"
	# include <Siv3D.hpp>

	// カニ
	class Crab
	{
	public:

		Crab() = default;

		explicit Crab(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			TextureAsset(U"Crab").drawAt(m_pos);
		}

	private:

		// 現在の位置
		Vec2 m_pos{ 0, 0 };

		// 目標の位置
		Vec2 m_targetPos{ 0, 0 };

		// 現在の速度
		Vec2 m_velocity{ 0,0 };

		// 目標変更までの残り時間
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			// Y 座標の移動量は抑えめにする
			return{ Random(0, 1280), (m_pos.y + Random(-40, 40)) };
		}
	};

	// ネコ
	class Cat
	{
	public:

		Cat() = default;

		explicit Cat(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			// 速度に応じて左右を反転させる
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Cat").mirrored(mirrored).drawAt(m_pos);
		}

	private:

		// 現在の位置
		Vec2 m_pos{ 0, 0 };

		// 目標の位置
		Vec2 m_targetPos{ 0, 0 };

		// 現在の速度
		Vec2 m_velocity{ 0,0 };

		// 目標変更までの残り時間
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// 蝶
	class Butterfly
	{
	public:

		Butterfly() = default;

		explicit Butterfly(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			// 上下に揺らすオフセット
			const double yOffset = (10.0 * Periodic::Sine1_1(0.5s));
			TextureAsset(U"Butterfly").drawAt(m_pos + Vec2{ 0, yOffset });
		}

	private:

		// 現在の位置
		Vec2 m_pos{ 0, 0 };

		// 目標の位置
		Vec2 m_targetPos{ 0, 0 };

		// 現在の速度
		Vec2 m_velocity{ 0,0 };

		// 目標変更までの残り時間
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

!!! info "チャレンジ"
	- 新しい動物クラスを追加してみよう
		- 🐕 Dog: マウスカーソルに向かって移動する
		- 👻 Ghost: 移動中は半透明になる
		- 🐢 Turtle: 目標の変更までの時間が長く、移動速度も遅い

??? note "チャレンジの実装例"
	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/polymorphism/2-2.png)

	```cpp hl_lines="173-228 230-292 294-349 359-361 367-369 377-379 385-387"
	# include <Siv3D.hpp>

	// カニ
	class Crab
	{
	public:

		Crab() = default;

		explicit Crab(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			TextureAsset(U"Crab").drawAt(m_pos);
		}

	private:

		// 現在の位置
		Vec2 m_pos{ 0, 0 };

		// 目標の位置
		Vec2 m_targetPos{ 0, 0 };

		// 現在の速度
		Vec2 m_velocity{ 0,0 };

		// 目標変更までの残り時間
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			// Y 座標の移動量は抑えめにする
			return{ Random(0, 1280), (m_pos.y + Random(-40, 40)) };
		}
	};

	// ネコ
	class Cat
	{
	public:

		Cat() = default;

		explicit Cat(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			// 速度に応じて左右を反転させる
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Cat").mirrored(mirrored).drawAt(m_pos);
		}

	private:

		// 現在の位置
		Vec2 m_pos{ 0, 0 };

		// 目標の位置
		Vec2 m_targetPos{ 0, 0 };

		// 現在の速度
		Vec2 m_velocity{ 0,0 };

		// 目標変更までの残り時間
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// 蝶
	class Butterfly
	{
	public:

		Butterfly() = default;

		explicit Butterfly(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			// 上下に揺らすオフセット
			const double yOffset = (10.0 * Periodic::Sine1_1(0.5s));
			TextureAsset(U"Butterfly").drawAt(m_pos + Vec2{ 0, yOffset });
		}

	private:

		// 現在の位置
		Vec2 m_pos{ 0, 0 };

		// 目標の位置
		Vec2 m_targetPos{ 0, 0 };

		// 現在の速度
		Vec2 m_velocity{ 0,0 };

		// 目標変更までの残り時間
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// 犬
	class Dog
	{
	public:

		Dog() = default;

		explicit Dog(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = 0.5;

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 500.0);
		}

		void draw() const
		{
			// 速度に応じて左右を反転させる
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Dog").mirrored(mirrored).drawAt(m_pos);
		}

	private:

		// 現在の位置
		Vec2 m_pos{ 0, 0 };

		// 目標の位置
		Vec2 m_targetPos{ 0, 0 };

		// 現在の速度
		Vec2 m_velocity{ 0,0 };

		// 目標変更までの残り時間
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			return Cursor::Pos();
		}
	};

	// 幽霊
	class Ghost
	{
	public:

		Ghost() = default;

		explicit Ghost(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const
		{
			double alpha = 1.0;

			if (2.0 < m_velocity.length()) // 速度が 2.0 以上の場合はアルファを下げる
			{
				alpha = 0.3;
			}

			// 速度に応じて左右を反転させる
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Ghost").mirrored(mirrored).drawAt(m_pos, ColorF{ 1.0, alpha });
		}

	private:

		// 現在の位置
		Vec2 m_pos{ 0, 0 };

		// 目標の位置
		Vec2 m_targetPos{ 0, 0 };

		// 現在の速度
		Vec2 m_velocity{ 0,0 };

		// 目標変更までの残り時間
		double m_timer = 0.0;

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// カメ
	class Turtle
	{
	public:

		Turtle() = default;

		explicit Turtle(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		void update()
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(8.0, 12.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.4, 50.0);
		}

		void draw() const
		{
			// 速度に応じて左右を反転させる
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Turtle").mirrored(mirrored).drawAt(m_pos);
		}

	private:

		// 現在の位置
		Vec2 m_pos{ 0, 0 };

		// 目標の位置
		Vec2 m_targetPos{ 0, 0 };

		// 現在の速度
		Vec2 m_velocity{ 0,0 };

		// 目標変更までの残り時間
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


## 3. 継承を使う
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/polymorphism/3.png)

- 基底クラス `BaseAnimal` を作成し、動物クラスは `BaseAnimal` を継承するようにします。

??? note "コード"
	```cpp hl_lines="3-38 41 48 50 69 75 84 91 93 112 120 129 135 137 156 164"
	# include <Siv3D.hpp>

	// 動物の基底クラス
	class BaseAnimal
	{
	public:

		BaseAnimal() = default;

		explicit BaseAnimal(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		// 仮想デストラクタ
		virtual ~BaseAnimal() = default;

		// virtual = 0 によって、純粋仮想関数になる
		// BaseAnimal を継承したクラスは、update() を必ず実装しなければならない
		virtual void update() = 0;

		// virtual = 0 によって、純粋仮想関数になる
		// BaseAnimal を継承したクラスは、draw() を必ず実装しなければならない
		virtual void draw() const = 0;

	protected: // BaseAnimal を継承したクラスからアクセスできる

		// 現在の位置
		Vec2 m_pos{ 0, 0 };

		// 目標の位置
		Vec2 m_targetPos{ 0, 0 };

		// 現在の速度
		Vec2 m_velocity{ 0,0 };

		// 目標変更までの残り時間
		double m_timer = 0.0;
	};

	// カニ
	class Crab : public BaseAnimal
	{
	public:

		Crab() = default;

		explicit Crab(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override // override によって、BaseAnimal の update() をオーバーライドしていることを明示する
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override // override によって、BaseAnimal の draw() をオーバーライドしていることを明示する
		{
			TextureAsset(U"Crab").drawAt(m_pos);
		}

	private:

		Vec2 getNextTarget() const
		{
			// Y 座標の移動量は抑えめにする
			return{ Random(0, 1280), (m_pos.y + Random(-40, 40)) };
		}
	};

	// ネコ
	class Cat : public BaseAnimal
	{
	public:

		Cat() = default;

		explicit Cat(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override // override によって、BaseAnimal の update() をオーバーライドしていることを明示する
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override // override によって、BaseAnimal の draw() をオーバーライドしていることを明示する
		{
			// 速度に応じて左右を反転させる
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Cat").mirrored(mirrored).drawAt(m_pos);
		}

	private:

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// 蝶
	class Butterfly : public BaseAnimal
	{
	public:

		Butterfly() = default;

		explicit Butterfly(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override
		{
			// 上下に揺らすオフセット
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


## 4. 多態性を使う
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/polymorphism/4.png)

- 派生したクラスは、基底クラスのポインタや参照でまとめて扱えます。
- 仮想関数を呼び出すと、基底クラスのポインタや参照を通して、派生クラスのメンバ関数（オーバーライドした関数）を呼び出すことができます。

??? note "コード"
	```cpp hl_lines="180-185 189-192 194-197"
	# include <Siv3D.hpp>

	// 動物の基底クラス
	class BaseAnimal
	{
	public:

		BaseAnimal() = default;

		explicit BaseAnimal(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		// 仮想デストラクタ
		virtual ~BaseAnimal() = default;

		// virtual = 0 によって、純粋仮想関数になる
		// BaseAnimal を継承したクラスは、update() を必ず実装しなければならない
		virtual void update() = 0;

		// virtual = 0 によって、純粋仮想関数になる
		// BaseAnimal を継承したクラスは、draw() を必ず実装しなければならない
		virtual void draw() const = 0;

	protected: // BaseAnimal を継承したクラスからアクセスできる

		// 現在の位置
		Vec2 m_pos{ 0, 0 };

		// 目標の位置
		Vec2 m_targetPos{ 0, 0 };

		// 現在の速度
		Vec2 m_velocity{ 0,0 };

		// 目標変更までの残り時間
		double m_timer = 0.0;
	};

	// カニ
	class Crab : public BaseAnimal
	{
	public:

		Crab() = default;

		explicit Crab(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override // override によって、BaseAnimal の update() をオーバーライドしていることを明示する
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override // override によって、BaseAnimal の draw() をオーバーライドしていることを明示する
		{
			TextureAsset(U"Crab").drawAt(m_pos);
		}

	private:

		Vec2 getNextTarget() const
		{
			// Y 座標の移動量は抑えめにする
			return{ Random(0, 1280), (m_pos.y + Random(-40, 40)) };
		}
	};

	// ネコ
	class Cat : public BaseAnimal
	{
	public:

		Cat() = default;

		explicit Cat(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override // override によって、BaseAnimal の update() をオーバーライドしていることを明示する
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override // override によって、BaseAnimal の draw() をオーバーライドしていることを明示する
		{
			// 速度に応じて左右を反転させる
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Cat").mirrored(mirrored).drawAt(m_pos);
		}

	private:

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// 蝶
	class Butterfly : public BaseAnimal
	{
	public:

		Butterfly() = default;

		explicit Butterfly(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override
		{
			// 上下に揺らすオフセット
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

		// BaseAnimal 型のポインタの配列
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


## 5. 仮想メンバ関数を追加する
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/polymorphism/5.png)

- 動物の名前を返すメンバ関数を追加します。
- 動物の領域（`Circle`）を返すメンバ関数を追加します。

??? note "コード"
	```cpp hl_lines="25-34 85-88 135-138 184-187 189-195 231"
	# include <Siv3D.hpp>

	// 動物の基底クラス
	class BaseAnimal
	{
	public:

		BaseAnimal() = default;

		explicit BaseAnimal(const Vec2& pos)
			: m_pos{ pos }
			, m_targetPos{ pos } {}

		// 仮想デストラクタ
		virtual ~BaseAnimal() = default;

		// virtual = 0 によって、純粋仮想関数になる
		// BaseAnimal を継承したクラスは、update() を必ず実装しなければならない
		virtual void update() = 0;

		// virtual = 0 によって、純粋仮想関数になる
		// BaseAnimal を継承したクラスは、draw() を必ず実装しなければならない
		virtual void draw() const = 0;

		// virtual = 0 によって、純粋仮想関数になる
		// BaseAnimal を継承したクラスは、getAnimalName() を必ず実装しなければならない
		virtual String getAnimalName() const = 0;

		// 関数を実装している（= 0 ではない）ので、オーバーライドは任意
		// オーバーライドしなかった場合は BaseAnimal の getCircle() が呼ばれる
		virtual Circle getCircle() const
		{
			return Circle{ m_pos, 60 };
		}

	protected: // BaseAnimal を継承したクラスからアクセスできる

		// 現在の位置
		Vec2 m_pos{ 0, 0 };

		// 目標の位置
		Vec2 m_targetPos{ 0, 0 };

		// 現在の速度
		Vec2 m_velocity{ 0,0 };

		// 目標変更までの残り時間
		double m_timer = 0.0;
	};

	// カニ
	class Crab : public BaseAnimal
	{
	public:

		Crab() = default;

		explicit Crab(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override // override によって、BaseAnimal の update() をオーバーライドしていることを明示する
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override // override によって、BaseAnimal の draw() をオーバーライドしていることを明示する
		{
			TextureAsset(U"Crab").drawAt(m_pos);
		}

		String getAnimalName() const override // override によって、BaseAnimal の getAnimalName() をオーバーライドしていることを明示する
		{
			return U"カニ";
		}

	private:

		Vec2 getNextTarget() const
		{
			// Y 座標の移動量は抑えめにする
			return{ Random(0, 1280), (m_pos.y + Random(-40, 40)) };
		}
	};

	// ネコ
	class Cat : public BaseAnimal
	{
	public:

		Cat() = default;

		explicit Cat(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override // override によって、BaseAnimal の update() をオーバーライドしていることを明示する
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override // override によって、BaseAnimal の draw() をオーバーライドしていることを明示する
		{
			// 速度に応じて左右を反転させる
			const bool mirrored = (0.0 < m_velocity.x);
			TextureAsset(U"Cat").mirrored(mirrored).drawAt(m_pos);
		}

		String getAnimalName() const override // override によって、BaseAnimal の getAnimalName() をオーバーライドしていることを明示する
		{
			return U"ネコ";
		}

	private:

		Vec2 getNextTarget() const
		{
			return{ Random(0, 1280), Random(0, 720) };
		}
	};

	// 蝶
	class Butterfly : public BaseAnimal
	{
	public:

		Butterfly() = default;

		explicit Butterfly(const Vec2& pos)
			: BaseAnimal{ pos } {}

		void update() override
		{
			// 残り時間を減らす
			m_timer -= Scene::DeltaTime();

			// 残り時間が 0 以下になったら
			if (m_timer <= 0.0)
			{
				// 残り時間をリセットする
				m_timer = Random(3.0, 6.0);

				// 次の目標位置を設定する
				m_targetPos = getNextTarget();
			}

			// 現在の位置を目標の位置に近づける
			m_pos = Math::SmoothDamp(m_pos, m_targetPos, m_velocity, 0.2, 200.0);
		}

		void draw() const override
		{
			// 上下に揺らすオフセット
			const double yOffset = (10.0 * Periodic::Sine1_1(0.5s));
			TextureAsset(U"Butterfly").drawAt(m_pos + Vec2{ 0, yOffset });
		}

		String getAnimalName() const override
		{
			return U"蝶";
		}

		// デフォルト実装では不正確なため、オーバーライドする
		Circle getCircle() const override
		{
			// 上下に揺らすオフセット
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

		// BaseAnimal 型のポインタの配列
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


## 演習
ここから先は、自分で考えてプログラムを追加・改良してみましょう。
