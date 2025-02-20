# 35. 無効値を表現できる型
無効値を表現できる型 `Optional` の基本的な使い方を学びます。

## 35.1 Optional
- `Optional<Type>` は `std::optional<Type>` に相当する型です
- `Type` 型のすべての表現に加え、有効な値を持たないことを表す「無効値」を持つことができる型です
- イメージとしては、サイズが 0 か 1 のどちらかである `Array<Type>` です
	- 有効な値を持つとき、配列のサイズは 1 で、その値にアクセスできます
	- 無効値のときはサイズが 0 で、値にはアクセスできません
- `Optional<Type>` 型の値は、初期値を与えられなかった場合、無効値として初期化されます
- `Optional` の値を `Print` で出力すると、有効値の場合は `(Optional)` とその値が出力され、無効値の場合は `none` が出力されます

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 有効値で初期化する
	Optional<Point> pos1 = Point{ 100, 200 };

	// 無効値で初期化する
	Optional<Point> pos2;

	Print << pos1;
	Print << pos2;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
(Optional)(100, 200)
none
```


## 35.2 有効値を持つかを調べる
- `Optional` 型の値 `opt` が有効値を持つかを調べるには、次の方法があります
	- `opt.has_value()` が `true` を返す場合、有効値を持つ
	- `if (opt)` または `if (not opt)` で調べる

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 有効値で初期化する
	Optional<Point> pos1 = Point{ 100, 200 };

	// 無効値で初期化する
	Optional<Point> pos2;

	Print << pos1.has_value();
	Print << pos2.has_value();

	if (pos1)
	{
		Print << U"pos1 has a value";
	}

	if (not pos2)
	{
		Print << U"pos2 does not have a value";
	}
	
	while (System::Update())
	{

	}
}
```
```txt title="出力"
true
false
pos1 has a value
pos2 does not have a value
```


## 35.3 有効値へのアクセス
- `Optional` 型の値 `opt` が有効値を持つ場合、`*opt` でその値にアクセスできます
- `opt->x` や `opt->y` のように、`->` 演算子を使ってメンバにアクセスすることもできます
- 有効値を持たないときに値にアクセスしてはいけません

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 有効値で初期化する
	Optional<Point> pos = Point{ 100, 200 };

	if (pos)
	{
		Print << *pos;

		pos->x += 20;
		pos->y += 30;

		Print << *pos;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
(100, 200)
(120, 230)
```


## 35.4 無効値にする
- `none` は `Optional` 型の無効値を表す定数です
- `Optional` 型の値 `opt` に無効値を代入するには、`opt = none` とします
- `opt.reset()` でも同じです

```cpp
# include <Siv3D.hpp>

void Main()
{
	Optional<Point> pos = Point{ 100, 200 };
	Print << pos;

	pos = none;
	Print << pos;

	pos = Point{ 300, 400 };
	Print << pos;

	pos.reset();
	Print << pos;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
(Optional)(100, 200)
none
(Optional)(300, 400)
none
```


## 35.5 有効値または代わりの値の取得
- `.value_or(defaultValue)` は、有効値を持つ場合、有効値の値を返し、無効値である場合は `defaultValue` を返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Optional<Point> pos1 = Point{ 100, 200 };

	Optional<Point> pos2;

	// pos1 は有効値を持つため Point{ 100, 200 } を返す
	Print << pos1.value_or(Point{ 0, 0 });

	// pos2 は有効値を持たないため Point{ 0, 0 } を返す
	Print << pos2.value_or(Point{ 0, 0 });

	while (System::Update())
	{

	}
}
```
```txt title="出力"
(100, 200)
(0, 0)
```


## 35.6 if との組み合わせ
- 次のように `if` と組み合わせることで、`Optional` 型の値が有効値を持つ場合の処理を簡潔に書くことができます

```cpp
# include <Siv3D.hpp>

Optional<int32> GetResult1()
{
	return 123;
}

Optional<int32> GetResult2()
{
	return none;
}

void Main()
{
	if (const auto result = GetResult1())
	{
		Print << *result;
	}

	if (const auto result = GetResult2())
	{
		Print << *result;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
123
```


## 35.7 活用例（1）
- マウスのドラッグで矢印を作るサンプルです
- マウスの左ボタンが押された位置を `Optional<Point>` で表現し、有効値を持つ場合はその位置から現在のマウスカーソルの位置まで矢印を描きます
- マウスの左ボタンが離されたら、無効値にします
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/optional/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// マウスの左ボタンが押された位置
	Optional<Point> start;

	while (System::Update())
	{
		ClearPrint();
		Print << start;

		if (MouseL.down()) // マウスの左ボタンが押されたら
		{
			// マウスカーソルの位置を有効値として代入する
			start = Cursor::Pos();
		}
		else if (MouseL.up()) // マウスの左ボタンが離されたら
		{
			// 無効値にする
			start.reset();
		}

		// 有効値を持っていれば
		if (start)
		{
			// スタート地点を中心に円を描く
			start->asCircle(10).draw(ColorF{ 0.2 });

			// 現在のマウスカーソルの位置まで矢印を描く
			Line{ *start, Cursor::Pos() }.drawArrow(6, SizeF{ 20, 20 }, ColorF{ 0.2 });
		}
	}
}
```


## 35.8 活用例（2）
- マウスのドラッグでアイテムを移動するサンプルです
- ドラッグ中のアイテムの種類を `Optional<int32>` で表現し、有効値を持つ場合はそのアイテムをマウスカーソルの位置に描きます
- ドラッグが終了したら、無効値にします
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/optional/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture item1Texture{ U"🍌"_emoji };
	const Texture item2Texture{ U"🍎"_emoji };

	const Circle item1Circle{ 100, 200, 60 };
	const Circle item2Circle{ 100, 400, 60 };
	const Rect boxRect{ 500, 200, 200, 200 };

	Optional<int32> grabbedItem;

	while (System::Update())
	{
		ClearPrint();
		Print << grabbedItem;

		if (grabbedItem || item1Circle.mouseOver() || item2Circle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		if (item1Circle.leftClicked())
		{
			grabbedItem = 1;
		}
		else if (item2Circle.leftClicked())
		{
			grabbedItem = 2;
		}
		else if (MouseL.up())
		{
			grabbedItem.reset();
		}

		item1Texture.drawAt(item1Circle.center);
		item2Texture.drawAt(item2Circle.center);
		boxRect.draw();

		// アイテムをつかんでいる
		if (grabbedItem)
		{
			// ボックスの上にカーソルがある場合
			if (boxRect.mouseOver())
			{
				// ボックスの枠を赤く描画する
				boxRect.drawFrame(0, 20, ColorF{ 1.0, 0.5, 0.5 });
			}

			// アイテムを描画する
			if (grabbedItem == 1)
			{
				item1Texture.drawAt(Cursor::Pos());
			}
			else if (grabbedItem == 2)
			{
				item2Texture.drawAt(Cursor::Pos());
			}
		}
	}
}
```

