# UI のサンプル

## 1. 選択領域を点線で描画する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/ui/1.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	/// @brief 長方形の領域を選択する点線を描画します。
	/// @param start 選択の開始位置
	/// @param cursorPos マウスカーソルの位置
	/// @param thickness 線の太さ
	/// @param lineColor 線の色
	void DrawSelectRect(const Vec2& start, const Vec2& cursorPos, double thickness, const ColorF& lineColor = Palette::White)
	{
		const RectF rect = RectF::FromPoints(start, cursorPos);

		Line top = rect.top(), right = rect.right(), bottom = rect.bottom(), left = rect.left();

		// 始点からの点線の伸縮が自然になるよう、線の方向を調整する
		{
			if (cursorPos.x < start.x)
			{
				top.reverse();
			}

			if (start.x < cursorPos.x)
			{
				bottom.reverse();
			}

			if (cursorPos.y < start.y)
			{
				right.reverse();
			}

			if (start.y < cursorPos.y)
			{
				left.reverse();
			}
		}

		top.draw(LineStyle::SquareDot, thickness, lineColor);
		right.draw(LineStyle::SquareDot, thickness, lineColor);
		bottom.draw(LineStyle::SquareDot, thickness, lineColor);
		left.draw(LineStyle::SquareDot, thickness, lineColor);
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		constexpr double Thickness = 4.0;

		Optional<Point> start;

		while (System::Update())
		{
			if (MouseL.down())
			{
				start = Cursor::Pos();
			}
			else if (MouseL.up())
			{
				start.reset();
			}

			if (start && MouseL.pressed())
			{
				DrawSelectRect(*start, Cursor::Pos(), Thickness);
			}
		}
	}
	```

## 2. プルダウン

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/ui/2.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	class Pulldown
	{
	public:

		Pulldown() = default;

		Pulldown(const Array<String>& items, const Font& font, const double fontSize, const Vec2& pos)
			: m_font{ font }
			, m_fontSize{ fontSize }
			, m_items{ items }
			, m_maxitemWidth{ getMaxItemWidth() }
			, m_rect{ getRect(pos) } {}

		bool isEmpty() const noexcept
		{
			return m_items.empty();
		}

		void setPos(const Vec2& pos) noexcept
		{
			m_rect.setPos(pos);
		}

		[[nodiscard]]
		const RectF& getRect() const noexcept
		{
			return m_rect;
		}

		[[nodiscard]]
		size_t getIndex() const noexcept
		{
			return m_index;
		}

		[[nodiscard]]
		const Array<String>& getItems() const noexcept
		{
			return m_items;
		}

		void update()
		{
			if (isEmpty())
			{
				return;
			}

			if (m_rect.leftClicked())
			{
				m_isOpen = (not m_isOpen);
				MouseL.clearInput();
			}

			if (not m_isOpen)
			{
				return;
			}

			Vec2 itemPos = m_rect.pos.movedBy(0, m_rect.h);

			for (size_t i = 0; i < m_items.size(); ++i)
			{
				const RectF itemRect{ itemPos, m_rect.w, m_rect.h };

				if (itemRect.leftClicked())
				{
					m_index = i;
					m_isOpen = false;
					MouseL.clearInput();
					break;
				}

				itemPos.y += m_rect.h;
			}
		}

		void draw() const
		{
			m_rect.draw();

			if (isEmpty())
			{
				return;
			}

			m_rect.drawFrame(1, 0, m_isOpen ? Palette::Orange : Palette::Gray);

			m_font(m_items[m_index]).draw(m_fontSize, (m_rect.pos + Padding), TextColor);

			Triangle{ (m_rect.rightX() - DownButtonSize / 2.0 - Padding.x), (m_rect.y + m_rect.h / 2.0),
				(DownButtonSize * 0.5), 180_deg }.draw(TextColor);

			if (not m_isOpen)
			{
				return;
			}

			Vec2 itemPos = m_rect.bl();

			const RectF backRect{ itemPos, m_rect.w, (m_rect.h * m_items.size()) };

			backRect.drawShadow({ 1, 1 }, 5, 0).draw();

			for (const auto& item : m_items)
			{
				const RectF rect{ itemPos, m_rect.size };

				if (rect.mouseOver())
				{
					rect.draw(Palette::Skyblue);
				}

				m_font(item).draw(m_fontSize, (itemPos + Padding), TextColor);

				itemPos.y += m_rect.h;
			}

			backRect.drawFrame(1, 0, Palette::Gray);
		}

	private:

		static constexpr Size Padding{ 8, 2 };

		static constexpr int32 DownButtonSize = 16;

		static constexpr ColorF TextColor{ 0.11 };

		Font m_font;

		double m_fontSize = 12;

		Array<String> m_items;

		size_t m_index = 0;

		double m_maxitemWidth = 0;

		RectF m_rect{ 0 };

		bool m_isOpen = false;

		[[nodiscard]]
		double getMaxItemWidth() const
		{
			double result = 0.0;

			for (const auto& item : m_items)
			{
				result = Max(result, (m_font(item).region(m_fontSize).w));
			}

			return result;
		}

		[[nodiscard]]
		RectF getRect(const Vec2& pos) const noexcept
		{
			const double fontHeight = (m_font.height() * (m_fontSize / m_font.fontSize()));

			return{
				pos,
				(m_maxitemWidth + (Padding.x * 3 + DownButtonSize)),
				(fontHeight + Padding.y * 2)
			};
		}
	};

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

		const Font font{ FontMethod::MSDF, 48 };

		const Array<String> items = { U"日本語", U"English", U"中文", U"Español", U"Français" };

		Pulldown pulldown1{ items, font, 24, Vec2{ 160, 40 } };

		Pulldown pulldown2{ items, font, 16, Vec2{ 320, 40 } };

		while (System::Update())
		{
			ClearPrint();
			Print << pulldown1.getItems()[pulldown1.getIndex()];
			Print << pulldown2.getItems()[pulldown2.getIndex()];

			pulldown1.update();
			pulldown2.update();

			pulldown1.draw();
			pulldown2.draw();
		}
	}
	```


## 3. トースト通知（Windows 版）

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/ui/3.jpg)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.9, 0.6, 0.3 });

		// 通知ごとに割り振られる ID
		ToastNotificationID latest = -1;

		// 画像を作成・保存
		Emoji::CreateImage(U"🍕").save(U"pizza.png");

		while (System::Update())
		{
			ClearPrint();

			// 通知の状態
			Print << (int32)Platform::Windows::ToastNotification::GetState(latest);

			// アクションボタンの結果
			Print << U"Action: " << Platform::Windows::ToastNotification::GetAction(latest);

			if (SimpleGUI::Button(U"Send a notification", Vec2{ 10, 70 }))
			{
				const ToastNotificationItem toast{
					.title = U"Title", // 通知のタイトル
					.message = U"Message", // 通知の本文
					.imagePath = U"pizza.png", // 大きい画像だと使われないことがある
					.actions = { U"Yes", U"No" } // アクションボタン（不要な場合は設定しない）
				};

				// 通知ごとに割り振られる ID を取得
				latest = Platform::Windows::ToastNotification::Show(toast);
			}
		}
	}
	```


## 4. 手書き風 UI

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/ui/4.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	struct Button
	{
		Rect rect;
		String label;
	};

	class PenEffect
	{
	public:

		PenEffect() = default;

		explicit PenEffect(const Size& size)
			: m_texture{ size, ColorF{ 1.0, 0.0 } }
		{
			initLines();
		}

		void reset()
		{
			initLines();
			m_texture.clear(ColorF{ 1.0, 0.0 });
			m_texture.resolve();
		}

		void update(double delta)
		{
			m_accumulatedLength = Min(m_accumulatedLength + (m_length * delta), m_length);

			if ((4.0 <= (m_accumulatedLength - m_paintedLength)))
			{
				BlendState bs = BlendState::Default2D;
				bs.srcAlpha = Blend::SrcAlpha;
				bs.dstAlpha = Blend::DestAlpha;
				bs.opAlpha = BlendOp::Max;
				const ScopedRenderStates2D blend(bs);
				const ScopedRenderTarget2D target{ m_texture };

				while (4.0 <= (m_accumulatedLength - m_paintedLength))
				{
					m_lines.calculatePointFromOrigin(m_paintedLength)
						.asCircle(6).draw(ColorF{ 1.0 });

					m_paintedLength += 4.0;
				}

				Graphics2D::Flush();
				m_texture.resolve();
			}
		}

		const Texture& getTexture() const
		{
			return m_texture;
		}

	private:

		void initLines()
		{
			m_lines.clear();

			const Size size = m_texture.size();

			Point penPos{ 8, (size.y - Random(8, 24)) };

			for (;;)
			{
				m_lines << penPos;
				penPos.x += Random(18, 28);
				penPos.y = Random(6, 20);

				if ((size.x - 8) < penPos.x)
				{
					break;
				}

				m_lines << penPos;
				penPos.x -= Random(8, 16);
				penPos.y = size.y - Random(6, 20);
			}

			m_length = m_lines.calculateLength();
			m_accumulatedLength = 0.0;
			m_paintedLength = 0.0;
		}

		MSRenderTexture m_texture;

		LineString m_lines;

		double m_length = 0.0;

		double m_accumulatedLength = 0.0;

		double m_paintedLength = 0.0;
	};

	void Main()
	{
		const ColorF backgroundColor{ 1.0, 0.98, 0.96 };
		Scene::SetBackground(backgroundColor);

		const Array<Button> buttons =
		{
			Button{ Rect{ Arg::center(400, 300), 300, 80 }, U"あそぶ" },
			Button{ Rect{ Arg::center(400, 400), 300, 80 }, U"スコア" },
			Button{ Rect{ Arg::center(400, 500), 300, 80 }, U"おわる" },
		};

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		Array<PenEffect> penEffects =
		{
			PenEffect{ Size{300, 90} },
			PenEffect{ Size{300, 90} },
			PenEffect{ Size{300, 90} }
		};

		Optional<size_t> selectedItem;
		Stopwatch stopwatch{ StartImmediately::Yes };

		while (System::Update())
		{
			if (SimpleGUI::Button(U"Restart", Vec2{ 20,20 }))
			{
				for (auto& penEffect : penEffects)
				{
					penEffect.reset();
				}

				stopwatch.restart();
			}

			for (size_t i = 0; i < penEffects.size(); ++i)
			{
				if ((i * 250) < stopwatch.ms())
				{
					penEffects[i].update(Scene::DeltaTime() * 0.5);
				}
			}

			selectedItem.reset();

			for (size_t i = 0; i < buttons.size(); ++i)
			{
				const auto& button = buttons[i];

				if (button.rect.mouseOver())
				{
					selectedItem = i;
					Cursor::RequestStyle(CursorStyle::Hand);
				}

				const bool selected = (selectedItem == i);

				penEffects[i].getTexture().drawAt(button.rect.center(), HSV{ 30 + i * 60 });

				font(button.label)
					.drawAt(TextStyle::OutlineShadow(0.3, HSV{ backgroundColor } - HSV{0.0, 0.0, 0.5}, Vec2{ 0, 0 }, backgroundColor),
						(selected ? 48 : 40), button.rect.center(), ColorF{ 1.0, 0.0 });
			}
		}
	}
	```


## 5. メニュー画面

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/ui/5.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.9, 0.95, 1.0 });

		while (System::Update())
		{
			Rect{ 450, 0, 100, 600 }
				.shearedX(150).draw(HSV{ 40, 0.5, 1.0 });

			Rect{ 550, 0, 400, 600 }
				.shearedX(150).draw(HSV{ 40, 0.8, 1.0 });

			for (int32 i = 0; i < 5; ++i)
			{
				const RoundRect rr{ 50, (60 + i * 100), 350, 80, 40 };

				rr.drawShadow(Vec2{ 4, 4 }, 18, 0)
					.draw();

				Circle{ rr.rect.pos.movedBy(rr.r, rr.r), rr.r }
					.stretched(-5)
					.draw(HSV{ 40, 0.5, 1.0 });
			}
		}
	}
	```


## 6. スプレッドシート

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/ui/6.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

		constexpr int32 CellCountX = 8;
		constexpr int32 CellCountY = 15;

		Array<double> minColumnWidths((CellCountX + 1), 120);
		minColumnWidths[0] = 20;

		Array<String> columnNames = { U"" };
		for (int32 i = 1; i < (CellCountX + 1); ++i)
		{
			columnNames.push_back(String{ (U'A' + i - 1) });
		}

		SimpleTable table{ minColumnWidths, {
			.cellHeight = 36,
			.variableWidth = true,
		} };
		table.push_back_row(columnNames, Array<int32>((CellCountX + 1), 0));
		table.setRowBackgroundColor(0, ColorF{ 0.9 });

		for (int32 i = 1; i < (CellCountY + 1); ++i)
		{
			Array<String> row(CellCountX + 1);
			row[0] = U"{}"_fmt(i);

			Array<int32> rowAlignments((CellCountX + 1), 1);
			rowAlignments[0] = 0;

			table.push_back_row(row, rowAlignments);
			table.setBackgroundColor(i, 0, ColorF{ 0.9 });
		}

		Optional<Point> activeIndex;
		Optional<Point> nextActiveIndex;
		TextEditState textEditState;

		while (System::Update())
		{
			if (nextActiveIndex)
			{
				activeIndex = *nextActiveIndex;
				textEditState = TextEditState{ table.getItem(*activeIndex).text };
				textEditState.cursorPos = textEditState.text.length();
				textEditState.active = true;
				nextActiveIndex.reset();
			}

			{
				constexpr Vec2 TablePos{ 40, 40 };

				if (MouseL.down())
				{
					const auto newActiveIndex = table.cellIndex(TablePos, Cursor::Pos());

					if (newActiveIndex != activeIndex)
					{
						activeIndex = table.cellIndex(TablePos, Cursor::Pos());

						if (activeIndex)
						{
							textEditState = TextEditState{ table.getItem(*activeIndex).text };
							textEditState.cursorPos = textEditState.text.length();
							textEditState.active = true;
							MouseL.clearInput();
						}
					}
				}

				table.draw(TablePos);

				if (activeIndex && ((activeIndex->x != 0) && (activeIndex->y != 0)))
				{
					const RectF cellRegion = table.cellRegion(TablePos, *activeIndex);

					if (SimpleGUI::TextBox(textEditState, cellRegion.pos, cellRegion.w))
					{
						table.setText(*activeIndex, textEditState.text);
					}

					if (textEditState.enterKey)
					{
						nextActiveIndex = Point{ activeIndex->x, (activeIndex->y + 1) };
					}

					if ((1 < activeIndex->y) && KeyUp.down())
					{
						nextActiveIndex = Point{ activeIndex->x, (activeIndex->y - 1) };
					}

					if ((activeIndex->y < CellCountY) && KeyDown.down())
					{
						nextActiveIndex = Point{ activeIndex->x, (activeIndex->y + 1) };
					}

					if ((1 < activeIndex->x) && KeyLeft.down())
					{
						nextActiveIndex = Point{ (activeIndex->x - 1), activeIndex->y };
					}

					if ((activeIndex->x < CellCountX) && KeyRight.down())
					{
						nextActiveIndex = Point{ (activeIndex->x + 1), activeIndex->y };
					}

					if (KeyDelete.down())
					{
						textEditState.clear();
						table.setText(*activeIndex, U"");
					}
				}
			}
		}
	}
	```

## 7. 絵文字付きのボタン

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/ui/7.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// ボタンの背景テクスチャを作成する
	Texture CreateButtonTexture()
	{
		MSRenderTexture renderTexture{ Size{ 160, 60 }, ColorF{ 0.96 } };
		{
			const ScopedRenderTarget2D renderTarget{ renderTexture };

			const ColorF PatternColor{ 0.85 };

			for (int32 x = 0; x <= 8; ++x)
			{
				RectF{ Arg::center((x * 20), 25), 2 }.rotated(45_deg).draw(PatternColor);
				RectF{ Arg::center((10 + x * 20), 30), 3 }.rotated(45_deg).draw(PatternColor);
				RectF{ Arg::center((x * 20), 35), 4 }.rotated(45_deg).draw(PatternColor);
				RectF{ Arg::center((10 + x * 20), 40), 5 }.rotated(45_deg).draw(PatternColor);
				RectF{ Arg::center((x * 20), 45), 6 }.rotated(45_deg).draw(PatternColor);
				RectF{ Arg::center((10 + x * 20), 50), 7 }.rotated(45_deg).draw(PatternColor);
				RectF{ Arg::center((x * 20), 55), 8 }.rotated(45_deg).draw(PatternColor);
				RectF{ Arg::center((10 + x * 20), 60), 9 }.rotated(45_deg).draw(PatternColor);
			}
		}

		// MSRenderTexture の完成には
		// 2D 描画命令の発行 (Flush) + MSAA の解決 (Resolve) が必要
		Graphics2D::Flush();
		renderTexture.resolve();

		// 完成したテクスチャを返す
		return renderTexture;
	}

	class RichButton
	{
	public:

		RichButton() = default;

		explicit RichButton(const Emoji& emoji)
			: m_emoji{ emoji }
			, m_bufferedEmoji{ MakeRoundBuffer(CreateEmojiPolygons(emoji), 5).scaled(EmojiScale) } {}

		void draw(const Rect& rect, const Texture& buttonTexture, const Font& font, const String& text)
		{
			const ColorF PrimaryColor{ 0.3, 0.5, 1.0 };

			const RoundRect roundRect{ rect, 10 };

			const bool mouseOver = roundRect.mouseOver();

			m_transition.update((not roundRect.intersects(Cursor::PreviousPos())) && mouseOver);

			if (mouseOver)
			{
				Cursor::RequestStyle(CursorStyle::Hand);

				roundRect(buttonTexture).draw(MouseL.pressed() ? ColorF{ 0.95 } : ColorF{ 1.05 }).drawFrame(0, 3, PrimaryColor);
			}
			else
			{
				roundRect(buttonTexture).draw().drawFrame(2);
			}

			{
				double angle = Math::Sin(m_transition.value() * 8_pi) * 5_deg * m_transition.value();
				const Vec2 emojiCenter = rect.getRelativePoint(0.5, 0.05);

				{
					const Transformer2D transformer{ Mat3x2::Rotate(angle, emojiCenter) };
					m_bufferedEmoji.draw(emojiCenter, mouseOver ? PrimaryColor : ColorF{ 0.3, 0.25, 0.2 });
					m_emoji.scaled(EmojiScale).rotated(angle).drawAt(emojiCenter);
				}
			}

			font(text).drawAt(TextStyle::Outline(0.0, 0.2, ColorF{ 1.0 }), 26, rect.getRelativePoint(0.5, 0.7), PrimaryColor);
		}

	private:

		Texture m_emoji;

		MultiPolygon m_bufferedEmoji;

		Transition m_transition{ 0.0s, 0.8s };

		static constexpr double EmojiScale = 0.4;

		static MultiPolygon CreateEmojiPolygons(const Emoji& emoji)
		{
			return Image{ emoji }.alphaToPolygonsCentered(160, AllowHoles::No);
		}

		static MultiPolygon MakeRoundBuffer(const MultiPolygon& polygons, int32 distance)
		{
			MultiPolygon result;

			for (const auto& polygon : polygons)
			{
				result = Geometry2D::Or(result, polygon.calculateRoundBuffer(distance));
			}

			return result;
		}
	};

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Texture buttonTexture = CreateButtonTexture();
		const Font font{ FontMethod::MSDF, 48, Typeface::Heavy };

		RichButton button1{ U"🗺"_emoji };
		RichButton button2{ U"🛠"_emoji };
		RichButton button3{ U"✉"_emoji };
		RichButton button4{ U"⚙"_emoji };

		while (System::Update())
		{
			button1.draw(Rect{ 40, 500, 160, 60 }, buttonTexture, font, U"マップ");
			button2.draw(Rect{ 220, 500, 160, 60 }, buttonTexture, font, U"開発");
			button3.draw(Rect{ 400, 500, 160, 60 }, buttonTexture, font, U"お知らせ");
			button4.draw(Rect{ 580, 500, 160, 60 }, buttonTexture, font, U"設定");
		}
	}
	```

## 8. 奥行き型 UI

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/ui/8.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1000, 600);
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
		const Texture compassIcon{ 0xF018B_icon, 90 };
		const Texture swordIcon{ 0xF18BE_icon, 90 };
		const Texture plusIcon{ 0xF0417_icon, 42 };
		const Texture moneyEmoji{ U"💰"_emoji };
		const Texture gemEmoji{ U"💎"_emoji };
		const ColorF PrimaryColor{ 0.98, 0.96, 0.94 };
		const ColorF HoverColor{ 1.0, 0.96, 0.8 };

		// 変換前の四角形
		const Rect BaseRect{ 0, 0, 600, 600 };
		// 変換後の四角形
		const Quad TargetQuad{ 500, 60, 1000, 0, 1000, 600, 480, 520 };
		// ホモグラフィ変換の射影行列を得る
		const Mat3x3 projection = Mat3x3::Homography(Rect{ 600 }.asQuad(), TargetQuad);

		const Rect Button1{ 40, 40, 560, 200 };
		const Rect Button2{ 100, 260, 240, 100 };
		const Rect Button3{ 360, 260, 240, 100 };
		const Rect Button4{ 160, 380, 440, 140 };
		const Rect Button5{ Arg::center(230, 570), 40 };

		// 各ボタンの射影後の四角形
		const Quad Button1Quad = projection.transformRect(Button1);
		const Quad Button2Quad = projection.transformRect(Button2);
		const Quad Button3Quad = projection.transformRect(Button3);
		const Quad Button4Quad = projection.transformRect(Button4);
		const Quad Button5Quad = projection.transformRect(Button5);

		// UI の描画先のレンダーテクスチャ
		MSRenderTexture renderTexture{ BaseRect.size };

		while (System::Update())
		{
			// レンダーテクスチャに UI を描く
			{
				// renderTexture を ColorF{ 1.0, 0.0 } でクリアし,
				// renderTexture をレンダーターゲットにする
				const ScopedRenderTarget2D renderTarget{ renderTexture.clear(ColorF{ 1.0, 0.0 }) };

				// renderTexture のアルファ値がすべて 0 なので、最大のアルファ値を書き込むようなブレンドステートを適用する
				BlendState blend = BlendState::Default2D;
				blend.opAlpha = BlendOp::Max;
				blend.dstAlpha = Blend::DestAlpha;
				blend.srcAlpha = Blend::SrcAlpha;
				const ScopedRenderStates2D renderState{ blend };

				// UI を描画する
				{
					// 探索
					{
						Button1.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
						Button1.draw(Button1Quad.mouseOver() ? HoverColor : PrimaryColor);
						font(U"探索").draw(88, Arg::leftCenter(80, 140), ColorF{ 0.4, 0.3, 0.2 });
						if (Button1Quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}
					}

					// 任務
					{
						Button2.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
						Button2.draw(Button2Quad.mouseOver() ? HoverColor : PrimaryColor);
						font(U"任務").draw(44, Arg::leftCenter(120, 310), ColorF{ 0.4, 0.3, 0.2 });
						compassIcon.drawAt(280, 310, ColorF{ 0.8 });
						if (Button2Quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}
					}

					// 編成
					{
						Button3.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
						Button3.draw(Button3Quad.mouseOver() ? HoverColor : PrimaryColor);
						font(U"編成").draw(44, Arg::leftCenter(380, 310), ColorF{ 0.4, 0.3, 0.2 });
						swordIcon.drawAt(540, 310, ColorF{ 0.8 });
						if (Button3Quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}
					}

					// イベント
					{
						Button4.draw(ColorF{ 0.2, 0.4, 0.6 });
						font(U"イベント").draw(33, Arg::leftCenter(180, 415));
						if (Button4Quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}
					}

					// ジェムとお金
					{
						Rect{ 60, 540, 540, 60 }.draw(ColorF{ 0.0, 0.6 });
						gemEmoji.scaled(0.36).drawAt(120, 570);
						font(U"67").draw(TextStyle::Outline(0.0, 0.2, ColorF{ 0.1 }), 36, Arg::leftCenter(150, 570));

						Circle{ Button5.center(), 20 }.draw(ColorF{ 0.2, 0.8 });
						plusIcon.drawAt(Button5.center(), Button5Quad.mouseOver() ? HoverColor : PrimaryColor);
						if (Button5Quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}

						moneyEmoji.scaled(0.36).drawAt(300, 570);
						font(ThousandsSeparate(12345)).draw(TextStyle::Outline(0.0, 0.2, ColorF{ 0.1 }), 36, Arg::leftCenter(330, 570));
					}
				}

				// MSRenderTexture の完成には
				// 2D 描画命令の発行 (Flush) + MSAA の解決 (Resolve) が必要
				Graphics2D::Flush();
				renderTexture.resolve();
			}

			// 奥行き型の UI を描く
			{
				// 右端に向かって影の効果
				Rect{ 460, 0, 540, 600 }.draw(Arg::left = ColorF{ 0.0, 0.0 }, Arg::right = ColorF{ 0.0, 0.2 });

				// レンダーテクスチャをホモグラフィ変換で射影する
				{
					const ScopedRenderStates2D sampler{ SamplerState::ClampAniso };
					Shader::QuadWarp(TargetQuad, renderTexture);
				}
			}
		}
	}
	```

## 9. 文章中に画像を挿入する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/ui/9.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	struct Item
	{
		Texture icon;

		String name;

		String desc;
	};

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font1{ 30, Typeface::Heavy }, font2{ 15, Typeface::Bold };

		const Array<Texture> emojis = {
			Texture{ U"⚙️"_emoji }, Texture{ U"⚡"_emoji }, Texture{ U"♥"_emoji } };

		const Array<Item> items =
		{
			{ Texture{ U"🏭"_emoji }, U"工場", U"毎ターン 6 $0 を生産する\n電力 3 $1 が必要" },
			{ Texture{ U"🏟"_emoji }, U"スタジアム", U"毎ターン 4 $2 を供給する\n電力 2 $1 が必要" },
			{ Texture{ U"🏖"_emoji }, U"ビーチ", U"毎ターン 2 $2 を供給する\n砂浜にしか建設できない" }
		};

		const RoundRect r0{ 0, 0, 360, 100, 6 };
		const RoundRect r1{ 5, 5, 90, 90, 5 };
		constexpr double EmojiSize = 22;

		while (System::Update())
		{
			for (size_t i = 0; i < items.size(); ++i)
			{
				const auto& item = items[i];

				const Transformer2D t{ Mat3x2::Translate(40, (40 + i * 110.0)) };

				r0.drawShadow(Vec2{ 4, 4 }, 8, 1)
					.draw(ColorF{ 0.2, 0.25, 0.3 })
					.drawFrame(1, 1, ColorF{ 0.4, 0.5, 0.6 });

				r1.draw(ColorF{ 0.85, 0.9, 0.95 });

				item.icon.resized(80).drawAt(r1.center());

				font1(item.name).draw(r1.rect.pos.movedBy(102, 0));

				const Vec2 penPos0 = r1.rect.pos.movedBy(102, 42);

				Vec2 penPos = penPos0;

				bool onTag = false;

				for (const auto& glyph : font2.getGlyphs(item.desc))
				{
					if (onTag)
					{
						emojis[(glyph.codePoint - U'0')].resized(EmojiSize).draw(Arg::leftCenter(penPos.x, penPos.y + font2.height() * 0.5));
						penPos.x += EmojiSize;
						onTag = false;
						continue;
					}

					if (glyph.codePoint == U'$')
					{
						onTag = true;
						continue;
					}

					onTag = false;

					if (glyph.codePoint == U'\n')
					{
						penPos.x = penPos0.x;
						penPos.y += font2.height();
					}
					else
					{
						glyph.texture.draw(Math::Round(penPos + glyph.getOffset()));
						penPos.x += glyph.xAdvance;
					}
				}
			}
		}
	}
	```


## 10. タイル型のボタン

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/ui/10.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	class TileButton
	{
	public:

		struct Palette
		{
			ColorF tileColor1;

			ColorF tileColor2;

			ColorF borderColor1;

			ColorF borderColor2;
		};

		TileButton() = default;

		TileButton(const Icon& icon, int32 iconSize, const Font& font, double fontSize, const String& text, const RectF& rect, const Palette& palette)
			: TileButton{ Texture{ icon, iconSize }, iconSize, font, fontSize, text, rect, palette } {}

		// Texture からアイコンを作成
		TileButton(const TextureRegion& textureRegion, int32 iconSize, const Font& font, double fontSize, const String& text, const RectF& rect, const Palette& palette)
			: m_icon{ textureRegion }
			, m_iconSize{ iconSize }
			, m_font{ font }
			, m_fontSize{ fontSize }
			, m_text{ text }
			, m_rect{ rect }
			, m_palette{ palette } {}

		bool update()
		{
			const bool mouseOver = m_rect.mouseOver();

			bool pushed = false;

			if (mouseOver)
			{
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			if (not m_pressed)
			{
				if (m_rect.leftClicked())
				{
					m_pressed = true;
				}
			}
			else
			{
				if (m_rect.leftReleased())
				{
					m_pressed = false;
					pushed = true;
				}
				else if (not m_rect.mouseOver())
				{
					m_pressed = false;
				}
			}

			m_transitionPressed.update(m_pressed);

			return pushed;
		}

		void draw() const
		{
			const double t = m_transitionPressed.value();

			const Transformer2D transform{ Mat3x2::Scale((1 + t * 0.06), m_rect.center()) };

			// タイル
			{
				m_rect.draw(m_palette.tileColor1.lerp(m_palette.tileColor2, t));

				m_rect.stretched(Math::Lerp(-InnerBorderMargin, 0, t))
					.drawFrame(0.1, (1.0 + t * 2.0), m_palette.borderColor1.lerp(m_palette.borderColor2, t));
			}

			// アイコン
			{
				m_icon
					.drawAt(m_rect.getRelativePoint(0.5, 0.4), m_palette.tileColor2.lerp(m_palette.tileColor1, t));
			}

			// ラベル
			{
				m_font(m_text)
					.drawAt(m_fontSize, m_rect.getRelativePoint(0.5, 0.8), m_palette.tileColor2.lerp(m_palette.tileColor1, t));
			}
		}

	private:

		static constexpr double InnerBorderMargin = 3.0;

		TextureRegion m_icon;

		int32 m_iconSize = 0;

		Font m_font;

		double m_fontSize = 0;

		String m_text;

		RectF m_rect;

		Transition m_transitionPressed{ 0.09s, 0.12s };

		Palette m_palette;

		bool m_pressed = false;
	};

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.3 });

		const Font font1{ FontMethod::MSDF, 48, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };
		const Font font2{ FontMethod::MSDF, 48, Typeface::Heavy };
		constexpr int32 IconSize1 = 65;
		constexpr int32 IconSize2 = 40;
		constexpr int32 IconSize3 = 60;
		constexpr double FontSize1 = 22;
		constexpr double FontSize2 = 15.5;
		constexpr double FontSize3 = 24;
		constexpr TileButton::Palette Palette1{
			.tileColor1 = ColorF{ 0.3, 0.2, 0.0 },
			.tileColor2 = ColorF{ 1.0, 0.95, 0.75 },
			.borderColor1 = ColorF{ 1.0, 0.4 },
			.borderColor2 = ColorF{ 1.0, 0.8, 0.4 }
		};

		Array<TileButton> buttons = {
			{ 0xF034D_icon, IconSize1, font1, FontSize1, U"マップ", Rect{40, 40, 130}, Palette1 },
			{ 0xF018B_icon, IconSize1, font1, FontSize1, U"イベント", Rect{180, 40, 130}, Palette1 },
			{ 0xF0E10_icon, IconSize1, font1, FontSize1, U"バッグ", Rect{ 320, 40, 130 }, Palette1 },
			{ 0xF05DA_icon, IconSize1, font1, FontSize1, U"冒険の記録", Rect{ 460, 40, 130 }, Palette1 },
			{ 0xF0538_icon, IconSize1, font1, FontSize1, U"実績", Rect{ 600, 40, 130 }, Palette1 },
			{ 0xF0493_icon, IconSize1, font1, FontSize1, U"設定", Rect{ 740, 40, 130 }, Palette1 },

			{ 0xF034D_icon, IconSize2, font1, FontSize2, U"マップ", Rect{ 40, 200, 90 }, Palette1 },
			{ 0xF018B_icon, IconSize2, font1, FontSize2, U"イベント", Rect{ 140, 200, 90 }, Palette1 },
			{ 0xF0E10_icon, IconSize2, font1, FontSize2, U"バッグ", Rect{ 240, 200, 90 }, Palette1 },
			{ 0xF05DA_icon, IconSize2, font1, FontSize2, U"冒険の記録", Rect{ 340, 200, 90 }, Palette1 },
			{ 0xF0538_icon, IconSize2, font1, FontSize2, U"実績", Rect{ 440, 200, 90 }, Palette1 },
			{ 0xF0493_icon, IconSize2, font1, FontSize2, U"設定", Rect{ 540, 200, 90 }, Palette1 },

			{ 0xF0A70_icon, IconSize3, font2, FontSize3, U"メニュー", Rect{ 40, 360, 150, 120 }, { HSV{ 25, 1, 0.8 }, Palette::White, ColorF{ 1.0, 0.4 }, HSV{ 25, 0.5, 1 } } },
			{ 0xF0AAF_icon, IconSize3, font2, FontSize3, U"具材", Rect{ 200, 360, 150, 120 }, { HSV{ 75, 1, 0.8 }, Palette::White, ColorF{ 1.0, 0.4 }, HSV{ 75, 0.5, 1 } } },
			{ 0xF110E_icon, IconSize3, font2, FontSize3, U"調味料", Rect{ 360, 360, 150, 120 }, { HSV{ 125, 1, 0.8 }, Palette::White, ColorF{ 1.0, 0.4 }, HSV{ 125, 0.5, 1 } } },
			{ 0xF0110_icon, IconSize3, font2, FontSize3, U"仕入れ", Rect{ 520, 360, 150, 120 }, { HSV{ 175, 1, 0.8 }, Palette::White, ColorF{ 1.0, 0.4 }, HSV{ 175, 0.5, 1 } } },
			{ 0xF04DE_icon, IconSize3, font2, FontSize3, U"設備", Rect{ 680, 360, 150, 120 }, { HSV{ 225, 1, 0.8 }, Palette::White, ColorF{ 1.0, 0.4 }, HSV{ 225, 0.5, 1 } } },
			{ 0xF00E6_icon, IconSize3, font2, FontSize3, U"宣伝", Rect{ 840, 360, 150, 120 }, { HSV{ 275, 1, 0.8 }, Palette::White, ColorF{ 1.0, 0.4 }, HSV{ 275, 0.5, 1 } } },
			{ 0xF012A_icon, IconSize3, font2, FontSize3, U"売り上げ", Rect{ 1000, 360, 150, 120 }, { HSV{ 325, 1, 0.8 }, Palette::White, ColorF{ 1.0, 0.4 }, HSV{ 325, 0.5, 1 } } },
		};

		while (System::Update())
		{
			Rect{ 0, 0, Scene::Width(), 320 }.draw(ColorF{ 0.8, 0.7, 0.6 });

			for (auto& button : buttons)
			{
				button.update();
			}

			for (const auto& button : buttons)
			{
				button.draw();
			}
		}
	}
	```

## 11. ゲームに便利なアイコン集

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/ui/11.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

		const Font iconFont{ FontMethod::MSDF, 40, Typeface::Icon_MaterialDesign };
		const Texture faceEmoji{ U"🐱"_emoji };

		double volume = 1.0;
		int32 up = 0, down = 0;

		while (System::Update())
		{
			SimpleGUI::Button(U"\U000F0009 アカウント", Vec2{ 20, 20 }, 220);
			SimpleGUI::Button(U"\U000F01F0 お知らせ", Vec2{ 20, 60 }, 220);
			SimpleGUI::Button(U"\U000F01DA ダウンロード", Vec2{ 20, 100 }, 220);
			SimpleGUI::Button(U"\U000F01A5 最高記録", Vec2{ 20, 140 }, 220);
			SimpleGUI::Button(U"\U000F0193 保存", Vec2{ 20, 180 }, 220);
			SimpleGUI::Button(U"\U000F1268 Copy to clipboard", Vec2{ 20, 220 }, 220);
			SimpleGUI::Button(U"\U000F0189 メッセージ", Vec2{ 20, 260 }, 220);
			SimpleGUI::Button(U"\U000F0493 設定", Vec2{ 20, 300 }, 220);
			SimpleGUI::Button(U"\U000F1398 中断する", Vec2{ 20, 340 }, 220);
			SimpleGUI::Button(U"\U000F0E1E OK", Vec2{ 20, 380 }, 220);
			SimpleGUI::Button(U"\U000F0639 カードを配る", Vec2{ 20, 420 }, 220);
			SimpleGUI::Button(U"\U000F0240 領土を広げる", Vec2{ 20, 460 }, 220);
			SimpleGUI::Button(U"\U000F02A1 プレゼントする", Vec2{ 20, 500 }, 220);
			SimpleGUI::Button(U"\U000F02DA 履歴", Vec2{ 20, 540 }, 220);

			// 音量調整
			SimpleGUI::Slider((0.5 < volume) ? U"\U000F057E"
				: (0.0 < volume) ? U"\U000F0580" : U"\U000F0581", volume, Vec2{ 260, 20 }, 30, 170);

			// Undo / Redo
			SimpleGUI::Button(U"\U000F054C", Vec2{ 260, 60 }, 40);
			SimpleGUI::Button(U"\U000F044E", Vec2{ 310, 60 }, 40);

			// upvote
			if (SimpleGUI::Button(U"\U000F0513  {}"_fmt(up), Vec2{ 260, 100 }, 90))
			{
				++up;
			}

			// downvote
			if (SimpleGUI::Button(U"\U000F0511  {}"_fmt(down), Vec2{ 370, 100 }, 90))
			{
				++down;
			}

			SimpleGUI::Button(U"公式サイト \U000F0327", Vec2{ 260, 140 }, 200);
			SimpleGUI::Button(U"\U000F0544 公式 Twitter", Vec2{ 260, 180 }, 200);
			SimpleGUI::Button(U"\U000F018C 任務一覧", Vec2{ 260, 220 }, 200);
			SimpleGUI::Button(U"\U000F0982 マップ", Vec2{ 260, 260 }, 200);
			SimpleGUI::Button(U"\U000F034E 現在地", Vec2{ 260, 300 }, 200);
			SimpleGUI::Button(U"\U000F0A7A 削除", Vec2{ 260, 340 }, 200);
			SimpleGUI::Button(U"\U000F05B7 修繕", Vec2{ 260, 380 }, 200);
			SimpleGUI::Button(U"\U000F0349 検索", Vec2{ 260, 420 }, 200);
			SimpleGUI::Button(U"\U000F0432 QR 作成", Vec2{ 260, 460 }, 200);
			SimpleGUI::Button(U"\U000F0433 QR 読み込み", Vec2{ 260, 500 }, 200);
			SimpleGUI::Button(U"\U000F04E6 同期", Vec2{ 260, 540 }, 200);

			// ハート
			iconFont(U"\U000F02D1\U000F02D1\U000F02D1\U000F06DF").draw(500, 20, ColorF{ 0.8, 0.2, 0.2 });

			// サイコロ
			iconFont(U"\U000F037D\U000F030C\U000F0297").draw(500, 80, ColorF{ 0.25 });

			// 操作方法
			iconFont(U"\U000F114A\U000F114B\U000F114C\U000F114D\U000F114E\U000F114F").draw(500, 140, ColorF{ 0.25 });

			// 動画の再生
			Rect{ 500, 200, 240, 160 }.draw(ColorF{ 0.6 });
			iconFont(U"\U000F040C").drawAt(80, 620, 280, ColorF{ 1.0 });

			// セリフアイコン
			faceEmoji.scaled(0.75).drawAt(560, 440);
			iconFont(U"\U000F1170").drawAt(50, 630, 400, ColorF{ 0.1 });

			// 拡大縮小
			Circle{ 540, 530, 30 }.draw();
			iconFont(U"\U000F06EC").drawAt(50, 540, 530, ColorF{ 0.1 });
			Circle{ 620, 530, 30 }.draw();
			iconFont(U"\U000F06ED").drawAt(50, 620, 530, ColorF{ 0.1 });
		}
	}
	```

## 12. タブ

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/TabSample/Screenshot/2.png)

[Siv3D-Sample | タブ :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/blob/main/Samples/TabSample){:target="_blank" .md-button}


## 13. HP バー

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/HPBar/Screenshot/2.png)

[Siv3D-Sample | HP バー :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/blob/main/Samples/HPBar){:target="_blank" .md-button}


## 14. パイメニュー

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/ui/14.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	/// @brief パイメニュー用のアイコン
	class PieMenuIcon
	{
	public:

		PieMenuIcon() = default;

		/// @brief パイメニュー用のアイコンを作成します。
		/// @param texture アイコンのテクスチャ
		/// @param color アイコンの描画色
		PieMenuIcon(const Texture& texture, const ColorF& color)
			: m_texture{ texture }
			, m_blurTexture{ m_texture.size(), ColorF{ 0.0, 1.0 } }
			, m_color{ color }
		{
			RenderTexture m_internalTexture{ m_texture.size() };

			// アイコンをやや小さめに描画する
			{
				const ScopedRenderTarget2D target{ m_blurTexture };
				m_texture.scaled(0.8).drawAt(m_blurTexture.size() * 0.5);
			}

			// ガウスぼかしを 2 回かける
			Shader::GaussianBlur(m_blurTexture, m_internalTexture, m_blurTexture);
			Shader::GaussianBlur(m_blurTexture, m_internalTexture, m_blurTexture);
		}

		void draw() const
		{
			// 背景のぼかした影を減算ブレンディングで描画する
			{
				const ScopedRenderStates2D blend{ BlendState::Subtractive };
				m_blurTexture.scaled(1.35).drawAt(Vec2{ 0, 0 }, ColorF{ 0.25 });
			}
			m_texture.drawAt(Vec2{ 0, 0 }, m_color);
		}

	private:

		Texture m_texture;

		RenderTexture m_blurTexture;

		ColorF m_color;
	};

	/// @brief パイメニュークラス
	class PieMenu
	{
	public:

		/// @brief パイメニューのスタイル
		struct Style
		{
			/// @brief パイメニューの外側の半径
			double outerRadius = 180.0;

			/// @brief パイメニューの内側の半径
			double innerRadius = 90.0;

			/// @brief アクティブなアイテムが外側に移動する距離
			double pieOuterOffset = 10.0;

			/// @brief アクティブなアイテムの枠の太さ
			double outlineThickness = 8.0;

			/// @brief パイメニューの色
			ColorF pieColor{ 0.0, 0.75 };

			/// @brief パイメニューの内側の枠の色
			ColorF pieInnerFrameColor{ 0.6 };

			/// @brief 無効なアイテムの色
			ColorF disabledPieColor{ 0.36, 0.4 };

			/// @brief アクティブなアイテムの色
			ColorF activePieColor{ 0.36, 0.84, 1.0 };

			/// @brief パイメニューの外側の枠の色
			ColorF activePieOutlineColor{ 1.0, 0.9, 0.2 };

			/// @brief アイテムを指す矢印の色
			ColorF arrowColor{ 1.0, 0.9, 0.2 };

			[[nodiscard]]
			static constexpr Style Default() noexcept
			{
				return{};
			}
		};

		SIV3D_NODISCARD_CXX20
		PieMenu() = default;

		/// @brief パイメニューを作成します。
		/// @param icons パイメニューのアイコン
		/// @param center パイメニューの中心座標
		/// @param style パイメニューのスタイル
		SIV3D_NODISCARD_CXX20
		PieMenu(const Array<PieMenuIcon>& icons, const Vec2& center, const Style& style = Style::Default())
			: m_itemCount{ static_cast<int32>(icons.size()) }
			, m_pieAngle{ Math::TwoPi / m_itemCount }
			, m_style{ style }
			, m_center{ center }
			, m_transitions{ icons.size(), Transition{ 0.1s, 0.2s } }
			, m_icons{ icons }
			, m_enabled(m_itemCount, true)
		{
			const Circle circle{ m_style.outerRadius - m_style.pieOuterOffset };
			const double activePieOffset = 0;
			const double outlineThicknessHalf = (m_style.outlineThickness * 0.5);

			{
				m_defaultPolygon = Polygon::CorrectOne(circle.arcAsPolygon((-m_pieAngle / 2), m_pieAngle, (m_style.outerRadius - m_style.innerRadius), 0.0).outer());
			}

			{
				m_innerOutline = m_defaultPolygon.calculateBuffer(-3).outer();
			}

			{
				const Array<Vec2> outline = circle.stretched(activePieOffset + outlineThicknessHalf)
					.arcAsPolygon((-m_pieAngle / 2), m_pieAngle, (m_style.outerRadius + activePieOffset - m_style.innerRadius + m_style.outlineThickness), 0.0).outer();

				// m_outlinePolygon の生成に失敗する確率を下げるためのおまじない
				{
					m_outlinePolygon = LineString{ outline.rotated(2) }.densified(4.0).calculateRoundBufferClosed(outlineThicknessHalf);

					if ((not m_outlinePolygon) || (not m_outlinePolygon.hasHoles()))
					{
						m_outlinePolygon = LineString{ outline.rotated(1) }.calculateRoundBufferClosed(outlineThicknessHalf);
					}
				}
			}
		}

		/// @brief パイメニューのアイテム数を返します。
		/// @return パイメニューのアイテム数
		[[nodiscard]]
		size_t size() const noexcept
		{
			return m_itemCount;
		}

		/// @brief パイメニューの指定したアイテムの有効・無効を設定します。
		/// @param index アイテムのインデックス
		/// @param enabled 有効にする場合 true, 無効にする場合は false
		/// @return *this
		PieMenu& setEnabled(size_t index, bool enabled) noexcept
		{
			m_enabled[index] = enabled;
			return *this;
		}

		/// @brief パイメニューの指定したアイテムが有効かを返します。
		/// @param index アイテムのインデックス
		/// @return アイテムが有効な場合 true, 無効な場合は false
		[[nodiscard]]
		bool getEnabled(size_t index) const noexcept
		{
			return m_enabled[index];
		}

		/// @brief パイメニューのアニメーションをリセットします。
		void reset() noexcept
		{
			m_selectedPie.reset();
			m_transitions.fill(Transition{ 0.1s, 0.2s });
		}

		/// @brief パイメニューを更新します。
		/// @return 選択されているアイテムのインデックス。選択されていない場合は none
		Optional<int32> update()
		{
			m_selectedPie.reset();

			const Vec2 cursorVector = (Cursor::PosF() - m_center).limitLengthSelf(Math::Lerp(m_style.innerRadius, m_style.outerRadius, 0.55));

			if (m_style.innerRadius <= cursorVector.length())
			{
				m_selectedPie = (static_cast<int32>((cursorVector.getAngle() + Math::TwoPi + (m_pieAngle / 2)) / m_pieAngle) % m_itemCount);
			}

			if (m_selectedPie && (not m_enabled[*m_selectedPie]))
			{
				m_selectedPie.reset();
			}

			for (int32 i = 0; i < m_itemCount; ++i)
			{
				const bool hovered = (i == m_selectedPie);
				m_transitions[i].update(hovered);
			}

			return m_selectedPie;
		}

		/// @brief パイメニューを描画します。
		void draw() const
		{
			for (int32 i = 0; i < m_itemCount; ++i)
			{
				const double centerAngle = (i * m_pieAngle);
				const bool hovered = (i == m_selectedPie);
				const Vec2 offset = Circular{ (m_style.pieOuterOffset + m_style.outlineThickness * m_transitions[i].value()), centerAngle };
				const Vec2 scalingCenter = Circular{ ((m_style.innerRadius + m_style.outerRadius) / 2), centerAngle };

				if (m_enabled[i])
				{
					{
						const Transformer2D transformer{ Mat3x2::Rotate(centerAngle).translated(m_center + offset) };
						m_defaultPolygon.draw(hovered ? m_style.activePieColor : m_style.pieColor);

						if (hovered)
						{
							m_outlinePolygon.draw(m_style.activePieOutlineColor);
						}
						else
						{
							m_innerOutline.drawClosed(m_style.pieInnerFrameColor);
						}
					}

					{
						const Vec2 iconOffset = Circular{ Math::Lerp(m_style.innerRadius, m_style.outerRadius, 0.53) + (m_style.outlineThickness * m_transitions[i].value()), centerAngle };
						const Transformer2D transformer{ Mat3x2::Translate(m_center + iconOffset) };
						m_icons[i].draw();
					}
				}
				else
				{
					const Transformer2D transformer{ Mat3x2::Rotate(centerAngle).translated(m_center + offset) };
					m_defaultPolygon.draw(m_style.disabledPieColor);

					if (hovered)
					{
						m_outlinePolygon.draw(m_style.activePieOutlineColor);
					}
				}
			}

			{
				const Vec2 cursorVector = (Cursor::PosF() - m_center).limitLengthSelf(Math::Lerp(m_style.innerRadius, m_style.outerRadius, 0.55));
				const double lineLength = Max(0.0, cursorVector.length() - (m_selectedPie ? 14 : 18));
				Line{ m_center, (m_center + cursorVector.withLength(lineLength)) }.draw(4, m_style.arrowColor);
				const Vec2 triangleCenter = (m_center + cursorVector);

				if (m_selectedPie)
				{
					Triangle{ triangleCenter, 12, cursorVector.getAngle() }.draw(m_style.arrowColor);
					Triangle{ triangleCenter, 26, cursorVector.getAngle() }.drawFrame(4, m_style.arrowColor);
				}
				else
				{
					Circle{ triangleCenter, 7 }.draw(m_style.arrowColor);
					Circle{ triangleCenter, 12 }.drawFrame(4, m_style.arrowColor);
				}
			}
		}

	private:

		int32 m_itemCount = 0;

		double m_pieAngle = 0.0;

		Style m_style;

		Vec2 m_center{ 0, 0 };

		Polygon m_defaultPolygon;

		LineString m_innerOutline;

		Polygon m_outlinePolygon;

		Array<Transition> m_transitions;

		Array<PieMenuIcon> m_icons;

		Array<bool> m_enabled;

		Optional<int32> m_selectedPie;
	};

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.8 });

		const Array<PieMenuIcon> icons =
		{
			PieMenuIcon{ Texture{ 0xF064C_icon, 60 }, ColorF{ 0.86, 0.98, 0.80 }},
			PieMenuIcon{ Texture{ 0xF0100_icon, 60 }, ColorF{ 0.60, 0.98, 0.60 }},
			PieMenuIcon{ Texture{ 0xF0E46_icon, 60 }, ColorF{ 1.00, 0.50, 0.31 }},
			PieMenuIcon{ Texture{ 0xF194B_icon, 60 }, ColorF{ 0.73, 0.33, 0.83 }},
			PieMenuIcon{ Texture{ 0xF18BF_icon, 60 }, ColorF{ 0.50, 1.00, 0.83 }},
			PieMenuIcon{ Texture{ 0xF0BEB_icon, 60 }, ColorF{ 1.0 }},
			PieMenuIcon{ Texture{ 0xF11DE_icon, 60 }, ColorF{ 1.00, 0.65, 0.00 }},
			PieMenuIcon{ Texture{ 0xF018C_icon, 60 }, ColorF{ 0.68, 1.00, 0.18 }},
		};

		std::unique_ptr<PieMenu> pieMenu;

		constexpr int32 CellSize = 20;

		while (System::Update())
		{
			// 右クリックされたらパイメニューを登場させる
			if (MouseR.down())
			{
				pieMenu = std::make_unique<PieMenu>(icons, Cursor::PosF());

				// 1 番目と 3 番目のアイテムを無効化する
				pieMenu->setEnabled(1, false).setEnabled(3, false);
			}

			// 背景の市松模様を描画する
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

			// パイメニューがあれば
			if (pieMenu)
			{
				const Optional<int32> selected = pieMenu->update();

				pieMenu->draw();

				// 右クリックが離されたら、選択されたアイテムを表示する
				if (MouseR.up())
				{
					ClearPrint();
					Print << selected;
					pieMenu.reset();
				}
			}
		}
	}
	```


## 15. 数値パッド

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/Numpad/Screenshot/1.png)

[Siv3D-Sample | 数値パッド :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/blob/main/Samples/Numpad){:target="_blank" .md-button}

