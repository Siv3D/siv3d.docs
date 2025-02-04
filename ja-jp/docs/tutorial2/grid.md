# 37. 二次元配列
二次元配列クラス `Grid` の基本的な使い方を学びます。

## 37.1 Grid
- Siv3D では二次元配列のための動的配列クラス `Grid<Type>` が用意されています
- 一連の要素は 1 つの `Array<Type>` で管理され、すべての要素がメモリ上に連続して配置されます
	- したがって `Array<Arrayr<Type>>` よりも効率的に二次元配列を扱うことができます


## 37.2 二次元配列の作成
- 二次元配列は次のような方法で作成します
	- 空の配列を作成
	- リストから配列を作成
	- サイズ × 値で配列を作成
		- `Grid<int32> grid(Size{ 4, 3 }, -1);` は幅（列数）4, 高さ（行数）3 の配列を作成し、全ての要素を -1 で初期化します
		- `Grid<int32> grid(4, 3, -1);` も同様です
	- サイズ × デフォルト値で配列を作成 

```cpp
# include <Siv3D.hpp>

void Main()
{
	// パターン ①: 空の配列を作成
	{
		Grid<int32> grid;
		Print << grid;
	}

	Print << U"----";

	// パターン ②: リストから配列を作成
	{
		Grid<int32> grid =
		{
			{ 1, 2, 3 },
			{ 4, 5, 6 },
			{ 7, 8, 9 },
			{ 10, 11, 12 },
		};
		Print << grid;
	}

	Print << U"----";

	// パターン ③: サイズ × 値 で配列を作成
	{
		// 幅（列数）4, 高さ（行数）3 の配列を作成し、全ての要素を -1 で初期化する
		Grid<int32> grid(Size{ 4, 3 }, -1);
		Print << grid;
	}

	Print << U"----";

	// パターン ④: サイズ × 値 で配列を作成
	{
		// 幅（列数）4, 高さ（行数）3 の配列を作成し、全ての要素を -1 で初期化する
		Grid<int32> grid(4, 3, -1);
		Print << grid;
	}

	Print << U"----";

	// パターン ⑤: 個数 × デフォルト値で配列を作成
	{
		// 幅（列数）4, 高さ（行数）3 の配列を作成し、全ての要素を 0 で初期化する
		Grid<int32> grid(Size{ 4, 3 });
		Print << grid;
	}

	Print << U"----";

	// パターン ⑥: 個数 × デフォルト値で配列を作成
	{
		// 幅（列数）4, 高さ（行数）3 の配列を作成し、全ての要素を 0 で初期化する
		Grid<int32> grid(4, 3);
		Print << grid;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{}
----
{{1, 2, 3},
{4, 5, 6},
{7, 8, 9},
{10, 11, 12}}
----
{{-1, -1, -1, -1},
{-1, -1, -1, -1},
{-1, -1, -1, -1}}
----
{{-1, -1, -1, -1},
{-1, -1, -1, -1},
{-1, -1, -1, -1}}
----
{{0, 0, 0, 0},
{0, 0, 0, 0},
{0, 0, 0, 0}}
----
{{0, 0, 0, 0},
{0, 0, 0, 0},
{0, 0, 0, 0}}
```


## 37.3 二次元配列のサイズ
- `.width()` は幅（列数）を `size_t` 型で返します
- `.height()` は高さ（行数）を `size_t` 型で返します
- `.size()` は幅と高さを `Size` 型で返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid(Size{ 4, 3 }, -1);

	Print << grid.width();
	Print << grid.height();
	Print << grid.size();

	while (System::Update())
	{

	}
}
```
```txt title="出力"
4
3
(4, 3)
```


## 37.4 二次元配列が空であるかを調べる（1）
- `.isEmpty()` は配列が空であるか（要素数が 0 であるか）を `bool` 型で返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid1(Size{ 4, 3 }, -1);
	Print << grid1.isEmpty();

	Grid<int32> grid2;
	Print << grid2.isEmpty();

	while (System::Update())
	{

	}
}
```
```txt title="出力"
false
true
```


## 37.5 二次元配列が空であるかを調べる（2）
- `if (配列)` を使って配列が空であるかを調べます
- 配列が空である場合、配列は `false` と評価されます

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid1(Size{ 4, 3 }, -1);

	if (grid1)
	{
		Print << U"grid1 is not empty";
	}

	Grid<int32> grid2;

	if (not grid2)
	{
		Print << U"grid2 is empty";
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
grid1 is not empty
grid2 is empty
```


## 37.6 すべての要素を削除する
- `.clear()` で配列のすべての要素を削除し、空の配列にします
- 要素数が 0 のときに呼び出しても問題ありません（何もしません）

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid(Size{ 4, 3 }, -1);
	Print << grid;

	Print << U"----";

	grid.clear();
	Print << grid;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{{-1, -1, -1, -1},
{-1, -1, -1, -1},
{-1, -1, -1, -1}}
----
{}
```


## 37.7 範囲 for 文で配列を走査する（const 参照）
- 範囲 for 文を使って配列の要素を一次元的に走査します
- 各要素へのアクセスは、通常は const 参照で行います
- 範囲 for 文の中で、対象の配列のサイズを変更する操作は行わないでください

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid =
	{
		{ 1, 2, 3 },
		{ 4, 5, 6 },
		{ 7, 8, 9 },
		{ 10, 11, 12 },
	};

	for (const auto& elem : grid)
	{
		Print << elem;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
1
2
3
4
5
6
7
8
9
10
11
12
```




## 37.8 範囲 for 文で配列を走査する（参照）
- 範囲 for 文を使って配列の要素を一次元的に走査します
- ループ内で要素を変更する場合、const 参照の代わりに参照を使って要素にアクセスします
- 範囲 for 文の中で、対象の配列のサイズを変更する操作は行わないでください

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid =
	{
		{ 1, 2, 3 },
		{ 4, 5, 6 },
		{ 7, 8, 9 },
		{ 10, 11, 12 },
	};

	for (auto& elem : grid)
	{
		elem *= 2;
	}

	Print << grid;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{{2, 4, 6},
{8, 10, 12},
{14, 16, 18},
{20, 22, 24}}
```


## 37.9 指定したインデックスの要素にアクセスする
- `[y][x]` で、指定したインデックス（y 行目, x 列目）の要素にアクセスします
- インデックスは 0 から始まります
- 範囲外にアクセスしてはいけません

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid =
	{
		{ 1, 2, 3 },
		{ 4, 5, 6 },
		{ 7, 8, 9 },
		{ 10, 11, 12 },
	};

	Print << grid[0][0];
	Print << grid[3][2];

	grid[0][2] = 30;
	grid[1][1] = 50;

	Print << grid;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
1
12
{{1, 2, 30},
{4, 50, 6},
{7, 8, 9},
{10, 11, 12}}
```

- `Point` 型の値を使って `[Point{ x, y }]` でもアクセスできます
- `x` と `y` の順番が `[y][x]` と異なるに注意してください

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid =
	{
		{ 1, 2, 3 },
		{ 4, 5, 6 },
		{ 7, 8, 9 },
		{ 10, 11, 12 },
	};

	Print << grid[Point{ 0, 0 }];
	Print << grid[Point{ 2, 3 }];

	grid[Point{ 2, 0 }] = 30;
	grid[Point{ 1, 1 }] = 50;

	Print << grid;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
1
12
{{1, 2, 30},
{4, 50, 6},
{7, 8, 9},
{10, 11, 12}}
```


## 37.10 配列の終端に行を追加する
- `.push_back_row(value)` で、配列の末尾に全ての要素が `value` である行を追加します
- W × H の二次元配列に対して、`.push_back_row(value)` を 1 回呼び出すと、W × (H + 1) の二次元配列になります

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid =
	{
		{ 1, 2, 3 },
		{ 4, 5, 6 },
		{ 7, 8, 9 },
		{ 10, 11, 12 },
	};

	grid.push_back_row(99);

	Print << grid;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{{1, 2, 3},
{4, 5, 6},
{7, 8, 9},
{10, 11, 12},
{99, 99, 99}}
```


## 37.11 配列の終端から行を削除する
- `.pop_back_row()` で配列の末尾の行を削除します
- W × H の二次元配列に対して、`.pop_back_row()` を 1 回呼び出すと、W × (H - 1) の二次元配列になります
- 空の配列に対して `.pop_back_row()` を呼んではいけません
	- 事前に配列が空でないことを確認してください

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid =
	{
		{ 1, 2, 3 },
		{ 4, 5, 6 },
		{ 7, 8, 9 },
		{ 10, 11, 12 },
	};

	grid.pop_back_row();

	Print << grid;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{{1, 2, 3},
{4, 5, 6},
{7, 8, 9}}
```


## 37.12 配列の要素数を変更する
- 次のようなメンバ関数で、二次元配列のサイズを変更できます

| コード | 説明 |
|---|---|
| `.resize(size)` | 幅と高さを `size` に変更し、全ての要素をデフォルト値で初期化します |
| `.resize(width, height)` | 幅と高さを `width` と `height` に変更し、全ての要素をデフォルト値で初期化します |
| `.resize(size, value)` | 幅と高さを `size` に変更し、全ての要素を `value` で初期化します |
| `.resize(width, height, value)` | 幅と高さを `width` と `height` に変更し、全ての要素を `value` で初期化します |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid =
	{
		{ 1, 2, 3 },
		{ 4, 5, 6 },
		{ 7, 8, 9 },
		{ 10, 11, 12 },
	};

	grid.resize(5, 5);

	Print << grid;

	Print << U"----";

	grid.resize(2, 3);

	Print << grid;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{{1, 2, 3, 0, 0},
{4, 5, 6, 0, 0},
{7, 8, 9, 0, 0},
{10, 11, 12, 0, 0},
{0, 0, 0, 0, 0}}
----
{{1, 2},
{4, 5},
{7, 8}}
```



## 37.13 その他の挿入・削除操作
- `.insert_row(pos, value)` で、指定した位置に全ての要素が `value` である行を挿入します
- `.push_back_column(value)` で、全ての要素が `value` である列を追加します
- `.pop_back_column()` で、配列の末尾の列を削除します
- `.insert_column(pos, value)` で、指定した位置に全ての要素が `value` である列を挿入します
- `.remove_row(pos)` で、指定した位置の行を削除します
- `.remove_column(pos)` で、指定した位置の列を削除します
- これらの関数は、既存要素の移動を伴うため、二次元配列の要素数に比例したコストがかかります
	- 通常は避けるか、小さい配列でのみ使用するべきです

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid =
	{
		{ 1, 2, 3 },
		{ 4, 5, 6 },
		{ 7, 8, 9 },
		{ 10, 11, 12 },
	};

	grid.insert_row(0, -1);

	Print << grid;
	Print << U"----";

	grid.push_back_column(100);

	Print << grid;
	Print << U"----";

	grid.pop_back_column();

	Print << grid;
	Print << U"----";

	grid.insert_column(1, -1);

	Print << grid;
	Print << U"----";

	grid.remove_row(0);

	Print << grid;
	Print << U"----";

	grid.remove_column(1);

	Print << grid;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{{-1, -1, -1},
{1, 2, 3},
{4, 5, 6},
{7, 8, 9},
{10, 11, 12}}
----
{{-1, -1, -1, 100},
{1, 2, 3, 100},
{4, 5, 6, 100},
{7, 8, 9, 100},
{10, 11, 12, 100}}
----
{{-1, -1, -1},
{1, 2, 3},
{4, 5, 6},
{7, 8, 9},
{10, 11, 12}}
----
{{-1, -1, -1, -1},
{1, -1, 2, 3},
{4, -1, 5, 6},
{7, -1, 8, 9},
{10, -1, 11, 12}}
----
{{1, -1, 2, 3},
{4, -1, 5, 6},
{7, -1, 8, 9},
{10, -1, 11, 12}}
----
{{1, 2, 3},
{4, 5, 6},
{7, 8, 9},
{10, 11, 12}}
```


## 37.14 全ての要素に同じ値を代入する
- .fill(値) で、すべての要素に同じ値を代入します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid =
	{
		{ 1, 2, 3 },
		{ 4, 5, 6 },
		{ 7, 8, 9 },
		{ 10, 11, 12 },
	};

	grid.fill(1);

	Print << grid;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{{1, 1, 1},
{1, 1, 1},
{1, 1, 1},
{1, 1, 1}}
```


## 37.15 すべての要素に関数を適用した結果の配列を得る
- `.map(関数)` で、すべての要素に関数を適用した結果の配列を得ます
- 関数は、要素を引数にとり、変換後の要素を返す関数オブジェクトです

```cpp
# include <Siv3D.hpp>

void Main()
{
	Grid<int32> grid1 =
	{
		{ 1, 2, 3 },
		{ 4, 5, 6 },
		{ 7, 8, 9 },
		{ 10, 11, 12 },
	};

	Grid<double> grid2 = grid1.map([](int32 x) { return (x * 1.01); });

	Print << grid2;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{{1.01, 2.02, 3.03},
{4.04, 5.05, 6.06},
{7.07, 8.08, 9.09},
{10.1, 11.11, 12.12}}
```


## 37.16 一次元配列でのアクセス
- `.asArray()` で、二次元配列内部の一次元配列への参照を取得できます

```cpp
# include <Siv3D.hpp>

void PrintArray(const Array<int32>& a)
{
	Print << a;
}

void Main()
{
	Grid<int32> grid =
	{
		{ 1, 2, 3 },
		{ 4, 5, 6 },
		{ 7, 8, 9 },
		{ 10, 11, 12 },
	};

	PrintArray(grid.asArray());

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12}
```


## 37.17 二次元配列の可視化（数値）
- 二次元配列の内容をグリッド状に可視化するサンプルです
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/grid/17.png)

```cpp
# include <Siv3D.hpp>

void VisualizeGrid(const Grid<int32>& grid, const Font& font)
{
	// 枠になる背景部分を描画する
	Rect{ grid.size() * 80 }.draw(ColorF{ 0.2 });

	// 各セルの長方形を描画する
	for (int32 y = 0; y < grid.height(); ++y)
	{
		for (int32 x = 0; x < grid.width(); ++x)
		{
			const Rect rect{ (x * 80), (y * 80), 80 };
			rect.stretched(-1).draw();
		}
	}

	// 各セルの数値を描画する
	for (int32 y = 0; y < grid.height(); ++y)
	{
		for (int32 x = 0; x < grid.width(); ++x)
		{
			const auto& value = grid[y][x];

			const Rect rect{ (x * 80), (y * 80), 80 };

			font(value).drawAt(36, rect.center(), ColorF{ 0.2 });
		}
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	Grid<int32> grid =
	{
		{ 1, 2, 3 },
		{ 4, 5, 6 },
		{ 7, 8, 9 },
		{ 10, 11, 12 },
	};

	while (System::Update())
	{
		VisualizeGrid(grid, font);
	}
}
```


## 37.18 二次元配列の可視化（カラーマップ）
- 二次元配列の内容をグリッド状のカラーマップで可視化するサンプルです
- `ColorMap01F(value)` は、`0` から `1` の範囲の値を受け取り、それを人間工学的に見やすいカラーマップの `ColorF` に変換します
	- 0 ～ 1 の範囲で、値に応じてサーモグラフィーのような色を生成します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/grid/18.png)

```cpp
# include <Siv3D.hpp>

void VisualizeGrid(const Grid<double>& grid)
{
	// 各セルを描画する
	for (int32 y = 0; y < grid.height(); ++y)
	{
		for (int32 x = 0; x < grid.width(); ++x)
		{
			const double value = grid[y][x];
			const ColorF color = Colormap01F(value);
			const Rect rect{ (x * 30), (y * 30), 30 };
			rect.draw(color);
		}
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Grid<double> grid(Size{ 20, 20 });

	for (int32 y = 0; y < grid.height(); ++y)
	{
		for (int32 x = 0; x < grid.width(); ++x)
		{
			const double value = ((x + y) / 40.0);
			grid[y][x] = value;
		}
	}

	while (System::Update())
	{
		VisualizeGrid(grid);
	}
}
```
