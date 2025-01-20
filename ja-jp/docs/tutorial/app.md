# 5. アプリの基本操作
Siv3D アプリケーションの基本的な操作について学びます。

## 5.1 プログラムを終了してウィンドウを閉じる
- 次のいずれかの操作で、実行中のプログラムを終了できます（チュートリアル 3.5 参照）
	1. ウィンドウの閉じるボタンを押す
	2. ++esc++ キーを押す
	3. プログラムの中で `System::Exit()` を呼び出す
- プログラムの終了と同時に、ウィンドウも閉じられます
- 終了操作をカスタマイズする方法は、チュートリアル 5.4 を参照してください

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

??? example "Windows での画面の録画"
	- Windows 10, 11 には、OS 自体に画面の録画機能が搭載されています
	- 詳しくは [Xbox Game Bar](../tools/gamebar.md){target="_blank"} を参照してください


## 5.3 スクリーンショット用のショートカットキーを変更する
- 実行中のプログラムのスクリーンショットの保存のショートカットキーをカスタマイズするには、`ScreenCapture::SetShortcutKeys({ キーのリスト })` を使います

```cpp title="スクリーンショットのショートカットキーを変更する"
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

```cpp title="スクリーンショットのショートカットキーを変更する"
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

	```cpp title="ライセンス情報を追加する"
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


## 5.6 終了操作を変更する



## 振り返りチェックリスト
- [x] スクリーンショットの撮影方法を学んだ
- [x] ライセンス情報を表示する方法を学んだ
- [x] キー操作で全画面表示にする方法を学んだ（Windows のみ）
- [x] プログラムの終了操作をカスタマイズする方法を学んだ
