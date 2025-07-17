# 57. Embedded Resources
Learn how to embed files such as images and audio into the application executable and load them in your program.

## 57.1 Overview of Embedded Resources
- You can embed images, audio, text, and other files used by your program into the executable file (.exe or .app), making the application appear as a single file to users
- Files embedded in the executable are called **embedded resources**
- Using embedded resources makes application distribution easier
- It also helps prevent troubles where files necessary for program execution are accidentally deleted or modified by users
- Embedded resources are **read-only**. You cannot delete or modify them from the program

## 57.2 Embedding Methods
- The procedure for embedding resource files into the application executable differs by platform

### 57.2.1 Windows
- Describe the paths of files you want to embed in `App/Resource.rc`
- Right-click `App/Resource.rc` in Visual Studio's Solution Explorer, select "View Code" to open it, and describe the path of each file you want to embed in the format `Resource(file_path)`
	- Using double quotes or spaces is prohibited
- The following image shows an example of making `example/windmill.png` an embedded resource (line 130):

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/resource/2a.png)

- `App/Resource.rc` originally contains various files from the `engine/` folder necessary for Siv3D's internal processing
- Add new embedded files following those
- After updating the .rc file, rebuild the project to embed the files into the .exe
	- You can also confirm that the generated executable file size increases due to embedding

!!! warning "Notes on using embedded resources on Windows"
	- In the Windows version of Siv3D, some types of files cannot be loaded properly when embedded (such as video files used with `VideoTexture`)
	- There is a workaround to [write embedded resources to temporary files :material-open-in-new:](https://gist.github.com/Reputeless/3d527302d459792f7a5e1094d30d0529){:target="_blank"}

### 57.2.2 macOS
- Drag folders into Xcode's project navigator and select "Create folder references" to display them as blue folder icons in the project navigator
- All files in such folders are embedded into the .app
- In the initial project state, the `engine/` folder necessary for Siv3D's internal processing is already set as an embedded resource using the same method

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/resource/2b.png)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/resource/2c.png)

### 57.2.3 Linux
- Embedded resources are not supported in the Linux version
- Instead, store necessary resource files in the `resources/` folder and include them with the application
- In the initial state, the `engine/` folder necessary for Siv3D's internal processing is stored in the `resources/` folder
- This `resources/` folder must always exist in the same directory as the executable file at runtime


## 57.3 Loading Embedded Resources
- To load embedded resources in a program, surround the embedded resource file path with `Resource()`
- For example, if you embedded `U"example/windmill.png"`, rewrite it as `Resource(U"example/windmill.png")` (common for Windows, macOS, Linux)

```cpp title="Loading embedded resource example/windmill.png"
# include <Siv3D.hpp>

void Main()
{
	// Load from embedded resource
	const Texture texture{ Resource(U"example/windmill.png") };

	while (System::Update())
	{
		texture.draw();
	}
}
```

- To confirm that a file has been properly embedded, copy the built executable file alone to another folder and run it to verify that the embedded resource image is displayed


## 57.4 Getting List of Embedded Resources
- You can get a list of embedded resources as `Array<FilePath>` using `EnumResourceFiles()`
- The files included in this list are the embedded resources that can be loaded with `Resource(...)`
- Running the following code will display a list of resources embedded by the Siv3D engine and resources you embedded yourself in the console
	- On Windows, all file names are capitalized

```cpp
# include <Siv3D.hpp>

void Main()
{
	for (const auto& path : EnumResourceFiles())
	{
		Console << path;
	}

	while (System::Update())
	{

	}
}
```


## 57.5 Limitations of Embedded Resources
- Embedded resource files cannot be modified at runtime, so they cannot be used for purposes like save files
- Embedded resource files can be extracted by users performing special operations. They are not suitable for hiding important files. Apply obfuscation, encryption, and other processing as necessary