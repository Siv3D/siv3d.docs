# Message Box

## 1. Overview
The message box feature allows you to display message boxes as separate windows from the main window, requesting user responses and obtaining user selections. While the message box is displayed, the main loop program progress is blocked.

## 2. Message Box API

### Functions

```cpp title="Displays a message box with an 'OK' button and returns the result."
MessageBoxResult System::MessageBoxOK(StringView text, MessageBoxStyle style = MessageBoxStyle::Default);

MessageBoxResult System::MessageBoxOK(StringView title, StringView text, MessageBoxStyle style = MessageBoxStyle::Default);
```

`title`
:   Message box title

`text`
:   Body text

`style`
:   Style

`Return value`
:   `MessageBoxResult::OK`


-----------------------------------


```cpp title="Displays a message box with 'OK' and 'Cancel' buttons and returns the result."
MessageBoxResult System::MessageBoxOKCancel(StringView text, MessageBoxStyle style = MessageBoxStyle::Default);

MessageBoxResult System::MessageBoxOKCancel(StringView title, StringView text, MessageBoxStyle style = MessageBoxStyle::Default);
```

`title`
:   Message box title

`text`
:   Body text

`style`
:   Style

`Return value`
:   `MessageBoxResult::OK` or `MessageBoxResult::Cancel`


-----------------------------------


```cpp title="Displays a message box with 'Yes' and 'No' buttons and returns the result."
MessageBoxResult System::MessageBoxYesNo(StringView text, MessageBoxStyle style = MessageBoxStyle::Default);

MessageBoxResult System::MessageBoxYesNo(StringView title, StringView text, MessageBoxStyle style = MessageBoxStyle::Default);
```

`title`
:   Message box title

`text`
:   Body text

`style`
:   Style

`Return value`
:   `MessageBoxResult::Yes` or `MessageBoxResult::No`


### Enumerations

#### MessageBoxResult
Constants representing user operations on message boxes. On some platforms, it may be possible to close the message box without selecting a button.

| Value | Description |
|--|--|
| `OK` | 'OK' was pressed |
| `Cancel` | 'Cancel' was pressed or the message box was closed |
| `Yes` | 'Yes' was pressed |
| `No` | 'No' was pressed |

#### MessageBoxStyle
Constants representing message box styles. If a style doesn't exist on a platform, the normal style is used.

| Value | Description |
|--|--|
| `Default` | Normal style |
| `Info` | Style for conveying information |
| `Warning` | Style for conveying warnings |
| `Error` | Style for conveying serious errors |
| `Question` | Style with question mark |



## 3. Message Box Samples

### 3.1 Exit program after a certain time

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 5-second countdown timer
	Timer timer{ 5s, StartImmediately::Yes };

	while (System::Update())
	{
		ClearPrint();

		// Display remaining time
		Print << U"Time remaining " << timer.format(U"mm:ss");

		// When timer reaches zero
		if (timer.reachedZero())
		{
			// Display OK message box
			System::MessageBoxOK(U"Trial Version End", U"This is as far as you can play with the trial version.");

			// Exit program
			return;
		}
	}
}
```


### 3.2 Confirm exit when window close button is pressed

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Prevent app from terminating by user action
	System::SetTerminationTriggers(UserAction::NoAction);

	while (System::Update())
	{
		// When window close button is pressed
		if (System::GetUserActions() & UserAction::CloseButtonClicked)
		{
			// Display Yes or No message box
			const MessageBoxResult result = System::MessageBoxYesNo(U"Do you want to exit the application?");

			// If Yes is chosen
			if (result == MessageBoxResult::Yes)
			{
				// Exit program
				return;
			}
		}
	}
}
```

### 3.3 Confirm loading previous save data at startup

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Save file path
	constexpr FilePathView SaveDataPath = U"save.txt";

	// Loaded save data
	String saveData;

	// If previous data exists
	if (FileSystem::Exists(SaveDataPath))
	{
		// Display Yes or No message box
		const MessageBoxResult result = System::MessageBoxYesNo(U"Previous data found. Do you want to load it and continue from there?");

		// If Yes is chosen
		if (result == MessageBoxResult::Yes)
		{
			// Read string from save file
			saveData = TextReader{ SaveDataPath }.readAll();
		}
	}

	// If save data was loaded
	if (saveData)
	{
		Print << U"Previous save data: " << saveData;
	}
	else
	{
		Print << U"New data";
	}

	while (System::Update())
	{

	}

	// Write save data (current date and time) to save file before exiting
	TextWriter{ SaveDataPath }.writeln(DateTime::Now());
}
```


### 3.4 Confirm saving work content

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Prevent app from terminating by user action
	System::SetTerminationTriggers(UserAction::NoAction);

	// Save file path
	constexpr FilePathView SaveDataPath = U"save-hsv.txt";

	// Background color
	HSV hsv = ColorF{ 0.8, 0.9, 1.0 };

	// Load color from save file if it exists
	if (FileSystem::Exists(SaveDataPath))
	{
		Deserializer<BinaryReader> reader{ SaveDataPath };
		reader(hsv);
	}

	// Whether currently selected color is saved
	bool saved = true;

	while (System::Update())
	{
		// Notify in window title if work is unsaved
		Window::SetTitle(saved ? U"Color Selection" : U"* Color Selection [Unsaved]");

		// Set background color
		Scene::SetBackground(hsv);

		// Select color with color picker
		if (SimpleGUI::ColorPicker(hsv, Vec2{ 40,40 }))
		{
			// Set to unsaved state if there are changes
			saved = false;
		}

		// If unsaved, display "Save Color" button
		if (SimpleGUI::Button(U"Save Color", Vec2{ 240, 40 }, unspecified, (not saved))) // If button is pressed
		{
			// Save color to save file
			Serializer<BinaryWriter> writer{ SaveDataPath };
			writer(hsv);

			// Set to saved state
			saved = true;
		}

		// When window close button is pressed
		if (System::GetUserActions() & UserAction::CloseButtonClicked)
		{
			if (saved) // If saved
			{
				return; // Exit without doing anything
			}
			else // If unsaved
			{
				// Display OK or Cancel message box
				const MessageBoxResult result = System::MessageBoxOKCancel(U"Color Selection", U"Do you want to exit the application without saving the color?");

				// If OK is chosen
				if (result == MessageBoxResult::OK)
				{
					// Exit program
					return;
				}
			}
		}
	}
}
```

### 3.5 Create your own message box equivalent function

```cpp
# include <Siv3D.hpp>

namespace s3dx
{
	class SceneMessageBoxImpl
	{
	public:

		static constexpr Size MessageBoxSize{ 360, 240 };

		static constexpr Size MessageBoxButtonSize{ 120, 40 };

		static constexpr ColorF MessageBoxBackgroundColor{ 0.96 };

		static constexpr ColorF MessageBoxActiveButtonColor{ 1.0 };

		static constexpr ColorF MessageBoxTextColor{ 0.11 };

		SceneMessageBoxImpl()
		{
			System::SetTerminationTriggers(UserAction::NoAction);
			Scene::SetBackground(ColorF{ 0.11 });
		}

		~SceneMessageBoxImpl()
		{
			System::SetTerminationTriggers(m_triggers);
			Scene::SetBackground(m_bgColor);
		}

		MessageBoxResult show(StringView text, const std::pair<String, MessageBoxResult>& button) const
		{
			while (System::Update())
			{
				drawMessageBox(text);
				m_buttonC.draw(m_buttonC.mouseOver() ? MessageBoxActiveButtonColor : MessageBoxBackgroundColor).drawFrame(0, 1, MessageBoxTextColor);
				m_font(button.first).drawAt(m_buttonC.center().moveBy(0, -1), MessageBoxTextColor);

				if (m_buttonC.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);

					if (MouseL.down())
					{
						break;
					}
				}
			}

			return button.second;
		}

		MessageBoxResult show(const StringView text, const std::pair<String, MessageBoxResult>& button0, const std::pair<String, MessageBoxResult>& button1) const
		{
			MessageBoxResult result = MessageBoxResult::Cancel;

			while (System::Update())
			{
				drawMessageBox(text);
				m_buttonL.draw(m_buttonL.mouseOver() ? MessageBoxActiveButtonColor : MessageBoxBackgroundColor).drawFrame(0, 1, MessageBoxTextColor);
				m_buttonR.draw(m_buttonR.mouseOver() ? MessageBoxActiveButtonColor : MessageBoxBackgroundColor).drawFrame(0, 1, MessageBoxTextColor);
				m_font(button0.first).drawAt(m_buttonL.center().moveBy(0, -1), MessageBoxTextColor);
				m_font(button1.first).drawAt(m_buttonR.center().moveBy(0, -1), MessageBoxTextColor);

				if (m_buttonL.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);

					if (MouseL.down())
					{
						result = button0.second;
						break;
					}
				}
				else if (m_buttonR.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);

					if (MouseL.down())
					{
						result = button1.second;
						break;
					}
				}
			}

			return result;
		}

	private:

		Transformer2D m_tr{ Mat3x2::Identity(), Mat3x2::Identity(), Transformer2D::Target::SetLocal };

		ScopedRenderStates2D m_rs{ BlendState::Default2D, SamplerState::Default2D, RasterizerState::Default2D };

		uint32 m_triggers = System::GetTerminationTriggers();

		ColorF m_bgColor = Scene::GetBackground();

		Vec2 m_pos = ((Scene::Size() - MessageBoxSize) * 0.5);

		RectF m_messageBoxRect{ m_pos, MessageBoxSize };

		RectF m_buttonC = RectF{ Arg::bottomCenter(m_messageBoxRect.bottomCenter().movedBy(0, -20)), MessageBoxButtonSize };

		RectF m_buttonL = RectF{ Arg::bottomCenter(m_messageBoxRect.bottomCenter().movedBy(-80, -20)), MessageBoxButtonSize };

		RectF m_buttonR = RectF{ Arg::bottomCenter(m_messageBoxRect.bottomCenter().movedBy(80, -20)), MessageBoxButtonSize };

		Font m_font = SimpleGUI::GetFont();

		void drawMessageBox(StringView text) const
		{
			m_messageBoxRect.draw(MessageBoxBackgroundColor).stretched(-5).drawFrame(1, 0, MessageBoxTextColor);
			m_font(text).draw(14, m_messageBoxRect.stretched(-20, -20, -80, -20), MessageBoxTextColor);
		}
	};

	inline MessageBoxResult SceneMessageBoxOK(StringView text)
	{
		return SceneMessageBoxImpl{}.show(text, { U"OK", MessageBoxResult::OK });
	}

	[[nodiscard]]
	inline MessageBoxResult SceneMessageBoxOKCancel(StringView text)
	{
		return SceneMessageBoxImpl{}.show(text, { U"OK", MessageBoxResult::OK }, { U"Cancel", MessageBoxResult::Cancel });
	}

	[[nodiscard]]
	inline MessageBoxResult SceneMessageBoxYesNo(StringView text)
	{
		return SceneMessageBoxImpl{}.show(text, { U"Yes", MessageBoxResult::Yes }, { U"No", MessageBoxResult::No });
	}
}

void Main()
{
	// 5-second countdown timer
	Timer timer{ 5s, StartImmediately::Yes };

	while (System::Update())
	{
		ClearPrint();

		// Display remaining time
		Print << U"Time remaining " << timer.format(U"mm:ss");

		// When timer reaches zero
		if (timer.reachedZero())
		{
			// Display OK message box
			s3dx::SceneMessageBoxOK(U"This is as far as you can play with the trial version.");

			// Exit program
			return;
		}
	}
}
```