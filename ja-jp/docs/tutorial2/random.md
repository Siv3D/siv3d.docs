# 29. ランダム
数や色、座標をランダムに生成したり、複数の選択肢から要素をランダムに選択したりする方法を学びます。

Siv3D は乱数に関する次のような機能を提供します。

- 乱数を生成する乱数エンジンクラス
- 乱数を特定の分布に分散せる分布クラス
- それらを組み合わせて実装されている乱数関数

Siv3D の乱数関数では、特に指定しない場合、Siv3D が内部でスレッドごとに確保するデフォルトの乱数エンジンが使われます。それらのシード値はハードウェアノイズによって決定されるため、プログラムを実行するたびに異なる乱数列を生成します。

乱数の再現性が必要な場合は、乱数エンジンに固定のシードを与えるか、乱数エンジンオブジェクトを固定シードで作成し、乱数関数に渡します。

## 29.1 ランダムな整数
`Random<Type>(max)` は 0 から max までの範囲の `Type` 型のランダムな値を、`Random<Type>(min, max)` は min から　max の範囲の `Type` 型のランダムな値を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"int32", Vec2{ 200, 20 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// 0～100 の範囲のランダムな整数
				Print << Random(100);
			}
		}

		if (SimpleGUI::Button(U"double", Vec2{ 200, 60 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// -100.0～100.0 の範囲のランダムな浮動小数点数
				Print << Random(-100.0, 100.0);
			}
		}

		if (SimpleGUI::Button(U"char32", Vec2{ 200, 100 }))
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

## 29.2 ランダムな整数
`int8`, `uint8` ～ `int64`, `uint64` までの整数型に対応した乱数生成関数が用意されています。

| 型 | 関数 | 値の範囲 | 
| --- | --- | --- |
| `int8` | `RandomInt8()` | -128 ～ 127 |
| `uint8` | `RandomUint8()` | 0 ～ 255 |
| `int16` | `RandomInt16()` | -32768 ～ 32767 |
| `uint16` | `RandomUint16()` | 0 ～ 65535 |
| `int32` | `RandomInt32()` | -2147483648 ～ 2147483647 |
| `uint32` | `RandomUint32()` | 0 ～ 4294967295 |
| `int64` | `RandomInt64()` | -9223372036854775808 ～ 9223372036854775807 |
| `uint64` | `RandomUint64()` | 0 ～ 18446744073709551615 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"int8", Vec2{ 200, 20 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// int8 の範囲のランダムな整数
				Print << RandomInt8();
			}
		}

		if (SimpleGUI::Button(U"uint8", Vec2{ 200, 60 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// uint8 の範囲のランダムな整数
				Print << RandomUint8();
			}
		}

		if (SimpleGUI::Button(U"uint32", Vec2{ 200, 100 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// uint32 の範囲のランダムな整数
				Print << RandomUint32();
			}
		}
	}
}
```


## 29.3 デフォルトの乱数エンジンのシードを設定する
`Reseed(seed)` を使うと、内部の乱数エンジンを指定したシードでリセット初期化できます。シードが同じであれば、生成される乱数のパターンは同じになります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"seed 1", Vec2{ 200, 20 }))
		{
			ClearPrint();

			Reseed(123456789ull);

			for (int32 i = 0; i < 10; ++i)
			{
				// 1～6 の範囲のランダムな整数
				Print << Random(1, 6);
			}
		}

		if (SimpleGUI::Button(U"seed 2", Vec2{ 200, 60 }))
		{
			ClearPrint();

			Reseed(3333333333ull);

			for (int32 i = 0; i < 10; ++i)
			{
				// 1～6 の範囲のランダムな整数
				Print << Random(1, 6);
			}
		}

		if (SimpleGUI::Button(U"seed 3", Vec2{ 200, 100 }))
		{
			ClearPrint();

			Reseed(55555555555555ull);

			for (int32 i = 0; i < 10; ++i)
			{
				// 1～6 の範囲のランダムな整数
				Print << Random(1, 6);
			}
		}
	}
}
```


## 29.4 使用する乱数エンジンオブジェクトを指定する
乱数関数の最後の引数に乱数エンジンオブジェクトを渡すことで、内部の乱数エンジンの代わりに、その乱数エンジンオブジェクトを使用して乱数を生成できます。内部の乱数エンジンに依存したくない場合にこの方法を使います。乱数を生成するたびに乱数エンジンオブジェクトの内部状態は更新されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const uint64 seed = RandomUint64();

	// 軽量な乱数エンジン
	SmallRNG rng{ seed };

	for (int32 i = 0; i < 10; ++i)
	{
		Print << Random(1, 6, rng);
	}

	while (System::Update())
	{

	}
}
```


## 29.5 半々の確率
`RandomBool()` は、呼び出しのたびに 50% の確率で `true`, 50% の確率で `false` を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
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


## 29.6 指定した確率で `true` を返す
`RandomBool(p)` は、`true` になる確率を 0.0 (0%) ～ 1.0 (100%) の範囲で 指定できます。10% の確率で `true` を返してほしい場合の `p` は `0.1`, 25% の確率で `true` を返してほしい場合の `p` は `0.25` です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
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


## 29.7 ランダムな色
`RandomColorF()` はランダムな色を生成して `ColorF` 型で返します。色は `HSV{ Random(360.0), 1.0, 1.0 }` によって生成されるため、淡い色や暗い色、白や黒は生成されません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/7.png)

```cpp
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
		if (SimpleGUI::Button(U"Reset", Vec2{ 20, 20 }))
		{
			for (auto& color : colors)
			{
				color = RandomColor();
			}
		}

		for (size_t i = 0; i < colors.size(); ++i)
		{
			Circle{ (50 + i * 50.0), 100, 20 }.draw(colors[i]);
		}
	}
}
```


## 29.8 ランダムな `Vec2`

`RandomVec2()` は、条件に沿ったランダムな `Vec2` 型の値を返します。条件は引数として渡す値によって異なります。

| 関数 | 説明 |
|---|---|
|`RandomVec2()`|長さが 1.0 のランダムなベクトル|
|`RandomVec2(double)`|指定した長さを持つランダムなベクトル|
|`RandomVec2(const Line&)`|指定した線分上にあるランダムな点の位置ベクトル|
|`RandomVec2(const Circle&)`|指定した円内にあるランダムな点の位置ベクトル|
|`RandomVec2(const RectF&)`|指定した長方形内にあるランダムな点の位置ベクトル|
|`RandomVec2(const Triangle&)`|指定した三角形内にあるランダムな点の位置ベクトル|
|`RandomVec2(const Quad&)`|指定した四角形内にあるランダムな点の位置ベクトル|

### 29.8.1 ランダムな座標
図形の内部にあるランダムな点を生成するプログラムです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/8.1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Line や Circle, Triangle, Quad も OK
	const RectF shape{ 100, 100, 400, 300 };

	Array<Vec2> points;

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Generate", Vec2{ 20, 20 }))
		{
			points.clear();

			for (int32 i = 0; i < 100; ++i)
			{
				// shape 内にあるランダムな点
				points << RandomVec2(shape);
			}
		}

		shape.draw(Palette::Gray);

		for (const auto& point : points)
		{
			point.asCircle(3).draw();
		}
	}
}
```

### 29.8.2 ランダムなベクトル
30 ピクセルずつランダムな方向に移動しながら線を引いていくプログラムです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/8.2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	LineString lines = { Vec2{ 400, 300 } };

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Move", Vec2{ 20, 20 }))
		{
			// 30 ピクセル移動
			lines << (lines.back() + RandomVec2(30));
		}

		lines.draw(2);
	}
}
```


## 29.9 配列中のランダムな要素
`Array` のメンバ関数 `.choice()` は、配列の中のランダムな要素を返します。空の配列に対して使うことはできません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<String> options =
	{
		U"Red", U"Green", U"Blue", U"Black", U"White"
	};

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
		{
			ClearPrint();

			// ランダムな要素を返す
			Print << options.choice();
		}
	}
}
```


## 29.10 配列中のランダムな複数の要素
`Array` の `.choice(N)` は、配列の中から重複なく N 個のランダムな要素を選択し、配列で返します。結果の配列の要素の順番は、元の配列内での順序と同じです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<int32> coins =
	{
		1, 2, 5, 10, 20, 50, 100
	};

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
		{
			ClearPrint();

			// ランダムな要素を 3 つ返す
			Print << coins.choice(3);
		}
	}
}
```


## 29.11 配列のシャッフル
`Array` の `.shuffle()` は配列の要素の順番をランダムにシャッフルします。一方 `.shuffled()` を使うと、自身は変更せずに、シャッフルした新しい配列を作成して返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/11.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<String> cities =
	{
		U"Guangzhou", U"Tokyo", U"Shanghai", U"Jakarta",
		U"Delhi", U"Metro Manila", U"Mumbai", U"Seoul",
		U"Mexico City", U"São Paulo", U"New York City", U"Cairo",
	};

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Shuffle", Vec2{ 200, 100 }))
		{
			ClearPrint();

			// 要素の順番をシャッフル
			cities.shuffle();

			Print << cities;
		}
	}
}
```


## 29.12 ランダムに 1 個選ぶ
`Sample(options)` は、`{}` で渡した複数の選択肢から要素をランダムに 1 個選択して返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/12.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
		{
			ClearPrint();

			// ランダムに選択
			Print << Sample({ 1, 2, 5, 10, 20, 50, 100 });
		}
	}
}
```


## 29.13 ランダムに複数個選ぶ
`Sample(N, options)` は、`{}` で渡した複数の選択肢から要素をランダムに N 個選択して返します。`Array` の `.choice()` と同様に、結果の配列内の要素の順番は、元の順序と同じです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/13.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
		{
			ClearPrint();

			// ランダムに 3 個選択
			Print << Sample(3, { 1, 2, 5, 10, 20, 50, 100 });
		}
	}
}
```


## 29.14 選択肢に対して出現確率を指定してランダムに 1 個選ぶ
確率にバイアスがある複数の選択肢からランダムな結果を選択するときは `DiscreteSample` 関数を使います。第 1 引数が選択肢で、配列で用意します。第 2 引数が選択肢の確率分布で、`DiscreteDistribution` 型で用意します。確率分布は `double` 型の値で指定し、合計は特定の数になる必要はありません。例えば `{ 2.0, 12.0, 6.0 }` なら 10%, 60%, 30% と割り振られます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/14.png)

```cpp
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
	DiscreteDistribution distribution{
	{
		1000,
		200,
		50,
		10,
		5,
		2,
		1,
	} };

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2{ 200, 20 }))
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


## 29.15 正規分布
乱数を生成するときに、正規分布に従う乱数を生成したいことがあります。`NormalDistribution` は、平均と標準偏差を指定して正規分布を作成します。`GetDefaultRNG()` で取得したデフォルトの乱数生成器を引数に渡し、`()` 演算子で正規分布に従った乱数を生成します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/15.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 平均 40, 標準偏差 8 の正規分布
	NormalDistribution dist{ 40.0, 8.0 };

	Array<int32> counts(80);

	auto& rng = GetDefaultRNG();

	for (int32 i = 0; i < 10000; ++i)
	{
		const double x = dist(rng);

        // 0 以上 79 の範囲の整数に変換
		const int32 xi = Clamp(static_cast<int32>(x), 0, 79);

		++counts[xi];
	}

	while (System::Update())
	{
		for (int32 i = 0; i < 80; ++i)
		{
			Rect{ Arg::bottomLeft((i * 10), 600), 10, counts[i] }
			    .draw(HSV{ (i * 3), 0.5, 0.9 });
		}
	}
}
```


## 29.16 （練習）パスワードジェネレータ
ランダムな英数字でパスワードを作成する、パスワードジェネレータのサンプルです。`Clipboard::SetText(s)` は、文字列 `s` をクリップボードにコピーします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/random/16.png)

```cpp
# include <Siv3D.hpp>

String GeneratePassword()
{
	String s;

	for (int32 i = 0; i < 16; ++i)
	{
		s << Random(U'!', U'~');
	}

	return s;
}

void Main()
{
	String password = GeneratePassword();

	while (System::Update())
	{
		ClearPrint();

		Print << password;

		if (SimpleGUI::Button(U"Generate", Vec2{ 200, 40 }))
		{
			password = GeneratePassword();
		}

		if (SimpleGUI::Button(U"Copy to clipboard", Vec2{ 200, 80 }))
		{
			// 文字列をクリップボードにコピーする
			Clipboard::SetText(password);
		}
	}
}
```

