# 22. ハッシュテーブル
ハッシュセット `HashSet` とハッシュテーブル `HashTable` の基本的な使い方を学びます。

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


## 22.7 ハッシュテーブルクラス
`HashTable<Key, Value>` は、`std::unordered_map<Key, Value>` に相当するクラスです。ハッシュに対応したキーと値のペアを要素とする集合を表現し、要素の追加、削除、検索を効率的に行うことができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashTable<String, int32> table =
	{
		{ U"curry", 3 },
		{ U"sushi", 3 },
		{ U"hamburger", 0 },
		{ U"pasta", 1 },
		{ U"pizza", 4 },
	};

	// 追加された順序と一覧表示の順序は一致しない
	Print << table;

	while (System::Update())
	{

	}
}
```


## 22.8 要素を追加する
`.emplace(key, value)` でキーと値のペアを追加することができます。すでに同じキーが存在する場合は、何もしません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashTable<String, int32> table =
	{
		{ U"curry", 3 },
		{ U"sushi", 3 },
		{ U"hamburger", 0 },
		{ U"pasta", 1 },
		{ U"pizza", 4 },
	};

	table.emplace(U"bulgogi", 2);

	// すでにキーが存在する場合は何もしない
	table.emplace(U"pasta", 500);

	table.emplace(U"pirozhki", 0);

	Print << table;

	while (System::Update())
	{

	}
}
```


## 22.9 要素数を調べる
`.size()` で要素数を調べることができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashTable<String, int32> table =
	{
		{ U"curry", 3 },
		{ U"sushi", 3 },
		{ U"hamburger", 0 },
		{ U"pasta", 1 },
		{ U"pizza", 4 },
	};

	table.emplace(U"bulgogi", 2);

	// すでにキーが存在する場合は何もしない
	table.emplace(U"pasta", 500);

	table.emplace(U"pirozhki", 0);

	Print << table.size();

	Print << table;

	while (System::Update())
	{

	}
}
```


## 22.10 要素にアクセスする
`[key]` で指定したキーに対応する値にアクセスできます。キーが存在しない場合は、新しく要素が追加され、値はデフォルト値で初期化されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashTable<String, int32> table =
	{
		{ U"curry", 3 },
		{ U"sushi", 3 },
		{ U"hamburger", 0 },
	};

	Print << table[U"sushi"];

	Print << U"------";

	++table[U"curry"];

	table[U"hamburger"] = 20;

	Print << table;

	Print << U"------";

	// 存在しないキーを [] で指定すると、新しく要素が追加され、値はデフォルト値で初期化される
    Print << table[U"pasta"];
	++table[U"nachos"];

	Print << table;

	while (System::Update())
	{

	}
}
```


## 22.11 指定したキーが存在するかを調べる（contains）
`.contains(key)` で指定したキーが存在するかを調べることができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/11.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const HashTable<String, int32> table =
	{
		{ U"curry", 3 },
		{ U"sushi", 3 },
		{ U"hamburger", 0 },
	};

	Print << table.contains(U"sushi");

	// [] ではないため、ここでは pasta は追加されない
	Print << table.contains(U"pasta");

	Print << table;

	while (System::Update())
	{

	}
}
```


## 22.12 指定したキーが存在するか調べる（イテレータ）
`.find(key)` で指定したキーが存在するかを調べることができます。存在する場合は、その要素へのイテレータを返します。存在しない場合は、`end()` へのイテレータを返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/12.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashTable<String, int32> table =
	{
		{ U"curry", 3 },
		{ U"sushi", 3 },
		{ U"hamburger", 0 },
	};

	if (auto it = table.find(U"sushi");
		it != table.end())
	{
		// 存在すれば値を 1 増やす
		++it->second;
	}
	else
	{
		// 存在しなければ要素を追加する
		table.emplace(U"sushi", 1);
	}

	if (auto it = table.find(U"pasta");
		it != table.end())
	{
		// 存在すれば値を 1 増やす
		++it->second;
	}
	else
	{
		// 存在しなければ要素を追加する
		table.emplace(U"pasta", 1);
	}

	Print << table;

	while (System::Update())
	{

	}
}
```


## 22.13 範囲 for 文で要素にアクセスする
範囲 for 文を使うと、ハッシュテーブルのすべての要素にアクセスできます。順序は保証されません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/13.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashTable<String, int32> table =
	{
		{ U"curry", 3 },
		{ U"sushi", 3 },
		{ U"hamburger", 0 },
		{ U"pasta", 1 },
		{ U"pizza", 4 },
	};

	// 構造化束縛を使う
	for (auto&& [key, value] : table)
	{
		Print << key << U": " << value;
	}

	Print << U"------";

	// pair を使う
	for (auto&& elem : table)
	{
		Print << elem.first << U": " << elem.second;
	}

	while (System::Update())
	{

	}
}
```


## 22.14 要素を削除する
`.erase(key)` で指定したキーに対応する要素を削除できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/hashtable/14.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashTable<String, int32> table =
	{
		{ U"curry", 3 },
		{ U"sushi", 3 },
		{ U"hamburger", 0 },
		{ U"pasta", 1 },
		{ U"pizza", 4 },
	};

	table.erase(U"pizza");

	table.erase(U"sushi");

	Print << table;

	while (System::Update())
	{

	}
}
```

