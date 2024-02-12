# 勉強会 7 コマ目（時間と乱数）

## 1. 時間

### 1.1 前フレームからの経過時間を調べる
`Scene::DeltaTime()` は前フレームからの経過時間（秒）を `double` 型で返します。60 Hz の場合は約 0.0166 です。これを加算することで経過時間を調べることができます。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-3.1.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	// 蓄積された時間（秒）
	double accumulatedTime = 0.0;

	while (System::Update())
	{
		// 60 Hz の場合, 1/60 秒（約 0.0166）
		double deltaTime = Scene::DeltaTime();

		accumulatedTime += deltaTime;

		font(U"accumulatedTime: {:.5f}"_fmt(accumulatedTime)).draw(40, 100, 20, Palette::Black);
	}
}
```


### 1.2 残り時間をカウントダウンする
残り時間から `Scene::DeltaTime()` の値を引いていくことで、時間のカウントダウンができます。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-3.2.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	// 残り時間（秒）
	double timeLeft = 5.0;

	while (System::Update())
	{
		double deltaTime = Scene::DeltaTime();

		timeLeft -= deltaTime;

		if (0.0 < timeLeft)
		{
			font(U"timeLeft: {:.5f}"_fmt(timeLeft)).draw(40, 100, 20, Palette::Black);
		}
		else
		{
			font(U"Game over").draw(40, 100, 20, Palette::Black);
		}
	}
}
```


### 1.3 一定時間ごとにイベントを発生させる
`Scene::DeltaTime()` を加算し、一定時間が経過したときにイベントを発生させるサンプルです。3 秒ごとに `Hello!` と `Print` します。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-3.3.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	double accumulatedTime = 0.0;

	while (System::Update())
	{
		double deltaTime = Scene::DeltaTime();

		accumulatedTime += deltaTime;

		font(U"accumulatedTime: {:.5f}"_fmt(accumulatedTime)).draw(40, 100, 20, Palette::Black);

		// 蓄積時間が一定時間を超えたら
		if (3.0 <= accumulatedTime)
		{
			Print << U"Hello!";

			// 蓄積時間からマイナス
			accumulatedTime -= 3.0;
		}
	}
}
```


## 2. 乱数

### 2.1 乱数を生成する
`Random(a, b)` は a 以上 b 以下の数を返します。ただし `a < b` です。

- 例: `Random(0, 100)`, `Random(0.8, 1.2)`

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/13-7.1.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			// 0 以上 2 以下のランダムな整数を作る
			int32 n = Random(0, 2);

			if (n == 0)
			{
				Print << U"大吉";
			}
			else if (n == 1)
			{
				Print << U"小吉";
			}
			else
			{
				Print << U"凶";
			}
		}		
	}
}
```


### 2.2 ランダムな場所に移動する
画面を左クリックするたびに絵文字がランダムな場所に移動するサンプルです。

```cpp hl_lines="18-22"
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

	double x = 400.0;

	double y = 300.0;

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			// ランダムな X 座標を代入
			x = Random(50, 750);

			// ランダムな Y 座標を代入
			y = Random(50, 550);
		}

		emoji.drawAt(x, y);
	}
}
```

`Vec2` 型を使うと、次のように書くこともできます。

```cpp hl_lines="16-17"
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			// ランダムな座標を代入
			pos = Vec2{ Random(50, 750), Random(50, 550) };
		}

		emoji.drawAt(pos);
	}
}
```

`rect` で指定した範囲内のランダムな座標を返す `RandomVec2(rect)` を使うこともできます。

```cpp hl_lines="16-17"
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			// ランダムな座標を代入
			pos = RandomVec2(Rect{ 50, 50, 700, 500 });

			emoji.drawAt(pos);
		}
	}
}
```

