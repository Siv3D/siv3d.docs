# Displaying Exception Locations

## Configuration Method
Due to the design of Windows Siv3D running `Main()` in a subthread, by default, the line where an exception occurs is not displayed in the code editor.

To make exception locations visible in the editor, open "Debug" → "Windows" → "Exception Settings" from the Visual Studio menu, and add `s3d::Error` to the "Break When Thrown" list. This will cause the program to break on the next line when that type of exception occurs, making it easier to identify where exceptions happen.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tools/msvc-exception.png)

## Sample Code

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 a = 10;

	if (a != 10)
	{
		throw Error{ U"A" };
	}

	int32 b = 20;

	if (b != 10)
	{
		throw Error{ U"B" }; // Exception is thrown
	}

	Print << a << b; // Program breaks at this line

	while (System::Update())
	{

	}
}
```