# 46. Hash Set
Learn about hash sets, a data structure for handling collections without duplicates.

## 46.1 HashSet Overview
- `HashSet<Key>` is equivalent to the C++ standard `std::unordered_set<Key>` class
- It's a container for handling collections where elements don't duplicate
- You can perform element addition, deletion, and search at high speed

### 46.1.1 Basic Characteristics
- Elements are unique. The same element cannot appear multiple times
- Implemented using hash tables
- The order of elements is not guaranteed (you cannot specify order or sort)
- The average computational complexity for element addition, deletion, and search is O(1) (nearly constant regardless of the number of elements), making it very fast

### 46.1.2 Main Operations

| Code | Description | Complexity |
| --- | --- | --- |
| `.insert(key)` | Add an element (does nothing if it already exists) | O(1) |
| `.erase(key)` | Delete the specified element (does nothing if it doesn't exist) | O(1) |
| `.contains(key)` | Return whether the specified element exists | O(1) |
| `.size()` | Return the number of elements | O(1) |
| `.empty()` | Return whether the number of elements is 0 | O(1) |
| `.clear()` | Delete all elements | O(N) |
| `.begin()`, `.end()` | Return iterators to the beginning and end positions | O(1) |


## 46.2 Creating Hash Sets
- `HashSet` can be created in the following ways:
	- Create an empty hash set
	- Create from an initializer list
	- Create from another container
	- Create an empty hash set and add elements
	
```cpp
# include <Siv3D.hpp>

void Main()
{
	// Create an empty hash set
	{
		HashSet<int32> hs;
		Print << hs.size();
		Print << hs;
	}

	// Create from initializer list
	{
		HashSet<int32> hs = { 3, 1, 4, 1, 5 };
		Print << hs.size();
		Print << hs;
	}

	// Create from another container
	{
		const Array<int32> a = { 3, 1, 4, 1, 5 };
		HashSet<int32> hs(a.begin(), a.end());
		Print << hs.size();
		Print << hs;
	}

	// Create an empty hash set and add elements
	{
		HashSet<int32> hs;
		hs.insert(3);
		hs.insert(1);
		hs.insert(4);
		hs.insert(1); // Ignored because it's a duplicate
		hs.insert(5);

		Print << hs.size();
		Print << hs;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
0
{}
4
{4, 1, 5, 3}
4
{4, 1, 5, 3}
4
{4, 1, 5, 3}
```


## 46.3 Getting the Number of Elements
- `.size()` returns the number of elements in the hash set as a `size_t` type

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
```txt title="Output"
0
3
```


## 46.4 Checking if Empty
- `.empty()` returns whether the hash set is empty (has 0 elements) as a `bool` type

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
```txt title="Output"
true
false
```


## 46.5 Adding Elements
- Use `.insert(key)` to add elements
- If the same element already exists, it does nothing
- The return value is `std::pair<iterator, bool>`, and if the element addition succeeds (the same element didn't exist), `.second` becomes `true`

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
```txt title="Output"
1 already exists
5 added
```


## 46.6 Checking for Element Existence
- Use `.contains(key)` to check if a specified element exists
- Returns `true` if it exists, `false` if it doesn't exist

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
```txt title="Output"
true
false
true
false
```


## 46.7 Enumerating Elements with Range-based for Loop
- Use a range-based for loop to access all elements in the hash set
- Elements are stored in an order that's convenient for the computer, so the order is not guaranteed
- Access to each element is done by const reference
- Do not perform operations that change the size of the target hash set within the range-based for loop

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
```txt title="Example Output"
C++
Python
C
Java
```


## 46.8 Deleting Elements
- Use `.erase(key)` to delete a specified element
- If the specified element doesn't exist, it does nothing

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
```txt title="Output"
{4, 5, 3}
```


## 46.9 Deleting All Elements
- Use `.clear()` to delete all elements from the hash set

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashSet<int32> hs = { 3, 1, 4, 1, 5 };

	hs.clear();

	Print << hs;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
{}
```