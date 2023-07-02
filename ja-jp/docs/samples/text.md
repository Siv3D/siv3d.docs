# 文字表示のサンプル

## 1. テキストの登場

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/text/1.gif)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// Glyph とエフェクトの関数を組み合わせてテキストを描画
	void DrawText(const Font& font, double fontSize, const String& text, const Vec2& pos, const ColorF& color, double t,
		void f(const Vec2&, double, const Glyph&, const ColorF&, double), double characterPerSec)
	{
		const double scale = (fontSize / font.fontSize());
		Vec2 penPos = pos;
		const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method()) };

		for (auto&& [i, glyph] : Indexed(font.getGlyphs(text)))
		{
			if (glyph.codePoint == U'\n')
			{
				penPos.x = pos.x;
				penPos.y += (font.height() * scale);
				continue;
			}

			const double targetTime = (i * characterPerSec);

			if (t < targetTime)
			{
				break;
			}

			f(penPos, scale, glyph, color, (t - targetTime));

			penPos.x += (glyph.xAdvance * scale);
		}
	}

	// 文字が上からゆっくり降ってくる表現
	void TextEffect1(const Vec2& penPos, double scale, const Glyph& glyph, const ColorF& color, double t)
	{
		const double y = EaseInQuad(Saturate(1 - t / 0.3)) * -20.0;
		const double a = Min(t / 0.3, 1.0);
		glyph.texture.scaled(scale).draw(penPos + glyph.getOffset(scale) + Vec2{ 0, y }, ColorF{color, a});
	}

	// 文字が勢いよく現れる表現
	void TextEffect2(const Vec2& penPos, double scale, const Glyph& glyph, const ColorF& color, double t)
	{
		const double s = Min(t / 0.1, 1.0);
		const double a = Min(t / 0.2, 1.0);
		glyph.texture.scaled(scale * (3.0 - s * 2)).draw(penPos + glyph.getOffset(scale), ColorF{ color, a });
	}

	// 落ちてきた文字がしばらく揺れる表現
	void TextEffect3(const Vec2& penPos, double scale, const Glyph& glyph, const ColorF& color, double t)
	{
		const double angle = Sin(t * 1440_deg) * 25_deg * Saturate(1.0 - t / 0.6);
		const double y = Saturate(1 - t / 0.05) * -20.0;
		glyph.texture.scaled(scale).rotated(angle).draw(penPos + glyph.getOffset(scale) + Vec2{ 0, y }, color);
	}

	void Main()
	{
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
		const String text = U"Lorem ipsum dolor sit amet, consectetur\n"
			U"adipiscing elit, sed do eiusmod tempor\n"
			U"incididunt ut labore et dolore magna aliqua.";

		Stopwatch stopwatch{ StartImmediately::Yes };

		while (System::Update())
		{
			if (SimpleGUI::Button(U"Restart", Vec2{ 620, 520 }))
			{
				stopwatch.restart();
			}

			const double t = stopwatch.sF();
			DrawText(font, 30, text, Vec2{ 40, 40 }, Palette::Skyblue, t, TextEffect1, 0.1);
			DrawText(font, 30, text, Vec2{ 40, 200 }, Palette::Orange, t, TextEffect2, 0.1);
			DrawText(font, 30, text, Vec2{ 40, 360 }, Palette::Seagreen, t, TextEffect3, 0.1);
		}
	}
	```


## 2. ランダムなテキストから目的のテキストに変化する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/text/2.gif)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		const Font font{ FontMethod::MSDF, 48 };
		const Array<char32> chars = Range(U'A', U'Z').asArray().append(Range(U'a', U'z'));
		const String targetText = U"C++/OpenSiv3D";

		Array<int32> delays = targetText.map([](auto) { return Random(20, 60); });
		String randomText = targetText;

		constexpr double Scale = 1.5;
		constexpr double displayTime = 0.05;
		double accumulatedTime = 0.0;

		while (System::Update())
		{
			accumulatedTime += Scene::DeltaTime();

			if (MouseL.down())
			{
				delays = targetText.map([](auto) { return Random(20, 60); });
			}

			if (displayTime <= accumulatedTime)
			{
				accumulatedTime -= displayTime;

				for (size_t i = 0; i < targetText.size(); ++i)
				{
					if (delays[i])
					{
						randomText[i] = chars.choice();
						--delays[i];
					}
					else
					{
						randomText[i] = targetText[i];
					}
				}
			}

			{
				const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method()) };
				Vec2 penPos{ 50, 240 };

				for (auto&& [i, glyph] : Indexed(font.getGlyphs(targetText)))
				{
					const auto glyph2 = font.getGlyph(randomText[i]);

					glyph2.texture.scaled(Scale).draw(penPos + glyph2.getOffset(Scale));

					penPos.x += (Scale * glyph.xAdvance * 1.2);
				}
			}
		}
	}
	```


## 3. ルビを振る

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/text/3.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// ルビの情報
	struct Ruby
	{
		// ルビを振る文字列の先頭の文字のインデックス
		int32 indexBegin;

		// ルビを振る文字列の末尾の文字のインデックス
		int32 indexEnd;

		// ルビの文字列
		String text;
	};

	void DrawTextWithRuby(const Vec2& basePos, const Font& font, const String& text, const Array<Ruby>& rubyList, double mainFontSize, double rubyFontSize, double rubyYOffset)
	{
		const double mainFontScale = (mainFontSize / font.fontSize());

		const Array<double> xAdvances = font(text).getXAdvances();

		Array<Vec2> allPenPos;

		{
			const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method()) };
			Vec2 penPos{ basePos };

			// 文字単位で描画を制御するためのループ
			for (const auto& glyph : font.getGlyphs(text))
			{
				allPenPos << penPos;

				// 改行文字なら
				if (glyph.codePoint == U'\n')
				{
					// ペンの X 座標をリセット
					penPos.x = basePos.x;

					// ペンの Y 座標をフォントの高さ分進める
					penPos.y += (font.height() * mainFontScale);

					continue;
				}

				// 文字のテクスチャをペンの位置に文字ごとのオフセットを加算して描画
				// FontMethod がビットマップ方式の場合に限り、Math::Round() で整数座標にすると品質が向上
				glyph.texture.scaled(mainFontScale).draw(penPos + glyph.getOffset(mainFontScale), ColorF{ 0.11 });

				// ペンの X 座標を文字の幅の分進める
				penPos.x += (glyph.xAdvance * mainFontScale);
			}
		}

		for (const auto& ruby : rubyList)
		{
			const Vec2 beginPenPos = (allPenPos[ruby.indexBegin] + Vec2{ 0, rubyYOffset });

			const Vec2 endPenPos = (allPenPos[ruby.indexEnd] + Vec2{ (xAdvances[ruby.indexEnd] * mainFontScale), rubyYOffset });

			const Vec2 center = ((beginPenPos + endPenPos) / 2);

			Line{ beginPenPos, endPenPos }.draw(2, Palette::Orange);

			font(ruby.text).draw(rubyFontSize, Arg::bottomCenter = center, ColorF{ 0.11 });
		}
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.99 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		constexpr double RubyYOffset = 8;

		const String text = U"吾輩は猫である。名前はまだ無い。";
		const Array<Ruby> rubyList{
			{ 0, 1, U"わがはい" },
			{ 3, 3, U"ねこ" },
			{ 8, 9, U"なまえ" },
			{ 13, 13, U"な" },
		};

		while (System::Update())
		{
			DrawTextWithRuby(Vec2{ 60, 60 }, font, text, rubyList, 36, 16, RubyYOffset);
		}
	}
	```


## 4. 文字と図形の交差判定

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/text/4.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		const Font font{ 100, Typeface::Bold };

		PolygonGlyph polygonGlyph = font.renderPolygon(U'%');

		polygonGlyph.polygons = polygonGlyph.polygons.scaled(5);

		while (System::Update())
		{
			Circle circle{ Cursor::Pos(), 20 };

			for (const auto& polygon : polygonGlyph.polygons)
			{
				if (polygon.intersects(circle))
				{
					polygon.draw(Palette::Yellow);
				}
				else
				{
					polygon.draw();
				}
			}

			circle.draw(Palette::Orange);
		}
	}
	```


## 5. OutlineGlyph の応用

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/text/5.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.99, 0.96, 0.93 });

		const Font font{ 130, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };
		const Array<OutlineGlyph> glyphs = font.renderOutlines(U"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz1234567890!?");

		while (System::Update())
		{
			const double t = Periodic::Sawtooth0_1(2.6s);
			const double len = Periodic::Sine0_1(16s) * 0.5;
			constexpr Vec2 BasePos{ 70, 0 };
			Vec2 penPos{ BasePos };

			for (const auto& glyph : glyphs)
			{
				const Transformer2D transform{ Mat3x2::Translate(penPos + glyph.getOffset()) };

				for (const auto& ring : glyph.rings)
				{
					const double length = ring.calculateLength(CloseRing::Yes);
					LineString z1 = ring.extractLineString(t * length, length * len, CloseRing::Yes);
					const LineString z2 = ring.extractLineString((t + 0.5) * length, length * len, CloseRing::Yes);
					z1.append(z2.reversed()).drawClosed(3, ColorF{ 0.25 });
				}

				if (penPos.x += glyph.xAdvance;
					1120 < penPos.x)
				{
					penPos.x = BasePos.x;
					penPos.y += font.fontSize();
				}
			}
		}
	}
	```


## 6. グラデーションのあるテキスト

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/text/6.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void DrawGradientText(const Font& font, const String& text, const Vec2& basePos, const ColorF& topColor, const ColorF& bottomColor)
	{
		Vec2 penPos{ basePos };

		// 文字単位で描画を制御するためのループ
		for (const auto& glyph : font.getGlyphs(text))
		{
			if (glyph.codePoint == U'\n')
			{
				penPos.x = basePos.x;
				penPos.y += font.height();
				continue;
			}

			const Vec2 offset = glyph.getOffset();
			const double topPos = offset.y;
			const double bottomPos = (offset.y + glyph.texture.size.y);

			const double topT = (topPos / font.height());
			const double bottomT = (bottomPos / font.height());

			// グラデーションの色
			const ColorF c1 = topColor.lerp(bottomColor, topT);
			const ColorF c2 = topColor.lerp(bottomColor, bottomT);

			// 文字のテクスチャを描画
			glyph.texture
				.draw(penPos + offset, Arg::top = c1, Arg::bottom = c2);

			penPos.x += glyph.xAdvance;
		}
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.99 });
		const Font font{ 60, Typeface::Heavy };

		HSV topColor{ 180, 0.6, 1 };
		HSV bottomColor{ 240, 0.8, 0.8 };

		while (System::Update())
		{
			const String text = U"OpenSiv3D\nABCDEFG\n1234567\nあいうえお\n{}"_fmt(Cursor::Pos());

			DrawGradientText(font, text,
				Vec2{ 40, 40 }, topColor, bottomColor);

			SimpleGUI::ColorPicker(topColor, Vec2{ 560, 40 });
			SimpleGUI::ColorPicker(bottomColor, Vec2{ 560, 180 });
		}
	}
	```


## 7. 光彩のあるテキスト

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/text/7.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// 光彩用の RenderTexture 管理クラス
	class GlowText
	{
	private:

		Size m_baseSize{ 0, 0 };
		mutable RenderTexture m_gaussianA1, m_gaussianB1;
		mutable RenderTexture m_gaussianA4, m_gaussianB4;
		mutable RenderTexture m_gaussianA8, m_gaussianB8;

	public:

		GlowText() = default;

		GlowText(int32 width, int32 height)
			: GlowText{ Size{ width, height } } {}

		explicit GlowText(const Size& size)
			: m_baseSize{ size }
			, m_gaussianA1{ size }, m_gaussianB1{ size }
			, m_gaussianA4{ size / 4 }, m_gaussianB4{ size / 4 }
			, m_gaussianA8{ size / 8 }, m_gaussianB8{ size / 8 } {}

		void renderGlow(const Font& font, double size, const String& text, const Vec2& pos) const
		{
			{
				const ScopedRenderTarget2D target{ m_gaussianA1.clear(ColorF{ 0.0 }) };
				font(text).draw(size, pos);
			}
			// オリジナルサイズのガウスぼかし (A1)
			// A1 を 1/4 サイズにしてガウスぼかし (A4)
			// A4 を 1/2 サイズにしてガウスぼかし (A8)
			Shader::GaussianBlur(m_gaussianA1, m_gaussianB1, m_gaussianA1);
			Shader::Downsample(m_gaussianA1, m_gaussianA4);
			Shader::GaussianBlur(m_gaussianA4, m_gaussianB4, m_gaussianA4);
			Shader::Downsample(m_gaussianA4, m_gaussianA8);
			Shader::GaussianBlur(m_gaussianA8, m_gaussianB8, m_gaussianA8);
		}

		void draw(const Vec2& pos, const ColorF& glowColor, double a1, double a4, double a8, bool subtractive = false) const
		{
			const ScopedRenderStates2D blend{ subtractive ? BlendState::Subtractive : BlendState::Additive };

			if (a1)
			{
				m_gaussianA1.resized(m_baseSize)
					.draw(pos, ColorF{ glowColor, a1 });
			}

			if (a4)
			{
				m_gaussianA4.resized(m_baseSize)
					.draw(pos, ColorF{ glowColor, a4 });
			}

			if (a8)
			{
				m_gaussianA8.resized(m_baseSize)
					.draw(pos, ColorF{ glowColor, a8 });
			}
		}
	};

	void Main()
	{
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		GlowText glowText{ 800, 600 }; // 必要最小限のサイズにすると実行時性能向上
		double a8 = 0.6, a4 = 0.45, a1 = 0.2;
		HSV backgroundColor = ColorF{ 0.1, 0.2, 0.3 };
		HSV textColor = Palette::White;
		HSV glowColor{ 120 };
		bool subtractive = false;

		while (System::Update())
		{
			Scene::SetBackground(backgroundColor);

			const String text = U"OpenSiv3D\nABCDEFG\n1234567\nあいうえお\n{}"_fmt(Cursor::Pos());
			const Vec2 pos{ 320, 80 };

			// 光彩を作成
			// 前フレームから内容を更新しない場合、スキップすることで実行時のコストを節約できる
			glowText.renderGlow(font, 60, text, pos);

			// 光彩を描画
			glowText.draw(Vec2{ 0, 0 }, glowColor, a1, a4, a8, subtractive);

			// テキストを描画
			font(text).draw(60, pos, textColor);

			// 光彩の強さや色を調整
			SimpleGUI::Slider(U"a8: {:.2f}"_fmt(a8), a8, 0.0, 4.0, Vec2{ 20, 20 });
			SimpleGUI::Slider(U"a4: {:.2f}"_fmt(a4), a4, 0.0, 4.0, Vec2{ 20, 60 });
			SimpleGUI::Slider(U"a1: {:.2f}"_fmt(a1), a1, 0.0, 4.0, Vec2{ 20, 100 });
			SimpleGUI::ColorPicker(backgroundColor, Vec2{ 20, 140 });
			SimpleGUI::ColorPicker(textColor, Vec2{ 20, 260 });
			SimpleGUI::ColorPicker(glowColor, Vec2{ 20, 380 });
			SimpleGUI::CheckBox(subtractive, U"Subtractive", Vec2{ 20, 500 }, 160);
		}
	}
	```


## 8. テキストの反射

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/text/8.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void DrawTextWithReflection(const Font& font, const String& text, const Vec2& basePos, double offsetY, const ColorF& color)
	{
		Vec2 penPos{ basePos };

		for (const auto& glyph : font.getGlyphs(text))
		{
			if (glyph.codePoint == U'\n')
			{
				penPos.x = basePos.x;
				penPos.y += font.height();
				continue;
			}

			const Vec2 offset = glyph.getOffset();
			glyph.texture.draw((penPos + offset), color);

			// 反射するテクスチャを描画する
			glyph.texture.flipped()
				.draw(penPos.x + offset.x, penPos.y + (font.height() * 2) - offset.y - glyph.texture.size.y + offsetY,
					Arg::top = ColorF{ color, 0.5 }, Arg::bottom = ColorF{ color, 0.0 });

			penPos.x += glyph.xAdvance;
		}
	}

	void Main()
	{
		const Font font{ 50 };
		const String text = U"OpenSiv3D あいうえお 12345";

		while (System::Update())
		{
			DrawTextWithReflection(font, text, Vec2{ 40, 40 }, -5, HSV{ 40 });
		}
	}
	```


## 9. 文字の後ろに図形

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/text/9.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	class CharacterShape
	{
	private:

		Polygon m_polygon;

	public:

		CharacterShape() = default;

		CharacterShape(const Font& font, const Glyph& glyph, double buffer)
		{
			Image image{ static_cast<size_t>(font.height()),  static_cast<size_t>(font.height()), Color{ 0, 0 } };

			font(glyph.codePoint).overwrite(image, Vec2{ 0, 0 });

			const MultiPolygon polygons = image.alphaToPolygons();

			Array<Vec2> points;

			for (const auto& polygon : polygons)
			{
				for (const auto& point : polygon.outer())
				{
					points << point;
				}
			}

			m_polygon = Geometry2D::ConvexHull(points)
				.calculateRoundBuffer(buffer);
		}

		void draw(const Vec2& pos, const ColorF& color) const
		{
			m_polygon.draw(pos, color);
		}
	};

	void DrawCharacterShapes(const Array<CharacterShape>& shapes,
		const Font& font, const String& text, const Vec2& basePos, const ColorF& color, double margin = 0.0)
	{
		Vec2 penPos{ basePos };
		size_t i = 0;

		for (const auto& glyph : font.getGlyphs(text))
		{
			if (glyph.codePoint == U'\n')
			{
				penPos.x = basePos.x;
				penPos.y += font.height();
				continue;
			}

			shapes[i].draw(penPos, color);
			penPos.x += (glyph.xAdvance + margin);
			++i;
		}
	}

	void DrawTextWithMargin(const Font& font, const String& text, const Vec2& basePos, const ColorF& color, double margin = 0.0)
	{
		Vec2 penPos{ basePos };

		for (const auto& glyph : font.getGlyphs(text))
		{
			if (glyph.codePoint == U'\n')
			{
				penPos.x = basePos.x;
				penPos.y += font.height();
				continue;
			}

			glyph.texture.draw((penPos + glyph.getOffset()), color);
			penPos.x += (glyph.xAdvance + margin);
		}
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });

		const Font font{ 66, Typeface::Heavy };
		constexpr double BufferWidth = 14;
		const String text = U"あいうえお12345";

		Array<CharacterShape> shapes;
		for (const auto& glyph : font.getGlyphs(text))
		{
			shapes.emplace_back(font, glyph, BufferWidth);
		}

		HSV shapeColor = Palette::Seagreen;
		HSV textColor = Palette::White;
		double margin = 0.0;

		while (System::Update())
		{
			const Vec2 pos{ 40, 40 };

			DrawCharacterShapes(shapes, font, text, pos, shapeColor, margin);
			DrawTextWithMargin(font, text, pos, textColor, margin);

			SimpleGUI::Slider(margin, 0.0, 20.0, Vec2{ 20, 160 });
			SimpleGUI::ColorPicker(shapeColor, Vec2{ 20, 200 });
			SimpleGUI::ColorPicker(textColor, Vec2{ 20, 320 });
		}
	}
	```

## 10. 絵文字をエイリアスで入力する
コード内の指示に従い、事前に `emoji.json` を用意してください。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/text/10.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// エイリアスと絵文字のペア
	struct EmojiAlias
	{
		// エイリアスとして使う単語
		String alias;

		// エイリアスに対応する絵文字
		String emoji;
	};

	class EmojiDictionary
	{
	public:

		EmojiDictionary() = default;

		explicit EmojiDictionary(FilePathView path)
		{
			const JSON json = JSON::Load(path);

			for (const auto& element : json)
			{
				const String alias = element.key;
				const String emoji = element.value.getString();
				m_emojis.push_back(EmojiAlias{ alias, emoji });
				m_hashTable.emplace(alias, emoji);
			}

			// エイリアスの文字数でソート
			m_emojis.sort_by([]
			(const EmojiAlias& a, const EmojiAlias& b)
				{
					return (a.alias.size() < b.alias.size());
				});
		}

		[[nodiscard]]
		explicit operator bool() const noexcept
		{
			return (not m_emojis.isEmpty());
		}

		[[nodiscard]]
		String getEmoji(StringView alias) const
		{
			auto it = m_hashTable.find(alias);

			if (it == m_hashTable.end())
			{
				return{};
			}

			return it->second;
		}

		[[nodiscard]]
		Array<EmojiAlias> getCandidates(const String& emojiAlias, size_t maxCandidates) const
		{
			if (not emojiAlias)
			{
				return{};
			}

			Array<EmojiAlias> candidates;

			for (const auto& emoji : m_emojis)
			{
				if (emoji.alias.includes(emojiAlias))
				{
					candidates << emoji;

					if (maxCandidates <= candidates.size())
					{
						break;
					}
				}
			}

			return candidates;
		}

	private:

		Array<EmojiAlias> m_emojis;

		HashTable<String, String> m_hashTable;
	};

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 36, Typeface::Medium };
		const Font emojiFont{ 24, Typeface::ColorEmoji };
		font.addFallback(emojiFont);

		// エイリアスと絵文字のペアを, エイリアスの文字数でソート
		// emoji.json は下記から入手
		// https://raw.githubusercontent.com/omnidan/node-emoji/master/lib/emoji.json
		const EmojiDictionary emojiDictionary{ U"emoji.json" };

		if (not emojiDictionary)
		{
			throw Error{ U"Failed to load emoji.json" };
		}

		String previousText, text;
		String emojiAlias;

		// 候補として表示する絵文字の最大個数
		constexpr size_t MaxCandidates = 8;
		Array<EmojiAlias> candidates;
		Optional<size_t> aliasBeginAt;
		size_t candidateIndex = 0;

		while (System::Update())
		{
			// テキスト入力の処理
			{
				TextInput::UpdateText(text, TextInputMode::AllowBackSpace);

				if (text != previousText)
				{
					aliasBeginAt.reset();

					for (size_t i = 0; i < text.size(); ++i)
					{
						const auto ch = text[i];

						if (ch == U':')
						{
							if (not aliasBeginAt)
							{
								emojiAlias.clear();
								aliasBeginAt = i;
							}
							else
							{
								if (String emoji = emojiDictionary.getEmoji(emojiAlias))
								{
									text.replace((text.begin() + *aliasBeginAt), (text.begin() + i + 1), emoji);
								}

								emojiAlias.clear();
								aliasBeginAt.reset();
							}
						}
						else if (aliasBeginAt)
						{
							emojiAlias << ch;
						}
					}

					previousText = text;
					candidates = emojiDictionary.getCandidates(emojiAlias, MaxCandidates);
					candidateIndex = 0;

					// デバッグ表示
					{
						ClearPrint();
						Print << U"emojiAlias: " << emojiAlias;
						Print << U"aliasBeginAt: " << aliasBeginAt;
					}
				}
			}

			// マウスオーバーによる候補の選択
			for (auto&& [i, candidate] : Indexed(candidates))
			{
				const Rect rect{ 40, (400 - candidates.size() * 40 + i * 40), 720, 38 };

				if (rect.mouseOver())
				{
					candidateIndex = i;
					Cursor::RequestStyle(CursorStyle::Hand);
					break;
				}
			}

			// キーボードによる候補の選択
			if (candidates)
			{
				if (KeyUp.down())
				{
					candidateIndex = (candidateIndex + candidates.size() - 1) % candidates.size();
				}
				else if (KeyDown.down())
				{
					++candidateIndex %= candidates.size();
				}
			}

			// 候補の表示と処理
			for (auto&& [i, candidate] : Indexed(candidates))
			{
				const Rect rect{ 40, (400 - candidates.size() * 40 + i * 40), 720, 38 };
				const bool selected = (candidateIndex == i);

				rect.rounded(4).draw(selected ? ColorF{ 0.7, 0.8, 0.9 } : ColorF{ 0.9 });
				emojiFont(candidate.emoji).draw(32, rect.pos.movedBy(10, 4));
				font(U':' + candidate.alias + U':').draw(24, rect.pos.movedBy(50, 2), ColorF{ 0.11 });

				// 候補がクリックされるか、エンターキーが押されたら
				if (rect.leftClicked()
					|| (selected && KeyEnter.down()))
				{
					text.replace((text.begin() + *aliasBeginAt + 1), text.end(), (candidate.alias + U':'));
					break;
				}
			}

			// テキストの表示
			{
				Rect{ 40, 400, 720, 50 }.draw();
				font(text).draw(32, Vec2{ 50, 402 }, ColorF{ 0.11 });
			}
		}
	}
	```

