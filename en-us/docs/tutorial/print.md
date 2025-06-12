# 6. Simple Output
Learn how to display text and numbers in a simple way within your program. Simple display doesn't allow you to specify fonts, positions, or colors, but you can display strings and numbers on screen with minimal code.

## 6.1 Simple Display of Strings and Numbers
- When you pass strings or numbers to `Print` using the `<<` operator, they are displayed in a simple way at the top-left of the screen
- When handling strings in Siv3D programs, **add `U` before the double quotes**
    - This is the notation for treating strings as Unicode (UTF-32) strings

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/print/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"C++";

	Print << U"Hello, " << U"Siv3D"; // Multiple parts are OK too

	Print << 123;

	Print << 4.567;

	while (System::Update())
	{

	}
}
```


## 6.2 Displaying Many Simple Outputs
- Simple outputs remain on screen
- When the screen can't fit any more, older outputs disappear in order

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/print/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	while (System::Update())
	{
		Print << count;

		++count;
	}
}
```


## 6.3 Clearing Simple Display
- To clear all simple display from the screen, use `ClearPrint()`
- If you always call `ClearPrint()` at the beginning of the main loop, you can display only the content output within the current frame

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/print/3.png)

```cpp hl_lines="9-10"
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	while (System::Update())
	{
		// Clear old outputs (outputs from previous frames)
		ClearPrint();

		Print << count;

		++count;
	}
}
```


## Review Checklist
- [x] Learned to send values to `Print` with `<<` to display strings and numbers in a simple way on screen
- [x] Learned to add `U` before double quotes when handling strings
- [x] Learned that outputs displayed with `Print` remain on screen
- [x] Learned to clear simple display with `ClearPrint()`