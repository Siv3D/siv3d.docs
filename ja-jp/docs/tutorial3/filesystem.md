# 42. ファイルシステム
ファイルやディレクトリの情報取得および操作に関する機能を学びます。

## 42.1 パスを表す型
（ファイルやディレクトリの）パスを Siv3D のプログラムで表現するときは、`String` 型のエイリアス（別名）である `FilePath` を使うと意図が明確になります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// String でも良いが、FilePath のほうがファイルパスであることが明確になる
	const FilePath path = U"example/windmill.png";

	const Texture texture{ path };

	while (System::Update())
	{
		texture.draw();
	}
}
```

パスがディレクトリを指す場合は、末尾に `/` を付与した形で表現します（例: `U"example/"`）。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const FilePath videoDirectory = U"example/video/";

	const VideoTexture videoTexture{ videoDirectory + U"river.mp4" };

	while (System::Update())
	{
		videoTexture.advance();

		videoTexture.scaled(0.5).draw();
	}
}
```


## 42.2 ファイルやディレクトリが存在するかを調べる
ファイルやディレクトリが存在するか調べるには `FileSystem::Exists(path)` を使います。ファイルが存在するかを調べるには `FileSystem::IsFile(path)`, ディレクトリ（フォルダ）が存在するかを調べるには `FileSystem::IsDirectory(path)` を使います。

| 関数 | 説明 |
|---|---|
| `FileSystem::Exists(path)` | `path` で示したファイルやディレクトリが存在するかを返します。 |
| `FileSystem::IsFile(path)` | `path` で示したファイルが存在するかを返します。 |
| `FileSystem::IsDirectory(path)` | `path` で示したディレクトリが存在するかを返します。 |

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルやディレクトリが存在するか
	Print << FileSystem::Exists(U"example/windmill.png");
	Print << FileSystem::Exists(U"example/video/");
	Print << FileSystem::Exists(U"aaa/bbb.txt");
	Print << FileSystem::Exists(U"");

	Print << U"----";

	// ファイルが存在するか
	Print << FileSystem::IsFile(U"example/windmill.png");
	Print << FileSystem::IsFile(U"example/video/");
	Print << FileSystem::IsFile(U"aaa/bbb.txt");
	Print << FileSystem::IsFile(U"");

	Print << U"----";

	// ディレクトリが存在するか
	Print << FileSystem::IsDirectory(U"example/windmill.png");
	Print << FileSystem::IsDirectory(U"example/video/");
	Print << FileSystem::IsDirectory(U"aaa/bbb.txt");
	Print << FileSystem::IsDirectory(U"");

	while (System::Update())
	{

	}
}
```


## 42.3 絶対パスを取得する
ファイルパスを絶対パスに変換するには `FileSystem::Fullpath(path)` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 絶対パスを取得する
	Print << FileSystem::FullPath(U"example/windmill.png");
	Print << FileSystem::FullPath(U"example/video/");

	while (System::Update())
	{

	}
}
```


## 42.4 相対パスに変換する
ファイルパスを、現在のカレントディレクトリからの相対パスに変換するには `FileSystem::RelativePath(path)` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 絶対パスを取得する
	const FilePath fullpath1 = FileSystem::FullPath(U"example/windmill.png");
	const FilePath fullpath2 = FileSystem::FullPath(U"example/video/");

	// 相対パスを取得する
	Print << FileSystem::RelativePath(fullpath1);
	Print << FileSystem::RelativePath(fullpath2);

	while (System::Update())
	{

	}
}
```


## 42.5 ファイルの名前部分や拡張子を取得する
ファイルパスから、親ディレクトリ部分を含まずに、ファイル名部分だけを取得するには `FileSystem::FileName(path)` を使います。拡張子を除いたファイル名を取得するには `FileSystem::BaseName(path)` を使います。ファイルの拡張子 (.を含まない) を小文字で取得するには `FileSystem::Extension(path)` を使います。パスがディレクトリである場合、いずれの関数でもディレクトリ名がファイル名であると見なされます。

| 関数 | 説明 |
|---|---|
| `FileSystem::FileName(path)` | `path` で示したファイルパスから、親ディレクトリ部分を除いたファイル名部分を返します。 |
| `FileSystem::BaseName(path)` | `path` で示したファイルパスから、親ディレクトリ部分と拡張子を除いたファイル名部分を返します。 |
| `FileSystem::Extension(path)` | `path` で示したファイルパスから、拡張子を小文字で返します。 |

```cpp
# include <Siv3D.hpp>

void Main()
{
	const FilePath path1 = U"example/windmill.png";
	const FilePath path2 = U"example/video/";

	// ファイル名を取得する
	Print << FileSystem::FileName(path1);
	Print << FileSystem::FileName(path2);

	Print << U"----";

	// 拡張子を除いたファイル名を取得する
	Print << FileSystem::BaseName(path1);
	Print << FileSystem::BaseName(path2);

	Print << U"----";

	// 拡張子を小文字で取得する
	Print << FileSystem::Extension(path1);
	Print << FileSystem::Extension(path2);

	Print << U"----";

	while (System::Update())
	{

	}
}
```


## 42.6 親ディレクトリを取得する
あるパスの親ディレクトリを取得するには、`FileSystem::ParentPath(path)` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 親ディレクトリを取得する
	Print << FileSystem::ParentPath(U"example/windmill.png");
	Print << FileSystem::ParentPath(U"example/video/river.mp4");
	Print << FileSystem::ParentPath(U"example/video/");
	Print << FileSystem::ParentPath(U"./");

	while (System::Update())
	{

	}
}
```


## 42.7 カレントディレクトリを取得する
現在のカレントディレクトリを取得するには、`FileSystem::CurrentDirectory()` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// カレントディレクトリを取得する
	Print << FileSystem::CurrentDirectory();

	while (System::Update())
	{

	}
}
```


## 42.8 実行ファイルのパスを取得する
現在のプログラムの実行ファイルのパスを取得するには、`FileSystem::ModulePath()` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 実行ファイルのパスを取得する
	Print << FileSystem::ModulePath();

	while (System::Update())
	{

	}
}
```


## 42.9 起動ディレクトリを取得する
起動ディレクトリは、プログラムが起動されたときのカレントディレクトリです。通常は、実行ファイルがあるディレクトリですが、コマンドラインから起動された場合や、拡張子の関連付けでファイルから起動された場合などに、異なるディレクトリになることがあります。起動ディレクトリを取得するには、`FileSystem::InitialDirectory()` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 起動ディレクトリを取得する
	Print << FileSystem::InitialDirectory();

	while (System::Update())
	{

	}
}
```


## 42.10 特殊フォルダのパスを取得する
デスクトップやドキュメントなど、特殊な用途のフォルダのパスを取得するには、`FileSystem::GetFolderPath(SpecialFolder)` を使います。存在しない場合は空の文字列を返します。

特殊フォルダの種類を指す `SpecialFolder` は次の値があります。

| 値 | 説明 |
|--|--|
|`SpecialFolder::Desktop`| デスクトップ |
|`SpecialFolder::Documents`| ドキュメント |
|`SpecialFolder::LocalAppData`| アプリケーション・キャッシュ |
|`SpecialFolder::Pictures`| ピクチャー |
|`SpecialFolder::Music`| ミュージック |
|`SpecialFolder::Videos`| ビデオ |
|`SpecialFolder::Caches`| アプリケーション・キャッシュ (`LocalAppData` と同じ) |
|`SpecialFolder::Movies`| ビデオ (`Videos` と同じ) |
|`SpecialFolder::SystemFonts`| システムフォント |
|`SpecialFolder::LocalFonts`| ローカルフォント |
|`SpecialFolder::UserFonts`| ユーザフォント |
|`SpecialFolder::UserProfile`| ユーザープロファイル |
|`SpecialFolder::ProgramFiles`| アプリケーション |


```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"Desktop: " << FileSystem::GetFolderPath(SpecialFolder::Desktop);
	Print << U"Documents: " << FileSystem::GetFolderPath(SpecialFolder::Documents);
	Print << U"LocalAppData: " << FileSystem::GetFolderPath(SpecialFolder::LocalAppData);
	Print << U"Pictures: " << FileSystem::GetFolderPath(SpecialFolder::Pictures);
	Print << U"Music: " << FileSystem::GetFolderPath(SpecialFolder::Music);
	Print << U"Videos: " << FileSystem::GetFolderPath(SpecialFolder::Videos);
	Print << U"SystemFonts: " << FileSystem::GetFolderPath(SpecialFolder::SystemFonts);
	Print << U"LocalFonts: " << FileSystem::GetFolderPath(SpecialFolder::LocalFonts);
	Print << U"UserFonts: " << FileSystem::GetFolderPath(SpecialFolder::UserFonts);
	Print << U"UserProfile: " << FileSystem::GetFolderPath(SpecialFolder::UserProfile);
	Print << U"ProgramFiles: " << FileSystem::GetFolderPath(SpecialFolder::ProgramFiles);

	while (System::Update())
	{

	}
}
```


## 42.11 一時ファイルの保存に使えるディレクトリを取得する
一時ファイルの保存用に使えるディレクトリを取得するには、`FileSystem::TempDirectory()` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 一時ファイルの保存に使えるディレクトリを取得する
	Print << FileSystem::TemporaryDirectoryPath();

	while (System::Update())
	{

	}
}
```


## 42.12 一時ファイル用に使えるファイルパスを取得する
あるディレクトリで、一時ファイル用に使えるファイルパスを取得するには、`FileSystem::UniqueFilePath(directory)` を使います。拡張子は `.tmp` で、これで返されたファイルパスと同名のファイルが存在しないことが保証されています。

引数を省略した場合は、42.11 で説明した、一時ファイルの保存用に使えるディレクトリが使われます。

```cpp
# include <Siv3D.hpp>

void Main()
{
    // example フォルダ内で、一時ファイル用に使えるファイルパスを取得する
	const FilePath tempPath1 = FileSystem::UniqueFilePath(U"example/");
	Print << tempPath1;

    // 一時ファイル用に使えるファイルパスを取得する
	const FilePath tempPath2 = FileSystem::UniqueFilePath();
	Print << tempPath2;

	while (System::Update())
	{

	}
}
```


## 42.13 パスを結合する
ユーザによる入力や、外部からの入力では、ディレクトリパスの末尾に `/` が付いていない場合があります。このようなパスを結合するには、`FileSystem::PathAppend(a, b)` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// パスを結合する
	Print << FileSystem::PathAppend(U"example/", U"windmill.png");
	Print << FileSystem::PathAppend(U"example", U"windmill.png");

	Print << U"----";

	Print << FileSystem::PathAppend(U"example/", U"");
	Print << FileSystem::PathAppend(U"example", U"");

	while (System::Update())
	{

	}
}
```


## 42.14 ファイルやフォルダのサイズを取得する
ファイルのサイズを取得するには `FileSystem::FileSize(path)` を使います。ファイルおよびフォルダのサイズを取得するには `FileSystem::Size(path)` を使います。

| 関数 | 説明 |
|--|--|
| `FileSystem::FileSize(path)` | ファイルのサイズをバイト単位で返します。ファイルが存在しないか、空である場合は 0 を返します。 |
| `FileSystem::Size(path)` | ファイルまたはフォルダのサイズをバイト単位で返します。ファイルまたはフォルダが存在しないか、空である場合は 0 を返します。 |

`FormatDataSize(int64)` を使うと、ファイルサイズを、2 進接頭辞を用いた見やすい形式の文字列に変換できます（例: `120KiB`）。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルのサイズを取得する
	Print << FileSystem::FileSize(U"example/windmill.png");
	Print << FormatDataSize(FileSystem::FileSize(U"example/windmill.png"));

	// ディレクトリのサイズを取得する
	Print << FileSystem::Size(U"example/");
	Print << FormatDataSize(FileSystem::Size(U"example/"));

	while (System::Update())
	{

	}
}
```

## 42.15 ファイルのタイムスタンプを取得する
ファイルの作成日時を取得するには `FileSystem::CreationTime(path)` を使います。ファイルの最終更新日時を取得するには `FileSystem::WriteTime(path)` を使います。ファイルの最終アクセス日時を取得するには `FileSystem::AccessTime(path)` を使います。戻り値は `Optional<DateTime>` で、ファイルが存在しない場合や取得に失敗した場合は `none` が返されます。

| 関数 | 説明 |
|--|--|
| `FileSystem::CreationTime(path)` | ファイルの作成日時を返します。ファイルが存在しない場合は、`none` を返します。 |
| `FileSystem::WriteTime(path)` | ファイルの最終更新日時を返します。ファイルが存在しない場合は、`none` を返します。 |
| `FileSystem::AccessTime(path)` | ファイルの最終アクセス日時を返します。ファイルが存在しない場合は、`none` を返します。 |

```cpp
# include <Siv3D.hpp>

void Main()
{
	const FilePath path = U"example/windmill.png";

	// ファイルの作成日時を取得する
	if (const auto time = FileSystem::CreationTime(path))
	{
		Print << U"CreationTime: " << *time;
	}

	// ファイルの最終更新日時を取得する
	if (const auto time = FileSystem::WriteTime(path))
	{
		Print << U"LastWriteTime: " << *time;
	}

	// ファイルのアクセス最終日時を取得する
	if (const auto time = FileSystem::AccessTime(path))
	{
		Print << U"LastAccessTime: " << *time;
	}

	while (System::Update())
	{

	}
}
```


## 42.16 ディレクトリの中身一覧を取得する
ディレクトリの中身（ファイルやディレクトリ）一覧を取得するには `FileSystem::DirectoryContents(path, recursive)` を使います。戻り値は `Array<FilePath>` です。

### 42.16.1 ディレクトリの中身一覧を取得する（再帰的に検索する）
```cpp
# include <Siv3D.hpp>

void Main()
{
    // ディレクトリの中身一覧を取得する
	const Array<FilePath> paths = FileSystem::DirectoryContents(U"example/");

	// 内容が多いため、Print ではなく Console で出力する
	for (const auto& path : paths)
	{
		Console << path;
	}

	while (System::Update())
	{

	}
}
```

### 42.16.2 ディレクトリの中身一覧を取得する（再帰的に検索しない）
第 2 引数は、ディレクトリ内のディレクトリを再帰的に検索するかどうかを指定します。デフォルトは `Recursive::Yes` ですが、再帰的に検索しない場合は `Recursive::No` を指定します。再帰的に検索しない場合、ディレクトリの中にあるディレクトリの中身は取得されません。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ディレクトリの中身一覧を取得する（再帰的には取得しない）
	const Array<FilePath> paths = FileSystem::DirectoryContents(U"example/", Recursive::No);

	// 内容が多いため、Print ではなく Console で出力する
	for (const auto& path : paths)
	{
		Console << path;
	}

	while (System::Update())
	{

	}
}
```


### 42.16.3 ディレクトリに含まれる .png ファイルの一覧を取得する
ディレクトリの中身一覧を取得した後、`Array::filter()` を使って、条件に合うパスのみを抽出することができます。

下記のサンプルコードでは、`example` ディレクトリに含まれる `.png` ファイルの一覧を取得しています。

```cpp
# include <Siv3D.hpp>

// ファイルパスの拡張子が png かどうかを判定する関数
bool IsPngFile(const FilePath& path)
{
	return (FileSystem::Extension(path) == U"png");
}

void Main()
{
	// ディレクトリの中身一覧を取得する（再帰的には取得しない）
	const Array<FilePath> paths = FileSystem::DirectoryContents(U"example/");

	// png ファイルのみを抽出する
	const Array<FilePath> pngFiles = paths.filter(IsPngFile);

	// 内容が多いため、Print ではなく Console で出力する
	for (const auto& pngFile : pngFiles)
	{
		Console << pngFile;
	}

	while (System::Update())
	{

	}
}
```


## 42.17 空のディレクトリであるかを調べる
あるディレクトリが空であるかどうかを調べるには、`FileSystem::IsEmptyDirectory(path)` を使います。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ディレクトリが空であるかを調べる
	Print << FileSystem::IsEmptyDirectory(U"example/");

	while (System::Update())
	{

	}
}
```


## 42.18 ディレクトリを作成する

### 42.18.1 ディレクトリ名を指定してディレクトリを作成する
ディレクトリを作成するには、`FileSystem::CreateDirectories(path)` を使います。この関数は、指定したパスのディレクトリが存在しない場合、そのディレクトリを作成します。作成に成功したか、すでに同名のディレクトリが存在する場合 `true`, それ以外の場合は `false` を返します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ディレクトリ test1/ を作成する
	Print << FileSystem::CreateDirectories(U"test1/");

	// ディレクトリ test2/aaa/bbb/ を作成する
	Print << FileSystem::CreateDirectories(U"test2/aaa/bbb/");

	while (System::Update())
	{

	}
}
```

### 42.18.2 ファイルパスを指定して、その親ディレクトリまでのディレクトリを作成する
パスを指定して、その親ディレクトリまでのディレクトリを作成するには、`FileSystem::CreateParentDirectories(path)` を使います。この関数は、指定したパスの親ディレクトリが存在しない場合、そのディレクトリを作成します。作成に成功したか、すでに同名のディレクトリが存在する場合 `true`, それ以外の場合は `false` を返します。パスのうち親ディレクトリ以降の部分（ファイルまたはディレクトリ名）は無視されます。

`FileSystem::CreateParentDirectories(U"aaa/bbb/ccc.txt")` は、`FileSystem::CreateDirectories(U"aaa/bbb/")` と同じです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// test3/a.txt の親ディレクトリを作成する
	Print << FileSystem::CreateParentDirectories(U"test3/a.txt");

	// test4/aaa/bbb/ccc/ の親ディレクトリを作成する
	Print << FileSystem::CreateParentDirectories(U"test4/aaa/bbb/ccc/");

	while (System::Update())
	{

	}
}
```


## 42.19 ファイルまたはディレクトリをコピーする
ファイルまたはディレクトリをコピーする場合は `FileSystem::Copy(src, dst)` を使います。`src` にはコピー元のファイルまたはディレクトリのパスを、`dst` にはコピー先のパスを指定します。コピーに成功した場合は `true`、失敗した場合は `false` を返します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルをコピーする
	Print << FileSystem::Copy(U"example/windmill.png", U"image.png");

	// ディレクトリをコピーする
	Print << FileSystem::Copy(U"example/video/", U"test5/");

	while (System::Update())
	{

	}
}
```


## 42.20 ファイルまたはディレクトリを削除する
ファイルやディレクトリを削除する場合は `FileSystem::Remove(path)` を使います。`path` には削除するファイルまたはディレクトリのパスを指定します。削除に成功した場合は `true`、失敗した場合は `false` を返します。

第 2 引数で `AllowUndo::Yes` を指定すると、可能な場合、ファイルはゴミ箱に送られ、あとで手動で復元できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルやディレクトリをコピーする
	FileSystem::Copy(U"example/windmill.png", U"image.png");
	FileSystem::Copy(U"example/video/", U"test5/");

	// ファイルを削除する
	Print << FileSystem::Remove(U"image.png");

	// ディレクトリを削除する
	Print << FileSystem::Remove(U"test5/");

	while (System::Update())
	{

	}
}
```


## 42.21 ディレクトリの中身だけを削除する
ディレクトリの中身だけを削除し、空のディレクトリを残す場合は `FileSystem::RemoveContents(path)` を使います。

第 2 引数で `AllowUndo::Yes` を指定すると、可能な場合、ファイルはゴミ箱に送られ、あとで手動で復元できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ディレクトリをコピーする
	FileSystem::Copy(U"example/video/", U"test5/");

	// ディレクトリの中身だけを削除する
	Print << FileSystem::RemoveContents(U"test5/");

	while (System::Update())
	{

	}
}
```


## 42.22 ファイルやディレクトリをリネームする
ファイルやディレクトリをリネームする場合は `FileSystem::Rename(src, dst)` を使います。`src` にはリネーム元のファイルまたはディレクトリのパスを、`dst` にはリネーム先のパスを指定します。リネームに成功した場合は `true`、失敗した場合は `false` を返します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルやディレクトリをコピーする
	FileSystem::Copy(U"example/windmill.png", U"image.png");
	FileSystem::Copy(U"example/video/", U"test5/");

	// ファイルをリネームする
	Print << FileSystem::Rename(U"image.png", U"image2.png");

	// ディレクトリをリネームする
	Print << FileSystem::Rename(U"test5/", U"test5-2/");

	while (System::Update())
	{

	}
}
```


## 42.23 ファイルの変更を検知する
`DirectoryWatcher` を使うと、指定したディレクトリ内でのファイルの変更イベントを検知できます。ユーザがファイルを変更したときに自動でリロードする仕組みの実装などに使えます。

`DirectoryWatcher watcher{ directory };` で、ディレクトリ `directory` 内でのファイルの変更を検知する `DirectoryWatcher` オブジェクトを作成します。

`DirectoryWatcher` のメンバ関数 `.retrieveChanges()` で、変更履歴の一覧を古い順に取得できます。一度取得した変更履歴は削除されます。

変更履歴は `Array<FileChange>` として取得できます。`FileChange` は、変更されたファイルのパス `.path` と、ファイルの操作を表す `.action` をメンバ変数として持ちます。

ファイルの操作には次の種類があります。

| ファイルの操作 | 説明 |
|--|--|
|`FileAction::Added`| ファイルが追加された |
|`FileAction::Removed`| ファイルが削除された |
|`FileAction::Modified`| ファイルの中身が変更された |
|`FileAction::Unknown`| 不明なアクション |

### 42.23.1 ディレクトリ内でのイベントを検知する
検知が不要になった場合は、空の `DirectoryWatcher` オブジェクトを代入します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// テスト用のディレクトリを作成する
	FileSystem::CreateDirectories(U"test7/");

	// test7/ ディレクトリ内でのイベントを監視するオブジェクトを作成する
	DirectoryWatcher watcher{ U"test7/" };

	while (System::Update())
	{
		// 絶対パスと、アクションの内容を取得する
		for (auto&& [path, action] : watcher.retrieveChanges())
		{
			if (action == FileAction::Added)
			{
				Print << U"Added:";
			}
			else if (action == FileAction::Removed)
			{
				Print << U"Removed:";
			}
			else if (action == FileAction::Modified)
			{
				Print << U"Modified:";
			}
			if (action == FileAction::Unknown)
			{
				Print << U"Unknown:";
			}

			Print << path;
		}

		if (SimpleGUI::Button(U"stop", Vec2{ 680, 40 }, 80, watcher.isOpen()))
		{
			watcher = DirectoryWatcher{};
		}
	}
}
```


### 42.23.2 特定のファイルの更新を検知する
一般に、ファイルの中身が更新されたときは、次のいずれかのイベントが発生します。

- Removed → Added
- Modified

したがって、特定のファイルの更新を取りこぼしなく検出するには、`Added` と `Modified` を監視します。ファイルの編集に使うアプリケーションの仕様によっては、1 回の保存で複数回の `Modified` イベントが発生することがあります。

次のサンプルでは `test8/` ディレクトリ内の `test.txt` ファイルの更新を検出します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// テスト用のディレクトリを作成する
	FileSystem::CreateDirectories(U"test8/");
	
	// 更新を検出したいファイルのパス
	const FilePath filePath = U"test8/test.txt";

	// 更新を検出したいファイルの絶対パス
	const FilePath fullPath = FileSystem::FullPath(filePath);

	// 親ディレクトリ
	const FilePath parentDirectory = FileSystem::ParentPath(filePath);

	// 親ディレクトリを監視する DirectoryWatcher を作成する
	DirectoryWatcher watcher{ parentDirectory };

	while (System::Update())
	{
		for (auto&& [path, action] : watcher.retrieveChanges())
		{
			// 更新を検出したいファイルのパスと一致するか
			if (path == fullPath)
			{
				// 追加または更新された
				if ((action == FileAction::Added) || (action == FileAction::Modified))
				{
					Print << U"updated!";
				}
			}
		}
	}
}
```
