description: OpenSiv3D のチュートリアル

!!! warning "このページよりも新しいドキュメントがあります"
	このドキュメントは古い OpenSiv3D v0.4.3 向けです。2021 年9 月 18 日に最新の OpenSiv3D v0.6.0 がリリースされました。最新のドキュメントは [Siv3D リファレンス v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation) です。

# 12. マウス入力

この章では、マウスの入力を処理する方法を学びます。

## 12.1 マウスカーソルの座標
マウスカーソルの座標は `Cursor::Pos()` を使うと `Point` 型で取得できます。シーンが拡大縮小されている場合には、`Cursor::PosF()` を使うと `Vec2` 型で取得できます。

!!! info
	`Cursor::Pos()` で取得できるマウスカーソル座標は、直前の `System::Update()` の呼び出し時点での座標のため、実際画面に見えているマウスカーソルよりも古い座標を示す場合があります。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/12/1-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle(Cursor::Pos(), 50).draw(Palette::Skyblue);
	}
}
```


## 12.2 マウスカーソルの移動量
直前のフレームからのマウスカーソルの移動量は `Cursor::Delta()` を使うと `Point` 型で取得できます。シーンが拡大縮小されている場合には、`Cursor::DeltaF()` を使うと `Vec2` 型で取得できます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/12/2-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	// 円をつかんでいるか
	bool grab = false;

	Circle circle(Scene::Center(), 50);

	while (System::Update())
	{
		if (grab)
		{
			// 移動量分だけ円を移動
			circle.moveBy(Cursor::Delta());
		}

		
		if (circle.leftClicked()) // 円を左クリックしたら
		{
			grab = true;
		}
		else if (MouseL.up()) // マウスの左ボタンが離されたら
		{
			grab = false;
		}

		if (grab || circle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		circle.draw(Palette::Skyblue);
	}
}
```

!!! info
	図形の `.moveBy()` 関数は、与えられた x, y 成分だけ自身の座標を移動します。一方で似たような名前の `.movedBy()` 関数は、与えられた x, y 成分だけ座標を移動した新しい図形を返し、自身の座標は変更しません。


## 12.3 マウスのボタンの入力状態
マウスのボタンには、以下の `Key` 型のオブジェクトが割り当てられています。

| 定数      | 対応するボタン |
|---------|---------|
| MouseL  | 左ボタン    |
| MouseR  | 右ボタン    |
| MouseM  | 中央ボタン   |
| MouseX1 | 拡張ボタン 1 |
| MouseX2 | 拡張ボタン 2 |
| MouseX3 | 拡張ボタン 3 |
| MouseX4 | 拡張ボタン 4 |
| MouseX5 | 拡張ボタン 5 |

前章のキーボードのキーと同様に、押された瞬間であるかを `.down()`, 押されているかを `.pressed()`, 離された瞬間であるかを `.up()` を使って `bool` 値で取得できます。

|          | down | pressed | up |
|----------|------|---------|----|
| 押していない   |      |         |    |
| 押した瞬間    | ✔    | ✔       |    |
| 押され続けている |      | ✔       |    |
| 離した瞬間    |      |         | ✔  |
| 離され続けている |      |         |    |

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/12/3-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << MouseL.pressed();
		Print << MouseM.pressed();
		Print << MouseR.pressed();
	}
}
```


## 12.4 ボタンが押されていた時間
`Key::pressedDuration()` は、そのボタンが押され続けている時間を `Duration` 型の値で返します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/12/4-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << MouseL.pressedDuration();
	}
}
```


## 12.5 マウスホイールの回転量
直前のフレームからのマウスホイールのスクロール量は、`Mouse::Wheel()` によって `double` 型で取得できます。水平ホイールのスクロール量は、`Mouse::WheelH()` によって `double` 型で取得できます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/12/5-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Vec2 pos = Scene::Center();

	while (System::Update())
	{
		pos.y -= Mouse::Wheel() * 10;

		pos.x += Mouse::WheelH() * 10;

		RectF(Arg::center = pos, 200).draw();
	}
}
```

