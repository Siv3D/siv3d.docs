# 14. マウス入力を扱う
マウスのクリックやカーソルの位置を取得する方法を学びます。

## 14.1 マウスクリックを調べる
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

## 14.2 マウスカーソルの座標に移動する
マウスカーソルの座標を `Point` 型で得るには `Cursor::Pos()` を使います。`Point` 型の値は `Vec2` 型に変換できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/mouse/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

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

	double x = 400;

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


## 14.3 図形をクリックしたかを調べる
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


## 14.4 図形の上にマウスカーソルがあるかを調べる
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


## 14.5 マウスカーソルを手の形にする
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


## 14.6 （応用）絵文字をクリックしたかを調べる
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


## 振り返りチェックリスト

- [x] `MouseL`, `MouseR` の `.down()` で、左クリック、右クリックされたかを調べることを学んだ
- [x] `Cursor::Pos()` で、マウスカーソルの位置を得ることを学んだ
- [x] `Circle` や `Rect`, `RectF` の `.leftClicked()` で、その図形が左クリックされたかを判定できることを学んだ
- [x] `Circle` や `Rect`, `RectF` の `.mouseOver()` で、マウスカーソルがその図形の上にあるかを判定できることを学んだ
- [x] `Cursor::RequestStyle(CursorStyle::Hand);` で、マウスカーソルを手の形にできることを学んだ
- [x] 絵文字（テクスチャ）には `.leftClicked()` や `.mouseOver()` が無いため、代わりに近い大きさの円を使って判定するテクニックを学んだ
