# 7. 背景の色を変える
画面の背景の色を変える方法を学びます。

## 7.1 背景を白色にする
背景の色を変更するには `Scene::SetBackground(色)` を呼びます。白色は `Palette::White` です。一度変更した背景色は、再度変更するまでそのままです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景を白色にする
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{

	}
}
```


## 7.2 背景を黒色にする
黒色は `Palette::Black` です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景を黒色にする
	Scene::SetBackground(Palette::Black);

	while (System::Update())
	{

	}
}
```


## 7.3 背景をそれ以外の色にする
`Palette::***` には、[HTML カラー :material-open-in-new:](https://voidproc.github.io/siv3d-palette-browser/){:target="_blank"} の色名を使えます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景色を HTML カラーで指定する
	Scene::SetBackground(Palette::Aliceblue);

	while (System::Update())
	{

	}
}
```


## 7.4 背景色を RGB で指定する (1)
色を RGB で指定するには、`ColorF{ r, g, b }` を使います。`r`, `g`, `b` はそれぞれ 0.0 から 1.0 の範囲の値で、それぞれ赤、緑、青の成分を表します。

例えば、淡い水色は `ColorF{ 0.8, 0.9, 1.0 }` になります。これは r = 80%, g = 90%, b = 100% です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景色を RGB で指定する
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	while (System::Update())
	{

	}
}
```


## 7.5 背景色を RGB で指定する (2)
RGB の各成分が等しい場合、`ColorF{ gray }` と書くことができます。例えば `ColorF{ 0.8 }` は `ColorF{ 0.8, 0.8, 0.8 }` と同じです。

白色は `ColorF{ 1.0 }`, 灰色は `ColorF{ 0.5 }`, 黒色は `ColorF{ 0.0 }` と表現できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景色を RGB で指定する
	Scene::SetBackground(ColorF{ 0.8 });

	while (System::Update())
	{

	}
}
```


## 7.6 背景色を HSV で指定する (1)
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/6-2.png)

<small>図表は https://bootcamp.uxdesign.cc/hsb-hsv-color-system-d14697d7c485 より引用</small>

HSV 表色系で色を指定するには、`HSV{ h, s, v }` を使います。


| 成分 | 値の範囲 | 説明 |
| --- | --- | --- |
| h | 0.0 から 360.0（範囲外も可能）| 色相 (hue)<br>色相環（上図）に対応する色を表す。|
| s | 0.0 から 1.0 | 彩度 (saturation) <br>小さいほど白っぽい色（淡い色）になる。|
| v | 0.0 から 1.0 | 明度 (value)<br>小さいほど黒っぽい色（暗い色）になる。 |

`h` は色相 (hue) で通常 0.0 から 360.0 の範囲の値ですが、角度と同じで 370.0 は 10.0 と同じ色を表します。-10.0 は 350.0 と同じ色を表します。`s` は彩度 (saturation) で 0.0 から 1.0 の範囲の値です。`v` は明度 (value) で 0.0 から 1.0 の範囲の値です。

淡い青色は `HSV{ 220.0, 0.4, 1.0 }` になります。これは h = 220.0°, s = 0.4, v = 1.0 です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景色を HSV で指定する
	Scene::SetBackground(HSV{ 220.0, 0.4, 1.0 });

	while (System::Update())
	{

	}
}
```

## 7.7 背景色を HSV で指定する (2)
`s` が 1.0, `v` が 1.0 の場合、`HSV{ h }` と書くこともできます。例えば `HSV{ 220.0 }` は `HSV{ 220.0, 1.0, 1.0 }` と同じです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 背景色を HSV で指定する
	Scene::SetBackground(HSV{ 220.0 });

	while (System::Update())
	{

	}
}
```

## 7.8 背景色を時間の経過とともに変化させる
背景色の変更は軽量な仕事です。`Scene::SetBackground()` はメインループ内に書いても問題ありません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/background/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double hue = (Scene::Time() * 60.0);

		// 背景色を HSV で指定する
		Scene::SetBackground(HSV{ hue });
	}
}
```

このコードでは、アプリケーションが起動してからの経過時間を`Scene::Time()` で秒単位で取得し、60.0 でかけた値を色相として使っています。つまり、6 秒かけて色が一周する背景色のアニメーションになっています。

??? summary "C++ の文法復習「const」"
	変数の前に `const` をつけると、その変数への再代入、あるいは変更処理を禁止できます。`const` がついている変数は、変更しようとするとコンパイルエラーになります。変更する必要のない変数には `const` をつけることで、意図しない変更を防ぐことができます。また「変更しない」意図が伝わるため、コードの読みやすさが改善されます。使える場面では積極的に `const` を使うのが良い C++ の書き方です。


## 振り返りチェックリスト
- [x] 背景色を指定するには `Scene::SetBackground()` を使うことを学んだ
- [x] `Palette::***` で定義されている色を使うことができることを学んだ
- [x] 色を RGB で指定するには `ColorF{ r, g, b }` または `ColorF{ gray }` を使うことを学んだ
- [x] 色を HSV で指定するには `HSV{ h, s, v }` または `HSV{ h }` を使うことを学んだ
- [x] `Scene::SetBackground()` はメインループ内で呼び出しても問題ないことを学んだ
