# 勉強会 4 コマ目（絵文字と動き）

## 1. 絵文字を描く
絵文字を自由な場所に描くには、絵文字からテクスチャ（`Texture` クラス）を作成し、そのメンバ関数 `.drawAt()` を使います。

まず、`Texture 変数名{ U"絵文字"_emoji };` で絵文字のテクスチャを作成します。テクスチャの作成はコストがかかるため、**メインループの前**で行います。

### 1.1 絵文字を指定した場所に描く
作成したテクスチャを画面に表示するには、`.drawAt(x, y)` または `.drawAt(pos)` を使います。指定した座標を中心として絵文字が描かれます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/emoji/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji1{ U"🐈"_emoji };

	const Texture emoji2{ U"🍎"_emoji };

	while (System::Update())
	{
		emoji1.drawAt(100, 100);

		emoji2.drawAt(200, 300);

		emoji1.drawAt(400, 300);

		emoji2.drawAt(Cursor::Pos());
	}
}
```

!!! info "絵文字を探す"
	- 絵文字の種類は [emojipedia :material-open-in-new:](https://emojipedia.org/){:target="_blank"} で探すと便利です。全部で 3700 種類以上が用意されています。
	- Windows の場合は、++windows+period++ で出てくる、OS 標準の絵文字入力メニューも使えます。

### 1.2 絵文字の大きさを変える
デフォルトの絵文字の大きさは余白（透明部分）も含めて 136x128 ピクセルです。`.drawAt(x, y)` の前に `.scaled(s)` を挟むことで、テクスチャが `s` 倍拡大縮小されます。

例えば、`0.5` を指定すると、絵文字の大きさが 136x128 の半分の 68x64 で描かれます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/emoji/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji1{ U"🐈"_emoji };

	const Texture emoji2{ U"🍎"_emoji };

	while (System::Update())
	{
		emoji1.scaled(0.6).drawAt(100, 100);

		emoji2.scaled(0.3).drawAt(200, 300);

		emoji1.drawAt(400, 300);
	}
}
```


### 1.3 絵文字を回転させる
`.drawAt(x, y)` の前に `.rotated(angle)` を挟むと、テクスチャが時計回りに `angle` 度回転します。`angle` は 1 周 360° を 2π とするラジアンで指定します。`45_deg`, `90_deg` のように `_deg` を付けて表記すれば、度数法の角度をラジアンに変換してくれます。

| _deg 記法 | ラジアン |
| --- | --- |
| `0_deg` | 0.0 |
| `45_deg` | 0.78539816339 |
| `90_deg` | 1.57079632679 |
| `180_deg` | 3.14159265359 |
| `360_deg` | 6.28318530718 |


例えば、`10_deg` を指定すると、絵文字が時計回りに 10 度回転します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/emoji/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji1{ U"🐈"_emoji };

	const Texture emoji2{ U"🍎"_emoji };

	while (System::Update())
	{
		emoji1.rotated(10_deg).drawAt(100, 100);

		emoji2.rotated(180_deg).drawAt(200, 300);

		emoji1.rotated(45_deg).drawAt(400, 300);
	}
}
```


### 1.4 絵文字を左右反転させる
`.drawAt(x, y)` の前に `.mirrored(mirror)` を挟むと、`mirror` が `true` のときにテクスチャが左右反転されます。`false` の場合は元の向きが使われます。

次のコードは、マウスカーソルの X 座標を `Cursor::Pos().x` によって取得し、マウスカーソルが絵文字の右側にある場合、絵文字を左右反転して（猫を右に向けて）描画するサンプルです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/emoji/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"🐈"_emoji };

	while (System::Update())
	{
		const int32 cursorX = Cursor::Pos().x;

		// マウスカーソルの X 座標が 400 以下のとき true, 400 より大きいとき false になる
		emoji.mirrored(400 <= cursorX).drawAt(400, 300);
	}
}
```


## 2. 動きをつくる

### 2.1 絵文字を動かす
メインループのたびに位置をずらすことで移動のモーションをつくれます。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/13-3.1.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

	// 絵文字の X 座標
	double x = 100.0;

	while (System::Update())
	{
		emoji.drawAt(x, 300);

		// 毎フレーム 2.5 ピクセル移動
		x += 2.5;
	}
}
```

!!! warning "フレームレートに依存した動き"
	このコードは、フレームレートに依存した動きになります。フレームレートが低いときには、絵文字がゆっくり動き、フレームレートが高いときには、絵文字が速く動きます。本格的なゲーム開発では、前フレームからの経過時間（秒）を `double` 型で返す `Scene::DeltaTime()` を使って、フレームレートに依存しない動きをつくることが推奨されます。

	```cpp title="フレームレートに依存しない動き"
	#include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

		const Texture emoji{ U"☃️"_emoji };

		// 絵文字の X 座標
		double x = 100.0;

		while (System::Update())
		{
			emoji.drawAt(x, 300);

			// 毎秒 200 ピクセル移動
			x += (200.0 * Scene::DeltaTime());
		}
	}
	```


### 2.2 絵文字を回す
メインループのたびに角度をずらすことで回転のモーションをつくれます。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/13-3.2.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"🍣"_emoji };

	// 回転角度
	double angle = 0_deg;

	while (System::Update())
	{
		// 回転ずし
		emoji.rotated(angle).drawAt(400, 300);

		// 毎フレーム 1° 増やす
		angle += 1_deg;
	}
}
```


### 2.3 図形を動かす
2.1 と同じ要領で図形の位置をずらすことで移動のモーションをつくれます。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/13-3.3.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	double x = 100.0;

	while (System::Update())
	{
		Circle{ x, 200, 80 }.draw();

		// double 型を使う場合, Rect の代わりに RectF
		RectF{ x, 400, 100, 80 }.draw();

		x += 4.0;
	}
}
```


## 3. 図形を変数で扱う

### 3.1 図形クラスの変数
`Circle` は次のようなクラスであり、`Circle` 型の変数を作成することができます。

```cpp
struct Circle
{
	double x;
	double y;
	double r;
};
```

`Rect` は次のようなクラスであり、`Rect` 型の変数を作成することができます。

```cpp
struct Rect
{
	int32 x;
	int32 y;
	int32 w;
	int32 h;
};
```

`RectF` は次のようなクラスであり、`RectF` 型の変数を作成することができます。

```cpp
struct RectF
{
	double x;
	double y;
	double w;
	double h;
};
```

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-2.1.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// Circle 型の変数
	Circle circle{ 400, 300, 100 };

	// RectF 型の変数
	RectF rect{ 400, 300, 300, 200 };

	while (System::Update())
	{
		circle.draw(Palette::White);

		rect.draw(Palette::Seagreen);
	}
}
```


### 3.2 図形クラスのメンバ変数の操作
図形クラスのメンバ変数の値を次のように操作できます。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/14-2.2.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// Circle 型の変数
	Circle circle{ 400, 300, 100 };

	// RectF 型の変数
	RectF rect{ 400, 300, 300, 200 };

	while (System::Update())
	{
		circle.draw(Palette::White);

		// 円の半径を毎フレーム 0.5 増やす
		circle.r += 0.5;

		rect.draw(Palette::Seagreen);

		// 矩形の X 座標を毎フレーム 0.5 左にずらす
		rect.x -= 0.5;
	}
}
```

