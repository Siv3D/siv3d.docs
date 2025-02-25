# 65. クリップボード
クリップボードの情報にアクセスする方法を学びます。

## 65.1 テキストの取得とコピー
- クリップボードにあるテキストを取得するには `Clipboard::GetText()` を使います
    - 引数として、`String` 型の変数の参照を渡します
    - 取得に成功した場合、`true` を返します
- クリップボードにテキストをコピーするには `Clipboard::SetText()` を使います

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

## 65.2 画像の取得とコピー
- クリップボードにある画像を取得するには `Clipboard::GetImage()` を使います
    - 引数として、`Image` 型の変数の参照を渡します
    - 取得に成功した場合、`true` を返します
- クリップボードに画像をコピーするには `Clipboard::SetImage()` を使います
    - 透過を含む画像は未サポートです

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

			// クリップボードから画像を取得する
			if (Clipboard::GetImage(image))
			{
				// 取得した画像からテクスチャを作成する
				texture = Texture{ image };
			}
			else // クリップボードに画像がない場合
			{
				// テクスチャを解放する
				texture.release();
			}
		}

		if (SimpleGUI::Button(U"Copy", Vec2{ 640, 120 }, 100))
		{
			// 画像をクリップボードにコピーする
			Clipboard::SetImage(image);
		}

		if (texture)
		{
			texture.fitted(Scene::Size()).drawAt(400, 300);
		}
	}
}
```


## 65.3 ファイルパスの取得
- クリップボードにあるファイルパスを取得するには `Clipboard::GetFilePaths()` を使います
    - 引数として、`Array<FilePath>` 型の変数の参照を渡します
    - 取得に成功した場合、`true` を返します

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


## 65.4 内容の消去
- クリップボードの内容を消去するには `Clipboard::Clear()` を使います

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

