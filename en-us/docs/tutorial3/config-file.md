# 55. Configuration Files
Learn how to read and write configuration files such as CSV, INI, JSON, TOML, and XML.

## 55.1 Configuration Files Overview
- Siv3D supports reading and writing the following configuration file formats:

| File Format | Read | Write |
|--|:--:|:--:|
| CSV | ✅ | ✅ |
| INI | ✅ | ✅ |
| JSON | ✅ | ✅ |
| TOML | ✅ |  |
| XML | ✅ |  |


## 55.2 Reading CSV
- Use the `CSV` class to parse and read data from CSV files
- Pass the path of the text file you want to read to the `CSV` constructor
- The file path should be a relative path based on the folder where the executable is located (the App folder during development) or an absolute path
- You can check if the reading was successful with `if (csv)` or `if (not csv)`
- CSV data is read in the format of `Array<Array<String>>`, and you can get the text at row `row` and column `col` using the subscript operator `[row][col]`
- `row` and `col` are counted from `0`
- `.rows()` returns the number of rows in the CSV data, and `.columns(row)` returns the number of columns in row `row`

```cpp
# include <Siv3D.hpp>

struct Item
{
	// Item label
	String label;

	// Item top-left position
	Point pos;
};

void Main()
{
	// Load data from CSV file
	const CSV csv{ U"example/csv/config.csv" };

	if (not csv) // If loading failed
	{
		throw Error{ U"Failed to load `config.csv`" };
	}

	// For each row
	for (size_t row = 0; row < csv.rows(); ++row)
	{
		// Display the contents of each column separated by tabs
		String line;

		for (size_t col = 0; col < csv.columns(row); ++col)
		{
			line += (csv[row][col] + U'\t');
		}

		Print << line;
	}

	Print << U"----";

	// Get each element and apply to window and scene settings
	{
		const String title	= csv[1][1];
		const int32 width	= Parse<int32>(csv[2][1]);
		const int32 height	= Parse<int32>(csv[3][1]);
		const bool sizable	= Parse<bool>(csv[4][1]);
		const ColorF background = Parse<ColorF>(csv[5][1]);

		Window::SetTitle(title);
		Window::Resize(width, height);
		Window::SetStyle(sizable ? WindowStyle::Sizable : WindowStyle::Fixed);
		Scene::SetBackground(background);
	}

	{
		const Array<int32> values = csv[6][1].split(U',').map(Parse<int32>);
		Print << values;
	}

	// Create an array of items from CSV data
	Array<Item> items;
	{
		const size_t itemCount = Parse<size_t>(csv[7][1]);
		const size_t baseRow = 8;

		for (size_t i = 0; i < itemCount; ++i)
		{
			items << Item
			{
				.label = csv[baseRow + i * 2][1],
				.pos = Parse<Point>(csv[baseRow + i * 2 + 1][1]),
			};
		}
	}

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		// Draw items
		for (const auto& item : items)
		{
			const Rect rect{ item.pos, 180, 80 };
			rect.draw();
			font(item.label).drawAt(30, rect.center(), ColorF{ 0.1 });
		}
	}
}
```


## 55.3 Writing CSV
- To write a CSV file, create an empty CSV object and add data from the first row using `.writeRow()`, `.write()`, `.newLine()`, etc.
- Formattable values are automatically converted to strings
- Finally, save with `.save(path)`
- If an element contains the character ",", that element is saved enclosed in quotation marks

```cpp
# include <Siv3D.hpp>

void Main()
{
	CSV csv;

	// Write one row
	csv.writeRow(U"item", U"price", U"count");
	csv.writeRow(U"Sword", 500, 1);

	// Write item by item
	csv.write(U"Arrow");
	csv.write(400);
	csv.write(2);
	csv.newLine();

	csv.writeRow(U"Shield", 300, 3);
	csv.writeRow(U"Carrot Seed", 20, 4);
	csv.writeRow(U"aa, bb, cc", 10, 5);
	csv.writeRow(Point{ 20, 30 }, Palette::Red, 100);

	// Save
	csv.save(U"tutorial.csv");
	
	while (System::Update())
	{

	}
}
```
Output file:
```csv title="tutorial.csv"
item,price,count
Sword,500,1
Arrow,400,2
Shield,300,3
Carrot Seed,20,4
"aa, bb, cc",10,5
"(20, 30)","(255, 0, 0, 255)",100
```


## 55.4 Updating CSV
- You can modify part of the loaded CSV data and then resave it to a file

```cpp
# include <Siv3D.hpp>

void Main()
{
	CSV csv{ U"example/csv/config.csv" };

	if (not csv)
	{
		throw Error{ U"Failed to load `config.csv`" };
	}

	// Modify data
	csv[2][1] = Format(1280);
	csv[3][1] = Format(720);

	// Add data
	csv.writeRow(U"Hello.Siv3D", 12345);

	csv.save(U"tutorial.csv");

	while (System::Update())
	{

	}
}
```
```csv title="tutorial.csv" hl_lines="3-4 15"
Name,Value
Window.title,My application
Window.width,1280
Window.height,720
Window.sizable,false
Scene.background,"(0.8, 0.9, 1.0)"
Array.values,"11, 22, 33, 44, 55"
Items.count,3
Item.label,Forest
Item.pos,"(100, 100)"
Item.label,Ocean
Item.pos,"(300, 200)"
Item.label,Mountain
Item.pos,"(500, 100)"
Hello.Siv3D,12345
```


## 55.5 Reading INI
- Use the `INI` class to parse and read data from INI files
- Pass the path of the text file you want to read to the `INI` constructor
- The file path should be a relative path based on the folder where the executable is located (the App folder during development) or an absolute path
- You can check if the reading was successful with `if (ini)` or `if (not ini)`
- INI data is read in the format of `HashTable<String, String>` for each section, and you can get the text of name NAME in section SECTION using the subscript operator `[U"SECTION.NAME"]`

```cpp
# include <Siv3D.hpp>

struct Item
{
	// Item label
	String label;

	// Item top-left position
	Point pos;
};

void Main()
{
	// Load data from INI file
	const INI ini{ U"example/ini/config.ini" };

	if (not ini) // If loading failed
	{
		throw Error{ U"Failed to load `config.ini`" };
	}

	// List all sections
	for (const auto& section : ini.sections())
	{
		// Section name
		Print << U"[{}]"_fmt(section.section);

		// List all records in the section
		for (auto&& [key, value] : section.keys)
		{
			// Key and value
			Print << U"{} = {}"_fmt(key, value);
		}
	}
	Print << U"----";

	// Get each element and apply to window and scene settings
	{
		const String title	= ini[U"Window.title"];
		const int32 width	= Parse<int32>(ini[U"Window.width"]);
		const int32 height	= Parse<int32>(ini[U"Window.height"]);
		const bool sizable	= Parse<bool>(ini[U"Window.sizable"]);
		const ColorF background = Parse<ColorF>(ini[U"Scene.background"]);

		Window::SetTitle(title);
		Window::Resize(width, height);
		Window::SetStyle(sizable ? WindowStyle::Sizable : WindowStyle::Fixed);
		Scene::SetBackground(background);
	}

	{
		const Array<int32> values = ini[U"Array.values"].split(U',').map(Parse<int32>);
		Print << values;
	}

	// Create an array of items from INI data
	Array<Item> items;
	{
		const size_t itemCount = Parse<size_t>(ini[U"Items.count"]);

		for (size_t i = 0; i < itemCount; ++i)
		{
			items << Item
			{
				.label = ini[U"Item{}.label"_fmt(i)],
				.pos = Parse<Point>(ini[U"Item{}.pos"_fmt(i)]),
			};
		}
	}

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		// Draw items
		for (const auto& item : items)
		{
			const Rect rect{ item.pos, 180, 80 };
			rect.draw();
			font(item.label).drawAt(30, rect.center(), ColorF{ 0.1 });
		}
	}
}
```


## 55.6 Writing INI
- To write an INI file, create an empty INI object and add sections and records using `.addSection(section name)`, `.write(section, key, value)`, etc.
- Finally, save with `.save(path)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	INI ini;

	// Add sections
	ini.addSection(U"Item");
	ini.addSection(U"Setting");

	ini.write(U"Item", U"Sword", 500);
	ini.write(U"Item", U"Arrow", 400);
	ini.write(U"Item", U"Shield", 300);
	ini.write(U"Item", U"Carrot Seed", 20);
	ini.write(U"Setting", U"pos", Point{ 20, 30 });
	ini.write(U"Setting", U"color", Palette::Red);

	// Save
	ini.save(U"tutorial.ini");
	
	while (System::Update())
	{

	}
}
```
```ini title="tutorial.ini"
[Item]
Sword = 500
Arrow = 400
Shield = 300
Carrot Seed = 20

[Setting]
pos = (20, 30)
color = (255, 0, 0, 255)
```


## 55.7 Updating INI
- You can modify part of the loaded INI data and then resave it to a file

```cpp
# include <Siv3D.hpp>

void Main()
{
	INI ini{ U"example/ini/config.ini" };

	if (not ini)
	{
		throw Error{ U"Failed to load `config.ini`" };
	}

	// Modify data
	ini[U"Window.width"] = 1280;
	ini[U"Window.height"] = 720;

	// Add data
	ini.addSection(U"Siv3D");
	ini.write(U"Siv3D", U"message", U"Hello!");

	// Delete data
	ini.removeSection(U"Item2");

	ini.save(U"tutorial.ini");

	while (System::Update())
	{

	}
}
```
```ini title="tutorial.ini" hl_lines="3-4 24-25"
[Window]
title = My application
width = 1280
height = 720
sizable = false

[Scene]
background = (0.8, 0.9, 1.0)

[Array]
values = 11, 22, 33, 44, 55

[Items]
count = 3

[Item0]
label = Forest
pos = (100, 100)

[Item1]
label = Ocean
pos = (300, 200)

[Siv3D]
message = Hello!
```


## 55.8 Reading JSON
- Use the `JSON` class to parse and read data from JSON files
- Pass the path of the text file you want to read to `JSON::Load()`
- The file path should be a relative path based on the folder where the executable is located (the App folder during development) or an absolute path
- You can check if the reading was successful with `if (json)` or `if (not json)`
- You can recursively traverse all elements of JSON data as shown in the `ShowObject()` function in the next sample
- You can also directly get the desired value by specifying the path with the subscript operator `[U"NAME1"][U"NAME2]...`

```cpp
# include <Siv3D.hpp>

struct Item
{
	// Item label
	String label;

	// Item top-left position
	Point pos;
};

// Recursively display JSON elements
void ShowObject(const JSON& value)
{
	switch (value.getType())
	{
	case JSONValueType::Empty:
		Console << U"empty";
		break;
	case JSONValueType::Null:
		Console << U"null";
		break;
	case JSONValueType::Object:
		for (const auto& object : value)
		{
			Console << U"[{}]"_fmt(object.key);
			ShowObject(object.value);
		}
		break;
	case JSONValueType::Array:
		for (auto&& [index, object] : value)
		{
			ShowObject(object);
		}
		break;
	case JSONValueType::String:
		Console << value.getString();
		break;
	case JSONValueType::Number:
		Console << value.get<double>();
		break;
	case JSONValueType::Bool:
		Console << value.get<bool>();
		break;
	}
}

void Main()
{
	// Load data from JSON file
	const JSON json = JSON::Load(U"example/json/config.json");

	if (not json) // If loading failed
	{
		throw Error{ U"Failed to load `config.json`" };
	}

	// Display all JSON data
	ShowObject(json);

	Console << U"-----";

	// Get each element and apply to window and scene settings
	{
		const String title	= json[U"Window"][U"title"].getString();
		const int32 width	= json[U"Window"][U"width"].get<int32>();
		const int32 height	= json[U"Window"][U"height"].get<int32>();
		const bool sizable	= json[U"Window"][U"sizable"].get<bool>();
		const ColorF background = json[U"Scene"][U"background"].get<ColorF>();

		Window::SetTitle(title);
		Window::Resize(width, height);
		Window::SetStyle(sizable ? WindowStyle::Sizable : WindowStyle::Fixed);
		Scene::SetBackground(background);
	}

	{
		Array<int32> values;
		
		for (auto&& [index, object] : json[U"Array"][U"values"])
		{
			values << object.get<int32>();
		}

		Console << values;
	}

	// Create an array of items from JSON data
	Array<Item> items;
	{
		for (auto&& [index, object] : json[U"Items"])
		{
			items << Item
			{
				.label = object[U"label"].getString(),
				.pos = Point{ object[U"pos"][U"x"].get<int32>(), object[U"pos"][U"y"].get<int32>() },
			};
		}
	}

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		// Draw items
		for (const auto& item : items)
		{
			const Rect rect{ item.pos, 180, 80 };
			rect.draw();
			font(item.label).drawAt(30, rect.center(), ColorF{ 0.1 });
		}
	}
}
```


## 55.9 Writing JSON
- To write a JSON file, add data using the `operator[]` of `JSON`, and finally save with `.save(path)`
- Objects are recorded in dictionary order
- For arrays, you can add elements like `json[U"Array"].push_back(100);`

```cpp
# include <Siv3D.hpp>

void Main()
{
	JSON json;

	json[U"Item"][U"Sword"][U"price"] = 500;
	json[U"Item"][U"Arrow"][U"price"] = 400;
	json[U"Item"][U"Shield"][U"price"] = 300;
	json[U"Item"][U"Carrot Seed"][U"price"] = 20;

	json[U"Setting"][U"pos"] = Point{ 20, 30 };
	json[U"Setting"][U"color"] = Palette::Red;

	json[U"Array"].push_back(10);
	json[U"Array"].push_back(20);
	json[U"Array"].push_back(30);

	json.save(U"tutorial.json");

	while (System::Update())
	{

	}
}
```
```json title="tutorial.json"
{
  "Array": [
    10,
    20,
    30
  ],
  "Item": {
    "Arrow": {
      "price": 400
    },
    "Carrot Seed": {
      "price": 20
    },
    "Shield": {
      "price": 300
    },
    "Sword": {
      "price": 500
    }
  },
  "Setting": {
    "color": "(255, 0, 0, 255)",
    "pos": "(20, 30)"
  }
}
```


## 55.10 Updating JSON
- You can modify part of the loaded JSON data and then resave it to a file

```cpp
# include <Siv3D.hpp>

void Main()
{
	JSON json = JSON::Load(U"example/json/config.json");

	if (not json)
	{
		throw Error{ U"Failed to load `config.json`" };
	}

	// Modify data
	json[U"Window"][U"width"] = 1280;
	json[U"Window"][U"height"] = 720;

	// Add data
	json[U"Siv3D"][U"message"] = U"Hello!";

	// Delete data
	json[U"Items"].erase(2);
	json.erase(U"Array");

	json.save(U"tutorial.json");

	while (System::Update())
	{

	}
}
```
```json title="tutorial.json"
{
  "Items": [
    {
      "label": "Forest",
      "pos": {
        "x": 100,
        "y": 100
      }
    },
    {
      "label": "Ocean",
      "pos": {
        "x": 300,
        "y": 200
      }
    }
  ],
  "Scene": {
    "background": "(0.8, 0.9, 1.0)"
  },
  "Siv3D": {
    "message": "Hello!"
  },
  "Window": {
    "height": 720,
    "sizable": false,
    "title": "My application",
    "width": 1280
  }
}
```


## 55.11 Stringifying JSON
- `.format()` of `JSON` converts JSON data to a formatted `String`
- `.formatMinimum()` returns a minimal `String` that omits whitespace and line breaks for formatting

```cpp
# include <Siv3D.hpp>

void Main()
{
	JSON json = JSON::Load(U"example/json/config.json");

	const String s = json.formatMinimum();

	Print << s;

	while (System::Update())
	{

	}
}
```
```txt title="Output"
{"Array":{"values":[11,22,33,44,55]},"Items":[{"label":"Forest","pos":{"x":100,"y":100}},{"label":"Ocean","pos":{"x":300,"y":200}},{"label":"Mountain","pos":{"x":500,"y":100}}],"Scene":{"background":"(0.8, 0.9, 1.0)"},"Window":{"height":600,"sizable":false,"title":"My application","width":800}}
```


## 55.12 JSON Literals
- You can directly write JSON data by adding `_json` to string literals
- It is recommended to use raw string literals `UR` so that double quotes can be written directly
- To convert from a regular `String` to `JSON`, use `JSON::Parse(s)`
- `MakeJSON1()`, `MakeJSON2()`, and `MakeJSON3()` in the following code create the same JSON data

```cpp
# include <Siv3D.hpp>

JSON MakeJSON1()
{
	JSON json;
	json[U"name"] = U"Albert";
	json[U"age"] = 42;
	json[U"object"] = JSON::Parse(U"{}");
	return json;
}

JSON MakeJSON2()
{
	return UR"({
    "name": "Albert",
    "age": 42,
    "object": {}
})"_json;
}

JSON MakeJSON3()
{
	const String s = UR"({
	"name": "Albert",
	"age": 42,
	"object": {}
})";

	return JSON::Parse(s);
}

void Main()
{
	const JSON json1 = MakeJSON1();
	const JSON json2 = MakeJSON2();
	const JSON json3 = MakeJSON3();

	Print << (json1 == json2);
	Print << (json2 == json3);

	while (System::Update())
	{

	}
}
```
```txt title="Output"
true
true
```


## 55.13 JSON Validation
- `JSONValidator` verifies whether JSON data has appropriate structure and values based on JSON Schema
- JSON Schema is JSON data that defines the structure, data types, value ranges, etc. of JSON data
- You can directly write JSON Schema by adding `jsonValidator` to string literals
- In the following sample code, it shows that `json1` and `json3` do not conform to the specified Schema and the reasons

```cpp
# include <Siv3D.hpp>

void Main()
{
	const JSON json1 = UR"({
    "name": "Albert",
    "age": 42,
    "object": {}
})"_json;

	const JSON json2 = UR"({
    "name": "Albert",
    "age": 42,
    "object": {
        "string": "aaaa"
    }
})"_json;

	const JSON json3 = UR"({
    "name": "Albert",
    "age": 999,
    "object": {
        "string": "bbbb"
    }
})"_json;

	const JSONValidator validator = UR"({
    "title": "A person",
    "properties": {
        "name": {
            "description": "Name",
            "type": "string"
        },
        "age": {
            "description": "Age of the person",
            "type": "number",
            "minimum": 2,
            "maximum": 200
        },
        "object": {
            "type": "object",
            "properties": {
                "string": {
                    "type": "string"
                }
            },
            "required": [
                "string"
            ]
        }
    },
    "required": [
        "name",
        "age",
        "object"
    ],
    "type": "object"
})"_jsonValidator;

	Print << U"json1:";
	{
		JSONValidator::ValidationError error;

		if (validator.validate(json1, error))
		{
			Print << U"OK";
		}
		else
		{
			Print << error;
		}
	}

	Print << U"json2:";
	{
		JSONValidator::ValidationError error;

		if (validator.validate(json2, error))
		{
			Print << U"OK";
		}
		else
		{
			Print << error;
		}
	}

	Print << U"json3:";
	{
		JSONValidator::ValidationError error;

		if (validator.validate(json3, error))
		{
			Print << U"OK";
		}
		else
		{
			Print << error;
		}
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output"
json1:
[JSONValidator::ValidationError] required property 'string' not found in object
json2:
OK
json3:
[JSONValidator::ValidationError] instance exceeds maximum of 200.000000
```


## 55.14 Reading TOML
- Use the `TOMLReader` class to parse and read data from TOML files
- Pass the path of the text file you want to read to the `TOMLReader` constructor
- The file path should be a relative path based on the folder where the executable is located (the App folder during development) or an absolute path
- You can check if the reading was successful with `if (toml)` or `if (not toml)`
- You can recursively traverse all elements of TOML data as shown in the `ShowTable()` function in the next sample
- You can also directly get the desired value by specifying the path with the subscript operator `[U"NAME1.NAME2.NAME3..."]`

!!! warning "Japanese is not supported"
	- Siv3D v0.6's `TOMLReader` cannot read TOML files containing non-ASCII characters
	- Support is planned for future versions

```cpp
# include <Siv3D.hpp>

struct Item
{
	// Item label
	String label;

	// Item top-left position
	Point pos;
};

// Recursively display TOML elements
void ShowTable(const TOMLValue& value)
{
	for (const auto& table : value.tableView())
	{
		switch (table.value.getType())
		{
		case TOMLValueType::Empty:
			Console << U"[Empty] " << table.name;
			break;
		case TOMLValueType::Table:
			Console << U"[Table] " << table.name;
			ShowTable(table.value);
			break;
		case TOMLValueType::Array:
			Console << U"[Array] " << table.name;
			for (const auto& element : table.value.arrayView())
			{
				switch (element.getType())
				{
				case TOMLValueType::String:
					Console << element.getString();
					break;
				case TOMLValueType::Number:
					Console << element.get<double>();
					break;
				case TOMLValueType::Bool:
					Console << element.get<bool>();
					break;
				case TOMLValueType::Date:
					Console << element.getDate();
					break;
				case TOMLValueType::DateTime:
					Console << element.getDateTime();
					break;
				default:
					break;
				}
			}
			break;
		case TOMLValueType::TableArray:
			Console << U"[TableArray] " << table.name;
			for (const auto& table2 : table.value.tableArrayView())
			{
				ShowTable(table2);
			}
			break;
		case TOMLValueType::String:
			Console << U"[String] " << table.name;
			Console << table.value.getString();
			break;
		case TOMLValueType::Number:
			Console << U"[Number] " << table.name;
			Console << table.value.get<double>();
			break;
		case TOMLValueType::Bool:
			Console << U"[Bool] " << table.name;
			Console << table.value.get<bool>();
			break;
		case TOMLValueType::Date:
			Console << U"[Date] " << table.name;
			Console << table.value.getDate();
			break;
		case TOMLValueType::DateTime:
			Console << U"[DateTime] " << table.name;
			Console << table.value.getDateTime();
			break;
		case TOMLValueType::Unknown:
			Console << U"[Unknown] " << table.name;
			break;
		}
	}
}

void Main()
{
	// Load data from TOML file
	const TOMLReader toml{ U"example/toml/config.toml" };

	if (not toml) // If loading failed
	{
		throw Error{ U"Failed to load `config.toml`" };
	}

	// Display all TOML data
	ShowTable(toml);

	Console << U"-----";

	// Get each element and apply to window and scene settings
	{
		const String title	= toml[U"Window.title"].getString();
		const int32 width	= toml[U"Window.width"].get<int32>();
		const int32 height	= toml[U"Window.height"].get<int32>();
		const bool sizable	= toml[U"Window.sizable"].get<bool>();
		const ColorF background = toml[U"Scene.background"].get<ColorF>();

		Window::SetTitle(title);
		Window::Resize(width, height);
		Window::SetStyle(sizable ? WindowStyle::Sizable : WindowStyle::Fixed);
		Scene::SetBackground(background);
	}

	{
		Array<int32> values;

		for (const auto& object : toml[U"Array.values"].arrayView())
		{
			values << object.get<int32>();
		}

		Console << values;
	}

	// Create an array of items from TOML data
	Array<Item> items;
	{
		for (const auto& object : toml[U"Items"].tableArrayView())
		{
			items << Item
			{
				.label = object[U"label"].getString(),
				.pos = Point{ object[U"pos.x"].get<int32>(), object[U"pos.y"].get<int32>() },
			};
		}
	}

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		// Draw items
		for (const auto& item : items)
		{
			const Rect rect{ item.pos, 180, 80 };
			rect.draw();
			font(item.label).drawAt(30, rect.center(), ColorF{ 0.1 });
		}
	}
}
```


## 55.15 Reading XML
- Use the `XMLReader` class to parse and read data from XML files
- Pass the path of the text file you want to read to the `XMLReader` constructor
- The file path should be a relative path based on the folder where the executable is located (the App folder during development) or an absolute path
- You can check if the reading was successful with `if (xml)` or `if (not xml)`
- You can recursively traverse all elements of XML data as shown in the `ShowElements()` function in the next sample

```cpp
# include <Siv3D.hpp>

struct Item
{
	// Item label
	String label;

	// Item top-left position
	Point pos;
};

// Recursively display XML elements
void ShowElements(const XMLElement& element)
{
	for (auto e = element.firstChild(); e; e = e.nextSibling())
	{
		Console << U"<{}>"_fmt(e.name());

		if (const auto attributes = e.attributes())
		{
			Console << attributes;
		}

		if (const auto text = e.text())
		{
			Console << text;
		}

		ShowElements(e);

		Console << U"</{}>"_fmt(e.name());
	}
}

void Main()
{
	// Load data from XML file
	const XMLReader xml(U"example/xml/config.xml");

	if (not xml) // If loading failed
	{
		throw Error{ U"Failed to load `config.xml`" };
	}

	// Display all XML data
	ShowElements(xml);

	Console << U"-----";

	Array<Item> items;
	{
		String title;
		int32 width = Window::DefaultClientSize.x;
		int32 height = Window::DefaultClientSize.y;
		bool sizable = false;
		ColorF background{ 0.0 };
		Array<int32> values;

		// Traverse elements to get desired values
		for (auto elem = xml.firstChild(); elem; elem = elem.nextSibling())
		{
			const String name = elem.name();

			if (name == U"Window")
			{
				for (auto elem2 = elem.firstChild(); elem2; elem2 = elem2.nextSibling())
				{
					const String name2 = elem2.name();

					if (name2 == U"title")
					{
						title = elem2.text();
					}
					else if (name2 == U"width")
					{
						width = Parse<int32>(elem2.text());
					}
					else if (name2 == U"height")
					{
						height = Parse<int32>(elem2.text());
					}
					else if (name2 == U"sizable")
					{
						sizable = Parse<bool>(elem2.text());
					}
				}
			}
			else if (name == U"Scene")
			{
				for (auto elem2 = elem.firstChild(); elem2; elem2 = elem2.nextSibling())
				{
					const String name2 = elem2.name();

					if (name2 == U"background")
					{
						background = Parse<ColorF>(elem2.text());
					}
				}
			}
			if (name == U"Array")
			{
				for (auto elem2 = elem.firstChild(); elem2; elem2 = elem2.nextSibling())
				{
					values << Parse<int32>(elem2.text());
				}
			}
			if (name == U"Items")
			{
				Item item;

				for (auto elem2 = elem.firstChild(); elem2; elem2 = elem2.nextSibling())
				{
					const String name2 = elem2.name();

					if (name2 == U"label")
					{
						item.label = elem2.text();
					}
					else if (name2 == U"pos")
					{
						Point pos{ 0, 0 };

						for (auto elem3 = elem2.firstChild(); elem3; elem3 = elem3.nextSibling())
						{
							const String name3 = elem3.name();

							if (name3 == U"x")
							{
								pos.x = Parse<int32>(elem3.text());
							}
							else if (name3 == U"y")
							{
								pos.y = Parse<int32>(elem3.text());
							}
						}

						item.pos = pos;
					}
				}

				items << item;
			}
		}

		Window::SetTitle(title);
		Window::Resize(width, height);
		Window::SetStyle(sizable ? WindowStyle::Sizable : WindowStyle::Fixed);
		Scene::SetBackground(background);

		Console << values;
	}

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		// Draw items
		for (const auto& item : items)
		{
			const Rect rect{ item.pos, 180, 80 };
			rect.draw();
			font(item.label).drawAt(30, rect.center(), ColorF{ 0.1 });
		}
	}
}
```