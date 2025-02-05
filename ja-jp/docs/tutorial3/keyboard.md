# 42. キーボード入力
キーボード入力を処理する方法を学びます。

## 42.1 キーの入力状態を調べる
- キーボードの各キーに対応する　`Input` 型の定数が用意されています
- おもな定数名は次の表のとおりです
	- 下表以外のキーは [`<Siv3D/Keyboard.hpp>` :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Keyboard.hpp){:target="_blank"} を参照してください

| キー | 定数名 |
| --- | --- |
| A, B, C, ... | `KeyA`, `KeyB`, `KeyC`, ... |
| 1, 2, 3, ... | `Key1`, `Key2`, `Key3`, ... |
| F1, F2, F3, ... | `KeyF1`, `KeyF2`, `KeyF3`, ... |
| ↑, ↓, ←, → | `KeyUp`, `KeyDown`, `KeyLeft`, `KeyRight` |
| スペースキー | `KeySpace` |
| エンターキー | `KeyEnter` |
| バックスペースキー | `KeyBackspace` |
| Tab キー | `KeyTab` |
| エスケープキー | `KeyEscape` |
| Page up, Page down | `KeyPageUp`, `KeyPageDown` |
| Delete キー | `KeyDelete` |
| テンキーの 0, 1, 2, ... | `KeyNum0`, `KeyNum1`, `KeyNum2`, ... |
| Shift キー | `KeyShift` |
| 左シフト, 右シフト | `KeyLShift`, `KeyRShift` |
| Ctrl キー | `KeyControl` |
| (macOS) コマンドキー | `KeyCommand` |
| カンマ, ピリオド, スラッシュ | `KeyComma`, `KeyPeriod`, `KeySlash` |

- `Input` 型は次のようなメンバ関数を持ち、現在のフレームでの状態を `bool` 型で返します
	- 例えば、`KeyA.down()` は、++a++ が押された瞬間に `true` を返します

| コード | 押していないとき | 押した瞬間 | 押し続けている | 離した瞬間 | 離し続けている |
|:--:|:--:|:--:|:--:|:--:|:--:|
| `.down()` | false | **✔ true** | false | false | false |
| `.pressed()` | false | **✔ true** | **✔ true** | false | false |
| `.up()` | false | false | false | **✔ true** | false |

```cpp
# include <Siv3D.hpp>

Vec2 GetMove(double deltaTime)
{
	const double delta = (deltaTime * 200);

	Vec2 move{ 0, 0 };

	if (KeyLeft.pressed())
	{
		move.x -= delta;
	}

	if (KeyRight.pressed())
	{
		move.x += delta;
	}

	if (KeyUp.pressed())
	{
		move.y -= delta;
	}

	if (KeyDown.pressed())
	{
		move.y += delta;
	}

	return move;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		// 上下左右キーで移動する
		const Vec2 move = GetMove(Scene::DeltaTime());

		pos += move;

		// [C] キーが押されたら中央に戻る
		if (KeyC.down())
		{
			pos = Vec2{ 400, 300 };
		}

		pos.asCircle(50).draw(ColorF{ 0.2 });
	}
}
```


## 42.2 特殊な操作が割り当てられているキー
- Siv3D では、一部のキーに特殊な操作が割り当てられています

### 42.2.1 エスケープキー
- ++esc++ はデフォルトで、アプリケーションを終了させるユーザアクションとして割り当てられています（**チュートリアル 3.5**）
- ++esc++ を押してもアプリケーションを終了しないようにするには、`System::SetTerminationTriggers()` に `UserAction::CloseButtonClicked` のみを渡します（**チュートリアル 5.6**）

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを閉じるユーザアクションのみを終了操作に設定する
	System::SetTerminationTriggers(UserAction::CloseButtonClicked);

	while (System::Update())
	{

	}
}
```

### 42.2.2 PrintScreen キーと F12 キー
- ++print-screen++ または ++f12++ を押すと、スクリーンショットが保存されます（**チュートリアル 5.2**）
	- ただし、Visual Studio でのデバッグ中は、++f12++ が別の機能に割り当てられているため使えません
- ++f12++ を押してもスクリーンショットを保存しないようにするには、`ScreenCapture::SetShortcutKeys()` に `{ KeyPrintScreen }` のみを渡します（**チュートリアル 5.3**）

```cpp
# include <Siv3D.hpp>

void Main()
{
	// [PrintScreen] キーが押されたときのみスクリーンショットを保存するよう設定する
	ScreenCapture::SetShortcutKeys({ KeyPrintScreen });

	while (System::Update())
	{
		Circle{ 400, 300, 100 }.draw();
	}
}
```

### 42.2.3 F1 キー
- ++f1++ を押すと、プログラムのライセンス情報を表示します（**チュートリアル 5.4**）
- +++f1++ を押してもライセンス情報を表示しないようにするには、`LicenseManager::DisableDefaultTrigger()` を呼びます
- その場合、代わりに `LicenseManager::ShowInBrowser()` を使ってブラウザでライセンス情報を表示する手段を提供してください

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 自分のアプリケーションのライセンス情報を追加する
	LicenseManager::AddLicense({
		.title = U"My game",
		.copyright = U"(C) 2025 My name",
		.text = U"License" });

	// [F1] キーを押してもライセンス情報を表示しないようにする
	LicenseManager::DisableDefaultTrigger();

	while (System::Update())
	{
		if (SimpleGUI::Button(U"License", Vec2{ 40, 40 }))
		{
			// Web ブラウザでライセンス情報を表示する
			LicenseManager::ShowInBrowser();
		}
	}
}
```

### 42.2.4 Alt + Enter キー（Windows）
- Windows では、アプリケーションの実行中に ++alt+enter++ を押すことで**全画面モード**にできます（**チュートリアル 5.5*）
- このキー操作を無効にするには `Window::SetToggleFullscreenEnabled(false)` を呼びます

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Window::SetToggleFullscreenEnabled(false);

	while (System::Update())
	{
		for (int32 y = 0; y < 10; ++y)
		{
			for (int32 x = 0; x < 10; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.5, 0.7, 0.6 });
				}
			}
		}
	}
}
```


## 42.3 キーが押されている時間
- `Input` の `.pressedDuration()` は、その入力が押され続けている時間を `Duration` 型で返します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/keyboard/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// A キーが押されている時間
		Print << KeyA.pressedDuration();

		// スペースキーが押されている時間
		Print << KeySpace.pressedDuration();

		// スペースキーが 1 秒以上押されていれば
		if (1s <= KeySpace.pressedDuration())
		{
			Print << U"Space";
		}
	}
}
```


## 42.4 キーが押されていた時間
- `.pressedDuration()` は、そのキーの `.up()` が `true` と判定されるフレームまで有効です
- 次のようにして、あるキーが離されたとき、それまで何秒間押され続けていたかを取得できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/keyboard/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// スペースキーが押されていた時間を表示する
		if (KeySpace.up())
		{
			Print << KeySpace.pressedDuration();
		}
	}
}
```


## 42.5 キーの名前
- `Input` の `.name()` は、そのキーの名前を `String` 型で返します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << KeyA.name();
	Print << KeySpace.name();
	Print << KeyLeft.name();
	Print << Key3.name();
	Print << KeyF11.name();

	while (System::Update())
	{

	}
}
```
```txt title="出力"
A
Space
Left
3
F11
```


## 42.6 すべてのキー入力の取得
- `Keyboard::GetAllInputs()` は、現在のフレームで`.down()`, `.pressed()`, `.up()` のいずれかが `true` になっている、アクティブなキーの一覧を `Array<Input>` で返します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/keyboard/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// down() / pressed() / up() のいずれかが true になっているキー一覧を取得する
		const Array<Input> keys = Keyboard::GetAllInputs();

		for (const auto& key : keys)
		{
			Print << key.name() << (key.pressed() ? U" pressed" : U" up");
		}
	}
}
```


## 42.7 キーの組み合わせ（A または B）
- `|` を使って複数のキー定数を組み合わせ、そのいずれかが押されているかどうかの判定ができます

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// [スペース] または [エンター] が押されている
		if ((KeySpace | KeyEnter).pressed())
		{
			Print << U"KeySpace / KeyEnter";
		}
	}
}
```


## 42.8 キーの組み合わせ（A を押しながら B）
- `+` を使って 2 つのキー定数を組み合わせ、左のキーが押されながら右のキーが押されたかどうかを判定できます

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// [Ctrl + C] または [Command + C] が押された
		if ((KeyControl + KeyC).down()
			|| (KeyCommand + KeyC).down())
		{
			Print << U"Ctrl + C / Command + C";
		}
	}
}
```


## 42.9 InputGroup
- `InputGroup` 型は `Input` や、`Input` の `|`, `+` による組み合わせ表現を格納できます

```cpp
# include <Siv3D.hpp>

void Main()
{
	// [Z] [スペース] [Enter] のいずれか
	const InputGroup inputOK = (KeyZ | KeySpace | KeyEnter);

	// [Ctrl] + [C] または [Command] + [C]
	const InputGroup inputCopy = ((KeyControl + KeyC) | (KeyCommand + KeyC));

	while (System::Update())
	{
		if (inputOK.down())
		{
			Print << U"OK";
		}

		if (inputCopy.down())
		{
			Print << U"Copy";
		}
	}
}
```


## 42.10 キーコンフィグ（1）
- 各種操作に `InputGroup` を割り当てることで、操作方法を柔軟にカスタマイズできます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/keyboard/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"🐥"_emoji };

	const Array<String> options
	{
		U"[←] [→] [Space]",
		U"[A] [D] [W]",
		U"[←]/[A] [→]/[D] [Space]/[W]"
	};

	size_t index = 0;

	// 左に移動する操作
	InputGroup inputLeft = KeyLeft;

	// 右に移動する操作
	InputGroup inputRight = KeyRight;

	// ジャンプする操作
	InputGroup inputJump = KeySpace;

	Vec2 pos{ 400, 450 };

	double jumpY = 0.0;

	while (System::Update())
	{
		// 🐥 の移動
		{
			const double deltaTime = Scene::DeltaTime();

			if (inputLeft.pressed())
			{
				pos.x -= (deltaTime * 200);
			}

			if (inputRight.pressed())
			{
				pos.x += (deltaTime * 200);
			}

			if (inputJump.down())
			{
				jumpY = 500.0;
			}

			pos.y = Min((pos.y - deltaTime * jumpY), 450.0);
			jumpY = Max((jumpY - deltaTime * 1000.0), -1000.0);
		}

		// 背景と 🐥 の描画
		{
			Rect{ 800, 500 }.draw(Arg::top(0.1, 0.4, 0.8), Arg::bottom(0.4, 0.7, 1.0));
			Rect{ 0, 500, 800, 100 }.draw(ColorF{ 0.2, 0.5, 0.3 });
			texture.drawAt(pos);
		}

		// キーコンフィグ
		if (SimpleGUI::RadioButtons(index, options, Vec2{ 40, 40 }))
		{
			if (index == 0)
			{
				inputLeft = KeyLeft;
				inputRight = KeyRight;
				inputJump = KeySpace;
			}
			else if (index == 1)
			{
				inputLeft = KeyA;
				inputRight = KeyD;
				inputJump = KeyW;
			}
			else
			{
				inputLeft = (KeyLeft | KeyA);
				inputRight = (KeyRight | KeyD);
				inputJump = (KeySpace | KeyW);
			}
		}
	}
}
```


## 42.11 キーコンフィグ（2）
- 実際に押されているキーを検出してキーコンフィグを設定したい場合は、`Keyboard::GetAllInputs()` を使います

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/keyboard/11.png)

```cpp
# include <Siv3D.hpp>

struct KeyConfig
{
	String name;

	Input defaultInput;

	Input input;
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	Array<KeyConfig> keyConfigs;
	keyConfigs << KeyConfig{ U"Up", KeyW, KeyW };
	keyConfigs << KeyConfig{ U"Down", KeyS, KeyS };
	keyConfigs << KeyConfig{ U"Left", KeyA, KeyA };
	keyConfigs << KeyConfig{ U"Right", KeyD, KeyD };

	Optional<size_t> selectedKeyConfig;

	while (System::Update())
	{
		if (MouseL.down())
		{
			selectedKeyConfig.reset();
		}

		for (size_t i = 0; i < keyConfigs.size(); ++i)
		{
			const KeyConfig& keyConfig = keyConfigs[i];
			const Rect rect{ 40, (40 + i * 60), 400, 50 };

			if (rect.leftClicked())
			{
				selectedKeyConfig = i;
			}

			if (selectedKeyConfig == i)
			{
				for (const auto& key : Keyboard::GetAllInputs())
				{
					if (key.down())
					{
						keyConfigs[selectedKeyConfig.value()].input = key;
						break;
					}
				}
			}

			rect.rounded(6).draw();
			font(keyConfig.name).draw(30, Arg::leftCenter = rect.leftCenter().movedBy(30, 0), ColorF{ 0.2 });
			font(keyConfig.input).draw(30, Arg::center = rect.leftCenter().movedBy(280, 0), ColorF{ 0.2 });

			if (selectedKeyConfig == i)
			{
				rect.rounded(6).drawFrame(0, 5, ColorF{ 0.1, 0.5, 1.0 });
			}
		}

		if (SimpleGUI::Button(U"Reset", Vec2{ 40, 300 }))
		{
			for (auto& keyConfig : keyConfigs)
			{
				keyConfig.input = keyConfig.defaultInput;
			}
		}
	}
}
```


## 42.12 テキスト入力
- `TextInput::UpdateText()` に `String` 型の変数を渡すことで、キーボード入力に基づいたテキスト入力処理ができます
- 日本語入力時、未変換のテキストを得たい場合は `TextInput::GetEditingText()` を使います

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/keyboard/12.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	String text;

	const Rect box{ 50, 50, 700, 300 };

	while (System::Update())
	{
		// キーボードからテキストを入力する
		TextInput::UpdateText(text);

		// 未変換の文字入力を取得する
		const String editingText = TextInput::GetEditingText();

		box.draw(ColorF{ 0.3 });

		font(text + U'|' + editingText).draw(30, box.stretched(-20));
	}
}
```


## 42.13 IME を無効化する（Windows 版）
- Windows で日本語入力の IME を無効化する場合、`Platform::Windows::TextInput::DisableIME()` を呼びます

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// IME を無効化する
	Platform::Windows::TextInput::DisableIME();

	const Font font{ FontMethod::MSDF, 48 };

	String text;

	const Rect box{ 50, 50, 700, 300 };

	while (System::Update())
	{
		// キーボードからテキストを入力する
		TextInput::UpdateText(text);

		font(text).draw(30, box.stretched(-20));
	}
}
```
