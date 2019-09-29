
## Scene 名前空間

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

シーンの幅と高さを変更します。デフォルト値は `Scene::DefaultSceneSize` です。両方またはどちらかの値を 0 以下にすること、8192 より大きい値を設定することはできません。

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


## System 名前空間

### 関数

#### `bool System::Update();`
- 戻り値: プログラムの続行の可否

描画や入力情報など、フレームを更新します。フレームレートを調整する働きもあります。アプリケーション終了トリガーが発生するか、内部で回復不能なエラーが発生した場合に `false` を返します。この関数が `false` を返したらプログラムを終了させるべきです。

#### `void System::Exit();`

プログラムの終了のために、この直後の `System::Update()` が `false` を返すように設定します。この関数自体が終了処理を行うわけではないので、この関数の呼び出しは必須ではありません。

#### `void System::SetTerminationTriggers(uint32 userActionFlags);`
- userActionFlags: アプリケーション終了トリガーに設定するユーザアクションのフラグ

アプリケーション終了トリガーに設定するユーザアクションを設定します。

#### `uint32 System::GetTerminationTriggers();`
- 戻り値: 前のフレームで発生したアプリケーション終了トリガーのフラグ

#### `uint32 System::GetUserActions();`
- 戻り値: 前のフレームで発生したユーザアクションのフラグ

#### `void System::Sleep(int32 milliseconds);`
#### `void System::Sleep(const Duration& duration);`
- milliseconds: スリープする時間（ミリ秒）
- duration: スリープする時間


#### `bool System::LaunchBrowser(const FilePath& url);`
- url: オープンする URL
- 戻り値: オープンの成功の可否

指定された URL をデフォルトの Web ブラウザでオープンします。
