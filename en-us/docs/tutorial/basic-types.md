# 6. 基本的なデータ型
Siv3D プログラムで使用する基本的なデータ型について学びます。

## 6.1 基本的な数値型
Siv3D の基本的な数値型は次のとおりです。よく使う重要なものに ★ を付けています。

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

Siv3D で整数を扱うときは、`int`, `unsigned long long` のような型名の代わりに、`int32`, `uint64` のように明示的にサイズを表現した型名を使います。これにより、プラットフォーム間での移植性が高まり、一貫性のある読みやすいコードになります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/basic-types/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	bool a = true;

	int32 b = 123;

	double c = 0.5;

	size_t d = 100;

	Print << U"a: " << a;

	Print << U"b: " << b;

	Print << U"c: " << c;

	Print << U"d: " << d;

	while (System::Update())
	{

	}
}
```

??? example "Siv3D で float 型を使う場面は限られる"
	実行環境のメモリや演算のリソースが限られるゲーム開発においては、浮動小数点数処理に `float` 型を使うことが一般的です。Siv3D もグラフィックスや並列処理に関連する内部処理では `float` 型を使うほか、シェーダの定数バッファ、行列、クォータニオン、FFT の結果など、ユーザの使う API にも `float` 型が登場することがあります。

	一方で、Siv3D は精度が要求されるシミュレーションや科学技術計算で使われることも想定しているため、主要なクラスや関数は `double` 型を扱い、描画など精度が要求されない処理に関しては内部で `float` 型を用いるハイブリッド方式になっています。`double` 型は精度に関連した問題が生じにくく、コードの読みやすさも向上し、一般的なアプリケーションプログラムであれば実行速度への影響もほとんどありません。


## 6.2 文字と文字列の基本的な型
Siv3D の文字と文字列の基本的な型は次のとおりです。重要なものに ★ を付けています。

| 型名        | 説明                                                                      |
| ----------- | ------------------------------------------------------------------------- |
| char32       | ★ UTF-32 の 1 要素（`char32_t` の別名） |
| String       | ★ 文字列クラス。要素は `char32`           |
| StringView   | 文字列のビュークラス                      |
| FilePath     | ★ ファイルパス文字列（`String` の別名）       |
| FilePathView | ファイルパス文字列のビュー（`StringView` の別名） |

Siv3D の API は、文字列を UTF-32 で処理するため、`std::string` の代わりに `String` を使います。詳しくは [文字列クラス](../tutorial2/string.md) で扱います。

`FilePath` は `String` の型エイリアスでどちらも同じ型ですが、プログラムでファイルパス文字列を扱う際に `String` の代わりに `FilePath` を用いることで、変数の目的を明確にできます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/basic-types/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	char32 a = U'A';

	String b = U"Hello";

	FilePath c = U"example/windmill.png";

	Print << U"a: " << a;

	Print << U"b: " << b;

	Print << U"c: " << c;

	while (System::Update())
	{

	}
}
```


## 6.3 基本的なデータ構造の型
Siv3D の基本的なデータ構造の型は次のとおりです。重要なものに ★ を付けています。

| 型名        | 説明                                                                      |
| ----------- | ------------------------------------------------------------------------- |
| Array&lt;Type, Allocator&gt;                              | ★ 動的配列（C++ 標準ライブラリの `std::vector` の置き換え）                   |
| Grid&lt;Type, Allocator&gt;                               | 動的な二次元配列                                                 |
| HashSet&lt;Type, Hash, Eq, Alloc&gt;                      | ハッシュテーブルによる Set（C++ 標準ライブラリの `std::unordered_set` の置き換え） |
| HashTable&lt;Key, Value, Hash, Eq, Alloc&gt;              | ハッシュテーブルによる Map（C++ 標準ライブラリの `std::unordered_map` の置き換え） |
| Optional&lt;Type&gt;                                      | ★ 無効値を表現できる型（C++ 標準ライブラリの `std::optional` の置き換え）           |
| std::array&lt;Type, size_t&gt;                            | 固定長配列                                                    |

`Array` は、C++ 標準ライブラリの `std::vector` の置き換えです。`std::vector` と同様に、動的に要素を追加・削除できます。処理コストは `std::vector` と同等です。詳しくは [動的配列](../tutorial2/array.md) で扱います。

`Optional` は、値が存在するかしないかを表現できる型です。`std::optional` と同様に、`none` という無効値を表現する値を持ちます。詳しくは [無効値を表現できる型](../tutorial2/optional.md) で扱います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/basic-types/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<int32> a = { 10, 20, 50, 100 };

	Optional<double> b;

	Print << U"a: " << a;

	Print << U"b: " << b;

	b = 12.3;

	Print << U"b: " << b;

	while (System::Update())
	{

	}
}
```


## 振り返りチェックリスト
- [x] Siv3D の基本的な数値型、`bool`, `int32`, `double`, `size_t` を理解した
- [x] Siv3D の基本的な文字型、`char32` を理解した
- [x] Siv3D の基本的な文字列型、`String`, `FilePath` を理解した
- [x] Siv3D の基本的なデータ構造型、`Array`, `Optional` を理解した
