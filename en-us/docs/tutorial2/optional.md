# 23. 無効値を表現できる型
無効値を表現できる型 `Optional` の基本的な使い方を学びます。

## 23.1 無効値を表現できる型
`Optional<Type>` は `std::optional<Type>` に相当する型です。`Type` 型の値を持つことができ、値を持たないことを表す「無効値」を持つこともできます。

イメージとしては、サイズが 0 または 1 である `Array<Type>` です。有効な値を持つとき、サイズが 1 でその値にアクセスできます。無効値を持つときはサイズが 0 で、値にはアクセスできません。

初期値を与えられなかった場合、`Optional<Type>` 型の値は無効値を持ちます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/optional/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 無効値で初期化する
	Optional<Point> pos1;

	// 有効値で初期化する
	Optional<Point> pos2 = Point{ 100, 200 };

	Print << pos1;

	Print << pos2;

	while (System::Update())
	{

	}
}
```

## 23.2 有効値を持つかを調べる
`Optional` 型の値 `opt` が有効値を持つ場合、`opt.has_value()` が `true` を返します。`if (opt)` や `if(not opt)` で調べることもできます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/optional/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Optional<Point> pos1;

	Optional<Point> pos2 = Point{ 100, 200 };

	Print << pos1.has_value();

	Print << pos2.has_value();
	
	if (not pos1)
	{
		Print << U"pos1 does not have a value";
	}

	if (pos2)
	{
		Print << U"pos2 has a value";
	}

	while (System::Update())
	{

	}
}
```

## 23.3 有効値にアクセスする
`Optional` 型の値 `opt` が有効値を持つ場合、`*opt` でその値にアクセスできます。`opt->x` や `opt->y` のように、`->` 演算子を使ってメンバにアクセスすることもできます。有効値を持たないときに値にアクセスしてはいけません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/optional/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
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

## 23.4 無効値にする
`none` は `Optional` 型の無効値を表す定数です。`Optional` 型の値 `opt` に無効値を代入するには、`opt = none` とします。`opt.reset()` でも同じです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/optional/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Optional<Point> pos = Point{ 100, 200 };

	Print << pos.has_value();

	pos = none;

	Print << pos.has_value();

	pos = Point{ 300, 400 };

	// = none と同じ
	pos.reset();

	Print << pos.has_value();

	while (System::Update())
	{

	}
}
```

## 23.5 有効値または代わりの値を返す
`Optional` 型の値 `opt` が有効値を持つ場合、`opt.value_or(defaultValue)` は有効値の値を返し、無効値である場合に `defaultValue` を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/optional/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Optional<Point> pos1;

	Optional<Point> pos2 = Point{ 100, 200 };

    // pos1 は有効値を持たないため Point{ 0, 0 } を返す
	Print << pos1.value_or(Point{ 0, 0 });

    // pos2 は有効値を持つため Point{ 100, 200 } を返す
	Print << pos2.value_or(Point{ 0, 0 });

	while (System::Update())
	{

	}
}
```

## 23.6 if との組み合わせ
`Optional` 型を返す関数の戻り値を `if ()` 内で受け取り、有効値を持つかを調べることができます。`Optional` を使うコードを短く書くのに役立ちます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/optional/6.png)

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


## 23.7 活用例
`Optional` を使うことで、次のように 1 つの変数だけで「マウスの左ボタンが押された位置」と「矢印を描くか」を表現できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/optional/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// マウスの左ボタンが押された位置
	Optional<Point> start;
	
	while (System::Update())
	{
		if (MouseL.down()) // マウスの左ボタンが押されたら
		{
			// マウスカーソルの位置を有効値として記録する
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
			// 現在のマウスカーソルの位置まで矢印を描く
			Line{ *start, Cursor::Pos() }.drawArrow(6, SizeF{ 20, 20 }, Palette::Orange);
		}
	}
}
```

