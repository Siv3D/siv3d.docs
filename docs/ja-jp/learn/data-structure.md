# 6. データ構造



#### 文字や文字列

??? summary "文字や文字列に関する主な型、クラス"
	| 型名           | 説明                              |
	|--------------|---------------------------------|
	| char32       | UTF-32 の 1 要素（`char32_t` の別名） |
	| String       | 文字列クラス。要素は `char32`           |


??? summary "データ構造に関する主なクラス"
	| 型名                                                        | 説明                                                         |
	|-----------------------------------------------------------|------------------------------------------------------------|
	| Array&lt;Type, Allocator&gt;                              | 動的配列（C++ 標準ライブラリの `std::vector` の置き換え）                   |
	| Grid&lt;Type, Allocator&gt;                               | 動的な二次元配列                                                 |
	| HashSet&lt;Type, Hash, Eq, Alloc&gt;                      | ハッシュテーブルによる Set（C++ 標準ライブラリの `std::unordered_set` の置き換え） |
	| HashTable&lt;Key, Value, Hash, Eq, Alloc&gt;              | ハッシュテーブルによる Map（C++ 標準ライブラリの `std::unordered_map` の置き換え） |
	| Optional&lt;Type&gt;                                      | 無効値を表現できる型（C++ 標準ライブラリの `std::optional` の置き換え）           |
	| std::array&lt;Type, size_t&gt;                            | 固定長配列                                                    |


## 6.1 文字列クラス
Siv3D では `String` 型を使って文字列を表現します。`String` は、UTF-32 のコードポイントを表現する `char32` 型（文字）の集合です。UTF-32 の文字や文字列リテラルには、`U'あ'`, `U"Hello"` のように `U` プレフィックスを付けます。

`String` の内部データは `std::u32string` で、格納されている文字列はメモリ上での連続性が保証されています。Siv3D の `String` は C++ の標準ライブラリの文字列クラスより多くのメンバ関数を持ち、様々な便利な機能を提供します。


### 6.1.1 String の基本
`.size()` を使うと、文字列の長さを取得できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/string/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"Siv3D";

	Print << s;

	// 文字列の長さ
	Print << s.size();

	while (System::Update())
	{

	}
}
```


### 6.1.2 指定したインデックスの文字にアクセス
`[]` を使って 0 から始まるインデックスを指定することで、指定したインデックスの位置にある要素にアクセスできます。インデックスを指定する代わりに、`.front()`, `.back()` を使うと、それぞれ先頭、末尾の要素にアクセスできます。存在しない要素にアクセスすることはできません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/string/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	// 0 番目の要素
	Print << s[0];

	// 2 番目の要素
	Print << s[2];

	// 先頭の要素
	Print << s.front();

	// 末尾の要素
	Print << s.back();

	s[3] = U'4';

	Print << s;

	while (System::Update())
	{

	}
}
```


### 6.1.3 空の文字列
要素を保持していない文字列を「空の文字列」と呼びます。`String` 型の値 `s` が空であるかは、`if (s.isEmpty())` や `if (s)` / `if (not s)` で調べられます。`not` は `!` と同じです。Siv3D では `!` よりも視認性の高い `not` を使うコーディングスタイルを採用しています。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/string/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s;

	if (s.isEmpty())
	{
		Print << U"s.isEmpty()";
	}

	if (not s)
	{
		Print << U"not s";
	}

	while (System::Update())
	{

	}
}
```


### 6.1.4 文字・文字列の追加
別の文字を末尾に追加するには `<<` を使います。別の文字列を末尾に追加するには `+=` を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/string/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s;

	s << U'S' << 'i';

	Print << s;

	s += U"v3D";

	Print << s;

	while (System::Update())
	{

	}
}
```


### 6.1.5 文字列の消去
`.clear()` を使うと自身の文字列を消去して空の文字列になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/string/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	Print << s;

	s = U"Hello";

	Print << s;

	s.clear();

	// 何も表示されない
	Print << s;

	s = U"Siv3D";

	Print << s;

	while (System::Update())
	{

	}
}
```


### 6.1.6 要素の削除
`.pop_front()` は先頭の要素を消去します。`.pop_back()` は末尾の要素を削除します。いずれも空の文字列に対して実行することはできません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/string/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	s.pop_front();

	Print << s;

	s.pop_back();

	Print << s;

	while (System::Update())
	{

	}
}
```


## 6.2 動的配列
Siv3D で動的配列を扱うときは `Array<Type>` クラステンプレートを使います。`std::vector` のメンバ関数に加え、さらに多くの便利なメンバ関数を提供し、Siv3D のクラスや関数とも連係しやすいため、優れた実行時性能と、コードの短縮につながります。Siv3D のプログラムでは `std::vector` よりも `Array` を優先して使います。`Array` は `std::vector` と同様に、格納されている要素のメモリの連続性が保証されています。

### 6.2.1 要素一覧の表示
`Array` の要素が `Print` で表示できる型であれば、`Array` を `Print` に送ることで、要素一覧を表示できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/1.1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> values = { 1, 6, 3, 5, 2, 10 };

	Print << values;

	Array<Vec2> points =
	{
		Vec2{ 100, 100 }, Vec2{ 200, 200 },
		Vec2{ 300, 300 }, Vec2{ 400, 400 }
	};

	Print << points;

	while (System::Update())
	{

	}
}
```


### 6.2.2 要素の追加
`Array` は `<<` 演算子で要素を追加できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/1.2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<Vec2> points;

	while (System::Update())
	{
		if (MouseL.down())
		{
			// 配列に要素を追加
			points << Cursor::Pos();
		}

		for (const auto& point : points)
		{
			Circle{ point, 10 }.draw();
		}
	}
}
```


### 6.2.3 指定したインデックスの要素にアクセス
`[]` を使って 0 から始まるインデックスを指定することで、指定したインデックスの位置にある要素にアクセスできます。インデックスを指定する代わりに、`.front()`, `.back()` を使うと、それぞれ先頭、末尾の要素にアクセスできます。存在しない要素にアクセスすることはできません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/1.3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<Vec2> points =
	{
		Vec2{ 100, 100 }, Vec2{ 200, 200 },
		Vec2{ 300, 300 }, Vec2{ 400, 400 }
	};

	// 0 番目の要素にアクセス
	Print << points[0];

	// 2 番目の要素にアクセス
	Print << points[2];

	// 先頭の要素にアクセス
	Print << points.front();

	// 末尾の要素にアクセス
	Print << points.back();

	while (System::Update())
	{

	}
}
```


### 6.2.4 要素の数、要素の削除
配列の要素数を調べるには `.size()`, 配列の要素をすべて削除するには `.clear()` を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/1.4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<Vec2> points =
	{
		Vec2{ 100, 100 }, Vec2{ 200, 200 },
		Vec2{ 300, 300 }, Vec2{ 400, 400 }
	};

	while (System::Update())
	{
		ClearPrint();
		Print << U"count: " << points.size();

		if (MouseL.down())
		{
			// 要素をすべて削除
			points.clear();
		}

		for (const auto& point : points)
		{
			Circle{ point, 10 }.draw();
		}
	}
}
```


### 6.2.5 空の配列
要素数が 0 の配列を「空の配列」と呼びます。配列 `a` が空であるかは、`if (a.isEmpty())` や `if (a)` / `if (not a)` で調べられます。`not` は `!` と同じです。Siv3D では `!` よりも視認性の高い `not` を使うコーディングスタイルを採用しています。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/1.5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> values = { 20, 30, 40, 50 };
	Array<Vec2> points;

	// 配列が空かどうかを表示
	Print << values.isEmpty();
	Print << points.isEmpty();

	if (values)
	{
		Print << U"A";
	}

	if (not points)
	{
		Print << U"B";
	}

	while (System::Update())
	{

	}
}
```


### 6.2.6 末尾の要素の削除
配列の末尾の要素を削除するには `.pop_back()` を使います。空の配列に `.pop_back()` を使うことはできないため、空かどうかのチェックを忘れないでください。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/1.6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<Vec2> points =
	{
		Vec2{ 100, 100 }, Vec2{ 200, 200 },
		Vec2{ 300, 300 }, Vec2{ 400, 400 }
	};

	while (System::Update())
	{
		ClearPrint();
		Print << U"count: " << points.size();

		if (points && MouseL.down())
		{
			// 末尾の要素を削除
			points.pop_back();
		}

		for (const auto& point : points)
		{
			Circle{ point, 10 }.draw();
		}
	}
}
```


### 6.2.7 特定の条件を満たす要素の削除
配列から特定の条件を満たす要素を削除するには、`.remove_if()` に、要素を引数にとり、削除の可否を `bool` 型で返すラムダ式、または関数オブジェクトを渡します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/1.7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> values = { 1, 6, 3, 5, 2, 10 };
	
	Print << U"before: " << values;
	
	// 5 よりも大きい要素を削除
	values.remove_if([](int32 n) { return (5 < n); });
	
	Print << U"after: " << values;


	Array<Vec2> points =
	{
		Vec2{ 100, 100 }, Vec2{ 200, 200 },
		Vec2{ 300, 300 }, Vec2{ 400, 400 }
	};
	
	Print << U"before: " << points;
	
	// y 成分が 250 より大きい要素を削除
	points.remove_if([](const Vec2& v) { return (250 < v.y); });
	
	Print << U"after: " << points;

	while (System::Update())
	{

	}
}
```


### 6.2.8 イテレータを使った要素の削除
`.erase()` に特定の要素を指すイテレータを渡すことで、その要素を配列から削除できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/1.8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<Vec2> points =
	{
		Vec2{ 100, 100 }, Vec2{ 200, 200 },
		Vec2{ 300, 300 }, Vec2{ 400, 400 }
	};

	while (System::Update())
	{
		// イテレータですべての要素にアクセスする
		for (auto it = points.begin(); it != points.end();)
		{
			// 円がクリックされたらその地点を表す要素を削除
			if (Circle(*it, 30).leftClicked())
			{
				// 現在のイテレータが指す要素を削除し、イテレータを進める
				it = points.erase(it);
			}
			else
			{
				// イテレータを進める
				++it;
			}
		}

		for (const auto& point : points)
		{
			Circle{ point, 30 }.draw();
		}
	}
}
```


### 6.2.9 要素数を指定した初期化
`Array` のコンストラクタ引数に要素の個数と初期化する値を渡し、指定した個数だけ値をコピーした配列を作成できます。このコンストラクタでは `{}` ではなく `()` を使うことに注意します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/1.9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 10 個の 5
	Array<int32> values(10, 5);

	Print << values;

	// 5 個の Vec2{ 0, 0 }
	Array<Vec2> points(5, Vec2{ 0, 0 });

	Print << points;

	while (System::Update())
	{

	}
}
```


## 6.3 二次元配列
方眼紙のように区切ったマップの情報や、スプレッドシートのように、二次元配列が必要な情報を扱うときには `Grid<Type>` クラステンプレートを使います。`Grid` を使うことで、動的な二次元配列を便利に効率的に扱えます。`Grid` の内部では 1 つの `Array` ですべての要素を連続的に保持しています。

`Grid` クラスは、実質的には次のような構造です。

```cpp
// 説明のため簡略化
template <class Type>
class Grid
{
	Array<Type> m_data;
	size_t m_width;
	size_t m_height;
};
```

`Grid` 型の変数 `grid` に対する、インデックスによる要素へのアクセス `grid[y][x] = value;` は、内部では `m_data[y * m_width + x] = value;` になります。また、`Point` 型の値 `pos` による `grid[pos] = value;` は、`m_data[pos.y * m_width + pos.x] = value;` になります。


### 6.3.1 Grid の基本

`.size()` はグリッドのサイズを `Size` 型（`Point` 型のエイリアス）で返します。サイズの X 成分、Y 成分は `.width()`, `.height()` に対応します。

要素にアクセスする際のインデックスとして、`[y][x]` の他に `Point` 型の値を使うことができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/2.1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<double> grid =
	{
		{ 0.1, 0.2, 0.3, 0.4, 0.5, 0.6 },
		{ 1.0, 0.95, 0.9, 0.85, 0.8, 0.75 }
	};

	// グリッドのサイズ Size{ 幅, 高さ } 
	Print << grid.size();

	// グリッドの幅
	Print << U"width: " << grid.width();

	// グリッドの高さ（行数）
	Print << U"height: " << grid.height();

	// 0 行目 0 番目の要素にアクセス
	Print << grid[0][0];

	// 0 行目 1 番目の要素にアクセス
	Print << grid[0][1];

	// 1 行目 5 番目の要素にアクセス
	Print << grid[1][5];

	// 1 行目 5 番目の要素にアクセス (X と Y の順番に注意)
	Print << grid[Point{ 5, 1 }];

	while (System::Update())
	{
		for (auto y : step(grid.height()))
		{
			for (auto x : step(grid.width()))
			{
				Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ grid[y][x] });
			}
		}
	}
}
```


### 6.3.2 サイズを指定した初期化
`Grid` のコンストラクタ引数に、グリッドのサイズと初期化する値を渡すことができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/2.1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 5 列 x 3 行のグリッド（幅が 5, 高さが 3)
	// 要素の値はすべて -1
	Grid<int32> grid(5, 3, -1);

	Print << grid;

	Print << U"----";

	grid[2][0] = 123;

	Print << grid;

	while (System::Update())
	{

	}
}
```


## 6.4 ハッシュテーブル
キーと値の組（エントリ）を格納し、高速に検索できるデータ構造（ハッシュテーブル）として、Siv3D は `HashTable<Key, Value>` クラステンプレートを提供しています。`std::unordered_map` よりも高速な実装を持ち、Siv3D のクラスや関数とも連係が容易です。`std::unordered_map` と同様に、各エントリは追加した順番とは異なる順序で格納されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ハッシュテーブルの構築
	HashTable<String, int32> table =
	{ 
		{ U"Red", 1 },
		{ U"Green", 2 },
		{ U"Black", 3 },
		{ U"White", 4 },
	};

	// エントリの追加
	table.emplace(U"Yellow", 5);

	// 値のルックアップ
	Print << table[U"Red"];
	Print << table[U"White"];

	// エントリが存在するかを取得
	Print << table.contains(U"Green");
	Print << table.contains(U"Pink");

	// エントリの削除
	table.erase(U"Red");

	// イテレータを使ったループと値の変更
	for (auto it = table.begin(); it != table.end(); ++it)
	{
		it->second += 100;
	}

	Print << U"---";

	// 値を変更しないループはこの書き方もできる
	for (auto [key, value] : table)
	{
		Print << key << U": " << value;
	}

	while (System::Update())
	{

	}
}
```


## 6.5 ハッシュセット
キーとのみを格納し、高速にキーを検索できるハッシュセットとして、Siv3D は `HashSet<Key>` クラステンプレートを提供しています。`std::unordered_set` よりも高速な実装を持ち、Siv3D のクラスや関数とも連係が容易です。`std::unordered_set` と同様に、各エントリは追加した順番とは異なる順序で格納されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ハッシュテーブルの構築
	HashSet<String> table =
	{
		U"Red", U"Green", U"Black", U"White"
	};

	// エントリの追加
	table.emplace(U"Yellow");

	// エントリが存在するかを取得
	Print << table.contains(U"Green");
	Print << table.contains(U"Pink");

	// エントリを削除
	table.erase(U"Red");

	Print << U"---";

	for (const auto& key : table)
	{
		Print << key;
	}

	while (System::Update())
	{

	}
}
```


## 6.6 オプショナル型
`Optioanl<Type>` 型を使うと、任意の型 `Type` が「無効値」も保持できるようになります。サイズが 0 か 1 のいずれかである `Array<Type>` と考えるとわかりやすいです。C++ 標準では `std::optional<Type>` に相当します。

オプショナル型の値 `opt` が有効値を保持しているかは `if (opt)` や `if (opt.has_value())` で調べられます。`Type` 型の値を取り出すには `*opt` または `opt.value()` を使います。後者は有効値を保持しているかチェックし、保持していなかった場合には `std::bad_optional_access` 例外を投げます。`opt.value_or(x)` は、有効値を保持している場合はその値を、保持していない場合は代わりに `x` を返します。`Type` 型のメンバにアクセスするには `opt->` を使います。

無効値を表現する `none` およびそれと同値の `unspecified` という定数があり、オプショナル型の値に代入できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/data-structure/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 最初は無効値
	Optional<Point> pos;

	if (not pos)
	{
		Print << U"not pos";
	}

	// 有効値を持たせる
	pos = Point{ 100, 200 };

	if (pos)
	{
		Print << *pos;
	}

	// 無効値にする
	pos.reset();

	Print << pos.has_value();

	// pos が無効値の場合 Point{ -1, -1 } を返す
	Print << pos.value_or(Point{ -1, -1 });

	pos = Point{ 100, 200 };

	Print << pos.value_or(Point{ -1, -1 });

	Print << pos->x;

	Print << pos->y;

	// 無効値を代入する
	pos = none;

	Print << pos.has_value();

	while (System::Update())
	{

	}
}
```


## 6.7 これ以外のデータ構造
Siv3D では、配列の要素数がコンパイル時に決まっていて実行中に変更されない静的配列は `std::array` を使います。また、動的配列は `std::deque` や `std::list` を使うよりも、メモリが連続する `Array` のほうが実行時性能に優れるケースが多くあります。要素数が数万規模まで大きくならないような動的配列については `Array` を優先して使うことを推奨します。

