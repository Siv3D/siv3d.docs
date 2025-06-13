# 33. String Class
Learn the basic usage of the `String` class.

## 33.1 String
- In Siv3D, strings are represented using the `String` type
- `String` is an array of `char32` type (characters) that represent UTF-32 code points
- UTF-32 character and string literals are prefixed with `U`, like `U'あ'` and `U"Hello"`
- `String` uses `std::u32string` internally to manage strings
- The following is guaranteed for the stored string data:
	- String data is contiguous in memory
	- There is a null character `U'\0'` immediately after the last element of the string
- It has more member functions than C++ standard library string classes and provides various convenient features

```cpp
String s1 = U"Siv3D";

String s2 = U"こんにちは、Siv3D!";
```


## 33.2 String Creation
- `String` is created using the following methods:
	- Create empty string
	- Create string from string literal
	- Create string with count × character

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		// Create empty string
		String s;
		Print << s;
	}

	{
		// Create string from string literal
		String s = U"Siv3D";
		Print << s;
	}

	{
		// Create string with count × character
		String s(5, U'A');
		Print << s;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
 
Siv3D
AAAAA
```


## 33.3 String Length
- `.size()` returns the length (number of elements) of the string as `size_t` type
- String length is the number of UTF-32 code points represented by `char32` type
- Most characters like alphabets, hiragana, and kanji are 1 character = 1 code point
- Some special characters like certain emoji may consist of multiple code points even though they appear as 1 character

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		String s = U"Siv3D";
		Print << s.size();
	}

	{
		String s = U"こんにちは";
		Print << s.size();
	}

	{
		String s = U"🐈";
		Print << s.size();
	}

	{
		// Some emoji consist of multiple code points
		String s = U"👩‍🎤";
		Print << s.size();
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
5
5
1
3
```


## 33.4 Checking if Empty (1)
- `.isEmpty()` returns whether the string is empty (has 0 elements) as `bool` type

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s1 = U"Siv3D";
	Print << s1.isEmpty();

	String s2;
	Print << s2.isEmpty();

	while (System::Update())
	{

	}
}
```
```txt title="Output"
false
true
```


## 33.5 Checking if Empty (2)
- Use `if (s)` to check if a string is empty
- If the string is empty, it evaluates to `false`

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s1 = U"Siv3D";
   
	if (s1)
	{
		Print << U"s1 is not empty";
	}

	String s2;

	if (not s2)
	{
		Print << U"s2 is empty";
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
s1 is not empty
s2 is empty
```


## 33.6 Adding Elements to End
- `s << ch;` adds element `ch` to the end of string `s`
- This is a shorthand for `.push_back(ch)`
- Adding to the end of a string is the most efficient compared to adding to other locations (beginning or middle)

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s;
	s << U'S';
	s << U'i';
	s << U'v';
	Print << s;

	s << U'3' << U'D';
	Print << s;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
Siv
Siv3D
```


## 33.7 Removing Last Element
- `.pop_back()` removes the last element of the string
- Must not be called when the number of elements is 0
	- Check that the number of elements is not 0 beforehand
- Removing the last element of a string is the most efficient compared to removing from other locations (beginning or middle)

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";
	Print << s;

	s.pop_back();
	Print << s;

	s.pop_back();
	Print << s;

	while (s)
	{
		s.pop_back();
	}

	Print << s.isEmpty();

	while (System::Update())
	{

	}
}
```
```txt title="Output"
Siv3D
Siv3
Siv
true
```


## 33.8 Removing All Elements
- `.clear()` removes all elements from the string, making it an empty string
- Can be called when the number of elements is 0 (does nothing)

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";
	Print << s;

	s.clear();
	Print << s.isEmpty();

	while (System::Update())
	{

	}
}
```
```txt title="Output"
Siv3D
true
```


## 33.9 Changing Number of Elements
- `.resize(n)` changes the number of elements in the string to `n`
- When the number of elements increases, new elements are initialized with null character U'\0', so they need to be overwritten with other characters

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";
	Print << s;

	s.resize(3);
	Print << s;

	s.resize(5);
	s[3] = U'!';
	s[4] = U'?';
	Print << s;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
Siv3D
Siv
Siv!?
```


## 33.10 Array Traversal with Range-for Loop (const reference)
- Use range-for loops to traverse array elements
- Element access is usually done with const references
- Do not perform operations that change the size of the target string within the range-for loop

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	for (const auto& ch : s)
	{
		Print << ch;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
S
i
v
3
D
```


## 33.11 Array Traversal with Range-for Loop (reference)
- Use range-for loops to traverse array elements
- When modifying elements within the loop, use references instead of const references to access elements
- Do not perform operations that change the size of the target string within the range-for loop

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	for (auto& ch : s)
	{
		++ch;
	}

	Print << s;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
Tjw4E
```



## 33.12 Accessing Elements at Specified Index
- `[i]` accesses the `i`-th element of the string
	- `i` is counted from 0. Valid indices are from 0 to `size() - 1`
- Must not access out of range

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	Print << s[0];
	Print << s[4];

	s[3] = U'4';
	Print << s;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
S
D
Siv4D
```


## 33.13 Accessing First and Last Elements
- `.front()` returns a reference to the first element
	- Same as `s[0]`
- `.back()` returns a reference to the last element
	- Same as `s[s.size() - 1]`
- Must not be called when the number of elements is 0

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	Print << s.front();
	Print << s.back();

	s.front() = U's';
	s.back() = U'd';
	Print << s;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
S
D
siv3d
```


## 33.14 Creating String by Concatenating Two Strings
- `s1 + s2` creates a new string by concatenating strings `s1` and `s2`
- `s1` and `s2` are not modified

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s1 = U"Hello, ";
	String s2 = U"Siv3D!";
	Print << (s1 + s2);
	Print << (s1 + s2 + U"!!!");

	while (System::Update())
	{

	}
}
```
```txt title="Output"
Hello, Siv3D!
Hello, Siv3D!!!!
```


## 33.15 Adding String to End
- `+=` adds a string to the end of the string

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Hello, ";
	s += U"Siv3D!";
	Print << s;

	s += U"!!!";
	Print << s;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
Hello, Siv3D!
Hello, Siv3D!!!!
```



## 33.16 Getting Iterators for Beginning and End Positions
- `.begin()` returns an iterator to the beginning position
- `.end()` returns an iterator to the end position

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	auto it = s.begin();

	Print << *it;

	++it;

	Print << *it;
	
	while (System::Update())
	{

	}
}
```
```txt title="Output"
S
i
```


## 33.17 Other Insert and Delete Operations
- `.push_front(value)` adds an element to the beginning
- `.pop_front()` removes the first element
- `.insert(iterator, value)` inserts an element at the specified iterator position
- `.erase(iterator)` removes the element at the specified iterator position
- `.erase(iterator1, iterator2)` removes elements in the specified range
- Inserting/deleting elements at the beginning or middle involves moving subsequent existing elements, so the cost is proportional to the number of elements after
	- Should normally be avoided or used only with small strings

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		String s = U"Siv3D";
		
		s.push_front(U'#');
		Print << s;

		s.pop_front();
		Print << s;
	}

	{
		String s = U"Siv3D";
		
		s.insert((s.begin() + 3), U'#');
		Print << s;
	}

	{
		String s = U"Siv3D";

		s.erase(s.begin() + 3);
		Print << s;

		s.erase(s.begin(), (s.begin() + 2));
		Print << s;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
#Siv3D
Siv3D
Siv#3D
SivD
vD
```


## 33.18 Checking if Contains Character or String
- `.contains(character)` returns whether the string contains the specified character as `bool` type
- `.contains(string)` returns whether the string contains the specified string as `bool` type

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Hello, Siv3D!";

	Print << s.contains(U'S');
	Print << s.contains(U'i');
	Print << s.contains(U'4');

	Print << s.contains(U"3D");
	Print << s.contains(U"Hello");
	Print << s.contains(U"Hi");

	while (System::Update())
	{

	}
}
```
```txt title="Output"
true
true
false
true
true
false
```


## 33.19 Checking if Starts With or Ends With Character or String
- `.starts_with(character)` returns whether the string starts with the specified character as `bool` type
- `.starts_with(string)` returns whether the string starts with the specified string as `bool` type
- `.ends_with(character)` returns whether the string ends with the specified character as `bool` type
- `.ends_with(string)` returns whether the string ends with the specified string as `bool` type

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Hello, Siv3D!";

	Print << s.starts_with(U'H');
	Print << s.starts_with(U'S');

	Print << s.starts_with(U"Hello");
	Print << s.starts_with(U"Hi");

	Print << s.ends_with(U'!');
	Print << s.ends_with(U'D');

	Print << s.ends_with(U"3D!");
	Print << s.ends_with(U"Hi");

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
true
false
true
false
```


## 33.20 Creating Substrings
- `.substr(start_position, length)` creates a substring of `length` characters starting from `start_position` in the string as a new `String`
	- `start_position` starts from 0
	- If `length` is omitted, a substring from the `start_position` character to the end is created
	- If `length` is larger than the actual string length, a substring exactly to the end is created

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"Hello, Siv3D!";

	Print << s.substr(0, 5);
	Print << s.substr(7, 3);
	Print << s.substr(7);
	Print << s.substr(0, 100);

	while (System::Update())
	{

	}
}
```
```txt title="Output"
Hello
Siv
Siv3D!
Hello, Siv3D!
```

- This can be applied to programs that display strings over time as follows:

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"Hello, Siv3D!";

	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		ClearPrint();

		const int32 count = (stopwatch.ms() / 300);

		Print << s.substr(0, count);
	}
}
```


## 33.21 Converting Alphabets to Lowercase/Uppercase
- `.lowercased()` creates a new string with alphabets converted to lowercase
- `.uppercased()` creates a new string with alphabets converted to uppercase

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"こんにちは、Siv3D!";

	// Create new string with lowercase
	Print << s.lowercased();

	// Create new string with uppercase
	Print << s.uppercased();

	while (System::Update())
	{

	}
}
```
```txt title="Output"
こんにちは、siv3d!
こんにちは、SIV3D!
```


## 33.22 Reversing String
- `.reversed()` creates a new string with the string reversed
	- The original string is not modified
- `.reverse()` reverses the string
	- The original string is modified 

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s1 = U"Hello, Siv3D!";

	// Create new string with reversed order
	Print << s1.reversed();

	String s2 = U"Hello, Siv3D!";

	// Reverse the string
	s2.reverse();
	Print << s2;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
!D3viS ,olleH
!D3viS ,olleH
```


## 33.23 Shuffling String Elements
- `.shuffled()` creates a new string with the string elements shuffled
	- The original string is not modified
- `.shuffle()` shuffles the string elements
	- The original string is modified

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s1 = U"Hello, Siv3D!";
	
	// Create new string with shuffled elements
	Print << s1.shuffled();

	String s2 = U"Hello, Siv3D!";

	// Shuffle string elements
	s2.shuffle();
	Print << s2;

	while (System::Update())
	{

	}
}
```
```txt title="Example Output"
vel SDH!,loi3
3 lo!vl,SHDie
```


## 33.24 Character and String Replacement
- `.replaced(from, to)` creates a new string with `from` replaced by `to` in the string
	- The original string is not modified
- `.replace(from, to)` replaces `from` with `to` in the string
	- The original string is modified

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Hello, Siv3D!";
	
	// Create new string with Siv3D replaced by C++
	Print << s.replaced(U"Siv3D", U"C++");
	
	// Replace ! with ?
	s.replace(U'!', U'?');
	Print << s;

	// Create new string with Hello replaced by Hi
	Print << s.replaced(U"Hello", U"Hi");

	while (System::Update())
	{

	}
}
```
```txt title="Output"
Hello, C++!
Hello, Siv3D?
Hi, Siv3D?
```


## 33.25 Removing Leading and Trailing Whitespace
- `.trimmed()` creates a new string with whitespace characters (spaces, tabs, newlines, etc.) removed from the beginning and end of the string
	- The original string is not modified
- `.trim()` removes whitespace characters from the beginning and end of the string
	- The original string is modified

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s1 = U" Hello, Siv3D!   ";

	// Remove leading and trailing whitespace
	s1.trim();
	Print << s1;
	Print << s1.size();

	const String s2 = U"\n\n Siv3D  \n\n\n";

	// Create new string with leading and trailing whitespace removed
	Print << s2.trimmed();

	while (System::Update())
	{

	}
}
```
```txt title="Output"
Hello, Siv3D!
13
Siv3D
```


## 33.26 Splitting by Specified Character
- `.split(delimiter)` returns the result of splitting the string by `delimiter` as `Array<String>`
	- `delimiter` is a single character string
	- If `delimiter` appears consecutively, empty strings are generated
	- If `delimiter` appears at the beginning or end of the string, empty strings are generated
	- If `delimiter` is not contained in the string, the original string is returned as is

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		const String s = U"red,green,blue";
		
		const Array<String> values = s.split(U',');
		Print << values;
	}

	{
		const String s = U",,a,";

		const Array<String> values = s.split(U',');
		Print << values;
	}

	{
		const String s = U"1, 2, 3, 4, 5";

		// Example converting number strings separated by U',' to Array<int32>
		const Array<int32> values = s.split(U',').map(Parse<int32>);
		Print << values;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
{red, green, blue}
{, , a, }
{1, 2, 3, 4, 5}
```


## 33.27 Converting to Other String Types
- `String` has the following member functions to convert to other string types:

| Code | Description |
|---|---|
| `.narrow()` | Convert to `std::string` (character encoding is environment-dependent) |
| `.toUTF8()` | Convert to `std::string` (UTF-8) |
| `.toWstr()` | Convert to `std::wstring` |
| `.toUTF16()` | Convert to `std::u16string` |
| `.toUTF32()` | Convert to `std::u32string` |

??? example "Environment-dependent character encoding"
	- The character encoding of `std::string` varies by environment
	- Japanese Windows uses Shift_JIS (CP932), macOS and Linux use UTF-8
	- From Siv3D v0.8 onwards, `std::string` character encoding will be unified to UTF-8

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"こんにちは、Siv3D!";

	const std::string s1 = s.narrow();
	const std::string s2 = s.toUTF8();
	const std::wstring s3 = s.toWstr();
	const std::u16string s4 = s.toUTF16();
	const std::u32string s5 = s.toUTF32();

	while (System::Update())
	{

	}
}
```

- To pass a `String` string to a function that takes `const char*`, get the head pointer of the `std::string` obtained with `.narrow()` using `c_str()`:

```cpp
f(s.narrow().c_str());
```


## 33.28 Converting from Other String Types
- The following functions convert from other string types to `String`:

| Code | Description |
|---|---|
| `Unicode::Widen(s)` | Convert from `std::string` (character encoding is environment-dependent) to `String` |
| `Unicode::WidenAscii(s)` | Convert from `std::string` (ASCII) to `String` |
| `Unicode::FromWstring(s)` | Convert from `std::wstring` to `String` |
| `Unicode::FromUTF8(s)` | Convert from `std::string` (UTF-8) to `String` |
| `Unicode::FromUTF16(s)` | Convert from `std::u16string` to `String` |
| `Unicode::FromUTF32(s)` | Convert from `std::u32string` to `String` |

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s1 = Unicode::Widen("こんにちは、Siv3D!");
	const String s2 = Unicode::WidenAscii("Hello, Siv3D!");
	const String s3 = Unicode::FromWstring(L"こんにちは、Siv3D!");
	const String s4 = Unicode::FromUTF8("こんにちは、Siv3D!");
	const String s5 = Unicode::FromUTF16(u"こんにちは、Siv3D!");
	const String s6 = Unicode::FromUTF32(U"こんにちは、Siv3D!");

	while (System::Update())
	{

	}
}
```


## 33.29 FilePath
- To clarify that a string refers to a file path, `FilePath` is provided as an alias for `String`

```cpp
# include <Siv3D.hpp>

void Main()
{
	FilePath path = U"example/windmill.png";

	Print << path;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
example/windmill.png
```