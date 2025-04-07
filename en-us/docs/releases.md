# Release Notes

## v0.6 Generation

???+ summary "v0.6.16 | 2025-04-07"

	#### 前バージョンからの更新ガイド
	- [v0.6.15 からのアップグレード手順（Windows）:material-open-in-new:](https://zenn.dev/link/comments/5c3c74c675beaf){:target="_blank"}

	#### 新機能
	- **Xcode 16.3 でのビルドに対応しました** ([#1289](https://github.com/Siv3D/OpenSiv3D/issues/1289))
	- **長方形を面取りした `Polygon` を返す `Rect::chamfered()`, `RectF::chamfered()` を追加しました** ([#1268](https://github.com/Siv3D/OpenSiv3D/issues/1268))
		- [サンプル :material-open-in-new:](https://zenn.dev/link/comments/bdd20930aae203){:target="_blank"}
	- **平面上の点群に対し最小包含円を求める `Geometry2D::SmallestEnclosingCircle()` を追加しました** ([#1272](https://github.com/Siv3D/OpenSiv3D/pull/1272))
		- [サンプル :material-open-in-new:](https://zenn.dev/link/comments/d3b2a501acc2ab){:target="_blank"}
	- 2D 物理演算において、三角形、四角形、多角形、線分などの形状のセンサーを作れるようにしました ([#916](https://github.com/Siv3D/OpenSiv3D/issues/916), [#1251](https://github.com/Siv3D/OpenSiv3D/pull/1251))
	- 線分に厚みを持たせて四角形を作る `Line::withThickness()` を追加しました ([#1276](https://github.com/Siv3D/OpenSiv3D/issues/1276))
	- `Rect`, `RectF` に `.stretched(Arg::left)` 等のオーバーロードを追加しました ([#1277](https://github.com/Siv3D/OpenSiv3D/issues/1277), [#1279](https://github.com/Siv3D/OpenSiv3D/pull/1279))
	- `Font::getGlyphByGlyphIndex()` を追加しました ([#1278](https://github.com/Siv3D/OpenSiv3D/issues/1278))
	- `SimpleMenuBar::mouseOver()` を追加しました ([#1290](https://github.com/Siv3D/OpenSiv3D/issues/1290))
	- 文字列中のランダムな要素を返す `String::choice()` を追加しました ([#1253](https://github.com/Siv3D/OpenSiv3D/issues/1253))
	- `Math::ClampAngle()` を追加しました ([#1271](https://github.com/Siv3D/OpenSiv3D/pull/1271))
	- `int128`, `uint128` のシリアライズに対応しました ([#1261](https://github.com/Siv3D/OpenSiv3D/issues/1261))
	- `YesNo` クラスに `operator==` を追加しました ([#1282](https://github.com/Siv3D/OpenSiv3D/pull/1282))

	#### 仕様変更
	- Windows 版、macOS 版における Boost の内部バージョンを 1.74.0 から 1.83.0 に更新しました。幾何計算系関数の細かい挙動が変化する可能性があります ([#1292](https://github.com/Siv3D/OpenSiv3D/pull/1292))
	- The Parallel Hashmap ライブラリを v1.3.8 から v2.0.0 に更新しました ([#1297](https://github.com/Siv3D/OpenSiv3D/issues/1297))
	- 使用していなかったため、Lua 関連のソースファイルをすべて削除しました ([#1293](https://github.com/Siv3D/OpenSiv3D/issues/1293), [#1294](https://github.com/Siv3D/OpenSiv3D/pull/1294))

	#### パフォーマンス向上
	- `DiscreteSample()` 一部のオーバーロードの戻り値を参照に変更しました ([#1275](https://github.com/Siv3D/OpenSiv3D/pull/1275))

	#### 不具合・バグ修正
	- Windows において、Web カメラが接続されていない環境で `System::EnumerateWebcams()` がクラッシュした不具合を修正しました ([#1250](https://github.com/Siv3D/OpenSiv3D/issues/1250))
	- `MemoryReader` と `MemoryViewReader` の `.lookahead()` が指定位置から読み込まなかった不具合を修正しました ([#1264](https://github.com/Siv3D/OpenSiv3D/issues/1264))
	- `MemoryWriter` のバグを修正しました ([#1266](https://github.com/Siv3D/OpenSiv3D/issues/1266))
	- `Camera2D` にて `CameraControl::None_` 設定時、マウスホイールがカメラの挙動に干渉していたバグを修正しました ([#1295](https://github.com/Siv3D/OpenSiv3D/issues/1295))
	- `MemoryReader` と `MemoryViewReader` の `.read()`, `.lookahead()` が範囲外アクセスをチェックしていなかった不具合を修正しました ([#1264](https://github.com/Siv3D/OpenSiv3D/issues/1264))
	- Windows において、コンソールウィンドウでテキストを選択した状態でコンソール出力すると、選択解除されるまでプログラムが停止した不具合を修正しました ([#1254](https://github.com/Siv3D/OpenSiv3D/issues/1254))
	- macOS / Linux の `System::LaunchFile()` が、名前にシングルクォーテーションを含むファイルを開けなかった不具合を修正しました ([#1283](https://github.com/Siv3D/OpenSiv3D/issues/1283))
	- `DebugCamera3D` がコンストラクタで指定した注視点と異なる位置を注視することがあったバグを修正しました ([#1255](https://github.com/Siv3D/OpenSiv3D/issues/1255), [#1274](https://github.com/Siv3D/OpenSiv3D/pull/1274))
	- `GlyphInfo::buffer` が取得方法によって設定されないことがあったバグを修正しました ([#1256](https://github.com/Siv3D/OpenSiv3D/issues/1256))
	- `Deserializer<MemoryViewReader>` で列挙型のデシリアライズができなかった不具合を修正しました ([#1288](https://github.com/Siv3D/OpenSiv3D/issues/1288))
	- 長方形内のテキスト描画において、省略時のドットの間隔がフォントサイズによって変わることがあったバグを修正しました ([#1273](https://github.com/Siv3D/OpenSiv3D/pull/1273))
	- シーン管理において、フェード時間を 0 に設定したとき二重にシーンの `.draw()` が呼ばれていたバグを修正しました ([#1258](https://github.com/Siv3D/OpenSiv3D/pull/1258))
	- 一部の環境で 2D 物理演算関連クラスがコンパイルエラーになった不具合を修正しました ([#1286](https://github.com/Siv3D/OpenSiv3D/issues/1286))
	- `RoundRect::drawShadow()` で `fill` が反映されない場合があったバグを修正しました ([#1269](https://github.com/Siv3D/OpenSiv3D/issues/1269))
	- 特殊なケースで `Indexed()` がダングリング参照を返すことがあった不具合を修正しました ([#1247](https://github.com/Siv3D/OpenSiv3D/pull/1247))
	- OpenGL ES 3.0 環境の不具合を修正しました ([#1244](https://github.com/Siv3D/OpenSiv3D/pull/1244))
	- `BinaryReader::lookahead()` でエラー時に読み込み位置が変化する可能性があった不具合を修正しました ([#1265](https://github.com/Siv3D/OpenSiv3D/issues/1265))
	- Linux のビルドにおける不具合を修正しました ([#1248](https://github.com/Siv3D/OpenSiv3D/pull/1248), [#1281](https://github.com/Siv3D/OpenSiv3D/pull/1281))
	- ドキュメントの誤植を修正しました ([#1262](https://github.com/Siv3D/OpenSiv3D/pull/1262), [#1270](https://github.com/Siv3D/OpenSiv3D/pull/1270))

	#### コントリビューション
	- [Raclamusi](https://github.com/Raclamusi): **長方形内のテキスト描画の不具合修正**, **`DebugCamera3D` の不具合修正**, `DiscreteSample()` の改善
	- [yaito3014](https://github.com/yaito3014): **`Indexed()` の不具合修正**, Linux ビルドの不具合修正
	- [Appbird](https://github.com/Appbird): **`Geometry2D::SmallestEnclosingCircle()` の実装**
	- [Aikawa3311](https://github.com/Aikawa3311): **2D 物理演算のセンサー機能の実装**
	- [sashi0034](https://github.com/sashi0034): **`Math::ClampAngle()` の実装**
	- [leaf2326](https://github.com/leaf2326): **`Rect::stretched(Arg::left)` 等のオーバーロードの実装**
	- [m4saka](https://github.com/m4saka): **`YesNo` の `operator==` の実装**
	- [yksake](https://github.com/yksake): シーン管理の不具合修正
	- [aFumihikoKobayashi](https://github.com/aFumihikoKobayashi): Linux ビルドの不具合修正
	- [yukidoke](https://github.com/yukidoke): ドキュメントの修正
	- [aoriika05](https://github.com/aoriika05): ドキュメントの修正

	### OpenSiv3D チャレンジ
	- \#19 SmallestEnclosingCircle: あぷりばーど, Nachia, Luke256, ラクラムシ, polyester, sasa


??? summary "v0.6.15 | 2024-07-03"

	#### 前バージョンからの更新ガイド
	- [v0.6.14 からのアップグレード手順（Windows）](https://zenn.dev/link/comments/8aaf45fc5ae077)

	#### 新機能
	- **指定した矩形内にテキストを描画できるかを、実際の描画を行わずに取得する関数 `font(text).fits(fontSize, rect)` を追加しました** ([#1202](https://github.com/Siv3D/OpenSiv3D/issues/1202))
		- [サンプル](https://zenn.dev/link/comments/76d0b5f552197f)
	- **Winows 版において、複数のファイルのドラッグを開始できるようにしました** ([#1218](https://github.com/Siv3D/OpenSiv3D/issues/1218))
		- [サンプル](https://zenn.dev/link/comments/4cb4905d78ae52)
	- ゼロベクトルの際に指定した値を返す `Vec2::normalized_or(Vec2)` 等を追加しました ([#1152](https://github.com/Siv3D/OpenSiv3D/issues/1152))
	- `Dialog::SaveFile()` でデフォルトのファイル名を指定できるようにしました ([#1199](https://github.com/Siv3D/OpenSiv3D/issues/1199))
	- `Circular` に `.lerp()` を追加しました ([#1203](https://github.com/Siv3D/OpenSiv3D/issues/1203), [#1206](https://github.com/Siv3D/OpenSiv3D/pull/1206))
	- BMP ファイルの準拠度を拡張しました ([#1204](https://github.com/Siv3D/OpenSiv3D/issues/1204), [#1207](https://github.com/Siv3D/OpenSiv3D/pull/1207))
	- `Texture` から `ID3D11Texture2D*` を取得する機能を追加しました ([#1219](https://github.com/Siv3D/OpenSiv3D/issues/1219))
	- `Image::inBounds()` を追加しました ([#1229](https://github.com/Siv3D/OpenSiv3D/issues/1229))
	- プログラムが IDE で実行されているかを取得する関数を追加しました ([#1230](https://github.com/Siv3D/OpenSiv3D/issues/1230))
	- `Camera2D::getGrabbedPos()` を追加しました ([#1242](https://github.com/Siv3D/OpenSiv3D/issues/1242))

	#### 仕様変更
	- **Windows 版について、コマンドプロンプトやスタートメニューの検索経由でアプリを起動しても、必ず実行ファイルのディレクトリがカレントディレクトリになるようにしました** ([#1227](https://github.com/Siv3D/OpenSiv3D/issues/1227))
	- **OpenAI::Char / Vision のデフォルトモデルを最新モデルの GPT-4o に変更しました** ([#1234](https://github.com/Siv3D/OpenSiv3D/issues/1234))
	- **ゼロベクトルに対する `.normalize()`, `.normalized()` はゼロベクトルを返すよう仕様変更しました** ([#1237](https://github.com/Siv3D/OpenSiv3D/issues/1237))
	- `LineString::calculateBuffer()` と `LineString::calculateRoundBuffer()` は 1 点の場合にも形状（正方形、円）を返すよう仕様変更しました ([#1209](https://github.com/Siv3D/OpenSiv3D/issues/1209), [#1210](https://github.com/Siv3D/OpenSiv3D/pull/1210))
	- LunaSVG のバージョンを v2.3.9 に更新しました ([#1211](https://github.com/Siv3D/OpenSiv3D/issues/1211))
	- 非 SSE 環境での `String::levenshteinDistanceFrom()` の実装を変更しました ([#1239](https://github.com/Siv3D/OpenSiv3D/issues/1239))
	- Windows 版の `Dialog::SaveFile()` で、デフォルトパスに `./` を指定できるようにしました ([#1240](https://github.com/Siv3D/OpenSiv3D/issues/1240))

	#### パフォーマンス向上
	- `Image::rotate90()`, `Image::rotate270()` などを高速化しました ([#1182](https://github.com/Siv3D/OpenSiv3D/issues/1182), [#1225](https://github.com/Siv3D/OpenSiv3D/pull/1225)))

	#### 不具合・バグ修正
	- **`Parse<double>(U"")` が例外を投げず `assert` となっていたバグを修正しました** ([#1226](https://github.com/Siv3D/OpenSiv3D/issues/1226))
	- **`Platform::Windows::Keyboard::GetEvents()` でクラッシュが発生することがあったバグを修正しました** ([#1221](https://github.com/Siv3D/OpenSiv3D/issues/1221))
	- `AsyncTask` のテンプレートパラメータ制約を修正しました ([#1213](https://github.com/Siv3D/OpenSiv3D/pull/1213)))
	- `Statistics::Mode()`, `Statistics::MultiMode()` のビルドエラーを修正しました ([#1214](https://github.com/Siv3D/OpenSiv3D/issues/1214))
	- `CSV` を `Reader` からロードする際のビルドエラーを修正しました ([#1215](https://github.com/Siv3D/OpenSiv3D/issues/1215))
	- ドキュメントの誤字を修正しました ([#1216](https://github.com/Siv3D/OpenSiv3D/pull/1216)))
	- `Point::length()`, `Point::lengthSq()` を整数オーバーフローしにくい実装に変更しました ([#1217](https://github.com/Siv3D/OpenSiv3D/issues/1217))
	- `FileSystem::Extension()`, `FileSystem::FileName()`, `FileSystem::BaseName()` の一貫性を改善しました ([#1223](https://github.com/Siv3D/OpenSiv3D/issues/1223))
	- エンジン初期化時に `RegisterDragDrop` に失敗してもエンジン初期化を続行するようにしました ([#1238](https://github.com/Siv3D/OpenSiv3D/issues/1238))
	- `StringView::lastIndexOfAny()` のデフォルト引数を 0 から npos に修正しました ([#1241](https://github.com/Siv3D/OpenSiv3D/issues/1241))

	#### コントリビューション
	- [Raclamusi](https://github.com/Raclamusi): **BMP ファイルの準拠度の拡張**, **`Image::rotate90()`, `Image::rotate270()` 等の高速化**, **`LineString::calculateBuffer()` と `LineString::calculateRoundBuffer()` の仕様変更**, `AsyncTask` の修正
	- [zaligan](https://github.com/zaligan): **`Circular::lerp()` の実装**
	- [Plinano](https://github.com/Plinano): ドキュメントの修正


??? summary "v0.6.14 | 2024-02-05"

	#### 前バージョンからの更新ガイド
	- [v0.6.13 からのアップグレード手順（Windows）](https://zenn.dev/link/comments/fd6585920f0136)

	#### 新機能
	- **`Shader::QuadWarp` を追加しました** ([#998](https://github.com/Siv3D/OpenSiv3D/issues/998), [#1183](https://github.com/Siv3D/OpenSiv3D/pull/1183))
		- [サンプル](https://zenn.dev/link/comments/00f59036fb188f)
	- **OpenAI の Vision, TextToSpeech を含む新しい API やモデルに対応しました** ([#1126](https://github.com/Siv3D/OpenSiv3D/issues/1126), [#1176](https://github.com/Siv3D/OpenSiv3D/pull/1176), [#1181](https://github.com/Siv3D/OpenSiv3D/issues/1181), [#1194](https://github.com/Siv3D/OpenSiv3D/issues/1194))
		- [サンプル](https://zenn.dev/link/comments/b4a570bf1e2a26)
	- **`Shape2D::Astroid()` を追加しました** ([#1191](https://github.com/Siv3D/OpenSiv3D/issues/1191))
		- [サンプル](https://zenn.dev/link/comments/bff536cae6cede)
	- **`Shader::GaussianBlur()` のフィルタサイズのオプションを追加しました** ([#1147](https://github.com/Siv3D/OpenSiv3D/issues/1147), [#1148](https://github.com/Siv3D/OpenSiv3D/pull/1148))
		- [サンプル](https://zenn.dev/link/comments/7308f87190f48c)
	- **シャドウのサンプルシェーダを追加しました** ([#1140](https://github.com/Siv3D/OpenSiv3D/issues/1140), [#1200](https://github.com/Siv3D/OpenSiv3D/issues/1200))
		- [サンプル](https://zenn.dev/link/comments/123b071121fbaf)
	- `MultiPlayer_Photon::sendEvent()` で送信先のプレイヤーを指定できるようにしました ([#1170](https://github.com/Siv3D/OpenSiv3D/issues/1170))
	- `Trail`, `TrailMotion` に `.clear()` を追加しました ([#1149](https://github.com/Siv3D/OpenSiv3D/issues/1149))
	- `Point`, `Vec2`, `Color`, `ColorF` 等に、1 要素だけを変更したコピーを返す `.with～()` を追加しました ([#1143](https://github.com/Siv3D/OpenSiv3D/issues/1143))
	- `MultiPolygon` に `.computeConvexHull()` を追加しました ([#1195](https://github.com/Siv3D/OpenSiv3D/issues/1195))
	- `MultiPolygon` に `.centroid()` を追加しました ([#1186](https://github.com/Siv3D/OpenSiv3D/issues/1186), [#1190](https://github.com/Siv3D/OpenSiv3D/pull/1190))
	- `MultiPolygon` に `.area()`, `.perimeter()` を追加しました ([#1185](https://github.com/Siv3D/OpenSiv3D/issues/1185), [#1187](https://github.com/Siv3D/OpenSiv3D/pull/1187))
	- `Rect`, `RectF` に `.rotate90()` 系のメンバ関数を追加しました ([#1094](https://github.com/Siv3D/OpenSiv3D/pull/1094))
	- `JSON` に `.getUTF8String()`, `.assignUTF8String()` を追加しました ([#1177](https://github.com/Siv3D/OpenSiv3D/issues/1177))
	- `PutText()` に `const char32*` や `StringView` を受け取るオーバーロードを追加しました ([#1159](https://github.com/Siv3D/OpenSiv3D/issues/1159))
	- `Mat3x3::Homography()` のオーバーロードを追加しました ([#1163](https://github.com/Siv3D/OpenSiv3D/issues/1163))
	- 将来の UI 機能の実装のために `Cursor::SetCapture()`, `Cursor::IsCaptured()` を追加しました ([#1045](https://github.com/Siv3D/OpenSiv3D/issues/1045))

	#### 仕様変更
	- **オーディオの同時再生可能数を 16 から 64 に増やしました** ([#1123](https://github.com/Siv3D/OpenSiv3D/issues/1123))
	- カラー絵文字のバージョンを Unicode 15.0 から Unicode 15.1 に引き上げました ([#1144](https://github.com/Siv3D/OpenSiv3D/issues/1144))
	- `DirectoryWatcher` のコンストラクタ引数を `FilePathView` に変更しました ([#1197](https://github.com/Siv3D/OpenSiv3D/issues/1197))
	- OpenAI 関連の API の名前空間と関数を設計しなおしました ([#1176](https://github.com/Siv3D/OpenSiv3D/issues/1176))
	- `FontAsset` の一部の関数の引数を `const String&` から `StringView` に変更しました ([#1158](https://github.com/Siv3D/OpenSiv3D/issues/1158))
	- fmt ライブラリを 8.1.1 から 10.1.1 に更新しました ([#1160](https://github.com/Siv3D/OpenSiv3D/issues/1160))
	- zstd ライブラリを 1.5.1 から 1.5.5 に更新しました ([#1161](https://github.com/Siv3D/OpenSiv3D/issues/1161), [#1162](https://github.com/Siv3D/OpenSiv3D/pull/1162))

	#### パフォーマンス向上
	- `MultiPolygon` のメンバ関数のオーバーヘッドを少しだけ削減しました ([#1188](https://github.com/Siv3D/OpenSiv3D/issues/1188), [#1189](https://github.com/Siv3D/OpenSiv3D/pull/1189))

	#### 不具合・バグ修正
	- `JSON` の複数の不具合を修正しました ([#1117](https://github.com/Siv3D/OpenSiv3D/issues/1117), [#1165](https://github.com/Siv3D/OpenSiv3D/pull/1165), [#1166](https://github.com/Siv3D/OpenSiv3D/issues/1166), [#1192](https://github.com/Siv3D/OpenSiv3D/issues/1192))
	- `Circle::drawArc(LineStyle::RoundCap)` で両端がグラデーションしなかった問題および内側と外側の色が反対だったバグを修正しました ([#1013](https://github.com/Siv3D/OpenSiv3D/issues/1013), [#1193](https://github.com/Siv3D/OpenSiv3D/issues/1193), [#1198](https://github.com/Siv3D/OpenSiv3D/pull/1198))
	- `RandomInt8()`, `RandomInt16()`, `RandomInt32()`, `RandomInt64()` が最小値を返さなかったバグを修正しました ([#1196](https://github.com/Siv3D/OpenSiv3D/issues/1196))
	- `Parse<double>(U"")` が `ParseError` を投げずに 0 を返していたバグを修正しました ([#1173](https://github.com/Siv3D/OpenSiv3D/issues/1173), [#1174](https://github.com/Siv3D/OpenSiv3D/pull/1174))
	- `MultiPlayer_Photon` で `joinRoomReturn` が呼ばれなかったバグを修正しました ([#1169](https://github.com/Siv3D/OpenSiv3D/issues/1169))
	- `Cursor::SetPos()` がシーンサイズと適切に連動していなかったバグを修正しました ([#1167](https://github.com/Siv3D/OpenSiv3D/issues/1167), [#1168](https://github.com/Siv3D/OpenSiv3D/pull/1168))
	- `RoundRect::drawShadow()` において、`blur` が大きいときの描画の乱れを修正しました ([#1164](https://github.com/Siv3D/OpenSiv3D/pull/1164))
	- `ConstantBuffer` のコピーが正しく行われなかったバグを修正しました ([#1154](https://github.com/Siv3D/OpenSiv3D/issues/1154), [#1155](https://github.com/Siv3D/OpenSiv3D/pull/1155))
	- `RectF` の一部のコンストラクタで `int32` を受け取ると縮小変換の警告が出た問題を修正しました ([#1184](https://github.com/Siv3D/OpenSiv3D/issues/1184))
	- 一部の Windows 環境で VSync 無効時にフレームレートが制限されていた問題を修正しました ([#1179](https://github.com/Siv3D/OpenSiv3D/pull/1179))
	- `ImageDecoder::GetImageInfo()` で取得される GIF 画像の解像度が正しくなかったバグを修正しました ([#1172](https://github.com/Siv3D/OpenSiv3D/pull/1172))
	- 幅や高さが 16384px より大きい GIF ファイルを読み込むとクラッシュした問題を修正しました ([#1171](https://github.com/Siv3D/OpenSiv3D/pull/1171))
	- `DebugCamera3D::drawTouchUI()` に `const` がついていなかった問題を修正しました ([#1091](https://github.com/Siv3D/OpenSiv3D/issues/1091))
	- 図形クラスの一部の constepr メンバ関数が非アクティブな共用体メンバを使用していた問題を修正しました ([#1139](https://github.com/Siv3D/OpenSiv3D/issues/1139), [#1141](https://github.com/Siv3D/OpenSiv3D/issues/1141))
	- フルスクリーン + レンダーターゲット変更時にメッセージボックスが適切に表示されなかった問題を修正しました ([#1150](https://github.com/Siv3D/OpenSiv3D/issues/1150))

	#### コントリビューション
	- [Ogame3334](https://github.com/Ogame3334): **`MultiPolygon` の機能追加・改善**
	- [m4saka](https://github.com/m4saka): VSync 関連の問題の修正, GIF 関連の不具合の修正
	- [Raclamusi](https://github.com/Raclamusi): **`Circle::drawArc()` の修正**, 図形クラスの `constexpr` 対応の改善
	- [sashi0034](https://github.com/sashi0034): `Cursor::SetPos()` の修正
	- [comefrombottom](https://github.com/comefrombottom): `Rect`, `RectF` への機能追加


??? summary "v0.6.13 | 2023-11-15"

	#### 新機能
	- Visual Studio 2022 17.8 に対応しました ([#1136](https://github.com/Siv3D/OpenSiv3D/issues/1136))
	- `DynamicTexture` でミップマップ生成を行えるようになりました ([#1130](https://github.com/Siv3D/OpenSiv3D/issues/1130), [#1135](https://github.com/Siv3D/OpenSiv3D/pull/1135))
	- `RenderTexture`, `MSRenderTexture` でミップマップ生成を行えるようになりました ([#1129](https://github.com/Siv3D/OpenSiv3D/issues/1129), [#1134](https://github.com/Siv3D/OpenSiv3D/pull/1134))
	- `TextureFormat::R16G16_Unorm` を追加しました ([#1122](https://github.com/Siv3D/OpenSiv3D/issues/1122))

	#### 仕様変更
	- `Texture::isMipped()` を `Texture::hasMipMap()` に変更しました ([#1131](https://github.com/Siv3D/OpenSiv3D/issues/1131))

	#### ユーザビリティ向上	
	- Windows 版で `<Siv3D/DLL.hpp>` をインクルードしたときに `Polygon` や `RoundRect` が使用できなくなる不便を修正しました ([#1120](https://github.com/Siv3D/OpenSiv3D/issues/1120))
	- `Font` の一部コンストラクタに `explicit` がついていなかった不便を修正しました ([#1115](https://github.com/Siv3D/OpenSiv3D/issues/1115))

	#### パフォーマンス向上
	- `Texture` のミップマップ生成を GPU で行うようにしました。`TextureDesc::Mipped` を指定した画像や、絵文字、アイコンからのテクスチャ作成が大幅に高速化されます ([#1133](https://github.com/Siv3D/OpenSiv3D/issues/1133), [#1137](https://github.com/Siv3D/OpenSiv3D/pull/1137))
	- `Polygon` の `scale` 系関数のバウンディングボックス再計算の速度と精度を改善しました ([#1069](https://github.com/Siv3D/OpenSiv3D/issues/1069), [#1132](https://github.com/Siv3D/OpenSiv3D/pull/1132))

	#### 不具合・バグ修正
	- macOS (Apple Silicon) で音声が再生できなくなっていたバグを修正しました ([#1127](https://github.com/Siv3D/OpenSiv3D/issues/1127))
	- OpenGL バックエンドで、テクスチャ描画時にミップマップが使われないことがあったバグを修正しました ([#1128](https://github.com/Siv3D/OpenSiv3D/issues/1128))
	- `Subdivision2D::findNearest()` の一部のケースで結果の座標が格納されなかったバグを修正しました ([#1116](https://github.com/Siv3D/OpenSiv3D/issues/1116))
	- `Subdivision2D::initDelaunay()` が `m_addedPoints` をリセットしていなかったバグを修正しました ([#1114](https://github.com/Siv3D/OpenSiv3D/issues/1114))
	- `Rect`, `RectF` の一部の constexpr メンバ関数が、コンパイル時計算で使用できなかった不具合を修正しました ([#1118](https://github.com/Siv3D/OpenSiv3D/issues/1118))

	#### コントリビューション
	- [Raclamusi](https://github.com/Raclamusi): **`Polygon` の `scale` 系関数の改善**


??? summary "v0.6.12 | 2023-09-27"

	#### 新機能
	- `Rect`, `RectF` から角度を指定して平行四辺形の `Quad` を作る関数を追加しました ([#1056](https://github.com/Siv3D/OpenSiv3D/issues/1056), [#1070](https://github.com/Siv3D/OpenSiv3D/pull/1070))
		- [サンプル](https://zenn.dev/link/comments/2f28a4366b3b2f)
	- 2D および 3D の Morton Order を計算する関数を追加しました ([#1072](https://github.com/Siv3D/OpenSiv3D/issues/1072))
		- [サンプル](https://zenn.dev/link/comments/a9015b37d00cc2)
	- Windows 11 で IME を使用する際に、変換候補ウィンドウを描画する `SimpleGUI::IMECandidateWindow()` を追加しました ([#1106](https://github.com/Siv3D/OpenSiv3D/issues/1106), [#1107](https://github.com/Siv3D/OpenSiv3D/pull/1107))
		- [サンプル](https://zenn.dev/link/comments/f9c5f75094806f)
	- `Point`, `Vector2D` に `.rotate90(N)` などを追加しました ([#1093](https://github.com/Siv3D/OpenSiv3D/issues/1093), [#1102](https://github.com/Siv3D/OpenSiv3D/pull/1102))
		- [サンプル](https://zenn.dev/link/comments/664156e39d1e3f)
	- `SceneManager::init()` に最初のフェードイン時間を指定できるようにしました ([#1078](https://github.com/Siv3D/OpenSiv3D/issues/1078), [#1081](https://github.com/Siv3D/OpenSiv3D/pull/1081))
		- [サンプル](https://zenn.dev/link/comments/1dae79127cb0c5)
	- 2 つの `Image` を比較する `==`, `!=` を追加しました ([#1099](https://github.com/Siv3D/OpenSiv3D/issues/1099))
	- `Image::rotate90(N)` オーバーロードを追加しました ([#1089](https://github.com/Siv3D/OpenSiv3D/issues/1089), [#1090](https://github.com/Siv3D/OpenSiv3D/pull/1090))
	- `Point3D` 型を追加しました ([#1073](https://github.com/Siv3D/OpenSiv3D/issues/1073), [#1074](https://github.com/Siv3D/OpenSiv3D/pull/1074))
	- `Point` 型に `operator%` と `operator%=` を追加しました ([#1055](https://github.com/Siv3D/OpenSiv3D/issues/1055), [#1058](https://github.com/Siv3D/OpenSiv3D/pull/1058))

	#### 仕様変更
	- `ScreenCapture::` の一部関数の引数の一貫性を改善しました ([#1080](https://github.com/Siv3D/OpenSiv3D/issues/1080))
	- サードパーティライブラリを更新しました ([#1100](https://github.com/Siv3D/OpenSiv3D/issues/1100))

	#### パフォーマンス向上
	- SDF/MSDF 方式のフォントのプリロードコストを大幅に改善しました ([#1095](https://github.com/Siv3D/OpenSiv3D/issues/1095), [#1096](https://github.com/Siv3D/OpenSiv3D/pull/1096))
	- `Image::clipped()` などの実行時性能を大幅に改善しました ([#1087](https://github.com/Siv3D/OpenSiv3D/issues/1087), [#1108](https://github.com/Siv3D/OpenSiv3D/pull/1108))
	- `Shape2D::indices()` が参照ではなく値を返していた非効率を修正しました ([#1065](https://github.com/Siv3D/OpenSiv3D/issues/1065), [#1071](https://github.com/Siv3D/OpenSiv3D/pull/1071))
	- 右辺値の `Array`, `String`, `Polygon` 等のための効率的なメンバ関数オーバーロードを追加しました ([#1059](https://github.com/Siv3D/OpenSiv3D/issues/1059), [#1060](https://github.com/Siv3D/OpenSiv3D/issues/1060), [#1064](https://github.com/Siv3D/OpenSiv3D/pull/1064))

	#### 不具合・バグ修正
	- 複雑な字体の SDF/MSDF フォントの文字にノイズが入ることがあった不具合を修正しました ([#1082](https://github.com/Siv3D/OpenSiv3D/issues/1082), [#1096](https://github.com/Siv3D/OpenSiv3D/pull/1096))
	- ウィンドウが非アクティブなとき、キーを押していないのに `.pressed()` が `true` を返すことがあったバグを修正しました ([#1083](https://github.com/Siv3D/OpenSiv3D/issues/1083))
	- v0.6.11 で `RoundRect::drawShadow()` の一部ケースの描画に異常が生じたバグを修正しました ([#1076](https://github.com/Siv3D/OpenSiv3D/issues/1076))
	- `RandomHSV(hMinMax, sMinMax, vMinMax)` の結果が正しくなかったバグを修正しました ([#1084](https://github.com/Siv3D/OpenSiv3D/issues/1084), [#1088](https://github.com/Siv3D/OpenSiv3D/pull/1088))
	- `String::trimmed()` に空白文字のみで構成される文字列を渡すと実行時エラーになったバグを修正しました ([#1101](https://github.com/Siv3D/OpenSiv3D/issues/1101))
	- Windows 版における IME の挙動を改善しました ([#1104](https://github.com/Siv3D/OpenSiv3D/issues/1104), [#1107](https://github.com/Siv3D/OpenSiv3D/pull/1107))
	- `P2Body` が空の `Polygon` を持つときの実行時エラーを修正しました ([#1075](https://github.com/Siv3D/OpenSiv3D/issues/1075))
	- `Wave::MaxSampleRate` が `Wave::MaxSamlpeRate` になっていた誤字を修正しました ([#1105](https://github.com/Siv3D/OpenSiv3D/pull/1105))
	- `PhongMaterial::ambientColor` が `PhongMaterial::amibientColor` になっていた誤字を修正しました ([#1105](https://github.com/Siv3D/OpenSiv3D/pull/1105))
	- `AsyncTask::wait_until()` がコンパイルエラーを起こすバグを修正しました ([#1068](https://github.com/Siv3D/OpenSiv3D/issues/1068))
	- 一部のシェーダファイルの誤字を修正しました ([#1105](https://github.com/Siv3D/OpenSiv3D/pull/1105))
	- ドキュメンテーションの誤りを修正しました ([#1054](https://github.com/Siv3D/OpenSiv3D/pull/1054))

	#### コントリビューション
	- [Raclamusi](https://github.com/Raclamusi): **右辺値の `Array` などのクラス向けの効率的なメンバ関数オーバーロードの実装**, **`Image::clipped()` 等の高速化**, 一部の関数の戻り値の不具合の修正
	- [yama-can](https://github.com/yama-can): **`Rect::skewedX()` 等の実装**, **`SceneManager::init()` のフェード時間指定の実装**
	- [comefrombottom](https://github.com/comefrombottom): **`Image::rotate90(n)` 等の実装**, **`Point` や `Vector2D` の `.rotate90()` の実装**
	- [ozone010](https://github.com/ozone010): **`Point` 型に `operator%` と `operator%=` を追加**
	- [voidproc](https://github.com/voidproc): ドキュメントとソースコードの誤字修正
	- [naga-karupi](https://github.com/naga-karupi): `RandomHSV()` のバグ修正
	- [sfpgmr](https://github.com/sfpgmr): 内部コードの修正
	- [aoriika05](https://github.com/aoriika05): ドキュメントの修正


??? summary "v0.6.11 | 2023-08-11"

	#### 新機能
	- 2D の軌跡を描画する機能を追加しました ([#1006](https://github.com/Siv3D/OpenSiv3D/issues/1006), [#1043](https://github.com/Siv3D/OpenSiv3D/pull/1043))
		- [サンプル](https://zenn.dev/link/comments/7de4b612bbc195)
	- 「9 スライス」を簡単に扱えるクラスを追加しました ([#1030](https://github.com/Siv3D/OpenSiv3D/issues/1030), [#1036](https://github.com/Siv3D/OpenSiv3D/pull/1036))
		- [サンプル](https://zenn.dev/link/comments/f62cd1a9e45fb0)
	- 目標に追従するシンプルな 3D カメラクラス `SimpleFollowCamera3D` を追加しました ([#1048](https://github.com/Siv3D/OpenSiv3D/issues/1048), [#1049](https://github.com/Siv3D/OpenSiv3D/pull/1049))
		- [サンプル](https://zenn.dev/link/comments/7034d12f405985)
	- OpenAI Chat API のモデル定数に `GPT3_5_Turbo_16K`（`gpt-3.5-turbo-16k`）を追加しました ([#1050](https://github.com/Siv3D/OpenSiv3D/issues/1055))
	- `Rect`, `RectF`, `RoundRectF` の `.drawShadow()` に、内部をすべて塗りつぶさないオプションを追加しました ([#1039](https://github.com/Siv3D/OpenSiv3D/issues/1039))
	- ベクトルの各要素間で最大値 / 最小値を計算する `Math::Max()`, `Math::Min()` を追加しました ([#1032](https://github.com/Siv3D/OpenSiv3D/issues/1032))
	- `Line::normalizedVector()` を追加しました ([#1029](https://github.com/Siv3D/OpenSiv3D/issues/1029))
	- `Triangle::isClockwise()` を追加しました ([#1028](https://github.com/Siv3D/OpenSiv3D/issues/1028))
	- `Transition::reset()` を追加しました ([#1025](https://github.com/Siv3D/OpenSiv3D/issues/1025))
	- `Math::MoveTowards()` を追加しました ([#1024](https://github.com/Siv3D/OpenSiv3D/issues/1024))
	- 3 つの頂点から時計回りの `Triangle` を作成する `Triangle::FromPoints(p0, p1, p2)` を追加しました ([#1015](https://github.com/Siv3D/OpenSiv3D/issues/1015))
	- `Quaternion::RollPitchYaw()` に、`Vec3` を引数にとるオーバーロードを追加しました ([#1014](https://github.com/Siv3D/OpenSiv3D/issues/1014))

	#### ユーザビリティ向上	
	- 初心者が踏みやすいランタイムエラーを踏んだ際にトラブルシューティングの Web ページを開く機能を追加しました ([#1007](https://github.com/Siv3D/OpenSiv3D/issues/1007), [#1034](https://github.com/Siv3D/OpenSiv3D/issues/1034), [#1035](https://github.com/Siv3D/OpenSiv3D/pull/1035))
	- エンジン起動前のアセットクラス初期化検知の対象を拡大しました ([#1047](https://github.com/Siv3D/OpenSiv3D/issues/1047))
	- `SimpleGUI::TextBox()` の挙動を改善しました ([#997](https://github.com/Siv3D/OpenSiv3D/issues/997))

	#### 仕様変更
	- `Circle::drawPie()`, `Circle::drawArc()`, `Circle::drawSegmentFromAngles()` で負の angle を指定した際にも、通常通り時計回りの三角形が描画されるようにしました ([#1042](https://github.com/Siv3D/OpenSiv3D/issues/1042))

	#### パフォーマンス向上
	- `Circle`, `Rect`, `RectF`, `RoundrRect` の `.drawShadow()` の CPU コストを 20～50 % 削減しました ([#1037](https://github.com/Siv3D/OpenSiv3D/issues/1037))

	#### 不具合・バグ修正
	- Windows 版でプログラムの終了時に mutex のエラーでクラッシュすることがあったバグを修正しました ([#1033](https://github.com/Siv3D/OpenSiv3D/issues/1033))
	- `DrawableText::getXAdvances()` の結果に改行文字の分が含まれていなかったバグを修正しました ([#1038](https://github.com/Siv3D/OpenSiv3D/issues/1038))
	- `RoundRect::drawShadow()` で `spread` を指定すると描画の r がずれていた不具合を修正しました ([#1040](https://github.com/Siv3D/OpenSiv3D/issues/1040))
	- `Font::renderPolygon()` で輪郭と穴が逆転することがあったバグを修正しました ([#1019](https://github.com/Siv3D/OpenSiv3D/issues/1019), [#1027](https://github.com/Siv3D/OpenSiv3D/pull/1027))
	- `Font` 描画時にタブ空白の間隔が乱れることがあったバグを修正しました ([#1002](https://github.com/Siv3D/OpenSiv3D/issues/1002), [#1026](https://github.com/Siv3D/OpenSiv3D/pull/1026))
	- `Circle::pieAsPolygon()` と `Circle::arcAsPolygon()` が反時計回り頂点の `Polygon` を生成していたバグを修正しました ([#1041](https://github.com/Siv3D/OpenSiv3D/issues/1041))
	- 一部のコンパイラでのビルドの問題を修正しました ([#1021](https://github.com/Siv3D/OpenSiv3D/pull/1021))
	- `SimpleGUI::TextBoxAt()` での Home, End キーの動作を修正しました ([#999](https://github.com/Siv3D/OpenSiv3D/pull/999))
	- ドキュメントや引数の誤字を修正しました ([#1016](https://github.com/Siv3D/OpenSiv3D/issues/1016), [#1017](https://github.com/Siv3D/OpenSiv3D/pull/1017))

	#### コントリビューション
	- [Raclamusi](https://github.com/Raclamusi): **`Font::renderPolygon()` のバグを修正**, **`Font` のタブ空白描画のバグを修正**
	- [yksake](https://github.com/yksake): `SimpleGUI::TextBoxAt()` の挙動改善
	- [polyester-CTRL](https://github.com/polyester-CTRL): 一部のコンパイラでのビルドの問題の修正
	- [voidproc](https://github.com/voidproc): ドキュメントや引数の誤字修正


??? summary "v0.6.10 | 2023-05-17"
	#### 重要
	- Visual Studio 2022 17.6 でのビルドの問題を修正しました ([#1011](https://github.com/Siv3D/OpenSiv3D/issues/1011))
		- v0.6.9 以前のプロジェクトでは、[プロジェクトのプロパティから「ISO C++23 標準ライブラリモジュールのビルド」を無効にする](https://github.com/Siv3D/OpenSiv3D/issues/1011){:target="_blank"}ことで解決します。

	#### 新機能
	- `JSON` において Binary Values に対応しました ([#1010](https://github.com/Siv3D/OpenSiv3D/issues/1010))

	#### パフォーマンス向上
	- `Optional::Optional(Optional<U>&&)` の実装を改善しました ([#1008](https://github.com/Siv3D/OpenSiv3D/issues/1008))

	#### 不具合・バグ修正
	- `ParseOr<double>()` と `ParseOpt<double>()` が `float` 精度で行われていたバグを修正しました ([#1009](https://github.com/Siv3D/OpenSiv3D/issues/1009))
	- バイナリフォーマット (BSON/CBOR/MessagePack) から作成した JSON の要素を一部適切に読み込めなかったバグを修正しました ([#1010](https://github.com/Siv3D/OpenSiv3D/issues/1010))

??? summary "v0.6.9 | 2023-04-16"
	#### 新機能
	- 表を描画する `SimpleTable` を追加しました ([#988](https://github.com/Siv3D/OpenSiv3D/issues/988), [#991](https://github.com/Siv3D/OpenSiv3D/pull/991), [#992](https://github.com/Siv3D/OpenSiv3D/pull/992))
		- [サンプル](https://zenn.dev/link/comments/6a2a05060c1dda)
	- 複数行のテキストボックス `SimpleGUI::TextArea()` を追加しました ([#789](https://github.com/Siv3D/OpenSiv3D/issues/789), [#994](https://github.com/Siv3D/OpenSiv3D/pull/994), [#996](https://github.com/Siv3D/OpenSiv3D/issues/996))
		- [サンプル](https://zenn.dev/link/comments/98f2844dae5705)

	#### 仕様変更
	- `Timer::isRunnning()` は 0 に達したときに `false` を返すよう仕様変更しました ([#987](https://github.com/Siv3D/OpenSiv3D/issues/987))
	- `Grid::resize(w, 0)` および `Grid::resize(0, h)` は `w` と `h` を保存するようにしました ([#989](https://github.com/Siv3D/OpenSiv3D/issues/989))

	#### パフォーマンス向上
	- `Array::fetch()` 等の実装を改善しました ([#990](https://github.com/Siv3D/OpenSiv3D/pull/990))

	#### 不具合・バグ修正
	- `Array::fetch()` 等の未定義動作を修正しました ([#990](https://github.com/Siv3D/OpenSiv3D/pull/990))
	- `Grid::assign()` のバグを修正しました ([#995](https://github.com/Siv3D/OpenSiv3D/issues/995))

	#### コントリビューション
	- [tomolatoon](https://github.com/tomolatoon): `Array::fetch()` 等の修正・改善
	- [m4saka](https://github.com/m4saka): SimpleTable の typo 修正

??? summary "v0.6.8 | 2023-04-01"
	#### 新機能
	- 指定したファイルをデフォルトのアプリケーションで開く `System::LaunchFile(fileName)` を追加しました ([#888](https://github.com/Siv3D/OpenSiv3D/issues/888), [#981](https://github.com/Siv3D/OpenSiv3D/pull/981))
		- [サンプル](https://zenn.dev/link/comments/8c7c4076bfbb6d)
	- 指定したファイルをテキストエディタで開く `System::LaunchFileWithTextEditor(fileName)` を追加しました ([#888](https://github.com/Siv3D/OpenSiv3D/issues/888), [#981](https://github.com/Siv3D/OpenSiv3D/pull/981))
		- [サンプル](https://zenn.dev/link/comments/4b67ba5b069306)
	- 同梱カラー絵文字を Unicode 14.0 から Unicode 15.0 に更新しました ([#980](https://github.com/Siv3D/OpenSiv3D/issues/980))
		- [サンプル](https://zenn.dev/link/comments/7e722e60bed8dc)
	- 同梱 Material Design Icons を v6.5.95 から v7.2.96 に更新しました（700 種類のアイコンが追加） ([#980](https://github.com/Siv3D/OpenSiv3D/issues/980))
		- [サンプル](https://zenn.dev/link/comments/adaafc47825fb1)
	- OpenAI の Embeddings API を扱う機能を追加しました ([#982](https://github.com/Siv3D/OpenSiv3D/issues/982))
		- [サンプル](https://zenn.dev/link/comments/6a8483ccd76a7f)
	- ユーザのコンピュータ名や名前、言語を取得する関数を追加しました ([#678](https://github.com/Siv3D/OpenSiv3D/issues/678), [#968](https://github.com/Siv3D/OpenSiv3D/issues/968), [#974](https://github.com/Siv3D/OpenSiv3D/pull/974))
		- [サンプル](https://zenn.dev/link/comments/381ae3d6c2b201)
	- ドライブの情報を取得する関数を追加しました（Windows 版）([#709](https://github.com/Siv3D/OpenSiv3D/issues/709), [#978](https://github.com/Siv3D/OpenSiv3D/pull/978))
		- [サンプル](https://zenn.dev/link/comments/8d3df3d50eb1fa)
	- インターネットに接続されているかを返す `Network::IsConnected()` を追加しました ([#975](https://github.com/Siv3D/OpenSiv3D/issues/975), [#976](https://github.com/Siv3D/OpenSiv3D/pull/976))

	#### ユーザビリティ向上	
	- `SimpleGUI::ListBox()` の項目を上下キーで選択できるようになりました ([#984](https://github.com/Siv3D/OpenSiv3D/issues/984))

	#### 不具合・バグ修正
	- フォールバックに設定した絵文字が特定条件下で正しく表示されなかったバグを修正しました ([#971](https://github.com/Siv3D/OpenSiv3D/issues/971), [#973](https://github.com/Siv3D/OpenSiv3D/pull/973))
	- `SimpleGUI::ListBox()` の項目数が多いときに、スクロールバーのつまみが細くなる / 消失していたバグを修正しました ([#985](https://github.com/Siv3D/OpenSiv3D/issues/985))

	#### コントリビューション
	- [Raclamusi](https://github.com/Raclamusi): **絵文字フォントのフォールバックのバグを修正**


??? summary "v0.6.7 | 2023-03-18"
	#### 新機能
	- OpenAI API (Chat, Image) を扱う機能を追加しました ([#957](https://github.com/Siv3D/OpenSiv3D/issues/957))
		- [サンプル](https://zenn.dev/link/comments/39ca09ff7febbf)
	- OSC 通信を扱う機能を追加しました ([#515](https://github.com/Siv3D/OpenSiv3D/issues/515), [#919](https://github.com/Siv3D/OpenSiv3D/issues/919), [#922](https://github.com/Siv3D/OpenSiv3D/pull/922))
		- [サンプル](https://zenn.dev/link/comments/37cb05690d7a9c)
	- 円を割線で切り取った形を描く関数 `Circle::drawSegment()`, `Circle::drawSegmentFromAngles()` を追加しました ([#956](https://github.com/Siv3D/OpenSiv3D/issues/956))
		- [サンプル](https://zenn.dev/link/comments/3deaea6384f9e9)
	- 長方形を斜め方向のグラデーションで描く関数を追加しました ([#955](https://github.com/Siv3D/OpenSiv3D/issues/955))
		- [サンプル](https://zenn.dev/link/comments/b184c90a6682d5)
	- SimpleMenuBar が項目のチェックに対応しました ([#948](https://github.com/Siv3D/OpenSiv3D/issues/948))
		- [サンプル](https://zenn.dev/link/comments/a1638e9fd4ca82)
	- JSON のバリデーションを行うクラス `JSONValidator` を追加しました ([#931](https://github.com/Siv3D/OpenSiv3D/issues/931), [#959](https://github.com/Siv3D/OpenSiv3D/pull/959))
		- [サンプル](https://zenn.dev/link/comments/9a60ef0856e4fa)
	- コマンドライン引数を直接取得する関数 `System::GetArgc()`, `System::GetArgv()` を追加しました ([#964](https://github.com/Siv3D/OpenSiv3D/issues/964))
		- [サンプル](https://zenn.dev/link/comments/9c62e50a751464)
	- アドオンの実行優先度を `update()` と `draw()` を個別に指定できるようにしました ([#949](https://github.com/Siv3D/OpenSiv3D/issues/949))
		- [サンプル](https://zenn.dev/link/comments/d5204d4530f196)
	- SimpleHTTP の非同期リクエストに関する関数を拡充しました ([#911](https://github.com/Siv3D/OpenSiv3D/issues/911), [#962](https://github.com/Siv3D/OpenSiv3D/issues/962))
	- 角度を `[0, TwoPi)` または `[-Pi, Pi)` の範囲に正規化する `Math::NormalizeAngle()` 関数を追加しました ([#927](https://github.com/Siv3D/OpenSiv3D/issues/927))
	- 時間に対して、デューティー比を指定した矩形波を返す `Periodic::Pulse0_1()` / `Periodic::Pulse1_1()` を追加しました ([#966](https://github.com/Siv3D/OpenSiv3D/issues/966), [#967](https://github.com/Siv3D/OpenSiv3D/pull/967))
	- `Input` をシリアライズする `Input::Serialize()`, `Input::Deserialize()` を追加しました ([#920](https://github.com/Siv3D/OpenSiv3D/issues/920))
	- `IAddon` に `postPresent()` を追加しました ([#942](https://github.com/Siv3D/OpenSiv3D/issues/942))
	- `TextEditState` がシリアライズに対応しました（文字列のみ保存） ([#930](https://github.com/Siv3D/OpenSiv3D/issues/930))
	- Base64 のデコードに妥当性チェックを追加しました ([#845](https://github.com/Siv3D/OpenSiv3D/issues/845), [#961](https://github.com/Siv3D/OpenSiv3D/pull/961))
	- `Math::Damp()`, `Math::SmoothDamp()` が `ColorF` 型に対応しました ([#947](https://github.com/Siv3D/OpenSiv3D/pull/947))
	- C++23 との一貫性のため、`StringView`, `String`, `Array` に、`.includes()`, `.includes_if()` と同機能の `.contains()`, `.contains_if()` を追加しました ([#944](https://github.com/Siv3D/OpenSiv3D/issues/944))
	- `JSON` の機能を強化しました ([#925](https://github.com/Siv3D/OpenSiv3D/pull/925), [#931](https://github.com/Siv3D/OpenSiv3D/issues/931), [#959](https://github.com/Siv3D/OpenSiv3D/pull/959))

	#### ユーザビリティ向上	
	- アセットの毎フレーム作成・破棄の問題をメッセージボックスで通知するようにしました ([#926](https://github.com/Siv3D/OpenSiv3D/issues/926))

	#### 仕様変更
	- デフォルトのサンプルプログラム Hello, Siv3D をリニューアルしました ([#940](https://github.com/Siv3D/OpenSiv3D/issues/940))
	- Photon アドオンを最新の Photon SDK に対応させました ([#954](https://github.com/Siv3D/OpenSiv3D/issues/954))

	#### パフォーマンス向上
	- Base64 のデコードを高速化しました ([#845](https://github.com/Siv3D/OpenSiv3D/issues/845), [#961](https://github.com/Siv3D/OpenSiv3D/pull/961))
	- `Array` や `MultiPolygon` などの一部のメンバ関数のパフォーマンスを改善しました ([#948](https://github.com/Siv3D/OpenSiv3D/pull/948))
	- `Array::includes()`, `Array::contains()` をパフォーマンスを改善しました ([#945](https://github.com/Siv3D/OpenSiv3D/issues/945))

	#### 不具合・バグ修正
	- macOS でカレントディレクトリにファイルを作成できないことがあった不具合を修正しました ([#963](https://github.com/Siv3D/OpenSiv3D/issues/963))
	- `Optional` の挙動が `std::optional` と異なっていたバグを修正しました ([#938](https://github.com/Siv3D/OpenSiv3D/issues/938), [#939](https://github.com/Siv3D/OpenSiv3D/pull/939))
	- `HTTPResponse` の内容がリダイレクト前のデータになっていたバグを修正しました ([#958](https://github.com/Siv3D/OpenSiv3D/issues/958))
	- `IReader` を引数に取る `JSON::Load()` がコンパイルエラーになるバグを修正しました ([#937](https://github.com/Siv3D/OpenSiv3D/issues/937))
	- `Polygon::calculateBuffer()`, `Polygon::calculateRoundBuffer()` が失敗することがあったバグを修正しました ([#965](https://github.com/Siv3D/OpenSiv3D/issues/965))
	- `SimpleMenuBar` 上でのマスカーソルの挙動を改善しました ([#950](https://github.com/Siv3D/OpenSiv3D/pull/950))
	- `App/example/script/piano.as` スクリプトで音が鳴らなかったバグを修正しました ([#935](https://github.com/Siv3D/OpenSiv3D/pull/935))
	- `PlayingCard::Pack` をデフォルト構築してジョーカーのカードを描画するとクラッシュしたバグを修正しました ([#921](https://github.com/Siv3D/OpenSiv3D/issues/921))
	- `GeoJSONGeometry::get()` が使えなかったバグを修正しました ([#933](https://github.com/Siv3D/OpenSiv3D/issues/933), [#934](https://github.com/Siv3D/OpenSiv3D/pull/934))
	- シリアライズの一部マクロのバグを修正しました ([#923](https://github.com/Siv3D/OpenSiv3D/pull/923))
	- Arch Linux でのビルドの問題を修正しました ([#917](https://github.com/Siv3D/OpenSiv3D/issues/917), [#918](https://github.com/Siv3D/OpenSiv3D/pull/918))

	#### コントリビューション
	- [nokotan](https://github.com/nokotan): **Web 版を更新**
	- [tomolatoon](https://github.com/tomolatoon): **`JSONValidator` を追加**, **`JSON` の機能強化**
	- [m4saka](https://github.com/m4saka): **`Optional` のバグを修正**
	- [Raclamusi](https://github.com/Raclamusi): **Base64 のデコードの改善**, `Array` 等の性能改善
	- [voidproc](https://github.com/voidproc): **Periodic 関数の追加**
	- [yksake](https://github.com/yksake): SimpleMenuBar の挙動改善、ドキュメントの改善
	- [sthairno](https://github.com/sthairno): `GeoJSONGeometry` のバグを修正
	- [Aikawa3311](https://github.com/Aikawa3311): スクリプトを修正
	- [sfpgmr](https://github.com/sfpgmr): シリアライズ API のバグを修正
	- [hashitaku](https://github.com/hashitaku) Arch Linux でのビルドを修正


??? summary "v0.6.6 | 2022-11-22"
	#### 新機能
	- シンプルなメニューバーを扱う機能を追加しました ([#898](https://github.com/Siv3D/OpenSiv3D/issues/898))
		- [サンプル](https://zenn.dev/link/comments/2deb08c8839b7c)
	- 入力処理を打ち切る機能を追加しました ([#897](https://github.com/Siv3D/OpenSiv3D/issues/897))
		- [サンプル](https://zenn.dev/link/comments/0199a3d4069432)
	- `std::map` の置き換えとなる `OrderedTable` 型を追加しました ([#909](https://github.com/Siv3D/OpenSiv3D/pull/909))
		- [サンプル](https://zenn.dev/link/comments/310475d5bb43b5)
	- `RoundRect::draw()` において、上下の色グラデーションを指定できるようになりました ([#906](https://github.com/Siv3D/OpenSiv3D/issues/906))
		- [サンプル](https://zenn.dev/link/comments/b075516ffa7b4e)
	- `Rect::drawFrame()`, `RectF::drawFrame()`, `RoundRect::draw()`, `RoundRect::drawFrame()` において、上下の色グラデーションを指定できるようになりました ([#906](https://github.com/Siv3D/OpenSiv3D/issues/906))
		- [サンプル](https://zenn.dev/link/comments/ea380caf8b5979)
	- （Windows 版）タスクバーにタスクの進捗状況を表示する機能を追加しました ([#904](https://github.com/Siv3D/OpenSiv3D/issues/904))
		- [サンプル](https://zenn.dev/link/comments/bb445a346947d3)
	- 2 つの長方形の重なる領域を長方形で返す関数を追加しました ([#872](https://github.com/Siv3D/OpenSiv3D/issues/872))
		- [サンプル](https://zenn.dev/link/comments/5fd7bc5a3814a8)
	- `P2Body` に弾丸モードを追加しました ([#901](https://github.com/Siv3D/OpenSiv3D/issues/901))
	- 時間型が `_fmt()` に対応しました ([#894](https://github.com/Siv3D/OpenSiv3D/issues/894), [#895](https://github.com/Siv3D/OpenSiv3D/pull/895))
	- 空の長方形を作成する `Rect::Empty()`, `RectF::Empty()` を追加しました ([#881](https://github.com/Siv3D/OpenSiv3D/issues/881))
	- 長方形が空であるかを返す `Rect::isEmpty()`, `Rect::operator bool()`, `RectF::isEmpty()`, `RectF::operator bool()` を追加しました ([#879](https://github.com/Siv3D/OpenSiv3D/issues/879), [#880](https://github.com/Siv3D/OpenSiv3D/issues/880))
	- `Array::partition()`, `Array::stable_partition()` を追加しました ([#869](https://github.com/Siv3D/OpenSiv3D/issues/869))
	- `Camera2DParameters`, `LicenseManager`, `LicenseInfo`, `XInput` がスクリプト内で使えるようになりました ([#868](https://github.com/Siv3D/OpenSiv3D/issues/868))

	#### ユーザビリティ向上	
	- ヘッダの軽量化のためのリファクタリングを行いました ([#883](https://github.com/Siv3D/OpenSiv3D/issues/883), [#886](https://github.com/Siv3D/OpenSiv3D/issues/886))
	- Windows 版における、フルスクリーン時にメッセージボックスを表示すると操作不能になる問題を解決しました。シーン内にフォールバックのメッセージボックスが表示されます ([#915](https://github.com/Siv3D/OpenSiv3D/issues/915))
	- `Array` のテンプレート引数推論を改善しました ([#887](https://github.com/Siv3D/OpenSiv3D/issues/887))
	- `CITATION.cff` を追加しました ([#882](https://github.com/Siv3D/OpenSiv3D/issues/882))
	- `Grid::resize()` のオーバーロードを追加しました ([#876](https://github.com/Siv3D/OpenSiv3D/issues/876))

	#### 仕様変更
	- 各種サードパーティライブラリを更新しました ([#914](https://github.com/Siv3D/OpenSiv3D/issues/914)) 
	- `PlayingCard` のデザインを微修正しました ([#905](https://github.com/Siv3D/OpenSiv3D/issues/905))
	- `PlayingCard.hpp` は experimental から正式な機能になりました ([#885](https://github.com/Siv3D/OpenSiv3D/issues/885))

	#### パフォーマンス向上
	- `DisjointSet` のメモリ消費を削減しました ([#878](https://github.com/Siv3D/OpenSiv3D/issues/878))

	#### 不具合・バグ修正
	- Web 版の不具合修正、互換性向上を行いました
	- `XMLReader` の一部のコンストラクタが使えなかった不具合を修正しました ([#896](https://github.com/Siv3D/OpenSiv3D/issues/896)) 
	- ドキュメントを修正しました ([#871](https://github.com/Siv3D/OpenSiv3D/issues/871), [#903](https://github.com/Siv3D/OpenSiv3D/issues/903))
	- 正規表現におけるキャプチャの仕様の不具合を修正しました ([#893](https://github.com/Siv3D/OpenSiv3D/issues/893))
	- `String::removed(StringView)` に空の文字列を渡すと無限ループになるバグを修正しました ([#892](https://github.com/Siv3D/OpenSiv3D/pull/892))
	- `Allocator` の不具合を修正しました ([#889](https://github.com/Siv3D/OpenSiv3D/issues/889), [#891](https://github.com/Siv3D/OpenSiv3D/pull/891))
	- `DisjointSet::operator bool()` の戻り値の `true`, `false` が逆だったバグを修正しました ([#877](https://github.com/Siv3D/OpenSiv3D/pull/877))
	- 各種クラスの `_fmt()` 対応の不具合を修正しました ([#873](https://github.com/Siv3D/OpenSiv3D/issues/873))
	- `LineString::calculateBufferClosed()`, `LineString::calculateRoundBufferClosed()` が閉じないことがあった不具合を修正しました ([#870](https://github.com/Siv3D/OpenSiv3D/issues/870))

	#### コントリビューション
	- [nokotan](https://github.com/nokotan): **Web 版を更新**
	- [MayFlyOvO](https://github.com/MayFlyOvO): **`OrderedTable` の追加**
	- [Raclamusi](https://github.com/Raclamusi): `Array`, `Allocator`, "fmt" の改善・バグ修正
	- [AngelCase](https://github.com/AngelCase): `String::removed()` のバグ修正
	- [yunba28](https://github.com/yunba28): ドキュメントの改善
	- [sknjpn](https://github.com/sknjpn): ドキュメントの改善


??? summary "v0.6.5 | 2022-08-10"
	#### 新機能
	- Visual Studio 2022 17.3 に対応しました ([#859](https://github.com/Siv3D/OpenSiv3D/issues/859))
	- `LineString::extractLineString(double, CloseRing)` オーバーロードを追加しました ([#866](https://github.com/Siv3D/OpenSiv3D/issues/866))
		- [サンプル](https://zenn.dev/link/comments/883084a7d6a77d)
	- `JSON` がバイナリフォーマット (BSON/CBOR/MessagePack) との相互変換に対応しました ([#842](https://github.com/Siv3D/OpenSiv3D/issues/842))
		- [サンプル](https://zenn.dev/link/comments/baec402709773f)
	- ファイルパスを結合する `FileSystem::PathAppend()` を追加しました ([#825](https://github.com/Siv3D/OpenSiv3D/issues/825))
		- [サンプル](https://zenn.dev/link/comments/d45decf3cb32f2)
	- `TextEditState` に、Tab キーや Enter キーによる入力完了を取得できるメンバ変数を追加しました ([#808](https://github.com/Siv3D/OpenSiv3D/issues/808))
		- [サンプル](https://zenn.dev/link/comments/11eff1e53bffcb)
	- 底辺の中心、頂点、底辺の長さから二等辺三角形を作成する `Triangle::FromPoints()` を追加しました ([#865](https://github.com/Siv3D/OpenSiv3D/issues/865))
	- 文字列をパーセントエンコードする `PercentEncode()` を追加しました ([#864](https://github.com/Siv3D/OpenSiv3D/issues/864))
	- `NavMesh::query()` に、結果の格納先を参照で渡すオーバーロードを追加しました ([#861](https://github.com/Siv3D/OpenSiv3D/issues/861))
	- `Math::` に `Dot()` と `Cross()` を追加しました。これまでは `Vec2, Vec3` などのメンバ関数を使う必要がありました ([#848](https://github.com/Siv3D/OpenSiv3D/issues/848))
	- 長方形の各辺・中心の X 座標、Y 座標だけを返す関数を追加しました ([#853](https://github.com/Siv3D/OpenSiv3D/issues/853))
	- 長方形の左上を (0 ,0), 右下を (1, 1) としたときの (relativeX, relativeY) の座標を返すメンバ関数を追加しました ([#850](https://github.com/Siv3D/OpenSiv3D/issues/850))
	- 同梱する Font Awesome を 5.15.2 → 6.1.1 に更新しました ([#846](https://github.com/Siv3D/OpenSiv3D/issues/846))
	- `Blob` にメンバ関数を追加しました ([#843](https://github.com/Siv3D/OpenSiv3D/issues/843))
	- `Font::height(double size)` を追加しました ([#830](https://github.com/Siv3D/OpenSiv3D/issues/830))
	- 同梱するモノクロ Noto Emoji を更新しました ([#816](https://github.com/Siv3D/OpenSiv3D/issues/816))
	- 水平方向のアスペクト比を返す `.horizontalAspectRatio()` 関数を `Point`, `Float2`, `Vec2`, `Rect`, `RectF`, `Image`, `Texture`, `Emoji`, `Scene::`, `RoundRect` に追加しました ([#810](https://github.com/Siv3D/OpenSiv3D/issues/810)), ([#812](https://github.com/Siv3D/OpenSiv3D/issues/812))
	- `Multiplayer_Photon` に、タイムスタンプ関連の関数を追加しました ([#807](https://github.com/Siv3D/OpenSiv3D/issues/807))
	- `Multiplayer_Photon` に `.joinRandomRoomOrCreate()` を追加しました ([#806](https://github.com/Siv3D/OpenSiv3D/issues/806))
	- `NotImplementedError` 例外クラスを追加しました ([#787](https://github.com/Siv3D/OpenSiv3D/issues/787))

	#### ユーザビリティ向上	
	- Linux 版の CMake を改善しました ([#829](https://github.com/Siv3D/OpenSiv3D/pull/829))
	- Linux 版の CMakeLists.txt において、boost のバージョンを範囲指定するようにしました ([#847](https://github.com/Siv3D/OpenSiv3D/pull/847))
	- `SimpleGUI::TextBox()` の挙動を改善しました ([#832](https://github.com/Siv3D/OpenSiv3D/pull/832)), ([#804](https://github.com/Siv3D/OpenSiv3D/issues/804))
	- 誤用防止のため `BigInt operator ""_big(long double x)` を `= delete` 指定しました ([#826](https://github.com/Siv3D/OpenSiv3D/issues/826))
	- いくつかのヘッダファイルでドキュメントを追加しました

	#### 仕様変更
	- `BigFloat` の文字列変換を改善しました ([#839](https://github.com/Siv3D/OpenSiv3D/issues/839))
	- `Multiplayer_Photon::getLocalPlayerID()` の戻り値を `LocalPlayerID` に変更しました ([#809](https://github.com/Siv3D/OpenSiv3D/issues/809))
	- `AsyncHTTPTask::isReady` を const メンバ関数に変更しました ([#805](https://github.com/Siv3D/OpenSiv3D/pull/805))
	- 各種サードパーティライブラリを更新しました ([#801](https://github.com/Siv3D/OpenSiv3D/issues/801))
	- engine ファイルを更新しました ([#817](https://github.com/Siv3D/OpenSiv3D/issues/817))

	#### パフォーマンス向上
	- `NavMesh::query()` の実行性能を改善しました ([#861](https://github.com/Siv3D/OpenSiv3D/issues/861))
	- `HLSL` や `GLSL` クラスのコンストラクタを改善しました ([#835](https://github.com/Siv3D/OpenSiv3D/issues/835))
	- `SimpleGUI` の文字列引数を `const String&` → `StringView` に変更しました ([#827](https://github.com/Siv3D/OpenSiv3D/issues/827))
	- 算術型から `BigInt`, `BigFloat` を引くときの実行性能を改善しました ([#822](https://github.com/Siv3D/OpenSiv3D/issues/822))
	- `Rect`, `RectF` の `constexpr` 対応を改善しました ([#813](https://github.com/Siv3D/OpenSiv3D/issues/813))

	#### 不具合・バグ修正
	- `LineString::extractLineString()` が正しくない結果を返すことがあったバグを修正しました ([#862](https://github.com/Siv3D/OpenSiv3D/issues/862))
	- 始点と終点が一致する `LineString` の `.calculateRoundBuffer()` に失敗することがあることがあったバグを修正しました ([#860](https://github.com/Siv3D/OpenSiv3D/issues/860))
	- macOS, Linux 版で NULL がマクロで空文字列に置換される不具合を修正しました ([#858](https://github.com/Siv3D/OpenSiv3D/issues/858))
	- `RoundRect::drawFrmae()` で不正な値を渡したときに、描画が乱れることがあった不具合を修正しました ([#856](https://github.com/Siv3D/OpenSiv3D/issues/856))
	- `BasicCamera3D` のメンバ関数名を `.getVerticlaFOV()` → `.getVerticalFOV()` に修正しました ([#854](https://github.com/Siv3D/OpenSiv3D/pull/854))
	- `Grid::choice()` がコンパイルに失敗するバグを修正しました ([#840](https://github.com/Siv3D/OpenSiv3D/issues/840))
	- `Base64::Decode()` で、特定条件においてバッファオーバーランが発生することがあったバグを修正しました ([#837](https://github.com/Siv3D/OpenSiv3D/issues/837))
	- `Parse<double>` が `float` 型の精度で行われていたバグを修正しました ([#831](https://github.com/Siv3D/OpenSiv3D/issues/831))
	- 一部条件で Line 同士の Intersect, IntersectAt の判定が誤っていたバグを修正しました ([#823](https://github.com/Siv3D/OpenSiv3D/pull/823))
	- `BigInt`, `BigFloat` の比較演算子のバグを修正しました ([#821](https://github.com/Siv3D/OpenSiv3D/pull/821))
	- macOS 版、Linux 版の `FileSystem::SelectFolder()` が結果の末尾に `/` を付けなかったバグを修正しました ([#824](https://github.com/Siv3D/OpenSiv3D/issues/824))
	- macOS 版の `FileSystem::FullPath()` の結果が不正になることがあったバグを修正しました ([#824](https://github.com/Siv3D/OpenSiv3D/issues/824))
	- `SFMT` のヘッダ・フォルダ名の typo を修正しました ([#818](https://github.com/Siv3D/OpenSiv3D/pull/818))
	- macOS 版で `TCPClient` の切断が `TCPServer` に伝わらなかった不具合を修正しました ([#799](https://github.com/Siv3D/OpenSiv3D/issues/799))

	#### コントリビューション
	- [nokotan](https://github.com/nokotan): **Web 版を更新**
	- [MurakamiShun](https://github.com/MurakamiShun): **Linux 版の CMake を改善**
	- [m4saka](https://github.com/m4saka): **Line 同士の Intersect, IntersectAt のバグを修正**
	- [Raclamusi](https://github.com/Raclamusi): `BigInt`, `BigFloat` の改善・バグ修正、ドキュメントの改善
	- [kestrel-90r](https://github.com/kestrel-90r): ソースファイル名の typo の修正
	- [ShivAlley](https://github.com/ShivAlley): `Math::` の数学関数を追加
	- [tas9n](https://github.com/tas9n): `AsyncHTTPTask` の改善
	- [ROCKTAKEY](https://github.com/ROCKTAKEY): CMakeLists.txt の改善
	- [yknishidate](https://github.com/yknishidate): コードの改善
	- [agehama](https://github.com/agehama): ドキュメントの改善
	- [curay168](https://github.com/curay168): ドキュメントの改善


??? summary "v0.6.4 | 2022-05-21"
	#### 新機能
	- Visual Studio 2022 17.2 以降に対応しました ([#790](https://github.com/Siv3D/OpenSiv3D/issues/790))
	- Xcode 13.3 以降に対応しました ([#753](https://github.com/Siv3D/OpenSiv3D/issues/753))
	- Photon SDK と連係する `Multiplayer_Photon`（マルチプレイヤー機能）アドオンを追加しました ([#734](https://github.com/Siv3D/OpenSiv3D/issues/734))
		- [チュートリアル](https://zenn.dev/reputeless/scraps/03d951dddb53ab)
		- [サンプル](https://zenn.dev/link/comments/10e5a407500f04)
	- 3D 標準頂点シェーダの定数バッファに UV transform を追加しました ([#764](https://github.com/Siv3D/OpenSiv3D/issues/764))
		- [サンプル](https://zenn.dev/link/comments/8247739e3be0fb)
	- `MeshData::RoundedBox()` を追加しました ([#769](https://github.com/Siv3D/OpenSiv3D/issues/769))
		- [サンプル](https://zenn.dev/link/comments/654aede40de72c)
	- 再生中のオーディオに動的に波形を書き込む機能を追加しました ([#736](https://github.com/Siv3D/OpenSiv3D/issues/736))
		- [サンプル](https://zenn.dev/link/comments/8af1a8f60deaa1)
	- Windows 版のトースト通知における通知音の無効化オプションを追加しました ([#748](https://github.com/Siv3D/OpenSiv3D/issues/748))
		- [サンプル](https://zenn.dev/link/comments/c076465eed0f45)
	- `DisjointSet` (Union Find) を追加しました ([#742](https://github.com/Siv3D/OpenSiv3D/issues/742))
		- [サンプル](https://zenn.dev/link/comments/5a327d5c40b86f)
	- `Shader::LinearToScreen()` においてテクスチャフィルタを変更可能にしました ([#762](https://github.com/Siv3D/OpenSiv3D/issues/762))
		- [サンプル](https://zenn.dev/link/comments/81ffc408b80ac8)
	- `Polygon::addHole()` オーバーロードを追加しました ([#786](https://github.com/Siv3D/OpenSiv3D/issues/786))
		- [サンプル](https://zenn.dev/link/comments/3a5e307fa484c6)
	- `Font` に合字を回避するオプションを追加しました ([#792](https://github.com/Siv3D/OpenSiv3D/issues/792))
		- [サンプル](https://zenn.dev/link/comments/b4e3e69c91b3a0)
	- -1.0 ～ 1.0 の範囲を返す` Periodic::` 関数を追加しました ([#761](https://github.com/Siv3D/OpenSiv3D/issues/761))
		- [サンプル](https://zenn.dev/link/comments/e321c3bf3fc6cc)
	- `ManagedScript` に、リロードを発生させるカスタムトリガーを設定する関数を追加しました ([#768](https://github.com/Siv3D/OpenSiv3D/issues/768))
		- [サンプル](https://zenn.dev/link/comments/9726f79f84c446)
	- `Script` 内で include したファイルを取得する機能を追加しました ([#767](https://github.com/Siv3D/OpenSiv3D/issues/767))
		- [サンプル](https://zenn.dev/link/comments/052f71ba876683)
	- `JSON::push_back()` を追加しました ([#725](https://github.com/Siv3D/OpenSiv3D/issues/725))
	- `String::replace()` のオーバーロードを増やしました ([#729](https://github.com/Siv3D/OpenSiv3D/issues/729))
	- `ImageProcessing::GenerateMips()` で最大レベルを指定できるようにしました ([#763](https://github.com/Siv3D/OpenSiv3D/issues/763))
	- スクリプトで enum の値を表示可能にしました ([#774](https://github.com/Siv3D/OpenSiv3D/issues/774))
	- スクリプトに `OpenMode`, `TextEncoding`, `TextReader`, `TextWriter` を追加しました ([#775](https://github.com/Siv3D/OpenSiv3D/issues/775))
	- スクリプトに `Parse` 系の関数を追加しました ([#782](https://github.com/Siv3D/OpenSiv3D/issues/782))
	- スクリプトに `INI` を追加しました ([#783](https://github.com/Siv3D/OpenSiv3D/issues/783))
	- `Deserializer<MemoryViewReader>` を追加しました ([#777](https://github.com/Siv3D/OpenSiv3D/issues/777))
	- `Serializer<Writer>::operator ->() const` を追加しました ([#776](https://github.com/Siv3D/OpenSiv3D/issues/776))
	- `Geometry2D::Or()` のオーバーロードを追加しました ([#793](https://github.com/Siv3D/OpenSiv3D/issues/793))

	#### ユーザビリティ向上
	- (非公式) ARM 向けのビルドを改善しました ([#707](https://github.com/Siv3D/OpenSiv3D/issues/707))
	- `SceneManager` のコードを改善しました ([#750](https://github.com/Siv3D/OpenSiv3D/issues/750))
	- `NavMesh` コンストラクタでマップ構築を可能にしました ([#785](https://github.com/Siv3D/OpenSiv3D/issues/785))
		- [サンプル](https://zenn.dev/link/comments/af9b9deb7f205c)

	#### 仕様変更
	- 各種サードパーティライブラリを更新しました ([#726](https://github.com/Siv3D/OpenSiv3D/issues/726)), ([#728](https://github.com/Siv3D/OpenSiv3D/issues/728)), ([#727](https://github.com/Siv3D/OpenSiv3D/issues/727)), ([#731](https://github.com/Siv3D/OpenSiv3D/issues/731)), ([#756](https://github.com/Siv3D/OpenSiv3D/issues/756)), ([#757](https://github.com/Siv3D/OpenSiv3D/issues/757)), ([#758](https://github.com/Siv3D/OpenSiv3D/issues/758)), ([#759](https://github.com/Siv3D/OpenSiv3D/issues/759)), ([#773](https://github.com/Siv3D/OpenSiv3D/issues/773)), ([#760](https://github.com/Siv3D/OpenSiv3D/issues/760))
	- `Polygon::addHole()` の仕様を変更しました ([#786](https://github.com/Siv3D/OpenSiv3D/issues/786))
	- engine / example ファイルを更新しました ([#740](https://github.com/Siv3D/OpenSiv3D/issues/740))

	#### 不具合・バグ修正
	- `Circle::boundingRect()` のバグを修正しました ([#718](https://github.com/Siv3D/OpenSiv3D/issues/718))
	- `SimpleAnimation::isDone()` の戻り値を修正しました ([#710](https://github.com/Siv3D/OpenSiv3D/issues/710))
	- `TextEditState::TextEditState(String&& defaultText)` の use after move バグを修正しました ([#703](https://github.com/Siv3D/OpenSiv3D/issues/703))
	- `JSON` クラスで空の配列を作れなかったバグを修正しました ([#723](https://github.com/Siv3D/OpenSiv3D/issues/723))
	- `operator>>(basic_istream&, Color&)` の警告を修正しました ([#720](https://github.com/Siv3D/OpenSiv3D/issues/720))
	- リモートデスクトップ環境で `System::EnumActiveMonitors()` に失敗した不具合を修正しました ([#719](https://github.com/Siv3D/OpenSiv3D/issues/719))
	- `TOMLReader` で存在しないファイルをロードしても失敗判定にならなかったバグを修正しました ([#732](https://github.com/Siv3D/OpenSiv3D/issues/732))
	- Windows 版でメッセージボックスがウィンドウの背面に表示されることがあった不具合を修正しました ([#706](https://github.com/Siv3D/OpenSiv3D/issues/706))
	- スクリプトのバインドのバグを修正しました ([#741](https://github.com/Siv3D/OpenSiv3D/issues/741))
	- `Shape2D::Stairs()` の第 5 引数が `false` だと三角形の向きが逆になっていたバグを修正しました ([#708](https://github.com/Siv3D/OpenSiv3D/issues/708))
	- macOS 版で `RectanglePacking` が利用できなかったバグを修正しました ([#754](https://github.com/Siv3D/OpenSiv3D/issues/754))
	- ARM 向けビルドにおける `Image` と OpenCV の連携を修正しました ([#778](https://github.com/Siv3D/OpenSiv3D/issues/778))
	- `SimpleGUI::ListBox()` で範囲外アクセスが発生することがあったバグを修正しました ([#780](https://github.com/Siv3D/OpenSiv3D/issues/780))
	- `WaveSample` の変換のバグを修正しました ([#795](https://github.com/Siv3D/OpenSiv3D/issues/795))

	#### コントリビューション
	- [nokotan](https://github.com/nokotan): **Web 版を更新**
	- [tana](https://github.com/tana): **ARM 向けビルドの改善**
	- [mak1a](https://github.com/mak1a): **Multiplayer_Photon の実装**, `SimpleAnimation::isDone()` の修正
	- [Ryoga-exe](https://github.com/Ryoga-exe): コードの改善
	- [k-sunako](https://github.com/k-sunako): CMakeLists.txt の改善
	- [falrnd](https://github.com/falrnd): `Circle::boundingRect()` の修正
	- [polyester-CTRL](https://github.com/polyester-CTRL): OpenCV_Bridge の改善
	- [yaito3014](https://github.com/yaito3014): コードの改善
	- [NachiaVivias](https://github.com/NachiaVivias): `WaveSample` の改善

	#### OpenSiv3D チャレンジ
	- \#12 Photon: mak1a, Luke, sthairno


??? summary "v0.6.3 | 2021-11-14"
	#### 新機能
	- Visual Studio 2022 に対応しました ([#683](https://github.com/Siv3D/OpenSiv3D/issues/683))
	- SimpleGUI にリストボックスを追加しました ([#659](https://github.com/Siv3D/OpenSiv3D/issues/659))
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

		ListBoxState ls1{
			{
				U"北海道", U"青森県", U"岩手県", U"宮城県", U"秋田県", U"山形県", U"福島県", U"茨城県",
				U"栃木県", U"群馬県", U"埼玉県", U"千葉県", U"東京都", U"神奈川県", U"新潟県", U"富山県",
				U"石川県", U"福井県", U"山梨県", U"長野県", U"岐阜県", U"静岡県", U"愛知県", U"三重県",
				U"滋賀県", U"京都府", U"大阪府", U"兵庫県", U"奈良県", U"和歌山県", U"鳥取県", U"島根県",
				U"岡山県", U"広島県", U"山口県", U"徳島県", U"香川県", U"愛媛県", U"高知県", U"福岡県",
				U"佐賀県", U"長崎県", U"熊本県", U"大分県", U"宮崎県", U"鹿児島県", U"沖縄県",
			}
		};

		ListBoxState ls2{
			{
				U"吾輩は猫である（1905年1月 - 1906年8月、『ホトトギス』/1905年10月 - 1907年5月、大倉書店・服部書店）",
				U"坊っちゃん（1906年4月、『ホトトギス』/1907年、春陽堂刊『鶉籠』収録）",
				U"草枕（1906年9月、『新小説』/『鶉籠』収録）",
				U"二百十日（1906年10月、『中央公論』/『鶉籠』収録）",
				U"野分（1907年1月、『ホトトギス』/1908年、春陽堂刊『草合』収録）",
				U"虞美人草（1907年6月 - 10月、『朝日新聞』/1908年1月、春陽堂）",
				U"坑夫（1908年1月 - 4月、『朝日新聞』/『草合』収録）",
				U"三四郎（1908年9 - 12月、『朝日新聞』/1909年5月、春陽堂）",
				U"それから（1909年6 - 10月、『朝日新聞』/1910年1月、春陽堂）",
				U"門（1910年3月 - 6月、『朝日新聞』/1911年1月、春陽堂）",
				U"彼岸過迄（1912年1月 - 4月、『朝日新聞』/1912年9月、春陽堂）",
				U"行人（1912年12月 - 1913年11月、『朝日新聞』/1914年1月、大倉書店）",
				U"こゝろ（1914年4月 - 8月、『朝日新聞』/1914年9月、岩波書店）",
				U"道草（1915年6月 - 9月、『朝日新聞』/1915年10月、岩波書店）",
				U"明暗（1916年5月 - 12月、『朝日新聞』/1917年1月、岩波書店）",
			}
		};

		ls2.selectedItemIndex = 3;

		ListBoxState ls3 = ls2;

		while (System::Update())
		{
			if (SimpleGUI::ListBox(ls1, Vec2{ 20, 20 }, 120, 156) && ls1.selectedItemIndex)
			{

			}

			if (SimpleGUI::ListBox(ls2, Vec2{ 180, 20 }, 240, 156, false) && ls2.selectedItemIndex)
			{

			}

			if (SimpleGUI::ListBox(ls3, Vec2{ 20, 200 }, 1020, 480) && ls3.selectedItemIndex)
			{

			}
		}
	}
	```
	- 同梱する Color Emoji を更新し、Unicode 14.0 の絵文字を扱えるようにしました ([#694](https://github.com/Siv3D/OpenSiv3D/issues/694))
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });

		const Texture e0{ U"🫠"_emoji };
		const Texture e1{ U"🫣"_emoji };
		const Texture e2{ U"🫡"_emoji };
		const Texture e3{ U"🫥"_emoji };
		const Texture e4{ U"🫵"_emoji };
		const Texture e5{ U"🧌"_emoji };
		const Texture e6{ U"🪸"_emoji };
		const Texture e7{ U"🪺"_emoji };
		const Texture e8{ U"🫘"_emoji };
		const Texture e9{ U"🫙"_emoji };
		const Texture e10{ U"🫧"_emoji };
		const Texture e11{ U"🛞"_emoji };

		while (System::Update())
		{
			e0.drawAt(100, 100);
			e1.drawAt(300, 100);
			e2.drawAt(500, 100);
			e3.drawAt(700, 100);
			e4.drawAt(100, 300);
			e5.drawAt(300, 300);
			e6.drawAt(500, 300);
			e7.drawAt(700, 300);
			e8.drawAt(100, 500);
			e9.drawAt(300, 500);
			e10.drawAt(500, 500);
			e11.drawAt(700, 500);
		}
	}
	```
	- GUI フォントに、デフォルトでアイコンフォントへのフォールバックを追加しました。SimpleGUI のテキストで `U"\U000F0493 Setting"` のようにアイコンコードを使ってアイコンを表示できます ([#698](https://github.com/Siv3D/OpenSiv3D/issues/698))
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });
		double volume = 1.0;

		while (System::Update())
		{
			SimpleGUI::Button(U"\U000F1677 ゆっくり", Vec2{ 20, 20 }, 160);
			SimpleGUI::Button(U"\U000F0907 いそいで", Vec2{ 20, 60 }, 160);
			SimpleGUI::Button(U"\U000F0493 設定", Vec2{ 20, 100 }, 160);
			SimpleGUI::Slider(0.5 < volume ? U"\U000F057E"
				: 0.0 < volume ? U"\U000F0580" : U"\U000F0581", volume, Vec2{ 20, 140 }, 30, 130);
		}
	}
	```
	- Windows 版の `System::EnumerateMonitors()` において、より区別しやすいモニターの名前を取得するようにしました ([#695](https://github.com/Siv3D/OpenSiv3D/issues/695))
	- 文字を 3D の Mesh で表現するための `MeshGlyph` クラスを追加しました ([#680](https://github.com/Siv3D/OpenSiv3D/issues/680))
	```cpp
	# include <Siv3D.hpp>

	class Font3D
	{
	public:

		Font3D() = default;

		SIV3D_NODISCARD_CXX20
		explicit Font3D(const Font& font)
			: m_font{ font } {}

		[[nodiscard]]
		Array<MeshGlyph> getGlyphs(StringView s) const
		{
			Array<MeshGlyph> results;

			for (auto ch : s)
			{
				auto it = m_table.find(ch);

				if (it == m_table.end())
				{
					it = m_table.emplace(ch, m_font.createMesh(ch)).first;
				}

				results << it->second;
			}

			return results;
		}

		void drawCylindricalInner(StringView s, const Vec3& center, double r, double scale, double startAngle, const ColorF& color) const
		{
			const double perimeter = (r * Math::TwoPi);
			double penPosX = 0.0;
			startAngle += 90_deg;

			for (auto meshGlyph : getGlyphs(s))
			{
				penPosX += (meshGlyph.xOffset * scale);

				if (meshGlyph.mesh)
				{
					const double angle = (penPosX / perimeter) * 360_deg;
					const Quaternion q = Quaternion::RotateY(-90_deg + angle - startAngle);
					const Vec3 pos = Cylindrical{ r, (-180_deg - angle + startAngle), 0.0 } + center;
					const Mat4x4 mat = Mat4x4::Translate(-meshGlyph.xOffset, 0, 0)
						.scaled(scale)
						.rotated(q)
						.translated(pos);
					meshGlyph.mesh.draw(mat, color);
				}

				penPosX += (meshGlyph.xAdvance - meshGlyph.xOffset) * scale;
			}
		}

		void drawCylindricalOuter(StringView s, const Vec3& center, double r, double scale, double startAngle, const ColorF& color) const
		{
			const double perimeter = (r * Math::TwoPi);
			double penPosX = 0.0;
			startAngle += 90_deg;

			for (auto meshGlyph : getGlyphs(s))
			{
				penPosX += (meshGlyph.xOffset * scale);

				if (meshGlyph.mesh)
				{
					const double angle = (penPosX / perimeter) * 360_deg;
					const Quaternion q = Quaternion::RotateY(90_deg - angle - startAngle);
					const Vec3 pos = Cylindrical{ r, (180_deg + angle + startAngle), 0.0 } + center;
					const Mat4x4 mat = Mat4x4::Translate(-meshGlyph.xOffset, 0, 0)
						.scaled(scale)
						.rotated(q)
						.translated(pos);
					meshGlyph.mesh.draw(mat, color);
				}

				penPosX += (meshGlyph.xAdvance - meshGlyph.xOffset) * scale;
			}
		}

	private:

		Font m_font;

		mutable HashTable<char32, MeshGlyph> m_table;
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const ColorF backgroundColor = ColorF{ 0.5, 0.6, 0.6 }.removeSRGBCurve();
		const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
		const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
		DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 10, 16, -32 } };
		const Font3D font3D{ Font{ 60 } };

		while (System::Update())
		{
			const double t = Scene::Time();
			camera.update(2.0);
			Graphics3D::SetCameraTransform(camera);

			// 3D 描画
			{
				Graphics3D::SetGlobalAmbientColor(Graphics3D::DefaultGlobalAmbientColor);
				Graphics3D::SetSunColor(ColorF{ 0.75 });

				const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };
				Plane{ 64 }.draw(uvChecker);
				Cylinder{ Vec3{0,0,0}, Vec3{0, 16, 0}, 5.6 }.draw(ColorF{ 0.25 }.removeSRGBCurve());

				// 3D Text Circle
				{
					// 両面描画、ライティング無効化
					const ScopedRenderStates3D rasterizer{ RasterizerState::SolidCullNone, BlendState::Additive };
					Graphics3D::SetGlobalAmbientColor(ColorF{ 1.0 });
					Graphics3D::SetSunColor(ColorF{ 0.0 });

					font3D.drawCylindricalOuter(DateTime::Now().format(U"HH:mm:ss"), Vec3{ 0, 11.5, 0 }, 6 * 1.2, 3.0 * 1.2, (t * -25_deg), ColorF{ 1.0, 0.98, 0.9 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(DateTime::Now().format(U"HH:mm:ss"), Vec3{ 0, 11.5, 0 }, 6 * 1.2, 3.0 * 1.2, (t * -25_deg) + 180_deg, ColorF{ 1.0, 0.98, 0.9 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(U"Monday, September 27, 2021", Vec3{ 0, 10, 0 }, 6 * 1.2, 1.2 * 1.2, (t * -50_deg), ColorF{ 1.0, 0.98, 0.9 }.removeSRGBCurve());

					font3D.drawCylindricalOuter(U"NIKKEI 225 30,248.81 +609.41", Vec3{ 0, 8, 0 }, 6, 1.0, (t * -72_deg), ColorF{ 0.6, 1.0, 0.8 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(U"HANG SENG 24,192,16 -318.82", Vec3{ 0, 7, 0 }, 6, 1.0, (t * -62_deg), ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(U"SHANGHAI 3,613.07 -29.15", Vec3{ 0, 6, 0 }, 6, 1.0, (t * -58_deg), ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(U"FTSE 7,051.48 -26.87", Vec3{ 0, 5, 0 }, 6, 1.0, (t * -70_deg), ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(U"CAC 6,638.46 -63.52", Vec3{ 0, 4, 0 }, 6, 1.0, (t * -60_deg), ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(U"DAX 15,531.75 -112.22", Vec3{ 0, 3, 0 }, 6, 1.0, (t * -66_deg), ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(U"NASDAQ 15,047.70 -4.54", Vec3{ 0, 2, 0 }, 6, 1.0, (t * -68_deg), ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());

					font3D.drawCylindricalOuter(U"NIKKEI 225 30,248.81 +609.41", Vec3{ 0, 8, 0 }, 6, 1.0, (t * -72_deg) + 180_deg, ColorF{ 0.6, 1.0, 0.8 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(U"HANG SENG 24,192,16 -318.82", Vec3{ 0, 7, 0 }, 6, 1.0, (t * -62_deg) + 180_deg, ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(U"SHANGHAI 3,613.07 -29.15", Vec3{ 0, 6, 0 }, 6, 1.0, (t * -58_deg) + 180_deg, ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(U"FTSE 7,051.48 -26.87", Vec3{ 0, 5, 0 }, 6, 1.0, (t * -70_deg) + 180_deg, ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(U"CAC 6,638.46 -63.52", Vec3{ 0, 4, 0 }, 6, 1.0, (t * -60_deg) + 180_deg, ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(U"DAX 15,531.75 -112.22", Vec3{ 0, 3, 0 }, 6, 1.0, (t * -66_deg) + 180_deg, ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
					font3D.drawCylindricalOuter(U"NASDAQ 15,047.70 -4.54", Vec3{ 0, 2, 0 }, 6, 1.0, (t * -68_deg) + 180_deg, ColorF{ 1.0, 0.6, 0.6 }.removeSRGBCurve());
				}
			}

			// 3D シーンを 2D シーンに描画
			{
				Graphics3D::Flush();
				renderTexture.resolve();
				Shader::LinearToScreen(renderTexture);
			}
		}
	}
	```
	- Windows 版において、Leap Motion デバイスをサポートしました ([#677](https://github.com/Siv3D/OpenSiv3D/issues/677))
	```cpp
	// Ultraleap SDK をインストールし、プロジェクトのプロパティの
	// 1. インクルード ディレクトリに
	// C:\Program Files\Ultraleap\LeapSDK\include を追加。
	// 2. ライブラリ ディレクトリに
	// C:\Program Files\Ultraleap\LeapSDK\lib\x64 を追加。
	// 3. App フォルダに LeapC.dll をコピー。

	# include <Siv3D.hpp>

	inline constexpr double HandScale = 0.08;

	void DrawSphere(uint32 handID, const Vec3& pos)
	{
		Sphere{ (pos * HandScale), (6 * HandScale) }
		.draw(HSV{ handID * 60 }.removeSRGBCurve());
	}

	void DrawCylinder(const Vec3& from, const Vec3& to)
	{
		Cylinder{ (from * HandScale), (to * HandScale), (3 * HandScale) }.draw();
	}

	void Main()
	{
		Window::Resize(1280, 720);
		const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();
		const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };
		const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };
		DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 0, 32, -32 } };

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
		size_t trackingModeIndex = 0;
		bool showInfo = true;
		Leap::Connection leap{ Leap::TrackingMode::Desktop };

		if (not leap)
		{
			throw Error{ U"Leap device not found" };
		}

		while (System::Update())
		{
			leap.update();

			camera.update(2.0);
			Graphics3D::SetCameraTransform(camera);

			// 3D 描画
			{
				const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

				if (trackingModeIndex == 0)
				{
					Plane{ 64 }.draw(uvChecker);

					const double z = 6;

					for (auto x : Range(-2, 2))
					{
						Cylinder{ (x * 6.0), 4, z, 2, 8 }.draw(ColorF{ 0.8 }.removeSRGBCurve());
					}

					for (auto x : Range(-8, 8))
					{
						const Box box{ (x * 2), 10, z, 1.8, 1, 10 };
						bool intersect = false;

						for (const auto& hand : leap.getHands())
						{
							for (auto fingerIndex : step(5))
							{
								for (auto boneIndex : Range(1, 3))
								{
									const Vec3 to = hand.fingerBone(fingerIndex, boneIndex).to;
									const Sphere sphere{ (to * HandScale), (6 * HandScale) };

									if (sphere.intersects(box))
									{
										intersect = true;
										break;
									}
								}

								if (intersect)
								{
									break;
								}
							}

							if (intersect)
							{
								break;
							}
						}

						box.draw(HSV{ (x * 40), (intersect ? 0.8 : 0.15), 1.0 }.removeSRGBCurve());
					}
				}

				for (const auto& hand : leap.getHands())
				{
					const auto handID = hand.id();

					for (auto fingerIndex : step(5))
					{
						for (auto boneIndex : step(4))
						{
							const Vec3 from = hand.fingerBone(fingerIndex, boneIndex).from;
							const Vec3 to = hand.fingerBone(fingerIndex, boneIndex).to;

							if (fingerIndex == 4 && boneIndex == 0)
							{
								DrawSphere(handID, from);
							}

							DrawSphere(handID, to);

							if ((fingerIndex != 0 && fingerIndex != 4) && boneIndex == 0)
							{
								continue;
							}

							DrawCylinder(from, to);
						}
					}

					DrawSphere(handID, hand.palmPosition());
					DrawCylinder(hand.fingerBone(0, 0).from, hand.fingerBone(1, 1).from);
					DrawCylinder(hand.fingerBone(1, 1).from, hand.fingerBone(2, 1).from);
					DrawCylinder(hand.fingerBone(2, 1).from, hand.fingerBone(3, 1).from);
					DrawCylinder(hand.fingerBone(3, 1).from, hand.fingerBone(4, 1).from);
					DrawCylinder(hand.fingerBone(0, 0).from, hand.fingerBone(4, 0).from);
				}
			}

			{
				Graphics3D::Flush();
				renderTexture.resolve();
				Shader::LinearToScreen(renderTexture);
			}

			if (SimpleGUI::RadioButtons(trackingModeIndex, { U"Desktop", U"Head-mounted", U"Screentop" }, Vec2{ 20,20 }))
			{
				leap.setTrackingMode(static_cast<Leap::TrackingMode>(trackingModeIndex));

				if (trackingModeIndex == 0)
				{
					camera = DebugCamera3D{ renderTexture.size(), 30_deg, Vec3{ 0, 32, -32 } };
				}
				else if (trackingModeIndex == 1)
				{
					camera = DebugCamera3D{ renderTexture.size(), 30_deg, Vec3{ 0, 32, -24 }, Vec3{ 0, 0, 8 } };
				}
				else
				{
					camera = DebugCamera3D{ renderTexture.size(), 30_deg, Vec3{ 0, 32, -56 }, Vec3{ 0, 0, -24 } };
				}
			}

			SimpleGUI::CheckBox(showInfo, U"showInfo", Vec2{ 20, 140 });

			if (showInfo)
			{
				for (const auto& hand : leap.getHands())
				{
					const Vec2 palmPos = camera.worldToScreenPoint(hand.palmPosition() * HandScale).xy();

					String ext;
					for (auto fingerIndex : step(5))
					{
						ext << (hand.isExtended(fingerIndex) ? U'1' : U'0');
					}

					const String state = U"pinchDistance: {:.2f}\ngrabAngle: {:.2f}\npinchStrength: {:.2f}\ngrabStrength: {:.2f}\nfingers:{}"_fmt(
						hand.pinchDistance(), hand.grabAngle(), hand.pinchStrength(), hand.grabStrength(), ext);

					font(hand.isLeftHand() ? U"L" : U"R")
						.draw(TextStyle::Outline(0.15, ColorF{ 0.0 }), 100, Arg::rightCenter = palmPos.movedBy(-20, 0));

					font(state)
						.draw(30, Arg::leftCenter = palmPos, ColorF{ 0.15 });
				}
			}
		}
	}
	```
	- `Math::Tau` や `0.5_tau` など、2π を表す定数 τ に対応しました ([#673](https://github.com/Siv3D/OpenSiv3D/issues/673))
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Print << Math::Pi;
		Print << Math::Tau;

		Print << Math::PiF;
		Print << Math::TauF;

		Print << 0.5_pi;
		Print << 0.5_tau;

		while (System::Update())
		{

		}
	}
	```
	- 異なる種類どうしの `Optional` の比較演算ができるようになりました ([#670](https://github.com/Siv3D/OpenSiv3D/issues/670))
	- `BigFloat` に `.isNan()`, `.isInf()` メンバ関数を追加しました ([#669](https://github.com/Siv3D/OpenSiv3D/issues/669))
	- `Array`, `Optional`, `BigInt`, `BigFloat` に三方比較演算子を実装しました ([#658](https://github.com/Siv3D/OpenSiv3D/issues/658))
	- `String`, `StringView`, `UUIDValue` に三方比較演算子を実装しました ([#664](https://github.com/Siv3D/OpenSiv3D/issues/664))
	- `DrawableText::regionBase()` のオーバーロードを追加しました ([#666](https://github.com/Siv3D/OpenSiv3D/issues/666))
	- Windows 版において、リフレッシュレート以上の頻度でキーボード入力を取得できる関数 `Platform::Windows::Keyboard::GetEvents()` を追加しました ([#662](https://github.com/Siv3D/OpenSiv3D/issues/662))
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		uint64 eventIndex = 0;

		while (System::Update())
		{
			if (SimpleGUI::Button(U"Clear", Vec2{ 680, 20 }))
			{
				ClearPrint();
			}

			for (const auto& event : Platform::Windows::Keyboard::GetEvents())
			{
				if (eventIndex < event.eventIndex)
				{
					Print << event.timeMillisec << U": " << Input { InputDeviceType::Keyboard, event.code }.name() << (event.down ? U" down (event)" : U" up (event)");

					eventIndex = event.eventIndex;
				}
			}

			/*
			for (const auto& key : Keyboard::GetAllInputs())
			{
				if (key.down())
				{
					Print << Time::GetMillisec() << U": " << key.name() << U" down";
				}
				else if (key.up())
				{
					Print << Time::GetMillisec() << U": " << key.name() << U" up";
				}
			}
			*/
		}
	}
	```

	#### パフォーマンス向上
	- スクリプトエンジンの初期化を遅延させ、スクリプト機能を使わない場合のアプリケーション初期化にかかる時間を短縮しました（数十ミリ秒） ([#657](https://github.com/Siv3D/OpenSiv3D/issues/657))
	- GLSL シェーダファイルのライセンス記述を簡素化し、ファイルサイズを少し削減しました ([#687](https://github.com/Siv3D/OpenSiv3D/issues/687))
	- `HalfFloat` のメンバ関数を constexpr にしました ([#689](https://github.com/Siv3D/OpenSiv3D/pull/689))

	#### ユーザビリティ向上
	- `NotoEmoji-Regular.ttf` をエンジンリソースに含まなくてもエンジンを初期化できるようにしました ([#684](https://github.com/Siv3D/OpenSiv3D/issues/684))
	- `NotoSansCJK-Regular.ttc.zstdcmp` や `NotoSansJP-Regular.otf.zstdcmp` の代替にできる、最低限必要なグリフを格納したフォント `engine/font/min/siv3d-min.woff` を追加しました ([#682](https://github.com/Siv3D/OpenSiv3D/issues/682))
	- Windows 版インストーラの対応言語を増やしました ([#671](https://github.com/Siv3D/OpenSiv3D/issues/671))

	#### 仕様変更
	- Web 版で通常と同じメインループが書けるようになったため、`SIV3D_MAINLOOP_BEGIN` を削除しました ([#674](https://github.com/Siv3D/OpenSiv3D/issues/674))
	- macOS 版と Linux 版において、ログは `std::cout` ではなく `std::clog` および `std::cerr` に出力するようにしました ([#630](https://github.com/Siv3D/OpenSiv3D/issues/630))
	- `engine` および `example` フォルダの更新 ([#686](https://github.com/Siv3D/OpenSiv3D/issues/686))

	#### 不具合・バグ修正
	- `DrawableText::draw(double, Arg:: ...)` や `DrawableText::region(double, Arg ...)` の位置が正しくなかったバグを修正しました ([#665](https://github.com/Siv3D/OpenSiv3D/issues/665))
	- Windows 版において`Window::IsToggleFullscreenEnabled()` が常に `false` を返すバグを修正しました ([#699](https://github.com/Siv3D/OpenSiv3D/issues/699))
	- `HalfFloat{ 0.0f } == HalfFloat{ -0.0f }` が `false` になるバグを修正しました ([#660](https://github.com/Siv3D/OpenSiv3D/issues/660))
	- `CircularBase<float, Oclock>` 使用時に発生する警告を解消しました ([#667](https://github.com/Siv3D/OpenSiv3D/issues/667))

	#### コントリビューション
	- [nokotan](https://github.com/nokotan): **Web 版を更新**
	- [tetsurom](https://github.com/tetsurom): `HalfFloat` の実装改善, `Optional` の実装改善, `BigFloat` の実装改善, 各種クラスへの三方比較演算子の実装


??? summary "v0.6.2 | 2021-09-29"
	#### パフォーマンス向上
	- Windows 版でアプリケーションの起動を高速化しました ([#650](https://github.com/Siv3D/OpenSiv3D/issues/650), [#651](https://github.com/Siv3D/OpenSiv3D/issues/651))
	- メモリ / VRAM の消費量を削減しました ([#648](https://github.com/Siv3D/OpenSiv3D/issues/648))

	#### 不具合・バグ修正
	- Windows 版で重い描画処理を行ったときに v0.4.3 よりもフレームレートが低下することがあった問題を修正しました ([#652](https://github.com/Siv3D/OpenSiv3D/issues/652))
	- Windows 版、プロジェクトテンプレートの stdafx.h を Header Files フィルタに移動しました ([#653](https://github.com/Siv3D/OpenSiv3D/issues/653))


??? summary "v0.6.1 | 2021-09-21"
	#### 新機能
	- SDF / MSDF テクスチャ描画時のパラメータを簡単に指定できる `Graphics2D::SetSDFParameters(const TextStyle&)`, `Graphics2D::SetMSDFParameters(const TextStyle&)` を追加しました ([#647](https://github.com/Siv3D/OpenSiv3D/issues/647))

	#### ユーザビリティ向上
	- Windows 版のプロジェクトで発生していたビルド時の IntelliSense の警告を 2 件抑制しました ([#643](https://github.com/Siv3D/OpenSiv3D/issues/643))
	- ドキュメンテーションを追加しました

	#### 仕様変更
	- `Monitor` は `MonitorInfo` に名前が変更されました ([#649](https://github.com/Siv3D/OpenSiv3D/issues/649))
	- `UUID` は `UUIDValue` に名前が変更されました

	#### 不具合・バグ修正
	- v0.6.0 において、Windows 版でタッチ操作をしたときに左クリックと判定されにくくなっていた不具合を修正しました ([#645](https://github.com/Siv3D/OpenSiv3D/issues/645))
	- v0.6.0 において、`<Siv3D/Windows/Windows.hpp>` をインクルードするとコンパイルエラーになっていた問題を修正しました ([#644](https://github.com/Siv3D/OpenSiv3D/issues/644))
	- v0.6.0 において、`Platform::Windows::GetHWND()` が実装されていなかった問題を修正しました ([#646](https://github.com/Siv3D/OpenSiv3D/issues/646))
	- `MathParser` に空の文字列を渡したときに例外を投げないようにしました ([#641](https://github.com/Siv3D/OpenSiv3D/issues/641))


??? summary "v0.6.0 | 2021-09-18"
	#### 新機能
	- 基本的な 3D 描画に対応しました
	- C++20 に対応し、Concepts や Designated initialization, コンストラクタへの `[[nodiscard]]`, 宇宙船演算子、より多くの `constexpr`, 新しい標準機能ライブラリ機能などが活用されています
	- 試験的な Web 版の実装を追加しました (詳しくは [OpenSiv3D for Web](https://siv3d.kamenokosoft.com/ja/index))
	- Windows で OpenGL バックエンドが利用可能になりました (詳しくは チュートリアル 35)
	- ファイルの非同期ダウンロードなどを行う HTTP クライアント機能を追加しました
	- デフォルトで HighDPI に対応しました
	- SVG ファイルの読み込みに対応しました
	- MIDI ファイルの読み込みに対応しました
	- 動画をテクスチャとして扱える `VideoTexture` クラスを追加しました
	- Windows でペンタブレットの入力（筆圧・傾き）を取得する機能を追加しました
	- 2D 描画でカスタム頂点シェーダを利用できるようになりました。3D でも頂点シェーダ、ピクセルシェーダをカスタマイズできます
	- すべてのプラットフォームでオーディオのフェードイン・フェードアウト（再生、停止、音量、パン、スピード）をサポートしました
	- HPF, LPF, ピッチシフトなどのリアルタイム音声フィルタ機能を追加しました
	- 文字の輪郭や Polygon を正確に取得できるようになりました
	- `Font` のレンダリング形式に SDF / MSDF を指定できるようになりました
	- `Font` の拡大縮小描画、輪郭、シャドウに対応しました
	- オーディオファイルのストリーミング再生に対応しました
	- `String` 型に対応した、正規表現を扱う機能を追加しました
	- 実行ファイルに埋める文字列の難読化をする機能を追加しました
	- デマングルを行う関数を追加しました
	- Kahan の加算アルゴリズムを行うクラスを追加しました
	- 128-bit 整数型を追加しました
	- `Stopwatch` や `Timer` がユーザ定義の基準時刻インタフェース `ISteadyClock` を利用することで、複数の `Stopwatch` や `Timer` を一括して一時停止させたり、遅く/早く進行させることが容易になりました
	- `TimeProfiler` がより詳細な統計情報を提供するようになりました
	- 地理空間データの交換形式である GeoJSON を読み込む機能を追加しました
	- 多くの数学定数を追加しました
	- `JSONReader`, `JSONWriter` を統合した `JSON` クラスを追加しました
	- 簡易的なキーフレームによるアニメーションを行う `SimpleAnimation` クラスを追加しました
	- 統計処理を行う関数を追加しました
	- 数値に応じたカラーマップを行う機能を追加しました
	- ベクトルクラスに多数の便利なメンバ関数を追加しました
	- 図形クラスに多数の便利なメンバ関数を追加しました
	- `Shape2D` にハート形、両方向矢印、Squircle 形を追加しました 
	- `Polygon` に柔軟にテクスチャをマッピングする機能を追加しました
	- 長方形詰込みに 90° 回転を許可するオプションを追加しました
	- ホモグラフィ変換を行うための機能を追加しました
	- 各種乱数関数が乱数エンジンを引数に取れるようになりました
	- UUID 生成機能を追加しました
	- 環境変数の取得機能を追加しました
	- コマンドライン引数の取得機能を追加しました
	- モニターの物理サイズなど、詳細な情報を取得できるようになりました
	- シリアル通信の詳細なオプションを追加しました
	- Klat 方式による音声読み上げ機能を追加しました
	- 画像形式 WebP, TIFF の読み込みに対応しました
	- 音声形式 Opus, AIFF, FLAC, MIDI, WMA の読み込みに対応しました
	- 画像の一部分に画像処理を適用できるようになりました
	- GrabCut 機能を追加しました
	- QR コード生成機能を改善しました
	- `VideoWriter` を改善しました
	- サウンドフォントファイルを利用できるようになりました
	- マウスカーソルのスタイルを追加しました
	- 全てのキー入力を取得する関数を追加しました
	- アセット管理における非同期ロードがより便利になりました
	- example ファイルを多数追加しました
	- ナビメッシュがより簡単に使えるようになりました
	- `Spline2D` クラスを追加しました
	- 図形の輪郭の一部の取得に対応しました
	- 図形の Lerp に対応しました
	- GPU だけでの三角形描画に対応しました
	- カスタムマウスカーソルに対応しました
	- オーディオをグループ化し、グループごとに音量を調整できる機能を追加しました
	- Ogg Vorbis のループタグ取得に対応しました
	- レーベンシュタイン距離機能を追加しました
	- 凹包 (Concave hull) 機能を追加しました
	- 柔軟な画像デコーダ、エンコーダクラスを追加されました
	- 閉 / 開区間を指定した乱数生成を追加しました
	- SIV3D_SET() によるビルド時のエンジン設定機能を追加しました
	- Effect の再帰が可能になりました
	- CJK フォントを追加し、`Print` が中国語、韓国語の表示に対応しました
	- 動画ファイルを読み込む `VideoReader` クラスを追加しました
	- 2D 物理演算の機能を追加しました
	- Siv3D くんドット絵素材が追加されました
	- Siv3D くん .obj 3D モデルファイルが追加されました
	- `Image::stamp()` が追加されました
	- `Line::drawDoubleHeadedArrow()` で両方向矢印を描けるようになりました
	- スクリーンショット保存のショートカットキーをカスタマイズできるようになりました
	- スクリプト機能を大幅に改善しました
	- 2D 図形の交差判定をより多くの組み合わせに対応しました
	- 多くの 3D 形状のクラスを追加しました
	- Linux 版の IMEの挙動を改善しました
	- ユーザアドオンの追加機能を追加しました
	- その他多数の新機能が追加されています

	#### パフォーマンス向上
	- Windows 版のアプリの起動時間が数百ミリ秒前後短縮しました
	- `Heterogeneous lookup` により、文字列リテラルや `StringView` で `HashTable` や `HashSet` のルックアップをする際の実行時性能が向上しました
	- ファイルの読み書き、画像ファイルや音声ファイルのロードが高速になりました
	- `Parse` / `ParseOpt` / `ParseOr` の速度を改善しました

	#### ユーザビリティ向上
	- インライン関数が .hpp ファイルから .ipp ファイルに移され、ヘッダファイルが読みやすくなりました
	- Windows 版のプロジェクトがデフォルトでプリコンパイル済みファイルを使用するようになり、ビルドが高速化しました

	#### 仕様変更
	- bool 型の関数パラメータの多くが、名前の付いた YesNo 型に置き換えられ、コードの可読性が向上しました
	- `Optional` 型が C++ 標準の `std::optional` に近い動作をするよう改善されました
	- `HashTable` 型が C++ 標準の `std::unordered_map` に近い動作をするよう改善されました
	- `KDTree` がより短い記述で利用可能になりました
	- `Concurrenttask` は `AsyncTask` に名前が変更されました
	- 子プロセス作成関数は `ChildProcess` クラスに統合されました
	- `Unicode::FromWString()` は `Unicode::FromWstring()` に名前が変更されました
	- 浮動小数点数型の `U"{}"_fmt(x)` は、有効桁数すべてを表示するようになりました
	- `Time::Get～` はシステム起動時間ではなく、アプリケーション起動からの時間を返すようになりました
	- `CustomStopwatch` は `VariableSpeedStopwatch` に名前が変更されました
	- `FileSystem::SpecialFolderPath()` は `FileSystem::GetFolderPath()` に名前が変更されました
	- `FileSystem::UniqueFilePath()` は UUID 機能を使って名前を作るようになりました
	- `ByteArray` は `Blob` および `MemoryReader` に置き換えられました
	- `CSVData` は `CSV` に名前が変更されました
	- `INIData` は `INI` に名前が変更されました
	- `JSONReader`, `JSONWriter` は `JSON` に統合されました
	- `EasingController` は `EasingAB` に名前が変更されました
	- `Sprite` は `Buffer2D` に置き換えられました
	- インデックス配列は `TriangleIndex` を使うようになりました
	- `MessageBox` の仕様が変わりました
	- トースト通知の仕様が変わりました
	- 物体検出機能は `CascadeClassifier` に置き換えられました
	- `Key` は `Input` になりました
	- 絵文字とアイコンデータセットを更新し使える絵文字やアイコンの種類が大幅に増えました
	- `Image` の最大サイズを 1 辺 8192px → 16384px に拡張しました
	- ConstantBuffer サイズ 16 × N バイト制約が撤廃されました
	- 並列実行に関する機能は `SIV3D_CONCURRENT` マクロを定義しなくても使えるようになりました
	- High DPI ウィンドウがデフォルトになり、`SIV3D_WINDOWS_HIGH_DPI` マクロは廃止されました

	#### 不具合・バグ修正
	- `Array` の並列関数の不具合を修正しました
	- `AsyncTask` のプラットフォーム間による差異を解消しました
	- Windows の `MakeDragDrop()` の不具合を修正しました
	- PPM 画像読み込みの不具合を修正しました
	- プラットフォームごとの乱数の再現性の改善しました
	- QR コード生成の不具合を修正しました

	#### 注意点
	- `Math::SmoothDmap()` の引数順が変更されています。コンパイルエラーで発見できません
	- フォントの縦書き機能は一時的に非搭載になりました
	- 自然言語処理機能は一時的に非搭載になりました
	- `SimpleGUIManager` 機能はキャンセルされました
	- `NoiseGenerator ` クラスは一時的に非搭載になりました
	- Shift_JIS 形式のテキストファイルはサポートしなくなりました
	- シーンのリサイズについて、仕組みが変更されました (チュートリアル 15 参照)
	- 絵文字のデザインが変更されました
	- 乱数の再現性が v0.4.3 と互換がありません
	- 2D 物理演算は cm をデフォルトの単位に変更しました
	- `Glyph` 単位での描画の方法が変更されました
	- Windows 版は `<Siv3D.hpp>` のプリコンパイル済みを全てのソースファイルで自動でインクルードするようになりました。Main.cpp にある `# include <Siv3D.hpp>` は実質的には無意味です。`# define NO_S3D_USING` が必要な場合はプリコンパイル済みヘッダ作成用ヘッダ `stdafx.h` で行ってください
	- `Audio` は `Wave` と互換の形式でデータを保持しなくなりました。`.getWave()` は `.getSamples()` に置き換わりました。`GlobalAudio::BusGetSamples()` も利用できます

	#### コントリビューション
	- [nokotan](https://github.com/nokotan): **Web 版開発を全面的に担当**
	- [Ebishu-0309](https://github.com/Ebishu-0309): **`Geometry2D::` に多数の関数を実装**, **`Shape2D::Squircle()` の実装**, `Bezier2`, `Bezier3` の `.boundingRect()` を実装, コードの改善
	- [taotao54321](https://github.com/taotao54321): `Grid` の修正, コードの改善
	- [sthairno](https://github.com/sthairno): **Linux 版の IME 処理改善**
	- [itakawa](https://github.com/itakawa) : **Siv3D くん .obj ファイル提供**
	- [take-cheeze](https://github.com/take-cheeze): **GitHub Actions を使った CI の整備**
	- [Luke256](https://github.com/Luke256): コードの改善
	- [YASAI03](https://github.com/YASAI03): **HTTP クライアント機能 `SimpleHTTP` の提案・実装**
	- [falrnd](https://github.com/falrnd): `Geometry2D` の交差判定の改善
	- [yurkth](https://github.com/yurkth): **`GeoJSON` 関連の機能を提案・実装**
	- [ianCK](https://github.com/ianCK): コードの改善
	- [lriki](https://github.com/lriki): **Siv3D くんドット絵素材の提供**
	- [Ryoga-exe](https://github.com/Ryoga-exe): `Color::gamma()` のバグ修正
	- [sivboard](https://github.com/sivboard): スクリプト機能の実装追加とバグ修正
	- [agehama](https://github.com/agehama): PPM 画像読み込みのバグ修正
	- [kurokoji](https://github.com/kurokoji): **Linux 版 MessageBox の追加**
	- [ichi-raven](https://github.com/ichi-raven): コードの改善
	- [azaika](https://github.com/azaika): **`JSON` クラスの設計・実装**

	#### OpenSiv3D チャレンジ
	- \#01 統計関数: 白坂, マキア, fal_rnd
	- \#03 `Shape2D::Heart`: 野菜ジュース, てゃいの
	- \#04 2D 図形の交差判定: Ebishu, fal_rnd, きつねび
	- [\#05 Squircle](https://zenn.dev/link/comments/ed180236e76181): Ebishu, Ryoga.exe
	- [\#07 国と都市](https://zenn.dev/link/comments/76224190023391): torin (yurkth)
	- [\#10 `OutlineGlyph` to `Array<Polygon>`](https://zenn.dev/link/comments/e75e5e80eee914): Ebishu, fal_rnd


## v0.4 Generation

??? summary "v0.4.3 | 2020-04-11"
	#### 1. ドロネー図、ボロノイ図の作成
	ドロネー図、ボロノイ図の計算を行う `Subdivision2D` クラスが追加されました。
	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v043/01.gif?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF(0.99));
		const Rect rect(50, 50, Scene::Size() - Size(100, 100));

		Subdivision2D subdiv(rect);

		// ドロネー三角形分割の三角形リスト
		Array<Triangle> triangles;

		// ボロノイ図の情報のリスト
		Array<VoronoiFacet> facets;

		// facets を長方形でクリップし Polygon に変換したリスト
		Array<Polygon> facetPolygons;

		while (System::Update())
		{
			const Vec2 pos = Cursor::Pos();

			// 長方形上をクリックしたら
			if (rect.leftClicked())
			{
				// 点を追加
				subdiv.addPoint(pos);

				// ドロネー三角形分割の計算
				subdiv.calculateTriangles(triangles);

				// ボロノイ図の計算
				subdiv.calculateVoronoiFacets(facets);

				// 長方形の範囲外をクリップ
				facetPolygons = facets.map([rect = rect.asPolygon()](const VoronoiFacet& f)
				{
					return Geometry2D::And(Polygon(f.points), rect).front();
				});
			}

			rect.draw(ColorF(0.75));

			for (auto [i, facetPolygon] : Indexed(facetPolygons))
			{
				facetPolygon.draw(HSV(i * 25.0, 0.65, 0.8)).drawFrame(3, ColorF(0.25));
			}

			for (const auto& triangle : triangles)
			{
				triangle.drawFrame(2.5, ColorF(0.9));
			}

			for (const auto& facet : facets)
			{
				Circle(facet.center, 6).drawFrame(5).draw(ColorF(0.25));
			}

			// 現在のマウスカーソルから最短距離にある点を探す
			if (const auto nearestVertexID = subdiv.findNearest(pos))
			{
				const Vec2 nearestVertex = subdiv.getVertex(nearestVertexID.value());
				Line(pos, nearestVertex).draw(LineStyle::RoundDot, 5, ColorF(0.6));
				Circle(nearestVertex, 16).drawFrame(3.5);
			}
		}
	}
	```

	#### 2. 長方形詰込み
	長方形の集合を、別の大きな長方形に効率的に詰め込む問題を解決する `std::pair<Array<Rect>, Size>  RectanglePacking::Pack(const Array<Rect>& rects, int32 maxSide)` 関数が追加されました。詰め込み後の長方形のリストと、それらを詰め込める最小の長方形のサイズのペアを返します。入力の `rects` の位置情報は無視されます。`maxSide` は幅または高さの最大値で、これに収まらない場合は空の配列と `Size(0, 0)` のペアを返します。配列に含まれる長方形の順番は、入力と出力で変わりません。
	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v043/02.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF(0.99));

		// 詰め込む長方形
		const Array<Rect> input =
		{
			Rect(240, 210), Rect(500, 30), Rect(150, 120),
			Rect(60, 120), Rect(180, 60), Rect(120, 240)
		};

		// 詰め込みを計算
		const std::pair<Array<Rect>, Size> result = RectanglePacking::Pack(input, 600);

		while (System::Update())
		{
			Rect(result.second).draw(ColorF(0.7));

			for (auto [i, rect] : Indexed(result.first))
			{
				rect.draw(HSV(i * 40.0));
			}
		}
	}
	```

	アニメーション

	<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v043/02.mp4?raw=true" autoplay loop muted playsinline></video>

	``` C++
	# include <Siv3D.hpp>

	// ランダムな長方形の配列を作成
	Array<Rect> GenerateRandomRects()
	{
		Array<Rect> rects(Random(4, 32));

		for (auto& rect : rects)
		{
			const Point pos = RandomPoint(Rect(0, 0, Scene::Size() - Size(150, 150)));
			rect.set(pos, Random(20, 150), Random(20, 150));
		}

		return rects;
	}

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF(0.99));
		Array<Rect> input, output;
		Size size(0, 0);
		Point offset(0, 0);
		Stopwatch s;

		while (System::Update())
		{
			if (!s.isStarted() || s > 1.8s)
			{
				input = GenerateRandomRects();
				std::tie(output, size) = RectanglePacking::Pack(input, 1024);

				// 画面中央に表示するよう位置を調整
				offset = (Scene::Size() - size) / 2;
				for (auto& rect : output)
				{
					rect.moveBy(offset);
				}

				s.restart();
			}

			// アニメーション
			const double k = Min(s.sF() * 10, 1.0);
			const double t = Saturate(s.sF() - 0.2);
			const double e = EaseInOutExpo(t);

			Rect(offset, size).draw(ColorF(0.7, e));

			for (auto i : step(input.size()))
			{
				const auto& in = input[i];
				const auto& out = output[i];
				const Vec2 pos = in.pos.lerp(out.pos, e);
				const RectF rect(pos, out.size);
				rect.scaledAt(rect.center(), k)
					.draw(HSV(i * 25.0, 0.65, 0.9))
					.drawFrame(2, 0, ColorF(0.25));
			}
		}
	}
	```

	#### 3. GIF アニメーション読み込み
	GIF アニメーションファイルを読み込み、一連のフレームの Image と、フレームごとの表示時間を取得する `AnimatedGIFReader` クラスが追加されました。
	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v043/03.gif?raw=true)

	```C++
	# include <Siv3D.hpp>

	// アニメーション描画用のクラス
	struct AnimationTexture
	{
		Array<Texture> textures;

		// フレームの時間
		Array<int32> delays;

		int32 duration = 0;

		explicit operator bool() const noexcept
		{
			return !textures.isEmpty();
		}

		Size size() const noexcept
		{
			if (!textures)
			{
				return Size(0, 0);
			}

			return textures.front().size();
		}

		size_t frames() const noexcept
		{
			return textures.size();
		}

		size_t getFrameIndex(int32 timeMillisec) const noexcept
		{
			return AnimatedGIFReader::MillisecToIndex(timeMillisec, delays, duration);
		}

		const Texture& getTexture(int32 timeMillisec) const noexcept
		{
			return textures[getFrameIndex(timeMillisec)];
		}
	};

	void Main()
	{
		AnimationTexture animation;
		{
			// GIF ファイルを開く
			const AnimatedGIFReader gif(U"example/test.gif");

			if (!gif)
			{
				throw Error(U"Failed to open a gif file");
			}

			Array<Image> images;

			// GIF アニメーションを読み込み
			if (gif.read(images, animation.delays, animation.duration))
			{
				// Image を Texture に変換
				animation.textures = images.map([](const Image& i) { return Texture(i); });
			}
			else
			{
				throw Error(U"Failed to load a gif animation");
			}
		}

		// 画像のサイズ、フレーム数、アニメーションの長さ（ミリ秒）
		Print << U"{}, {} frames ({} ms)"_fmt(animation.size(), animation.frames(), animation.duration);

		const Point pos(10, 90);

		while (System::Update())
		{
			const int32 timeMillisec = static_cast<int32>(Scene::Time() * 1000);
			
			animation.getTexture(timeMillisec).draw(pos);
		}
	}
	```

	#### 4. Rect::rounded() で 4 つの角に異なる値を指定可能に
	`Rect::rounded()` に、長方形の左上、右上、右下、左下で異なる値を指定するオーバーロードが追加されました。
	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v043/04.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF(0.3));

		Array<Rect> rects;

		for (auto p : step(Size(3, 4)))
		{
			rects << Rect(p * Size(220, 140), 180, 100).movedBy(80, 40);
		}

		while (System::Update())
		{
			rects[0].rounded(30, 0, 0, 0).draw(HSV(20, 0.75, 1.0));
			rects[1].rounded(30, 30, 0, 0).draw(HSV(40, 0.75, 1.0));
			rects[2].rounded(0, 30, 0, 0).draw(HSV(60, 0.75, 1.0));

			rects[3].rounded(30, 0, 0, 30).draw(HSV(80, 0.75, 1.0));
			rects[4].rounded(10, 20, 30, 40).draw(HSV(100, 0.75, 1.0));
			rects[5].rounded(0, 30, 30, 0).draw(HSV(120, 0.75, 1.0));

			rects[6].rounded(100, 0, 0, 0).draw(HSV(140, 0.75, 1.0));
			rects[7].rounded(100, 0, 100, 0).draw(HSV(160, 0.75, 1.0));
			rects[8].rounded(0, 0, 100, 0).draw(HSV(180, 0.75, 1.0));

			rects[9].rounded(100, 0, 0, 20).draw(HSV(200, 0.75, 1.0));
			rects[10].rounded(100, 20, 100, 20).draw(HSV(220, 0.75, 1.0));
			rects[11].rounded(0, 20, 100, 0).draw(HSV(240, 0.75, 1.0));
		}
	}
	```


	#### 5. SimpleGUI::HorizontalRadioButtons()
	水平に並んだラジオボタン `SimpleGUI::HorizontalRadioButtons()` が SimpleGUI に追加されました。
	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v043/05.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF(0.8, 0.9, 1.0));

		const Array<String> options = { U"Windows", U"macOS", U"Linux" };
		size_t indexA = 0;
		size_t indexB = 0;

		while (System::Update())
		{
			// 水平
			SimpleGUI::HorizontalRadioButtons(indexA, options, Vec2(20, 20));

			// 縦
			SimpleGUI::RadioButtons(indexB, options, Vec2(20, 60));
		}
	}
	```

	#### 6. Math::InvLerp()
	- `Math::Lerp(begin, end, t) == value`
	- `Math::InvLerp(begin, end, value) == t`

	となるような値 `t` を求める `Math::InvLerp()` が追加されました。
	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v043/06.gif?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF(0.6, 0.8, 0.7));
		const Font font(40, Typeface::Bold);

		const double begin = 240.0;
		const double end = 450.0;

		while (System::Update())
		{
			const double value = Cursor::Pos().y;

			// Math::Lerp(begin, end, t) == value になる値 t を求める
			const double t = Math::InvLerp(begin, end, value);

			// 値を [0.0, 1.0] の範囲に収める
			const double st = Saturate(t);

			font(st).draw(20, 20);

			Line(Vec2(0, begin), Arg::direction(Scene::Width(), 0)).draw(2, ColorF(0.5));
			Line(Vec2(0, end), Arg::direction(Scene::Width(), 0)).draw(2, ColorF(0.5));

			Circle(Cursor::Pos(), 50).draw(ColorF(st));
		}
	}
	```

	#### 7. Line のコンストラクタ追加
	名前付き引数を使った `Line` のコンストラクタが 2 種類追加されました。`Line(pos, pos + dir)` のように `pos` を 2 回書く必要がなくなります。
	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v043/07.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		while (System::Update())
		{
			// 始点の位置、始点から見た終点の方向、終点までの距離
			Line(Scene::Center(), Arg::angle = 45_deg, 200)
				.draw(LineStyle::RoundCap, 10);

			// 始点の位置、終点までのベクトル
			Line(Scene::Center(), Arg::direction = Vec2(0, 200))
				.draw(LineStyle::RoundCap, 10, Palette::Orange);
		}
	}
	```

	#### 8. Rect::drawFrame(), Circle::drawPie(), Circle::drawArc() の 2 色指定
	`Rect::drawFrame()`, `Circle::drawPie()`, `Circle::drawArc()` に、内側の色と外側の色を別々に指定するオーバーロードが追加されました。
	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v043/08.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(Palette::White);

		while (System::Update())
		{
			// 内側 ColorF(0.1, 0.6, 0.3), 外側 ColorF(0.6, 1.0, 0.8)
			Rect(50, 50, 300)
				.drawFrame(30, ColorF(0.1, 0.6, 0.3), ColorF(0.6, 1.0, 0.8));

			// 内側 HSV(50), 外側 HSV(0)
			Circle(200, 200, 100)
				.drawPie(0_deg, 120_deg, HSV(50), HSV(0));

			// 内側 Palette::White, 外側 Palette::Black
			Circle(200, 200, 100)
				.drawArc(180_deg, 120_deg, 10, 10, Palette::White, Palette::Black);
		}
	}
	```

	#### 9. ZIP アーカイブの読み込み
	ZIP アーカイブ (.zip) の中身の取得や展開を行う `ZIPReader` クラスが追加されました。`ZIPReader::extractToMemory()` を使うと、ファイルをメモリ上で展開して `Texture` や `Audio` などを作成できます。
	- Windows で作成された Shift-JIS エンコードの ZIP アーカイブに含まれる日本語ファイル名は、Windows 以外の環境では正しく扱えません
	- 日本語ファイル名をあらゆるプラットフォームで正しく扱いたい場合、`UTF-8` エンコードで ZIP アーカイブを作成してください (7-zip の場合は `cu=on` オプションをつける）

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v043/09.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		const ZIPReader zip(U"example/zip/zip_test.zip");

		// 含まれているファイルやディレクトリの列挙
		for (const auto& path : zip.enumPaths())
		{
			Print << path;
		}

		// `zip_test/loremipsum.txt` を `unzipped1/` フォルダに展開
		zip.extract(U"zip_test/loremipsum.txt", U"unzipped1/");

		// `zip_test/image/` に含まれているすべてのファイルを `unzipped2/` フォルダに展開
		zip.extract(U"zip_test/image/*", U"unzipped2/");

		// すべてを `unzipped3/` フォルダに展開
		zip.extractAll(U"unzipped3/");

		// `zip_test/image/windmill.png` をメモリ上で展開してテクスチャを作成
		const Texture textureA(zip.extractToMemory(U"zip_test/image/windmill.png"));
		
		// `zip_test/image/siv3d-kun.png` をメモリ上で展開してテクスチャを作成
		const Texture textureB(zip.extractToMemory(U"zip_test/image/siv3d-kun.png"));

		while (System::Update())
		{
			textureA.draw();
			textureB.draw();
		}
	}
	```

	#### 10. 不正な Polygon 頂点の自動修正
	手入力などによる不正な `Polygon` の頂点を修正し、妥当な `Array<Polygon>` に変換する機能が追加されました。
	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v043/10.gif?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		const Font font(20, Typeface::Bold);

		Array<Vec2> points;
		Array<Polygon> solvedPolygons;

		while (System::Update())
		{
			if (MouseL.down())
			{
				points << Cursor::Pos();

				// 頂点列から適切な Polygon を作成
				solvedPolygons = Polygon::Correct(points, {});
			}
			else if (MouseR.down())
			{
				points.clear();
				solvedPolygons.clear();
			}

			for (auto [i, point] : Indexed(points))
			{
				Circle(point, 5).draw();
				Line(points[i], points[(i + 1) % points.size()])
					.drawArrow(2, Vec2(20, 20), Palette::Orange);
			}

			font(points).draw(Rect(20, 20, 600, 720));

			{
				Transformer2D trans(Mat3x2::Translate(640, 0));

				font(solvedPolygons).draw(Rect(20, 20, 600, 720));

				for (auto [i, solvedPolygon] : Indexed(solvedPolygons))
				{
					const HSV color(i * 40.0, 0.7, 1.0);
					solvedPolygon.draw(color);

					const auto& outer = solvedPolygon.outer();

					for (auto [k, point] : Indexed(outer))
					{
						const Vec2 begin = outer[k];
						const Vec2 end = outer[(k + 1) % outer.size()];
						const Vec2 v = (end - begin).normalized();
						const Vec2 c = (begin + end) / 2;
						const Vec2 oc = c + v.rotated(-90_deg) * 10;
						Line(oc - v * 20, oc + v * 20)
							.drawArrow(2, Vec2(10, 10), color);
					}
				}
			}
		}
	}
	```

	#### 11. Direct3D ドライバ / デバイスの種類の変更
	Windows 版で `#include <Siv3D.hpp>` の前に特別なマクロを定義すると、アプリケーションが使用する Direct3D ドライバーの種類を WARP, Reference などに変更できるようになりました。GPU のドライバの問題で正常な描画ができない場合に WARP によるたソフトウェアレンダリングを使用してください。描画負荷が軽いアプリケーションであれば、WARP で動かすプログラムをリリースすることも選択肢となります。これらのフラグは重複して指定することはできません。

	- デフォルト → dGPU (GeForce など) 優先
	- `SIV3D_WINDOWS_D3D_DRIVER_TYPE_HARDWARE_FAVOR_INTEGRATED` → iGPU (Intel UHD Graphics など) 優先
	- `SIV3D_WINDOWS_D3D_DRIVER_TYPE_WARP` → ソフトウェアラスタライザ
	- `SIV3D_WINDOWS_D3D_DRIVER_TYPE_REFERENCE` → リファレンスドライバ
	- 参考: https://docs.microsoft.com/en-us/windows/win32/api/d3dcommon/ne-d3dcommon-d3d_driver_type

	```C++
	// ソフトウェアレンダラ―を使用 (Windows でのみ有効）
	# define SIV3D_WINDOWS_D3D_DRIVER_TYPE_WARP

	// iGPU (Intel UHD Graphics など) 優先 (Windows でのみ有効）
	//# define SIV3D_WINDOWS_D3D_DRIVER_TYPE_HARDWARE_FAVOR_INTEGRATED

	// リファレンスドライバを使用 (Windows でのみ有効）
	//# define SIV3D_WINDOWS_D3D_DRIVER_TYPE_REFERENCE

	// Siv3D.hpp よりも前で定義
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF(0.8, 0.9, 1.0));

		const Texture texture(U"example/windmill.png");

		while (System::Update())
		{
			texture.draw();
		}
	}
	```

	#### 12. その他
	- Image to Polygon の堅牢性が向上し、クラッシュしなくなりました
	- Linux 版のビルドで AngelScript のリンクが不要になりました
	- macOS と Linux の一部環境で `Microphone` の初期化に失敗することがあった問題を修正しました
	- `isOpened()` というメンバ関数は `isOpen()` に名前が変更されました
	- zlib の圧縮展開を行う `Zlib::Compress()`, `Zlib:: Decompress()` を追加しました
	- `ParseOpt<float>()` が例外を投げることがあった問題を修正しました
	- `Math::InvSqrt2_v` が正しくなかったのを修正しました
	- Visual Studio 用のプロジェクトテンプレートにタグを指定しました
	- Visual Studio のプロジェクト作成時に Main.cpp が自動で開くようにしました
	- Windows 用プロジェクトの Icon.ico を icon.ico にリネームしました
	- `Camera2D` の `Scene::Size()` 依存を解消しました
	- `ParticleSystem2DParameters` の仕様を改善しました
	- 各種 RNG のシリアライズ、デシリアライズを実装しました
	- `Serial` が切断されても `isOpen()` が `true` を返していた問題を修正しました
	- `RoundRect` の頂点生成品質の問題を修正しました
	- `DynamicTexture` でサイズとフォーマットのみ指定した際のエラーを修正しました
	- macOS で日本語パスを扱うと一部の関数がクラッシュすることがあった問題を修正しました
	- Windows で `Graphics::SetTargetFrameRateHz()` が大きく不正確になることがあった問題を修正しました
	- `RenderTexture` のコンストラクタでは、特に明示しなければ `ColorF(0.0, 1.0)` で中身をクリアするよう仕様変更しました
	- `JSONWriter::write(bool)` の挙動が正しくなかった問題を修正しました
	- `BasicCamera3D` の `experimental::` を外しました
	- その他軽微な修正多数


??? summary "v0.4.2 | 2019-12-01"
	#### 1. SDFFont
	`SDFFont` は、グリフの画像を Distance field 形式で持つ `Font` クラスです。  
	これまでの `Font` クラスは、コンストラクタで指定した固定サイズでグリフごとのビットマップ画像を生成し、それをレンダリングするため、拡大描画時にぼやけるなど、サイズの変更に弱く、縁取りのようなエフェクトを適用することも困難でした。  
	`SDFFont` クラスは、グリフごとの Distance Field を生成し、拡大してもぼやけない手法でテキストをレンダリングします。`draw()` ごとに第一引数でフォントのサイズを指定でき、大きな値を入れても結果がぼやけることはありません。また、`Garphics2D::SetSDFParameters()` でパラメータを調整することで、レンダリング時に文字を太らせられます。太さと色を変えて 2 回以上テキストを描画することで、縁取りの表現も可能です。  
	ただし、`SDFFont` の生成や描画のコストは `Font` に比べて大きいため、`Font` で十分なケースでは従来通り `Font` を使うべきです。  
	`SDFFont` の品質は、コンストラクタで指定するグリフの Distance Field のサイズと、描画する字形の複雑さに影響されます。画数の少ない数字やアルファベット、曲線的でシンプルな字形であれば、40 ピクセル以下の Distance Field でもきれいなテキストをレンダリングできますが、複雑な字形になるほど、小さな Distance Field では描画結果が乱れたり、ノイズが目立つことがあります。文字の太らせについても、大きい値ではノイズが目立つことがあります。`SDFFont` をアプリケーションで使用する際は、テキストの描画結果をチェックし、適切な Distance Field サイズを設定しましょう。

	##### SDFFont の基本

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v042/01.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF(0.4, 0.5, 0.6));

		// グリフごとの Distance field のサイズ
		const int32 distanceFieldSize = 60;
		
		// SDFFont
		const SDFFont sdfFont(distanceFieldSize, Typeface::Bold);

		const String text = U"OpenSiv3D";

		while (System::Update())
		{
			// SDF パラメータの設定
			Graphics2D::SetSDFParameters(sdfFont.pixelRange());
			sdfFont(text).draw(40, Vec2(20, 20));
			sdfFont(text).draw(80, Vec2(20, 80));
			sdfFont(text).draw(120, Vec2(20, 180));

			// SDF パラメータの設定、太らせを 0.2 に
			Graphics2D::SetSDFParameters(sdfFont.pixelRange(), 0.2);
			sdfFont(text).draw(120, Vec2(20, 320), Palette::Black);

			// SDF パラメータの設定、太らせを 0.0 に
			Graphics2D::SetSDFParameters(sdfFont.pixelRange(), 0.0);
			sdfFont(text).draw(120, Vec2(20, 320));
		}
	}
	```

	##### SDFFont の事前生成
	`SDFFont` の各グリフの Distance field は、生成に時間がかかるため、使用するグリフをあらかじめ生成して保存しておくとアプリケーションの速度低下が防げます。`SDFFont::preload(s)` で、文字列 `s` 含まれるグリフの Distance field を生成、`SDFFont::preload(imagePath, jsonPath)` で 2 つのファイルに生成結果を保存し、`SDFFont` のコンストラクタでこれらのファイルをロードします。事前生成されていなかったグリフは実行時に生成されます。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v042/02.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF(0.4, 0.5, 0.6));

		// グリフごとの Distance field のサイズ
		const int32 distanceFieldSize = 60;

		//////////////////////////////////////////
		//
		// SDFFont Distance field の事前生成
		//
		// ※ 保存できたら不要なのでコメントアウト
		{
			String s;
			for (auto i : Range(32, 126))
			{
				s << char32(i);
			}

			// SDF の作成には時間がかかるので、
			// ASCII 文字をあらかじめ SDF 化して、フォント情報を保存しておく
			SDFFont(distanceFieldSize, Typeface::Bold)
				.preload(s)
				.saveGlyphs(U"sdf-font/bold_60.png", U"sdf-font/bold_60.json");
		}
		//
		//////////////////////////////////////////
		
		// SDFFont を作成し、事前生成した Distance field をロード
		// フォントの種類や Distance field が一致しないといけない
		const SDFFont sdfFont({ U"sdf-font/bold_60.png", U"sdf-font/bold_60.json" }, distanceFieldSize, Typeface::Bold);

		if (!sdfFont) // ロードに失敗したら
		{
			throw Error(U"Failed to load SDFFont");
		}

		const String text = U"OpenSiv3D";

		while (System::Update())
		{
			// SDF パラメータの設定
			Graphics2D::SetSDFParameters(sdfFont.pixelRange());
			sdfFont(text).draw(120, Vec2(20, 20));
		}
	}
	```

	##### 比較用サンプル

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v042/03.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF(0.4, 0.5, 0.6));

		constexpr Vec2 pos(0, 0);
		const String text = U"OpenSiv3D\nあいうえお";

		//////////////////////////////////////////
		//
		// SDFFont Distance field の事前生成
		//
		// ※ 保存できたら不要なのでコメントアウト
		{
			String s;
			for (auto i : Range(32, 126))
			{
				s << char32(i);
			}
			s += text;

			// SDF の作成には時間がかかるので、
			// ASCII 文字と text をあらかじめ SDF 化して、フォント情報を保存しておく
			SDFFont(60, Typeface::Light).preload(s).saveGlyphs(U"sdf-font/light_60.png", U"sdf-font/light_60.json");
			SDFFont(60, Typeface::Heavy).preload(s).saveGlyphs(U"sdf-font/heavy_60.png", U"sdf-font/heavy_60.json");
			SDFFont(50, U"example/font/LogoTypeGothic/LogoTypeGothic.otf").preload(s).saveGlyphs(U"sdf-font/logo_50.png", U"sdf-font/logo_50.json");
		}
		//
		//////////////////////////////////////////

		// SDFFont を作成し、事前生成した Distance field をロード
		const Array<SDFFont> sdfFonts =
		{
			SDFFont({ U"sdf-font/light_60.png", U"sdf-font/light_60.json" }, 60, Typeface::Light),
			SDFFont({ U"sdf-font/heavy_60.png", U"sdf-font/heavy_60.json" }, 60, Typeface::Heavy),
			SDFFont({ U"sdf-font/logo_50.png", U"sdf-font/logo_50.json" }, 50, U"example/font/LogoTypeGothic/LogoTypeGothic.otf"),
		};

		for (const auto& sdfFont : sdfFonts)
		{
			if (!sdfFont) // ロードに失敗したら
			{
				throw Error(U"Failed to load SDFFont");
			}
		}

		// 比較用の通常 Font
		const Array<Font> fonts =
		{
			Font(60, Typeface::Light),
			Font(60, Typeface::Heavy),
			Font(50, U"example/font/LogoTypeGothic/LogoTypeGothic.otf"),
		};

		size_t fontIndex = 0, method = 0;
		double fontSize = 80, outline1 = 0.0, outline2 = 0.0;
		HSV innerColor = Palette::Black, outlineColor = Palette::White;

		while (System::Update())
		{
			const auto& sdfFont = sdfFonts[fontIndex];
			const auto& font = fonts[fontIndex];

			if (method == 0)
			{
				Graphics2D::SetSDFParameters(sdfFont.pixelRange(), outline2);
				sdfFont(text).draw(fontSize, pos, innerColor);

				Graphics2D::SetSDFParameters(sdfFont.pixelRange(), outline1);
				sdfFont(text).draw(fontSize, pos, outlineColor);

				Graphics2D::SetSDFParameters(sdfFont.pixelRange());
				sdfFont(text).draw(fontSize, pos, innerColor);
			}
			else if (method == 1)
			{
				Transformer2D tr(Mat3x2::Scale(fontSize / font.fontSize()));
				font(text).draw(pos, innerColor);
			}

			SimpleGUI::RadioButtons(fontIndex, { U"Light 60", U"Heavy 60", U"Logo 50" }, Vec2(20, 360), 150);
			SimpleGUI::RadioButtons(method, { U"SDFFont", U"Font" }, Vec2(20, 480), 150);
			SimpleGUI::Slider(U"size: {:.0f}"_fmt(fontSize), fontSize, 15, 550, Vec2(20, 560), 150, 200);
			SimpleGUI::Slider(U"outline1: {:.2f}"_fmt(outline1), outline1, 0.0, 0.49, Vec2(20, 600), 150, 200, (method == 0));
			SimpleGUI::Slider(U"outline2: {:.2f}"_fmt(outline2), outline2, 0.0, 0.49, Vec2(20, 640), 150, 200, (method == 0));
			SimpleGUI::ColorPicker(innerColor, Vec2(400, 560));
			SimpleGUI::ColorPicker(outlineColor, Vec2(580, 560));
		}
	}
	```

	#### 2. 集中線描画
	実験的ライブラリ群 HamFramework に追加された `SaturatedLinework` クラスによって、コミカルな効果や疾走感を演出するための集中線を簡単に描画できるようになりました。設定するパラメータは、ターゲットの図形、外周の長方形、線の本数、線の太さ、長さのばらつき、乱数シードなどがあり、`.draw()` の引数で色を指定できます。多数の三角形を生成して描画する方法で表現されています。パラメータを変更しなければ、生成した三角形は再利用されます。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v042/04.png?raw=true)

	```C++
	# include <Siv3D.hpp>
	# include <HamFramework.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF(0.98, 0.96, 0.94));

		// ターゲットの図形
		Ellipse target(400, 300, 180, 120);

		// 外周の長方形
		Rect outer = Scene::Rect();

		// 線の太さ
		double minThickness = 3.0, maxThickness = 10.0;
		
		// 線の本数
		double lineCount = 150;

		// 線の長さのばらつき
		double offsetRange = 60.0;

		// 乱数シード
		uint64 seed = 12345;

		SaturatedLinework<Ellipse> linework(target, outer);
		linework
			.setThickness(minThickness, maxThickness)
			.setLineCount(static_cast<size_t>(lineCount))
			.setOffsetRange(offsetRange);

		const Texture texture(Emoji(U"🦀"));

		while (System::Update())
		{
			if (MouseR.down())
			{
				target.setCenter(Cursor::Pos());
				linework.setTargetShape(target);
			}

			texture.scaled(1.6).drawAt(target.center);

			// 集中線を描画
			linework.draw(ColorF(0.1));

			if (SimpleGUI::Slider(U"lineCount", lineCount, 0.0, 400.0, Vec2(20, 20), 150))
			{
				linework.setLineCount(static_cast<size_t>(lineCount));
			}

			if (SimpleGUI::Slider(U"offsetRange", offsetRange, 0.0, 100.0, Vec2(20, 60), 150))
			{
				linework.setOffsetRange(offsetRange);
			}

			if (SimpleGUI::Button(U"Change seed", Vec2(20, 100)))
			{
				seed = RandomUint64();
				linework.setSeed(seed);
			}
		}
	}
	```

	#### 3. シリアル通信
	シリアル通信を使って、外部デバイスとデータの送受信ができるようになりました。以下では Arduino UNO で LED の点灯/消灯を PC から制御し、合わせて 1 バイトの数値データをやり取りするサンプルを示します。

	##### Arduino のコード

	```C++
	void setup()
	{
		pinMode(13, OUTPUT); // 13 ピン - LED - 抵抗 - GND

		// 9600bps でシリアルポートを開く
		Serial.begin(9600);
	}

	unsigned char i = 0; // テスト用に PC 側に送る値

	void loop()
	{
		// 250 ミリ秒止める
		delay(250);

		// シリアルポートに 1 バイト出力
		Serial.write(i);

		++i;

		// シリアル通信で受信したデータを読み込む
		const int val = Serial.read();

		if (val == -1) // 受信したデーが無い
		{
			return;
		}

		if (val == 0)
		{
			digitalWrite(13, LOW); // LOW を出力
		}
		else if (val == 1)
		{
			digitalWrite(13, HIGH); // HIGH を出力
		}
		else if (val == 2)
		{
			i = 0;
		}
	}
	```

	##### PC 側のコード

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		// シリアルポートの一覧を取得
		const Array<SerialPortInfo> infos = System::EnumerateSerialPorts();
		const Array<String> options = infos.map([](const SerialPortInfo& info)
		{
			return U"{} ({})"_fmt(info.port, info.description);
		}) << U"none";

		Serial serial;
		size_t index = (options.size() - 1);

		while (System::Update())
		{
			const bool isOpen = serial.isOpen(); // OpenSiv3D v0.4.2 以前は serial.isOpened()

			if (SimpleGUI::Button(U"Write 0", Vec2(200, 20), 120, isOpen))
			{
				// 1 バイトのデータ (0) を書き込む
				serial.writeByte(0);
			}

			if (SimpleGUI::Button(U"Write 1", Vec2(340, 20), 120, isOpen))
			{
				// 1 バイトのデータ (1) を書き込む
				serial.writeByte(1);
			}

			if (SimpleGUI::Button(U"Write 2", Vec2(480, 20), 120, isOpen))
			{
				// 1 バイトのデータ (2) を書き込む
				serial.writeByte(2);
			}

			if (SimpleGUI::RadioButtons(index, options, Vec2(200, 60)))
			{
				ClearPrint();

				if (index == (options.size() - 1))
				{
					serial = Serial();
				}
				else
				{
					Print << U"Open {}"_fmt(infos[index].port);

					// シリアルポートをオープン
					if (serial.open(infos[index].port))
					{
						Print << U"Succeeded";
					}
					else
					{
						Print << U"Failed";
					}
				}
			}

			if (const size_t available = serial.available())
			{
				// シリアル通信で受信したデータを読み込んで表示
				Print << U"READ: " << serial.readBytes();
			}
		}
	}
	```

	#### 4. PoissonDisk2D
	ほどよい距離で重ならない点群を生成する `PoissonDisk2D` クラスが追加されました。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v042/05.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF(0.2, 0.3, 0.4));

		const Rect rect(100, 100, 600, 400);

		double r = 15.0;

		// 点群を生成
		PoissonDisk2D pd(rect.size, r);

		while (System::Update())
		{
			rect.drawFrame(1, 1, ColorF(0.2));

			for (const auto& point : pd.getPoints())
			{
				Circle(point, r / 4).movedBy(rect.pos).draw();
			}

			if (SimpleGUI::Slider(r, 5.0, 40.0, Vec2(10, 10)))
			{
				pd = PoissonDisk2D(rect.size, r);
			}
		}
	}
	```

	#### 5. JSONWriter
	成形された JSON ファイルを出力するヘルパークラスが追加されました。順次出力のため、実際に保存されるのと同じ順番でデータを出力をする必要があります。

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		JSONWriter json;

		json.startObject();
		{
			json.key(U"Window").startObject();
			{
				json.key(U"title").write(U"My application");
				json.key(U"width").write(800);
				json.key(U"height").write(600);
				json.key(U"sizable").write(false);
			}
			json.endObject();

			json.key(U"Scene").startObject();
			{
				json.key(U"background").write(ColorF(0.8, 0.9, 1.0));
			}
			json.endObject();

			json.key(U"Array").startObject();
			{
				json.key(U"values").writeArray({ 11, 22, 33, 44, 55 });
			}
			json.endObject();

			json.key(U"Items").startArray();
			{
				json.startObject();
				{
					json.key(U"label").write(U"Forest");

					json.key(U"pos").startObject();
					{
						json.key(U"x").write(100);
						json.key(U"y").write(100);
					}
					json.endObject();
				}
				json.endObject();

				json.startObject();
				{
					json.key(U"label").write(U"Ocean");

					json.key(U"pos").startObject();
					{
						json.key(U"x").write(300);
						json.key(U"y").write(200);
					}
					json.endObject();
				}
				json.endObject();

				json.startObject();
				{
					json.key(U"label").write(U"Mountain");

					json.key(U"pos").startObject();
					{
						json.key(U"x").write(500);
						json.key(U"y").write(100);
					}
					json.endObject();
				}
				json.endObject();
			}
			json.endArray();
		}
		json.endObject();

		// ここまでの内容を保存
		json.save(U"test.json");

		while (System::Update())
		{

		}
	}
	```

	出力される JSON ファイル
	```JSON
	{
	"Window": {
		"title": "My application",
		"width": 800,
		"height": 600,
		"sizable": "false"
	},
	"Scene": {
		"background": "(0.8, 0.9, 1, 1)"
	},
	"Array": {
		"values": [
		11,
		22,
		33,
		44,
		55
		]
	},
	"Items": [
		{
		"label": "Forest",
		"pos": {
			"x": 100,
			"y": 100
		}
		},
		{
		"label": "Ocean",
		"pos": {
			"x": 300,
			"y": 200
		}
		},
		{
		"label": "Mountain",
		"pos": {
			"x": 500,
			"y": 100
		}
		}
	]
	}
	```

	#### 6. Geometry2D::IsClockwise()
	頂点の配列が時計回りかどうかを判定する関数が追加されました。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v042/06.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF(0.96, 0.98, 1.0));
		
		Array<Vec2> points;

		while (System::Update())
		{
			if (MouseL.down())
			{
				points << Cursor::Pos();
			}

			if (MouseR.down())
			{
				points.clear();
			}

			const bool isClockwise = Geometry2D::IsClockwise(points);

			ClearPrint();
			Print << isClockwise;

			for (const auto& point : points)
			{
				Circle(point, 10).draw(Palette::Orange);
			}

			if (points.size() > 2)
			{
				// 時計回りになるように矢印でつなぐ
				if (isClockwise)
				{
					for (size_t i = 0; i < points.size(); ++i)
					{
						Line(points[i], points[(i + 1) % points.size()])
							.stretched(-10)
							.drawArrow(3, Vec2::All(20), ColorF(0.25));
					}
				}
				else
				{
					for (size_t i = 0; i < points.size(); ++i)
					{
						Line(points[i], points[(i + 1) % points.size()])
							.reversed()
							.stretched(-10)
							.drawArrow(3, Vec2::All(20), ColorF(0.25));
					}
				}
			}
		}
	}
	```

	#### 7. Circle::draw(innerColor, outerColor)
	`Circle` や `Ellipse` で中心の色と外周の色を指定し、グラデーションで描画できるようになりました。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v042/07.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		while (System::Update())
		{
			// 中心が黄色、外周が黒
			Circle(Scene::Center(), 400).draw(Palette::Yellow, Palette::Black);
		}
	}
	```

	#### 8. SimpleGUI::Headline / ColorPicker
	SimpleGUI に、見出しを付けるヘッドラインと、色を選択するカラーピッカーが追加されました。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v042/08.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		double p0 = 1.0, p1 = 0.4, p2 = 0.5;
		HSV hsv = Palette::Gray;
		size_t index = 0;

		while (System::Update())
		{
			Scene::SetBackground(hsv);

			// Headline
			SimpleGUI::Headline(U"Config", Vec2(20, 20));
			{
				SimpleGUI::Slider(U"Param1", p0, Vec2(20, 60));
				SimpleGUI::Slider(U"Param2", p1, Vec2(20, 100));
				SimpleGUI::Slider(U"Param3", p2, Vec2(20, 140));
			}

			SimpleGUI::Headline(U"Background", Vec2(240, 20));
			{
				// カラーピッカー
				SimpleGUI::ColorPicker(hsv, Vec2(240, 60));
			}

			SimpleGUI::Headline(U"Terrain", Vec2(420, 20));
			{
				SimpleGUI::RadioButtons(index, { U"Plain", U"Hill", U"Mountain" }, Vec2(420, 60), 150);
			}
		}
	}
	```

	#### 9. ToastNotification
	Windows でトースト通知を出せるようになりました。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v042/09.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF(0.9, 0.6, 0.3));
		
		// 通知ごとに割り振られる ID
		NotificationID latest = -1;

		// 画像を作成・保存
		Emoji::CreateImage(U"🍕").save(U"pizza.png");

		while (System::Update())
		{
			ClearPrint();

			// 通知の状態
			Print << (int32)Platform::Windows::ToastNotification::GetState(latest);

			// アクションボタンの結果
			Print << U"Action: " << Platform::Windows::ToastNotification::GetAction(latest);

			if (SimpleGUI::Button(U"Send a notification", Vec2(10, 70)))
			{
				ToastNotificationProperty toast{
					.title = U"Title", // 通知のタイトル
					.message = U"Message", // 通知の本文
					.imagePath = U"pizza.png", // 大きい画像だと使われないことがある
					.actions = { U"Yes", U"No" } // アクションボタン（不要な場合は設定しない）
				};

				// 通知ごとに割り振られる ID を取得
				latest = Platform::Windows::ToastNotification::Show(toast);
			}
		}
	}
	```

	#### 10. SimpleGUIManager
	TOML ファイルに SimpleGUI の各ウィジェットを記述し、プログラムでロードできるようになります。実行中に操作した値を保存することもできます。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v042/10.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		// SimpleGUI のウィジェット情報を記述したファイル
		const FilePath tomlPath = FileSystem::FullPath(U"example/gui/gui.toml");
		
		// 更新を検知
		const DirectoryWatcher watcher(FileSystem::ParentPath(tomlPath));
		
		// GUI をロード
		SimpleGUIManager gui(tomlPath);

		while (System::Update())
		{
			// TOML が更新されたら GUI を再ロード
			for (const auto& change : watcher.retrieveChanges())
			{
				if (change.first == tomlPath && change.second == FileAction::Modified)
				{
					ClearPrint();
					gui.load(tomlPath);
				}
			}

			// GUI を更新・描画
			gui.draw();

			if (gui.button(U"bt-OK")) // "bt-OK" という名前のボタンが押された
			{
				Print << U"OK";
			}
			else if (gui.button(U"bt-Cancel")) // "bt-Cancel" という名前のボタンが押された
			{
				Print << U"Cancel";
			}

			Scene::SetBackground(gui.colorPicker(U"cp-Color")); // "cp-Color という名前のカラーピッカーの値 
		}

		// ウィジェット情報と値を save.toml という名前で保存する
		// これを SimpleGUIManager で読み込ませることもできる
		//gui.save(U"save.toml");
	}
	```

	#### 11. Print の排他制御
	デバッグなどの用途のために、`Print` を複数スレッドから同時に呼び出し可能になりました。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v042/11.png?raw=true)

	```C++
	// Siv3D の並列処理関数を有効化するマクロ
	// ヘッダが増えるためコンパイル時間が少し長くなる
	# define SIV3D_CONCURRENT
	# include <Siv3D.hpp>

	void Main()
	{
		// 0～15 の数に対して、複数スレッドで処理
		Range(0, 15).parallel_each([](int32 i)
		{
			// スレッド識別子を合わせて表示
			Print << U"{}: {}"_fmt(std::this_thread::get_id(), i);
		});

		while (System::Update())
		{

		}
	}
	```

	#### 12. 3D 形状
	`Quaternion`, `OBB` が追加されました。`Ray` と各種 3D 形状との交差判定もいくつか追加されました。

	ただし v0.4.1 と同様、2D 描画で 3D をエミュレートする簡易的なものなので、次のような制約があります。

	- 深度バッファが無いので前後判定ができない
	- 遠近クリップが無いのでカメラに近すぎるオブジェクトが正しく表示されない

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v042/12.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		constexpr double fov = 45_deg;
		constexpr Vec3 focusPosition(0, 0, 0);
		Vec3 eyePosition(0, 10, 0);
		experimental::BasicCamera3D camera(Scene::Size(), fov, eyePosition, focusPosition);

		Array<OBB> objects;

		for (auto x : Range(-2, 2))
		{
			for (auto z : Range(2, -2, -1))
			{
				objects << OBB(Vec3(x * 4, 1, z * 4), Vec3(3, 2, 0.5), Quaternion::RollPitchYaw(0, x * 30_deg, 0));
				objects << OBB(Vec3(x * 4, 5, z * 4), Vec3(2, 1, 2), Quaternion::RollPitchYaw(x * 30_deg, 0, 0));
			}
		}

		while (System::Update())
		{
			eyePosition = Cylindrical(20, Scene::Time() * 30_deg, 8 + Periodic::Sine0_1(4s) * 8);
			camera.setView(eyePosition, focusPosition);
			const Mat4x4 mat = camera.getMat4x4();

			{
				ScopedRenderStates2D culling(RasterizerState::SolidCullBack);

				for (auto i : Range(-10, 10))
				{
					Line3D(Vec3(-10, 0, i), Vec3(10, 0, i)).draw(mat, ColorF(0.5));
					Line3D(Vec3(i, 0, -10), Vec3(i, 0, 10)).draw(mat, ColorF(0.5));
				}

				const Vec3 eyePos = camera.getEyePosition();
				const Vec3 rayEnd = camera.screenToWorldPoint(Cursor::Pos(), 0.5f);
				const Ray cursorRay(eyePos, (rayEnd - eyePos).normalized());

				objects.sort_by([&](const OBB& a, const OBB& b)
				{
					return (eyePos.distanceFromSq(a.center)) > (eyePos.distanceFromSq(b.center));
				});

				Optional<size_t> intersectionIndex;

				for (auto [i, object] : IndexedReversed(objects))
				{
					if (cursorRay.intersects(object))
					{
						intersectionIndex = i;
						Cursor::RequestStyle(CursorStyle::Hand);
						break;
					}
				}

				for (auto [i, object] : Indexed(objects))
				{
					const HSV color((object.center.x * 50 + object.center.z * 10), 1.0, (i == intersectionIndex) ? 1.0 : 0.3);
					object.draw(mat, color);
				}
			}
		}
	}
	```

	#### v0.4.1 サンプルアップデート
	`AABB`, `Triangle3D`, `Line3D` などが、`s3d::experimental` 名前空間から `s3d` 名前空間に移動しました。

	###### 3D Triangles

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v041/17.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		constexpr std::array<Vec3, 8> vertices =
		{
			Vec3(-1, 1, -1),
			Vec3(1, 1, -1),
			Vec3(-1, -1, -1),
			Vec3(1, -1, -1),
			Vec3(1, 1, 1),
			Vec3(-1, 1, 1),
			Vec3(1, -1, 1),
			Vec3(-1, -1, 1),
		};

		constexpr std::array<uint32, 36> indices =
		{
			0, 1, 2, 2, 1, 3,
			5, 4, 0, 0, 4, 1,
			1, 4, 3, 3, 4, 6,
			5, 0, 7, 7, 0, 2,
			4, 5, 6, 6, 5, 7,
			2, 3, 7, 7, 3, 6,
		};

		constexpr double fov = 45_deg;
		constexpr Vec3 focusPosition(0, 0, 0);
		Vec3 eyePosition(0, 4, 0);
		experimental::BasicCamera3D camera(Scene::Size(), fov, eyePosition, focusPosition);

		while (System::Update())
		{
			eyePosition = Cylindrical(8, Scene::Time() * 30_deg, Math::Sin(Scene::Time()) * 4);
			camera.setView(eyePosition, focusPosition);
			const Mat4x4 mat = camera.getMat4x4();

			{
				ScopedRenderStates2D culling(RasterizerState::SolidCullBack);

				for (auto i : step(12))
				{
					const Vec3 p0(vertices[indices[i * 3 + 0]]);
					const Vec3 p1(vertices[indices[i * 3 + 1]]);
					const Vec3 p2(vertices[indices[i * 3 + 2]]);

					Triangle3D(p0, p1, p2).draw(mat, HSV(i * 30));
				}
			}
		}
	}
	```

	##### 3D AABB

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v041/18.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		constexpr double fov = 45_deg;
		constexpr Vec3 focusPosition(0, 0, 0);
		Vec3 eyePosition(0, 10, 0);
		experimental::BasicCamera3D camera(Scene::Size(), fov, eyePosition, focusPosition);

		while (System::Update())
		{
			eyePosition = Cylindrical(20, Scene::Time() * 30_deg, 8 + Periodic::Sine0_1(4s) * 8);
			camera.setView(eyePosition, focusPosition);
			const Mat4x4 mat = camera.getMat4x4();

			{
				ScopedRenderStates2D culling(RasterizerState::SolidCullBack);

				for (auto i : Range(-10, 10))
				{
					Line3D(Vec3(-10, 0, i), Vec3(10, 0, i)).draw(mat, ColorF(0.5));
					Line3D(Vec3(i, 0, -10), Vec3(i, 0, 10)).draw(mat, ColorF(0.5));
				}

				AABB(Vec3(0, 1, 0), Vec3(2, 2, 2)).draw(mat, Palette::White);
				AABB(Vec3(-8, 1, 8), Vec3(2, 2, 2)).draw(mat, HSV(0));
				AABB(Vec3(8, 1, 8), Vec3(2, 2, 2)).draw(mat, HSV(90));
				AABB(Vec3(8, 1, -8), Vec3(2, 2, 2)).draw(mat, HSV(270));
				AABB(Vec3(-8, 1, -8), Vec3(2, 2, 2)).draw(mat, HSV(180));
			}
		}
	}
	```

	##### 3D Terrain

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v041/19.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF(0.05, 0.3, 0.7));

		RenderTexture rt(100, 100, ColorF(0.0), TextureFormat::R32_Float);
		Grid<float> heightMap;
		Grid<Float3> positions;

		constexpr double fov = 45_deg;
		constexpr Vec3 focusPosition(50, 0, -50);
		Vec3 eyePosition(0, 100, 0);
		experimental::BasicCamera3D camera(Scene::Size(), fov, eyePosition, focusPosition);

		while (System::Update())
		{
			eyePosition = Cylindrical(Arg::r = 80, Arg::phi = Scene::Time() * 30_deg, Arg::y = 50) + Vec3(50, 0, -50);
			camera.setView(eyePosition, focusPosition);
			const Mat4x4 mat = camera.getMat4x4();

			rt.read(heightMap);
			{
				positions.resize(heightMap.size());

				for (auto p : step(heightMap.size()))
				{
					positions[p] = Float3(p.x, heightMap[p], -p.y);
				}
			}

			{
				ScopedRenderTarget2D target(rt);
				ScopedRenderStates2D blend(BlendState::Additive);

				if (MouseL.pressed())
				{
					Circle(Cursor::Pos(), 8).draw(ColorF(Scene::DeltaTime() * 24.0));
				}
			}

			if (positions)
			{
				ScopedRenderStates2D culling(RasterizerState::SolidCullBack);

				for (auto x : step(positions.width() - 1))
				{
					for (auto y : step(positions.height()))
					{
						const Float3 begin = positions[{x, y}];
						const Float3 end = positions[{x + 1, y}];
						const ColorF color = HSV(120 - (begin.y + end.y) * 3, 0.75, 0.7);
						Line3D(begin, end).draw(mat, color);
					}
				}

				for (auto x : step(positions.width()))
				{
					for (auto y : step(positions.height() - 1))
					{
						const Float3 begin = positions[{x, y}];
						const Float3 end = positions[{x, y + 1}];
						const ColorF color = HSV(120 - (begin.y + end.y) * 3, 0.75, 0.7);
						Line3D(begin, end).draw(mat, color);
					}
				}
			}

			rt.draw(ColorF(0.1));
		}
	}
	```

	#### 13. Microphone 不具合修正
	macOS など一部の環境でマイクが使えなかった不具合を修正しました。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v042/13.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		// マイクをセットアップ
		Microphone mic(unspecified); // unspecified を指定すると既定の音声入力デバイスを選択

		if (!mic)
		{
			// マイクを利用できない場合、終了
			throw Error(U"Microphone not available");
		}

		// 録音をスタート
		mic.start();

		LineString points(800);

		FFTResult fft;

		while (System::Update())
		{
			// 波形を可視化
			{
				const size_t pos = mic.posSample();
				const Array<WaveSampleS16>& buffer = mic.getBuffer();
				const size_t bufferLength = buffer.size();

				for (size_t i = 0; i < points.size(); ++i)
				{
					const size_t bufferPos = (pos + bufferLength - (800 - i)) % bufferLength;
					const double value = buffer[bufferPos].left / 32768.0;
					points[i].set(i, 300 - value * 300);
				}

				points.draw(2);
			}

			// 周波数スペクトラムを取得
			mic.fft(fft);

			// 周波数スペクトラム結果を可視化
			for (auto i : step(800))
			{
				const double size = Pow(fft.buffer[i], 0.6f) * 1200;
				RectF(Arg::bottomLeft(i, 600), 1, size).draw(HSV(240 - i));
			}

			// 周波数スペクトラム上に周波数を表示
			Rect(Cursor::Pos().x, 0, 1, Scene::Height()).draw();
			ClearPrint();
			Print << U"{} Hz"_fmt(Cursor::Pos().x * fft.resolution);
		}
	}
	```

	#### 14. QRDecoder 不具合修正
	複数の QR コードの検出の不具合を修正しました。

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);

		// Web カメラを起動
		Webcam webcam(0);
		webcam.setResolution(1280, 720);
		if (!webcam.start())
		{
			throw Error(U"");
		}

		Image image;
		DynamicTexture texture;
		QRDecoder qrDecoder;
		Array<std::pair<Quad, bool>> quads;

		while (System::Update())
		{
			// 新しい撮影フレームを取得
			if (webcam.hasNewFrame())
			{
				webcam.getFrame(image);

				Array<QRContent> qrs;

				// QR コードを検出
				qrDecoder.decode(image, qrs);

				quads.clear();

				for (const auto& qr : qrs)
				{
					quads.emplace_back(qr.quad, qr.isValid());

					// データの読み込みに成功した場合テキストを表示
					if (qr.isValid())
					{
						Print << qr.text;
					}
				}

				texture.fill(image);
			}

			texture.draw();

			// QR コードの領域を表示
			// データの読み込みに成功した場合赤色
			for (const auto& quad : quads)
			{
				quad.first.drawFrame(6, quad.second ? Palette::Red : Palette::Gray);
			}
		}
	}
	```

	#### 15. RenderTexture, MSRenderTexture 改善
	`RenderTexture` や `MSRenderTexture` の `.clear()`, `.read()`, `.resolve()` が const メンバ関数に修正され、使いやすくなりました。


??? summary "v0.4.1 | 2019-07-20"
	#### 1. レンダーテクスチャ
	これまで、図形やテクスチャはシーンにしか描画できませんでしたが、プログラムで用意した別のレンダーテクスチャにも描画できるようになりました。`RenderTexture` を作成し、`ScopedRenderTarget2D` オブジェクトのコンストラクタにレンダーテクスチャを渡すと、`ScopedRenderTarget2D` オブジェクトのスコープが有効な間、図形やテクスチャがそのレンダーテクスチャに描画されます（レンダーターゲットの変更）。描画されたレンダーテクスチャは、レンダーターゲットから解除されたあとにテクスチャとして描画に転用できます。

	注意: レンダーターゲットとして設定されている最中のレンダーテクスチャを、描画に使用することはできません。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v041/1.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		// シーンの背景色を淡い水色に設定
		Scene::SetBackground(ColorF(0.8, 0.9, 1.0));
		
		// 絵文字
		const Texture emoji(Emoji(U"😇"));

		// レンダーテクスチャ
		RenderTexture rt(600, 600, Palette::White);

		while (System::Update())
		{
			// マウスの左ボタンが押されていたら
			if (MouseL.pressed())
			{
				// レンダーターゲットを rt に設定
				ScopedRenderTarget2D target(rt);
				emoji.drawAt(Cursor::Pos());
			}

			rt.draw();
			emoji.drawAt(Cursor::Pos());

			// Clear ボタンが押されたら
			if (SimpleGUI::Button(U"Clear", Vec2(650, 20)))
			{
				// レンダーテクスチャを白でクリア
				rt.clear(Palette::White);
			}
		}
	}
	```

	#### 2. マルチサンプル・レンダーテクスチャ
	通常の `RenderTexture` への描画ではマルチサンプル・アンチエイリアシングが有効にならないので、図形を描画した際にジャギーが生じます。`MSRenderTexture` を使うと、通常のシーンへの描画と同じように、マルチサンプル・アンチエイリアシングを有効にして描画できます。ただし、`MSRenderTexture` に描画された結果を、別の描画で使う際には、`Graphics2D::Flush()` によってその時点までの描画処理をすべて実行（フラッシュ）して `MSRenderTexture` に確実に描画したあとに、`MSRenderTexture::resolve()` を行い、`MSRenderTexture` 内のマルチサンプル・テクスチャを、描画で使用可能な通常のテクスチャに変換しておく必要があります。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v041/2.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF(0.8, 0.9, 1.0));

		// レンダーテクスチャ
		RenderTexture rt(200, 200);

		// マルチサンプル・レンダーテクスチャ
		MSRenderTexture msrt(200, 200);

		while (System::Update())
		{
			rt.clear(ColorF(0.0, 1.0));
			{
				ScopedRenderTarget2D target(rt);

				Rect(Arg::center(100, 100), 80)
					.rotated(Scene::Time() * 30_deg).draw();
			}

			msrt.clear(ColorF(0.0, 1.0));
			{
				{
					ScopedRenderTarget2D target(msrt);

					Rect(Arg::center(100, 100), 80)
						.rotated(Scene::Time() * 30_deg).draw();
				}

				// 2D 描画をフラッシュ
				Graphics2D::Flush();

				// マルチサンプルテクスチャを描画可能なテクスチャに変換
				msrt.resolve();
			}

			rt.draw(100, 0);
		
			msrt.draw(400, 0);
		}
	}
	```

	#### 3. レンダーテクスチャへのシェーダ処理
	テクスチャから別のレンダーテクスチャへの様々な変換処理を関数 1 つで実行できます。レンダーステートの変更も不要です。提供される関数は次のとおりです。

	##### `void Copy(const TextureRegion& from, RenderTexture& to);`
	- from: 入力テクスチャ
	- to: 出力テクスチャ

	`from` のテクスチャの内容を `to` に描画します。`from` と `to` はともに有効なテクスチャで、互いに異なり、領域のサイズが同じでなければなりません。

	##### `void Downsample(const TextureRegion& from, RenderTexture& to);`
	- from: 入力テクスチャ
	- to: 出力テクスチャ

	`from` のテクスチャの内容を縮小して `to` に描画します。`from` と `to` はともに有効なテクスチャで、互いに異なるテクスチャでなければなりません。

	##### `void GaussianBlurH(const TextureRegion& from, RenderTexture& to);`
	- from: 入力テクスチャ
	- to: 出力テクスチャ

	`from` のテクスチャの内容に横方向のガウスブラーをかけて `to` に描画します。`from` と `to` はともに有効なテクスチャで、互いに異なり、領域のサイズが同じでなければなりません。

	##### `void GaussianBlurV(const TextureRegion& from, RenderTexture& to);`
	- from: 入力テクスチャ
	- to: 出力テクスチャ

	`from` のテクスチャの内容に縦方向のガウスブラーをかけて `to` に描画します。`from` と `to` はともに有効なテクスチャで、互いに異なり、領域のサイズが同じでなければなりません。

	##### `void GaussianBlur(const TextureRegion& from, RenderTexture& to, const Vec2& direction);`
	- from: 入力テクスチャ
	- to: 出力テクスチャ
	- direction: ブラーの方向

	`from` のテクスチャの内容に指定した方向のガウスブラーをかけて `to` に描画します。`from` と `to` はともに有効なテクスチャで、互いに異なり、領域のサイズが同じでなければなりません。

	##### `void GaussianBlur(const TextureRegion& from, RenderTexture& internalBuffer, RenderTexture& to);`
	- from: 入力テクスチャ
	- internalBuffer: 中間テクスチャ
	- to: 出力テクスチャ

	`from` のテクスチャの内容をに縦方向と横方向のガウスブラーをかけて `to` に描画します。`from`, `internalBuffer`, `to` はいずれも有効なテクスチャで、隣り合うもの同士は異なり、領域のサイズが同じでなければなりません。  
	`GaussianBlurH(from, internalBuffer); GaussianBlurV(internalBuffer, to);` と等価です。

	##### ダウンサンプリング

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v041/3.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		const Texture texture(U"example/windmill.png");

		// 縦、横が 4 分の 1 サイズのレンダーテクスチャ
		RenderTexture rt(texture.size() / 4);

		// ダウンサンプリング
		Shader::Downsample(texture, rt);

		while (System::Update())
		{
			rt.draw();
		}
	}
	```

	##### ガウスぼかし

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v041/4.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズ
		Window::Resize(1280, 720);

		// bay.jpg は 2560x1440 なのでサイズを小さくしてロード
		const Texture texture(Image(U"example/bay.jpg").scale(1280, 720));

		// ぼかしを適用する領域のサイズ
		constexpr Size blurAreaSize(480, 320);

		// ガウスぼかしの中間で使うレンダーテクスチャを用意
		RenderTexture rtA(blurAreaSize), rtB(blurAreaSize);
		RenderTexture rtA4(blurAreaSize / 4), rtB4(blurAreaSize / 4);
		RenderTexture rtA8(blurAreaSize / 8), rtB8(blurAreaSize / 8);

		while (System::Update())
		{
			const Point cursorPos = Cursor::Pos();

			// 背景画像のうちぼかしを適用する領域
			const Rect blurArea(cursorPos, blurAreaSize);

			// [オリジナル]->[ガウスぼかし]->[1/4サイズ]->[ガウスぼかし]->[1/8サイズ]->[ガウスぼかし]
			Shader::GaussianBlur(texture(blurArea), rtB, rtA);
			Shader::Downsample(rtA, rtA4);
			Shader::GaussianBlur(rtA4, rtB4, rtA4);
			Shader::Downsample(rtA4, rtA8);
			Shader::GaussianBlur(rtA8, rtB8, rtA8);

			// 背景を描画
			texture.draw();

			// ガウスぼかし後のテクスチャを RoundRect に貼り付けて描画
			RoundRect(cursorPos, blurAreaSize, 40)(rtA8.resized(blurAreaSize)).draw();
		}
	}
	```


	#### 4. カスタムピクセルシェーダ
	2D のテクスチャや図形がレンダーターゲットに描かれるとき、どのような色を出力するかは、「ピクセルシェ―ダ」と呼ばれる、ピクセルごとに GPU 上で実行されるプログラムを通して決定されます。そのプログラムをカスタマイズできるようになりました。

	#### 5. 子プロセスの作成
	別のプログラムを「子プロセス」として起動、管理できるようになりました。別のアプリケーションを起動したり、別のプログラムと情報をやり取りする際に使えます。

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
	# if SIV3D_PLATFORM(WINDOWS)

		// 子プロセスで実行するファイルのパス
		const FilePath path = U"C:/Windows/System32/notepad.exe";

	# elif SIV3D_PLATFORM(MACOS)

		// 子プロセスで実行するファイルのパス
		const FilePath path = U"/System/Applications/Calculator.app/Contents/MacOS/Calculator";

	# endif

		// 子プロセスを作成
		ChildProcess child = Process::Spawn(path);

		if (!child)
		{
			throw Error(U"Failed to create a process");
		}

		while (System::Update())
		{
			ClearPrint();

			// プロセスが実行中かを取得
			Print << child.isRunning();

			// プロセスが終了した場合、その終了コード
			Print << child.getExitCode();

			if (child.isRunning())
			{
				if (SimpleGUI::Button(U"Terminate", Vec2(100, 20)))
				{
					// プロセスを強制終了
					child.terminate();
				}
			}
		}
	}
	```

	##### 子プロセスとの標準入出力のパイプライン処理
	子プロセスとのパイプライン処理によって、一方の標準出力を他方の標準入力とすることができます。次のサンプルでは、"Console" は通常の C++ コンソールプロジェクトとしてビルドします。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v041/16.png?raw=true)

	```C++
	# include <iostream>

	int main()
	{
		int a, b;
		std::cin >> a >> b;
		std::cout << (a + b) << std::endl;
	}
	```

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
	# if SIV3D_PLATFORM(WINDOWS)

		// 子プロセスで実行するファイルのパス
		const FilePath path = U"Console.exe";

	# else

		// 子プロセスで実行するファイルのパス
		const FilePath path = U"Console";

	# endif

		// 子プロセスを作成（パイプライン処理）
		ChildProcess child = Process::Spawn(path, Pipe::StdInOut);

		if (!child)
		{
			throw Error(U"Failed to create a process");
		}

		child.ostream() << 10 << std::endl;
		child.ostream() << 20 << std::endl;

		int32 result;
		child.istream() >> result;
		Print << U"result: " << result;

		while (System::Update())
		{

		}
	}
	```


	#### 6. 実験的な 3D 描画対応
	実験的な 3D 機能が実装されました。ただし、2D 描画で 3D をエミュレートする簡易的なものなので、次のような制約があります。

	- 深度バッファが無いので前後判定ができない
	- 遠近クリップが無いのでカメラに近すぎるオブジェクトが正しく表示されない


	##### 3D Triangles

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v041/17.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		constexpr std::array<Vec3, 8> vertices =
		{
			Vec3(-1, 1, -1),
			Vec3(1, 1, -1),
			Vec3(-1, -1, -1),
			Vec3(1, -1, -1),
			Vec3(1, 1, 1),
			Vec3(-1, 1, 1),
			Vec3(1, -1, 1),
			Vec3(-1, -1, 1),
		};

		constexpr std::array<uint32, 36> indices =
		{
			0, 1, 2, 2, 1, 3,
			5, 4, 0, 0, 4, 1,
			1, 4, 3, 3, 4, 6,
			5, 0, 7, 7, 0, 2,
			4, 5, 6, 6, 5, 7,
			2, 3, 7, 7, 3, 6,
		};

		constexpr double fov = 45_deg;
		constexpr Vec3 focusPosition(0, 0, 0);
		Vec3 eyePosition(0, 4, 0);
		experimental::BasicCamera3D camera(Scene::Size(), fov, eyePosition, focusPosition);

		while (System::Update())
		{
			eyePosition = Cylindrical(8, Scene::Time() * 30_deg, Math::Sin(Scene::Time()) * 4);
			camera.setView(eyePosition, focusPosition);
			const Mat4x4 mat = camera.getMat4x4();

			{
				ScopedRenderStates2D culling(RasterizerState::SolidCullBack);

				for (auto i : step(12))
				{
					const Vec3 p0(vertices[indices[i * 3 + 0]]);
					const Vec3 p1(vertices[indices[i * 3 + 1]]);
					const Vec3 p2(vertices[indices[i * 3 + 2]]);

					experimental::Triangle3D(p0, p1, p2).draw(mat, HSV(i * 30));
				}
			}
		}
	}
	```


	##### 3D AABB

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v041/18.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		constexpr double fov = 45_deg;
		constexpr Vec3 focusPosition(0, 0, 0);
		Vec3 eyePosition(0, 10, 0);
		experimental::BasicCamera3D camera(Scene::Size(), fov, eyePosition, focusPosition);

		while (System::Update())
		{
			eyePosition = Cylindrical(20, Scene::Time() * 30_deg, 8 + Periodic::Sine0_1(4s) * 8);
			camera.setView(eyePosition, focusPosition);
			const Mat4x4 mat = camera.getMat4x4();

			{
				ScopedRenderStates2D culling(RasterizerState::SolidCullBack);

				for (auto i : Range(-10, 10))
				{
					experimental::Line3D(Vec3(-10, 0, i), Vec3(10, 0, i)).draw(mat, ColorF(0.5));
					experimental::Line3D(Vec3(i, 0, -10), Vec3(i, 0, 10)).draw(mat, ColorF(0.5));
				}

				experimental::AABB(Vec3(0, 1, 0), Vec3(2, 2, 2)).draw(mat, Palette::White);
				experimental::AABB(Vec3(-8, 1, 8), Vec3(2, 2, 2)).draw(mat, HSV(0));
				experimental::AABB(Vec3(8, 1, 8), Vec3(2, 2, 2)).draw(mat, HSV(90));
				experimental::AABB(Vec3(8, 1, -8), Vec3(2, 2, 2)).draw(mat, HSV(270));
				experimental::AABB(Vec3(-8, 1, -8), Vec3(2, 2, 2)).draw(mat, HSV(180));
			}
		}
	}
	```


	##### 3D Terrain
	マウスクリックで、左上の高さマップに山を描きます。

	![](https://github.com/Siv3D/siv3d.docs.images/blob/master/news/v041/19.png?raw=true)

	```C++
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF(0.05, 0.3, 0.7));

		RenderTexture rt(100, 100, ColorF(0.0), TextureFormat::R32_Float);
		Grid<float> heightMap;
		Grid<Float3> positions;

		constexpr double fov = 45_deg;
		constexpr Vec3 focusPosition(50, 0, -50);
		Vec3 eyePosition(0, 100, 0);
		experimental::BasicCamera3D camera(Scene::Size(), fov, eyePosition, focusPosition);

		while (System::Update())
		{
			eyePosition = Cylindrical(Arg::r = 80, Arg::phi = Scene::Time() * 30_deg, Arg::y = 50) + Vec3(50, 0, -50);
			camera.setView(eyePosition, focusPosition);
			const Mat4x4 mat = camera.getMat4x4();

			rt.read(heightMap);
			{
				positions.resize(heightMap.size());

				for (auto p : step(heightMap.size()))
				{
					positions[p] = Float3(p.x, heightMap[p], -p.y);
				}
			}

			{
				ScopedRenderTarget2D target(rt);
				ScopedRenderStates2D blend(BlendState::Additive);

				if (MouseL.pressed())
				{
					Circle(Cursor::Pos(), 8).draw(ColorF(Scene::DeltaTime() * 24.0));
				}
			}

			if (positions)
			{
				ScopedRenderStates2D culling(RasterizerState::SolidCullBack);

				for (auto x : step(positions.width() - 1))
				{
					for (auto y : step(positions.height()))
					{
						const Float3 begin = positions[{x, y}];
						const Float3 end = positions[{x + 1, y}];
						const ColorF color = HSV(120 - (begin.y + end.y) * 3, 0.75, 0.7);
						experimental::Line3D(begin, end).draw(mat, color);
					}
				}

				for (auto x : step(positions.width()))
				{
					for (auto y : step(positions.height() - 1))
					{
						const Float3 begin = positions[{x, y}];
						const Float3 end = positions[{x, y + 1}];
						const ColorF color = HSV(120 - (begin.y + end.y) * 3, 0.75, 0.7);
						experimental::Line3D(begin, end).draw(mat, color);
					}
				}
			}

			rt.draw(ColorF(0.1));
		}
	}
	```

## v0.0～v0.3 Generation

## Old Siv3D
- [Old Siv3D Change Log :material-open-in-new:](https://github.com/Siv3D/Reference-JP/wiki/%E6%9B%B4%E6%96%B0%E5%B1%A5%E6%AD%B4){:target="_blank"}

