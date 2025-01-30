# 30. テクスチャを描く
絵文字やアイコン、画像ファイルからテクスチャを作成し描画する方法を学びます。

## 30.1 テクスチャの作成と描画

### テクスチャの作成
- 画面に描画する画像はテクスチャクラス `Texture` で管理します
- テクスチャの作成にはいくつかの方法があります
	- **30.2** 絵文字から作成
	- **30.3** アイコンから作成
	- **30.4** 画像ファイルから作成
	- **30.5** 画像データから作成
- テクスチャの作成にはコストがかかるため、通常はメインループの前で行います
- メインループ内で作成する場合には、毎フレーム作成されないような制御が必要です

### テクスチャの描画
- テクスチャを描画するには `Texture` のメンバ関数を使います
	- **30.9** 左上座標を指定した描画 `.draw()`
	- **30.10** 中心座標を指定した描画 `.drawAt()`
	- **30.11** それ以外の座標を指定した描画 `.draw(Arg::...)`
- 拡大縮小・回転・反転・部分切り出しなどの操作を適用したテクスチャを表現する、次のようなクラスが用意されています
	- `TextureRegion`
	- `TexturedQuad`
	- `TexturedCircle`
	- `TexturedRoundRect`
- これらのクラスは `Texture` のメンバ関数によって作成されますが、ほとんど意識することなく `Texture` と同様に使えます

```cpp
// .scaled() は TextureRegion を返す
// .rotated() は TexturedQuad を返す
texture.scaled(2.0).rotated(30_deg).drawAt(400, 300);
```


## 30.2 絵文字から作成
- Siv3D には Unicode 15.1 に準拠した 3,700 種類以上の絵文字が標準で同梱されています
- `Texture{ U"絵文字"_emoji }` で、絵文字からテクスチャを作成します

```cpp
Texture texture{ U"🐈"_emoji };
```

- 絵文字一覧は [Emojipedia: Google Noto Color Emoji :material-open-in-new:](https://emojipedia.org/ja/google){:target="_blank"} で確認できます
- どのプラットフォーム（Windows, macOS, Linux, Web）でも同じデザインの絵文字を描画できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/2.png)

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


## 30.3 アイコンから作成
- Siv3D には 7,000 種類以上のアイコンが標準で同梱されています
- `Texture{ 0xアイコン番号_icon, サイズ }` で、アイコンからテクスチャを作成します

```cpp
Texture texture{ 0xF0493_icon, 80 };
```

- アイコン番号は [Material Design Icons :material-open-in-new:](https://pictogrammers.com/library/mdi/){:target="_blank"} または [Font Awesome :material-open-in-new:](https://fontawesome.com/v5/search?o=r&m=free){:target="_blank"} の 16 進数コードです
- どのプラットフォーム（Windows, macOS, Linux, Web）でも同じデザインのアイコンを描画できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture icon1{ 0xF0493_icon, 80 };
	const Texture icon2{ 0xF0787_icon, 80 };

	while (System::Update())
	{
		icon1.drawAt(200, 200);
		icon2.drawAt(400, 200, ColorF{ 0.2 });
	}
}
```


## 30.4 画像ファイルから作成
- 画像ファイルからテクスチャを作成するには、`Texture{ ファイルパス }` を使います
- ファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します
	- 例えば `U"example/windmill.png"` は、実行ファイルがあるフォルダ（`App` フォルダ）の `example` フォルダにある `windmill.png` というファイルを指します
- Siv3D は次の 9 種類の画像フォーマットの読み込みをサポートします

| フォーマット   | 拡張子             | 対応状況       |
|----------|-----------------|:----------:|
| PNG      | png             | ✅          |
| JPEG     | jpg / jpeg / jfif   | ✅          |
| BMP      | bmp             | ✅          |
| SVG      | svg             | ✅          |
| GIF      | gif             | ✅          |
| TGA      | tga             | ✅          |
| PPM      | ppm / pgm / pbm / pnm | ✅          |
| WebP     | webp            | ✅          |
| TIFF     | tif / tiff        | ✅          |
| DDS      | dds             | (将来のバージョン) |
| WBMP     | wbmp            | (将来のバージョン) |
| JPEG XL  | jxl             | (将来のバージョン) |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

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


## 30.5 画像データから作成
- プログラムで生成・加工した画像データ（`Image` クラス）からテクスチャを作成できます
	- `Image` クラスについては [**チュートリアル ??. 画像編集**](../tutorial4/image.md) を参照してください
- `Texture{ 画像データ }` で、画像データからテクスチャを作成します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/5.png)

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
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture texture{ MakeImage() };

	while (System::Update())
	{
		texture.draw();
	}
}
```


## 30.6 テクスチャのサイズ
- テクスチャの幅（ピクセル）は `.width()` で取得できます。戻り値は `int32` 型です
- テクスチャの高さ（ピクセル）は `.height()` で取得できます。戻り値は `int32` 型です
- 幅と高さを同時に取得するには `.size()` を使います。戻り値は `Size`（`Point`） 型です

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"example/windmill.png" };
	const Texture emoji{ U"🐈"_emoji };

	Print << texture.width();
	Print << texture.height();
	Print << emoji.size();

	while (System::Update())
	{

	}
}
```
```txt title="出力"
480
320
(136, 128)
```


## 30.7 空のテクスチャ
- `Texture` 型の変数は、デフォルトでは**空のテクスチャ**を持っています
- 空のテクスチャは、16x16 の黄色の画像で、**有効なテクスチャと同じように扱うことができます**
- 絵文字やアイコン、画像ファイルのロードに失敗した場合にも空のテクスチャになります
- 空のテクスチャであるかを調べるには、`if (texture.isEmpty())`, `if (texture)`, `if (not texture)` を使います
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Texture texture1;

	Print << texture1.isEmpty();

	// テクスチャを代入する
	texture1 = Texture{ U"🐈"_emoji };

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


## 30.8 ミップマップの生成
- 1/2, 1/4, ... サイズの縮小版画像を事前に内部で生成しておく**ミップマップ**という技術があります
- ミップマップを使うと、そのテクスチャのビデオメモリ使用量が約 30 % 増加しますが、次のようなメリットがあります
	- 縮小描画時のノイズやちらつきが少なくなる（画質の向上）
	- 縮小描画時の処理負荷が低減する
- 一切縮小描画を行わない場合には、ミップマップを生成しないという選択肢もあります
- Siv3D ではミップマップは `Texture` の内部で管理されています
- 絵文字やアイコンからテクスチャを作成する際にはデフォルトでミップマップが生成されます
- 画像ファイルや `Image` からテクスチャを作成する場合には、コンストラクタで明示的に `TextureDesc::Mipped` の指定が必要です

| テクスチャの作成方法 | ミップマップの自動生成 |
|-----------------|:----------:|
| 絵文字から作成        | ✅          |
| アイコンから作成       | ✅          |
| 画像ファイルから作成     | `TextureDesc::Mipped` の指定が必要 |
| `Image` から作成    | `TextureDesc::Mipped` の指定が必要 |

- 次のサンプルでは、1 つ目のテクスチャはミップマップを生成せず、2 つ目のテクスチャはミップマップを生成して描画しています
- ミップマップを使用したほうが、縮小時のノイズが少ないことがわかります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48 };

	const Texture texture1{ U"example/windmill.png" };
	const Texture texture2{ U"example/windmill.png", TextureDesc::Mipped };

	while (System::Update())
	{
		const double scale = Periodic::Sine0_1(12s);

		font(U"No mipmaps").draw(30, Vec2{ 20, 20 }, ColorF{ 0.2 });
		font(U"Mipmaps").draw(30, Vec2{ 20, 300 }, ColorF{ 0.2 });

		texture1.scaled(scale).draw(240, 20);
		texture2.scaled(scale).draw(240, 300);
	}
}
```


## 30.9 左上座標を指定した描画
- 左上の座標を指定してテクスチャを描画するには、`.draw()` を使います

| コード | 説明 |
|---|---|
| `.draw(色 = Palette::White)` | テクスチャを座標 (0, 0) から描画する |
| `.draw(x, y, 色 = Palette::White)` | テクスチャを座標 (x, y) から描画する |
| `.draw(pos, 色 = Palette::White)` | テクスチャを座標 pos から描画する |
| `.draw(x, y, Arg::top = 上側の色, Arg::bottom = 下側の色)` | 上下の色を指定して描画する |
| `.draw(x, y, Arg::left = 左側の色, Arg::right = 右側の色)` | 左右の色を指定して描画する |
| `.draw(pos, Arg::top = 上側の色, Arg::bottom = 下側の色)` | 上下の色を指定して描画する |
| `.draw(pos, Arg::left = 左側の色, Arg::right = 右側の色)` | 左右の色を指定して描画する |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture texture1{ U"🐈"_emoji };
	const Texture texture2{ U"example/windmill.png" };

	while (System::Update())
	{
		texture1.draw();

		texture2.draw(400, 300);
	}
}
```


## 30.10 中心座標を指定した描画
- 中心の座標を指定してテクスチャを描画するには、`.drawAt()` を使います

| コード | 説明 |
|---|---|
| `.drawAt(x, y, 色 = Palette::White)` | テクスチャを座標 (x, y) を中心に描画する |
| `.drawAt(pos, 色 = Palette::White)` | テクスチャを座標 pos を中心に描画する |
| `.drawAt(x, y, Arg::top = 上側の色, Arg::bottom = 下側の色)` | 上下の色を指定して描画する |
| `.drawAt(x, y, Arg::left = 左側の色, Arg::right = 右側の色)` | 左右の色を指定して描画する |
| `.drawAt(pos, Arg::top = 上側の色, Arg::bottom = 下側の色)` | 上下の色を指定して描画する |
| `.drawAt(pos, Arg::left = 左側の色, Arg::right = 右側の色)` | 左右の色を指定して描画する |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture texture1{ U"🐈"_emoji };
	const Texture texture2{ U"example/windmill.png" };

	while (System::Update())
	{
		texture1.drawAt(0, 0);

		texture2.drawAt(400, 300);
	}
}
```


## 30.11 それ以外の座標を指定した描画
- **右端の中心位置**を指定してテクスチャを描画するには、次の方法を使います
	- `.draw(Arg::topRight = pos, ...)`
	- `.draw(Arg::topRight(x, y), ...)
- このように指定できる基準位置は、全部で 9 種類あります

| 基準位置 | 説明 |
|---|---|
| `Arg::topLeft` | テクスチャの左上。`.draw()` と同じ |
| `Arg::topCenter` | 上辺の中央 |
| `Arg::topRight` | 右上|
| `Arg::leftCenter` | 左辺の中央 |
| `Arg::center` | 中心。`.drawAt()` と同じ |
| `Arg::rightCenter` | 右辺の中央 |
| `Arg::bottomLeft` | 左下 |
| `Arg::bottomCenter` | 下辺の中央 |
| `Arg::bottomRight` | 右下 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/11.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture texture1{ U"🐈"_emoji };
	const Texture texture2{ U"example/windmill.png" };

	while (System::Update())
	{
		texture1.draw(Arg::topRight = Vec2{ 800, 0 });

		texture2.draw(Arg::bottomLeft(20, 580));
	}
}
```


## 30.12 色を乗算した描画

### 30.12.1 RGB 各成分を乗算
- `.draw()` と `.drawAt()` ではテクスチャに乗算する色を指定できます
- テクスチャのピクセル `ColorF{ sr, sg, sb }` を描くとき、色 `ColorF{ r, g, b }` を乗算すると、描画される色は `ColorF{ (sr * r), (sg * g), (sb * b) }` になります（通常のブレンドモード時）
- デフォルトでは `Palette::White`（`ColorF{ 1.0 }`）が乗算色として使われます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/12-1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture texture{ U"example/windmill.png" };
	const Texture icon{ 0xF0493_icon, 80 };

	while (System::Update())
	{
		texture.draw(40, 40, ColorF{ 0.4 });

		icon.draw(600, 40, ColorF{ 0.5, 0.0, 0.0 });

		icon.draw(600, 140, ColorF{ 0.0, 0.5, 0.0 });
	}
}
```

### 30.12.2 アルファ値の使用
- 不透明度（アルファ値）を使うこともできます
- テクスチャのピクセル `ColorF{ sr, sg, sb }` を、書き込み先のピクセル `ColorF{ dr, dg, db }` に描くとき、描画される色は `ColorF{ (sr * a + dr * (1 - a)), (sg * a + dg * (1 - a)), (sb * a + db * (1 - a)) }` になります（通常のブレンドモード時）

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/12-2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture texture{ U"example/windmill.png" };
	const Texture icon{ 0xF0493_icon, 80 };

	while (System::Update())
	{
		texture.draw(40, 40, ColorF{ 1.0, 0.5 });

		icon.draw(500, 40, ColorF{ 0.5, 0.0, 0.0, 0.3 });

		icon.draw(500, 140, ColorF{ 0.0, 0.5, 0.0, 0.3 });
	}
}
```


## 30.13 拡大縮小した描画
- テクスチャを拡大縮小して描画するには、次のメンバ関数を使って、拡大縮小を適用した `TextureRegion` を作成します

| コード | 説明 |
|---|---|
| `.scaled(s)` | テクスチャを縦横 `s` 倍の大きさに拡大縮小した `TextureRegion` を作成する |
| `.scaled(sx, sy)` | テクスチャを縦横 `sx`, `sy` 倍の大きさに拡大縮小した `TextureRegion` を作成する |
| `.resized(size)` | テクスチャの長辺を `size`（ピクセル）の大きさに拡大縮小した `TextureRegion` を作成する |
| `.resized(width, height)` | テクスチャを幅 `width`（ピクセル）, 高さ `height`（ピクセル）の大きさに拡大縮小した `TextureRegion` を作成する |

- `TextureRegion` は `Texture` と同じように描画できます
- 既存の `Texture` から `TextureRegion` を作成するコストは小さいため、メインループ内で実行して問題ありません

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/13.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture texture{ U"example/windmill.png", TextureDesc::Mipped };
	const Texture emoji{ U"🍎"_emoji };

	while (System::Update())
	{
		texture.scaled(0.25).draw(40, 40);
		texture.scaled(0.8, 0.5).draw(40, 140);
		texture.scaled(2).draw(40, 340);

		emoji.resized(40).draw(500, 40);
		emoji.resized(120, 40).draw(600, 40);
		emoji.resized(40, 120).draw(500, 140);
	}
}
```


## 30.14 長方形内に収めた描画
- あるサイズ内で最大限大きくなるようにテクスチャを描くには、次のメンバ関数を使って、拡大縮小を適用した `TextureRegion` を作成します

| コード | 説明 |
|---|---|
| `.fitted(size)` | テクスチャのアスペクト比を保ったまま、幅 `size.x`, 高さ `size.y` 以内に収まり、最大限大きくなるよう拡大縮小した `TextureRegion` を返す |
| `.fitted(width, height)` | テクスチャのアスペクト比を保ったまま、幅 `width`, 高さ `height` 以内に収まり、最大限大きくなるよう拡大縮小した `TextureRegion` を返す |

- `TextureRegion` は `Texture` と同じように描画できます
- 既存の `Texture` から `TextureRegion` を作成するコストは小さいため、メインループ内で実行して問題ありません

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/14.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture texture1{ U"example/windmill.png", TextureDesc::Mipped };
	const Texture texture2{ U"example/siv3d-kun.png", TextureDesc::Mipped };

	const Rect rect1{ 50, 100, 320, 200 };
	const Rect rect2{ 400, 200, 300 };

	while (System::Update())
	{
		rect1.drawFrame(0, 4, Palette::Seagreen);
		texture1.fitted(rect1.size).drawAt(rect1.center());

		rect2.drawFrame(0, 4, Palette::Seagreen);
		texture2.fitted(rect2.size).drawAt(rect2.center());
	}
}
```


## 30.15 回転した描画
- テクスチャを回転して描画するには、次のメンバ関数を使って、回転を適用した `TexturedQuad` を作成します

| コード | 説明 |
|---|---|
| `.rotated(angle)` | テクスチャを `angle`（ラジアン）だけ回転させた `TexturedQuad` を作成する |
| `.rotatedAt(x, y, angle)` | テクスチャを座標 (x, y) を軸に `angle`（ラジアン）だけ回転させた `TexturedQuad` を作成する |
| `.rotatedAt(pos, angle)` | テクスチャ上の `pos` を軸に `angle`（ラジアン）だけ回転させた `TexturedQuad` を作成する |

- `.rotated()` は、テクスチャの中心に画鋲を打ち込んだようなイメージで、テクスチャを回転させます
- `.rotatedAt()` は、テクスチャ上の指定した座標に画鋲を打ち込んだようなイメージで、テクスチャを回転させます
- `TexturedQuad` は `Texture` のように描画できます
- 既存の `Texture` から `TexturedQuad` を作成するコストは小さいため、メインループ内で実行して問題ありません

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/15.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture texture{ U"example/windmill.png" };
	const Texture emoji{ U"🍎"_emoji };

	double angle = 0.0_deg;

	while (System::Update())
	{
		angle += (Scene::DeltaTime() * 30_deg);

		texture.rotated(angle).drawAt(200, 300);

		emoji.rotatedAt(Vec2{ 58, 13 }, angle).drawAt(600, 300);
	}
}
```


## 30.16 上下・左右反転した描画
- テクスチャを上下・左右反転して描画するには、次のメンバ関数を使って、反転を適用した `TextureRegion` を作成します

| コード | 説明 |
|---|---|
| `.flipped()` | テクスチャを上下反転した `TextureRegion` を作成する |
| `.flipped(onOff)` | テクスチャを上下反転した `TextureRegion` を作成する。`onOff` が `true` のとき反転する |
| `.mirrored()` | テクスチャを左右反転した `TextureRegion` を作成する |
| `.mirrored(onOff)` | テクスチャを左右反転した `TextureRegion` を作成する。`onOff` が `true` のとき反転する |

- `TextureRegion` は `Texture` と同じように描画できます
- 既存の `Texture` から `TextureRegion` を作成するコストは小さいため、メインループ内で実行して問題ありません

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/16.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🐈"_emoji };

	while (System::Update())
	{
		emoji.drawAt(100, 100);
		emoji.mirrored().drawAt(300, 100);
		emoji.mirrored(false).drawAt(500, 100);
		emoji.mirrored(true).drawAt(700, 100);

		emoji.drawAt(100, 300);
		emoji.flipped().drawAt(300, 300);
		emoji.flipped(false).drawAt(500, 300);
		emoji.flipped(true).drawAt(700, 300);
	}
}
```


## 30.17 部分描画
- テクスチャの一部の長方形領域だけを描画するには、次のメンバ関数を使って、部分切り出しを適用した `TextureRegion` を作成します

| コード | 説明 |
|---|---|
| `(x, y, w, h)` | テクスチャの `(x, y)` から幅 `w`, 高さ `h` を切り出した `TextureRegion` を作成する |
| `(rect)` | テクスチャの `rect` の領域を切り出した `TextureRegion` を作成する |
| `.uv(u, v, w, h)` | テクスチャの UV 座標 `(u, v)` から幅 `w`, 高さ `h` を切り出した `TextureRegion` を作成する |
| `.uv(rect)` | テクスチャの UV 座標 `rect` の領域を切り出した `TextureRegion` を作成する |

- 前者 2 つはピクセル座標で、後者 2 つは UV 座標で指定します
- UV 座標はテクスチャの左上を (0.0, 0.0)、右下を (1.0, 1.0) としたときの座標で、画像の大きさに関係なく、常に 0.0 から 1.0 の範囲です
- テクスチャ `texture` のサイズが 400 × 200 のとき、`texture(0.5, 0.0, 0.5, 1.0)` は `texture(200, 0, 200, 200)` と同じです

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/17.png)

```cpp

```


## 30.18 敷き詰め描画
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/18.png)

```cpp

```


## 30.19 操作の組み合わせ
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/19.png)

```cpp

```


## 30.20 図形の形に合わせた描画
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/20.png)

```cpp

```


## 30.21 `Polygon` に合わせた描画
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/21.png)

```cpp

```


## 30.22 大きな画像の事前縮小
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/22.png)

```cpp

```


## 30.23 ミップマップの自前生成
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/23.png)

```cpp

```


## 30.24 テクスチャ描画に関するトラブル
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/texture/24.png)

```cpp

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