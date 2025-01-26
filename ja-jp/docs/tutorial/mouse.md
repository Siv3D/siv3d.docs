# 16. マウス入力を扱う

## 16.1 マウスカーソルの位置を取得する
- マウスカーソルの現在の座標を取得するには `Cursor::Pos()` を使います
- `Cursor::Pos()` は `Point` 型の値を返します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/1.png)

```cpp title="マウスカーソルの位置に絵文字を表示する" hl_lines="13"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };
	
	const Texture texture{ U"🐥"_emoji };

	while (System::Update())
	{
		const Point cursorPos = Cursor::Pos();

		font(U"{}"_fmt(cursorPos)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });

		texture.drawAt(cursorPos);
	}
}
```


## 16.2 マウスのボタンが押されたかを調べる
- マウスの左ボタンが押されると、`MouseL.down()` が `true` を返します
- マウスの右ボタンが押されると、`MouseR.down()` が `true` を返します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/2.png)

```cpp title="マウスのボタンが押されたらメッセージを出力する" hl_lines="10 16"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		// 左クリックされたら
		if (MouseL.down())
		{
			Print << U"Left Click";
		}

		// 右クリックされたら
		if (MouseR.down())
		{
			Print << U"Right Click";
		}
	}
}
```


## 16.3 マウスのボタンが押されているかを調べる
- `.down()` と異なり、`.pressed()` は押されている間ずっと `true` を返します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/3.png)

```cpp title="マウスのボタンが押されている間、メッセージを出力する" hl_lines="10 16"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		// 左ボタンが押されていたら
		if (MouseL.pressed())
		{
			Print << U"Left Pressed";
		}

		// 右ボタンが押されていたら
		if (MouseR.pressed())
		{
			Print << U"Right Pressed";
		}
	}
}
```


## 16.4 マウス入力の組み合わせ
- マウス入力を使った絵文字の移動のサンプルコードです
	- 左クリックした場所に絵文字が移動します
	- 右クリックしたら画面中央に戻ります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture texture{ U"🐥"_emoji };

	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		// 左クリックしたら
		if (MouseL.down())
		{
			// 絵文字の表示位置をマウスカーソルの位置に変更する
			pos = Cursor::Pos();
		}

		// 右クリックしたら
		if (MouseR.down())
		{
			// 絵文字の表示位置を画面中央にリセットする
			pos = Vec2{ 400, 300 };
		}

		texture.drawAt(pos);
	}
}
```


## 16.5 図形をクリックしたかを調べる
- `Rect` や `Circle` などの図形がクリックされたかを調べるには、各図形クラスのメンバ関数 `.leftClicked()` を使います
- `.leftClicked()` はその図形が左クリックされた場合に `true` を返します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/5.png)

```cpp title="図形をクリックしたらメッセージを出力する" hl_lines="14 20"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Circle circle{ 200, 150, 100 };

	const Rect rect{ 400, 300, 200, 100 };

	while (System::Update())
	{
		// 円を左クリックしたら
		if (circle.leftClicked())
		{
			Print << U"Circle";
		}

		// 長方形を左クリックしたら
		if (rect.leftClicked())
		{
			Print << U"Rect";
		}

		circle.draw(Palette::Seagreen);
		rect.draw(ColorF{ 0.4 });
	}
}
```


## 16.6 クリック判定と描画は無関係
- 実際にその図形が画面に描かれているかは、`.leftClicked()` の結果に影響しません
- 次のコードに登場する `rect` は、画面左半分に相当する長方形です
- `.draw()` を使って描画はしていませんが、その長方形の領域が左クリックされたかを `.leftClicked()` で調べることができます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/5.png)

```cpp title="画面の左半分をクリックしたらメッセージを出力する"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 画面の左半分を覆う長方形
	const Rect rect{ 0, 0, 400, 600 };

	while (System::Update())
	{
		// 画面の左半分を左クリックしたら
		if (rect.leftClicked())
		{
			Print << U"Click!";
		}
	}
}
```


## 16.7 図形の上にマウスカーソルがあるかを調べる
- 各図形クラスのメンバ関数 `.mouseOver()` を使うと、その図形の上にマウスカーソルがあるかを調べることができます
- `.mouseOver()` はその図形の上にマウスカーソルがある場合に `true` を返します
- これもクリック判定と同様に、図形が実際に描画されているかは結果に影響しません
- 次のコードでは、条件演算子 `A ? B : C` を使って、マウスカーソルが図形の上にあるかに応じて、図形の色を変えています

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/6.png)

```cpp title="マウスカーソルが図形の上にある場合、図形の色を変える"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Circle circle{ 200, 150, 100 };

	const Rect rect{ 400, 300, 200, 100 };

	while (System::Update())
	{
		circle.draw(circle.mouseOver() ? Palette::Seagreen : Palette::White);

		rect.draw(rect.mouseOver() ? ColorF{ 0.8 } : ColorF{ 0.6 });
	}
}
```


## 16.8 マウスカーソルを手の形にする
- 対象をマウスで操作できることをユーザに伝えるために、マウスカーソルを手の形に変更することがあります
- `Cursor::RequestStyle(CursorStyle::Hand);` を呼ぶと、そのフレームはマウスカーソルを手の形にできます

```cpp title="マウスカーソルが図形の上にあるとき、カーソルを手の形にする" hl_lines="13"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Circle circle{ 200, 150, 100 };

	while (System::Update())
	{
		if (circle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		circle.draw();
	}
}
```


## 16.9 絵文字をクリックしたかを調べる
- 絵文字（テクスチャ）には `.leftClicked()` や `.mouseOver()` がありません
- 代わりに、近い大きさの図形で近似して判定するという方法があります
	- 絵文字であれば、半径 `60` の円でざっくり近似できます
- 次のコードは画面にあるリンゴをクリックさせるサンプルです
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/mouse/8.png)

```cpp title="絵文字をクリックしたらメッセージを出力する" hl_lines="9 14 21"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🍎"_emoji };

	const Circle circle{ 200, 150, 60 };

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
			Print << U"Apple";
		}

		emoji.drawAt(circle.center);

		// 円は描かない
		//circle.draw();
	}
}
```
