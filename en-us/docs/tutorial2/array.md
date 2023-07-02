# 20. 動的配列
動的配列クラス `Array` の基本的な使い方を説明します。

## 20.1 配列クラス
Siv3D では `std::vector<Type>` の代わりに `Array<Type>` クラスを使って `Type` 型の動的配列を扱います。

`Array` は内部で `std::vector` を使って配列を管理しています。ただし、標準ライブラリよりも多くのメンバ関数を持ち、様々な便利な機能を提供します。格納されている要素はメモリ上での連続性が保証されています。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<String> words = { U"apple", U"bird", U"cat" };

	Print << words;

	// 5 個の -1 で初期化された配列を作成する
	Array<int32> numbers(5, -1);

	Print << numbers;

	while (System::Update())
	{

	}
}
```


## 20.2 配列のサイズ
配列の要素数は `.size()` で取得します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<String> words = { U"apple", U"bird", U"cat" };

	Print << words.size();

	Array<int32> numbers(5, -1);

	Print << numbers.size();

	while (System::Update())
	{

	}
}
```


## 20.3 指定したインデックスの要素にアクセスする
配列内の指定したインデックスにある要素にアクセスするには、`[]` 演算子を使います。インデックスは 0 から始まります。`v.front()` は `v[0]` と同じです。`v.back()` は `v[v.size() - 1]` と同じで末尾の要素にアクセスします。範囲外のインデックスにアクセスしてはいけません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<String> words = { U"apple", U"bird", U"cat", U"dog", U"egg" };

	Print << words[0];

	Print << words[3];

	Print << words.front();

	Print << words.back();

	while (System::Update())
	{

	}
}
```


## 20.4 範囲ベースの for 文で要素にアクセスする
範囲ベース for 文を使って配列の各要素にアクセスできます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<String> words = { U"apple", U"bird", U"cat", U"dog", U"egg" };

	for (const auto& word : words) // 要素を変更しない場合は const 参照
	{
		Print << word;
	}

	Array<int32> values = { 1, 2, 3, 4, 5 };

	for (auto& value : values) // 要素を変更する場合は参照
	{
		value *= 2;
	}

	Print << values;

	while (System::Update())
	{

	}
}
```


## 20.5 空の配列
要素を保持していない、要素数が 0 の配列は**空の配列**です。代入や追加によって要素を追加すると、空でない配列になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<String> words;

	Print << words;

	Print << words.size();

	words = { U"apple", U"orange", U"melon" };

	Print << words;

	Print << words.size();

	while (System::Update())
	{

	}
}
```


## 20.6 配列が空であるかを調べる
`Array` 型の値 `v` が空であるかは、`if (v.isEmpty())` や `if (v)` / `if (not v)` で調べられます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<String> words;

	Array<int32> numbers = { 1, 2, 3, 4, 5 };

	Print << words.isEmpty();

	Print << numbers.isEmpty();

	if (not words)
	{
		Print << U"words is empty";
	}

	if (numbers)
	{
		Print << U"numbers is not empty";
	}

	while (System::Update())
	{

	}
}
```


## 20.7 配列の末尾に要素を追加する
`<<` で配列の末尾に新しい要素を追加することができます。`<<` は連続して使うこともできます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<String> words;

	words << U"apple";

	words << U"bird";

	words << U"cat";

	Print << words;

	Array<int32> numbers = { 1, 2 };

	numbers << 3;

	Print << numbers;

	while (System::Update())
	{

	}
}
```


## 20.8 配列を空にする
`.clear()` で配列を空にすることができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<String> words = { U"apple", U"bird", U"cat" };

	Print << words;

	Print << words.isEmpty();

	// 配列を空にする
	words.clear();

	Print << words;

	Print << words.isEmpty();

	while (System::Update())
	{

	}
}
```


## 20.9 末尾の要素を削除する
`.pop_back()` で配列の末尾の要素を削除することができます。`.pop_back_N(n)` で末尾から `n` 要素を削除することができます。

空の配列で `.pop_back()` を呼んではいけません。一方、`.pop_back_N(n)` は空の配列でも呼び出すことができます。`.pop_back_N(n)` は実際の要素数以上を削除しようとした場合に配列を空にします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> numbers = { 100, 200, 300, 400, 500 };

	Print << numbers;

	numbers.pop_back();

	Print << numbers;

	numbers.pop_back_N(2);

	Print << numbers;

	// 実際の要素数以上を削除しようとしているので、配列を空にする
	numbers.pop_back_N(100);

	Print << numbers;

	while (System::Update())
	{

	}
}
```


## 20.10 先頭の要素を削除する
`.pop_front()` で配列の先頭の要素を削除することができます。`.pop_front_N(n)` で先頭から `n` 要素を削除することができます。

空の配列で `.pop_front()` を呼んではいけません。一方、`.pop_front_N(n)` は空の配列でも呼び出すことができます。`.pop_front_N(n)` は実際の要素数以上を削除しようとした場合に配列を空にします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> numbers = { 100, 200, 300, 400, 500 };

	Print << numbers;

	numbers.pop_front();

	Print << numbers;

	numbers.pop_front_N(2);

	Print << numbers;

	// 実際の要素数以上を削除しようとしているので、配列を空にする
	numbers.pop_front_N(100);

	Print << numbers;

	while (System::Update())
	{

	}
}
```


## 20.11 配列の要素数を変更する
`.resize(n)` で配列の要素数を `n` に変更することができます。`.resize(n, value)` で配列の要素数を `n` に変更し、新しい要素を `value` で初期化することができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/11.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> numbers = { 1, 2, 3 };

	numbers.resize(10, -1);

	Print << numbers;

	Print << numbers.size();

	numbers.resize(2);

	Print << numbers;

	Print << numbers.size();

	while (System::Update())
	{

	}
}
```


## 20.12 条件を満たす要素を削除する (remove_if)
`.remove_if(f)` で、条件を満たす要素を削除することができます。`f` は、配列の要素の型を引数に取り、`bool` 型を返す関数です。`f` が `true` を返した要素は削除されます。

#### 関数で条件を指定する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/12.png)

```cpp
# include <Siv3D.hpp>

bool IsLessThanFive(int32 n)
{
	return (n < 5);
}

void Main()
{
	Array<int32> values = { 1, 2, 3, 4, 5, 6, 7, 8, 9 };

	// 値が 5 未満の要素を削除する
	values.remove_if(IsLessThanFive);

	Print << values;

	while (System::Update())
	{

	}
}
```

#### ラムダ式で条件を指定する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/12.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> values = { 1, 2, 3, 4, 5, 6, 7, 8, 9 };

	// 値が 5 未満の要素を削除する
	values.remove_if([](int32 n) { return (n < 5); });

	Print << values;

	while (System::Update())
	{

	}
}
```


## 20.13 条件を満たす要素を削除する (イテレータ)
`std::vector` のように、イテレータと `.erase()` を使って要素を削除することもできます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/13.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> values = { 1, 2, 3, 4, 5, 6, 7, 8, 9 };

	for (auto it = values.begin(); it != values.end();)
	{
		if (*it < 5)
		{
			it = values.erase(it);
		}
		else
		{
			++it;
		}
	}

	Print << values;

	while (System::Update())
	{

	}
}
```


## 20.14 配列の要素をシャッフルする
`.shuffle()` で配列の要素をランダムに並び替えます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/14.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> values = { 1, 2, 3, 4, 5 };

	// ランダムに並び替える
	values.shuffle();

	Print << values;

	// ランダムに並び替える
	values.shuffle();

	Print << values;

	while (System::Update())
	{

	}
}
```


## 20.15 配列の要素をソートする
`.sort()` で配列の要素を昇順にソートします。`.rsort()` で配列の要素を降順にソートします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/15.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> values = { 2, 5, 3, 1, 4 };

	// 昇順にソートする
	values.sort();

	Print << values;

	Array<String> words = { U"apple", U"dog", U"bird", U"cat" };

	// 降順にソートする
	words.rsort();

	Print << words;

	while (System::Update())
	{

	}
}
```

クラスのソートを行うときには、`.sort_by()` にカスタムの比較関数を指定します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/15b.png)

```cpp
# include <Siv3D.hpp>

struct Player
{
	int32 rank;

	String name;
};

void Main()
{
	Array<Player> players =
	{
		{ 30, U"Tom" },
		{ 20, U"Bob" },
		{ 10, U"Alice" },
		{ 40, U"David" },
		{ 50, U"Eve" },
	};

	// ランクの昇順にソートする
	players.sort_by([](const Player& a, const Player& b) { return (a.rank < b.rank); });

	for (const auto& player : players)
	{
		Print << U"{} {}"_fmt(player.rank, player.name);
	}

	while (System::Update())
	{

	}
}
```


## 20.16 配列の要素を逆順にする
`.reverse()` で配列の要素を逆順します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/16.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> values = { 1, 2, 3, 4, 5 };

	// 逆順に並び替える
	values.reverse();

	Print << values;

	while (System::Update())
	{

	}
}
```


## 20.17 配列の要素の合計を計算する
`.sum()` で配列の要素の合計を計算します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/17.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> values = { 1, 2, 3, 4, 5 };

	// 合計を計算する
	Print << values.sum();

	while (System::Update())
	{

	}
}
```


## 20.18 全ての要素に同じ値を代入する
`.fill(value)` で配列の全ての要素に `value` を代入します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/18.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> values = { 1, 2, 3, 4, 5 };

	Print << values;

	// すべての要素に 0 を代入する
	values.fill(0);

	Print << values;

	while (System::Update())
	{

	}
}
```


## 20.19 map 処理
`.map(f)` で配列の全ての要素に関数 `f` を適用した結果を持つ新しい配列を作成できます。`f` は、配列の要素の型を引数に取り、新しい要素の型を返す関数です。

`Format(value)` は、`value` を文字列に変換する関数です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/array/19.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Array<int32> a = { 1, 2, 3, 4, 5 };

	// a の各要素を 0.1 倍した配列を作成する
	const Array<double> b = a.map([](int32 n) { return (n * 0.1); });

	Print << b;

	// a の各要素を文字列にした配列を作成する
	const Array<String> c = a.map(Format);

	Print << c;

	while (System::Update())
	{

	}
}
```
