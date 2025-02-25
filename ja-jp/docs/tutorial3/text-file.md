# 54. テキストファイル
テキストファイルの内容を読み込んだり、テキストファイルに文字列を書き込んだりする方法を学びます。

!!! info "設定ファイルは専用のクラスを使う"
	- JSON, INI, CSV, XML, TOML などの設定ファイルを扱いたい場合は、**チュートリアル 55** で学ぶ、専用のクラスを使うと便利です

## 54.1 テキストファイルのオープン
- `TextReader 変数名{ U"ファイルパス" };` で、テキストファイルを読み取り専用でオープンできます
- ファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します
- Siv3D が対応するテキストファイルのエンコーディングは次のとおりです：
	- UTF-8
	- UTF-8（BOM あり）
	- UTF-16LE
	- UTF-16BE
- ファイルをオープンできたかを調べるには、`if (reader.isOpen())` や `if (reader)`, `if (not reader)` を使います
- オープンしたファイルはデストラクタでクローズされるため、明示的にクローズする必要はありません

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルをオープンする
	TextReader reader{ U"example/txt/en.txt" };

	// ファイルをオープンできなかったら
	if (not reader)
	{
		// 例外を投げて終了する
		throw Error{ U"Failed to open `en.txt`" };
	}

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```


## 54.2 1 行ずつ読み込む
- テキストファイルのオープン直後は、読み込み位置がファイルの先頭にセットされています
- `.readLine()` に `String` 型の変数を参照で渡すと、読み込み位置から次の改行またはファイル終端のうち先に来るほうまでを読み込んだ内容をその変数に格納し、読み込み位置をその直後まで進めて `true` を返します
- 読み込んだ文字列には改行文字は含まれません
- 読み込み位置がすでにファイルの終端にあり、これ以上読み込めないときには、空の文字列を格納し `false` を返します

### 例
- 例えば次のようなテキストファイルを読み込む場合を考えます
	- 1 回目の `.readLine()` では `abc`
	- 2 回目の `.readLine()` では `defg`
	- 3 回目の `.readLine()` では空の文字列
	- 4 回目の `.readLine()` では `hijklmn` が読み込まれます
	- それ以降の `.readLine()` の結果は空の文字列となり `false` を返します

```txt
abc
defg

hijklmn
```

| `.readLine()` の呼び出し | `.readLine()` の戻り値 | `String` 型の変数の内容 |
| --- | --- | --- |
| 1 回目 | `true` | `abc` |
| 2 回目 | `true` | `defg` |
| 3 回目 | `true` | 空の文字列 |
| 4 回目 | `true` | `hijklmn` |
| 5 回目以降 | `false` | 空の文字列 |

- `.readLine()` は、次のサンプルコードのように `while` ループと組み合わせると便利です

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルをオープンする
	TextReader reader{ U"example/txt/en.txt" };

	// ファイルをオープンできなかったら
	if (not reader)
	{
		// 例外を投げて終了する
		throw Error{ U"Failed to open `en.txt`" };
	}

	// 行の内容を読み込む変数
	String line;

	// 行数のカウント
	size_t count = 0;

	// 終端に達するまで 1 行ずつ読み込む
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


## 54.3 全部の内容を読み込む
- テキストファイルの内容を、全部まとめて `String` として取得するには `.readAll()` を使います
- ただし、非常に大きいサイズ（数 MB 以上）のファイルを `.readAll()` で読み込むと、関数が制御を返すまでの時間が長くなることがあるため、あらかじめサイズが小さいとわかっているファイルに対して使うことが望ましいです

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルをオープンする
	TextReader reader{ U"example/txt/en.txt" };

	// ファイルをオープンできなかったら
	if (not reader)
	{
		// 例外を投げて終了する
		throw Error{ U"Failed to open `en.txt`" };
	}

	// テキストファイルの内容をすべて読み込む
	const String text = reader.readAll();

	Print << text;

	while (System::Update())
	{

	}
}
```


## 54.4 オープン・クローズのタイミング制御
- コンストラクタでファイルをオープンする代わりに、`.open()` でファイルをオープンできます
- オープンに成功した場合は `true`, 失敗した場合は `false` を返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	TextReader reader;

	// en.txt をオープンする
	if (not reader.open(U"example/txt/en.txt"))
	{
		throw Error{ U"Failed to open `en.txt`" };
	}

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```

- ファイルが既にオープンされている場合、`.open()` は現在のファイルをクローズしてから指定されたファイルを改めてオープンします

```cpp
# include <Siv3D.hpp>

void Main()
{
	// en.txt をオープンする
	TextReader reader{ U"example/txt/en.txt" };

	// en.txt をクローズして jp.txt をオープンする
	if (not reader.open(U"example/txt/jp.txt"))
	{
		throw Error{ U"Failed to open `jp.txt`" };
	}

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```

- ファイルのクローズは、通常 `TextReader` のデストラクタで行われますが、ファイルの内容を読み込終えたあと、そのテキストファイルを削除したり、別の内容を改めて書き込んだりする場合、ファイルがオープンされたままでは操作ができないため、直ちにファイルをクローズする必要があります
- その場合は、`.close()` でファイルを明示的にクローズします
- ファイルがすでにクローズされている場合、以降の `.close()` やデストラクタは何もしません

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルをオープンする
	TextReader reader{ U"example/txt/en.txt" };

	Print << reader.isOpen();

	// ファイルを明示的にクローズする
	reader.close();

	Print << reader.isOpen();

	while (System::Update())
	{

	}
}
```


## 54.5 数値データの読み込み
- `.readLine()` と、**チュートリアル 36** で学んだパース関数を組み合わせることで、テキストファイルから数値データを読み込むことができます

### 54.5.1 各行に 1 つの数値がある場合

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

	// 数値を格納する配列
	Array<int32> values;

	String line;

	// 1 行ずつ読み込む
	while (reader.readLine(line))
	{
		// 読み込んだ文字列を数値に変換して配列に追加する
		values << Parse<int32>(line);
	}

	Print << values;

	while (System::Update())
	{

	}
}
```

### 54.5.2 各行に 1 つの数値がある場合（改良版）
- **54.5.1** の方法は、テキストファイルの途中に空白行が含まれていたり、末尾が 2 つ以上の改行で終わる場合に空の文字列を数値に変換しようとしてエラーが発生します
- これを回避するために、`.readLine()` で読み込んだ文字列が空の文字列の場合に処理をスキップするようにします

```cpp
# include <Siv3D.hpp>

void Main()
{
	TextReader reader{ U"test1.txt" };

	if (not reader)
	{
		throw Error{ U"Failed to open a file" };
	}

	// 数値を格納する配列
	Array<int32> values;

	String line;

	// 1 行ずつ読み込む
	while (reader.readLine(line))
	{
		// 空の文字列だった場合はスキップする
		if (not line)
		{
			continue;
		}
		
		// 読み込んだ文字列を数値に変換して配列に追加する
		values << Parse<int32>(line);
	}

	Print << values;

	while (System::Update())
	{

	}
}
```


### 54.5.3 各行に複数のデータがある場合
- テキストの 1 行に複数の情報を含む場合は、`String` のメンバ関数 `.split(delimiter)` （**チュートリアル 33.26**）を使って、1 行を複数の要素に分割します
- `.split(delimiter)` は、`delimiter` を区切り文字として分割した文字列を `Array<String>` として返します
- 例えば `U"123,456,789"` を `U','` で分割した場合や、`U"123 456 789"` を `U' '` で分割した場合は、`Array<String>{ U"123", U"456", U"789" }` が得られます。

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

	// データを格納する配列
	Array<Player> players;

	String line;

	// 1 行ずつ読み込む
	while (reader.readLine(line))
	{
		// 空の文字列だった場合はスキップする
		if (not line)
		{
			continue;
		}

		// 空白文字で分割する
		const Array<String> items = line.split(U' ');

		// 想定した要素数でなかった場合はエラーを投げる
		if (items.size() != 3)
		{
			throw Error{ U"Invalid format" };
		}

		// データを格納する
		players << Player{ items[0], Parse<int32>(items[1]), Parse<int32>(items[2]) };
	}

	// データを表示する
	for (const auto& player : players)
	{
		Print << U"{} (ID {}): {}"_fmt(player.name, player.id, player.score);
	}

	while (System::Update())
	{

	}
}
```

### 54.5.4 各行に複数のデータがある場合（改良版）
- **54.5.3** のプログラムについては、
	- データの読み込みが終わったらすぐにファイルをクローズする
	- データの異常に対応する
- といった処理を追加して関数化すると便利です
- これを実装したものが次のサンプルコードです：

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
	// 一旦クリアする
	players.clear();

	TextReader reader{ path };

	// ファイルをオープンできなかった場合は失敗
	if (not reader)
	{
		return false;
	}

	String line;

	while (reader.readLine(line))
	{
		// 空白行はスキップする
		if (not line)
		{
			continue;
		}

		// 空白で分割する
		const Array<String> items = line.split(U' ');

		// 想定した要素数でなかった場合は失敗
		if (items.size() != 3)
		{
			players.clear();
			return false;
		}

		// パースに失敗した場合は失敗
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
	// データを格納する配列
	Array<Player> players;

	// データを読み込む
	if (not LoadPlayers(U"test2.txt", players))
	{
		throw Error{ U"Failed to load players" };
	}

	// データを表示する
	for (const auto& player : players)
	{
		Print << U"{} (ID {}): {}"_fmt(player.name, player.id, player.score);
	}

	while (System::Update())
	{

	}
}
```


## 54.6 書き込み用のテキストファイルのオープン
- `TextWriter 変数名{ U"ファイルパス" };` で、テキストファイルを書き込み専用でオープンします
- ファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します
- 指定したパスのファイルが存在しない場合は、新しく空のファイルが作成されます
- 親ディレクトリが存在しない場合は、親ディレクトリも作成されます
- ファイルをオープンできたかを調べるには、`if (writer.isOpen())` や `if (writer)`, `if (not writer)` を使います
- オープンしたファイルはデストラクタでクローズされるため、明示的にクローズする必要はありません
- ファイルをオープン・クローズするタイミングを制御したい場合は、`TextReader` と同じように、`.open()` を使ってファイルをオープン、`.close()` を使ってファイルをクローズします

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 書き込み用のテキストファイルをオープンする
	TextWriter writer{ U"test3.txt" };

	// ファイルをオープンできなかったら
	if (not writer)
	{
		// 例外を投げて終了する
		throw Error{ U"Failed to open `test3.txt`" };
	}

	while (System::Update())
	{

	}

	// writer のデストラクタで自動的にファイルがクローズされる
}
```


## 54.7 テキストファイルへの書き込み
- `TextWriter` を使ってテキストファイルに文字列を書き込む方法は、次の 3 つがあります：
	- `Print` のように `<<` を使って書き込む
	- `.write(s)` を使って書き込む
	- `.writeln(s)` を使って書き込む
- 書き込む値はフォーマット可能であれば自動的に文字列に変換されます
- `<<` または `.writeln()` で書き込んだ場合、書き込みの最後には改行（`"\r\n"`）が自動で挿入されます
- 改行の挿入を避けたい場合は `.write()` を使います

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 書き込み用のテキストファイルをオープンする
	TextWriter writer{ U"test3.txt" };

	// ファイルをオープンできなかったら
	if (not writer)
	{
		// 例外を投げて終了する
		throw Error{ U"Failed to open `test3.txt`" };
	}

	// 文章を 1 行書き込む
	writer << U"Hello, Siv3D!";

	// 値や文字を　1 行書き込む
	writer << 123 << U", " << 456 << U", " << Point{ 10, 20 };

	// 1 文字書き込んで改行する
	writer.writeln(-3333);
	writer.writeln(1.234);
	writer.writeln(U"C++");

	// 値を書き込む（改行無し）
	writer.write(777);
	writer.write(U", ");
	writer.write(888);

	while (System::Update())
	{

	}
}
```

```txt title="書き込み結果"
Hello, Siv3D!
123, 456, (10, 20)
-3333
1.234
C++
777, 888
```


## 54.8 既存のテキストファイルへの追加書き込み
- 既存のテキストファイルの末尾に追加する形で書き込みを行いたい場合は、オープン時にファイルオープンモードとして `OpenMode::Append` (追加モード) を指定します
- 同名のテキストファイルが存在しなかった場合は、新しいファイルが作成されます
- 次のサンプルコードでは、プログラムを実行するたび、`test4.txt` に `Hello, Siv3D!` という文字列と現在時刻を追加書き込みします

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 書き込み用のテキストファイルを「追加モード」でオープンする
	TextWriter writer{ U"test4.txt", OpenMode::Append };

	// ファイルをオープンできなかったら
	if (not writer)
	{
		// 例外を投げて終了する
		throw Error{ U"Failed to open `test4.txt`" };
	}

	// 文章をと現在時刻を書き込む
	writer << U"Hello, Siv3D!";
	writer << DateTime::Now();

	while (System::Update())
	{

	}
}
```


## 54.9 スコープによる制御
- 次のサンプルコードでは、`TextWriter` によってオープンしたファイル `test5.txt` をクローズせずに `TextReader` でオープンしようとするため、オープンに失敗します

```cpp hl_lines="17-18"
# include <Siv3D.hpp>

void Main()
{
	// 書き込み用のテキストファイルをオープンする
	TextWriter writer{ U"test5.txt" };

	// ファイルをオープンできなかったら
	if (not writer)
	{
		Print << U"Error 1";
	}

	writer << U"Hello, Siv3D!";
	writer << DateTime::Now();

	// TextWriter でオープン中であるため、オープンに失敗する
	TextReader reader{ U"test5.txt" };

	// ファイルをオープンできなかったら
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

- 次のようにファイルのオープンとクローズのタイミングの制御を行う事で、問題を解決できます

```cpp hl_lines="17-18"
# include <Siv3D.hpp>

void Main()
{
	// 書き込み用のテキストファイルをオープンする
	TextWriter writer{ U"test5.txt" };

	// ファイルをオープンできなかったら
	if (not writer)
	{
		Print << U"Error 1";
	}

	writer << U"Hello, Siv3D!";
	writer << DateTime::Now();

	// ファイルをクローズする
	writer.close();

	// 読み込み用でファイルをオープンする
	TextReader reader{ U"test5.txt" };

	// ファイルをオープンできなかったら
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

- 上記のサンプルコードのように、同じファイルを指す `TextWriter` や `TextReader` を同一スコープ内に混在させると、コードが複雑になり、間違いやすいです
- そこで、`{ }` でスコープを作り、その中で限定して `TextWriter` や `TextReader` の変数を作成すると、コードの見通しが良くなります
- スコープの外では変数を使えないため、クローズされたファイルに対して誤って操作を行ってしまうことも防げます

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		// 書き込み用のテキストファイルをオープンする
		TextWriter writer{ U"test5.txt" };

		// ファイルをオープンできなかったら
		if (not writer)
		{
			Print << U"Error 1";
		}

		writer << U"Hello, Siv3D!";
		writer << DateTime::Now();
	}

	{
		// 読み込み用でファイルをオープンする
		TextReader reader{ U"test5.txt" };

		// ファイルをオープンできなかったら
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


## 54.10 エンコーディングを指定した書き込み
- `TextWriter` でテキストファイルを新規作成する際に、エンコーディングを指定できます
- エンコーディングを指定しなかった場合は、BOM 付きの UTF-8 が使われます

| コード | エンコーディング |
| --- | --- |
| `TextEncoding::UTF8_NO_BOM` | BOM なしの UTF-8 |
| `TextEncoding::UTF8_WITH_BOM` | BOM 付きの UTF-8 |
| `TextEncoding::UTF16LE` | BOM 付きの UTF-16 (LE) |
| `TextEncoding::UTF16BE` | BOM 付きの UTF-16 (BE) |

```cpp
# include <Siv3D.hpp>

void Main()
{
	// BOM 付き UTF-8
	{
		TextWriter writer{ U"test-utf8bom.txt" };
		writer.write(U"Siv3D");
	}

	// BOM なし UTF-8
	{
		TextWriter writer{ U"test-utf8.txt", TextEncoding::UTF8_NO_BOM };
		writer.write(U"Siv3D");
	}

	while (System::Update())
	{

	}
}
```


## 54.11 設定ファイルの書き出し
- `TextWriter` を使って、HTML や XML, JSON, INI, CSV などの設定ファイルをプログラムによって書き出すことができます

!!! info
	- CSV, INI, JSON, HTML ファイルの書き出しに関しては、専用のクラスが用意されています。詳しくは **チュートリアル 55** を参照してください

```cpp
# include <Siv3D.hpp>

void Main()
{
	// CSV ファイルを書き出す
	{
		TextWriter writer{ U"test.csv" };
		writer.writeln(U"name,id,score");
		writer.writeln(U"Alice,1,100");
		writer.writeln(U"Bob,2,80");
		writer.writeln(U"Carol,3,60");
	}

	// HTML ファイルを書き出す
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
