# 4. アプリケーションの基本操作
Siv3D アプリケーションの基本的な操作方法について学びます。

## 4.1 アプリケーションの基本操作

### 4.1.1 プログラムを終了してウィンドウを閉じる
Siv3D アプリケーションは、次のいずれかの方法で終了します。

- ウィンドウを閉じる
- ++esc++ を押す
- プログラムが `System::Exit()` を呼ぶ

この動作をカスタマイズする方法は、チュートリアル 4.2 を参照してください。

### 4.1.2 スクリーンショットを保存する
Siv3D アプリケーションは、次のいずれかの方法でスクリーンショットを保存します。

- ++print-screen++ を押す
- ++f12++ を押す

スクリーンショットの保存先は OS によって異なります。

| OS | 保存先 |
| --- | --- |
| Windows | 実行ファイルと同じディレクトリの `Screenshot` フォルダ |
| macOS | ピクチャフォルダ内の `Screenshot` フォルダ |
| Linux | 実行ファイルと同じディレクトリの `Screenshot` フォルダ |

??? example "Windows での画面の録画"
	Windows 10, 11 では、Windows に標準で組み込まれている画面の録画機能を使うことで、アプリケーションの動作を動画に保存することができます。詳しくは下記の記事を参照してください。
	
	- [Xbox Game Bar](../../tools/gamebar){target="_blank"}


??? example "スクリーンショット保存のショートカットキーのカスタマイズ"
	`ScreenCapture::SetShortcutKeys()` を使って、実行中のプログラムのスクリーンショットの保存のショートカットキーをカスタマイズできます。

	```cpp
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


### 4.1.3 ライセンス情報を表示する
Siv3D アプリケーションは、次のいずれかの方法でライセンス情報を表示します。

- ++f1++ を押す
- `LicenseManager::ShowInBrowser()` を呼ぶ

この操作により、アプリケーションで使われているサードパーティ・ソフトウェアのライセンス情報を Web ブラウザで表示します。

??? example "ライセンス情報を追加する"
	`LicenseManager::AddLicense()` を使って、ライセンス情報を追加することができます。

	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		LicenseManager::AddLicense({
			.title = U"My game",
			.copyright = U"(C) 2023 My name",
			.text = U"License" });

		while (System::Update())
		{

		}
	}
	```

### 4.1.4 全画面表示にする（Windows 版）
Windows では、次の方法でウィンドウを全画面表示にすることができます。

- ++alt+enter++ を押す

再び ++alt+enter++ を押すと元のウィンドウ表示に戻ります。


## 4.2 アプリケーションの終了操作をカスタマイズする
`System::Update()` は、アプリケーションを終了させる特別な **ユーザアクション** が実行されると、以降ずっと `false` を返すようになります。

デフォルトでは、以下の 2 つのユーザアクションと `System::Exit()` の呼び出しが、アプリケーションを終了させる操作として設定されています。

| ユーザアクション定数 | 説明 |
|:--|:--|
| `UserAction::CloseButtonClicked` | ウィンドウの閉じるボタンを押す |
| `UserAction::EscapeKeyDown` | ++esc++ を押す |

アプリケーションを終了させるユーザアクションは、`System::SetTerminationTriggers()` に `UserAction` の組み合わせを渡すことでカスタマイズできます。組み合わせはビット和で表現します。

### 4.2.1 ウィンドウを閉じる操作のみを終了操作に設定する
++esc++ を押してもアプリケーションを終了しないようにするには、`System::SetTerminationTriggers()` に `UserAction::CloseButtonClicked` のみを渡します。

```cpp hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを閉じる操作のみを終了操作に設定する。
	System::SetTerminationTriggers(UserAction::CloseButtonClicked);

	while (System::Update())
	{

	}
}
```

これで、++esc++ を押しても終了しなくなりました。


### 4.2.2 プログラムによってのみ終了させる
ウィンドウの閉じるボタンを押したり、++esc++ を押したりしても終了しないようにするには、`System::SetTerminationTriggers()` に `UserAction::NoAction` を渡します。この場合、`System::Exit()` を呼び出すか、`Main()` で `return` しない限り、アプリケーションを終了させることはできません。

```cpp hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// 終了操作を設定しない。
	System::SetTerminationTriggers(UserAction::NoAction);

	while (System::Update())
	{
		// 実行時間が 5 秒以上経過したら
		if (5.0 <= Scene::Time())
		{
			System::Exit();
		}
	}
}
```

これで、ウィンドウの閉じるボタンを押したり、++esc++ を押したりしても終了しなくなりました。実行時間が 5 秒以上経過したら、`System::Exit()` を呼び出して終了します。


### 4.2.3 デフォルトの終了操作に戻す
デフォルトの終了操作（ウィンドウを閉じる操作と ++esc++ を押す操作）に戻すには、`System::SetTerminationTriggers()` に `UserAction::Default` を渡します。

`UserAction::Default` は、`(UserAction::CloseButtonClicked | UserAction::EscapeKeyDown)` と同じです。

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

これで、デフォルトの終了操作に戻りました。


## 振り返りチェックリスト
- [x] プログラムを終了してウィンドウを閉じる方法を学んだ
- [x] スクリーンショットの撮影方法を学んだ
- [x] ライセンス情報を表示する方法を学んだ
- [x] アプリケーションの終了操作をカスタマイズする方法を学んだ
