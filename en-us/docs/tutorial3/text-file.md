# 54. Text Files
Learn how to read the contents of text files and write strings to text files.

!!! info "Use dedicated classes for configuration files"
	- When handling configuration files like JSON, INI, CSV, XML, TOML, it's convenient to use the dedicated classes learned in **Tutorial 55**

## 54.1 Opening Text Files
- You can open text files for read-only access with `TextReader variable_name{ U"file_path" };`
- File paths use relative paths based on the folder containing the executable file (the `App` folder during development) or absolute paths
- The text file encodings supported by Siv3D are:
	- UTF-8
	- UTF-8 (with BOM)
	- UTF-16LE
	- UTF-16BE
- To check if a file was opened successfully, use `if (reader.isOpen())`, `if (reader)`, or `if (not reader)`
- Opened files are closed by the destructor, so explicit closing is not necessary

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Open file
	TextReader reader{ U"example/txt/en.txt" };

	// If file couldn't be opened
	if (not reader)
	{
		// Throw exception and exit
		throw Error{ U"Failed to open `en.txt`" };
	}

	while (System::Update())
	{

	}

	// File is automatically closed by reader's destructor
}
```


## 54.2 Reading Line by Line
- Immediately after opening a text file, the read position is set to the beginning of the file
- Passing a `String` type variable by reference to `.readLine()` reads from the read position to the next newline or end of file (whichever comes first), stores the content in that variable, advances the read position to just after that, and returns `true`
- The read string does not include newline characters
- When the read position is already at the end of the file and cannot read further, it stores an empty string and returns `false`

### Example
- Consider reading the following text file:
	- 1st `.readLine()` reads `abc`
	- 2nd `.readLine()` reads `defg`
	- 3rd `.readLine()` reads an empty string
	- 4th `.readLine()` reads `hijklmn`
	- Subsequent `.readLine()` calls result in empty strings and return `false`

```txt
abc
defg

hijklmn
```

| `.readLine()` call | `.readLine()` return value | `String` variable content |
| --- | --- | --- |
| 1st | `true` | `abc` |
| 2nd | `true` | `defg` |
| 3rd | `true` | empty string |
| 4th | `true` | `hijklmn` |
| 5th and later | `false` | empty string |

- `.readLine()` is convenient when combined with a `while` loop as in the following sample code

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Open file
	TextReader reader{ U"example/txt/en.txt" };

	// If file couldn't be opened
	if (not reader)
	{
		// Throw exception and exit
		throw Error{ U"Failed to open `en.txt`" };
	}

	// Variable to read line content
	String line;

	// Line count
	size_t count = 0;

	// Read line by line until end
	while (reader.readLine(line))
	{
		Print << ++count;
		Print << line;
	}

	while (System::Update())
	{

	}
}
```


## 54.3 Reading All Content
- To get all content of a text file as a `String`, use `.readAll()`
- However, using `.readAll()` on very large files (several MB or more) can take a long time for the function to return control, so it's recommended to use it on files known to be small in advance

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Open file
	TextReader reader{ U"example/txt/en.txt" };

	// If file couldn't be opened
	if (not reader)
	{
		// Throw exception and exit
		throw Error{ U"Failed to open `en.txt`" };
	}

	// Read all text file content
	const String text = reader.readAll();

	Print << text;

	while (System::Update())
	{

	}
}
```


## 54.4 Controlling Open/Close Timing
- Instead of opening files in the constructor, you can open files with `.open()`
- Returns `true` on success, `false` on failure

```cpp
# include <Siv3D.hpp>

void Main()
{
	TextReader reader;

	// Open en.txt
	if (not reader.open(U"example/txt/en.txt"))
	{
		throw Error{ U"Failed to open `en.txt`" };
	}

	while (System::Update())
	{

	}

	// File is automatically closed by reader's destructor
}
```

- If a file is already open, `.open()` closes the current file and then opens the specified file

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Open en.txt
	TextReader reader{ U"example/txt/en.txt" };

	// Close en.txt and open jp.txt
	if (not reader.open(U"example/txt/jp.txt"))
	{
		throw Error{ U"Failed to open `jp.txt`" };
	}

	while (System::Update())
	{

	}

	// File is automatically closed by reader's destructor
}
```

- File closing is normally done by the `TextReader` destructor, but if you need to immediately close a file after reading its contents to delete the text file or write different content to it, you need to close the file immediately since operations cannot be performed while the file is open
- In such cases, explicitly close the file with `.close()`
- If a file is already closed, subsequent `.close()` calls or destructor do nothing

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Open file
	TextReader reader{ U"example/txt/en.txt" };

	Print << reader.isOpen();

	// Explicitly close file
	reader.close();

	Print << reader.isOpen();

	while (System::Update())
	{

	}
}
```


## 54.5 Reading Numeric Data
- By combining `.readLine()` with the parse functions learned in **Tutorial 36**, you can read numeric data from text files

### 54.5.1 When Each Line Contains One Number

```txt title="test1.txt"
123
456
789
```

```cpp
# include <Siv3D.hpp>

void Main()
{
	TextReader reader{ U"test1.txt" };

	if (not reader)
	{
		throw Error{ U"Failed to open a file" };
	}

	// Array to store numbers
	Array<int32> values;

	String line;

	// Read line by line
	while (reader.readLine(line))
	{
		// Convert read string to number and add to array
		values << Parse<int32>(line);
	}

	Print << values;

	while (System::Update())
	{

	}
}
```

### 54.5.2 When Each Line Contains One Number (Improved Version)
- The method in **54.5.1** will cause errors when trying to convert empty strings to numbers if the text file contains blank lines or ends with two or more newlines
- To avoid this, skip processing when the string read by `.readLine()` is empty

```cpp
# include <Siv3D.hpp>

void Main()
{
	TextReader reader{ U"test1.txt" };

	if (not reader)
	{
		throw Error{ U"Failed to open a file" };
	}

	// Array to store numbers
	Array<int32> values;

	String line;

	// Read line by line
	while (reader.readLine(line))
	{
		// Skip if empty string
		if (not line)
		{
			continue;
		}
		
		// Convert read string to number and add to array
		values << Parse<int32>(line);
	}

	Print << values;

	while (System::Update())
	{

	}
}
```


### 54.5.3 When Each Line Contains Multiple Data
- When a text line contains multiple pieces of information, use the `String` member function `.split(delimiter)` (**Tutorial 33.26**) to split one line into multiple elements
- `.split(delimiter)` returns an `Array<String>` of strings split using `delimiter` as the separator
- For example, splitting `U"123,456,789"` with `U','` or splitting `U"123 456 789"` with `U' '` yields `Array<String>{ U"123", U"456", U"789" }`

```txt title="test2.txt"
Alice 111 222
Bob 333 444
Carol 555 666
```

```cpp
# include <Siv3D.hpp>

struct Player
{
	String name;
	int32 id;
	int32 score;
};

void Main()
{
	TextReader reader{ U"test2.txt" };

	if (not reader)
	{
		throw Error{ U"Failed to open a file" };
	}

	// Array to store data
	Array<Player> players;

	String line;

	// Read line by line
	while (reader.readLine(line))
	{
		// Skip if empty string
		if (not line)
		{
			continue;
		}

		// Split by space character
		const Array<String> items = line.split(U' ');

		// Throw error if not expected number of elements
		if (items.size() != 3)
		{
			throw Error{ U"Invalid format" };
		}

		// Store data
		players << Player{ items[0], Parse<int32>(items[1]), Parse<int32>(items[2]) };
	}

	// Display data
	for (const auto& player : players)
	{
		Print << U"{} (ID {}): {}"_fmt(player.name, player.id, player.score);
	}

	while (System::Update())
	{

	}
}
```

### 54.5.4 When Each Line Contains Multiple Data (Improved Version)
- For the program in **54.5.3**, it's convenient to add processing like:
	- Close the file immediately after data reading is complete
	- Handle data anomalies
- The following sample code implements this:

```cpp
# include <Siv3D.hpp>

struct Player
{
	String name;
	int32 id;
	int32 score;
};

bool LoadPlayers(const FilePath& path, Array<Player>& players)
{
	// Clear first
	players.clear();

	TextReader reader{ path };

	// Failed if file couldn't be opened
	if (not reader)
	{
		return false;
	}

	String line;

	while (reader.readLine(line))
	{
		// Skip blank lines
		if (not line)
		{
			continue;
		}

		// Split by spaces
		const Array<String> items = line.split(U' ');

		// Failed if not expected number of elements
		if (items.size() != 3)
		{
			players.clear();
			return false;
		}

		// Failed if parsing fails
		try
		{
			players << Player{ items[0], Parse<int32>(items[1]), Parse<int32>(items[2]) };
		}
		catch (const ParseError&)
		{
			players.clear();
			return false;
		}
	}

	return true;
}

void Main()
{
	// Array to store data
	Array<Player> players;

	// Read data
	if (not LoadPlayers(U"test2.txt", players))
	{
		throw Error{ U"Failed to load players" };
	}

	// Display data
	for (const auto& player : players)
	{
		Print << U"{} (ID {}): {}"_fmt(player.name, player.id, player.score);
	}

	while (System::Update())
	{

	}
}
```


## 54.6 Opening Text Files for Writing
- Open text files for write-only access with `TextWriter variable_name{ U"file_path" };`
- File paths use relative paths based on the folder containing the executable file (the `App` folder during development) or absolute paths
- If a file at the specified path doesn't exist, a new empty file is created
- If the parent directory doesn't exist, the parent directory is also created
- To check if a file was opened successfully, use `if (writer.isOpen())`, `if (writer)`, or `if (not writer)`
- Opened files are closed by the destructor, so explicit closing is not necessary
- If you want to control the timing of file opening and closing, use `.open()` to open files and `.close()` to close files, just like with `TextReader`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Open text file for writing
	TextWriter writer{ U"test3.txt" };

	// If file couldn't be opened
	if (not writer)
	{
		// Throw exception and exit
		throw Error{ U"Failed to open `test3.txt`" };
	}

	while (System::Update())
	{

	}

	// File is automatically closed by writer's destructor
}
```


## 54.7 Writing to Text Files
- There are three ways to write strings to text files using `TextWriter`:
	- Write using `<<` like `Print`
	- Write using `.write(s)`
	- Write using `.writeln(s)`
- Values that can be formatted are automatically converted to strings
- When writing with `<<` or `.writeln()`, a newline (`"\r\n"`) is automatically inserted at the end
- Use `.write()` if you want to avoid inserting newlines

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Open text file for writing
	TextWriter writer{ U"test3.txt" };

	// If file couldn't be opened
	if (not writer)
	{
		// Throw exception and exit
		throw Error{ U"Failed to open `test3.txt`" };
	}

	// Write a sentence on one line
	writer << U"Hello, Siv3D!";

	// Write values and characters on one line
	writer << 123 << U", " << 456 << U", " << Point{ 10, 20 };

	// Write one character and newline
	writer.writeln(-3333);
	writer.writeln(1.234);
	writer.writeln(U"C++");

	// Write values (no newline)
	writer.write(777);
	writer.write(U", ");
	writer.write(888);

	while (System::Update())
	{

	}
}
```

```txt title="Write result"
Hello, Siv3D!
123, 456, (10, 20)
-3333
1.234
C++
777, 888
```


## 54.8 Appending to Existing Text Files
- When you want to write by appending to the end of an existing text file, specify `OpenMode::Append` (append mode) as the file open mode when opening
- If a text file with the same name doesn't exist, a new file is created
- The following sample code appends the string `Hello, Siv3D!` and the current time to `test4.txt` each time the program is run

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Open text file for writing in "append mode"
	TextWriter writer{ U"test4.txt", OpenMode::Append };

	// If file couldn't be opened
	if (not writer)
	{
		// Throw exception and exit
		throw Error{ U"Failed to open `test4.txt`" };
	}

	// Write sentence and current time
	writer << U"Hello, Siv3D!";
	writer << DateTime::Now();

	while (System::Update())
	{

	}
}
```


## 54.9 Control by Scope
- The following sample code fails to open because it tries to open file `test5.txt` with `TextReader` without closing the file opened by `TextWriter`

```cpp hl_lines="17-18"
# include <Siv3D.hpp>

void Main()
{
	// Open text file for writing
	TextWriter writer{ U"test5.txt" };

	// If file couldn't be opened
	if (not writer)
	{
		Print << U"Error 1";
	}

	writer << U"Hello, Siv3D!";
	writer << DateTime::Now();

	// Opening fails because it's already open with TextWriter
	TextReader reader{ U"test5.txt" };

	// If file couldn't be opened
	if (not reader)
	{
		Print << U"Error 2";
	}

	const String text = reader.readAll();
	Print << text;

	while (System::Update())
	{

	}
}
```

- The problem can be solved by controlling the timing of file opening and closing as follows:

```cpp hl_lines="17-18"
# include <Siv3D.hpp>

void Main()
{
	// Open text file for writing
	TextWriter writer{ U"test5.txt" };

	// If file couldn't be opened
	if (not writer)
	{
		Print << U"Error 1";
	}

	writer << U"Hello, Siv3D!";
	writer << DateTime::Now();

	// Close file
	writer.close();

	// Open file for reading
	TextReader reader{ U"test5.txt" };

	// If file couldn't be opened
	if (not reader)
	{
		Print << U"Error 2";
	}

	const String text = reader.readAll();
	Print << text;

	while (System::Update())
	{

	}
}
```

- As in the above sample code, mixing `TextWriter` and `TextReader` for the same file in the same scope makes code complex and error-prone
- Therefore, creating scopes with `{ }` and creating `TextWriter` or `TextReader` variables limited within them improves code readability
- Since variables cannot be used outside the scope, it also prevents accidentally operating on closed files

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		// Open text file for writing
		TextWriter writer{ U"test5.txt" };

		// If file couldn't be opened
		if (not writer)
		{
			Print << U"Error 1";
		}

		writer << U"Hello, Siv3D!";
		writer << DateTime::Now();
	}

	{
		// Open file for reading
		TextReader reader{ U"test5.txt" };

		// If file couldn't be opened
		if (not reader)
		{
			Print << U"Error 2";
		}

		const String text = reader.readAll();
		Print << text;
	}

	while (System::Update())
	{

	}
}
```


## 54.10 Writing with Specified Encoding
- You can specify encoding when creating new text files with `TextWriter`
- If no encoding is specified, UTF-8 with BOM is used

| Code | Encoding |
| --- | --- |
| `TextEncoding::UTF8_NO_BOM` | UTF-8 without BOM |
| `TextEncoding::UTF8_WITH_BOM` | UTF-8 with BOM |
| `TextEncoding::UTF16LE` | UTF-16 (LE) with BOM |
| `TextEncoding::UTF16BE` | UTF-16 (BE) with BOM |

```cpp
# include <Siv3D.hpp>

void Main()
{
	// UTF-8 with BOM
	{
		TextWriter writer{ U"test-utf8bom.txt" };
		writer.write(U"Siv3D");
	}

	// UTF-8 without BOM
	{
		TextWriter writer{ U"test-utf8.txt", TextEncoding::UTF8_NO_BOM };
		writer.write(U"Siv3D");
	}

	while (System::Update())
	{

	}
}
```


## 54.11 Writing Configuration Files
- You can use `TextWriter` to programmatically write configuration files like HTML, XML, JSON, INI, CSV

!!! info
	- Dedicated classes are available for writing CSV, INI, JSON, HTML files. See **Tutorial 55** for details

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Write CSV file
	{
		TextWriter writer{ U"test.csv" };
		writer.writeln(U"name,id,score");
		writer.writeln(U"Alice,1,100");
		writer.writeln(U"Bob,2,80");
		writer.writeln(U"Carol,3,60");
	}

	// Write HTML file
	{
		TextWriter writer{ U"test.html" };
		writer.writeln(U"<html>");
		writer.writeln(U"<head>");
		writer.writeln(U"<title>Test</title>");
		writer.writeln(U"</head>");
		writer.writeln(U"<body>");
		writer.writeln(U"<h1>Hello, Siv3D!</h1>");
		writer.writeln(U"</body>");
		writer.writeln(U"</html>");
	}

	while (System::Update())
	{

	}
}
```