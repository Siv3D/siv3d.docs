# Addon Samples

Features that you want to enable only at specific times in games or apps can be implemented as addons to avoid cluttering your code (such as inside `Main()`). Here are samples of several features that can be implemented with addons.

## 1. Loading circle display

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/addon/1.mp4?raw=true" autoplay loop muted playsinline></video>

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	/// @brief Addon that draws a loading circle
	class LoadingCircleAddon : public IAddon
	{
	public:

		/// @brief Starts drawing the loading circle.
		/// @param circle Circle
		/// @param thickness Trail thickness 
		/// @param color Trail color
		static void Begin(const Circle& circle, double thickness, const ColorF& color)
		{
			if (auto p = Addon::GetAddon<LoadingCircleAddon>(U"LoadingCircleAddon"))
			{
				p->begin(circle, thickness, color);
			}
		}

		/// @brief Ends drawing the loading circle.
		static void End()
		{
			if (auto p = Addon::GetAddon<LoadingCircleAddon>(U"LoadingCircleAddon"))
			{
				p->end();
			}
		}

		/// @brief Returns whether the loading circle drawing is active.
		[[nodiscard]]
		static bool IsActive()
		{
			if (auto p = Addon::GetAddon<LoadingCircleAddon>(U"LoadingCircleAddon"))
			{
				return p->m_active;
			}
			else
			{
				return false;
			}
		}

	private:

		bool init() override
		{
			m_trail = Trail{ LifeTime, [](double) { return 1.0; }, EaseOutExpo };

			return true;
		}

		bool update() override
		{
			if (not m_active)
			{
				return true;
			}

			m_accumulatedTime += Scene::DeltaTime();

			while (UpdateInterval <= m_accumulatedTime)
			{
				m_theta = Math::NormalizeAngle(m_theta + AngleStep);

				const Vec2 pos = OffsetCircular{ m_circle.center, m_circle.r, m_theta };

				m_trail.update(UpdateInterval);

				m_trail.add(pos, m_color, m_thickness);

				m_accumulatedTime -= UpdateInterval;
			}

			return true;
		}

		void draw() const override
		{
			if (not m_active)
			{
				return;
			}

			m_trail.draw();
		}

		static constexpr double LifeTime = 1.5;

		static constexpr double UpdateInterval = (1.0 / 120.0);

		static constexpr double AngleStep = 1.6_deg;

		Circle m_circle{ 0, 0, 0 };

		double m_thickness = 0.0;

		ColorF m_color = Palette::White;

		Trail m_trail;

		double m_accumulatedTime = 0.0;

		double m_theta = 180_deg;

		bool m_active = false;

		void begin(const Circle& circle, double thickness, const ColorF& color)
		{
			m_circle = circle;
			m_thickness = thickness;
			m_color = color;
			m_active = true;

			// Generate a trail of sufficient length at the start
			prewarm();
		}

		void end()
		{
			m_active = false;
		}

		void prewarm()
		{
			// Clear previous trail. In v0.6.14, m_trail.clear() can be used
			m_trail.update(LifeTime);

			m_accumulatedTime = LifeTime;

			m_theta = 180_deg;

			update();
		}
	};

	void Main()
	{
		// Register the addon
		Addon::Register<LoadingCircleAddon>(U"LoadingCircleAddon");

		while (System::Update())
		{
			if (const bool isActive = LoadingCircleAddon::IsActive();
				SimpleGUI::Button((isActive ? U"\U000F04DB" : U"\U000F040A"), Vec2{ 40, 40 }, 60))
			{
				if (not isActive)
				{
					// Start drawing the loading circle
					LoadingCircleAddon::Begin(Circle{ 400, 300, 80 }, 10, ColorF{ 0.8, 0.9, 1.0 });
				}
				else
				{
					// End drawing the loading circle
					LoadingCircleAddon::End();
				}
			}
		}
	}
	```

## 2. Message notifications

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/addon/2.mp4?raw=true" autoplay loop muted playsinline></video>

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	/// @brief Addon that manages notifications
	class NotificationAddon : public IAddon
	{
	public:

		/// @brief Notification types
		enum class Type
		{
			/// @brief Normal
			Normal,

			/// @brief Information
			Information,

			/// @brief Question
			Question,

			/// @brief Success
			Success,

			/// @brief Warning
			Warning,

			/// @brief Failure
			Failure,
		};

		/// @brief Notification style
		struct Style
		{
			/// @brief Notification width
			double width = 300.0;

			/// @brief Notification background color
			ColorF backgroundColor{ 0.0, 0.8 };

			/// @brief Notification frame color
			ColorF frameColor{ 0.75 };

			/// @brief Notification text color
			ColorF textColor{ 1.0 };

			/// @brief Information icon color
			ColorF informationColor{ 0.0, 0.72, 0.83 };

			/// @brief Question icon color
			ColorF questionColor{ 0.39, 0.87, 0.09 };

			/// @brief Success icon color
			ColorF successColor{ 0.0, 0.78, 0.33 };

			/// @brief Warning icon color
			ColorF warningColor{ 1.0, 0.57, 0.0 };

			/// @brief Failure icon color
			ColorF failureColor{ 1.00, 0.32, 0.32 };
		};

		/// @brief Shows a notification.
		/// @param message Message
		/// @param type Notification type
		static void Show(const StringView message, const Type type = NotificationAddon::Type::Normal)
		{
			if (auto p = Addon::GetAddon<NotificationAddon>(U"NotificationAddon"))
			{
				p->show(message, type);
			}
		}

		/// @brief Sets the notification display time.
		/// @param lifeTime Display time (seconds)
		static void SetLifeTime(const double lifeTime)
		{
			if (auto p = Addon::GetAddon<NotificationAddon>(U"NotificationAddon"))
			{
				p->m_lifeTime = lifeTime;
			}
		}

		/// @brief Sets the notification style.
		/// @param style Style
		static void SetStyle(const Style& style)
		{
			if (auto p = Addon::GetAddon<NotificationAddon>(U"NotificationAddon"))
			{
				p->m_style = style;
			}
		}

	private:

		static constexpr StringView Icons = U" \U000F02FC\U000F02D7\U000F0E1E\U000F0029\U000F1398";

		struct Notification
		{
			String message;

			double time = 0.0;

			double currentIndex = 0.0;

			double velocity = 0.0;

			Type type = Type::Normal;
		};

		Style m_style;

		Array<Notification> m_notifications;

		double m_lifeTime = 10.0;

		bool update() override
		{
			const double deltaTime = Scene::DeltaTime();

			for (auto& notification : m_notifications)
			{
				notification.time += deltaTime;
			}

			m_notifications.remove_if([lifeTime = m_lifeTime](const Notification& notification) { return (lifeTime < notification.time); });

			for (size_t i = 0; i < m_notifications.size(); ++i)
			{
				auto& notification = m_notifications[i];
				notification.currentIndex = Math::SmoothDamp(notification.currentIndex,
					static_cast<double>(i), notification.velocity, 0.15, 9999.0, deltaTime);
			}

			return true;
		}

		void draw() const override
		{
			const Font& font = SimpleGUI::GetFont();

			for (const auto& notification : m_notifications)
			{
				double xScale = 1.0;
				double alpha = 1.0;

				if (notification.time < 0.2)
				{
					xScale = alpha = (notification.time / 0.2);
				}
				else if ((m_lifeTime - 0.4) < notification.time)
				{
					alpha = ((m_lifeTime - notification.time) / 0.4);
				}

				alpha = EaseOutExpo(alpha);
				xScale = EaseOutExpo(xScale);

				ColorF backgroundColor = m_style.backgroundColor;
				backgroundColor.a *= alpha;

				ColorF frameColor = m_style.frameColor;
				frameColor.a *= alpha;

				ColorF textColor = m_style.textColor;
				textColor.a *= alpha;

				const RectF rect{ 10, (10 + notification.currentIndex * 32), (m_style.width * xScale), 31 };
				rect.rounded(3).draw(backgroundColor).drawFrame(1, 0, frameColor);

				if (notification.type != Type::Normal)
				{
					ColorF color = notification.type == Type::Information ? m_style.informationColor
						: notification.type == Type::Question ? m_style.questionColor
						: notification.type == Type::Success ? m_style.successColor
						: notification.type == Type::Warning ? m_style.warningColor
						: m_style.failureColor;
					color.a *= alpha;

					font(Icons[FromEnum(notification.type)]).draw(18, Arg::leftCenter = rect.leftCenter().movedBy(8, -1), color);
				}

				font(notification.message).draw(18, Arg::leftCenter = rect.leftCenter().movedBy(32, -1), textColor);
			}
		}

		void show(const StringView message, const Type type)
		{
			const double currentIndex = (m_notifications.empty() ? 0.0 : m_notifications.back().currentIndex + 1.0);
			const double velocity = (m_notifications.empty() ? 0.0 : m_notifications.back().velocity);

			m_notifications << Notification{
				.message = String{ message },
				.time = 0.0,
				.currentIndex = currentIndex,
				.velocity = velocity,
				.type = type };
		}
	};

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Register the addon
		Addon::Register<NotificationAddon>(U"NotificationAddon");

		while (System::Update())
		{
			if (SimpleGUI::Button(U"normal", Vec2{ 600, 40 }, 160))
			{
				NotificationAddon::Show(U"normal");
			}

			if (SimpleGUI::Button(U"information", Vec2{ 600, 80 }, 160))
			{
				NotificationAddon::Show(U"information", NotificationAddon::Type::Information);
			}

			if (SimpleGUI::Button(U"question", Vec2{ 600, 120 }, 160))
			{
				NotificationAddon::Show(U"question", NotificationAddon::Type::Question);
			}

			if (SimpleGUI::Button(U"success", Vec2{ 600, 160 }, 160))
			{
				NotificationAddon::Show(U"success", NotificationAddon::Type::Success);
			}

			if (SimpleGUI::Button(U"warning", Vec2{ 600, 200 }, 160))
			{
				NotificationAddon::Show(U"warning", NotificationAddon::Type::Warning);
			}

			if (SimpleGUI::Button(U"failure", Vec2{ 600, 240 }, 160))
			{
				NotificationAddon::Show(U"failure", NotificationAddon::Type::Failure);
			}
		}
	} 
	```


## 3. 2D drawing with homography transformation
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/addon/3.gif)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	/// @brief Addon for 2D drawing with homography transformation
	class HomographyAddon : public IAddon
	{
	public:

		/// @brief Performs 2D drawing with homography transformation.
		/// @param quad Projection target quadrilateral
		/// @param texture Texture to draw
		static void Draw(const Quad& quad, const Texture& texture)
		{
			if (auto p = Addon::GetAddon<HomographyAddon>(U"HomographyAddon"))
			{
				p->setQuad(quad);
				p->draw(texture);
			}
		}

	private:

		bool init() override
		{
			setQuad(Quad{ Scene::Rect() });
			return (m_vs && m_ps);
		}

		struct Homography
		{
			Float4 m1;
			Float4 m2;
			Float4 m3;
		};

		VertexShader m_vs = HLSL{ U"example/shader/hlsl/homography.hlsl", U"VS" }
			| GLSL{ U"example/shader/glsl/homography.vert", { { U"VSConstants2D", 0 }, { U"VSHomography", 1 } } };

		PixelShader m_ps = HLSL{ U"example/shader/hlsl/homography.hlsl", U"PS" }
			| GLSL{ U"example/shader/glsl/homography.frag", { { U"PSConstants2D", 0 }, { U"PSHomography", 1 } } };

		ConstantBuffer<Homography> m_vsConstant;

		ConstantBuffer<Homography> m_psConstant;

		Quad m_quad{};

		void setQuad(const Quad& quad)
		{
			if (m_quad == quad)
			{
				return;
			}

			m_quad = quad;
			const Mat3x3 mat = Mat3x3::Homography(quad);
			m_vsConstant = { Float4{ mat._11_12_13, 0 }, Float4{ mat._21_22_23, 0 }, Float4{ mat._31_32_33, 0 } };

			const Mat3x3 inv = mat.inverse();
			m_psConstant = { Float4{ inv._11_12_13, 0 }, Float4{ inv._21_22_23, 0 }, Float4{ inv._31_32_33, 0 } };
		}

		void draw(const Texture& texture) const
		{
			const ScopedCustomShader2D shader{ m_vs, m_ps };
			const ScopedRenderStates2D sampler{ SamplerState::ClampAniso };
			Graphics2D::SetVSConstantBuffer(1, m_vsConstant);
			Graphics2D::SetPSConstantBuffer(1, m_psConstant);
			Rect{ 1 }(texture).draw();
		}
	};

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
		const MSRenderTexture renderTexture{ Size{ 800, 600 } };
		const Texture texture{ U"example/windmill.png", TextureDesc::Mipped };

		// Register the addon
		Addon::Register<HomographyAddon>(U"HomographyAddon");

		const Quad q1{ Vec2{ 150, 300 }, Vec2{ 650, 300 }, Vec2{ 800, 600 }, Vec2{ 0, 600 } };
		const Quad q2{ Vec2{ 400, 50 }, Vec2{ 800, 0 }, Vec2{ 800, 300 }, Vec2{ 400, 250 } };

		// Homography transformation matrix from q1 to Scene::Rect()
		const Mat3x3 mat = Mat3x3::Homography(q1, Rect{ 800, 600 }.asQuad());

		while (System::Update())
		{
			// Transform coordinates on q1 to coordinates on Scene::Rect() and calculate cell index
			const Point index = (mat.transformPoint(Cursor::Pos()).asPoint() / 40);
			{
				const ScopedRenderTarget2D target{ renderTexture.clear(ColorF{ 1.0 }) };

				for (int32 y = 0; y < 15; ++y)
				{
					for (int32 x = 0; x < 20; ++x)
					{
						if (Point{ x, y } == index)
						{
							Rect{ (x * 40), (y * 40), 40 }.draw(ColorF{ 0.25 });
						}
						else if (IsEven(y + x))
						{
							Rect{ (x * 40), (y * 40), 40 }.draw(ColorF{ 0.75 });
						}
					}
				}
			}

			// Finalize and resolve MSRenderTexture content
			{
				Graphics2D::Flush();
				renderTexture.resolve();
			}

			HomographyAddon::Draw(q1, renderTexture);
			HomographyAddon::Draw(q2, texture);
		}
	}
	```