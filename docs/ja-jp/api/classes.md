
# Siv3D のクラス一覧

よく使う重要なものに ★ を付けています。

## 数値

| 型名        | 説明                                                                      |
|-----------|-------------------------------------------------------------------------|
| bool      | ★ ブーリアン型（`false` または `true`）                                            |
| int8      | 符号付き 8-bit 整数型（-128 ～ 127）                                              |
| uint8     | 符号無し 8-bit 整数型（0 ～ 255）                                                 |
| int16     | 符号付き 16-bit 整数型（-32,768 ～ 32,767）                                       |
| uint16    | 符号無し 16-bit 整数型（0 ～ 65,535）                                             |
| int32     | ★ 符号付き 32-bit 整数型（-2,147,483,648 ～ 2,147,483,647）                       |
| uint32    | ★ 符号無し 32-bit 整数型（0 ～ 4,294,967,295）                                    |
| int64     | 符号付き 64-bit 整数型（-9,223,372,036,854,775,808 ～ 9,223,372,036,854,775,807） |
| uint64    | 符号無し 64-bit 整数型（0 ～ 18,446,744,073,709,551,615）                         |
| int128    | 符号付き 128-bit 整数型                                                        |
| uint128   | 符号無し 128-bit 整数型                                                        |
| float     | 単精度浮動小数点数型                                                              |
| double    | ★ 倍精度浮動小数点数型                                                            |
| size_t    | ★ オブジェクトのサイズを表現する符号無し 64-bit 整数型（0 ～ 18,446,744,073,709,551,615）        |
| BigInt    | 任意精度多倍長整数型                                                              |
| HalfFloat | 半精度浮動小数点数型                                                              |
| BigFloat  | 有効数字 100 桁の浮動小数点数型                                                      |


## 文字や文字列

| 型名           | 説明                              |
|--------------|---------------------------------|
| char8        | UTF-8 の 1 要素（`char` の別名）        |
| char16       | UTF-16 の 1 要素（`char16_t` の別名）   |
| char32       | ★ UTF-32 の 1 要素（`char32_t` の別名） |
| String       | ★ 文字列クラス。要素は `char32`           |
| StringView   | 文字列のビュークラス                      |
| FilePath     | ★ ファイルパス文字列（`String` の別名）       |
| FilePathView | ファイルパス文字列のビュー（`StringView` の別名） |
| URL          | URL 文字列（`String` の別名）           |
| URLView      | URL 文字列のビュー（`StringView` の別名）   |


## データ構造

| 型名                                                        | 説明                                                         |
|-----------------------------------------------------------|------------------------------------------------------------|
| Array&lt;Type, Allocator&gt;                              | ★ 動的配列（C++ 標準ライブラリの `std::vector` の置き換え）                   |
| DisjointSet&lt;IndexType&gt;                              | Union-Find 木                                               |
| Grid&lt;Type, Allocator&gt;                               | ★ 動的な二次元配列                                                 |
| HashSet&lt;Type, Hash, Eq, Alloc&gt;                      | ★ ハッシュテーブルによる Set（C++ 標準ライブラリの `std::unordered_set` の置き換え） |
| HashTable&lt;Key, Value, Hash, Eq, Alloc&gt;              | ★ ハッシュテーブルによる Map（C++ 標準ライブラリの `std::unordered_map` の置き換え） |
| KDTree&lt;DatasetAdapter&gt;                              | KD 木                                                       |
| KDTreeAdapter&lt;Dataset, PointType, ElementType, Dim&gt; | KD 木 の情報                                                   |
| None_t                                                    | `Optional` 型で無効値を表現する型（`std::nullopt_t` の別名）               |
| NonNull&lt;Pointer&gt;                                    | ヌルポインタを持たないポインタを管理するクラス                                    |
| Optional&lt;Type&gt;                                      | ★ 無効値を表現できる型（C++ 標準ライブラリの `std::optional` の置き換え）           |
| std::array&lt;Type, size_t&gt;                            | ★ 固定長配列                                                    |
| StringCompare                                             | ハッシュテーブルで文字列をキーにする際の補助クラス                                  |
| StringHash                                                | ハッシュテーブルで文字列をキーにする際の補助クラス                                  |


## 2D 図形

| 型名                               | 説明                                          |
|----------------------------------|---------------------------------------------|
| Bezier2                          | ★ 二次ベジェ曲線                                   |
| Bezier3                          | ★ 三次ベジェ曲線                                   |
| Circle                           | ★ 円                                         |
| Circular                         | ★ 円座標（`CircularBase<double, 0>` の別名）        |
| Circular0                        | 円座標（`CircularBase<double, 0>` の別名）          |
| Circular0F                       | 円座標（`CircularBase<float, 0>` の別名）           |
| Circular3                        | 円座標（`CircularBase<double, 3>` の別名）          |
| Circular3F                       | 円座標（`CircularBase<float, 3>` の別名）           |
| Circular6                        | 円座標（`CircularBase<double, 6>` の別名）          |
| Circular6F                       | 円座標（`CircularBase<float, 6>` の別名）           |
| Circular9                        | 円座標（`CircularBase<double, 9>` の別名）          |
| Circular9F                       | 円座標（`CircularBase<float, 9>` の別名）           |
| CircularBase&lt;Float, int32&gt; | 円座標                                         |
| CircularF                        | 円座標（`CircularBase<float, 0>` の別名）           |
| Ellipse                          | ★ 楕円                                        |
| Float2                           | 2 次元のベクトル（要素は `float`）                      |
| FloatQuad                        | 凸四角形（要素は `float`）                           |
| FloatRect                        | 上下左右で定義する長方形（要素は `float`）                   |
| Line                             | ★ 線分                                        |
| LineString                       | ★ 連続する線分（`Array<Vec2>` の置き換え）               |
| Mat3x2                           | ★ アフィン変換用の 3x2 行列                           |
| Mat3x3                           | ホモグラフィ変換用の 3x3 行列                           |
| MultiPolygon                     | 多角形の集合（`Array<Polygon>` の置き換え）              |
| OffsetCircular                   | ★ オフセット付き円座標（`CircularBase<double, 0>` の別名） |
| OffsetCircular0                  | オフセット付き円座標（`CircularBase<double, 0>` の別名）   |
| OffsetCircular0F                 | オフセット付き円座標（`CircularBase<float, 0>` の別名）    |
| OffsetCircular3                  | オフセット付き円座標（`CircularBase<double, 3>` の別名）   |
| OffsetCircular3F                 | オフセット付き円座標（`CircularBase<float, 3>` の別名）    |
| OffsetCircular6                  | オフセット付き円座標（`CircularBase<double, 6>` の別名）   |
| OffsetCircular6F                 | オフセット付き円座標（`CircularBase<float, 6>` の別名）    |
| OffsetCircular9                  | オフセット付き円座標（`CircularBase<double, 9>` の別名）   |
| OffsetCircular9F                 | オフセット付き円座標（`CircularBase<float, 9>` の別名）    |
| OffsetCircularBase               | オフセット付き円座標                                  |
| OffsetCircularF                  | オフセット付き円座標（`CircularBase<float, 0>` の別名）    |
| Point                            | ★ 2 次元のベクトル（要素は `int32`）                    |
| Polygon                          | ★ 多角形（穴も持てる）                                |
| Quad                             | ★ 凸四角形                                      |
| Rect                             | ★ 長方形（要素は `int32`）                          |
| RectF                            | ★ 長方形（要素は `double`）                         |
| RoundRect                        | ★ 角丸長方形                                     |
| Shape2D                          | ★ 多角形作成ユーティリティ                              |
| Size                             | ★ 横、縦の大きさ（要素は `int32`） （`Point` の別名）        |
| SizeF                            | ★ 横、縦の大きさ（要素は `double`） （`Vec2` の別名）        |
| Spline2D                         | スプライン曲線                                     |
| Triangle                         | ★ 三角形                                       |
| Vec2                             | ★ 2 次元のベクトル（要素は `double`）                   |


## 3D 形状

| 型名                           | 説明                                    |
|------------------------------|---------------------------------------|
| Box                          | ★ 各辺が XYZ 軸に平行な直方体                    |
| Cone                         | 円錐                                    |
| Cylinder                     | ★ 円柱                                  |
| Cylindrical                  | ★ 円柱座標（`CylindricalBase<double>` の別名） |
| CylindricalBase&lt;Float&gt; | 円柱座標                                  |
| CylindricalF                 | 円柱座標（`CylindricalBase<float>` の別名）    |
| Disc                         | 円盤                                    |
| Float3                       | 3 次元のベクトル（要素は `float`）                |
| Float4                       | 4 次元のベクトル（要素は `float`）                |
| InfinitePlane                | 平面                                    |
| Line3D                       | ★ 3D 線分                               |
| Mat4x4                       | ★ 4x4 行列                              |
| OrientedBox                  | ★ 向きのある直方体                            |
| Plane                        | ★ 大きさが有限の XZ 平面                       |
| Quaternion                   | ★ クォータニオン                             |
| Ray                          | ★ レイ                                  |
| Sphere                       | ★ 球                                   |
| Spherical                    | ★ 球面座標（`SphericalBase<double>` の別名）   |
| SphericalBase&lt;Float&gt;   | 球面座標                                  |
| SphericalF                   | 球面座標（`SphericalBase<float>` の別名）      |
| Triangle3D                   | 3D 三角形                                |
| Vec3                         | ★ 3 次元のベクトル（要素は `double`）             |
| Vec4                         | 4 次元のベクトル（要素は `double`）               |
| ViewFrustum                  | 視錐台                                   |


## 色

| 型名           | 説明                       |
|--------------|--------------------------|
| Color        | ★ RGBA カラー（要素は `uint8`）  |
| ColorF       | ★ RGBA カラー（要素は `double`） |
| ColormapType | カラーマップの種類                |
| ColorOption  | 色空間の設定                   |
| HSV          | ★ HSVA カラー               |


## 時間の単位

| 型名            | 説明                              |
|---------------|---------------------------------|
| Days          | 時間（日）（整数）                       |
| DaysF         | 時間（日）（浮動小数点数）                   |
| Hours         | 時間（時）（整数）                       |
| HoursF        | 時間（時）（浮動小数点数）                   |
| Minutes       | 時間（分）（整数）                       |
| MinutesF      | 時間（分）（浮動小数点数）                   |
| Seconds       | 時間（秒）（整数）                       |
| SecondsF      | 時間（秒）（浮動小数点数）                   |
| Milliseconds  | 時間（ミリ秒）（整数）                     |
| MillisecondsF | 時間（ミリ秒）（浮動小数点数）                 |
| Microseconds  | 時間（マイクロ秒）（整数）                   |
| MicrosecondsF | 時間（マイクロ秒）（浮動小数点数）               |
| Nanoseconds   | 時間（ナノ秒）（整数）                     |
| NanosecondsF  | 時間（ナノ秒）（浮動小数点数）                 |
| Duration      | ★ 時間（秒）（浮動小数点数）（`SecondsF` の別名） |


## エラー

| 型名                | 説明                       |
|-------------------|--------------------------|
| BadOptionalAccess | 無効な `Optional` へのアクセスエラー |
| EngineError       | エンジン内部のエラー               |
| Error             | ★ エラー                    |
| ParseError        | パース関数のエラー                |


## 様々なクラス

| 型名                                         | 説明                                         |
|--------------------------------------------|--------------------------------------------|
| ACLineStatus                               | 電源の接続状態を表す列挙型                              |
| AdaptiveThresholdMethod                    | 適応的閾値処理において閾値を計算する方法を表す列挙型                 |
| aligned_float4                             | ネイティブの SIMD Float4 型                       |
| Allocator&lt;Type, size_t&gt;              | メモリアライメント対応アロケータ                           |
| AnimatedGIFReader                          | GIF アニメーションの読み込みを行うクラス                     |
| AnimatedGIFWriter                          | GIF アニメーションの書き出しを行うクラス                     |
| ArcEmitter2D                               | 2D パーティクル放出器（円弧形状）                         |
| AssetHandle&lt;AssetType&gt;               | アセットハンドル                                   |
| AssetID&lt;AssetTag&gt;                    | アセット ID                                    |
| AssetIDWrapper&lt;AssetTag&gt;             | アセット ID                                    |
| AssetState                                 | アセットのロード状況を表す列挙型                           |
| AsyncHTTPTask                              | 非同期ダウンロードを管理するクラス                          |
| AsyncTask&lt;Type&gt;                      | 非同期処理クラス（C++ 標準ライブラリの `std::future` の置き換え） |
| Audio                                      | ★ オーディオクラス                                 |
| AudioAsset                                 | ★ オーディオアセット                                |
| AudioAssetData                             | オーディオアセットの定義                               |
| AudioFormat                                | 音声フォーマットを表す列挙型                             |
| AudioGroup                                 | グループ化したオーディオ                               |
| AudioLoopTiming                            | オーディオのループ位置指定                              |
| BasicCamera2D                              | 2D カメラの基本クラス                               |
| BasicCamera3D                              | 3D カメラの基本クラス                               |
| BasicPerlinNoise&lt;Float&gt;              | Perlin ノイズ                                 |
| BatteryStatus                              | バッテリーの残量を表す列挙型                             |
| BinaryReader                               | ★ バイナリファイルの読み込みクラス                         |
| BinaryWriter                               | ★ バイナリファイルの書き込みクラス                         |
| BitmapGlyph                                | ビットマップグリフ                                  |
| Blend                                      | ブレンドモードを表す列挙型                              |
| BlendOp                                    | ブレンド式を表す列挙型                                |
| BlendState                                 | ★ ブレンドステート                                 |
| Blob                                       | ★ バイナリデータ                                  |
| BorderType                                 | 画像フィルタ処理時の境界線の扱いを表す列挙型                     |
| Buffer2D                                   | 2D 描画バッファ                                  |
| Byte                                       | 1 バイトを表現する型                                |
| Camera2D                                   | ★ 2D カメラ                                   |
| Camera2DParameters                         | 2D カメラの設定                                  |
| CameraControl                              | カメラの操作方法を表す列挙型                             |
| CascadeClassifier                          | Cascade による画像分類器                           |
| ChildProcess                               | 子プロセスの作成と管理クラス                             |
| CircleEmitter2D                            | 2D パーティクル放出器（円形状）                          |
| CommonFloat&lt;T, U&gt;                    | 異なる数値型どうしの計算結果として使う浮動小数点数型                 |
| CommonFloat_t&lt;T, U&gt;                  | 異なる数値型どうしの計算結果として使う浮動小数点数型                 |
| CommonVector&lt;T, U, bool&gt;             | 異なる数値型ベクトルどうしの計算結果として使うベクトル型               |
| CommonVector_t&lt;T, U, bool&gt;           | 異なる数値型ベクトルどうしの計算結果として使うベクトル型               |
| ConstantBuffer&lt;Type&gt;                 | ★ シェーダ定数バッファ                               |
| ConstantBufferBase                         | シェーダ定数バッファの詳細情報                            |
| ConstantBufferBinding                      | シェーダ定数バッファのバインディング                         |
| CopyOption                                 | ファイルコピー時の動作を表す列挙型                          |
| CPUInfo                                    | CPU 情報                                     |
| CSV                                        | ★ CSV 形式のデータの読み書きクラス                       |
| CursorStyle                                | ★ マウスカーソルの形状を表す列挙型                         |
| Date                                       | ★ 日付                                       |
| DateTime                                   | ★ 日付と時刻                                    |
| DayOfWeek                                  | 曜日を表す列挙型                                   |
| DeadZone                                   | デッドゾーンの設定                                  |
| DeadZoneType                               | デッドゾーンの計算方式を表す列挙型                          |
| DebugCamera3D                              | ★ デバッグ用の 3D カメラ                            |
| DefaultAllocator&lt;Type&gt;               | メモリアライメントを考慮したデフォルトのアロケータ                  |
| DepthFunc                                  | デプステスト関数を表す列挙型                             |
| DepthStencilState                          | デプス・ステンシルステート                              |
| Deserializer&lt;Reader&gt;                 | デシリアライザ定義用クラステンプレート                        |
| detail::Gamepad_impl                       | ★ ゲームパッド。`Gamepad(…)` の戻り値                 |
| detail::XInput_impl                        | ★ Xinput ゲームパッド。`XInput(…)` の戻り値           |
| DirectoryWatcher                           | ディレクトリ内でのファイルの操作の監視クラス                     |
| DragItemType                               | ドラッグするアイテムの種類を表す列挙型                        |
| DragStatus                                 | ドラッグの状態                                    |
| DrawableText                               | ★ 描画テキスト。`font(…)` の戻り値                    |
| DroppedFilePath                            | ドロップされたファイルパスの情報                           |
| DroppedText                                | ドロップされたテキストの情報                             |
| DynamicMesh                                | 中身を更新できる、動的メッシュ                            |
| DynamicTexture                             | ★ 中身を更新できる、動的テクスチャ                         |
| EdgePreservingFilterType                   | EdgePreservingFilter の種類を表す列挙型             |
| Effect                                     | ★ エフェクト                                    |
| Emission2D                                 | 2D パーティクルにおける放出                            |
| Emoji                                      | 標準絵文字                                      |
| EngineOption                               | エンジンの設定                                    |
| ESSL                                       | OpenGL ES Shading Language ファイル            |
| Exif                                       | Exif データ                                   |
| FFTResult                                  | ★ FFT の結果                                  |
| FFTSampleLength                            | FFT サンプル数を表す列挙型                            |
| FileAction                                 | ファイルの操作を表す列挙型                              |
| FileChange                                 | ファイルの操作とファイルパス                             |
| FileFilter                                 | ファイル拡張子フィルタ                                |
| FloodFillConnectivity                      | 画像塗りつぶしの連結性を表す列挙型                          |
| Font                                       | ★ フォント                                     |
| FontAsset                                  | ★ フォントアセット                                 |
| FontAssetData                              | フォントアセットの定義                                |
| FontMethod                                 | ★ フォントの描画方式を表す列挙型                          |
| FontStyle                                  | フォントのスタイルを表す列挙型                            |
| FormatData                                 | 文字列フォーマットの情報格納バッファ                         |
| GamepadInfo                                | ゲームパッドの情報                                  |
| GeoJSONBase                                | GeoJSON オブジェクトの基本クラス                       |
| GeoJSONFeature                             | GeoJSON Feature オブジェクト                     |
| GeoJSONFeatureCollection                   | GeoJSON FeatureCollection オブジェクト           |
| GeoJSONGeometry                            | GeoJSON Geometry オブジェクト                    |
| GeoJSONType                                | GeoJSONで定義されているオブジェクトの型を表す列挙型              |
| GLSL                                       | ★ GLSL ファイル                                |
| Glyph                                      | グリフ                                        |
| GlyphCluster                               | グリフクラスタ                                    |
| GlyphIndex                                 | フォントファイル内のグリフインデックス（`uint32` の別名）          |
| GlyphInfo                                  | グリフ情報                                      |
| GMInstrument                               | ★ General MIDI (GM) における楽器を表す列挙型           |
| GrabCut                                    | 画像からの背景抽出                                  |
| GrabCutClass                               | 画像からの背景抽出における背景と前景を表す列挙型                   |
| HLSL                                       | ★ HLSL ファイル                                |
| HTMLWriter                                 | HTML 文書の書き出しクラス                            |
| HTTPAsyncStatus                            | ダウンロードの進行状況を表す列挙型                          |
| HTTPProgress                               | HTTP 通信の進捗                                 |
| HTTPResponse                               | HTTP レスポンス                                 |
| HTTPStatusCode                             | HTTP ステータスコードを表す列挙型                        |
| IAddon                                     | アドオンのインタフェース                               |
| IAsset                                     | アセットの基本クラス                                 |
| IAudioDecoder                              | 音声デコーダインタフェース                              |
| IAudioEncoder                              | 音声エンコーダインタフェース                             |
| IAudioStream                               | 動的更新オーディオのインタフェース                          |
| Icon                                       | 標準アイコン                                     |
| IEffect                                    | ★ エフェクトのインタフェース                            |
| IEmitter2D                                 | 2D パーティクル放出器のインタフェース                       |
| IImageDecoder                              | 画像デコーダインタフェース                              |
| IImageEncoder                              | 画像エンコーダインタフェース                             |
| Image                                      | ★ 画像データ                                    |
| ImageAddressMode                           | 画像アドレスモードを表す列挙型                            |
| ImageFormat                                | 画像フォーマットを表す列挙型                             |
| ImageInfo                                  | 画像ファイルの情報                                  |
| ImagePixelFormat                           | 画像のピクセルフォーマットを表す列挙型                        |
| ImageROI                                   | 画像データ内の領域                                  |
| InfiniteList&lt;Type&gt;                   | 無限リスト                                      |
| INI                                        | ★ INI 形式のデータの読み書き                          |
| INIKey                                     | INI 形式のデータのキー                              |
| INISection                                 | INI 形式のデータのセクション                           |
| INIValueWrapper                            | INI 形式のデータのヘルパークラス                         |
| Input                                      | ★ 入力オブジェクト                                 |
| InputCombination                           | `Input` の組み合わせ                             |
| InputDeviceType                            | 入力デバイスの種類を表す列挙型                            |
| InputGroup                                 | `Input` の組み合わせ                             |
| InterpolationAlgorithm                     | 画像拡大縮小の手法を表す列挙型                            |
| IPv4Address                                | IPv4 アドレス                                  |
| IReader                                    | Reader インタフェース                             |
| IScene&lt;State, Data&gt;                  | ★ シーン管理用のシーンインタフェース                        |
| ISteadyClock                               | 時刻提供インタフェース                                |
| IWriter                                    | Writer インタフェース                             |
| JoyCon                                     | Joy-Con                                    |
| KahanSummation&lt;Float&gt;                | カハンの加算アルゴリズム用ユーティリティ                       |
| KeyEvent                                   | キー入力の詳細                                    |
| KlattTTSParameters                         | Klatt 方式によるテキスト読み上げの設定                     |
| KlattWaveform                              | Klatt 方式によるテキスト読み上げの波形種類を表す列挙型             |
| LanguageCode                               | 言語コードを表す列挙型                                |
| Leap::Bone                                 | Leap Motion におけるボーンの情報                     |
| Leap::Connection                           | 接続された Leap デバイスのハンドル                       |
| Leap::Hand                                 | Leap Motion における手の情報                       |
| Leap::TrackingMode                         | Leap Motion におけるトラッキングモードを表す列挙型            |
| LetterCase                                 | アルファベットの大文字・小文字を表す列挙型                      |
| LicenseInfo                                | ライセンス情報                                    |
| LineStyle                                  | 線のスタイル                                     |
| ListBoxState                               | ★ リストボックスの状態                               |
| LogLevel                                   | 出力されるログの詳細度を表す列挙型                          |
| LogType                                    | ログ出力の種類を表す列挙型                              |
| ManagedScript                              | 自動管理されたスクリプト                               |
| MatchResults                               | 正規表現のマッチ結果                                 |
| Material                                   | 3D オブジェクトのマテリアル                            |
| MathParser                                 | 数式パーサ                                      |
| MD5Value                                   | MD5                                        |
| MemoryMappedFile                           | メモリマップトファイルクラス                             |
| MemoryMappedFileView                       | メモリマップトファイルビュークラス                          |
| MemoryReader                               | メモリの読み込みクラス                                |
| MemoryViewReader                           | メモリビューの読み込みクラス                             |
| MemoryWriter                               | メモリへのバイナリデータ書き出しクラス                        |
| Mesh                                       | ★ 3D メッシュ                                  |
| MeshData                                   | ★ 3D メッシュの頂点バッファとインデックスバッファ                |
| MeshGlyph                                  | メッシュグリフ                                    |
| MessageBoxResult                           | メッセージボックスの結果を表す列挙型                         |
| MessageBoxStyle                            | メッセージボックスのスタイルを表す列挙型                       |
| Microphone                                 | ★ マイク                                      |
| MicrophoneInfo                             | マイクの情報                                     |
| MicrosecClock                              | マイクロ秒カウンター                                 |
| MIDINote                                   | MIDI ノート                                   |
| MillisecClock                              | ミリ秒カウンター                                   |
| MiniScene&lt;State&gt;                     | 簡易版のシーンマネージャー                              |
| MixBus                                     | オーディオのミックスバス番号を表す列挙型                       |
| MMFOpenMode_if_Exists                      | メモリマップトファイルのオープンモードを表す列挙型                  |
| MMFOpenMode_if_NotFound                    | メモリマップトファイルのオープンモードを表す列挙型                  |
| Model                                      | ★ 3D モデル                                   |
| ModelMeshPart                              | 3D モデルを構成するモデルのパーツの構成要素                    |
| ModelObject                                | 3D モデルを構成するモデルのパーツ                         |
| MonitorInfo                                | モニターの情報                                    |
| MSDFGlyph                                  | MSDF 方式のグリフ                                |
| MSL                                        | Metal Shading Language ファイル（未実装）           |
| MSRenderTexture                            | マルチサンプル（アンチエイリアス付き）レンダーテクスチャ               |
| NamedParameter&lt;Tag, Type&gt;            | 名前付き引数用のヘルパークラス                            |
| NamedParameterHelper&lt;Tag&gt;            | 名前付き引数用のヘルパークラス                            |
| NativeFilePath                             | OS ネイティブのファイルパス表現型                         |
| NavMesh                                    | ナビメッシュ                                     |
| NavMeshConfig                              | ナビメッシュの設定                                  |
| NormalComputation                          | 法線の計算方式を表す列挙型                              |
| OpenMode                                   | ファイルのオープンモードを表す列挙型                         |
| OutlineGlyph                               | 輪郭グリフ                                      |
| Particle2D                                 | 2D パーティクル                                  |
| ParticleSystem2D                           | 2D パーティクルシステム                              |
| ParticleSystem2DParameters                 | 2D パーティクルシステムの設定                           |
| PerlinNoise                                | Perlin ノイズ（`BasicPerlinNoise<double>` の別名） |
| PerlinNoiseF                               | Perlin ノイズ（`BasicPerlinNoise<float>` の別名）  |
| PhongMaterial                              | Phong モデルの Material                        |
| PhongMaterialInternal                      | Phong モデルの Material の内部形式                  |
| PianoKey                                   | ★ 音名を表す列挙型                                 |
| Pipe                                       | パイプ通信の設定を表す列挙型                             |
| PixelShader                                | ★ ピクセルシェーダ                                 |
| PixelShaderAsset                           | ピクセルシェーダアセット                               |
| PixelShaderAssetData                       | ピクセルシェーダアセットの定義                            |
| PlaceHolder_t                              | プレースホルダー型                                  |
| Platform::Windows::HLSLCompileOption       | HLSL のコンパイルオプション                           |
| PlayingCard::Card                          | トランプカードの番号、スート、裏表などのデータ                    |
| PlayingCard::CardInfo                      | トランプカードの描画用の情報                             |
| PlayingCard::Pack                          | トランプカードを作成するクラス                            |
| PlayingCard::Suit                          | トランプカードのスート（絵柄のマーク）を表す列挙型                  |
| PoissonDisk2D                              | 2D ポワソン分布クラス                               |
| PolygonEmitter2D                           | 2D パーティクル放出器（多角形）                          |
| PolygonFailureType                         | Polygon の入力頂点の検証結果                         |
| PolygonGlyph                               | 多角形によるグリフ                                  |
| PowerStatus                                | システムの電源の状態                                 |
| ProController                              | Pro コントローラ用の Gamepad アダプタ                  |
| ProfilerStat                               | プロファイリング情報                                 |
| QRContent                                  | QR コードのスキャン結果                              |
| QRErrorCorrection                          | QR コードの誤り訂正レベルを表す列挙型                       |
| QRMode                                     | QR コードのモードを表す列挙型                           |
| QRScanner                                  | QR コードの読み取りクラス                             |
| RDTSCClock                                 | CPU サイクル数カウンター                             |
| RectanglePack                              | 長方形詰込み結果                                   |
| RectEmitter2D                              | 2D パーティクル放出器（長方形）                          |
| RegExp                                     | 正規表現                                       |
| RenderTexture                              | ★ レンダーテクスチャ                                |
| ResizeMode                                 | ★ シーンの自動リサイズモードを表す列挙型                      |
| ResourceOption                             | リソースパス使用のオプションを表す列挙型                       |
| SamplerState                               | ★ サンプラーステート                                |
| SaturatedLinework&lt;TargetShape, URNG&gt; | 集中線描画クラス                                   |
| SceneManager&lt;State, Data&gt;            | ★ シーンマネージャー                                |
| ScopedColorAdd2D                           | 2D 描画カラー加算設定スコープオブジェクト                     |
| ScopedColorMul2D                           | 2D 描画カラー乗算設定スコープオブジェクト                     |
| ScopedCustomShader2D                       | 2D 描画カスタムシェーダ設定スコープオブジェクト                  |
| ScopedCustomShader3D                       | 3D 描画カスタムシェーダ設定スコープオブジェクト                  |
| ScopedRenderStates2D                       | ★ 2D 描画レンダーステート設定スコープオブジェクト                |
| ScopedRenderStates3D                       | 3D 描画レンダーステート設定スコープオブジェクト                  |
| ScopedRenderTarget2D                       | ★ 2D 描画レンダーターゲット設定スコープオブジェクト               |
| ScopedRenderTarget3D                       | 3D 描画レンダーターゲット設定スコープオブジェクト                 |
| ScopedViewport2D                           | 2D 描画ビューポート設定スコープオブジェクト                    |
| ScopedViewport3D                           | 3D 描画ビューポート設定スコープオブジェクト                    |
| ScopeGuard&lt;Callback&gt;                 | スコープガード                                    |
| Script                                     | スクリプト                                      |
| ScriptCompileOption                        | スクリプトのコンパイルオプションを表す列挙型                     |
| ScriptFunction&lt;Ret(Args…)&gt;           | スクリプト関数                                    |
| ScriptModule                               | スクリプトのモジュール                                |
| SDFGlyph                                   | SDF 方式によるグリフ                               |
| Serial                                     | シリアル通信                                     |
| Serializer&lt;Writer&gt;                   | シリアライザ定義用クラステンプレート                         |
| ShaderGroup                                | シェーダ言語の差を吸収するクラス                           |
| ShaderStage                                | シェーダステージを表す列挙型                             |
| SIMD_Float4                                | SIMD 対応 Float4                             |
| SimpleAnimation                            | キーフレームアニメーション補助クラス                         |
| Sky                                        | 天空レンダリングエンジン（実験的）                          |
| SoundFont                                  | サウンドフォント                                   |
| SpecialFolder                              | 特殊フォルダを表す列挙型                               |
| SplineIndex                                | `Spline2D` 上の位置                            |
| Step&lt;T, N, S&gt;                        | ★ ループのユーティリティ                              |
| Step2D                                     | ★ 2D ループの一元化ユーティリティ                        |
| Stopwatch                                  | ★ ストップウォッチ                                 |
| Subdivision2D                              | 2D サブディビジョンクラス                             |
| Subdivision2DEdgeType                      | 2D サブディビジョンのエッジの情報                         |
| Subdivision2DPointLocation                 | 2D サブディビジョンの点の位置を表す列挙型                     |
| SVG                                        | SVG データ                                    |
| TCPClient                                  | TCP クライアント                                 |
| TCPError                                   | TCP 通信のエラーを表す列挙型                           |
| TCPServer                                  | TCP サーバ                                    |
| TCPSessionID                               | TCP のセッション ID（`uint64` の別名）                |
| TextEditState                              | ★ テキストボックス内のテキストの状態                        |
| TextEncoding                               | テキストファイルのエンコーディング形式                        |
| TextInputMode                              | テキストの入力モード                                 |
| TextReader                                 | ★ テキストファイルの読み込みクラス                         |
| TextStyle                                  | テキストのスタイル                                  |
| Texture                                    | ★ テクスチャ                                    |
| TextureAddressMode                         | テクスチャアドレスモードを表す列挙型                         |
| TextureAsset                               | ★ テクスチャアセット                                |
| TextureAssetData                           | テクスチャアセットの定義                               |
| TexturedCircle                             | 円形に切り抜いたテクスチャ                              |
| TextureDesc                                | ★ テクスチャの設定を表す列挙型                           |
| TexturedQuad                               | 凸四角形に切り抜いたテクスチャ                            |
| TexturedRoundRect                          | テクスチャ上の角丸長方形の領域                            |
| TextureFilter                              | ★ テクスチャフィルタ                                |
| TextureFormat                              | テクスチャフォーマット                                |
| TexturePixelFormat                         | テクスチャのピクセルフォーマットを表す列挙型                     |
| TextureRegion                              | ★ テクスチャ上の長方形の領域                            |
| TextWriter                                 | ★ テキストファイルの書き込みクラス                         |
| TimeProfiler                               | プロファイラユーティリティークラス                          |
| Timer                                      | タイマー                                       |
| ToastNotificationID                        | トースト通知の ID（`int64` の別名）                    |
| ToastNotificationItem                      | トースト通知の設定                                  |
| ToastNotificationState                     | トースト通知の状態を表す列挙型                            |
| Transformer2D                              | ★ 2D 座標変換スコープオブジェクト                        |
| Transformer3D                              | 3D 座標変換スコープオブジェクト                          |
| Transition                                 | 値の遷移ヘルパークラス                                |
| TriangleIndex                              | 三角形を構成する頂点インデックス（要素は `uint16`）             |
| TriangleIndex32                            | 三角形を構成する頂点インデックス（要素は `uint32`）             |
| Typeface                                   | ★ 標準フォントの種類を表す列挙型                          |
| Uncopyable                                 | コピー禁止 Mixin                                |
| UnderlineStyle                             | 下線のスタイルを表す列挙型                              |
| unique_resource                            | オブジェクトの破棄時に、指定したデリータを呼ぶ RAII ラッパー          |
| UserAction                                 | ★ アプリケーションを終了させるためのユーザアクションを表す列挙型          |
| UTF16toUTF32_Converter                     | UTF-8 から UTF-32 への逐次変換クラス                  |
| UTF32toUTF16_Converter                     | UTF-16 から UTF-32 への逐次変換クラス                 |
| UTF32toUTF8_Converter                      | UTF-32 から UTF-8 への逐次変換クラス                  |
| UTF8toUTF32_Converter                      | UTF-32 から UTF-16 への逐次変換クラス                 |
| UUIDValue                                  | UUID                                       |
| VariableSpeedStopwatch                     | 速度を変更可能なストップウォッチ                           |
| Vertex2D                                   | 2D 図形の基本頂点データ                              |
| Vertex3D                                   | 3D 図形の基本頂点データ                              |
| VertexShader                               | ★ 頂点シェーダ                                   |
| VertexShaderAsset                          | 頂点シェーダアセット                                 |
| VertexShaderAssetData                      | 頂点シェーダアセットの定義                              |
| VideoReader                                | 動画ファイルの読み込みクラス                             |
| VideoTexture                               | 動画を Texture のように扱えるクラス                     |
| VideoWriter                                | 動画ファイルの書き出しクラス                             |
| VoronoiFacet                               | ボロノイ Facets                                |
| Wave                                       | ★ 音声波形データ                                  |
| WaveSample                                 | 単精度浮動小数点数によるステレオの波形サンプル                    |
| WaveSampleS16                              | 符号付き 16-bit 整数によるステレオの波形サンプル               |
| Webcam                                     | ★ Web カメラ                                  |
| WebcamInfo                                 | Web カメラの情報                                 |
| WGSL                                       | WebGPU Shading Language ファイル               |
| WindowState                                | ウィンドウの状態                                   |
| WindowStyle                                | ウィンドウスタイルを表す列挙型                            |
| X86Features                                | CPU の対応命令セット                               |
| XInputVibration                            | XInput コントローラのバイブレーション設定                   |
| XMLElement                                 | XML の要素                                    |
| XMLReader                                  | XML の読み込みクラス                               |
| YesNo&lt;Tag&gt;                           | YesNo 用のクラステンプレート                          |
| ZIPReader                                  | ZIP アーカイブファイルの読み込みクラス                      |


## 乱数と分布

| 型名                         | 説明                                               |
|----------------------------|--------------------------------------------------|
| BernoulliDistribution      | ベルヌーイ分布クラス                                       |
| DefaultRNG                 | デフォルトの乱数生成器（`PRNG::SFMT19937_64` の別名）            |
| DiscreteDistribution       | 確率分布を生成するクラス                                     |
| HardwareRNG                | ハードウェアによる非決定的乱数生成器                               |
| NormalDistribution         | 正規分布クラス                                          |
| PRNG::SFMT19937_64         | SIMD-oriented Fast Mersenne Twister 方式による疑似乱数生成器 |
| PRNG::SplitMix64           | SplitMix64 方式による疑似乱数生成器                          |
| PRNG::Xoroshiro128Plus     | xoshiro128+ 方式による疑似乱数生成器                         |
| PRNG::Xoroshiro128PlusPlus | xoroshiro128++ 方式による疑似乱数生成器                      |
| PRNG::Xoroshiro128StarStar | xoroshiro128** 方式による疑似乱数生成器                      |
| PRNG::Xoshiro128Plus       | xoshiro128+ 方式による疑似乱数生成器                         |
| PRNG::Xoshiro128PlusPlus   | xoshiro128++ 方式による疑似乱数生成器                        |
| PRNG::Xoshiro128StarStar   | xoshiro128** 方式による疑似乱数生成器                        |
| PRNG::Xoshiro256Plus       | xoshiro256+ 方式による疑似乱数生成器                         |
| PRNG::Xoshiro256PlusPlus   | xoshiro256++ 方式による疑似乱数生成器                        |
| PRNG::Xoshiro256StarStar   | xoshiro256** 方式による疑似乱数生成器                        |
| SmallRNG                   | 省サイズのデフォルトの乱数生成器（`PRNG::Xoshiro256PlusPlus` の別名） |
| UniformDistribution        | 整数と浮動小数点数に使える一様分布クラス                             |
| UniformIntDistribution     | 整数の一様分布クラス                                       |
| UniformRealDistribution    | 浮動小数点数の一様分布クラス                                   |


## 2D 物理演算

| 型名              | 説明                                                             |
|-----------------|----------------------------------------------------------------|
| P2Body          | ★ 物理演算のワールドに存在する物体の 1 単位。0 個以上（通常は 1 個以上）の部品（`P2Shape`）から構成される |
| P2BodyID        | 物体 P2Body に与えられる一意の ID の型（`uint32` の別名）                        |
| P2BodyType      | ★ 物体の種類に関する列挙型                                                 |
| P2Circle        | 物体（`P2Body`）を構成する部品。円の形状を持つ                                    |
| P2Collision     | 2 つの物体にはたらく全ての接触に関する情報で、最大 2 つの `P2Contact` を持つ                |
| P2Contact       | 2 つの物体に発生した衝突に関する情報                                            |
| P2ContactPair   | 2 つの物体が接触しているときのそれらの ID (P2BodyID) のペア                         |
| P2DistanceJoint | 2 つの物体をつなぐ距離ジョイント                                              |
| P2Filter        | 部品（`P2Shape`）にカテゴリビットフラグを指定し、特定のビットフラグを持つ部品と干渉しないようにできる        |
| P2Line          | 物体（`P2Body`）を構成する部品。線分の形状を持つ                                   |
| P2LineString    | 物体（`P2Body`）を構成する部品。連続した線分の形状を持つ                               |
| P2Material      | 部品（`P2Shape`）の材質を定義する                                          |
| P2MouseJoint    | 2 つの物体をつなぐマウスジョイント                                             |
| P2PivotJoint    | 2 つの物体をつなぐピボットジョイント                                            |
| P2Polygon       | 物体（`P2Body`）を構成する部品。多角形の形状を持つ                                  |
| P2Quad          | 物体（`P2Body`）を構成する部品。凸四角形の形状を持つ                                 |
| P2Rect          | 物体（`P2Body`）を構成する部品。長方形の形状を持つ                                  |
| P2Shape         | 物体（`P2Body`）を構成する部品のインタフェース                                    |
| P2ShapeType     | 部品（`P2Shape`）の形状の種類を表す列挙型                                      |
| P2SliderJoint   | 2 つの物体をつなぐスライダージョイント                                           |
| P2Triangle      | 物体（`P2Body`）を構成する部品。三角形の形状を持つ                                  |
| P2WheelJoint    | 2 つの物体をつなぐホイールジョイント                                            |
| P2World         | ★ 物理演算を行うワールド                                                  |


## JSON データ

| 型名                 | 説明                    |
|--------------------|-----------------------|
| JSON               | ★ JSON 形式のデータの読み書きクラス |
| JSONArrayView      | JSON の配列のビュー          |
| JSONConstIterator  | JSON const イテレータ      |
| JSONItem           | JSON の要素              |
| JSONIterationProxy | JSON イテレータ補助クラス       |
| JSONIterator       | JSON イテレータ            |
| JSONValueType      | JSON の要素の型を表す列挙型      |


## TOML データ

| 型名                     | 説明                    |
|------------------------|-----------------------|
| TOMLArrayIterator      | TOML の配列のイテレータ        |
| TOMLArrayView          | TOML の配列のビュー          |
| TOMLReader             | ★ TOML 形式のデータの読み込みクラス |
| TOMLTableArrayIterator | TOML のテーブル配列のイテレータ    |
| TOMLTableArrayView     | TOML のテーブル配列のビュー      |
| TOMLTableIterator      | TOML のテーブルのイテレータ      |
| TOMLTableMember        | TOML のテーブルメンバ         |
| TOMLTableView          | TOML のテーブルのビュー        |
| TOMLValue              | TOML の要素              |
| TOMLValueType          | TOML の要素の型を表す列挙型      |


## 画像コーデック

| 型名          | 説明                      |
|-------------|-------------------------|
| BMPDecoder  | BMP 形式画像データのデコーダ        |
| BMPEncoder  | BMP 形式画像データのエンコーダ       |
| GIFDecoder  | GIF 形式画像データのデコーダ        |
| GIFEncoder  | GIF 形式画像データのエンコーダ       |
| JPEGDecoder | JPEG 形式画像のデコーダ          |
| JPEGEncoder | JPEG 形式画像のエンコーダ         |
| PNGDecoder  | PNG 形式画像のデコーダ           |
| PNGEncoder  | PNG 形式画像のエンコーダ          |
| PNGFilter   | PNG 圧縮時のフィルタを表す列挙型      |
| PPMDecoder  | PPM 形式画像のデコーダ           |
| PPMEncoder  | PPM 形式画像のエンコーダ          |
| PPMType     | PPM 画像の保存形式を表す列挙型       |
| SVGDecoder  | SVG 形式画像のデコーダ           |
| TGADecoder  | TGA 形式画像のデコーダ           |
| TGAEncoder  | TGA 形式画像のエンコーダ          |
| TIFFDecoder | TIFF 形式画像のデコーダ          |
| WebPDecoder | WebP 形式画像のデコーダ          |
| WebPEncoder | WebP 形式画像のエンコーダ         |
| WebPMethod  | WebP 形式画像のエンコード手法を表す列挙型 |


## 音声コーデック

| 型名               | 説明                      |
|------------------|-------------------------|
| AACDecoder       | AAC 形式音声データのデコーダ        |
| AIFFDecoder      | AIFF 形式音声データのデコーダ       |
| FLACDecoder      | FLAC 形式音声データのデコーダ       |
| MIDIDecoder      | MIDI 形式音声データのデコーダ       |
| MP3Decoder       | MP3 形式音声データのデコーダ        |
| OggVorbisDecoder | OggVorbis 形式音声データのデコーダ  |
| OggVorbisEncoder | OggVorbis 形式音声データのエンコーダ |
| OpusDecoder      | Opus 形式音声データのデコーダ       |
| WAVEDecoder      | WAVE 形式音声データのデコーダ       |
| WAVEEncoder      | WAVE 形式音声データのエンコーダ      |
| WAVEFormat       | WAVE の保存形式を表す列挙型        |
| WMADecoder       | WMA 形式音声データのデコーダ        |


