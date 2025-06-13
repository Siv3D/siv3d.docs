# 65. Clipboard
Learn how to access clipboard information.

## 65.1 Getting and Copying Text
- To get text from the clipboard, use `Clipboard::GetText()`
    - Pass a reference to a `String` type variable as an argument
    - Returns `true` if retrieval is successful
- To copy text to the clipboard, use `Clipboard::SetText()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Paste", Vec2{ 640, 80 }, 100))
		{
			String text;
			Clipboard::GetText(text);

			ClearPrint();
			Print << text;
		}

		if (SimpleGUI::Button(U"Copy", Vec2{ 640, 120 }, 100))
		{
			const String text = U"Hello, Siv3D!";
			Clipboard::SetText(text);
		}
	}
}
```

## 65.2 Getting and Copying Images
- To get an image from the clipboard, use `Clipboard::GetImage()`
    - Pass a reference to an `Image` type variable as an argument
    - Returns `true` if retrieval is successful
- To copy an image to the clipboard, use `Clipboard::SetImage()`
    - Images with transparency are not supported

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Image image{ U"example/windmill.png" };

	Texture texture;

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Paste", Vec2{ 640, 80 }, 100))
		{
			Image image;

			// Get image from clipboard
			if (Clipboard::GetImage(image))
			{
				// Create texture from retrieved image
				texture = Texture{ image };
			}
			else // If there's no image in clipboard
			{
				// Release texture
				texture.release();
			}
		}

		if (SimpleGUI::Button(U"Copy", Vec2{ 640, 120 }, 100))
		{
			// Copy image to clipboard
			Clipboard::SetImage(image);
		}

		if (texture)
		{
			texture.fitted(Scene::Size()).drawAt(400, 300);
		}
	}
}
```


## 65.3 Getting File Paths
- To get file paths from the clipboard, use `Clipboard::GetFilePaths()`
    - Pass a reference to an `Array<FilePath>` type variable as an argument
    - Returns `true` if retrieval is successful

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Paste", Vec2{ 640, 80 }, 100))
		{
			Array<FilePath> paths;
			Clipboard::GetFilePaths(paths);

			ClearPrint();

			for (const auto& path : paths)
			{
				Print << path;
			}
		}
	}
}
```


## 65.4 Clearing Contents
- To clear the clipboard contents, use `Clipboard::Clear()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Clear", Vec2{ 640, 80 }, 100))
		{
			Clipboard::Clear();
		}
	}
}
```