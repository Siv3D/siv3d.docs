
!!! warning "This is the documentation for an old version"
	This is the documentation for an old version of Siv3D (v0.4.3). See [Siv3D Reference v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation-en) for the latest version.

# Text encoding conversion

Siv3D のプログラムでは `String` を標準の文字列型として使いますが、他のライブラリとの互換のために文字コードを変換する場合は、以下の文字コード変換機能を使います。

## std::string, std::wstring, String の変換

| 変換の種類            |  std::string から      |  std::wstring から       |  String から               |
|------------------|----------------------|------------------------|--------------------------|
| std::string へ    | -                    | N/A                    | String::narrow()         |
| std::wstring へ   | N/A                  | -                      | String::toWstr()         |
| String へ         | Unicode::Widen()     | Unicode::FromWString() | -                        |
| const char* へ    | std::string::c_str() | N/A                    | String::narrow().c_str() |
| const wchar_t* へ | N/A                  | std::wstring::c_str()  | String::toWstr().c_str() |


## String から Unicode への変換

| 変換の種類                     |  String から        |
|---------------------------|-------------------|
| std::string (UTF-8) へ     | String::toUTF8()  |
| std::u16string (UTF-16) へ | String::toUTF16() |
| std::u32string (UTF-32) へ | String::toUTF32() |


## Unicode から String への変換

| 変換の種類    |  std::string (UTF-8) から |  std::u16string (UTF-16) から |  std::u32string (UTF-32) から |
|----------|-------------------------|-----------------------------|-----------------------------|
| String へ | Unicode::FromUTF8()     | Unicode::FromUTF16()        | Unicode::FromUTF32()        |


## Unicode の変換

| 変換の種類                     |  std::string (UTF-8) から |  std::u16string (UTF-16) から |  std::u32string (UTF-32) から |
|---------------------------|-------------------------|-----------------------------|-----------------------------|
| std::string (UTF-8) へ     | -                       | Unicode::UTF16ToUTF8()      | Unicode::UTF32ToUTF8()      |
| std::u16string (UTF-16) へ | Unicode::UTF8ToUTF16()  | -                           | Unicode::UTF32ToUTF16()     |
| std::u32string (UTF-32) へ | Unicode::UTF8ToUTF32()  | Unicode::UTF16ToUTF32()     | -                           |


## ASCII 文字のみの場合
テキストに含まれる文字が ASCII 文字のみの場合は、次の関数を使うと高速に処理できます。

- std::string_view → String への変換: `Unicode::WidenAscii()`
- StringView → std::string への変換: `Unicode::NarrowAscii()`


## ストリーム処理
メモリ使用量を小さくするため、ストリーム処理で変換したい場合は次のクラスを使います。

 - `Translator_UTF8toUTF32`
 - `Translator_UTF16toUTF32`
 - `Translator_UTF32toUTF8`
 - `Translator_UTF32toUTF16`

