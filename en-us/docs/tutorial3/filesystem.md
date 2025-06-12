# 53. File System

## 53.1 Type for Representing Paths
- When representing file or directory paths in Siv3D code, using `FilePath`, which is an alias (alternate name) for the `String` type, makes the intent clearer

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Using FilePath rather than String makes it clearer that it's a file path
	const FilePath path = U"example/windmill.png";

	const Texture texture{ path };

	while (System::Update())
	{
		texture.draw();
	}
}
```

- Directory paths are represented with a trailing `/`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Directory paths have a trailing /
	const FilePath videoDirectory = U"example/video/";

	const VideoTexture videoTexture{ videoDirectory + U"river.mp4" };

	while (System::Update())
	{
		videoTexture.advance();

		videoTexture.scaled(0.5).draw();
	}
}
```


## 53.2 Existence Check
- Use `FileSystem::Exists(path)` to check if a file or directory exists
- Use `FileSystem::IsFile(path)` to check if a file exists
- Use `FileSystem::IsDirectory(path)` to check if a directory exists

| Code | Description |
|---|---|
| `FileSystem::Exists(path)` | Returns whether the file or directory indicated by `path` exists |
| `FileSystem::IsFile(path)` | Returns whether the file indicated by `path` exists |
| `FileSystem::IsDirectory(path)` | Returns whether the directory indicated by `path` exists |

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Check if file or directory exists
	Print << FileSystem::Exists(U"example/windmill.png");
	Print << FileSystem::Exists(U"example/video/");
	Print << FileSystem::Exists(U"aaa/bbb.txt");
	Print << FileSystem::Exists(U"");

	Print << U"----";

	// Check if file exists
	Print << FileSystem::IsFile(U"example/windmill.png");
	Print << FileSystem::IsFile(U"example/video/");
	Print << FileSystem::IsFile(U"aaa/bbb.txt");
	Print << FileSystem::IsFile(U"");

	Print << U"----";

	// Check if directory exists
	Print << FileSystem::IsDirectory(U"example/windmill.png");
	Print << FileSystem::IsDirectory(U"example/video/");
	Print << FileSystem::IsDirectory(U"aaa/bbb.txt");
	Print << FileSystem::IsDirectory(U"");

	while (System::Update())
	{

	}
}
```
```txt title="Output"
true
true
false
false
----
true
false
false
false
----
false
true
false
false
```


## 53.3 Absolute Path
- Use `FileSystem::FullPath(path)` to convert a path to an absolute path

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Get absolute path
	Print << FileSystem::FullPath(U"example/windmill.png");
	Print << FileSystem::FullPath(U"example/video/");

	while (System::Update())
	{

	}
}
```
```txt title="Example output"
C:/Users/siv3d/Desktop/projects/hello/hello/App/example/windmill.png
C:/Users/siv3d/Desktop/projects/hello/hello/App/example/video/
```



## 53.4 Converting to Relative Path
- Use `FileSystem::RelativePath(path)` to convert a path to a relative path from the current directory

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Get absolute path
	const FilePath fullpath1 = FileSystem::FullPath(U"example/windmill.png");
	const FilePath fullpath2 = FileSystem::FullPath(U"example/video/");

	// Get relative path
	Print << FileSystem::RelativePath(fullpath1);
	Print << FileSystem::RelativePath(fullpath2);

	while (System::Update())
	{

	}
}
```
```txt title="Output"
example/windmill.png
example/video/
```


## 53.5 File Name and Extension
- To get just the file name portion from a file path without the parent directory, use `FileSystem::FileName(path)`
- To get the file name without the extension, use `FileSystem::BaseName(path)`
- To get the file extension (without the dot) in lowercase, use `FileSystem::Extension(path)`
- When the path is a directory, all functions treat the directory name as if it were a file name

| Code | Description |
|---|---|
| `FileSystem::FileName(path)` | Returns the file name portion from the file path indicated by `path`, excluding the parent directory |
| `FileSystem::BaseName(path)` | Returns the file name portion from the file path indicated by `path`, excluding the parent directory and extension |
| `FileSystem::Extension(path)` | Returns the extension from the file path indicated by `path` in lowercase |

```cpp
# include <Siv3D.hpp>

void Main()
{
	const FilePath path = U"example/windmill.png";

	// Get file name
	Print << FileSystem::FileName(path);

	// Get file name without extension
	Print << FileSystem::BaseName(path);

	// Get extension in lowercase
	Print << FileSystem::Extension(path);

	while (System::Update())
	{

	}
}
```
```txt title="Output"
windmill.png
windmill
png
```


## 53.6 Parent Directory
- To get the parent directory of a path, use `FileSystem::ParentPath(path)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Get parent directory
	Print << FileSystem::ParentPath(U"example/windmill.png");
	Print << FileSystem::ParentPath(U"example/video/river.mp4");
	Print << FileSystem::ParentPath(U"example/video/");
	Print << FileSystem::ParentPath(U"./");

	while (System::Update())
	{

	}
}
```
```txt title="Example output"
C:/Users/siv3d/Desktop/projects/hello/hello/App/example/
C:/Users/siv3d/Desktop/projects/hello/hello/App/example/video/
C:/Users/siv3d/Desktop/projects/hello/hello/App/example/
C:/Users/siv3d/Desktop/projects/hello/hello/
```



## 53.7 Current Directory
- To get the current directory, use `FileSystem::CurrentDirectory()`
- To change the current directory, use `FileSystem::ChangeCurrentDirectory(path)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Get current directory
	Print << FileSystem::CurrentDirectory();

	while (System::Update())
	{

	}
}
```
```txt title="Example output"
C:/Users/siv3d/Desktop/projects/hello/hello/App/
```


## 53.8 Executable File Path
- To get the path of the current program's executable file, use `FileSystem::ModulePath()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Get executable file path
	Print << FileSystem::ModulePath();

	while (System::Update())
	{

	}
}
```
```txt title="Example output"
C:/Users/siv3d/Desktop/projects/hello/Intermediate/hello/Debug/hello(debug).exe
```


## 53.9 Initial Directory
- The initial directory is the current directory when the program was launched
- It is usually the directory where the executable file is located, but it may be different when launched from the command line or when launched from a file through file association
- To get the initial directory, use `FileSystem::InitialDirectory()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Get initial directory
	Print << FileSystem::InitialDirectory();

	while (System::Update())
	{

	}
}
```
```txt title="Example output"
C:/Users/siv3d/Desktop/projects/hello/hello/App/
```



## 53.10 Special Folder Paths
- To get the path of special folders such as Desktop or Documents, use `FileSystem::GetFolderPath(SpecialFolder)`
- Returns an empty string if it doesn't exist
- The `SpecialFolder` values that indicate special folder types are:

| Code | Description |
|--|--|
|`SpecialFolder::Desktop`| Desktop |
|`SpecialFolder::Documents`| Documents |
|`SpecialFolder::LocalAppData`| Application cache |
|`SpecialFolder::Pictures`| Pictures |
|`SpecialFolder::Music`| Music |
|`SpecialFolder::Videos`| Videos |
|`SpecialFolder::Caches`| Application cache (same as `LocalAppData`) |
|`SpecialFolder::Movies`| Videos (same as `Videos`) |
|`SpecialFolder::SystemFonts`| System fonts |
|`SpecialFolder::LocalFonts`| Local fonts |
|`SpecialFolder::UserFonts`| User fonts |
|`SpecialFolder::UserProfile`| User profile |
|`SpecialFolder::ProgramFiles`| Applications |


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
```txt title="Example output"
Desktop: C:/Users/siv3d/Desktop/
Documents: C:/Users/siv3d/Documents/
LocalAppData: C:/Users/siv3d/AppData/Local/
Pictures: C:/Users/siv3d/Pictures/
Music: C:/Users/siv3d/Music/
Videos: C:/Users/siv3d/Videos/
SystemFonts: C:/Windows/Fonts/
LocalFonts: C:/Windows/Fonts/
UserFonts: C:/Windows/Fonts/
UserProfile: C:/Users/siv3d/
ProgramFiles: C:/Program Files/
```


## 53.11 Temporary Directory
- To get a directory that can be used for saving temporary files, use `FileSystem::TempDirectory()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Get directory for saving temporary files
	Print << FileSystem::TemporaryDirectoryPath();

	while (System::Update())
	{

	}
}
```
```txt title="Example output"
C:/Users/siv3d/AppData/Local/Temp/
```



## 53.12 Temporary Files
- To get a file path that can be used for saving temporary files in a certain directory, use `FileSystem::UniqueFilePath(directory)`
- The file extension is `.tmp`, and it is guaranteed that no file with the same name exists in that directory
- If the argument is omitted, the directory described in **53.11** that can be used for saving temporary files is used

```cpp
# include <Siv3D.hpp>

void Main()
{
    // Get a file path for temporary files within the example folder
	const FilePath tempPath1 = FileSystem::UniqueFilePath(U"example/");
	Print << tempPath1;

    // Get a file path for temporary files
	const FilePath tempPath2 = FileSystem::UniqueFilePath();
	Print << tempPath2;

	while (System::Update())
	{

	}
}
```
```txt title="Example output"
example/8763c77e-8a6c-4b63-9432-b6bf9f6ca95a.tmp
C:/Users/siv3d/AppData/Local/Temp/5533afc3-c401-4731-81b1-62c938871332.tmp
```


## 53.13 Path Concatenation
- User input or external input may have directory paths without a trailing `/`
- To concatenate such paths, use `FileSystem::PathAppend(a, b)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Concatenate paths
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
```txt title="Output"
example/windmill.png
example/windmill.png
----
example/
example/
```


## 53.14 Size
- Use `FileSystem::FileSize(path)` to get the size of a file
- Use `FileSystem::Size(path)` to get the size of a file or entire directory

| Code | Description |
|--|--|
| `FileSystem::FileSize(path)` | Returns the file size in bytes. Returns 0 if the file doesn't exist or is empty |
| `FileSystem::Size(path)` | Returns the size of a file or directory in bytes. Returns 0 if the file or directory doesn't exist or is empty |

- You can use `FormatDataSize(int64)` to convert file size to a readable string format with binary prefixes (e.g., `120KiB`)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Get file size
	Print << FileSystem::FileSize(U"example/windmill.png");
	Print << FormatDataSize(FileSystem::FileSize(U"example/windmill.png"));

	// Get directory size
	Print << FileSystem::Size(U"example/");
	Print << FormatDataSize(FileSystem::Size(U"example/"));

	while (System::Update())
	{

	}
}
```
```txt title="Example output"
253286
247KiB
41456337
39.5MiB
```


## 53.15 Timestamps
- Use `FileSystem::CreationTime(path)` to get the creation time of a file
- Use `FileSystem::WriteTime(path)` to get the last write time of a file
- Use `FileSystem::AccessTime(path)` to get the last access time of a file
- The return value is `Optional<DateTime>`, and returns `none` if the file doesn't exist or if retrieval fails

| Code | Description |
|--|--|
| `FileSystem::CreationTime(path)` | Returns the file creation time. Returns `none` if the file doesn't exist |
| `FileSystem::WriteTime(path)` | Returns the file's last write time. Returns `none` if the file doesn't exist |
| `FileSystem::AccessTime(path)` | Returns the file's last access time. Returns `none` if the file doesn't exist |

```cpp
# include <Siv3D.hpp>

void Main()
{
	const FilePath path = U"example/windmill.png";

	// Get file creation time
	if (const auto time = FileSystem::CreationTime(path))
	{
		Print << U"CreationTime: " << *time;
	}

	// Get file's last write time
	if (const auto time = FileSystem::WriteTime(path))
	{
		Print << U"LastWriteTime: " << *time;
	}

	// Get file's last access time
	if (const auto time = FileSystem::AccessTime(path))
	{
		Print << U"LastAccessTime: " << *time;
	}

	while (System::Update())
	{

	}
}
```
```txt title="Example output"
CreationTime: 2025-01-18 11:18:34
LastWriteTime: 2022-01-18 18:27:24
LastAccessTime: 2025-01-18 11:18:34
```


## 53.16 Directory Contents List
- Use `FileSystem::DirectoryContents(path, recursive)` to get a list of contents (files and directories) in a directory
- The return value is `Array<FilePath>`

### 53.16.1 Directory Contents List (with recursive search)
- By default, directories within directories are searched recursively, so contents within subdirectories are also retrieved

```cpp
# include <Siv3D.hpp>

void Main()
{
    // Get directory contents list
	const Array<FilePath> paths = FileSystem::DirectoryContents(U"example/");

	// Output to Console instead of Print due to large content
	for (const auto& path : paths)
	{
		Console << path;
	}

	while (System::Update())
	{

	}
}
```

### 53.16.2 Directory Contents List (without recursive search)
- If you specify `Recursive::No` as the second argument, directories within directories are not searched recursively
- Files and directories deeper than the initially specified directory are not retrieved

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Get directory contents list (without recursive search)
	const Array<FilePath> paths = FileSystem::DirectoryContents(U"example/", Recursive::No);

	// Output to Console instead of Print due to large content
	for (const auto& path : paths)
	{
		Console << path;
	}

	while (System::Update())
	{

	}
}
```


### 53.16.3 List of Files with Specific Extensions
- After getting the directory contents list, you can use `Array::filter()` to extract only paths that meet certain conditions
- The following sample code gets a list of `.png` files contained in the `example` directory

```cpp
# include <Siv3D.hpp>

// Function to determine if a file path has png extension
bool IsPngFile(const FilePath& path)
{
	return (FileSystem::Extension(path) == U"png");
}

void Main()
{
	// Get directory contents list
	const Array<FilePath> paths = FileSystem::DirectoryContents(U"example/");

	// Extract only png files
	const Array<FilePath> pngFiles = paths.filter(IsPngFile);

	// Output to Console instead of Print due to large content
	for (const auto& pngFile : pngFiles)
	{
		Console << pngFile;
	}

	while (System::Update())
	{

	}
}
```


## 53.17 Empty Directory Check
- To check if a directory is empty, use `FileSystem::IsEmptyDirectory(path)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Check if directory is empty
	Print << FileSystem::IsEmptyDirectory(U"example/");

	while (System::Update())
	{

	}
}
```
```txt title="Output"
false
```


## 53.18 Creating Directories

### 53.18.1 Creating a Directory by Specifying Directory Name
- To create a new directory, use `FileSystem::CreateDirectories(path)`
- This function creates the directory at the specified path if it doesn't exist
- Returns `true` if creation succeeds or if a directory with the same name already exists, otherwise returns `false`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Create directory test1/
	Print << FileSystem::CreateDirectories(U"test1/");

	// Create directory test2/aaa/bbb/
	Print << FileSystem::CreateDirectories(U"test2/aaa/bbb/");

	while (System::Update())
	{

	}
}
```
```txt title="Output"
true
true
```


### 53.18.2 Creating Directories up to the Parent Directory by Specifying a Path
- To create directories up to the parent directory for a given path, use `FileSystem::CreateParentDirectories(path)`
- This function creates the parent directory of the specified path if it doesn't exist
- Returns `true` if creation succeeds or if a directory with the same name already exists, otherwise returns `false`
- `FileSystem::CreateParentDirectories(U"aaa/bbb/ccc.txt")` is the same as `FileSystem::CreateDirectories(U"aaa/bbb/")`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Create parent directory of test3/a.txt
	Print << FileSystem::CreateParentDirectories(U"test3/a.txt");

	// Create parent directory of test4/aaa/bbb/ccc/
	Print << FileSystem::CreateParentDirectories(U"test4/aaa/bbb/ccc/");

	while (System::Update())
	{

	}
}
```
```txt title="Output"
true
true
```


## 53.19 Copy
- To copy a file or directory, use `FileSystem::Copy(src, dst)`
- Specify the source file or directory path in `src` and the destination path in `dst`
- Returns `true` if copy succeeds, `false` if it fails

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Copy file
	Print << FileSystem::Copy(U"example/windmill.png", U"image.png");

	// Copy directory
	Print << FileSystem::Copy(U"example/video/", U"test5/");

	while (System::Update())
	{

	}
}
```
```txt title="Output"
true
true
```


## 53.20 Delete
- To delete a file or directory, use `FileSystem::Remove(path)`
- Specify the path of the file or directory to delete in `path`
- Returns `true` if deletion succeeds, `false` if it fails
- If you specify `AllowUndo::Yes` as the second argument, the file is sent to the trash if possible and can be manually restored later

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Copy and delete file
	FileSystem::Copy(U"example/windmill.png", U"image.png");
	Print << FileSystem::Remove(U"image.png");

	// Copy and delete directory
	FileSystem::Copy(U"example/video/", U"test5/");
	Print << FileSystem::Remove(U"test5/");

	while (System::Update())
	{

	}
}
```
```txt title="Output"
true
true
```


## 53.21 Deleting Directory Contents
- To delete only the contents of a directory and leave an empty directory, use `FileSystem::RemoveContents(path)`
- If you specify `AllowUndo::Yes` as the second argument, the files are sent to the trash if possible and can be manually restored later

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Copy directory
	FileSystem::Copy(U"example/video/", U"test5/");

	// Delete only directory contents
	Print << FileSystem::RemoveContents(U"test5/");

	while (System::Update())
	{

	}
}
```
```txt title="Output"
true
```


## 53.22 Rename
- To rename a file or directory, use `FileSystem::Rename(src, dst)`
- Specify the source file or directory path in `src` and the destination path in `dst`
- Returns `true` if rename succeeds, `false` if it fails

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Copy and rename file
	FileSystem::Copy(U"example/windmill.png", U"image.png");
	Print << FileSystem::Rename(U"image.png", U"image2.png");

	// Copy and rename directory
	FileSystem::Copy(U"example/video/", U"test5/");
	Print << FileSystem::Rename(U"test5/", U"test5-2/");

	while (System::Update())
	{

	}
}
```
```txt title="Output"
true
true
```


## 53.23 File Change Detection
- `DirectoryWatcher` allows you to detect file change events within a specified directory
	- This can be used to implement features like automatically reloading when users modify files
- `DirectoryWatcher watcher{ directory };` creates a `DirectoryWatcher` object that detects file changes within directory `directory`
- The member function `.retrieveChanges()` of `DirectoryWatcher` can retrieve a list of events that occurred as `Array<FileChange>` in chronological order
	- Retrieved change history is deleted
- `FileChange` has the member variables `.path` for the path of the changed file and `.action` for the type of file operation
- File operations include the following types:

| File Operation | Description |
|--|--|
|`FileAction::Added`| File was added |
|`FileAction::Removed`| File was deleted |
|`FileAction::Modified`| File contents were changed |
|`FileAction::Unknown`| Unknown action |

### 53.23.1 Event Detection in a Directory
- The following sample code creates a `test6/` folder and detects file changes within it, outputting the contents
- To stop detection, assign an empty `DirectoryWatcher` object

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Create test directory
	FileSystem::CreateDirectories(U"test6/");

	// Create object to monitor events in test7/ directory
	DirectoryWatcher watcher{ U"test6/" };

	while (System::Update())
	{
		// Get absolute path and action content
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


### 53.23.2 Update Detection for Specific Files
- Generally, when file contents are updated, one of the following events occurs:
	- Removed → Added
	- Modified
- To reliably detect updates to a specific file, monitor `Added` and `Modified`
- Depending on the application used for file editing, multiple `Modified` events may occur for a single save operation
- The following sample tracks updates to the `test.txt` file in the `test6/` directory

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Create test directory
	FileSystem::CreateDirectories(U"test6/");
	
	// Path of the file to detect updates
	const FilePath filePath = U"test6/test.txt";

	// Absolute path of the file to detect updates
	const FilePath fullPath = FileSystem::FullPath(filePath);

	// Parent directory
	const FilePath parentDirectory = FileSystem::ParentPath(filePath);

	// Create DirectoryWatcher to monitor parent directory
	DirectoryWatcher watcher{ parentDirectory };

	while (System::Update())
	{
		for (auto&& [path, action] : watcher.retrieveChanges())
		{
			// Check if it matches the path of the file to detect updates
			if (path == fullPath)
			{
				// Added or updated
				if ((action == FileAction::Added) || (action == FileAction::Modified))
				{
					Print << U"updated!";
				}
			}
		}
	}
}
```