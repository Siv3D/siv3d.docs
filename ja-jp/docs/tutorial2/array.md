# 22. 配列
動的配列クラス `Array` の基本的な使い方を学びます。

## 22.1 Array
- Siv3D では `Array<Type>` で動的配列を扱います
- `Array` は `std::vector` と同等の機能を提供するほか、追加のメンバ関数を持ちます
- `std::vector` のように要素がメモリ上に連続していることが保証されています

```cpp
// int32 型の値を格納する配列
Array<int32> a = { 10, 20, 30, 40, 50 };

// double 型の値を格納する配列
Array<double> b = { 1.1, 2.2, 3.3, 4.4, 5.5 };

// String 型の値を格納する配列
Array<String> c = { U"Apple", U"Bird", U"Cat", U"Dog" };
```


## 22.2 配列の作成
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
# include <Siv3D.hpp>

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
```txt title="出力"
{}
{10, 50, 30, 20, 40}
{-5, -5, -5, -5, -5}
{0, 0, 0, 0, 0}
```


## 22.3 配列の要素数
- `.size()` は配列の要素数を `size_t` 型で返します

```cpp
# include <Siv3D.hpp>

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
```txt title="出力"
5
0
```


## 22.4 配列が空であるかを調べる（1）
- `.isEmpty()` は配列が空であるか（要素数が 0 であるか）を `bool` 型で返します

```cpp
# include <Siv3D.hpp>

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
```txt title="出力"
false
true
```


## 22.5 配列が空であるかを調べる（2）
- `if (配列)` を使って配列が空であるかを調べます
- 配列が空である場合、配列は `false` と評価されます

```cpp
# include <Siv3D.hpp>

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
```txt title="出力"
v1 is not empty
v2 is empty
```


## 22.6 末尾に要素を追加する
- `v << x;` で配列 `v` の末尾に要素 `x` を追加します
- `std::vector` の `.push_back(x)` を短く書けるようにしたものです
- 配列の末尾への追加は、それ以外の場所（先頭や途中）への追加に比べて最も効率的です

```cpp
# include <Siv3D.hpp>

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
```txt title="出力"
{10, 50, 30}
{10, 50, 30, 20, 40}
```


## 22.7 末尾の要素を削除する
- `.pop_back()` で配列の末尾の要素を削除します
- 要素数が 0 のときに呼び出してはいけません
	- 事前に要素数が 0 でないことを確認してください
- 配列の末尾の要素の削除は、それ以外の場所（先頭や途中）の要素の削除に比べて最も効率的です

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10, 50, 30, 20, 40 };
	Print << v;

	v.pop_back();
	Print << v;

	v.pop_back();
	Print << v;

	while (v)
	{
		v.pop_back();
	}

	Print << v;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{10, 50, 30, 20, 40}
{10, 50, 30, 20}
{10, 50, 30}
{}
```


## 22.8 すべての要素を削除する
- `.clear()` で配列のすべての要素を削除し、空の配列にします
- 要素数が 0 のときに呼び出しても問題ありません（何もしません）

```cpp
# include <Siv3D.hpp>

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
```txt title="出力"
{10, 50, 30, 20, 40}
{}
```


## 22.9 要素数を変更する
- `.resize(n)` で配列の要素数を `n` に変更します
- 要素数が増える場合、新しい要素はデフォルト値で初期化されます

```cpp
# include <Siv3D.hpp>

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
```txt title="出力"
{10, 50, 30, 20, 40}
{10, 50, 30}
{10, 50, 30, 0, 0}
```


## 22.10 範囲 for 文で配列を走査する（const 参照）
- 範囲 for 文を使って配列の要素を走査します
- 各要素へのアクセスは、通常は const 参照で行います

```cpp
# include <Siv3D.hpp>

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
```txt title="出力"
10
50
30
20
40
```


## 22.11 範囲 for 文で配列を走査する（参照）
- 範囲 for 文を使って配列の要素を走査します
- ループ内で要素を変更する場合、const 参照の代わりに参照を使って要素にアクセスします

```cpp
# include <Siv3D.hpp>

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
}
```
```txt title="出力"
{20, 100, 60, 40, 80}
```


## 22.12 指定したインデックスの要素にアクセスする
- `[i]` で配列の `i` 番目の要素にアクセスします
	- `i` は 0 から数えます。有効なインデックスは `0` から `.size() - 1` までです
- 範囲外にアクセスしてはいけません

```cpp
# include <Siv3D.hpp>

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
```txt title="出力"
10
40
{10, 500, 30, 20, 40}
```


## 22.13 先頭・末尾の要素にアクセスする
- `.front()` は先頭の要素への参照を返します
	- `v[0]` と同じです
- `.back()` は末尾の要素への参照を返します
	- `v[v.size() - 1]` と同じです
- いずれも要素数が 0 のときに呼び出してはいけません

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10, 50, 30, 20, 40 };

	Print << v.front();
	Print << v.back();

	v.front() = 100;
	v.back() = 400;
	Print << v;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
10
40
{100, 50, 30, 20, 400}
```


## 22.14 先頭・終端位置のイテレータを取得する
- `.begin()` は先頭位置のイテレータを返します
- `.end()` は終端位置のイテレータを返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10, 50, 30, 20, 40 };

	auto it = v.begin();

	Print << *it;

	++it;

	Print << *it;
	
	while (System::Update())
	{

	}
}
```
```txt title="出力"
10
50
```


## 22.15 その他の挿入・削除操作
- .push_front(値) で、先頭に要素を追加します
- .pop_front() で、先頭の要素を削除します
- `.insert(イテレータ, 値)` で、指定したイテレータの位置に要素を挿入します
- `.append(配列)` で、別の配列を末尾に追加します
- `.erase(イテレータ)` で、指定したイテレータの位置の要素を削除します
- `.erase(イテレータ1, イテレータ2)` で、指定した範囲の要素を削除します
- 先頭や途中への要素の挿入・削除は、それ以降の既存要素の移動を伴うため、うしろの要素数に比例したコストがかかります
	- 通常は避けるか、小さい配列でのみ使用するべきです

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		Array<int32> v = { 10, 20, 30, 40, 50 };

		v.push_front(5);
		Print << v;

		v.pop_front();
		Print << v;
	}

	{
		Array<int32> v = { 10, 20, 30, 40, 50 };

		v.insert((v.begin() + 2), 25);
		Print << v;
	}

	{
		Array<int32> v1 = { 10, 20, 30 };
		Array<int32> v2 = { 40, 50 };

		v1.append(v2);
		Print << v1;
	}

	{
		Array<int32> v = { 10, 20, 30, 40, 50 };

		v.erase(v.begin() + 2);
		Print << v;

		v.erase(v.begin(), (v.begin() + 2));
		Print << v;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{5, 10, 20, 30, 40, 50}
{10, 20, 30, 40, 50}
{10, 20, 25, 30, 40, 50}
{10, 20, 30, 40, 50}
{10, 20, 40, 50}
{40, 50}
```


## 22.16 条件を満たす要素を削除する（イテレータ方式）
- イテレータによるループを利用して、条件を満たす要素を削除するには次のようにします

```cpp title="配列から 30 未満の要素を削除する"
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10, 50, 30, 20, 40 };

	for (auto it = v.begin(); it != v.end();)
	{
		if (*it < 30)
		{
			it = v.erase(it);
		}
		else
		{
			++it;
		}
	}

	Print << v;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{50, 30, 40}
```


## 22.17 条件を満たす要素を削除する（`.remove_if()` 方式）
- `.remove_if(条件を記述した関数)` で、条件を満たす要素を削除します
	- イテレータ方式よりも簡潔に書けます
- 「条件を記述した関数」は、要素を引数（値渡しまたは const 参照渡し）で受け取り `bool` を返す関数オブジェクトです
	- 関数やラムダ式を使います

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		Array<int32> v = { 11, 22, 33, 44, 55 };

		// 偶数の要素を削除する
		v.remove_if(IsEven);

		Print << v;
	}

	{
		Array<int32> v = { 10, 50, 30, 20, 40 };

		// 30 未満の要素を削除する
		v.remove_if([](int32 x) { return x < 30; });

		Print << v;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{11, 33, 55}
{50, 30, 40}
```


## 22.18 配列をソートする
- `.sort()` で配列を昇順にソートします
- `.rsort()` で配列を降順にソートします

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		Array<int32> v = { 10, 50, 30, 20, 40 };

		v.sort();
		Print << v;
	}

	{
		Array<String> v = { U"Bird", U"Dog", U"Apple", U"Cat" };

		v.rsort();
		Print << v;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{10, 20, 30, 40, 50}
{Dog, Cat, Bird, Apple}
```


## 22.19 配列を逆順にする
- `.reverse()` で配列を逆順にします

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10, 50, 30, 20, 40 };

	v.reverse();
	Print << v;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{40, 20, 30, 50, 10}
```


## 22.20 配列の要素をシャッフルする
- `.shuffle()` で配列の要素をシャッフルします

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 1, 2, 3, 4, 5, 6 };

	v.shuffle();
	Print << v;

	while (System::Update())
	{

	}
}
```
```txt title="出力例"
{4, 6, 2, 1, 5, 3}
```


## 22.21 配列の要素の合計を計算する
- `.sum()` で配列の要素の合計を計算します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10, 50, 30, 20, 40 };

	Print << v.sum();

	while (System::Update())
	{

	}
}
```
```txt title="出力"
150
```


## 22.22 すべての要素に同じ値を代入する
- .fill(値) で、すべての要素に同じ値を代入します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v = { 10, 50, 30, 20, 40 };

	v.fill(100);
	Print << v;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{100, 100, 100, 100, 100}
```


## 22.23 すべての要素に関数を適用した結果の配列を得る
- `.map(関数)` で、すべての要素に関数を適用した結果の配列を得ます
- 関数は、要素を引数にとり、変換後の要素を返す関数オブジェクトです

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> v1 = { 10, 50, 30, 20, 40 };

	Array<double> v2 = v1.map([](int32 x) { return (x * 1.01); });

	Print << v2;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{10.1, 50.5, 30.3, 20.2, 40.4}
```
