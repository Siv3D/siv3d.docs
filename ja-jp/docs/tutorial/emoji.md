# 10. 絵文字を描く
絵文字を描く方法を学びます。

## 10.1 絵文字を指定した場所に描く
絵文字を自由な場所に描くには、絵文字からテクスチャ（`Texture` クラス）を作成し、そのメンバ関数 `.drawAt()` を使います。

まず、`Texture 変数名{ U"絵文字"_emoji };` で絵文字のテクスチャを作成します。テクスチャの作成はコストがかかるため、**メインループの前**で行います。

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


## 10.2 絵文字の大きさを変えて描く
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


## 10.3 絵文字を回転させて描く
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


## 10.4 絵文字を左右反転させて描く
`.drawAt(x, y)` の前に `.mirrored(mirror)` を挟むと、`mirror` が `true` のときにテクスチャが左右反転されます。`false` の場合は元の向きが使われます。

次のコードは、マウスカーソルの X 座標を `Cursor::Pos().x` によって取得し、マウスカーソルが絵文字の右側にある場合、絵文字を左右反転して（猫を右に向けて）描画するサンプルです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/emoji/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji1{ U"🐈"_emoji };

	while (System::Update())
	{
		const int32 cursorX = Cursor::Pos().x;

		emoji1.mirrored(400 <= cursorX).drawAt(400, 300);
	}
}
```


## 振り返りチェックリスト
- [x] 絵文字からテクスチャを作成する方法を学んだ
- [x] テクスチャの作成はコストがかかるため、メインループの前で行うことを学んだ
- [x] テクスチャを `.drawAt(x, y)`, `.drawAt(pos)` を使って指定した場所に描く方法を学んだ
- [x] テクスチャを `.scaled(s)` を使って拡大縮小する方法を学んだ
- [x] テクスチャを `.rotated(angle)` を使って回転させる方法を学んだ
- [x] 度数法をラジアンに変換する `_deg` を使う方法を学んだ
- [x] テクスチャを `.mirrored(mirrored)` を使って左右反転させる方法を学んだ
