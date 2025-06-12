# 76. Asynchronous Processing
Learn how to implement asynchronous processing in Siv3D.

## 76.1 Overview of Asynchronous Processing
- Since Siv3D's `Main()` function runs on a single thread, if you call time-consuming functions within the main loop, processing will stop at that point until the function returns, causing screen updates to halt or frame rates to drop
- Using Siv3D's asynchronous APIs allows you to execute time-consuming operations asynchronously (basically on a separate thread), enabling the `Main()` function to continue with other processing and frame updates while waiting for completion

### Important Notes
- You cannot use Siv3D's rendering-related APIs (`.draw()`, render states, shaders, texture creation/manipulation, etc.) in threads other than `Main()`. The only exception is `Print`, which can be used
- If you want to create assets (`Texture`, `Audio`, `Font`, `PixelShader`, etc.) in threads other than `Main()`, use the asynchronous functionality provided by asset management (**Tutorial 50**). Other methods may not work properly
- For functionality that provides explicit asynchronous functions like HTTP client (**Tutorial 62**) or OpenAI API (**Tutorial 67**), it's recommended to use those
- Many standalone features that don't closely interact with Siv3D's core systems, such as `Array`, `Stopwatch`, `Image`, `Wave`, `BinaryReader`, `JSON`, `TextWriter`, `NavMesh`, can be used outside the main thread
- When sharing the same object across multiple threads, implement proper synchronization to prevent data races

### Parallel Processing in the Engine
- The Siv3D engine is structured to utilize multiple threads by default for various internal processes
- Audio playback, webcam processing, and communication processing are mainly executed asynchronously


## 76.2 Asynchronous Asset Creation
- When creating assets like `Texture`, `Audio`, `Font`, `PixelShader` asynchronously, the proper approach is to use the asynchronous APIs provided by **asset management**
- You can check if asynchronous asset loading is complete using `IsReady()`
- `Wait()` causes the main thread to wait until loading is complete
- If you access an asset while it's loading asynchronously, an empty asset will be returned
- There's no upper limit to the number of assets that can be loaded asynchronously simultaneously, but it's recommended to limit to an appropriate number considering memory and disk access load

!!! warning "Note for OpenGL Backend"
	- In the OpenGL backend (default on macOS and Linux, and when selected on Windows), asynchronous loading of `TextureAsset` progresses within `System::Update()`
	- During asynchronous loading of `TextureAsset`, call `System::Update()` at the usual frequency
	
```cpp
# include <Siv3D.hpp>

void Main()
{
	const String preloadText = U"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";

	FontAsset::Register(U"MyFont", FontMethod::MSDF, 48, Typeface::Bold);
	TextureAsset::Register(U"MyTexture", U"example/bay.jpg");
	AudioAsset::Register(U"MyAudio", Audio::Stream, U"example/test.mp3");
	AudioAsset::Register(U"MyMIDI", U"example/midi/test.mid");

	// Start asynchronous loading
	FontAsset::LoadAsync(U"MyFont", preloadText);
	TextureAsset::LoadAsync(U"MyTexture");
	AudioAsset::LoadAsync(U"MyAudio");
	AudioAsset::LoadAsync(U"MyMIDI");

	while (System::Update())
	{
		ClearPrint();

		// Check if loading is complete
		Print << FontAsset::IsReady(U"MyFont");
		Print << TextureAsset::IsReady(U"MyTexture");
		Print << AudioAsset::IsReady(U"MyAudio");
		Print << AudioAsset::IsReady(U"MyMIDI");
	}
}
```


## 76.3 (Sample) Asynchronous Loading of Multiple Image Files
- This is a sample that uses the asynchronous API of asset management functionality to asynchronously load and display multiple image files contained in a specified folder
- Since image reading is performed with multiple threads, all loading is expected to complete in a fraction of the time compared to single-threaded operation

```cpp
# include <Siv3D.hpp>

// Function to determine if a file path is an image file (simple implementation)
bool IsImageFilePath(const FilePath& path)
{
	const String extension = FileSystem::Extension(path);

	// Consider files with extensions png, jpg, jpeg as image files 
	return (extension == U"png")
		|| (extension == U"jpg")
		|| (extension == U"jpeg");
}

// Function to return a list of image file paths in the folder selected by folder selection dialog
Array<FilePath> GetImageFilePaths()
{
	// If a folder is selected in the folder selection dialog
	if (const auto directory = Dialog::SelectFolder())
	{
		// Return a list of image file paths contained in that folder
		return FileSystem::DirectoryContents(*directory, Recursive::No)
			.filter(IsImageFilePath);
	}

	return{};
}

void Main()
{
	Window::Resize(1200, 800);

	const Array<FilePath> imageFilePaths = GetImageFilePaths() // From the list of image file paths
		.take(24); // Get up to 24 files

    // Array to record texture asset names
	Array<AssetName> assetNames(imageFilePaths.size());

    // For each file
	for (size_t i = 0; i < imageFilePaths.size(); ++i)
	{
		// Texture asset name (arbitrary)
		const AssetName assetName = U"texture_{}"_fmt(i);

		// Image file path
		const FilePath path = imageFilePaths[i];

        // Record the texture asset name
		assetNames[i] = assetName;

		// Register the asset
		TextureAsset::Register(assetName, path, TextureDesc::Mipped);

		// Start asynchronous loading
		TextureAsset::LoadAsync(assetName);
	}

	while (System::Update())
	{
		// For each asset
		for (size_t i = 0; i < imageFilePaths.size(); ++i)
		{
			const double x = (100.0 + (i % 6) * 200.0);
			const double y = (100.0 + (i / 6) * 200.0);

			if (TextureAsset::IsReady(assetNames[i])) // If asynchronous loading is complete
			{
				// Display the texture
				TextureAsset(assetNames[i]).resized(200).drawAt(x, y);
			}
			else
			{
				// If still loading, display a circle instead
				Circle{ x, y, 50 }.drawFrame(20, ColorF{ 0.75 });
			}
		}
	}
}
```


## 76.4 Asynchronous Processing API
- When writing asynchronous processing in Siv3D, use the `AsyncTask<Type>` class and `Async()` function
- You can use them similarly to C++ standard `std::future<Type>` and `std::async()`

### 76.4.1 `AsyncTask<Type>`
- An asynchronous task class that executes a function asynchronously and manages its state and results
- Usually created by the `Async()` function
- `AsyncTask` has one of the following states:
	- ① Does not have an asynchronous process
	- ② Has an asynchronous process, the task is running, and results cannot be returned yet
	- ③ Has an asynchronous process, the task is complete, and results can be returned immediately

#### Member Functions

```cpp title="Returns whether it has an asynchronous process"
bool AsyncTask<Type>::isValid() const;
```

- Return value: `true` if it has an asynchronous process, `false` otherwise
- Calling `AsyncTask<Type>::get()` returns to a state without an asynchronous process

-----------------------------------

```cpp title="Returns whether it has a completed asynchronous process and can return results immediately"
bool AsyncTask<Type>::isReady() const;
```

- Return value: `true` if it has a completed asynchronous process and can return results immediately, `false` otherwise
- Calling `AsyncTask<Type>::get()` returns to a state without an asynchronous process

-----------------------------------

```cpp title="Returns the result of a completed asynchronous process"
Type AsyncTask<Type>::get();
```

- Return value: The result of the completed asynchronous process
- If the task is not complete, it waits until completion

-----------------------------------

```cpp title="Waits for the asynchronous process task to complete"
void AsyncTask<Type>::wait() const;
```

- If it doesn't have an asynchronous process, this function returns control immediately


### 76.4.2 Async() Function

```cpp title="Creates an asynchronous task"
template <class Fty, class... Args>
auto Async(Fty&& f, Args&&... args);
```

- `f`: Function to execute asynchronously
- `args`: Arguments to pass to function `f`
	- For reference passing, use `std::ref()`
- Return value: Asynchronous task of function `f`'s return type (`AsyncTask<f's return type>`)


## 76.5 Asynchronous Task Basics
- Shows basic usage of executing asynchronous processing using `AsyncTask` and `Async()`
- In the following sample code, when the user presses a button, a heavy 5-second process (`F(5)`) runs in the background without stopping screen animations, and displays the result (5) when complete
- You can confirm that the UI remains responsive even during long-running processes
- At program termination, if there are any running asynchronous tasks, it waits for their completion
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/async/5.png)

```cpp
# include <Siv3D.hpp>

// Function that returns n after n seconds (representing heavy processing with Sleep)
int32 F(int32 n)
{
	// Sleep for n seconds
	System::Sleep(n * 1s);

	return n;
}

void Main()
{
	Scene::SetBackground(ColorF{ 1.0, 0.98, 0.96 });

	// Asynchronous task
	AsyncTask<int32> task;

	while (System::Update())
	{
		// Button can be pressed when there's no asynchronous task
		if (SimpleGUI::Button(U"Call", Vec2{ 600, 20 }, unspecified, (not task.isValid())))
		{
			// Create an asynchronous task that executes function F. Pass 5 as argument to F
			task = Async(F, 5);
		}

		// When the asynchronous task is complete
		if (task.isReady())
		{
			// Get the result
			Print << task.get();
		}

		const double angle = (Scene::Time() * 30_deg);

		for (int32 i = 0; i < 12; ++i)
		{
			const double theta = (i * 30_deg + angle);

			const Vec2 pos = OffsetCircular{ Vec2{ 400, 300 }, 200, theta };

			pos.asCircle(28)
				.drawShadow(Vec2{ 0, 4 }, 12, 4)
				.draw(HSV{ (i * 30), 0.8, 1.0 })
				.drawFrame(3, 2, ColorF{ 1.0 });
		}
	}

	// Wait for any running tasks to complete
    if (task.isValid())
    {
	    task.wait();
    }
}
```


## 76.6 Canceling Asynchronous Tasks
- You can implement a mechanism to safely cancel asynchronous task processing midway
- Cancellation functionality is important for controlling long-running processes through user operations
- This extends the sample from **76.5**, allowing immediate cancellation of running asynchronous processing by pressing an "Abort" button

### Cancellation Mechanism
- Use `std::atomic<bool>` to implement a cancellation flag
	- Using atomic types ensures safe value sharing between threads
	- The flag is shared between the asynchronous process and main thread via reference passing
- The asynchronous process periodically checks the cancellation flag and stops processing if the flag is set
- When cancelled, a special return value (in this example `-1`) is returned to notify the caller of cancellation

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/async/6.png)

```cpp
# include <Siv3D.hpp>

// Function that returns n after n seconds (with cancellation support)
int32 F(int32 n, const std::atomic<bool>& abort)
{
	for (int i = 0; i < (n * 10); ++i)
	{
		// Check cancellation flag every 100 milliseconds
		if (abort) // If cancellation flag is true
		{
			// Cancel before processing is fully complete
			return -1; // Return special value when cancelled
		}

		System::Sleep(100ms);
	}

	return n; // Return argument as-is on normal completion
}

void Main()
{
	Scene::SetBackground(ColorF{ 1.0, 0.98, 0.96 });

	// Asynchronous task
	AsyncTask<int32> task;

	// Cancellation flag (initial value is false)
	std::atomic<bool> abort{ false };

	while (System::Update())
	{
		// Enable Call button only when there's no asynchronous task
		if (SimpleGUI::Button(U"Call", Vec2{ 600, 20 }, unspecified, (not task.isValid())))
		{
			// Create an asynchronous task that executes function F
			// Pass 5 as first argument to F, and reference to cancellation flag as second argument
			task = Async(F, 5, std::ref(abort));
		}

		// Enable Abort button only when asynchronous task is running
		if (SimpleGUI::Button(U"Abort", Vec2{ 600, 60 }, unspecified, task.isValid()))
		{
			// Set cancellation flag to true to instruct asynchronous process to cancel
			abort = true;
		}

		// When the asynchronous task is complete
		if (task.isReady())
		{
			// Get the result
			Print << task.get();

			// Reset cancellation flag to false
			abort = false;
		}

		const double angle = (Scene::Time() * 30_deg);

		for (int32 i = 0; i < 12; ++i)
		{
			const double theta = (i * 30_deg + angle);

			const Vec2 pos = OffsetCircular{ Vec2{ 400, 300 }, 200, theta };

			pos.asCircle(28)
				.drawShadow(Vec2{ 0, 4 }, 12, 4)
				.draw(HSV{ (i * 30), 0.8, 1.0 })
				.drawFrame(3, 2, ColorF{ 1.0 });
		}
	}

	// Cancel and wait for completion if there are running tasks
	if (task.isValid())
	{
		// Issue cancellation instruction
		abort = true;

		// Wait for task completion (completes quickly due to cancellation instruction)
		task.wait();
	}
}
```

#### Key Points
- Use `std::atomic<bool>` to safely share values between multiple threads
- Pass the cancellation flag as a reference using `std::ref` to convey cancellation signals from the main thread to asynchronous processing
- The asynchronous process periodically checks the cancellation flag (every 100 milliseconds in this example)
- At application termination, properly cancel running tasks and wait for completion to prevent resource leaks


## 76.7 (Sample) Getting Intermediate States of Images Being Generated in Another Thread
- The following sample code asynchronously executes a process that fills an image orange one row at a time, safely providing progress and intermediate results to the main thread
- It has the following characteristics:
	- **Thread-safe design**:
		- Cancellation flag using `std::atomic<bool>`
		- Progress management using `std::atomic<size_t>`
		- Shared data protection using `std::mutex`
	- **Resource management**:
		- Safe termination of asynchronous tasks in destructor
		- Release of unnecessary resources after processing completion
	- **Incremental result retrieval**:
		- Update result image row by row
		- Current results can be retrieved from main thread at any time
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/async/7.png)

```cpp
# include <Siv3D.hpp>

// Class for performing image processing asynchronously
// Executes a process that fills an image row by row asynchronously,
// providing functionality to safely retrieve intermediate progress
class ImageTask
{
public:

	ImageTask()
		: m_processingImage{ Size{ 400, 400 }, Palette::White } // Create white image for processing
		, m_result{ m_processingImage } // Initialize result image
	{
		// Start asynchronous task
		m_task = Async(Update, this);
	}

	// Destructor
	// Safely terminates running asynchronous tasks if any
	~ImageTask()
	{
		// If there's an asynchronous task
		if (m_task.isValid())
		{
			// Turn on cancellation flag to instruct processing cancellation
			m_abort = true;

			// Wait until task returns control
			m_task.wait();
		}
	}

	// Get processing progress
	size_t getProgress() const
	{
		return m_processedLine;
	}

	// Reflect current result image to texture
	void get(DynamicTexture& texture)
	{
		// Protect access to result image with mutex
		std::lock_guard lock{ m_mutex };

		// Reflect current result image to texture
		texture.fill(m_result);
	}

private:

	// Execute image processing for one row
	bool update()
	{
		const size_t y = m_processedLine;

		// Fill current row
		for (size_t x = 0; x < m_processingImage.width(); ++x)
		{
			m_processingImage[y][x] = Palette::Orange;
		}

		// Update result image (protected by mutex)
		{
			std::lock_guard lock{ m_mutex };

			// Reflect processing contents to result image
			m_result = m_processingImage;

			// Increment number of processed rows
			++m_processedLine;
		}

		// Check if all rows have been processed
		if (m_processedLine == m_processingImage.height())
		{
			// Release processing image to save memory
			m_processingImage.release();

			// Notify processing completion
			return true;
		}

		// Notify processing continuation
		return false;
	}

	// Static function for asynchronous processing
	static void Update(ImageTask* pImageTask)
	{
		// Continue processing while cancellation flag is not set
		while (not pImageTask->m_abort)
		{
			// Wait 5 milliseconds to adjust processing load
			System::Sleep(5ms);

			// Execute one row of processing, exit loop if complete
			if (pImageTask->update())
			{
				break;
			}
		}
	}

	// Asynchronous task
	AsyncTask<void> m_task;

	// Processing cancellation flag (use atomic type for safe sharing between threads)
	std::atomic<bool> m_abort{ false };

	// Image data being processed
	Image m_processingImage;

	// Number of completed rows (use atomic type for safe sharing between threads)
	std::atomic<size_t> m_processedLine = 0;

	////////
	//
	// Data area protected by mutex
	//
	std::mutex m_mutex;

	// Result image (data provided to main thread)
	Image m_result;
	//
	////////
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Create asynchronous image processing task
	ImageTask imageTask;

	// Texture for displaying results
	DynamicTexture texture;

	// Current progress
	size_t currentProgress = 0;

	while (System::Update())
	{
		// Check if there's a change in progress
		if (const size_t newProgress = imageTask.getProgress();
			currentProgress != newProgress)
		{
			// Get latest result image and reflect to texture
			imageTask.get(texture);

			// Update progress
			currentProgress = newProgress;

			// Display current progress
			Print << currentProgress;
		}

		if (texture)
		{
			texture.draw();
		}
	}
}
```