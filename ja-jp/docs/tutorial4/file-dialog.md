# 61. ファイルダイアログ
ファイルダイアログを開いて画像やオーディオを読み込んだり、オープンするファイルや、ファイルの保存名を決める方法を学びます。

## 61.1 ファイルダイアログの概要
- ファイルダイアログを使うことで、ユーザーにファイルを選択させることができます
- ファイルダイアログ機能は、ファイルのパスを取得するだけの関数と、実際のファイルの読み込みや保存を合わせて行う関数があります
- ダイアログがオープンされている間、メインスレッドの処理は停止します
- Web 版の Siv3D では、プラットフォームの制約から、別途用意されたファイルダイアログ関数を使う必要があります


## 61.2 画像を開く
- ファイルダイアログを使って画像ファイルを選択し、テクスチャを作成するには `Dialog::OpenTexture()` を使います
- 選択がキャンセルされた場合や、テクスチャの作成に失敗した場合は空のテクスチャを返します
- `TextureDesc::Mipped`（**チュートリアル 31.8**）を引数に渡すと、テクスチャの作成時に適用されます

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルダイアログを使ってテクスチャをオープン
	Texture texture = Dialog::OpenTexture();

	while (System::Update())
	{
		if (texture)
		{
			// シーンのサイズにフィットさせて中心に描く
			texture.fitted(Scene::Size()).drawAt(400, 300);
		}

		if (SimpleGUI::Button(U"Open", Vec2{ 20, 20 }))
		{
			// TextureDesc を指定することもできる
			texture = Dialog::OpenTexture(TextureDesc::Mipped);
		}
	}
}
```

- 初期フォルダを指定するには次のようにします

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルダイアログを使ってテクスチャをオープン
	Texture texture = Dialog::OpenTexture(U"example/");

	while (System::Update())
	{
		if (texture)
		{
			// シーンのサイズにフィットさせて中心に描く
			texture.fitted(Scene::Size()).drawAt(400, 300);
		}

		if (SimpleGUI::Button(U"Open", Vec2{ 20, 20 }))
		{
			// TextureDesc を指定することもできる
			texture = Dialog::OpenTexture(TextureDesc::Mipped, U"example/");
		}
	}
}
```


## 61.3 オーディオを開く
- ファイルダイアログを使って音声ファイルを選択し、オーディオを作成するには `Dialog::OpenAudio()` を使います
- 選択がキャンセルされた場合や、オーディオの作成に失敗した場合は空のオーディオを返します
- `Audio::Stream`（**チュートリアル 41.3**）を引数に渡すと、オーディオの作成時に適用されます

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルダイアログを使ってオーディオをオープン
	Audio audio = Dialog::OpenAudio();

	while (System::Update())
	{
		if (audio && (not audio.isPlaying()))
		{
			audio.play();
		}

		if (SimpleGUI::Button(U"Open", Vec2{ 20, 20 }))
		{
			// Audio::Stream を指定することもできる
			audio = Dialog::OpenAudio(Audio::Stream);
		}
	}
}
```

- 初期フォルダを指定するには次のようにします

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルダイアログを使ってオーディオをオープン
	Audio audio = Dialog::OpenAudio(U"example/");

	while (System::Update())
	{
		if (audio && (not audio.isPlaying()))
		{
			audio.play();
		}

		if (SimpleGUI::Button(U"Open", Vec2{ 20, 20 }))
		{
			// Audio::Stream を指定することもできる
			audio = Dialog::OpenAudio(Audio::Stream, U"example/");
		}
	}
}
```


## 61.4 フォルダの選択
- フォルダ選択ダイアログを使ってフォルダを選択するには `Dialog::SelectFolder()` を使います
- 結果は `Optional<FilePath>` 型で返され、キャンセルされた場合は `none` を返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	// フォルダ選択ダイアログを使ってフォルダを選択
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

- 初期フォルダを指定するには次のようにします

```cpp
# include <Siv3D.hpp>

void Main()
{
	// フォルダ選択ダイアログを使ってフォルダを選択
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
			// 現在のカレントディレクトリを初期フォルダにする
			path = Dialog::SelectFolder(U"./");
		}
	}
}
```


## 61.5 ファイルの選択
- オープンファイルダイアログを使って 1 つのファイルパスを選択するには `Dialog::OpenFile()` を使います
- 結果は `Optional<FilePath>` 型で返され、キャンセルされた場合は `none` を返します
- 引数の `Array<FileFilter>` 型で、どのような拡張子のファイルを対象にするかを設定します
- この関数はオープンするファイル名を取得するだけであるため、ファイルに対する処理は別途記述する必要があります

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルオープンダイアログを使ってファイルを選択
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
			// 画像ファイル
			path = Dialog::OpenFile({ FileFilter::AllImageFiles() });
		}

		if (SimpleGUI::Button(U"Open text file", Vec2{ 20, 120 }))
		{
			// .txt
			path = Dialog::OpenFile({ FileFilter::Text() });
		}

		if (SimpleGUI::Button(U"Open .bin file", Vec2{ 20, 160 }))
		{
			// 独自の拡張子
			path = Dialog::OpenFile({ { U"Binary file", { U"bin" } } });
		}
	}
}
```

- 初期フォルダを指定するには次のようにします

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルオープンダイアログを使ってファイルを選択
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
			// 画像ファイル
			path = Dialog::OpenFile({ FileFilter::AllImageFiles() }, U"example/");
		}

		if (SimpleGUI::Button(U"Open text file", Vec2{ 20, 120 }))
		{
			// .txt
			path = Dialog::OpenFile({ FileFilter::Text() }, U"example/");
		}

		if (SimpleGUI::Button(U"Open .bin file", Vec2{ 20, 160 }))
		{
			// 独自の拡張子
			path = Dialog::OpenFile({ { U"Binary file", { U"bin" } } }, U"example/");
		}
	}
}
```


## 61.6 ファイルの選択（複数）
- オープンファイルダイアログを使って複数のファイルパスを選択するには `Dialog::OpenFiles()` を使います
- 結果は `Array<FilePath>` 型で返され、キャンセルされた場合は空の配列を返します
- 引数の `Array<FileFilter>` 型で、どのような拡張子のファイルを対象にするかを設定します
- この関数はオープンするファイル名を取得するだけであるため、ファイルに対する処理は別途記述する必要があります

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルオープンダイアログを使ってファイルを選択
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
			// 画像ファイル
			paths = Dialog::OpenFiles({ FileFilter::AllImageFiles() });
		}

		if (SimpleGUI::Button(U"Open text file", Vec2{ 600, 120 }))
		{
			// .txt
			paths = Dialog::OpenFiles({ FileFilter::Text() });
		}

		if (SimpleGUI::Button(U"Open .bin file", Vec2{ 600, 160 }))
		{
			// 独自の拡張子
			paths = Dialog::OpenFiles({ { U"Binary file",{ U"bin" } } });
		}
	}
}
```

- 初期フォルダを指定するには次のようにします

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルオープンダイアログを使ってファイルを選択
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
			// 画像ファイル
			paths = Dialog::OpenFiles({ FileFilter::AllImageFiles() }, U"example/");
		}

		if (SimpleGUI::Button(U"Open text file", Vec2{ 600, 120 }))
		{
			// .txt
			paths = Dialog::OpenFiles({ FileFilter::Text() }, U"example/");
		}

		if (SimpleGUI::Button(U"Open .bin file", Vec2{ 600, 160 }))
		{
			// 独自の拡張子
			paths = Dialog::OpenFiles({ { U"Binary file",{ U"bin" } } }, U"example/");
		}
	}
}
```


## 61.6 画像の保存
- 画像データ（**チュートリアル 63**）を、セーブファイルダイアログを使って保存するには `Image` クラスの `.saveWithDialog()` を使います
- ファイルダイアログが開き、有効なファイルパスが選択されと、画像を保存します
- 同名のファイルが存在する場合、上書き保存されます
- 画像形式は拡張子から自動的に判断されます

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Image image{ U"example/windmill.png" };

	// 画像を左右反転する
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


## 61.7 ファイルの保存
- セーブファイルダイアログを使って保存するファイル名を決定するには `Dialog::SaveFile()` を使います
- 結果は `Optional<FilePath>` 型で返され、キャンセルされた場合は `none` を返します
- 引数の `Array<FileFilter>` 型で、保存するファイルの拡張子を設定できます。
- この関数は保存するファイル名を取得するだけであるため、ファイルに対する処理は別途記述する必要があります

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
			// ファイルセーブダイアログを使ってファイル名を選択
			// PNG ファイル
			path = Dialog::SaveFile({ FileFilter::PNG() });
		}

		if (SimpleGUI::Button(U"Save text file", Vec2{ 20, 120 }))
		{
			// .txt
			path = Dialog::SaveFile({ FileFilter::Text() });
		}

		if (SimpleGUI::Button(U"Open .bin file", Vec2{ 20, 160 }))
		{
			// 独自の拡張子
			path = Dialog::SaveFile({ { U"Binary file",{ U"bin" } } });
		}
	}
}
```

- 初期フォルダを指定するには次のようにします

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
			// ファイルセーブダイアログを使ってファイル名を選択
			// PNG ファイル
			path = Dialog::SaveFile({ FileFilter::PNG() }, U"example/");
		}

		if (SimpleGUI::Button(U"Save text file", Vec2{ 20, 120 }))
		{
			// .txt
			path = Dialog::SaveFile({ FileFilter::Text() }, U"example/");
		}

		if (SimpleGUI::Button(U"Open .bin file", Vec2{ 20, 160 }))
		{
			// 独自の拡張子
			path = Dialog::SaveFile({ { U"Binary file",{ U"bin" } } }, U"example/");
		}
	}
}
```
