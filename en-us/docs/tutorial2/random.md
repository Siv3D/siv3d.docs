# 39. Random
Learn how to generate random numbers, colors, and coordinates, or randomly select elements from multiple choices.

## 39.1 Random Number Overview
- Siv3D provides the following functionality related to random numbers:
    - Random number engine classes that generate random numbers
    - Distribution classes that distribute random numbers to specific distributions
    - Random number functions implemented by combining these
- Unless otherwise specified, Siv3D uses a **global random number engine** secured internally for each thread
- The seed value of the global random number engine is determined by non-deterministic hardware noise, so it generates different random number sequences each time the program is run
- When random number reproducibility is needed, give a fixed seed to the random number engine, or create a random number engine object with a fixed seed and pass it to random number functions

## 39.2 Random Numbers with Specified Range (1)
- `Random<Type>(max)` returns a random value of `Type` in the range from 0 to max
- `Random<Type>(min, max)` returns a random value of `Type` in the range from min to max
- For integers, values in the range `[min, max]` are returned; for floating-point numbers, values in the range `[min, max)` are returned
	
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
				// Random integer in range 0-100
				Print << Random(100);
			}
		}

		if (SimpleGUI::Button(U"double", Vec2{ 200, 60 }, 120))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// Random floating-point number in range -100.0 to 100.0
				Print << Random(-100.0, 100.0);
			}
		}

		if (SimpleGUI::Button(U"char32", Vec2{ 200, 100 }, 120))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// Random character in range A-Z
				Print << Random(U'A', U'Z');
			}
		}
	}
}
```


## 39.3 Random Numbers with Specified Range (2)
- Random number generation functions corresponding to specific integer type ranges are provided

| Type | Function | Value Range | 
| --- | --- | --- |
| `int8` | `RandomInt8()` | -128 to 127 |
| `uint8` | `RandomUint8()` | 0 to 255 |
| `int16` | `RandomInt16()` | -32768 to 32767 |
| `uint16` | `RandomUint16()` | 0 to 65535 |
| `int32` | `RandomInt32()` | -2147483648 to 2147483647 |
| `uint32` | `RandomUint32()` | 0 to 4294967295 |
| `int64` | `RandomInt64()` | -9223372036854775808 to 9223372036854775807 |
| `uint64` | `RandomUint64()` | 0 to 18446744073709551615 |
	
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
				// Random integer in int8 range
				Print << RandomInt8();
			}
		}

		if (SimpleGUI::Button(U"uint8", Vec2{ 200, 60 }, 120))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// Random integer in uint8 range
				Print << RandomUint8();
			}
		}

		if (SimpleGUI::Button(U"uint32", Vec2{ 200, 100 }, 120))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// Random integer in uint32 range
				Print << RandomUint32();
			}
		}
	}
}
```


## 39.4 Global Random Engine Seed Initialization
- To initialize the global random engine with a new seed, use `Reseed(uint64 seed)`
- If the seed is the same, the generated random number pattern will be the same

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


## 39.5 Creating Independent Random Engine Objects
- You can create random engine objects independent from the global random engine and use them with random functions
- For most purposes, using the `SmallRNG` class provides sufficient quality random numbers
- `SmallRNG` is initialized with a `uint64` type seed value
- By passing a random engine object as the last argument to various random functions, that random engine is used instead of the global random engine
	
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


## 39.6 50% Probability
- `RandomBool()` returns `true` with 50% probability and `false` with 50% probability each time it's called
	
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
				// 50% probability true, 50% probability false
				Print << RandomBool();
			}
		}
	}
}
```


## 39.7 Returning `true` with Specified Probability
- `RandomBool(p)` returns `true` with probability `p` and `false` with probability `(1 - p)` each time it's called
	- If you want `true` with 10% probability, `p` is `0.1`
	- If you want `true` with 25% probability, `p` is `0.25`
	
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


## 39.8 Random Colors
- `RandomColorF()` generates a random color and returns it as a `ColorF` type
- Colors are generated by `HSV{ Random(360.0), 1.0, 1.0 }`, so pale colors, dark colors, white, or black are not generated
	
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


## 39.9 Random Coordinates
- Functions to generate random point coordinates on specified shapes are provided

| Code | Description |
|---|---|
|`RandomVec2(const Line&)`|Returns random point coordinates on the specified line segment|
|`RandomVec2(const Circle&)`|Returns random point coordinates on the specified circle|
|`RandomVec2(const RectF&)`|Returns random point coordinates on the specified rectangle|
|`RandomVec2(const Triangle&)`|Returns random point coordinates on the specified triangle|
|`RandomVec2(const Quad&)`|Returns random point coordinates on the specified quadrilateral|
	
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


## 39.10 Random Vectors
- Functions to generate random two-dimensional vectors with specified lengths are provided
	
| Code | Description |
|---|---|
|`RandomVec2()`| Returns a vector with length 1.0 and random direction |
|`RandomVec2(double)`| Returns a random vector with the specified length |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/10.png)

```cpp title="Drawing lines while moving in random directions by 30 pixels each time"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	LineString lines = { Vec2{ 400, 300 } };

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Move", Vec2{ 40, 40 }))
		{
			// Add new point moved 30 pixels
			lines << (lines.back() + RandomVec2(30));
		}

		lines.draw(2, ColorF{ 0.2 });

		lines.back().asCircle(5).draw(Palette::Seagreen);
	}
}
```


## 39.11 Random Elements from Array
- The `Array` member function `.choice()` returns a reference to a random element in the array
- Cannot be used on empty arrays
	
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
			// Return random element
			Print << options.choice();
		}
	}
}
```


## 39.12 Multiple Random Elements from Array
- `Array`'s `.choice(N)` selects N random elements from the array without duplication and returns them as an array
- The order of elements in the result array is the same as their order in the original array
	
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
			// Return 3 random elements
			Print << coins.choice(3);
		}
	}
}
```


## 39.13 Array Shuffling
- `Array`'s `.shuffle()` randomly shuffles the order of array elements
- `.shuffled()` creates and returns a new shuffled array without changing itself
	
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

			// Shuffle element order
			cities.shuffle();

			Print << cities;
		}
	}
}
```


## 39.14 Randomly Selecting One
- `Sample({...})` randomly selects and returns one element from the list `{...}` of choices
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/14.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Sample", Vec2{ 200, 40 }))
		{
			// Randomly select
			Print << Sample({ 1, 2, 5, 10, 20, 50, 100, 500 });
		}
	}
}
```


## 39.15 Randomly Selecting Multiple
- `Sample(N, {...})` randomly selects and returns N elements from the list `{...}` of multiple choices
- The order of elements in the result array is the same as their order in the list
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/15.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Sample", Vec2{ 200, 40 }))
		{
			// Randomly select 3
			Print << Sample(3, { 1, 2, 5, 10, 20, 50, 100, 500 });
		}
	}
}
```


## 39.16 Specifying Appearance Probability
- When selecting random results from multiple choices with probability bias, use the `DiscreteSample` function
- The first argument is the choices, prepared as an array
- The second argument is the probability distribution of choices, prepared as a `DiscreteDistribution` type
- The probability distribution is specified with `double` type values, and the total doesn't need to be a specific number
	- For example, `{ 2.0, 12.0, 6.0 }` would be distributed as 10%, 60%, 30%

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/16.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Choices
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

	// Probability distribution corresponding to choices
	// ($0 is 1000 times more likely than $2000)
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
			// Randomly select based on probability distribution
			Print << DiscreteSample(options, distribution);
		}
	}
}
```


## 39.17 Normal Distribution
- To generate random numbers following a normal distribution, use the `NormalDistribution` class
- Create a normal distribution by specifying mean and standard deviation, and generate random numbers following the normal distribution with the `()` operator
- You can use the global random engine obtained with `GetDefaultRNG()`
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/random/17.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Normal distribution with mean 40, standard deviation 8
	NormalDistribution dist{ 40.0, 8.0 };

	Array<int32> counts(80);

	// Global random engine
	auto& rng = GetDefaultRNG();

	for (int32 i = 0; i < 10000; ++i)
	{
		const double x = dist(rng);

		// Convert result to integer in range 0 to 79
		const int32 xi = Clamp(static_cast<int32>(x), 0, 79);

		++counts[xi];
	}

	while (System::Update())
	{
		// Draw histogram
		for (int32 i = 0; i < 80; ++i)
		{
			Rect{ Arg::bottomLeft((i * 10), 600), 10, counts[i] }
				.draw(HSV{ (i * 3), 0.5, 0.9 });
		}
	}
}
```


## 39.18 (Reference) Password Generator
- This is a sample password generator that creates passwords with random alphanumeric characters
- `Clipboard::SetText(s)` copies the string `s` to the clipboard
	
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
			// Copy string to clipboard
			Clipboard::SetText(password);
		}
	}
}
```