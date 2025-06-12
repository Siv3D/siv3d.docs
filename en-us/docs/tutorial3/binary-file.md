# 56. Binary Files
Learn how to read and write data in binary format.

## 56.1 Binary Files

### 56.1.1 Text Files vs Binary Files
- **Binary files** are files that store data in binary data format that computers can directly process
- For example, when saving the `int32` value `299792458` to a file:
	- Text format saves it as the ASCII string "299792458" in 9 bytes
	- Binary format saves it as a 32-bit integer value in 4 bytes (`00010001110111100111100001001010`)

#### Text Files
- Advantages
	- Easy for humans to read and write
	- Can check and edit contents with text editors
- Disadvantages
	- Overhead from number ⇔ text conversion processing
	- Larger file sizes

#### Binary Files
- Advantages
	- Fast reading and writing by programs
	- Minimal file sizes
- Disadvantages
	- Difficult for humans to directly check contents

### 56.1.2 Using Serialization
- Built-in types (`int32`, `double`, etc.) and simple classes composed of built-in types (`Point`, `Vec2`, `Rect`, `ColorF`, etc.) can be directly written to files as byte sequences in memory (*trivially copyable*)
- On the other hand, complex data structures (`String`, `Array`, etc.) contain pointers and memory management, so they cannot be directly converted to binary. They need to be converted to appropriate binary data using the **serialization feature** explained in **56.7-56.9**
- Custom classes can also be made compatible with binary file input/output using the serialization feature


## 56.2 Writing Binary Data
- Use the `BinaryWriter` class to create binary files
- Pass the path of the destination file as a constructor argument to `BinaryWriter`
- File paths use relative paths from the folder where the executable is located (the App folder during development) or absolute paths
- You can check if the file opened successfully with `if (writer)` or `if (not writer)`
- If a file with the same name already exists, it will be discarded and a new file will be created
- Opened files are closed by the destructor, so explicit closing is not necessary
- If you want to control the timing of opening and closing, use `.open()` to open files and `.close()` to close files, just like `TextWriter` (**Tutorial 54**)
- Passing a *trivially copyable* type value to `.write()` writes the binary data of that value to the end of the file
- Running the following sample code will create a 36-byte binary file

```cpp
# include <Siv3D.hpp>

// Struct to record game scores
struct GameScore
{
	int32 a, b, c, d;
};

void Main()
{
	// Open binary file for writing
	BinaryWriter writer{ U"tutorial1.bin" };

	if (not writer) // If opening failed
	{
		throw Error{ U"Failed to open `tutorial1.bin`" };
	}

	// Write int32 value (4 bytes)
	writer.write(777);

	// Write double value (8 bytes)
	writer.write(3.1415);

	// Write Point value (8 bytes)
	writer.write(Point{ 123, 456 });

	// Write GameScore value (16 bytes)
	const GameScore s = { 10, 20, 30, 40 };
	writer.write(s);

	while (System::Update())
	{

	}

	// File is automatically closed by writer's destructor
}
```


## 56.2 Reading Binary Data
- Use the `BinaryReader` class to read binary data from binary files
- Pass the path of the text file you want to read as a constructor argument to `BinaryReader`
- File paths use relative paths from the folder where the executable is located (the App folder during development) or absolute paths
- You can check if the file opened successfully with `if (reader)` or `if (not reader)`
- Immediately after opening, the read position is set to the beginning of the file
- Passing a *trivially copyable* type variable by reference to `.read()` reads binary data of that value's size starting from the read position, copies it to that variable, advances the read position accordingly, and returns `true`
- When the read position is already at the end of the file and cannot read any more from the file, it returns `false`
- The following sample code reads the values stored in the binary file created in **56.1** in order

```cpp
# include <Siv3D.hpp>

// Struct to record game scores
struct GameScore
{
	int32 a, b, c, d;
};

void Main()
{
	BinaryReader reader{ U"tutorial1.bin" };

	// If opening failed
	if (not reader)
	{
		throw Error{ U"Failed to open `tutorial1.bin`" };
	}

	// Read int32 value (4 bytes)
	int32 n;
	reader.read(n);
	Print << n;

	// Read double value (8 bytes)
	double d;
	reader.read(d);
	Print << d;

	// Read Point value (8 bytes)
	Point pos;
	reader.read(pos);
	Print << pos;

	// Read GameScore value (16 bytes)
	GameScore s;
	reader.read(s);
	Print << U"{}, {}, {}, {}"_fmt(s.a, s.b, s.c, s.d);

	while (System::Update())
	{

	}

	// File is automatically closed by reader's destructor
}
```
```txt title="Output"
777
3.1415
(123, 456)
10, 20, 30, 40
```


## 56.3 Changing Read Position
- You can move the read position to a specified location with `BinaryReader`'s `.setPos(pos)`
- Using `.skip(size)` allows you to skip forward by the specified size
- The following sample reads only some of the values stored in the binary file created in **56.1**

```cpp
# include <Siv3D.hpp>

// Struct to record game scores
struct GameScore
{
	int32 a, b, c, d;
};

void Main()
{
	BinaryReader reader{ U"tutorial1.bin" };

	// If opening failed
	if (not reader)
	{
		throw Error{ U"Failed to open `tutorial1.bin`" };
	}

	// Move to position 4 bytes from the beginning
	reader.setPos(4);

	// Read double value (8 bytes)
	double d;
	reader.read(d);
	Print << d;

	// Skip 8 bytes
	reader.skip(8);

	// Read GameScore value (16 bytes)
	GameScore s;
	reader.read(s);
	Print << U"{}, {}, {}, {}"_fmt(s.a, s.b, s.c, s.d);

	while (System::Update())
	{

	}

	// File is automatically closed by reader's destructor
}
```
```txt title="Output"
3.1415
10, 20, 30, 40
```


## 56.4 Writing Complex Data (Without Using Serialization)
- This is an example of writing data that cannot be simply copied (not `trivially copyable`) like `String` and `Array` to binary files without using the serialization feature
- `.write(data start pointer, data size)` writes data of the specified size from the data start pointer

```cpp
# include <Siv3D.hpp>

void Main()
{
	BinaryWriter writer{ U"tutorial2.bin" };

	// If opening failed
	if (not writer)
	{
		throw Error{ U"Failed to open `tutorial2.bin`" };
	}

	// String to write
	const String text = U"Hello, Siv3D";

	// Length of string to write
	const uint64 length = text.length();

	// Write string length (8 bytes)
	writer.write(length);

	// Write string data for (4 bytes × length) from the start pointer of stored data
	writer.write(text.data(), (sizeof(char32) * length));

	while (System::Update())
	{

	}

	// File is automatically closed by writer's destructor
}
```


## 56.5 Reading Complex Data (Without Using Serialization)
- This is an example of reading data that cannot be simply copied (not `trivially copyable`) like `String` and `Array` from binary files without using the serialization feature
- `.read(destination start pointer, data size)` reads data of the specified size from the file and copies it to the specified destination
- The following sample code reads the `String` stored in the binary file created in **56.4**

```cpp
# include <Siv3D.hpp>

void Main()
{
	BinaryReader reader{ U"tutorial2.bin" };

	// If opening failed
	if (not reader)
	{
		throw Error{ U"Failed to open `tutorial2.bin`" };
	}

	// Variable to store the length of text to read
	uint64 length = 0;

	// Destination for read text
	String text;

	// Read text length
	reader.read(length);

	if (0 < length)
	{
		// Resize to store text data
		text.resize(length);

		// Read data for the size of the text
		reader.read(text.data(), (sizeof(char32) * length));
	}

	Print << U"length: " << length;;
	Print << U"text: " << text;

	while (System::Update())
	{

	}

	// File is automatically closed by reader's destructor
}
```


## 56.6 Writing Complex Data (Serialization)
- Using the serialization feature allows easy conversion to binary format and restoration from binary format for types that support serialization (including types that are not `trivially copyable`)
- To link `BinaryWriter` with the serialization feature, use the `Serializer<BinaryWriter>` class
- Pass the path of the destination file as a constructor argument to `Serializer<BinaryWriter>`
- You can check if the file opened successfully with `if (writer)` or `if (not writer)`
- Passing a value of a type that supports serialization to `Serializer<BinaryWriter>` converts that data to binary data and writes it to the end of the file
- Passing a type that doesn't support serialization will result in a compile error

```cpp
# include <Siv3D.hpp>

void Main()
{
	Serializer<BinaryWriter> writer{ U"tutorial3.bin" };

	// If opening failed
	if (not writer) 
	{
		throw Error{ U"Failed to open `tutorial3.bin`" };
	}

	// String to write
	const String text = U"Hello, Siv3D";

	// Data to write
	int a = 123, b = 456;

	// Write serialization-compatible type data to binary file
	writer(text);

	// Can also be written together
	writer(a, b);

	while (System::Update())
	{

	}

	// File is automatically closed by writer's destructor
}
```


## 56.7 Reading Complex Data (Serialization)
- To read data written using the serialization feature, use the functionality of the `Deserializer<BinaryReader>` class
- Pass the path of the text file you want to read as a constructor argument to `Deserializer<BinaryReader>`
- You can check if the file opened successfully with `if (reader)` or `if (not reader)`
- Passing variable references with `()` to `Deserializer<BinaryReader>` deserializes data from the file and stores the result in those variables
- Passing a type that doesn't support serialization will result in a compile error
- The following sample code reads the `String` and `int32` stored in the binary file created in **56.6**

```cpp
# include <Siv3D.hpp>

void Main()
{
	Deserializer<BinaryReader> reader{ U"tutorial3.bin" };

	// If opening failed
	if (not reader)
	{
		throw Error{ U"Failed to open `tutorial3.bin`" };
	}

	// Destination String for reading
	String text;

	// Destination variables for reading
	int32 a, b;

	// Read serialization-compatible type data from binary file
	// (Strings and Arrays are automatically resized)
	reader(text);
	reader(a, b);

	Print << U"length: " << text.length();
	Print << U"text: " << text;
	Print << a << U", " << b;

	while (System::Update())
	{

	}

	// File is automatically closed by reader's destructor
}
```
```txt title="Output"
length: 12
text: Hello, Siv3D
123, 456
```


## 56.8 Making Custom Classes Serialization-Compatible
- To make custom classes serialization-compatible, implement a public member function `template <class Archive> void SIV3D_SERIALIZE(Archive& archive)` in the class
- In that function, write code that passes serialization-compatible member variables sequentially like `archive(a, b, c, ...)`, making the class itself serializable and usable with `Serializer` and `Deserializer`

```cpp
# include <Siv3D.hpp>

// Struct to record user data and game scores
struct GameScore
{
	String name;

	int32 id;

	int32 score;

	// Member function to support serialization
	template <class Archive>
	void SIV3D_SERIALIZE(Archive& archive)
	{
		archive(name, id, score);
	}
};

void Main()
{
	{
		// Data to record
		const Array<GameScore> scores =
		{
			{ U"Player1", 111, 1000 },
			{ U"Player2", 222, 2000 },
			{ U"Player3", 333, 3000 },
		};

		Serializer<BinaryWriter> writer{ U"tutorial4.bin" };

		if (not writer)
		{
			throw Error{ U"Failed to open `tutorial4.bin`" };
		}

		// Write serialization-compatible data (array) to binary file
		writer(scores);

		// File is automatically closed by writer's destructor
	}

	// Destination for reading
	Array<GameScore> scores;
	{
		Deserializer<BinaryReader> reader{ U"tutorial4.bin" };

		if (not reader)
		{
			throw Error{ U"Failed to open `tutorial4.bin`" };
		}

		// Read serialization-compatible data from binary file
		// (Array is automatically resized)
		reader(scores);

		// File is automatically closed by reader's destructor
	}

	// Confirm correct reading
	for (const auto& score : scores)
	{
		Print << U"{}(id: {}): {}"_fmt(score.name, score.id, score.score);
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
Player1(id: 111): 1000
Player2(id: 222): 2000
Player3(id: 333): 3000
```