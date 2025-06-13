# 47. Hash Table
Learn about hash tables, a data structure for handling collections of elements that consist of pairs of unique keys and their corresponding values.

## 47.1 HashTable Overview
- `HashTable<Key, Value>` is equivalent to the C++ standard `std::unordered_map<Key, Value>` class
- It's a container for handling collections of elements that consist of pairs of unique keys and their corresponding values
- You can perform element addition, deletion, and search at high speed

### 47.1.1 Basic Characteristics
- Keys are unique. Elements with the same key cannot exist
- Implemented using hash tables
- The order of elements is not guaranteed (you cannot specify order or sort)
- The average computational complexity for element addition, deletion, and search is O(1) (nearly constant regardless of the number of elements), making it very fast

### 47.1.2 Main Operations

| Code | Description | Complexity |
| --- | --- | --- |
| `.emplace(key, value)` | Add an element (does nothing if it already exists) | O(1) |
| `[key]` | Return a reference to the value corresponding to the specified key. If it doesn't exist, add a new element | O(1) |
| `.erase(key)` | Delete the element with the specified key (does nothing if it doesn't exist) | O(1) |
| `.contains(key)` | Return whether an element with the specified key exists | O(1) |
| `.size()` | Return the number of elements | O(1) |
| `.empty()` | Return whether the number of elements is 0 | O(1) |
| `.clear()` | Delete all elements | O(N) |
| `.begin()`, `.end()` | Return iterators to the beginning and end positions | O(1) |


## 47.2 Creating Hash Tables
- `HashTable` can be created in the following ways:
	- Create an empty hash table
	- Create from an initializer list
	- Create an empty hash table and add elements
	
```cpp
# include <Siv3D.hpp>

void Main()
{
	// Create an empty hash table
	{
		HashTable<int32, String> ht;
		
		Print << ht.size();
		Print << ht;
	}

	Print << U"----";

	// Create from initializer list (list of key-value pairs)
	{
		HashTable<int32, String> ht = { { 3, U"three" }, { 1, U"one" }, { 4, U"four" }, { 1, U"one" }, { 5, U"five" } };
		
		Print << ht.size();
		Print << ht;
	}

	Print << U"----";

	// Create an empty hash table and add elements
	{
		HashTable<int32, String> ht;
		ht.emplace(3, U"three");
		ht.emplace(1, U"one");
		ht.emplace(4, U"four");
		ht.emplace(1, U"one"); // Does nothing because the key already exists
		ht.emplace(5, U"five");

		Print << ht.size();
		Print << ht;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Example Output"
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


## 47.3 Getting the Number of Elements
- `.size()` returns the number of elements in the hash table as a `size_t` type

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
```txt title="Output"
0
5
```


## 47.4 Checking if Empty
- `.empty()` returns whether the hash table is empty (has 0 elements) as a `bool` type

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
```txt title="Output"
true
false
```


## 47.5 Adding Elements (emplace)
- Use `.emplace(key, value)` to add elements
- If the same element already exists, it does nothing
- The return value is `std::pair<iterator, bool>`, and if the element addition succeeds (the same element didn't exist), `.second` becomes `true`

```cpp
#include <Siv3D.hpp>

void Main()
{
	HashTable<int32, String> ht = { { 3, U"three" }, { 1, U"one" }, { 4, U"four" } };

	if (ht.emplace(1, U"one").second)
	{
		Print << U"1 added";
	}
	else
	{
		Print << U"1 already exists";
	}

	if (ht.emplace(5, U"five").second)
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


## 47.6 Element Reference or Addition (`[]` operator)
- `table[key]` returns a reference to the value corresponding to the specified key, but if the specified key doesn't exist, it adds a new element
- When adding an element this way, the value corresponding to the key is default-initialized (`0` for `int32`, empty string for `String`, etc.)

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

		ht[U"Python"]; // Since the key doesn't exist, it's newly added and the value becomes 0

		Print << ht.size();
		Print << ht;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Example Output"
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

- You can update the value corresponding to an existing key by using the `[]` operator on it

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
```txt title="Example Output"
3
{
        {4:     four},
        {3:     THREE},
        {1:     ONE},
}
```


## 47.7 Checking for Element Existence (contains)
- Use `.contains(key)` to check if a specified element exists
- Returns `true` if it exists, `false` if it doesn't exist

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
```txt title="Output"
true
false
true
false
```


## 47.8 Checking for Element Existence (iterator)
- `.find(key)` searches for an element with the specified key and returns its iterator
- If not found, it returns `.end()`
- You can access the element `std::pair<const Key, Value>&` that the iterator points to using the dereference operator `*`

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
```txt title="Output"
three
1
```


## 47.9 Element Access with Range-based for Loop (const reference)
- Use a range-based for loop to access all elements in the hash table
- Access to each element is typically done by const reference
- Do not perform operations that change the size of the target hash table within the range-based for loop

```cpp
#include <Siv3D.hpp>

void Main()
{
	HashTable<int32, String> ht = { { 3, U"three" }, { 1, U"one" }, { 4, U"four" } };

	// Get key and value using structured binding
	for (auto&& [key, value] : ht)
	{
		Print << key << U": " << value;
	}

	Print << U"----";

	// Get key and value using pair
	for (const auto& elem : ht)
	{
		Print << elem.first << U": " << elem.second;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Example Output"
4: four
3: three
1: one
----
4: four
3: three
1: one
```


## 47.10 Element Access with Range-based for Loop (reference)
- When accessing all elements in a hash table using a range-based for loop, you can also access each element by reference
- In this case, you can only modify the "value" part of the key-value pair

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
```txt title="Example Output"
4: four!
3: three!
1: one!
```


## 47.11 Deleting Elements
- Use `.erase(key)` to delete an element with the specified key
- If an element with the specified key doesn't exist, it does nothing

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
```txt title="Example Output"
{
        {4:     four},
        {5:     five},
        {3:     three},
}
```


## 47.12 Deleting All Elements
- Use `.clear()` to delete all elements from the hash table

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
```txt title="Output"
{
}
```