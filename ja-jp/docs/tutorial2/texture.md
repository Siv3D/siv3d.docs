# 25. テクスチャを描く
絵文字やアイコン、画像ファイルからテクスチャを作成し描画する方法を学びます。

画面に描画する画像データはテクスチャクラス `Texture` で管理します。テクスチャはいくつかの方法で作成できます。テクスチャの作成にはコストがかかるため、通常はメインループの前で行います。メインループ内で作成する必要がある場合には、毎フレーム作成されないような制御が必要です。

## 25.1 絵文字からテクスチャを作成する
`Texture 変数名{ U"絵文字"_emoji };` で、絵文字をもとに固定サイズ（136x128）のテクスチャを作成できます。

!!! info "絵文字を探す"
    - 絵文字の種類は [emojipedia :material-open-in-new:](https://emojipedia.org/){:target="_blank"} で探すと便利です。全部で 3700 種類以上が用意されています。
    - Windows の場合は、++windows+period++ で出てくる、OS 標準の絵文字入力メニューも使えます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/1.png)

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


## 25.2 アイコンからテクスチャを作成する
`Texture 変数名{ アイコン番号_icon, サイズ };` で、アイコンをもとにテクスチャを作成できます。アイコンは [Material Design Icons :material-open-in-new:](https://pictogrammers.com/library/mdi/){:target="_blank"} または [Font Awesome :material-open-in-new:](https://fontawesome.com/v5/search?o=r&m=free){:target="_blank"} で調べられる 16 進数コードに `_icon` を付けた値を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture icon1{ 0xF034E_icon, 80 };

	const Texture icon2{ 0xF0493_icon, 120 };

	while (System::Update())
	{
		icon1.drawAt(100, 100);

		icon2.drawAt(200, 300);

		icon1.drawAt(400, 300, ColorF{ 0.25 });

		icon2.drawAt(Cursor::Pos(), ColorF{ 0.5, 0.25, 0.0 });
	}
}
```


## 25.3 画像ファイルからテクスチャを作成する
`Texture 変数名{ U"ファイルパス" };` で、画像ファイルからテクスチャを作成できます。ファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します。

例えば `U"example/windmill.png"` とすると、実行ファイルがあるフォルダ（`App` フォルダ）の `example` フォルダの `windmill.png` というファイルを指します。

Siv3D では、次の 9 種類の画像フォーマットの読み込みをサポートしています。

| フォーマット   | 拡張子             | 対応状況       |
|----------|-----------------|:----------:|
| PNG      | png             | ✔          |
| JPEG     | jpg/jpeg/jfif   | ✔          |
| BMP      | bmp             | ✔          |
| SVG      | svg             | ✔          |
| GIF      | gif             | ✔          |
| TGA      | tga             | ✔          |
| PPM      | ppm/pgm/pbm/pnm | ✔          |
| WebP     | webp            | ✔          |
| TIFF     | tif/tiff        | ✔          |
| JPEG2000 | jp2             | (将来のバージョン) |
| DDS      | dds             | (将来のバージョン) |
| WBMP     | wbmp            | (将来のバージョン) |
| JPEG XL  | jxl             | (将来のバージョン) |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// 風車の画像
	const Texture texture1{ U"example/windmill.png" };

	// Siv3D くん（Siv3D の公式マスコットキャラクター）の画像
	const Texture texture2{ U"example/siv3d-kun.png" };

	while (System::Update())
	{
		texture1.draw(40, 20);

		texture2.draw(400, 100);
	}
}
```


## 25.4 画像クラス（Image）からテクスチャを作成する
プログラムで生成・加工した画像データ（`Image` クラス）からテクスチャを作成できます。`Image` クラスについては [チュートリアル 53. 画像編集](../tutorial3/image) を参照してください。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/4.png)

```cpp
# include <Siv3D.hpp>

Image MakeImage()
{
	Image image{ 256, 256 };

	for (int32 y = 0; y < image.height(); ++y)
	{
		for (int32 x = 0; x < image.width(); ++x)
		{
			image[y][x] = ColorF{ (x / 255.0), (y / 255.0), 0.0 };
		}
	}

	return image;
}

void Main()
{
	const Texture texture{ MakeImage() };

	while (System::Update())
	{
		texture.draw();
	}
}
```


## 25.5 ミップマップの生成
テクスチャを元のサイズよりも縮小して描画する場合には、**ミップマップ**を使用すると、滑らかな描画が可能になります。ミップマップとは、元の画像を縮小した画像の集合です。ミップマップを使用すると、縮小描画時のノイズと処理コストが提言します。

Siv3D では、ミップマップは `Texture` の内部で管理されていて、`Texture` を作成する際に `TextureDesc::Mipped` を指定することでミップマップが生成され、適切に使用されます。絵文字とアイコンからテクスチャを作成する際にはデフォルトでミップマップが生成されます。一方、画像ファイルや `Image` からテクスチャを作成する場合には明示的な `TextureDesc::Mipped` の指定が必要です。

| テクスチャの作成方法 | ミップマップの生成 |
|-----------------|:----------:|
| 絵文字から作成        | ✔          |
| アイコンから作成       | ✔          |
| 画像ファイルから作成     | `TextureDesc::Mipped` の指定が必要 |
| `Image` から作成    | `TextureDesc::Mipped` の指定が必要 |

ミップマップを生成すると、そのテクスチャのビデオメモリ使用量が約 30% 増加しますが、縮小描画時の処理負荷は大きく軽減されます。縮小描画を行わない場合には、ミップマップを生成しないという選択肢もあります。

次のサンプルでは、1 つ目のテクスチャはミップマップを生成せず、2 つ目のテクスチャはミップマップを生成して描画しています。縮小時にノイズが少ないことがわかります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ミップマップを生成しない
	const Texture texture1{ U"example/windmill.png" };

	// ミップマップを生成する
	const Texture texture2{ U"example/windmill.png", TextureDesc::Mipped };

	while (System::Update())
	{
		const double scale = Periodic::Sine0_1(12s);

		texture1.scaled(scale).drawAt(400, 150);

		texture2.scaled(scale).drawAt(400, 450);
	}
}
```


## 25.6 空のテクスチャ
`Texture` 型の変数は、デフォルトでは**空のテクスチャ**を持っています。絵文字やアイコン、画像ファイルのロードに失敗した場合も空のテクスチャになります。

空のテクスチャは、16x16 の黄色の画像で、**有効なテクスチャと同じように扱うことができ**、描画してもエラーは発生しません。

空のテクスチャであるかを調べるには、`if (texture.isEmpty())`, `if (texture)`, `if (not texture)` を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Texture texture1;

	Print << texture1.isEmpty();

    // テクスチャを代入する
	texture1 = Texture{ U"🐈"_emoji };

	Print << texture1.isEmpty();

	// 存在しない画像ファイルを指定する
	const Texture texture2{ U"example/aaa.png" };

	if (not texture2)
	{
		Print << U"Failed to load a texture";
	}

	while (System::Update())
	{
		// 空のテクスチャを描画する（16x16 の黄色い画像）
		texture2.drawAt(400, 300);
	}
}
```


## 25.7 テクスチャのサイズ
テクスチャの幅と高さを調べるには、`.width()`, `.height()`, `.size()` を使います。`.size()` は `Size` 型の値を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture1{ U"🐈"_emoji };

	Print << texture1.size();

	const Texture texture2{ U"example/windmill.png" };

	Print << texture2.width();

	Print << texture2.height();

	while (System::Update())
	{

	}
}
```


## 25.8 テクスチャを描画する

### 25.8.1 左上の座標を指定して描画する
テクスチャを画面に描画するには、描画するテクスチャの左上の座標を画面のどこに置くかを指定して `.draw(x, y)` または `.draw(pos)` します。座標を省略した場合、`.draw(Vec2{ 0, 0 })` として扱われます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/8a.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture1{ U"🐈"_emoji };

	const Texture texture2{ U"example/windmill.png" };

	while (System::Update())
	{
		// 座標を指定しない場合 (0, 0) から描画する
		texture1.draw();

		// テクスチャを座標 (200, 100) から描画する
		texture2.draw(200, 100);
	}
}
```

### 25.8.2 中心座標を指定して描画する
テクスチャの左上位置ではなく、中心座標を指定して描画するには、`.drawAt(x, y)` または `.drawAt(pos)` を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/8b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture1{ U"🐈"_emoji };

	const Texture texture2{ U"example/windmill.png" };

	while (System::Update())
	{
		// テクスチャを座標 (100, 100) を中心に描画する
		texture1.drawAt(100, 100);

		// テクスチャを座標 (400, 300) を中心に描画する
		texture2.drawAt(400, 300);
	}
}
```


### 25.8.3 それ以外の座標を指定して描画する
左上、中心以外の座標を指定する場合は、次の表のパターンを使って、`.draw(Arg::bottomLeft(x, y))` あるいは `.draw(Arg::bottomLeft = pos)` のようにします。この場合、テクスチャの左下が `x, y` または `pos` で指定した位置になるように描画されます。

| 座標指定 | 説明 |
|---|---|
| `Arg::topLeft` | テクスチャの左上の位置を指定する（通常の `.draw()` と同じ） |
| `Arg::topCenter` | テクスチャの上辺の中央を指定する |
| `Arg::topRight` | テクスチャの右上の位置を指定する |
| `Arg::leftCenter` | テクスチャの左辺の中央を指定する |
| `Arg::center` | テクスチャの中心を指定する（通常の `.drawAt()` と同じ） |
| `Arg::rightCenter` | テクスチャの右辺の中央を指定する |
| `Arg::bottomLeft` | テクスチャの左下の位置を指定する |
| `Arg::bottomCenter` | テクスチャの下辺の中央を指定する |
| `Arg::bottomRight` | テクスチャの右下の位置を指定する |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/8c.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture1{ U"🐈"_emoji };

	const Texture texture2{ U"example/windmill.png" };

	while (System::Update())
	{
		// テクスチャを座標 (600, 0) が右上になるように描画する
		texture1.draw(Arg::topRight = Vec2{ 800, 0 });

		// テクスチャを座標 (20, 580) が左下になるように描画する
		texture2.draw(Arg::bottomLeft(20, 580));
	}
}
```


## 25.9 色を乗算してテクスチャを描画する
テクスチャを描画する際に、色を乗算することができます。

元のテクスチャのピクセル色が `ColorF{ sr, sg, sb }` であるとき、色 `ColorF{ r, g, b }` を乗算すると、描画される色は `ColorF{ (sr * r), (sg * g), (sb * b) }` になります（通常のブレンドモード時）。

つまり、`ColorF{ 0.5 }` を乗算すると、色の成分がすべて半分になるということです。元のテクスチャのピクセル色が `ColorF{ 1.0 }` の場合は乗算した色がそのまま使われます。元のテクスチャのピクセル色が `ColorF{ 0.0 }` の場合は、どのような色を乗算しても `ColorF{ 0.0 }` で描画されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/9a.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture1{ 0xF034E_icon, 100 };

	const Texture texture2{ U"example/windmill.png" };

	while (System::Update())
	{
		texture1.drawAt(100, 100, ColorF{ 0.0 });

		texture1.drawAt(300, 100, ColorF{ 0.5 });

		texture1.drawAt(500, 100, ColorF{ 0.3, 0.8, 0.5 });

		texture2.draw(200, 200, ColorF{ 0.5 });
	}
}
```

不透明度（アルファ値）を使うこともできます。元のテクスチャのピクセル色が `ColorF{ sr, sg, sb }` で、書き込み先のピクセルの色が `ColorF{ dr, dg, db }` であるとき、色 `ColorF{ r, g, b, a }` を乗算すると、描画される色は `ColorF{ (sr * a + dr * (1 - a)), (sg * a + dg * (1 - a)), (sb * a + db * (1 - a)) }` になります（通常のブレンドモード時）。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/9b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture1{ 0xF034E_icon, 100 };

	const Texture texture2{ U"example/windmill.png" };

	while (System::Update())
	{
		Rect{ 100, 100, 600, 400 }.draw();

		texture1.drawAt(100, 100, ColorF{ 0.0, 0.2 });

		texture1.drawAt(300, 100, ColorF{ 0.5, 0.8 });

		texture1.drawAt(500, 100, ColorF{ 0.3, 0.8, 0.5, 0.5 });

		const double a = Periodic::Sine0_1(4s);

		texture2.draw(200, 200, ColorF{ 1.0, a });
	}
}
```


## 25.10 テクスチャを拡大縮小して描画する

### 25.10.1 倍率を指定する
テクスチャを`.scaled(s)` または `.scaled(sx, sy)` することで、`Texture` にサイズ情報を付加した `TextureRegion` オブジェクトを作成できます。具体的には縦横 s 倍あるいは (sx, sy) 倍の大きさに拡大縮小されたテクスチャです。

既存の `Texture` から `TextureRegion` を作成する操作は低コストなので、メインループ内で実行して問題ありません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/10a.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture{ U"🍎"_emoji };

	while (System::Update())
	{
		// 0.3 倍の大きさで描画する
		texture.scaled(0.3).drawAt(100, 100);

		// 1.0 倍の大きさで描画する
		texture.scaled(1.0).drawAt(200, 200);

		// 2.0 倍の大きさで描画する
		texture.scaled(2.0).drawAt(400, 400);

		// 幅 0.5 倍、高さ 4.0 倍の大きさで描画する
		texture.scaled(0.5, 4.0).drawAt(700, 300);
	}
}
```


### 25.10.2 幅と高さを指定する
倍率の代わりにピクセル数を指定して拡大縮小するには、`.resized(size)` または `.resized(width, height)` を使います。`.scaled()` と同じように、`Texture` にサイズ情報を付加した `TextureRegion` オブジェクトを作成します。`size` は `double` 型の値で長辺を指定するか、`SizeF` 型の値で幅と高さを指定します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/10b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture{ U"🍎"_emoji };

	while (System::Update())
	{
		// 長辺が 40 ピクセルになるように拡大縮小して描画する
		texture.resized(40).drawAt(100, 100);

		// 長辺が 100 ピクセルになるように拡大縮小して描画する
		texture.resized(100).drawAt(200, 200);

		// 幅 250 ピクセルになるように拡大縮小して描画する
		texture.resized(250).drawAt(400, 400);

		// 幅 80 ピクセル、高さ 400 ピクセルになるように拡大縮小して描画する
		texture.resized(80, 400).drawAt(700, 300);
	}
}
```


## 25.11 テクスチャを回転して描画する
`.rotated(angle)` または `.rotatedAt(pos, angle)` によって、テクスチャに回転情報を付加した `TexturedQuad` オブジェクトを作成できます。`TexturedQuad` は `Texture` のように `.draw()` または `.drawAt()` できます。`TextureRegion` と同様、`TexturedQuad` を作成するのは軽量な操作であるため、メインループ内で実行して問題ありません。

### 25.11.1 テクスチャの中心を軸にして回転する
`.rotated(angle)` は、テクスチャの中心を軸にして `angle` だけ回転した `TexturedQuad` を作成します。`angle` は `double` 型の値で、単位はラジアンです。

テクスチャの中心に画鋲を打ち込んだようなイメージで、テクスチャを回転させます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/11a.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture{ U"🍎"_emoji };

	while (System::Update())
	{
		// 時計回りに 15° 回転させて描画する
		texture.rotated(15_deg).drawAt(100, 100);

		// 時計回りに 180° 回転させて描画する
		texture.rotated(180_deg).drawAt(200, 300);

		// 時計回りに 45° 回転させて描画する
		texture.rotated(45_deg).drawAt(400, 300);

		// 0.5 倍に縮小して時計回りに 90° 回転させて描画する
		texture.scaled(0.5).rotated(90_deg).drawAt(600, 300);
	}
}
```


### 25.11.2 テクスチャ上の指定した座標を軸にして回転する
`.rotatedAt(pos, angle)` は、テクスチャ上の `pos` を軸にして `angle` だけ回転した `TexturedQuad` を作成します。`pos` は `Vec2` 型の値です。`angle` は `double` 型の値で、単位はラジアンです。

テクスチャ上の指定した座標に画鋲を打ち込んだようなイメージで、テクスチャを回転させます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/11b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture{ U"🍎"_emoji };

	while (System::Update())
	{
		const double angle = (Scene::Time() * 90_deg);

		// 画像中の (58, 13) を軸に回転させて、画面の中心に描画する
		texture.rotatedAt(Vec2{ 58, 13 }, angle).drawAt(Scene::Center());
	}
}
```


## 25.12 テクスチャを上下・左右反転して描画する
テクスチャを上下反転して描画するには、`.flipped()` または `.flipped(onOff)` を使います。テクスチャを左右反転して描画するには、`.mirrored()` または `.mirrored(onOff)` を使います。

これらの関数は `Texture` に上下反転または左右反転の情報を付加した `TextureRegion` オブジェクトを作成します。`onOff` は `bool` 型の値で、`true` のときに反転します。`onOff` を省略した場合は `true` とみなされます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/12.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture{ U"🐈"_emoji };

	while (System::Update())
	{
		// 左右反転して描画する
		texture.mirrored().drawAt(200, 100);

		// 左右反転して描画する
		texture.mirrored(true).drawAt(400, 100);

		// 通常
		texture.mirrored(false).drawAt(600, 100);

		// 上下反転して描画する
		texture.flipped().drawAt(200, 300);

		// 上下反転して描画する
		texture.flipped(true).drawAt(400, 300);

		// 通常
		texture.flipped(false).drawAt(600, 300);

		// 左右反転・上下反転して描画する
		texture.mirrored().flipped().drawAt(200, 500);

		// 左右反転・上下反転して描画する
		texture.mirrored(true).flipped(true).drawAt(400, 500);

		// 通常
		texture.mirrored(false).flipped(false).drawAt(600, 500);
	}
}
```


## 25.13 テクスチャの一部を描画する（ピクセル指定）
テクスチャの全部ではなく、一部の長方形の領域だけを描画したい場合は、`(x, y, w, h)` を使って、テクスチャの `(x, y)` から幅 `w`、高さ `h` を選択した `TexturedRegion` オブジェクトを作成し、それを描画します。`x`, `y`, `w`, `h` の単位はピクセルです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/13.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture1{ U"example/windmill.png" };

	const Texture texture2{ U"🍎"_emoji };

	while (System::Update())
	{
		// 画像の (250, 100) から幅 200, 高さ 150 の部分を描画する
		texture1(250, 100, 200, 150).draw(40, 20);

		// 画像の (0, 0) から幅 68, 高さ 64 の部分を描画する
		texture2(0, 0, 68, 64).drawAt(400, 300);
	}
}
```


## 25.14 テクスチャの一部を描画する（UV 指定）
テクスチャの一部の長方形の領域を選択する方法として、UV 座標を指定する方法もあります。UV 座標は、テクスチャの左上を (0.0, 0.0)、右下を (1.0, 1.0) としたときの座標で、画像の大きさに関係なく、常に 0.0 から 1.0 の範囲になります。

`.uv(u, v, w, h)` を使って、テクスチャの UV 座標 `(u, v)` から幅 `w`、高さ `h` を選択した `TexturedRegion` オブジェクトを作成し、それを描画します。

例えばテクスチャ `texture` のサイズが 400 x 200 のとき、`texture.uv(0.5, 0.0, 0.5, 1.0)` は `texture(200, 0, 200, 200)` と同じです。計算方法は `texture((texture.width() * 0.5), (texture.height() * 0.0), (texture.width() * 0.5), (texture.height() * 1.0))` です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/14.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture1{ U"example/windmill.png" };

	const Texture texture2{ U"🍎"_emoji };

	while (System::Update())
	{
		// 画像の UV 座標 (0.1, 0.2) から幅 0.5, 高さ 0.8 の部分を描画する
		texture1.uv(0.1, 0.2, 0.5, 0.8).draw(40, 20);

		// 画像の UV 座標 (0.5, 0.0) から幅 0.5, 高さ 0.75 の部分を描画する
		texture2.uv(0.5, 0.0, 0.5, 0.75).drawAt(400, 300);
	}
}
```


## 25.15 基本図形の形に合わせてテクスチャを描く
`Rect` や `RectF`, `Circle`, `Quad`, `RoundRect` に、テクスチャ全体やテクスチャの一部領域を貼り付けて描くことができます。図形を `shape`, `Texture` あるいは `TextureRegion` を `texture` とすると、`shape(texture).draw()` で、図形の形に合わせてテクスチャを描きます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/15.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture1{ U"example/windmill.png", TextureDesc::Mipped };
	const Texture texture2{ U"example/siv3d-kun.png", TextureDesc::Mipped };

	const Rect rect{ 430, 50, 100, 100 };
	const RoundRect roundRect{ 430, 190, 100, 100, 25 };
	const Circle circle{ 480, 380, 50 };

	while (System::Update())
	{
		// テクスチャを長方形に貼り付けて描画する
		Rect{ 50, 50, 350, 400 }(texture1).draw();

		rect.draw(HSV{ 0, 0.5, 1.0 });
		// テクスチャを長方形に貼り付けて描画する
		rect(texture2(90, 5, 110, 110)).draw();

		roundRect.draw(HSV{ 120, 0.5, 1.0 });
		// テクスチャを角丸長方形に貼り付けて描画する
		roundRect(texture2(90, 5, 110, 110)).draw();

		circle.draw(HSV{ 240, 0.5, 1.0 });
		// テクスチャを円に貼り付けて描画する
		circle(texture2(90, 5, 110, 110)).draw();
	}
}
```


## 25.16 Polygon の形に合わせてテクスチャを描く
`Polygon` にテクスチャを貼り付けるときは、より細かい制御ができます。`Polygon` の `.toBuffer2D(offset, size)` あるいは `.toBuffer(Arg::center = offset, size)` で、`Polygon` の形に合わせてテクスチャを描くための `Buffer2D` オブジェクトを作成します。

`offset`はテクスチャを画面座標基準でどの位置に貼り付けるかを指定します。`size` は貼り付けるときのテクスチャのサイズです。`size` が元のテクスチャのサイズより小さい場合、テクスチャは縮小されます。`size` が元のテクスチャのサイズより大きい場合、テクスチャは拡大されます。

`Buffer2D` の作成は少しだけコストがかかるため、可能であればメインループの前で作成し、作成したオブジェクトを使い回すようにするとよいです。

`Buffer2D` オブジェクトを `b`, `Texture`を `t` とすると、`b.draw(t)` で、`Polygon` の形に合わせてテクスチャを描きます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/16.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture1{ U"example/windmill.png", TextureDesc::Mipped };
	const Texture texture2{ U"example/siv3d-kun.png", TextureDesc::Mipped };

	const Polygon star = Shape2D::Star(180, Vec2{ 200, 200 });
	const Polygon hexagon = Shape2D::Hexagon(60, Vec2{ 480, 380 });

	while (System::Update())
	{
		const double xOffset = (200 + Periodic::Sine1_1(5s) * 80.0);

		// star に対し、(xOffset, 200) を画像の中心とするようにテクスチャを貼り付けて描画する
		star.toBuffer2D(Arg::center(xOffset, 200), texture1.size())
			.draw(texture1);

		hexagon.draw(HSV{ 240, 0.5, 1.0 });
		// hexagon に対し、(515, 560) を画像の中心とするようにテクスチャを貼り付けて描画する
		hexagon.toBuffer2D(Arg::center = Vec2{ 515, 560 }, texture2.size())
			.draw(texture2);
	}
}
```


## 25.17 長方形内にフィットするようにテクスチャを描く
あるサイズの枠内に、最大限大きくなるようにテクスチャを描くには、`.fitted(size)` を使います。`.fitted(size)` は、テクスチャのアスペクト比を保ったまま、幅 size.x, 高さ size.y の枠内に収まり、最大限大きくなるよう拡大縮小した `TextureRegion` を返します。

`Rect` および `RectF` には `.center()` という、長方形の中心座標を返すメンバ関数があります。これを組み合わせることで、枠内に収まる形で、最大限大きく、中心にテクスチャを描くことができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/17.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture1{ U"example/windmill.png", TextureDesc::Mipped };
	const Texture texture2{ U"example/siv3d-kun.png", TextureDesc::Mipped };

	const Rect rect1{ 50, 100, 320, 200 };
	const Rect rect2{ 400, 200, 300 };

	while (System::Update())
	{
		rect1.drawFrame(0, 8, ColorF{ 0.25 });
		texture1.fitted(rect1.size).drawAt(rect1.center());

		rect2.drawFrame(0, 8, ColorF{ 0.25 });
		texture2.fitted(rect2.size).drawAt(rect2.center());
	}
}
```


## 25.18 テクスチャを繰り返し敷き詰めて描く
通常のテクスチャアドレスモード（Clamp）では、テクスチャの範囲外を描こうとすると、その部分はテクスチャの端の色で塗りつぶされます。UV 座標で 0.0 より小さい値や 1.0 より大きい値を指定したとき、それぞれ 0.0 と 1.0 として扱ういうことです。時計の針で 13 を指そうとしても、時計の針が 12 から進まないイメージです。

一方、時計の針で 13 を指そうとしたとき、時計の針が 12 から進み、0 に戻って 1 になるように繰り返すこともでき、テクスチャでも同じことを行うことができます。UV 座標で 1.1 や 2.3, -0.3 といった値を指定したとき、それぞれ 0.1 や 0.3, 0.7 として扱うということです。このテクスチャアドレスモードは「繰り返し」を意味する Repeat と呼ばれます。


### 25.18.1 繰り返しの範囲を指定する
テクスチャアドレスモードを Repeat にするには、`const ScopedRenderStates2D sampler{ SamplerState::RepeatLinear };` というオブジェクトを作成します。すると、そのオブジェクトが有効な間は、テクスチャアドレスモードが Repeat になります。このオブジェクトは、スコープを抜けると自動的に破棄され、テクスチャアドレスモードは元に戻ります。詳しくはチュートリアル 39. 2D レンダーステートで解説します。

`Texture` を `.mapped(width, height)` すると、そのテクスチャを、幅 width, 高さ height の範囲に敷き詰めるように繰り返した `TextureRegion` が得られます。テクスチャアドレスモードが Repeat であるときにそれを描画すると、テクスチャが敷き詰められて描画されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/18a.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture{ U"🌳"_emoji };

	while (System::Update())
	{
		{
			// テクスチャアドレスモードをリピートに設定する
			const ScopedRenderStates2D sampler{ SamplerState::RepeatLinear };

			// 600x400 の範囲に敷き詰めるよう繰り返したテクスチャを描画する
			texture.mapped(600, 400).draw(0, 0);
		}
	}
}
```

### 25.18.2 繰り返しの回数を指定する
`Texture` を `.repeated(x, y)` すると、そのテクスチャを、横に x 回、縦に y 回繰り返した `TextureRegion` が得られます。テクスチャアドレスモードが Repeat であるときにそれを描画すると、テクスチャが繰り返されて描画されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/18b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture{ U"🌳"_emoji };

	while (System::Update())
	{
		{
			// テクスチャアドレスモードをリピートに設定する
			const ScopedRenderStates2D sampler{ SamplerState::RepeatLinear };

			// 横に 4, 縦に 3 繰り返したテクスチャを描画する
			texture.repeated(4, 3).draw(0, 0);
		}
	}
}
```


## 25.19 大きな画像を縮小して読み込む
解像度の大きい画像ファイルを縮小してからテクスチャにすることで、メモリの節約や描画速度の向上が期待できます。`Image` を `.scaled(scale)` すると、その画像を scale 倍に縮小した `Image` が得られます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/19.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// 画像を 1/4 に縮小してテクスチャを作成する
	const Texture texture{ Image{ U"example/bay.jpg"}.scaled(0.25) };

	Print << texture.size();

	while (System::Update())
	{
		texture.draw();
	}
}
```


## 25.20 ミップマップを明示的に指定してテクスチャを作成する
画像ファイルまたは `Image` からテクスチャを作成するとき、`TextureDesc::Mipped` を指定することで自動的に元の画像を縮小していくミップマップが生成されますが、ミップマップの内容を明示的に指定できます。

`Texture` のコンストラクタに、`Image` と `Array<Image>` を渡すことで、ミップマップの内容を指定できます。`Image` はミップマップの最上位レベルの画像で、`Array<Image>` はミップマップの下位レベルの画像の配列です。ミップマップの下位レベルの画像は、上位レベルの画像を 2 分の 1 ずつ縮小した画像です。

次のサンプルでは、テクスチャの縮小描画時に下位のミップマップが使われることが、色の変化によって可視化されます（ミップマップはレベルをまたいでブレンドされるため、色の変化はなめらかです）。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/texture/20.png)

```cpp
# include <Siv3D.hpp>

Texture CreateTexture()
{
	const Image image{ 320, 256, HSV{ 0 } };

	// 幅と高さを 2 分の 1 ずつ縮小した一連の画像
	const Array<Image> mipmaps
	{
		Image{ 160, 128, HSV{ 40 }},
		Image{ 80, 64, HSV{ 80 }},
		Image{ 40, 32, HSV{ 120 }},
		Image{ 20, 16, HSV{ 160 }},
		Image{ 10, 8, HSV{ 200 }},
		Image{ 5, 4, HSV{ 240 }},
		Image{ 2, 2, HSV{ 280 }},
		Image{ 1, 1, HSV{ 320 }},
	};

	return Texture{ image, mipmaps };
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture texture = CreateTexture();

	while (System::Update())
	{
		const double scale = Periodic::Sine0_1(10s);

		// 縮小率に応じて異なるレベルのミップマップテクスチャが使われる
		texture.scaled(scale).drawAt(Scene::Center());
	}
}
```
