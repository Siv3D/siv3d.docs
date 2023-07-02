# 33. キーボード入力
キーボードの入力を処理する方法を学びます。

## 33.1 キーの入力状態を調べる
キーボードのキーには「Key～」と名付けられた `Input` 型の値が割り当てられています。

!!! info "主なキー名"
    - ++a++ , ++b++ , ++c++ , ... は `KeyA`, `KeyB`, `KeyC` , ...
    - ++1++ , ++2++ , ++3++ , ... は `Key1`, `Key2`, `Key3`, ...
    - ++f1++ , ++f2++ , ++f3++ , ... は `KeyF1`, `KeyF2`, `KeyF3`, ...
    - ++up++ , ++down++ , ++left++ , ++right++ は `KeyUp`, `KeyDown`, `KeyLeft`, `KeyRight`
    - ++space++ は `KeySpace`
    - ++enter++ は `KeyEnter`
    - ++backspace++ は `KeyBackspace`
    - ++tab++ キーは `KeyTab`
    - ++esc++ キーは `KeyEscape`
    - ++page-up++ , ++page-down++ は `KeyPageUp`, `KeyPageDown`
    - ++delete++ キーは `KeyDelete`
    - Numpad の ++num0++ , ++num1++ , ++num2++ , ... は `KeyNum0`, `KeyNum1`, `KeyNum2`, ...
    - ++shift++ は `KeyShift`
    - ++left-shift++ (左シフト), ++right-shift++ (右シフト) は `KeyLShift`, `KeyRShift`
    - ++control++ は `KeyControl`
    - (macOS) ++command++ は `KeyCommand`
    - ++comma++ , ++period++ , ++slash++ キーは `KeyComma`, `KeyPeriod`, `KeySlash`
    - 上記以外のキーは [`<Siv3D/Keyboard.hpp>`](https://github.com/Siv3D/OpenSiv3D/blob/main/Siv3D/include/Siv3D/Keyboard.hpp) を参照

`Input` 型の値はメンバ関数を持ち、押した瞬間であるかを `.down()`, 押し続けているかを `.pressed()`, 離した瞬間であるかを `.up()` を使って `bool` 値で取得できます。

| 関数 | 押していないとき | 押した瞬間 | 押し続けている | 離した瞬間 | 離し続けている |
|:--:|:--:|:--:|:--:|:--:|:--:|
| `.down()` | false | **✔ true** | false | false | false |
| `.pressed()` | false | **✔ true** | **✔ true** | false | false |
| `.up()` | false | false | false | **✔ true** | false |

```cpp
# include <Siv3D.hpp>

void Main()
{
	Vec2 pos = Scene::Center();

	while (System::Update())
	{
		const double delta = (Scene::DeltaTime() * 200);

		// 上下左右キーで移動する
		if (KeyLeft.pressed())
		{
			pos.x -= delta;
		}

		if (KeyRight.pressed())
		{
			pos.x += delta;
		}

		if (KeyUp.pressed())
		{
			pos.y -= delta;
		}

		if (KeyDown.pressed())
		{
			pos.y += delta;
		}

		// [C] キーが押されたら中央に戻る
		if (KeyC.down())
		{
			pos = Scene::Center();
		}

		pos.asCircle(50).draw();
	}
}
```


## 33.2 Siv3D で特殊な操作が割り当てられているキー
一部のキーは Siv3D によって特殊な操作が割り当てられています。

### 33.2.1 エスケープキー
++esc++ はデフォルトで、アプリケーションを終了させるユーザアクションとして割り当てられています（チュートリアル 4.2 参照）。

++esc++ を押してもアプリケーションを終了しないようにするには、`System::SetTerminationTriggers()` に `UserAction::CloseButtonClicked` のみを渡します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを閉じる操作のみを終了操作に設定する
	System::SetTerminationTriggers(UserAction::CloseButtonClicked);

	while (System::Update())
	{

	}
}
```


### 33.2.2 PrintScreen キーと F12 キー
++print-screen++ または ++f12++ を押すと、スクリーンショットが保存されます（チュートリアル 4.1.2 参照）。

++f12++ をゲームやアプリの操作に割り当て、++f12++ を押してもスクリーンショットを保存しないようにするには、`ScreenCapture::SetShortcutKeys()` に `{ KeyPrintScreen }` のみを渡します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// [PrintScreen] キーが押されたときのみスクリーンショットを保存するよう設定する
	ScreenCapture::SetShortcutKeys({ KeyPrintScreen });

	while (System::Update())
	{
		Circle{ Scene::Center(), 100 }.draw();
	}
}
```


### 33.2.3 F1 キー
++f1++ を押すと、プログラムのライセンス情報を表示します。

++f1++ をゲームやアプリの操作に割り当て、++f1++ を押してもライセンス情報を表示しないようにするには、`LicenseManager::DisableDefaultTrigger()` を呼びます。その場合、代わりに `LicenseManager::ShowInBrowser()` を使ってブラウザでライセンス情報を表示する手段を提供すべきです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 自分のアプリケーションのライセンス情報を追加する
	LicenseManager::AddLicense({
		.title = U"My game",
		.copyright = U"(C) 2023 My name",
		.text = U"License" });

	// [F1] キーでライセンス情報を表示しないようにする
	LicenseManager::DisableDefaultTrigger();

	while (System::Update())
	{
		// ボタンを押すとライセンス情報を表示する
		if (SimpleGUI::Button(U"License", Vec2{ 40, 40 }))
		{
			LicenseManager::ShowInBrowser();
		}
	}
}
```


### 33.2.4 Alt + Enter キー（Windows 版）
Windows 版では、アプリケーションの実行中に ++alt+enter++ を押すことで**全画面モード**にできます（チュートリアル 32.15 参照）。このキー操作を無効にするには `Window::SetToggleFullscreenEnabled(false)` を呼びます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::SetToggleFullscreenEnabled(false);

	while (System::Update())
	{
		// 100px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ x * 100, y * 100, 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}
	}
}
```


## 33.3 キーが押されている時間を調べる
`Input` の `.pressedDuration()` は、その入力が押され続けている時間を `Duration` 型の値で返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/keyboard/3a.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// A キーが押されている時間
		Print << KeyA.pressedDuration();

		// スペースキーが 1 秒以上押されていれば
		if (1s <= KeySpace.pressedDuration())
		{
			Print << U"Space";
		}
	}
}
```

押され続けている時間は `.up()` が `true` になるフレームまで有効です。`.up()` されたときに `.pressedDuration()` を調べると、そのキーが離されるまで何秒間押され続けていたかを取得できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/keyboard/3b.png)

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


## 33.4 キーの名前を取得する
`Input` の `.name()` は、そのキーの名前を `String` 型の値で返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/keyboard/4.png)

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


## 33.5 すべてのキー入力を取得する
`Keyboard::GetAllInputs()` は、`.down()`, `.pressed()`, `.up()` のいずれかが `true` になっている、アクティブなキーの一覧を `Array<Input>` で返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/keyboard/5.png)

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


## 33.6 複数のキーの組み合わせ（A または B）
`|` を使って複数のキーを組み合わせると、そのいずれかが押されているかどうかを判定できます。

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


## 33.7 複数のキーの組み合わせ（A を押しながら B）
`+` を使って 2 つのキーを組み合わせると、左のキーが押されながら、右のキーが押されたかどうかを判定できます。

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


## 33.8 InputGroup
`InputGroup` 型は `Input` や、`Input` の `|`, `+` による組み合わせを格納できます。

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


## 33.9 キーコンフィグ
`InputGroup` を応用することで、次のようにキーコンフィグを簡単に実現できます。 

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/keyboard/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 左に移動する操作
	InputGroup inputLeft = KeyLeft;

	// 右に移動する操作
	InputGroup inputRight = KeyRight;

	// ジャンプする操作
	InputGroup inputJump = KeySpace;

	size_t index = 0;

	const Array<String> options
	{
		U"[←] [→] [Space]",
		U"[A] [D] [W]",
		U"[←]/[A] [→]/[D] [Space]/[W]"
	};

	const Texture texture{ U"🐥"_emoji };

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

			pos.y = Min(pos.y - deltaTime * jumpY, 450.0);
			jumpY = Max(jumpY - deltaTime * 1000.0, -1000.0);
		}

		// 背景と 🐥 の描画
		{
			Rect{ 800, 500 }
			.draw(Arg::top = ColorF{ 0.1, 0.4, 0.8 }, Arg::bottom = ColorF{ 0.4, 0.7, 1.0 });
			Rect{ 0, 500, 800, 100 }
			.draw(ColorF{ 0.2, 0.5, 0.3 });

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


## 33.10 テキスト入力
`TextInput::UpdateText()` に `String` 型の変数を渡すことで、キーボード入力に基づいたテキスト入力処理ができます。

日本語入力時に未変換のテキストを得たい場合は `TextInput::GetEditingText()` を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/keyboard/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
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

## 33.11 IME を無効化する（Windows 版）
Windows 版において、日本語入力の IME を無効化する場合、`Platform::Windows::TextInput::DisableIME()` を呼びます。

```cpp
# include <Siv3D.hpp>

void Main()
{
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
