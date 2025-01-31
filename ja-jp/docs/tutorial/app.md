# 5. アプリの基本操作
Siv3D アプリケーションの基本的な操作を学びます。

## 5.1 プログラムを終了してウィンドウを閉じる
- 次のいずれかの操作で、実行中のプログラムを終了できます（**チュートリアル 3.5** 参照）
	1. ウィンドウの閉じるボタンを押す
	2. ++esc++ キーを押す
	3. プログラムの中で `System::Exit()` を呼び出す
- プログラムの終了と同時に、ウィンドウも閉じられます
- 終了操作をカスタマイズする方法は、チュートリアル 5.6 を参照してください

## 5.2 スクリーンショットを保存する
- プログラムの実行中、次のいずれかのショートカットキーでスクリーンショットを保存します
	- ++print-screen++ を押す
	- ++f12++ を押す（Visual Studio でのデバッグ中は、++f12++ が別の機能に割り当てられているため使えません）
- スクリーンショットの保存先は OS によって異なります

| OS | 保存先 |
| --- | --- |
| Windows | 実行ファイルと同じディレクトリの `Screenshot` フォルダ |
| macOS | ピクチャフォルダ内の `Screenshot` フォルダ |
| Linux | 実行ファイルと同じディレクトリの `Screenshot` フォルダ |

- パソコンの設定によっては、++print-screen++ キーが別のアプリケーション（OneDrive や Dropbox）に関連付けられていて、上記の場所にスクリーンショットが保存されないことがあります

??? example "Windows での画面の録画"
	- Windows 10, 11 には、OS 自体に画面の録画機能が搭載されています
	- 詳しくは [**Xbox Game Bar**](../tools/gamebar.md){target="_blank"} を参照してください


## 5.3 スクリーンショット用のショートカットキーを変更する
- 現在の Siv3D アプリでスクリーンショットを保存するショートカットキーを変更するには、`ScreenCapture::SetShortcutKeys({ キーのリスト })` を使います

```cpp title="スクリーンショットのショートカットキーを変更する" hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// [A] キーが押されたときのみスクリーンショットを保存するよう設定する
	ScreenCapture::SetShortcutKeys({ KeyA });

	while (System::Update())
	{
		Circle{ Scene::Center(), 100 }.draw();
	}
}
```

- 複数のキーの指定もできます

```cpp title="スクリーンショットのショートカットキーを変更する" hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// [PrintScreen] キーまたは [P] キーが押されたときのみスクリーンショットを保存するよう設定する
	ScreenCapture::SetShortcutKeys({ KeyPrintScreen, KeyP });

	while (System::Update())
	{
		Circle{ Scene::Center(), 100 }.draw();
	}
}
```


## 5.4 ライセンス情報を表示する
- プログラムの実行中、次のいずれかの方法でライセンス情報を表示します
	- ++f1++ を押す
	- `LicenseManager::ShowInBrowser()` を呼ぶ
- Siv3D に関連するライセンス情報が Web ブラウザで表示されます

??? example "ライセンス情報を追加する"
	- `LicenseManager::AddLicense()` を使って、ライセンス情報の先頭に新しい項目を追加できます

	```cpp title="ライセンス情報を追加する" hl_lines="5-8"
	# include <Siv3D.hpp>

	void Main()
	{
		LicenseManager::AddLicense({
			.title = U"My game",
			.copyright = U"(C) 2025 My name",
			.text = U"License" });

		while (System::Update())
		{

		}
	}
	```


## 5.5 キー操作で全画面表示にする（Windows のみ）
- Windows では、次のキー操作でウィンドウを全画面表示にできます
	- ++alt+enter++ を押す
	- 再び ++alt+enter++ を押すと元のウィンドウ表示に戻ります
- ゲーム画面の展示やプレゼンテーションをするときに便利です


## 5.6 終了操作を変更する
- `System::Update()` は、アプリケーションを終了させる特別な **ユーザアクション** が実行されると、以降 `false` を返すようになります
- 終了操作のユーザアクションは、`System::SetTerminationTriggers()` に `UserAction` フラグを渡すことで変更できます
	- 複数のユーザアクションを設定する場合は、ビット OR 演算子 `|` を使います
- デフォルトでは、以下の 2 つのユーザアクションが終了操作として設定されています

| ユーザアクション | 説明 |
|:--|:--|
| `UserAction::CloseButtonClicked` | ウィンドウの閉じるボタンを押す |
| `UserAction::EscapeKeyDown` | ++esc++ を押す |


### 5.6.1 ++esc++ で終了しないようにする
- ++esc++ を押しても終了しないようにするには、次のようにします

```cpp hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを閉じるユーザアクションのみを終了操作に設定する。
	System::SetTerminationTriggers(UserAction::CloseButtonClicked);

	while (System::Update())
	{

	}
}
```

### 5.6.2 プログラムによってのみ終了させる
- `System::SetTerminationTriggers()` に `UserAction::NoAction` だけを渡すと、ウィンドウの閉じるボタンや ++esc++ キーは終了操作とみなされなくなります
- その場合、`System::Exit()` を呼び出すか、`Main()` で `return` しない限り、アプリケーションは終了しません

```cpp hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// 終了操作を設定しない。
	System::SetTerminationTriggers(UserAction::NoAction);

	while (System::Update())
	{
		// プログラムの開始から 5 秒経過したら
		if (5.0 <= Scene::Time())
		{
			System::Exit();
		}
	}
}
```


### 5.6.3 デフォルトの終了操作に戻す
- 終了操作をデフォルト設定（ウィンドウを閉じる操作、++esc++ を押す操作）に戻すには、`System::SetTerminationTriggers()` に `UserAction::Default` を渡します
- `UserAction::Default` は `(UserAction::CloseButtonClicked | UserAction::EscapeKeyDown)` と同じです

```cpp hl_lines="8-9"
# include <Siv3D.hpp>

void Main()
{
	// 終了操作を設定しない。
	System::SetTerminationTriggers(UserAction::NoAction);

	// デフォルトの終了操作に戻す。
	System::SetTerminationTriggers(UserAction::Default);

	while (System::Update())
	{

	}
}
```


## 振り返りチェックリスト
- [x] ++print-screen++ または ++f12++ でスクリーンショットが保存されることを学んだ
- [x] スクリーンショットのショートカットキーを変更する方法を学んだ
- [x] ライセンス情報を表示する方法を学んだ
- [x] Windows では ++alt+enter++ でウィンドウを全画面表示できることを学んだ
- [x] プログラムの終了操作をカスタマイズできることを学んだ
