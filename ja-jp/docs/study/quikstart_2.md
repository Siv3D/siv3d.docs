# 勉強会 2 コマ目（基本）

## 1. Main() 関数とメインループ
Siv3D の C++ プロジェクトでは、`<Siv3D.hpp>` ヘッダをインクルードするだけで、Siv3D のさまざまな関数やクラスを使ったプログラムを書けます。`<iostream>` や `<vector>` などの C++ 標準ライブラリのインクルードは不要です。

Siv3D のプログラムでは、`int main()` ではなく `void Main()` という関数をメイン関数として使います。

`while (System::Update()) { }` というループが**メインループ**です。メインループは、プログラムを実行しているモニタ 🖥️ の表示周期（リフレッシュレート）に合わせて繰り返されます。一般に毎秒 60 回や 120 回です。

```C++ hl_lines="5-8"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update()) // System::Update() が false を返すまで繰り返す
	{

	}
}
```

`System::Update()` 関数は普段は `true` を返すため、半永久的にメインループが続きますが、ウィンドウを閉じたり ++esc++ を押したりするなど特定の操作をすると、それ以降は `false` を返すことでメインループを終了させ、そのまま `Main()` 関数の終端まで到達するとプログラムが終了します。


## 2. 文字列や数値を簡易表示する
`Print` を使って画面に文字列を簡易表示します。Siv3D のプログラムで文字列を扱うときは、ダブルクォーテーションの前に `U` を付けます。これは、文字列を Unicode (UTF-32) 文字列として扱うための記法です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/print/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"C++"; // U を付ける

	Print << U"Hello, " << U"Siv3D";

	Print << 123;

	Print << 4.567;

	while (System::Update())
	{

	}
}
```

## 3. 簡易表示をたくさん行う
`Print` したものは画面に残り続けます。画面に収まらなくなったものは、古いものから順に消えていきます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/print/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	while (System::Update())
	{
		Print << count;

		++count;
	}
}
```

このコードに登場する `int32` は `int` と同じ意味で、整数型を表します。ほかにも `int64` や `uint32` などの型があります。`int` や `long long` などの C++ 標準の型名の代わりに、Siv3D では明示的にサイズを表現した型名を使います。これにより、プラットフォーム間での移植性が高まり、一貫性のある読みやすいコードになります。次の表の ★ が付いた型は特に重要です。

| 型名        | 説明                                                                      |
|-----------|-------------------------------------------------------------------------|
| bool      | ★ ブーリアン型（`false` または `true`）                                            |
| int8      | 符号付き 8-bit 整数型（-128 ～ 127）                                              |
| uint8     | 符号無し 8-bit 整数型（0 ～ 255）                                                 |
| int16     | 符号付き 16-bit 整数型（-32,768 ～ 32,767）                                       |
| uint16    | 符号無し 16-bit 整数型（0 ～ 65,535）                                             |
| int32     | ★ 符号付き 32-bit 整数型（-2,147,483,648 ～ 2,147,483,647）                       |
| uint32    | 符号無し 32-bit 整数型（0 ～ 4,294,967,295）                                    |
| int64     | 符号付き 64-bit 整数型（-9,223,372,036,854,775,808 ～ 9,223,372,036,854,775,807） |
| uint64    | 符号無し 64-bit 整数型（0 ～ 18,446,744,073,709,551,615）                         |
| float     | 単精度浮動小数点数型                                                              |
| double    | ★ 倍精度浮動小数点数型                                                            |
| size_t    | ★ オブジェクトのサイズを表現する符号無し 64-bit 整数型（0 ～ 18,446,744,073,709,551,615）        |
| char32       | ★ UTF-32 の 1 要素（`char32_t` の別名） |
| String       | ★ 文字列クラス。要素は `char32`           |
| FilePath     | ★ ファイルパス文字列（`String` の別名）       |


## 4. 簡易表示を消去する
`ClearPrint()` は、画面に残っている `Print` による出力をすべて消去します。

メインループの先頭で常に `ClearPrint()` することで、現在のフレーム内で `Print` した内容のみを画面に表示することができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/print/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	while (System::Update())
	{
		// 古い出力（以前のフレームの出力）を消去する
		ClearPrint();

		Print << count;

		++count;
	}
}
```

## 5. 整数型を使う
整数を表現するときは、`int32` が最も一般的です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 score = 100;

	Print << score;

	score = 200; // 代入

	Print << score;

	score += 50; // 加算

	Print << score;

	while (System::Update())
	{

	}
}
```

## 6. 浮動小数点数型を使う
浮動小数点数を表現するときは、`double` が最も一般的です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	double x = 1.23;

	Print << x;

	x = 4.56; // 代入

	Print << x;

	x += 7.89; // 加算

	Print << x;

	while (System::Update())
	{

	}
}
```

## 7. ブーリアン型を使う
ブーリアン型は `bool` です。`true` または `false` の値を持ちます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	bool a = true;

	bool b = false;

	Print << a;

	Print << b;

	if (a)
	{
		Print << U"a は true です";
	}

	if (not b) // not は ! と同じ意味です
	{
		Print << U"b は false です";
	}

	while (System::Update())
	{

	}
}
```


## 8. 文字列型を使う
文字列型は `String` です。コード中に直接書く文字列リテラルは `U` を付けてダブルクォーテーションで囲み、UTF-32 文字列とします。

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Hello, Siv3D";

	Print << s; // Hello, Siv3D

	s += U"!";

	Print << s; // Hello, Siv3D!

	// Siv3D を C++ に置き換える
	s.replace(U"Siv3D", U"C++");

	Print << s; // Hello, C++!

	while (System::Update())
	{

	}
}
```


## 9. 配列などのデータ構造
配列などのデータ構造は、Siv3D が提供するクラスを使うと便利です。次の表の ★ が付いたクラスは特に重要です。


| 型名        | 説明                                                                      |
| ----------- | ------------------------------------------------------------------------- |
| Array&lt;Type, Allocator&gt;                              | ★ 動的配列（C++ 標準ライブラリの `std::vector` の Siv3D 版）                   |
| Grid&lt;Type, Allocator&gt;                               | 動的な二次元配列                                                 |
| HashSet&lt;Type, Hash, Eq, Alloc&gt;                      | ハッシュテーブルによる Set（C++ 標準ライブラリの `std::unordered_set` の Siv3D 版） |
| HashTable&lt;Key, Value, Hash, Eq, Alloc&gt;              | ハッシュテーブルによる Map（C++ 標準ライブラリの `std::unordered_map` の Siv3D 版え） |
| Optional&lt;Type&gt;                                      | ★ 無効値を表現できる型（C++ 標準ライブラリの `std::optional` の Siv3D 版）           |
| std::array&lt;Type, size_t&gt;                            | 固定長配列                                                    |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> coins;

	// 配列の末尾に新しい要素を追加する。
	// vector の push_back と同じ
	coins << 10;
	coins << 5;
	coins << 1;
	coins << 50;

	Print << coins; // { 10, 5, 1, 50 }

	// 要素をソートする
	coins.sort();

	Print << coins; // { 1, 5, 10, 50 }

	while (System::Update())
	{

	}
}
```