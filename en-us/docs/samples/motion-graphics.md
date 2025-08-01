# Motion Graphics Samples

## 1. Lines

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/motion-graphics/1.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	namespace MotionGraphics
	{
		/// @brief Draw lines with motion graphics
		/// @param begin Starting point of the line
		/// @param end Ending point of the line
		/// @param thickness Line thickness
		/// @param t Elapsed time [0.0, 1.0]
		/// @param interval Time with nothing displayed before and after motion starts [0.0, 1.0)
		inline void DrawLine(const Vec2& begin, const Vec2& end, double thickness, double t, double interval = 0.0)
		{
			const double s = (0.5 - interval);
			const Line line = Line{ begin, end }.stretched(thickness * 0.5);

			if (InRange(t, interval, 0.5))
			{
				t = (t - interval) / s;
				const double e = Min((EaseOutExpo(t) * 1.03), 1.0);
				Line{ line.begin, line.begin.lerp(line.end, e) }
					.draw(LineStyle::Uncapped, thickness);
			}
			else if (t < (1.0 - interval))
			{
				t = (t - 0.5) / s;
				const double e = Min((EaseOutExpo(t) * 1.03), 1.0);
				Line{ line.begin.lerp(line.end, e), line.end }
					.draw(LineStyle::Uncapped, thickness);
			}
		}
	}

	void Main()
	{
		using MotionGraphics::DrawLine;
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.4 });
		constexpr double period = 1.2;

		while (System::Update())
		{
			// Repeat 0.0 → 1.0 with a period of period (seconds)
			const double t = Periodic::Sawtooth0_1(period);
			DrawLine({ 200, 360 }, { 1080, 360 }, 40, t);
		}
	}
	```


## 2. Symbols

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/motion-graphics/2.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	namespace MotionGraphics
	{
		/// @brief Draw lines with motion graphics
		/// @param begin Starting point of the line
		/// @param end Ending point of the line
		/// @param thickness Line thickness
		/// @param t Elapsed time [0.0, 1.0]
		/// @param interval Time with nothing displayed before and after motion starts [0.0, 1.0)
		inline void DrawLine(const Vec2& begin, const Vec2& end, double thickness, double t, double interval = 0.0)
		{
			const double s = (0.5 - interval);
			const Line line = Line{ begin, end }.stretched(thickness * 0.5);

			if (InRange(t, interval, 0.5))
			{
				t = (t - interval) / s;
				const double e = Min((EaseOutExpo(t) * 1.03), 1.0);
				Line{ line.begin, line.begin.lerp(line.end, e) }
					.draw(LineStyle::Uncapped, thickness);
			}
			else if (t < (1.0 - interval))
			{
				t = (t - 0.5) / s;
				const double e = Min((EaseOutExpo(t) * 1.03), 1.0);
				Line{ line.begin.lerp(line.end, e), line.end }
					.draw(LineStyle::Uncapped, thickness);
			}
		}
	}

	void Main()
	{
		using MotionGraphics::DrawLine;
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.4 });
		constexpr double period = 1.2;

		while (System::Update())
		{
			const double t = Periodic::Sawtooth0_1(period);

			DrawLine({ 440, 160 }, { 840, 160 }, 20, t);
			DrawLine({ 840, 160 }, { 840, 560 }, 20, t);
			DrawLine({ 840, 560 }, { 440, 560 }, 20, t);
			DrawLine({ 440, 560 }, { 440, 160 }, 20, t);

			DrawLine({ 740, 260 }, { 460, 260 }, 20, t);
			DrawLine({ 540, 260 }, { 540, 540 }, 20, t);
			DrawLine({ 540, 460 }, { 820, 460 }, 20, t);
			DrawLine({ 740, 460 }, { 740, 180 }, 20, t);
		}
	}
	```


## 3. Radial pattern

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/motion-graphics/3.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	namespace MotionGraphics
	{
		/// @brief Draw lines with motion graphics
		/// @param begin Starting point of the line
		/// @param end Ending point of the line
		/// @param thickness Line thickness
		/// @param t Elapsed time [0.0, 1.0]
		/// @param interval Time with nothing displayed before and after motion starts [0.0, 1.0)
		inline void DrawLine(const Vec2& begin, const Vec2& end, double thickness, double t, double interval = 0.0)
		{
			const double s = (0.5 - interval);
			const Line line = Line{ begin, end }.stretched(thickness * 0.5);

			if (InRange(t, interval, 0.5))
			{
				t = (t - interval) / s;
				const double e = Min((EaseOutExpo(t) * 1.03), 1.0);
				Line{ line.begin, line.begin.lerp(line.end, e) }
					.draw(LineStyle::Uncapped, thickness);
			}
			else if (t < (1.0 - interval))
			{
				t = (t - 0.5) / s;
				const double e = Min((EaseOutExpo(t) * 1.03), 1.0);
				Line{ line.begin.lerp(line.end, e), line.end }
					.draw(LineStyle::Uncapped, thickness);
			}
		}
	}

	void Main()
	{
		using MotionGraphics::DrawLine;
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.4 });
		constexpr double period = 1.2;

		while (System::Update())
		{
			const double t = Periodic::Sawtooth0_1(period);

			for (auto i : step(6))
			{
				DrawLine(
					OffsetCircular{ Scene::Center(), 80, (i * 60_deg) },
					OffsetCircular{ Scene::Center(), 300, (i * 60_deg) }, 12, t);
			}
		}
	}
	```


## 4. Crossing horizontal lines

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/motion-graphics/4.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	namespace MotionGraphics
	{
		/// @brief Draw lines with motion graphics
		/// @param begin Starting point of the line
		/// @param end Ending point of the line
		/// @param thickness Line thickness
		/// @param t Elapsed time [0.0, 1.0]
		/// @param interval Time with nothing displayed before and after motion starts [0.0, 1.0)
		inline void DrawLine(const Vec2& begin, const Vec2& end, double thickness, double t, double interval = 0.0)
		{
			const double s = (0.5 - interval);
			const Line line = Line{ begin, end }.stretched(thickness * 0.5);

			if (InRange(t, interval, 0.5))
			{
				t = (t - interval) / s;
				const double e = Min((EaseOutExpo(t) * 1.03), 1.0);
				Line{ line.begin, line.begin.lerp(line.end, e) }
					.draw(LineStyle::Uncapped, thickness);
			}
			else if (t < (1.0 - interval))
			{
				t = (t - 0.5) / s;
				const double e = Min((EaseOutExpo(t) * 1.03), 1.0);
				Line{ line.begin.lerp(line.end, e), line.end }
					.draw(LineStyle::Uncapped, thickness);
			}
		}
	}

	void Main()
	{
		using MotionGraphics::DrawLine;
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.4 });
		constexpr double period = 1.2;

		while (System::Update())
		{
			const double t = Periodic::Sawtooth0_1(period);

			for (auto i : step(8))
			{
				DrawLine({ 0, (50 + i * 100) }, { 1280, (50 + i * 100) }, 10, t);
				DrawLine({ 1280, (50 + i * 100 + 50) }, { 0, (50 + i * 100 + 50) }, 10, t);
			}
		}
	}
	```


## 5. Depth effects

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/motion-graphics/5.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	namespace MotionGraphics
	{
		/// @brief Draw lines with motion graphics
		/// @param begin Starting point of the line
		/// @param end Ending point of the line
		/// @param thickness Line thickness
		/// @param t Elapsed time [0.0, 1.0]
		/// @param interval Time with nothing displayed before and after motion starts [0.0, 1.0)
		inline void DrawLine(const Vec2& begin, const Vec2& end, double thickness, double t, double interval = 0.0)
		{
			const double s = (0.5 - interval);
			const Line line = Line{ begin, end }.stretched(thickness * 0.5);

			if (InRange(t, interval, 0.5))
			{
				t = (t - interval) / s;
				const double e = Min((EaseOutExpo(t) * 1.03), 1.0);
				Line{ line.begin, line.begin.lerp(line.end, e) }
					.draw(LineStyle::Uncapped, thickness);
			}
			else if (t < (1.0 - interval))
			{
				t = (t - 0.5) / s;
				const double e = Min((EaseOutExpo(t) * 1.03), 1.0);
				Line{ line.begin.lerp(line.end, e), line.end }
					.draw(LineStyle::Uncapped, thickness);
			}
		}
	}

	void Main()
	{
		using MotionGraphics::DrawLine;
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.4 });
		constexpr double period = 1.2;

		while (System::Update())
		{
			const double t = Periodic::Sawtooth0_1(period);
			const double t2 = Periodic::Sawtooth0_1(period, (Scene::Time() + (period * 0.5)));

			for (auto i : step(12))
			{
				const double x = (60 + i * 120);
				DrawLine({ x, 360 }, { x, 360 - (i + 1) * 30 }, 10, t);
				DrawLine({ x, 360 }, { x, 360 + (i + 1) * 30 }, 10, t);

				const double x2 = (90 + i * 120);
				DrawLine({ x2, 360 }, { x2, 360 - (i + 1) * 10 }, 10, t2);
				DrawLine({ x2, 360 }, { x2, 360 + (i + 1) * 10 }, 10, t2);
			}
		}
	}
	```


## 6. Squares and rotation

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/motion-graphics/6.mp4?raw=true" autoplay loop muted playsinline></video>

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	namespace MotionGraphics
	{
		/// @brief Draw lines with motion graphics
		/// @param begin Starting point of the line
		/// @param end Ending point of the line
		/// @param thickness Line thickness
		/// @param t Elapsed time [0.0, 1.0]
		/// @param interval Time with nothing displayed before and after motion starts [0.0, 1.0)
		inline void DrawLine(const Vec2& begin, const Vec2& end, double thickness, double t, double interval = 0.0)
		{
			const double s = (0.5 - interval);
			const Line line = Line{ begin, end }.stretched(thickness * 0.5);

			if (InRange(t, interval, 0.5))
			{
				t = (t - interval) / s;
				const double e = Min((EaseOutExpo(t) * 1.03), 1.0);
				Line{ line.begin, line.begin.lerp(line.end, e) }
					.draw(LineStyle::Uncapped, thickness);
			}
			else if (t < (1.0 - interval))
			{
				t = (t - 0.5) / s;
				const double e = Min((EaseOutExpo(t) * 1.03), 1.0);
				Line{ line.begin.lerp(line.end, e), line.end }
					.draw(LineStyle::Uncapped, thickness);
			}
		}
	}

	void Main()
	{
		using MotionGraphics::DrawLine;
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.4 });
		constexpr double period = 1.2;

		while (System::Update())
		{
			const double gt = Scene::Time();
			const double t = Periodic::Sawtooth0_1(period);
			const double t2 = Periodic::Sawtooth0_1(period, Scene::Time() + (period * 0.5));

			for (auto i : step(20))
			{
				const double a2 = (i * 3_deg + gt * 18_deg);

				for (auto k : step(4))
				{
					DrawLine(
						OffsetCircular{ Scene::Center(), (i * 60.0), (k * 90_deg + a2) },
						OffsetCircular{ Scene::Center(), (i * 60.0), ((k + (IsEven(i) ? 1 : -1)) * 90_deg + a2) },
						6, (IsEven(i) ? t : t2));
				}
			}
		}
	}
	```