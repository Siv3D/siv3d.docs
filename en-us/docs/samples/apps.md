# Application Samples

## 1. Game of Life editor

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/apps/1.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Using bit fields so that 1 cell becomes 1 byte
	struct Cell
	{
		bool previous : 1 = 0;
		bool current : 1 = 0;
	};

	// Function to fill the field with random cell values
	void RandomFill(Grid<Cell>& grid)
	{
		grid.fill(Cell{});

		// Update excluding boundary cells
		for (auto y : Range(1, (grid.height() - 2)))
		{
			for (auto x : Range(1, (grid.width() - 2)))
			{
				grid[y][x] = Cell{ 0, RandomBool(0.5) };
			}
		}
	}

	// Function to update the field state
	void Update(Grid<Cell>& grid)
	{
		for (auto& cell : grid)
		{
			cell.previous = cell.current;
		}

		// Update excluding boundary cells
		for (auto y : Range(1, (grid.height() - 2)))
		{
			for (auto x : Range(1, (grid.width() - 2)))
			{
				const int32 c = grid[y][x].previous;

				int32 n = 0;
				n += grid[y - 1][x - 1].previous;
				n += grid[y - 1][x].previous;
				n += grid[y - 1][x + 1].previous;
				n += grid[y][x - 1].previous;
				n += grid[y][x + 1].previous;
				n += grid[y + 1][x - 1].previous;
				n += grid[y + 1][x].previous;
				n += grid[y + 1][x + 1].previous;

				// Update cell state
				grid[y][x].current = (c == 0 && n == 3) || (c == 1 && (n == 2 || n == 3));
			}
		}
	}

	// Function to convert field state to image
	void CopyToImage(const Grid<Cell>& grid, Image& image)
	{
		for (auto y : step(image.height()))
		{
			for (auto x : step(image.width()))
			{
				image[y][x] = grid[y + 1][x + 1].current
					? Color{ 0, 255, 0 } : Palette::Black;
			}
		}
	}

	void Main()
	{
		// Number of cells in the field (horizontal)
		constexpr int32 Width = 60;

		// Number of cells in the field (vertical)
		constexpr int32 Height = 60;

		// Allocate 2D array with size including boundary parts that are not computed
		Grid<Cell> grid((Width + 2), (Height + 2), Cell{ 0,0 });

		// Image to visualize the field state
		Image image{ Width, Height, Palette::Black };

		// Dynamic texture
		DynamicTexture texture{ image };

		Stopwatch stopwatch{ StartImmediately::Yes };

		// Auto play
		bool autoStep = false;

		// Update frequency
		double speed = 0.5;

		// Grid display
		bool showGrid = true;

		// Whether image update is needed
		bool updated = false;

		while (System::Update())
		{
			// Button to fill field with random values
			if (SimpleGUI::ButtonAt(U"Random", Vec2{ 700, 40 }, 170))
			{
				RandomFill(grid);
				updated = true;
			}

			// Button to set all field cells to zero
			if (SimpleGUI::ButtonAt(U"Clear", Vec2{ 700, 80 }, 170))
			{
				grid.fill({ 0, 0 });
				updated = true;
			}

			// Pause / play button
			if (SimpleGUI::ButtonAt((autoStep ? U"Pause" : U"Run ▶"), Vec2{ 700, 160 }, 170))
			{
				autoStep = (not autoStep);
			}

			// Update frequency change slider
			SimpleGUI::SliderAt(U"Speed", speed, 1.0, 0.1, Vec2{ 700, 200 }, 70, 100);

			// Step forward button, or update timing check
			if (SimpleGUI::ButtonAt(U"Step", Vec2{ 700, 240 }, 170, (not autoStep))
				|| (autoStep && ((speed * speed) <= stopwatch.sF())))
			{
				Update(grid);
				updated = true;
				stopwatch.restart();
			}

			// Checkbox to specify whether to display grid
			SimpleGUI::CheckBoxAt(showGrid, U"Grid", Vec2{ 700, 320 }, 170);

			// Cell editing on the field
			if (Rect{ 0, 0, 599 }.mouseOver())
			{
				const Point target = (Cursor::Pos() / 10 + Point{ 1, 1 });

				if (MouseL.pressed())
				{
					grid[target].current = true;
					updated = true;
				}
				else if (MouseR.pressed())
				{
					grid[target].current = false;
					updated = true;
				}
			}

			// Update the image
			if (updated)
			{
				CopyToImage(grid, image);
				texture.fill(image);
				updated = false;
			}

			// Display the image enlarged without filter
			{
				const ScopedRenderStates2D sampler{ SamplerState::ClampNearest };
				texture.scaled(10).draw();
			}

			// Display the grid
			if (showGrid)
			{
				for (auto i : step(61))
				{
					Rect{ 0, i * 10, 600, 1 }.draw(ColorF{ 0.4 });
					Rect{ i * 10, 0, 1, 600 }.draw(ColorF{ 0.4 });
				}
			}

			// Highlight the cell under mouse cursor
			if (Rect{ 0, 0, 599 }.mouseOver())
			{
				Cursor::RequestStyle(CursorStyle::Hidden);
				Rect{ Cursor::Pos() / 10 * 10, 10 }.draw(Palette::Orange);
			}
		}
	}
	```


## 2. QR code creation

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/apps/2.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Text to convert
		TextEditState textEdit{ U"Abc" };

		String previous;

		// Dynamic texture to display QR code
		DynamicTexture texture;

		while (System::Update())
		{
			// Text input
			SimpleGUI::TextBox(textEdit, Vec2{ 20,20 }, 1240);

			// If text is updated, recreate QR code
			if (const String current = textEdit.text;
				current != previous)
			{
				// Convert input text to QR code
				if (const auto qr = QR::EncodeText(current))
				{
					// Update dynamic texture with enlarged image with frame
					texture.fill(QR::MakeImage(qr).scaled(500, 500, InterpolationAlgorithm::Nearest));
				}

				previous = current;
			}

			texture.drawAt(640, 400);
		}
	}
	```


## 3. Pixel art drawing

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/apps/3.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Function to get index of cell under cursor
	Optional<Point> CursorPosToIndex(int32 cellSize, const Size& gridSize)
	{
		const Point cursorPos = Cursor::Pos();

		if ((cursorPos.x < 0) || (cursorPos.y < 0))
		{
			return none;
		}

		const Point index = (cursorPos / cellSize);

		if ((not InRange(index.x, 0, (gridSize.x - 1)))
			|| (not InRange(index.y, 0, (gridSize.y - 1))))
		{
			return none;
		}

		return index;
	}

	// Function to calculate cell Rect from index
	Rect IndexToRect(const Point& index, int32 cellSize)
	{
		return Rect{ (index * cellSize), cellSize };
	}

	void Main()
	{
		Scene::SetBackground(Palette::White);

		constexpr int32 CellSize = 40;

		// Calculate number of cells horizontally and vertically from scene size and cell size
		Grid<int32> grid(Scene::Size() / CellSize);

		while (System::Update())
		{
			// Make cursor hand-shaped
			Cursor::RequestStyle(CursorStyle::Hand);

			for (auto p : step(grid.size()))
			{
				IndexToRect(p, CellSize).stretched(-1).draw(ColorF{ 0.95 - grid[p] * 0.3 });
			}

			// Get index of cell under cursor
			// (More efficient than performing click detection on all cells)
			if (const auto index = CursorPosToIndex(CellSize, grid.size()))
			{
				// If left clicked
				if (MouseL.down())
				{
					// Transition 0 → 1 → 2 → 3 → 0 → 1 → ...
					++grid[*index] %= 4;
				}

				// If right button is pressed
				if (MouseR.pressed())
				{
					grid[*index] = 0;
				}
			}
		}
	}
	```


## 4. Clock

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/apps/4.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
		const Vec2 center = Scene::Center();

		while (System::Update())
		{
			Circle{ center, 240 }.drawShadow(Vec2{ 0, 2 }, 12).draw().drawFrame(20, 0, ColorF{ 0.8 });

			// Numbers
			for (auto i : Range(1, 12))
			{
				const Vec2 pos = OffsetCircular{ center, 170, (i * 30_deg) };
				font(i).drawAt(50, pos, ColorF{ 0.3 });
			}

			for (auto i : Range(0, 59))
			{
				const Vec2 pos = OffsetCircular{ center, 210, i * 6_deg };
				Circle{ pos, (i % 5 ? 3 : 6) }.draw(ColorF{ 0.3 });
			}

			// Get current time
			const DateTime time = DateTime::Now();

			// Hour hand
			const double hour = ((time.hour + time.minute / 60.0) * 30_deg);
			Line{ center, Arg::direction = Circular(110, hour) }
				.draw(LineStyle::RoundCap, 18, ColorF{ 0.11 });

			// Minute hand
			const double minute = ((time.minute + time.second / 60.0) * 6_deg);
			Line{ center, Arg::direction = Circular(190, minute) }
				.draw(LineStyle::RoundCap, 8, ColorF{ 0.11 });

			// Second hand
			const double second = (time.second * 6_deg);
			Line{ center, Arg::direction = Circular(190, second) }
				.stretched(40, 0)
				.draw(3, ColorF{ 0.11 });
		}
	}
	```


## 5. Image viewer

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/apps/5.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Texture texture;

		while (System::Update())
		{
			// File was dropped
			if (DragDrop::HasNewFilePaths())
			{
				// File could be loaded as image
				if (const Image image{ DragDrop::GetDroppedFilePaths().front().path })
				{
					// Scale image to fit screen size
					texture = Texture{ image.fitted(Scene::Size()) };
				}
			}

			if (texture)
			{
				texture.drawAt(Scene::CenterF());
			}
		}
	}
	```


## 6. Resizable image viewer

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/apps/6.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::SetStyle(WindowStyle::Sizable);

		Scene::SetResizeMode(ResizeMode::Actual);

		Scene::SetBackground(ColorF{ 0.5 });

		Texture texture;

		while (System::Update())
		{
			// File was dropped
			if (DragDrop::HasNewFilePaths())
			{
				// File could be loaded as image
				if (const Image image{ DragDrop::GetDroppedFilePaths().front().path })
				{
					texture = Texture{ image, TextureDesc::Mipped };
				}
			}

			if (texture)
			{
				texture.fitted(Scene::Size()).drawAt(Scene::CenterF());
			}
		}
	}
	```


## 7. World map

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/apps/7.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		const Array<MultiPolygon> countries = GeoJSONFeatureCollection{ JSON::Load(U"example/geojson/countries.geojson") }.getFeatures()
			.map([](const GeoJSONFeature& f) { return f.getGeometry().getPolygons(); });

		Camera2D camera{ Vec2{ 0, 0 }, 2.0, Camera2DParameters{.maxScale = 4096.0 } };
		Optional<size_t> selected;

		while (System::Update())
		{
			ClearPrint();

			camera.update();
			{
				const auto transformer = camera.createTransformer();
				const double lineThickness = (1.0 / Graphics2D::GetMaxScaling());
				const RectF viewRect = camera.getRegion();

				Print << Cursor::PosF();
				Print << camera.getScale() << U"x";

				Rect{ Arg::center(0, 0), 360, 180 }.draw(ColorF{ 0.2, 0.6, 0.9 }); // Ocean
				{
					for (auto&& [i, country] : Indexed(countries))
					{
						// Skip drawing if outside screen
						if (not country.computeBoundingRect().intersects(viewRect))
						{
							continue;
						}

						if (country.leftClicked())
						{
							selected = i;
						}

						country.draw((selected == i) ? ColorF{ 0.9, 0.8, 0.7 } : ColorF{ 0.93, 0.99, 0.96 });
						country.drawFrame(lineThickness, ColorF{ 0.25 });
					}
				}
			}
			camera.draw(Palette::Orange);
		}
	}
	```


## 8. Video player

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/apps/8.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		VideoTexture videoTexture;
		Audio audio;
		bool playing = false;

		while (System::Update())
		{
			if (playing)
			{
				videoTexture.advance();
			}

			const double videoTime = videoTexture.posSec();
			const double audioTime = audio.posSec();

			// If difference between video and audio playback position is 0.1 seconds or more
			if (audio && (0.1 < AbsDiff(audioTime, videoTime)))
			{
				// Sync audio playback position to video playback position
				audio.seekTime(videoTime);
			}

			if (videoTexture)
			{
				videoTexture.fitted(Scene::Size()).drawAt(Scene::CenterF());
			}

			if (SimpleGUI::Button(U"Open", Vec2{ 40, 640 }, 100))
			{
				playing = false;

				if (audio)
				{
					audio.pause();
				}

				if (const auto path = Dialog::OpenFile({ FileFilter::AllVideoFiles() }))
				{
					videoTexture = VideoTexture{ *path };
					audio = Audio{ Audio::Stream, *path };

					if (videoTexture)
					{
						videoTexture.advance(0.0);
						playing = true;
					}

					if (audio)
					{
						audio.play();
					}
				}
			}

			if (SimpleGUI::Button(U"\U000F04AB", Vec2{ 150, 640 }, 60, (not videoTexture.isEmpty())))
			{
				videoTexture.setPosSec(0.0);
				videoTexture.advance(0.0);
				audio.seekTime(0.0);
			}

			if (SimpleGUI::Button((playing ? U"\U000F03E4" : U"\U000F040A"), Vec2{ 220, 640 }, 60, (not videoTexture.isEmpty())))
			{
				playing = (not playing);

				if (audio)
				{
					if (playing)
					{
						audio.play();
					}
					else
					{
						audio.pause();
					}
				}
			}
		}
	}
	```


## 9. Koch curve

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/apps/9.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	Array<Line> Next(const Array<Line>& lines)
	{
		Array<Line> result;

		for (const auto& line : lines)
		{
			const Vec2 p0 = line.begin;
			const Vec2 p1 = (line.begin + (line.vector() / 3));
			const Vec2 p2 = (p1 + (line.vector() / 3).rotate(-60_deg));
			const Vec2 p3 = (line.end - (line.vector() / 3));
			const Vec2 p4 = line.end;

			result.emplace_back(p0, p1);
			result.emplace_back(p1, p2);
			result.emplace_back(p2, p3);
			result.emplace_back(p3, p4);
		}

		return result;
	}

	void Draw(const Array<Line>& lines)
	{
		const double thickness = Min(2.0 / Graphics2D::GetMaxScaling(), 2.0);

		for (const auto& line : lines)
		{
			line.draw(thickness, Palette::Black);
		}
	}

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });
		const Font font{ FontMethod::MSDF, 48, Typeface::Heavy };

		const Array<Line> e0 = { Line{ -400, 0, 400, 0 } };
		const Array<Line> e1 = Next(e0);
		const Array<Line> e2 = Next(e1);
		const Array<Line> e3 = Next(e2);
		const Array<Line> e4 = Next(e3);
		const Array<Line> e5 = Next(e4);
		const Array<Line> e6 = Next(e5);

		Camera2D camera{ Vec2{ 0, 0 },1.0 };
		size_t level = 0;

		while (System::Update())
		{
			{
				const auto t = camera.createTransformer();
				camera.update();

				if (level == 0)
					Draw(e0);
				else if (level == 1)
					Draw(e1);
				else if (level == 2)
					Draw(e2);
				else if (level == 3)
					Draw(e3);
				else if (level == 4)
					Draw(e4);
				else if (level == 5)
					Draw(e5);
				else if (level == 6)
					Draw(e6);

				camera.draw(Palette::Orange);
			}

			SimpleGUI::RadioButtons(level, { U"E0", U"E1", U"E2", U"E3", U"E4", U"E5", U"E6" }, Vec2{ 20, 20 });

			Rect{ 20, 500, 300, 200 }
				.drawShadow(Vec2{ 3, 3 }, 8, 0)
				.draw(ColorF{ 1.0, 0.9, 0.8 });

			const Line base{ 40, 600, 280, 600 };
			Draw(Next({ base }));
			font(U"Generator").drawAt(24, Vec2{ 160, 680 }, ColorF{ 0.0, 0.5 });
		}
	}
	```

## 10. AI story generation

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/GPT3Story/Screenshot/1.png)

[Siv3D-Sample | AI story generation :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/blob/main/Samples/GPT3Story){:target="_blank" .md-button}