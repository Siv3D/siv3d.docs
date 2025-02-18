# 46. ハッシュセット
高速な要素の追加、削除、検索が可能なハッシュセットを使って、要素の重複を許さない集合を扱う方法を学びます。

## 46.1 HashSet の概要
- `HashSet<Key>` は、C++ 標準の `std::unordered_set<Key>` に相当するクラスです
- 要素が重複しない集合を扱うためのコンテナです

### 46.1.1 基本的な特徴
- 要素は一意です（同じ要素の重複を許しません）
- ハッシュを使って実装されています
- 要素の順序は保証されません（指定した順序やソートができません）

### 46.1.2 実行時性能
- 要素の追加、削除、検索の平均的な計算量は O(1) （要素数によらずほぼ一定）で、非常に高速です

### 46.1.3 主な操作

| コード | 説明 | 計算量 |
| --- | --- | --- |
| `.insert(key)` | 要素を追加する（すでに存在する場合は何もしない） | O(1) |
| `.erase(key)` | 指定した要素を削除する（存在しない場合は何もしない） | O(1) |
| `.contains(key)` | 指定した要素が存在するかを返す | O(1) |
| `.size()` | 要素数を返す | O(1) |
| `.isEmpty()` | 要素数が 0 であるかを返す | O(1) |
| `.clear()` | すべての要素を削除する | O(N) |
| `.begin()`, `.end()` | 先頭・終端位置のイテレータを返す | O(1) |


## 46.2 ハッシュセットの作成
- XXX
	
```cpp

```


## 46.3 要素の追加
- XXX

```cpp

```


## 46.4 要素の数を調べる
- XXX

```cpp

```


## 46.5 指定した要素が存在するかを調べる
- XXX

```cpp

```

## 46.6 範囲 for で要素にアクセスする
- XXX

```cpp

```

## 46.7 要素の削除
- XXX

```cpp

```





## 22.1 ハッシュセットクラス
`HashSet<Key>` は、`std::unordered_set<Key>` に相当するクラスです。ハッシュに対応したキーの集合を表現し、キーの追加、削除、検索を効率的に行うことができます。集合内の順序は保証されません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashSet<String> table = { U"C++", U"C", U"Java" };

	// 追加された順序と一覧表示の順序は一致しない
	Print << table;

	while (System::Update())
	{

	}
}
```


## 22.2 キーを追加する
`.insert(key)` でキーを追加することができます。すでに同じキーが存在する場合は、何もしません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashSet<String> table = { U"C++", U"C", U"Java" };

	table.insert(U"Python");

	table.insert(U"C#");

	// すでに存在するため追加されない
	table.insert(U"Java");
	table.insert(U"C#");

	Print << table;

	while (System::Update())
	{

	}
}
```


## 22.3 キーの数を調べる
`.size()` でキーの数を調べることができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashSet<String> table = { U"C++", U"C", U"Java" };

	table.insert(U"Python");

	table.insert(U"C#");

	table.insert(U"Java");

	table.insert(U"C#");

	Print << table.size();

	Print << table;

	while (System::Update())
	{

	}
}
```


## 22.4 指定したキーが存在するかを調べる
`.contains(key)` で指定したキーが存在するかを調べることができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashSet<String> table = { U"C++", U"C", U"Java" };

	table.insert(U"Python");

	table.insert(U"C#");

	// "C++" というキーが存在するかを調べる
	Print << table.contains(U"C++");

	// "Ruby" というキーがが存在するかを調べる
	Print << table.contains(U"Ruby");

	while (System::Update())
	{

	}
}
```


## 22.5 範囲 for 文で要素にアクセスする
範囲 for 文を使うと、ハッシュセットのすべてのキーにアクセスできます。順序は保証されません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashSet<String> table = { U"C++", U"C", U"Java" };

	table.insert(U"Python");

	table.insert(U"C#");

	// 追加された順序とアクセスの順序は一致しない
	for (const auto& key : table)
	{
		Print << key;
	}

	while (System::Update())
	{

	}
}
```


## 22.6 キーを削除する
`.erase(key)` で指定したキーを削除することができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashSet<String> table = { U"C++", U"C", U"Java" };

	table.insert(U"Python");

	table.insert(U"C#");

	Print << table;

	// "Python" を削除する
	table.erase(U"Python");

	Print << table;

	while (System::Update())
	{

	}
}
```
