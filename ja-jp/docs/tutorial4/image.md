# 63. 画像処理
画像処理を行うための機能と、その結果をシーンに表示する方法を学びます。

## 63.1 画像処理の概要
- `Texture` クラスに読み込んだ画像データは GPU のメモリ上に配置されるため、C++ プログラムを通して画像の内容にアクセスすることはできません
- 一方、`Image` クラスで読み込んだ（または作成した）画像データはメインメモリ上に配置されるため、`Array` や `Grid` のように、C++ プログラムで簡単に内容にアクセスできます
- `Image` には自身をシーンに描画する機能はなく、`Image` をもとに `Texture` や `DynamicTexture`（**63.18**）を作成し、テクスチャとして描画する必要があります

|  | Image | DynamicTexture | Texture |
|--|:--:|:--:|:--:|
| データの格納場所 | メインメモリ | GPU メモリ | GPU メモリ |
| 内容の更新 | ✅ | ✅<br>`.fill()` を使う | |
| 描画 | | ✅ | ✅ |
| CPU からのアクセス | ✅ | | |
| GPU（シェーダ）からのアクセス | | ✅ | ✅ |


## 63.2 Image クラスの基本
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


## 63.3 画像ファイルの読み込み
- 画像ファイルから `Image` を作成するには、`Image{ ファイルパス }` を使います
- ファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します
- 対応する画像フォーマットは **チュートリアル 31.4** を参照してください
- 次のサンプルでは、画像の任意の位置をマウスカーソルで選択し、そのピクセル色を表示します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/3.png)

```cpp

```


## 63.4 絵文字とアイコン
- `Texture` と同様に、絵文字やアイコンから `Image` を作成できます
- `Image{ U"絵文字"_emoji }` で、絵文字の画像データを作成します
	- 絵文字一覧は [Emojipedia: Google Noto Color Emoji :material-open-in-new:](https://emojipedia.org/ja/google){:target="_blank"} で確認できます
- `Image{ 0xアイコン番号_icon, サイズ }` で、アイコンからテクスチャを作成します
	- アイコン番号は [Material Design Icons :material-open-in-new:](https://pictogrammers.com/library/mdi/){:target="_blank"} または [Font Awesome :material-open-in-new:](https://fontawesome.com/v5/search?o=r&m=free){:target="_blank"} の 16 進数コードです

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/4.png)

```cpp

```


## 63.5 メモリの節約
- `Image` から `Texture` を作成したあと、`Image` は不要になります
- 次のコードでは、不要になった `image` が、メインループ中もメモリを消費し続けています

```cpp

```

- `Image` の `.release()` で、画像データと消費していたメモリを明示的に解放し、`Image` を空の状態にすることができます
- あるいは、`Image` を返す関数の戻り値を `Texture` のコンストラクタに直接渡すことで、`Image` がすぐに解放されるよう設計することもできます

```cpp

```


## 63.6 画像のサイズ
- 画像データの幅（ピクセル）は `.width()` で取得できます。戻り値は `int32` 型です
- 画像データの高さ（ピクセル）は `.height()` で取得できます。戻り値は `int32` 型です
- 幅と高さを同時に取得するには `.size()` を使います。戻り値は `Size`（`Point`） 型です
- 次のようなループで、`Image` 内のすべてのピクセルにアクセスできます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/5.png)

```cpp

```


## 63.7 範囲 for 文による全ピクセル走査
- 範囲 for 文を使って画像データの要素を走査します
- 範囲 for 文の中で、対象の画像のサイズを変更する操作は行わないでください
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/6.png)

```cpp

```


## 63.8 塗りつぶし
- 画像の内容をすべて単色で塗りつぶすには `.fill(color)` を使います
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/7.png)

```cpp

```


## 63.9 画像の保存
- 画像データを画像ファイルとして保存するには `.save(path)` を使います
- 画像の保存形式は、`path` の拡張子から自動的に適切なものが選択されます
	- 通常は PNG 形式、JPEG 形式を使うとよいでしょう
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/8.png)

```cpp

```


## 63.10 画像の保存（ダイアログ）
- 画像データをダイアログでファイル名を指定して画像ファイルとして保存するには `.saveWithDialog()` を使います
- 画像の保存形式は、ダイアログで選択した拡張子をもとに選択されます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/10.png)

```cpp

```


## 63.11 画像の拡大縮小
- `.scaled(double scale)` は、画像を指定した倍率で拡大縮小した結果を、新しい `Image` で返します
- `.scaled(Size size)` は、画像を指定したサイズに拡大縮小した結果を、新しい `Image` で返します
- 第 2 引数に `InterpolationAlgorithm::Nearest` を指定すると、最近傍補間で拡大縮小します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/11.png)

```cpp

```


## 63.12 画像の部分コピー
- `.clipped(x, y, w, h)` は、画像の指定した範囲をコピーした新しい `Image` を返します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/12.png)

```cpp

```


## 63.13 画像処理
- さまざまな画像処理関数が用意されています
- どの画像処理も、自身を変更するメンバ関数と、画像処理後の結果を返すメンバ関数の 2 種類があります

| 処理 | 結果の画像の例 | 自身を変更するメンバ関数 / 結果を返すメンバ関数 |
|--|:--:|--|
|色の反転|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`negate` / `negated`|
|グレイスケール化|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`grayscale` / `grayscaled`|
|セピアカラー|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`sepia` / `sepiaed`|
|ポスタライズ|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`posterize` / `posterized`|
|明度レベル変更|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`brighten` / `brightened`|
|左右反転|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`mirror` / `mirrored`|
|上下反転|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`flip` / `flipped`|
|90° 回転|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`rotate90` / `rotated90`|
|180° 回転|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`rotate180` / `rotated180`|
|270° 回転|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`rotate270` / `rotated270`|
|ガンマ補正|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`gammaCorrect` / `gammaCorrected`|
|二値化|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`threshold` / `thresholded`|
|大津の手法による二値化|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`threshold_Otsu` / `thresholded_Otsu`|
|適応的二値化|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`adaptiveThreshold` / `adaptiveThresholded`|
|モザイク|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`mosaic` / `mosaiced`|
|拡散|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`spread` / `spreaded`|
|ブラー|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`blur` / `blurred`|
|メディアンブラー|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`medianBlur` / `medianBlurred`|
|ガウスぼかし|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`gaussianBlur` / `gaussianBlurred`|
|バイラテラルフィルタ|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`bilateralFilter` / `bilateralFiltered`|
|膨張|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`dilate` / `dilated`|
|収縮|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`erode` / `eroded`|
|拡大縮小|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`scale` / `scaled`|
|周囲に枠を加える|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)|`border` / `bordered`|
|任意角度の回転|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)| なし / `rotated`|
|正方形での切り抜き|![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/13a.png)| なし / `squareClipped`|

```cpp

```


## 63.14 部分画像処理
- 一部の画像処理関数は、画像の一部の矩形範囲のみに適用できます
- `image(x, y, w, h).gaussianBlur()` で、画像の `(x, y)` から `(x + w, y + h)` の範囲にのみガウスぼかしを適用します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/14.png)

```cpp

```


## 63.15 図形の書き込み
- `Circle` や `Line`, `Rect` などの図形は、メンバ関数 `.paint()` および `.overwrite()` を使って `Image` に書き込むことができます
- `.paint()` はアルファ値に応じて色をブレンドします
- `.overwrite()` は引数で指定した色をそのまま書き込みます
- 画像の範囲外の部分には何も書き込まれません
- `Image` への書き込みは CPU で処理されるため、通常の `.draw()` よりも大きなコストがかかります
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/15.png)

```cpp

```


## 63.16 画像の書き込み
- `Image` や `Image` の一部を別の `Image` に書き込むことができます
- 書き込みの対象を自分自身にすることはできません
- 書き込みに使うメンバ関数は次の通りです：

| メンバ関数 | アルファブレンド | 書き込み先のアルファ値の更新 |
|--|:--:|:--:|
|`.paint()`<br>`.paintAt()`| ✅ | |
|`.stamp()`<br>`.stampAt()`| ✅ | ✅<br>大きいほう |
|`.overwrite()`<br>`.overwriteAt()`| | ✅ |

- `Image` への書き込みは CPU で処理されるため、通常の `.draw()` よりも大きなコストがかかります
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/16.png)

```cpp

```


## 63.17 テキストの書き込み
- `Font` から各文字の画像を `BitmapGlyph` として取得し、その画像を自由描画（**チュートリアル 34.24**）の要領で書き込みます
- `Image` への書き込みは CPU で処理されるため、通常の `.draw()` よりも大きなコストがかかります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/17.png)

```cpp

```


## 63.18 DynamicTexture
- ペイントアプリのように、`Image` の内容をプログラムの実行中に頻繁に変更し、その結果をシーンに描きたい場合があります
- `Image` の内容を更新するたびに古い `Texture` を破棄して新しい `Texture` を作成するのは非効率です
- そのような用途では、`DynamicTexture` を使うのが適切です
- `DynamicTexture` は、中身を動的に変更できる `Texture` です。通常の `Texture` のメンバ関数に加え、`.fill(image)` メンバ関数を持ちます
- `.fill()` は、`DynamicTexture` が空の場合は `image` で新しいテクスチャを作成し、既にデータを持っている場合はその内容を `image` で置き換えます
- このとき新旧の画像データの縦横サイズは一致している必要があります
- `DynamicTexture` の `.fill()` は、既に保持しているデータ領域を上書きするだけであるため、新しく `Texture` を作成するよりも効率的です
- しかし、`.fill()` のコストも依然と大きいため、不必要な場合には呼び出さないようにする必要があります
- 用途によっては `RenderTexture`（**チュートリアル 52**）を使うほうが適切な場合もあります
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/18.png)

```cpp

```


## 63.19 ペイントアプリ（1）
- 次のようなプログラムでペイントアプリを作れます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/19.png)

```cpp

```


## 63.20 ペイントアプリ（2）
- `.floodFill()` は、指定した座標から同じ色の領域を再帰的に塗りつぶします
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/20.png)

```cpp

```


## 63.21 大量のグリッドの可視化
- 大量の要素がある `Grid` を可視化する場合、幅 × 高さのマスをそれぞれ `Rect` で描くよりも、縦横が同じ要素数の `Image` を作成して 1 枚のテクスチャとして描画したほうが効率的です
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/image/21.png)

```cpp

```

