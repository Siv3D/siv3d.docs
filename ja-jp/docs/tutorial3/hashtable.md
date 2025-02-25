# 47. ハッシュテーブル
重複しないキーと、そのキーに対応する値のペアを要素とし、その集合を扱うデータ構造、ハッシュテーブルについて学びます。

## 47.1 HashTable の概要
- `HashTable<Key, Value>` は、C++ 標準の `std::unordered_map<Key, Value>` に相当するクラスです
- 重複しないキーと、そのキーに対応する値のペアを要素として、その集合を扱うためのコンテナです
- 要素の追加、削除、検索を高速に行えます

### 47.1.1 基本的な特徴
- キーは一意です。同じキーを持つ要素は存在しません
- ハッシュを使って実装されています
- 要素の順序は保証されません（指定した順序やソートができません）
- 要素の追加、削除、検索の平均的な計算量は O(1) （要素数によらずほぼ一定）で、非常に高速です

### 47.1.2 主な操作

| コード | 説明 | 計算量 |
| --- | --- | --- |
| `.emplace(key, value)` | 要素を追加する（すでに存在する場合は何もしない） | O(1) |
| `[key]` | 指定したキーに対応する値への参照を返す。存在しない場合は新たに要素を追加する | O(1) |
| `.erase(key)` | 指定したキーの要素を削除する（存在しない場合は何もしない） | O(1) |
| `.contains(key)` | 指定したキーの要素が存在するかを返す | O(1) |
| `.size()` | 要素数を返す | O(1) |
| `.empty()` | 要素数が 0 であるかを返す | O(1) |
| `.clear()` | すべての要素を削除する | O(N) |
| `.begin()`, `.end()` | 先頭・終端位置のイテレータを返す | O(1) |


## 47.2 ハッシュテーブルの作成
- `HashTable` は次のような方法で作成します
	- 空のハッシュテーブルを作成
	- 初期化リストから作成
	- 空のハッシュテーブルを作成し、要素を追加
	
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 空のハッシュテーブルを作成
	{
		HashTable<int32, String> ht;
		
		Print << ht.size();
		Print << ht;
	}

	Print << U"----";

	// 初期化リスト（キーと値のペアのリスト）から作成
	{
		HashTable<int32, String> ht = { { 3, U"three" }, { 1, U"one" }, { 4, U"four" }, { 1, U"one" }, { 5, U"five" } };
		
		Print << ht.size();
		Print << ht;
	}

	Print << U"----";

	// 空のハッシュテーブルを作成し、要素を追加
	{
		HashTable<int32, String> ht;
		ht.emplace(3, U"three");
		ht.emplace(1, U"one");
		ht.emplace(4, U"four");
		ht.emplace(1, U"one"); // キーがすでに存在するので何もしない
		ht.emplace(5, U"five");

		Print << ht.size();
		Print << ht;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力例"
0
{
}
----
4
{
        {4:     four},
        {1:     one},
        {5:     five},
        {3:     three},
}
----
4
{
        {4:     four},
        {1:     one},
        {5:     five},
        {3:     three},
}
```


## 47.3 要素数の取得
- `.size()` はハッシュテーブルの要素数を `size_t` 型で返します

```cpp
#include <Siv3D.hpp>

void Main()
{
	{
		HashTable<int32, String> ht;
		Print << ht.size();
	}

	{
		HashTable<String, int32> ht = { { U"C", 3 }, { U"C++", 1 }, { U"Java", 4 }, { U"C#", 1 }, { U"Python", 5 } };
		Print << ht.size();
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
0
5
```


## 47.4 空であるかを調べる
- `.empty()` はハッシュテーブルが空であるか（要素数が 0 であるか）を `bool` 型で返します

```cpp
#include <Siv3D.hpp>

void Main()
{
	{
		HashTable<int32, String> ht;
		Print << ht.empty();
	}

	{
		HashTable<String, int32> ht = { { U"C", 3 }, { U"C++", 1 }, { U"Java", 4 }, { U"C#", 1 }, { U"Python", 5 } };
		Print << ht.empty();
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
true
false
```


## 47.5 要素の追加（emplace）
- `.emplace(key, value)` で要素を追加します
- すでに同じ要素が存在する場合は、何もしません
- 戻り値は `std::pair<iterator, bool>` で、要素の追加に成功した（同じ要素が存在しなかった）場合、`.second` が `true` になります

```cpp
#include <Siv3D.hpp>

void Main()
{
	HashTable<int32, String> ht = { { 3, U"three" }, { 1, U"one" }, { 4, U"four" } };

	if (hs.emplace(1, U"one"))
	{
		Print << U"1 added";
	}
	else
	{
		Print << U"1 already exists";
	}

	if (hs.emplace(5, U"five"))
	{
		Print << U"5 added";
	}
	else
	{
		Print << U"5 already exists";
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
1 already exists
5 added
```


## 47.6 要素の参照または追加（`[]` 演算子）
- `table[key]` は指定したキーに対応する値への参照を返しますが、指定したキーが存在しない場合は、新たに要素を追加します
- この方法で要素を追加する場合、キーに対応する値はデフォルト初期化されます（`int32` なら `0`、`String` なら空文字列など）

```cpp
#include <Siv3D.hpp>

void Main()
{
	{
		HashTable<int32, String> ht;
		ht[3] = U"three";
		ht[1] = U"one";
		ht[4] = U"four";

		Print << ht.size();
		Print << ht;
	}

	{
		HashTable<String, int32> ht;
		ht[U"C"] = 3;
		ht[U"C++"] = 1;
		ht[U"Java"] = 4;

		ht[U"Python"]; // キーが存在しないため新たに追加され、値は 0 になる

		Print << ht.size();
		Print << ht;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力例"
3
{
        {4:     four},
        {3:     three},
        {1:     one},
}
4
{
        {C++:   1},
        {Python:        0},
        {C:     3},
        {Java:  4},
}
```

- すでに存在するキーに対して `[]` 演算子を使うことで、そのキーに対応する値を更新できます

```cpp
#include <Siv3D.hpp>

void Main()
{
	HashTable<int32, String> ht = { { 3, U"three" }, { 1, U"one" }, { 4, U"four" } };

	ht[1] = U"ONE";
	ht[3] = U"THREE";

	Print << ht.size();
	Print << ht;

	while (System::Update())
	{

	}
}
```
```txt title="出力例"
3
{
        {4:     four},
        {3:     THREE},
        {1:     ONE},
}
```


## 47.7 指定した要素の存在チェック（contains）
- `.contains(key)` で指定した要素が存在するかを調べます
- 存在する場合は `true` を、存在しない場合は `false` を返します

```cpp
#include <Siv3D.hpp>

void Main()
{
	{
		HashTable<int32, String> ht = { { 3, U"three" }, { 1, U"one" }, { 4, U"four" } };

		Print << ht.contains(3);
		Print << ht.contains(2);
	}

	{
		HashTable<String, int32> ht = { { U"C", 3 }, { U"C++", 1 }, { U"Java", 4 } };

		Print << ht.contains(U"C");
		Print << ht.contains(U"Python");
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
true
false
true
false
```


## 47.8 指定した要素の存在チェック（イテレータ）
- `.find(key)` は、指定したキーの要素を探し、そのイテレータを返します
- 見つからなかった場合は、`.end()` を返します
- 間接参照演算子 `*` を使って、イテレータが指す要素 `std::pair<const Key, Value>&` にアクセスできます

```cpp
#include <Siv3D.hpp>

void Main()
{
	{
		HashTable<int32, String> ht = { { 3, U"three" }, { 1, U"one" }, { 4, U"four" } };

		if (auto it = ht.find(3); it != ht.end())
		{
			Print << it->second;
		}

		if (auto it = ht.find(2); it != ht.end())
		{
			Print << it->second;
		}
	}

	{
		HashTable<String, int32> ht = { { U"C", 3 }, { U"C++", 1 }, { U"Java", 4 } };

		if (auto it = ht.find(U"C++"); it != ht.end())
		{
			Print << it->second;
		}

		
		if (auto it = ht.find(U"Python"); it != ht.end())
		{
			Print << it->second;
		}
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
three
1
```


## 47.9 範囲 for 文による要素アクセス（const 参照）
- 範囲 for 文を使って、ハッシュテーブルのすべての要素にアクセスします
- 各要素へのアクセスは、通常は const 参照で行います
- 範囲 for 文の中で、対象のハッシュテーブルのサイズを変更する操作は行わないでください

```cpp
#include <Siv3D.hpp>

void Main()
{
	HashTable<int32, String> ht = { { 3, U"three" }, { 1, U"one" }, { 4, U"four" } };

	// 構造化束縛を使ってキーと値を取得
	for (auto&& [key, value] : ht)
	{
		Print << key << U": " << value;
	}

	Print << U"----";

	// pair を使ってキーと値を取得
	for (const auto& elem : ht)
	{
		Print << elem.first << U": " << elem.second;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力例"
4: four
3: three
1: one
----
4: four
3: three
1: one
```


## 47.10 範囲 for 文による要素アクセス（参照）
- 範囲 for 文を使って、ハッシュテーブルのすべての要素にアクセスする際、各要素へのアクセスを参照で行うこともできます
- この場合、キーと値のうち「値」の部分に対してのみ変更を行うことができます

```cpp
#include <Siv3D.hpp>

void Main()
{
	HashTable<int32, String> ht = { { 3, U"three" }, { 1, U"one" }, { 4, U"four" } };

	for (auto&& [key, value] : ht)
	{
		value.push_back(U'!');
	}

	for (const auto& elem : ht)
	{
		Print << elem.first << U": " << elem.second;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力例"
4: four!
3: three!
1: one!
```


## 47.11 要素の削除
- `.erase(key)` で指定したキーの要素を削除します
- 指定したキーの要素が存在しない場合は、何もしません

```cpp
#include <Siv3D.hpp>

void Main()
{
	HashTable<int32, String> ht = { { 3, U"three" }, { 1, U"one" }, { 4, U"four" }, { 1, U"one" }, { 5, U"five" } };

	ht.erase(1);
	ht.erase(2);

	Print << ht;

	while (System::Update())
	{

	}
}
```
```txt title="出力例"
{
        {4:     four},
        {5:     five},
        {3:     three},
}
```


## 47.12 すべての要素の削除
- `.clear()` でハッシュテーブルのすべての要素を削除します

```cpp
#include <Siv3D.hpp>

void Main()
{
	HashTable<int32, String> ht = { { 3, U"three" }, { 1, U"one" }, { 4, U"four" }, { 1, U"one" }, { 5, U"five" } };

	ht.clear();

	Print << ht;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{
}
```
