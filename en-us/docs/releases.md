# Release Notes

## v0.6 Generation

???+ summary "v0.6.16 | 2025-04-07"

	#### Upgrade Guide from Previous Version
	- [Upgrade procedure from v0.6.15 (Windows) :material-open-in-new:](https://zenn.dev/link/comments/5c3c74c675beaf){:target="_blank"}

	#### New Features
	- **Added support for building with Xcode 16.3** ([#1289](https://github.com/Siv3D/OpenSiv3D/issues/1289))
	- **Added `Rect::chamfered()`, `RectF::chamfered()` that return a `Polygon` with chamfered rectangle** ([#1268](https://github.com/Siv3D/OpenSiv3D/issues/1268))
		- [Sample :material-open-in-new:](https://zenn.dev/link/comments/bdd20930aae203){:target="_blank"}
	- **Added `Geometry2D::SmallestEnclosingCircle()` to find the smallest enclosing circle for point sets on a plane** ([#1272](https://github.com/Siv3D/OpenSiv3D/pull/1272))
		- [Sample :material-open-in-new:](https://zenn.dev/link/comments/d3b2a501acc2ab){:target="_blank"}
	- Enabled creation of sensors for triangle, quadrilateral, polygon, line segment shapes in 2D physics simulation ([#916](https://github.com/Siv3D/OpenSiv3D/issues/916), [#1251](https://github.com/Siv3D/OpenSiv3D/pull/1251))
	- Added `Line::withThickness()` to create a rectangle by adding thickness to a line segment ([#1276](https://github.com/Siv3D/OpenSiv3D/issues/1276))
	- Added overloads like `.stretched(Arg::left)` etc. to `Rect`, `RectF` ([#1277](https://github.com/Siv3D/OpenSiv3D/issues/1277), [#1279](https://github.com/Siv3D/OpenSiv3D/pull/1279))
	- Added `Font::getGlyphByGlyphIndex()` ([#1278](https://github.com/Siv3D/OpenSiv3D/issues/1278))
	- Added `SimpleMenuBar::mouseOver()` ([#1290](https://github.com/Siv3D/OpenSiv3D/issues/1290))
	- Added `String::choice()` that returns a random element from a string ([#1253](https://github.com/Siv3D/OpenSiv3D/issues/1253))
	- Added `Math::ClampAngle()` ([#1271](https://github.com/Siv3D/OpenSiv3D/pull/1271))
	- Added serialization support for `int128`, `uint128` ([#1261](https://github.com/Siv3D/OpenSiv3D/issues/1261))
	- Added `operator==` to `YesNo` class ([#1282](https://github.com/Siv3D/OpenSiv3D/pull/1282))

	#### Specification Changes
	- Updated the internal Boost version from 1.74.0 to 1.83.0 for Windows and macOS versions. The detailed behavior of geometry calculation functions may change ([#1292](https://github.com/Siv3D/OpenSiv3D/pull/1292))
	- Updated The Parallel Hashmap library from v1.3.8 to v2.0.0 ([#1297](https://github.com/Siv3D/OpenSiv3D/issues/1297))
	- Removed all Lua-related source files as they were not being used ([#1293](https://github.com/Siv3D/OpenSiv3D/issues/1293), [#1294](https://github.com/Siv3D/OpenSiv3D/pull/1294))

	#### Performance Improvements
	- Changed the return value of some `DiscreteSample()` overloads to references ([#1275](https://github.com/Siv3D/OpenSiv3D/pull/1275))

	#### Bug Fixes
	- Fixed a bug where `System::EnumerateWebcams()` crashed on Windows when no webcam was connected ([#1250](https://github.com/Siv3D/OpenSiv3D/issues/1250))
	- Fixed a bug where `.lookahead()` of `MemoryReader` and `MemoryViewReader` did not read from the specified position ([#1264](https://github.com/Siv3D/OpenSiv3D/issues/1264))
	- Fixed bugs in `MemoryWriter` ([#1266](https://github.com/Siv3D/OpenSiv3D/issues/1266))
	- Fixed a bug where the mouse wheel interfered with camera behavior when `CameraControl::None_` was set in `Camera2D` ([#1295](https://github.com/Siv3D/OpenSiv3D/issues/1295))
	- Fixed a bug where `.read()` and `.lookahead()` of `MemoryReader` and `MemoryViewReader` did not check for out-of-bounds access ([#1264](https://github.com/Siv3D/OpenSiv3D/issues/1264))
	- Fixed a bug on Windows where the program would stop until selection was cleared when text was selected in the console window during console output ([#1254](https://github.com/Siv3D/OpenSiv3D/issues/1254))
	- Fixed a bug where `System::LaunchFile()` on macOS/Linux could not open files with single quotes in their names ([#1283](https://github.com/Siv3D/OpenSiv3D/issues/1283))
	- Fixed a bug where `DebugCamera3D` sometimes focused on a different position than the focus point specified in the constructor ([#1255](https://github.com/Siv3D/OpenSiv3D/issues/1255), [#1274](https://github.com/Siv3D/OpenSiv3D/pull/1274))
	- Fixed a bug where `GlyphInfo::buffer` was sometimes not set depending on the acquisition method ([#1256](https://github.com/Siv3D/OpenSiv3D/issues/1256))
	- Fixed a bug where enum types could not be deserialized with `Deserializer<MemoryViewReader>` ([#1288](https://github.com/Siv3D/OpenSiv3D/issues/1288))
	- Fixed a bug where the spacing of ellipsis dots in text rendering within rectangles could vary depending on font size ([#1273](https://github.com/Siv3D/OpenSiv3D/pull/1273))
	- Fixed a bug in scene management where scene `.draw()` was called twice when fade time was set to 0 ([#1258](https://github.com/Siv3D/OpenSiv3D/pull/1258))
	- Fixed a bug where 2D physics-related classes caused compile errors in some environments ([#1286](https://github.com/Siv3D/OpenSiv3D/issues/1286))
	- Fixed a bug where `fill` was not reflected in some cases in `RoundRect::drawShadow()` ([#1269](https://github.com/Siv3D/OpenSiv3D/issues/1269))
	- Fixed a bug where `Indexed()` could return dangling references in special cases ([#1247](https://github.com/Siv3D/OpenSiv3D/pull/1247))
	- Fixed bugs in OpenGL ES 3.0 environment ([#1244](https://github.com/Siv3D/OpenSiv3D/pull/1244))
	- Fixed a bug where the read position could change on error in `BinaryReader::lookahead()` ([#1265](https://github.com/Siv3D/OpenSiv3D/issues/1265))
	- Fixed bugs in Linux builds ([#1248](https://github.com/Siv3D/OpenSiv3D/pull/1248), [#1281](https://github.com/Siv3D/OpenSiv3D/pull/1281))
	- Fixed typos in documentation ([#1262](https://github.com/Siv3D/OpenSiv3D/pull/1262), [#1270](https://github.com/Siv3D/OpenSiv3D/pull/1270))

	#### Contributions
	- [Raclamusi](https://github.com/Raclamusi): **Text rendering bug fixes within rectangles**, **`DebugCamera3D` bug fixes**, `DiscreteSample()` improvements
	- [yaito3014](https://github.com/yaito3014): **`Indexed()` bug fixes**, Linux build bug fixes
	- [Appbird](https://github.com/Appbird): **`Geometry2D::SmallestEnclosingCircle()` implementation**
	- [Aikawa3311](https://github.com/Aikawa3311): **2D physics sensor feature implementation**
	- [sashi0034](https://github.com/sashi0034): **`Math::ClampAngle()` implementation**
	- [leaf2326](https://github.com/leaf2326): **`Rect::stretched(Arg::left)` etc. overload implementation**
	- [m4saka](https://github.com/m4saka): **`YesNo` `operator==` implementation**
	- [yksake](https://github.com/yksake): Scene management bug fixes
	- [aFumihikoKobayashi](https://github.com/aFumihikoKobayashi): Linux build bug fixes
	- [yukidoke](https://github.com/yukidoke): Documentation fixes
	- [aoriika05](https://github.com/aoriika05): Documentation fixes

	### OpenSiv3D Challenge
	- \#19 SmallestEnclosingCircle: Appbird, Nachia, Luke256, Raclamusi, polyester, sasa


??? summary "v0.6.15 | 2024-07-03"

	#### Upgrade Guide from Previous Version
	- [Upgrade procedure from v0.6.14 (Windows)](https://zenn.dev/link/comments/8aaf45fc5ae077)

	#### New Features
	- **Added function `font(text).fits(fontSize, rect)` to check if text can be rendered within a specified rectangle without actually rendering** ([#1202](https://github.com/Siv3D/OpenSiv3D/issues/1202))
		- [Sample](https://zenn.dev/link/comments/76d0b5f552197f)
	- **Enabled starting drag operations for multiple files on Windows** ([#1218](https://github.com/Siv3D/OpenSiv3D/issues/1218))
		- [Sample](https://zenn.dev/link/comments/4cb4905d78ae52)
	- Added `Vec2::normalized_or(Vec2)` etc. that return a specified value when the vector is zero ([#1152](https://github.com/Siv3D/OpenSiv3D/issues/1152))
	- Enabled specifying default filename in `Dialog::SaveFile()` ([#1199](https://github.com/Siv3D/OpenSiv3D/issues/1199))
	- Added `.lerp()` to `Circular` ([#1203](https://github.com/Siv3D/OpenSiv3D/issues/1203), [#1206](https://github.com/Siv3D/OpenSiv3D/pull/1206))
	- Extended BMP file compliance ([#1204](https://github.com/Siv3D/OpenSiv3D/issues/1204), [#1207](https://github.com/Siv3D/OpenSiv3D/pull/1207))
	- Added functionality to get `ID3D11Texture2D*` from `Texture` ([#1219](https://github.com/Siv3D/OpenSiv3D/issues/1219))
	- Added `Image::inBounds()` ([#1229](https://github.com/Siv3D/OpenSiv3D/issues/1229))
	- Added function to check if program is running in IDE ([#1230](https://github.com/Siv3D/OpenSiv3D/issues/1230))
	- Added `Camera2D::getGrabbedPos()` ([#1242](https://github.com/Siv3D/OpenSiv3D/issues/1242))

	#### Specification Changes
	- **For Windows version, ensured that the executable directory is always the current directory even when the app is launched via command prompt or Start menu search** ([#1227](https://github.com/Siv3D/OpenSiv3D/issues/1227))
	- **Changed default model for OpenAI::Chat / Vision to the latest model GPT-4o** ([#1234](https://github.com/Siv3D/OpenSiv3D/issues/1234))
	- **Changed specification so that `.normalize()`, `.normalized()` for zero vectors return zero vectors** ([#1237](https://github.com/Siv3D/OpenSiv3D/issues/1237))
	- Changed specification so that `LineString::calculateBuffer()` and `LineString::calculateRoundBuffer()` return shapes (square, circle) even for single points ([#1209](https://github.com/Siv3D/OpenSiv3D/issues/1209), [#1210](https://github.com/Siv3D/OpenSiv3D/pull/1210))
	- Updated LunaSVG version to v2.3.9 ([#1211](https://github.com/Siv3D/OpenSiv3D/issues/1211))
	- Changed implementation of `String::levenshteinDistanceFrom()` for non-SSE environments ([#1239](https://github.com/Siv3D/OpenSiv3D/issues/1239))
	- Enabled specifying `./` as default path in `Dialog::SaveFile()` for Windows version ([#1240](https://github.com/Siv3D/OpenSiv3D/issues/1240))

	#### Performance Improvements
	- Accelerated `Image::rotate90()`, `Image::rotate270()` etc. ([#1182](https://github.com/Siv3D/OpenSiv3D/issues/1182), [#1225](https://github.com/Siv3D/OpenSiv3D/pull/1225)))

	#### Bug Fixes
	- **Fixed bug where `Parse<double>(U"")` did not throw an exception but became `assert`** ([#1226](https://github.com/Siv3D/OpenSiv3D/issues/1226))
	- **Fixed bug where crashes could occur in `Platform::Windows::Keyboard::GetEvents()`** ([#1221](https://github.com/Siv3D/OpenSiv3D/issues/1221))
	- Fixed template parameter constraints for `AsyncTask` ([#1213](https://github.com/Siv3D/OpenSiv3D/pull/1213)))
	- Fixed build errors in `Statistics::Mode()`, `Statistics::MultiMode()` ([#1214](https://github.com/Siv3D/OpenSiv3D/issues/1214))
	- Fixed build error when loading `CSV` from `Reader` ([#1215](https://github.com/Siv3D/OpenSiv3D/issues/1215))
	- Fixed typos in documentation ([#1216](https://github.com/Siv3D/OpenSiv3D/pull/1216)))
	- Changed `Point::length()`, `Point::lengthSq()` to implementation less prone to integer overflow ([#1217](https://github.com/Siv3D/OpenSiv3D/issues/1217))
	- Improved consistency of `FileSystem::Extension()`, `FileSystem::FileName()`, `FileSystem::BaseName()` ([#1223](https://github.com/Siv3D/OpenSiv3D/issues/1223))
	- Made engine initialization continue even if `RegisterDragDrop` fails during engine initialization ([#1238](https://github.com/Siv3D/OpenSiv3D/issues/1238))
	- Fixed default argument of `StringView::lastIndexOfAny()` from 0 to npos ([#1241](https://github.com/Siv3D/OpenSiv3D/issues/1241))

	#### Contributions
	- [Raclamusi](https://github.com/Raclamusi): **BMP file compliance extension**, **`Image::rotate90()`, `Image::rotate270()` etc. acceleration**, **`LineString::calculateBuffer()` and `LineString::calculateRoundBuffer()` specification changes**, `AsyncTask` fixes
	- [zaligan](https://github.com/zaligan): **`Circular::lerp()` implementation**
	- [Plinano](https://github.com/Plinano): Documentation fixes


??? summary "v0.6.14 | 2024-02-05"

	#### Upgrade Guide from Previous Version
	- [Upgrade procedure from v0.6.13 (Windows)](https://zenn.dev/link/comments/fd6585920f0136)

	#### New Features
	- **Added `Shader::QuadWarp`** ([#998](https://github.com/Siv3D/OpenSiv3D/issues/998), [#1183](https://github.com/Siv3D/OpenSiv3D/pull/1183))
		- [Sample](https://zenn.dev/link/comments/00f59036fb188f)
	- **Added support for new OpenAI APIs and models including Vision and TextToSpeech** ([#1126](https://github.com/Siv3D/OpenSiv3D/issues/1126), [#1176](https://github.com/Siv3D/OpenSiv3D/pull/1176), [#1181](https://github.com/Siv3D/OpenSiv3D/issues/1181), [#1194](https://github.com/Siv3D/OpenSiv3D/issues/1194))
		- [Sample](https://zenn.dev/link/comments/b4a570bf1e2a26)
	- **Added `Shape2D::Astroid()`** ([#1191](https://github.com/Siv3D/OpenSiv3D/issues/1191))
		- [Sample](https://zenn.dev/link/comments/bff536cae6cede)
	- **Added filter size options for `Shader::GaussianBlur()`** ([#1147](https://github.com/Siv3D/OpenSiv3D/issues/1147), [#1148](https://github.com/Siv3D/OpenSiv3D/pull/1148))
		- [Sample](https://zenn.dev/link/comments/7308f87190f48c)
	- **Added shadow sample shaders** ([#1140](https://github.com/Siv3D/OpenSiv3D/issues/1140), [#1200](https://github.com/Siv3D/OpenSiv3D/issues/1200))
		- [Sample](https://zenn.dev/link/comments/123b071121fbaf)

	[... Additional version notes would continue in similar format ...]

	
## Previous Versions

For complete release history including older versions, please refer to the [GitHub Releases page :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D/releases){:target="_blank"}.