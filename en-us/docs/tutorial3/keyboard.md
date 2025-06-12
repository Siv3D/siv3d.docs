# 42. Keyboard Input
Learn how to handle keyboard input.

## 42.1 Key Input State
- Constants of `Input` type corresponding to each key on the keyboard are provided
- The main constant names are shown in the table below
	- For keys other than those listed below, see [`<Siv3D/Keyboard.hpp>` :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Keyboard.hpp){:target="_blank"}

| Key | Constant Name |
| --- | --- |
| A, B, C, ... | `KeyA`, `KeyB`, `KeyC`, ... |
| 1, 2, 3, ... | `Key1`, `Key2`, `Key3`, ... |
| F1, F2, F3, ... | `KeyF1`, `KeyF2`, `KeyF3`, ... |
| ↑, ↓, ←, → | `KeyUp`, `KeyDown`, `KeyLeft`, `KeyRight` |
| Space key | `KeySpace` |
| Enter key | `KeyEnter` |
| Backspace key | `KeyBackspace` |
| Tab key | `KeyTab` |
| Escape key | `KeyEscape` |
| Page up, Page down | `KeyPageUp`, `KeyPageDown` |
| Delete key | `KeyDelete` |
| Numpad 0, 1, 2, ... | `KeyNum0`, `KeyNum1`, `KeyNum2`, ... |
| Shift key | `KeyShift` |
| Left shift, Right shift | `KeyLShift`, `KeyRShift` |
| Ctrl key | `KeyControl` |
| (macOS) Command key | `KeyCommand` |
| Comma, Period, Slash | `KeyComma`, `KeyPeriod`, `KeySlash` |

- The `Input` type has the following member functions that return the state in the current frame as `bool` type
	- For example, `KeyA.down()` returns `true` when ++a++ is pressed

| Code | Not pressed | Just pressed | Held down | Just released | Released |
|:--:|:--:|:--:|:--:|:--:|:--:|
| `.down()` | false | **✔ true** | false | false | false |
| `.pressed()` | false | **✔ true** | **✔ true** | false | false |
| `.up()` | false | false | false | **✔ true** | false |

```cpp
# include <Siv3D.hpp>

Vec2 GetMove(double deltaTime)
{
	const double delta = (deltaTime * 200);

	Vec2 move{ 0, 0 };

	if (KeyLeft.pressed())
	{
		move.x -= delta;
	}

	if (KeyRight.pressed())
	{
		move.x += delta;
	}

	if (KeyUp.pressed())
	{
		move.y -= delta;
	}

	if (KeyDown.pressed())
	{
		move.y += delta;
	}

	return move;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		// Move with arrow keys
		const Vec2 move = GetMove(Scene::DeltaTime());

		pos += move;

		// Return to center when [C] key is pressed
		if (KeyC.down())
		{
			pos = Vec2{ 400, 300 };
		}

		pos.asCircle(50).draw(ColorF{ 0.2 });
	}
}
```


## 42.2 Keys with Special Operations Assigned
- In Siv3D, some keys have special operations assigned to them

### 42.2.1 Escape Key
- ++esc++ is assigned by default as a user action to terminate the application (**Tutorial 3.5**)
- To prevent the application from terminating when ++esc++ is pressed, pass only `UserAction::CloseButtonClicked` to `System::SetTerminationTriggers()` (**Tutorial 5.6**)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Set only the window close user action as the termination operation
	System::SetTerminationTriggers(UserAction::CloseButtonClicked);

	while (System::Update())
	{

	}
}
```

### 42.2.2 PrintScreen Key and F12 Key
- Pressing ++print-screen++ or ++f12++ saves a screenshot (**Tutorial 5.2**)
	- However, during debugging in Visual Studio, ++f12++ is assigned to another function and cannot be used
- To prevent screenshots from being saved when ++f12++ is pressed, pass only `{ KeyPrintScreen }` to `ScreenCapture::SetShortcutKeys()` (**Tutorial 5.3**)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Set to save screenshots only when [PrintScreen] key is pressed
	ScreenCapture::SetShortcutKeys({ KeyPrintScreen });

	while (System::Update())
	{
		Circle{ 400, 300, 100 }.draw();
	}
}
```

### 42.2.3 F1 Key
- Pressing ++f1++ displays the program's license information (**Tutorial 5.4**)
- To prevent license information from being displayed when ++f1++ is pressed, call `LicenseManager::DisableDefaultTrigger()`
- In that case, please provide a means to display license information in a browser using `LicenseManager::ShowInBrowser()` instead

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Add your application's license information
	LicenseManager::AddLicense({
		.title = U"My game",
		.copyright = U"(C) 2025 My name",
		.text = U"License" });

	// Prevent license information from being displayed when [F1] key is pressed
	LicenseManager::DisableDefaultTrigger();

	while (System::Update())
	{
		if (SimpleGUI::Button(U"License", Vec2{ 40, 40 }))
		{
			// Display license information in web browser
			LicenseManager::ShowInBrowser();
		}
	}
}
```

### 42.2.4 Alt + Enter Key (Windows)
- On Windows, you can enter **fullscreen mode** by pressing ++alt+enter++ while the application is running (**Tutorial 5.5**)
- To disable this key operation, call `Window::SetToggleFullscreenEnabled(false)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Window::SetToggleFullscreenEnabled(false);

	while (System::Update())
	{
		for (int32 y = 0; y < 10; ++y)
		{
			for (int32 x = 0; x < 10; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.5, 0.7, 0.6 });
				}
			}
		}
	}
}
```


## 42.3 Key Press Duration
- `.pressedDuration()` of `Input` returns the duration that the input has been pressed as a `Duration` type

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/keyboard/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// Duration A key has been pressed
		Print << KeyA.pressedDuration();

		// Duration Space key has been pressed
		Print << KeySpace.pressedDuration();

		// If Space key has been pressed for 1 second or more
		if (1s <= KeySpace.pressedDuration())
		{
			Print << U"Space";
		}
	}
}
```


## 42.4 Duration a Key Was Pressed
- `.pressedDuration()` is valid until the frame when that key's `.up()` returns `true`
- You can get how long a key was pressed when it's released as follows

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/keyboard/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// Display how long the Space key was pressed
		if (KeySpace.up())
		{
			Print << KeySpace.pressedDuration();
		}
	}
}
```


## 42.5 Key Names
- `.name()` of `Input` returns the name of that key as a `String` type

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << KeyA.name();
	Print << KeySpace.name();
	Print << KeyLeft.name();
	Print << Key3.name();
	Print << KeyF11.name();

	while (System::Update())
	{

	}
}
```
```txt title="Output"
A
Space
Left
3
F11
```


## 42.6 Getting All Key Inputs
- `Keyboard::GetAllInputs()` returns a list of active keys as `Array<Input>` where `.down()`, `.pressed()`, or `.up()` is `true` in the current frame

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// Get list of keys where down() / pressed() / up() is true
		const Array<Input> keys = Keyboard::GetAllInputs();

		for (const auto& key : keys)
		{
			Print << key.name() << (key.pressed() ? U" pressed" : U" up");
		}
	}
}
```


## 42.7 Key Combinations (A or B)
- You can use `|` to combine multiple key constants and check whether any of them are pressed

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// [Space] or [Enter] is pressed
		if ((KeySpace | KeyEnter).pressed())
		{
			Print << U"KeySpace / KeyEnter";
		}
	}
}
```


## 42.8 Key Combinations (A while pressing B)
- You can use `+` to combine two key constants and check whether the right key is pressed while the left key is pressed

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// [Ctrl + C] or [Command + C] was pressed
		if ((KeyControl + KeyC).down()
			|| (KeyCommand + KeyC).down())
		{
			Print << U"Ctrl + C / Command + C";
		}
	}
}
```


## 42.9 InputGroup
- The `InputGroup` type can store `Input` or combinations of `Input` using `|` and `+`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Any of [Z] [Space] [Enter]
	const InputGroup inputOK = (KeyZ | KeySpace | KeyEnter);

	// [Ctrl] + [C] or [Command] + [C]
	const InputGroup inputCopy = ((KeyControl + KeyC) | (KeyCommand + KeyC));

	while (System::Update())
	{
		if (inputOK.down())
		{
			Print << U"OK";
		}

		if (inputCopy.down())
		{
			Print << U"Copy";
		}
	}
}
```


## 42.10 Key Configuration (1)
- By assigning `InputGroup` to various operations, you can flexibly customize the operation methods

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/keyboard/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"🐥"_emoji };

	const Array<String> options
	{
		U"[←] [→] [Space]",
		U"[A] [D] [W]",
		U"[←]/[A] [→]/[D] [Space]/[W]"
	};

	size_t index = 0;

	// Operation to move left
	InputGroup inputLeft = KeyLeft;

	// Operation to move right
	InputGroup inputRight = KeyRight;

	// Operation to jump
	InputGroup inputJump = KeySpace;

	Vec2 pos{ 400, 450 };

	double jumpY = 0.0;

	while (System::Update())
	{
		// 🐥 movement
		{
			const double deltaTime = Scene::DeltaTime();

			if (inputLeft.pressed())
			{
				pos.x -= (deltaTime * 200);
			}

			if (inputRight.pressed())
			{
				pos.x += (deltaTime * 200);
			}

			if (inputJump.down())
			{
				jumpY = 500.0;
			}

			pos.y = Min((pos.y - deltaTime * jumpY), 450.0);
			jumpY = Max((jumpY - deltaTime * 1000.0), -1000.0);
		}

		// Draw background and 🐥
		{
			Rect{ 800, 500 }.draw(Arg::top(0.1, 0.4, 0.8), Arg::bottom(0.4, 0.7, 1.0));
			Rect{ 0, 500, 800, 100 }.draw(ColorF{ 0.2, 0.5, 0.3 });
			texture.drawAt(pos);
		}

		// Key configuration
		if (SimpleGUI::RadioButtons(index, options, Vec2{ 40, 40 }))
		{
			if (index == 0)
			{
				inputLeft = KeyLeft;
				inputRight = KeyRight;
				inputJump = KeySpace;
			}
			else if (index == 1)
			{
				inputLeft = KeyA;
				inputRight = KeyD;
				inputJump = KeyW;
			}
			else
			{
				inputLeft = (KeyLeft | KeyA);
				inputRight = (KeyRight | KeyD);
				inputJump = (KeySpace | KeyW);
			}
		}
	}
}
```


## 42.11 Key Configuration (2)
- If you want to detect the keys actually being pressed to set up key configuration, use `Keyboard::GetAllInputs()`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/keyboard/11.png)

```cpp
# include <Siv3D.hpp>

struct KeyConfig
{
	String name;

	Input defaultInput;

	Input input;
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	Array<KeyConfig> keyConfigs;
	keyConfigs << KeyConfig{ U"Up", KeyW, KeyW };
	keyConfigs << KeyConfig{ U"Down", KeyS, KeyS };
	keyConfigs << KeyConfig{ U"Left", KeyA, KeyA };
	keyConfigs << KeyConfig{ U"Right", KeyD, KeyD };

	Optional<size_t> selectedKeyConfig;

	while (System::Update())
	{
		if (MouseL.down())
		{
			selectedKeyConfig.reset();
		}

		for (size_t i = 0; i < keyConfigs.size(); ++i)
		{
			const KeyConfig& keyConfig = keyConfigs[i];
			const Rect rect{ 40, (40 + i * 60), 400, 50 };

			if (rect.leftClicked())
			{
				selectedKeyConfig = i;
			}

			if (selectedKeyConfig == i)
			{
				for (const auto& key : Keyboard::GetAllInputs())
				{
					if (key.down())
					{
						keyConfigs[selectedKeyConfig.value()].input = key;
						break;
					}
				}
			}

			rect.rounded(6).draw();
			font(keyConfig.name).draw(30, Arg::leftCenter = rect.leftCenter().movedBy(30, 0), ColorF{ 0.2 });
			font(keyConfig.input).draw(30, Arg::center = rect.leftCenter().movedBy(280, 0), ColorF{ 0.2 });

			if (selectedKeyConfig == i)
			{
				rect.rounded(6).drawFrame(0, 5, ColorF{ 0.1, 0.5, 1.0 });
			}
		}

		if (SimpleGUI::Button(U"Reset", Vec2{ 40, 300 }))
		{
			for (auto& keyConfig : keyConfigs)
			{
				keyConfig.input = keyConfig.defaultInput;
			}
		}
	}
}
```


## 42.12 Text Input
- By passing a `String` type variable to `TextInput::UpdateText()`, you can handle text input processing based on keyboard input
- When inputting Japanese, use `TextInput::GetEditingText()` to get unconverted text

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/keyboard/12.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	String text;

	const Rect box{ 50, 50, 700, 300 };

	while (System::Update())
	{
		// Input text from keyboard
		TextInput::UpdateText(text);

		// Get unconverted character input
		const String editingText = TextInput::GetEditingText();

		box.draw(ColorF{ 0.3 });

		font(text + U'|' + editingText).draw(30, box.stretched(-20));
	}
}
```


## 42.13 Disabling IME (Windows)
- On Windows, to disable the Japanese input IME, call `Platform::Windows::TextInput::DisableIME()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Disable IME
	Platform::Windows::TextInput::DisableIME();

	const Font font{ FontMethod::MSDF, 48 };

	String text;

	const Rect box{ 50, 50, 700, 300 };

	while (System::Update())
	{
		// Input text from keyboard
		TextInput::UpdateText(text);

		font(text).draw(30, box.stretched(-20));
	}
}
```