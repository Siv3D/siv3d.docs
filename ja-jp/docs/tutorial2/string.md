# 33. 文字列クラス
文字列クラス `String` の基本的な使い方を学びます。

## 33.1 文字列クラスの基本
- Siv3D では `String` 型を使って文字列を表現します
- `String` は、UTF-32 のコードポイントを表現する `char32` 型（文字）の配列です
- UTF-32 の文字や文字列リテラルには、`U'あ'`, `U"Hello"` のように `U` プレフィックスを付けます
- `String` は内部で `std::u32string` を使って文字列を管理しています
- ただし、標準ライブラリの文字列クラスより多くのメンバ関数を持ち、様々な便利な機能を提供します
- 格納されている文字列データについて、次のことが保証されています
    - 文字列データがメモリ上で連続していること
    - 文字列の最終要素の直後にヌル文字 `U'\0'` があること

```cpp

```


## 33.2 文字列の長さ
文字列の長さは `.size()` で取得します。一部の絵文字などを除き、文字列の長さは見た目の文字数と同じです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s1 = U"Siv3D";

	Print << s1.size();

	const String s2 = U"こんにちは";

	Print << s2.size();

	const String s3 = U"🐈";

	Print << s3.size();

	// 複数のコードポイントから構成されている絵文字もある
	const String s4 = U"👩‍🎤";

	Print << s4.size();

	while (System::Update())
	{

	}
}
```


## 33.3 指定したインデックスの文字にアクセスする
文字列内の指定したインデックスにある文字にアクセスするには、`[]` 演算子を使います。インデックスは 0 から始まります。`s.front()` は `s[0]` と同じです。`s.back()` は `s[s.size() - 1]` と同じで末尾の文字にアクセスします。範囲外のインデックスにアクセスしてはいけません。

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	const char32 c = s[0];

	Print << c;

	Print << s[1];

	// 先頭の文字にアクセス
	Print << s.front();

	// 末尾の文字にアクセス
	Print << s.back();

	s[3] = U'4';

	s.back() = U'X';

	Print << s;

	while (System::Update())
	{

	}
}
```


## 33.4 範囲ベースの for 文で文字にアクセスする
範囲ベース for 文を使って文字列の各文字にアクセスできます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"Siv3D";

	for (const auto& ch : s)
	{
		Print << ch;
	}

	while (System::Update())
	{

	}
}
```


## 33.5 空の文字列
要素を保持していない、長さが 0 の文字列は**空の文字列**です。代入や追加によって要素を追加すると、空でない文字列になります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s;

	Print << s;

	Print << s.size();

	s = U"Hello, Siv3D!";

	Print << s;

	Print << s.size();

	while (System::Update())
	{

	}
}
```


## 33.6 文字列が空であるかを調べる
`String` 型の値 `s` が空であるかは、`if (s.isEmpty())` や `if (s)` / `if (not s)` で調べられます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s1;

	String s2 = U"Hello, Siv3D!";

	Print << s1.isEmpty();

	Print << s2.isEmpty();

	if (not s1)
	{
		Print << U"s1 is empty";
	}

	if (s2)
	{
		Print << U"s2 is not empty";
	}

	while (System::Update())
	{

	}
}
```


## 33.7 文字列の末尾に文字を追加する
`<<` で文字列の末尾に文字を追加することができます。`<<` は連続して使うこともできます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3";

	Print << s;

	s << U'D';

	for (int32 i = 0; i < 3; ++i)
	{
		s << U'!';

		Print << s;
	}

	while (System::Update())
	{

	}
}
```


## 33.8 文字列の末尾に文字列を追加する
`+=` で文字列の末尾に文字列を追加することができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv";

	Print << s;

	s += U"3D";

	Print << s;

	for (int32 i = 0; i < 3; ++i)
	{
		s += U"!?";

		Print << s;
	}

	while (System::Update())
	{

	}
}
```


## 33.9 文字列を結合する
`+` で左右の文字列を結合した新しい文字列を作成できます。元の文字列は変更されません。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s1 = U"Hello, ";

	const String s2 = U"Siv3D!";

	Print << (s1 + s2);

	const String s3 = (s1 + s2);

	Print << (s3 + U"!!!");

	while (System::Update())
	{

	}
}
```


## 33.10 文字列を空にする
`.clear()` で文字列を空にすることができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Hello, Siv3D!";

	Print << s;

	Print << s.isEmpty();

	// 文字列を空にする
	s.clear();

	Print << s;

	Print << s.isEmpty();

	while (System::Update())
	{

	}
}
```


## 33.11 末尾の要素を削除する
`.pop_back()` で文字列の末尾の要素を削除することができます。`.pop_back_N(n)` で末尾から `n` 要素を削除することができます。

空の文字列で `.pop_back()` を呼んではいけません。一方、`.pop_back_N(n)` は空の文字列でも呼び出すことができます。`.pop_back_N(n)` は実際の要素数以上を削除しようとした場合に文字列を空にします。


```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D!";

	Print << s;

	s.pop_back();

	Print << s;

	s.pop_back_N(2);

	Print << s;

	// 実際の要素数以上を削除しようとしているので、文字列を空にする
	s.pop_back_N(100);

	Print << s.isEmpty();

	while (System::Update())
	{

	}
}
```


## 33.12 先頭の文字を削除する
`.pop_front()` で文字列の先頭の要素を削除することができます。`.pop_front_N(n)` で先頭から `n` 要素を削除することができます。

空の文字列で `.pop_front()` を呼んではいけません。一方、`.pop_front_N(n)` は空の文字列でも呼び出すことができます。`.pop_front_N(n)` は実際の要素数以上を削除しようとした場合に文字列を空にします。

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D!";

	Print << s;

	s.pop_front();

	Print << s;

	s.pop_front_N(2);

	Print << s;

	// 実際の要素数以上を削除しようとしているので、文字列を空にする
	s.pop_front_N(100);

	Print << s.isEmpty();

	while (System::Update())
	{

	}
}
```


## 33.13 ある文字や文字列を含むかを調べる
ある文字や文字列を含むかを、`.contains()` で調べることができます。ある文字や文字列で始まるかを、`.starts_with()` で調べることができます。ある文字や文字列で終わるかを、`.ends_with()` で調べることができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"Hello, Siv3D!";

	// 含むかを調べる
	Print << s.contains(U"Siv");
	Print << s.contains(U'S');

	// 指定した文字や文字列で始まるかを調べる
	Print << s.starts_with(U"Siv");
	Print << s.starts_with(U'H');

	// 指定した文字や文字列で終わるかを調べる
	Print << s.ends_with(U"3D!");
	Print << s.ends_with(U'?');

	while (System::Update())
	{

	}
}
```


## 33.14 文字列の一部から新しい文字列を作る
`.substr(offset, count)` で、文字列の `offset` 文字目から `count` 文字の部分文字列（`String`）を作成することができます。`offset` は 0 から始まります。`count` が省略された場合は、`offset` 文字目から末尾までの部分文字列を作成します。`offset` が実際の文字列の長さより大きい場合は、末尾までの部分文字列を作成します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"Hello, Siv3D!";

	// 0 文字目から 5 文字の部分文字列を作成する
	Print << s.substr(0, 5);

	// 7 文字目から 3 文字の部分文字列を作成する
	Print << s.substr(7, 3);

	// 7 文字目から末尾までの部分文字列を作成する
	Print << s.substr(7);

	// 第 2 引数が実際の文字列の長さより大きい場合は、末尾までの部分文字列を作成する
	Print << s.substr(0, 100);

	while (System::Update())
	{

	}
}
```


次のように、時間経過に応じて文字列を表示するプログラムに応用することができます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"Hello, Siv3D!";

	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		ClearPrint();

		const int32 count = (stopwatch.ms() / 300);

		Print << s.substr(0, count);
	}
}
```


## 33.15 アルファベットを小文字 / 大文字にする
`.lowercased()` で、アルファベットを小文字にした新しい文字列を作成します。

`.uppercased()` で、アルファベットを大文字にした新しい文字列を作成します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s1 = U"Hello, Siv3D!";

	Print << s1.lowercased();

	Print << s1.uppercased();

	const String s2 = U"こんにちは、Siv3D!";

	Print << s2.lowercased();

	Print << s2.uppercased();

	while (System::Update())
	{

	}
}
```


## 33.16 文字や文字列を置き換える
`.replace(from, to)` で、文字列の中の `from` を `to` に置き換えます。

`.replaced(from, to)` で、文字列の中の `from` を `to` に置き換えた新しい文字列を作成します。元の文字列は変更されません。

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Hello, Siv3D!";

	// Siv3D を C++ に置き換える
	s.replace(U"Siv3D", U"C++");

	Print << s;

	// ! を ? に置き換える
	s.replace(U'!', U'?');

	Print << s;

	// 置き換えた新しい文字列を返す
	Print << s.replaced(U"Hello", U"Hi");

	// 元の文字列は変更されていない
	Print << s;

	while (System::Update())
	{

	}
}
```


## 33.17 前後の空白文字を削除する
`.trim()` で、文字列の前後にある空白文字（スペース、タブ、改行など）を削除します。

`.trimmed()` で、文字列の前後にある空白文字を削除した新しい文字列を作成します。元の文字列は変更されません。

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s1 = U" Hello, Siv3D!   ";

	s1.trim();

	Print << s1;

	Print << s1.size();

	const String s2 = U"\n\n Siv3D  \n\n\n";

	Print << s2.trimmed();

	while (System::Update())
	{

	}
}
```


## 33.18 文字列を指定した文字で分割する
`.split(delimiter)` を使うと、文字列を `delimiter` で分割した結果を `Array<String>` で返します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"red,green,blue";

	const Array<String> values = s.split(U',');

	for (const auto& value : values)
	{
		Print << value;
	}

	while (System::Update())
	{

	}
}
```

`U','` で区切られた数字の文字列を `Array<int32>` に変換するサンプルです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"1, 2, 3, 4, 5";

	const Array<int32> a = s.split(U',').map(Parse<int32>);

	Print << a;

	while (System::Update())
	{

	}
}
```


## 33.19 他の文字列型へ変換する
`String` を別の文字列型に変換するには、次のメンバ関数を使います。

| メンバ関数 | 説明 |
|---|---|
| `.narrow()` | `std::string` (文字コードは環境依存) に変換します。 |
| `.toUTF8()` | `std::string` (UTF-8) に変換します。 |
| `.toWstr()` | `std::wstring` に変換します。 |
| `.toUTF16()` | `std::u16string` に変換します。 |
| `.toUTF32()` | `std::u32string` に変換します。 |

??? example "環境依存の文字コード"
	`std::string` の文字コードは環境によって異なります。日本語の Windows では Shift_JIS (CP932), macOS や Linux では UTF-8 です。Siv3D v0.8.0 以降は `std::string` の文字コードは UTF-8 に統一される予定です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"こんにちは、Siv3D!";

	const std::string s1 = s.narrow();

	const std::string s2 = s.toUTF8();

	const std::wstring s3 = s.toWstr();

	const std::u16string s4 = s.toUTF16();

	const std::u32string s5 = s.toUTF32();

	while (System::Update())
	{

	}
}
```

`const char*` を受け取る関数に `String` の文字列を渡すには、`.narrow()` で得られた `std::string` の先頭ポインタを `c_str()` で取得します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"12345";

	// const char* を受け取る関数に String の文字列を渡す
	const int32 n = atoi(s.narrow().c_str());

	Print << n;

	// String の文字列を int32 に変換する Siv3D の標準的な方法
	Print << Parse<int32>(s);

	while (System::Update())
	{

	}
}
```


## 33.20 他の文字列型から変換する
別の文字列型から `String` に変換するには、次の関数を使います。

| 関数 | 説明 |
|---|---|
| `Unicode::Widen(s)` | `std::string` (文字コードは環境依存) から `String` に変換します。 |
| `Unicode::WidenAscii(s)` | `std::string` (ASCII) から `String` に変換します。 |
| `Unicode::FromWstring(s)` | `std::wstring` から `String` に変換します。 |
| `Unicode::FromUTF8(s)` | `std::string` (UTF-8) から `String` に変換します。 |
| `Unicode::FromUTF16(s)` | `std::u16string` から `String` に変換します。 |
| `Unicode::FromUTF32(s)` | `std::u32string` から `String` に変換します。 |

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s1 = Unicode::Widen("こんにちは、Siv3D!");

	const String s2 = Unicode::WidenAscii("Hello, Siv3D!");

	const String s3 = Unicode::FromWstring(L"こんにちは、Siv3D!");

	const String s4 = Unicode::FromUTF8("こんにちは、Siv3D!");

	const String s5 = Unicode::FromUTF16(u"こんにちは、Siv3D!");

	const String s6 = Unicode::FromUTF32(U"こんにちは、Siv3D!");

	while (System::Update())
	{

	}
}
```
