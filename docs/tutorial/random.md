
!!! warning "This is the documentation for an old version"
	This is the documentation for an old version of Siv3D (v0.4.3). See [Siv3D Reference v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation-en) for the latest version.

# 14. Random

この章では、数や色、座標をランダムに生成したり、配列から無作為に要素を選択したりする方法を学びます。

## 14.1 半々の確率
`RandomBool()` は 50% の確率で `true`, 50% の確率で `false` を返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/1-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2(300, 20)))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// 50% の確率で true, 50% の確率で false
				Print << RandomBool();
			}
		}
	}
}
```


## 14.2 確率を指定
`RandomBool()` には 0.0 ～ 1.0 の範囲で `true` になる確率を指定できます。10% の確率で `true` を返してほしい場合は `0.1` を、25% の確率で `true` を返してほしい場合は `0.25` を渡します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/2-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2(300, 20)))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// 30% の確率で true, 70% の確率で false
				Print << RandomBool(0.3);
			}
		}
	}
}
```


## 14.3 ランダムな数
`Random<Type>(max)` は 0 から max までのランダムな数を、`Random<Type>(min, max)` は min から　max の範囲でランダムな数を返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/3-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"int32", Vec2(200, 20)))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// 0～100 の範囲のランダムな整数
				Print << Random(100);
			}
		}

		if (SimpleGUI::Button(U"double", Vec2(200, 60)))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// -100.0～100.0 の範囲のランダムな浮動小数点数
				Print << Random(-100.0, 100.0);
			}
		}

		if (SimpleGUI::Button(U"char32", Vec2(200, 100)))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// A～Z の範囲のランダムな文字
				Print << Random(U'A', U'Z');
			}
		}
	}
}
```


## 14.4 ランダムな色
`RandomColorF()` はランダムな色を `HSV(Random(360.0), 1.0, 1.0)` という式で生成して `ColorF` 型で返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/4-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Array<ColorF> colors;

	for (int32 i = 0; i < 10; ++i)
	{
		// ランダムな色
		colors << RandomColor();
	}

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2(20, 20)))
		{
			for (auto& color : colors)
			{
				color = RandomColor();
			}
		}

		for (size_t i : step(colors.size()))
		{
			Circle(50 + i * 50.0, 100, 20).draw(colors[i]);
		}
	}
}
```


## 14.5 ランダムな座標


### RandomVec2(double)

`RandomVec2(double)` は、指定した長さを持つランダムなベクトルを `Vec2` 型で返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/5-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Vec2 center = Scene::Center();

	// 長さが 200 のランダムなベクトル
	Vec2 dir = RandomVec2(200);
	
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2(20, 20)))
		{
			// 長さが 200 のランダムなベクトル
			dir = RandomVec2(200);
		}

		Circle(center, 20).draw();

		Circle(center, 200).drawFrame(1, 1, Palette::Gray);

		Circle(center + dir, 10).draw();
	}
}
```


### RandomVec2(RectF)

`RandomVec2(RectF)` は、指定した長方形の内部のランダムな位置を `Vec2` 型で返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/5-1.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	constexpr RectF rect(100, 100, 400, 300);
	
	Array<Vec2> points;

	for (size_t i = 0; i < 100; ++i)
	{
		// rect の内部のランダムな点
		points << RandomVec2(rect);
	}

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2(20, 20)))
		{
			for (auto& point : points)
			{
				// rect の内部のランダムな点
				point = RandomVec2(rect);
			}
		}

		rect.draw(Palette::Gray);

		for (const auto& point : points)
		{
			Circle(point, 3).draw();
		}
	}
}
```


### RandomVec2(Circle)

`RandomVec2(Circle)` は、指定した円の内部のランダムな位置を `Vec2` 型で返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/5-2.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	constexpr Circle circle(300, 200, 150);
	
	Array<Vec2> points;

	for (size_t i = 0; i < 100; ++i)
	{
		// circle の内部のランダムな点
		points << RandomVec2(circle);
	}

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2(20, 20)))
		{
			for (auto& point : points)
			{
				// circle の内部のランダムな点
				point = RandomVec2(circle);
			}
		}

		circle.draw(Palette::Gray);

		for (const auto& point : points)
		{
			Circle(point, 3).draw();
		}
	}
}
```


### RandomVec2(Triangle)

`RandomVec2(Triangle)`  は、指定した三角形の内部のランダムな位置を `Vec2` 型で返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/5-3.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	constexpr Triangle triangle(100, 100, 500, 300, 200, 300);
	
	Array<Vec2> points;

	for (size_t i = 0; i < 100; ++i)
	{
		// triangle の内部のランダムな点
		points << RandomVec2(triangle);
	}

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2(20, 20)))
		{
			for (auto& point : points)
			{
				// triangle の内部のランダムな点
				point = RandomVec2(triangle);
			}
		}

		triangle.draw(Palette::Gray);

		for (const auto& point : points)
		{
			Circle(point, 3).draw();
		}
	}
}
```


## 14.6 配列中のランダムな要素
`Array::choice()` は、配列の中のランダムな要素を返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/6-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Array<String> options =
	{
		U"Red", U"Green", U"Blue", U"Black", U"White"
	};

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2(200, 20)))
		{
			ClearPrint();

			// ランダムな要素を返す
			Print << options.choice();
		}
	}
}
```


## 14.7 配列中のランダムな複数の要素
`Array::choice()` に個数を渡すと、配列の中から、重複なくその個数だけランダムな要素を選択し、配列で返します。要素の順番は配列内での順序と同じです。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/7-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Array<String> options =
	{
		U"Red", U"Green", U"Blue", U"Black", U"White"
	};

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2(200, 20)))
		{
			ClearPrint();

			// ランダムな要素を 3 つ返す
			Print << options.choice(3);
		}
	}
}
```


## 14.8 配列のシャッフル
`Array::shuffle()` は配列の要素の順番をランダムにシャッフルします。`Array::shuffled()` を使うと、自身は変更せずに、シャッフルした新しい配列を作成して返します。 

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/8-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Array<String> options =
	{
		U"Red", U"Green", U"Blue", U"Black", U"White"
	};

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Shuffle", Vec2(300, 20)))
		{
			ClearPrint();

			// 要素の順番をシャッフル
			options.shuffle();

			Print << options;
		}
	}
}
```


## 14.9 ランダムに選択
`Sample()` を使うと、`{}` で渡した複数の選択肢から要素をランダムに選択できます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/9-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2(300, 20)))
		{
			ClearPrint();

			// ランダムに選択
			Print << Sample({ 1, 2, 5, 10, 20, 50, 100 });
		}
	}
}
```

第 1 引数に個数、第 2 引数に選択肢を渡すこともできます。`Arrai::choice()` の時と同様に、要素の順番は最初に渡された順序と同じです。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/9-1.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2(300, 20)))
		{
			ClearPrint();

			// ランダムに 3 個選択
			Print << Sample(3, { 1, 2, 5, 10, 20, 50, 100 });
		}
	}
}
```


## 14.10 出現確率
確率にバイアスがある複数の選択肢からランダムな結果を選択するときは `DiscreteSample` を使います。選択肢を配列で、選択肢の確率分布を `DiscreteDistribution` で準備しておく必要があります。確率分布は `double` 型の値で指定し、合計が特定の数になる必要はありません。{1, 6, 3} なら 10%, 60%, 30% と割り振られます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/14/10-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	// 選択肢
	const Array<String> options =
	{
		U"$0",
		U"$1",
		U"$5",
		U"$20",
		U"$100",
		U"$500",
		U"$2000",
	};

	// 選択肢に対応する確率分布
	// （$0 は $2000 よりも 1000 倍出やすい）
	DiscreteDistribution distribution(
	{
		1000,
		200,
		50,
		10,
		5,
		2,
		1,
	});

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2(300, 20)))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// 確率分布に基づいてランダムに選択
				Print << DiscreteSample(options, distribution);
			}
		}
	}
}
```

