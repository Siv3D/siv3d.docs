# 45. 図形の交差判定
図形のクリック判定や、交差判定を行う方法を学びます。

## 45.1 マウスオーバー
- 各図形クラスのメンバ関数 `.mouseOver()` を使うと、その図形の上にマウスカーソルがあるかを調べることができます
- `.mouseOver()` はその図形の上にマウスカーソルがある場合に true を返します
- 図形が実際に描画されているかは結果に影響しません
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/geometry2d/1.png)

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


## 45.2 図形のクリック
- 各図形クラスには、クリック判定を行う次のようなメンバ関数が用意されています

| コード | 説明 |
| --- | --- |
| `.leftClicked()` | 図形上でマウスの左ボタンを押したか |
| `.leftPressed()` | 図形上でマウスの左ボタンを押しているか |
| `.leftReleased()` | 図形上でマウスの左ボタンを離したか |
| `.rightClicked()` | 図形上でマウスの右ボタンを押したか |
| `.rightPressed()` | 図形上でマウスの右ボタンを押しているか |
| `.rightReleased()` | 図形上でマウスの右ボタンを離したか |
	
- click, pressed, released の関係は、`Input` の `.down()`, `.pressed()`, `.up()` と同じです
	- 例えば、`.leftClicked()` は、クリックしたフレームのみ `true` を返します
	- `.leftPressed()` は、クリックしたフレームだけでなく、それ以降押され続けている場合にも `true` を返します
	- `.leftReleased()` は、クリックが離されたフレームのみ `true` を返します
- マウスのボタンを押しながら図形の範囲外にカーソルを移動した場合、`.leftPressed()` は `true` を返しません
- 図形の範囲外でボタンを離した場合、`.leftReleased()` は `true` を返しません

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/geometry2d/2.png)

```cpp

```


## 45.3 図形の交差
- 2 つの図形 `a` と `b` が交差しているかは、`a.intersects(b)` で調べられます
- ほとんどのケースで、異なる図形クラス間で交差判定が可能です
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/geometry2d/3.png)

- 次のサンプルでは、マウスカーソルに追従する円が長方形や星などの図形の内部に完全に含まれているときに、その図形の色を変更します

```cpp

```


## 45.4 図形を内側に含む
- ある図形 `a` が別の図形 `b` を完全に内側に含んでいるかは、`a.contains(b)` で調べられます
- ほとんどのケースで、異なる図形クラス間で内側に含む判定が可能です
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/geometry2d/4.png)

```cpp

```


## 45.5 線分どうしの交差位置取得
- 2 つの線分 `a`, `b` の交差情報を `a.intersectsAt(b)` で取得できます
- この関数の戻り値は `Optional<Vec2>` で、交差の状況に応じて次のような値になります

| 交差の状況 | 戻り値 |
| --- | --- |
| 交差していない | `none` |
| 交差している | `Vec2{ 交点の座標 }` |
| 平行に重なっている | `Vec2{ QNaN, QNaN }` |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/geometry2d/5.png)

```cpp

```

- 2 つの線分が平行に重なっているケースを、次のコードで確認できます

```cpp

```


## 45.6 図形と図形の交差位置取得
- 2 つの図形 `a`, `b` の交差情報を `a.intersectsAt(b)` で取得できます
- この関数の戻り値は `Optional<Array<Vec2>>` で、交差の状況に応じて次のような値になります

| 交差の状況 | 戻り値 |
| --- | --- |
| 交差していない | `none` |
| 交差している | `Array<Vec2>{ 交点の座標, ... }` |
| 交差しているが交点を求められなかった | `Array<Vec2>{}`（空の配列） |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/geometry2d/6.png)

```cpp

```


## 45.7 長方形が重なる領域の取得
- 2 つの長方形 `a`, `b` が重なる領域を `a.getOverlap(b)` で取得できます
- この関数の戻り値は `Rect` または `RectF` で、重なる領域がない場合は空の長方形（大きさが 0 の長方形）を返します
- ある長方形 `rect` が空であるかは `if (rect.isEmpty())`, `if (rect)`, `if (not rect)` などで判定できます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/geometry2d/7.png)

```cpp

```


## 45.8 多角形の AND
- 2 つの `Polygon` `a`, `b` の共通部分を `Geometry2D::And(a, b)` で取得できます
- 戻り値は `Array<Polygon>` で、共通部分がない場合は空の配列を返します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/geometry2d/8.png)

```cpp

```


## 45.9 多角形の差
- 2 つの `Polygon` `a`, `b` の差（`a` から `b` と重なる部分を取り除いたもの）を `Geometry2D::Subtract(a, b)` で取得できます
- 戻り値は `Array<Polygon>` です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/geometry2d/9.png)

```cpp

```


## 45.10 多角形の凸包
- `Polygon` クラスには、自身の凸包（すべての頂点を完全に囲む最小の凸多角形）を求めるメンバ関数 `.computeConvexHull()` が用意されています
- 戻り値は `Polygon` です 

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/geometry2d/10.png)

```cpp

```

