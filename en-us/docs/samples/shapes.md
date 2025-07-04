# Shape Samples

## 1. Checkerboard Background

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/1.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.8 });

		constexpr int32 CellSize = 20;

		while (System::Update())
		{
			for (int32 y = 0; y < (Scene::Height() / CellSize); ++y)
			{
				for (int32 x = 0; x < (Scene::Width() / CellSize); ++x)
				{
					if (IsEven(y + x))
					{
						Rect{ (x * CellSize), (y * CellSize), CellSize }.draw(ColorF{ 0.75 });
					}
				}
			}
		}
	}
	```

## 2. Irregular-Looking Rectangular Grid

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/2.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	//
	// Reference: OffGrid by Chris Cox
	// https://gitlab.com/chriscox/offgrid
	//

	uint64 g_seed = RandomUint64();

	Vec2 PointToRandomVector(int32 x, int32 y)
	{
		PRNG::SplitMix64 rng{ (Point{ x, y }.hash() ^ g_seed) };
		return RandomVec2(Circle{ 1 }, rng);
	}

	Vec2 GetXY(int32 x, int32 y, double t, const Vec2& offset, const double cellSize)
	{
		const Vec2 pos{ offset + (cellSize * Vec2{ x, y }) };
		return (pos + (PointToRandomVector(x, y) * t * (cellSize * 0.5)));
	}

	ColorF GetColor(int32 x, int32 y)
	{
		PRNG::SplitMix64 rng{ (Point{ x, y }.hash() ^ g_seed) };
		return HSV{ Random(60.0, 140.0, rng), Random(0.5, 0.9, rng), Random(0.4, 1.0, rng) };
	}

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 1.0 });

		constexpr Size CellCount{ 10, 6 };
		constexpr Vec2 Offset{ 60, 80 };
		constexpr double CellSize = 90.0;
		constexpr ColorF LineColor{ 0.15 };
		constexpr double LineThickness = 4.0;
		constexpr double LineLengthHalf = (CellSize * 0.4);

		double t = 0.0;
		bool showLine = true;

		while (System::Update())
		{
			SimpleGUI::Slider(t, Vec2{ 1030, 60 }, 160);
			SimpleGUI::CheckBox(showLine, U"Show line", Vec2{ 1030, 100 });
			if (SimpleGUI::Button(U"New seed", Vec2{ 1030, 140 }))
			{
				g_seed = RandomUint64();
			}

			for (int32 y = 0; y < CellCount.y; ++y)
			{
				for (int32 x = 0; x < CellCount.x; ++x)
				{
					const Vec2 p0 = GetXY(x, y, t, Offset, CellSize);
					const Vec2 p1 = GetXY(x + 1, y, t, Offset, CellSize);
					const Vec2 p2 = GetXY(x, y + 1, t, Offset, CellSize);
					const Vec2 p3 = GetXY(x + 1, y + 1, t, Offset, CellSize);
					const ColorF color = GetColor(x, y);

					if (IsEven(x + y))
					{
						const double top = p0.y;
						const double bottom = p3.y;
						const double left = p2.x;
						const double right = p1.x;
						RectF{ left, top, (right - left), (bottom - top) }.stretched(-1).draw(color);
					}
					else
					{
						const double top = p1.y;
						const double bottom = p2.y;
						const double left = p0.x;
						const double right = p3.x;
						RectF{ left, top, (right - left), (bottom - top) }.stretched(-1).draw(color);
					}
				}
			}

			if (showLine)
			{
				for (int32 y = 0; y <= CellCount.y; ++y)
				{
					for (int32 x = 0; x <= CellCount.x; ++x)
					{
						const Vec2 p0 = GetXY(x, y, t, Offset, CellSize);

						if (IsEven(x + y))
						{
							Line{ p0.movedBy(-LineLengthHalf, 0), p0.movedBy(LineLengthHalf, 0) }
								.draw(LineStyle::RoundCap, LineThickness, LineColor);
						}
						else
						{
							Line{ p0.movedBy(0, -LineLengthHalf), p0.movedBy(0, LineLengthHalf) }
								.draw(LineStyle::RoundCap, LineThickness, LineColor);
						}
					}
				}
			}
		}
	}
	```

## 3. Voronoi Diagram

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/3.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		constexpr Size SceneSize{ 1280, 720 };
		constexpr Rect SceneRect{ SceneSize };

		Window::Resize(SceneSize);

		Subdivision2D subdiv{ SceneRect };

		// Generate points at reasonable intervals within the scene rectangle
		for (const PoissonDisk2D pd{ SceneSize, 40 };
			const auto& point : pd.getPoints())
		{
			if (SceneRect.contains(point))
			{
				subdiv.addPoint(point);
			}
		}

		const Array<Polygon> facetPolygons = subdiv
			.calculateVoronoiFacets() // Calculate Voronoi diagram
			.map([SceneRect](const VoronoiFacet& f) // Clip to scene rectangle
		{
			return Geometry2D::And(Polygon{ f.points }, SceneRect).front();
		});

		while (System::Update())
		{
			for (auto&& [i, facetPolygon] : Indexed(facetPolygons))
			{
				facetPolygon
					.draw(HSV{ (i * 25.0), 0.5, 0.9 })
					.drawFrame(3, ColorF{ 1.0 });
			}
		}
	}
	```


## 4. Dynamic Generation of Voronoi and Delaunay Diagrams

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/4.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		constexpr Size SceneSize{ 1280, 720 };
		constexpr Rect SceneRect{ SceneSize };
		constexpr Rect AreaRect = SceneRect.stretched(-50);

		Window::Resize(SceneSize);
		Scene::SetBackground(ColorF{ 0.99 });

		Subdivision2D subdiv{ AreaRect };

		// List of triangles from Delaunay triangulation
		Array<Triangle> triangles;

		// List of Voronoi facet information
		Array<VoronoiFacet> facets;

		// List of facets clipped to rectangle and converted to Polygons
		Array<Polygon> facetPolygons;

		while (System::Update())
		{
			const Vec2 pos = Cursor::PosF();

			// When clicking on the rectangle
			if (AreaRect.leftClicked())
			{
				// Add point
				subdiv.addPoint(pos);

				// Calculate Delaunay triangulation
				subdiv.calculateTriangles(triangles);

				// Calculate Voronoi diagram
				subdiv.calculateVoronoiFacets(facets);

				// Clip to area rectangle
				facetPolygons = facets.map([AreaRect](const VoronoiFacet& f)
				{
					return Geometry2D::And(Polygon{ f.points }, AreaRect).front();
				});
			}

			AreaRect.draw(ColorF{ 0.75 });

			for (auto&& [i, facetPolygon] : Indexed(facetPolygons))
			{
				facetPolygon.draw(HSV{ (i * 25.0), 0.65, 0.8 }).drawFrame(3, ColorF{ 0.25 });
			}

			for (const auto& triangle : triangles)
			{
				triangle.drawFrame(2.5, ColorF{ 0.9 });
			}

			for (const auto& facet : facets)
			{
				Circle{ facet.center, 6 }.drawFrame(5).draw(ColorF{ 0.25 });
			}

			// Find the nearest point to current mouse cursor
			if (const auto nearestVertexID = subdiv.findNearest(pos))
			{
				const Vec2 nearestVertex = subdiv.getVertex(nearestVertexID.value());
				Line{ pos, nearestVertex }.draw(LineStyle::RoundDot, 6, ColorF{ 0.75 });
				Circle{ nearestVertex, 16 }.drawFrame(3.5);
			}
		}
	}
	```


## 5. Getting Part of Shape Outline as LineString

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/5.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.15 });

		const Polygon polygon0 = Shape2D::Plus(180, 100, Scene::Center().movedBy(-350, -120));
		const Polygon polygon1 = Shape2D::Heart(180, Scene::Center().movedBy(0, 120));
		const Polygon polygon2 = Shape2D::NStar(8, 180, 140, Scene::Center().movedBy(350, -120));

		while (System::Update())
		{
			const double t = (Scene::Time() * 720);

			polygon0.draw(ColorF{ 0.4 });
			polygon0.outline(t, 200).draw(LineStyle::RoundCap, 8, ColorF{ 0, 1, 0.5 });

			polygon1.draw(ColorF{ 0.4 });
			polygon1.outline(t, 200).draw(LineStyle::RoundCap, 8, ColorF{ 0, 1, 0.5 });

			polygon2.draw(ColorF{ 0.4 });
			polygon2.outline(t, 200).draw(LineStyle::RoundCap, 8, ColorF{ 0, 1, 0.5 });
		}
	}
	```


## 6. GPU Vertex Generation

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/6.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

		const VertexShader vs
			= HLSL{ U"example/shader/hlsl/soft_shape.hlsl" }
			| GLSL{ U"example/shader/glsl/soft_shape.vert", { { U"VSConstants2D", 0 }, { U"SoftShape", 1 } }};

		if (not vs)
		{
			throw Error{ U"Failed to load a shader file" };
		}

		ConstantBuffer<float> cb;

		while (System::Update())
		{
			cb = static_cast<float>(Scene::Time());
			Graphics2D::SetVSConstantBuffer(1, cb);

			{
				const ScopedCustomShader2D shader{ vs };

				// Draw 360 triangles without vertex data
				// (Vertex data is set in the vertex shader)
				Graphics2D::DrawTriangles(360);
			}
		}
	}
	```


## 7. Rectangle Packing

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/7.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	// Function to create an array of random rectangles scattered on screen
	Array<Rect> GenerateRandomRects()
	{
		Array<Rect> rects(Random(4, 32));

		for (auto& rect : rects)
		{
			const Point center = RandomPoint(Scene::Rect().stretched(-80));
			rect = Rect{ Arg::center = center, Random(20, 150), Random(20, 150) };
		}

		return rects;
	}

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.99 });

		Array<Rect> input;
		Array<double> rotations;
		RectanglePack output;
		Point offset{ 0, 0 };
		Stopwatch stopwatch;

		while (System::Update())
		{
			if ((not stopwatch.isStarted()) || (1.8s < stopwatch))
			{
				input = GenerateRandomRects();
				rotations.resize(input.size());
				rotations.fill(0.0);

				// AllowFlip::Yes allows 90° rotation for packing
				output = RectanglePacking::Pack(input, 1024, AllowFlip::Yes);

				for (size_t i = 0; i < input.size(); ++i)
				{
					if (input[i].w != output.rects[i].w)
					{
						rotations[i] = 270_deg;
					}
				}

				// Adjust position to center on screen
				offset = ((Scene::Size() - output.size) / 2);
				for (auto& rect : output.rects)
				{
					rect.moveBy(offset);
				}

				stopwatch.restart();
			}

			// Animation
			const double k = Min(stopwatch.sF() * 10, 1.0);
			const double t = Math::Saturate(stopwatch.sF() - 0.2);
			const double e = EaseInOutExpo(t);

			Rect{ offset, output.size }.draw(ColorF{ 0.7, e });

			for (size_t i = 0; i < input.size(); ++i)
			{
				const RectF in = input[i].scaledAt(input[i].center(), k);
				const RectF out = output.rects[i];
				const Vec2 center = in.center().lerp(out.center(), e);
				const RectF rect{ Arg::center = center, in.size };

				rect.rotatedAt(rect.center(), Math::Lerp(0.0, rotations[i], e))
					.draw(HSV{ i * 25.0, 0.65, 0.9 })
					.drawFrame(2, 0, ColorF{ 0.25 });
			}
		}
	}
	```


## 8. Hexagonal Tiles

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/8.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	namespace Hex
	{
		inline constexpr Vec2 IndexToPixel(const Point& index, const double hexR) noexcept
		{
			const double tileWidth = (hexR * Math::Sqrt3);
			const double halfWidth = (tileWidth * 0.5);
			const double tileHeight = (hexR * 1.5);
			return{ (index.x * tileWidth + IsOdd(index.y) * halfWidth), (index.y * tileHeight) };
		}

		// Reference
		// https://stackoverflow.com/questions/7705228/hexagonal-grids-how-do-you-find-which-hexagon-a-point-is-in
		inline Point PixelToIndex(const Vec2& _pos, const double hexR)
		{
			const double tileWidth = (hexR * Math::Sqrt3);
			const double halfWidth = (tileWidth * 0.5);
			const double tileHeight = (hexR * 1.5);

			const Vec2 pos{ (_pos.x + halfWidth), (_pos.y + hexR) };
			int32 row = static_cast<int32>(Math::Floor(pos.y / tileHeight));
			const bool rowIsOdd = IsOdd(row);
			int32 column = static_cast<int32>(Math::Floor(rowIsOdd ? ((pos.x - halfWidth) / tileWidth) : (pos.x / tileWidth)));

			const double relY = (pos.y - (row * tileHeight));
			const double relX = (rowIsOdd ? ((pos.x - (column * tileWidth)) - halfWidth) : (pos.x - (column * tileWidth)));
			const double c = (hexR * 0.5);
			const double m = (c / halfWidth);

			if (relY < (-m * relX) + c)
			{
				return{ (column - (not rowIsOdd)), (row - 1) };
			}
			else if (relY < (m * relX) - c)
			{
				return{ (column + rowIsOdd), (row - 1) };
			}

			return{ column, row };
		}
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.5, 0.6, 0.7 });
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		constexpr Vec2 Offset{ 60, 60 };
		constexpr double HexRadius = 50.0;
		const Size GridSize{ 8, 7 };

		while (System::Update())
		{
			for (auto p : step(GridSize))
			{
				const Vec2 center = (Hex::IndexToPixel(p, HexRadius) + Offset);

				Shape2D::Hexagon(HexRadius, center)
					.draw(ColorF{ 0.75 })
					.drawFrame(2);

				font(p).drawAt(16, center);
			}

			{
				const Point index = Hex::PixelToIndex(Cursor::Pos() - Offset, HexRadius);
				const Vec2 center = (Hex::IndexToPixel(index, HexRadius) + Offset);
				Shape2D::Hexagon(HexRadius, center).drawFrame(10);
			}
		}
	}
	```


## 9. 2D Map Visibility Area

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/9.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	class VisibilityMap
	{
	public:

		explicit VisibilityMap(const RectF& region)
			: m_region{ region }
			, m_maxDistance{ m_region.w + m_region.h }
		{
			add(m_region);
		}

		void add(const Triangle& t)
		{
			m_lines << t.side(0) << t.side(1) << t.side(2);
		}

		void add(const RectF& r)
		{
			m_lines << r.top() << r.right() << r.bottom() << r.left();
		}

		void add(const Quad& q)
		{
			m_lines << q.side(0) << q.side(1) << q.side(2) << q.side(3);
		}

		void add(const Circle& c, int32 quality = 8)
		{
			const double da = (2_pi / Max(quality, 6));

			for (int32 i = 0; i < quality; ++i)
			{
				m_lines.emplace_back(c.getPointByAngle(da * i), c.getPointByAngle(da * (i + 1)));
			}
		}

		void add(const Polygon& p)
		{
			const auto& outer = p.outer();

			for (size_t i = 0; i < outer.size(); ++i)
			{
				m_lines.emplace_back(outer[i], outer[(i + 1) % outer.size()]);
			}
		}

		template <class Shape>
		void add(const Array<Shape>& shapes)
		{
			for (const auto& shape : shapes)
			{
				add(shape);
			}
		}

		const RectF& getRegion() const
		{
			return m_region;
		}

		Array<Triangle> calculateVisibilityTriangles(const Vec2& eyePos) const
		{
			const auto points = calculateCollidePoints(eyePos);

			Array<Triangle> triangles(points.size());

			for (size_t i = 0; i < triangles.size(); ++i)
			{
				triangles[i].set(eyePos, points[i].second, points[(i + 1) % points.size()].first);
			}

			return triangles;
		}

	private:

		static constexpr double m_epsilon = 1e-10;

		RectF m_region;

		double m_maxDistance = 0.0;

		Array<Line> m_lines;

		const Array<std::pair<Vec2, Vec2>> calculateCollidePoints(const Vec2& eyePos) const
		{
			if (not m_region.stretched(-1).contains(eyePos))
			{
				return{};
			}

			Array<double> angles{ Arg::reserve = m_lines.size() };
			{
				for (const auto& line : m_lines)
				{
					const Vec2 v = (line.begin - eyePos);
					angles.push_back(Math::Atan2(v.y, v.x));
				}
				angles.sort();
			}

			Array<std::pair<Vec2, Vec2>> points{ Arg::reserve = angles.size() };

			for (auto angle : angles)
			{
				const double left = (angle - m_epsilon);
				const double right = (angle + m_epsilon);
				const Line leftRay{ eyePos, Arg::direction = (Vec2::Right().rotated(left) * m_maxDistance) };
				const Line rightRay{ eyePos, Arg::direction = (Vec2::Right().rotated(right) * m_maxDistance) };

				Vec2 leftCollidePoint = leftRay.end;
				Vec2 rightCollidePoint = rightRay.end;

				for (const auto& line : m_lines)
				{
					if (const auto p = leftRay.intersectsAt(line))
					{
						if (p->distanceFromSq(eyePos) < leftCollidePoint.distanceFromSq(eyePos))
						{
							leftCollidePoint = *p;
						}
					}

					if (const auto p = rightRay.intersectsAt(line))
					{
						if (p->distanceFromSq(eyePos) < rightCollidePoint.distanceFromSq(eyePos))
						{
							rightCollidePoint = *p;
						}
					}
				}

				points.emplace_back(leftCollidePoint, rightCollidePoint);
			}

			return points;
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);

		constexpr ColorF objectColor = Palette::Deepskyblue;
		const Array<Triangle> triangles{ Triangle{ 120, 120, 300, 120, 120, 500 } };
		const Array<RectF> rects{ Rect{ 600, 40, 40, 260 }, Rect{ 440, 300, 440, 40 }, Rect{ 1040, 300, 200, 40 }, Rect{ 480, 480, 240, 100 } };
		const Array<Circle> circles{ Circle{ 1000, 500, 80 }, Circle{ 460, 180, 30 }, Circle{ 240, 480, 30 }, Circle{ 300, 560, 30 } };
		const Array<Polygon> polygons{ Shape2D::Star(60, Vec2{ 940, 180 }) };

		VisibilityMap map(Rect{ 40, 40, 1200, 640 });
		{
			map.add(triangles);
			map.add(rects);
			map.add(circles);
			map.add(polygons);
		}

		while (System::Update())
		{
			Cursor::RequestStyle(CursorStyle::Hidden);

			for (const auto& triangle : triangles)
			{
				triangle.draw(objectColor);
			}

			for (const auto& rect : rects)
			{
				rect.draw(objectColor);
			}

			for (const auto& circle : circles)
			{
				circle.draw(objectColor);
			}

			for (const auto& polygon : polygons)
			{
				polygon.draw(objectColor);
			}

			map.getRegion().drawFrame(0, 8, objectColor);

			const Vec2 eyePos = Cursor::Pos();

			const auto vTriangles = map.calculateVisibilityTriangles(eyePos);

			for (const auto& vTriangle : vTriangles)
			{
				vTriangle.draw(ColorF{ 1.0, 0.5 });
			}

			Circle{ eyePos, 20 }.draw(Palette::Orange).drawFrame(1, 2);
		}
	}
	```


## 10. Generate Non-Overlapping Points at Reasonable Distances

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/10.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		constexpr Rect AreaRect{ 100, 120, 600, 400 };

		double r = 15.0;

		// Generate point set
		PoissonDisk2D pd{ AreaRect.size, r };

		while (System::Update())
		{
			AreaRect.stretched(r).draw(ColorF{ 0.7 });

			AreaRect.draw(ColorF{ 0.2 });

			for (const auto& point : pd.getPoints())
			{
				Circle{ point, (r / 4) }.movedBy(AreaRect.pos).draw();
			}

			if (SimpleGUI::Slider(r, 5.0, 40.0, Vec2{ 40, 40 }))
			{
				// Regenerate point set
				pd = PoissonDisk2D{ AreaRect.size, r };
			}
		}
	}
	```

## 11. Sketch to Polygon

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/11.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Array of created Polygons
		Array<Polygon> polygons;

		// LineString being drawn
		LineString points;

		while (System::Update())
		{
			// If left click occurs or continues with movement
			if (MouseL.down() ||
				(MouseL.pressed() && (not Cursor::DeltaF().isZero())))
			{
				points << Cursor::PosF();
			}
			else if (MouseL.up())
			{
				points = points.simplified(2.0);

				if (const Polygon polygon = Polygon::CorrectOne(points))
				{
					polygons << polygon;
				}

				points.clear();
			}

			// Draw each Polygon
			for (auto&& [i, polygon] : Indexed(polygons))
			{
				polygon.draw(HSV{ (i * 20), 0.4, 1.0 })
					.drawWireframe(1, Palette::Gray)
					.drawFrame(4, HSV{ i * 20 });
			}

			points.draw(4);
		}
	}
	```


## 12. Getting Spline2D Curvature

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/BMUod9VWbgI?si=iPu3wvIvn3wbTBWs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.75 });

		Array<Vec2> points;
		Spline2D spline;

		Polygon polygon;
		Stopwatch stopwatch;
		SplineIndex si;

		while (System::Update())
		{
			// Add a control point
			if (MouseL.down())
			{
				points << Cursor::Pos();
				spline = Spline2D{ points, CloseRing::Yes };
				polygon = spline.calculateRoundBuffer(24);
				stopwatch.restart();
			}

			// Visualize the Bounding Rect of each segment
			for (size_t i = 0; i < spline.size(); ++i)
			{
				const ColorF color = Colormap01F(i / 18.0);
				spline.boundingRect(i)
					.draw(ColorF{ color, 0.1 })
					.drawFrame(1, 0, ColorF{ color, 0.5 });
			}

			// Display the triangulation for 1 second after adding a point
			if (stopwatch.isRunning()
				&& (stopwatch < 1s))
			{
				polygon.drawWireframe(1, ColorF{ 0.25, (1.0 - stopwatch.sF()) });
				polygon.draw(ColorF{ 0.4, stopwatch.sF() });
			}
			else
			{
				polygon.draw(ColorF{ 0.4 });
				// Draw the spline with a color corresponding to its curvature
				spline.draw(10, [&](SplineIndex si) { return Colormap01F(spline.curvature(si) * 24); });
			}

			// Display the control points
			for (const auto& point : points)
			{
				Circle{ point, 8 }.drawFrame(2, ColorF{ 0.8 });
			}

			// Draw an object moving along the spline
			if (spline)
			{
				si = spline.advanceWrap(si, (Scene::DeltaTime() * 400));
				Circle{ spline.position(si), 20 }.draw(HSV{ 145, 0.9, 0.95 });
			}
		}
	}
	```


## 13. LineString Total Length and Point at a Specific Distance

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/f18dwkDLApI?si=IuIMLb1nCHDBkwvy" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		LineString points;
		Polygon polygon;

		// The distance of the moving object from the starting position
		double distanceFromOrigin = 0.0;

		// The total length of the LineString
		double length = 0.0;

		while (System::Update())
		{
			if (MouseL.down())
			{
				points << Cursor::Pos();
				polygon = points.calculateRoundBuffer(20);
				length = points.calculateLength();
			}

			polygon.draw().drawFrame(2, ColorF{ 0.7 });
			points.draw(2, ColorF{ 0.75 });

			if ((2 <= points.size()) && length)
			{
				distanceFromOrigin += (Scene::DeltaTime() * 800);

				if (length < distanceFromOrigin)
				{
					distanceFromOrigin = Math::Fmod(distanceFromOrigin, length);
				}

				// The point on the LineString at the specified distance
				const Vec2 position = points.calculatePointFromOrigin(distanceFromOrigin);
				position.asCircle(20).draw(ColorF{ 0.5 });
			}
		}
	}
	```


## 14. Hausdorff Distance

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/14.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 1.0, 0.96, 0.92 });
		const Font font{ FontMethod::MSDF, 48, Typeface::Heavy };

		const Polygon polygon = Shape2D::Star(240, Scene::Center());
		const LineString contour = polygon.outline();

		// Use a LineString that goes around the polygon, connecting the start and end points, as a reference
		LineString contourClosed = contour;
		contourClosed << contour.front();

		// Densify the LineString to improve the calculation accuracy
		const LineString base = contourClosed.densified(10.0);

		LineString lines;
		double distance = Math::Inf;

		while (System::Update())
		{
			contour.drawClosed(12, ColorF{ 0.7 });

			lines.draw(10, HSV{ 10, 1.0, 0.95 });

			if (MouseL.pressed())
			{
				lines << Cursor::Pos();

				// Hausdorff distance
				distance = Geometry2D::HausdorffDistance(base, lines);
			}

			if (MouseR.pressed())
			{
				lines.clear();
				distance = Math::Inf;
			}

			if (IsFinite(distance))
			{
				font(U"{:.2f}"_fmt(distance)).draw(40, Vec2{ 20, 20 }, ColorF{ 0.25 });
			}
		}
	}
	```


## 15. Convex Hull of a Point Set

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/15.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Array<Vec2> points;

		Polygon convexHull;

		while (System::Update())
		{
			if (MouseL.down())
			{
				// Add a point
				points << Cursor::Pos();

				// Calculate the convex hull
				convexHull = Geometry2D::ConvexHull(points);
			}

			convexHull.draw(Palette::Skyblue);

			for (const auto& point : points)
			{
				Circle{ point, 5 }.draw(Palette::Seagreen);
			}
		}
	}
	```


## 16. Extending a Polygon

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/16.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Polygon polygon;

		while (System::Update())
		{
			const Rect rect{ Arg::center = Cursor::Pos(), 100 };

			if (MouseL.down())
			{
				// Add a rect to the polygon
				// However, it will fail and return false if the polygon and rect do not connect
				polygon.append(rect);
			}

			polygon
				.draw(Palette::Skyblue)
				.drawWireframe(1, Palette::White);

			rect.drawFrame(1, 0, Palette::Skyblue);
		}
	}
	```


## 17. Determining if a Vertex Array is Clockwise

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/17.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void DrawArrow(const Vec2& start, const Vec2& end)
	{
		Line{ start, end }.stretched(-10)
			.drawArrow(3, Vec2::All(20), ColorF{ 0.25 });
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.96, 0.98, 1.0 });

		Array<Vec2> points;

		while (System::Update())
		{
			// Add a point with a left-click
			if (MouseL.down())
			{
				points << Cursor::Pos();
			}

			// Remove all points with a right-click
			if (MouseR.down())
			{
				points.clear();
			}

			const bool isClockwise = Geometry2D::IsClockwise(points);

			ClearPrint();
			Print << isClockwise;

			for (const auto& point : points)
			{
				Circle{ point, 10 }.draw(Palette::Orange);
			}

			if (2 < points.size())
			{
				// Draw arrows to always appear clockwise
				if (isClockwise)
				{
					for (size_t i = 0; i < points.size(); ++i)
					{
						DrawArrow(points[i], points[(i + 1) % points.size()]);
					}
				}
				else
				{
					for (size_t i = 0; i < points.size(); ++i)
					{
						// Draw arrows in the opposite direction
						DrawArrow(points[(i + 1) % points.size()], points[i]);
					}
				}
			}
		}
	}
	```


## 18. Automatic Correction of Invalid Polygon Vertices

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/shapes/18.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		const Font font{ 20, Typeface::Bold };

		// Input vertex sequence
		Array<Vec2> points;

		// Array of proper Polygons generated from the vertex sequence
		Array<Polygon> solvedPolygons;

		while (System::Update())
		{
			if (MouseL.down())
			{
				points << Cursor::Pos();

				// Create proper Polygons from the input vertex sequence
				solvedPolygons = Polygon::Correct(points, {});
			}
			else if (MouseR.down())
			{
				points.clear();
				solvedPolygons.clear();
			}

			// Visualize the input vertex sequence
			for (auto [i, point] : Indexed(points))
			{
				Circle{ point, 5 }.draw();
				Line{ points[i], points[(i + 1) % points.size()] }
					.drawArrow(2, Vec2{ 20, 20 }, Palette::Orange);
			}

			font(points).draw(Rect{ 20, 20, 600, 720 });

			// Visualize the Polygon
			{
				const Transformer2D transformer{ Mat3x2::Translate(640, 0) };

				for (auto [i, solvedPolygon] : Indexed(solvedPolygons))
				{
					const HSV color{ (i * 40.0), 0.7, 1.0 };
					solvedPolygon.draw(color);

					const auto& outer = solvedPolygon.outer();

					for (auto [k, point] : Indexed(outer))
					{
						const Vec2 begin = outer[k];
						const Vec2 end = outer[(k + 1) % outer.size()];
						const Vec2 v = (end - begin).normalized();
						const Vec2 c = (begin + end) / 2;
						const Vec2 oc = c + v.rotated(-90_deg) * 10;
						Line{ oc - v * 20, oc + v * 20 }
						.drawArrow(2, Vec2{ 10, 10 }, color);
					}
				}

				font(solvedPolygons).draw(Rect{ 20, 20, 600, 720 });
			}
		}
	}
	```
