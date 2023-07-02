# Features of Siv3D

Here is a list of the main features provided by Siv3D.

## Graphics
- Advanced 2D graphics
- Basic 3D graphics (Wavefront OBJ, several basic shapes)
- Custom vertex & pixel shaders (HLSL, GLSL)
- Text rendering (Bitmap, SDF, MSDF)
- Image formats (PNG, JPEG, BMP, SVG, GIF, Animated GIF, TGA, PPM, WebP, TIFF)
- Over 7,000 icons and Unicode 15.0 emojis
- Image processing
- Video rendering

## Audio
- Audio formats (WAVE, MP3, AAC, OggVorbis, Opus, MIDI, WMA, FLAC, AIFF)
- Volume, pan, speed, and pitch adjustments
- Streaming playback (WAVE, MP3, OggVorbis)
- Writing waveforms to the buffer during playback
- Fade-in, fade-out
- Looping
- Mixing bus
- Filter processing (Low-pass filter, High-pass filter, Echo, Reverb)
- FFT
- SoundFont rendering
- Text-to-speech

## Input Devices
- Mouse
- Keyboard
- Gamepad
- Webcam
- Microphone
- Joy-Con / Pro Controller
- XInput Gamepad
- Pen Tablet
- Leap Motion

## Window
- Full-screen mode
- High DPI support
- Window styles (resizeable, frameless)
- File dialog
- Drag & drop
- Message box
- Toast notifications

## Network and Communication
- HTTP client
- Multiplayer (Photon SDK)
- TCP communication
- Serial communication
- Inter-process communication (pipe)
- OSC (Open Sound Control) communication

## Mathematics
- Vector and matrix classes (`Point`, `Float2`, `Vec2`, `Float3`, `Vec3`, `Float4`, `Vec4`, `Mat3x2`, `Mat3x3`, `Mat4x4`, `SIMD_Float4`, `Quaternion`)
- 2D shape classes (`Line`, `Circle`, `Ellipse`, `Rect`, `RectF`, `Triangle`, `Quad`, `RoundRect`, `Polygon`, `MultiPolygon`, `LineString`, `Spline2D`, `Bezier2`, `Bezier3`)
- 3D shape classes (`Plane`, `InfinitePlane`, `Sphere`, `Box`, `OrientedBox`, `Ray`, `Line3D`, `Triangle3D`, `ViewFrustum`, `Disc`, `Cylinder`, `Cone`)
- Color classes (`Color`, `ColorF`, `HSV`)
- Curvilinear coordinate classes
- 2D / 3D intersection determination and intersection calculations
- 2D / 3D geometric calculations
- Rectangle packing
- Planar subdivision
- Linear color space and gamma color space
- Pseudo random number generator
- Interpolation, easing, smoothing
- Perlin noise
- Formula parser
- NavMesh
- Extended number types (`HalfFloat`, `int128`, `uint128`, `BigInt`, `BigFloat`)

## String Processing
- String classes (`String`, `StringView`)
- Unicode conversion (UTF-8 / UTF-16 / UTF-32)
- Regular expressions
- `{fmt}` style string formatting
- Reading and writing text files
- CSV / INI / JSON / XML / TOML parsers
- CSV / INI / JSON output
- JSON validation

## Other
- Basic GUI (buttons, sliders, radio buttons, checkboxes, text boxes, text areas, list boxes, color pickers, menu bars, tables)
- Integration of 2D physics engine (Box2D)
- Array classes (`Array`, `Grid`)
- Kd-tree
- Disjoint Set Union
- Asynchronous file loading
- Data compression (zlib, Zstandard)
- Scene transition
- File system
- Directory monitoring
- QR code
- GeoJSON
- Date and time
- Time measurement
- Logging
- Serialization
- UUID
- Child process management
- Clipboard
- Power management
- Scripting (AngelScript)
- OpenAI API (Chat, Image, Embedding)

<small>(*Some features are supported only on specific platforms)</small>