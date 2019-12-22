description: OpenSiv3D ビジュアル表現のプログラムのサンプル

# ビジュアル表現

## 紙から切り抜いたような描画
![](https://github.com/Siv3D/siv3d.docs.images/blob/master/sample/visual/paper-cut.png?raw=true)
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
![](https://github.com/Siv3D/siv3d.docs.images/blob/master/sample/visual/sticky-note.png?raw=true)
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
![](https://github.com/Siv3D/siv3d.docs.images/blob/master/sample/visual/2d-reflection.png?raw=true)
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
<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/sample/visual/text-floating.mp4?raw=true" autoplay loop muted></video>
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


## RenderTexture を使って図形や文字の影を描く
<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/sample/visual/2d-shadow.mp4?raw=true" autoplay loop muted></video>
```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));
	const Font font(100, Typeface::Heavy);
	const Texture emoji(Emoji(U"🐧"));

	// 影の形状を書き込むレンダーテクスチャ
	RenderTexture shadow(800, 600);
	RenderTexture shadowInternal(shadow.size());

	// 影の形状を書き込むためのブレンドステート
	BlendState bs = BlendState::Default;
	bs.op = BlendOp::Max;
	bs.srcAlpha = Blend::SrcAlpha;
	bs.dstAlpha = Blend::DestAlpha;
	bs.opAlpha = BlendOp::Max;

	while (System::Update())
	{
		const RectF rect(100 + Periodic::Sine0_1(4s) * 400, 200, 200);
		const Line line(100, 100, 400, 500);

		shadow.clear(ColorF(1.0, 0.0));
		{
			// 影の形状を書き込む
			ScopedRenderTarget2D target(shadow);
			ScopedRenderStates2D blend(bs);

			font(U"Siv3D").draw(400, 60);
			rect.draw();
			line.draw(LineStyle::RoundCap, 10);
			emoji.rotated(Scene::Time() * 30_deg).drawAt(600, 500);
		}

		// 書き込まれた影をガウスぼかしして灰色で描画
		Shader::GaussianBlur(shadow, shadowInternal, shadow);
		const Vec2 shadowDirection = Circular(10, Scene::Time() * 50_deg);
		shadow.draw(shadowDirection, ColorF(0.5));

		{
			// 本来の色で描画する
			font(U"Siv3D").draw(400, 60, Palette::Orange);
			rect.draw(Palette::Seagreen);
			line.draw(LineStyle::RoundCap, 10, Palette::White);
			emoji.rotated(Scene::Time() * 30_deg).drawAt(600, 500);
		}
	}
}
```


## 2D ライトプルーム
<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/sample/visual/2d-light-bloom.mp4?raw=true" autoplay loop muted></video>
```C++
# include <Siv3D.hpp>

void DrawScene()
{
	Circle(680, 40, 20).draw();
	Rect(Arg::center(680, 110), 30).draw();
	Triangle(680, 180, 40).draw();

	Circle(740, 40, 20).draw(HSV(0));
	Rect(Arg::center(740, 110), 30).draw(HSV(120));
	Triangle(740, 180, 40).draw(HSV(240));

	Circle(50, 200, 300).drawFrame(4);
	Circle(550, 450, 200).drawFrame(4);

	for (auto i : step(12))
	{
		const double angle = i * 30_deg + Scene::Time() * 5_deg;
		const Vec2 pos = OffsetCircular(Scene::Center(), 200, angle);
		Circle(pos, 8).draw(HSV(i * 30));
	}
}

void Main()
{
	constexpr Size sceneSize(800, 600);
	RenderTexture gaussianA1(sceneSize), gaussianB1(sceneSize);
	RenderTexture gaussianA4(sceneSize / 4), gaussianB4(sceneSize / 4);
	RenderTexture gaussianA8(sceneSize / 8), gaussianB8(sceneSize / 8);

	double a8 = 0.0, a4 = 0.0, a1 = 0.0;

	while (System::Update())
	{
		// 通常のシーン描画
		DrawScene();

		{
			// ガウスぼかし用テクスチャにもう一度シーンを描く
			gaussianA1.clear(ColorF(0.0));
			{
				ScopedRenderTarget2D target(gaussianA1);
				ScopedRenderStates2D blend(BlendState::Additive);
				DrawScene();
			}

			// オリジナルサイズのガウスぼかし (A1)
			// A1 を 1/4 サイズにしてガウスぼかし (A4)
			// A4 を 1/2 サイズにしてガウスぼかし (A8)
			Shader::GaussianBlur(gaussianA1, gaussianB1, gaussianA1);
			Shader::Downsample(gaussianA1, gaussianA4);
			Shader::GaussianBlur(gaussianA4, gaussianB4, gaussianA4);
			Shader::Downsample(gaussianA4, gaussianA8);
			Shader::GaussianBlur(gaussianA8, gaussianB8, gaussianA8);
		}

		{
			ScopedRenderStates2D blend(BlendState::Additive);

			if (a1)
			{
				gaussianA1.resized(sceneSize).draw(ColorF(a1));
			}

			if (a4)
			{
				gaussianA4.resized(sceneSize).draw(ColorF(a4));
			}

			if (a8)
			{
				gaussianA8.resized(sceneSize).draw(ColorF(a8));
			}
		}

		SimpleGUI::Slider(U"a8: {:.1f}"_fmt(a8), a8, 0.0, 4.0, Vec2(20, 20));
		SimpleGUI::Slider(U"a4: {:.1f}"_fmt(a4), a4, 0.0, 4.0, Vec2(20, 60));
		SimpleGUI::Slider(U"a1: {:.1f}"_fmt(a1), a1, 0.0, 4.0, Vec2(20, 100));
		
		if (SimpleGUI::Button(U"0, 0, 0", Vec2(20, 140)))
		{
			a1 = a4 = a8 = 0.0;
		}

		if (SimpleGUI::Button(U"1, 0, 0", Vec2(20, 180)))
		{
			a8 = 1.0;
			a4 = a1 = 0.0;
		}

		if (SimpleGUI::Button(U"1, 1, 0", Vec2(20, 220)))
		{
			a8 = a4 = 1.0;
			a1 = 0.0;
		}

		if (SimpleGUI::Button(U"1, 1, 1", Vec2(20, 260)))
		{
			a8 = a4 = a1 = 1.0;
		}
	}
}
```
