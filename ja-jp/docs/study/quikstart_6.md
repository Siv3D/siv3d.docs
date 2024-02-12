# 勉強会 6 コマ目（キーとマウス）

## 1. キーボード入力

### 1.1 キーが押されたかを調べる

`if (キー名.down())` で、キーが押されたかを調べることができます。

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


### 1.2 キーが押されているかを調べる

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


### 1.3 キーで絵文字を動かす
矢印キーを使って絵文字を左右に移動させるには次のようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/keyboard/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

	// 絵文字の X 座標
	double x = 400.0;

	while (System::Update())
	{
		// ← キーが押されていたら
		if (KeyLeft.pressed())
		{
			x -= 4.0;
		}

		// → キーが押されていたら
		if (KeyRight.pressed())
		{
			x += 4.0;
		}

		emoji.drawAt(x, 300);
	}
}
```

??? info "（チャレンジ）左右だけでなく上下にも移動できるようにしてみよう"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

		const Texture emoji{ U"☃️"_emoji };

		// 絵文字の X 座標
		double x = 400.0;

		// 絵文字の Y 座標
		double y = 300.0;

		while (System::Update())
		{
			// ← キーが押されていたら
			if (KeyLeft.pressed())
			{
				x -= 4.0;
			}

			// → キーが押されていたら
			if (KeyRight.pressed())
			{
				x += 4.0;
			}

			// ↑ キーが押されていたら
			if (KeyUp.pressed())
			{
				y -= 4.0;
			}

			// ↓ キーが押されていたら
			if (KeyDown.pressed())
			{
				y += 4.0;
			}

			emoji.drawAt(x, y);
		}
	}
	```

!!! success "振り返りチェックリスト"
	- [x] キーが押されたか調べるには `if (キー名.down())` を使うことを学んだ
	- [x] キーが押されているか調べるには `if (キー名.pressed())` を使うことを学んだ


## 2. マウス入力

### 2.1 マウスのボタンが押されたかを調べる
`if (MouseL.down())` でマウスの左ボタンが押されたかを、`if (MouseR.down())` でマウスの右ボタンが押されたかを調べることができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/mouse/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			Print << U"左クリック";
		}

		// 右クリックされたら
		if (MouseR.down())
		{
			Print << U"右クリック";
		}
	}
}
```


### 2.2 マウスカーソルの位置を調べる
マウスカーソルの座標を `Point` 型で得るには `Cursor::Pos()` を使います。`Point` 型の値は `Vec2` 型に変換できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/mouse/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

	// 絵文字の座標
	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			// 現在のマウスカーソルの座標を代入
			pos = Cursor::Pos();
		}

		emoji.drawAt(pos);
	}
}
```

X 座標、Y 座標をそれぞれ `Cursor::Pos().x`、`Cursor::Pos().y` で得ることもできます。前述のプログラムと次のプログラムは同じ動作をします。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

	// 絵文字の X 座標
	double x = 400;

	// 絵文字の Y 座標
	double y = 300;

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			// 現在のマウスカーソルの X 座標を代入
			x = Cursor::Pos().x;

			// 現在のマウスカーソルの Y 座標を代入
			y = Cursor::Pos().y;
		}

		emoji.drawAt(x, y);
	}
}
```


### 2.3 図形をクリックしたかを調べる
`Circle` や `Rect`, `RectF` の `.leftClicked()` で、その図形が左クリックされたかを判定できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/mouse/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Circle circle{ 200, 200, 50 };

	const Rect rect{ 400, 400, 200, 40 };

	while (System::Update())
	{
		// 円を左クリックしたら
		if (circle.leftClicked())
		{
			Print << U"円をクリック";
		}

		// 長方形を左クリックしたら
		if (rect.leftClicked())
		{
			Print << U"長方形をクリック";
		}

		circle.draw(Palette::Orange);

		rect.draw();
	}
}
```


### 2.4 図形の上にマウスカーソルがあるかを調べる
`Circle` や `Rect`, `RectF` の `.mouseOver()` で、マウスカーソルがその図形の上にあるかを判定できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/mouse/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Circle circle{ 200, 200, 50 };

	const Rect rect{ 400, 400, 200, 40 };

	while (System::Update())
	{
		ClearPrint();

		// 円の上にマウスカーソルがあれば
		if (circle.mouseOver())
		{
			Print << U"円の上にある";
		}

		// 長方形の上にマウスカーソルがあれば
		if (rect.mouseOver())
		{
			Print << U"長方形の上にある";
		}

		circle.draw(Palette::Orange);

		rect.draw();
	}
}
```


### 2.5 マウスカーソルを手の形にする
`Cursor::RequestStyle(CursorStyle::Hand);` を呼ぶと、そのフレームはマウスカーソルが手の形のアイコンで表示されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/mouse/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Circle circle{ 200, 200, 50 };

	while (System::Update())
	{
		// 円の上にマウスカーソルがあれば
		if (circle.mouseOver())
		{
			// マウスカーソルを手のアイコンにする
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		circle.draw(Palette::Orange);
	}
}
```


### 2.6 絵文字をクリックしたかを調べる
絵文字（テクスチャ）には `.leftClicked()` や `.mouseOver()` が無いため、代わりに近い大きさの円を使って判定します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/mouse/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"🍪"_emoji };

	const Circle circle{ 200, 200, 60 };

	while (System::Update())
	{
		// 円の上にマウスカーソルがあれば
		if (circle.mouseOver())
		{
			// マウスカーソルを手のアイコンにする
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// 円を左クリックしたら
		if (circle.leftClicked())
		{
			Print << U"クッキーをクリック";
		}

		// 円は描かない
		//circle.draw();

        // circle.center は Vec2{ circle.x, circle.y } と同じ
		emoji.drawAt(circle.center, Palette::Orange);
	}
}
```
