# まとめ

## 入門チュートリアル

### 2. Siv3D の基本

#### `# include <Siv3D.hpp>`
Siv3D のヘッダファイルをインクルードする。これを書くことで、Siv3D の機能を使うことができる。

#### `void Main()`
ユーザが実装するメイン関数。この関数の実行が終了すると、Siv3D の終了処理が行われる。

#### `bool System::Update();`
ウィンドウの表示や音楽の再生、マウスやキーボードの入力情報などを更新する。普段は `true` を返すが、アプリケーションを終了すべき時は `false` を返す。

#### `void System::Exit();`
これ以降に呼ばれる `System::Update()` が `false` を返すようにする。必ずしも呼ぶ必要はない。

#### `double Scene::Time();`
プログラムの実行時間を秒単位で返す。

### 3. Main 関数の構成

#### `int32 Profiler::FPS();`
1 秒間に実行されたメインループの回数（フレームレート）を返す。

#### `void Window::SetTitle(...);`
ウィンドウのタイトルを設定する。

### 4. アプリケーションの基本操作

#### `System::SetTerminationTriggers(actions);`
アプリケーションを終了させるユーザアクションを設定する。デフォルトでは、ウィンドウの閉じるボタンを押す操作と、++esc++ を押す操作が設定されている。

#### `UserAction::CloseButtonClicked`
ウィンドウの閉じるボタンを押すユーザアクション。

#### `UserAction::EscapeKeyClicked`
++esc++ を押すユーザアクション。

#### `UserAction::NoAction`
アプリケーションを終了させるためのユーザアクションを設定しないことを示す。

#### `UserAction::Default`
デフォルトのユーザアクション。`(UserAction::CloseButtonClicked | UserAction::EscapeKeyClicked)` と同じ。

### 5. 簡易的なデータ表示

#### `Print << ...;`
文字列や数値などのデータを簡易表示する。

#### `ClearPrint();`
簡易表示の内容をすべて消去する。


