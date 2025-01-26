# 13. 絵文字を描く
画面に絵文字を描く方法を学びます。

## 13.1 テクスチャと絵文字

### テクスチャ
- Siv3D では、画面に描く画像を**テクスチャ**（`Texture` クラス）で管理します
- テクスチャは、画像ファイルや、プログラムで生成した画像から作成できます
- テクスチャの最も簡単な作り方は、絵文字から作ることです

### 絵文字の種類
- Siv3D には 3,000 種類以上の絵文字が標準で同梱されています
- 次のような簡単なコードで、絵文字からテクスチャを作成できます

```cpp
Texture texture{ U"🐈"_emoji };
```

- Siv3D で使える絵文字一覧は [Emojipedia: Google Noto Color Emoji :material-open-in-new:](https://emojipedia.org/ja/google){:target="_blank"} で確認できます
- Siv3D ではどのプラットフォーム（Windows, macOS, Linux, Web）でも同じデザインの絵文字を描画できます


## 13.2 絵文字を描く
- テクスチャの作成はコストがかかるため、**メインループの前**で行います
- テクスチャを画面に表示するには、`.drawAt(x, y)` または `.drawAt(pos)` を使います
    - 指定した座標を中心としてテクスチャを描画します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/emoji/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji1{ U"🐈"_emoji };
	const Texture emoji2{ U"🍎"_emoji };

	while (System::Update())
	{
		emoji1.drawAt(100, 100);
		emoji1.drawAt(400, 300);

		emoji2.drawAt(200, 300);        
		emoji2.drawAt(Cursor::Pos());
	}
}
```


## 13.3 絵文字を拡大縮小する
- デフォルトの絵文字の大きさは余白（透明部分）も含めて 136x128 ピクセルです
- `.drawAt()` の前に `.scaled(倍率)` を挟むことで、テクスチャを指定した倍率で拡大縮小して描画できます
- 例えば `.scaled(0.5).drawAt(Cursor::Pos());` は、テクスチャを 50 % の大きさで描画します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/emoji/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji1{ U"🐈"_emoji };
	const Texture emoji2{ U"🍎"_emoji };

	while (System::Update())
	{
		emoji1.scaled(0.5).drawAt(100, 100);
		emoji1.scaled(2).drawAt(400, 300);

		emoji2.scaled(0.3).drawAt(200, 300);
	}
}
```


## 13.4 絵文字を回転させる
- `.drawAt()` の前に `.rotated(時計回りの角度)` を挟むことで、テクスチャを指定した角度で回転させて描画できます
- 角度は 1 周 360° を 2π とするラジアン単位で指定します
- 慣れている度数法を使いたい場合、`45_deg`, `90_deg` のように度数法をラジアンに変換する `_deg` リテラルを使います

| _deg 記法 | ラジアン |
| --- | --- |
| `0_deg` | 0.0 |
| `45_deg` | 0.78539816339 |
| `90_deg` | 1.57079632679 |
| `180_deg` | 3.14159265359 |
| `360_deg` | 6.28318530718 |

- 例えば `10_deg` を指定すると、絵文字が時計回りに 10° 回転します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/emoji/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji1{ U"🐈"_emoji };
	const Texture emoji2{ U"🍎"_emoji };

	while (System::Update())
	{
		emoji1.rotated(10_deg).drawAt(100, 100);
		emoji1.rotated(-45_deg).drawAt(400, 300);

		emoji2.rotated(180_deg).drawAt(200, 300);
	}
}
```


## 13.5 絵文字の拡大縮小と回転を組み合わせる
- `.scaled(倍率).rotated(時計回りの角度)` を使うことで、拡大縮小と回転を同時に行えます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/emoji/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji1{ U"🐈"_emoji };
	const Texture emoji2{ U"🍎"_emoji };

	while (System::Update())
	{
		emoji1.scaled(0.5).rotated(10_deg).drawAt(100, 100);
		emoji1.scaled(2).rotated(-45_deg).drawAt(400, 300);

		emoji2.scaled(0.3).rotated(180_deg).drawAt(200, 300);
	}
}
```


## 13.6 絵文字を左右反転させる
- `.drawAt()` の前に `.mirrored(反転)` を挟むことで、テクスチャを左右反転して描画できます
- `反転` は `bool` 型で、`true` を指定すると左右反転します。`false` の場合は元の向きで描画します
- 次のコードは、マウスカーソルの X 座標を `Cursor::Pos().x` によって取得し、マウスカーソルが絵文字の右側にある場合、絵文字を左右反転して（猫を右に向けて）描画します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/emoji/6.png)

```cpp title="マウスカーソルの X 座標に応じて絵文字を左右反転させる"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji1{ U"🐈"_emoji };

	while (System::Update())
	{
		const int32 cursorX = Cursor::Pos().x;

		emoji1.mirrored(400 <= cursorX).drawAt(400, 300);
	}
}
```


## 振り返りチェックリスト
- [x] 画像を描くときはテクスチャを使うことを学んだ
- [x] 絵文字からテクスチャを作成できることを学んだ
- [x] Siv3D で使える絵文字は 3,000 種類以上あり、どのプラットフォームでも同じデザインで描画できることを学んだ
- [x] テクスチャはメインループの前で作成することを学んだ
- [x] `.drawAt(x, y)` または `.drawAt(pos)` でテクスチャを画面に表示することを学んだ
- [x] `.scaled(倍率)` でテクスチャを拡大縮小することを学んだ
- [x] `.rotated(時計回りの角度)` でテクスチャを回転させることを学んだ
- [x] `_deg` リテラルを使うと、角度を度数法で記述できることを学んだ
- [x] `.scaled(倍率).rotated(時計回りの角度)` で、テクスチャの拡大縮小と回転を同時に行うことを学んだ
- [x] `.mirrored(反転)` でテクスチャを左右反転させることを学んだ
