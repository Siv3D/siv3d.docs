# 13. キーボード入力を扱う
キーボードの入力を調べる方法を学びます。

## 13.1 キーが押されたか調べる
`if (キー名.down())` で、キーが押されたかを調べることができます。

??? info "主なキー名"
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

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/keyboard/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// A キーが押されたら
		if (KeyA.down())
		{
			Print << U"A";
		}

		// スペースキーが押されたら
		if (KeySpace.down())
		{
			Print << U"Space";
		}

		// 1 キーが押されたら
		if (Key1.down())
		{
			Print << U"1";
		}	
	}
}
```


## 13.2 キーが押されているか調べる
`if (キー名.pressed())` で、キーが押されているかを調べることができます。`.down()` は押された瞬間のみ、`.pressed()` は押されている間ずっと `true` になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/keyboard/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// A キーが押されていたら
		if (KeyA.pressed())
		{
			Print << U"A";
		}

		// スペースキーが押されていたら
		if (KeySpace.pressed())
		{
			Print << U"Space";
		}

		// 1 キーが押されていたら
		if (Key1.pressed())
		{
			Print << U"1";
		}	
	}
}
```


## 13.3 キーで左右に移動する
矢印キーを使って絵文字を左右に移動させるには次のようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/keyboard/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

	// 移動の速さ（ピクセル / 秒）
	const double speed = 200;

	double x = 400;

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		// ← キーが押されていたら
		if (KeyLeft.pressed())
		{
			x -= (speed * deltaTime);
		}

		// → キーが押されていたら
		if (KeyRight.pressed())
		{
			x += (speed * deltaTime);
		}

		emoji.drawAt(x, 300);
	}
}
```


## 13.4 キーで上下左右に移動する
矢印キーを使って絵文字を上下左右に移動させるには次のようにします。`x`, `y` の 2 つの変数を使う代わりに `Vec2` 型の変数を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/keyboard/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

	// 移動の速さ（ピクセル / 秒）
	const double speed = 200;

	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		// ← キーが押されていたら
		if (KeyLeft.pressed())
		{
			pos.x -= (speed * deltaTime);
		}

		// → キーが押されていたら
		if (KeyRight.pressed())
		{
			pos.x += (speed * deltaTime);
		}

		// ↑ キーが押されていたら
		if (KeyUp.pressed())
		{
			pos.y -= (speed * deltaTime);
		}

		// ↓ キーが押されていたら
		if (KeyDown.pressed())
		{
			pos.y += (speed * deltaTime);
		}

		emoji.drawAt(pos);
	}
}
```


## 振り返りチェックリスト

- [x] キーが押されたか調べるには `if (キー名.down())` を使うことを学んだ
- [x] キーが押されているか調べるには `if (キー名.pressed())` を使うことを学んだ
