# メッセージボックス

## 1. 概要

### 1.1 機能の説明
メッセージボックス機能を使うと、メインウィンドウとは別のウィンドウとして、ユーザに応答を求めるメッセージボックスを表示し、ユーザの選択を取得できます。メッセージボックスの表示中は、メインループのプログラムの進行がブロックされます。

### 1.2 注意事項
- プラットフォームによっては、フルスクリーンモード中にメッセージボックスを使用すると、メッセージボックスがメインウィンドウの裏に隠れ、ユーザが操作不能に陥る場合があります。ウィンドウがフルスクリーンの際は、代わりにシーン内の描画でメッセージボックスを再現するなど、メッセージボックス機能を使わないことを推奨します

## 2. メッセージボックスの API

### 関数

```cpp title="「OK」ボタンを持つメッセージボックスを表示し、その結果を返します。"
MessageBoxResult System::MessageBoxOK(StringView text, MessageBoxStyle style = MessageBoxStyle::Default);

MessageBoxResult System::MessageBoxOK(StringView title, StringView text, MessageBoxStyle style = MessageBoxStyle::Default);
```

`title`
:   メッセージボックスのタイトル

`text`
:   本文

`style`
:   スタイル

`戻り値`
:   `MessageBoxResult::OK`


-----------------------------------


```cpp title="「OK」「キャンセル」ボタンを持つメッセージボックスを表示し、その結果を返します。"
MessageBoxResult System::MessageBoxOKCancel(StringView text, MessageBoxStyle style = MessageBoxStyle::Default);

MessageBoxResult System::MessageBoxOKCancel(StringView title, StringView text, MessageBoxStyle style = MessageBoxStyle::Default);
```

`title`
:   メッセージボックスのタイトル

`text`
:   本文

`style`
:   スタイル

`戻り値`
:   `MessageBoxResult::OK` または `MessageBoxResult::Cancel`


-----------------------------------


```cpp title="「はい」「いいえ」ボタンを持つメッセージボックスを表示し、その結果を返します。"
MessageBoxResult System::MessageBoxYesNo(StringView text, MessageBoxStyle style = MessageBoxStyle::Default);

MessageBoxResult System::MessageBoxYesNo(StringView title, StringView text, MessageBoxStyle style = MessageBoxStyle::Default);
```

`title`
:   メッセージボックスのタイトル

`text`
:   本文

`style`
:   スタイル

`戻り値`
:   `MessageBoxResult::Yes` または `MessageBoxResult::No`


### 列挙型

#### MessageBoxResult
メッセージボックスに対するユーザの操作を表す定数です。プラットフォームによっては、ボタンを選択せずにメッセージボックスを閉じることができる場合があります。

| 値 | 説明 |
|--|--|
| `OK` | 「OK」が押された |
| `Cancel` | 「キャンセル」が押されたか、メッセージボックスが閉じられた |
| `Yes` | 「はい」が押された |
| `No` | 「いいえ」が押された |

#### MessageBoxStyle
メッセージボックスのスタイルを表す定数です。プラットフォームによってはスタイルが存在しない場合があり、その場合は通常のスタイルが使われます。

| 値 | 説明 |
|--|--|
| `Default` | 通常のスタイル |
| `Info` | 情報を伝えるスタイル |
| `Warning` | 警告を伝えるスタイル |
| `Error` | 深刻なエラーを伝えるスタイル |
| `Question` | クエスチョンマークのスタイル |



## 3. メッセージボックスのサンプル

### 3.1 一定時間が経過したらプログラムを終了する

```cpp
# include <Siv3D.hpp>

void Main()
{
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
			return;
		}
	}
}
```


### 3.2 ウィンドウの閉じるボタンを押したときに終了するか確認する

```cpp
# include <Siv3D.hpp>

void Main()
{
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
				return;
			}
		}
	}
}
```

### 3.3 起動時に前回のセーブデータを読み込むか確認する

```cpp
# include <Siv3D.hpp>

void Main()
{
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


### 3.4 作業内容を保存するか確認する

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ユーザ操作でアプリが終了しないようにする
	System::SetTerminationTriggers(UserAction::NoAction);

	// セーブファイルのパス
	constexpr FilePathView SaveDataPath = U"save-hsv.txt";

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
				return; // 何もせず終了する
			}
			else // 未保存なら
			{
				// OK か キャンセル のメッセージボックスを表示する
				const MessageBoxResult result = System::MessageBoxOKCancel(U"色の選択", U"色を保存せずにアプリケーションを終了しますか？");

				// OK が選ばれたら
				if (result == MessageBoxResult::OK)
				{
					// プログラムを終了する
					return;
				}
			}
		}
	}
}
```

### 3.5 メッセージボックス相当の機能を自作する

```cpp
# include <Siv3D.hpp>

namespace s3dx
{
	class SceneMessageBoxImpl
	{
	public:

		static constexpr Size MessageBoxSize{ 360, 240 };

		static constexpr Size MessageBoxButtonSize{ 120, 40 };

		static constexpr ColorF MessageBoxBackgroundColor{ 0.96 };

		static constexpr ColorF MessageBoxActiveButtonColor{ 1.0 };

		static constexpr ColorF MessageBoxTextColor{ 0.11 };

		SceneMessageBoxImpl()
		{
			System::SetTerminationTriggers(UserAction::NoAction);
			Scene::SetBackground(ColorF{ 0.11 });
		}

		~SceneMessageBoxImpl()
		{
			System::SetTerminationTriggers(m_triggers);
			Scene::SetBackground(m_bgColor);
		}

		MessageBoxResult show(StringView text, const std::pair<String, MessageBoxResult>& button) const
		{
			while (System::Update())
			{
				drawMessageBox(text);
				m_buttonC.draw(m_buttonC.mouseOver() ? MessageBoxActiveButtonColor : MessageBoxBackgroundColor).drawFrame(0, 1, MessageBoxTextColor);
				m_font(button.first).drawAt(m_buttonC.center().moveBy(0, -1), MessageBoxTextColor);

				if (m_buttonC.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);

					if (MouseL.down())
					{
						break;
					}
				}
			}

			return button.second;
		}

		MessageBoxResult show(const StringView text, const std::pair<String, MessageBoxResult>& button0, const std::pair<String, MessageBoxResult>& button1) const
		{
			MessageBoxResult result = MessageBoxResult::Cancel;

			while (System::Update())
			{
				drawMessageBox(text);
				m_buttonL.draw(m_buttonL.mouseOver() ? MessageBoxActiveButtonColor : MessageBoxBackgroundColor).drawFrame(0, 1, MessageBoxTextColor);
				m_buttonR.draw(m_buttonR.mouseOver() ? MessageBoxActiveButtonColor : MessageBoxBackgroundColor).drawFrame(0, 1, MessageBoxTextColor);
				m_font(button0.first).drawAt(m_buttonL.center().moveBy(0, -1), MessageBoxTextColor);
				m_font(button1.first).drawAt(m_buttonR.center().moveBy(0, -1), MessageBoxTextColor);

				if (m_buttonL.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);

					if (MouseL.down())
					{
						result = button0.second;
						break;
					}
				}
				else if (m_buttonR.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);

					if (MouseL.down())
					{
						result = button1.second;
						break;
					}
				}
			}

			return result;
		}

	private:

		Transformer2D m_tr{ Mat3x2::Identity(), Mat3x2::Identity(), Transformer2D::Target::SetLocal };

		ScopedRenderStates2D m_rs{ BlendState::Default2D, SamplerState::Default2D, RasterizerState::Default2D };

		uint32 m_triggers = System::GetTerminationTriggers();

		ColorF m_bgColor = Scene::GetBackground();

		Vec2 m_pos = ((Scene::Size() - MessageBoxSize) * 0.5);

		RectF m_messageBoxRect{ m_pos, MessageBoxSize };

		RectF m_buttonC = RectF{ Arg::bottomCenter(m_messageBoxRect.bottomCenter().movedBy(0, -20)), MessageBoxButtonSize };

		RectF m_buttonL = RectF{ Arg::bottomCenter(m_messageBoxRect.bottomCenter().movedBy(-80, -20)), MessageBoxButtonSize };

		RectF m_buttonR = RectF{ Arg::bottomCenter(m_messageBoxRect.bottomCenter().movedBy(80, -20)), MessageBoxButtonSize };

		Font m_font = SimpleGUI::GetFont();

		void drawMessageBox(StringView text) const
		{
			m_messageBoxRect.draw(MessageBoxBackgroundColor).stretched(-5).drawFrame(1, 0, MessageBoxTextColor);
			m_font(text).draw(14, m_messageBoxRect.stretched(-20, -20, -80, -20), MessageBoxTextColor);
		}
	};

	inline MessageBoxResult SceneMessageBoxOK(StringView text)
	{
		return SceneMessageBoxImpl{}.show(text, { U"OK", MessageBoxResult::OK });
	}

	[[nodiscard]]
	inline MessageBoxResult SceneMessageBoxOKCancel(StringView text)
	{
		return SceneMessageBoxImpl{}.show(text, { U"OK", MessageBoxResult::OK }, { U"キャンセル", MessageBoxResult::Cancel });
	}

	[[nodiscard]]
	inline MessageBoxResult SceneMessageBoxYesNo(StringView text)
	{
		return SceneMessageBoxImpl{}.show(text, { U"はい", MessageBoxResult::Yes }, { U"いいえ", MessageBoxResult::No });
	}
}

void Main()
{
	// 5 秒間のカウントダウンタイマー
	Timer timer{ 2s, StartImmediately::Yes };

	while (System::Update())
	{
		ClearPrint();

		// 残り時間を表示する
		Print << U"残り " << timer.format(U"mm:ss");

		// タイマーが 0 に到達したら
		if (timer.reachedZero())
		{
			// OK のメッセージボックスを表示する
			s3dx::SceneMessageBoxOK(U"体験版で遊べるのはここまでです。");

			// プログラムを終了する
			return;
		}
	}
}
```
