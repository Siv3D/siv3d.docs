# 21. Useful Functions
Learn about several small functions that make Siv3D programming more convenient.

## 21.1 Minimum and Maximum Values
- `Min(a, b)` returns the smaller of `a` and `b`
- `Max(a, b)` returns the larger of `a` and `b`
- Both arguments must be of the same type
- If the arguments are of different types, explicitly specify the type like `Min<size_t>(a, b)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << Min(10, 20);
	Print << Max(10, 20);

	Print << Min(12.3, 45.6);
	Print << Max(12.3, 45.6);

	String s = U"Hello";
	Print << Min<size_t>(s.size(), 4);
	Print << Max<size_t>(s.size(), 4);

	while (System::Update())
	{

	}
}
```
```txt title="Output"
10
20
12.3
45.6
4
5
```


## 21.2 Clamping to a Range
- `Clamp(value, min, max)` returns `value` clamped to the range `[min, max]`
	- `Clamp(-20, 0, 100)` returns `0`
	- `Clamp(50, 0, 100)` returns `50`
	- `Clamp(120, 0, 100)` returns `100`
- All three arguments must be of the same type

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << Clamp(-20, 0, 100);
	Print << Clamp(50, 0, 100);
	Print << Clamp(120, 0, 100);

	while (System::Update())
	{

	}
}
```
``` txt title="Output"
0
50
100
```


## 21.3 Checking if in Range
- `InRange(value, min, max)` returns a `bool` indicating whether `value` is within the range `[min, max]`
	- `InRange(50, 0, 100)` returns `true`
	- `InRange(120, 0, 100)` returns `false`

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << InRange(50, 0, 100);
	Print << InRange(120, 0, 100);

	while (System::Update())
	{

	}
}
```
```txt title="Output"
true
false
```


## 21.4 Checking Odd and Even Numbers
- `IsOdd(n)` returns a `bool` indicating whether the integer `n` is odd
- `IsEven(n)` returns a `bool` indicating whether the integer `n` is even
	
```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << IsOdd(3);
	Print << IsOdd(4);

	Print << IsEven(3);
	Print << IsEven(4);

	while (System::Update())
	{

	}
}
```
```txt title="Output"
true
false
false
true
```


## 21.5 Absolute Value
- `Abs(value)` returns the absolute value of `value`
	- `Abs(-10)` returns `10`
	- `Abs(10)` returns `10`
	- `Abs(-3.14)` returns `3.14`
	- `Abs(3.14)` returns `3.14`

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << Abs(-10);
	Print << Abs(10);
	Print << Abs(-3.14);
	Print << Abs(3.14);

	while (System::Update())
	{

	}
}
```
```txt title="Output"
10
10
3.14
3.14
```


## 21.6 Absolute Difference
- `AbsDiff(a, b)` returns the absolute difference between `a` and `b`
	- `AbsDiff(10, 20)` returns `10`
	- `AbsDiff(20, 10)` returns `10`
	- `AbsDiff(3.14, 2.71)` returns `0.43`
	- `AbsDiff(2.71, 3.14)` returns `0.43`

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << AbsDiff(10, 20);
	Print << AbsDiff(20, 10);
	Print << AbsDiff(3.14, 2.71);
	Print << AbsDiff(2.71, 3.14);

	while (System::Update())
	{

	}
}
```
```txt title="Output"
10
10
0.43
0.43
```


## 21.7 Indexed Range-based for Loop
- In range-based for loops, you can use `Indexed()` to handle both integer indices and elements of the range simultaneously

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<String> animals = { U"cat", U"dog", U"bird" };

	for (auto&& [i, animal] : Indexed(animals))
	{
		Print << U"{}: {}"_fmt(i, animal);
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
0: cat
1: dog
2: bird
```


## 21.8 Indexed Range-based for Loop (Reference)
- To get each element by reference in an indexed range-based for loop, use `IndexedRef()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> numbers = { 10, 20, 30 };

	for (auto&& [i, number] : IndexedRef(numbers))
	{
		number += i;
	}

	for (const auto& number : numbers)
	{
		Print << number;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
10
21
32
```


## 21.9 Loop Shorthand
- You can write `for (int32 i = 0; i < N; ++i)` more concisely as `for (auto i : step(N))`
- You can write `for (auto i = from; i <= to; ++i)` more concisely as `for (auto i : Range(from, to))`

```cpp
# include <Siv3D.hpp>

void Main()
{
	for (auto i : step(3))
	{
		Print << i;
	}

	Print << U"---";

	for (auto i : Range(5, 10))
	{
		Print << i;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
0
1
2
---
5
6
7
8
9
10
```


## 21.10 Checking Character Properties
- There are functions to check properties of characters (mainly ASCII characters)

| Function | Description |
|--|--|
|`bool IsASCII(char32)`| Returns whether the character is an ASCII character |
|`bool IsDigit(char32)`| Returns whether the character is a decimal digit |
|`bool IsLower(char32)`| Returns whether the character is a lowercase letter |
|`bool IsUpper(char32)`| Returns whether the character is an uppercase letter |
|`bool IsAlpha(char32)`| Returns whether the character is an alphabetic letter |
|`bool IsAlnum(char32)`| Returns whether the character is alphabetic or numeric |
|`bool IsXdigit(char32)`| Returns whether the character is a hexadecimal digit |
|`bool IsControl(char32)`| Returns whether the character is a control character |
|`bool IsBlank(char32)`| Returns whether the character is a blank character (`' '`, `'\t'`, and full-width space) |
|`bool IsSpace(char32)`| Returns whether the character is a whitespace character (`' '`, `'\t'`, `'\n'`, `'\v'`, `'\f'`, `'\r'`, and full-width space) |
|`bool IsPrint(char32)`| Returns whether the character is a printable character |
|`char32 ToLower(char32)`| Converts an uppercase letter to lowercase |
|`char32 ToUpper(char32)`| Converts a lowercase letter to uppercase |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << IsASCII(U'A') << U' ' << IsASCII(U'あ');
	Print << IsUpper(U'A') << U' ' << IsUpper(U'a');
	Print << IsAlnum(U'4') << U' ' << IsAlnum(U'#');
	Print << IsSpace(U' ') << U' ' << IsSpace(U'-');
	Print << ToLower(U'A') << U' ' << ToLower(U'a');

	while (System::Update())
	{

	}
}
```
```txt title="Output"
true false
true false
true false
true false
a a
```


## 21.11 Mathematical Constants
- The following mathematical constants are available

=== "double type"
    | Name | Description | Value (shown with higher precision than actual) |
    |--|--|--|
    | `Math::E` | Base of natural logarithm | 2.718281828459045235360287471352662498 |
    | `Math::Log2E` | Base-2 logarithm of e | 1.442695040888963407359924681001892137 |
    | `Math::Log10E` | Base-10 logarithm of e | 0.434294481903251827651128918916605082 |
    | `Math::Pi` | π (pi) | 3.141592653589793238462643383279502884 |
    | `Math::QuarterPi` | π/4 | 0.785398163397448309615660845819875721 |
    | `Math::OneThirdPi` | π/3 | 1.047197551196597746154214461093167628 |
    | `Math::HalfPi` | π/2 | 1.570796326794896619231321691639751442 |
    | `Math::TwoPi` | 2π | 6.283185307179586476925286766559005768 |
    | `Math::Tau` | τ (2π) | 6.283185307179586476925286766559005768 |
    | `Math::InvTwoPi` | 1/(2π) | 0.159154943091895335768883763372514362 |
    | `Math::InvPi` | 1/π | 0.318309886183790671537767526745028724 |
    | `Math::InvSqrtPi` | 1/√π | 0.564189583547756286948079451560772586 |
    | `Math::Ln2` | Natural logarithm of 2 | 0.693147180559945309417232121458176568 |
    | `Math::Ln10` | Natural logarithm of 10 | 2.302585092994045684017991454684364208 |
    | `Math::Sqrt2` | √2 | 1.414213562373095048801688724209698078 |
    | `Math::Sqrt3` | √3 | 1.732050807568877293527446341505872366 |
    | `Math::InvSqrt2` | 1/√2 | 0.707106781186547524400844362104849039 |
    | `Math::InvSqrt3` | 1/√3 | 0.577350269189625764509148780501957456 |
    | `Math::EGamma` | Euler's constant | 0.577215664901532860606512090082402431 |
    | `Math::Phi` | Golden ratio (φ) | 1.618033988749894848204586834365638117 |
    | `Math::QNaN` | Quiet NaN | QNaN |
    | `Math::NaN` | Signaling NaN | SNaN |
    | `Math::Inf` | Inf | Inf |

=== "float type"
    | Name | Description | Value (shown with higher precision than actual) |
    |--|--|--|
    | `Math::PiF` | π (pi) | 3.141592653589793238462643383279502884 |
    | `Math::QuarterPiF` | π/4 | 0.785398163397448309615660845819875721 |
    | `Math::OneThirdPiF` | π/3 | 1.047197551196597746154214461093167628 |
    | `Math::HalfPiF` | π/2 | 1.570796326794896619231321691639751442 |
    | `Math::TwoPiF` | 2π | 6.283185307179586476925286766559005768 |
    | `Math::TauF` | τ (2π) | 6.283185307179586476925286766559005768 |
    | `Math::InvTwoPiF` | 1/(2π) | 0.159154943091895335768883763372514362 |
    | `Math::InvPiF` | 1/π | 0.318309886183790671537767526745028724 |
    | `Math::InvSqrtPiF` | 1/√π | 0.564189583547756286948079451560772586 |
    | `Math::QNaNF` | Quiet NaN | QNaN |
    | `Math::NaNF` | Signaling NaN | SNaN |
    | `Math::InfF` | Inf | Inf |

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Pi
	Print << Math::Pi;

	// Golden ratio
	Print << Math::Phi;

	// NaN
	Print << Math::QNaN;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
3.14159
1.61803
nan
```


## 21.12 Angle Representation
- While C++ and Siv3D APIs handle angles in radians, you can use different units for notation for better readability
- Using the `_deg` suffix, you can express angles in degrees
	- For example, `90_deg` becomes `(90 * Math::Pi / 180.0)`, which is 90° expressed in radians
- Using the `_pi` suffix, you can omit multiplication with π
	- For example, `0.5_pi` is the same as `(0.5 * Math::Pi)`
- You can express angles using the constant τ, which is defined as the ratio of a circle's circumference to its radius. The suffix is `_tau`
	- For example, `0.5_tau` is the same as `(0.5 * Math::TwoPi)`, which is 180° expressed in radians

| Suffix | Description | Value multiplied to the number |
| --- | --- | --- |
| `_deg` | Degrees | Math::Pi / 180.0 |
| `_pi` | π | Math::Pi |
| `_tau` | τ | Math::TwoPi |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"{}"_fmt(180_deg);
	Print << U"{}"_fmt(1_pi);
	Print << U"{}"_fmt(0.5_tau);

	while (System::Update())
	{

	}
}
```
```txt title="Output"
3.141592653589793
3.141592653589793
3.141592653589793
```


## 21.13 Angle Normalization
- To normalize an angle (in radians), use `Math::NormalizeAngle(radian, center = Pi)`
- The second argument is the center angle for normalization. If omitted, `Pi` is used
	- When the center angle is Pi, the return value range is `[0, 2π)`
	- When the center angle is 0, the return value range is `[-π, π)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double a = (Scene::Time() * -90_deg);
		const double b = (Scene::Time() * 90_deg);

		ClearPrint();

		Print << a;
		// Returns the angle normalized to the range [0.0, 2π)
		Print << Math::NormalizeAngle(a);
		// Returns the angle normalized to the range [-π, π)
		Print << Math::NormalizeAngle(a, 0.0);

		Print << U"----";

		Print << b;
		// Returns the angle normalized to the range [0.0, 2π)
		Print << Math::NormalizeAngle(b);
		// Returns the angle normalized to the range [-π, π)
		Print << Math::NormalizeAngle(b, 0.0);
	}
}
```
```txt title="Example Output"
-3.86462
2.41857
2.41857
----
3.86462
3.86462
-2.41857
```


## 21.14 Converting Between Radians and Degrees
- To convert from radians to degrees, use `Math::ToDegrees(radian)`
- To convert from degrees to radians, use `Math::ToRadians(degrees)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	const double angle = 45_deg;

	Print << angle;
	Print << Math::ToDegrees(angle);
	Print << Math::ToRadians(Math::ToDegrees(angle));

	while (System::Update())
	{

	}
}
```
```txt title="Output"
0.7854
45
0.7854
```


## 21.15 Converting from Enum to Integer
- Use `FromEnum(enum)` to convert an enum value to an integer

```cpp
# include <Siv3D.hpp>

enum class State
{
	Menu,
	Game,
	Result
};

void Main()
{
	State state = State::Result;
	const int32 n = FromEnum(state);
	Print << n;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
2
```


## 21.16 Converting from Integer to Enum
- Use `ToEnum<Enum>(i)` to convert an integer to an enum

```cpp
# include <Siv3D.hpp>

enum class State
{
	Menu,
	Game,
	Result
};

void Main()
{
	const int32 n = 2;
	State state = ToEnum<State>(n);
	Print << (state == State::Result);

	while (System::Update())
	{

	}
}
```
```txt title="Output"
true
```


## 21.17 Errors
- When you want to throw an exception to report an error in a Siv3D program, the `Error` class is convenient
- If this exception is not caught, the Siv3D engine will display the error message content in a message box and terminate the program
- For displaying the exception location in the IDE on Windows (Visual Studio), see [Displaying Exception Locations](../tools/msvc-exception.md){:target="_blank"}

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"aaa.png" };

	if (not texture)
	{
		// Throw an exception
		throw Error{ U"Failed to load `aaa.png`" };
	}

	while (System::Update())
	{

	}
}
```


## 21.18 Getting Command Line Arguments
- To get command line arguments passed when the program starts, use `System::GetCommandLineArgs()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<String> commands = System::GetCommandLineArgs();

	for (const auto& command : commands)
	{
		Print << command;
	}

	while (System::Update())
	{

	}
}
```


## 21.19 Console Output
- When you want to visualize output data that is too much for `Print` to handle, or when you want to copy output data to the clipboard, console output is convenient
- By outputting to `Console` instead of `Print`, you can perform console output
- On Windows, it outputs to the command prompt

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10,20,30,40,50 };

	Print << v;

	// Console output
	Console << v;

	Console << U"Hello, Siv3D!";

	while (System::Update())
	{

	}
}
```


## 21.20 Log Output
- By outputting to `Logger` instead of `Print`, you can perform log output
- On Windows, it outputs to the Visual Studio "Output" window (during debug execution)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10,20,30,40,50 };

	Print << v;

	// Log output
	Logger << v;

	Logger << U"Hello, Siv3D!";

	while (System::Update())
	{

	}
}
```