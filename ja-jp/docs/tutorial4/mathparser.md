# 77. 数式パーサー

## 77.1 数式のパース
- 数式をパースし、計算結果を取得する次のような関数があります

| コード | 説明 |
|---|---|
| `double Eval(数式)` | 数式をパースし、計算結果を `double` 型で返す。<br>数式が不正な場合は `NaN` を返す |
| `Optional<double> EvalOpt(数式)` | 数式をパースし、計算結果を `double` 型で返す。<br>数式が不正な場合は `none` を返す |

- 数式内では次のような演算子や関数を使用できます：
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
		// 不正な数式
		const String expression = U"100 +";
		Print << Eval(expression);
		Print << EvalOpt(expression);
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
123.4
(Optional)123.4
nan
none
```


## 77.2 定数の追加
- 独自の定数など、新しい要素を数式内で使えるようにしたい場合は `MathParser` クラスを使用します

```cpp
# include <Siv3D.hpp>

void Main()
{
	MathParser parser;

    // 定数の追加
	parser.setConstant(U"pi", Math::Pi);
	parser.setConstant(U"x", 100);

    // 数式の設定
	parser.setExpression(U"x * pi");

	Print << parser.eval();
	Print << parser.evalOpt();

	while (System::Update())
	{

	}
}
```
```txt title="出力"
314.15927
(Optional)314.15927
```


## 77.3 変数の追加
- `MathParser` に `double` 型の変数のアドレスを登録することで、数式内で変数を使用できます

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
```txt title="出力"
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


## 77.4 関数の追加
- `MathParser` に独自の関数を登録できます

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
```txt title="出力"
314.15927
6
```
