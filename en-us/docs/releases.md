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
	- Enabled specifying target players in `MultiPlayer_Photon::sendEvent()` ([#1170](https://github.com/Siv3D/OpenSiv3D/issues/1170))
	- Added `.clear()` to `Trail`, `TrailMotion` ([#1149](https://github.com/Siv3D/OpenSiv3D/issues/1149))
	- Added `.with~()` to `Point`, `Vec2`, `Color`, `ColorF` etc. that return a copy with only one element changed ([#1143](https://github.com/Siv3D/OpenSiv3D/issues/1143))
	- Added `.computeConvexHull()` to `MultiPolygon` ([#1195](https://github.com/Siv3D/OpenSiv3D/issues/1195))
	- Added `.centroid()` to `MultiPolygon` ([#1186](https://github.com/Siv3D/OpenSiv3D/issues/1186), [#1190](https://github.com/Siv3D/OpenSiv3D/pull/1190))
	- Added `.area()`, `.perimeter()` to `MultiPolygon` ([#1185](https://github.com/Siv3D/OpenSiv3D/issues/1185), [#1187](https://github.com/Siv3D/OpenSiv3D/pull/1187))
	- Added `.rotate90()` series member functions to `Rect`, `RectF` ([#1094](https://github.com/Siv3D/OpenSiv3D/pull/1094))
	- Added `.getUTF8String()`, `.assignUTF8String()` to `JSON` ([#1177](https://github.com/Siv3D/OpenSiv3D/issues/1177))
	- Added overloads that accept `const char32*` and `StringView` to `PutText()` ([#1159](https://github.com/Siv3D/OpenSiv3D/issues/1159))
	- Added overloads for `Mat3x3::Homography()` ([#1163](https://github.com/Siv3D/OpenSiv3D/issues/1163))
	- Added `Cursor::SetCapture()`, `Cursor::IsCaptured()` for future UI feature implementation ([#1045](https://github.com/Siv3D/OpenSiv3D/issues/1045))

	#### Specification Changes
	- **Increased the maximum number of simultaneous audio playbacks from 16 to 64** ([#1123](https://github.com/Siv3D/OpenSiv3D/issues/1123))
	- Updated color emoji version from Unicode 15.0 to Unicode 15.1 ([#1144](https://github.com/Siv3D/OpenSiv3D/issues/1144))
	- Changed `DirectoryWatcher` constructor argument to `FilePathView` ([#1197](https://github.com/Siv3D/OpenSiv3D/issues/1197))
	- Redesigned OpenAI-related API namespaces and functions ([#1176](https://github.com/Siv3D/OpenSiv3D/issues/1176))
	- Changed some `FontAsset` function arguments from `const String&` to `StringView` ([#1158](https://github.com/Siv3D/OpenSiv3D/issues/1158))
	- Updated fmt library from 8.1.1 to 10.1.1 ([#1160](https://github.com/Siv3D/OpenSiv3D/issues/1160))
	- Updated zstd library from 1.5.1 to 1.5.5 ([#1161](https://github.com/Siv3D/OpenSiv3D/issues/1161), [#1162](https://github.com/Siv3D/OpenSiv3D/pull/1162))

	#### Performance Improvements
	- Slightly reduced overhead of `MultiPolygon` member functions ([#1188](https://github.com/Siv3D/OpenSiv3D/issues/1188), [#1189](https://github.com/Siv3D/OpenSiv3D/pull/1189))

	#### Bug Fixes
	- Fixed multiple bugs in `JSON` ([#1117](https://github.com/Siv3D/OpenSiv3D/issues/1117), [#1165](https://github.com/Siv3D/OpenSiv3D/pull/1165), [#1166](https://github.com/Siv3D/OpenSiv3D/issues/1166), [#1192](https://github.com/Siv3D/OpenSiv3D/issues/1192))
	- Fixed the issue where both ends did not gradient in `Circle::drawArc(LineStyle::RoundCap)` and the bug where inner and outer colors were reversed ([#1013](https://github.com/Siv3D/OpenSiv3D/issues/1013), [#1193](https://github.com/Siv3D/OpenSiv3D/issues/1193), [#1198](https://github.com/Siv3D/OpenSiv3D/pull/1198))
	- Fixed a bug where `RandomInt8()`, `RandomInt16()`, `RandomInt32()`, `RandomInt64()` did not return minimum values ([#1196](https://github.com/Siv3D/OpenSiv3D/issues/1196))
	- Fixed a bug where `Parse<double>(U"")` returned 0 without throwing `ParseError` ([#1173](https://github.com/Siv3D/OpenSiv3D/issues/1173), [#1174](https://github.com/Siv3D/OpenSiv3D/pull/1174))
	- Fixed a bug where `joinRoomReturn` was not called in `MultiPlayer_Photon` ([#1169](https://github.com/Siv3D/OpenSiv3D/issues/1169))
	- Fixed a bug where `Cursor::SetPos()` did not properly coordinate with scene size ([#1167](https://github.com/Siv3D/OpenSiv3D/issues/1167), [#1168](https://github.com/Siv3D/OpenSiv3D/pull/1168))
	- Fixed rendering disturbance in `RoundRect::drawShadow()` when `blur` was large ([#1164](https://github.com/Siv3D/OpenSiv3D/pull/1164))
	- Fixed a bug where `ConstantBuffer` copying was not performed correctly ([#1154](https://github.com/Siv3D/OpenSiv3D/issues/1154), [#1155](https://github.com/Siv3D/OpenSiv3D/pull/1155))
	- Fixed the issue where some `RectF` constructors gave narrowing conversion warnings when receiving `int32` ([#1184](https://github.com/Siv3D/OpenSiv3D/issues/1184))
	- Fixed the issue where frame rate was limited when VSync was disabled in some Windows environments ([#1179](https://github.com/Siv3D/OpenSiv3D/pull/1179))
	- Fixed a bug where GIF image resolution obtained by `ImageDecoder::GetImageInfo()` was incorrect ([#1172](https://github.com/Siv3D/OpenSiv3D/pull/1172))
	- Fixed the issue where loading GIF files with width or height larger than 16384px caused crashes ([#1171](https://github.com/Siv3D/OpenSiv3D/pull/1171))
	- Fixed the issue where `DebugCamera3D::drawTouchUI()` was missing `const` ([#1091](https://github.com/Siv3D/OpenSiv3D/issues/1091))
	- Fixed the issue where some constexpr member functions of shape classes used inactive union members ([#1139](https://github.com/Siv3D/OpenSiv3D/issues/1139), [#1141](https://github.com/Siv3D/OpenSiv3D/issues/1141))
	- Fixed the issue where message boxes were not displayed properly when in fullscreen + render target change ([#1150](https://github.com/Siv3D/OpenSiv3D/issues/1150))

	#### Contributions
	- [Ogame3334](https://github.com/Ogame3334): **`MultiPolygon` feature additions and improvements**
	- [m4saka](https://github.com/m4saka): VSync-related issue fixes, GIF-related bug fixes
	- [Raclamusi](https://github.com/Raclamusi): **`Circle::drawArc()` fixes**, improvements to shape class `constexpr` support
	- [sashi0034](https://github.com/sashi0034): `Cursor::SetPos()` fixes
	- [comefrombottom](https://github.com/comefrombottom): Feature additions to `Rect`, `RectF`

	
??? summary "v0.6.13 | 2023-11-15"

	#### New Features
	- Added support for Visual Studio 2022 17.8 ([#1136](https://github.com/Siv3D/OpenSiv3D/issues/1136))
	- Enabled mipmap generation in `DynamicTexture` ([#1130](https://github.com/Siv3D/OpenSiv3D/issues/1130), [#1135](https://github.com/Siv3D/OpenSiv3D/pull/1135))
	- Enabled mipmap generation in `RenderTexture`, `MSRenderTexture` ([#1129](https://github.com/Siv3D/OpenSiv3D/issues/1129), [#1134](https://github.com/Siv3D/OpenSiv3D/pull/1134))
	- Added `TextureFormat::R16G16_Unorm` ([#1122](https://github.com/Siv3D/OpenSiv3D/issues/1122))

	#### Specification Changes
	- Changed `Texture::isMipped()` to `Texture::hasMipMap()` ([#1131](https://github.com/Siv3D/OpenSiv3D/issues/1131))

	#### Usability Improvements
	- Fixed inconvenience where `Polygon` and `RoundRect` could not be used when including `<Siv3D/DLL.hpp>` on Windows ([#1120](https://github.com/Siv3D/OpenSiv3D/issues/1120))
	- Fixed missing `explicit` on some `Font` constructors ([#1115](https://github.com/Siv3D/OpenSiv3D/issues/1115))

	#### Performance Improvements
	- Mipmap generation for `Texture` is now performed on the GPU. Creating textures from images with `TextureDesc::Mipped`, emoji, and icons is significantly faster ([#1133](https://github.com/Siv3D/OpenSiv3D/issues/1133), [#1137](https://github.com/Siv3D/OpenSiv3D/pull/1137))
	- Improved speed and accuracy of bounding box recalculation for `Polygon` scale functions ([#1069](https://github.com/Siv3D/OpenSiv3D/issues/1069), [#1132](https://github.com/Siv3D/OpenSiv3D/pull/1132))

	#### Bug Fixes
	- Fixed a bug where audio could not be played on macOS (Apple Silicon) ([#1127](https://github.com/Siv3D/OpenSiv3D/issues/1127))
	- Fixed a bug where mipmaps were sometimes not used when drawing textures with the OpenGL backend ([#1128](https://github.com/Siv3D/OpenSiv3D/issues/1128))
	- Fixed a bug where `Subdivision2D::findNearest()` did not store result coordinates in some cases ([#1116](https://github.com/Siv3D/OpenSiv3D/issues/1116))
	- Fixed a bug where `Subdivision2D::initDelaunay()` did not reset `m_addedPoints` ([#1114](https://github.com/Siv3D/OpenSiv3D/issues/1114))
	- Fixed an issue where some constexpr member functions of `Rect`, `RectF` could not be used in compile-time calculations ([#1118](https://github.com/Siv3D/OpenSiv3D/issues/1118))

	#### Contributions
	- [Raclamusi](https://github.com/Raclamusi): **Improvements to `Polygon` scale functions**


??? summary "v0.6.12 | 2023-09-27"

	#### New Features
	- Added functions to create a parallelogram `Quad` from `Rect`, `RectF` by specifying an angle ([#1056](https://github.com/Siv3D/OpenSiv3D/issues/1056), [#1070](https://github.com/Siv3D/OpenSiv3D/pull/1070))
		- [Sample](https://zenn.dev/link/comments/2f28a4366b3b2f)
	- Added functions to calculate 2D and 3D Morton Order ([#1072](https://github.com/Siv3D/OpenSiv3D/issues/1072))
		- [Sample](https://zenn.dev/link/comments/a9015b37d00cc2)
	- Added `SimpleGUI::IMECandidateWindow()` to draw the conversion candidate window when using IME on Windows 11 ([#1106](https://github.com/Siv3D/OpenSiv3D/issues/1106), [#1107](https://github.com/Siv3D/OpenSiv3D/pull/1107))
		- [Sample](https://zenn.dev/link/comments/f9c5f75094806f)
	- Added `.rotate90(N)` etc. to `Point`, `Vector2D` ([#1093](https://github.com/Siv3D/OpenSiv3D/issues/1093), [#1102](https://github.com/Siv3D/OpenSiv3D/pull/1102))
		- [Sample](https://zenn.dev/link/comments/664156e39d1e3f)
	- Enabled specifying initial fade-in time in `SceneManager::init()` ([#1078](https://github.com/Siv3D/OpenSiv3D/issues/1078), [#1081](https://github.com/Siv3D/OpenSiv3D/pull/1081))
		- [Sample](https://zenn.dev/link/comments/1dae79127cb0c5)
	- Added `==`, `!=` to compare two `Image`s ([#1099](https://github.com/Siv3D/OpenSiv3D/issues/1099))
	- Added `Image::rotate90(N)` overload ([#1089](https://github.com/Siv3D/OpenSiv3D/issues/1089), [#1090](https://github.com/Siv3D/OpenSiv3D/pull/1090))
	- Added `Point3D` type ([#1073](https://github.com/Siv3D/OpenSiv3D/issues/1073), [#1074](https://github.com/Siv3D/OpenSiv3D/pull/1074))
	- Added `operator%` and `operator%=` to `Point` type ([#1055](https://github.com/Siv3D/OpenSiv3D/issues/1055), [#1058](https://github.com/Siv3D/OpenSiv3D/pull/1058))

	#### Specification Changes
	- Improved consistency of arguments for some `ScreenCapture::` functions ([#1080](https://github.com/Siv3D/OpenSiv3D/issues/1080))
	- Updated third-party libraries ([#1100](https://github.com/Siv3D/OpenSiv3D/issues/1100))

	#### Performance Improvements
	- Significantly improved preload cost of SDF/MSDF fonts ([#1095](https://github.com/Siv3D/OpenSiv3D/issues/1095), [#1096](https://github.com/Siv3D/OpenSiv3D/pull/1096))
	- Significantly improved runtime performance of `Image::clipped()` etc. ([#1087](https://github.com/Siv3D/OpenSiv3D/issues/1087), [#1108](https://github.com/Siv3D/OpenSiv3D/pull/1108))
	- Fixed inefficiency where `Shape2D::indices()` returned by value instead of reference ([#1065](https://github.com/Siv3D/OpenSiv3D/issues/1065), [#1071](https://github.com/Siv3D/OpenSiv3D/pull/1071))
	- Added efficient member function overloads for rvalue `Array`, `String`, `Polygon` etc. ([#1059](https://github.com/Siv3D/OpenSiv3D/issues/1059), [#1060](https://github.com/Siv3D/OpenSiv3D/issues/1060), [#1064](https://github.com/Siv3D/OpenSiv3D/pull/1064))

	#### Bug Fixes
	- Fixed noise in complex SDF/MSDF font characters ([#1082](https://github.com/Siv3D/OpenSiv3D/issues/1082), [#1096](https://github.com/Siv3D/OpenSiv3D/pull/1096))
	- Fixed a bug where `.pressed()` could return `true` when not pressing keys while window was inactive ([#1083](https://github.com/Siv3D/OpenSiv3D/issues/1083))
	- Fixed rendering anomaly in some cases of `RoundRect::drawShadow()` in v0.6.11 ([#1076](https://github.com/Siv3D/OpenSiv3D/issues/1076))
	- Fixed incorrect results from `RandomHSV(hMinMax, sMinMax, vMinMax)` ([#1084](https://github.com/Siv3D/OpenSiv3D/issues/1084), [#1088](https://github.com/Siv3D/OpenSiv3D/pull/1088))
	- Fixed runtime error when passing a string consisting only of whitespace to `String::trimmed()` ([#1101](https://github.com/Siv3D/OpenSiv3D/issues/1101))
	- Improved IME behavior on Windows ([#1104](https://github.com/Siv3D/OpenSiv3D/issues/1104), [#1107](https://github.com/Siv3D/OpenSiv3D/pull/1107))
	- Fixed runtime error when `P2Body` has an empty `Polygon` ([#1075](https://github.com/Siv3D/OpenSiv3D/issues/1075))
	- Fixed typo: `Wave::MaxSampleRate` was `Wave::MaxSamlpeRate` ([#1105](https://github.com/Siv3D/OpenSiv3D/pull/1105))
	- Fixed typo: `PhongMaterial::ambientColor` was `PhongMaterial::amibientColor` ([#1105](https://github.com/Siv3D/OpenSiv3D/pull/1105))
	- Fixed compile error with `AsyncTask::wait_until()` ([#1068](https://github.com/Siv3D/OpenSiv3D/issues/1068))
	- Fixed typos in some shader files ([#1105](https://github.com/Siv3D/OpenSiv3D/pull/1105))
	- Fixed documentation errors ([#1054](https://github.com/Siv3D/OpenSiv3D/pull/1054))

	#### Contributions
	- [Raclamusi](https://github.com/Raclamusi): **Implementation of efficient member function overloads for rvalue `Array` and other classes**, **Acceleration of `Image::clipped()` etc.**, fixing return value issues in some functions
	- [yama-can](https://github.com/yama-can): **Implementation of `Rect::skewedX()` etc.**, **Implementation of fade time specification in `SceneManager::init()`**
	- [comefrombottom](https://github.com/comefrombottom): **Implementation of `Image::rotate90(n)` etc.**, **Implementation of `.rotate90()` for `Point` and `Vector2D`**
	- [ozone010](https://github.com/ozone010): **Added `operator%` and `operator%=` to `Point` type**
	- [voidproc](https://github.com/voidproc): Fixed typos in documentation and source code
	- [naga-karupi](https://github.com/naga-karupi): Fixed `RandomHSV()` bug
	- [sfpgmr](https://github.com/sfpgmr): Fixed internal code
	- [aoriika05](https://github.com/aoriika05): Fixed documentation


??? summary "v0.6.11 | 2023-08-11"

	#### New Features
	- Added 2D trail rendering feature ([#1006](https://github.com/Siv3D/OpenSiv3D/issues/1006), [#1043](https://github.com/Siv3D/OpenSiv3D/pull/1043))
		- [Sample](https://zenn.dev/link/comments/7de4b612bbc195)
	- Added class for easy handling of "9-slice" ([#1030](https://github.com/Siv3D/OpenSiv3D/issues/1030), [#1036](https://github.com/Siv3D/OpenSiv3D/pull/1036))
		- [Sample](https://zenn.dev/link/comments/f62cd1a9e45fb0)
	- Added simple 3D camera class `SimpleFollowCamera3D` that follows a target ([#1048](https://github.com/Siv3D/OpenSiv3D/issues/1048), [#1049](https://github.com/Siv3D/OpenSiv3D/pull/1049))
		- [Sample](https://zenn.dev/link/comments/7034d12f405985)
	- Added `GPT3_5_Turbo_16K` (`gpt-3.5-turbo-16k`) to OpenAI Chat API model constants ([#1050](https://github.com/Siv3D/OpenSiv3D/issues/1055))
	- Added option to `.drawShadow()` of `Rect`, `RectF`, `RoundRectF` to not fill the entire interior ([#1039](https://github.com/Siv3D/OpenSiv3D/issues/1039))
	- Added `Math::Max()`, `Math::Min()` to calculate max/min values between vector elements ([#1032](https://github.com/Siv3D/OpenSiv3D/issues/1032))
	- Added `Line::normalizedVector()` ([#1029](https://github.com/Siv3D/OpenSiv3D/issues/1029))
	- Added `Triangle::isClockwise()` ([#1028](https://github.com/Siv3D/OpenSiv3D/issues/1028))
	- Added `Transition::reset()` ([#1025](https://github.com/Siv3D/OpenSiv3D/issues/1025))
	- Added `Math::MoveTowards()` ([#1024](https://github.com/Siv3D/OpenSiv3D/issues/1024))
	- Added `Triangle::FromPoints(p0, p1, p2)` to create a clockwise `Triangle` from three vertices ([#1015](https://github.com/Siv3D/OpenSiv3D/issues/1015))
	- Added overload to `Quaternion::RollPitchYaw()` that takes `Vec3` as argument ([#1014](https://github.com/Siv3D/OpenSiv3D/issues/1014))

	#### Usability Improvements
	- Added feature to open troubleshooting web page when encountering common runtime errors for beginners ([#1007](https://github.com/Siv3D/OpenSiv3D/issues/1007), [#1034](https://github.com/Siv3D/OpenSiv3D/issues/1034), [#1035](https://github.com/Siv3D/OpenSiv3D/pull/1035))
	- Expanded detection of asset class initialization before engine startup ([#1047](https://github.com/Siv3D/OpenSiv3D/issues/1047))
	- Improved behavior of `SimpleGUI::TextBox()` ([#997](https://github.com/Siv3D/OpenSiv3D/issues/997))

	#### Specification Changes
	- Made `Circle::drawPie()`, `Circle::drawArc()`, `Circle::drawSegmentFromAngles()` draw clockwise triangles normally even when negative angle is specified ([#1042](https://github.com/Siv3D/OpenSiv3D/issues/1042))

	#### Performance Improvements
	- Reduced CPU cost of `.drawShadow()` for `Circle`, `Rect`, `RectF`, `RoundRect` by 20-50% ([#1037](https://github.com/Siv3D/OpenSiv3D/issues/1037))

	#### Bug Fixes
	- Fixed crash with mutex error on program termination on Windows ([#1033](https://github.com/Siv3D/OpenSiv3D/issues/1033))
	- Fixed bug where `DrawableText::getXAdvances()` result did not include newline characters ([#1038](https://github.com/Siv3D/OpenSiv3D/issues/1038))
	- Fixed misalignment of r in `RoundRect::drawShadow()` when specifying `spread` ([#1040](https://github.com/Siv3D/OpenSiv3D/issues/1040))
	- Fixed bug where outline and holes could be reversed in `Font::renderPolygon()` ([#1019](https://github.com/Siv3D/OpenSiv3D/issues/1019), [#1027](https://github.com/Siv3D/OpenSiv3D/pull/1027))
	- Fixed bug where tab spacing could be disrupted during `Font` drawing ([#1002](https://github.com/Siv3D/OpenSiv3D/issues/1002), [#1026](https://github.com/Siv3D/OpenSiv3D/pull/1026))
	- Fixed bug where `Circle::pieAsPolygon()` and `Circle::arcAsPolygon()` generated counterclockwise vertex `Polygon` ([#1041](https://github.com/Siv3D/OpenSiv3D/issues/1041))
	- Fixed build issues with some compilers ([#1021](https://github.com/Siv3D/OpenSiv3D/pull/1021))
	- Fixed Home, End key behavior in `SimpleGUI::TextBoxAt()` ([#999](https://github.com/Siv3D/OpenSiv3D/pull/999))
	- Fixed typos in documentation and arguments ([#1016](https://github.com/Siv3D/OpenSiv3D/issues/1016), [#1017](https://github.com/Siv3D/OpenSiv3D/pull/1017))

	#### Contributions
	- [Raclamusi](https://github.com/Raclamusi): **Fixed `Font::renderPolygon()` bug**, **Fixed `Font` tab spacing drawing bug**
	- [yksake](https://github.com/yksake): Improved `SimpleGUI::TextBoxAt()` behavior
	- [polyester-CTRL](https://github.com/polyester-CTRL): Fixed build issues with some compilers
	- [voidproc](https://github.com/voidproc): Fixed typos in documentation and arguments


??? summary "v0.6.10 | 2023-05-17"
	#### Important
	- Fixed build issues with Visual Studio 2022 17.6 ([#1011](https://github.com/Siv3D/OpenSiv3D/issues/1011))
		- For projects using v0.6.9 or earlier, [disable "Build ISO C++23 Standard Library Modules" from project properties :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D/issues/1011){:target="_blank"} to resolve the issue.

	#### New Features
	- Added Binary Values support for `JSON` ([#1010](https://github.com/Siv3D/OpenSiv3D/issues/1010))

	#### Performance Improvements
	- Improved implementation of `Optional::Optional(Optional<U>&&)` ([#1008](https://github.com/Siv3D/OpenSiv3D/issues/1008))

	#### Bug Fixes
	- Fixed bug where `ParseOr<double>()` and `ParseOpt<double>()` were performed with `float` precision ([#1009](https://github.com/Siv3D/OpenSiv3D/issues/1009))
	- Fixed bug where some elements of JSON created from binary formats (BSON/CBOR/MessagePack) could not be read properly ([#1010](https://github.com/Siv3D/OpenSiv3D/issues/1010))

??? summary "v0.6.9 | 2023-04-16"
	#### New Features
	- Added `SimpleTable` for drawing tables ([#988](https://github.com/Siv3D/OpenSiv3D/issues/988), [#991](https://github.com/Siv3D/OpenSiv3D/pull/991), [#992](https://github.com/Siv3D/OpenSiv3D/pull/992))
		- [Sample](https://zenn.dev/link/comments/6a2a05060c1dda)
	- Added multi-line text box `SimpleGUI::TextArea()` ([#789](https://github.com/Siv3D/OpenSiv3D/issues/789), [#994](https://github.com/Siv3D/OpenSiv3D/pull/994), [#996](https://github.com/Siv3D/OpenSiv3D/issues/996))
		- [Sample](https://zenn.dev/link/comments/98f2844dae5705)

	#### Specification Changes
	- Changed `Timer::isRunning()` to return `false` when it reaches 0 ([#987](https://github.com/Siv3D/OpenSiv3D/issues/987))
	- `Grid::resize(w, 0)` and `Grid::resize(0, h)` now preserve `w` and `h` ([#989](https://github.com/Siv3D/OpenSiv3D/issues/989))

	#### Performance Improvements
	- Improved implementation of `Array::fetch()` etc. ([#990](https://github.com/Siv3D/OpenSiv3D/pull/990))

	#### Bug Fixes
	- Fixed undefined behavior in `Array::fetch()` etc. ([#990](https://github.com/Siv3D/OpenSiv3D/pull/990))
	- Fixed bug in `Grid::assign()` ([#995](https://github.com/Siv3D/OpenSiv3D/issues/995))

	#### Contributions
	- [tomolatoon](https://github.com/tomolatoon): Fixed and improved `Array::fetch()` etc.
	- [m4saka](https://github.com/m4saka): Fixed typo in SimpleTable

??? summary "v0.6.8 | 2023-04-01"
	#### New Features
	- Added `System::LaunchFile(fileName)` to open a specified file with the default application ([#888](https://github.com/Siv3D/OpenSiv3D/issues/888), [#981](https://github.com/Siv3D/OpenSiv3D/pull/981))
		- [Sample](https://zenn.dev/link/comments/8c7c4076bfbb6d)
	- Added `System::LaunchFileWithTextEditor(fileName)` to open a specified file with a text editor ([#888](https://github.com/Siv3D/OpenSiv3D/issues/888), [#981](https://github.com/Siv3D/OpenSiv3D/pull/981))
		- [Sample](https://zenn.dev/link/comments/4b67ba5b069306)
	- Updated bundled color emoji from Unicode 14.0 to Unicode 15.0 ([#980](https://github.com/Siv3D/OpenSiv3D/issues/980))
		- [Sample](https://zenn.dev/link/comments/7e722e60bed8dc)
	- Updated bundled Material Design Icons from v6.5.95 to v7.2.96 (700 new icons added) ([#980](https://github.com/Siv3D/OpenSiv3D/issues/980))
		- [Sample](https://zenn.dev/link/comments/adaafc47825fb1)
	- Added functionality to handle OpenAI's Embeddings API ([#982](https://github.com/Siv3D/OpenSiv3D/issues/982))
		- [Sample](https://zenn.dev/link/comments/6a8483ccd76a7f)
	- Added functions to get user's computer name, username, and language ([#678](https://github.com/Siv3D/OpenSiv3D/issues/678), [#968](https://github.com/Siv3D/OpenSiv3D/issues/968), [#974](https://github.com/Siv3D/OpenSiv3D/pull/974))
		- [Sample](https://zenn.dev/link/comments/381ae3d6c2b201)
	- Added functions to get drive information (Windows version) ([#709](https://github.com/Siv3D/OpenSiv3D/issues/709), [#978](https://github.com/Siv3D/OpenSiv3D/pull/978))
		- [Sample](https://zenn.dev/link/comments/8d3df3d50eb1fa)
	- Added `Network::IsConnected()` to check internet connection ([#975](https://github.com/Siv3D/OpenSiv3D/issues/975), [#976](https://github.com/Siv3D/OpenSiv3D/pull/976))

	#### Usability Improvements
	- Items in `SimpleGUI::ListBox()` can now be selected with up/down arrow keys ([#984](https://github.com/Siv3D/OpenSiv3D/issues/984))

	#### Bug Fixes
	- Fixed bug where fallback emoji were not displayed correctly under certain conditions ([#971](https://github.com/Siv3D/OpenSiv3D/issues/971), [#973](https://github.com/Siv3D/OpenSiv3D/pull/973))
	- Fixed bug where scrollbar thumb in `SimpleGUI::ListBox()` became too thin or disappeared when there were many items ([#985](https://github.com/Siv3D/OpenSiv3D/issues/985))

	#### Contributions
	- [Raclamusi](https://github.com/Raclamusi): **Fixed emoji font fallback bug**


??? summary "v0.6.7 | 2023-03-18"
	#### New Features
	- Added functionality to handle OpenAI API (Chat, Image) ([#957](https://github.com/Siv3D/OpenSiv3D/issues/957))
		- [Sample](https://zenn.dev/link/comments/39ca09ff7febbf)
	- Added functionality to handle OSC communication ([#515](https://github.com/Siv3D/OpenSiv3D/issues/515), [#919](https://github.com/Siv3D/OpenSiv3D/issues/919), [#922](https://github.com/Siv3D/OpenSiv3D/pull/922))
		- [Sample](https://zenn.dev/link/comments/37cb05690d7a9c)
	- Added functions to draw a circle cut by a secant: `Circle::drawSegment()`, `Circle::drawSegmentFromAngles()` ([#956](https://github.com/Siv3D/OpenSiv3D/issues/956))
		- [Sample](https://zenn.dev/link/comments/3deaea6384f9e9)
	- Added functions to draw rectangles with diagonal gradients ([#955](https://github.com/Siv3D/OpenSiv3D/issues/955))
		- [Sample](https://zenn.dev/link/comments/b184c90a6682d5)
	- SimpleMenuBar now supports item checkmarks ([#948](https://github.com/Siv3D/OpenSiv3D/issues/948))
		- [Sample](https://zenn.dev/link/comments/a1638e9fd4ca82)
	- Added `JSONValidator` class for JSON validation ([#931](https://github.com/Siv3D/OpenSiv3D/issues/931), [#959](https://github.com/Siv3D/OpenSiv3D/pull/959))
		- [Sample](https://zenn.dev/link/comments/9a60ef0856e4fa)
	- Added functions to directly get command line arguments: `System::GetArgc()`, `System::GetArgv()` ([#964](https://github.com/Siv3D/OpenSiv3D/issues/964))
		- [Sample](https://zenn.dev/link/comments/9c62e50a751464)
	- Addon execution priority can now be specified separately for `update()` and `draw()` ([#949](https://github.com/Siv3D/OpenSiv3D/issues/949))
		- [Sample](https://zenn.dev/link/comments/d5204d4530f196)
	- Enhanced functions related to SimpleHTTP asynchronous requests ([#911](https://github.com/Siv3D/OpenSiv3D/issues/911), [#962](https://github.com/Siv3D/OpenSiv3D/issues/962))
	- Added `Math::NormalizeAngle()` function to normalize angles to ranges [0, TwoPi) or [-Pi, Pi) ([#927](https://github.com/Siv3D/OpenSiv3D/issues/927))
	- Added `Periodic::Pulse0_1()` / `Periodic::Pulse1_1()` that return rectangular waves with specified duty ratio over time ([#966](https://github.com/Siv3D/OpenSiv3D/issues/966), [#967](https://github.com/Siv3D/OpenSiv3D/pull/967))
	- Added `Input::Serialize()`, `Input::Deserialize()` to serialize `Input` ([#920](https://github.com/Siv3D/OpenSiv3D/issues/920))
	- Added `postPresent()` to `IAddon` ([#942](https://github.com/Siv3D/OpenSiv3D/issues/942))
	- `TextEditState` now supports serialization (string only) ([#930](https://github.com/Siv3D/OpenSiv3D/issues/930))
	- Added validity checking to Base64 decoding ([#845](https://github.com/Siv3D/OpenSiv3D/issues/845), [#961](https://github.com/Siv3D/OpenSiv3D/pull/961))
	- `Math::Damp()`, `Math::SmoothDamp()` now support `ColorF` type ([#947](https://github.com/Siv3D/OpenSiv3D/pull/947))
	- Added `.contains()`, `.contains_if()` with the same functionality as `.includes()`, `.includes_if()` to `StringView`, `String`, `Array` for consistency with C++23 ([#944](https://github.com/Siv3D/OpenSiv3D/issues/944))
	- Enhanced `JSON` functionality ([#925](https://github.com/Siv3D/OpenSiv3D/pull/925), [#931](https://github.com/Siv3D/OpenSiv3D/issues/931), [#959](https://github.com/Siv3D/OpenSiv3D/pull/959))

	#### Usability Improvements
	- Added message box notification for asset creation/destruction issues every frame ([#926](https://github.com/Siv3D/OpenSiv3D/issues/926))

	#### Specification Changes
	- Renewed the default sample program "Hello, Siv3D" ([#940](https://github.com/Siv3D/OpenSiv3D/issues/940))
	- Updated Photon addon to be compatible with the latest Photon SDK ([#954](https://github.com/Siv3D/OpenSiv3D/issues/954))

	#### Performance Improvements
	- Accelerated Base64 decoding ([#845](https://github.com/Siv3D/OpenSiv3D/issues/845), [#961](https://github.com/Siv3D/OpenSiv3D/pull/961))
	- Improved performance of some member functions of `Array` and `MultiPolygon` ([#948](https://github.com/Siv3D/OpenSiv3D/pull/948))
	- Improved performance of `Array::includes()`, `Array::contains()` ([#945](https://github.com/Siv3D/OpenSiv3D/issues/945))

	#### Bug Fixes
	- Fixed issue where files could not be created in the current directory on macOS ([#963](https://github.com/Siv3D/OpenSiv3D/issues/963))
	- Fixed bug where `Optional` behavior differed from `std::optional` ([#938](https://github.com/Siv3D/OpenSiv3D/issues/938), [#939](https://github.com/Siv3D/OpenSiv3D/pull/939))
	- Fixed bug where `HTTPResponse` content was from before redirect ([#958](https://github.com/Siv3D/OpenSiv3D/issues/958))
	- Fixed compile error with `JSON::Load()` taking `IReader` as argument ([#937](https://github.com/Siv3D/OpenSiv3D/issues/937))
	- Fixed bug where `Polygon::calculateBuffer()`, `Polygon::calculateRoundBuffer()` could fail ([#965](https://github.com/Siv3D/OpenSiv3D/issues/965))
	- Improved mouse cursor behavior on `SimpleMenuBar` ([#950](https://github.com/Siv3D/OpenSiv3D/pull/950))
	- Fixed bug where sound did not play in `App/example/script/piano.as` script ([#935](https://github.com/Siv3D/OpenSiv3D/pull/935))
	- Fixed crash when drawing a joker card from default-constructed `PlayingCard::Pack` ([#921](https://github.com/Siv3D/OpenSiv3D/issues/921))
	- Fixed bug where `GeoJSONGeometry::get()` could not be used ([#933](https://github.com/Siv3D/OpenSiv3D/issues/933), [#934](https://github.com/Siv3D/OpenSiv3D/pull/934))
	- Fixed bugs in some serialization macros ([#923](https://github.com/Siv3D/OpenSiv3D/pull/923))
	- Fixed build issues on Arch Linux ([#917](https://github.com/Siv3D/OpenSiv3D/issues/917), [#918](https://github.com/Siv3D/OpenSiv3D/pull/918))

	#### Contributions
	- [nokotan](https://github.com/nokotan): **Updated Web version**
	- [tomolatoon](https://github.com/tomolatoon): **Added `JSONValidator`**, **Enhanced `JSON` functionality**
	- [m4saka](https://github.com/m4saka): **Fixed `Optional` bugs**
	- [Raclamusi](https://github.com/Raclamusi): **Improved Base64 decoding**, improved performance of `Array` etc.
	- [voidproc](https://github.com/voidproc): **Added Periodic functions**
	- [yksake](https://github.com/yksake): Improved SimpleMenuBar behavior, improved documentation
	- [sthairno](https://github.com/sthairno): Fixed `GeoJSONGeometry` bug
	- [Aikawa3311](https://github.com/Aikawa3311): Fixed script
	- [sfpgmr](https://github.com/sfpgmr): Fixed serialization API bug
	- [hashitaku](https://github.com/hashitaku): Fixed build on Arch Linux


??? summary "v0.6.6 | 2022-11-22"
	#### New Features
	- Added simple menu bar functionality ([#898](https://github.com/Siv3D/OpenSiv3D/issues/898))
		- [Sample](https://zenn.dev/link/comments/2deb08c8839b7c)
	- Added functionality to abort input processing ([#897](https://github.com/Siv3D/OpenSiv3D/issues/897))
		- [Sample](https://zenn.dev/link/comments/0199a3d4069432)
	- Added `OrderedTable` type as a replacement for `std::map` ([#909](https://github.com/Siv3D/OpenSiv3D/pull/909))
		- [Sample](https://zenn.dev/link/comments/310475d5bb43b5)
	- Enabled specifying vertical color gradients in `RoundRect::draw()` ([#906](https://github.com/Siv3D/OpenSiv3D/issues/906))
		- [Sample](https://zenn.dev/link/comments/b075516ffa7b4e)
	- Enabled specifying vertical color gradients in `Rect::drawFrame()`, `RectF::drawFrame()`, `RoundRect::draw()`, `RoundRect::drawFrame()` ([#906](https://github.com/Siv3D/OpenSiv3D/issues/906))
		- [Sample](https://zenn.dev/link/comments/ea380caf8b5979)
	- (Windows version) Added functionality to display task progress in taskbar ([#904](https://github.com/Siv3D/OpenSiv3D/issues/904))
		- [Sample](https://zenn.dev/link/comments/bb445a346947d3)
	- Added function to return the overlapping area of two rectangles as a rectangle ([#872](https://github.com/Siv3D/OpenSiv3D/issues/872))
		- [Sample](https://zenn.dev/link/comments/5fd7bc5a3814a8)
	- Added bullet mode to `P2Body` ([#901](https://github.com/Siv3D/OpenSiv3D/issues/901))
	- Time types now support `_fmt()` ([#894](https://github.com/Siv3D/OpenSiv3D/issues/894), [#895](https://github.com/Siv3D/OpenSiv3D/pull/895))
	- Added `Rect::Empty()`, `RectF::Empty()` to create empty rectangles ([#881](https://github.com/Siv3D/OpenSiv3D/issues/881))
	- Added `Rect::isEmpty()`, `Rect::operator bool()`, `RectF::isEmpty()`, `RectF::operator bool()` to check if rectangles are empty ([#879](https://github.com/Siv3D/OpenSiv3D/issues/879), [#880](https://github.com/Siv3D/OpenSiv3D/issues/880))
	- Added `Array::partition()`, `Array::stable_partition()` ([#869](https://github.com/Siv3D/OpenSiv3D/issues/869))
	- `Camera2DParameters`, `LicenseManager`, `LicenseInfo`, `XInput` can now be used in scripts ([#868](https://github.com/Siv3D/OpenSiv3D/issues/868))

	#### Usability Improvements
	- Performed refactoring to reduce header weight ([#883](https://github.com/Siv3D/OpenSiv3D/issues/883), [#886](https://github.com/Siv3D/OpenSiv3D/issues/886))
	- Resolved the issue on Windows where showing a message box during fullscreen would make the application unresponsive. A fallback message box is displayed within the scene ([#915](https://github.com/Siv3D/OpenSiv3D/issues/915))
	- Improved template argument deduction for `Array` ([#887](https://github.com/Siv3D/OpenSiv3D/issues/887))
	- Added `CITATION.cff` ([#882](https://github.com/Siv3D/OpenSiv3D/issues/882))
	- Added overloads for `Grid::resize()` ([#876](https://github.com/Siv3D/OpenSiv3D/issues/876))

	#### Specification Changes
	- Updated various third-party libraries ([#914](https://github.com/Siv3D/OpenSiv3D/issues/914))
	- Minor design adjustments to `PlayingCard` ([#905](https://github.com/Siv3D/OpenSiv3D/issues/905))
	- `PlayingCard.hpp` moved from experimental to official feature ([#885](https://github.com/Siv3D/OpenSiv3D/issues/885))

	#### Performance Improvements
	- Reduced memory consumption of `DisjointSet` ([#878](https://github.com/Siv3D/OpenSiv3D/issues/878))

	#### Bug Fixes
	- Fixed bugs and improved compatibility for Web version
	- Fixed issue where some constructors of `XMLReader` could not be used ([#896](https://github.com/Siv3D/OpenSiv3D/issues/896))
	- Fixed documentation ([#871](https://github.com/Siv3D/OpenSiv3D/issues/871), [#903](https://github.com/Siv3D/OpenSiv3D/issues/903))
	- Fixed issues with regex capture specification ([#893](https://github.com/Siv3D/OpenSiv3D/issues/893))
	- Fixed infinite loop bug when passing empty string to `String::removed(StringView)` ([#892](https://github.com/Siv3D/OpenSiv3D/pull/892))
	- Fixed issues with `Allocator` ([#889](https://github.com/Siv3D/OpenSiv3D/issues/889), [#891](https://github.com/Siv3D/OpenSiv3D/pull/891))
	- Fixed bug where `DisjointSet::operator bool()` returned inverted `true`/`false` ([#877](https://github.com/Siv3D/OpenSiv3D/pull/877))
	- Fixed issues with `_fmt()` support for various classes ([#873](https://github.com/Siv3D/OpenSiv3D/issues/873))
	- Fixed issue where `LineString::calculateBufferClosed()`, `LineString::calculateRoundBufferClosed()` sometimes did not close ([#870](https://github.com/Siv3D/OpenSiv3D/issues/870))

	#### Contributions
	- [nokotan](https://github.com/nokotan): **Updated Web version**
	- [MayFlyOvO](https://github.com/MayFlyOvO): **Added `OrderedTable`**
	- [Raclamusi](https://github.com/Raclamusi): Improvements and bug fixes for `Array`, `Allocator`, "fmt"
	- [AngelCase](https://github.com/AngelCase): Fixed `String::removed()` bug
	- [yunba28](https://github.com/yunba28): Improved documentation
	- [sknjpn](https://github.com/sknjpn): Improved documentation


??? summary "v0.6.5 | 2022-08-10"
	#### New Features
	- Added support for Visual Studio 2022 17.3 ([#859](https://github.com/Siv3D/OpenSiv3D/issues/859))
	- Added `LineString::extractLineString(double, CloseRing)` overload ([#866](https://github.com/Siv3D/OpenSiv3D/issues/866))
		- [Sample](https://zenn.dev/link/comments/883084a7d6a77d)
	- `JSON` now supports conversion to/from binary formats (BSON/CBOR/MessagePack) ([#842](https://github.com/Siv3D/OpenSiv3D/issues/842))
		- [Sample](https://zenn.dev/link/comments/baec402709773f)
	- Added `FileSystem::PathAppend()` to join file paths ([#825](https://github.com/Siv3D/OpenSiv3D/issues/825))
		- [Sample](https://zenn.dev/link/comments/d45decf3cb32f2)
	- Added member variables to `TextEditState` to detect input completion via Tab or Enter key ([#808](https://github.com/Siv3D/OpenSiv3D/issues/808))
		- [Sample](https://zenn.dev/link/comments/11eff1e53bffcb)
	- Added `Triangle::FromPoints()` to create an isosceles triangle from base center, apex, and base length ([#865](https://github.com/Siv3D/OpenSiv3D/issues/865))
	- Added `PercentEncode()` for percent-encoding strings ([#864](https://github.com/Siv3D/OpenSiv3D/issues/864))
	- Added overload to `NavMesh::query()` that takes result destination by reference ([#861](https://github.com/Siv3D/OpenSiv3D/issues/861))
	- Added `Dot()` and `Cross()` to `Math::`. Previously, member functions of `Vec2, Vec3` etc. had to be used ([#848](https://github.com/Siv3D/OpenSiv3D/issues/848))
	- Added functions to return only X or Y coordinates of rectangle edges and center ([#853](https://github.com/Siv3D/OpenSiv3D/issues/853))
	- Added member function to return coordinates at (relativeX, relativeY) when top-left of rectangle is (0, 0) and bottom-right is (1, 1) ([#850](https://github.com/Siv3D/OpenSiv3D/issues/850))
	- Updated bundled Font Awesome from 5.15.2 → 6.1.1 ([#846](https://github.com/Siv3D/OpenSiv3D/issues/846))
	- Added member functions to `Blob` ([#843](https://github.com/Siv3D/OpenSiv3D/issues/843))
	- Added `Font::height(double size)` ([#830](https://github.com/Siv3D/OpenSiv3D/issues/830))
	- Updated bundled monochrome Noto Emoji ([#816](https://github.com/Siv3D/OpenSiv3D/issues/816))
	- Added `.horizontalAspectRatio()` function to `Point`, `Float2`, `Vec2`, `Rect`, `RectF`, `Image`, `Texture`, `Emoji`, `Scene::`, `RoundRect` to return horizontal aspect ratio ([#810](https://github.com/Siv3D/OpenSiv3D/issues/810), [#812](https://github.com/Siv3D/OpenSiv3D/issues/812))
	- Added timestamp-related functions to `Multiplayer_Photon` ([#807](https://github.com/Siv3D/OpenSiv3D/issues/807))
	- Added `.joinRandomRoomOrCreate()` to `Multiplayer_Photon` ([#806](https://github.com/Siv3D/OpenSiv3D/issues/806))
	- Added `NotImplementedError` exception class ([#787](https://github.com/Siv3D/OpenSiv3D/issues/787))

	#### Usability Improvements
	- Improved CMake for Linux version ([#829](https://github.com/Siv3D/OpenSiv3D/pull/829))
	- Specified boost version range in Linux CMakeLists.txt ([#847](https://github.com/Siv3D/OpenSiv3D/pull/847))
	- Improved behavior of `SimpleGUI::TextBox()` ([#832](https://github.com/Siv3D/OpenSiv3D/pull/832), [#804](https://github.com/Siv3D/OpenSiv3D/issues/804))
	- Specified `= delete` for `BigInt operator ""_big(long double x)` to prevent misuse ([#826](https://github.com/Siv3D/OpenSiv3D/issues/826))
	- Added documentation to several header files

	#### Specification Changes
	- Improved string conversion for `BigFloat` ([#839](https://github.com/Siv3D/OpenSiv3D/issues/839))
	- Changed return value of `Multiplayer_Photon::getLocalPlayerID()` to `LocalPlayerID` ([#809](https://github.com/Siv3D/OpenSiv3D/issues/809))
	- Changed `AsyncHTTPTask::isReady` to const member function ([#805](https://github.com/Siv3D/OpenSiv3D/pull/805))
	- Updated various third-party libraries ([#801](https://github.com/Siv3D/OpenSiv3D/issues/801))
	- Updated engine files ([#817](https://github.com/Siv3D/OpenSiv3D/issues/817))

	#### Performance Improvements
	- Improved execution performance of `NavMesh::query()` ([#861](https://github.com/Siv3D/OpenSiv3D/issues/861))
	- Improved constructors of `HLSL` and `GLSL` classes ([#835](https://github.com/Siv3D/OpenSiv3D/issues/835))
	- Changed string arguments in `SimpleGUI` from `const String&` → `StringView` ([#827](https://github.com/Siv3D/OpenSiv3D/issues/827))
	- Improved execution performance when subtracting arithmetic types from `BigInt`, `BigFloat` ([#822](https://github.com/Siv3D/OpenSiv3D/issues/822))
	- Improved `constexpr` support for `Rect`, `RectF` ([#813](https://github.com/Siv3D/OpenSiv3D/issues/813))

	#### Bug Fixes
	- Fixed bug where `LineString::extractLineString()` could return incorrect results ([#862](https://github.com/Siv3D/OpenSiv3D/issues/862))
	- Fixed bug where `.calculateRoundBuffer()` of `LineString` with matching start and end points could fail ([#860](https://github.com/Siv3D/OpenSiv3D/issues/860))
	- Fixed issue where NULL was replaced with empty string by macro on macOS and Linux ([#858](https://github.com/Siv3D/OpenSiv3D/issues/858))
	- Fixed rendering corruption when passing invalid values to `RoundRect::drawFrame()` ([#856](https://github.com/Siv3D/OpenSiv3D/issues/856))
	- Fixed member function name from `.getVerticlaFOV()` → `.getVerticalFOV()` in `BasicCamera3D` ([#854](https://github.com/Siv3D/OpenSiv3D/pull/854))
	- Fixed compile failure bug with `Grid::choice()` ([#840](https://github.com/Siv3D/OpenSiv3D/issues/840))
	- Fixed buffer overrun bug in `Base64::Decode()` under certain conditions ([#837](https://github.com/Siv3D/OpenSiv3D/issues/837))
	- Fixed bug where `Parse<double>` was performed with `float` precision ([#831](https://github.com/Siv3D/OpenSiv3D/issues/831))
	- Fixed incorrect Line-to-Line Intersect, IntersectAt judgment under certain conditions ([#823](https://github.com/Siv3D/OpenSiv3D/pull/823))
	- Fixed comparison operator bugs in `BigInt`, `BigFloat` ([#821](https://github.com/Siv3D/OpenSiv3D/pull/821))
	- Fixed bug where `FileSystem::SelectFolder()` on macOS and Linux did not append `/` to the result ([#824](https://github.com/Siv3D/OpenSiv3D/issues/824))
	- Fixed bug where `FileSystem::FullPath()` result could be incorrect on macOS ([#824](https://github.com/Siv3D/OpenSiv3D/issues/824))
	- Fixed typo in `SFMT` header/folder name ([#818](https://github.com/Siv3D/OpenSiv3D/pull/818))
	- Fixed issue where `TCPClient` disconnection was not communicated to `TCPServer` on macOS ([#799](https://github.com/Siv3D/OpenSiv3D/issues/799))

	#### Contributions
	- [nokotan](https://github.com/nokotan): **Updated Web version**
	- [MurakamiShun](https://github.com/MurakamiShun): **Improved CMake for Linux version**
	- [m4saka](https://github.com/m4saka): **Fixed Line-to-Line Intersect, IntersectAt bugs**
	- [Raclamusi](https://github.com/Raclamusi): Improvements and bug fixes for `BigInt`, `BigFloat`, documentation improvements
	- [kestrel-90r](https://github.com/kestrel-90r): Fixed source file name typo
	- [ShivAlley](https://github.com/ShivAlley): Added math functions to `Math::`
	- [tas9n](https://github.com/tas9n): Improved `AsyncHTTPTask`
	- [ROCKTAKEY](https://github.com/ROCKTAKEY): Improved CMakeLists.txt
	- [yknishidate](https://github.com/yknishidate): Code improvements
	- [agehama](https://github.com/agehama): Documentation improvements
	- [curay168](https://github.com/curay168): Documentation improvements


??? summary "v0.6.4 | 2022-05-21"
	#### New Features
	- Added support for Visual Studio 2022 17.2 and later ([#790](https://github.com/Siv3D/OpenSiv3D/issues/790))
	- Added support for Xcode 13.3 and later ([#753](https://github.com/Siv3D/OpenSiv3D/issues/753))
	- Added `Multiplayer_Photon` addon (multiplayer functionality) that works with Photon SDK ([#734](https://github.com/Siv3D/OpenSiv3D/issues/734))
		- [Tutorial](https://zenn.dev/reputeless/scraps/03d951dddb53ab)
		- [Sample](https://zenn.dev/link/comments/10e5a407500f04)
	- Added UV transform to 3D standard vertex shader constant buffer ([#764](https://github.com/Siv3D/OpenSiv3D/issues/764))
		- [Sample](https://zenn.dev/link/comments/8247739e3be0fb)
	- Added `MeshData::RoundedBox()` ([#769](https://github.com/Siv3D/OpenSiv3D/issues/769))
		- [Sample](https://zenn.dev/link/comments/654aede40de72c)
	- Added functionality to dynamically write waveforms to playing audio ([#736](https://github.com/Siv3D/OpenSiv3D/issues/736))
		- [Sample](https://zenn.dev/link/comments/8af1a8f60deaa1)
	- Added option to disable notification sound in Windows toast notifications ([#748](https://github.com/Siv3D/OpenSiv3D/issues/748))
		- [Sample](https://zenn.dev/link/comments/c076465eed0f45)
	- Added `DisjointSet` (Union Find) ([#742](https://github.com/Siv3D/OpenSiv3D/issues/742))
		- [Sample](https://zenn.dev/link/comments/5a327d5c40b86f)
	- Made texture filter changeable in `Shader::LinearToScreen()` ([#762](https://github.com/Siv3D/OpenSiv3D/issues/762))
		- [Sample](https://zenn.dev/link/comments/81ffc408b80ac8)
	- Added `Polygon::addHole()` overload ([#786](https://github.com/Siv3D/OpenSiv3D/issues/786))
		- [Sample](https://zenn.dev/link/comments/3a5e307fa484c6)
	- Added option to avoid ligatures in `Font` ([#792](https://github.com/Siv3D/OpenSiv3D/issues/792))
		- [Sample](https://zenn.dev/link/comments/b4e3e69c91b3a0)
	- Added `Periodic::` functions that return range -1.0 to 1.0 ([#761](https://github.com/Siv3D/OpenSiv3D/issues/761))
		- [Sample](https://zenn.dev/link/comments/e321c3bf3fc6cc)
	- Added function to set custom trigger for reload in `ManagedScript` ([#768](https://github.com/Siv3D/OpenSiv3D/issues/768))
		- [Sample](https://zenn.dev/link/comments/9726f79f84c446)
	- Added functionality to get files included in `Script` ([#767](https://github.com/Siv3D/OpenSiv3D/issues/767))
		- [Sample](https://zenn.dev/link/comments/052f71ba876683)
	- Added `JSON::push_back()` ([#725](https://github.com/Siv3D/OpenSiv3D/issues/725))
	- Added more overloads for `String::replace()` ([#729](https://github.com/Siv3D/OpenSiv3D/issues/729))
	- Enabled specifying maximum level in `ImageProcessing::GenerateMips()` ([#763](https://github.com/Siv3D/OpenSiv3D/issues/763))
	- Made enum values displayable in scripts ([#774](https://github.com/Siv3D/OpenSiv3D/issues/774))
	- Added `OpenMode`, `TextEncoding`, `TextReader`, `TextWriter` to scripts ([#775](https://github.com/Siv3D/OpenSiv3D/issues/775))
	- Added `Parse` functions to scripts ([#782](https://github.com/Siv3D/OpenSiv3D/issues/782))
	- Added `INI` to scripts ([#783](https://github.com/Siv3D/OpenSiv3D/issues/783))
	- Added `Deserializer<MemoryViewReader>` ([#777](https://github.com/Siv3D/OpenSiv3D/issues/777))
	- Added `Serializer<Writer>::operator ->() const` ([#776](https://github.com/Siv3D/OpenSiv3D/issues/776))
	- Added overloads for `Geometry2D::Or()` ([#793](https://github.com/Siv3D/OpenSiv3D/issues/793))

	#### Usability Improvements
	- (Unofficial) Improved ARM builds ([#707](https://github.com/Siv3D/OpenSiv3D/issues/707))
	- Improved `SceneManager` code ([#750](https://github.com/Siv3D/OpenSiv3D/issues/750))
	- Enabled map construction in `NavMesh` constructor ([#785](https://github.com/Siv3D/OpenSiv3D/issues/785))
		- [Sample](https://zenn.dev/link/comments/af9b9deb7f205c)

	#### Specification Changes
	- Updated various third-party libraries ([#726](https://github.com/Siv3D/OpenSiv3D/issues/726), [#728](https://github.com/Siv3D/OpenSiv3D/issues/728), [#727](https://github.com/Siv3D/OpenSiv3D/issues/727), [#731](https://github.com/Siv3D/OpenSiv3D/issues/731), [#756](https://github.com/Siv3D/OpenSiv3D/issues/756), [#757](https://github.com/Siv3D/OpenSiv3D/issues/757), [#758](https://github.com/Siv3D/OpenSiv3D/issues/758), [#759](https://github.com/Siv3D/OpenSiv3D/issues/759), [#773](https://github.com/Siv3D/OpenSiv3D/issues/773), [#760](https://github.com/Siv3D/OpenSiv3D/issues/760))
	- Changed specification of `Polygon::addHole()` ([#786](https://github.com/Siv3D/OpenSiv3D/issues/786))
	- Updated engine / example files ([#740](https://github.com/Siv3D/OpenSiv3D/issues/740))

	#### Bug Fixes
	- Fixed bug in `Circle::boundingRect()` ([#718](https://github.com/Siv3D/OpenSiv3D/issues/718))
	- Fixed return value of `SimpleAnimation::isDone()` ([#710](https://github.com/Siv3D/OpenSiv3D/issues/710))
	- Fixed use after move bug in `TextEditState::TextEditState(String&& defaultText)` ([#703](https://github.com/Siv3D/OpenSiv3D/issues/703))
	- Fixed bug where empty arrays could not be created with `JSON` class ([#723](https://github.com/Siv3D/OpenSiv3D/issues/723))
	- Fixed warning in `operator>>(basic_istream&, Color&)` ([#720](https://github.com/Siv3D/OpenSiv3D/issues/720))
	- Fixed failure of `System::EnumActiveMonitors()` in remote desktop environment ([#719](https://github.com/Siv3D/OpenSiv3D/issues/719))
	- Fixed bug where loading non-existent file with `TOMLReader` did not result in failure ([#732](https://github.com/Siv3D/OpenSiv3D/issues/732))
	- Fixed issue where message box could appear behind window on Windows ([#706](https://github.com/Siv3D/OpenSiv3D/issues/706))
	- Fixed script binding bugs ([#741](https://github.com/Siv3D/OpenSiv3D/issues/741))
	- Fixed bug where triangle orientation was reversed when 5th argument of `Shape2D::Stairs()` was `false` ([#708](https://github.com/Siv3D/OpenSiv3D/issues/708))
	- Fixed bug where `RectanglePacking` was not available on macOS ([#754](https://github.com/Siv3D/OpenSiv3D/issues/754))
	- Fixed `Image` and OpenCV integration in ARM builds ([#778](https://github.com/Siv3D/OpenSiv3D/issues/778))
	- Fixed out-of-bounds access bug that could occur in `SimpleGUI::ListBox()` ([#780](https://github.com/Siv3D/OpenSiv3D/issues/780))
	- Fixed `WaveSample` conversion bugs ([#795](https://github.com/Siv3D/OpenSiv3D/issues/795))

	#### Contributions
	- [nokotan](https://github.com/nokotan): **Updated Web version**
	- [tana](https://github.com/tana): **Improved ARM builds**
	- [mak1a](https://github.com/mak1a): **Implementation of Multiplayer_Photon**, fixed `SimpleAnimation::isDone()`
	- [Ryoga-exe](https://github.com/Ryoga-exe): Code improvements
	- [k-sunako](https://github.com/k-sunako): CMakeLists.txt improvements
	- [falrnd](https://github.com/falrnd): Fixed `Circle::boundingRect()`
	- [polyester-CTRL](https://github.com/polyester-CTRL): Improved OpenCV_Bridge
	- [yaito3014](https://github.com/yaito3014): Code improvements
	- [NachiaVivias](https://github.com/NachiaVivias): Improved `WaveSample`

	#### OpenSiv3D Challenge
	- #12 Photon: mak1a, Luke, sthairno


??? summary "v0.6.3 | 2021-11-14"
	#### New Features
	- Added support for Visual Studio 2022 ([#683](https://github.com/Siv3D/OpenSiv3D/issues/683))
	- Added list box to SimpleGUI ([#659](https://github.com/Siv3D/OpenSiv3D/issues/659))
	- Updated bundled Color Emoji to support Unicode 14.0 emoji ([#694](https://github.com/Siv3D/OpenSiv3D/issues/694))
	- Added icon font fallback to GUI font by default. Icons can be displayed in SimpleGUI text using icon codes like `U"\U000F0493 Setting"` ([#698](https://github.com/Siv3D/OpenSiv3D/issues/698))
	- Windows version's `System::EnumerateMonitors()` now retrieves more distinguishable monitor names ([#695](https://github.com/Siv3D/OpenSiv3D/issues/695))
	- Added `MeshGlyph` class to represent characters as 3D Mesh ([#680](https://github.com/Siv3D/OpenSiv3D/issues/680))
	- Added Leap Motion device support for Windows version ([#677](https://github.com/Siv3D/OpenSiv3D/issues/677))
	- Added support for mathematical constant τ (tau) representing 2π, such as `Math::Tau` and `0.5_tau` ([#673](https://github.com/Siv3D/OpenSiv3D/issues/673))
	- Enabled comparison operations between different types of `Optional` ([#670](https://github.com/Siv3D/OpenSiv3D/issues/670))
	- Added `.isNan()`, `.isInf()` member functions to `BigFloat` ([#669](https://github.com/Siv3D/OpenSiv3D/issues/669))
	- Implemented three-way comparison operator for `Array`, `Optional`, `BigInt`, `BigFloat` ([#658](https://github.com/Siv3D/OpenSiv3D/issues/658))
	- Implemented three-way comparison operator for `String`, `StringView`, `UUIDValue` ([#664](https://github.com/Siv3D/OpenSiv3D/issues/664))
	- Added overload for `DrawableText::regionBase()` ([#666](https://github.com/Siv3D/OpenSiv3D/issues/666))
	- Added `Platform::Windows::Keyboard::GetEvents()` for Windows to get keyboard input at a frequency higher than refresh rate ([#662](https://github.com/Siv3D/OpenSiv3D/issues/662))

	#### Performance Improvements
	- Delayed script engine initialization to reduce application initialization time when not using script features (saves tens of milliseconds) ([#657](https://github.com/Siv3D/OpenSiv3D/issues/657))
	- Simplified GLSL shader file license descriptions to slightly reduce file size ([#687](https://github.com/Siv3D/OpenSiv3D/issues/687))
	- Made `HalfFloat` member functions constexpr ([#689](https://github.com/Siv3D/OpenSiv3D/pull/689))

	#### Usability Improvements
	- Made engine initialization possible without including `NotoEmoji-Regular.ttf` in engine resources ([#684](https://github.com/Siv3D/OpenSiv3D/issues/684))
	- Added `engine/font/min/siv3d-min.woff` containing minimum required glyphs as an alternative to `NotoSansCJK-Regular.ttc.zstdcmp` or `NotoSansJP-Regular.otf.zstdcmp` ([#682](https://github.com/Siv3D/OpenSiv3D/issues/682))
	- Added more supported languages for Windows installer ([#671](https://github.com/Siv3D/OpenSiv3D/issues/671))

	#### Specification Changes
	- Removed `SIV3D_MAINLOOP_BEGIN` as Web version now supports normal main loop ([#674](https://github.com/Siv3D/OpenSiv3D/issues/674))
	- On macOS and Linux, logs are now output to `std::clog` and `std::cerr` instead of `std::cout` ([#630](https://github.com/Siv3D/OpenSiv3D/issues/630))
	- Updated `engine` and `example` folders ([#686](https://github.com/Siv3D/OpenSiv3D/issues/686))

	#### Bug Fixes
	- Fixed incorrect positioning bug in `DrawableText::draw(double, Arg:: ...)` and `DrawableText::region(double, Arg ...)` ([#665](https://github.com/Siv3D/OpenSiv3D/issues/665))
	- Fixed bug where `Window::IsToggleFullscreenEnabled()` always returned `false` on Windows ([#699](https://github.com/Siv3D/OpenSiv3D/issues/699))
	- Fixed bug where `HalfFloat{ 0.0f } == HalfFloat{ -0.0f }` was `false` ([#660](https://github.com/Siv3D/OpenSiv3D/issues/660))
	- Resolved warning when using `CircularBase<float, Oclock>` ([#667](https://github.com/Siv3D/OpenSiv3D/issues/667))

	#### Contributions
	- [nokotan](https://github.com/nokotan): **Updated Web version**
	- [tetsurom](https://github.com/tetsurom): Improved `HalfFloat` implementation, improved `Optional` implementation, improved `BigFloat` implementation, implemented three-way comparison operators for various classes


??? summary "v0.6.2 | 2021-09-29"
	#### Performance Improvements
	- Accelerated application startup on Windows ([#650](https://github.com/Siv3D/OpenSiv3D/issues/650), [#651](https://github.com/Siv3D/OpenSiv3D/issues/651))
	- Reduced memory / VRAM consumption ([#648](https://github.com/Siv3D/OpenSiv3D/issues/648))

	#### Bug Fixes
	- Fixed issue where frame rate could be lower than v0.4.3 when performing heavy drawing on Windows ([#652](https://github.com/Siv3D/OpenSiv3D/issues/652))
	- Moved stdafx.h in Windows project template to Header Files filter ([#653](https://github.com/Siv3D/OpenSiv3D/issues/653))


??? summary "v0.6.1 | 2021-09-21"
	#### New Features
	- Added `Graphics2D::SetSDFParameters(const TextStyle&)`, `Graphics2D::SetMSDFParameters(const TextStyle&)` for easily specifying parameters when drawing SDF / MSDF textures ([#647](https://github.com/Siv3D/OpenSiv3D/issues/647))

	#### Usability Improvements
	- Suppressed 2 IntelliSense warnings that occurred during build in Windows projects ([#643](https://github.com/Siv3D/OpenSiv3D/issues/643))
	- Added documentation

	#### Specification Changes
	- `Monitor` was renamed to `MonitorInfo` ([#649](https://github.com/Siv3D/OpenSiv3D/issues/649))
	- `UUID` was renamed to `UUIDValue`

	#### Bug Fixes
	- Fixed issue in v0.6.0 where left click was less likely to be detected when performing touch operations on Windows ([#645](https://github.com/Siv3D/OpenSiv3D/issues/645))
	- Fixed compile error when including `<Siv3D/Windows/Windows.hpp>` in v0.6.0 ([#644](https://github.com/Siv3D/OpenSiv3D/issues/644))
	- Fixed issue where `Platform::Windows::GetHWND()` was not implemented in v0.6.0 ([#646](https://github.com/Siv3D/OpenSiv3D/issues/646))
	- Made `MathParser` not throw exceptions when passed an empty string ([#641](https://github.com/Siv3D/OpenSiv3D/issues/641))


??? summary "v0.6.0 | 2021-09-18"
	#### New Features
	- Added support for basic 3D drawing
	- Added support for C++20, utilizing Concepts, Designated initialization, `[[nodiscard]]` for constructors, spaceship operator, more `constexpr`, new standard library features, etc.
	- Added experimental Web version implementation (see [OpenSiv3D for Web](https://siv3d.kamenokosoft.com/en/index) for details)
	- OpenGL backend is now available on Windows (see Tutorial 35 for details)
	- Added HTTP client functionality for asynchronous file downloads
	- Added HighDPI support by default
	- Added support for loading SVG files
	- Added support for loading MIDI files
	- Added `VideoTexture` class to handle videos as textures
	- Added functionality to get pen tablet input (pressure, tilt) on Windows
	- Custom vertex shaders can now be used in 2D drawing. Custom vertex and pixel shaders can also be used in 3D.
	- Added support for audio fade-in/fade-out (play, stop, volume, pan, speed) on all platforms
	- Added real-time audio filter functionality such as HPF, LPF, pitch shift
	- Character outlines and Polygons can now be obtained accurately
	- SDF / MSDF can now be specified as `Font` rendering format
	- Added support for `Font` scaled drawing, outlines, and shadows
	- Added support for streaming playback of audio files
	- Added regular expression functionality supporting `String` type
	- Added functionality to obfuscate strings embedded in executables
	- Added function to perform demangling
	- Added class that performs Kahan's summation algorithm
	- Added 128-bit integer types
	- `Stopwatch` and `Timer` can now use user-defined reference time interface `ISteadyClock`, making it easy to pause multiple `Stopwatch` and `Timer` collectively or make them progress slower/faster
	- `TimeProfiler` now provides more detailed statistics
	- Added functionality to load GeoJSON, a geospatial data exchange format
	- Added many mathematical constants
	- Added `JSON` class that integrates `JSONReader`, `JSONWriter`
	- Added `SimpleAnimation` class for simple keyframe animations
	- Added functions for statistical processing
	- Added functionality for color mapping based on values
	- Added many convenient member functions to vector classes
	- Added many convenient member functions to shape classes
	- Added heart shape, bidirectional arrow, Squircle shape to `Shape2D`
	- Added functionality to flexibly map textures to `Polygon`
	- Added option to allow 90° rotation in rectangle packing
	- Added functionality for homography transformation
	- Various random functions can now take random engines as arguments
	- Added UUID generation functionality
	- Added functionality to get environment variables
	- Added functionality to get command line arguments
	- More detailed information such as monitor physical size can now be obtained
	- Added detailed options for serial communication
	- Added text-to-speech functionality using Klatt method
	- Added support for reading WebP, TIFF image formats
	- Added support for reading Opus, AIFF, FLAC, MIDI, WMA audio formats
	- Image processing can now be applied to parts of images
	- Added GrabCut functionality
	- Improved QR code generation functionality
	- Improved `VideoWriter`
	- Sound font files can now be used
	- Added mouse cursor styles
	- Added function to get all key inputs
	- Asynchronous loading in asset management became more convenient
	- Added many example files
	- Navigation mesh became easier to use
	- Added `Spline2D` class
	- Added support for obtaining parts of shape outlines
	- Added support for shape Lerp
	- Added support for GPU-only triangle drawing
	- Added support for custom mouse cursors
	- Added functionality to group audio and adjust volume by group
	- Added support for getting Ogg Vorbis loop tags
	- Added Levenshtein distance functionality
	- Added concave hull functionality
	- Added flexible image decoder and encoder classes
	- Added random generation with closed/open intervals specified
	- Added build-time engine configuration functionality via SIV3D_SET()
	- Effect recursion is now possible
	- Added CJK fonts, `Print` now supports Chinese and Korean display
	- Added `VideoReader` class to load video files
	- Added 2D physics simulation functionality
	- Added Siv3D-kun pixel art assets
	- Added Siv3D-kun .obj 3D model file
	- Added `Image::stamp()`
	- `Line::drawDoubleHeadedArrow()` can now draw bidirectional arrows
	- Screenshot save shortcut key can now be customized
	- Significantly improved script functionality
	- Supported more combinations for 2D shape intersection detection
	- Added many 3D shape classes
	- Improved IME behavior on Linux
	- Added user addon functionality
	- Many other new features have been added

	#### Performance Improvements
	- Windows app startup time reduced by several hundred milliseconds
	- `Heterogeneous lookup` improves runtime performance when looking up `HashTable` or `HashSet` with string literals or `StringView`
	- File reading/writing, loading image and audio files became faster
	- Improved speed of `Parse` / `ParseOpt` / `ParseOr`

	#### Usability Improvements
	- Inline functions moved from .hpp files to .ipp files, making header files more readable
	- Windows projects now use precompiled files by default, speeding up builds

	#### Specification Changes
	- Many bool function parameters replaced with named YesNo types, improving code readability
	- `Optional` type improved to behave more like C++ standard `std::optional`
	- `HashTable` type improved to behave more like C++ standard `std::unordered_map`
	- `KDTree` can now be used with shorter notation
	- `ConcurrentTask` was renamed to `AsyncTask`
	- Child process creation functions integrated into `ChildProcess` class
	- `Unicode::FromWString()` was renamed to `Unicode::FromWstring()`
	- Floating-point `U"{}"`_fmt(x)` now displays all significant digits
	- `Time::Get~` now returns time since application start, not system uptime
	- `CustomStopwatch` was renamed to `VariableSpeedStopwatch`
	- `FileSystem::SpecialFolderPath()` was renamed to `FileSystem::GetFolderPath()`
	- `FileSystem::UniqueFilePath()` now uses UUID functionality to create names
	- `ByteArray` replaced with `Blob` and `MemoryReader`
	- `CSVData` was renamed to `CSV`
	- `INIData` was renamed to `INI`
	- `JSONReader`, `JSONWriter` integrated into `JSON`
	- `EasingController` was renamed to `EasingAB`
	- `Sprite` replaced with `Buffer2D`
	- Index arrays now use `TriangleIndex`
	- `MessageBox` specification changed
	- Toast notification specification changed
	- Object detection functionality replaced with `CascadeClassifier`
	- `Key` became `Input`
	- Updated emoji and icon datasets, significantly increasing available emoji and icon types
	- Extended maximum `Image` size from 8192px → 16384px per side
	- ConstantBuffer size 16 × N byte constraint removed
	- Parallel execution features can now be used without defining `SIV3D_CONCURRENT` macro
	- High DPI windows became default, `SIV3D_WINDOWS_HIGH_DPI` macro deprecated

	#### Bug Fixes
	- Fixed bugs in `Array` parallel functions
	- Resolved platform differences in `AsyncTask`
	- Fixed bugs in Windows `MakeDragDrop()`
	- Fixed PPM image loading bugs
	- Improved random reproducibility across platforms
	- Fixed QR code generation bugs

	#### Important Notes
	- Argument order of `Math::SmoothDamp()` has changed. Cannot be detected by compile errors
	- Font vertical writing feature temporarily unavailable
	- Natural language processing feature temporarily unavailable
	- `SimpleGUIManager` feature cancelled
	- `NoiseGenerator` class temporarily unavailable
	- Shift_JIS text files no longer supported
	- Scene resize mechanism changed (see Tutorial 15)
	- Emoji design changed
	- Random reproducibility not compatible with v0.4.3
	- 2D physics simulation changed to use cm as default unit
	- Drawing method per `Glyph` unit changed
	- Windows version automatically includes precompiled `<Siv3D.hpp>` in all source files. `# include <Siv3D.hpp>` in Main.cpp is effectively meaningless. If `# define NO_S3D_USING` is needed, do it in `stdafx.h` for precompiled header creation
	- `Audio` no longer holds data in `Wave` compatible format. `.getWave()` replaced with `.getSamples()`. `GlobalAudio::BusGetSamples()` is also available

	#### Contributions
	- [nokotan](https://github.com/nokotan): **Led Web version development**
	- [Ebishu-0309](https://github.com/Ebishu-0309): **Implemented many functions in `Geometry2D::`**, **Implementation of `Shape2D::Squircle()`**, implemented `.boundingRect()` for `Bezier2`, `Bezier3`, code improvements
	- [taotao54321](https://github.com/taotao54321): Fixed `Grid`, code improvements
	- [sthairno](https://github.com/sthairno): **Improved IME processing on Linux**
	- [itakawa](https://github.com/itakawa): **Provided Siv3D-kun .obj file**
	- [take-cheeze](https://github.com/take-cheeze): **Set up CI using GitHub Actions**
	- [Luke256](https://github.com/Luke256): Code improvements
	- [YASAI03](https://github.com/YASAI03): **Proposed and implemented HTTP client functionality `SimpleHTTP`**
	- [falrnd](https://github.com/falrnd): Improved `Geometry2D` intersection detection
	- [yurkth](https://github.com/yurkth): **Proposed and implemented `GeoJSON` related functionality**
	- [ianCK](https://github.com/ianCK): Code improvements
	- [lriki](https://github.com/lriki): **Provided Siv3D-kun pixel art assets**
	- [Ryoga-exe](https://github.com/Ryoga-exe): Fixed `Color::gamma()` bug
	- [sivboard](https://github.com/sivboard): Added script functionality implementation and bug fixes
	- [agehama](https://github.com/agehama): Fixed PPM image loading bug
	- [kurokoji](https://github.com/kurokoji): **Added Linux MessageBox**
	- [ichi-raven](https://github.com/ichi-raven): Code improvements
	- [azaika](https://github.com/azaika): **Design and implementation of `JSON` class**

	#### OpenSiv3D Challenge
	- #01 Statistical functions: Shirasaka, Makia, fal_rnd
	- #03 `Shape2D::Heart`: Vegetable Juice, Teyaino
	- #04 2D shape intersection detection: Ebishu, fal_rnd, Kitsunebi
	- [#05 Squircle](https://zenn.dev/link/comments/ed180236e76181): Ebishu, Ryoga.exe
	- [#07 Countries and cities](https://zenn.dev/link/comments/76224190023391): torin (yurkth)
	- [#10 `OutlineGlyph` to `Array<Polygon>`](https://zenn.dev/link/comments/e75e5e80eee914): Ebishu, fal_rnd


## v0.4 Generation

??? summary "v0.4.3 | 2020-04-11"


## Previous Versions
- [旧 Siv3D 更新履歴 :material-open-in-new:](https://github.com/Siv3D/Reference-JP/wiki/%E6%9B%B4%E6%96%B0%E5%B1%A5%E6%AD%B4){:target="_blank"}