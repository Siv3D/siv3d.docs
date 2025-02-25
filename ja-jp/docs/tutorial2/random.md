# 39. ランダム
ランダムな数や色、座標を生成したり、複数の選択肢から要素をランダムに選択したりする方法を学びます。

## 39.1 乱数の概要
- Siv3D は乱数に関する次のような機能を提供します。
    - 乱数を生成する乱数エンジンクラス
    - 乱数を特定の分布に分散せる分布クラス
    - それらを組み合わせて実装される乱数関数
- 特に指定しない場合、Siv3D が内部でスレッドごとに確保する**グローバル乱数エンジン**が使われます
- グローバル乱数エンジンのシード値は非決定的なハードウェアノイズによって決定されるため、プログラムを実行するたびに異なる乱数列を生成します
- 乱数の再現性が必要な場合は、乱数エンジンに固定のシードを与えるか、乱数エンジンオブジェクトを固定シードで作成し、乱数関数に渡します

## 39.2 範囲を指定した乱数（1）
- `Random<Type>(max)` は 0 から max までの範囲の `Type` 型のランダムな値を返します
- `Random<Type>(min, max)` は min から　max の範囲の `Type` 型のランダムな値を返します
- 整数の場合は `[min, max]` の範囲、浮動小数点数の場合は `[min, max)` の範囲の値が返されます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"int32", Vec2{ 200, 20 }, 120))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// 0～100 の範囲のランダムな整数
				Print << Random(100);
			}
		}

		if (SimpleGUI::Button(U"double", Vec2{ 200, 60 }, 120))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// -100.0～100.0 の範囲のランダムな浮動小数点数
				Print << Random(-100.0, 100.0);
			}
		}

		if (SimpleGUI::Button(U"char32", Vec2{ 200, 100 }, 120))
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


## 39.3 範囲を指定した乱数（2）
- 特定の整数型の範囲に対応した乱数生成関数が用意されています

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
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"int8", Vec2{ 200, 20 }, 120))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// int8 の範囲のランダムな整数
				Print << RandomInt8();
			}
		}

		if (SimpleGUI::Button(U"uint8", Vec2{ 200, 60 }, 120))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// uint8 の範囲のランダムな整数
				Print << RandomUint8();
			}
		}

		if (SimpleGUI::Button(U"uint32", Vec2{ 200, 100 }, 120))
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


## 39.4 グローバル乱数エンジンのシード初期化
- グローバル乱数エンジンを新しいシードで初期化するには `Reseed(uint64 seed)` を使います
- シードが同じであれば、生成される乱数のパターンは同じになります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/4.png)

```cpp
# include <Siv3D.hpp>

void Generate()
{
	for (int32 i = 0; i < 10; ++i)
	{
		Print << Random(1, 6);
	}
}

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"seed 1", Vec2{ 200, 20 }, 120))
		{
			ClearPrint();
			Reseed(123456789ull);
			Generate();
		}

		if (SimpleGUI::Button(U"seed 2", Vec2{ 200, 60 }, 120))
		{
			ClearPrint();
			Reseed(3333333333ull);
			Generate();
		}

		if (SimpleGUI::Button(U"seed 3", Vec2{ 200, 100 }, 120))
		{
			ClearPrint();
			Reseed(55555555555555ull);
			Generate();
		}
	}
}
```


## 39.5 独立した乱数エンジンオブジェクトの作成
- グローバル乱数エンジンから独立した乱数エンジンオブジェクトを作成し、乱数関数で使うことができます
- ほとんどの用途では、`SmallRNG` クラスを使うことで十分な品質の乱数が得られます
- `SmallRNG` は、`uint64` 型のシード値で初期化します
- 各種乱数関数の最後の引数に乱数エンジンオブジェクトを渡すことで、グローバル乱数エンジンの代わりに、その乱数エンジンが使われます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/5.png)

```cpp hl_lines="5 9"
# include <Siv3D.hpp>

void Generate(uint64 seed)
{
	SmallRNG rng{ seed };

	for (int32 i = 0; i < 10; ++i)
	{
		Print << Random(1, 6, rng);
	}
}

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"seed 1", Vec2{ 200, 20 }, 120))
		{
			ClearPrint();
			Generate(123456789ull);
		}

		if (SimpleGUI::Button(U"seed 2", Vec2{ 200, 60 }, 120))
		{
			ClearPrint();
			Generate(3333333333ull);
		}

		if (SimpleGUI::Button(U"seed 3", Vec2{ 200, 100 }, 120))
		{
			ClearPrint();
			Generate(55555555555555ull);
		}
	}
}
```


## 39.6 50 % の確率
- `RandomBool()` は、呼び出しのたびに 50% の確率で `true`, 50% の確率で `false` を返します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Generate", Vec2{ 200, 20 }))
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


## 39.7 指定した確率で `true` を返す
- `RandomBool(p)` は、呼び出しのたびに `p` の確率で `true`, `(1 - p)` の確率で `false` を返します
	- 10% の確率で `true` を返してほしい場合、`p` は `0.1` です
	- 25% の確率で `true` を返してほしい場合、`p` は `0.25` です
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"10 %", Vec2{ 200, 20 }, 120))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				Print << RandomBool(0.1);
			}
		}

		if (SimpleGUI::Button(U"50 %", Vec2{ 200, 60 }, 120))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				Print << RandomBool(0.5);
			}
		}

		if (SimpleGUI::Button(U"80 %", Vec2{ 200, 100 }, 120))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				Print << RandomBool(0.8);
			}
		}
	}
}
```


## 39.8 ランダムな色
- `RandomColorF()` はランダムな色を生成して `ColorF` 型で返します
- 色は `HSV{ Random(360.0), 1.0, 1.0 }` によって生成されるため、淡い色や暗い色、白や黒は生成されません
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	Array<ColorF> colors(10);

	for (auto& color : colors)
	{
		color = RandomColor();
	}

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Generate", Vec2{ 40, 40 }))
		{
			for (auto& color : colors)
			{
				color = RandomColor();
			}
		}

		for (size_t i = 0; i < colors.size(); ++i)
		{
			Circle{ (40 + i * 80.0), 200, 30 }
				.drawShadow(Vec2{ 2, 2 }, 12, 1)
				.draw(colors[i]);
		}
	}
}
```


## 39.9 ランダムな座標
- 指定した図形上にあるランダムな点の座標を生成する関数が用意されています

| コード | 説明 |
|---|---|
|`RandomVec2(const Line&)`|指定した線分上にあるランダムな点の座標を返す|
|`RandomVec2(const Circle&)`|指定した円の上にあるランダムな点の座標を返す|
|`RandomVec2(const RectF&)`|指定した長方形上にあるランダムな点の座標を返す|
|`RandomVec2(const Triangle&)`|指定した三角形上にあるランダムな点の座標を返す|
|`RandomVec2(const Quad&)`|指定した四角形上にあるランダムな点の座標を返す|
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Rect rect{ 100, 150, 300, 200 };
	const Circle circle{ 600, 400, 150 };

	Array<Vec2> rectPoints(100);
	Array<Vec2> circlePoints(100);

	for (auto& point : rectPoints)
	{
		point = RandomVec2(rect);
	}

	for (auto& point : circlePoints)
	{
		point = RandomVec2(circle);
	}

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Generate", Vec2{ 40, 40 }))
		{
			for (auto& point : rectPoints)
			{
				point = RandomVec2(rect);
			}

			for (auto& point : circlePoints)
			{
				point = RandomVec2(circle);
			}
		}

		rect.draw(ColorF{ 0.2 });
		circle.draw(ColorF{ 0.2 });

		for (const auto& point : rectPoints)
		{
			point.asCircle(3).draw(ColorF{ 0.2, 1.0, 0.4 });
		}

		for (const auto& point : circlePoints)
		{
			point.asCircle(3).draw(ColorF{ 0.2, 0.4, 1.0 });
		}
	}
}
```


## 39.10 ランダムなベクトル
- 指定した長さのランダムな二次元ベクトルを生成する関数が用意されています
	
| コード | 説明 |
|---|---|
|`RandomVec2()`| 長さが 1.0 で向きがランダムなベクトルを返す |
|`RandomVec2(double)`| 指定した長さを持つランダムなベクトルを返す |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/10.png)

```cpp title="30 ピクセルずつランダムな方向に移動しながら線を引く"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	LineString lines = { Vec2{ 400, 300 } };

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Move", Vec2{ 40, 40 }))
		{
			// 30 ピクセル移動した新しい点を追加する
			lines << (lines.back() + RandomVec2(30));
		}

		lines.draw(2, ColorF{ 0.2 });

		lines.back().asCircle(5).draw(Palette::Seagreen);
	}
}
```


## 39.11 配列中のランダムな要素
- `Array` のメンバ関数 `.choice()` は、配列の中のランダムな要素への参照を返します
- 空の配列に対して使うことはできません。
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/11.png)

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
		if (SimpleGUI::Button(U"Choice", Vec2{ 200, 40 }))
		{
			// ランダムな要素を返す
			Print << options.choice();
		}
	}
}
```


## 39.12 配列中のランダムな複数の要素
- `Array` の `.choice(N)` は、配列の中から重複なく N 個のランダムな要素を選択し、配列で返します
- 結果の配列の要素の順序は、元の配列内での順序と同じです
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/12.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<int32> coins =
	{
		1, 2, 5, 10, 20, 50, 100, 500
	};

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Choice", Vec2{ 200, 40 }))
		{
			// ランダムな要素を 3 つ返す
			Print << coins.choice(3);
		}
	}
}
```


## 39.13 配列のシャッフル
- `Array` の `.shuffle()` は配列の要素の順番をランダムにシャッフルします
- 一方 `.shuffled()` を使うと、自身は変更せずに、シャッフルした新しい配列を作成して返します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/13.png)

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

			// 要素の順番をシャッフルする
			cities.shuffle();

			Print << cities;
		}
	}
}
```


## 39.14 ランダムに 1 個選ぶ
- `Sample({...})` は、リスト `{...}` で渡した選択肢から要素をランダムに 1 個選択して返します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/14.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Sample", Vec2{ 200, 40 }))
		{
			// ランダムに選択する
			Print << Sample({ 1, 2, 5, 10, 20, 50, 100, 500 });
		}
	}
}
```


## 39.15 ランダムに複数個選ぶ
- `Sample(N, {...})` は、リスト `{...}` で渡した複数の選択肢から要素をランダムに N 個選択して返します
- `結果の配列内の要素の順序は、リスト内の順序と同じです
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/15.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Sample", Vec2{ 200, 40 }))
		{
			// ランダムに 3 個選択する
			Print << Sample(3, { 1, 2, 5, 10, 20, 50, 100, 500 });
		}
	}
}
```


## 39.16 出現確率の指定
- 確率にバイアスがある複数の選択肢からランダムな結果を選択するときは `DiscreteSample` 関数を使います
- 第 1 引数が選択肢で、配列で用意します
- 第 2 引数が選択肢の確率分布で、`DiscreteDistribution` 型で用意します
- 確率分布は `double` 型の値で指定し、合計は特定の数になる必要はありません
	- 例えば `{ 2.0, 12.0, 6.0 }` なら 10%, 60%, 30% と割り振られます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/16.png)

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
		if (SimpleGUI::Button(U"Sample", Vec2{ 200, 40 }))
		{
			// 確率分布に基づいてランダムに選択する
			Print << DiscreteSample(options, distribution);
		}
	}
}
```


## 39.17 正規分布
- 正規分布に従う乱数を生成するには、`NormalDistribution` クラスを使います
- 平均と標準偏差を指定して正規分布を作成し、`()` 演算子で正規分布に従った乱数を生成します
- `GetDefaultRNG()` で取得したグローバル乱数エンジンを使うことができます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/17.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 平均 40, 標準偏差 8 の正規分布
	NormalDistribution dist{ 40.0, 8.0 };

	Array<int32> counts(80);

	// グローバル乱数エンジン
	auto& rng = GetDefaultRNG();

	for (int32 i = 0; i < 10000; ++i)
	{
		const double x = dist(rng);

		// 結果を 0 以上 79 の範囲の整数に変換する
		const int32 xi = Clamp(static_cast<int32>(x), 0, 79);

		++counts[xi];
	}

	while (System::Update())
	{
		// ヒストグラムを描画する
		for (int32 i = 0; i < 80; ++i)
		{
			Rect{ Arg::bottomLeft((i * 10), 600), 10, counts[i] }
				.draw(HSV{ (i * 3), 0.5, 0.9 });
		}
	}
}
```


## 39.18 （参考）パスワードジェネレータ
- ランダムな英数字でパスワードを作成する、パスワードジェネレータのサンプルです
- `Clipboard::SetText(s)` は、文字列 `s` をクリップボードにコピーします
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/18.png)

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
