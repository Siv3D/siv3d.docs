# 61. File Dialogs
Learn how to open file dialogs to load images and audio, and to determine files to open or save names.

## 61.1 Overview of File Dialogs
- File dialogs allow users to select files
- File dialog functionality includes functions that only get file paths, and functions that also perform actual file loading or saving
- While a dialog is open, main thread processing is paused
- In the web version of Siv3D, due to platform constraints, you need to use separately provided file dialog functions


## 61.2 Opening Images
- To use a file dialog to select an image file and create a texture, use `Dialog::OpenTexture()`
- Returns an empty texture if selection is cancelled or texture creation fails
- You can pass `TextureDesc::Mipped` (**Tutorial 31.8**) as an argument to apply it during texture creation

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Open texture using file dialog
	Texture texture = Dialog::OpenTexture();

	while (System::Update())
	{
		if (texture)
		{
			// Draw fitted to scene size and centered
			texture.fitted(Scene::Size()).drawAt(400, 300);
		}

		if (SimpleGUI::Button(U"Open", Vec2{ 20, 20 }))
		{
			// You can also specify TextureDesc
			texture = Dialog::OpenTexture(TextureDesc::Mipped);
		}
	}
}
```

- To specify an initial folder, do the following:

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Open texture using file dialog
	Texture texture = Dialog::OpenTexture(U"example/");

	while (System::Update())
	{
		if (texture)
		{
			// Draw fitted to scene size and centered
			texture.fitted(Scene::Size()).drawAt(400, 300);
		}

		if (SimpleGUI::Button(U"Open", Vec2{ 20, 20 }))
		{
			// You can also specify TextureDesc
			texture = Dialog::OpenTexture(TextureDesc::Mipped, U"example/");
		}
	}
}
```


## 61.3 Opening Audio
- To use a file dialog to select an audio file and create audio, use `Dialog::OpenAudio()`
- Returns empty audio if selection is cancelled or audio creation fails
- You can pass `Audio::Stream` (**Tutorial 41.3**) as an argument to apply it during audio creation

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Open audio using file dialog
	Audio audio = Dialog::OpenAudio();

	while (System::Update())
	{
		if (audio && (not audio.isPlaying()))
		{
			audio.play();
		}

		if (SimpleGUI::Button(U"Open", Vec2{ 20, 20 }))
		{
			// You can also specify Audio::Stream
			audio = Dialog::OpenAudio(Audio::Stream);
		}
	}
}
```

- To specify an initial folder, do the following:

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Open audio using file dialog
	Audio audio = Dialog::OpenAudio(U"example/");

	while (System::Update())
	{
		if (audio && (not audio.isPlaying()))
		{
			audio.play();
		}

		if (SimpleGUI::Button(U"Open", Vec2{ 20, 20 }))
		{
			// You can also specify Audio::Stream
			audio = Dialog::OpenAudio(Audio::Stream, U"example/");
		}
	}
}
```


## 61.4 Folder Selection
- To select a folder using a folder selection dialog, use `Dialog::SelectFolder()`
- Returns `Optional<FilePath>` type, returning `none` if cancelled

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Select folder using folder selection dialog
	Optional<FilePath> path = Dialog::SelectFolder();

	while (System::Update())
	{
		ClearPrint();

		if (path)
		{
			Print << *path;
		}

		if (SimpleGUI::Button(U"Open", Vec2{ 20, 80 }))
		{
			path = Dialog::SelectFolder();
		}
	}
}
```

- To specify an initial folder, do the following:

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Select folder using folder selection dialog
	Optional<FilePath> path = Dialog::SelectFolder(U"example/");

	while (System::Update())
	{
		ClearPrint();

		if (path)
		{
			Print << *path;
		}

		if (SimpleGUI::Button(U"Open", Vec2{ 20, 80 }))
		{
			// Use current directory as initial folder
			path = Dialog::SelectFolder(U"./");
		}
	}
}
```


## 61.5 File Selection
- To select a single file path using an open file dialog, use `Dialog::OpenFile()`
- Returns `Optional<FilePath>` type, returning `none` if cancelled
- Use the `Array<FileFilter>` type argument to set what file extensions to target
- This function only gets the file name to open, so you need to write file processing separately

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Select file using file open dialog
	Optional<FilePath> path = Dialog::OpenFile({ FileFilter::AllFiles() });

	while (System::Update())
	{
		ClearPrint();

		if (path)
		{
			Print << *path;
		}

		if (SimpleGUI::Button(U"Open image file", Vec2{ 20, 80 }))
		{
			// Image files
			path = Dialog::OpenFile({ FileFilter::AllImageFiles() });
		}

		if (SimpleGUI::Button(U"Open text file", Vec2{ 20, 120 }))
		{
			// .txt
			path = Dialog::OpenFile({ FileFilter::Text() });
		}

		if (SimpleGUI::Button(U"Open .bin file", Vec2{ 20, 160 }))
		{
			// Custom extension
			path = Dialog::OpenFile({ { U"Binary file", { U"bin" } } });
		}
	}
}
```

- To specify an initial folder, do the following:

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Select file using file open dialog
	Optional<FilePath> path = Dialog::OpenFile({ FileFilter::AllFiles() }, U"example/");

	while (System::Update())
	{
		ClearPrint();

		if (path)
		{
			Print << *path;
		}

		if (SimpleGUI::Button(U"Open image file", Vec2{ 20, 80 }))
		{
			// Image files
			path = Dialog::OpenFile({ FileFilter::AllImageFiles() }, U"example/");
		}

		if (SimpleGUI::Button(U"Open text file", Vec2{ 20, 120 }))
		{
			// .txt
			path = Dialog::OpenFile({ FileFilter::Text() }, U"example/");
		}

		if (SimpleGUI::Button(U"Open .bin file", Vec2{ 20, 160 }))
		{
			// Custom extension
			path = Dialog::OpenFile({ { U"Binary file", { U"bin" } } }, U"example/");
		}
	}
}
```


## 61.6 File Selection (Multiple)
- To select multiple file paths using an open file dialog, use `Dialog::OpenFiles()`
- Returns `Array<FilePath>` type, returning an empty array if cancelled
- Use the `Array<FileFilter>` type argument to set what file extensions to target
- This function only gets the file names to open, so you need to write file processing separately

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Select files using file open dialog
	Array<FilePath> paths = Dialog::OpenFiles({ FileFilter::AllFiles() });

	while (System::Update())
	{
		ClearPrint();

		for (const auto& path : paths)
		{
			Print << path;
		}

		if (SimpleGUI::Button(U"Open image file", Vec2{ 600, 80 }))
		{
			// Image files
			paths = Dialog::OpenFiles({ FileFilter::AllImageFiles() });
		}

		if (SimpleGUI::Button(U"Open text file", Vec2{ 600, 120 }))
		{
			// .txt
			paths = Dialog::OpenFiles({ FileFilter::Text() });
		}

		if (SimpleGUI::Button(U"Open .bin file", Vec2{ 600, 160 }))
		{
			// Custom extension
			paths = Dialog::OpenFiles({ { U"Binary file",{ U"bin" } } });
		}
	}
}
```

- To specify an initial folder, do the following:

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Select files using file open dialog
	Array<FilePath> paths = Dialog::OpenFiles({ FileFilter::AllFiles() }, U"example/");

	while (System::Update())
	{
		ClearPrint();

		for (const auto& path : paths)
		{
			Print << path;
		}

		if (SimpleGUI::Button(U"Open image file", Vec2{ 600, 80 }))
		{
			// Image files
			paths = Dialog::OpenFiles({ FileFilter::AllImageFiles() }, U"example/");
		}

		if (SimpleGUI::Button(U"Open text file", Vec2{ 600, 120 }))
		{
			// .txt
			paths = Dialog::OpenFiles({ FileFilter::Text() }, U"example/");
		}

		if (SimpleGUI::Button(U"Open .bin file", Vec2{ 600, 160 }))
		{
			// Custom extension
			paths = Dialog::OpenFiles({ { U"Binary file",{ U"bin" } } }, U"example/");
		}
	}
}
```


## 61.6 Saving Images
- To save image data (**Tutorial 63**) using a save file dialog, use the `Image` class's `.saveWithDialog()`
- Opens a file dialog and saves the image when a valid file path is selected
- If a file with the same name exists, it will be overwritten
- The image format is automatically determined from the extension

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Image image{ U"example/windmill.png" };

	// Flip the image horizontally
	image.mirror();

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Save", Vec2{ 40, 40 }))
		{
			const bool result = image.saveWithDialog();

			if (result)
			{
				Print << U"Saved!";
			}
		}
	}
}
```


## 61.7 File Saving
- To determine a save file name using a save file dialog, use `Dialog::SaveFile()`
- Returns `Optional<FilePath>` type, returning `none` if cancelled
- You can set the file extension for saving using the `Array<FileFilter>` type argument
- This function only gets the file name to save, so you need to write file processing separately

```cpp
 include <Siv3D.hpp>

void Main()
{
	Optional<FilePath> path;

	while (System::Update())
	{
		ClearPrint();

		if (path)
		{
			Print << *path;
		}

		if (SimpleGUI::Button(U"Save PNG file", Vec2{ 20, 80 }))
		{
			// Select file name using file save dialog
			// PNG file
			path = Dialog::SaveFile({ FileFilter::PNG() });
		}

		if (SimpleGUI::Button(U"Save text file", Vec2{ 20, 120 }))
		{
			// .txt
			path = Dialog::SaveFile({ FileFilter::Text() });
		}

		if (SimpleGUI::Button(U"Open .bin file", Vec2{ 20, 160 }))
		{
			// Custom extension
			path = Dialog::SaveFile({ { U"Binary file",{ U"bin" } } });
		}
	}
}
```

- To specify an initial folder, do the following:

```cpp
# include <Siv3D.hpp>

void Main()
{
	Optional<FilePath> path;

	while (System::Update())
	{
		ClearPrint();

		if (path)
		{
			Print << *path;
		}

		if (SimpleGUI::Button(U"Save PNG file", Vec2{ 20, 80 }))
		{
			// Select file name using file save dialog
			// PNG file
			path = Dialog::SaveFile({ FileFilter::PNG() }, U"example/");
		}

		if (SimpleGUI::Button(U"Save text file", Vec2{ 20, 120 }))
		{
			// .txt
			path = Dialog::SaveFile({ FileFilter::Text() }, U"example/");
		}

		if (SimpleGUI::Button(U"Open .bin file", Vec2{ 20, 160 }))
		{
			// Custom extension
			path = Dialog::SaveFile({ { U"Binary file",{ U"bin" } } }, U"example/");
		}
	}
}
```