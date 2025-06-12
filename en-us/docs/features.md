# Siv3D Features

This is a list of major features provided by Siv3D.

## Graphics
- Various 2D graphics features
- Basic 3D graphics features (Wavefront OBJ, several basic shapes)
- Custom vertex and pixel shaders (HLSL, GLSL)
- Text rendering (Bitmap, SDF, MSDF)
- Image formats (PNG, JPEG, BMP, SVG, GIF, Animated GIF, TGA, PPM, WebP, TIFF)
- Unicode 15.1 emojis and over 7,000 types of icons
- Video playback
- Image processing

## Audio
- Various audio formats (WAVE, MP3, AAC, OggVorbis, Opus, MIDI, WMA, FLAC, AIFF)
- Volume, pan, speed, and pitch adjustment
- Streaming playback (WAVE, MP3, OggVorbis)
- Writing waveforms to buffers during playback
- Fade in, fade out
- Looping
- Mixing bus
- Filter processing (low-pass filter, high-pass filter, echo, reverb)
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
- XInput gamepad
- Pen tablet
- Leap Motion

## Window
- Full-screen mode
- High DPI support
- Window styles (resizable, frameless)
- File dialogs
- Drag & drop
- Message boxes
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
- Coordinate system classes
- 2D / 3D intersection detection and intersection point calculation
- 2D / 3D geometric calculations
- Rectangle packing
- Plane subdivision
- Linear and gamma color spaces
- Pseudo-random number generators
- Interpolation, easing, smoothing
- Perlin noise
- Math expression parser
- Navigation mesh
- Extended numeric types (`HalfFloat`, `int128`, `uint128`, `BigInt`, `BigFloat`)

## String Processing
- String classes (`String`, `StringView`)
- Unicode conversion (UTF-8 / UTF-16 / UTF-32)
- Regular expressions
- `{fmt}` style string formatting
- Text file reading and writing
- CSV / INI / JSON / XML / TOML parsers
- CSV / INI / JSON output
- JSON validation

## Other Features
- Basic GUI (buttons, sliders, radio buttons, checkboxes, text boxes, text areas, list boxes, color pickers, menu bars, tables)
- 2D physics engine integration (Box2D)
- Array classes (`Array`, `Grid`)
- Kd-tree
- Disjoint Set Union
- Asynchronous file loading
- Data compression (zlib, Zstandard)
- Scene transitions
- File system
- Directory monitoring
- QR codes
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
- OpenAI API (Chat, Vision, Image, Embedding)

<small>(*Some features are supported on specific platforms only)</small>