# 19. Handling Time
Learn how to manage time using "elapsed time from the previous frame."

## 19.1 Measuring Elapsed Time
- `Scene::DeltaTime` returns the elapsed time from the previous frame (in seconds) as a `double` type
- By accumulating this elapsed time, you can measure the elapsed time (in seconds) since the program started

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/time/1.png)

```cpp hl_lines="10 15"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	// Elapsed time since program start (seconds)
	double time = 0.0;

	while (System::Update())
	{
		// Elapsed time from previous frame (seconds)
		const double deltaTime = Scene::DeltaTime();

		// Add deltaTime to time
		time += deltaTime;

		// Display time
		font(U"time: {:.2f}"_fmt(time)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });

		// Display deltaTime
		font(U"deltaTime: {:.4f}"_fmt(deltaTime)).draw(40, Vec2{ 40, 100 }, ColorF{ 0.1 });
	}
}
```

- The convenient `Stopwatch` class for measuring elapsed time will be explained in detail in **Tutorial 30**


## 19.2 Countdown Timer
- Apply **19.1** to create a program that counts down remaining time from 10 seconds
- When the remaining time reaches 0, display "Time's up!"
- Pressing ++enter++ resets the remaining time and starts the countdown again

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/time/2.png)

```cpp title="10-second countdown" hl_lines="10 13 21"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	// Countdown time (seconds)
	const double countdownTime = 10.0;

	// Remaining time (seconds)
	double remainingTime = countdownTime;

	while (System::Update())
	{
		// Elapsed time from previous frame (seconds)
		const double deltaTime = Scene::DeltaTime();

		// Reduce remaining time
		remainingTime -= deltaTime;

		if (0.0 < remainingTime) // If there's remaining time
		{
			font(U"time: {:.2f}"_fmt(remainingTime)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });
		}
		else // If time is up
		{
			font(U"Time's up!").draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });
			font(U"Press Enter to restart").draw(30, Vec2{ 40, 100 }, ColorF{ 0.1 });

			// If Enter key is pressed
			if (KeyEnter.down())
			{
				// Reset remaining time and restart
				remainingTime = countdownTime;
			}
		}
	}
}
```

- The convenient `Timer` class for countdown timers will be explained in detail in **Tutorial 30**


## 19.3 Doing Something at Regular Intervals (1)
- Create a program that accumulates time and does something when a certain amount of time has accumulated
- Decide on an event period and trigger an event when accumulated time (seconds) exceeds that period (seconds)
- After triggering an event, subtract that period from the accumulated time
- The following code increases the level every 2 seconds

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/time/3.png)

```cpp title="Increase level every 2 seconds" hl_lines="10 13 24 27 33"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	// Level up interval (seconds)
	const double levelUpInterval = 2.0;

	// Accumulated time (seconds)
	double accumulatedTime = 0.0;

	// Level
	int32 level = 0;

	while (System::Update())
	{
		// Elapsed time from previous frame (seconds)
		const double deltaTime = Scene::DeltaTime();

		// Increase accumulated time
		accumulatedTime += deltaTime;

		// If accumulated time exceeds the interval
		if (levelUpInterval < accumulatedTime)
		{
			// Increase level
			++level;

			// Reduce accumulated time by the interval amount
			accumulatedTime -= levelUpInterval;

			Print << U"Level up!";
		}

		// Display current level
		font(U"Level: {}"_fmt(level)).draw(40, Vec2{ 200, 40 }, ColorF{ 0.1 });

		// Display accumulated time
		font(U"accumulatedTime: {:.2f}"_fmt(accumulatedTime)).draw(30, Vec2{ 200, 100 }, ColorF{ 0.1 });
	}
}
```


## 19.4 Doing Something at Regular Intervals (2)
- In **19.3**, you had to wait until the first event occurred after starting the program
- If you want the first event to occur immediately after startup, set the initial value of accumulated time to the same value as the period
- The following code increases the number of enemies immediately at the start and then every 2 seconds thereafter

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/time/4.png)

```cpp title="Increase enemy count immediately at start and every 2 seconds thereafter" hl_lines="13"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	// Enemy increase interval (seconds)
	const double levelUpInterval = 2.0;

	// Accumulated time (seconds)
	double accumulatedTime = levelUpInterval;

	// Number of enemies
	int32 enemyCount = 0;

	while (System::Update())
	{
		// Elapsed time from previous frame (seconds)
		const double deltaTime = Scene::DeltaTime();

		// Increase accumulated time
		accumulatedTime += deltaTime;

		// If accumulated time exceeds the interval
		if (levelUpInterval < accumulatedTime)
		{
			// Increase number of enemies
			++enemyCount;

			// Reduce accumulated time by the interval amount
			accumulatedTime -= levelUpInterval;

			Print << U"Enemy appeared!";
		}

		// Display current number of enemies
		font(U"Enemy count:	{}"_fmt(enemyCount)).draw(40, Vec2{ 200, 40 }, ColorF{ 0.1 });

		// Display accumulated time
		font(U"accumulatedTime: {:.2f}"_fmt(accumulatedTime)).draw(30, Vec2{ 200, 100 }, ColorF{ 0.1 });
	}
}
```

## Review Checklist
- [x] Learned that `Scene::DeltaTime` returns the elapsed time from the previous frame (in seconds) as a `double` type
- [x] Learned that elapsed time (in seconds) can be measured by accumulating elapsed time
- [x] Learned how to create a countdown timer for remaining time
- [x] Learned how to do something at regular intervals