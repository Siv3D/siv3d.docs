# 66. ユーザ環境の取得

## 66.1 ユーザ情報の取得
- ユーザの情報を取得する次のような関数があります：
    - いずれも OS に登録されている情報を `String` 型で返します

| コード | 説明 |
|--|--|
| `System::ComputerName()` | プログラムを実行しているコンピュータの名前を返す |
| `System::UserName()` | プログラムを実行しているユーザ名を返す |
| `System::FullUserName()` | プログラムを実行しているユーザのフルネームを返す |
| `System::DefaultLocale()` | プログラムを実行しているユーザのデフォルトのロケールを返す |
| `System::DefaultLanguage()` | プログラムを実行しているユーザのデフォルト言語を返す |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"ComputerName: " << System::ComputerName();
	Print << U"UserName: " << System::UserName();
	Print << U"FullUserName: " << System::FullUserName();
	Print << U"DefaultLocale: " << System::DefaultLocale();
	Print << U"DefaultLanguage: " << System::DefaultLanguage();

	while (System::Update())
	{

	}
}
```

## 66.2 環境変数の取得
- 環境変数を取得するには、`EnvironmentVariable::Get(名前)` を使います
    - 環境変数が存在する場合、その値を `String` 型で返します
    - 存在しない場合、空の `String` を返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String home = EnvironmentVariable::Get(U"HOME");
	const String path = EnvironmentVariable::Get(U"PATH");

	Print << U"HOME";
	Print << home;
	Print << U"PATH";
	Print << path;

	while (System::Update())
	{

	}
}
```
