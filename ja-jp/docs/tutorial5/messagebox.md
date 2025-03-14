# 81. メッセージボックス
メッセージボックスを表示し、ユーザの選択を取得する方法を学びます。

## 81.1 メッセージボックスの概要
- メッセージボックス機能を使うと、ユーザに応答を求めるメッセージボックスを表示し、ユーザの選択を取得できます
- メッセージボックスの表示中は、メインスレッドの処理は停止します

### 関数

```cpp title="「OK」ボタンを持つメッセージボックスを表示し、その結果を返す"
MessageBoxResult System::MessageBoxOK(StringView text, MessageBoxStyle style = MessageBoxStyle::Default);

MessageBoxResult System::MessageBoxOK(StringView title, StringView text, MessageBoxStyle style = MessageBoxStyle::Default);
```

- `title`: メッセージボックスのタイトル
- `text`: 本文
- `style`: スタイル
- 戻り値: `MessageBoxResult::OK`

-----------------------------------

```cpp title="「OK」「キャンセル」ボタンを持つメッセージボックスを表示し、その結果を返す"
MessageBoxResult System::MessageBoxOKCancel(StringView text, MessageBoxStyle style = MessageBoxStyle::Default);

MessageBoxResult System::MessageBoxOKCancel(StringView title, StringView text, MessageBoxStyle style = MessageBoxStyle::Default);
```

- `title`: メッセージボックスのタイトル
- `text`: 本文
- `style`: スタイル
- 戻り値: `MessageBoxResult::OK` または `MessageBoxResult::Cancel`

-----------------------------------

```cpp title="「はい」「いいえ」ボタンを持つメッセージボックスを表示し、その結果を返す"
MessageBoxResult System::MessageBoxYesNo(StringView text, MessageBoxStyle style = MessageBoxStyle::Default);

MessageBoxResult System::MessageBoxYesNo(StringView title, StringView text, MessageBoxStyle style = MessageBoxStyle::Default);
```

- `title`: メッセージボックスのタイトル
- `text`: 本文
- `style` : スタイル
- 戻り値: `MessageBoxResult::Yes` または `MessageBoxResult::No`

### 列挙型

#### MessageBoxResult
- メッセージボックスに対するユーザの操作を表す定数です
- プラットフォームによっては、ボタンを選択せずにメッセージボックスを閉じることができる場合があります

| 値 | 説明 |
|--|--|
| `OK` | 「OK」が押された |
| `Cancel` | 「キャンセル」が押されたか、メッセージボックスが閉じられた |
| `Yes` | 「はい」が押された |
| `No` | 「いいえ」が押された |

#### MessageBoxStyle
- メッセージボックスのスタイルを表す定数です
- プラットフォームによっては特定のスタイルが存在しない場合があり、その場合は通常のスタイルが使われます

| 値 | 説明 |
|--|--|
| `Default` | 通常のスタイル |
| `Info` | 情報を伝えるスタイル |
| `Warning` | 警告を伝えるスタイル |
| `Error` | 深刻なエラーを伝えるスタイル |
| `Question` | クエスチョンマークのスタイル |


## 81.2 （サンプル）一定時間が経過でプログラムを終了する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial5/messagebox/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 5 秒間のカウントダウンタイマー
	Timer timer{ 5s, StartImmediately::Yes };

	while (System::Update())
	{
		ClearPrint();

		// 残り時間を表示する
		Print << U"残り " << timer.format(U"mm:ss");

		// タイマーが 0 に到達したら
		if (timer.reachedZero())
		{
			// OK のメッセージボックスを表示する
			System::MessageBoxOK(U"体験版の終了", U"体験版で遊べるのはここまでです。");

			// プログラムを終了する
			break;
		}
	}
}
```


## 81.3 （サンプル）ウィンドウの閉じるボタンを押したときに終了するか確認する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial5/messagebox/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// ユーザ操作でアプリが終了しないようにする
	System::SetTerminationTriggers(UserAction::NoAction);

	while (System::Update())
	{
		// ウィンドウの閉じるボタンが押されたら
		if (System::GetUserActions() & UserAction::CloseButtonClicked)
		{
			// Yes か No のメッセージボックスを表示する
			const MessageBoxResult result = System::MessageBoxYesNo(U"アプリケーションを終了しますか？");

			// Yes が選ばれたら
			if (result == MessageBoxResult::Yes)
			{
				// プログラムを終了する
				break;
			}
		}
	}
}
```


## 81.4 （サンプル）起動時に前回のセーブデータを読み込むか確認する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial5/messagebox/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// セーブファイルのパス
	constexpr FilePathView SaveDataPath = U"save.txt";

	// 読み込んだセーブデータ
	String saveData;

	// もし前回のデータが存在すれば
	if (FileSystem::Exists(SaveDataPath))
	{
		// Yes か No のメッセージボックスを表示する
		const MessageBoxResult result = System::MessageBoxYesNo(U"前回のデータが見つかりました。読み込んでそこから再開しますか？");

		// Yes が選ばれたら
		if (result == MessageBoxResult::Yes)
		{
			// セーブファイルから文字列を読み込む
			saveData = TextReader{ SaveDataPath }.readAll();
		}
	}

	// セーブデータが読み込まれていたら
	if (saveData)
	{
		Print << U"前回のセーブデータ: " << saveData;
	}
	else
	{
		Print << U"新規データ";
	}

	while (System::Update())
	{

	}

	// セーブデータ（現在の日付と時刻）をセーブファイルに書き込んでから終了する
	TextWriter{ SaveDataPath }.writeln(DateTime::Now());
}
```


## 81.5 （サンプル）作業内容を保存するか確認する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial5/messagebox/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// ユーザ操作でアプリが終了しないようにする
	System::SetTerminationTriggers(UserAction::NoAction);

	// セーブファイルのパス
	constexpr FilePathView SaveDataPath = U"hsv.save";

	// 背景色
	HSV hsv = ColorF{ 0.8, 0.9, 1.0 };

	// セーブファイルがあればそこから色を読み込む
	if (FileSystem::Exists(SaveDataPath))
	{
		Deserializer<BinaryReader> reader{ SaveDataPath };
		reader(hsv);
	}

	// 現在選択している色が保存されているか
	bool saved = true;

	while (System::Update())
	{
		// 作業内容が未保存の場合、ウィンドウのタイトルで知らせる
		Window::SetTitle(saved ? U"色の選択" : U"* 色の選択 [未保存]");

		// 背景色を設定する
		Scene::SetBackground(hsv);

		// カラーピッカーで色を選択する
		if (SimpleGUI::ColorPicker(hsv, Vec2{ 40,40 }))
		{
			// 変更があれば未保存状態にする
			saved = false;
		}

		// 未保存の場合、「色を保存する」ボタンを表示
		if (SimpleGUI::Button(U"色を保存する", Vec2{ 240, 40 }, unspecified, (not saved))) // ボタンが押されたら
		{
			// セーブファイルに色を保存
			Serializer<BinaryWriter> writer{ SaveDataPath };
			writer(hsv);

			// 保存済みにする
			saved = true;
		}

		// ウィンドウの閉じるボタンが押されたら
		if (System::GetUserActions() & UserAction::CloseButtonClicked)
		{
			if (saved) // 保存済みなら
			{
				// 何もせず終了する
				break;
			}
			else // 未保存なら
			{
				// OK か キャンセル のメッセージボックスを表示する
				const MessageBoxResult result = System::MessageBoxOKCancel(U"色の選択", U"色を保存せずにアプリケーションを終了しますか？");

				// OK が選ばれたら
				if (result == MessageBoxResult::OK)
				{
					// プログラムを終了する
					break;
				}
			}
		}
	}
}
```
