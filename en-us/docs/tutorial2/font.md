# 34. Drawing Text
Learn how to draw text in various styles using fonts.

## 34.1 Font Basics
- Fonts are managed by the `Font` class when drawing text on screen

### 34.1.1 Rendering Method
- Regular fonts can choose from 3 **rendering methods**:
	- **Bitmap method:** Stores font character image data as bitmaps. Quality degrades when drawn larger than the base size. Suitable for drawing fonts at a fixed size or when using typefaces with complex glyphs
	- **SDF method:** Stores font character image data as single-channel SDF (Signed Distance Field). Quality is maintained even when drawn larger than the base size. Can add shadow and outline effects. Has a side effect of slightly rounding character corners
	- **MSDF method:** Stores font character image data as 3-channel MSDF (Multi-channel Signed Distance Field). Quality is maintained even when drawn larger than the base size. Can add shadow and outline effects

| Rendering Method | Quality | Scaling Down | Scaling Up | Shadow | Outline | Runtime Load |
|--|:--:|:--:|:--:|:--:|:--:|:--:|
| Bitmap method| ◎ |  〇 | △ | 〇<br>(2 draws) | × | Low |
| SDF method| △ |  〇 | 〇 | ◎ | ◎ | Medium |
| MSDF method| 〇 | ◎ | ◎ | 〇 | 〇 | High |

- If no rendering method is specified, **bitmap method** is used
- Some typefaces can only use the bitmap method
- The rendering method is specified when creating the font and cannot be changed later

### 34.1.2 Base Size
- Individual character image data is created internally by the engine and cached in memory
- The size of the cached character images is called the **base size**
- The base size is specified when creating the font and cannot be changed later

#### Rendering Method and Base Size
- When text is drawn at a different size than the base size, scaling is performed
- With bitmap method, when scaling up occurs, the character appearance becomes rough like when enlarging a low-resolution image
    - **Bitmap method is intended to draw at the same size as the base size**
- With SDF/MSDF methods, quality is maintained even when drawing text scaled larger than the base size
- SDF/MSDF methods require a reasonably large base size
    - For complex characters with small base sizes, quality may degrade and glyph shapes may collapse
    - For SDF/MSDF methods, base size of `40` is recommended for alphanumeric characters and `48` for Japanese fonts
    - However, large base sizes increase memory consumption and runtime character image cache creation time, so balance must be considered

#### Runtime Cost
- Character image data is created and cached as needed during runtime (see **34.27**)
- When drawing many new character types that don't exist in the cache in a single frame, all that character image data must be created at once, increasing processing time for that frame
- Especially with SDF/MSDF methods, creating each character image takes time, potentially causing noticeable delays during runtime
- Using the preload feature in **34.28** to create necessary character image data in advance can reduce runtime delays

### 34.1.3 Typeface
- The font type such as "Meiryo" or "Arial" is called a **typeface**
- The typeface is specified when creating the font and cannot be changed later
- If no typeface is specified, the standard typeface (regular) bundled with Siv3D is used

### 34.1.4 Font Style
- Some typefaces can change styles like bold, italic, or bitmap fonts by specifying a **font style**
- Font style is specified when creating the font and cannot be changed later
- If no font style is specified, a regular font is created

### 34.1.5 Text Style
- SDF/MSDF method fonts can apply **text styles** during text drawing such as:
	- **Shadow:** Adds shadows in any direction
	- **Outline:** Adds outlines to characters
- Text style is specified during individual text drawing using the font
- If no text style is specified, drawing is done with normal style

### 34.1.6 Image Buffer Width
- Image buffer width is the margin width around characters when creating character image data
- By default, `2` is used
- For SDF/MSDF methods when creating large shadows or outlines, if the image buffer width is too small, shadows or outlines may be clipped
- Image buffer width is specified with `.setBufferThickness()` after font creation
- Large image buffer widths increase memory consumption and runtime character image cache creation time, so balance must be considered

### 34.1.7 Font Fallback
- A single typeface may not cover all necessary characters
- You can register a font of a different typeface as a **fallback** to cover characters that the main typeface cannot handle
- This is mainly used when text contains multiple languages or emoji

## 34.2 Font Creation and Drawing

### Font Creation
- There are several ways to create fonts:
    - **34.3, 34.4** Create from standard typeface
    - **34.5** Create from font file
    - **34.6** Create from font file installed on PC
- Font creation has a cost, so it's usually done before the main loop
- When creating in the main loop, control is needed to prevent creation every frame

### Text Drawing
- Passing text to the `()` operator of a `Font` object returns a `DrawableText` object
- To actually draw text, use member functions of `DrawableText`:
    - **34.10** Drawing with top-left coordinates `.draw()`
    - **34.11** Drawing with center coordinates `.drawAt()`
	- **34.12** Drawing with baseline `.drawBase()`
	- **34.13** Drawing with other coordinates `.draw(Args::...)`
    - **34.14** Drawing within a rectangle `.draw(rect)`

```cpp
font(U"Hello, Siv3D!").draw(40, Vec2{ 40, 40 });
```


## 34.3 Standard Typeface (1)
- Siv3D includes several standard typefaces
- If no typeface is specified when creating a font, the standard typeface (regular) is used
- Fonts can be created with 3 rendering methods:
	- Bitmap method
	- SDF method
	- MSDF method
- If no method is specified when creating a font, bitmap method is used

| Code | Description |
| --- | --- |
| `Font font{ base_size };` | Create bitmap method font with standard typeface (regular) |
| `Font font{ FontMethod::Bitmap, base_size };` | Create bitmap method font with standard typeface (regular) |
| `Font font{ FontMethod::SDF, base_size };` | Create SDF method font with standard typeface (regular) |
| `Font font{ FontMethod::MSDF, base_size };` | Create MSDF method font with standard typeface (regular) |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font fontBitmap{ 48 };
	const Font fontSDF{ FontMethod::SDF, 48 };
	const Font fontMSDF{ FontMethod::MSDF, 48 };

	while (System::Update())
	{
		fontBitmap(U"Hello, Siv3D!").draw(Vec2{ 40, 100 }, ColorF{ 0.2 });
		fontSDF(U"Hello, Siv3D!").draw(Vec2{ 40, 200 }, ColorF{ 0.2 });
		fontMSDF(U"Hello, Siv3D!").draw(Vec2{ 40, 300 }, ColorF{ 0.2 });
	}
}
```


## 34.4 Standard Typeface (2)
- Siv3D includes the following typefaces as standard:
	- 7 types of Japanese typefaces with different weights
	- CJK (Chinese, Korean, Japanese) typefaces for 5 regions
	- Monochrome emoji typeface
	- Color emoji typeface
- You can create fonts from standard typefaces by specifying `Typeface::` in the `Font` constructor

| Code |Description|
|--|--|
|`Typeface::Thin`|Thin Japanese typeface|
|`Typeface::Light`|Light Japanese typeface|
|`Typeface::Regular`|Regular Japanese typeface|
|`Typeface::Medium`|Medium Japanese typeface|
|`Typeface::Bold`|Bold Japanese typeface|
|`Typeface::Heavy`|Heavy Japanese typeface|
|`Typeface::Black`|Black Japanese typeface|
|`Typeface::CJK_Regular_JP`|Japanese design CJK typeface|
|`Typeface::CJK_Regular_KR`|Korean design CJK typeface|
|`Typeface::CJK_Regular_SC`|Simplified Chinese design CJK typeface|
|`Typeface::CJK_Regular_TC`|Traditional Chinese (Taiwan) design CJK typeface|
|`Typeface::CJK_Regular_HK`|Traditional Chinese (Hong Kong) design CJK typeface|
|`Typeface::MonochromeEmoji`|Monochrome emoji typeface|
|`Typeface::ColorEmoji`|Color emoji typeface|
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font fontThin{ FontMethod::MSDF, 48, Typeface::Thin };
	const Font fontLight{ FontMethod::MSDF, 48, Typeface::Light };
	const Font fontRegular{ FontMethod::MSDF, 48, Typeface::Regular };
	const Font fontMedium{ FontMethod::MSDF, 48, Typeface::Medium };
	const Font fontBold{ FontMethod::MSDF, 48, Typeface::Bold };
	const Font fontHeavy{ FontMethod::MSDF, 48, Typeface::Heavy };
	const Font fontBlack{ FontMethod::MSDF, 48, Typeface::Black };

	const Font fontJP{ FontMethod::MSDF, 48, Typeface::CJK_Regular_JP };
	const Font fontKR{ FontMethod::MSDF, 48, Typeface::CJK_Regular_KR };
	const Font fontSC{ FontMethod::MSDF, 48, Typeface::CJK_Regular_SC };
	const Font fontTC{ FontMethod::MSDF, 48, Typeface::CJK_Regular_TC };
	const Font fontHK{ FontMethod::MSDF, 48, Typeface::CJK_Regular_HK };

	const Font fontMono{ FontMethod::MSDF, 48, Typeface::MonochromeEmoji };

	// Color emoji fonts ignore method and base size
	const Font fontEmoji{ FontMethod::MSDF, 48, Typeface::ColorEmoji };

	const String s0 = U"Hello, Siv3D!";
	const String s1 = U"こんにちは 你好 안녕하세요 骨曜喝愛遙扇";
	const String s2 = U"🐈🐕🚀";

	while (System::Update())
	{
		fontThin(s0).draw(36, Vec2{ 40, 20 }, ColorF{ 0.2 });
		fontLight(s0).draw(36, Vec2{ 40, 60 }, ColorF{ 0.2 });
		fontRegular(s0).draw(36, Vec2{ 40, 100 }, ColorF{ 0.2 });
		fontMedium(s0).draw(36, Vec2{ 40, 140 }, ColorF{ 0.2 });
		fontBold(s0).draw(36, Vec2{ 40, 180 }, ColorF{ 0.2 });
		fontHeavy(s0).draw(36, Vec2{ 40, 220 }, ColorF{ 0.2 });
		fontBlack(s0).draw(36, Vec2{ 40, 260 }, ColorF{ 0.2 });

		fontJP(s1).draw(36, Vec2{ 40, 300 }, ColorF{ 0.2 });
		fontKR(s1).draw(36, Vec2{ 40, 340 }, ColorF{ 0.2 });
		fontSC(s1).draw(36, Vec2{ 40, 380 }, ColorF{ 0.2 });
		fontTC(s1).draw(36, Vec2{ 40, 420 }, ColorF{ 0.2 });
		fontHK(s1).draw(36, Vec2{ 40, 460 }, ColorF{ 0.2 });

		fontMono(s2).draw(36, Vec2{ 340, 20 }, ColorF{ 0.2 });
		fontEmoji(s2).draw(36, Vec2{ 500, 20 });
	}
}
```


## 34.5 Creating from Font File
- To create a font from your own font file, specify the font file path in the `Font` constructor
- File paths should use relative paths based on the folder containing the executable (the `App` folder during development) or absolute paths
	- For example, `U"example/font/RocknRoll/RocknRollOne-Regular.ttf"` refers to the `RocknRollOne-Regular.ttf` file in the `example/font/RocknRoll/` folder of the executable's folder (`App` folder)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Load and use RocknRollOne-Regular.ttf
	const Font font{ FontMethod::MSDF, 48, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"Hello, Siv3D!\nこんにちは！").draw(60, Vec2{ 40, 40 }, ColorF{ 0.2 });
	}
}
```


## 34.6 Creating from Font File Installed on PC
- Fonts installed on PC are stored in different locations depending on the OS
- You can get the folder path with `FileSystem::GetFolderPath()` and construct an absolute path by joining it with the font filename
- The correspondence between arguments passed to `FileSystem::GetFolderPath()` and the paths obtained is as follows:

| Argument                       | Windows             | macOS                  | Linux       |
|----------------------------|:---------------------|:------------------------|:-------------|
| `SpecialFolder::SystemFonts` | (OS):/WINDOWS/Fonts/ | /System/Library/Fonts/ | /usr/share/fonts/ |
| `SpecialFolder::LocalFonts`  | (OS):/WINDOWS/Fonts/ | /Library/Fonts/        | /usr/local/share/fonts/<br>(if exists) |
| `SpecialFolder::UserFonts`   | (OS):/WINDOWS/Fonts/ | ~/Library/Fonts/       | /usr/local/share/fonts/<br>(if exists) |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

# if SIV3D_PLATFORM(WINDOWS)

	const FilePath path = (FileSystem::GetFolderPath(SpecialFolder::SystemFonts) + U"arial.ttf");

# elif SIV3D_PLATFORM(MACOS)

	const FilePath path = (FileSystem::GetFolderPath(SpecialFolder::SystemFonts) + U"Helvetica.dfont");

# endif

	Print << path;

	const Font font{ FontMethod::MSDF, 48, path };

	while (System::Update())
	{
	# if SIV3D_PLATFORM(WINDOWS)

		font(U"Arial").draw(80, Vec2{ 40, 40 }, ColorF{ 0.2 });

	# elif SIV3D_PLATFORM(MACOS)

		font(U"Helvetica").draw(80, Vec2{ 40, 40 }, ColorF{ 0.2 });

	# endif
	}
}
```


## 34.7 Empty Font
- By default, `Font` type objects hold an **empty font**
- Using an empty font does not cause an error, but nothing is drawn
- Empty fonts also result when font file loading fails
- To check if a font is empty, use `if (font.isEmpty())`, `if (font)`, or `if (not font)`
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Font font1;

	Print << font1.isEmpty();

	// Assign a font
	font1 = Font{ FontMethod::MSDF, 48 };

	Print << font1.isEmpty();

	// Specify a non-existent font file
	const Font font2{ FontMethod::MSDF, 48, U"example/aaa.ttf" };

	if (not font2)
	{
		Print << U"Failed to load the font file";
	}

	while (System::Update())
	{
		// Draw using empty font
		font2(U"Arial").draw(80, Vec2{ 40, 40 }, ColorF{ 0.2 });
	}
}
```


## 34.8 String Formatting
- Using a created font `font`, display text with specified size, position, and color using `font(text).draw(size, pos, color);`
- The text part of `font(text)` can contain not only strings but any number of `Print`-able values (numbers, `Point`, etc.)
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		font(U"Hello, Siv3D!").draw(60, Vec2{ 40, 40 }, ColorF{ 1.0 });

		// Non-string values are formatted (converted to strings)
		font(Cursor::Pos()).draw(60, Vec2{ 40, 200 }, ColorF{ 0.2 });

		// Multiple values are formatted and concatenated
		font(123, U"ABC").draw(40, Vec2{ 40, 360 }, Palette::Seagreen);

		font(U"{}/{}/{}"_fmt(2025, 12, 31)).draw(40, Vec2{ 40, 420 }, Palette::Deepskyblue);
	}
}
```


## 34.9 Line Breaks
- If the text contains newline characters `'\n'`, line breaks occur at those positions
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		font(U"Hello,\nSiv3D!\n\n!!").draw(60, Vec2{ 40, 40 }, ColorF{ 0.2 });
	}
}
```


## 34.10 Drawing with Top-Left Coordinates
- To draw text by specifying the top-left coordinates, use `font(text).draw()`
- This function returns the area where the text was drawn as a `RectF`

| Code | Description |
| --- | --- |
| `.draw(x, y, color);` | Draw text from top-left coordinates `(x, y)` |
| `.draw(pos, color);` | Draw text from top-left coordinates `pos` |
| `.draw(fontSize, x, y, color);` | Draw text with font size `fontSize` from top-left coordinates `(x, y)` |
| `.draw(fontSize, pos, color);` | Draw text with font size `fontSize` from top-left coordinates `pos` |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		font(U"Siv3D").draw(80, Vec2{ 0, 0 }, ColorF{ 0.2 });

		font(U"Siv3D").draw(60, Vec2{ 200, 200 }, ColorF{ 0.2 });

		font(U"Siv3D").draw(40, Cursor::Pos(), ColorF{0.2});
	}
}
```


## 34.11 Drawing with Center Coordinates
- To draw text by specifying the center coordinates, use `font(text).drawAt()`
- This function returns the area where the text was drawn as a `RectF`

| Code | Description |
| --- | --- |
| `.drawAt(x, y, color);` | Draw text from center coordinates `(x, y)` |
| `.drawAt(pos, color);` | Draw text from center coordinates `pos` |
| `.drawAt(fontSize, x, y, color);` | Draw text with font size `fontSize` from center coordinates `(x, y)` |
| `.drawAt(fontSize, pos, color);` | Draw text with font size `fontSize` from center coordinates `pos` |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/11.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	const Rect rect{ 100, 300, 280, 80 };

	const Circle circle{ 600, 400, 80 };

	while (System::Update())
	{
		font(U"Siv3D").drawAt(80, Vec2{ 400, 60 }, ColorF{ 0.2 });

		rect.draw();
		font(U"Siv3D").drawAt(60, rect.center(), ColorF{ 0.2 });

		circle.draw();
		font(U"Siv3D").drawAt(40, circle.center, ColorF{ 0.2 });
	}
}
```


## 34.12 Drawing with Baseline
- To draw text by specifying the baseline start position, use `font(text).drawBase()`
	- When drawing text with different font sizes, baselines can be aligned
- This function returns the area where the text was drawn as a `RectF`

| Code | Description |
| --- | --- |
| `.drawBase(x, y, color);` | Draw text from baseline start position `(x, y)` |
| `.drawBase(pos, color);` | Draw text from baseline start position `pos` |
| `.drawBase(fontSize, x, y, color);` | Draw text with font size `fontSize` from baseline start position `(x, y)` |
| `.drawBase(fontSize, pos, color);` | Draw text with font size `fontSize` from baseline start position `pos` |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/12.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font1{ FontMethod::MSDF, 48, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };
	const Font font2{ FontMethod::MSDF, 48, Typeface::Bold };

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		// Baselines don't align
		font1(text).draw(30, Vec2{ 40, 100 }, ColorF{ 0.2 });
		font2(text).draw(20, Vec2{ 280, 100 }, ColorF{ 0.2 });
		font2(text).draw(50, Vec2{ 440, 100 }, ColorF{ 0.2 });

		Rect{ 0, 400, 800, 10 }.draw(Palette::Skyblue);

		// Draw text so that (40, 400) becomes the baseline start position
		font1(text).drawBase(30, Vec2{ 40, 400 }, ColorF{ 0.2 });
		Circle{ 40, 400 , 5 }.drawFrame(2, Palette::Red);

		// Draw text so that (280, 400) becomes the baseline start position
		font2(text).drawBase(20, Vec2{ 280, 400 }, ColorF{ 0.2 });
		Circle{ 280, 400 , 5 }.drawFrame(2, Palette::Red);

		// Draw text so that (440, 400) becomes the baseline start position
		font2(text).drawBase(50, Vec2{ 440, 400 }, ColorF{ 0.2 });
		Circle{ 440, 400 , 5 }.drawFrame(2, Palette::Red);
	}
}
```


## 34.13 Drawing with Other Coordinate References
- To specify the **right-center position** and draw text aligned to it, use:
	- `.draw(Arg::topRight = pos, ...)`
	- `.draw(Arg::topRight(x, y), ...)`
- There are 9 types of reference positions that can be specified
- These functions return the area where the text was drawn as a `RectF`

| Reference Position | Description |
|---|---|
| `Arg::topLeft` | Top-left. Same as `.draw()` |
| `Arg::topCenter` | Top-center |
| `Arg::topRight` | Top-right|
| `Arg::leftCenter` | Left-center |
| `Arg::center` | Center. Same as `.drawAt()` |
| `Arg::rightCenter` | Right-center |
| `Arg::bottomLeft` | Bottom-left |
| `Arg::bottomCenter` | Bottom-center |
| `Arg::bottomRight` | Bottom-right |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/13.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	const Rect rect{ 100, 300, 280, 80 };

	while (System::Update())
	{
		font(U"C++").draw(60, Arg::topCenter = Vec2{ 400, 20 }, ColorF{ 0.2 });

		font(U"Hello, Siv3D!").draw(30, Arg::topRight(780, 20), ColorF{ 0.2 });

		rect.draw();

		// Draw text right-aligned within the rectangle
		font(U"Siv3D").draw(32, Arg::rightCenter = rect.rightCenter().movedBy(-20, 0), ColorF{0.2});
	}
}
```


## 34.14 Drawing Within a Rectangle
- To draw text so it fits within a specified rectangle, use `font(text).draw(rect)`
- If all text characters fit within the rectangle, the function returns `true`
- If text overflows, the overflowing part is replaced with "…" and the function returns `false`

| Code | Description |
| --- | --- |
| `.draw(rect, color);` | Draw text to fit within rectangle `rect` |
| `.draw(fontSize, rect, color);` | Draw text with font size `fontSize` to fit within rectangle `rect` |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/14.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"The quick brown fox jumps over the lazy dog.";

	const Rect rect1{ 60, 40, 200, 100 };
	const Rect rect2{ 60, 200, 300, 100 };
	const Rect rect3{ 60, 360, 420, 120 };

	while (System::Update())
	{
		{
			rect1.draw();

			const bool ok = font(text).draw(24, rect1.stretched(-10), ColorF{ 0.2 });

			if (not ok)
			{
				rect1.drawFrame(8, ColorF{ 1.0, 0.0, 0.0, 0.5 });
			}
		}

		{
			rect2.draw();

			const bool ok = font(text).draw(24, rect2.stretched(-10), ColorF{ 0.2 });

			if (not ok)
			{
				rect2.drawFrame(8, ColorF{ 1.0, 0.0, 0.0, 0.5 });
			}
		}

		{
			rect3.draw();

			const bool ok = font(text).draw(24, rect3.stretched(-20), ColorF{ 0.2 });

			if (not ok)
			{
				rect3.drawFrame(8, ColorF{ 1.0, 0.0, 0.0, 0.5 });
			}
		}
	}
}
```


## 34.15 Getting the Drawing Area
- To get the area that would be drawn without actually drawing, use the following member functions of `font(text)`:

| Code | Description |
| --- | --- |
| `.region(x, y);` | Return the area as `RectF` when drawing text from `(x, y)` |
| `.region(pos);` | Return the area as `RectF` when drawing text from `pos` |
| `.region(fontSize, x, y);` | Return the area as `RectF` when drawing text with font size `fontSize` from `(x, y)` |
| `.region(fontSize, pos);` | Return the area as `RectF` when drawing text with font size `fontSize` from `pos` |
| `.regionAt(x, y);` | Return the area as `RectF` when drawing text centered at `(x, y)` |
| `.regionAt(pos);` | Return the area as `RectF` when drawing text centered at `pos` |
| `.regionAt(fontSize, x, y);` | Return the area as `RectF` when drawing text with font size `fontSize` centered at `(x, y)` |
| `.regionAt(fontSize, pos);` | Return the area as `RectF` when drawing text with font size `fontSize` centered at `pos` |
| `.regionBase(x, y);` | Return the area as `RectF` when drawing text with `(x, y)` as baseline start position |
| `.regionBase(pos);` | Return the area as `RectF` when drawing text with `pos` as baseline start position |
| `.regionBase(fontSize, x, y);` | Return the area as `RectF` when drawing text with font size `fontSize` with `(x, y)` as baseline start position |
| `.regionBase(fontSize, pos);` | Return the area as `RectF` when drawing text with font size `fontSize` with `pos` as baseline start position |


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/15.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"Hello, Siv3D!";
	const Vec2 pos{ 40, 40 };

	// Get the text area when drawing text at pos position using font
	const RectF rect = font(text).region(60,  pos);

	while (System::Update())
	{
		// Fill the drawing area rectangle in advance
		rect.draw(Palette::Skyblue);

		// Draw text on top of the rectangle
		font(text).draw(60, pos, ColorF{ 0.2 });

		// Text area
		font(text)
			.drawAt(80, Vec2{ 400, 300 }, ColorF{ 1.0 })
			.stretched(40, 0)	// Stretch horizontally
			.shearedX(20)		// Make parallelogram
			.drawFrame(2);		// Draw frame
	}
}
```


## 34.16 Font Style (Bold/Italic)
- You can apply styles like bold and italic to fonts by specifying `FontStyle` in the `Font` constructor
- If the typeface doesn't support them, bold and italic are simulated, which may cause glyph abnormalities in SDF/MSDF methods

| Code | Description |
| --- | --- |
| `FontStyle::Bold` | Bold |
| `FontStyle::Italic` | Italic |
| `FontStyle::BoldItalic` | Bold and italic |

	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/16.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 48, Typeface::CJK_Regular_JP };
	const Font fontBold{ 48, Typeface::CJK_Regular_JP, FontStyle::Bold };
	const Font fontItalic{ 48, Typeface::CJK_Regular_JP, FontStyle::Italic };
	const Font fontBoldItalic{ 48, Typeface::CJK_Regular_JP, FontStyle::BoldItalic };

	const String text = U"Hello, Siv3D! こんにちは。";

	while (System::Update())
	{
		font(text).draw(48, Vec2{ 40, 40 }, ColorF{ 0.2 });
		fontBold(text).draw(48, Vec2{ 40, 100 }, ColorF{ 0.2 });
		fontItalic(text).draw(48, Vec2{ 40, 160 }, ColorF{ 0.2 });
		fontBoldItalic(text).draw(48, Vec2{ 40, 220 }, ColorF{ 0.2 });
	}
}
```


## 34.17 Font Style (Bitmap)
- If the typeface supports bitmap fonts, you can draw characters that preserve the pixel feel by specifying `FontStyle` in the `Font` constructor
- Uses the bitmap rendering method

| Code | Description |
| --- | --- |
| `FontStyle::Bitmap` | Bitmap font |
| `FontStyle::BoldBitmap` | Bold bitmap font |
| `FontStyle::ItalicBitmap` | Italic bitmap font |
| `FontStyle::BoldItalicBitmap` | Bold and italic bitmap font |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/17.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font1{ 32, U"example/font/DotGothic16/DotGothic16-Regular.ttf", FontStyle::Bitmap };
	const Font font2{ 32, U"example/font/DotGothic16/DotGothic16-Regular.ttf", FontStyle::ItalicBitmap };
	const Font font3{ 60, U"example/font/DotGothic16/DotGothic16-Regular.ttf", FontStyle::Bitmap };
	const Font font4{ 60, U"example/font/DotGothic16/DotGothic16-Regular.ttf", FontStyle::ItalicBitmap };

# if SIV3D_PLATFORM(WINDOWS)

	const FilePath path = (FileSystem::GetFolderPath(SpecialFolder::SystemFonts) + U"msgothic.ttc");
	const Font font5{ 16, path, FontStyle::Bitmap };

# endif

	const String text = U"こんにちは、Siv3D!";

	while (System::Update())
	{
		font1(text).draw(32, Vec2{ 40, 40 }, ColorF{ 0.2 });
		font2(text).draw(32, Vec2{ 40, 100 }, ColorF{ 0.2 });
		font3(text).draw(60, Vec2{ 40, 160 }, ColorF{ 0.2 });
		font4(text).draw(60, Vec2{ 40, 240 }, ColorF{ 0.2 });

	# if SIV3D_PLATFORM(WINDOWS)

		{
			// Preserve pixel feel when scaling up
			const ScopedRenderStates2D states{ SamplerState::ClampNearest };

			font5(text).draw(64, Vec2{ 40, 360 }, ColorF{ 0.2 });
		}

	# endif
	}
}
```


## 34.18 Adding Shadow to Text (Double Drawing)
- You can create a shadow effect by drawing text twice with offset coordinates
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/18.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	
	const Vec2 pos{ 40, 40 };

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font(text).draw(100, pos.movedBy(4, 4), ColorF{ 0.2, 0.4, 0.3 });
		font(text).draw(100, pos, ColorF{ 1.0 });
	}
}
```


## 34.19 Adding Shadow to Text (Text Style)
- SDF/MSDF method fonts can add shadow effects by specifying `TextStyle::Shadow(shadow offset, shadow color)` during drawing
- The offset value is relative to the base size
- If the image buffer width is insufficient, large shadow offsets may cause shadows to be clipped

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/19.png)

```cpp hl_lines="7 13"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font = Font{ FontMethod::MSDF, 48, Typeface::Bold }.setBufferThickness(4);

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font(text).draw(TextStyle::Shadow(Vec2{ 2, 2 }, ColorF{ 0.2, 0.4, 0.3 }), 100, Vec2{ 40, 40 }, ColorF{ 1.0 });
	}
}
```


## 34.20 Adding Outline to Text
- SDF/MSDF method fonts can add outline effects by specifying the following styles during drawing:
	- `TextStyle::Outline(outline scale, outline color)`
	- `TextStyle::Outline(inner outline scale, outer outline scale, outline color)`
- If the outline scale is too large, the drawing result will have noise
- The maximum is around 0.2-0.25 depending on the typeface
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/20.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font = Font{ FontMethod::MSDF, 48, Typeface::Bold }.setBufferThickness(4);

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font(text).draw(TextStyle::Outline(0.2, ColorF{ 0.0 }), 100, Vec2{ 40, 40 }, ColorF{ 1.0 });
	}
}
```


## 34.21 Adding Shadow and Outline to Text
- SDF/MSDF method fonts can add both shadow and outline effects by specifying the following styles during drawing:
	- `TextStyle::OutlineShadow(outline scale, outline color, shadow offset, shadow color)`
	- `TextStyle::OutlineShadow(inner outline scale, outer outline scale, outline color, shadow offset, shadow color)`
- If the outline scale is too large, the drawing result will have noise
- The maximum is around 0.2-0.25 depending on the typeface
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/21.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font = Font{ FontMethod::MSDF, 48, Typeface::Bold }.setBufferThickness(4);

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font(text).draw(TextStyle::OutlineShadow(0.2, ColorF{ 0.0 }, Vec2{ 2, 2 }, ColorF{ 0.2, 0.4, 0.3 }), 100, Vec2{ 40, 40 }, ColorF{ 1.0 });

		font(text).draw(TextStyle::OutlineShadow(0.15, ColorF{ 0.0 }, Vec2{ 1.0, 1.5 }, ColorF{ 0.0 }), 80, Vec2{ 40, 200 }, ColorF{ 1.0 });
	}
}
```


## 34.22 (Reference) Text Style Preview
- This is a sample to preview text style effects for each method
- You can move and zoom the view with right-click and wheel
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/22.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// Base size: larger makes scaled drawing cleaner but increases runtime cost
	const int32 baseSize = 70;

	// Image buffer width: larger allows large shadow text styles but increases runtime cost
	const int32 bufferThickness = 5;

	// Bitmap method cannot use outline or shadow effects
	const Font fontBitmap{ FontMethod::Bitmap, baseSize, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };

	// SDF method
	const Font fontSDF{ FontMethod::SDF, baseSize, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };
	fontSDF.setBufferThickness(bufferThickness);

	// MSDF method
	const Font fontMSDF{ FontMethod::MSDF, baseSize, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };
	fontMSDF.setBufferThickness(bufferThickness);

	bool outline = false;
	bool shadow = false;
	double inner = 0.1, outer = 0.1;
	Vec2 shadowOffset{ 2.0, 2.0 };
	ColorF textColor{ 1.0 };
	ColorF outlineColor{ 0.0 };
	ColorF shadowColor{ 0.0, 0.5 };
	HSV background = ColorF{ 0.6, 0.8, 0.7 };

	Camera2D camera{ Vec2{ 640, 360 }, 1.0 };

	while (System::Update())
	{
		Scene::SetBackground(background);

		TextStyle textStyle;
		{
			if (outline && shadow)
			{
				textStyle = TextStyle::OutlineShadow(inner, outer, outlineColor, shadowOffset, shadowColor);
			}
			else if (outline)
			{
				textStyle = TextStyle::Outline(inner, outer, outlineColor);
			}
			else if (shadow)
			{
				textStyle = TextStyle::Shadow(shadowOffset, shadowColor);
			}
		}

		camera.update();
		{
			auto t = camera.createTransformer();
			fontBitmap(U"Siv3D, 渋三次元 (Bitmap)").draw(Vec2{ 100, 250 }, textColor);
			fontSDF(U"Siv3D, 渋三次元 (SDF)").draw(textStyle, Vec2{ 100, 330 }, textColor);
			fontMSDF(U"Siv3D, 渋三次元 (MSDF)").draw(textStyle, Vec2{ 100, 410 }, textColor);
		}

		SimpleGUI::CheckBox(outline, U"Outline", Vec2{ 20, 20 }, 130);
		SimpleGUI::Slider(U"Inner: {:.2f}"_fmt(inner), inner, -0.5, 0.5, Vec2{ 160, 20 }, 120, 120, outline);
		SimpleGUI::Slider(U"Outer: {:.2f}"_fmt(outer), outer, -0.5, 0.5, Vec2{ 160, 60 }, 120, 120, outline);

		SimpleGUI::CheckBox(shadow, U"Shadow", Vec2{ 20, 100 }, 130);
		SimpleGUI::Slider(U"offsetX: {:.1f}"_fmt(shadowOffset.x), shadowOffset.x, -5.0, 5.0, Vec2{ 160, 100 }, 120, 120, shadow);
		SimpleGUI::Slider(U"offsetY: {:.1f}"_fmt(shadowOffset.y), shadowOffset.y, -5.0, 5.0, Vec2{ 160, 140 }, 120, 120, shadow);

		SimpleGUI::Headline(U"Text", Vec2{ 420, 20 });
		SimpleGUI::Slider(U"R", textColor.r, Vec2{ 420, 60 }, 20, 80);
		SimpleGUI::Slider(U"G", textColor.g, Vec2{ 420, 100 }, 20, 80);
		SimpleGUI::Slider(U"B", textColor.b, Vec2{ 420, 140 }, 20, 80);
		SimpleGUI::Slider(U"A", textColor.a, Vec2{ 420, 180 }, 20, 80);

		SimpleGUI::Headline(U"Outline", Vec2{ 540, 20 });
		SimpleGUI::Slider(U"R", outlineColor.r, Vec2{ 540, 60 }, 20, 80, outline);
		SimpleGUI::Slider(U"G", outlineColor.g, Vec2{ 540, 100 }, 20, 80, outline);
		SimpleGUI::Slider(U"B", outlineColor.b, Vec2{ 540, 140 }, 20, 80, outline);
		SimpleGUI::Slider(U"A", outlineColor.a, Vec2{ 540, 180 }, 20, 80, outline);

		SimpleGUI::Headline(U"Shadow", Vec2{ 660, 20 });
		SimpleGUI::Slider(U"R", shadowColor.r, Vec2{ 660, 60 }, 20, 80, shadow);
		SimpleGUI::Slider(U"G", shadowColor.g, Vec2{ 660, 100 }, 20, 80, shadow);
		SimpleGUI::Slider(U"B", shadowColor.b, Vec2{ 660, 140 }, 20, 80, shadow);
		SimpleGUI::Slider(U"A", shadowColor.a, Vec2{ 660, 180 }, 20, 80, shadow);

		SimpleGUI::ColorPicker(background, Vec2{ 780, 20 });
	}
}
```


## 34.23 Drawing Text Character by Character
- You can create a substring from the beginning to `count` characters using the `.substr(0, count)` member function of the `String` class
	- If `count` is larger than the actual string length, a substring up to the end is created
	- See **Tutorial 33.20**
- By increasing `count` using a stopwatch or similar, you can display text character by character

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/23.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";
	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		const int32 count = (stopwatch.ms() / 30);

		font(text.substr(0, count)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.2 });
	}
}
```


## 34.24 Free Drawing by Character
- Normal text drawing cannot control color, position, size, or rotation on a per-character basis
- For free per-character drawing, use `Array<Glyph>` obtained from the font's `.getGlyphs(text)`
- `Glyph` provides the information needed to freely control and draw individual characters

### 34.24.1 Basic Free Drawing (Bitmap Method)
- `Glyph` has the following members:

| Code | Description |
| --- | --- |
| `.codePoint` | UTF-32 code point of the character |
| `.texture` | Character image `TextureRegion` |
| `.getOffset(scale)` | Additional offset needed from pen position (with scale factor) |
| `.xAdvance` | X-coordinate distance to advance for current character |

- The following code uses `Glyph` information for free per-character drawing while reproducing normal text drawing:

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Font& font, const String& text, const double fontSize, const Vec2& basePos, const ColorF& color)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);
	const double scale = (fontSize / font.fontSize());
	const double fontHeight = (font.height() * scale);

	Vec2 penPos{ basePos };

	// Loop for per-character drawing control
	for (const auto& glyph : glyphs)
	{
		// If newline character
		if (glyph.codePoint == U'\n')
		{
			// Reset pen X coordinate
			penPos.x = basePos.x;

			// Advance pen Y coordinate by font height
			penPos.y += fontHeight;

			continue;
		}

		// Uncomment to visualize penPos
		//penPos.asCircle(3).drawFrame(1, Palette::Red);
		//(penPos + glyph.getOffset(scale)).asCircle(3).drawFrame(1, Palette::Green);

		// Draw character texture at pen position plus character-specific offset
		if (scale == 1.0)
		{
			// For bitmap method only at 1x scale, adjusting to integer coordinates with Math::Round() improves quality
			glyph.texture.draw(Math::Round(penPos + glyph.getOffset()), color);
		}
		else
		{
			glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale)), color);
		}

		// Advance pen X coordinate by character width
		penPos.x += (glyph.xAdvance * scale);
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ 48, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		DrawGlyphs(font, text, 48, Vec2{ 40, 40 }, ColorF{ 0.2 });

		DrawGlyphs(font, text, 36, Vec2{ 40, 240 }, ColorF{ 1.0 });

		DrawGlyphs(font, text, 24, Vec2{ 40, 440 }, Palette::Seagreen);
	}
}
```

### 34.24.2 Basic Free Drawing (SDF/MSDF Method)
- SDF/MSDF methods require special shader application, so use code like this:
	- Note that shape drawing is not available while the shader is applied

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Font& font, const String& text, const double fontSize, const Vec2& basePos, const ColorF& color)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);
	const double scale = (fontSize / font.fontSize());
	const double fontHeight = (font.height() * scale);

	// While this object exists, SDF/MSDF shader is applied to all 2D drawing
	const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method()) };

	Vec2 penPos{ basePos };

	// Loop for per-character drawing control
	for (const auto& glyph : glyphs)
	{
		// If newline character
		if (glyph.codePoint == U'\n')
		{
			// Reset pen X coordinate
			penPos.x = basePos.x;

			// Advance pen Y coordinate by font height
			penPos.y += fontHeight;

			continue;
		}

		// Draw character texture at pen position plus character-specific offset
		glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale)), color);

		// Advance pen X coordinate by character width
		penPos.x += (glyph.xAdvance * scale);
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		DrawGlyphs(font, text, 48, Vec2{ 40, 40 }, ColorF{ 0.2 });

		DrawGlyphs(font, text, 36, Vec2{ 40, 240 }, ColorF{ 1.0 });

		DrawGlyphs(font, text, 24, Vec2{ 40, 440 }, Palette::Seagreen);
	}
}
```

### 34.24.3 Advanced Free Drawing
- Sample controlling coordinates and colors per character

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Font& font, const String& text, const double fontSize, const Vec2& basePos)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);
	const double scale = (fontSize / font.fontSize());
	const double fontHeight = (font.height() * scale);

	const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method()) };

	Vec2 penPos{ basePos };
	int32 index = 0;

	for (const auto& glyph : glyphs)
	{
		if (glyph.codePoint == U'\n')
		{
			penPos.x = basePos.x;
			penPos.y += fontHeight;

			++index;
			continue;
		}

		const Vec2 offset{ 0, (Periodic::Sine1_1(2s, (Scene::Time() + index * 0.3)) * 8.0) };

		glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale) + offset), HSV{ (index * 10) });
		penPos.x += (glyph.xAdvance * scale);

		++index;
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		DrawGlyphs(font, text, 55, Vec2{ 40, 40 });
	}
}
```

### 34.24.4 Text Style Support
- To support text styles in free drawing with SDF/MSDF method fonts, do this:

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Font& font, const TextStyle& textStyle, const String& text, const double fontSize, const Vec2& basePos)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);
	const double scale = (fontSize / font.fontSize());
	const double fontHeight = (font.height() * scale);

	const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method(), textStyle.type) };
	Graphics2D::SetMSDFParameters(textStyle);

	Vec2 penPos{ basePos };
	int32 index = 0;

	for (const auto& glyph : glyphs)
	{
		if (glyph.codePoint == U'\n')
		{
			penPos.x = basePos.x;
			penPos.y += fontHeight;

			++index;
			continue;
		}

		const Vec2 offset{ 0, (Periodic::Sine1_1(2s, (Scene::Time() + index * 0.3)) * 8.0) };

		glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale) + offset), HSV{ (index * 10) });
		penPos.x += (glyph.xAdvance * scale);

		++index;
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		DrawGlyphs(font, TextStyle::Default(), text, 55, Vec2{ 40, 40 });

		DrawGlyphs(font, TextStyle::OutlineShadow(0.2, ColorF{ 0.0 }, Vec2{ 2, 2 }, ColorF{ 0.0 }), text, 55, Vec2{ 40, 240 });
	}
}
```


## 34.25 Vertical Writing
- Vertical text writing functionality is not yet implemented. It's planned for future versions
- You can reproduce it with free drawing as follows, but there's a limitation that quotes and punctuation don't become vertical-style
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/25.png)

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Font& font, const String& text, const double fontSize, const Vec2& basePos, const ColorF& color)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);
	const double scale = (fontSize / font.fontSize());
	const double fontHeight = (font.height() * scale);

	// While this object exists, SDF/MSDF shader is applied to all 2D drawing
	const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method()) };

	Vec2 penPos{ basePos };

	// Loop for per-character drawing control
	for (const auto& glyph : glyphs)
	{
		// If newline character
		if (glyph.codePoint == U'\n')
		{
			// Reset pen Y coordinate
			penPos.y = basePos.y;

			// Advance pen X coordinate
			penPos.x -= fontHeight;

			continue;
		}

		// Draw character texture at pen position plus character-specific offset
		glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale)), color);

		// Advance pen Y coordinate by character height
		penPos.y += (glyph.yAdvance * scale);
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"古池や\n蛙飛び込む\n水の音";

	while (System::Update())
	{
		DrawGlyphs(font, text, 48, Vec2{ 600, 40 }, ColorF{ 0.2 });

		DrawGlyphs(font, text, 36, Vec2{ 400, 40 }, ColorF{ 1.0 });

		DrawGlyphs(font, text, 24, Vec2{ 200, 40 }, Palette::Seagreen);
	}
}
```


## 34.26 Fallback Fonts
- A single typeface may not cover all characters that appear in text
- You can register a font of a different typeface as a **fallback** to draw characters that the main typeface can't cover with another typeface
- When a fallback font is set, if there's a character that can't be drawn with the base font but can be drawn with the fallback font, that font is used
- Set fallback fonts using `.addFallback()` to pass a created `Font`
- You can set any number of fallback fonts; earlier registered ones take priority
- When a color emoji font is set as a fallback font, the drawing size is adjusted to match the registered font's size
- Fallback fonts are mainly used when text contains emoji or multiple languages

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font0{ FontMethod::MSDF, 48, Typeface::Regular };
	const Font font1{ FontMethod::MSDF, 48, Typeface::Regular };
	const Font font2{ FontMethod::MSDF, 48, Typeface::Regular };

	const Font fontCJK{ FontMethod::MSDF, 48, Typeface::CJK_Regular_JP };
	const Font fontEmoji{ 48, Typeface::ColorEmoji };

	// Add one fallback font to font1
	font1.addFallback(fontCJK);

	// Add two fallback fonts to font2
	font2.addFallback(fontCJK);
	font2.addFallback(fontEmoji);

	const String text = U"Hello! こんにちは 你好 안녕하세요 🐈🐕🚀";

	while (System::Update())
	{
		font0(U"font0:\n" + text).draw(36, Vec2{40, 40}, ColorF{ 0.2 });
		font1(U"font1:\n" + text).draw(36, Vec2{ 40, 200 }, ColorF{ 0.2 });
		font2(U"font2:\n" + text).draw(36, Vec2{ 40, 360 }, ColorF{ 0.2 });
	}
}
```


## 34.27 Accessing Font Cache
- Fonts render and cache character image data when drawing characters for the first time
- You can get the font cache as a `Texture` using `.getTexture()` to check its contents
- Bitmap method uses white + alpha channel images, while SDF/MSDF methods use Distance field format images
- Running the following sample shows characters being added to the font cache over time
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/27.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 24, Typeface::Bold };

	const String text = U"Siv3D（シブスリーディー）は、音や画像、AI を使ったゲームやアプリを、モダンな C++ コードで楽しく簡単に開発できるオープンソースのフレームワークです。豊富なサンプルコードとチュートリアルが用意され、オンラインのユーザコミュニティで気軽に質問や相談ができます。";

	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		const int32 count = (stopwatch.ms() / 50);

		font(text.substr(0, count)).draw(Rect{ 20, 20, 760, 240 }, Palette::Seagreen);

		Rect{ 20, 300, font.getTexture().size() }.draw(ColorF{ 0.0 });

		font.getTexture().draw(20, 300);
	}
}
```


## 34.28 Font Preloading
- When displaying large amounts of text for the first time during real-time games, many characters need to be rendered and cached at once
- This causes frame time spikes (only that frame takes extremely long), which can affect gameplay experience
- Using `.preload(text)` you can pre-render and cache characters contained in `text`
- Performing preloading during game startup or loading screens can prevent frame time spikes during gameplay
- The `String` member function `.sorted_and_uniqued()` returns a string with characters sorted and duplicates removed. Applying this preprocessing to preload strings reduces preloading load
- The following sample preloads all characters at startup

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/28.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 24, Typeface::Bold };

	const String text = U"Siv3D（シブスリーディー）は、音や画像、AI を使ったゲームやアプリを、モダンな C++ コードで楽しく簡単に開発できるオープンソースのフレームワークです。豊富なサンプルコードとチュートリアルが用意され、オンラインのユーザコミュニティで気軽に質問や相談ができます。";

	// Removing duplicates with .sorted_and_uniqued() reduces preload load
	font.preload(text.sorted_and_uniqued());

	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		const int32 count = (stopwatch.ms() / 50);

		font(text.substr(0, count)).draw(Rect{ 20, 20, 760, 240 }, Palette::Seagreen);

		Rect{ 20, 300, font.getTexture().size() }.draw(ColorF{ 0.0 });

		font.getTexture().draw(20, 300);
	}
}
```


## 34.29 Getting Characters as Polygons
- You can get and draw characters as `Polygon` format instead of image format
- This can be used for vertex-level processing or effects using character shapes
- Using the `Font` member function `.renderPolygons()` you can get `PolygonGlyph` for each character when drawing a string
	- The rendering method doesn't affect this function
- Larger font base sizes increase polygon vertex count (giving higher quality polygons)
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/29.png)

```cpp
# include <Siv3D.hpp>

// Function returning Polygon for each character when drawing a string
Array<Polygon> ToPolygons(const Vec2& basePos, const Array<PolygonGlyph>& glyphs)
{
	Array<Polygon> polygons;

	Vec2 penPos{ basePos };

	for (const auto& glyph : glyphs)
	{
		for (const auto& polygon : glyph.polygons)
		{
			polygons << polygon.movedBy(penPos + glyph.getOffset());
		}

		penPos.x += glyph.xAdvance;
	}

	return polygons;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 80, Typeface::Bold };

	const String text = U"こんにちは、Siv3D!";

	const Array<Polygon> polygons = ToPolygons(Vec2{ 20, 20 }, font.renderPolygons(text));

	while (System::Update())
	{
		for (size_t i = 0; i < polygons.size(); ++i)
		{
			polygons[i].draw(HSV{ (i * 50) });

			polygons[i].drawWireframe(1, ColorF{ 0.2, Periodic::Square0_1(2s) });
		}
	}
}
```


## 34.30 Getting Characters as LineStrings
- You can get and draw characters as `LineString` format
- This can be used for outline processing or effects using character shapes
- Using the `Font` member function `.renderOutlines()` you can get `OutlineGlyph` for each character when drawing a string
	- The rendering method doesn't affect this function
- Larger font base sizes increase vertex count (giving higher quality line segments)
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/30.png)

```cpp
# include <Siv3D.hpp>

// Function returning LineString for each character when drawing a string
Array<LineString> ToLineStrings(const Vec2& basePos, const Array<OutlineGlyph>& glyphs)
{
	Array<LineString> lines;

	Vec2 penPos{ basePos };

	for (const auto& glyph : glyphs)
	{
		for (const auto& ring : glyph.rings)
		{
			lines << ring.movedBy(penPos + glyph.getOffset());
		}

		penPos.x += glyph.xAdvance;
	}

	return lines;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 80, Typeface::Bold };

	const String text = U"こんにちは、Siv3D!";

	const Array<LineString> lines = ToLineStrings(Vec2{ 20, 20 }, font.renderOutlines(text));

	while (System::Update())
	{
		for (size_t i = 0; i < lines.size(); ++i)
		{
			lines[i].drawClosed(2, HSV{ (i * 50) });
		}
	}
}
```