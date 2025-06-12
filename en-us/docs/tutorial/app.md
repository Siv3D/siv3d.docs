# 5. Basic App Operations
Learn the basic operations of Siv3D applications.

## 5.1 Exit the Program and Close the Window
- You can exit the running program with any of the following operations (see **Tutorial 3.5**):
	1. Press the window close button
	2. Press the ++esc++ key
	3. Call `System::Exit()` in the program
- When the program exits, the window is also closed
- For how to customize exit operations, see Tutorial 5.6

## 5.2 Save Screenshots
- While the program is running, save screenshots with any of the following shortcut keys:
	- Press ++print-screen++
	- Press ++f12++ (not available during Visual Studio debugging as ++f12++ is assigned to another function)
- The screenshot save location varies by OS

| OS | Save Location |
| --- | --- |
| Windows | `Screenshot` folder in the same directory as the executable file |
| macOS | `Screenshot` folder within the Pictures folder |
| Linux | `Screenshot` folder in the same directory as the executable file |

- Depending on your computer settings, the ++print-screen++ key may be associated with other applications (OneDrive or Dropbox), and screenshots may not be saved to the above location

??? example "Recording screens on Windows"
	- Windows 10 and 11 have built-in screen recording functionality
	- For details, see [**Xbox Game Bar**](../tools/gamebar.md){:target="_blank"}


## 5.3 Change Screenshot Shortcut Keys
- To change the shortcut keys for saving screenshots in the current Siv3D app, use `ScreenCapture::SetShortcutKeys({ list of keys })`

```cpp title="Change Screenshot Shortcut Keys" hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// Set to save screenshots only when [A] key is pressed
	ScreenCapture::SetShortcutKeys({ KeyA });

	while (System::Update())
	{
		Circle{ 400, 300, 100 }.draw();
	}
}
```

- You can also specify multiple keys

```cpp title="Change Screenshot Shortcut Keys" hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// Set to save screenshots only when [PrintScreen] or [P] key is pressed
	ScreenCapture::SetShortcutKeys({ KeyPrintScreen, KeyP });

	while (System::Update())
	{
		Circle{ 400, 300, 100 }.draw();
	}
}
```


## 5.4 Display License Information
- While the program is running, display license information using any of the following methods:
	- Press ++f1++
	- Call `LicenseManager::ShowInBrowser()`
- License information related to Siv3D will be displayed in a web browser

??? example "Add License Information"
	- You can use `LicenseManager::AddLicense()` to add new items at the beginning of the license information

	```cpp title="Add License Information" hl_lines="5-8"
	# include <Siv3D.hpp>

	void Main()
	{
		LicenseManager::AddLicense({
			.title = U"My game",
			.copyright = U"(C) 2025 My name",
			.text = U"License" });

		while (System::Update())
		{

		}
	}
	```


## 5.5 Full Screen with Key Operation (Windows Only)
- On Windows, you can make the window full screen with the following key operation:
	- Press ++alt+enter++
	- Press ++alt+enter++ again to return to windowed display
- This is convenient when displaying games or giving presentations


## 5.6 Change Exit Operations
- `System::Update()` returns `false` thereafter when special **user actions** to exit the application are performed
- Exit operation user actions can be changed by passing `UserAction` flags to `System::SetTerminationTriggers()`
	- When setting multiple user actions, use the bitwise OR operator `|`
- By default, the following 2 user actions are set as exit operations:

| User Action | Description |
|:--|:--|
| `UserAction::CloseButtonClicked` | Press the window close button |
| `UserAction::EscapeKeyDown` | Press ++esc++ |


### 5.6.1 Prevent Exit with ++esc++
- To prevent exiting when ++esc++ is pressed, do the following:

```cpp hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// Set only window close user action as exit operation
	System::SetTerminationTriggers(UserAction::CloseButtonClicked);

	while (System::Update())
	{

	}
}
```

### 5.6.2 Exit Only by Program
- When only `UserAction::NoAction` is passed to `System::SetTerminationTriggers()`, the window close button and ++esc++ key are no longer considered exit operations
- In this case, the application will not exit unless `System::Exit()` is called or `return` is used in `Main()`

```cpp hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// Don't set exit operations
	System::SetTerminationTriggers(UserAction::NoAction);

	while (System::Update())
	{
		// If 5 seconds have passed since program start
		if (5.0 <= Scene::Time())
		{
			System::Exit();
		}
	}
}
```


### 5.6.3 Return to Default Exit Operations
- To return exit operations to default settings (window close operation, ++esc++ key operation), pass `UserAction::Default` to `System::SetTerminationTriggers()`
- `UserAction::Default` is the same as `(UserAction::CloseButtonClicked | UserAction::EscapeKeyDown)`

```cpp hl_lines="8-9"
# include <Siv3D.hpp>

void Main()
{
	// Don't set exit operations
	System::SetTerminationTriggers(UserAction::NoAction);

	// Return to default exit operations
	System::SetTerminationTriggers(UserAction::Default);

	while (System::Update())
	{

	}
}
```


## Review Checklist
- [x] Learned that screenshots are saved with ++print-screen++ or ++f12++
- [x] Learned how to change screenshot shortcut keys
- [x] Learned how to display license information
- [x] Learned that on Windows you can make the window full screen with ++alt+enter++
- [x] Learned that program exit operations can be customized