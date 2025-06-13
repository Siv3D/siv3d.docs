# 66. Accessing System and User Information
Learn how to get user information and environment variables.

## 66.1 Getting User Information
- There are functions to get user information as follows:
    - All return information registered in the OS as `String` type
- Default language information is useful for setting initial settings to match user language in multilingual applications

| Code | Description |
|--|--|
| `System::ComputerName()` | Returns the name of the computer running the program |
| `System::UserName()` | Returns the username running the program |
| `System::FullUserName()` | Returns the full name of the user running the program |
| `System::DefaultLocale()` | Returns the default locale of the user running the program |
| `System::DefaultLanguage()` | Returns the default language of the user running the program |

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

## 66.2 Getting Environment Variables
- To get environment variables, use `EnvironmentVariable::Get(name)`
    - If the environment variable exists, returns its value as `String` type
    - If it doesn't exist, returns an empty `String`

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