# Siv3D の機能

Siv3D が提供する機能の一覧です。

## グラフィックス
- Advanced 2D graphics
- Basic 3D graphics (Wavefront OBJ, primitive shapes)
- Custom vertex / pixel shaders (HLSL, GLSL)
- Text rendering (Bitmap, SDF, MSDF)
- PNG, JPEG, BMP, SVG, GIF, Animated GIF, TGA, PPM, WebP, TIFF
- Unicode 14.0 emojis and 7,000+ icons
- Image processing
- Video rendering

## オーディオ
- WAVE, MP3, AAC, OggVorbis, Opus, MIDI, WMA*, FLAC*, AIFF*
- Adjustable volume, pan, play speed and pitch
- File streaming (WAVE, MP3, OggVorbis)
- Dynamic audio buffer ✨new in v0.6.4
- Fade in and fade out
- Looping
- Mixing busses
- Filters (LPF, HPF, echo, reverb)
- FFT
- SoundFont rendering
- Text to speech

## 入力デバイス
- Mouse
- Keyboard
- Gamepad
- Webcam
- Microphone
- Joy-Con / Pro Controller
- XInput
- Digital drawing tablet
- Leap Motion

## ウィンドウ
- Fullscreen mode
- High DPI support
- Window styles (sizable, borderless)
- File dialog
- Drag & drop
- Message box
- Toast notification

## ネットワークと通信
- HTTP client
- Multiplayer (Photon SDK) ✨new in v0.6.4
- TCP communication
- Serial communication
- Interprocess communication (pipe)

## 数学
- Vector and matrix classes (`Point`, `Float2`, `Vec2`, `Float3`, `Vec3`, `Float4`, `Vec4`, `Mat3x2`, `Mat3x3`, `Mat4x4`, `SIMD_Float4`, `Quaternion`)
- 2D shape classes (`Line`, `Circle`, `Ellipse`, `Rect`, `RectF`, `Triangle`, `Quad`, `RoundRect`, `Polygon`, `MultiPolygon`, `LineString`, `Spline2D`, `Bezier2`, `Bezier3`)
- 3D shape classes (`Plane`, `InfinitePlane`, `Sphere`, `Box`, `OrientedBox`, `Ray`, `Line3D`, `Triangle3D`, `ViewFrustum`, `Disc`, `Cylinder`, `Cone`)
- Color classes (`Color`, `ColorF`, `HSV`)
- Polar / cylindrical / spherical coordinates system
- 2D / 3D shape intersection
- 2D / 3D geometry processing
- Rectangle packing
- Planar subdivisions
- Linear and gamma color space
- Pseudo random number generators
- Interpolation, easing, and smoothing
- Perlin noise
- Math parser
- Navigation mesh
- Extended arithmetic types (`HalfFloat`, `int128`, `uint128`, `BigInt`, `BigFloat`)

## 文字列処理
- Advanced String class (`String`, `StringView`)
- Unicode conversion
- Regular expression
- `{fmt}` style text formatting
- Text reader / writer classes
- CSV / INI / JSON / XML / TOML reader classes
- CSV / INI / JSON writer classes

## その他
- Basic GUI (button, slider, radio buttons, checkbox, text box, color picker, list box)
- Integrated 2D physics engine (Box2D)
- Advanced array / 2D array classes (`Array`, `Grid`)
- Kd-tree
- Disjoint Set Union ✨new in v0.6.4
- Asynchronous asset file streaming
- Data compression (zlib, Zstandard)
- Transitions between scenes
- File system
- Directory watcher
- QR code reader / writer
- GeoJSON
- Date and time
- Stopwatch and timer
- Logging
- Serialization
- UUID
- Child process
- Clipboard
- Power status
- Scripting (AngelScript)

<small>（\*一部の機能は特定のプラットフォームのみでのサポートです）</small>