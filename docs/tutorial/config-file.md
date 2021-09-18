
!!! warning "This is the documentation for an old version"
	This is the documentation for an old version of Siv3D (v0.4.3). See [Siv3D Reference v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation-en) for the latest version.

# 19. Configuration file

この章では、CSV, INI, JSON, TOML, XML などの設定ファイルを読み込む方法を学びます。  
OpenSiv3D での設定の記述には、読みやすさと書きやすさに優れた TOML の使用を推奨します。

!!! warning
    OpenSiv3D v0.4.1 よりも古いバージョンには `if (!ini)` と `if (!csv)` が反対の結果を返すバグがあります。代わりに `if (!ini.isOpened())` `if (!csv.isOpened())` を使ってください。OpenSiv3D v0.4.1 で修正されました。

## 19.1 CSV ファイルからデータを読み込む
CSV ファイルをパースしてデータを読み込むには `CSVData` を使います。`CSVData` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが存在しない場合やパースに失敗した場合など、ロードに失敗したかどうかは `if (!csv)` で調べられます。

CSV データは `Array<Array<String>>` の形式で読み込まれ、添え字演算子 `[row][col]` によって row 行 col 列目のテキストを取得できます。row, col は 0 からカウントすることに注意しましょう。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/19/csv-0.png?raw=true)

```C++
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
	const CSVData csv(U"example/config/config.csv");

	if (!csv) // もし読み込みに失敗したら
	{
		throw Error(U"Failed to load `config.csv`");
	}

	// 全ての行を列挙
	for (size_t row = 0; row < csv.rows(); ++row)
	{
		// 行 (Array<String>) の要素を表示
		Print << csv[row];
	}
	Print << U"-----";

	// 1 行 1 列目のテキストを取得 (0 行, 0 列からカウント）
	const String windowTitle = csv[1][1];

	// 2 行 1 列目のテキストを int32 型の値に変換
	const int32 windowWidth = Parse<int32>(csv[2][1]);

	// 3 行 1 列目のテキストを int32 型の値に変換
	const int32 windowHeight = Parse<int32>(csv[3][1]);

	// 4 行 1 列目のテキストを bool 型の値に変換
	const bool windowSizable = Parse<bool>(csv[4][1]);

	// 5 行 1 列目のテキストを ColorF 型の値に変換
	const ColorF sceneBackground = Parse<ColorF>(csv[5][1]);

	Window::SetTitle(windowTitle);
	Window::Resize(windowWidth, windowHeight);
	Window::SetStyle(windowSizable ? WindowStyle::Sizable : WindowStyle::Fixed);
	Scene::SetBackground(sceneBackground);

	// 6 行 1 列目のテキストを ',' で区切って配列にし、それぞれの要素を int32 型の値に変換
	Array<int32> values = csv[6][1].split(U',').map(Parse<int32>);
	Print << values;

	// アイテムの配列を CSV データから作成
	Array<Item> items;
	{
		const size_t itemsCount = Parse<size_t>(csv[7][1]);
		const size_t baseLine = 8;

		for (auto i : step(itemsCount))
		{
			Item item;
			item.label = csv[baseLine + i * 2][1];
			item.pos = Parse<Point>(csv[baseLine + i * 2 + 1][1]);
			items << item;
		}
	}

	// アイテム描画用のフォント
	const Font font(30, Typeface::Bold);

	while (System::Update())
	{
		// アイテムを描画
		for (const auto& item : items)
		{
			const Rect rect(item.pos, 180, 80);

			rect.draw();

			font(item.label).drawAt(rect.center(), ColorF(0.25));
		}
	}
}
```


## 19.2 INI ファイルからデータを読み込む
INI ファイルをパースしてデータを読み込むには `INIData` を使います。`INIData` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが存在しない場合やパースに失敗した場合など、ロードに失敗したかどうかは `if (!ini)` で調べられます。

INI データはセクションごとに `HashTable<String, String>` の形式で読み込まれ、添え字演算子 `[U"SECTION.NAME"]` によってセクション SECTION にある名前 NAME のテキストを取得できます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/19/ini-0.png?raw=true)

```C++
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
	const INIData ini(U"example/config/config.ini");

	if (!ini) // もし読み込みに失敗したら
	{
		throw Error(U"Failed to load `config.ini`");
	}

	// 全てのセクションを列挙
	for (const auto& section : ini.sections())
	{
		// セクション名
		Print << U"[{}]"_fmt(section.section);

		// セクション内のすべてのキーを列挙
		for (const auto& key : section.keys)
		{
			// キーの名前と値
			Print << U"{} = {}"_fmt(key.name, key.value);
		}
	}
	Print << U"-----";

	// Windows セクションの title キーの値（テキスト）を取得
	const String windowTitle = ini[U"Window.title"];

	// Windows セクションの width キーの値（テキスト）を int32 型の値に変換
	const int32 windowWidth = Parse<int32>(ini[U"Window.width"]);

	// Windows セクションの height キーの値（テキスト）を int32 型の値に変換
	const int32 windowHeight = Parse<int32>(ini[U"Window.height"]);
	
	// Windows セクションの sizable キーの値（テキスト）を bool 型の値に変換
	const bool windowSizable = Parse<bool>(ini[U"Window.sizable"]);
	
	// Scene セクションの background キーの値（テキスト）を ColorF 型の値に変換
	const ColorF sceneBackground = Parse<ColorF>(ini[U"Scene.background"]);

	Window::SetTitle(windowTitle);
	Window::Resize(windowWidth, windowHeight);
	Window::SetStyle(windowSizable ? WindowStyle::Sizable : WindowStyle::Fixed);
	Scene::SetBackground(sceneBackground);

	// Array セクションの values キーの値（テキスト）を ',' で区切って配列にし、それぞれの要素を int32 型の値に変換
	Array<int32> values = ini[U"Array.values"].split(U',').map(Parse<int32>);
	Print << values;

	// アイテムの配列を INI データから作成
	Array<Item> items;
	{
		const size_t itemsCount = Parse<size_t>(ini[U"Items.count"]);

		for (auto i : step(itemsCount))
		{
			Item item;
			item.label = ini[U"Item{}.label"_fmt(i)];
			item.pos = Parse<Point>(ini[U"Item{}.pos"_fmt(i)]);
			items << item;
		}
	}

	// アイテム描画用のフォント
	const Font font(30, Typeface::Bold);

	while (System::Update())
	{
		// アイテムを描画
		for (const auto& item : items)
		{
			const Rect rect(item.pos, 180, 80);

			rect.draw();

			font(item.label).drawAt(rect.center(), ColorF(0.25));
		}
	}
}
```


## 19.3 JSON ファイルからデータを読み込む
JSON ファイルをパースしてデータを読み込むには `JSONReader` を使います。`JSONReader` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが存在しない場合やパースに失敗した場合など、ロードに失敗したかどうかは `if (!json)` で調べられます。

JSON データは次のサンプルの `ShowObject()` 関数のようにして再帰的に全要素を走査できます。また、添え字演算子 `[U"NAME1.NAME2.NAME3..."]` によってパスを指定して目的の値を直接得ることもできます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/19/json-0.png?raw=true)

```C++
# include <Siv3D.hpp>

struct Item
{
	// アイテムのラベル
	String label;

	// アイテムの左上座標
	Point pos;
};

// 再帰的に JSON の要素を表示
void ShowObject(const JSONValue& value)
{
	for (const auto& object : value.objectView())
	{
		switch (object.value.getType())
		{
		case JSONValueType::Empty:
			Print << U"[Empty]" << object.name;
			break;
		case JSONValueType::Null:
			Print << U"[Null]" << object.name;
			break;
		case JSONValueType::Object:
			Print << U"[Object]" << object.name;
			ShowObject(object.value);
			break;
		case JSONValueType::Array:
			Print << U"[Array]" << object.name;
			for (const auto& element : object.value.arrayView())
			{
				ShowObject(element);
			}
			break;
		case JSONValueType::String:
			Print << U"[String]" << object.name;
			Print << object.value.getString();
			break;
		case JSONValueType::Number:
			Print << U"[Number]" << object.name;
			Print << object.value.get<double>();
			break;
		case JSONValueType::Bool:
			Print << U"[Bool]" << object.name;
			Print << object.value.get<bool>();
			break;
		}
	}
}

void Main()
{
    // JSON ファイルからデータを読み込む
	const JSONReader json(U"example/config/config.json");

	if (!json) // もし読み込みに失敗したら
	{
		throw Error(U"Failed to load `config.json`");
	}

	// JSON データをすべて表示
	ShowObject(json);

	Print << U"-----";

	// 要素のパスで値を取得
	const String windowTitle = json[U"Window.title"].getString();
	const int32 windowWidth = json[U"Window.width"].get<int32>();
	const int32 windowHeight = json[U"Window.height"].get<int32>();
	const bool windowSizable = json[U"Window.sizable"].get<bool>();
	const ColorF sceneBackground = json[U"Scene.background"].get<ColorF>();

	Window::SetTitle(windowTitle);
	Window::Resize(windowWidth, windowHeight);
	Window::SetStyle(windowSizable ? WindowStyle::Sizable : WindowStyle::Fixed);
	Scene::SetBackground(sceneBackground);

	// 数値の配列を JSON データから作成
	Array<int32> values;
	{
		for (const auto& object : json[U"Array.values"].arrayView())
		{
			values << object.get<int32>();
		}
	}
	Print << values;

	// アイテムの配列を JSON データから作成
	Array<Item> items;
	{
		for (const auto& object : json[U"Items"].arrayView())
		{
			Item item;
			item.label = object[U"label"].getString();
			item.pos = Point(object[U"pos.x"].get<int32>(), object[U"pos.y"].get<int32>());
			items << item;
		}
	}

	// アイテム描画用のフォント
	const Font font(30, Typeface::Bold);

	while (System::Update())
	{
		// アイテムを描画
		for (const auto& item : items)
		{
			const Rect rect(item.pos, 180, 80);

			rect.draw();

			font(item.label).drawAt(rect.center(), ColorF(0.25));
		}
	}
}
```


## 19.4 TOML ファイルからデータを読み込む
TOML ファイルをパースしてデータを読み込むには `TOMLReader` を使います。`TOMLReader` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが存在しない場合やパースに失敗した場合など、ロードに失敗したかどうかは `if (!toml)` で調べられます。

TOML データは次のサンプルの `ShowObject()` 関数のようにして再帰的に全要素を走査できます。また、添え字演算子 `[U"NAME1.NAME2.NAME3..."]` によってパスを指定して目的の値を直接得ることもできます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/19/toml-0.png?raw=true)

```C++
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
			Print << U"[Empty]" << table.name;
			break;
		case TOMLValueType::Table:
			Print << U"[Table]" << table.name;
			ShowTable(table.value);
			break;
		case TOMLValueType::Array:
			Print << U"[Array]" << table.name;
			for (const auto& element : table.value.arrayView())
			{
				switch (element.getType())
				{
				case TOMLValueType::String:
					Print << element.getString();
					break;
				case TOMLValueType::Number:
					Print << element.get<double>();
					break;
				case TOMLValueType::Bool:
					Print << element.get<bool>();
					break;
				case TOMLValueType::Date:
					Print << element.getDate();
					break;
				case TOMLValueType::DateTime:
					Print << element.getDateTime();
					break;
				default:
					break;
				}
			}
			break;
		case TOMLValueType::TableArray:
			Print << U"[TableArray]" << table.name;
			for (const auto& table2 : table.value.tableArrayView())
			{
				ShowTable(table2);
			}
			break;
		case TOMLValueType::String:
			Print << U"[String]" << table.name;
			Print << table.value.getString();
			break;
		case TOMLValueType::Number:
			Print << U"[Number]" << table.name;
			Print << table.value.get<double>();
			break;
		case TOMLValueType::Bool:
			Print << U"[Bool]" << table.name;
			Print << table.value.get<bool>();
			break;
		case TOMLValueType::Date:
			Print << U"[Date]" << table.name;
			Print << table.value.getDate();
			break;
		case TOMLValueType::DateTime:
			Print << U"[DateTime]" << table.name;
			Print << table.value.getDateTime();
			break;
		case TOMLValueType::Unknown:
			Print << U"[Unknown]" << table.name;
			break;
		}
	}
}

void Main()
{
	// TOML ファイルからデータを読み込む
	const TOMLReader toml(U"example/config/config.toml");

	if (!toml) // もし読み込みに失敗したら
	{
		throw Error(U"Failed to load `config.toml`");
	}

	// TOML データをすべて表示
	ShowTable(toml);

	Print << U"-----";

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
	Print << values;

	// アイテムの配列を TOML データから作成
	Array<Item> items;
	{
		for (const auto& object : toml[U"Items"].tableArrayView())
		{
			Item item;
			item.label = object[U"label"].getString();
			item.pos = Point(object[U"pos.x"].get<int32>(), object[U"pos.y"].get<int32>());
			items << item;
		}
	}

	// アイテム描画用のフォント
	const Font font(30, Typeface::Bold);

	while (System::Update())
	{
		// アイテムを描画
		for (const auto& item : items)
		{
			const Rect rect(item.pos, 180, 80);

			rect.draw();

			font(item.label).drawAt(rect.center(), ColorF(0.25));
		}
	}
}
```


## 19.5 XML ファイルからデータを読み込む
XML ファイルをパースしてデータを読み込むには `XMLReader` を使います。`XMLReader` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが存在しない場合やパースに失敗した場合など、ロードに失敗したかどうかは `if (!xml)` で調べられます。

XML データは次のサンプルの `ShowElements()` 関数のようにして再帰的に全要素を走査できます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/19/xml-0.png?raw=true)

```C++
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
		Print << U"<{}>"_fmt(e.name());

		if (const auto attributes = e.attributes())
		{
			Print << attributes;
		}

		if (const auto text = e.text())
		{
			Print << text;
		}

		ShowElements(e);

		Print << U"</{}>"_fmt(e.name());
	}
}

void Main()
{
	// XML ファイルからデータを読み込む
	const XMLReader xml(U"example/config/config.xml");

	if (!xml) // もし読み込みに失敗したら
	{
		throw Error(U"Failed to load `config.xml`");
	}

	// XML データをすべて表示
	ShowElements(xml);

	Print << U"-----";

	String windowTitle;
	int32 windowWidth = Window::DefaultClientSize.x;
	int32 windowHeight = Window::DefaultClientSize.y;
	bool windowSizable = false;
	ColorF sceneBackground = ColorF(0.0);
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
					Point pos(0, 0);

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

	Print << values;

	// アイテム描画用のフォント
	const Font font(30, Typeface::Bold);

	while (System::Update())
	{
		// アイテムを描画
		for (const auto& item : items)
		{
			const Rect rect(item.pos, 180, 80);

			rect.draw();

			font(item.label).drawAt(rect.center(), ColorF(0.25));
		}
	}
}
```


## 19.6 ファイルの更新を検知してリロードする
`DirectoryWatcher` を使うと、指定したディレクトリ内でのファイルの変更イベントを検知できます。設定ファイルを `DirectoryWatcher` で監視し、ファイルが更新されたときに読み込みなおす仕組みを導入することで、プログラムを実行しながら設定ファイルの変更を反映させることができます。

```C++
# include <Siv3D.hpp>

struct Item
{
	// アイテムのラベル
	String label;

	// アイテムの左上座標
	Point pos;
};

// アイテム情報を読み込む関数
Array<Item> LoadItems(const FilePath& tomlPath)
{
	const TOMLReader toml(tomlPath);

	Array<Item> items;

	if (!toml)
	{
		return items;
	}

	for (const auto& object : toml[U"Items"].tableArrayView())
	{
		Item item;
		item.label = object[U"label"].getString();
		item.pos = Point(object[U"pos.x"].get<int32>(), object[U"pos.y"].get<int32>());
		items << item;
	}

	return items;
}

void Main()
{
	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));

	// DirectoryWatcher でのチェックのためにフルパスが必要
	const FilePath tomlPath = FileSystem::FullPath(U"example/config/config.toml");

	// `config.toml` が存在するディレクトリの変更の監視
	DirectoryWatcher watcher(FileSystem::ParentPath(tomlPath));

	// アイテム情報をロード
	Array<Item> items = LoadItems(tomlPath);

	// アイテム描画用のフォント
	const Font font(30, Typeface::Bold);

	while (System::Update())
	{
		// ディレクトリ内の変更イベントをチェック
		for (const auto& change : watcher.retrieveChanges())
		{
			if (change.first == tomlPath
				&& change.second == FileAction::Modified) // TOML ファイルが更新されたら
			{
				Print << U"reload";

				// アイテム情報を再読み込み
				items = LoadItems(tomlPath);
			}
		}

		// アイテムを描画
		for (const auto& item : items)
		{
			const Rect rect(item.pos, 180, 80);

			rect.draw();

			font(item.label).drawAt(rect.center(), ColorF(0.25));
		}
	}
}
```

