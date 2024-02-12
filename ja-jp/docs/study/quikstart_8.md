# 勉強会 8 コマ目（配列と図形の交差）

## 1. 配列を使う

### 1.1 配列の使い方
- `Siv3D` では `std::vector<T>` の代わりに `Array<T>` を使います。
- `Array` は、`v.push_back(x);` の代わりに `v << x;` と記述できるなど、便利な機能がいくつか追加されています。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-4.1.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	// std::vector<int> に相当する
	Array<int32> v = { 10, 20, 30, 40 };

	// v.push_back(50) と同じ
	v << 50;

	// v.push_back(60) と同じ
	v << 60;

	while (System::Update())
	{
		font(U"v: {}"_fmt(v)).draw(30, 20, 20, Palette::Black);
	}
}
```


### 1.2 配列の要素数
- 配列の要素数を調べる場合は `.size()` を使います。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-4.2.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	Array<int32> v;

	while (System::Update())
	{
		font(U"v: {}"_fmt(v)).draw(30, 20, 20, Palette::Black);

		font(U"要素数: {}"_fmt(v.size())).draw(30, 20, 60, Palette::Black);

		// もし左クリックされたら
		if (MouseL.down())
		{
			// 0 以上 100 以下のランダムな整数を追加する
			v << Random(0, 100);
		}
	}
}
```


### 1.3 すべての値を調べる
- `for (const auto& elem : v)` による繰り返しで各要素にアクセスします。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-4.3.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	Array<double> xs = { 100, 200, 300, 500 };

	while (System::Update())
	{
		font(U"xs: {}"_fmt(xs)).draw(30, 20, 20, Palette::Black);

		for (const auto& x : xs)
		{
			Circle{ x, 300, 30 }.draw();
		}
	}
}
```


### 1.4 すべての値を変更する
- `for (auto& elem : v)` による繰り返しで各要素にアクセスしつつ、要素を変更できます。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-4.3.png)

```cpp hl_lines="20-23"
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	Array<double> xs = { 100, 200, 300, 500 };

	while (System::Update())
	{
		font(U"xs: {}"_fmt(xs)).draw(30, 20, 20, Palette::Black);

		for (const auto& x : xs)
		{
			Circle{ x, 300, 30 }.draw();
		}

		for (auto& x : xs) // 配列の各要素を変更する場合 const を付けない
		{
			x += 1.0;
		}
	}
}
```


## 2. クラスを配列に追加する

### 2.1 Circle の配列 (1)
- `Circle` の配列を作成できます。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-5.1.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	Array<Circle> circles;

	while (System::Update())
	{
		if (MouseL.down())
		{
			circles << Circle{ Cursor::Pos(), Random(10, 30) };
		}

		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


### 2.2 Circle の配列 (2)
- 一定時間ごとに円を配列に追加するサンプルです。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-5.2.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	Array<Circle> circles;

	// 蓄積された時間（秒）
	double accumulatedTime = 0.0;

	while (System::Update())
	{
		double deltaTime = Scene::DeltaTime();

		accumulatedTime += deltaTime;

		// 蓄積時間が一定時間を超えたら
		if (0.5 <= accumulatedTime)
		{
			circles << Circle{ Cursor::Pos(), Random(10, 30) };

			// 蓄積時間からマイナス
			accumulatedTime -= 0.5;
		}

		for (const auto& circle : circles)
		{
			circle.draw();
		}
	}
}
```


### 2.3 Circle の配列 (3)
- スペースキーを押したとき、円を配列に追加するサンプルです。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-5.3.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"🤠"_emoji };

	Array<Circle> circles;

	// プレイヤーの X 座標
	double playerPosX = 400;

	while (System::Update())
	{
		// ← キーを押したら
		if (KeyLeft.pressed())
		{
			// 左へ移動
			playerPosX -= 3.0;
		}

		// → キーを押したら
		if (KeyRight.pressed())
		{
			// 右へ移動
			playerPosX += 3.0;
		}

		// スペースキーを押したら
		if (KeySpace.down())
		{
			// 配列に円を追加
			circles << Circle{ playerPosX, 420, 10 };
		}

		// すべての円を移動させる
		for (auto& circle : circles)
		{
			circle.y -= 4.0;
		}

		// すべての円を描く
		for (const auto& circle : circles)
		{
			circle.draw(Palette::Gray);
		}

		// 絵文字を描く
		emoji.drawAt(playerPosX, 500);
	}
}
```


## 3. 配列から要素を削除する

### 3.1 配列から要素を削除する
- `Array` は、`.remove_if(チェック関数)` を使うことで、チェックに引っかかる要素を配列から削除できます。
- チェック関数の戻り値は `bool` 型、引数は配列の要素と同じ型にします。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-6.1.png)

```cpp hl_lines="3-7 17-18"
#include <Siv3D.hpp>

// 偶数であるかをチェックする関数
bool CheckInt(int32 n)
{
	return (n % 2 == 0);
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	Array<int32> v = { 1, 2, 3, 4, 5, 6 };

	Print << v;

	// チェックに引っかかる要素を配列から削除する
	v.remove_if(CheckInt);

	Print << v;

	while (System::Update())
	{

	}
}
```


### 3.2 配列からチェックに引っかかる円を削除する
- 画面の外に出そうな円を削除するサンプルです。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-6.2.png)

```cpp hl_lines="3-7 49-50"
#include <Siv3D.hpp>

// 円の Y 座標が 100 未満であるかをチェックする関数
bool CheckCircle(const Circle& circle)
{
	return circle.y < 100;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"🤠"_emoji };

	Array<Circle> circles;

	// プレイヤーの X 座標
	double playerPosX = 400;

	while (System::Update())
	{
		// ← キーを押したら
		if (KeyLeft.pressed())
		{
			// 左へ移動
			playerPosX -= 3.0;
		}

		// → キーを押したら
		if (KeyRight.pressed())
		{
			// 右へ移動
			playerPosX += 3.0;
		}

		// スペースキーを押したら
		if (KeySpace.down())
		{
			// 配列に円を追加
			circles << Circle{ playerPosX, 420, 10 };
		}

		// すべての円を移動させる
		for (auto& circle : circles)
		{
			circle.y -= 4.0;
		}

		// チェックに引っかかる要素を配列から削除する
		circles.remove_if(CheckCircle);

		// すべての円を描く
		for (const auto& circle : circles)
		{
			circle.draw(Palette::Gray);
		}

		// 絵文字を描く
		emoji.drawAt(playerPosX, 500);
	}
}
```


## 4. 図形同士の交差を調べる

### 4.1 図形同士の交差
- 図形 A と 図形 B が重なっているかは `if (A.intersects(B))` で調べられます。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-7.1.png)

```cpp hl_lines="19-23"
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	Circle enemyCircle{ 400, 200, 100 };

	while (System::Update())
	{
		const Circle bulletCircle{ Cursor::Pos(), 20};

		enemyCircle.draw();

		bulletCircle.draw(Palette::Gray);

		// もし 2 つの Circle が交差している場合
		if (enemyCircle.intersects(bulletCircle))
		{
			font(U"交差").draw(30, 20, 20, Palette::Black);
		}
	}
}
```


### 4.2 複数の図形との交差判定 (1)
- 複数の図形と交差判定をするサンプルです。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-7.2.png)

```cpp hl_lines="19-27"
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	Array<Circle> enemyCircles = {
		Circle{ 200, 200, 60 },
		Circle{ 400, 200, 60 },
		Circle{ 600, 200, 60 },
	};

	while (System::Update())
	{
		const Circle bulletCircle{ Cursor::Pos(), 20};

		// 各 enemyCircle について
		for (auto& enemyCircle : enemyCircles)
		{
			// もし 2 つの Circle が交差している場合
			if (enemyCircle.intersects(bulletCircle))
			{
				font(U"交差").draw(30, 20, 20, Palette::Black);
			}
		}

		for (const auto& enemyCircle : enemyCircles)
		{
			enemyCircle.draw();
		}

		bulletCircle.draw(Palette::Gray);
	}
}
```


### 4.3 複数の図形との交差判定 (2)
- 複数の図形と交差判定をし、交差した円を削除するサンプルです。
- 交差した円の Y 座標を `-999` に変更することで、その後のチェックに引っかかるようにし、配列から削除します。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-7.3.png)

```cpp hl_lines="3-7 27-39"
#include <Siv3D.hpp>

// 円の Y 座標が 100 未満であるかをチェックする関数
bool CheckCircle(const Circle& circle)
{
	return circle.y < 100;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	Array<Circle> enemyCircles = {
		Circle{ 200, 200, 60 },
		Circle{ 400, 200, 60 },
		Circle{ 600, 200, 60 },
	};

	while (System::Update())
	{
		font(U"enemyCircles: {}"_fmt(enemyCircles)).draw(15, 20, 20, Palette::Black);

		const Circle bulletCircle{ Cursor::Pos(), 20};

		// 各 enemyCircle について
		for (auto& enemyCircle : enemyCircles)
		{
			// もし 2 つの Circle が交差している場合
			if (enemyCircle.intersects(bulletCircle))
			{
				// その enemyCircle の Y 座標を -999 に変更する
				enemyCircle.y = -999;
			}
		}

		// チェックに引っかかる要素を配列から削除する
		enemyCircles.remove_if(CheckCircle);

		for (const auto& enemyCircle : enemyCircles)
		{
			enemyCircle.draw();
		}

		bulletCircle.draw(Palette::Gray);
	}
}
```

