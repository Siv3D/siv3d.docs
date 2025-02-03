# 33. 文字列クラス
文字列クラス `String` の基本的な使い方を学びます。

## 33.1 String
- Siv3D では `String` 型を使って文字列を表現します
- `String` は、UTF-32 のコードポイントを表現する `char32` 型（文字）の配列です
- UTF-32 の文字や文字列リテラルには、`U'あ'`, `U"Hello"` のように `U` プレフィックスを付けます
- `String` は内部で `std::u32string` を使って文字列を管理します
- 格納されている文字列データについて、次のことが保証されています
	- 文字列データがメモリ上で連続していること
	- 文字列の最終要素の直後にヌル文字 `U'\0'` があること
- C++ 標準ライブラリの文字列クラスよりも多くのメンバ関数を持ち、様々な便利な機能を提供します

```cpp
String s1 = U"Siv3D";

String s2 = U"こんにちは、Siv3D!";
```


## 33.2 文字列の作成
- `String` は次のような方法で作成します
	- 空の文字列を作成
	- 文字列リテラルから文字列を作成
	- 個数 × 文字で文字列を作成

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		// 空の文字列を作成する
		String s;
		Print << s;
	}

	{
		// 文字列リテラルから文字列を作成する
		String s = U"Siv3D";
		Print << s;
	}

	{
		// 個数 × 文字で文字列を作成する
		String s(5, U'A');
		Print << s;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
 
Siv3D
AAAAA
```


## 33.3 文字列の長さ
- `.size()` は文字列の長さ（要素数）を `size_t` 型で返します
- 文字列の長さは、`char32` 型で表現される UTF-32 のコードポイントの数です
- アルファベットやひらがな、漢字など、大半の文字は 1 文字が 1 コードポイントです
- 一部の絵文字などの特殊な文字では、見た目が 1 文字でも複数のコードポイントから構成されていることがあります

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		String s = U"Siv3D";
		Print << s.size();
	}

	{
		String s = U"こんにちは";
		Print << s.size();
	}

	{
		String s = U"🐈";
		Print << s.size();
	}

	{
		// 複数のコードポイントから構成されている絵文字もある
		String s = U"👩‍🎤";
		Print << s.size();
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
5
5
1
3
```


## 33.4 文字列が空であるかを調べる（1）
- `.isEmpty()` は文字列が空であるか（要素数が 0 であるか）を `bool` 型で返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s1 = U"Siv3D";
	Print << s1.isEmpty();

	String s2;
	Print << s2.isEmpty();

	while (System::Update())
	{

	}
}
```
```txt title="出力"
false
true
```


## 33.5 文字列が空であるかを調べる（2）
- `if (s)` を使って文字列がからであるかを調べます
- 文字列が空である場合、`false` と評価されます

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s1 = U"Siv3D";
   
	if (s1)
	{
		Print << U"s1 is not empty";
	}

	String s2;

	if (not s2)
	{
		Print << U"s2 is empty";
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
s1 is not empty
s2 is empty
```


## 33.6 末尾に要素を追加する
- `s << ch;` で文字列 `s` の末尾に要素 `ch` を追加します
- `.push_back(ch)` を短く書けるようにしたものです
- 文字列の末尾への追加は、それ以外の場所（先頭や途中）への追加に比べて最も効率的です

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s;
	s << U'S';
	s << U'i';
	s << U'v';
	Print << s;

	s << U'3' << U'D';
	Print << s;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
Siv
Siv3D
```


## 33.7 末尾の要素を削除する
- `.pop_back()` で文字列の末尾の要素を削除します
- 要素数が 0 のときに呼び出してはいけません
	- 事前に要素数が 0 でないことを確認してください
- 文字列の末尾の要素の削除は、それ以外の場所（先頭や途中）の削除に比べて最も効率的です

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";
	Print << s;

	s.pop_back();
	Print << s;

	s.pop_back();
	Print << s;

	while (s)
	{
		s.pop_back();
	}

	Print << s.isEmpty();

	while (System::Update())
	{

	}
}
```
```txt title="出力"
Siv3D
Siv3
Siv
true
```


## 33.8 すべての要素を削除する
- `.clear()` で文字列のすべての要素を削除し、空の文字列にします
- 要素数が 0 のときに呼び出しても問題ありません（何もしません）

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";
	Print << s;

	s.clear();
	Print << s.isEmpty();

	while (System::Update())
	{

	}
}
```
```txt title="出力"
Siv3D
true
```


## 33.9 要素数を変更する
- `.resize(n)` で文字列の要素数を `n` に変更します
- 要素数が増える場合、新しい要素はヌル文字 U'\0' で初期化されるので、別の文字で上書きする必要があります

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";
	Print << s;

	s.resize(3);
	Print << s;

	s.resize(5);
	s[3] = U'!';
	s[4] = U'?';
	Print << s;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
Siv3D
Siv
Siv!?
```


## 33.10 範囲 for 文で配列を走査する（const 参照）
- 範囲 for 文を使って配列の要素を走査します
- 各要素へのアクセスは、通常は const 参照で行います
- 範囲 for 文の中で、対象の文字列のサイズを変更する操作は行わないでください

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	for (const auto& ch : s)
	{
		Print << ch;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
S
i
v
3
D
```


## 33.11 範囲 for 文で配列を走査する（参照）
- 範囲 for 文を使って配列の要素を走査します
- ループ内で要素を変更する場合、const 参照の代わりに参照を使って要素にアクセスします
- 範囲 for 文の中で、対象の文字列のサイズを変更する操作は行わないでください

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	for (auto& ch : s)
	{
		++ch;
	}

	Print << s;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
Tjw4E
```



## 33.12 指定したインデックスの文字にアクセスする
- `[i]` で文字列の `i` 番目の要素にアクセスします
	- `i` は 0 から数えます。有効なインデックスは 0 から `size() - 1` までです
- 範囲外にアクセスしてはいけません

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	Print << s[0];
	Print << s[4];

	s[3] = U'4';
	Print << s;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
S
D
Siv4D
```


## 33.13 先頭・末尾の要素にアクセスする
- `.front()` は先頭の要素への参照を返します
	- `s[0]` と同じです
- `.back()` は末尾の要素への参照を返します
	- `s[s.size() - 1]` と同じです
- いずれも要素数が 0 のときに呼び出してはいけません

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	Print << s.front();
	Print << s.back();

	s.front() = U's';
	s.back() = U'd';
	Print << s;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
S
D
siv3d
```


## 33.14 2 つの文字列を結合した文字列を作成する
- `s1 + s2` で左右の文字列 `s1`, `s2` を結合した新しい文字列を作成できます
- `s1` や `s2` は変更されません

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s1 = U"Hello, ";
	String s2 = U"Siv3D!";
	Print << (s1 + s2);
	Print << (s1 + s2 + U"!!!");

	while (System::Update())
	{

	}
}
```
```txt title="出力"
Hello, Siv3D!
Hello, Siv3D!!!!
```


## 33.15 末尾に文字列を追加する
- `+=` で文字列の末尾に文字列を追加します

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Hello, ";
	s += U"Siv3D!";
	Print << s;

	s += U"!!!";
	Print << s;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
Hello, Siv3D!
Hello, Siv3D!!!!
```



## 33.16 先頭・終端位置のイテレータを取得する
- `.begin()` は先頭位置のイテレータを返します
- `.end()` は終端位置のイテレータを返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Siv3D";

	auto it = s.begin();

	Print << *it;

	++it;

	Print << *it;
	
	while (System::Update())
	{

	}
}
```
```txt title="出力"
S
i
```


## 33.17 その他の挿入・削除操作
- `.push_front(値)` で、先頭に要素を追加します
- `.pop_front()` で、先頭の要素を削除します
- `.insert(イテレータ, 値)` で、指定したイテレータの位置に要素を挿入します
- `.erase(イテレータ)` で、指定したイテレータの位置の要素を削除します
- `.erase(イテレータ1, イテレータ2)` で、指定した範囲の要素を削除します
- 先頭や途中への要素の挿入・削除は、それ以降の既存要素の移動を伴うため、うしろの要素数に比例したコストがかかります
	- 通常は避けるか、小さい文字列でのみ使用するべきです

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		String s = U"Siv3D";
		
		s.push_front(U'#');
		Print << s;

		s.pop_front();
		Print << s;
	}

	{
		String s = U"Siv3D";
		
		s.insert((s.begin() + 3), U'#');
		Print << s;
	}

	{
		String s = U"Siv3D";

		s.erase(s.begin() + 3);
		Print << s;

		s.erase(s.begin(), (s.begin() + 2));
		Print << s;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
#Siv3D
Siv3D
Siv#3D
SivD
vD
```


## 33.18 ある文字や文字列を含むかを調べる
- `.contains(文字)` は、文字列が指定した文字を含むかを `bool` 型で返します
- `.contains(文字列)` は、文字列が指定した文字列を含むかを `bool` 型で返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Hello, Siv3D!";

	Print << s.contains(U'S');
	Print << s.contains(U'i');
	Print << s.contains(U'4');

	Print << s.contains(U"3D");
	Print << s.contains(U"Hello");
	Print << s.contains(U"Hi");

	while (System::Update())
	{

	}
}
```
```txt title="出力"
true
true
false
true
true
false
```


## 33.19 ある文字や文字列で始まるか・終わるかを調べる
- `.starts_with(文字)` は、文字列が指定した文字で始まるかを `bool` 型で返します
- `.starts_with(文字列)` は、文字列が指定した文字列で始まるかを `bool` 型で返します
- `.ends_with(文字)` は、文字列が指定した文字で終わるかを `bool` 型で返します
- `.ends_with(文字列)` は、文字列が指定した文字列で終わるかを `bool` 型で返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Hello, Siv3D!";

	Print << s.starts_with(U'H');
	Print << s.starts_with(U'S');

	Print << s.starts_with(U"Hello");
	Print << s.starts_with(U"Hi");

	Print << s.ends_with(U'!');
	Print << s.ends_with(U'D');

	Print << s.ends_with(U"3D!");
	Print << s.ends_with(U"Hi");

	while (System::Update())
	{

	}
}
```
```txt title="出力"
true
false
true
false
true
false
true
false
```


## 33.20 部分文字列を作成する
- `.substr(開始位置, 長さ)` で、文字列の `開始位置` から `長さ` 文字の部分文字列を、新しい `String` として作成します
	- `開始位置` は 0 から始まります
	- `長さ` が省略された場合は、`開始位置` 文字目から末尾までの部分文字列を作成します
	- `開始位置` が実際の文字列の長さより大きい場合は、末尾までの部分文字列を作成します

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"Hello, Siv3D!";

	Print << s.substr(0, 5);
	Print << s.substr(7, 3);
	Print << s.substr(7);
	Print << s.substr(0, 100);

	while (System::Update())
	{

	}
}
```
```txt title="出力"
Hello
Siv
Siv3D!
Hello, Siv3D!
```

- 次のように、時間経過に応じて文字列を表示するプログラムに応用することができます

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


## 33.21 アルファベットを小文字 / 大文字にする
- `.lowercased()` は、アルファベットを小文字にした新しい文字列を作成します
- `.uppercased()` は、アルファベットを大文字にした新しい文字列を作成します

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s = U"こんにちは、Siv3D!";

	// 小文字にした新しい文字列を作成する
	Print << s.lowercased();

	// 大文字にした新しい文字列を作成する
	Print << s.uppercased();

	while (System::Update())
	{

	}
}
```
```txt title="出力"
こんにちは、siv3d!
こんにちは、SIV3D!
```


## 33.22 文字列を逆順にする
- `.reversed()` は、文字列を逆順にした新しい文字列を作成します
	- 元の文字列は変更されません
- `.reverse()` は、文字列を逆順にします
	- 元の文字列が変更されます 

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s1 = U"Hello, Siv3D!";

	// 逆順にした新しい文字列を作成する
	Print << s1.reversed();

	String s2 = U"Hello, Siv3D!";

	// 文字列を逆順にする
	s2.reverse();
	Print << s2;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
!D3viS ,olleH
!D3viS ,olleH
```


## 33.23 文字列の要素をシャッフルする
- `.shuffled()` は、文字列の要素をシャッフルした新しい文字列を作成します
	- 元の文字列は変更されません
- `.shuffle()` は、文字列の要素をシャッフルします
	- 元の文字列が変更されます

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String s1 = U"Hello, Siv3D!";
	
	// シャッフルした新しい文字列を作成する
	Print << s1.shuffled();

	String s2 = U"Hello, Siv3D!";

	// 文字列の要素をシャッフルする
	s2.shuffle();
	Print << s2;

	while (System::Update())
	{

	}
}
```
```txt title="出力例"
vel SDH!,loi3
3 lo!vl,SHDie
```


## 33.24  文字や文字列を置き換える
- `.replaced(from, to)` は、文字列の中の `from` を `to` に置き換えた新しい文字列を作成します
	- 元の文字列は変更されません
- `.replace(from, to)` は、文字列の中の `from` を `to` に置き換えます
	- 元の文字列が変更されます

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s = U"Hello, Siv3D!";
	
	// Siv3D を C++ に置き換えた新しい文字列を作成する
	Print << s.replaced(U"Siv3D", U"C++");
	
	// ! を ? に置き換える
	s.replace(U'!', U'?');
	Print << s;

	// Hello を Hi に置き換えた新しい文字列を作成する
	Print << s.replaced(U"Hello", U"Hi");

	while (System::Update())
	{

	}
}
```
```txt title="出力"
Hello, C++!
Hello, Siv3D?
Hi, Siv3D?
```


## 33.25 前後の空白文字を削除する
- `.trimmed()` は、文字列の前後にある空白文字（スペース、タブ、改行など）を削除した新しい文字列を作成します
	- 元の文字列は変更されません
- `.trim()` は、文字列の前後にある空白文字を削除します
	- 元の文字列が変更されます

```cpp
# include <Siv3D.hpp>

void Main()
{
	String s1 = U" Hello, Siv3D!   ";

	// 前後の空白文字を削除する
	s1.trim();
	Print << s1;
	Print << s1.size();

	const String s2 = U"\n\n Siv3D  \n\n\n";

	// 前後の空白文字を削除した新しい文字列を作成する
	Print << s2.trimmed();

	while (System::Update())
	{

	}
}
```
```txt title="出力"
Hello, Siv3D!
13
Siv3D
```


## 33.26 指定した文字で分割する
- `.split(delimiter)` は、文字列を `delimiter` で分割した結果を `Array<String>` で返します
	- `delimiter` は 1 文字の文字列です
	- `delimiter` が連続している場合、空の文字列が生成されます
	- `delimiter` が文字列の先頭や末尾にある場合、空の文字列が生成されます
	- `delimiter` が文字列に含まれていない場合、元の文字列がそのまま返されます

```cpp
# include <Siv3D.hpp>

void Main()
{
	{
		const String s = U"red,green,blue";
		
		const Array<String> values = s.split(U',');
		Print << values;
	}

	{
		const String s = U",,a,";

		const Array<String> values = s.split(U',');
		Print << values;
	}

	{
		const String s = U"1, 2, 3, 4, 5";

		// U',' で区切られた数字の文字列を Array<int32> に変換する例
		const Array<int32> values = s.split(U',').map(Parse<int32>);
		Print << values;
	}

	while (System::Update())
	{

	}
}
```
```txt title="出力"
{red, green, blue}
{, , a, }
{1, 2, 3, 4, 5}
```


## 33.27 他の文字列型へ変換する
- `String` を別の形式の文字列型に変換する次のようなメンバ関数があります

| コード | 説明 |
|---|---|
| `.narrow()` | `std::string`（文字コードは環境依存）に変換します |
| `.toUTF8()` | `std::string`（UTF-8）に変換します |
| `.toWstr()` | `std::wstring` に変換します |
| `.toUTF16()` | `std::u16string` に変換します |
| `.toUTF32()` | `std::u32string` に変換します |

??? example "環境依存の文字コード"
	- `std::string` の文字コードは環境によって異なります
	- 日本語の Windows では Shift_JIS (CP932), macOS や Linux では UTF-8 です
	- Siv3D v0.8 以降では `std::string` の文字コードは UTF-8 に統一されます

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

- `const char*` を受け取る関数に `String` の文字列を渡すには、`.narrow()` で得られた `std::string` の先頭ポインタを `c_str()` で取得します

```cpp
f(s.narrow().c_str());
```


## 33.28 他の文字列型から変換する
- 別の形式の文字列型から `String` に変換する次のような関数があります

| コード | 説明 |
|---|---|
| `Unicode::Widen(s)` | `std::string`（文字コードは環境依存）から `String` に変換します |
| `Unicode::WidenAscii(s)` | `std::string`（ASCII）から `String` に変換します |
| `Unicode::FromWstring(s)` | `std::wstring` から `String` に変換します |
| `Unicode::FromUTF8(s)` | `std::string`（UTF-8）から `String` に変換します |
| `Unicode::FromUTF16(s)` | `std::u16string` から `String` に変換します |
| `Unicode::FromUTF32(s)` | `std::u32string` から `String` に変換します |

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


## 33.29 FilePath
- ファイルパスを指す文字列であることを明示するために、`String` のエイリアスとして `FilePath` が用意されています

```cpp
# include <Siv3D.hpp>

void Main()
{
	FilePath path = U"example/windmill.png";

	Print << path;

	while (System::Update())
	{

	}
}
```
```txt title="出力"
example/windmill.png
```

