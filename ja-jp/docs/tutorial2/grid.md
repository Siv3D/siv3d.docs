# 21. 二次元配列
二次元配列クラス `Grid` の基本的な使い方を学びます。

## 21.1 二次元配列クラス
Siv3D では二次元配列のための動的配列クラス `Grid<Type>` が用意されています。内部の要素は `Array<Type>` で管理されていて、すべての要素がメモリ上に連続して配置されています。そのため `std::vector<std::vector<Type>>` よりも効率的に要素にアクセスできます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/grid/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 4x3 の二次元配列を作成し、全ての要素を -1 で初期化する
	Grid<int32> grid(4, 3, -1);

	Print << grid;

	while (System::Update())
	{

	}
}
```


## 21.2 配列のサイズ
`.width()` で列数（幅）、`.height()` で行数（高さ）、`.size()` で行数と列数をまとめた `Size` 型の値を取得できます。`Size` は `Point` の型エイリアスです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/grid/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid(4, 3, -1);

	Print << grid.size(); // Size 型

	Print << grid.width();

	Print << grid.height();

	while (System::Update())
	{

	}
}
```


## 21.3 指定したインデックスの要素にアクセスする
`[y][x]` で、指定したインデックス（y 行目, x 列目）の要素にアクセスできます。インデックスは 0 から始まります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/grid/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid(4, 3, -1);

	grid[0][0] = 0;

	grid[0][1] = 10;

	grid[1][0] = 20;

	Print << grid;

	while (System::Update())
	{

	}
}
```

インデックス `[y][x]` は `Point` 型の値を使って `[Point{ x, y }]` でも指定できます。x と y の順番に注意してください。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid(4, 3, -1);

	const Point index{ 3, 2 };

	grid[index] = 99;

	Print << grid;

	while (System::Update())
	{

	}
}
```


## 21.4 範囲ベースの for 文で要素にアクセスする
範囲ベース for 文を使って配列の各要素にアクセスできます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/grid/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 4x3 の二次元配列を作成し、全ての要素を 0 で初期化する
	Grid<int32> grid(4, 3);

	int32 count = 0;

	for (auto& elem : grid) // 要素を変更する場合は参照
	{
		elem = count++;
	}

	Print << grid;

	for (const auto& elem : grid) // 要素を変更しない場合は const 参照
	{
		Print << elem;
	}

	while (System::Update())
	{

	}
}
```


## 21.5 空の配列
要素を保持していない、要素数が 0 の配列は**空の配列**です。代入や追加によって要素を追加すると、空でない配列になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/grid/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid;

	Print << grid.size();

	Print << grid.width();

	Print << grid.height();

	while (System::Update())
	{

	}
}
```


## 21.6 配列が空であるかを調べる
`Grid` 型の値 `g` が空であるかは、`if (g.isEmpty())` や `if (g)` / `if (not g)` で調べられます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/grid/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<String> words;

	Grid<int32> numbers(4, 3, -1);

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


## 21.7 配列に行を追加する
`.push_back_row(value)` で配列の末尾に、全ての要素が `value` である行を追加できます。W x H の二次元配列に対して、`.push_back_row(value)` を呼び出すと、W x (H + 1) の二次元配列になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/grid/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid(4, 3, -1);

	// 要素がすべて 5 である行を末尾に追加し、
	// 4x4 の二次元配列にする
	grid.push_back_row(5);

	Print << grid.size();

	Print << grid;

	while (System::Update())
	{

	}
}
```


## 21.8 配列を空にする
`.clear()` で配列を空にすることができます。二次元配列のサイズは 0 x 0 になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/grid/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid(4, 3, -1);

	// 配列を空にする
	grid.clear();

	Print << grid;

	Print << grid.isEmpty();

	Print << grid.size();

	while (System::Update())
	{

	}
}
```


## 21.9 末尾の行を削除する
`.pop_back_row()` で配列の末尾の行を削除できます。W x H の二次元配列に対して、`.pop_back_row()` を呼び出すと、W x (H - 1) の二次元配列になります。空の配列で `.pop_back_row()` を呼んではいけません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/grid/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid(4, 3, -1);

	// 末尾の行を削除して 4x2 の二次元配列にする
	grid.pop_back_row();

	Print << grid.size();

	Print << grid;

	while (System::Update())
	{

	}
}
```


## 21.10 配列の要素数を変更する
`.resize(w, h, value)` または `.resize(w, h)` で配列の要素数を変更できます。前者で要素数が増える場合、新しい要素は `value` で初期化されます。後者では、新しい要素はデフォルト値で初期化されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/grid/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid(2, 2, -1);

	grid.resize(6, 6, 2);

	Print << grid;

	Print << U"------";

	int32 count = 0;

	for (auto& elem : grid)
	{
		elem = count++;
	}

	Print << grid;

	Print << U"------";

	grid.resize(3, 3);

	Print << grid;

	while (System::Update())
	{

	}
}
```

## 21.11 全ての要素に同じ値を代入する
`.fill(value)` で配列の全ての要素に `value` を代入できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/grid/11.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid(4, 3, -1);

	Print << grid;

	// 全ての要素に 3 を代入する
	grid.fill(3);

	Print << grid;

	while (System::Update())
	{

	}
}
```


## 21.12 map 処理
`.map(f)` で配列の全ての要素に関数 `f` を適用した結果を持つ新しい二次元配列を作成できます。`f` は、配列の要素の型を引数に取り、新しい要素の型を返す関数です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/grid/12.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid;

	grid.resize(6, 6);

	int32 count = 0;

	for (auto& elem : grid)
	{
		elem = count++;
	}

	Print << grid;

	Print << U"------";

	// grid の全ての要素を 0.1 倍した値を持つ Grid<double> を作成する
	const Grid<double> grid2 = grid.map([](int32 n) { return (n * 0.1); });

	Print << grid2;

	while (System::Update())
	{

	}
}
```


## 21.13 一次元でのアクセス
`.asArray()` で、二次元配列の内部の一次元配列への参照を取得できます。W x H の二次元配列に対して、`.asArray()` を呼び出すと、要素数が (W x H) の一次元配列を得られます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/grid/13.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid;

	grid.resize(4, 3);

	int32 count = 0;

	for (auto& elem : grid)
	{
		elem = count++;
	}

	// 内部の一次元配列を取得する
	const Array<int32>& a = grid.asArray();

	Print << a;
	
	while (System::Update())
	{

	}
}
```
