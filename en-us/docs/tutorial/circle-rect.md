# 8. 円と長方形を描く
円と長方形を描く方法を学びます。

## 8.1 画面の座標系
ウィンドウ内で、背景色を変えられる部分が **画面（シーン）**です。Siv3D はこの領域に文字や図形、画像を表示できます。

画面のサイズは、基本の状態では**幅 800 ピクセル**、**高さ 600 ピクセル**です。

画面上の位置を表す座標系は、最も左上のピクセルが「X 座標 0」「Y 座標 0」を表す (0, 0) です。右に進むと X 座標が大きくなり、下に進むと Y 座標が大きくなります。画面の最も右下のピクセルの座標は (799, 599) です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// 現在のマウスカーソル座標を表示する
		Print << Cursor::Pos();
	}
}
```

このコードを実行すると、マウスカーソルの座標が画面の左上に簡易表示されます。


## 8.2 円を描く (1)
Siv3D では、何かを描く命令はメインループの中に記述します。円を描くときは `Circle` を作成し、その `.draw()` を呼びます。

`Circle` は `Circle{ x, y, r }` のように、中心の X 座標、Y 座標、半径を指定して作成します。

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

中心の座標や半径を変えてみましょう。

!!! info "Circle の構造"
	簡単に説明すると、`Circle` は次のようなクラスです（実際にはこれ以外にもメンバ関数がたくさんあります）。
	```cpp
	struct Circle
	{
		double x;
		double y;
		double r;
	};
	```


## 8.3 円を描く (2)
`Circle` は、二次元座標を表す `Point` 型や `Vec2` 型の値を使って、`Circle{ pos, r }` のように、2 つの引数から作成することもできます。

例えば `Cursor::Pos()` は現在のマウスカーソルの座標を `Point` 型で返す関数なので、これを使ってマウスに追随する円を描くことができます。

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
	簡単に説明すると、`Point` と `Vec2` は次のようなクラスです（実際にはこれ以外にもメンバ関数がたくさんあります）。
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


## 8.4 円の色を変える
図形に色を付けたいときは `.draw()` 関数の引数に色を渡します。色はチュートリアル 7 で学んだように以下の方法で指定します。

- `Palette::Red` などの色名を使う
- `ColorF{ r, g, b }`, `ColorF{ gray }` のように RGB 値を指定する
- `HSV{ h, s, v }`, `HSV{ h }` のように HSV 値を指定する

`.draw()` の引数に色を渡さないと、図形は白色（`ColorF{ 1.0 }`）で描画されます。

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

## 8.5 半透明の色を指定する
`ColorF` と `HSV` は、**不透明度**を指定できます。不透明度は 0.0 から 1.0 の範囲で指定します。0.0 は完全に透明、1.0 は完全に不透明で、0.5 のときは背景色と描画色が 50% ずつ混ざった色になります。

不透明度 `a` （アルファ）は、`ColorF{ r, g, b, a }`, `ColorF{ gray, a }`, `HSV{ h, s, v, a }`, `HSV{ h, a }` のように、最後の引数に指定します。

不透明度を指定しない場合は、デフォルトで 1.0 になります。`Scene::SetBackground()` に指定する色については、不透明度は無視されるため無意味です。

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


## 8.6 長方形を描く (1)
長方形を描くときは `Rect` を作成して `.draw()` します。

`Rect{ x, y, w, h }` は、左上座標が (x, y), 幅が w, 高さが h の長方形です。

`Rect{ x, y, s }` は引数を省略したオーバーロードで、正方形を作成するときに便利です。左上座標が (x, y), 幅と高さが s の正方形です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 長方形を描く 
		Rect{ 20, 40, 400, 100 }.draw();

		// 正方形を描く 
		Rect{ 100, 200, 80 }.draw(Palette::Orange);
	}
}
```

!!! info "Rect の構造"
	簡単に説明すると、`Rect` は次のようなクラスです（実際にはこれ以外にもメンバ関数がたくさんあります）。
	```cpp
	struct Rect
	{
		int32 x;
		int32 y;
		int32 w;
		int32 h;
	};
	```

## 8.7 長方形を描く (2)
座標や大きさを `double` 型で扱いたい場合は、`Rect` の代わりに `RectF` 使います。

`Scene::Time()` は、ゲーム開始からの経過時間を秒 （`double` 型）で返します。下記のコードでは、長方形の幅 `w` が毎秒 20.0 のペースで増えていきます。`w` は `double` 型のため、`Rect` ではなく `RectF` を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double w = (Scene::Time() * 20.0);

		// 長方形を描く 
		RectF{ 20, 40, w, 100 }.draw();
	}
}
```

!!! info "RectF の構造"
	簡単に説明すると、`RectF` は次のようなクラスです（実際にはこれ以外にもメンバ関数がたくさんあります）。
	```cpp
	struct Rect
	{
		double x;
		double y;
		double w;
		double h;
	};
	```

## 8.8 円や長方形の枠を描く
円や長方形の枠だけを描きたい場合、`.draw()` の代わりに `.drawFrame(innerThickness, outerThickness, color)` を使います。

`innerThickness` は内側方向への太さ、`outerThickness` は外側方向への太さを表す `double` 型の値です。`innerThickness` と `outerThickness` には、それぞれ 0.0 以上の値を指定します。

`color` を省略した場合、`.draw()` と同様に白色で描画されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// 長方形の枠を描く
		Rect{ 100, 100, 100, 30 }
			.drawFrame(3, 0);

		// 長方形の枠を描く
		Rect{ 220, 100, 100, 30 }
			.drawFrame(0, 3);

		// 長方形の枠を描く
		Rect{ 200, 200, 400, 100 }
			.drawFrame(3, 3, Palette::Orange);

		// 円の枠を描く
		Circle{ Cursor::Pos(), 40 }
			.drawFrame(1, 1, Palette::Seagreen);
	}
}
```

## 8.9 グラデーションで長方形を描く (1)
`Rect` や `RectF` を `.draw()` する際、`.draw(Arg::top = 色, Arg::bottom = 色)` のように書くことで、上下方向のグラデーションで長方形を描画できます。

`Arg::top = 色` では上側の色、`Arg::bottom = 色` では下側の色を指定します。`top` と `bottom` の引数の順番を入れ替えることはできません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// グラデーションで長方形を描く
		Rect{ 0, 0, 600, 500 }
			.draw(Arg::top = ColorF{ 0.5, 0.7, 0.9 }, Arg::bottom = ColorF{ 0.5, 0.9, 0.7 });
	}
}
```

## 8.10 グラデーションで長方形を描く (2)
`Rect` や `RectF` を `.draw()` する際、`.draw(Arg::left = 色, Arg::right = 色)` のように書くことで、左右方向のグラデーションで長方形を描画できます。

`Arg::left = 色` では左側の色、`Arg::right = 色` では右側の色を指定します。`left` と `right` の引数の順番を入れ替えることはできません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{
		// グラデーションで長方形を描く
		Rect{ 0, 0, 600, 500 }
			.draw(Arg::left = ColorF{ 0.5, 0.7, 0.9 }, Arg::right = ColorF{ 0.5, 0.9, 0.7 });
	}
}
```


## 振り返りチェックリスト
- [x] 画面の座標系と基本の画面サイズ (800x600) を理解した
- [x] `Circle` を使って円を描画する方法を学んだ
- [x] 半透明の色の指定方法を学んだ
- [x] `Rect` または `RectF` を使って長方形を描画する方法を学んだ
- [x] 円や長方形の枠を描画する方法を学んだ
- [x] グラデーションで長方形を描画する方法を学んだ
