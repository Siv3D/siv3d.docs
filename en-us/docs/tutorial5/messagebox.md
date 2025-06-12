# 81. Message Box
Learn how to display message boxes and get user input.

## 81.1 Message Box Overview
- Message box functionality allows you to display message boxes that request a response from the user and obtain the user's selection
- While a message box is displayed, main thread processing is paused

### Functions

```cpp title="Display a message box with an 'OK' button and return the result"
MessageBoxResult System::MessageBoxOK(StringView text, MessageBoxStyle style = MessageBoxStyle::Default);

MessageBoxResult System::MessageBoxOK(StringView title, StringView text, MessageBoxStyle style = MessageBoxStyle::Default);
```

- `title`: Message box title
- `text`: Body text
- `style`: Style
- Return value: `MessageBoxResult::OK`

-----------------------------------

```cpp title="Display a message box with 'OK' and 'Cancel' buttons and return the result"
MessageBoxResult System::MessageBoxOKCancel(StringView text, MessageBoxStyle style = MessageBoxStyle::Default);

MessageBoxResult System::MessageBoxOKCancel(StringView title, StringView text, MessageBoxStyle style = MessageBoxStyle::Default);
```

- `title`: Message box title
- `text`: Body text
- `style`: Style
- Return value: `MessageBoxResult::OK` or `MessageBoxResult::Cancel`

-----------------------------------

```cpp title="Display a message box with 'Yes' and 'No' buttons and return the result"
MessageBoxResult System::MessageBoxYesNo(StringView text, MessageBoxStyle style = MessageBoxStyle::Default);

MessageBoxResult System::MessageBoxYesNo(StringView title, StringView text, MessageBoxStyle style = MessageBoxStyle::Default);
```

- `title`: Message box title
- `text`: Body text
- `style`: Style
- Return value: `MessageBoxResult::Yes` or `MessageBoxResult::No`

### Enumerations

#### MessageBoxResult
- Constants representing user operations on a message box
- On some platforms, it may be possible to close a message box without selecting a button

| Value | Description |
|--|--|
| `OK` | "OK" was pressed |
| `Cancel` | "Cancel" was pressed or the message box was closed |
| `Yes` | "Yes" was pressed |
| `No` | "No" was pressed |

#### MessageBoxStyle
- Constants representing message box styles
- On some platforms, certain styles may not exist, in which case the default style is used

| Value | Description |
|--|--|
| `Default` | Default style |
| `Info` | Style for conveying information |
| `Warning` | Style for conveying warnings |
| `Error` | Style for conveying serious errors |
| `Question` | Question mark style |


## 81.2 (Sample) Exit program after a certain time has elapsed

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial5/messagebox/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 5-second countdown timer
	Timer timer{ 5s, StartImmediately::Yes };

	while (System::Update())
	{
		ClearPrint();

		// Display remaining time
		Print << U"残り " << timer.format(U"mm:ss");

		// When timer reaches 0
		if (timer.reachedZero())
		{
			// Display OK message box
			System::MessageBoxOK(U"体験版の終了", U"体験版で遊べるのはここまでです。");

			// Exit the program
			break;
		}
	}
}
```


## 81.3 (Sample) Confirm exit when window close button is pressed

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial5/messagebox/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Prevent the app from terminating via user actions
	System::SetTerminationTriggers(UserAction::NoAction);

	while (System::Update())
	{
		// When the window close button is pressed
		if (System::GetUserActions() & UserAction::CloseButtonClicked)
		{
			// Display Yes or No message box
			const MessageBoxResult result = System::MessageBoxYesNo(U"アプリケーションを終了しますか？");

			// If Yes is selected
			if (result == MessageBoxResult::Yes)
			{
				// Exit the program
				break;
			}
		}
	}
}
```


## 81.4 (Sample) Confirm loading previous save data on startup

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial5/messagebox/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Save file path
	constexpr FilePathView SaveDataPath = U"save.txt";

	// Loaded save data
	String saveData;

	// If previous data exists
	if (FileSystem::Exists(SaveDataPath))
	{
		// Display Yes or No message box
		const MessageBoxResult result = System::MessageBoxYesNo(U"前回のデータが見つかりました。読み込んでそこから再開しますか？");

		// If Yes is selected
		if (result == MessageBoxResult::Yes)
		{
			// Read string from save file
			saveData = TextReader{ SaveDataPath }.readAll();
		}
	}

	// If save data was loaded
	if (saveData)
	{
		Print << U"前回のセーブデータ: " << saveData;
	}
	else
	{
		Print << U"新規データ";
	}

	while (System::Update())
	{

	}

	// Write save data (current date and time) to save file before exiting
	TextWriter{ SaveDataPath }.writeln(DateTime::Now());
}
```


## 81.5 (Sample) Confirm saving work content

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial5/messagebox/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Prevent the app from terminating via user actions
	System::SetTerminationTriggers(UserAction::NoAction);

	// Save file path
	constexpr FilePathView SaveDataPath = U"hsv.save";

	// Background color
	HSV hsv = ColorF{ 0.8, 0.9, 1.0 };

	// Load color from save file if it exists
	if (FileSystem::Exists(SaveDataPath))
	{
		Deserializer<BinaryReader> reader{ SaveDataPath };
		reader(hsv);
	}

	// Whether the currently selected color is saved
	bool saved = true;

	while (System::Update())
	{
		// Indicate unsaved work in window title
		Window::SetTitle(saved ? U"色の選択" : U"* 色の選択 [未保存]");

		// Set background color
		Scene::SetBackground(hsv);

		// Select color with color picker
		if (SimpleGUI::ColorPicker(hsv, Vec2{ 40,40 }))
		{
			// Mark as unsaved if there are changes
			saved = false;
		}

		// Display "Save color" button when unsaved
		if (SimpleGUI::Button(U"色を保存する", Vec2{ 240, 40 }, unspecified, (not saved))) // When button is pressed
		{
			// Save color to save file
			Serializer<BinaryWriter> writer{ SaveDataPath };
			writer(hsv);

			// Mark as saved
			saved = true;
		}

		// When window close button is pressed
		if (System::GetUserActions() & UserAction::CloseButtonClicked)
		{
			if (saved) // If already saved
			{
				// Exit without doing anything
				break;
			}
			else // If unsaved
			{
				// Display OK or Cancel message box
				const MessageBoxResult result = System::MessageBoxOKCancel(U"色の選択", U"色を保存せずにアプリケーションを終了しますか？");

				// If OK is selected
				if (result == MessageBoxResult::OK)
				{
					// Exit the program
					break;
				}
			}
		}
	}
}
```