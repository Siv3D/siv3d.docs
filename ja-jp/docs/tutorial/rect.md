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
- 次のサンプルで `Scene::Time()` は、アプリケーションが起動してからの経過時間（秒）を `double` 型で返します。この値に 20.0 をかけた値を長方形の幅にして描画します

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
- 長方形を、左上でなく中心座標指定で作成したい場合、次のようにします
	- `Rect{ Arg::center(x, y), w, h }`
	- `Rect{ Arg::center(x, y), s }`
	- `Rect{ Arg::center = pos, w, h }`
	- `Rect{ Arg::center = pos, s }`
	- `RectF` の場合も同様です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/3.png)

```cpp title="中心座標指定で長方形を描く"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Rect{ Arg::center(100, 100), 180 }.draw();

		Rect{ Arg::center(400, 300), 300, 100 }.draw(Palette::Orange);

		Rect{ Arg::center = Cursor::Pos(), 200 }.draw(ColorF{ 0.8, 0.9, 1.0, 0.8 });
	}
}
```


## 11.4 長方形の枠を描く
- 長方形の枠だけを描きたい場合、`.draw()` の代わりに `.drawFrame()` を使います
- `.drawFrame()` は次の 2 通りの書き方があります
	- `.drawFrame(太さ, color)`
	- `.drawFrame(内側方向の太さ, 外側方向の太さ, color)`
- 内側方向・外側方向は、基準の長方形から内側、外側に向かっての太さを表し、最終的な太さはそれらの和になります
- 太さはいずれも 0.0 以上の値を指定します
- `color` を省略すると、`.draw()` のときと同様に白色になります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/4.png)

```cpp title="長方形の枠を描く"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Rect{ Arg::center(100, 100), 180 }.drawFrame(10);

		Rect{ 400, 300, 200 }.drawFrame(8, 0, Palette::Seagreen);

		Rect{ 100, 300, 400, 200 }.drawFrame(0, 4, ColorF{ 0.8, 0.9, 1.0 });
	}
}
```


## 11.5 上下のグラデーション
- 長方形の色を上下方向にグラデーションさせたい場合、次のような `.draw()` を使います
	- `.draw(Arg::top = 上の色, Arg::bottom = 下の色)`
	- `Arg::top(r, g, b)` や `Arg::top(gray)`, `Arg::top(r, g, b, a)`, `Arg::top(gray, a)` のような書き方もできます
- `top` と `bottom` の順番は入れ替えできません

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/5.png)

```cpp title="上下のグラデーションで長方形を描く"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Rect{ 0, 0, 600, 500 }
			.draw(Arg::top = ColorF{ 0.5, 0.7, 0.9 }, Arg::bottom = ColorF{ 0.5, 0.9, 0.7 });

		Rect{ 660, 40, 80, 520 }
			.draw(Arg::top(1.0), Arg::bottom(0.0));
	}
}
```


## 11.6 左右のグラデーション
- 長方形の色を左右方向にグラデーションさせたい場合、次のような `.draw()` を使います
	- `.draw(Arg::left = 左の色, Arg::right = 右の色)`
	- `Arg::left(r, g, b)` や `Arg::left(gray)`, `Arg::left(r, g, b, a)`, `Arg::left(gray, a)`  のような書き方もできます
- `left` と `right` の順番は入れ替えできません

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/rect/6.png)


```cpp title="上下のグラデーションで長方形を描く"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Rect{ 0, 0, 600, 500 }
			.draw(Arg::left = ColorF{ 0.5, 0.7, 0.9 }, Arg::right = ColorF{ 0.5, 0.9, 0.7 });

		Rect{ 660, 40, 80, 520 }
			.draw(Arg::left(1.0), Arg::right(0.0));
	}
}
```

## 振り返りチェックリスト
- [x] `Rect` を作成し、`.draw()` で長方形を描画することを学んだ
- [x] 位置や大きさを `double` 型で扱う場合は、`RectF` を使うことを学んだ
- [x] 中心座標指定で長方形を作成する方法を学んだ
- [x] `.drawFrame()` を使って、長方形の枠を描画する方法を学んだ
- [x] 上下や左右方向にグラデーションをつけた長方形を描画する方法を学んだ
