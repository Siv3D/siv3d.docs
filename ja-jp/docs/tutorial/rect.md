# 11. 長方形を描く
画面に長方形を描く方法を学びます。

## 11.1 長方形を描く
- 長方形を描くときは `Rect` クラスのオブジェクトを作成し、その `.draw()` を呼びます
- `Rect` は次のように作成します
	- 左上の座標 (x, y), 幅 w, 高さ h を指定して `Rect{ x, y, w, h }`（長方形）
	- 左上の座標 (x, y), 一片の長さ s を指定して `Rect{ x, y, s }`（正方形）
	- 値はいずれも整数 (`int32`) で指定します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/1.png)

```cpp title="長方形を描く"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Rect{ 20, 40, 400, 100 }.draw();

		Rect{ 100, 200, 80 }.draw(Palette::Orange);

		Rect{ 400, 300, 360, 260 }.draw(ColorF{ 0.8, 0.9, 1.0 });
	}
}
```


## 11.2 長方形を描く（浮動小数点数）
- 座標や大きさを `double` 型で扱いたい場合は、`Rect` の代わりに `RectF` を使います
- `RectF` は次のように作成します
	- 左上の座標 (x, y), 幅 w, 高さ h を指定して `RectF{ x, y, w, h }`（長方形）
	- 左上の座標 (x, y), 一片の長さ s を指定して `RectF{ x, y, s }`（正方形）
	- 値は整数または浮動小数点数 (`double`) で指定します
- 次のサンプルで `Scene::Time()` は、アプリケーションが起動してからの経過時間（秒）を `double` 型で返します。この値に 20.0 をかけた値を長方形の幅として描画します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/2.png)

```cpp title="時間経過で横幅が変わる長方形を描く"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double w = (Scene::Time() * 20.0);

		RectF{ 0, 250, w, 100 }.draw();
	}
}
```

- `w` は `double` 型なので、`RectF` ではなく`Rect` を使うとコンパイルエラーになります


## 11.3 中心を指定して長方形を作成する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/3.png)


## 11.4 長方形の枠を描く

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/4.png)


## 11.5 上下のグラデーション

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/5.png)


## 11.6 左右のグラデーション

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/6.png)


## 振り返りチェックリスト






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
