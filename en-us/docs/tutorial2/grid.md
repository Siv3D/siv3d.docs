# 37. Two-Dimensional Arrays
Learn the basic usage of the two-dimensional array class `Grid`.

## 37.1 Grid
- Siv3D provides the dynamic array class `Grid<Type>` for two-dimensional arrays
- All elements are managed by a single `Array<Type>` and arranged consecutively in memory
	- Therefore, it can handle two-dimensional arrays more efficiently than `Array<Array<Type>>`


## 37.2 Creating Two-Dimensional Arrays
- Two-dimensional arrays are created in the following ways:
	- Create an empty array
	- Create an array from a list
	- Create an array with size × value
		- `Grid<int32> grid(Size{ 4, 3 }, -1);` creates an array with width (columns) 4, height (rows) 3, initializing all elements to -1
		- `Grid<int32> grid(4, 3, -1);` does the same
	- Create an array with size × default value

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Pattern ①: Create an empty array
	{
		Grid<int32> grid;
		Print << grid;
	}

	Print << U"----";

	// Pattern ②: Create an array from a list
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

	// Pattern ③: Create an array with size × value
	{
		// Create an array with width (columns) 4, height (rows) 3, initializing all elements to -1
		Grid<int32> grid(Size{ 4, 3 }, -1);
		Print << grid;
	}

	Print << U"----";

	// Pattern ④: Create an array with size × value
	{
		// Create an array with width (columns) 4, height (rows) 3, initializing all elements to -1
		Grid<int32> grid(4, 3, -1);
		Print << grid;
	}

	Print << U"----";

	// Pattern ⑤: Create an array with count × default value
	{
		// Create an array with width (columns) 4, height (rows) 3, initializing all elements to 0
		Grid<int32> grid(Size{ 4, 3 });
		Print << grid;
	}

	Print << U"----";

	// Pattern ⑥: Create an array with count × default value
	{
		// Create an array with width (columns) 4, height (rows) 3, initializing all elements to 0
		Grid<int32> grid(4, 3);
		Print << grid;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
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


## 37.3 Getting Size
- `.width()` returns the width (columns) as `size_t` type
- `.height()` returns the height (rows) as `size_t` type
- `.size()` returns width and height as `Size` type

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
```txt title="Output"
4
3
(4, 3)
```


## 37.4 Checking if Empty (1)
- `.isEmpty()` returns whether the array is empty (has 0 elements) as `bool` type

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
```txt title="Output"
false
true
```


## 37.5 Checking if Empty (2)
- Use `if (array)` to check if the array is empty
- If the array is empty, it evaluates to `false`

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
```txt title="Output"
grid1 is not empty
grid2 is empty
```


## 37.6 Removing All Elements
- `.clear()` removes all elements from the array, making it empty
- It's safe to call when there are 0 elements (does nothing)

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
```txt title="Output"
{{-1, -1, -1, -1},
{-1, -1, -1, -1},
{-1, -1, -1, -1}}
----
{}
```


## 37.7 Array Traversal with Range-for Loop (const reference)
- Use range-for loops to traverse array elements one-dimensionally
- Element access is usually done with const reference
- Don't perform operations that change the target array's size within range-for loops

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
```txt title="Output"
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


## 37.8 Array Traversal with Range-for Loop (reference)
- Use range-for loops to traverse array elements one-dimensionally
- When modifying elements within loops, use reference instead of const reference to access elements
- Don't perform operations that change the target array's size within range-for loops

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
```txt title="Output"
{{2, 4, 6},
{8, 10, 12},
{14, 16, 18},
{20, 22, 24}}
```


## 37.9 Accessing Elements at Specified Index
- Use `[y][x]` to access elements at specified index (row y, column x)
- Indices start from 0
- Don't access out of bounds

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
```txt title="Output"
1
12
{{1, 2, 30},
{4, 50, 6},
{7, 8, 9},
{10, 11, 12}}
```

- You can also access using `Point` type values with `[Point{ x, y }]`
- Note that the order of `x` and `y` is different from `[y][x]`

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
```txt title="Output"
1
12
{{1, 2, 30},
{4, 50, 6},
{7, 8, 9},
{10, 11, 12}}
```


## 37.10 Adding Rows to the End
- `.push_back_row(value)` adds a row where all elements are `value` to the end of the array
- For a W × H two-dimensional array, calling `.push_back_row(value)` once makes it a W × (H + 1) two-dimensional array

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
```txt title="Output"
{{1, 2, 3},
{4, 5, 6},
{7, 8, 9},
{10, 11, 12},
{99, 99, 99}}
```


## 37.11 Removing Rows from the End
- `.pop_back_row()` removes the last row from the array
- For a W × H two-dimensional array, calling `.pop_back_row()` once makes it a W × (H - 1) two-dimensional array
- Don't call `.pop_back_row()` on an empty array
	- Check that the array is not empty beforehand

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
```txt title="Output"
{{1, 2, 3},
{4, 5, 6},
{7, 8, 9}}
```


## 37.12 Changing Element Count
- You can change the size of a two-dimensional array with the following member functions:

| Code | Description |
|---|---|
| `.resize(size)` | Change width and height to `size`, initializing all elements to default value |
| `.resize(width, height)` | Change width and height to `width` and `height`, initializing all elements to default value |
| `.resize(size, value)` | Change width and height to `size`, initializing all elements to `value` |
| `.resize(width, height, value)` | Change width and height to `width` and `height`, initializing all elements to `value` |

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
```txt title="Output"
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


## 37.13 Other Insert/Delete Operations
- `.insert_row(pos, value)` inserts a row where all elements are `value` at the specified position
- `.push_back_column(value)` adds a column where all elements are `value`
- `.pop_back_column()` removes the last column from the array
- `.insert_column(pos, value)` inserts a column where all elements are `value` at the specified position
- `.remove_row(pos)` removes the row at the specified position
- `.remove_column(pos)` removes the column at the specified position
- These functions involve moving existing elements, so cost is proportional to the number of elements in the two-dimensional array
	- Should usually be avoided or used only with small arrays

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
```txt title="Output"
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


## 37.14 Assigning the Same Value to All Elements
- `.fill(value)` assigns the same value to all elements

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
```txt title="Output"
{{1, 1, 1},
{1, 1, 1},
{1, 1, 1},
{1, 1, 1}}
```


## 37.15 Getting Results of Applying Functions to All Elements
- `.map(function)` gets an array with the function applied to all elements
- The function is a function object that takes an element as argument and returns the converted element

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
```txt title="Output"
{{1.01, 2.02, 3.03},
{4.04, 5.05, 6.06},
{7.07, 8.08, 9.09},
{10.1, 11.11, 12.12}}
```


## 37.16 Accessing as One-Dimensional Array
- `.asArray()` gets a reference to the internal one-dimensional array of the two-dimensional array

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
```txt title="Output"
{1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12}
```


## 37.17 Visualizing Two-Dimensional Arrays (Numbers)
- This is a sample for visualizing two-dimensional array contents in a grid
- Due to Siv3D's specifications, drawing shapes together and fonts together improves runtime performance, so we use double loops twice for drawing
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/grid/17.png)

```cpp
# include <Siv3D.hpp>

void VisualizeGrid(const Grid<int32>& grid, const Font& font)
{
	// Draw background part that becomes the frame
	Rect{ grid.size() * 80 }.draw(ColorF{ 0.2 });

	// Draw rectangles for each cell
	for (int32 y = 0; y < grid.height(); ++y)
	{
		for (int32 x = 0; x < grid.width(); ++x)
		{
			const Rect rect{ (x * 80), (y * 80), 80 };
			rect.stretched(-1).draw();
		}
	}

	// Draw numbers for each cell
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


## 37.18 Visualizing Two-Dimensional Arrays (Color Map)
- This is a sample for visualizing two-dimensional array contents as a grid color map
- `ColorMap01F(value)` takes a value in the range `0` to `1` and converts it to a `ColorF` with ergonomically visible color mapping
	- Generates thermography-like colors according to values in the 0-1 range
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/grid/18.png)

```cpp
# include <Siv3D.hpp>

void VisualizeGrid(const Grid<double>& grid)
{
	// Draw each cell
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