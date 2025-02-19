# 46. ハッシュセット
重複のない集合を扱うデータ構造、ハッシュセットについて学びます。

## 46.1 HashSet の概要
- `HashSet<Key>` は、C++ 標準の `std::unordered_set<Key>` に相当するクラスです
- 要素が重複しない集合を扱うためのコンテナです
- 要素の追加、削除、検索を高速に行えます

### 46.1.1 基本的な特徴
- 要素は一意です。同じ要素は重複しません
- ハッシュを使って実装されています
- 要素の順序は保証されません（指定した順序やソートができません）
- 要素の追加、削除、検索の平均的な計算量は O(1) （要素数によらずほぼ一定）で、非常に高速です

### 46.1.2 主な操作

| コード | 説明 | 計算量 |
| --- | --- | --- |
| `.insert(key)` | 要素を追加する（すでに存在する場合は何もしない） | O(1) |
| `.erase(key)` | 指定した要素を削除する（存在しない場合は何もしない） | O(1) |
| `.contains(key)` | 指定した要素が存在するかを返す | O(1) |
| `.size()` | 要素数を返す | O(1) |
| `.empty()` | 要素数が 0 であるかを返す | O(1) |
| `.clear()` | すべての要素を削除する | O(N) |
| `.begin()`, `.end()` | 先頭・終端位置のイテレータを返す | O(1) |


## 46.2 ハッシュセットの作成
- `HashSet` は次のような方法で作成します
	- 空のハッシュセットを作成
	- 初期化リストから作成
	- 別のコンテナから作成
	- 空のハッシュセットを作成し、要素を追加
	
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 空のハッシュセットを作成
	{
		HashSet<int32> hs;
		Print << hs.size();
		Print << hs;
	}

	// 初期化リストから作成
	{
		HashSet<int32> hs = { 3, 1, 4, 1, 5 };
		Print << hs.size();
		Print << hs;
	}

	// 別のコンテナから作成
	{
		const Array<int32> a = { 3, 1, 4, 1, 5 };
		HashSet<int32> hs(a.begin(), a.end());
		Print << hs.size();
		Print << hs;
	}

	// 空のハッシュセットを作成し、要素を追加
	{
		HashSet<int32> hs;
		hs.insert(3);
		hs.insert(1);
		hs.insert(4);
		hs.insert(1); // 重複しているので無視される
		hs.insert(5);

		Print << hs.size();
		Print << hs;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
0
{}
4
{4, 1, 5, 3}
4
{4, 1, 5, 3}
4
{4, 1, 5, 3}
```


## 46.3 要素数の取得
- `.size()` はハッシュセットの要素数を `size_t` 型で返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		HashSet<int32> hs;
		Print << hs.size();
	}

	{
		HashSet<String> hs = { U"C", U"C++", U"Java" };
		Print << hs.size();
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
0
3
```


## 46.4 空であるかを調べる
- `.empty()` はハッシュセットが空であるか（要素数が 0 であるか）を `bool` 型で返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		HashSet<int32> hs;
		Print << hs.empty();
	}

	{
		HashSet<String> hs = { U"C", U"C++", U"Java" };
		Print << hs.empty();
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


## 46.5 要素の追加
- `.insert(key)` で要素を追加できます
- すでに同じ要素が存在する場合は、何もしません
- 戻り値は `std::pair<iterator, bool>` で、要素の追加に成功した（同じ要素が存在しなかった）場合、`.second` が `true` になります

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashSet<int32> hs = { 3, 1, 4 };

	if (hs.insert(1).second)
	{
		Print << U"1 added";
	}
	else
	{
		Print << U"1 already exists";
	}

	if (hs.insert(5).second)
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


## 46.6 指定した要素の存在チェック
- `.contains(key)` で指定した要素が存在するかを調べることができます
- 存在する場合は `true` を、存在しない場合は `false` を返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		HashSet<int32> hs = { 3, 1, 4, 1, 5 };
		Print << hs.contains(3);
		Print << hs.contains(2);
	}

	{
		HashSet<String> hs = { U"C", U"C++", U"Java" };
		Print << hs.contains(U"C");
		Print << hs.contains(U"Python");
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


## 46.7 範囲 for 文による要素の列挙
- 範囲 for 文を使って、ハッシュセットのすべての要素にアクセスできます
- 要素はコンピュータにとって都合のよい並び方で格納されるため、順序は保証されません
- 各要素へのアクセスは const 参照で行います
- 範囲 for 文の中で、対象のハッシュセットのサイズを変更する操作は行わないでください

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashSet<String> hs = { U"C", U"C++", U"Java" };

	hs.insert(U"Python");

	for (const auto& elem : hs)
	{
		Print << elem;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力例"
C++
Python
C
Java
```


## 46.8 要素の削除
- `.erase(key)` で指定した要素を削除できます
- 指定した要素が存在しない場合は、何もしません

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashSet<int32> hs = { 3, 1, 4, 1, 5 };

	hs.erase(1);
	hs.erase(2);

	Print << hs;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{4, 5, 3}
```
