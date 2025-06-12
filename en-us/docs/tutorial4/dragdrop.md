# 64. Drag & Drop
Learn how to get information about files that are dragged and dropped.

## 64.1 Getting Dropped Files
- You can check if there are files dragged and dropped onto the application window using `DragDrop::HasNewFilePaths()`
- When this function returns `true`, you can call `DragDrop::GetDroppedFilePaths()` to get the list of dropped files as `Array<DroppedFilePath>` type
- The member variables of `DroppedFilePath` are as follows:

| Code | Description |
|--|--|
| `FilePath path` | Absolute path of file or directory |
| `Point pos` | Position where it was dropped (scene coordinates) |
| `uint64 timeMillisec` | Time point when it was dropped<br>(app runtime measured by `Time::GetMillisec()`) |

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (DragDrop::HasNewFilePaths())
		{
			for (auto&& [path, pos, timeMillisec] : DragDrop::GetDroppedFilePaths())
			{
				Print << U"{} @{} :{}"_fmt(path, pos, timeMillisec);
			}
		}
	}
}
```


## 64.2 Disabling File Drop
- To prevent files from being dropped onto the current application window, call `DragDrop::AcceptFilePaths(false)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Don't accept file path drops
	DragDrop::AcceptFilePaths(false);

	while (System::Update())
	{		
		// Nothing will be dropped since we don't accept them
		if (DragDrop::HasNewFilePaths())
		{
			for (auto&& [path, pos, timeMillisec] : DragDrop::GetDroppedFilePaths())
			{
				Print << U"{} @{} :{}"_fmt(path, pos, timeMillisec);
			}
		}
	}
}
```


## 64.3 Getting Dropped Text
- Text (string) drops to the application window are disabled by default
- You can enable text drops by calling `DragDrop::AcceptText(true)`
- You can check if there's text dragged and dropped onto the application window using `DragDrop::HasNewText()`
- When this function returns `true`, you can call `DragDrop::GetDroppedText()` to get the list of dropped text as `Array<DroppedText>` type
- The member variables of `DroppedText` are as follows:

| Code | Description |
|--|--|
| `String text` | Dropped text |
| `Point pos` | Position where it was dropped (scene coordinates) |
| `uint64 timeMillisec` | Time point when it was dropped<br>(app runtime measured by `Time::GetMillisec()`) |

- You can test text dropping by dragging selected text on a web browser

```cpp
# include <Siv3D.hpp>

void Main()
{
	DragDrop::AcceptText(true);

	while (System::Update())
	{
		if (DragDrop::HasNewText())
		{
			for (auto&& [text, pos, timeMillisec] : DragDrop::GetDroppedText())
			{
				Print << U"{} @{} :{}"_fmt(text, pos, timeMillisec);
			}
		}
	}
}
```


## 64.4 Clearing Dropped Item Information
- Information about dropped items is cleared when you call `DragDrop::GetDroppedFilePaths()` and `DragDrop::GetDroppedText()`, but you can also clear it using `DragDrop::Clear()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{		
		if (DragDrop::HasNewFilePaths())
		{
			// Clear dropped item information
			DragDrop::Clear();

			// Nothing will be retrieved since it's been cleared
			for (auto&& [path, pos, timeMillisec] : DragDrop::GetDroppedFilePaths())
			{
				Print << U"{} @{} :{}"_fmt(path, pos, timeMillisec);
			}
		}
	}
}
```


## 64.5 Getting Information While Dragging
- To get information about items being dragged over the window, use `DragDrop::DragOver()`
- This function returns `Optional<DragStatus>`, or `none` if there are no items being dragged
- The member variables of `DragStatus` are as follows:

| Code | Description |
|--|--|
| `DragItemType itemType` | Type of item being dragged<br>`DragItemType::FilePaths` or `DragItemType::Text` |
| `Point cursorPos` | Position of cursor while dragging (scene coordinates) |

- The following sample code displays an icon on screen when trying to drop files

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture icon{ 0xf15b_icon, 40 };

	while (System::Update())
	{
		// There's an item being dragged over the window
		if (const auto status = DragDrop::DragOver())
		{
			if (status->itemType == DragItemType::FilePaths)
			{
				// Display icon
				icon.drawAt(status->cursorPos, ColorF{ 0.1 });
			}
		}

		if (DragDrop::HasNewFilePaths())
		{
			for (auto&& [path, pos, timeMillisec] : DragDrop::GetDroppedFilePaths())
			{
				Print << U"{} @{} :{}"_fmt(path, pos, timeMillisec);
			}
		}
	}
}
```


## 64.6 Starting a Drag (Windows)
- In the Windows version, you can start dragging files from within the application window
- Call `Platform::Windows::DragDrop::MakeDragDrop(file path)` to start dragging the specified file path
- You can also specify multiple file paths with an array

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"example/windmill.png" };

	const Rect rect{ 80, 80, texture.size() };

	while (System::Update())
	{
		if (rect.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);

			if (MouseL.down())
			{
				Platform::Windows::DragDrop::MakeDragDrop(U"example/windmill.png");
			}
		}

		rect(texture).draw();
	}
}
```