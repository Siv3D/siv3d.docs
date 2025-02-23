# 63. 画像処理
画像処理を行うための機能と、その結果をシーンに表示する方法を学びます。

## 63.1 画像処理の概要
- `Texture` クラスに読み込んだ画像データは GPU のメモリ上に配置されるため、C++ プログラムを通して画像の内容にアクセスすることはできません
- 一方、`Image` クラスで読み込んだ（または作成した）画像データはメインメモリ上に配置されるため、`Array` や `Grid` のように、C++ プログラムで簡単に内容にアクセスできます
- `Image` には自身をシーンに描画する機能はなく、`Image` をもとに `Texture` や `DynamicTexture`（**63.11**）を作成し、テクスチャとして描画する必要があります

|  | Image | DynamicTexture | Texture |
|--|:--:|:--:|:--:|
| データの格納場所 | メインメモリ | GPU メモリ | GPU メモリ |
| 内容の更新 | ✅ | ✅<br>`.fill()` を使う | |
| 描画 | | ✅ | ✅ |
| CPU からのアクセス | ✅ | | |
| GPU（シェーダ）からのアクセス | | ✅ | ✅ |


## 63.2 Image クラスの基本（1）
- 画像データを扱うときは `Image` クラスを使います
- `Image` クラスは、画像データを `Gird<Color>` のようなインタフェースで扱います
- `Color` 型は、`ColorF` 型と異なり、r, g, b, a の各色を `uint8` 型で保持する 4 バイトの構造体です
- `Color` ⇔ `ColorF` は相互に変換できます

```cpp
struct Color
{
	uint8 r;
	uint8 g;
	uint8 b;
	uint8 a;
};
```

- 次のサンプルコードでは、サイズ 400 x 300 の白い画像を作成し、その左上 120 x 60 の領域を青色で塗りつぶします
- その画像をもとにテクスチャを作成し、シーンに描画します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Image image{ Size{ 400 ,300 }, Palette::White };

	for (int32 y = 0; y < 60; ++y)
	{
		for (int32 x = 0; x < 120; ++x)
		{
			image[y][x] = Color{ 0, 127, 255 };
		}
	}

	const Texture texture{ image };

	while (System::Update())
	{
		texture.draw();
	}
}
```


## 63.3 Image クラスの基本（2）
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/3.png)

```cpp

```


## 63.4 XXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/4.png)

```cpp

```


## 63.5 XXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/5.png)

```cpp

```


## 63.6 XXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/6.png)

```cpp

```


## 63.7 XXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/7.png)

```cpp

```


## 63.8 XXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/8.png)

```cpp

```


## 63.9 XXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/9.png)

```cpp

```


## 63.10 XXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/10.png)

```cpp

```


## 63.11 XXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/11.png)

```cpp

```


## 63.12 XXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/12.png)

```cpp

```













## 33.1 Image クラスの基本

次のサンプルでは、マウスカーソルで選択した、画像の任意の位置のピクセル色を取得し表示します。

![](/images/doc_v6/tutorial/33/1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// 画像ファイルから Image を作成
	const Image image{ U"example/windmill.png" };

	// Image から　Texture を作成
	const Texture texture{ image };

	while (System::Update())
	{
		const Point pos = Cursor::Pos();

		if (InRange(pos.x, 0, image.width() - 1)
			&& InRange(pos.y, 0, image.height() - 1))
		{
			// マウスカーソルの位置にあるピクセルの色を取得
			const Color pixelColor = image[pos];

			Rect{ 500, 20, 40 }.draw(pixelColor);

			PutText(U"{}"_fmt(pixelColor), Arg::topLeft(560, 20));
		}

		texture.draw();
	}
}
```


## 33.2 プログラムで画像を作成する
`Image` のコンストラクタに幅、高さ、色を渡して、画像を作成することができます。

![](/images/doc_v6/tutorial/33/2.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	const Image image{ 400, 300, Color{ 63, 127, 255 } };

	const Texture texture{ image };

	while (System::Update())
	{
		texture.draw();
	}
}
```

なお、`Texture` のコンストラクタに `Image` を渡したときには、画像データが `Texture` にコピーされるので、`Texture` を作成したあとで `Image` を破棄しても問題ありません。上記のプログラムでは、メインループ中に使われない `image` がメモリを消費したままなので、次のようにするとメモリ消費量を減らせます。

```cpp
# include <Siv3D.hpp>

Image MakeImage()
{
	return Image{ 400, 300, Color{ 63, 127, 255 } };
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


## 33.3 ピクセルを編集する
`Image` が持つ画像データの幅は `.width()`, 高さは `.height()`, 幅と高さは `.size()` で取得できます。次のようなループで、`Image` 内のすべてのピクセルにアクセスできます。

![](/images/doc_v6/tutorial/33/3.png)
```cpp
# include <Siv3D.hpp>

Image MakeImage()
{
	Image image{ 400, 300 };

	for (int32 y = 0; y < image.height(); ++y)
	{
		for (int32 x = 0; x < image.width(); ++x)
		{
			image[y][x] = ColorF{ (x / 399.0), (y / 299.0), 1.0 };
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

次のように `step(Size)` を使って、ループを 1 つにまとめることもできます。

```cpp
# include <Siv3D.hpp>

Image MakeImage()
{
	Image image{ 400, 300 };

	for (auto p : step(image.size()))
	{
		image[p] = ColorF{ (p.x / 399.0), (p.y / 299.0), 1.0 };
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


## 33.4 range-based for
range-based for を使って、すべてのピクセルにアクセスすることもできます。

![](/images/doc_v6/tutorial/33/4.png)
```cpp
# include <Siv3D.hpp>

Image MakeImage()
{
	Image image{ U"example/windmill.png" };

	for (auto& pixel : image)
	{
		// R 成分と B 成分を入れ替える
		std::swap(pixel.r, pixel.b);
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


## 33.5 画像を保存する
`Image` の画像データを画像ファイルとして保存するには `.save(path)` を使います。画像の保存形式は、`path` の拡張子から自動的に適切なものが選択されます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Image image{ U"example/windmill.png" };

	for (auto& pixel : image)
	{
		std::swap(pixel.r, pixel.b);
	}

	// 画像を保存
	image.save(U"tutorial1.png");

	while (System::Update())
	{

	}
}
```


## 33.6 ダイアログでファイル名を指定して画像を保存する
`Image` の画像データを、ダイアログでファイル名を指定して画像ファイルとして保存するには `.saveWithDialog()` を使います。`.save()`　同様、画像の保存形式は、`path` の拡張子から自動的に適切なものが選択されます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Image image{ U"example/windmill.png" };

	for (auto& pixel : image)
	{
		std::swap(pixel.r, pixel.b);
	}

	// ダイアログでファイル名を指定して画像を保存
	image.saveWithDialog();

	while (System::Update())
	{

	}
}
```


## 33.7 画像処理
`Image` には様々な画像処理関数が用意されています。どの画像処理も、自身を変更するメンバ関数と、画像処理後の結果を返すメンバ関数の 2 種類があります。

| 処理 | 結果の画像の例 | 自身を変更するメンバ関数 / 結果を返すメンバ関数 |
|--|:--:|--|
|色の反転|![](/images/doc_v6/tutorial/33/7.1.png)|`negate` / `negated`|
|グレイスケール化|![](/images/doc_v6/tutorial/33/7.2.png)|`grayscale` / `grayscaled`|
|セピアカラー|![](/images/doc_v6/tutorial/33/7.3.png)|`sepia` / `sepiaed`|
|ポスタライズ|![](/images/doc_v6/tutorial/33/7.4.png)|`posterize` / `posterized`|
|明度レベル変更|![](/images/doc_v6/tutorial/33/7.5.png)|`brighten` / `brightened`|
|左右反転|![](/images/doc_v6/tutorial/33/7.6.png)|`mirror` / `mirrored`|
|上下反転|![](/images/doc_v6/tutorial/33/7.7.png)|`flip` / `flipped`|
|90° 回転|![](/images/doc_v6/tutorial/33/7.8.png)|`rotate90` / `rotated90`|
|180° 回転|![](/images/doc_v6/tutorial/33/7.9.png)|`rotate180` / `rotated180`|
|270° 回転|![](/images/doc_v6/tutorial/33/7.10.png)|`rotate270` / `rotated270`|
|ガンマ補正|![](/images/doc_v6/tutorial/33/7.11.png)|`gammaCorrect` / `gammaCorrected`|
|二値化|![](/images/doc_v6/tutorial/33/7.12.png)|`threshold` / `thresholded`|
|大津の手法による二値化|![](/images/doc_v6/tutorial/33/7.13.png)|`threshold_Otsu` / `thresholded_Otsu`|
|適応的二値化|![](/images/doc_v6/tutorial/33/7.14.png)|`adaptiveThreshold` / `adaptiveThresholded`|
|モザイク|![](/images/doc_v6/tutorial/33/7.15.png)|`mosaic` / `mosaiced`|
|拡散|![](/images/doc_v6/tutorial/33/7.25.png)|`spread` / `spreaded`|
|ブラー|![](/images/doc_v6/tutorial/33/7.16.png)|`blur` / `blurred`|
|メディアンブラー|![](/images/doc_v6/tutorial/33/7.17.png)|`medianBlur` / `medianBlurred`|
|ガウスぼかし|![](/images/doc_v6/tutorial/33/7.18.png)|`gaussianBlur` / `gaussianBlurred`|
|バイラテラルフィルタ|![](/images/doc_v6/tutorial/33/7.19.png)|`bilateralFilter` / `bilateralFiltered`|
|膨張|![](/images/doc_v6/tutorial/33/7.20.png)|`dilate` / `dilated`|
|収縮|![](/images/doc_v6/tutorial/33/7.21.png)|`erode` / `eroded`|
|拡大縮小|![](/images/doc_v6/tutorial/33/7.22.png)|`scale` / `scaled`|
|周囲に枠を加える|![](/images/doc_v6/tutorial/33/7.23.png)|`border` / `bordered`|
|任意角度の回転|![](/images/doc_v6/tutorial/33/7.24.png)| なし / `rotated`|
|正方形での切り抜き|![](/images/doc_v6/tutorial/33/7.26.png)| なし / `squareClipped`|


```cpp
# include <Siv3D.hpp>

void Main()
{
	const Image image{ U"example/windmill.png" };

	// ガウスぼかしした画像からテクスチャを作成
	const Texture texture{ image.grayscaled() };

	while (System::Update())
	{
		texture.draw();
	}
}
```


## 33.8 画像に図形を書き込む
`Circle` や `Line`, `Rect` などの図形型を、メンバ関数 `.paint()` および `.overwrite()` を使って `Image` に書き込むことができます。`.paint()` はアルファ値に応じて色をブレンドします。`.overwrite()` は引数で指定した色をそのまま書き込むため、処理が高速です。また、次のサンプル星形のように、画像外にはみ出した部分は書き込まれません。

![](/images/doc_v6/tutorial/33/8.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Image image{ 600, 600, Palette::White };

	Circle{ 100, 100, 100 }.overwrite(image, Palette::Orange);

	Rect{ 150, 150, 300, 200 }.paint(image, ColorF{ 0.0, 1.0, 0.5, 0.5 });

	Line{ 100, 400, 400, 200 }.overwrite(image, 10, Palette::Seagreen);

	// Shape2D には .paint() / .overwrite() が無いので、.asPolygon() で Polygon 型にする
	Shape2D::Star(200, Vec2{ 500, 200 }).asPolygon().overwrite(image, Palette::Yellow);

	// 透明の穴をあける
	Rect{ 400, 400, 50 }.overwrite(image, ColorF{ 1.0, 0.0 });

	image.save(U"tutorial2.png");

	const Texture texture{ image };

	while (System::Update())
	{
		texture.draw();
	}
}
```


## 33.9 画像に別の画像を書き込む
`Image` を別の `Image` に書き込むことができます。書き込みの対象を自分自身にすることはできません。書き込みに使うメンバ関数は次の通りです。

| メンバ関数 | アルファブレンド | 書き込み先のアルファ値の更新 |
|--|--|--|
|`.paint()`<br>`.paintAt()`| ✔ | |
|`.stamp()`<br>`.stampAt()`| ✔ | 大きいほう |
|`.overwrite()`<br>`.overwriteAt()`| | ✔ |

`Image` は `Texture` のように絵文字やアイコンもロードできます。

![](/images/doc_v6/tutorial/33/9.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Image image{ 600, 600, Palette::White };

	const Image windmillImage{ U"example/windmill.png" };
	const Image catImage{ U"🐈"_emoji };

	windmillImage.overwrite(image, 40, 40);

	// 透過ピクセルに対する paint / stamp / overwrite の違い
	Rect{ 100, 400, 400, 40 }.overwrite(image, Color{ 255, 0 });
	catImage.paintAt(image, 150, 400);
	catImage.stampAt(image, 300, 400);
	catImage.overwriteAt(image, 450, 400);

	const Texture texture{ image };

	while (System::Update())
	{
		texture.draw();
	}
}
```

## 33.10 内容を更新できるテクスチャ DynamicTexture
ペイントアプリのように、`Image` をプログラムの実行中に頻繁に変更し、その結果をシーンに描きたい場合、`Image` の内容を変更するたびに古い `Texture` を破棄して新しい `Texture` を作成するのは非効率です。そのような用途では、`DynamicTexture` を 1 度だけ作成し、そのを更新するのが効率的です。

`DynamicTexture` は `Texture` のメンバ関数に加え、`.fill(image)` メンバ関数を持ちます。`.fill()` は、`DynamicTexture` が空の場合は `image` で新しいテクスチャを作成し、既にデータを持っている場合はその内容を `image` で置き換えます。このとき新旧の画像データの縦横サイズは一致している必要があります。`DynamicTexture` の `.fill()` は、既に保持しているデータを上書きするだけなので、新しく `Texture` を作成するよりも効率的です。

なお、`Image` への書き込みは `.draw()` と異なり、CPU 上で行われるため、大量の書き込みは実行時性能を低下させます。テクスチャに対して頻繁に複雑な書き込みを行いたい場合は、次の章で扱う `RenderTexture` を使うのが基本です。

### 33.10.1 絵文字を書き込む

![](/images/doc_v6/tutorial/33/10.1.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Image image{ 600, 600, Palette::White };
	const Image emoji{ U"😃"_emoji };

	DynamicTexture dtexture{ image };

	while (System::Update())
	{
		if (MouseL.down())
		{
			emoji.paintAt(image, Cursor::Pos());

			// DynamicTexture の中身を Image で更新
			dtexture.fill(image);
		}

		dtexture.draw();
	}
}
```

### 33.10.2 線を書き込む
次のようなプログラムでペイントアプリが作れます。

`Image` の `.fill(color)` は、その色で画像を塗りつぶすメンバ関数です。

![](/images/doc_v6/sample/application/sketch.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	// キャンバスのサイズ
	constexpr Size canvasSize{ 600, 600 };

	// ペンの太さ
	constexpr int32 thickness = 8;

	// ペンの色
	constexpr Color penColor = Palette::Orange;

	// 書き込み用の画像データを用意
	Image image{ canvasSize, Palette::White };

	// 表示用のテクスチャ（内容を更新するので DynamicTexture）
	DynamicTexture texture{ image };

	while (System::Update())
	{
		if (MouseL.pressed())
		{
			// 書き込む線の始点は直前のフレームのマウスカーソル座標
			// （初回はタッチ操作時の座標のジャンプを防ぐため、現在のマウスカーソル座標にする）
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());

			// 書き込む線の終点は現在のマウスカーソル座標
			const Point to = Cursor::Pos();

			// image に線を書き込む
			Line{ from, to }.overwrite(image, thickness, penColor);

			// 書き込み終わった image でテクスチャを更新
			texture.fill(image);
		}

		// 描いたものを消去するボタンが押されたら
		if (SimpleGUI::Button(U"Clear", Vec2{ 640, 40 }, 120))
		{
			// 画像を白で塗りつぶす
			image.fill(Palette::White);

			// 塗りつぶし終わった image でテクスチャを更新
			texture.fill(image);
		}

		// テクスチャを表示
		texture.draw();
	}
}
```


## 33.11 画像にテキストを書き込む
画像にテキストを書き込むには、`Font` から各文字の画像を `BitmapGlyph` 型で取得し、その画像をチュートリアル 14.19 の自由描画の要領で書き込みます、

![](/images/doc_v6/tutorial/33/11.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	Image image{ 600, 600, Palette::White };

	const Font font{ 60, Typeface::Bold };
	{
		const String text = U"Hello, Siv3D!\nこんにちは。";
		constexpr Vec2 basePos{ 20, 20 };
		Vec2 penPos{ basePos };

		for (const auto& ch : text)
		{
			// 改行文字なら
			if (ch == U'\n')
			{
				// ペンの X 座標をリセット
				penPos.x = basePos.x;

				// ペンの Y 座標をフォントの高さ分進める
				penPos.y += font.height();

				continue;
			}

			const BitmapGlyph bitmapGlyph = font.renderBitmap(ch);

			// 文字のテクスチャをペンの位置に文字ごとのオフセットを加算して描画
			// .asPoint() は Vec2 を Point に変換する関数
			bitmapGlyph.image.paint(image, (penPos + bitmapGlyph.getOffset()).asPoint(), Palette::Seagreen);

			// ペンの X 座標を文字の幅の分進める
			penPos.x += bitmapGlyph.xAdvance;
		}
	}

	const Texture texture{ image };

	while (System::Update())
	{
		texture.draw();
	}
}
```

