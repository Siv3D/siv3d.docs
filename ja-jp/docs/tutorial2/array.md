# 21. 配列
動的配列クラス `Array` の基本的な使い方を学びます。

## 21.1 Array
- Siv3D では `std::vector<Type>` の代わりに `Array<Type>` で動的配列を扱います
- `Array` は `std::vector` と同等の機能を提供するほか、追加のメンバ関数を4持ちます
- `std::vector` のように要素がメモリ上に連続していることが保証されています

```cpp
// int32 型の値を格納する配列
Array<int32> a = { 10, 20, 30, 40, 50 };

// double 型の値を格納する配列
Array<double> b = { 1.1, 2.2, 3.3, 4.4, 5.5 };

// String 型の値を格納する配列
Array<String> c = { U"Apple", U"Bird", U"Cat", U"Dog" };
```


## 21.2 配列の作成
- 配列は次のような方法で作成します
	- 空の配列を作成
	- リストから配列を作成
	- 個数 × 値 で配列を作成
	- 個数 × デフォルト値で配列を作成
		- `Array<int32> v(5);` は 5 個の 0 で初期化された配列を作成します
		- `Array<double> v(5);` は 5 個の 0.0 で初期化された配列を作成します
		- `Array<String> v(5);` は 5 個の空文字列で初期化された配列を作成します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/xxxx/1.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	// パターン ①: 空の配列を作成
	{
		Array<int32> v;
		Print << v;
	}

	// パターン ②: リストから配列を作成
	{
		Array<int32> v = { 10, 50, 30, 20, 40 };
		Print << v;
	}

	// パターン ③: 個数 × 値 で配列を作成
	{
		Array<int32> v(5, -5);
		Print << v;
	}

	// パターン ④: 個数 × デフォルト値で配列を作成
	{
		Array<int32> v(5);
		Print << v;
	}

	while (System::Update())
	{

	}
}
```

## 21.3 配列の要素数
- `.size()` は配列の要素数を `size_t` 型で返します

```cpp
#include <Siv3D.hpp>

void Main()
{
	Array<int32> v1 = { 10, 50, 30, 20, 40 };
	Print << v1.size();

	Array<int32> v2;
	Print << v2.size();

	while (System::Update())
	{

	}
}
```


## 21.4 配列が空であるかを調べる（1）
- `.isEmpty()` は配列が空であるか（要素数が 0 であるか）を `bool` 型で返します

```cpp
#include <Siv3D.hpp>

void Main()
{
	Array<int32> v1 = { 10, 50, 30, 20, 40 };
	Print << v1.isEmpty();

	Array<int32> v2;
	Print << v2.isEmpty();

	while (System::Update())
	{

	}
}
```


## 21.5 配列が空であるかを調べる（2）
- `if (配列)` を使って配列が空であるかを調べます
- 配列が空である場合、配列は `false` と評価されます

```cpp
#include <Siv3D.hpp>

void Main()
{
	Array<int32> v1 = { 10, 50, 30, 20, 40 };
   
	if (v1)
	{
		Print << U"v1 is not empty";
	}

	Array<int32> v2;

	if (not v2)
	{
		Print << U"v2 is empty";
	}

	while (System::Update())
	{

	}
}
```


## 21.6 末尾に要素を追加する
- `v << x;` で配列 `v` の末尾に要素 `x` を追加します
- `std::vector` の `.push_back(x)` を短く書けるようにしたものです
- 末尾への追加は、それ以外の場所への追加に比べて最も効率的です

```cpp
#include <Siv3D.hpp>

void Main()
{
	Array<int32> v;
	v << 10;
	v << 50;
	v << 30;
	Print << v;

	v << 20 << 40;
	Print << v;

	while (System::Update())
	{

	}
}
```


## 21.7 末尾の要素を削除する
- `.pop_back()` で配列の末尾の要素を削除します
- 要素数が 0 のときに呼び出してはいけません。事前に要素数が 0 でないことを確認してください

```cpp
#include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10, 50, 30, 20, 40 };
	Print << v;

	v.pop_back();
	Print << v;

	v.pop_back();
	Print << v;

	while (System::Update())
	{

	}
}
```


## 21.8 すべての要素を削除する
- `.clear()` で配列のすべての要素を削除し、空の配列にします
- 要素数が 0 のときに呼び出しても問題ありません（何もしません）

```cpp
#include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10, 50, 30, 20, 40 };
	Print << v;

	v.clear();
	Print << v;

	while (System::Update())
	{

	}
}
```


## 21.9 要素数を変更する
- `.resize(n)` で配列の要素数を `n` に変更します
- 要素数が増える場合、新しい要素はデフォルト値で初期化されます

```cpp
#include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10, 50, 30, 20, 40 };
	Print << v;

	v.resize(3);
	Print << v;

	v.resize(5);
	Print << v;

	while (System::Update())
	{

	}
}
```


## 21.10 範囲 for 文で配列を走査する（const 参照）
- 範囲 for 文を使って配列の要素を走査します。各要素へのアクセスは、通常は const 参照で行います

```cpp
#include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10, 50, 30, 20, 40 };

	for (const auto& elem : v)
	{
		Print << elem;
	}

	while (System::Update())
	{

	}
}
```


## 21.11 範囲 for 文で配列を走査する（参照）
- 範囲 for 文を使って配列の要素を走査します
- ループ内で要素を変更する場合、const 参照の代わりに参照を使って要素にアクセスします

```cpp
#include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10, 50, 30, 20, 40 };

	for (auto& elem : v)
	{
		elem *= 2;
	}

	Print << v;

	while (System::Update())
	{

	}

```


## 21.12 指定したインデックスの要素にアクセスする
- `[i]` で配列の `i` 番目の要素にアクセスします
	- `i` は 0 から数えます。有効なインデックスは 0 から `.size() - 1` までです
- 範囲外にアクセスしてはいけません

```cpp
#include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10, 50, 30, 20, 40 };

	Print << v[0];
	Print << v[4];

	v[1] = 500;
	Print << v;

	while (System::Update())
	{

	}
}
```


## 21.13 先頭・末尾の要素にアクセスする

```cpp

```


## 21.14 先頭・終端位置のイテレータを取得する

```cpp

```

## 21.15 条件を満たす要素を削除する（イテレータ）

```cpp

```


## 21.16 条件を満たす要素を削除する（メンバ関数）

```cpp

```

## 21.17 その他の挿入・削除操作

```cpp

```


## 21.18 配列をソートする

```cpp

```


## 21.19 配列を逆順にする

```cpp

```


## 21.20 配列の要素をシャッフルする

```cpp

```


## 21.21 配列の要素の合計を計算する

```cpp

```


## 21.22 すべての要素に同じ値を代入する

```cpp

```


## 21.23 すべての要素に関数を適用した結果の配列を得る

```cpp

```
