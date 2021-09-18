
!!! warning "This is the documentation for an old version"
	This is the documentation for an old version of Siv3D (v0.4.3). See [Siv3D Reference v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation-en) for the latest version.

# 21. Binary file

この章では、データを最小限のコストでファイルとやり取りする方法を学びます。

## 21.1 バイナリファイルとは

**バイナリファイル** は、データをテキストではなく **バイナリデータ** 形式で保存したものです。例えば `299792458` という `int32` 型の数値は、`"299792458"` という 9 文字（9 バイト) ではなく、`00010001110111100111100001001010` のビット列で表される 4 バイトのデータとして保存します。

データをファイルに保存する際、テキスト形式で保存すると、テキストから数値、数値からテキストへの変換にコストがかかり、必要なサイズも大きくなります。一方、バイナリデータは変換のコストがかからず、コンパクトな固定サイズのデータ容量しか必要としません。

`int32` や `double` などのプリミティブ型や、プリミティブ型で構成された `trivially copyable` なクラス (`Point`, `Vec2`, `Rect`, `ColorF`) などは、単純にデータをコピーするだけで容易にバイナリデータとして扱えますが、`Array` や `String` など、ポインタで内部データを管理するデータ型をバイナリデータとして適切に扱には少し手間がかかります。

この章の後半て説明する **シリアライズ機能** を使うと、`Array` や `String`, その他いくつかの Siv3D の `trivially copyable` でないクラスを簡単にバイナリデータとして扱えるようになります。独自に定義した型をシリアライズに対応させることもできます。

## 21.2 バイナリファイルに単純な値を書き込む
ファイルにバイナリデータを書き込むには `BinaryWriter` の機能を使います。`BinaryWriter` のコンストラクタ引数に、書き込み先のファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが使用中だったり、ファイル名が不正なものだったりしてオープンに失敗したかどうかは `if (!writer)` で調べられます。

同名のファイルがすでに存在する場合はそれを破棄してからオープンし、存在しない場合は新しい空のファイルを作成してオープンします。

`.BinaryWriter::write()` に `trivially copyable` な型の値を渡すと、その値のバイナリデータをコピーしてファイルの末尾に追加で書き込みます。

```C++
# include <Siv3D.hpp>

// ゲームのスコアを記録する構造体
struct GameScore
{
	int32 a, b, c, d;
};

void Main()
{
	// 書き込み用のバイナリファイルをオープン
	BinaryWriter writer(U"test.bin");

	if (!writer) // もしオープンに失敗したら
	{
		throw Error(U"Failed to open `test.bin`");
	}

	// int32 型の値 (4 バイト) を書き込む
	writer.write(777);

	// double 型の値 (8 バイト) を書き込む
	writer.write(3.1415);

	// Point 型の値 (8 バイト) を書き込む
	writer.write(Point(123, 456));

	// GameScore 型の値 (16 バイト) を書き込む
	const GameScore s = { 10, 20, 30, 40 };
	writer.write(s);

	while (System::Update())
	{

	} 

	// writer のデストラクタで自動的にファイルがクローズされる
}
```

## 21.3 バイナリファイルから単純な値を読み込む
バイナリファイルからバイナリデータを読み込むには `BinaryReader` の機能を使います。`BinaryReader` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。リリース用のアプリを作るときには埋め込みリソースパスの使用を推奨します。ファイルが存在しない場合など、オープンに失敗したかどうかは `if (!reader)` で調べられます。

オープン直後は、読み込み位置はファイルの先頭にセットされています。`BinaryReader::read()` に `trivially copyable` な型の変数を参照で渡すと、読み込み位置を始点とし、その値のサイズ分のバイナリデータを読み込んでその変数にコピーし、読み込み位置をその分進めて `true` を返します。読み込み位置がすでにファイルの終端にあって、これ以上読み込めないときには `false` を返します。

```C++
# include <Siv3D.hpp>

// ゲームのスコアを記録する構造体
struct GameScore
{
	int32 a, b, c, d;
};

void Main()
{
	// バイナリファイルをオープン
	BinaryReader reader(U"test.bin");

	if (!reader) // もしオープンに失敗したら
	{
		throw Error(U"Failed to open `test.bin`");
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


### 読み込み位置の移動
`BinaryReader::setPos()` で、読み込み位置を指定した場所に移動できます。
`BinaryReader::skip()` で、指定したサイズ分読み飛ばすことができます。

```C++
# include <Siv3D.hpp>

// ゲームのスコアを記録する構造体
struct GameScore
{
	int32 a, b, c, d;
};

void Main()
{
	// バイナリファイルをオープン
	BinaryReader reader(U"test.bin");

	if (!reader) // もしオープンに失敗したら
	{
		throw Error(U"Failed to open `test.bin`");
	}

	// 先頭から 4 バイト進んだ位置に移動
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

## 21.4 ファイルをオープン・クローズするタイミングを制御する
`BinaryReader` のコンストラクタでファイルをオープンせずに、`BinaryReader::open()` でファイルをオープンすることもできます。オープンに成功した場合は `true`, 失敗した場合は `false` を返します。

また通常は `BinaryReader` のデストラクタが実行されるタイミングでファイルがクローズされますが、例えば内容を読み込んだあとにファイルを削除したり、別の内容を書き込みたい場合には、ファイルがオープンされたままだと操作ができないため、すぐにクローズしたいというケースがあるでしょう。そうしたときには `BinaryReader::close()` でファイルを明示的にクローズします。

```C++
# include <Siv3D.hpp>

void Main()
{
	BinaryReader reader;

	if (!reader.open(U"test.bin")) // もしオープンに失敗したら
	{
		throw Error(U"Failed to open `test.bin`");
	}

	// int32 型の値 (4 バイト) を読み込む
	int32 n;
	reader.read(n);
	Print << n;

	// ファイルを明示的にクローズ
	reader.close();

	while (System::Update())
	{

	}
}
```


## 21.5 バイナリファイルに複雑なデータを書き込む
`String` や `Array` のように単純にコピーできない (`trivially copyable` でない) データをバイナリファイルで扱うのは少し手間がかかります。後述するシリアライズ機能を使えば簡単になりますが、まずはシリアライズ機能を使わないサンプルを紹介します。

```C++
# include <Siv3D.hpp>

void Main()
{
	// 書き込み用のバイナリファイルをオープン
	BinaryWriter writer(U"test-no-serialize.bin");

	if (!writer) // もしオープンに失敗したら
	{
		throw Error(U"Failed to open `test-no-serialize.bin`");
	}

	// 書き込みたいテキスト
	const String text = U"Hello, Siv3D";

	// 書き込みたいテキストの長さ
	const uint64 length = text.length();

	// テキストの長さを書き込む
	writer.write(length);

	// テキストデータを書き込む
	writer.write(text.data(), sizeof(char32) * length);

	while (System::Update())
	{

	}

	// writer のデストラクタで自動的にファイルがクローズされる
}
```


## 21.6 バイナリファイルから複雑なデータを読み込む
前項に続いて、`String` や `Array` のように単純にコピーできない (`trivially copyable` でない) データを、シリアライズ機能を使わずにバイナリファイルで扱う方法です。

```C++
# include <Siv3D.hpp>

void Main()
{
	// バイナリファイルをオープン
	BinaryReader reader(U"test-no-serialize.bin");

	if (!reader) // もしオープンに失敗したら
	{
		throw Error(U"Failed to open `test-no-serialize.bin`");
	}

	// 書き込みたいテキストの長さ
	uint64 length = 0;

	// 書き込みたいテキスト
	String text;

	// テキストの長さを読み込む
	reader.read(length);

	if (0 < length)
	{
		// テキストデータの読み込みのためにリサイズ
		text.resize(length);

		// テキストのサイズ分だけデータを読み込む
		reader.read(text.data(), sizeof(char32) * length);
	}

	Print << length;

	Print << text;

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```


## 21.7 バイナリファイルに複雑なデータを書き込む（シリアライズ機能）
シリアライズ機能を使うと、シリアライズに対応したデータ型 (`trivially copyable` でない型も含む) を、少ない記述でバイナリファイルで扱えます。ファイルにシリアライズ機能を使ってバイナリデータを書き込むには `Serializer<BinaryWriter>` の機能を使います。`Serializer<BinaryWriter>` のコンストラクタ引数に、書き込み先のファイルのパスを渡します。実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。ファイルが使用中だったり、ファイル名が不正なものだったりしてオープンに失敗したかどうかは `if (!writer.getWriter())` で調べられます。
`Serializer<BinaryWriter>::operator()` で値を渡すと、そのデータをシリアライズしてファイルの末尾に追加で書き込みます。

```C++
# include <Siv3D.hpp>

void Main()
{
	// 書き込み用のバイナリファイルをオープン
	Serializer<BinaryWriter> writer(U"test-serialize.bin");

	if (!writer.getWriter()) // もしオープンに失敗したら
	{
		throw Error(U"Failed to open `test-serialize.bin`");
	}

	// 書き込みたいテキスト
	const String text = U"Hello, Siv3D";

	// バイナリファイルにシリアライズ対応型のデータを書き込む
	writer(text);

	while (System::Update())
	{

	}

	// writer のデストラクタで自動的にファイルがクローズされる
}
```


## 21.8 バイナリファイルから複雑なデータを読み込む（シリアライズ機能）
シリアライズ機能を使って書き込んだデータを読み込むには `Deserializer<BinaryReader>` の機能を使います。`Deserializer<BinaryReader>` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。リリース用のアプリを作るときには埋め込みリソースパスの使用を推奨します。ファイルが存在しない場合など、オープンに失敗したかどうかは `if (!reader.getReader())` で調べられます。
`Deserializer<BinaryReader>::operator()` で値を渡すと、そのデータをシリアライズしてファイルの末尾に追加で書き込みます。

```C++
# include <Siv3D.hpp>

void Main()
{
	// バイナリファイルをオープン
	Deserializer<BinaryReader> reader(U"test-serialize.bin");

	if (!reader.getReader()) // もしオープンに失敗したら
	{
		throw Error(U"Failed to open `test-serialize.bin`");
	}

	// 読み込み先のテキスト
	String text;

	// バイナリファイルからシリアライズ対応型のデータを読み込む
	// （自動でリサイズが行われる）
	reader(text);

	Print << text.length();

	Print << text;

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```


## 21.9 ユーザ定義型をシリアライズに対応させる
ユーザが定義したクラスをシリアライズに対応させるには、`template <class Archive> void SIV3D_SERIALIZE(Archive& archive)` という public メンバ関数をクラスに実装します。`archive()` に、シリアライズに対応したオブジェクトを渡すコードを書くと、そのクラスはシリアライズ可能になり、`Serializer` や `Deserializer` で読み書きできるようになります。

```C++
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
			GameScore{ U"Player1", 111, 1000 },
			GameScore{ U"Player2", 222, 2000 },
			GameScore{ U"Player3", 333, 3000 },
		};

		// バイナリファイルをオープン
		Serializer<BinaryWriter> writer(U"score.bin");

		if (!writer.getWriter()) // もしオープンに失敗したら
		{
			throw Error(U"Failed to open `score.bin`");
		}

		// シリアライズに対応したデータを記録
		writer(scores);

		// writer のデストラクタで自動的にファイルがクローズされる
	}

	// 読み込み先のデータ
	Array<GameScore> scores;
	{
		// バイナリファイルをオープン
		Deserializer<BinaryReader> reader(U"score.bin");

		if (!reader.getReader()) // もしオープンに失敗したら
		{
			throw Error(U"Failed to open `score.bin`");
		}

		// バイナリファイルからシリアライズ対応型のデータを読み込む
		// （自動でリサイズが行われる）
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
