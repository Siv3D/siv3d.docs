# 3. Siv3D Basics
Learn the basic structure of Siv3D programs.

## 3.1 Header to Include
- You can use all Siv3D features by including just the header file `<Siv3D.hpp>`

```cpp
# include <Siv3D.hpp>
```

- Standard library headers used in regular C++ programming are basically unnecessary
- Major standard library header files like `<algorithm>` and `<vector>` are included within `<Siv3D.hpp>`

## 3.2 How Programs Start
- Regular C++ starts with `int main()`, but in Siv3D you write a `void Main()` function instead

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Write your program here
}
```

- This is because Siv3D's internal program has the following structure:
	- ① Before the program starts, Siv3D's internal program performs all necessary initialization
	- ② Execute the `Main()` written by the developer
	- ③ When `Main()` finishes, Siv3D's internal program automatically performs cleanup

```C++ title="Simplified Siv3D Internal Program"
int main()
{
	Siv3D初期化(); // Perform necessary initialization
	
	Main(); // Execute the program written by the developer
	
	Siv3D終了処理(); // Perform cleanup
}
```

- Developers don't need to worry about tedious preparation or cleanup; just write what you want to do in the `Main()` function
- Using a restaurant kitchen as an analogy:
	- Staff prepare cooking utensils in advance (initialization)
	- **The chef can focus only on cooking (the `Main` function)**
	- When finished, staff clean up (cleanup process)

!!! info "Cannot use Siv3D within existing programs"
	- Since Siv3D has a `main()` function internally, you cannot use Siv3D within existing programs written in `main()` functions (such as previously created game programs)
	

## 3.3 Minimal Siv3D Program
- Let's write and build/run the minimal Siv3D program in your editor

```cpp title="Minimal Siv3D Program"
# include <Siv3D.hpp>

void Main()
{

}
```

- This `Main` function has nothing to do and will exit immediately when executed
- Since no window is displayed, it may appear that nothing is happening


## 3.4 Write a Main Loop to Keep the Window Displayed
- To prevent the program from exiting immediately, you need to write a **main loop**
- The highlighted part `while (System::Update()){ }` in the following code is the main loop
- The `while` statement causes the highlighted program to repeat semi-permanently

```cpp title="Program with Main Loop Added" hl_lines="5-8"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// Write your program here
	}
}
```

- With each iteration, `System::Update()` performs window display, graphics processing, mouse and keyboard input reception, etc.
- While the main loop is running, the window continues to be displayed

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/basics/4.png)

- Now you're ready to continuously process graphics display and user input acquisition


## 3.5 How to Exit the Program
- `System::Update()` normally returns `true`, so the main loop continues semi-permanently
- When specific operations are performed, `System::Update()` returns `false`, causing the main loop to exit
- When you exit the main loop and reach the end of the `Main` function, the program terminates

```cpp title="Program Exits When Main Loop is Exited" hl_lines="6"
# include <Siv3D.hpp>

void Main()
{
	// Repeat main loop until System::Update() returns false
	while (System::Update())
	{

	}
}
```

- There are several conditions that cause `System::Update()` to return `false`:
	1. **Press the window close button**
	2. **Press the ++esc++ key**
	3. **Call `System::Exit()` in the program**
- Methods a and b can be confirmed by actually closing the window or pressing the ++esc++ key
- Here's code to try method c:
	- Call `System::Exit()` 5 seconds after the program starts, causing subsequent `System::Update()` calls to return `false`

```cpp title="Exit Program 5 Seconds After Start" hl_lines="11"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// If 5 seconds have passed since program start
		if (5.0 <= Scene::Time())
		{
			// Instruct program to exit
			System::Exit();
		}
	}
}
```

- Calling `System::Exit()` doesn't immediately terminate the program
- It only gives the instruction to "make the next `System::Update()` return `false`"


## 3.6 Return from Main Function
- Apart from the method in **3.5**, you can also exit the program by `return`ing from the `Main` function
- This is especially useful when you want to exit the program immediately without waiting for the subsequent `System::Update()`
- Switch between the tabs below to see the difference between `System::Exit()` and `return`

=== "Exit by Method 3.5"

	- After calling `System::Exit()`, **`Process A` and `Process B` are executed before** the `Main` function ends

	```C++ hl_lines="10 13 16"
	# include <Siv3D.hpp>

	void Main()
	{
		while (System::Update())
		{
			// If 5 seconds have passed since program start
			if (5.0 <= Scene::Time())
			{
				System::Exit(); // Make next System::Update() return false
			}

			ProcessA();
		}

		ProcessB();
	}
	```

=== "Exit by Method 3.6"

	- When you `return;`, **`Process A` and `Process B` are not executed** and the `Main` function ends immediately

	```C++ hl_lines="10"
	# include <Siv3D.hpp>

	void Main()
	{
		while (System::Update())
		{
			// If 5 seconds have passed since program start
			if (5.0 <= Scene::Time())
			{
				return; // Main() ends immediately here
			}

			ProcessA();
		}

		ProcessB();
	}
	```

- Which method to use depends on the program structure and purpose
- In Siv3D's official samples, when explicitly writing exit processing, method **3.5** is used
	- This is convenient because you can write game save processing and audio fade-out in the "Process B" location
- How to customize exit operations (such as preventing exit even when ++esc++ is pressed) will be covered in **Tutorial 5.6**

!!! info "System::Exit() is not required"
	- `System::Exit()` is not a "cleanup" function, so it's not necessarily required
	- Whether you press the ++esc++ key, press the window close button, or exit with `return;`, the program will terminate normally in all cases


## Review Checklist
- [x] Learned that you only need to include `<Siv3D.hpp>`
- [x] Learned to write `void Main()` instead of `int main()`
- [x] Learned how to write a main loop and how to keep the window displayed with the main loop
- [x] Learned that when `System::Update()`'s return value becomes `false`, it exits the main loop and the application terminates
- [x] Specifically learned that `System::Update()` returns `false` when the window is closed or ++esc++ is pressed
- [x] Learned that `System::Exit()` can set `System::Update()`'s return value to `false`
- [x] Learned that `System::Exit()` is not required