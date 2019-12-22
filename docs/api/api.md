description: OpenSiv3D の API 一覧

## シーン (Scene)

### 定数

#### `enum class ScaleMode`
ウィンドウを手動でリサイズしたときのシーンのサイズの扱いです。ウィンドウをリサイズする関数で `WindowResizeOption::UseDefaultScaleMode` が指定されたときにも参照されます。 

##### `ScaleMode::ResizeFill`
ウィンドウのクライアント領域のサイズに合わせてシーンをリサイズします。

##### `ScaleMode::AspectFit`
シーンのサイズはそのままで、アスペクト比を維持して拡大縮小してクライアント領域にフィットさせ描画します。

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


## システム (System)

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


## ウィンドウ (Window)

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

## マウスカーソル (Cursor)

### 定数

#### `enum class CursorStyle`
マウスカーソルの形状を表します。

##### `CursorStyle::Arrow`
通常の矢印カーソルです。

##### `CursorStyle::IBeam`
テキスト入力時に使う I の形をしたカーソルです。

##### `CursorStyle::Cross`
十字型のカーソルです。

##### `CursorStyle::Hand`
人差し指を伸ばした手のアイコンのカーソルです。

##### `CursorStyle::NotAllowed`
禁止マークのアイコンのカーソルです。

##### `CursorStyle::ResizeUpDown`
上下へのリサイズ操作を表現するカーソルです。

##### `CursorStyle::ResizeLeftRight`
左右へのリサイズ操作を表現するカーソルです。

##### `CursorStyle::Hidden`
マウスカーソルを非表示にします。

##### `CursorStyle::Default = Arrow` 
デフォルトのマウスカーソルです。デフォルト値は `CursorStyle::Arrow` です。

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


## 時間 (Time)

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
