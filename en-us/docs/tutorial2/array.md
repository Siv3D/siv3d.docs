# 22. Arrays
Learn the basic usage of the dynamic array class `Array`.

## 22.1 Array
- In Siv3D, dynamic arrays are handled with `Array<Type>`
- `Array` provides functionality equivalent to `std::vector`, plus additional member functions
- Like `std::vector`, elements are guaranteed to be contiguous in memory

```cpp
// Array to store int32 type values
Array<int32> a = { 10, 20, 30, 40, 50 };

// Array to store double type values
Array<double> b = { 1.1, 2.2, 3.3, 4.4, 5.5 };

// Array to store String type values
Array<String> c = { U"Apple", U"Bird", U"Cat", U"Dog" };
```


## 22.2 Creating Arrays
- Arrays can be created in the following ways:
	- Create an empty array
	- Create an array from a list
	- Create an array with count × value
	- Create an array with count × default value
		- `Array<int32> v(5);` creates an array initialized with 5 zeros
		- `Array<double> v(5);` creates an array initialized with 5 values of 0.0
		- `Array<String> v(5);` creates an array initialized with 5 empty strings

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Pattern ①: Create an empty array
	{
		Array<int32> v;
		Print << v;
	}

	// Pattern ②: Create an array from a list
	{
		Array<int32> v = { 10, 50, 30, 20, 40 };
		Print << v;
	}

	// Pattern ③: Create an array with count × value
	{
		Array<int32> v(5, -5);
		Print << v;
	}

	// Pattern ④: Create an array with count × default value
	{
		Array<int32> v(5);
		Print << v;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
{}
{10, 50, 30, 20, 40}
{-5, -5, -5, -5, -5}
{0, 0, 0, 0, 0}
```


## 22.3 Getting the Number of Elements
- `.size()` returns the number of elements in the array as type `size_t`

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
```txt title="Output"
5
0
```


## 22.4 Checking if Empty (1)
- `.isEmpty()` returns whether the array is empty (has 0 elements) as a `bool`

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
```txt title="Output"
false
true
```


## 22.5 Checking if Empty (2)
- Use `if (array)` to check if an array is empty
- If the array is empty, it evaluates to `false`

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
```txt title="Output"
v1 is not empty
v2 is empty
```


## 22.6 Adding Elements to the End
- Use `v << x;` to add element `x` to the end of array `v`
- This is a shorter way to write `.push_back(x)` from `std::vector`
- Adding to the end of an array is the most efficient compared to adding at other positions (beginning or middle)

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
```txt title="Output"
{10, 50, 30}
{10, 50, 30, 20, 40}
```


## 22.7 Removing Elements from the End
- `.pop_back()` removes the last element of the array
- Must not be called when the number of elements is 0
	- Check that the number of elements is not 0 beforehand
- Removing the last element of an array is the most efficient compared to removing elements at other positions (beginning or middle)

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
```txt title="Output"
{10, 50, 30, 20, 40}
{10, 50, 30, 20}
{10, 50, 30}
{}
```


## 22.8 Removing All Elements
- `.clear()` removes all elements from the array, making it empty
- It's safe to call when the number of elements is 0 (it does nothing)

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
```txt title="Output"
{10, 50, 30, 20, 40}
{}
```


## 22.9 Changing the Number of Elements
- `.resize(n)` changes the number of elements in the array to `n`
- If the number of elements increases, new elements are initialized with default values

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
```txt title="Output"
{10, 50, 30, 20, 40}
{10, 50, 30}
{10, 50, 30, 0, 0}
```


## 22.10 Traversing Arrays with Range-based for Loop (const reference)
- Use range-based for loops to traverse array elements
- Access to each element is typically done by const reference
- Do not perform operations that change the size of the target array within a range-based for loop

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
```txt title="Output"
10
50
30
20
40
```


## 22.11 Traversing Arrays with Range-based for Loop (reference)
- Use range-based for loops to traverse array elements
- When modifying elements within the loop, use reference instead of const reference to access elements
- Do not perform operations that change the size of the target array within a range-based for loop

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
```txt title="Output"
{20, 100, 60, 40, 80}
```


## 22.12 Accessing Elements at Specified Index
- Use `[i]` to access the `i`-th element of the array
	- `i` is counted from 0. Valid indices are from `0` to `.size() - 1`
- Do not access out of bounds

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
```txt title="Output"
10
40
{10, 500, 30, 20, 40}
```


## 22.13 Accessing First and Last Elements
- `.front()` returns a reference to the first element
	- Same as `v[0]`
- `.back()` returns a reference to the last element
	- Same as `v[v.size() - 1]`
- Neither should be called when the number of elements is 0

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
```txt title="Output"
10
40
{100, 50, 30, 20, 400}
```


## 22.14 Getting Iterators to Beginning and End
- `.begin()` returns an iterator to the beginning
- `.end()` returns an iterator to the end

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
```txt title="Output"
10
50
```


## 22.15 Other Insertion and Deletion Operations
- `.push_front(value)` adds an element to the beginning
- `.pop_front()` removes the first element
- `.insert(iterator, value)` inserts an element at the position specified by the iterator
- `.append(array)` adds another array to the end
- `.erase(iterator)` removes the element at the position specified by the iterator
- `.erase(iterator1, iterator2)` removes elements in the specified range
- Inserting or deleting elements at the beginning or middle involves moving existing elements after that position, so it has a cost proportional to the number of elements after
	- This should usually be avoided or used only with small arrays

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
```txt title="Output"
{5, 10, 20, 30, 40, 50}
{10, 20, 30, 40, 50}
{10, 20, 25, 30, 40, 50}
{10, 20, 30, 40, 50}
{10, 20, 40, 50}
{40, 50}
```


## 22.16 Removing Elements that Meet a Condition (Iterator Method)
- To remove elements that meet a condition using iterator loops, do the following:

```cpp title="Remove elements less than 30 from the array"
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
```txt title="Output"
{50, 30, 40}
```


## 22.17 Removing Elements that Meet a Condition (`.remove_if()` Method)
- `.remove_if(function describing condition)` removes elements that meet the condition
	- This is more concise than the iterator method
- The "function describing condition" is a function object that takes an element as an argument (by value or const reference) and returns a `bool`
	- Use functions or lambda expressions

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		Array<int32> v = { 11, 22, 33, 44, 55 };

		// Remove even elements
		v.remove_if(IsEven);

		Print << v;
	}

	{
		Array<int32> v = { 10, 50, 30, 20, 40 };

		// Remove elements less than 30
		v.remove_if([](int32 x) { return x < 30; });

		Print << v;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
{11, 33, 55}
{50, 30, 40}
```


## 22.18 Sorting Arrays
- `.sort()` sorts the array in ascending order
- `.rsort()` sorts the array in descending order

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
```txt title="Output"
{10, 20, 30, 40, 50}
{Dog, Cat, Bird, Apple}
```


## 22.19 Reversing Arrays
- `.reverse()` reverses the order of the array

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
```txt title="Output"
{40, 20, 30, 50, 10}
```


## 22.20 Shuffling Array Elements
- `.shuffle()` shuffles the elements of the array

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
```txt title="Example Output"
{4, 6, 2, 1, 5, 3}
```


## 22.21 Sum of Array Elements
- `.sum()` calculates the sum of array elements

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
```txt title="Output"
150
```


## 22.22 Assigning the Same Value to All Elements
- `.fill(value)` assigns the same value to all elements

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
```txt title="Output"
{100, 100, 100, 100, 100}
```


## 22.23 Getting Results of Applying a Function to All Elements
- `.map(function)` returns an array with the results of applying a function to all elements
- The function is a function object that takes an element as an argument and returns the transformed element

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
```txt title="Output"
{10.1, 50.5, 30.3, 20.2, 40.4}
```