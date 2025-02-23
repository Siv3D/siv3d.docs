# 64. ドラッグ & ドロップ
ドラッグ & ドロップされたファイルの情報を取得する方法を学びます。

## 64.1 ドロップされたファイルの取得
- アプリケーションのウィンドウに対してドラッグ & ドロップされたファイルがあるかを `DragDrop::HasNewFilePaths()` で取得できます
- この関数が `true` を返したとき、`DragDrop::GetDroppedFilePaths()` を呼ぶことで、ドロップされたファイル一覧を `Array<DroppedFilePath>` 型で取得できます
- `DroppedFilePath` のメンバ変数は次の通りです：

| コード | 説明 |
|--|--|
| `FilePath path` | ファイルまたはディレクトリの絶対パス |
| `Point pos` | ドロップされた位置（シーン座標） |
| `uint64 timeMillisec` | ドロップされたタイムポイント<br>（`Time::GetMillisec()` で計測されるアプリ起動時間） |

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


## 64.2 ファイルドロップの禁止
- 現在のアプリケーションのウィンドウに、ファイルをドロップできないようにするには、`DragDrop::AcceptFilePaths(false)` を呼びます

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルパスのドロップを受け付けないようにする
	DragDrop::AcceptFilePaths(false);

	while (System::Update())
	{		
		// 受け付けないので何もドロップされない
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


## 64.3 ドロップされたテキストの取得
- アプリケーションのウィンドウへのテキスト（文字列）のドロップは、デフォルトでは無効になっています
- `DragDrop::AcceptText(true)` を呼ぶことで、テキストのドロップを有効にできます
- アプリケーションのウィンドウに対してドラッグ & ドロップされたテキストがあるかを `DragDrop::HasNewText()` で取得できます
- この関数が `true` を返したとき、`DragDrop::GetDroppedText()` を呼ぶことで、ドロップされたテキスト一覧を `Array<DroppedText>` 型で取得できます
- `DroppedText` のメンバ変数は次の通りです：

| コード | 説明 |
|--|--|
| `String text` | ドロップされたテキスト |
| `Point pos` | ドロップされた位置（シーン座標） |
| `uint64 timeMillisec` | ドロップされたタイムポイント<br>（`Time::GetMillisec()` で計測されるアプリ起動時間） |

- Web ブラウザ上で選択したテキストをドラッグすることで、テキストのドロップを試すことができます

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


## 64.4 ドロップされたアイテムの情報を消去する
- ドロップされたアイテムの情報は `DragDrop::GetDroppedFilePaths()` および `DragDrop::GetDroppedText()` を呼ぶことで消去されますが、`DragDrop::Clear()` を使って消去することもできます

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{		
		if (DragDrop::HasNewFilePaths())
		{
			// ドロップされたアイテムの情報を消去する
			DragDrop::Clear();

			// 消去されているので、何も取得されない
			for (auto&& [path, pos, timeMillisec] : DragDrop::GetDroppedFilePaths())
			{
				Print << U"{} @{} :{}"_fmt(path, pos, timeMillisec);
			}
		}
	}
}
```


## 64.5 ドラッグ中のアイテムの情報を得る
- ウィンドウ上でドラッグ中のアイテムの情報を取得するには `DragDrop::DragOver()` を使います
- この関数は `Optional<DragStatus>` を、ドラッグ中のアイテムが無い場合は `none` を返します
- `DragStatus` のメンバ変数は次の通りです：

| コード | 説明 |
|--|--|
| `DragItemType itemType` | ドラッグしているアイテムの種類<br>`DragItemType::FilePaths` または `DragItemType::Text` |
| `Point cursorPos` | ドラッグ中のカーソルの位置（シーン座標） |

- 次のサンプルコードは、ファイルをドロップしようとしているときに、画面上にアイコンを表示します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture icon{ 0xf15b_icon, 40 };

	while (System::Update())
	{
		// ウィンドウ上でドラッグ中のアイテムがある
		if (const auto status = DragDrop::DragOver())
		{
			if (status->itemType == DragItemType::FilePaths)
			{
				// アイコンを表示する
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
