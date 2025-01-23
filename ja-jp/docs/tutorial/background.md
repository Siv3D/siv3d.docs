# 8. 背景の色を変える
画面の背景の色を変える方法を学びます。

## 8.1 背景を白色にする
- 背景の色を変更するには `Scene::SetBackground(色)` を使います
- 白色は `Palette::White` です
- 一度変更した背景色は、再度変更するまでそのままです

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/1.png)

```cpp hl_lines="5-6"
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


## 8.2 背景を黒色にする
- 黒色は `Palette::Black` です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/2.png)

```cpp hl_lines="5-6"
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


## 8.3 背景をそれ以外の色にする
- `Palette::***` では [HTML カラー :material-open-in-new:](https://voidproc.github.io/siv3d-palette-browser/){:target="_blank"} の色名を使えます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/3.png)

```cpp hl_lines="5-6"
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


## 8.4 色を RGB で指定する（1）
- 色を RGB で指定するには、`ColorF{ r, g, b }` を使います
- `r`, `g`, `b` はそれぞれ 0.0 から 1.0 の範囲の値で、それぞれ赤、緑、青の成分を表します
- 例えば、淡い水色は `ColorF{ 0.8, 0.9, 1.0 }` （r: 80 %, g: 90 %, b: 100 %）です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/4.png)

```cpp hl_lines="5-6"
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


## 8.5 色を RGB で指定する（2）
- RGB の各成分が等しい `ColorF{ 0.6, 0.6, 0.6 }` のような色は、グレースケールの色（白～灰色～黒）になります
- このような色は `ColorF{ gray }` と短く書くことができます
- 例えば `ColorF{ 0.8 }` は `ColorF{ 0.8, 0.8, 0.8 }` と同じです
- 白色は `ColorF{ 1.0 }`, 灰色は `ColorF{ 0.5 }`, 黒色は `ColorF{ 0.0 }` です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/5.png)

```cpp hl_lines="5-6"
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


## 8.6 色を HSV で指定する（1）
- **色相**、**彩度**、**明度**の 3 要素で色を指定する **HSV 表色系**を使うこともできます
- HSV 表色系で色を指定するには、`HSV{ h, s, v }` を使います。

### 色相
- `h` は色相 (hue) で、0.0° から 360.0° までの角度で色を表します
- 色の系統（赤・黄・緑・青・紫など）を直感的に扱うことができ、角度を回転（加減算）させることで、色相環（下図）に沿ったなめらかな色変化が可能です
- 角度と同じで 370.0° は 10.0° と同じ色を表します。-10.0° は 350.0° と同じ色を表します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/hue.png)

### 彩度
- `s` は彩度（saturation）で、最も淡い 0.0 から最も鮮やかな 1.0 の範囲で色の鮮やかさを表現します
- 0.0 に近づくほど白っぽい色（淡い色）になります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/saturation.png)

### 明度

- `v` は明度（value）で、最も暗い 0.0 から最も明るい 1.0 の範囲で色の明るさを制御します
- 0.0 に近づくほど黒っぽい色（暗い色）になります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/value.png)

| 成分 | 値の範囲 | 説明 |
| --- | --- | --- |
| h | 0.0 から 360.0（範囲外も可能）| **色相**：色相環に対応する色を表す。|
| s | 0.0 から 1.0 | **彩度**：小さいほど白っぽい色（淡い色）になる。|
| v | 0.0 から 1.0 | **明度**：小さいほど黒っぽい色（暗い色）になる。 |

- 例えば `HSV{ 220.0, 0.4, 1.0 }`（h: 220.0°, s: 0.4, v: 1.0）は淡い青色になります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/6.png)

```cpp hl_lines="5-6"
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

## 8.7 色を HSV で指定する（2）
- `HSV{ h }` と短く書いた場合、`HSV{ h, 1.0, 1.0 }` と同じです
- 例えば `HSV{ 220.0 }` は `HSV{ 220.0, 1.0, 1.0 }` です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/7.png)

```cpp hl_lines="5-6"
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

## 8.8 背景色を時間の経過とともに変化させる
- 背景色の変更は軽量な仕事です。`Scene::SetBackground()` をメインループ内に書いても問題ありません
- 次のコードでは、アプリケーションが起動してからの経過時間（秒）を `Scene::Time()` で取得し、それに 60 をかけた値を背景色の色相として使っています
	- つまり、6 秒かけて色が一周する、背景色のアニメーションです

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/background/8.png)

```cpp hl_lines="7 10"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		const double hue = (Scene::Time() * 60);

		// 背景色を HSV で指定する
		Scene::SetBackground(HSV{ hue });
	}
}
```




## 振り返りチェックリスト
- [x] `Scene::SetBackground()` を使って背景色を変更することを学んだ
- [x] `Palette::***` を使って色名で色を指定することを学んだ
- [x] `ColorF{ r, g, b }` または `ColorF{ gray }` を使って RGB で色を指定することを学んだ
- [x] `HSV{ h, s, v }` または `HSV{ h }` を使って HSV で色を指定することを学んだ
- [x] `Scene::SetBackground()` は軽量なので、メインループ内でも使えることを学んだ
