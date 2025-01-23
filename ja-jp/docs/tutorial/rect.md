# 11. 長方形を描く
画面に長方形を描く方法を学びます。



#### 3.7.3 長方形を描く
長方形を描くときは `Rect` を作成して `.draw()` します。

`Rect{ x, y, w, h }` は、左上座標が (x, y), 幅が w, 高さが h の長方形です。

`Rect{ x, y, s }` は引数を省略したオーバーロードで、左上座標が (x, y), 幅と高さが s の正方形です。

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
	`Rect` は次のようなクラスです。
	```cpp
	struct Rect
	{
		int32 x;
		int32 y;
		int32 w;
		int32 h;
	};
	```

座標や大きさを `double` 型で扱いたい場合は、`Rect` の代わりに `RectF` 使います。

`Scene::Time()` は、プログラム開始からの経過時間を秒 （`double` 型）で返します。

下記のコードでは、長方形の幅 `w` が毎秒 20.0 のペースで増えていきます。`double` 型である `w` に合わせるため、`Rect` ではなく `RectF` を使います。

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
	`RectF` は次のようなクラスです。
	```cpp
	struct Rect
	{
		double x;
		double y;
		double w;
		double h;
	};
	```

#### 3.7.4 円や長方形の枠を描く
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


#### 3.7.5 長方形のグラデーション
`Rect` や `RectF` を `.draw()` する際、`.draw(Arg::top = 色, Arg::bottom = 色)` のように書くことで、上下方向のグラデーションで長方形を描画できます。

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

`.draw(Arg::left = 色, Arg::right = 色)` のように書くことで、左右方向のグラデーションで長方形を描画できます。

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

!!! success "予習チェックリスト"
	- [x] 画面の座標系と基本の画面サイズ（800x600）を理解した
	- [x] `Circle` を使って円を描画する方法を学んだ
	- [x] 半透明の色の指定方法を学んだ
	- [x] `Rect` または `RectF` を使って長方形を描画する方法を学んだ
	- [x] 円や長方形の枠を描画する方法を学んだ
	- [x] グラデーションの長方形を描画する方法を学んだ
