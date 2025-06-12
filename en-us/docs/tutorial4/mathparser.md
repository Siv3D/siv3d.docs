# 77. Math Parser
Learn how to parse mathematical expressions represented as strings and get calculation results.

## 77.1 Parsing Mathematical Expressions
- There are functions to parse mathematical expressions and get calculation results:

| Code | Description |
|---|---|
| `double Eval(expression)` | Parses mathematical expression and returns result as `double` type.<br>Returns `NaN` if expression is invalid |
| `Optional<double> EvalOpt(expression)` | Parses mathematical expression and returns result as `double` type.<br>Returns `none` if expression is invalid |

- You can use the following operators and functions in expressions:
    - `+`, `-`, `*`, `/`, `%`, `^`
    - `log2`, `log10`, `log`, `ln`, `exp`, `sqrt`, `sign`, `abs`, `min`, `max`, `sin`, `cos`, `tan`, `asin`, `acos`, `atan`, `sinh`, `cosh`, `tanh`, `asinh`, `acosh`, `atanh`

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		const String expression = U"100 + 10 * 2 + sqrt(9) + 10 * 0.2 ^2";
		Print << Eval(expression);
		Print << EvalOpt(expression);
	}

	{
		// Invalid expression
		const String expression = U"100 +";
		Print << Eval(expression);
		Print << EvalOpt(expression);
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
123.4
(Optional)123.4
nan
none
```


## 77.2 Adding Constants
- If you want to make new elements like custom constants available in expressions, use the `MathParser` class

```cpp
# include <Siv3D.hpp>

void Main()
{
	MathParser parser;

    // Add constants
	parser.setConstant(U"pi", Math::Pi);
	parser.setConstant(U"x", 100);

    // Set expression
	parser.setExpression(U"x * pi");

	Print << parser.eval();
	Print << parser.evalOpt();

	while (System::Update())
	{

	}
}
```
```txt title="Output"
314.15927
(Optional)314.15927
```


## 77.3 Adding Variables
- By registering the address of `double` type variables with `MathParser`, you can use variables in expressions

```cpp
# include <Siv3D.hpp>

void Main()
{
	double x = 0.0;

	MathParser parser;
	parser.setVariable(U"x", &x);
	parser.setExpression(U"100 + x");

	for (int32 i = 0; i < 10; ++i)
	{
		x = (i * 0.1);
		Print << parser.eval();
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
100
100.1
100.2
100.3
100.4
100.5
100.6
100.7
100.8
100.9
```


## 77.4 Adding Functions
- You can register custom functions with `MathParser`

```cpp
# include <Siv3D.hpp>

double CircleArea(double r)
{
	return (Math::Pi * r * r);
}

double TriangleArea(double a, double b, double c)
{
	double s = ((a + b + c) / 2.0);
	return std::sqrt(s * (s - a) * (s - b) * (s - c));
}

void Main()
{
	MathParser parser;
	parser.setFunction(U"circleArea", CircleArea);
	parser.setFunction(U"triangleArea", TriangleArea);

	parser.setExpression(U"circleArea(10)");
	Print << parser.eval();

	parser.setExpression(U"triangleArea(3, 4, 5)");
	Print << parser.eval();

	while (System::Update())
	{

	}
}
```
```txt title="Output"
314.15927
6
```