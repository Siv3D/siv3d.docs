
# Visual effect

## 紙から切り抜いたような描画
![](images/visual-paper-cut.png)
```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(1.0, 0.9, 0.7));

	constexpr Vec2 pos(220, 60);

	const Image image(U"example/siv3d-kun.png");

	const Texture texture(image);

	// 画像の輪郭から Polygon を作成
	const Polygon polygon = image.alphaToPolygon(160, false);

	// 凸包を計算
	const Polygon convexHull = polygon.calculateConvexHull();

	// Polygon を太らせる
	const Polygon largeConvex = convexHull.calculateBuffer(20);

	// 影用の画像
	Image shadowImage(Scene::Size(), Color(255, 0));

	// 影のもとになる図形を書き込む
	convexHull.calculateBuffer(10).movedBy(pos + Vec2(10, 10)).overwrite(shadowImage, Color(255));

	// それをぼかしたものを影用テクスチャにする
	const Texture shadow(shadowImage.gaussianBlurred(40, 40));

	while (System::Update())
	{
		shadow.draw(ColorF(0.0, 0.5));

		largeConvex.draw(pos, ColorF(0.96, 0.98, 1.0));

		texture.draw(pos);
	}
}
```


## 付箋
![](images/visual-sticky-note.png)
```C++
# include <Siv3D.hpp>

void DrawStickyNote(const RectF& rect, const ColorF& noteColor)
{
	// 少しだけ回転させて影を描く
	{
		Transformer2D t(Mat3x2::Rotate(2_deg, rect.pos));

		rect.stretched(-2, 1, 1, -4).drawShadow(Vec2(0, 0), 12, 0, ColorF(0.0, 0.4));
	}

	rect.draw(noteColor);
}

void Main()
{
	Scene::SetBackground(ColorF(1.0, 0.98, 0.96));

	const Font font(36, Typeface::Bold);

	while (System::Update())
	{
		for (auto i : step(10))
		{
			const RectF rect(60 + i / 5 * 280, 20 + i % 5 * 90, 230, 70);

			DrawStickyNote(rect, HSV(i * 36, 0.46, 1.0));

			font(U"Text").draw(rect.pos.movedBy(20, 10), ColorF(0.1, 0.95));
		}
	}
}
```

## テクスチャの反射
![](images/visual-2d-reflection.png)
```C++
# include <Siv3D.hpp>

void Main()
{
	const std::array<Texture, 3> textures =
	{
		Texture(Emoji(U"💹")),
		Texture(Emoji(U"📅")),
		Texture(Emoji(U"🏡")),
	};

	constexpr Size imageSize = Emoji::ImageSize;

	while (System::Update())
	{
		Rect(0, 300, 800, 300).draw(ColorF(0.2, 0.3, 0.4));

		for (auto [i, texture] : Indexed(textures))
		{
			const Vec2 pos(140 + i * 200, 220);

			texture.draw(pos);

			// 反射するテクスチャ
			texture(0, imageSize.y / 2, imageSize.x, imageSize.y / 2).flipped()
				.draw(pos.x, pos.y + imageSize.y,
					Arg::top = AlphaF(0.8), Arg::bottom = AlphaF(0.0));
		}
	}
}
```


## テキストの登場
<video src="../images/visual-text-floatin.mp4" autoplay loop muted></video>
```C++
# include <Siv3D.hpp>

// Glyph とエフェクトの関数を組み合わせてテキストを描画
void DrawText(const DrawableText& fontText, const Vec2& pos, const ColorF& color, double t,
	void f(const Vec2& penPos, const Glyph& glyph, const ColorF& color, double t), double characterdPerSec)
{
	Vec2 penPos = pos;

	for (const auto& glyph : fontText)
	{
		if (glyph.codePoint == U'\n')
		{
			penPos.x = pos.x;
			penPos.y += fontText.font.height();
			continue;
		}

		const double targetTime = glyph.index * characterdPerSec;

		if (targetTime > t)
		{
			break;
		}

		f(penPos, glyph, color, t - targetTime);

		penPos.x += glyph.xAdvance;
	}
}

// 文字が上からゆっくり降ってくる表現
void TextEffect1(const Vec2& penPos, const Glyph& glyph, const ColorF& color, double t)
{
	const double y = EaseInQuad(Saturate(1 - t / 0.3)) * -20.0;
	const double a = Min(t / 0.3, 1.0);
	glyph.texture.draw(penPos + glyph.offset + Vec2(0, y), ColorF(color, a));
}

// 文字が勢いよく現れる表現
void TextEffect2(const Vec2& penPos, const Glyph& glyph, const ColorF& color, double t)
{
	const double s = Min(t / 0.1, 1.0);
	const double a = Min(t / 0.2, 1.0);
	glyph.texture.scaled(3.0 - s * 2).draw(penPos + glyph.offset, ColorF(color, a));
}

// 落ちてきた文字がしばらく揺れる表現
void TextEffect3(const Vec2& penPos, const Glyph& glyph, const ColorF& color, double t)
{
	const double angle = Sin(t * 1440_deg) * 25_deg * Saturate(1.0 - t / 0.6);
	const double y = Saturate(1 - t / 0.05) * -20.0;
	glyph.texture.rotated(angle).draw(penPos + glyph.offset + Vec2(0, y), color);
}

void Main()
{
	const Font font(32, Typeface::Bold);
	const String text = U"Lorem ipsum dolor sit amet, consectetur\n"
						U"adipiscing elit, sed do eiusmod tempor\n"
						U"incididunt ut labore et dolore magna aliqua.";

	Stopwatch stopwatch(true);

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Reset", Vec2(620, 520)))
		{
			stopwatch.restart();
		}

		const double t = stopwatch.sF();
		DrawText(font(text), Vec2(40, 40), Palette::Skyblue, t, TextEffect1, 0.1);
		DrawText(font(text), Vec2(40, 200), Palette::Orange, t, TextEffect2, 0.1);
		DrawText(font(text), Vec2(40, 360), Palette::Seagreen, t, TextEffect3, 0.1);
	}
}
```


