# 7. 基本的な型とクラス
Siv3D プログラムで使用する基本的な型とクラスを学びます。

- よく使う重要な型に ★ を付けています

## 7.1 整数
- 整数を扱う場合、サイズが明示された型名 `int32`, `uint64` などを使います
- `int`, `long` なども使えますが、サイズが環境によって異なり、移植性が悪いため避けるべきです
- 配列の要素数は C++ 標準と同じ `size_t` 型で表現します

| 型名 | サイズ | 説明 | 値の範囲 |
| --- | --- | --- | --- |
| `int8` | 1 バイト | 符号付き 8 ビット整数 | -128 ～ 127 |
| `uint8` | 1 バイト | 符号なし 8 ビット整数 | 0 ～ 255 |
| `int16` | 2 バイト | 符号付き 16 ビット整数 | -32,768 ～ 32,767 |
| `uint16` | 2 バイト | 符号なし 16 ビット整数 | 0 ～ 65,535 |
| `int32` ★ | 4 バイト | 符号付き 32 ビット整数 | -2,147,483,648 ～ 2,147,483,647 |
| `uint32` | 4 バイト | 符号なし 32 ビット整数 | 0 ～ 4,294,967,295 |
| `int64` | 8 バイト | 符号付き 64 ビット整数 | -9,223,372,036,854,775,808 ～ 9,223,372,036,854,775,807 |
| `uint64` | 8 バイト | 符号なし 64 ビット整数 | 0 ～ 18,446,744,073,709,551,615 |
| `size_t` ★ | 8 バイト | 符号なし 64 ビット整数 | 0 ～ 18,446,744,073,709,551,615 |

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


## 7.2 浮動小数点数
- 小数以下の数を扱う場合、C++ 標準の浮動小数点数型 `float`, `double` を使います

| 型名 | サイズ | 説明 | 値の範囲 | 精度 |
| --- | --- | --- | --- | --- |
| `float` | 4 バイト | 単精度浮動小数点数 | 3.4E +/- 38 | 7 桁 |
| `double` ★ | 8 バイト | 倍精度浮動小数点数 | 1.7E +/- 308 | 15 桁 |


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

!!! info "Siv3D で開発者が float 型を使う場面は限られる"
	- 計算リソースを少しでも節約したいゲーム開発では、通常、浮動小数点数処理に `float` 型を使うことが多いです
	- 一方、Siv3D の大部分の API では `double` 型が標準で使われています
		- シミュレーションや科学技術計算など、精度が要求される用途で使われることも想定しているためです
	- Siv3D エンジン内部では、精度よりも速度が重要な内部処理（描画など）では `float` 型で処理するなど、バランスを取っています
	- シェーダの定数バッファ、行列、クォータニオン、FFT の結果など、開発者が使う API にも一部 `float` 型は登場します


## 7.3 真偽値
- プログラム中で Yes / No のような二択の状態を表す場合は、整数型ではなく、C++ 標準の真偽値 `bool` 型を使います
- `bool` 型の値は `true` または `false` の 2 つのみです
- `true` は真、`false` は偽を表します

| 型名 | サイズ | 説明 | 値の範囲 |
| --- | --- | --- | --- |
| `bool` ★ | 1 バイト | 真偽値 | `true` または `false` |

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


## 7.4 文字
- 文字を扱うときは、UTF-32 形式の文字リテラルや、UTF-32 形式で文字を表現する `char32` 型を使います
- `char` 型では、ひらがなの「あ」を 1 要素で表現できませんが、`char32` 型では 1 要素で表現できて便利です

| 型名 | サイズ | 説明 | 値の範囲 |
| --- | --- | --- | --- |
| `char32` ★ | 4 バイト | UTF-32 エンコードの文字 | 0 ～ 0x10FFFF |

- `char32` 型の文字リテラルは、シングルクォーテーションの前に `U` を付けます 

```cpp
# include <Siv3D.hpp>

void Main()
{
	char32 c1 = U'A';
	char32 c2 = U'あ';

	Print << c1;
	Print << c2;

	while (System::Update())
	{

	}
}
```


## 7.5 文字列
- 文字列を扱うときは、UTF-32 形式の文字列リテラルや、`String` クラスを使います
	- `String` クラスは、UTF-32 形式の文字列を扱うためのクラスで、ざっくり言うと `char32` 版の `std::string` です
	- チュートリアル ?? で詳しく説明します
- `std::string_view` に相当する `StringView` クラスもあります
- 文字列がファイルパスを表す場合、それぞれの型エイリアス `FilePath`, `FilePathView` を使うとコードの可読性が向上します

| 型名 | 説明 |
| --- | --- |
| `String` ★ | UTF-32 エンコードの文字列 |
| `StringView` | UTF-32 エンコードの文字列ビュー |
| `FilePath` | ファイルパス文字列（`String` の別名） |
| `FilePathView` | ファイルパス文字列ビュー（`StringView` の別名） |

- UTF-32 形式の文字列リテラルは、ダブルクォーテーションの前に `U` を付けます

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s1 = U"Hello!";
	String s2 = U"こんにちは！";
	FilePath s3 = U"example/windmill.png";

	Print << s1;
	Print << s2;
	Print << s3;
	Print << U"Siv3D!";

	while (System::Update())
	{

	}
}
```


## 7.6 配列
- 固定長配列は C++ 標準ライブラリの `std::array<Type, N>` を使います
	- Type は要素の型、N は要素数です
- 動的配列は `Array<Type>` クラスを使います
	- Type は要素の型です
	- チュートリアル ?? で詳しく説明します
- 動的な二次元配列は `Grid<Type>` クラスで表現できます
	- Type は要素の型です
	- チュートリアル ?? で詳しく説明します

| 型名 | 説明 |
| --- | --- |
| `std::array<Type, N>` | 固定長配列 |
| `Array<Type>` ★ | 動的配列（C++ 標準の `std::vector` に相当） |
| `Grid<Type>` | 動的二次元配列 |


```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> a = { 1, 2, 3, 4, 5 };
	Grid<int32> b(4, 3, 0);

	Print << a;
	Print << b;

	while (System::Update())
	{

	}
}
```


## 7.7 その他のデータ型
- ハッシュテーブルによる Set は `HashSet<Type>` クラスを使います
	- Type は要素の型です
	- チュートリアル ?? で詳しく説明します
- ハッシュテーブルによる Map は `HashTable<Key, Value>` クラスを使います
	- Key はキーの型、Value は値の型です
	- チュートリアル ?? で詳しく説明します
- 任意の型に無効値の表現を追加する `Optional<Type>` クラスがあります
	- Type は要素の型です
	- チュートリアル ?? で詳しく説明します

| 型名 | 説明 |
| --- | --- |
| `HashSet<Type>` | ハッシュテーブルによる Set（C++ 標準の `std::unordered_set` に相当）|
| `HashTable<Key, Value>` | ハッシュテーブルによる Map（C++ 標準の `std::unordered_map` に相当）|
| `Optional<Type>` | 任意の型に無効値の表現を追加するクラス（C++ 標準の `std::optional` に相当）|

```cpp
# include <Siv3D.hpp>

void Main()
{
	HashSet<int32> a = { 1, 2, 3, 4, 5 };
	HashTable<int32, String> b = { { 1, U"one" }, { 2, U"two" }, { 3, U"three" } };
	Optional<int32> c = 42;
	Optional<int32> d = none;

	Print << a;
	Print << b;
	Print << c;
	Print << d;

	while (System::Update())
	{

	}
}
```


## 振り返りチェックリスト
- [x] よく使う整数型 `int32`, `size_t` を学んだ
- [x] よく使う浮動小数点数型 `double` を学んだ
- [x] よく使う真偽値型 `bool` を学んだ
- [x] `char32` 型で文字を表現することを学んだ
- [x] `String` で文字列を扱うことを学んだ
- [x] `Array<Type>` で動的配列を扱うことを学んだ
