# 55. 設定ファイル
CSV, INI, JSON, TOML, XML などの設定ファイルを読み書きする方法を学びます。

## 55.1 設定ファイルの概要
- Siv3D は次の設定ファイルの読み書きに対応しています：

| ファイル形式 | 読み込み | 書き出し |
|--|:--:|:--:|
| CSV | ✅ | ✅ |
| INI | ✅ | ✅ |
| JSON | ✅ | ✅ |
| TOML | ✅ |  |
| XML | ✅ |  |


## 55.2 CSV の読み込み
- CSV ファイルをパースしてデータを読み込むには `CSV` クラスを使います
- `CSV` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します
- ファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します
- 読み込みに成功したかどうかは、`if (csv)`, `if (not csv)` で調べられます
- CSV データは `Array<Array<String>>` の形式で読み込まれ、添え字演算子 `[row][col]` によって row 行 col 列目のテキストを取得できます
- `row`, `col` は `0` からカウントします
- `.rows()` は CSV データの行数、`.columns(row)` は row 行目の列数を返します

```cpp
# include <Siv3D.hpp>

struct Item
{
	// アイテムのラベル
	String label;

	// アイテムの左上座標
	Point pos;
};

void Main()
{
	// CSV ファイルからデータを読み込む
	const CSV csv{ U"example/csv/config.csv" };

	if (not csv) // もし読み込みに失敗したら
	{
		throw Error{ U"Failed to load `config.csv`" };
	}

	// 各行について
	for (size_t row = 0; row < csv.rows(); ++row)
	{
		// 各列の内容をタブ区切りで表示する
		String line;

		for (size_t col = 0; col < csv.columns(row); ++col)
		{
			line += (csv[row][col] + U'\t');
		}

		Print << line;
	}

	Print << U"----";

	// 各要素を取得し、ウィンドウやシーンの設定に反映させる
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

	// アイテムの配列を CSV データから作成する
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
		// アイテムを描画する
		for (const auto& item : items)
		{
			const Rect rect{ item.pos, 180, 80 };
			rect.draw();
			font(item.label).drawAt(30, rect.center(), ColorF{ 0.1 });
		}
	}
}
```


## 55.3 CSV の書き出し
- CSV ファイルを書き出すには、空の CSV オブジェクトを作成し、`.writeRow()`, `.write()`, `.newLine()` などを使って先頭の行からデータを追加していきます
- フォーマット可能な値は自動的に文字列に変換されます
- 最後に `.save(path)` で保存します
- 要素が文字「,」を含む場合、その要素は「"」で囲んで保存されます

```cpp
# include <Siv3D.hpp>

void Main()
{
	CSV csv;

	// 1 行書き込む場合
	csv.writeRow(U"item", U"price", U"count");
	csv.writeRow(U"Sword", 500, 1);

	// 1 項目ずつ書き込む場合
	csv.write(U"Arrow");
	csv.write(400);
	csv.write(2);
	csv.newLine();

	csv.writeRow(U"Shield", 300, 3);
	csv.writeRow(U"Carrot Seed", 20, 4);
	csv.writeRow(U"aa, bb, cc", 10, 5);
	csv.writeRow(Point{ 20, 30 }, Palette::Red, 100);

	// 保存
	csv.save(U"tutorial.csv");
	
	while (System::Update())
	{

	}
}
```
出力されるファイル
```csv title="tutorial.csv"
item,price,count
Sword,500,1
Arrow,400,2
Shield,300,3
Carrot Seed,20,4
"aa, bb, cc",10,5
"(20, 30)","(255, 0, 0, 255)",100
```


## 55.4 CSV の更新
- 読み込んだ CSV データの一部を変更したうえで、ファイルに再保存することができます

```cpp
# include <Siv3D.hpp>

void Main()
{
	CSV csv{ U"example/csv/config.csv" };

	if (not csv)
	{
		throw Error{ U"Failed to load `config.csv`" };
	}

	// データを書き換える
	csv[2][1] = Format(1280);
	csv[3][1] = Format(720);

	// データを追加する
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


## 55.5 INI の読み込み
- INI ファイルをパースしてデータを読み込むには `INI` クラスを使います
- `INI` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します
- ファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します
- 読み込みに成功したかどうかは、`if (ini)`, `if (not ini)` で調べられます
- INI データは、セクションごとに `HashTable<String, String>` の形式で読み込まれ、添え字演算子 `[U"SECTION.NAME"]` によってセクション SECTION にある名前 NAME のテキストを取得できます

```cpp
# include <Siv3D.hpp>

struct Item
{
	// アイテムのラベル
	String label;

	// アイテムの左上座標
	Point pos;
};

void Main()
{
	// INI ファイルからデータを読み込む
	const INI ini{ U"example/ini/config.ini" };

	if (not ini) // もし読み込みに失敗したら
	{
		throw Error{ U"Failed to load `config.ini`" };
	}

	// 全てのセクションを列挙
	for (const auto& section : ini.sections())
	{
		// セクション名
		Print << U"[{}]"_fmt(section.section);

		// セクション内のすべてのレコードを列挙する
		for (auto&& [key, value] : section.keys)
		{
			// キーと値
			Print << U"{} = {}"_fmt(key, value);
		}
	}
	Print << U"----";

	// 各要素を取得し、ウィンドウやシーンの設定に反映させる
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

	// アイテムの配列を INI データから作成
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
		// アイテムを描画する
		for (const auto& item : items)
		{
			const Rect rect{ item.pos, 180, 80 };
			rect.draw();
			font(item.label).drawAt(30, rect.center(), ColorF{ 0.1 });
		}
	}
}
```


## 55.6 INI の書き出し
- INI ファイルを書き出すには、空の INI オブジェクトを作成し、`.addSection(セクション名)`, `.write(セクション, キー, 値)` などを使ってセクションやレコードを追加していきます
- 最後に `.save(path)` で保存します

```cpp
# include <Siv3D.hpp>

void Main()
{
	INI ini;

	// セクションを追加
	ini.addSection(U"Item");
	ini.addSection(U"Setting");

	ini.write(U"Item", U"Sword", 500);
	ini.write(U"Item", U"Arrow", 400);
	ini.write(U"Item", U"Shield", 300);
	ini.write(U"Item", U"Carrot Seed", 20);
	ini.write(U"Setting", U"pos", Point{ 20, 30 });
	ini.write(U"Setting", U"color", Palette::Red);

	// 保存
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


## 55.7 INI の更新
- 読み込んだ INI データの一部を変更したうえで、ファイルに再保存することができます

```cpp
# include <Siv3D.hpp>

void Main()
{
	INI ini{ U"example/ini/config.ini" };

	if (not ini)
	{
		throw Error{ U"Failed to load `config.ini`" };
	}

	// データを書き換える
	ini[U"Window.width"] = 1280;
	ini[U"Window.height"] = 720;

	// データを追加する
	ini.addSection(U"Siv3D");
	ini.write(U"Siv3D", U"message", U"Hello!");

	// データを削除する
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


## 55.8 JSON の読み込み
- JSON ファイルをパースしてデータを読み込むには `JSON` クラスを使います
- 読み込みたいテキストファイルのパスを `JSON::Load()` に渡します
- ファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します
- 読み込みに成功したかどうかは、`if (json)`, `if (not json)` で調べられます
- JSON データは次のサンプルの `ShowObject()` 関数のようにして再帰的に全要素を走査できます
- 添え字演算子 `[U"NAME1"][U"NAME2]...` によってパスを指定して目的の値を直接得ることもできます

```cpp
# include <Siv3D.hpp>

struct Item
{
	// アイテムのラベル
	String label;

	// アイテムの左上座標
	Point pos;
};

// 再帰的に JSON の要素を表示
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
	// JSON ファイルからデータを読み込む
	const JSON json = JSON::Load(U"example/json/config.json");

	if (not json) // もし読み込みに失敗したら
	{
		throw Error{ U"Failed to load `config.json`" };
	}

	// JSON データをすべて表示する
	ShowObject(json);

	Console << U"-----";

	// 各要素を取得し、ウィンドウやシーンの設定に反映させる
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

	// 数値の配列を JSON データから作成
	Array<int32> values;
	{
		for (auto&& [index, object] : json[U"Array"][U"values"])
		{
			values << object.get<int32>();
		}
	}
	Console << values;

	// アイテムの配列を JSON データから作成
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
		// アイテムを描画する
		for (const auto& item : items)
		{
			const Rect rect{ item.pos, 180, 80 };
			rect.draw();
			font(item.label).drawAt(30, rect.center(), ColorF{ 0.1 });
		}
	}
}
```


## 55.9 JSON の書き出し
- JSON ファイルを書き出すには、`JSON` の `operator[]` でデータを追加し、最後に `.save(path)` で保存します
- オブジェクトは辞書順に記録されます
- 配列の場合 `json[U"Array"].push_back(100);` のようにして要素を追加できます

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


## 55.10 JSON の更新
- 読み込んだ JSON データの一部を変更したうえで、ファイルに再保存することができます

```cpp
# include <Siv3D.hpp>

void Main()
{
	JSON json = JSON::Load(U"example/json/config.json");

	if (not json)
	{
		throw Error{ U"Failed to load `config.json`" };
	}

	// データを書き換える
	json[U"Window"][U"width"] = 1280;
	json[U"Window"][U"height"] = 720;

	// データを追加する
	json[U"Siv3D"][U"message"] = U"Hello!";

	// データを削除する
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


## 55.11 JSON の文字列化
- `JSON` の `.format()` は JSON データを整形された `String` に変換します
- `.formatMinimum()` は、整形のための空白や改行を省略した、最小限の `String` を返します

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
```txt title="出力"
{"Array":{"values":[11,22,33,44,55]},"Items":[{"label":"Forest","pos":{"x":100,"y":100}},{"label":"Ocean","pos":{"x":300,"y":200}},{"label":"Mountain","pos":{"x":500,"y":100}}],"Scene":{"background":"(0.8, 0.9, 1.0)"},"Window":{"height":600,"sizable":false,"title":"My application","width":800}}
```


## 55.12 TOML の読み込み
- TOML ファイルをパースしてデータを読み込むには `TOMLReader` クラスを使います
- `TOMLReader` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します
- ファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します
- 読み込みに成功したかどうかは、`if (toml)`, `if (not toml)` で調べられます
- TOML データは次のサンプルの `ShowTable()` 関数のようにして再帰的に全要素を走査できます
- 添え字演算子 `[U"NAME1.NAME2.NAME3..."]` によってパスを指定して目的の値を直接得ることもできます

!!! warning "日本語は未サポート"
	- Siv3D v0.6 では、`TOMLReader` を使って非 ASCII 文字を含む TOML ファイルを読み込むことができません
	- 将来のバージョンで対応予定です

```cpp
# include <Siv3D.hpp>

struct Item
{
	// アイテムのラベル
	String label;

	// アイテムの左上座標
	Point pos;
};

// 再帰的に TOML の要素を表示
void ShowTable(const TOMLValue& value)
{
	for (const auto& table : value.tableView())
	{
		switch (table.value.getType())
		{
		case TOMLValueType::Empty:
			Console << U"[Empty]" << table.name;
			break;
		case TOMLValueType::Table:
			Console << U"[Table]" << table.name;
			ShowTable(table.value);
			break;
		case TOMLValueType::Array:
			Console << U"[Array]" << table.name;
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
			Console << U"[TableArray]" << table.name;
			for (const auto& table2 : table.value.tableArrayView())
			{
				ShowTable(table2);
			}
			break;
		case TOMLValueType::String:
			Console << U"[String]" << table.name;
			Console << table.value.getString();
			break;
		case TOMLValueType::Number:
			Console << U"[Number]" << table.name;
			Console << table.value.get<double>();
			break;
		case TOMLValueType::Bool:
			Console << U"[Bool]" << table.name;
			Console << table.value.get<bool>();
			break;
		case TOMLValueType::Date:
			Console << U"[Date]" << table.name;
			Console << table.value.getDate();
			break;
		case TOMLValueType::DateTime:
			Console << U"[DateTime]" << table.name;
			Console << table.value.getDateTime();
			break;
		case TOMLValueType::Unknown:
			Console << U"[Unknown]" << table.name;
			break;
		}
	}
}

void Main()
{
	// TOML ファイルからデータを読み込む
	const TOMLReader toml{ U"example/toml/config.toml" };

	if (not toml) // もし読み込みに失敗したら
	{
		throw Error{ U"Failed to load `config.toml`" };
	}

	// TOML データをすべて表示
	ShowTable(toml);

	Console << U"-----";

	// 要素のパスで値を取得
	const String windowTitle = toml[U"Window.title"].getString();
	const int32 windowWidth = toml[U"Window.width"].get<int32>();
	const int32 windowHeight = toml[U"Window.height"].get<int32>();
	const bool windowSizable = toml[U"Window.sizable"].get<bool>();
	const ColorF sceneBackground = toml[U"Scene.background"].get<ColorF>();

	Window::SetTitle(windowTitle);
	Window::Resize(windowWidth, windowHeight);
	Window::SetStyle(windowSizable ? WindowStyle::Sizable : WindowStyle::Fixed);
	Scene::SetBackground(sceneBackground);

	// 数値の配列を TOML データから作成
	Array<int32> values;
	{
		for (const auto& object : toml[U"Array.values"].arrayView())
		{
			values << object.get<int32>();
		}
	}
	Console << values;

	// アイテムの配列を TOML データから作成
	Array<Item> items;
	{
		for (const auto& object : toml[U"Items"].tableArrayView())
		{
			Item item;
			item.label = object[U"label"].getString();
			item.pos = Point{ object[U"pos.x"].get<int32>(), object[U"pos.y"].get<int32>() };
			items << item;
		}
	}

	// アイテム描画用のフォント
	const Font font{ 30, Typeface::Bold };

	while (System::Update())
	{
		// アイテムを描画
		for (const auto& item : items)
		{
			const Rect rect{ item.pos, 180, 80 };

			rect.draw();

			font(item.label).drawAt(rect.center(), ColorF{ 0.25 });
		}
	}
}
```


## 55.13 XML の読み込み
- XML ファイルをパースしてデータを読み込むには `XMLReader` クラスを使います
- `XMLReader` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します
- ファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します
- 読み込みに成功したかどうかは、`if (xml)`, `if (not xml)` で調べられます
- XML データは次のサンプルの `ShowElements()` 関数のようにして再帰的に全要素を走査できます

```cpp
# include <Siv3D.hpp>

struct Item
{
	// アイテムのラベル
	String label;

	// アイテムの左上座標
	Point pos;
};

// 再帰的に XML の要素を表示
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
	// XML ファイルからデータを読み込む
	const XMLReader xml(U"example/xml/config.xml");

	if (not xml) // もし読み込みに失敗したら
	{
		throw Error{ U"Failed to load `config.xml`" };
	}

	// XML データをすべて表示
	ShowElements(xml);

	Console << U"-----";

	String windowTitle;
	int32 windowWidth = Window::DefaultClientSize.x;
	int32 windowHeight = Window::DefaultClientSize.y;
	bool windowSizable = false;
	ColorF sceneBackground{ 0.0 };
	Array<int32> values;
	Array<Item> items;

	// 要素を走査して目的の値を取得
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
					windowTitle = elem2.text();
				}
				else if (name2 == U"width")
				{
					windowWidth = Parse<int32>(elem2.text());
				}
				else if (name2 == U"height")
				{
					windowHeight = Parse<int32>(elem2.text());
				}
				else if (name2 == U"sizable")
				{
					windowSizable = Parse<bool>(elem2.text());
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
					sceneBackground = Parse<ColorF>(elem2.text());
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

	Window::SetTitle(windowTitle);
	Window::Resize(windowWidth, windowHeight);
	Window::SetStyle(windowSizable ? WindowStyle::Sizable : WindowStyle::Fixed);
	Scene::SetBackground(sceneBackground);

	Console << values;

	// アイテム描画用のフォント
	const Font font{ 30, Typeface::Bold };

	while (System::Update())
	{
		// アイテムを描画
		for (const auto& item : items)
		{
			const Rect rect{ item.pos, 180, 80 };

			rect.draw();

			font(item.label).drawAt(rect.center(), ColorF{ 0.25 });
		}
	}
}
```
