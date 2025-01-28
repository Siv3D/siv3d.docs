# 21. 便利な関数
Siv3D プログラミングを便利にする、いくつかの小さな関数を学びます。

## 21.1 最小値・最大値
- `Min(a, b)` は `a` と `b` のうち小さい方を返します
- `Max(a, b)` は `a` と `b` のうち大きい方を返します
- 2 つの引数の型は同じである必要があります
- 2 つの引数の型が異なる場合は `Min<size_t>(a, b)` のように明示的に型を指定します

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
```txt title="出力"
10
20
12.3
45.6
4
5
```


## 21.2 指定した範囲に収める
- `Clamp(value, min, max)` は `value` を `[min, max]` の範囲に収めた値を返します
    - `Clamp(-20, 0, 100)` は `0` を返します
    - `Clamp(50, 0, 100)` は `50` を返します
    - `Clamp(120, 0, 100)` は `100` を返します
- 3 つの引数の型は同じである必要があります

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
``` txt title="出力"
0
50
100
```


## 21.3 指定した範囲内であるかを調べる
- `InRange(value, min, max)` は `value` が `[min, max]` の範囲内にあるかを `bool` 型で返します
    - `InRange(50, 0, 100)` は `true` を返します
    - `InRange(120, 0, 100)` は `false` を返します

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
```txt title="出力"
true
false
```


## 21.4 奇数・偶数を判定する
- `IsOdd(n)` は整数 `n` が奇数であるかを `bool` 型で返します
- `IsEven(n)` は整数 `n` が偶数であるかを `bool` 型で返します
	
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
```txt title="出力"
true
false
false
true
```


## 21.5 絶対値
- `Abs(value)` は `value` の絶対値を返します
    - `Abs(-10)` は `10` を返します
    - `Abs(10)` は `10` を返します
    - `Abs(-3.14)` は `3.14` を返します
    - `Abs(3.14)` は `3.14` を返します

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
```txt title="出力"
10
10
3.14
3.14
```


## 21.6 差の絶対値
- `AbsDiff(a, b)` は `a` と `b` の差の絶対値を返します
    - `AbsDiff(10, 20)` は `10` を返します
    - `AbsDiff(20, 10)` は `10` を返します
    - `AbsDiff(3.14, 2.71)` は `0.43` を返します
    - `AbsDiff(2.71, 3.14)` は `0.43` を返します

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
```txt title="出力"
10
10
0.43
0.43
```
