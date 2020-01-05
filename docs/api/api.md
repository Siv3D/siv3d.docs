description: OpenSiv3D の API 一覧

## シーン関連の定数

### `enum class ScaleMode`
ウィンドウを手動でリサイズしたときのシーンのサイズの扱いです。ウィンドウをリサイズする関数で `WindowResizeOption::UseDefaultScaleMode` が指定されたときにも参照されます。 

#### `ScaleMode::ResizeFill`
ウィンドウのクライアント領域のサイズに合わせてシーンをリサイズします。

#### `ScaleMode::AspectFit`
シーンのサイズはそのままで、アスペクト比を維持して拡大縮小してクライアント領域にフィットさせ描画します。

## シーン名前空間 (namespace Scene)

### 定数

#### `constexpr Size Scene::DefaultSceneSize = Window::DefaultClientSize;`
シーンの幅と高さ（ピクセル）のデフォルト値です。

#### `constexpr ScaleMode Scene::DefaultScaleMode = ScaleMode::AspectFit;`
ウィンドウを手動でリサイズしたときのシーンのサイズの扱いのデフォルト値です。

#### `constexpr TextureFilter Scene::DefaultFilter = TextureFilter::Linear;`
ウィンドウのクライアント領域がシーンのサイズと異なる場合、シーンを拡大縮小描画するために使うテクスチャフィルタのデフォルト値です。

#### `constexpr ColorF Scene::DefaultBackgroundColor = Palette::DefaultBackground;`
シーンの背景色のデフォルト色です。

#### `constexpr ColorF Scene::DefaultLetterBoxColor = Palette::DefaultLetterbox;`
ウィンドウのクライアント領域がシーンよりも大きい場合に余白となるスペース「レターボックス」のデフォルト色です。

#### `constexpr double Scene::DefaultMaxDeltaTime = 0.1;`
`Scene::DeltaTime()` が返す最大の時間（秒）のデフォルト値です。

### 関数

#### `void Scene::Resize(const s3d::Size& size);`
#### `void Scene::Resize(int32 width, int32 height);`
- size: 新しいシーンの幅と高さ
- width, height: 新しいシーンの幅と高さ

シーンの幅と高さを変更します。デフォルト値は `Scene::DefaultSceneSize` です。両方またはどちらかの値を 0 以下にしたり 8192 より大きくすることはできません。

#### `Size Scene::Size();`
- 戻り値: シーンの幅と高さ（ピクセル）

シーンの幅と高さ（ピクセル）の現在の設定を返します。

#### `int32 Scene::Width();`
- 戻り値: シーンの幅（ピクセル）

シーンの幅（ピクセル）の現在の設定を返します。

#### `int32 Scene::Height();`
- 戻り値: シーンの高さ（ピクセル）

シーンの高さ（ピクセル）の現在の設定を返します。

#### `Point Scene::Center();`
- 戻り値: シーンの中心座標

シーンの中心の座標を `Point` 型で返します。シーンのサイズが (333, 444) の場合、(116, 222) を返します。

#### `Vec2 Scene::CenterF();`
- 戻り値: シーンの中心座標

シーンの中心の座標を `Vec2` 型で返します。シーンのサイズが (333, 444) の場合、(116.5, 222) を返します。

#### `Rect Scene::Rect();`
- 戻り値: シーンの `Rect`

左上が (0, 0) で現在のシーンと同じ大きさの `Rect` を返します。

#### `void Scene::SetScaleMode(ScaleMode scaleMode);`
- scaleMode: ウィンドウを手動でリサイズしたときのシーンのサイズの扱い

ウィンドウを手動でリサイズしたときのシーンのサイズの扱いを設定します。デフォルトは `ScaleMode::AspectFit` です。

#### `ScaleMode Scene::GetScaleMode();`
- 戻り値: ウィンドウを手動でリサイズしたときのシーンのサイズの扱い

ウィンドウを手動でリサイズしたときのシーンのサイズの扱いの現在の設定を返します。

#### `void Scene::SetTextureFilter(TextureFilter textureFilter);`
- textureFilter: シーンを拡大縮小描画する際に使うテクスチャフィルタ

ウィンドウのクライアント領域がシーンのサイズと異なる場合にシーンを拡大縮小描画するために使うテクスチャフィルタを設定します。デフォルトは `Scene::DefaultFilter` です。ドット絵感を維持したい場合などには `TextureFilter::Nearest` を設定します。

#### `TextureFilter Scene::GetTextureFilter();`
- 戻り値: シーンを拡大縮小描画する際に使うテクスチャフィルタ

ウィンドウのクライアント領域がシーンのサイズと異なる場合にシーンを拡大縮小描画するために使うテクスチャフィルタの現在の設定を返します。`Scene::SetTextureFilter()` で変更できます。

#### `void Scene::SetBackground(const ColorF& color);`
- color: シーンの背景色

シーンの背景色を設定します。色のアルファ成分は無視されます。デフォルトは `Scene::DefaultBackgroundColor` です。

#### `void Scene::SetLetterbox(const ColorF& color);`
- color: レターボックスの色

ウィンドウのクライアント領域がシーンよりも大きい場合に余白となるスペース「レターボックス」の色を設定します。色のアルファ成分は無視されます。デフォルトは `Scene::DefaultLetterBoxColor` です。

#### `void Scene::SetMaxDeltaTime(double timeSec);`
- timeSec: 最大の時間（秒）

`Scene::DeltaTime()` が返す最大の時間（秒）を設定します。デフォルトは `Scene::DefaultMaxDeltaTime` です。`Scene::DeltaTime()` が大きな値を返して物理演算などの経過時間処理に負荷がかかるのを防ぐ役割があります。

#### `double Scene::GetMaxDeltaTime();`
- 戻り値: `Scene::DeltaTime()` が返す最大の時間（秒）

`Scene::DeltaTime()` が返す最大の時間（秒）の現在の設定を返します。`Scene::SetMaxDeltaTime()` で変更できます。

#### `double Scene::DeltaTime();`
- 戻り値: 前回のフレームからの経過時間（秒）と `Scene::SetMaxDeltaTime()` の小さいほうの値

前回の `System::Update()` からの経過時間（秒）を返します。この値をもとにアニメーションやイベントの処理などを行うことで、フレームレートが上下しても対応できます。`System::Update()` によって値が更新されます。

#### `double Scene::Time();`
- 戻り値: アプリケーションが起動してからの経過時間（秒）

アプリケーションが起動してからの経過時間（秒）を返します。`System::Update()` によって値が更新されます。

#### `int32 Scene::FrameCount();`
- 戻り値: `System::Update()` が呼ばれた回数

`System::Update()` が呼ばれた回数（= フレームカウント）を返します。ユーザの環境によってフレームレートが変わるため、この値をアニメーション等の制御に使ってはいけません。

#### `Vec2 Scene::ClientToScene(const Vec2& pos);`
- pos: ウィンドウのクライアント領域上の座標
- 戻り値: シーン上の座標

ウィンドウのクライアント領域上の座標をシーン上の座標に変換します。シーンの範囲外の座標になることもあります。


## システム名前空間 (namespace System)

### 関数

#### `bool System::Update();`
- 戻り値: プログラムの続行の可否

描画や入力情報など、フレームを更新します。フレームレートを調整する働きもあります。アプリケーション終了トリガーが発生するか、内部で回復不能なエラーが発生した場合に `false` を返します。この関数が `false` を返したらプログラムを終了させるべきです。

#### `void System::Exit();`

プログラムを終了するために、この直後の `System::Update()` が `false` を返すように設定します。この関数自体が終了処理を行うわけではないので、この関数の呼び出しは必須ではありません。

#### `void System::SetTerminationTriggers(uint32 userActionFlags);`
- userActionFlags: アプリケーション終了トリガーに設定するユーザアクションのフラグ

アプリケーション終了トリガーに設定するユーザアクションを設定します。フラグには `UserAction` の値の組み合わせを使います。

#### `uint32 System::GetTerminationTriggers();`
- 戻り値: アプリケーション終了トリガーに設定したユーザアクションのフラグ

アプリケーション終了トリガーに設定したユーザアクションのフラグの現在の設定を返します。フラグには `UserAction` の値の組み合わせが使われています。

#### `uint32 System::GetUserActions();`
- 戻り値: 前のフレームで発生したユーザアクションのフラグ

前回のフレームで発生したユーザアクションのフラグを返します。フラグには `UserAction` の値の組み合わせが使われています。

#### `void System::Sleep(int32 milliseconds);`
#### `void System::Sleep(const Duration& duration);`
- milliseconds: スリープする時間（ミリ秒）
- duration: スリープする時間

現在のスレッドの実行を指定した時間だけ停止します。

#### `bool System::LaunchBrowser(const FilePath& url);`
- url: オープンする URL
- 戻り値: オープンの成功の可否

指定した URL をデフォルトの Web ブラウザでオープンします。

#### `Array<Monitor> System::EnumerateActiveMonitors()`
- 戻り値: 使用可能なモニターの一覧

使用可能なモニターの一覧を返します。

#### `size_t System::GetCurrentMonitorIndex()`
- 戻り値: ウィンドウが配置されているモニターのインデックス

ウィンドウが配置されているモニターのインデックスを取得します。`System::EnumerateActiveMonitors()` の戻り値に対して使えます。

#### `Array<GamepadInfo> System::EnumerateGamepads();`
- 戻り値: 使用可能なゲームパッドの一覧

使用可能なゲームパッドの一覧を返します。

#### `Array<WebcamInfo> System::EnumerateWebcams();`
- 戻り値: 使用可能な Web カメラの一覧

使用可能な Web カメラの一覧を返します。


## ウィンドウ名前空間 (namespace Window)

### 定数

#### `constexpr Size Window::DefaultClientSize = Size(800, 600);`
ウィンドウのクライアント領域の幅と高さ（ピクセル）のデフォルト値です。

### 関数

#### `void Window::SetTitle(const String& title);`
#### `template <class... Args> void Window::SetTitle(const Args&... args);`
- title: 新しいタイトル
- args: 新しいタイトル

ウィンドウのタイトルを変更します。改行は無視され、すでに同じタイトルの場合は何もしません。ウィンドウタイトルの変更はシステムに負荷がかかる場合があります。

#### `const String& Window::GetTitle();`
- 戻り値: 現在のウィンドウタイトル

現在のウィンドウタイトルを返します。

#### `WindowState Window::GetState();`
- 戻り値: 現在のウィンドウステート

現在のウィンドウステートを返します。

#### `void Window::SetStyle(WindowStyle style);`
- style: ウィンドウスタイルを変更します。すでに同じタイトルの場合は何もしません。

ウィンドウスタイルを変更します。

#### `WindowStyle Window::GetStyle();`
- 戻り値: 現在のウィンドウスタイル

現在のウィンドウスタイルを返します。

#### `Size Window::ClientSize();`
- 戻り値: ウィンドウのクライアント領域の幅と高さ（ピクセル）

現在のウィンドウのクライアント領域の幅と高さ（ピクセル）を返します。

#### `Point Window::ClientCenter();`
- 戻り値: ウィンドウのクライアント領域の中心座標

現在のウィンドウのクライアント領域における中心座標（ピクセル）を返します。

#### `int32 Window::ClientWidth();`
- 戻り値: ウィンドウのクライアント領域の幅（ピクセル）

現在のウィンドウのクライアント領域の幅（ピクセル）を返します。

#### `int32 Window::ClientHeight();`
- 戻り値: ウィンドウのクライアント領域の高さ（ピクセル）

現在のウィンドウのクライアント領域の高さ（ピクセル）を返します。

#### `void Window::SetPos(const Point& pos);`
#### `void Window::SetPos(int32 x, int32 y);`
- pos: ウィンドウの左上の位置（スクリーン座標）
- x, y: ウィンドウの左上の位置（スクリーン座標）

ウィンドウを指定した座標に移動させます。

#### `void Window::Centering();`
ウィンドウをスクリーンの中心に移動させます。

#### `bool Window::Resize(const Size& size, WindowResizeOption option = WindowResizeOption::ResizeSceneSize, bool centering = true);`
#### `bool Window::Resize(int32 width, int32 height, WindowResizeOption option = WindowResizeOption::ResizeSceneSize, bool centering = true);`
- size: ウィンドウのクライアント領域の新しいサイズ（ピクセル）
- width, height: ウィンドウのクライアント領域の新しいサイズ（ピクセル）
- option: シーンの解像度を追従させるかを決めるオプション
- centering: サイズ変更後にウィンドウをスクリーンの中心に移動させるかのフラグ
- 戻り値: サイズの変更に成功した場合 `true`, それ以外の場合 `false`

ウィンドウのクライアント領域のサイズを変更します。`WindowResizeOption::ResizeSceneSize` が指定されている場合、シーンのサイズも合わせて変更されます。

#### `void Window::Maximize();`
ウィンドウを最大化します。

#### `void Window::Restore();`
最大・最小化されたウィンドウを元のサイズに戻します。

#### `void Window::Minimize();`
ウィンドウを最小化します。

#### `bool Window::SetFullscreen(bool fullscreen, const Optional<Size>& fullscreenResolution = unspecified, WindowResizeOption option = WindowResizeOption::ResizeSceneSize);`
- fullscreen: フルスクリーンモードにする場合 `false`, ウィンドウモードにする場合 `false`
- fullscreenResolution: フルスクリーンの解像度
- option: シーンの解像度を追従させるかを決めるオプション
- 戻り値: フルスクリーンモードの変更に成功した場合 `true`, それ以外の場合 `false`

フルスクリーンモードの設定をします。`fullscreenResolution` には `unspecified` か `Graphics::GetFullscreenResolutions()` に含まれる値を使います。フルスクリーンモードにする際、`fullscreenResolution` に `unspecified` を指定すると、ディスプレイの解像度（スケーリング適用後）のサイズでフルスクリーンモードに入ります。`unspecified` は切り替えが早く堅牢です。

## マウスカーソル関連の定数

### `enum class CursorStyle`
マウスカーソルの形状を表します。

#### `CursorStyle::Arrow`
通常の矢印カーソルです。

#### `CursorStyle::IBeam`
テキスト入力時に使う I の形をしたカーソルです。

#### `CursorStyle::Cross`
十字型のカーソルです。

#### `CursorStyle::Hand`
人差し指を伸ばした手のアイコンのカーソルです。

#### `CursorStyle::NotAllowed`
禁止マークのアイコンのカーソルです。

#### `CursorStyle::ResizeUpDown`
上下へのリサイズ操作を表現するカーソルです。

#### `CursorStyle::ResizeLeftRight`
左右へのリサイズ操作を表現するカーソルです。

#### `CursorStyle::Hidden`
マウスカーソルを非表示にします。

#### `CursorStyle::Default = Arrow` 
デフォルトのマウスカーソルです。デフォルト値は `CursorStyle::Arrow` です。

## マウスカーソル名前空間 (namespace Cursor)

### 関数

#### `Point Cursor::Pos();`
- 戻り値: 現在のフレームにおける、マウスカーソルの座標 (ピクセル)

現在のフレームにおける、マウスカーソルのクライアント座標（ピクセル）を返します。

#### `Point Cursor::PreviousPos();`
- 戻り値: 直前のフレームおける、マウスカーソルの座標 (ピクセル)

直前のフレームにおける、マウスカーソルのクライアント座標（ピクセル）を返します。

#### `Point Cursor::Delta();`
- 戻り値: 直前のフレームから現在のフレームまでのマウスカーソルの移動量 (ピクセル)

直前のフレームから現在のフレームまでのマウスカーソルの移動量（ピクセル）を返します。`Cursor::Pos() - Cursor::PreviousPos()` と同値です。

#### `Vec2 Cursor::PosF();`
- 戻り値: 現在のフレームおける、マウスカーソルの座標 (ピクセル)

現在のフレームにおける、マウスカーソルのクライアント座標（ピクセル）を返します。マウスカーソル座標にカスタムの変換行列が適用されている場合、座標が小数値を含む場合があります。

#### `Vec2 Cursor::PreviousPosF();`
- 戻り値: 直前のフレームおける、マウスカーソルの座標 (ピクセル)

直前のフレームにおける、マウスカーソルのクライアント座標（ピクセル）を返します。マウスカーソル座標にカスタムの変換行列が適用されている場合、座標が小数値を含む場合があります。

#### `Vec2 Cursor::DeltaF();`
- 戻り値: 直前のフレームから現在のフレームまでのマウスカーソルの移動量 (ピクセル)

直前のフレームから現在のフレームまでのマウスカーソルの移動量（ピクセル）を返します。`Cursor::PosF() - Cursor::PreviousPosF()` と同値です。

#### `Point Cursor::PosRaw();`
- 戻り値: 現在のフレームおける、マウスカーソルの座標 (ピクセル)

現在のフレームにおける、カスタムの変換行列を適用しない状態でのマウスカーソルのクライアント座標（ピクセル）を返します。

#### `Point Cursor::PreviousPosRaw();`
- 戻り値: 直前のフレームおける、マウスカーソルの座標 (ピクセル)

直前のフレームにおける、カスタムの変換行列を適用しない状態でのマウスカーソルのクライアント座標（ピクセル）を返します。

#### `Point Cursor::DeltaRaw();`
- 戻り値: 直前のフレームから現在のフレームまでのマウスカーソルの移動量 (ピクセル)

直前のフレームから現在のフレームまでの、カスタムの変換行列を適用しない状態でのマウスカーソルの移動量（ピクセル）を返します。`Cursor::PosRaw() - Cursor::PreviousPosRaw()` と同値です。

#### `Point Cursor::ScreenPos();`
- 戻り値: 現在のフレームおける、マウスカーソルのスクリーン座標 (ピクセル)

現在のフレームにおける、マウスカーソルのスクリーン座標（ピクセル）を返します。

#### `Point Cursor::PreviousScreenPos();`
- 戻り値: 直前のフレームおける、マウスカーソルのスクリーン座標 (ピクセル)

直前のフレームにおける、マウスカーソルのスクリーン座標（ピクセル）を返します。

#### `Point Cursor::ScreenDelta();`
- 戻り値: 戻り値: 直前のフレームから現在のフレームまでのスクリーン上でのマウスカーソルの移動量 (ピクセル)

直前のフレームから現在のフレームまでの、スクリーン上でのマウスカーソルの移動量（ピクセル）を返します。`Cursor::ScreenPos() - Cursor::PreviousScreenPos()` と同値です。

#### `Array<std::pair<Point, uint64>> Cursor::GetBuffer();`
- 戻り値: 直近 1 秒間のマウスカーソル座標を古い順に並べた配列

現在のフレームから過去 1 秒間のマウスカーソルの座標とタイムスタンプの配列を返します。Windows 版に限り、フレームレート以上の情報を取得できます。

#### `void Cursor::SetPos(int32 x, int32 y);`
#### `void Cursor::SetPos(const Point& pos);`
- x: 移動先のクライアント X 座標 (ピクセル)
- y: 移動先のクライアント Y 座標 (ピクセル)
- pos: 移動先のクライアント座標 (ピクセル)

マウスカーソルを指定したクライアント座標に移動させます。

#### `bool Cursor::OnClientRect();`
- 戻り値: マウスカーソルがクライアント画面上にある場合 `true`, それ以外の場合 `false`

現在のフレームで、マウスカーソルがクライアント画面上にあるかどうかを返します。

#### `const Mat3x2& Cursor::GetLocalTransform();`
- 戻り値: マウスカーソル座標を変換するために `Transformer2D` によって設定されている行列

マウスカーソル座標に適用されている、ローカルな変換行列を返します。

#### `const Mat3x2& Cursor::GetCameraTransform();`
- 戻り値: マウスカーソル座標を変換するために `BasicCamera2D` によって設定されている行列

マウスカーソル座標に適用されている、2D カメラによる変換行列を返します。

#### `void Cursor::ClipToWindow(bool clip);`
- clip: クリッピングを有向にする場合 `true`, それ以外の場合 `false`

マウスカーソルをクライアント画面上にクリッピングするかどうかを設定します。デフォルトでは `false` です。マウスカーソルをクリッピングしている間は、ウィンドウの閉じるボタンをクリックできないので注意してください。

#### `void Cursor::RequestStyle(CursorStyle style);`
- style: 変更後のカーソルスタイル

現在のフレームについて、カーソルスタイルの変更をリクエストします。

#### `void Cursor::SetDefaultStyle(CursorStyle style);`
- style: デフォルトに設定するカーソルスタイル

`Cursor::RequestStyle()` を呼ばなかった場合に適用される、デフォルトのカーソルスタイルを設定します。デフォルトでは `CursorStyle::Default` です。

#### `CursorStyle Cursor::GetRequestedStyle();`
- 戻り値: 現在のフレームについてリクエストされたカーソルスタイル

現在のフレームについて、リクエストされているカーソルスタイルを返します。リクエストが無い場合はデフォルトのカーソルスタイルを返します。

#### `CursorStyle Cursor::GetDefaultStyle();`
- 戻り値: デフォルトのカーソルスタイル

デフォルトのカーソルスタイルを返します。


## 時間名前空間 (namespace Time)

### 関数

#### `uint64 Time::GetSec();`
- 戻り値: コンピューターが起動してからの経過時間（秒）

コンピューターが起動してからの経過時間を秒で返します。

#### `uint64 Time::GetMillisec();`
- 戻り値: コンピューターが起動してからの経過時間（ミリ秒）

コンピューターが起動してからの経過時間をミリ秒で返します。

#### `uint64 Time::GetMicrosec();`
- 戻り値: コンピューターが起動してからの経過時間（マイクロ秒）

コンピューターが起動してからの経過時間をマイクロ秒で返します。

#### `uint64 Time::GetNanosec();`
- 戻り値: コンピューターが起動してからの経過時間（ナノ秒）

コンピューターが起動してからの経過時間をナノ秒で返します。

#### `uint64 Time::GetSecSinceEpoch();`
- 戻り値: 1970 年 1 月 1 日午前 0 時からの経過秒数

協定世界時 (UTC) における、1970 年 1 月 1 日午前 0 時からの経過時間を秒で返します。

#### `uint64 Time::GetMillisecSinceEpoch();`
- 戻り値: 1970 年 1 月 1 日午前 0 時からの経過時間（ミリ秒）

協定世界時 (UTC) における、1970 年 1 月 1 日午前 0 時からの経過時間をミリ秒で返します。

#### `uint64 Time::GetMicrosecSinceEpoch();`
- 戻り値: 1970 年 1 月 1 日午前 0 時からの経過時間（マイクロ秒）

協定世界時 (UTC) における、1970 年 1 月 1 日午前 0 時からの経過時間をマイクロ秒で返します。

#### `int32 Time::UTCOffsetMinutes();`
- 戻り値: 現在の協定世界時 (UTC) との時差（分）

現在の協定世界時 (UTC) との時差を分で返します。

## 文字に関する機能

### 関数

#### `bool IsASCII(char32 ch);`
- ch: 文字
- 戻り値: ASCII 文字である場合 `true`, それ以外の場合 `false`

文字 `ch` が ASCII 文字であるかを返します。

#### `bool IsDigit(char32 ch);`
- ch: 文字
- 戻り値: 10 進数の数字である場合 `true`, それ以外の場合 `false`

文字 `ch` が 10 進数の数字 (0～9) であるかを返します。

#### `bool IsLower(char32 ch);`
- ch: 文字
- 戻り値: アルファベットの小文字である場合 `true`, それ以外の場合 `false`

文字 `ch` がアルファベットの小文字 (a～z) であるかを返します。

#### `bool IsUpper(char32 ch);`
- ch: 文字
- 戻り値: アルファベットの大文字である場合 `true`, それ以外の場合 `false`

文字 `ch` がアルファベットの大文字 (A～Z) であるかを返します。

#### `char32 ToLower(char32 ch);`
- ch: 文字
- 戻り値: アルファベット ch の小文字

文字 `ch` がアルファベットの大文字 (A～Z) の場合、小文字に変換して返します。それ以外の場合は `ch` を返します。

#### `char32 ToUpper(char32 ch);`
- ch: 文字
- 戻り値: アルファベット ch の大文字

文字 `ch` がアルファベットの小文字 (a～z) の場合、大文字に変換して返します。それ以外の場合は `ch` を返します。

#### `bool IsAlpha(char32 ch);`
- ch: 文字
- 戻り値: アルファベットである場合 `true`, それ以外の場合 `false`

文字 `ch` がアルファベット (A～Z, a～Z) であるかを返します。

#### `bool IsAlnum(char32 ch);`
- ch: 文字
- 戻り値: アルファベットもしくは数字である場合 `true`, それ以外の場合 `false`

文字 `ch` がアルファベット (A～Z, a～Z) もしくは数字 (0～9) であるかを返します。

#### `bool IsXdigit(char32 ch);`
- ch: 文字
- 戻り値: 16 進数に使われる文字である場合 `true`, それ以外の場合 `false`

文字 `ch` が 16 進数に使われる文字 (0～9, A～F, a～f) であるかを返します。

#### `bool IsControl(char32 ch);`
- ch: 文字
- 戻り値: 制御文字である場合 `true`, それ以外の場合 `false`

文字 `ch` が制御文字 (0x00～0x1F, 0x7F～0x9F) であるかを返します。

#### `bool IsBlank(char32 ch);`
- ch: 文字
- 戻り値: 空白文字である場合 `true`, それ以外の場合 `false`

文字 `ch` が空白文字 (半角スペース、タブ、全角スペース) であるかを返します。

#### `bool IsSpace(char32 ch);`
- ch: 文字
- 戻り値: 空白類文字である場合 `true`, それ以外の場合 `false`

文字 `ch` が空白類文字 (半角スペース、タブ、全角スペース, `'\n'`, `'\v'`, `'\f'`, `'\r'`) であるかを返します。

#### `bool IsPrint(char32 ch);`
- ch: 文字
- 戻り値: 印字可能文字である場合 `true`, それ以外の場合 `false`

文字 `ch` が印字可能文字であるかを返します。

#### `bool CaseInsensitiveEquals(char32 a, char32 b);`
- a: 文字
- b: 文字
- 戻り値: 大文字と小文字を区別せずに 2 つの文字を比較して、等しい場合 `true`, それ以外の場合 `false`

文字 `a`, `b` が等しいかを返します。大文字小文字の違いは無視され、`'s'` と `'S'` は等しいとみなされます。

#### `int32 CaseInsensitiveCompare(char32 a, char32 b);`
- a: 文字
- b: 文字
- 戻り値: 大文字と小文字を区別せずに 2 つの文字を比較し、`a` が `b` より小さい場合 `-1`, 等しい場合 `0`, 大きい場合 `1`

大文字小文字の違いを無視して文字 `a`, `b` を比較した結果を返します。

## 2D グラフィックス名前空間 (namespace Graphics2D)

### 関数

#### `ColorF Graphics2D::GetColorMul();`
- 戻り値: 現在の乗算カラー

現在 2D 描画に適用されている乗算カラーを返します。デフォルトでは `ColorF(1, 1, 1, 1)` です。

#### `ColorF Graphics2D::GetColorAdd();`
- 戻り値: 現在の加算カラー

現在 2D 描画に適用されている加算カラーを返します。デフォルトでは `ColorF(0, 0, 0, 0)` です。

#### `BlendState Graphics2D::GetBlendState();`
- 戻り値: 現在のブレンドステート

現在 2D 描画に適用されているブレンドステートを返します。デフォルトでは `BlendState::Default` です。

#### `RasterizerState Graphics2D::GetRasterizerState();`
- 戻り値: 現在のラスタライザーステート

現在 2D 描画に適用されているラスタライザーステートを返します。デフォルトでは `RasterizerState::Default2D` です。

#### `void Graphics2D::SetSamplerState(uint32 slot, const SamplerState& samplerState);`
- slot: テクスチャスロットのインデックス (0～ (`SamplerState::MaxSamplerCount - 1`))
- samplerState: 設定するサンプラーステート

2D 描画に適用するサンプラーステートを、テクスチャスロットを指定して設定します。0 番に指定する場合は `ScopedRenderStates2D` が使えるので、それ以外のスロットの設定を変更する場合にこの関数を使います。テクスチャスロットのインデックスの最大値は `(SamplerState::MaxSamplerCount - 1)` です。

#### `SamplerState Graphics2D::GetSamplerState(uint32 slot = 0);`
- slot: テクスチャスロットのインデックス (0～ (`SamplerState::MaxSamplerCount - 1`))
- 戻り値: 現在のサンプラーステート

指定したテクスチャスロットで現在 2D 描画に適用されているサンプラーステートを返します。テクスチャスロットのインデックスの最大値は `(SamplerState::MaxSamplerCount - 1)` です。

#### `Optional<Rect> Graphics2D::GetViewport();`
- 戻り値: 現在のカスタムビューポート。設定されていない場合 `none`

現在 2D 描画に適用されているカスタムビューポートを返します。設定されていない場合は `none` を返します。

#### `Optional<PixelShader> Graphics2D::GetCustomPixelShader();`
- 戻り値: 現在のカスタムピクセルシェーダ。設定されていない場合 `none`

現在 2D 描画に適用されているカスタムピクセルシェーダを返します。設定されていない場合は `none` を返します。

#### `Optional<RenderTexture> Graphics2D::GetRenderTarget();`
- 戻り値: 現在のレンダーターゲットに設定されているレンダーテクスチャ. デフォルトのシーンの場合 `none`

現在 2D 描画でレンダーターゲットに設定されているレンダーテクスチャを返します。設定されていない場合は `none` を返します。

#### `void Graphics2D::SetScissorRect(const Rect& rect);`
- rect: シザー矩形

2D 描画に適用するシザー矩形を設定します。`RasterizerState` でシザー矩形を有効にした場合に使われます。

#### `Rect Graphics2D::GetScissorRect();`
- 戻り値: 現在のシザー矩形

現在 2D 描画に適用されているシザー矩形を返します。デフォルトでは `Rect(0, 0, 0, 0)` です。

#### `void Graphics2D::SetLocalTransform(const Mat3x2& transform);`
- transform: 座標変換行列

2D 描画に適用する、ローカルな変換行列を設定します。通常は `Transformer2D` を用いるため、この関数は使いません。

#### `const Mat3x2& Graphics2D::GetLocalTransform();`
- 戻り値: 2D 描画座標を変換するために `Transformer2D` によって設定されている行列

2D 描画に適用されている、ローカルな変換行列を返します。

#### `void Graphics2D::SetCameraTransform(const Mat3x2& transform);`
- transform: 座標変換行列

2D 描画に適用する、2D カメラによる変換行列を設定します。通常は `BasicCamera2D` を用いるため、この関数は使いません。

#### `const Mat3x2& Graphics2D::GetCameraTransform();`
- 戻り値: 2D 描画座標を変換するために `BasicCamera2D` によって設定されている行列

2D 描画に適用されている、2D カメラによる変換行列を返します。

#### `double Graphics2D::GetMaxScaling();`
- 戻り値: 2D 描画の最大拡大倍率

現在 2D 描画に適用されている座標変換行列を用いて直径 1 の円を描いたときに、描画される円または楕円の最大の径を返します。どのような座標変換行列においても線分を同じ太さで描画したいときに、この戻り値の値が使えます。

#### `Size Graphics2D::GetRenderTargetSize();`
- 戻り値: 現在のレンダーターゲットの解像度

現在 2D 描画でレンダーターゲットに設定されているレンダーテクスチャもしくはデフォルトのシーンの解像度を返します。

#### `void Graphics2D::SetSDFParameters(double pixelRange, double offset = 0.0);`
#### `void Graphics2D::SetSDFParameters(cosnt Float4& parameters);`
- pixelRange: 使用する SDF フォントの `SDFFont::pixelRange()` の値
- offset: SDF フォント描画の閾値のオフセット
- parameters: SDF フォントのパラメータ。x 成分が pixelRange, y 成分が offset に対応

SDF フォントを描画するためのパラメータを設定します。

#### `Float4 Graphics2D::GetSDFParameters();`
- 戻り値: 現在の SDF フォント描画用のパラメータ

現在 SDF フォント描画用に設定されているパラメータを返します。x 成分が pixelRange, y 成分が offset に対応します。

#### `void Graphics2D::SetTexture(uint32 slot, const Optional<Texture>& texture);`
- slot: テクスチャスロットのインデックス (0～ (`SamplerState::MaxSamplerCount - 1`))
- texture: テクスチャ

2D 描画に適用する、追加のテクスチャを設定します。テクスチャを `.draw()` すると、そのテクスチャはスロットインデックス 0 にセットされます。それ以外に追加のテクスチャをシェーダで処理したい場合にこの関数を使います。テクスチャスロットのインデックスの最大値は `(SamplerState::MaxSamplerCount - 1)` です。

#### `void Graphics2D::Flush();`

現在のフレームでリクエストされた 2D 描画関連の命令をすべて実行します。`.draw()` や `Graphics2D::～` による 2D 描画命令は、処理の効率化のために一旦エンジン内でストックされ、命令が集約されてから `System::Update()` 内で一斉に実行されます。そのため、`RenderTexture` の中身を `.read()` で読み出す場合や `MSRenderTexture` を `.resolve()` する際に、実際には描画がなされていないケースが生じます。それを回避するためにこの関数を使います。

#### `template <class Type> void Graphics2D::SetConstantBuffer(ShaderStage stage, uint32 index, const ConstantBuffer<Type>& buffer);`
- stage: 対象のシェーダ
- index: 定数バッファインデックス (0～13)
- buffer: 定数バッファのデータ

2D 描画のカスタムシェーダで用いる、定数バッファを設定します。インデックス 0 の定数バッファはエンジンによって予約されているため、変更してはいけません。

## グラフィックス名前空間 (namespace Graphics)

### 関数

#### `void Graphics::SkipClearScreen();`
この次のフレームの開始時に、シーンの描画内容を背景色でクリアしないようにします。

#### `Array<DisplayOutput> Graphics::EnumOutputs();`
- 戻り値: 対応しているディスプレイ出力の一覧

現在のメインディスプレイが対応しているディスプレイ出力の一覧を返します。

#### `Array<Size> Graphics::GetFullscreenResolutions(double minRefreshRate = 49.0);`
- minRefreshRate: 要求する最低限のリフレッシュレート (Hz)
- 戻り値: フルスクリーンとして使用できる解像度の一覧

リフレッシュレートが `minRefreshRate` (Hz) 以上で、フルスクリーンとして使用できる解像度の一覧を返します。

#### `void Graphics::SetTargetFrameRateHz(const Optional<double>& targetFrameRateHz);`
- targetFrameRateHz: 設定する最大フレームレート (Hz)

アプリケーションのフレームレートの最大値を設定します。`none` を渡すと vSync が有効になり、ディスプレイの設定に沿ったフレームレートになります。デフォルトでは `none` です。コンピュータの性能によっては、実測のフレームレートが、この関数で設定した値を下回る場合があります。

#### `Optional<double> Graphics::GetTargetFrameRateHz();`
- 戻り値: アプリケーションのフレームレートの最大値、設定されていない場合は `none`

`Graphics::SetTargetFrameRateHz()` で設定した、アプリケーションのフレームレートの最大値を返します。デフォルトでは `none` です。

#### `double Graphics::GetDisplayRefreshRateHz();`
- 戻り値: 現在のメインディスプレイの表示リフレッシュレート (Hz)

現在のメインディスプレイの表示リフレッシュレートを返します。

#### `double Graphics::GetDPIScaling();`
- 戻り値: 現在のメインディスプレイの DPI 拡大率 (倍)

現在のメインディスプレイに対してユーザがシステムで設定している DPI 拡大率を返します。

## ディスプレイモード構造体 (struct DisplayMode)

### メンバ変数

#### `Size size;`
ディスプレイの表示解像度（ピクセル）

#### `double refreshRateHz;`
ディスプレイの表示リフレッシュレート (Hz)

## ディスプレイ出力構造体 (struct DisplayOutput)

### メンバ変数

#### `String name;`
ディスプレイの名称

#### `Rect displayRect;`
ディスプレイの仮想座標

#### `Array<DisplayMode> displayModes;`
フルスクリーンとして利用できる表示モードの一覧

## GUI ユーティリティー (namespace SimpleGUI)

### 関数

#### `RectF SimpleGUI::HeadlineRegion(const String& text, const Vec2& pos, const Optional<double>& width = unspecified);`
- text: 見出しのテキスト
- pos: 見出しの左上の座標（ピクセル）
- width: 見出しの幅（ピクセル）
- 戻り値: 見出しの領域（ピクセル）

SimpleGUI スタイルで見出しを表示したときの領域を返します。`width` が `unspecified` の場合、見出しのテキストに合わせた幅になります。

#### `void SimpleGUI::Headline(const String& text, const Vec2& pos, const Optional<double>& width = unspecified, bool enabled = true);`
- text: 見出しのテキスト
- pos: 見出しの左上の座標（ピクセル）
- width: 見出しの幅（ピクセル）
- enabled: アクティブ状態

SimpleGUI スタイルで見出しを描画します。`width` が `unspecified` の場合、見出しのテキストに合わせた幅になります。

#### `RectF SimpleGUI::ButtonRegion(const String& label, const Vec2& pos, const Optional<double>& width = unspecified);`
- label: ボタンのラベル
- pos: ボタンの左上の座標（ピクセル）
- width: ボタンの幅（ピクセル）
- 戻り値: ボタンの領域（ピクセル）

SimpleGUI スタイルでボタンを表示したときの領域を返します。`width` が `unspecified` の場合、ラベルに合わせた幅になります。

#### `RectF SimpleGUI::ButtonRegionAt(const String& label, const Vec2& center, const Optional<double>& width = unspecified);`
- label: ボタンのラベル
- center: ボタンの中心の座標（ピクセル）
- width: ボタンの幅（ピクセル）
- 戻り値: ボタンの領域（ピクセル）

SimpleGUI スタイルでボタンを表示したときの領域を返します。`width` が `unspecified` の場合、ラベルに合わせた幅になります。

#### `bool SimpleGUI::Button(const String& label, const Vec2& pos, const Optional<double>& width = unspecified, bool enabled = true);`
- label: ボタンのラベル
- pos: ボタンの左上の座標（ピクセル）
- widht: ボタンの幅（ピクセル）
- enabled: アクティブ状態
- 戻り値: ボタンがクリックされた場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルでボタンを表示します。ボタンがクリックされた場合 `true` を返します。

#### `bool SimpleGUI::ButtonAt(const String& label, const Vec2& center, const Optional<double>& width = unspecified, bool enabled = true);`
- label: ボタンのラベル
- center: ボタンの中心の座標（ピクセル）
- width: ボタンの幅（ピクセル）
- enabled: アクティブ状態
- 戻り値: ボタンがクリックされた場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルでボタンを表示します。ボタンがクリックされた場合 `true` を返します。

#### `RectF SimpleGUI::SliderRegion(const Vec2& pos, double labelWidth = 80.0, double sliderWidth = 120.0);`
- pos: スライダーの左上の座標（ピクセル）
- labelWidth: ラベルの幅（ピクセル）
- sliderWidth: スライド部分の長さ（ピクセル）
- 戻り値: スライダーの領域（ピクセル）

SimpleGUI スタイルで水平スライダーを表示したときの領域を返します。

#### `RectF SimpleGUI::SliderRegionAt(const Vec2& center, double labelWidth = 80.0, double sliderWidth = 120.0);`
- center: スライダーの中心の座標（ピクセル）
- labelWidth: ラベルの幅（ピクセル）
- sliderWidth: スライド部分の長さ（ピクセル）
- 戻り値: スライダーの領域（ピクセル）

SimpleGUI スタイルで水平スライダーを表示したときの領域を返します。

#### `bool SimpleGUI::Slider(double& value, const Vec2& pos, double sliderWidth = 120.0, bool enabled = true);`
#### `bool SimpleGUI::Slider(double& value, double min, double max, const Vec2& pos, double sliderWidth = 120.0, bool enabled = true);`
#### `bool SimpleGUI::Slider(const String& label, double& value, const Vec2& pos, double labelWidth = 80.0, double sliderWidth = 120.0, bool enabled = true);`
#### `bool SimpleGUI::Slider(const String& label, double& value, double min, double max, const Vec2& pos, double labelWidth = 80.0, double sliderWidth = 120.0, bool enabled = true);`
- value: 操作する値
- pos: スライダーの左上の座標（ピクセル）
- sliderWidth: スライド部分の長さ（ピクセル）
- enabled: アクティブ状態
- min: 値の最小値
- max: 値の最大値
- label: ラベルのテキスト
- labelWidth: ラベルの幅（ピクセル）
- 戻り値: ユーザのスライダー操作によって値が変更された場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルで水平スライダーを表示します。ユーザのスライダー操作によって値が変更された場合 `true` を返します。

#### `bool SimpleGUI::SliderAt(double& value, const Vec2& center, double sliderWidth = 120.0, bool enabled = true);`
#### `bool SimpleGUI::SliderAt(double& value, double min, double max, const Vec2& center, double sliderWidth = 120.0, bool enabled = true);`
#### `bool SimpleGUI::SliderAt(const String& label, double& value, const Vec2& center, double labelWidth = 80.0, double sliderWidth = 120.0, bool enabled = true);`
#### `bool SimpleGUI::SliderAt(const String& label, double& value, double min, double max, const Vec2& center, double labelWidth = 80.0, double sliderWidth = 120.0, bool enabled = true);`
- value: 操作する値
- center: スライダーの中心の座標（ピクセル）
- sliderWidth: スライド部分の長さ（ピクセル）
- enabled: アクティブ状態
- min: 値の最小値
- max: 値の最大値
- label: ラベルのテキスト
- labelWidth: ラベルの幅（ピクセル）
- 戻り値: ユーザのスライダー操作によって値が変更された場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルで水平スライダーを表示します。ユーザのスライダー操作によって値が変更された場合 `true` を返します。

#### `RectF SimpleGUI::VerticalSliderRegion(const Vec2& pos, double sliderHeight = 120.0);`
- pos: スライダーの左上の座標（ピクセル）
- sliderHeight: スライド部分の高さ（ピクセル）
- 戻り値: スライダーの領域（ピクセル）

SimpleGUI スタイルで垂直スライダーを表示したときの領域を返します。

#### `RectF SimpleGUI::VerticalSliderRegionAt(const Vec2& center, double sliderHeight = 120.0);`
- center: スライダーの中心の座標（ピクセル）
- sliderHeight: スライド部分の高さ（ピクセル）
- 戻り値: スライダーの領域（ピクセル）

SimpleGUI スタイルで垂直スライダーを表示したときの領域を返します。

#### `bool SimpleGUI::VerticalSlider(double& value, const Vec2& pos, double sliderHeight = 120.0, bool enabled = true);`
#### `bool SimpleGUI::VerticalSlider(double& value, double min, double max, const Vec2& pos, double sliderHeight = 120.0, bool enabled = true);`
- value: 操作する値
- pos: スライダーの左上の座標（ピクセル）
- sliderHeight: スライド部分の高さ（ピクセル）
- enabled: アクティブ状態
- min: 値の最小値
- max: 値の最大値
- 戻り値: ユーザのスライダー操作によって値が変更された場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルで垂直スライダーを表示します。ユーザのスライダー操作によって値が変更された場合 `true` を返します。

#### `bool SimpleGUI::VerticalSliderAt(double& value, const Vec2& center, double sliderHeight = 120.0, bool enabled = true);`
#### `bool SimpleGUI::VerticalSliderAt(double& value, double min, double max, const Vec2& center, double sliderHeight = 120.0, bool enabled = true);`
- value: 操作する値
- center: スライダーの中心の座標（ピクセル）
- sliderHeight: スライド部分の高さ（ピクセル）
- enabled: アクティブ状態
- min: 値の最小値
- max: 値の最大値
- 戻り値: ユーザのスライダー操作によって値が変更された場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルで垂直スライダーを表示します。ユーザのスライダー操作によって値が変更された場合 `true` を返します。

#### `RectF SimpleGUI::CheckBoxRegion(const String& label, const Vec2& pos, const Optional<double>& width = unspecified);`
- label: ラベル
- pos: チェックボックスの左上の座標（ピクセル）
- width: チェックボックスの幅（ピクセル）
- 戻り値: チェックボックスの領域（ピクセル）

SimpleGUI スタイルでチェックボックスを表示したときの領域を返します。`width` が `unspecified` の場合、見出しのテキストに合わせた幅になります。

#### `RectF SimpleGUI::CheckBoxRegionAt(const String& label, const Vec2& center, const Optional<double>& width = unspecified);`
- label: ラベル
- center: チェックボックスの中心の座標（ピクセル）
- width: チェックボックスの幅（ピクセル）
- 戻り値: チェックボックスの領域（ピクセル）

SimpleGUI スタイルでチェックボックスを表示したときの領域を返します。`width` が `unspecified` の場合、見出しのテキストに合わせた幅になります。

#### ` bool CheckBox(bool& checked, const String& label, const Vec2& pos, const Optional<double>& width = unspecified, bool enabled = true);`
- checked: チェック状態
- label: ラベル
- pos: チェックボックスの左上の座標（ピクセル）
- width: チェックボックスの幅（ピクセル）
- enabled: アクティブ状態
- 戻り値: ユーザの操作によって値が変更された場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルでチェックボックスを表示します。ユーザの操作によって値が変更された場合 `true` を返します。`width` が `unspecified` の場合、見出しのテキストに合わせた幅になります。

#### `bool SimpleGUI::CheckBoxAt(bool& checked, const String& label, const Vec2& center, const Optional<double>& width = unspecified, bool enabled = true);`
- checked: チェック状態
- label: ラベル
- center: チェックボックスの中心の座標（ピクセル）
- width: チェックボックスの幅（ピクセル）
- enabled: アクティブ状態
- 戻り値: ユーザの操作によって値が変更された場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルでチェックボックスを表示します。ユーザの操作によって値が変更された場合 `true` を返します。`width` が `unspecified` の場合、見出しのテキストに合わせた幅になります。

#### `RectF SimpleGUI::RadioButtonsRegion(const Array<String>& options, const Vec2& pos, const Optional<double>& width = unspecified);`
- options: ラジオボタンの選択肢
- pos: ラジオボタンの左上の座標（ピクセル）
- width: ラジオボタンの幅（ピクセル）
- 戻り値: ラジオボタンの領域（ピクセル）

SimpleGUI スタイルでラジオボタンを表示したときの領域を返します。`width` が `unspecified` の場合、見出しのテキストに合わせた幅になります。

#### `RectF SimpleGUI::RadioButtonsRegionAt(const Array<String>& options, const Vec2& center, const Optional<double>& width = unspecified);`
- options: ラジオボタンの選択肢
- center: ラジオボタンの中心の座標（ピクセル）
- width: ラジオボタンの幅（ピクセル）
- 戻り値: ラジオボタンの領域（ピクセル）

SimpleGUI スタイルでラジオボタンを表示したときの領域を返します。`width` が `unspecified` の場合、見出しのテキストに合わせた幅になります。

#### `bool SimpleGUI::RadioButtons(size_t& index, const Array<String>& options, const Vec2& pos, const Optional<double>& width = unspecified, bool enabled = true);`
- index: 選択されている選択肢のインデックス
- options: ラジオボタンの選択肢
- pos: ラジオボタンの左上の座標（ピクセル）
- width: ラジオボタンの幅（ピクセル）
- enabled: アクティブ状態
- 戻り値: ユーザの操作によって値が変更された場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルでラジオボタンを表示します。ユーザの操作によって値が変更された場合 `true` を返します。`width` が `unspecified` の場合、見出しのテキストに合わせた幅になります。

#### `bool SimpleGUI::RadioButtonsAt(size_t& index, const Array<String>& options, const Vec2& center, const Optional<double>& width = unspecified, bool enabled = true);`
- index: 選択されている選択肢のインデックス
- options: ラジオボタンの選択肢
- center: ラジオボタンの中心の座標（ピクセル）
- width: ラジオボタンの幅（ピクセル）
- enabled: アクティブ状態
- 戻り値: ユーザの操作によって値が変更された場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルでラジオボタンを表示します。ユーザの操作によって値が変更された場合 `true` を返します。`width` が `unspecified` の場合、見出しのテキストに合わせた幅になります。

#### `RectF SimpleGUI::TextBoxRegion(const Vec2& pos, double width = 200.0);`
- pos: テキストボックスの左上の座標（ピクセル）
- width: テキストボックスの幅（ピクセル）
- 戻り値: テキストボックスの領域（ピクセル）

SimpleGUI スタイルでテキストボックスを表示したときの領域を返します。

#### `RectF SimpleGUI::TextBoxRegionAt(const Vec2& center, double width = 200.0);`
- center: テキストボックスの中心の座標（ピクセル）
- width: テキストボックスの幅（ピクセル）
- 戻り値: テキストボックスの領域（ピクセル）

SimpleGUI スタイルでテキストボックスを表示したときの領域を返します。

#### `bool SimpleGUI::TextBox(TextEditState& text, const Vec2& pos, double width = 200.0, const Optional<size_t>& maxChars = unspecified, bool enabled = true);`
- text: テキスト編集情報
- pos: テキストボックスの左上の座標（ピクセル）
- width: テキストボックスの幅（ピクセル）
- maxChars: 入力文字数の上限、設定しない場合は `unspecified`
- enabled: アクティブ状態
- 戻り値: ユーザの操作によってテキストが変更された場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルでテキストボックスを表示します。ユーザの操作によって値が変更された場合 `true` を返します。`maxChars` が `unspecified` の場合、入力文字数の上限はありません。

#### `bool SimpleGUI::TextBoxAt(TextEditState& text, const Vec2& center, double width = 200.0, const Optional<size_t>& maxChars = unspecified, bool enabled = true);`
- text: テキスト編集情報
- center: テキストボックスの中心の座標（ピクセル）
- width: テキストボックスの幅（ピクセル）
- maxChars: 入力文字数の上限、設定しない場合は `unspecified`
- enabled: アクティブ状態
- 戻り値: ユーザの操作によってテキストが変更された場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルでテキストボックスを表示します。ユーザの操作によって値が変更された場合 `true` を返します。`maxChars` が `unspecified` の場合、入力文字数の上限はありません。

#### `RectF SimpleGUI::ColorPickerRegion(const Vec2& pos);`
- pos: カラーピッカーの左上の座標（ピクセル）
- 戻り値: カラーピッカーの領域（ピクセル）

SimpleGUI スタイルでカラーピッカーを表示したときの領域を返します。

#### `RectF SimpleGUI::ColorPickerRegionAt(const Vec2& center);`
- center: カラーピッカーの中心の座標（ピクセル）
- 戻り値: カラーピッカーの領域（ピクセル）

SimpleGUI スタイルでカラーピッカーを表示したときの領域を返します。

#### `bool SimpleGUI::ColorPicker(HSV& hsv, const Vec2& pos, bool enabled = true);`
- hsv: 操作する色
- pos: カラーピッカーの左上の座標（ピクセル）
- enabled: アクティブ状態
- 戻り値: ユーザの操作によって色が変更された場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルでカラーピッカーを表示します。ユーザの操作によって色が変更された場合 `true` を返します。

#### `bool ColorPickerAt(HSV& hsv, const Vec2& center, bool enabled = true);`
- hsv: 操作する色
- center: カラーピッカーの中心の座標（ピクセル）
- enabled: アクティブ状態
- 戻り値: ユーザの操作によって色が変更された場合 `true`, それ以外の場合 `false`

SimpleGUI スタイルでカラーピッカーを表示します。ユーザの操作によって色が変更された場合 `true` を返します。

## テキスト編集構造体 (struct TextEditState)

テキストボックスなどでテキストを編集する際に使う情報です。

### メンバ変数

#### `String text;`
入力されたテキスト

#### `size_t cursorPos = 0;`
入力カーソル位置

#### `bool active = false;`
アクティブ状態

#### `Stopwatch leftPressStopwatch;`
カーソルの左移動タイミング用のストップウォッチ（内部処理用）

#### `Stopwatch rightPressStopwatch;`
カーソルの右移動タイミング用のストップウォッチ（内部処理用）

#### `Stopwatch cursorStopwatch;`
入力カーソルの点滅タイミング用のストップウォッチ（内部処理用）

### コンストラクタ

#### `TextEditState() = default();`
#### `TextEditState(const String& defaultText);`
- defaultText: 初期テキスト

テキスト編集構造体を初期化します。

### メンバ関数

#### `void clear();`

テキスト編集情報をすべてクリアし、入力テキストを空にします。

## ストップウォッチクラス (class Stopwatch)

経過時間を計測するためのクラスです。

### コンストラクタ

#### `Stopwatch(bool startImmediately = false);`
#### `Stopwatch(const Duration& time, bool startImmediately = false);`
- startImmediately: 時間の計測を直ちに開始するかのフラグ
- time: 最初に進めておく時間

ストップウォッチを作成します。`startImmediately` が `true` の場合は作成と同時に時間の計測が開始されます。

### メンバ関数

#### `void start();`

時間の計測を開始または再開します。すでに計測中の場合は何もしません。

#### `int32 d();`
- 戻り値: ストップウォッチの経過時間（日）

計測された経過時間（日）を返します。小数点以下は切り捨てられ、51 時間の場合は `2` を返します。

#### `int64 d64();`
- 戻り値: ストップウォッチの経過時間（日）

計測された経過時間（日）を返します。小数点以下は切り捨てられ、51 時間の場合は `2` を返します。

#### `double dF();`
- 戻り値: ストップウォッチの経過時間（日）

計測された経過時間（日）を浮動小数点数の値で返します。51 時間の場合は `2.125` を返します。

#### `int32 h();`
- 戻り値: ストップウォッチの経過時間（時）

計測された経過時間（時）を返します。小数点以下は切り捨てられ、40 時間半の場合は `40` を返します。

#### `int64 h64();`
- 戻り値: ストップウォッチの経過時間（時）

計測された経過時間（時）を返します。小数点以下は切り捨てられ、40 時間半の場合は `40` を返します。

#### `double hF();`
- 戻り値: ストップウォッチの経過時間（時）

計測された経過時間（時）を浮動小数点数の値で返します。40 時間半の場合は `40.5` を返します。

#### `int32 min();`
- 戻り値: ストップウォッチの経過時間（分）

計測された経過時間（分）を返します。小数点以下は切り捨てられ、2 時間 3 分 6 秒の場合は `123` を返します。

#### `int64 min64();`
- 戻り値: ストップウォッチの経過時間（分）

計測された経過時間（分）を返します。小数点以下は切り捨てられ、2 時間 3 分 6 秒の場合は `123` を返します。

#### `double minF();`
- 戻り値: ストップウォッチの経過時間（分）

計測された経過時間（分）を浮動小数点数の値で返します。2 時間 3 分 6 秒の場合は `123.1` を返します。

#### `int32 s();`
- 戻り値: ストップウォッチの経過時間（秒）

計測された経過時間（秒）を返します。小数点以下は切り捨てられ、3 分 15 秒 10 の場合は `195` を返します。

#### `int64 s64();`
- 戻り値: ストップウォッチの経過時間（秒）

計測された経過時間（秒）を返します。小数点以下は切り捨てられ、3 分 15 秒 10 の場合は `195` を返します。

#### `double sF();`
- 戻り値: ストップウォッチの経過時間（秒）

計測された経過時間（秒）を浮動小数点数の値で返します。3 分 15 秒 10 の場合は `195.1` を返します。

#### `int32 ms();`
- 戻り値: ストップウォッチの経過時間（ミリ秒）

計測された経過時間（ミリ秒）を返します。小数点以下は切り捨てられ、9123.4 ミリ秒 の場合は `9123` を返します。

#### `int64 ms64();`
- 戻り値: ストップウォッチの経過時間（ミリ秒）

計測された経過時間（ミリ秒）を返します。小数点以下は切り捨てられ、9123.4 ミリ秒 の場合は `9123` を返します。

#### `double msF();`
- 戻り値: ストップウォッチの経過時間（ミリ秒）

計測された経過時間（ミリ秒）を浮動小数点数の値で返します。9123.4 ミリ秒 の場合は `9123.4` を返します。

#### `int64 us();`
- 戻り値: ストップウォッチの経過時間（マイクロ秒）

計測された経過時間（マイクロ秒）を返します。小数点以下は切り捨てられ、5678.9 マイクロ秒 の場合は `5678` を返します。

#### `int64 us64();`
- 戻り値: ストップウォッチの経過時間（マイクロ秒）

計測された経過時間（マイクロ秒）を返します。小数点以下は切り捨てられ、5678.9 マイクロ秒 の場合は `5678` を返します。

#### `double usF();`
- 戻り値: ストップウォッチの経過時間（マイクロ秒）

計測された経過時間（マイクロ秒）を浮動小数点数の値で返します。5678.9 ミリ秒 の場合は `5678.9` を返します。

#### `Duration elapsed();`
- 戻り値: 経過時間 (`Duration` 型)

計測された経過時間を `Duration` 型で返します。

#### `bool isStarted();`
- 戻り値: 計測開始済みの場合 `true`

ストップウォッチが計測開始済みであるかを返します。

#### `bool isPaused();`
- 戻り値: 計測開始済みで、一時停止中の場合 `true`

ストップウォッチが計測開始済みで、一時停止中であるかを返します。

#### `bool isRunning();`
- 戻り値: 計測開始済みで、なおかつ一時停止中でない場合 `true`

ストップウォッチが計測中であるかを返します。

#### `void pause();`

時間の計測を一時停止します。計測開始済みでない場合や、すでに一時停止中の場合は何もしません。

#### `void resume();`

一時停止中の時間の計測を再びスタートさせます。一時停止中出ない場合は何もしません。

#### `void reset();`

計測された時間の記録を消去してストップウォッチを計測未開始の状態に戻します。

#### `void restart();`

計測された時間の記録を消去してストップウォッチを計測未開始の状態に戻し、再度計測を開始します。

#### `void set(const Duration& time);`
- time: 設定する時間

計測された経過時間を変更します。

#### `String format(StringView format = U"H:mm:ss.xx"_sv);`
- format: 時刻の表現方法
- 戻り値: 文字列に変換した時刻

計測された経過時間を文字列に変換します。

### 非メンバ関数

#### `bool operator <(const Stopwatch& s, const MicrosecondsF& time);`
#### `bool operator <=(const Stopwatch& s, const MicrosecondsF& time);`
#### `bool operator >(const Stopwatch& s, const MicrosecondsF& time);`
#### `bool operator >=(const Stopwatch& s, const MicrosecondsF& time);`
#### `bool operator <(const MicrosecondsF& time, const Stopwatch& s);`
#### `bool operator <=(const MicrosecondsF& time, const Stopwatch& s);`
#### `bool operator >(const MicrosecondsF& time, const Stopwatch& s);`
#### `bool operator >=(const MicrosecondsF& time, const Stopwatch& s);`
- s: ストップウォッチ
- time: 比較する時間
- 戻り値: 比較の結果

ストップウォッチで計測された経過時間を、別の時間と比較します。

#### `void Formatter(FormatData& formatData, const Stopwatch& value);`
- formatData: フォーマットデータ（内部処理用）
- value: ストップウォッチ

計測された経過時間を文字列にフォーマットします。変換には `Stopwatch::format()` が使われます。

#### `template <class CharType> std::basic_ostream<CharType>& operator <<(std::basic_ostream<CharType> output, const Stopwatch& value);`
- output: 出力ストリーム
- value: ストップウォッチ
- 戻り値: 出力ストリーム

計測された経過時間を出力ストリームに出力します。

## ファイルシステム関連の定数

### `enum class OpenMode`

ファイルを書き込み用途でオープンする際、同名のファイルが存在したときの扱いです。`BinaryWriter`, `TextWriter` などで使います。

#### `OpenMode::Trunc`

古い内容を破棄し、サイズをゼロにした状態から書き込みをします。

#### `OpenMode::Append`

古い内容を保持し、ファイルの末尾から新しく書き込みをします。

### `enum class CopyOption`

ファイルをコピーする際、同名のファイルが存在したときの扱いです。`FileSystem::Copy()` などで使います。

#### `CopyOption::None`

ファイル名が既に使われていた場合、コピーを失敗させ、エラー値を返します。

#### `CopyOption::SkipExisting`

ファイル名が既に使われていた場合、エラーを発生させずにスキップします。

#### `CopyOption::OverwriteExisting`

ファイル名が既に使われていた場合、コピー先のファイルを上書きします。

#### `CopyOption::UpdateExisting`

ファイルが既に存在する場合、コピーするファイルがコピー先のファイルよりも新しければ上書きし、そうでなければスキップします。

#### `CopyOption::Default = None`

デフォルトのコピーオプション、`CopyOption::None` です。

### `enum class SpecialFolder`

特殊フォルダを表します。

#### `SpecialFolder::Desktop`

ユーザのデスクトップフォルダです。

#### `SpecialFolder::Documents`

ユーザのドキュメントフォルダです。

#### `SpecialFolder::LocalAppData`

ユーザのキャッシュフォルダです。重要でないキャッシュデータなど、一時ファイルの保存に適しています。

#### `SpecialFolder::Pictures`

ユーザのピクチャーフォルダです。

#### `SpecialFolder::Music`

ユーザのミュージックフォルダです。

#### `SpecialFolder::Videos`

ユーザのビデオフォルダです。

#### `SpecialFolder::Caches = LocalAppData`

ユーザのキャッシュフォルダです。重要でないキャッシュデータなど、一時ファイルの保存に適しています。`SpecialFolder::LocalAppData` の別名です。

#### `SpecialFolder::Movies = Videos`

ユーザのビデオフォルダです。`SpecialFolder::Movies` の別名です。

#### `SpecialFolder::SystemFonts`

システムのフォントフォルダです。Windows では OS ボリュームの `WINDOWS/Fonts/`, macOS では `/System/Library/Fonts/`, Linux では `Documents` ディレクトリを指します。

#### `SpecialFolder::LocalFonts`

ローカルのフォントフォルダです。Windows では OS ボリュームの `WINDOWS/Fonts/`, macOS では `/Library/Fonts/`, Linux では `Documents` ディレクトリを指します。

#### `SpecialFolder::UserFonts`

ユーザのフォントフォルダです。Windows では OS ボリュームの `WINDOWS/Fonts/`, macOS では `~/Library/Fonts/`, Linux では `Documents` ディレクトリを指します。

## ファイルシステム名前空間 (namespace FileSystem)

### 関数

#### `bool FileSystem::Exists(FilePathView path);`
- path: パス
- 戻り値: 存在する場合は true, それ以外の場合 false

指定したパスのファイルまたはディレクトリが存在するかを返します。

#### `bool FileSystem::IsDirectory(FilePathView path);`
- path: パス
- 戻り値: 存在するディレクトリを指すパスである場合は true, それ以外の場合 false

指定したパスが、存在するディレクトリを指すものであるかを返します。

#### `bool FileSystem::IsFile(FilePathView path);`
- path: パス
- 戻り値: 存在するファイルを指すパスである場合は true, それ以外の場合 false

指定したパスが、存在するファイルを指すものであるかを返します。

#### `bool FileSystem::IsResource(FilePathView path);`
- path: パス
- 戻り値: 存在するリソースを指すパスである場合は true, それ以外の場合 false

指定したパスが、存在するリソースを指すものであるかを返します。リソースは実行ファイルに埋め込み (Windows) またはバンドル (macOS) されたファイルのことです。Linux では `/resources/` 以下のファイルがリソースとして扱われます。

#### `FilePath FileSystem::FullPath(FilePathView path);`
- path: パス
- 戻り値: 絶対パス。失敗した場合は空の文字列

指定したパスの絶対パスを返します。ディレクトリの場合は末尾が `/` になります。

#### `Platform::NativeFilePath FileSystem::NativePath(FilePathView path);`
- path: パス
- 戻り値: ネイティブパス。失敗した場合は空の文字列

指定したパスの、OS ネイティブ表現でのパス文字列を返します。

#### `String FileSystem::Extension(FilePathView path);`
- path: パス
- 戻り値: 小文字の拡張子。失敗した場合は空の文字列

指定したファイルの .を含まない小文字の拡張子を返します。（例: "png"）

#### `String FileSystem::FileName(FilePathView path);`
- path: パス
- 戻り値: ファイル名。失敗した場合は空の文字列

 指定したファイルの、親ディレクトリを含まない名前を返します。（例: "picture.png"）

#### `String FileSystem::BaseName(FilePathView path);`
- path: パス
- 戻り値: ファイル名。失敗した場合は空の文字列

指定したファイルの、親ディレクトリと拡張子を含まない名前を返します。（例: "picture"）

#### `FilePath FileSystem::ParentPath(FilePathView path, size_t level = 0, FilePath* baseFullPath = nullptr);`
- path: パス
- level: 親ディレクトリの階層。デフォルトでは 0
- baseFullPath: `path` の絶対パス（オプション）
- 戻り値: 親ディレクトリ。失敗した場合は空の文字列

指定したファイルの親ディレクトリを返します。親のさらに親ディレクトリを得たい場合には `level` を増やします。ちょうど 1 つ上位のディレクトリを基準に `0` で、1 ずつ増やします。

#### `FilePath FileSystem::VolumePath(FilePathView path);`
- path: パス
- 戻り値: ドライブのパス。失敗した場合は空の文字列

指定したファイルのドライブのパスを返します。（例: "C:/"）

#### `bool FileSystem::IsEmptyDirectory(FilePathView path);`
- path: パス
- 戻り値: ディレクトリが空である場合は true, それ以外の場合 false

指定したディレクトリが空であるかを返します。

#### `int64 FileSystem::Size(FilePathView path);`
- path: パス
- 戻り値: ファイルやディレクトリが存在しなかったり、空である場合は 0 を返します。

指定したファイルまたはディレクトリのサイズを返します。

#### `int64 FileSystem::FileSize(FilePathView path);`
- path: パス
- 戻り値: ファイルが存在しなかったり、空である場合は 0 を返します。

指定したファイルのサイズを返します。

#### `Optional<DateTime> FileSystem::CreationTime(FilePathView path);`
- path: パス
- 戻り値: 作成日時。ファイルが存在しない場合 none

ファイルまたはディレクトリの作成日時を返します。

#### `Optional<DateTime> FileSystem::WriteTime(FilePathView path);`
- path: パス
- 戻り値: 更新日時。ファイルが存在しない場合 none

ファイルまたはディレクトリの更新日時を返します。

#### `Optional<DateTime> FileSystem::AccessTime(FilePathView path);`
- path: パス
- 戻り値: アクセス日時。ファイルが存在しない場合 none

ファイルまたはディレクトリのアクセス日時を返します。

#### `Array<FilePath> FileSystem::DirectoryContents(const FilePath& path, bool recursive = true);`
- path: ディレクトリのパス
- recursive: 指定したパスにフォルダが含まれる場合、その中のファイルも再帰的に列挙する場合は `true`
- 戻り値: ファイルとディレクトリの一覧

指定したディレクトリにあるファイルとディレクトリの一覧を返します。

#### `const FilePath& FileSystem::InitialDirectory();`
- 戻り値: プログラムが起動したパス

プログラムが起動したパスを返します。

#### `const FilePath& FileSystem::ModulePath();`
- 戻り値: 現在のアプリケーションの絶対パス

現在のアプリケーションの絶対パスを返します。

#### `FilePath FileSystem::CurrentDirectory();`
- 戻り値: カレントディレクトリ

実行中のプログラムのカレントディレクトリを返します。

#### `bool FileSystem::ChangeCurrentDirectory(FilePathView path);`
- path: 新しいディレクトリ
- 戻り値: 変更に成功した場合 `true`, それ以外の場合 `false`

実行中のプログラムのカレントディレクトリを変更します。

#### `FilePath FileSystem::SpecialFolderPath(SpecialFolder folder);`
- folder: 特殊フォルダの種類
- 戻り値: 特殊フォルダのパス

特殊フォルダのパスを返します。

#### `FilePath FileSystem::TemporaryDirectoryPath();`
- 戻り値: 一時ファイル用のディレクトリのパス

一時ファイル用のディレクトリのパスを返します。パスの末尾には '/' が付きます。

#### `FilePath FileSystem::UniqueFilePath(FilePathView directory = TemporaryDirectoryPath());`
- directory: 一時ファイル用のディレクトリ
- 戻り値: 一時ファイル用のファイルパス

一時ファイル用の固有なファイルパスを返します。ファイルの拡張子は ".tmp" です。

#### `FilePath FileSystem::RelativePath(FilePathView path, FilePathView start = FileSystem::CurrentDirectory());`
- path: パス
- start: 相対パスの基準位置
- 戻り値: 相対パス

指定したパスを相対パスに変換します。

#### `bool FileSystem::CreateDirectories(FilePathView path);`
- path: ディレクトリパス
- 戻り値: 成功した場合  `true`, それ以外の場合 `false`

空のディレクトリを作成します。

#### `bool FileSystem::CreateParentDirectories(FilePathView path);`
- path: パス
- 戻り値: 成功した場合  `true`, それ以外の場合 `false`

指定したパスまでの親ディレクトリを作成します。

#### `bool FileSystem::Copy(FilePathView from, FilePathView to, CopyOption copyOption = CopyOption::Default);`
- from: コピーするパス
- to: コピー先のパス
- copyOption: 名前衝突時のふるまい
- 戻り値: 成功した場合  `true`, それ以外の場合 `false`

ファイルまたはディレクトリの中身をコピーします。

#### `bool FileSystem::Remove(FilePathView path, bool allowUndo = false);`
- path: パス
- allowUndo: 削除したファイルをごみ箱に送る場合 `true`, それ以外の場合 `false`
- 戻り値: 成功した場合  `true`, それ以外の場合 `false`

ファイルまたはディレクトリを削除します。

#### `bool FileSystem::RemoveContents(FilePathView path, bool allowUndo = false);`
- path: ディレクトリのパス
- allowUndo: 削除したファイルをごみ箱に送る場合 `true`, それ以外の場合 `false`
- 戻り値: 成功した場合  `true`, それ以外の場合 `false`

ディレクトリの中身だけを削除します。

#### `bool FileSystem::Rename(FilePathView from, FilePathView to);`
- from: 変更前のパス
- to: 変更後のパス
- 戻り値: 

ファイルまたはディレクトリの名前を変更します。

#### `bool FileSystem::IsSandBoxed();`
- 戻り値: macOS のサンドボックスモードで実行されている場合 `true`, それ以外の場合 `false`

macOS のサンドボックスモードで実行されているかを返します。Windows や Linux では常に `false` を返します。

## キー定数

### マウスボタン

#### `constexpr Key MouseL;`

マウスの左ボタンです。

#### `constexpr Key MouseR;`

マウスの右ボタンです。

#### `constexpr Key MouseM;`

マウスの中央ボタンです。マウスによっては存在しません。

#### `constexpr Key MouseX1;`

マウスの拡張ボタン 1 です。マウスによっては存在しません。

#### `constexpr Key MouseX2;`

マウスの拡張ボタン 2 です。マウスによっては存在しません。

#### `constexpr Key MouseX3;`

マウスの拡張ボタン 3 です。マウスによっては存在しません。

#### `constexpr Key MouseX4;`

マウスの拡張ボタン 4 です。マウスによっては存在しません。

#### `constexpr Key MouseX5;`

マウスの拡張ボタン 5 です。マウスによっては存在しません。

## マウス名前空間 (namespace Mouse)

### 関数

#### `double Wheel();`
- 戻り値: マウスホイールのスクロール量

マウスホイールのスクロール量を返します。

#### `double WheelH();`
- 戻り値: マウスの水平ホイールのスクロール量

マウスの水平ホイールのスクロール量を返します。
