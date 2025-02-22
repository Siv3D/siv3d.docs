# 56. バイナリファイル
バイナリ形式でデータの読み書きを行う方法を学びます。

## 56.1 バイナリファイル

### 56.1.1 テキストファイルとバイナリファイル
- **バイナリファイル**は、データをコンピュータが直接処理できるバイナリデータ形式で保存したファイルです
- 例えば `299792458` という `int32` 型の数値をファイルに保存する場合：
	- テキスト形式では、ASCII文字列 "299792458" として 9 バイトで保存します
	- バイナリ形式では、32 ビットの整数値として 4 バイトで保存します（`00010001110111100111100001001010`）

#### テキストファイル
- メリット
	- 人間が読み書きしやすい
	- テキストエディタで内容を確認・編集できる
- デメリット
	- 数値 ⇔ テキストの変換処理でオーバーヘッドが生じる
	- ファイルサイズが大きくなる

#### バイナリファイル
- メリット
	- プログラムによる読み書きが高速
	- ファイルサイズを最小限に抑えられる
- デメリット
	- 人間が直接内容を確認しづらい

### 56.1.2 シリアライズの利用
- 組み込み型（`int32`, `double` など）や、組み込み型で構成された単純なクラス（`Point`, `Vec2`, `Rect`, `ColorF` など）は、メモリ上のバイト列を直接ファイルに書き込み可能です（*trivially copyable*）
- 一方で、複雑なデータ構造（`String`, `Array` など）は、ポインタやメモリ管理を含むため、直接バイナリ化できません。**56.7～56.9** で説明する **シリアライズ機能** を使って、適切なバイナリデータに変換する必要があります
- 自作のクラスについても、シリアライズ機能を使ってバイナリファイルでの入出力に対応させることができます


## 56.2 バイナリデータの書き込み
- バイナリファイルを作成するには `BinaryWriter` クラスを使います
- `BinaryWriter` のコンストラクタ引数に、書き込み先のファイルのパスを渡します
- ファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します
- ファイルのオープンに成功したかどうかは、`if (writer)`, `if (not writer)` で調べられます
- 同名のファイルがすでに存在する場合は、それを破棄して新しいファイルを作成します
- オープンしたファイルはデストラクタでクローズされるため、明示的にクローズする必要はありません
- オープン・クローズのタイミングを制御したい場合は、`TextWriter` （**チュートリアル 54**）と同じように、`.open()` を使ってファイルをオープン、`.close()` を使ってファイルをクローズします
- `.write()` に *trivially copyable* な型の値を渡すと、その値のバイナリデータをファイルの末尾に追加で書き込みます
- 次のサンプルコードを実行すると、36 バイトのバイナリファイルが作成されます

```cpp
# include <Siv3D.hpp>

// ゲームのスコアを記録する構造体
struct GameScore
{
	int32 a, b, c, d;
};

void Main()
{
	// 書き込み用のバイナリファイルをオープン
	BinaryWriter writer{ U"tutorial1.bin" };

	if (not writer) // もしオープンに失敗したら
	{
		throw Error{ U"Failed to open `tutorial1.bin`" };
	}

	// int32 型の値 (4 バイト) を書き込む
	writer.write(777);

	// double 型の値 (8 バイト) を書き込む
	writer.write(3.1415);

	// Point 型の値 (8 バイト) を書き込む
	writer.write(Point{ 123, 456 });

	// GameScore 型の値 (16 バイト) を書き込む
	const GameScore s = { 10, 20, 30, 40 };
	writer.write(s);

	while (System::Update())
	{

	}

	// writer のデストラクタで自動的にファイルがクローズされる
}
```


## 56.2 バイナリデータの読み込み
- バイナリファイルからバイナリデータを読み込むには `BinaryReader` クラスを使います
- `BinaryReader` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します
- ファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します
- ファイルのオープンに成功したかどうかは、`if (reader)`, `if (not reader)` で調べられます
- オープン直後は、読み込み位置はファイルの先頭にセットされています
- `.read()` に *trivially copyable* な型の変数を参照で渡すと、読み込み位置を始点とし、その値のサイズ分のバイナリデータを読み込んでその変数にコピーし、読み込み位置をその分進めて `true` を返します
- 読み込み位置がすでにファイルの終端にあって、これ以上ファイルから読み込めないときには `false` を返します
- 次のサンプルコードでは、**56.1** で作成したバイナリファイルに保存されている値を順に読み出します

```cpp
# include <Siv3D.hpp>

// ゲームのスコアを記録する構造体
struct GameScore
{
	int32 a, b, c, d;
};

void Main()
{
	BinaryReader reader{ U"tutorial1.bin" };

	// もしオープンに失敗したら
	if (not reader)
	{
		throw Error{ U"Failed to open `tutorial1.bin`" };
	}

	// int32 型の値 (4 バイト) を読み込む
	int32 n;
	reader.read(n);
	Print << n;

	// double 型の値 (8 バイト) を読み込む
	double d;
	reader.read(d);
	Print << d;

	// Point 型の値 (8 バイト) を読み込む
	Point pos;
	reader.read(pos);
	Print << pos;

	// GameScore 型の値 (16 バイト) を読み込む
	GameScore s;
	reader.read(s);
	Print << U"{}, {}, {}, {}"_fmt(s.a, s.b, s.c, s.d);

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```
```txt title="出力"
777
3.1415
(123, 456)
10, 20, 30, 40
```


## 56.3 読み込み位置の変更
- `BinaryReader` の `.setPos(pos)` で、読み込み位置を指定した場所に移動できます
- `.skip(size)` を使うと、指定したサイズ分読み飛ばすことができます
- 次のサンプルでは、**56.1** で作成したバイナリファイルに保存されている値の一部だけを読み出します

```cpp
# include <Siv3D.hpp>

// ゲームのスコアを記録する構造体
struct GameScore
{
	int32 a, b, c, d;
};

void Main()
{
	BinaryReader reader{ U"tutorial1.bin" };

	// もしオープンに失敗したら
	if (not reader)
	{
		throw Error{ U"Failed to open `tutorial1.bin`" };
	}

	// 先頭から 4 バイト進んだ位置に移動する
	reader.setPos(4);

	// double 型の値 (8 バイト) を読み込む
	double d;
	reader.read(d);
	Print << d;

	// 8 バイト分読み飛ばす
	reader.skip(8);

	// GameScore 型の値 (16 バイト) を読み込む
	GameScore s;
	reader.read(s);
	Print << U"{}, {}, {}, {}"_fmt(s.a, s.b, s.c, s.d);

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```
```txt title="出力"
3.1415
10, 20, 30, 40
```


## 56.4 複雑なデータの書き込み（シリアライズを使わない場合）
- `String` や `Array` のように単純にコピーできない (`trivially copyable` でない) データを、シリアライズ機能を使わずにバイナリファイルに書き込む例です
- `.write(データの先頭ポインタ, データのサイズ)` で、データの先頭ポインタから指定したサイズ分のデータを書き込みます

```cpp
# include <Siv3D.hpp>

void Main()
{
	BinaryWriter writer{ U"tutorial2.bin" };

	// もしオープンに失敗したら
	if (not writer)
	{
		throw Error{ U"Failed to open `tutorial2.bin`" };
	}

	// 書き込みたいテキスト
	const String text = U"Hello, Siv3D";

	// 書き込みたいテキストの長さ
	const uint64 length = text.length();

	// テキストの長さを書き込む (8 バイト)
	writer.write(length);

	// 格納されてるデータの先頭ポインタから
	// (4 バイト × 長さ）分のテキストデータを書き込む 
	writer.write(text.data(), (sizeof(char32) * length));

	while (System::Update())
	{

	}

	// writer のデストラクタで自動的にファイルがクローズされる
}
```


## 56.5 複雑なデータの読み込み（シリアライズを使わない場合）
- `String` や `Array` のように単純にコピーできない (`trivially copyable` でない) データを、シリアライズ機能を使わずにバイナリファイルから読み込む例です
- `.read(格納先の先頭ポインタ, データのサイズ)` で、ファイルから指定したサイズ分のデータを読み込み、指定された格納先にコピーします
- 次のサンプルコードでは、**56.4** で作成したバイナリファイルに保存されている `String` を読み込みます

```cpp
# include <Siv3D.hpp>

void Main()
{
	BinaryReader reader{ U"tutorial2.bin" };

	// もしオープンに失敗したら
	if (not reader)
	{
		throw Error{ U"Failed to open `tutorial2.bin`" };
	}

	// 読み込むテキストの長さを格納する変数
	uint64 length = 0;

	// 読み込んだテキストの格納先
	String text;

	// テキストの長さを読み込む
	reader.read(length);

	if (0 < length)
	{
		// テキストデータを格納するためにリサイズする
		text.resize(length);

		// テキストのサイズ分だけデータを読み込む
		reader.read(text.data(), (sizeof(char32) * length));
	}

	Print << U"length: " << length;;
	Print << U"text: " << text;

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```


## 56.6 複雑なデータの書き込み（シリアライズ）






シリアライズ機能を使うと、シリアライズに対応したデータ型 (`trivially copyable` でない型も含む) を、バイナリファイルで扱いやすくなります。ファイルにシリアライズ機能を使ってバイナリデータを書き込むには `Serializer<BinaryWriter>` クラスの機能を使います。`Serializer<BinaryWriter>` のコンストラクタ引数に、書き込み先のファイルのパスを渡します。実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが使用中だったり、ファイル名が不正なものだったりしてオープンに失敗したかどうかは `if (not writer)` で調べられます。

`Serializer<BinaryWriter>` に `()` で値を渡すと、そのデータをシリアライズしてファイルの末尾に追加で書き込みます。シリアライズに対応していない型を渡した場合はビルドエラーになります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 書き込み用のバイナリファイルをオープン
	Serializer<BinaryWriter> writer{ U"tutorial3.bin" };

	if (not writer) // もしオープンに失敗したら
	{
		throw Error{ U"Failed to open `tutorial3.bin`" };
	}

	// 書き込みたいテキスト
	const String text = U"Hello, Siv3D";

	// 書き込みたいデータ
	int a = 123, b = 456;

	// バイナリファイルにシリアライズ対応型のデータを書き込む
	writer(text);

	// まとめて記述することもできる
	writer(a, b);

	while (System::Update())
	{

	}

	// writer のデストラクタで自動的にファイルがクローズされる
}
```


## 56.7 複雑なデータの読み込み（シリアライズ）
シリアライズ機能を使って書き込んだデータを読み込むには `Deserializer<BinaryReader>` クラスの機能を使います。`Deserializer<BinaryReader>` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。リリース用のアプリを作るときには埋め込みリソースパスの使用を推奨します。ファイルが存在しない場合など、オープンに失敗したかどうかは `if (not reader)` で調べられます。

`Deserializer<BinaryReader>` に `()` で変数の参照を渡すと、ファイルからデータをデシリアライズし、その値に代入します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// バイナリファイルをオープン
	Deserializer<BinaryReader> reader{ U"tutorial3.bin" };

	if (not reader) // もしオープンに失敗したら
	{
		throw Error{ U"Failed to open `tutorial3.bin`" };
	}

	// 読み込み先のテキスト
	String text;

	// 読み込み先の変数
	int32 a, b;

	// バイナリファイルからシリアライズ対応型のデータを読み込む
	// （文字列や Array は自動でリサイズが行われる）
	reader(text);
	reader(a, b);

	Print << text.length();

	Print << text;

	Print << a << U", " << b;

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```


## 56.8 自作クラスのシリアライズ対応
ユーザが定義したクラスをシリアライズに対応させるには、`template <class Archive> void SIV3D_SERIALIZE(Archive& archive)` という public メンバ関数をクラスに実装します。`archive()` に、シリアライズに対応したオブジェクトを渡すコードを書くと、そのクラスはシリアライズ可能になり、`Serializer` や `Deserializer` で使用できるようになります。

```cpp
# include <Siv3D.hpp>

// ユーザデータとゲームのスコアを記録する構造体
struct GameScore
{
	String name;

	int32 id;

	int32 score;

	// シリアライズに対応させるためのメンバ関数を定義する
	template <class Archive>
	void SIV3D_SERIALIZE(Archive& archive)
	{
		archive(name, id, score);
	}
};

void Main()
{
	{
		// 記録したいデータ
		const Array<GameScore> scores =
		{
			{ U"Player1", 111, 1000 },
			{ U"Player2", 222, 2000 },
			{ U"Player3", 333, 3000 },
		};

		// バイナリファイルをオープン
		Serializer<BinaryWriter> writer{ U"tutorial4.bin" };

		if (not writer) // もしオープンに失敗したら
		{
			throw Error{ U"Failed to open `tutorial4.bin`" };
		}

		// シリアライズに対応したデータを記録
		writer(scores);

		// writer のデストラクタで自動的にファイルがクローズされる
	}

	// 読み込み先のデータ
	Array<GameScore> scores;
	{
		// バイナリファイルをオープン
		Deserializer<BinaryReader> reader{ U"tutorial4.bin" };

		if (not reader) // もしオープンに失敗したら
		{
			throw Error{ U"Failed to open `tutorial4.bin`" };
		}

		// バイナリファイルからシリアライズ対応型のデータを読み込む
		// （Array は自動でリサイズが行われる）
		reader(scores);

		// reader のデストラクタで自動的にファイルがクローズされる
	}

	// 読み込んだスコアを確認
	for (const auto& score : scores)
	{
		Print << U"{}(id: {}): {}"_fmt(score.name, score.id, score.score);
	}

	while (System::Update())
	{

	}
}
```

