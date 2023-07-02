# 多態性を使ったプログラミング

## 1. 多態性の基礎

```cpp
# include <Siv3D.hpp>

enum class EnemyType
{
	Cat,
	Dog,
};

class IEnemy
{
public:

	virtual ~IEnemy() = default;

	virtual void draw() const = 0;

	virtual EnemyType getType() const = 0;
};

class EnemyCat : public IEnemy
{
public:

	EnemyCat() = default;

	explicit EnemyCat(const Vec2& pos)
		: m_pos{ pos } {}

	void draw() const override
	{
		TextureAsset(U"cat").drawAt(m_pos);
	}

	EnemyType getType() const override
	{
		return EnemyType::Cat;
	}

private:

	Vec2 m_pos{ 0,0 };
};

class EnemyDog : public IEnemy
{
public:

	EnemyDog() = default;

	explicit EnemyDog(const Vec2& pos)
		: m_pos{ pos } {}

	void draw() const override
	{
		TextureAsset(U"dog").drawAt(m_pos);
	}

	EnemyType getType() const override
	{
		return EnemyType::Dog;
	}

private:

	Vec2 m_pos{ 0, 0 };
};

void Main()
{
	TextureAsset::Register(U"cat", U"🐈"_emoji);
	TextureAsset::Register(U"dog", U"🐕"_emoji);

	Array<std::unique_ptr<IEnemy>> enemies;
	enemies << std::make_unique<EnemyCat>(Vec2{ 100, 100 });
	enemies << std::make_unique<EnemyDog>(Vec2{ 300, 300 });
	enemies << std::make_unique<EnemyDog>(Vec2{ 500, 500 });

	while (System::Update())
	{
		for (const auto& enemy : enemies)
		{
			enemy->draw();
		}
	}
}
```

