# 7. Basic Types and Classes
Learn about the basic types and classes used in Siv3D programs.

- Frequently used important types are marked with ★

## 7.1 Integers
- When handling integers, use type names with explicit sizes like `int32`, `uint64`, etc.
- While `int`, `long`, etc. can also be used, they should be avoided as their sizes vary by environment and have poor portability
- Array element counts are represented by the `size_t` type, same as the C++ standard

| Type Name | Size | Description | Value Range |
| --- | --- | --- | --- |
| `int8` | 1 byte | Signed 8-bit integer | -128 to 127 |
| `uint8` | 1 byte | Unsigned 8-bit integer | 0 to 255 |
| `int16` | 2 bytes | Signed 16-bit integer | -32,768 to 32,767 |
| `uint16` | 2 bytes | Unsigned 16-bit integer | 0 to 65,535 |
| `int32` ★ | 4 bytes | Signed 32-bit integer | -2,147,483,648 to 2,147,483,647 |
| `uint32` | 4 bytes | Unsigned 32-bit integer | 0 to 4,294,967,295 |
| `int64` | 8 bytes | Signed 64-bit integer | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |
| `uint64` | 8 bytes | Unsigned 64-bit integer | 0 to 18,446,744,073,709,551,615 |
| `size_t` ★ | 8 bytes | Unsigned 64-bit integer | 0 to 18,446,744,073,709,551,615 |

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 a = 123;
	size_t b = 100;

	Print << U"a: " << a;
	Print << U"b: " << b;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
a: 123
b: 100
```


## 7.2 Floating Point Numbers
- When handling decimal numbers, use the C++ standard floating point types `float` and `double`

| Type Name | Size | Description | Value Range | Precision |
| --- | --- | --- | --- | --- |
| `float` | 4 bytes | Single precision floating point | 3.4E +/- 38 | 7 digits |
| `double` ★ | 8 bytes | Double precision floating point | 1.7E +/- 308 | 15 digits |


```cpp
# include <Siv3D.hpp>

void Main()
{
	double a = 123.456;
	float b = 100.5f;

	Print << U"a: " << a;
	Print << U"b: " << b;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
a: 123.456
b: 100.5
```

!!! info "Limited use of float type for developers in Siv3D"
	- In game development where computational resources need to be conserved, `float` type is usually used for floating point processing
	- On the other hand, most of Siv3D's APIs use `double` type as standard
		- This is because it's also intended for use in simulations and scientific computing where precision is required
	- Within the Siv3D engine, balance is achieved by using `float` type for internal processing (such as rendering) where speed is more important than precision
	- Some APIs used by developers also feature `float` type, such as shader constant buffers, matrices, quaternions, and FFT results
	- It's good practice to use `double` type normally and use `float` type only when necessary


## 7.3 Boolean Values
- When representing binary states like Yes/No in programs, use the C++ standard boolean `bool` type instead of integer types
- `bool` type values can only be `true` or `false`
- `true` represents true, `false` represents false

| Type Name | Size | Description | Value Range |
| --- | --- | --- | --- |
| `bool` ★ | 1 byte | Boolean value | `true` or `false` |

```cpp
# include <Siv3D.hpp>

void Main()
{
	bool a = true;
	bool b = false;

	Print << U"a: " << a;
	Print << U"b: " << b;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
a: true
b: false
```


## 7.4 Characters
- When handling characters, use UTF-32 character literals and the `char32` type which represents characters in UTF-32 format

| Type Name | Size | Description | Value Range |
| --- | --- | --- | --- |
| `char32` ★ | 4 bytes | UTF-32 encoded character | 0 to 0x10FFFF |

- While the `char` type cannot represent the hiragana "あ" in 1 element, the `char32` type can conveniently represent it in 1 element

```cpp
char a = 'あ'; // NG
char32 b = U'あ'; // OK
```

- Character literals of `char32` type are prefixed with `U` before the single quotation marks

```cpp
# include <Siv3D.hpp>

void Main()
{
	char32 c1 = U'A';
	char32 c2 = U'あ';

	Print << U"c1: " << c1;
	Print << U"c2: " << c2;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
c1: A
c2: あ
```


## 7.5 Strings
- When handling strings, use UTF-32 string literals and the `String` class
	- The `String` class is for handling UTF-32 strings, roughly speaking it's the `char32` version of `std::string`
	- This will be explained in detail in **Tutorial 33**
- There's also a `StringView` class equivalent to `std::string_view`
- When strings represent file paths, using the respective type aliases `FilePath` and `FilePathView` improves code readability

| Type Name | Description |
| --- | --- |
| `String` ★ | UTF-32 encoded string |
| `StringView` | UTF-32 encoded string view |
| `FilePath` | File path string (alias for `String`) |
| `FilePathView` | File path string view (alias for `StringView`) |

- UTF-32 string literals are prefixed with `U` before the double quotation marks

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s1 = U"Hello!";
	String s2 = U"こんにちは！";
	FilePath s3 = U"example/windmill.png";

	Print << U"s1: " << s1;
	Print << U"s2: " << s2;
	Print << U"s3: " << s3;
	Print << U"Siv3D!";

	while (System::Update())
	{

	}
}
```
```txt title="Output"
s1: Hello!
s2: こんにちは！
s3: example/windmill.png
Siv3D!
```


## 7.6 Arrays
- For fixed-length arrays, use the C++ standard library's `std::array<Type, N>`
	- Type is the element type, N is the number of elements
- For dynamic arrays, use the `Array<Type>` class
	- Type is the element type
	- This will be explained in detail in **Tutorial 22**
- Dynamic two-dimensional arrays can be represented with the `Grid<Type>` class
	- Type is the element type
	- This will be explained in detail in **Tutorial 37**

| Type Name | Description |
| --- | --- |
| `std::array<Type, N>` | Fixed-length array |
| `Array<Type>` ★ | Dynamic array (equivalent to C++ standard `std::vector`) |
| `Grid<Type>` | Dynamic two-dimensional array |


```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> a = { 1, 2, 3, 4, 5 };
	Grid<int32> b(4, 3, 0);

	Print << U"a: " << a;
	Print << U"b:\n" << b;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
a: {1, 2, 3, 4, 5}
b:
{{0, 0, 0, 0},
{0, 0, 0, 0},
{0, 0, 0, 0}}
```


## 7.7 Other Data Types
- There's an `Optional<Type>` class that adds invalid value representation to any type
	- Type is the element type
	- This will be explained in detail in **Tutorial 33**
- For hash table-based Sets (containers that handle collections of non-duplicate elements), use the `HashSet<Type>` class
	- Type is the element type
	- This will be explained in detail in **Tutorial 46**
- For hash table-based Maps (containers that handle collections of key-value pairs with non-duplicate keys), use the `HashTable<Key, Value>` class
	- Key is the key type, Value is the value type
	- This will be explained in detail in **Tutorial 47**

| Type Name | Description |
| --- | --- |
| `Optional<Type>` | Class that adds invalid value representation to any type (equivalent to C++ standard `std::optional`) |
| `HashSet<Type>` | Hash table-based Set (equivalent to C++ standard `std::unordered_set`) |
| `HashTable<Key, Value>` | Hash table-based Map (equivalent to C++ standard `std::unordered_map`) |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Optional<int32> a = 42;
	Optional<int32> b = none;
	HashSet<int32> c = { 1, 2, 3, 4, 5 };
	HashTable<int32, String> d = { { 1, U"one" }, { 2, U"two" }, { 3, U"three" } };

	Print << U"a: " << a;
	Print << U"b: " << b;
	Print << U"c: " << c;
	Print << U"d:\n" << d;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
a: (Optional)42
b: none
c: {4, 1, 5, 2, 3}
d:
{
	{2:	two},
	{1:	one},
	{3:	three},
}
```


## Review Checklist
- [x] Learned commonly used integer types `int32` and `size_t`
- [x] Learned commonly used floating point type `double`
- [x] Learned commonly used boolean type `bool`
- [x] Learned to represent characters with `char32` type
- [x] Learned to handle strings with `String`
- [x] Learned to handle dynamic arrays with `Array<Type>`