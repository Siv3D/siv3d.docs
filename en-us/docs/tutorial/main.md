# 4. Main Function and Main Loop
Learn about the structure of the Main function and the main loop.

## 4.1 Three Parts of the Main Function
- The `Main` function can be divided into three main parts

| Part | Description | What to do |
|--|--|--|
| Before main loop | Section executed first after program startup | Screen settings, texture creation, font initialization, etc. |
| Inside main loop | Section inside the loop that repeats dozens of times per second | Input processing and drawing |
| After main loop | Section executed when the program ends | (If necessary) Saving game data, etc. |

=== "Before main loop"
	```cpp hl_lines="5-6"
	# include <Siv3D.hpp>

	void Main()
	{


		while (System::Update())
		{

		}
	}
	```

=== "Inside main loop"
	```cpp hl_lines="7-9"
	# include <Siv3D.hpp>

	void Main()
	{
		while (System::Update())
		{



		}
	}
	```

=== "After main loop"
	```cpp hl_lines="9-10"
	# include <Siv3D.hpp>

	void Main()
	{
		while (System::Update())
		{

		}


	}
	```

- When you start developing in earnest, most of your work will be written **inside the main loop**
- It's rare to write anything **after the main loop**

## 4.2 Main Loop Execution Frequency
- The **main loop** repeats according to the refresh rate of the monitor 🖥️ running the program. Generally 60 or 120 times per second
- If you write a loop like `for(;;)` in a regular C++ program, it would loop tens of thousands of times per second as long as performance allows, but `while (System::Update())` is different
- The `System::Update()` function internally performs **waiting synchronized with the monitor's display timing**, controlling it to be a loop frequency that matches the monitor's display timing (dozens of times per second)
	- It doesn't always match the monitor's refresh rate exactly. Depending on timing, it may slightly fall below the refresh rate
	- If very heavy processing is performed within the main loop, it may fall significantly below
- Running the following program will display the FPS in the top-left of the screen for confirmation
	- **FPS (Frames Per Second)** is an indicator of how many times the screen is updated per second

```cpp title="Display how many times per second the main loop repeats (FPS) in the top-left of the screen"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << Profiler::FPS();
	}
}
```

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/main/2.png)


## 4.3 What to Do Before the Main Loop
- When you want to display images in your program, the "job of loading images" should be written **before the main loop**
- By loading images only once **before the main loop** to create textures, then drawing them **inside the main loop**, you can achieve your goal with the least amount of work

!!! success "OK"
	- Before the main loop, load images only once to create textures
	- After that, just draw the already-created textures inside the main loop

	```cpp hl_lines="6"
	# include <Siv3D.hpp>

	void Main()
	{
		// Load image data from image file and create texture
		const Texture texture{ U"example/windmill.png" };

		while (System::Update())
		{
			// Draw the texture
			texture.draw();
		}
	}
	```

- If you write texture creation **inside the main loop**, the texture gets destroyed at the end of the loop, so wasteful processing of create-then-destroy is repeated every frame

!!! failure "Repeated create-then-destroy"
	- Inside the main loop, images are loaded and textures are created and destroyed every loop
	- When Siv3D detects such problems, it outputs a warning message and ends the main loop

	```cpp hl_lines="8"
	# include <Siv3D.hpp>

	void Main()
	{
		while (System::Update())
		{
			// Load image data from image file and create texture
			const Texture texture{ U"example/windmill.png" };

			// Draw the texture
			texture.draw();
		} // Texture is destroyed at end of loop
	}
	```

- Don't worry if you accidentally write such code. Siv3D automatically detects "repeated create-then-destroy" and displays a message box to end the program

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/main/3.png)


## Review Checklist
- [x] Learned the three parts of the Main function
- [x] Learned that `System::Update()` controls the execution frequency of the main loop
- [x] Learned to write heavy jobs like "loading images" before the main loop
- [x] Learned that even if you mistakenly repeat "create-then-destroy" in the main loop, Siv3D automatically detects the problem and ends the program