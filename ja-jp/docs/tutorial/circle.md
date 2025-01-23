# 10. 円を描く
画面に円を描く方法を学びます。

## 10.1 円を描く
Siv3D では、何かを描く命令はメインループの中に記述します。円を描くときは `Circle` を作成し、その `.draw()` を呼びます。`Circle` は `Circle{ x, y, r }` のように、中心の X 座標、Y 座標、半径を指定して作成します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 円を描く
		Circle{ 400, 300, 20 }.draw();
	}
}
```

!!! info "Circle の構造"
	`Circle` は次のようなクラスです。
	```cpp
	struct Circle
	{
		double x;
		double y;
		double r;
	};
	```

## 10.2 マウスに追随する円を描く


`Circle` は、二次元座標を表す `Point` 型や `Vec2` 型の値を使って、`Circle{ pos, r }` のように、2 つの引数から作成することもできます。`Cursor::Pos()` は現在のマウスカーソルの座標を `Point` 型で返す関数なので、これを使ってマウスに追随する円を描くことができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// マウスに追随する円を描く
		Circle{ Cursor::Pos(), 100 }.draw();
	}
}
```

!!! info "Point や Vec2 の構造"
	`Point` と `Vec2` は次のようなクラスです。
	```cpp
	struct Point
	{
		int32 x;
		int32 y;
	};

	struct Vec2
	{
		double x;
		double y;
	};
	```

## 10.3 色のついた円を描く

図形に色を付けたいときは `.draw()` 関数の引数に色を渡します。色を指定しなかった場合は白色になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ 100, 200, 40 }.draw(); // 色を指定しない場合は白色

		Circle{ 200, 200, 40 }.draw(Palette::Green);

		Circle{ 300, 200, 40 }.draw(Palette::Skyblue);

		Circle{ 400, 200, 40 }.draw(ColorF{ 1.0, 0.8, 0.0 });

		Circle{ 500, 200, 40 }.draw(ColorF{ 0.8 });

		Circle{ 600, 200, 40 }.draw(HSV{ 160.0, 0.5, 1.0 });

		Circle{ 700, 200, 40 }.draw(HSV{ 160.0 });
	}
}
```


## 10.4 半透明の色を指定する

`ColorF` と `HSV` は、**不透明度**を指定できます。不透明度は 0.0 から 1.0 の範囲で指定します。

0.0 は完全に透明、1.0 は完全に不透明で、0.5 のときは背景色と描画色が 50% ずつ混ざった色になります。

不透明度 `a` （アルファ）は、`ColorF{ r, g, b, a }`, `ColorF{ gray, a }`, `HSV{ h, s, v, a }`, `HSV{ h, a }` のように、最後の引数に指定します。

不透明度を指定しない場合は、デフォルトで 1.0 になります。

`Scene::SetBackground()` に指定する色については、不透明度は無視されるため無意味です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 中央に白い円を描く
		Circle{ 400, 300, 200 }.draw();

		// 左に半透明の円を描く
		Circle{ 200, 300, 200 }.draw(ColorF{ 1.0, 0.0, 0.0, 0.9 });

		// 右に半透明の円を描く
		Circle{ 600, 300, 200 }.draw(HSV{ 240.0, 0.5, 1.0, 0.2 });

		// マウスカーソルの位置に半透明の円を描く
		Circle{ Cursor::Pos(), 100 }.draw(ColorF{ 0.0, 0.5 });
	}
}
```


#### 10.5 円の枠を描く
円や長方形の枠だけを描きたい場合、`.draw()` の代わりに `.drawFrame(innerThickness, outerThickness, color)` を使います。

`innerThickness` は内側方向への太さ、`outerThickness` は外側方向への太さを表す `double` 型の値です。`innerThickness` と `outerThickness` には、それぞれ 0.0 以上の値を指定します。

`color` を省略した場合、`.draw()` と同様に白色で描画されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/8.png)

```cpp

```



!!! success "予習チェックリスト"
	- [x] `Circle` を使って円を描画する方法を学んだ
	- [x] 半透明の色の指定方法を学んだ
	- [x] `Rect` または `RectF` を使って長方形を描画する方法を学んだ
	- [x] 円や長方形の枠を描画する方法を学んだ
	- [x] グラデーションの長方形を描画する方法を学んだ
