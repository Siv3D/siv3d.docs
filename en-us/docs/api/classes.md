# Siv3D Class List

Important and frequently used items are marked with ★.

## Numeric Types

| Type Name | Description |
|-----------|-------------|
| bool      | ★ Boolean type (`false` or `true`) |
| int8      | Signed 8-bit integer (-128 to 127) |
| uint8     | Unsigned 8-bit integer (0 to 255) |
| int16     | Signed 16-bit integer (-32,768 to 32,767) |
| uint16    | Unsigned 16-bit integer (0 to 65,535) |
| int32     | ★ Signed 32-bit integer (-2,147,483,648 to 2,147,483,647) |
| uint32    | ★ Unsigned 32-bit integer (0 to 4,294,967,295) |
| int64     | Signed 64-bit integer (-9,223,372,036,854,775,808 to 9,223,372,036,854,775,807) |
| uint64    | Unsigned 64-bit integer (0 to 18,446,744,073,709,551,615) |
| int128    | Signed 128-bit integer |
| uint128   | Unsigned 128-bit integer |
| float     | Single-precision floating-point number |
| double    | ★ Double-precision floating-point number |
| size_t    | ★ Unsigned 64-bit integer for expressing object sizes (0 to 18,446,744,073,709,551,615) |
| BigInt    | Arbitrary precision big integer |
| HalfFloat | Half-precision floating-point number |
| BigFloat  | Floating-point number with 100 significant digits |


## Characters and Strings

| Type Name     | Description |
|---------------|-------------|
| char8         | UTF-8 element (alias for `char`) |
| char16        | UTF-16 element (alias for `char16_t`) |
| char32        | ★ UTF-32 element (alias for `char32_t`) |
| String        | ★ String class with `char32` elements |
| StringView    | String view class |
| FilePath      | ★ File path string (alias for `String`) |
| FilePathView  | File path string view (alias for `StringView`) |
| URL           | URL string (alias for `String`) |
| URLView       | URL string view (alias for `StringView`) |


## Data Structures

| Type Name | Description |
|-----------|-------------|
| Array&lt;Type, Allocator&gt; | ★ Dynamic array (replacement for C++ standard library `std::vector`) |
| DisjointSet&lt;IndexType&gt; | Union-Find tree |
| Grid&lt;Type, Allocator&gt; | ★ Dynamic 2D array |
| HashSet&lt;Type, Hash, Eq, Alloc&gt; | ★ Hash table-based Set (replacement for C++ standard library `std::unordered_set`) |
| HashTable&lt;Key, Value, Hash, Eq, Alloc&gt; | ★ Hash table-based Map (replacement for C++ standard library `std::unordered_map`) |
| KDTree&lt;DatasetAdapter&gt; | KD-tree |
| KDTreeAdapter&lt;Dataset, PointType, ElementType, Dim&gt; | KD-tree information |
| None_t | Type representing invalid value in `Optional` type (alias for `std::nullopt_t`) |
| NonNull&lt;Pointer&gt; | Class managing pointers that don't hold null pointers |
| Optional&lt;Type&gt; | ★ Type that can represent invalid values (replacement for C++ standard library `std::optional`) |
| std::array&lt;Type, size_t&gt; | ★ Fixed-length array |
| StringCompare | Helper class for using strings as keys in hash tables |
| StringHash | Helper class for using strings as keys in hash tables |


## 2D Shapes

| Type Name | Description |
|-----------|-------------|
| Bezier2 | ★ Quadratic Bézier curve |
| Bezier3 | ★ Cubic Bézier curve |
| Circle | ★ Circle |
| Circular | ★ Circular coordinates (alias for `CircularBase<double, 0>`) |
| Circular0 | Circular coordinates (alias for `CircularBase<double, 0>`) |
| Circular0F | Circular coordinates (alias for `CircularBase<float, 0>`) |
| Circular3 | Circular coordinates (alias for `CircularBase<double, 3>`) |
| Circular3F | Circular coordinates (alias for `CircularBase<float, 3>`) |
| Circular6 | Circular coordinates (alias for `CircularBase<double, 6>`) |
| Circular6F | Circular coordinates (alias for `CircularBase<float, 6>`) |
| Circular9 | Circular coordinates (alias for `CircularBase<double, 9>`) |
| Circular9F | Circular coordinates (alias for `CircularBase<float, 9>`) |
| CircularBase&lt;Float, int32&gt; | Circular coordinates |
| CircularF | Circular coordinates (alias for `CircularBase<float, 0>`) |
| Ellipse | ★ Ellipse |
| Float2 | 2D vector with `float` elements |
| FloatQuad | Convex quadrilateral with `float` elements |
| FloatRect | Rectangle defined by top, bottom, left, right with `float` elements |
| Line | ★ Line segment |
| LineString | ★ Continuous line segments (replacement for `Array<Vec2>`) |
| Mat3x2 | ★ 3x2 matrix for affine transformations |
| Mat3x3 | 3x3 matrix for homography transformations |
| MultiPolygon | Collection of polygons (replacement for `Array<Polygon>`) |
| OffsetCircular | ★ Circular coordinates with offset (alias for `CircularBase<double, 0>`) |
| OffsetCircular0 | Circular coordinates with offset (alias for `CircularBase<double, 0>`) |
| OffsetCircular0F | Circular coordinates with offset (alias for `CircularBase<float, 0>`) |
| OffsetCircular3 | Circular coordinates with offset (alias for `CircularBase<double, 3>`) |
| OffsetCircular3F | Circular coordinates with offset (alias for `CircularBase<float, 3>`) |
| OffsetCircular6 | Circular coordinates with offset (alias for `CircularBase<double, 6>`) |
| OffsetCircular6F | Circular coordinates with offset (alias for `CircularBase<float, 6>`) |
| OffsetCircular9 | Circular coordinates with offset (alias for `CircularBase<double, 9>`) |
| OffsetCircular9F | Circular coordinates with offset (alias for `CircularBase<float, 9>`) |
| OffsetCircularBase | Circular coordinates with offset |
| OffsetCircularF | Circular coordinates with offset (alias for `CircularBase<float, 0>`) |
| Point | ★ 2D vector with `int32` elements |
| Polygon | ★ Polygon (can have holes) |
| Quad | ★ Convex quadrilateral |
| Rect | ★ Rectangle with `int32` elements |
| RectF | ★ Rectangle with `double` elements |
| RoundRect | ★ Rounded rectangle |
| Shape2D | ★ Polygon creation utility |
| Size | ★ Width and height with `int32` elements (alias for `Point`) |
| SizeF | ★ Width and height with `double` elements (alias for `Vec2`) |
| Spline2D | Spline curve |
| Triangle | ★ Triangle |
| Vec2 | ★ 2D vector with `double` elements |


## 3D Shapes

| Type Name | Description |
|-----------|-------------|
| Box | ★ Rectangular parallelepiped with edges parallel to XYZ axes |
| Cone | Cone |
| Cylinder | ★ Cylinder |
| Cylindrical | ★ Cylindrical coordinates (alias for `CylindricalBase<double>`) |
| CylindricalBase&lt;Float&gt; | Cylindrical coordinates |
| CylindricalF | Cylindrical coordinates (alias for `CylindricalBase<float>`) |
| Disc | Disc |
| Float3 | 3D vector with `float` elements |
| Float4 | 4D vector with `float` elements |
| InfinitePlane | Plane |
| Line3D | ★ 3D line segment |
| Mat4x4 | ★ 4x4 matrix |
| OrientedBox | ★ Oriented bounding box |
| Plane | ★ Finite XZ plane |
| Quaternion | ★ Quaternion |
| Ray | ★ Ray |
| Sphere | ★ Sphere |
| Spherical | ★ Spherical coordinates (alias for `SphericalBase<double>`) |
| SphericalBase&lt;Float&gt; | Spherical coordinates |
| SphericalF | Spherical coordinates (alias for `SphericalBase<float>`) |
| Triangle3D | 3D triangle |
| Vec3 | ★ 3D vector with `double` elements |
| Vec4 | 4D vector with `double` elements |
| ViewFrustum | View frustum |


## Colors

| Type Name    | Description |
|--------------|-------------|
| Color        | ★ RGBA color with `uint8` elements |
| ColorF       | ★ RGBA color with `double` elements |
| ColormapType | Colormap type |
| ColorOption  | Color space settings |
| HSV          | ★ HSVA color |


## Time Units

| Type Name     | Description |
|---------------|-------------|
| Days          | Time (days) (integer) |
| DaysF         | Time (days) (floating-point) |
| Hours         | Time (hours) (integer) |
| HoursF        | Time (hours) (floating-point) |
| Minutes       | Time (minutes) (integer) |
| MinutesF      | Time (minutes) (floating-point) |
| Seconds       | Time (seconds) (integer) |
| SecondsF      | Time (seconds) (floating-point) |
| Milliseconds  | Time (milliseconds) (integer) |
| MillisecondsF | Time (milliseconds) (floating-point) |
| Microseconds  | Time (microseconds) (integer) |
| MicrosecondsF | Time (microseconds) (floating-point) |
| Nanoseconds   | Time (nanoseconds) (integer) |
| NanosecondsF  | Time (nanoseconds) (floating-point) |
| Duration      | ★ Time (seconds) (floating-point) (alias for `SecondsF`) |


## Errors

| Type Name           | Description |
|---------------------|-------------|
| BadOptionalAccess   | Error for accessing invalid `Optional` |
| EngineError         | Engine internal error |
| Error               | ★ Error |
| NotImplementedError | Error for using unimplemented features |
| ParseError          | Parse function error |


## Various Classes

| Type Name | Description |
|-----------|-------------|
| ACLineStatus | Enumeration representing AC power connection status |
| AdaptiveThresholdMethod | Enumeration representing threshold calculation methods in adaptive thresholding |
| aligned_float4 | Native SIMD Float4 type |
| Allocator&lt;Type, size_t&gt; | Memory alignment-aware allocator |
| AnimatedGIFReader | Class for reading GIF animations |
| AnimatedGIFWriter | Class for writing GIF animations |
| ArcEmitter2D | 2D particle emitter (arc shape) |
| AssetHandle&lt;AssetType&gt; | Asset handle |
| AssetID&lt;AssetTag&gt; | Asset ID |
| AssetIDWrapper&lt;AssetTag&gt; | Asset ID |
| AssetState | Enumeration representing asset loading status |
| AsyncHTTPTask | Class managing asynchronous downloads |
| AsyncTask&lt;Type&gt; | Asynchronous processing class (replacement for C++ standard library `std::future`) |
| Audio | ★ Audio class |
| AudioAsset | ★ Audio asset |
| AudioAssetData | Audio asset definition |
| AudioFormat | Enumeration representing audio format |
| AudioGroup | Grouped audio |
| AudioLoopTiming | Audio loop position specification |
| BasicCamera2D | Basic class for 2D cameras |
| BasicCamera3D | Basic class for 3D cameras |
| BasicPerlinNoise&lt;Float&gt; | Perlin noise |
| BatteryStatus | Enumeration representing battery level |
| BinaryReader | ★ Binary file reading class |
| BinaryWriter | ★ Binary file writing class |
| BitmapGlyph | Bitmap glyph |
| Blend | Enumeration representing blend mode |
| BlendOp | Enumeration representing blend equation |
| BlendState | ★ Blend state |
| Blob | ★ Binary data |
| BorderType | Enumeration representing border handling in image filtering |
| Buffer2D | 2D drawing buffer |
| Byte | Type representing one byte |
| Camera2D | ★ 2D camera |
| Camera2DParameters | 2D camera settings |
| CameraControl | Enumeration representing camera control method |
| CascadeClassifier | Cascade-based image classifier |
| ChildProcess | Child process creation and management class |
| CircleEmitter2D | 2D particle emitter (circle shape) |
| CommonFloat&lt;T, U&gt; | Floating-point type used as calculation result between different numeric types |
| CommonFloat_t&lt;T, U&gt; | Floating-point type used as calculation result between different numeric types |
| CommonVector&lt;T, U, bool&gt; | Vector type used as calculation result between different numeric vector types |
| CommonVector_t&lt;T, U, bool&gt; | Vector type used as calculation result between different numeric vector types |
| ConstantBuffer&lt;Type&gt; | ★ Shader constant buffer |
| ConstantBufferBase | Shader constant buffer detailed information |
| ConstantBufferBinding | Shader constant buffer binding |
| CopyOption | Enumeration representing behavior during file copying |
| CPUInfo | CPU information |
| CSV | ★ CSV format data reading and writing class |
| CursorStyle | ★ Enumeration representing mouse cursor shape |
| Date | ★ Date |
| DateTime | ★ Date and time |
| DayOfWeek | Enumeration representing day of the week |
| DeadZone | Dead zone settings |
| DeadZoneType | Enumeration representing dead zone calculation method |
| DebugCamera3D | ★ Debug 3D camera |
| DefaultAllocator&lt;Type&gt; | Default allocator considering memory alignment |
| DepthFunc | Enumeration representing depth test function |
| DepthStencilState | Depth stencil state |
| Deserializer&lt;Reader&gt; | Class template for deserializer definition |
| detail::Gamepad_impl | ★ Gamepad. Return value of `Gamepad(…)` |
| detail::XInput_impl | ★ XInput gamepad. Return value of `XInput(…)` |
| DirectoryWatcher | Class for monitoring file operations within directories |
| DragItemType | Enumeration representing drag item type |
| DragStatus | Drag status |
| DrawableText | ★ Drawable text. Return value of `font(…)` |
| DroppedFilePath | Information about dropped file paths |
| DroppedText | Information about dropped text |
| DynamicMesh | Dynamic mesh that can be updated |
| DynamicTexture | ★ Dynamic texture that can be updated |
| EdgePreservingFilterType | Enumeration representing EdgePreservingFilter types |
| Effect | ★ Effect |
| Emission2D | Emission in 2D particles |
| Emoji | Standard emoji |
| EngineOption | Engine settings |
| ESSL | OpenGL ES Shading Language file |
| Exif | Exif data |
| FFTResult | ★ FFT result |
| FFTSampleLength | Enumeration representing FFT sample count |
| FileAction | Enumeration representing file operations |
| FileChange | File operation and file path |
| FileFilter | File extension filter |
| FloodFillConnectivity | Enumeration representing connectivity for image flood fill |
| Font | ★ Font |
| FontAsset | ★ Font asset |
| FontAssetData | Font asset definition |
| FontMethod | ★ Enumeration representing font rendering method |
| FontStyle | Enumeration representing font style |
| FormatData | String format information storage buffer |
| GamepadInfo | Gamepad information |
| GeoJSONBase | Base class for GeoJSON objects |
| GeoJSONFeature | GeoJSON Feature object |
| GeoJSONFeatureCollection | GeoJSON FeatureCollection object |
| GeoJSONGeometry | GeoJSON Geometry object |
| GeoJSONType | Enumeration representing object types defined in GeoJSON |
| GLSL | ★ GLSL file |
| Glyph | Glyph |
| GlyphCluster | Glyph cluster |
| GlyphIndex | Glyph index within font file (alias for `uint32`) |
| GlyphInfo | Glyph information |
| GMInstrument | ★ Enumeration representing instruments in General MIDI (GM) |
| GrabCut | Background extraction from images |
| GrabCutClass | Enumeration representing background and foreground in image background extraction |
| HLSL | ★ HLSL file |
| HTMLWriter | HTML document writing class |
| HTTPAsyncStatus | Enumeration representing download progress status |
| HTTPProgress | HTTP communication progress |
| HTTPResponse | HTTP response |
| HTTPStatusCode | Enumeration representing HTTP status codes |
| IAddon | Addon interface |
| IAsset | Asset base class |
| IAudioDecoder | Audio decoder interface |
| IAudioEncoder | Audio encoder interface |
| IAudioStream | Dynamic update audio interface |
| Icon | Standard icon |
| IEffect | ★ Effect interface |
| IEmitter2D | 2D particle emitter interface |
| IImageDecoder | Image decoder interface |
| IImageEncoder | Image encoder interface |
| Image | ★ Image data |
| ImageAddressMode | Enumeration representing image address mode |
| ImageFormat | Enumeration representing image format |
| ImageInfo | Image file information |
| ImagePixelFormat | Enumeration representing image pixel format |
| ImageROI | Region within image data |
| InfiniteList&lt;Type&gt; | Infinite list |
| INI | ★ INI format data reading and writing |
| INIKey | INI format data key |
| INISection | INI format data section |
| INIValueWrapper | INI format data helper class |
| Input | ★ Input object |
| InputCombination | Combination of `Input` |
| InputDeviceType | Enumeration representing input device type |
| InputGroup | Combination of `Input` |
| InterpolationAlgorithm | Enumeration representing image scaling method |
| IPv4Address | IPv4 address |
| IReader | Reader interface |
| IScene&lt;State, Data&gt; | ★ Scene interface for scene management |
| ISteadyClock | Time provider interface |
| IWriter | Writer interface |
| JoyCon | Joy-Con |
| KahanSummation&lt;Float&gt; | Utility for Kahan summation algorithm |
| KeyEvent | Key input details |
| KlattTTSParameters | Klatt method text-to-speech settings |
| KlattWaveform | Enumeration representing Klatt method text-to-speech waveform types |
| LanguageCode | Enumeration representing language codes |
| Leap::Bone | Bone information in Leap Motion |
| Leap::Connection | Handle for connected Leap device |
| Leap::Hand | Hand information in Leap Motion |
| Leap::TrackingMode | Enumeration representing tracking mode in Leap Motion |
| LetterCase | Enumeration representing uppercase/lowercase alphabets |
| LicenseInfo | License information |
| LineStyle | Line style |
| ListBoxState | ★ List box state |
| LogLevel | Enumeration representing log output detail level |
| LogType | Enumeration representing log output type |
| ManagedScript | Automatically managed script |
| MatchResults | Regular expression match results |
| Material | 3D object material |
| MathParser | Mathematical expression parser |
| MD5Value | MD5 |
| MemoryMappedFile | Memory-mapped file class |
| MemoryMappedFileView | Memory-mapped file view class |
| MemoryReader | Memory reading class |
| MemoryViewReader | Memory view reading class |
| MemoryWriter | Binary data writing class to memory |
| Mesh | ★ 3D mesh |
| MeshData | ★ 3D mesh vertex buffer and index buffer |
| MeshGlyph | Mesh glyph |
| MessageBoxResult | Enumeration representing message box result |
| MessageBoxStyle | Enumeration representing message box style |
| Microphone | ★ Microphone |
| MicrophoneInfo | Microphone information |
| MicrosecClock | Microsecond counter |
| MIDINote | MIDI note |
| MillisecClock | Millisecond counter |
| MiniScene&lt;State&gt; | Simplified scene manager |
| MixBus | Enumeration representing audio mix bus number |
| MMFOpenMode_if_Exists | Enumeration representing memory-mapped file open mode |
| MMFOpenMode_if_NotFound | Enumeration representing memory-mapped file open mode |
| Model | ★ 3D model |
| ModelMeshPart | Component of model parts that make up a 3D model |
| ModelObject | Model part that makes up a 3D model |
| MonitorInfo | Monitor information |
| MSDFGlyph | MSDF method glyph |
| MSL | Metal Shading Language file (not implemented) |
| MSRenderTexture | Multi-sample (anti-aliased) render texture |
| NamedParameter&lt;Tag, Type&gt; | Helper class for named arguments |
| NamedParameterHelper&lt;Tag&gt; | Helper class for named arguments |
| NativeFilePath | OS native file path representation type |
| NavMesh | Navigation mesh |
| NavMeshConfig | Navigation mesh settings |
| NormalComputation | Enumeration representing normal calculation method |
| OpenMode | Enumeration representing file open mode |
| OutlineGlyph | Outline glyph |
| Particle2D | 2D particle |
| ParticleSystem2D | 2D particle system |
| ParticleSystem2DParameters | 2D particle system settings |
| PerlinNoise | Perlin noise (alias for `BasicPerlinNoise<double>`) |
| PerlinNoiseF | Perlin noise (alias for `BasicPerlinNoise<float>`) |
| PhongMaterial | Phong model Material |
| PhongMaterialInternal | Internal format of Phong model Material |
| PianoKey | ★ Enumeration representing note names |
| Pipe | Enumeration representing pipe communication settings |
| PixelShader | ★ Pixel shader |
| PixelShaderAsset | Pixel shader asset |
| PixelShaderAssetData | Pixel shader asset definition |
| PlaceHolder_t | Placeholder type |
| Platform::Windows::HLSLCompileOption | HLSL compile options |
| PlayingCard::Card | Playing card number, suit, face/back data |
| PlayingCard::CardInfo | Playing card drawing information |
| PlayingCard::Pack | Class for creating playing cards |
| PlayingCard::Suit | Enumeration representing playing card suit (pattern mark) |
| PoissonDisk2D | 2D Poisson distribution class |
| PolygonEmitter2D | 2D particle emitter (polygon) |
| PolygonFailureType | Validation result for Polygon input vertices |
| PolygonGlyph | Polygon-based glyph |
| PowerStatus | System power status |
| ProController | Gamepad adapter for Pro Controller |
| ProfilerStat | Profiling information |
| QRContent | QR code scan result |
| QRErrorCorrection | Enumeration representing QR code error correction level |
| QRMode | Enumeration representing QR code mode |
| QRScanner | QR code reading class |
| RDTSCClock | CPU cycle counter |
| RectanglePack | Rectangle packing result |
| RectEmitter2D | 2D particle emitter (rectangle) |
| RegExp | Regular expression |
| RenderTexture | ★ Render texture |
| ResizeMode | ★ Enumeration representing scene auto-resize mode |
| ResourceOption | Enumeration representing resource path usage options |
| SamplerState | ★ Sampler state |
| SaturatedLinework&lt;TargetShape, URNG&gt; | Concentrated line drawing class |
| SceneManager&lt;State, Data&gt; | ★ Scene manager |
| ScopedColorAdd2D | 2D drawing color addition setting scope object |
| ScopedColorMul2D | 2D drawing color multiplication setting scope object |
| ScopedCustomShader2D | 2D drawing custom shader setting scope object |
| ScopedCustomShader3D | 3D drawing custom shader setting scope object |
| ScopedRenderStates2D | ★ 2D drawing render state setting scope object |
| ScopedRenderStates3D | 3D drawing render state setting scope object |
| ScopedRenderTarget2D | ★ 2D drawing render target setting scope object |
| ScopedRenderTarget3D | 3D drawing render target setting scope object |
| ScopedViewport2D | 2D drawing viewport setting scope object |
| ScopedViewport3D | 3D drawing viewport setting scope object |
| ScopeGuard&lt;Callback&gt; | Scope guard |
| Script | Script |
| ScriptCompileOption | Enumeration representing script compile options |
| ScriptFunction&lt;Ret(Args…)&gt; | Script function |
| ScriptModule | Script module |
| SDFGlyph | SDF method glyph |
| Serial | Serial communication |
| Serializer&lt;Writer&gt; | Class template for serializer definition |
| ShaderGroup | Class that absorbs differences between shader languages |
| ShaderStage | Enumeration representing shader stage |
| SIMD_Float4 | SIMD-enabled Float4 |
| SimpleAnimation | Keyframe animation helper class |
| Sky | Sky rendering engine (experimental) |
| SoundFont | Sound font |
| SpecialFolder | Enumeration representing special folders |
| SplineIndex | Position on `Spline2D` |
| Step&lt;T, N, S&gt; | ★ Loop utility |
| Step2D | ★ 2D loop unification utility |
| Stopwatch | ★ Stopwatch |
| Subdivision2D | 2D subdivision class |
| Subdivision2DEdgeType | 2D subdivision edge information |
| Subdivision2DPointLocation | Enumeration representing point position in 2D subdivision |
| SVG | SVG data |
| TCPClient | TCP client |
| TCPError | Enumeration representing TCP communication errors |
| TCPServer | TCP server |
| TCPSessionID | TCP session ID (alias for `uint64`) |
| TextEditState | ★ Text state within text box |
| TextEncoding | Text file encoding format |
| TextInputMode | Text input mode |
| TextReader | ★ Text file reading class |
| TextStyle | Text style |
| Texture | ★ Texture |
| TextureAddressMode | Enumeration representing texture address mode |
| TextureAsset | ★ Texture asset |
| TextureAssetData | Texture asset definition |
| TexturedCircle | Texture cropped to circle |
| TextureDesc | ★ Enumeration representing texture settings |
| TexturedQuad | Texture cropped to convex quadrilateral |
| TexturedRoundRect | Rounded rectangle region on texture |
| TextureFilter | ★ Texture filter |
| TextureFormat | Texture format |
| TexturePixelFormat | Enumeration representing texture pixel format |
| TextureRegion | ★ Rectangular region on texture |
| TextWriter | ★ Text file writing class |
| TimeProfiler | Profiler utility class |
| Timer | Timer |
| ToastNotificationID | Toast notification ID (alias for `int64`) |
| ToastNotificationItem | Toast notification settings |
| ToastNotificationState | Enumeration representing toast notification state |
| Transformer2D | ★ 2D coordinate transformation scope object |
| Transformer3D | 3D coordinate transformation scope object |
| Transition | Value transition helper class |
| TriangleIndex | Vertex indices that make up triangle (elements are `uint16`) |
| TriangleIndex32 | Vertex indices that make up triangle (elements are `uint32`) |
| Typeface | ★ Enumeration representing standard font types |
| Uncopyable | Copy-prohibited Mixin |
| UnderlineStyle | Enumeration representing underline style |
| unique_resource | RAII wrapper that calls specified deleter when object is destroyed |
| UserAction | ★ Enumeration representing user action to terminate application |
| UTF16toUTF32_Converter | Sequential conversion class from UTF-8 to UTF-32 |
| UTF32toUTF16_Converter | Sequential conversion class from UTF-16 to UTF-32 |
| UTF32toUTF8_Converter | Sequential conversion class from UTF-32 to UTF-8 |
| UTF8toUTF32_Converter | Sequential conversion class from UTF-32 to UTF-16 |
| UUIDValue | UUID |
| VariableSpeedStopwatch | Variable speed stopwatch |
| Vertex2D | Basic vertex data for 2D shapes |
| Vertex3D | Basic vertex data for 3D shapes |
| VertexShader | ★ Vertex shader |
| VertexShaderAsset | Vertex shader asset |
| VertexShaderAssetData | Vertex shader asset definition |
| VideoReader | Video file reading class |
| VideoTexture | Class that treats video like Texture |
| VideoWriter | Video file writing class |
| VoronoiFacet | Voronoi Facets |
| Wave | ★ Audio waveform data |
| WaveSample | Stereo waveform sample using single-precision floating-point |
| WaveSampleS16 | Stereo waveform sample using signed 16-bit integer |
| Webcam | ★ Web camera |
| WebcamInfo | Web camera information |
| WGSL | WebGPU Shading Language file |
| WindowState | Window state |
| WindowStyle | Enumeration representing window style |
| X86Features | CPU supported instruction set |
| XInputVibration | XInput controller vibration settings |
| XMLElement | XML element |
| XMLReader | XML reading class |
| YesNo&lt;Tag&gt; | Class template for YesNo |
| ZIPReader | ZIP archive file reading class |


## Random Numbers and Distributions

| Type Name | Description |
|-----------|-------------|
| BernoulliDistribution | Bernoulli distribution class |
| DefaultRNG | Default random number generator (alias for `PRNG::SFMT19937_64`) |
| DiscreteDistribution | Class generating probability distributions |
| HardwareRNG | Hardware-based non-deterministic random number generator |
| NormalDistribution | Normal distribution class |
| PRNG::SFMT19937_64 | SIMD-oriented Fast Mersenne Twister pseudo-random number generator |
| PRNG::SplitMix64 | SplitMix64 pseudo-random number generator |
| PRNG::Xoroshiro128Plus | xoshiro128+ pseudo-random number generator |
| PRNG::Xoroshiro128PlusPlus | xoroshiro128++ pseudo-random number generator |
| PRNG::Xoroshiro128StarStar | xoroshiro128** pseudo-random number generator |
| PRNG::Xoshiro128Plus | xoshiro128+ pseudo-random number generator |
| PRNG::Xoshiro128PlusPlus | xoshiro128++ pseudo-random number generator |
| PRNG::Xoshiro128StarStar | xoshiro128** pseudo-random number generator |
| PRNG::Xoshiro256Plus | xoshiro256+ pseudo-random number generator |
| PRNG::Xoshiro256PlusPlus | xoshiro256++ pseudo-random number generator |
| PRNG::Xoshiro256StarStar | xoshiro256** pseudo-random number generator |
| SmallRNG | Small-size default random number generator (alias for `PRNG::Xoshiro256PlusPlus`) |
| UniformDistribution | Uniform distribution class for integers and floating-point numbers |
| UniformIntDistribution | Integer uniform distribution class |
| UniformRealDistribution | Floating-point uniform distribution class |


## 2D Physics Simulation

| Type Name | Description |
|-----------|-------------|
| P2Body | ★ One unit of object existing in physics simulation world. Composed of 0 or more (usually 1 or more) parts (`P2Shape`) |
| P2BodyID | Type of unique ID given to object P2Body (alias for `uint32`) |
| P2BodyType | ★ Enumeration for object type |
| P2Circle | Part that makes up object (`P2Body`). Has circle shape |
| P2Collision | Information about all contacts acting on two objects, with up to 2 `P2Contact` |
| P2Contact | Information about collision between two objects |
| P2ContactPair | Pair of IDs (P2BodyID) when two objects are in contact |
| P2DistanceJoint | Distance joint connecting two objects |
| P2Filter | Can specify category bit flags for parts (`P2Shape`) to prevent interference with parts having specific bit flags |
| P2Line | Part that makes up object (`P2Body`). Has line segment shape |
| P2LineString | Part that makes up object (`P2Body`). Has continuous line segment shape |
| P2Material | Defines material of part (`P2Shape`) |
| P2MouseJoint | Mouse joint connecting two objects |
| P2PivotJoint | Pivot joint connecting two objects |
| P2Polygon | Part that makes up object (`P2Body`). Has polygon shape |
| P2Quad | Part that makes up object (`P2Body`). Has convex quadrilateral shape |
| P2Rect | Part that makes up object (`P2Body`). Has rectangle shape |
| P2Shape | Interface for parts that make up object (`P2Body`) |
| P2ShapeType | Enumeration representing shape type of part (`P2Shape`) |
| P2SliderJoint | Slider joint connecting two objects |
| P2Triangle | Part that makes up object (`P2Body`). Has triangle shape |
| P2WheelJoint | Wheel joint connecting two objects |
| P2World | ★ World where physics simulation is performed |


## JSON Data

| Type Name | Description |
|-----------|-------------|
| JSON | ★ JSON format data reading and writing class |
| JSONArrayView | JSON array view |
| JSONConstIterator | JSON const iterator |
| JSONItem | JSON element |
| JSONIterationProxy | JSON iterator helper class |
| JSONIterator | JSON iterator |
| JSONValueType | Enumeration representing JSON element type |


## TOML Data

| Type Name | Description |
|-----------|-------------|
| TOMLArrayIterator | TOML array iterator |
| TOMLArrayView | TOML array view |
| TOMLReader | ★ TOML format data reading class |
| TOMLTableArrayIterator | TOML table array iterator |
| TOMLTableArrayView | TOML table array view |
| TOMLTableIterator | TOML table iterator |
| TOMLTableMember | TOML table member |
| TOMLTableView | TOML table view |
| TOMLValue | TOML element |
| TOMLValueType | Enumeration representing TOML element type |


## Image Codecs

| Type Name | Description |
|-----------|-------------|
| BMPDecoder | BMP format image data decoder |
| BMPEncoder | BMP format image data encoder |
| GIFDecoder | GIF format image data decoder |
| GIFEncoder | GIF format image data encoder |
| JPEGDecoder | JPEG format image decoder |
| JPEGEncoder | JPEG format image encoder |
| PNGDecoder | PNG format image decoder |
| PNGEncoder | PNG format image encoder |
| PNGFilter | Enumeration representing PNG compression filter |
| PPMDecoder | PPM format image decoder |
| PPMEncoder | PPM format image encoder |
| PPMType | Enumeration representing PPM image save format |
| SVGDecoder | SVG format image decoder |
| TGADecoder | TGA format image decoder |
| TGAEncoder | TGA format image encoder |
| TIFFDecoder | TIFF format image decoder |
| WebPDecoder | WebP format image decoder |
| WebPEncoder | WebP format image encoder |
| WebPMethod | Enumeration representing WebP format image encoding method |


## Audio Codecs

| Type Name | Description |
|-----------|-------------|
| AACDecoder | AAC format audio data decoder |
| AIFFDecoder | AIFF format audio data decoder |
| FLACDecoder | FLAC format audio data decoder |
| MIDIDecoder | MIDI format audio data decoder |
| MP3Decoder | MP3 format audio data decoder |
| OggVorbisDecoder | OggVorbis format audio data decoder |
| OggVorbisEncoder | OggVorbis format audio data encoder |
| OpusDecoder | Opus format audio data decoder |
| WAVEDecoder | WAVE format audio data decoder |
| WAVEEncoder | WAVE format audio data encoder |
| WAVEFormat | Enumeration representing WAVE save format |
| WMADecoder | WMA format audio data decoder |

