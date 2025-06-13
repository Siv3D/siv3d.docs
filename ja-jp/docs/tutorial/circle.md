# 10. 円を描く
画面に円を描く方法を学びます。

## 10.1 円を描く
- 描画命令を**メインループの中**に書きます
	- 毎フレーム背景色で画面がクリアされるため、円を表示し続けたい場合は、円を毎フレーム描画する必要があります
- 円を描くときは `Circle` クラスのオブジェクトを作成し、その `.draw()` を呼びます
- `Circle` は中心の X 座標、Y 座標、半径を指定して `Circle{ x, y, r }` のように作成します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/circle/1.png)

```cpp title="円を描く"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ 200, 300, 30 }.draw();

		Circle{ 600, 300, 100 }.draw();
	}
}
```


## 10.2 マウスに追随する円を描く
- `Point` 型や `Vec2` 型の値を使って `Circle{ pos, r }` のように 2 つの引数から `Circle` を作成できます
- 現在のマウスカーソル座標を `Point` 型で返す `Cursor::Pos()` と組み合わせ、マウスに追随する円を描くことができます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/circle/2.png)

```cpp title="マウスに追随する円を描く"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ Cursor::Pos(), 100 }.draw();
	}
}
```


## 10.3 色のついた円を描く
- 図形に色を付けるには、`.draw()` 関数の引数に色を渡します
- 色の指定には、[**チュートリアル 8. 背景の色を変える**](./background.md){:target="_blank"} で学んだ `Palette` や `ColorF`, `HSV` を使います
- 色を指定しなかった場合は白色になります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/circle/3.png)

```cpp title="色のついた円を描く"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ 100, 300, 30 }.draw(); // 色を指定しない場合は白色

		Circle{ 200, 300, 30 }.draw(Palette::Green);

		Circle{ 300, 300, 30 }.draw(ColorF{ 1.0, 0.8, 0.0 });

		Circle{ 400, 300, 40 }.draw(ColorF{ 0.8 });

		Circle{ 500, 300, 40 }.draw(HSV{ 160.0, 0.5, 1.0 });

		Circle{ 600, 300, 40 }.draw(HSV{ 160.0 });
	}
}
```


## 10.4 半透明の色を指定する
- `ColorF` と `HSV` による色指定では、**不透明度**を設定できます
- 不透明度は 0.0 から 1.0 の範囲です
- 0.0 は完全に透明、1.0 は完全に不透明です
- 0.5 のときは背景色と描画色が 50 % ずつ混ざった色になります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/circle/alpha.png)

- 不透明度 `a` （アルファ）は、次のように、`ColorF` や `HSV` の最後の引数で指定します
	- `ColorF{ r, g, b, a }`, `ColorF{ gray, a }`
	- `HSV{ h, s, v, a }`, `HSV{ h, a }`
- `ColorF{ 0.0, 0.6, 0.2 }` のように、不透明度を指定しない場合、`a` は 1.0 になります
- `Scene::SetBackground()` で背景色を設定するときは、不透明度の値には意味がないため無視されます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/circle/4.png)

```cpp title="半透明の円を描く" hl_lines="11"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ 200, 300, 200 }.draw(ColorF{ 1.0 });
		Circle{ 600, 300, 200 }.draw(HSV{ 190.0, 0.5, 0.9 });

		// マウスカーソルに追随する半透明の円を描く
		Circle{ Cursor::Pos(), 100 }.draw(ColorF{ 0.0, 0.6, 0.2, 0.5 });
	}
}
```


## 10.5 円の枠を描く
- 円の枠だけを描きたい場合、`.draw()` の代わりに `.drawFrame()` を使います
- `.drawFrame()` は次の 2 通りの書き方があります
	- `.drawFrame(太さ, color)`
	- `.drawFrame(内側方向の太さ, 外側方向の太さ, color)`
- 内側方向・外側方向は、基準円から内側、外側に向かっての太さを表し、最終的な太さはそれらの和になります
- 太さはいずれも 0.0 以上の値を指定します
- `color` を省略すると、`.draw()` のときと同様に白色になります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/circle/5.png)

```cpp title="円の枠を描く"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		Circle{ 100, 300, 60 }.drawFrame(8);

		Circle{ 300, 300, 60 }.drawFrame(4, 4); // .drawFrame(8) と同じ

		Circle{ 500, 300, 80 }.drawFrame(10, 0, HSV{ 70.0, 0.8, 1.0 });

		Circle{ 700, 300, 80 }.drawFrame(0, 10, HSV{ 160.0, 0.8, 1.0 });
	}
}
```


## 振り返りチェックリスト
- [x] `Circle` を作成し、`.draw()` で円を描画することを学んだ
- [x] `.draw()` の引数に色を指定して、色のついた円を描画する方法を学んだ
- [x] `ColorF` や `HSV` で不透明度を指定して、半透明の円を描画する方法を学んだ
- [x] `.drawFrame()` を使って、円の枠を描画する方法を学んだ
