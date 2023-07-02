# Siv3D の機能

Siv3D が提供する主要な機能のリストです。

## グラフィックス
- 発展的な 2D グラフィックス
- 基本的な 3D グラフィックス (Wavefront OBJ, いくつかの基本形状)
- カスタム頂点・ピクセルシェーダ (HLSL, GLSL)
- テキストレンダリング (Bitmap, SDF, MSDF)
- 画像形式 (PNG, JPEG, BMP, SVG, GIF, Animated GIF, TGA, PPM, WebP, TIFF)
- Unicode 15.0 絵文字と 7,000 種類以上のアイコン
- 画像処理
- ビデオレンダリング

## オーディオ
- 音声形式 (WAVE, MP3, AAC, OggVorbis, Opus, MIDI, WMA, FLAC, AIFF)
- 音量やパン，スピード，ピッチの調整
- ストリーミング再生 (WAVE, MP3, OggVorbis)
- 再生中のバッファへの波形書き込み
- フェードイン，フェードアウト
- ループ
- ミキシングバス
- フィルタ処理 (ローパスフィルタ，ハイパスフィルタ, エコー, リバーブ)
- FFT
- サウンドフォントレンダリング
- テキスト読み上げ

## 入力デバイス
- マウス
- キーボード
- ゲームパッド
- ウェブカメラ
- マイク
- Joy-Con / Pro Controller
- XInput ゲームパッド
- ペンタブレット
- Leap Motion

## ウィンドウ
- フルスクリーンモード
- 高 DPI サポート
- ウィンドウのスタイル（サイズ変更、枠無し）
- ファイルダイアログ
- ドラッグ & ドロップ
- メッセージボックス
- トースト通知

## ネットワークと通信
- HTTP クライアント
- マルチプレイ (Photon SDK)
- TCP 通信
- シリアル通信
- プロセス間通信 (pipe)
- OSC (Open Sound Control) 通信

## 数学
- ベクトルと行列クラス (`Point`, `Float2`, `Vec2`, `Float3`, `Vec3`, `Float4`, `Vec4`, `Mat3x2`, `Mat3x3`, `Mat4x4`, `SIMD_Float4`, `Quaternion`)
- 2D 形状クラス (`Line`, `Circle`, `Ellipse`, `Rect`, `RectF`, `Triangle`, `Quad`, `RoundRect`, `Polygon`, `MultiPolygon`, `LineString`, `Spline2D`, `Bezier2`, `Bezier3`)
- 3D 形状クラス (`Plane`, `InfinitePlane`, `Sphere`, `Box`, `OrientedBox`, `Ray`, `Line3D`, `Triangle3D`, `ViewFrustum`, `Disc`, `Cylinder`, `Cone`)
- 色クラス (`Color`, `ColorF`, `HSV`)
- 曲座標系クラス
- 2D / 3D 交差判定・交点計算
- 2D / 3D 幾何計算
- 長方形詰込み
- 平面細分割
- リニア色空間とガンマ色空間
- 疑似乱数生成器
- 補間，イージング，スムージング
- パーリンノイズ
- 数式パーサ
- ナビメッシュ
- 拡張数値型 (`HalfFloat`, `int128`, `uint128`, `BigInt`, `BigFloat`)

## 文字列処理
- 文字列クラス (`String`, `StringView`)
- Unicode 変換 (UTF-8 / UTF-16 / UTF-32)
- 正規表現
- `{fmt}` スタイルの文字列フォーマット
- テキストファイル読み書き
- CSV / INI / JSON / XML / TOML パーサ
- CSV / INI / JSON 出力
- JSON バリデーション

## その他
- 基本的なGUI (ボタン、スライダー、ラジオボタン、チェックボックス、テキストボックス、テキストエリア、リストボックス、カラーピッカー、メニューバー、テーブル)
- 2D 物理エンジンの統合 (Box2D)
- 配列クラス (`Array`, `Grid`)
- Kd-tree
- Disjoint Set Union
- 非同期ファイルロード
- データ圧縮 (zlib, Zstandard)
- シーン遷移
- ファイルシステム
- ディレクトリ監視
- QR コード
- GeoJSON
- 日付と時刻
- 時間計測
- ロギング
- シリアライズ
- UUID
- 子プロセス管理
- クリップボード
- 電源管理
- スクリプティング (AngelScript)
- OpenAI API (Chat, Image, Embedding)

<small>（\*一部の機能は特定のプラットフォームのみでのサポートです）</small>