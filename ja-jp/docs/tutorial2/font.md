# 33. 文字を描く
フォントを使って様々なスタイルのテキストを描く方法を学びます。

## 33.1 フォントの基本
- 画面にテキストを描画するときのフォントは `Font` クラスで管理します

### 33.1.1 描画方式
- 通常のフォントは、**描画方式** を次の 3 つから選ぶことができます
	- **ビットマップ方式：**フォントの文字画像データをビットマップとして保存します。基本サイズ以上に拡大描画すると品質が低下します。常に固定サイズでフォントを描画する場合や、複雑な字形の書体を用いる場合に適しています
	- **SDF 方式：**フォントの文字画像データを 1 チャンネルの SDF（Signed Distance Field）として保存します。基本サイズ以上に拡大描画しても品質が維持されます。影や輪郭エフェクトを追加できます。文字の角が少し丸くなる副作用があります
	- **MSDF 方式：**フォントの文字画像データを 3 チャンネルの MSDF（Multi-channel Signed Distance Field）として保存します。基本サイズ以上に拡大描画しても品質が維持されます。影や輪郭エフェクトを追加できます

| 描画方式 | 品質 | 縮小 | 拡大 | 影 | 輪郭 | 実行時負荷 |
|--|:--:|:--:|:--:|:--:|:--:|:--:|
| ビットマップ方式| ◎ |  〇 | △ | 〇<br>(2 回 draw) | × | 低 |
| SDF 方式| △ |  〇 | 〇 | ◎ | ◎ | 中 |
| MSDF 方式| 〇 | ◎ | ◎ | 〇 | 〇 | 高 |

- 描画方式を指定しない場合は、**ビットマップ方式** が使われます
- 一部の書体は、ビットマップ方式しか選択できません
- 描画方式はフォントを作成するときに指定し、あとから変更できません

### 33.1.2 基本サイズ
- 個々の文字画像データはエンジン内部で作成され、メモリ上にキャッシュ（保存）されます
- このときキャッシュされる文字画像のサイズを **基本サイズ** と呼びます
- 基本サイズはフォントを作成するときに指定し、あとから変更できません

#### 描画方式と基本サイズ
- 基本サイズと異なるサイズでテキストを描画すると、拡大縮小が行われます
- ビットマップ方式で拡大が行われると、解像度の低い画像を拡大したときのように、文字の見た目が荒くなります
    - **ビットマップ方式では、基本サイズと同じサイズで描画**することが想定されています
- SDF / MSDF 方式では、基本サイズ以上に拡大してテキストを描画しても品質が維持されます
- SDF / MSDF 方式では、ある程度大きな基本サイズが必要です
    - 複雑な文字で基本サイズが小さいと、品質が低下して字形が崩れることがあります
    - SDF / MSDF 方式では、英数字は `40`, 日本語フォントは `48` が基本サイズとして推奨されます
    - 一方で、大きな基本サイズは、メモリの消費量と、実行時の文字画像キャッシュ作成時間を増加させるため、バランスを考えて選ぶ必要があります

#### 実行中のコスト
- 文字画像データは、実行中に必要に応じて作成され、キャッシュされます（**33.27** 参照）
- あるフレームで、キャッシュに存在しないたくさんの新しい字種を描画する場合、それらの文字画像データを一度に作成する必要があるため、そのフレームの処理時間が増加します
- とくに SDF / MSDF 方式では、1 つあたりの文字画像データの作成に時間がかかるため、実行時に目立つ遅延が発生する可能性があります
- **33.28** のプリロード機能を使って、あらかじめ必要な文字画像データを作成しておくことで、実行時の遅延を軽減できます

### 33.1.3 書体
- 「メイリオ」「Arial」など、フォントの種類を**書体**と呼びます
- 書体はフォントを作成するときに指定し、あとから変更できません
- 書体を指定しない場合は、Siv3D に同梱されている標準書体（レギュラー）が使われます

### 33.1.4 フォントスタイル
- 一部の書体は、**フォントスタイル** を指定することで、太字や斜体、ビットマップフォントなどのスタイルを変更できます
- フォントスタイルはフォントを作成するときに指定し、あとから変更できません
- フォントスタイルを指定しない場合は、通常のフォントが作成されます

### 33.1.5 テキストスタイル
- SDF / MSDF 方式のフォントでは、次のような **テキストスタイル** をテキスト描画時に適用できます
	- **影：**任意方向に影を追加します
	- **輪郭：**文字に輪郭を追加します
- テキストスタイルは、フォントを使った個々のテキスト描画時に指定します
- テキストスタイルを指定しない場合は、通常のスタイルで描画されます

### 33.1.6 画像バッファ幅
- 画像バッファ幅は、文字画像データを作成するときの、文字の周囲の余白の幅です
- デフォルトでは `2` が使われます
- SDF / MSDF 方式で大きな影や輪郭を作りたい場合、大きめの画像バッファ幅を指定しないと、影や輪郭が切れてしまうことがあります
- 画像バッファ幅はフォント作成後に `.setBufferThickness()` で指定します
- 大きな画像バッファ幅は、メモリの消費量と、実行時の文字画像キャッシュ作成時間を増加させるため、バランスを考えて選ぶ必要があります

### 33.1.7 フォントのフォールバック
- 1 つの書体では、必要なすべての文字をカバーできない場合があります
- そこで、別の書体のフォントを**フォールバック**として登録し、メインの書体でカバーできない文字を別のフォントでカバーすることができます
- おもにテキスト内に複数の言語や絵文字を含む場合に使用します

## 33.2 フォントの作成と描画

### フォントの作成
- フォントの作成にはいくつかの方法があります
    - **33.3, 33.4** 標準書体から作成
    - **33.5** フォントファイルから作成
    - **33.6** PC にインストールされているフォントファイルから作成
- フォントの作成にはコストがかかるため、通常はメインループの前で行います
- メインループ内で作成する場合には、毎フレーム作成されないような制御が必要です

### テキストの描画
- `Font` オブジェクトの `()` 演算子にテキストを渡すと `DrawableText` オブジェクトが得られます
- 実際にテキストを描画するには、`DrawableText` のメンバ関数を使います
    - **33.10** 左上座標を指定した描画 `.draw()`
    - **33.11** 中心座標を指定した描画 `.drawAt()`
	- **33.12** ベースラインを指定した描画 `.drawBase()`
	- **33.13** それ以外の座標を指定した描画 `.draw(Args::...)`
    - **33.14** 長方形を指定した描画 `.draw(rect)`

```cpp
font(U"Hello, Siv3D!").draw(40, Vec2{ 40, 40 });
```


## 33.3 標準書体（1）
- Siv3D には、いくつかの標準書体が用意されています
- フォントの作成時に書体を指定しなかった場合、標準書体（レギュラー）が使われます
- フォントは 3 種類の描画方式で作成できます
	- ビットマップ方式
	- SDF 方式
	- MSDF 方式
- フォントの作成時に方式を指定しなかった場合、ビットマップ方式が使われます

| コード | 説明 |
| --- | --- |
| `Font font{ 基本サイズ };` | ビットマップ方式で標準書体（レギュラー）のフォントを作成 |
| `Font font{ FontMethod::Bitmap, 基本サイズ };` | ビットマップ方式で標準書体（レギュラー）のフォントを作成 |
| `Font font{ FontMethod::SDF, 基本サイズ };` | SDF 方式で標準書体（レギュラー）のフォントを作成 |
| `Font font{ FontMethod::MSDF, 基本サイズ };` | MSDF 方式で標準書体（レギュラー）のフォントを作成 |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font fontBitmap{ 48 };
	const Font fontSDF{ FontMethod::SDF, 48 };
	const Font fontMSDF{ FontMethod::MSDF, 48 };

	while (System::Update())
	{
		fontBitmap(U"Hello, Siv3D!").draw(Vec2{ 40, 100 }, ColorF{ 0.2 });
		fontSDF(U"Hello, Siv3D!").draw(Vec2{ 40, 200 }, ColorF{ 0.2 });
		fontMSDF(U"Hello, Siv3D!").draw(Vec2{ 40, 300 }, ColorF{ 0.2 });
	}
}
```


## 33.4 標準書体（2）
- Siv3D には次の書体が標準書体として同梱されています
	- 異なる太さの 7 種類の日本語書体
	- 5 地域向けの CJK（中国語・韓国語・日本語対応）書体
	- 白黒絵文字書体
	- カラー絵文字書体
- `Font` のコンストラクタにおいて `Typeface::` で書体を指定することで、標準書体からフォントを作成できます

| コード |説明|
|--|--|
|`Typeface::Thin`|細い日本語書体|
|`Typeface::Light`|やや細い日本語書体|
|`Typeface::Regular`|通常日本語書体|
|`Typeface::Medium`|やや太い日本語書体|
|`Typeface::Bold`|太い日本語書体|
|`Typeface::Heavy`|とても太い日本語書体|
|`Typeface::Black`|最も太い日本語書体|
|`Typeface::CJK_Regular_JP`|日本語デザインの CJK 書体|
|`Typeface::CJK_Regular_KR`|韓国語デザインの CJK 書体|
|`Typeface::CJK_Regular_SC`|簡体字デザインの CJK 書体|
|`Typeface::CJK_Regular_TC`|台湾繁体字デザインの CJK 書体|
|`Typeface::CJK_Regular_HK`|香港繁体字デザインの CJK 書体|
|`Typeface::MonochromeEmoji`|モノクロ絵文字書体|
|`Typeface::ColorEmoji`|カラー絵文字書体|
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font fontThin{ FontMethod::MSDF, 48, Typeface::Thin };
	const Font fontLight{ FontMethod::MSDF, 48, Typeface::Light };
	const Font fontRegular{ FontMethod::MSDF, 48, Typeface::Regular };
	const Font fontMedium{ FontMethod::MSDF, 48, Typeface::Medium };
	const Font fontBold{ FontMethod::MSDF, 48, Typeface::Bold };
	const Font fontHeavy{ FontMethod::MSDF, 48, Typeface::Heavy };
	const Font fontBlack{ FontMethod::MSDF, 48, Typeface::Black };

	const Font fontJP{ FontMethod::MSDF, 48, Typeface::CJK_Regular_JP };
	const Font fontKR{ FontMethod::MSDF, 48, Typeface::CJK_Regular_KR };
	const Font fontSC{ FontMethod::MSDF, 48, Typeface::CJK_Regular_SC };
	const Font fontTC{ FontMethod::MSDF, 48, Typeface::CJK_Regular_TC };
	const Font fontHK{ FontMethod::MSDF, 48, Typeface::CJK_Regular_HK };

	const Font fontMono{ FontMethod::MSDF, 48, Typeface::MonochromeEmoji };

	// カラー絵文字フォントでは、方式・基本サイズが無視される
	const Font fontEmoji{ FontMethod::MSDF, 48, Typeface::ColorEmoji };

	const String s0 = U"Hello, Siv3D!";
	const String s1 = U"こんにちは 你好 안녕하세요 骨曜喝愛遙扇";
	const String s2 = U"🐈🐕🚀";

	while (System::Update())
	{
		fontThin(s0).draw(36, Vec2{ 40, 20 }, ColorF{ 0.2 });
		fontLight(s0).draw(36, Vec2{ 40, 60 }, ColorF{ 0.2 });
		fontRegular(s0).draw(36, Vec2{ 40, 100 }, ColorF{ 0.2 });
		fontMedium(s0).draw(36, Vec2{ 40, 140 }, ColorF{ 0.2 });
		fontBold(s0).draw(36, Vec2{ 40, 180 }, ColorF{ 0.2 });
		fontHeavy(s0).draw(36, Vec2{ 40, 220 }, ColorF{ 0.2 });
		fontBlack(s0).draw(36, Vec2{ 40, 260 }, ColorF{ 0.2 });

		fontJP(s1).draw(36, Vec2{ 40, 300 }, ColorF{ 0.2 });
		fontKR(s1).draw(36, Vec2{ 40, 340 }, ColorF{ 0.2 });
		fontSC(s1).draw(36, Vec2{ 40, 380 }, ColorF{ 0.2 });
		fontTC(s1).draw(36, Vec2{ 40, 420 }, ColorF{ 0.2 });
		fontHK(s1).draw(36, Vec2{ 40, 460 }, ColorF{ 0.2 });

		fontMono(s2).draw(36, Vec2{ 340, 20 }, ColorF{ 0.2 });
		fontEmoji(s2).draw(36, Vec2{ 500, 20 });
	}
}
```


## 33.5 フォントファイルから作成
- 独自に用意したフォントファイルをからフォントを作成するには、`Font` のコンストラクタにフォントファイルのパスを指定します
- ファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します
	- 例えば `U"example/font/RocknRoll/RocknRollOne-Regular.ttf"` は、実行ファイルがあるフォルダ（`App` フォルダ）の `example/font/RocknRoll/` フォルダにある `RocknRollOne-Regular.ttf` というファイルを指します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/5.png)

```cpp

```


## 33.6 PC にインストールされているフォントファイルから作成
- PC にインストールされたフォントは、OS によって異なる場所に保存されています
- そのフォルダのパスを `FileSystem::GetFolderPath()` で取得し、フォントファイル名とつなげることで、絶対パスを構築できます
- `FileSystem::GetFolderPath()` に渡す引数と、それによって取得できるパスの対応表は次のとおりです

| 引数                       | Windows             | macOS                  | Linux       |
|----------------------------|:---------------------|:------------------------|:-------------|
| `SpecialFolder::SystemFonts` | (OS):/WINDOWS/Fonts/ | /System/Library/Fonts/ | /usr/share/fonts/ |
| `SpecialFolder::LocalFonts`  | (OS):/WINDOWS/Fonts/ | /Library/Fonts/        | /usr/local/share/fonts/<br>(存在する場合) |
| `SpecialFolder::UserFonts`   | (OS):/WINDOWS/Fonts/ | ~/Library/Fonts/       | /usr/local/share/fonts/<br>(存在する場合) |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/6.png)

```cpp

```


## 33.7 空のフォント
- `Font` 型のオブジェクトは、デフォルトでは**空のフォント**を持っています
- 空のフォントを使用してもエラーにはなりませんが、何も描画されません
- フォントファイルのロードに失敗した場合にも空のテクスチャになります
- 空のフォントであるかを調べるには、`if (font.isEmpty())`, `if (font)`, `if (not font)` を使います
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/7.png)

```cpp

```


## 33.8 文字列のフォーマット
- 作成したフォント `font` を使い、`font(テキスト).draw(サイズ, pos, 色);` のようにして、サイズ・位置・色を指定してテキストを表示します
- `font(テキスト)` のテキストの部分には、文字列だけでなく、`Print` 可能な値（数値や `Point` など）をいくつでも記述できます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/8.png)

```cpp

```


## 33.9 改行
- テキストの中に改行文字 `'\n'` が含まれていると、そこで改行されます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/9.png)

```cpp

```


## 33.10 左上座標を指定した描画
- 左上の座標を指定してテキストを描画するには、`font(テキスト).draw()` を使います
- この関数は、テキストが描画された領域を `RectF` で返します

| コード | 説明 |
| --- | --- |
| `.draw(x, y, color);` | 左上座標 `(x, y)` からテキストを描画 |
| `.draw(pos, color);` | 左上座標 `pos` からテキストを描画 |
| `.draw(fontSize, x, y, color);` | フォントサイズ `fontSize` で、左上座標 `(x, y)` からテキストを描画 |
| `.draw(fontSize, pos, color);` | フォントサイズ `fontSize` で、左上座標 `pos` からテキストを描画 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/10.png)

```cpp

```


## 33.11 中心座標を指定した描画
- 中心の座標を指定してテキストを描画するには、`font(テキスト).drawAt()` を使います
- この関数は、テキストが描画された領域を `RectF` で返します

| コード | 説明 |
| --- | --- |
| `.drawAt(x, y, color);` | 中心座標 `(x, y)` からテキストを描画 |
| `.drawAt(pos, color);` | 中心座標 `pos` からテキストを描画 |
| `.drawAt(fontSize, x, y, color);` | フォントサイズ `fontSize` で、中心座標 `(x, y)` からテキストを描画 |
| `.drawAt(fontSize, pos, color);` | フォントサイズ `fontSize` で、中心座標 `pos` からテキストを描画 |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/11.png)

```cpp

```


## 33.12 ベースラインを指定した描画
- ベースラインの開始位置を指定してテキストを描画するには、`font(テキスト).drawBase()` を使います
	- フォントサイズが異なるテキストを描画する際に、ベースラインを揃えることができます
- この関数は、テキストが描画された領域を `RectF` で返します

| コード | 説明 |
| --- | --- |
| `.drawBase(x, y, color);` | ベースラインの開始位置 `(x, y)` からテキストを描画 |
| `.drawBase(pos, color);` | ベースラインの開始位置 `pos` からテキストを描画 |
| `.drawBase(fontSize, x, y, color);` | フォントサイズ `fontSize` で、ベースラインの開始位置 `(x, y)` からテキストを描画 |
| `.drawBase(fontSize, pos, color);` | フォントサイズ `fontSize` で、ベースラインの開始位置 `pos` からテキストを描画 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/12.png)

```cpp

```


## 33.13 それ以外の座標を指定した描画
- - **右端の中心位置**を指定し、それに合わせてテキストを描画するには、次の方法を使います
	- `.draw(Arg::topRight = pos, ...)`
	- `.draw(Arg::topRight(x, y), ...)
- このように指定できる基準位置は、全部で 9 種類あります
- これらの関数は、テキストが描画された領域を `RectF` で返します

| 基準位置 | 説明 |
|---|---|
| `Arg::topLeft` | 左上。`.draw()` と同じ |
| `Arg::topCenter` | 上辺の中央 |
| `Arg::topRight` | 右上|
| `Arg::leftCenter` | 左辺の中央 |
| `Arg::center` | 中心。`.drawAt()` と同じ |
| `Arg::rightCenter` | 右辺の中央 |
| `Arg::bottomLeft` | 左下 |
| `Arg::bottomCenter` | 下辺の中央 |
| `Arg::bottomRight` | 右下 |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/13.png)

```cpp

```


## 33.14 長方形の中に収めた描画
- テキストを指定した長方形の中に収まるように描画するには、`font(テキスト).draw(rect)` を使います
- テキストのすべての文字が長方形内に収まった場合、関数は `true` を返します
- 一方、テキストがあふれる場合、あふれる部分が「`…`」に置き換えられ、関数は `false` を返します

| コード | 説明 |
| --- | --- |
| `.draw(rect, color);` | 長方形 `rect` の中に収まるようにテキストを描画 |
| `.draw(fontSize, rect, color);` | フォントサイズ `fontSize` で、長方形 `rect` の中に収まるようにテキストを描画 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/14.png)

```cpp

```


## 33.15 描画される領域の取得
- 実際に描画を行わずに、描画される領域を取得するには、`font(テキスト)` の `.region()` や `.regionAt()` を使います

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/15.png)

```cpp

```


## 33.16 フォントスタイル（太字・斜体）
- `Font` のコンストラクタで次のような `FontStyle` を指定することで、太字や斜体などのスタイルをフォントに適用できます

| コード | 説明 |
| --- | --- |
| `FontStyle::Bold` | 太字 |
| `FontStyle::Italic` | 斜体 |
| `FontStyle::BoldItalic` | 太字・斜体 |

	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/16.png)

```cpp

```


## 33.17 フォントスタイル（ビットマップ）
- 書体がビットマップフォントに対応している場合、`Font` のコンストラクタで次のような `FontStyle` を指定することで、ドット感を保った文字を描画できます

| コード | 説明 |
| --- | --- |
| `FontStyle::Bitmap` | ビットマップフォント |
| `FontStyle::BoldBitmap` | 太字のビットマップフォント |
| `FontStyle::ItalicBitmap` | 斜体のビットマップフォント |
| `FontStyle::BoldItalicBitmap` | 太字・斜体のビットマップフォント |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/17.png)

```cpp

```


## 33.18 文字に影を付ける（2 回描画）
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/18.png)

```cpp

```


## 33.19 文字に影を付ける（テキストスタイル）
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/19.png)

```cpp

```


## 33.20 文字に輪郭を付ける
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/20.png)

```cpp

```


## 33.21 文字に影と輪郭を付ける
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/21.png)

```cpp

```


## 33.22 （参考）テキストスタイルのプレビュー
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/22.png)

```cpp

```


## 33.23 テキストを 1 文字ずつ描画
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/23.png)

```cpp

```


## 33.24 文字単位での自由描画
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/24.png)

```cpp

```


## 33.25 縦書き
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/25.png)

```cpp

```


## 33.26 フォールバックフォント
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/26.png)

```cpp

```


## 33.27 フォントのキャッシュへのアクセス
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/27.png)

```cpp

```


## 33.28 フォントのプリロード
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/28.png)

```cpp

```


## 33.29 文字を `Polygon` で取得
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/29.png)

```cpp

```


## 33.30 文字を `LineString` で取得
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/30.png)

```cpp

```









## 31.16 テキストを 1 文字ずつ表示する
`.substr(offset, count)` で、文字列の `offset` 文字目から `count` 文字の部分文字列（`String`）を作成することができます。`offset` は 0 から始まります。`count` が省略された場合は、`offset` 文字目から末尾までの部分文字列を作成します。`offset` が実際の文字列の長さより大きい場合は、末尾までの部分文字列を作成します。

これと `Stopwatch` を組み合わせると、文字列を 1 文字ずつ表示することができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/16.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";
	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		const int32 count = (stopwatch.ms() / 30);

		font(text.substr(0, count)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.11 });
	}
}
```

## 31.17 文字に影の効果を付ける（ビットマップ方式、2 回描画する手法）
ビットマップ方式のフォントでは、座標をずらして 2回 テキストを描くことで、影の効果を作成できます。

`Vec2` のメンバ関数 `.movedBy(x, y)` は、指定した値だけ要素を加算した `Vec2` を作成する関数です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/17.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });

	const Font font{ 100, Typeface::Bold };

	const Vec2 center{ 400, 150 };

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		// center から (4, 4) ずらした位置を中心にテキストを描く
		font(text).drawAt(center.movedBy(4, 4), ColorF{ 0.0, 0.5 });

		// center を中心にテキストを描く
		font(text).drawAt(center);
	}
}
```

## 31.18 文字に影の効果を付ける（SDF / MSDF 方式）
SDF / MSDF 方式のフォントは、`TextStyle` を `.draw()` や `.drawAt()`, `.drawBase()` に設定することで、影や輪郭エフェクトを付与できます。SDF / MSDF 方式のフォントを使って文字を描画する際に影の効果を付けるには `TextStyle::Shadow(影のオフセット, 影の色)` を設定します。

影のオフセットが大きく、文字の Distance Field の範囲外に及んだ場合、影が途切れてしまいます。それを防ぐには `Font` のメンバ関数 `.setBufferThickness(Distance Field の余白のサイズ)` で、Distance Field を大きめに作成しておきます。デフォルトは 2 です。この値を大きくするとメモリ消費量や描画負荷が増加しますが、影や輪郭の効果をより大きく適用できるようになります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/18.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });

	const int32 baseSize = 40;
	const int32 bufferThickness = 3;

	const Font fontSDF{ FontMethod::SDF, baseSize, Typeface::Bold };
	fontSDF.setBufferThickness(bufferThickness);

	const Font fontMSDF{ FontMethod::MSDF, baseSize, Typeface::Bold };
	fontMSDF.setBufferThickness(bufferThickness);

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		const Vec2 shadowOffset{ 2, 2 };
		const ColorF shadowColor{ 0.0, 0.5 };
		const double fontSize = 100;

		// SDF 方式
		fontSDF(text).draw(TextStyle::Shadow(shadowOffset, shadowColor), 20, 20);
		fontSDF(text).draw(TextStyle::Shadow(shadowOffset, shadowColor), fontSize, 20, 60);

		// MSDF 方式
		fontMSDF(text).draw(TextStyle::Shadow(shadowOffset, shadowColor), 20, 220);
		fontMSDF(text).draw(TextStyle::Shadow(shadowOffset, shadowColor), fontSize, 20, 260);
	}
}
```

## 31.19 文字に輪郭を付ける（SDF / MSDF）
SDF / MSDF 方式のフォントを使って文字を描画する際に輪郭の効果を付けるには

- `TextStyle::Outline(輪郭スケール, 輪郭の色)`
- `TextStyle::Outline(内側方向の輪郭スケール, 外側方向の輪郭スケール, 輪郭の色)`

のいずれかを設定します。

文字に輪郭と影、両方の効果を付けるには

- `TextStyle::OutlineShadow(輪郭スケール, 輪郭の色, 影のオフセット, 影の色)`
- `TextStyle::OutlineShadow(内側方向の輪郭スケール, 外側方向の輪郭スケール, 輪郭の色, 影のオフセット, 影の色)`

のいずれかを設定します。輪郭スケールの単位はピクセルではないことに注意してください。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/19.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });

	const int32 baseSize = 40;
	const int32 bufferThickness = 3;

	const Font fontSDF{ FontMethod::SDF, baseSize, Typeface::Bold };
	fontSDF.setBufferThickness(bufferThickness);

	const Font fontMSDF{ FontMethod::MSDF, baseSize, Typeface::Bold };
	fontMSDF.setBufferThickness(bufferThickness);

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		const double outlineScale = 0.2;
		const ColorF outlineColor{ 0.0, 0.3, 0.6 };

		const Vec2 shadowOffset{ 2, 2 };
		const ColorF shadowColor{ 0.0, 0.5 };
		const double fontSize = 100;

		// SDF 方式
		fontSDF(text).draw(TextStyle::Outline(outlineScale, outlineColor), 20, 20);
		fontSDF(text).draw(TextStyle::Outline(outlineScale, outlineColor), fontSize, 20, 40);
		fontSDF(text).draw(TextStyle::OutlineShadow(outlineScale, outlineColor, shadowOffset, shadowColor), fontSize, 20, 150);

		// MSDF 方式
		fontMSDF(text).draw(TextStyle::Outline(outlineScale, outlineColor), 20, 300);
		fontMSDF(text).draw(TextStyle::Outline(outlineScale, outlineColor), fontSize, 20, 320);
		fontMSDF(text).draw(TextStyle::OutlineShadow(outlineScale, outlineColor, shadowOffset, shadowColor), fontSize, 20, 430);
	}
}
```

## 31.20 （サンプル）テキストスタイルのプレビューアプリ
次のサンプルプログラムで、各方式におけるテキストスタイルの効果をプレビューできます。マウスの右クリックやホイールで移動・拡大を行うことができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/20.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 基本サイズ: 大きいと拡大描画時にきれいになるが、フォントの生成時間・メモリ消費が増える
	const int32 baseSize = 70;

	// このサイズだけ、文字の周囲に輪郭や影のエフェクトを付加できる。フォントの生成時間・メモリ消費が増える
	const int32 bufferThickness = 5;

	// ビットマップ方式では輪郭や影のエフェクトの利用は不可
	const Font fontBitmap{ FontMethod::Bitmap, baseSize, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };

	// SDF 方式
	const Font fontSDF{ FontMethod::SDF, baseSize, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };
	fontSDF.setBufferThickness(bufferThickness);

	// MSDF 方式
	const Font fontMSDF{ FontMethod::MSDF, baseSize, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };
	fontMSDF.setBufferThickness(bufferThickness);

	bool outline = false;
	bool shadow = false;
	double inner = 0.1, outer = 0.1;
	Vec2 shadowOffset{ 2.0, 2.0 };
	ColorF textColor{ 1.0 };
	ColorF outlineColor{ 0.0 };
	ColorF shadowColor{ 0.0, 0.5 };
	HSV background = ColorF{ 0.8 };

	Camera2D camera{ Scene::Center(), 1.0 };

	while (System::Update())
	{
		Scene::SetBackground(background);

		TextStyle textStyle;
		{
			if (outline && shadow)
			{
				textStyle = TextStyle::OutlineShadow(inner, outer, outlineColor, shadowOffset, shadowColor);
			}
			else if (outline)
			{
				textStyle = TextStyle::Outline(inner, outer, outlineColor);
			}
			else if (shadow)
			{
				textStyle = TextStyle::Shadow(shadowOffset, shadowColor);
			}
		}

		camera.update();
		{
			auto t = camera.createTransformer();
			fontBitmap(U"Siv3D, 渋三次元 (Bitmap)").draw(Vec2{ 100, 250 }, textColor);
			fontSDF(U"Siv3D, 渋三次元 (SDF)").draw(textStyle, Vec2{ 100, 330 }, textColor);
			fontMSDF(U"Siv3D, 渋三次元 (MSDF)").draw(textStyle, Vec2{ 100, 410 }, textColor);
		}

		SimpleGUI::CheckBox(outline, U"Outline", Vec2{ 20, 20 }, 130);
		SimpleGUI::Slider(U"Inner: {:.2f}"_fmt(inner), inner, -0.5, 0.5, Vec2{ 160, 20 }, 120, 120, outline);
		SimpleGUI::Slider(U"Outer: {:.2f}"_fmt(outer), outer, -0.5, 0.5, Vec2{ 160, 60 }, 120, 120, outline);

		SimpleGUI::CheckBox(shadow, U"Shadow", Vec2{ 20, 100 }, 130);
		SimpleGUI::Slider(U"offsetX: {:.1f}"_fmt(shadowOffset.x), shadowOffset.x, -5.0, 5.0, Vec2{ 160, 100 }, 120, 120, shadow);
		SimpleGUI::Slider(U"offsetY: {:.1f}"_fmt(shadowOffset.y), shadowOffset.y, -5.0, 5.0, Vec2{ 160, 140 }, 120, 120, shadow);

		SimpleGUI::Headline(U"Text", Vec2{ 420, 20 });
		SimpleGUI::Slider(U"R", textColor.r, Vec2{ 420, 60 }, 20, 80);
		SimpleGUI::Slider(U"G", textColor.g, Vec2{ 420, 100 }, 20, 80);
		SimpleGUI::Slider(U"B", textColor.b, Vec2{ 420, 140 }, 20, 80);
		SimpleGUI::Slider(U"A", textColor.a, Vec2{ 420, 180 }, 20, 80);

		SimpleGUI::Headline(U"Outline", Vec2{ 540, 20 });
		SimpleGUI::Slider(U"R", outlineColor.r, Vec2{ 540, 60 }, 20, 80, outline);
		SimpleGUI::Slider(U"G", outlineColor.g, Vec2{ 540, 100 }, 20, 80, outline);
		SimpleGUI::Slider(U"B", outlineColor.b, Vec2{ 540, 140 }, 20, 80, outline);
		SimpleGUI::Slider(U"A", outlineColor.a, Vec2{ 540, 180 }, 20, 80, outline);

		SimpleGUI::Headline(U"Shadow", Vec2{ 660, 20 });
		SimpleGUI::Slider(U"R", shadowColor.r, Vec2{ 660, 60 }, 20, 80, shadow);
		SimpleGUI::Slider(U"G", shadowColor.g, Vec2{ 660, 100 }, 20, 80, shadow);
		SimpleGUI::Slider(U"B", shadowColor.b, Vec2{ 660, 140 }, 20, 80, shadow);
		SimpleGUI::Slider(U"A", shadowColor.a, Vec2{ 660, 180 }, 20, 80, shadow);

		SimpleGUI::ColorPicker(background, Vec2{ 780, 20 });
	}
}
```


## 31.21 文字単位で自由描画をする
通常のテキスト描画では、文字ごとに色や位置、大きさや回転を自由にカスタマイズすることができません。

文字単位で自由な描画を行いたい場合、`Font` の `.getGlyphs(text)` を使用して得られる `Array<Glyph>` を使います。`Glyph` には、個々の文字を自由に制御して描画するために必要な情報が用意されています。

### 31.21.1 基本
`Glyph` の `.codePoint` はその文字の UTF-32 コードポイントを、`.texture` は文字画像の `TextureRegion` を、`.getOffset()` はペンの位置からさらに必要なオフセットを、`.xAdvance` は現在の文字で進む X 座標の距離を表します。

次のようなコードを書くことで、自由描画で通常の描画を再現できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/21.1.png)

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Vec2& basePos, const Font& font, const String& text, const ColorF& color)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);

	const double fontHeight = font.height();

	Vec2 penPos{ basePos };

	// 文字単位で描画を制御するためのループ
	for (const auto& glyph : glyphs)
	{
		// 改行文字なら
		if (glyph.codePoint == U'\n')
		{
			// ペンの X 座標をリセット
			penPos.x = basePos.x;

			// ペンの Y 座標をフォントの高さ分進める
			penPos.y += fontHeight;

			continue;
		}

		// penPos を可視化したい場合はコメントを外す
		//penPos.asCircle(3).drawFrame(1, Palette::Red);
		//Math::Round(penPos + glyph.getOffset()).asCircle(3).drawFrame(1, Palette::Green);

		// 文字のテクスチャをペンの位置に文字ごとのオフセットを加算して描画
		//（ビットマップ方式に限り、Math::Round() で整数座標に調整すると品質が向上する）
		glyph.texture.draw(Math::Round(penPos + glyph.getOffset()), color);

		// ペンの X 座標を文字の幅の分進める
		penPos.x += glyph.xAdvance;
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });
	const Font font{ 50, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		DrawGlyphs(Vec2{ 40, 40 }, font, text, ColorF{ 0.11 });
	}
}
```

### 31.21.2 応用
前述のコードをカスタマイズすることで、文字単位で自由な描画を行うことができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/21.2.png)

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Vec2& basePos, const Font& font, const String& text)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);
	const double fontHeight = font.height();
	Vec2 penPos{ basePos };
	int32 index = 0;

	for (const auto& glyph : glyphs)
	{
		if (glyph.codePoint == U'\n')
		{
			penPos.x = basePos.x;
			penPos.y += fontHeight;
			++index;
			continue;
		}

		const Vec2 offset{ 0, (Periodic::Sine1_1(2s, Scene::Time() + index * 0.3) * 8.0) };
		glyph.texture.draw((penPos + glyph.getOffset() + offset), HSV{ (index * 10) });
		penPos.x += glyph.xAdvance;
		++index;
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });
	const Font font{ 50, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		DrawGlyphs(Vec2{ 40, 40 }, font, text);
	}
}
```

### 31.21.3 SDF / MSDF 対応
SDF / MSDF 方式のフォントを自由描画する場合、次のように `ScopedCustomShader2D` を作成し、そのオブジェクトが有効なスコープ内でグリフテクスチャを描画します。Distance field 画像を描画するために、`Font::GetPixelShader()` で取得できるカスタムシェーダの適用が必要であるためです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/21.3.png)

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Vec2& basePos, const Font& font, const String& text, double fontSize, const ColorF& color)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);
	const double scale = (fontSize / font.fontSize());
	const double fontHeight = (font.height() * scale);
	{
		const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method()) };
		Vec2 penPos{ basePos };

		for (const auto& glyph : glyphs)
		{
			if (glyph.codePoint == U'\n')
			{
				penPos.x = basePos.x;
				penPos.y += fontHeight;
				continue;
			}

			glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale)), color);
			penPos.x += (glyph.xAdvance * scale);
		}
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });
	const Font font{ FontMethod::MSDF, 50, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		DrawGlyphs(Vec2{ 40, 40 }, font, text, 30, ColorF{ 0.11 });

		DrawGlyphs(Vec2{ 40, 240 }, font, text, 50, ColorF{ 0.11 });
	}
}
```

### 31.21.4 SDF / MSDF + テキストスタイル対応
SDF / MSDF 方式のフォントの自由描画でテキストスタイルに対応するには、次のようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/21.4.png)

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Vec2& basePos, const Font& font, const String& text, double fontSize, const TextStyle& textStyle, const ColorF& color)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);
	const double scale = (fontSize / font.fontSize());
	const double fontHeight = (font.height() * scale);
	{
		const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method(), textStyle.type) };
		Graphics2D::SetSDFParameters(textStyle);
		Vec2 penPos{ basePos };

		for (const auto& glyph : glyphs)
		{
			if (glyph.codePoint == U'\n')
			{
				penPos.x = basePos.x;
				penPos.y += fontHeight;
				continue;
			}

			glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale)), color);
			penPos.x += (glyph.xAdvance * scale);
		}
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });
	const Font font{ FontMethod::MSDF, 50, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		DrawGlyphs(Vec2{ 40, 40 }, font, text, 30, TextStyle::Default(), ColorF{ 0.11 });

		const double outlineScale = 0.2;
		const ColorF outlineColor{ 0.0, 0.3, 0.6 };
		const Vec2 shadowOffset{ 2, 2 };
		const ColorF shadowColor{ 0.0, 0.5 };

		DrawGlyphs(Vec2{ 40, 240 }, font, text, 50, TextStyle::OutlineShadow(outlineScale, outlineColor, shadowOffset, shadowColor), ColorF{ 1.0 });
	}
}
```


## 31.22 縦書きでテキストを描画する
テキストの縦書きに関する機能は未実装です。将来のバージョンで実装予定です。


## 31.23 フォントのプリロード
Siv3D の `Font` は、初めて描いた文字の画像を内部でレンダリングしてキャッシュするため、リアルタイムで動作するゲームの途中で大量のテキストを初めて表示すると、そのフレームの実行時間が長くなり、フレームレートが一瞬低下することがあります。`.preload(text)` を使って、`text` に含まれる文字を（重複する場合は除去して）あらかじめレンダリングしてキャッシュしておくと、ゲームの実行中の瞬間的な高負荷を防ぐことができます。

`.getTexture()` を使うと、`Font` の内部にキャッシュされている `Texture` を取得できます。

### 31.23.1 プリロードを使わないときの動作の様子
次のコードはプリロードを使わない場合の動作の様子です。キャッシュテクスチャには、実行中に随時文字が追加されていきます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/23.1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 20, Typeface::Bold };

	const String text = U"Siv3D の Font は、初めて描いた文字の画像を内部でレンダリングしてキャッシュするため、リアルタイムで動作するゲームの途中で大量のテキストを初めて表示すると、そのフレームの実行時間が長くなり、フレームレートが一瞬低下することがあります。.preload(text) を使って、text に含まれる文字をあらかじめレンダリングしてキャッシュしておくと、ゲームの実行中の瞬間的な高負荷を防ぐことができます。";

	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		const int32 count = (stopwatch.ms() / 30);

		font(text.substr(0, count)).draw(Rect{ 20, 20, 760, 240 }, ColorF{ 0.25 });

		font.getTexture().draw(20, 300).drawFrame(0, 1, Palette::Black);
	}
}
```

### 31.23.2 プリロードを使ったときの様子
次のコードはプリロードを使うサンプルです。実行前にキャッシュテクスチャには、`text` に含まれる文字がすべてレンダリングされています。

`String` のメンバ関数 `.sorted_and_uniqued()` は、文字列中の文字をソートして重複を除去した文字列を返します。文字列に対してこの前処理を行うと、プリロード時の負荷が軽減されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/23.2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 20, Typeface::Bold };

	const String text = U"Siv3D の Font は、初めて描いた文字の画像を内部でレンダリングしてキャッシュするため、リアルタイムで動作するゲームの途中で大量のテキストを初めて表示すると、そのフレームの実行時間が長くなり、フレームレートが一瞬低下することがあります。.preload(text) を使って、text に含まれる文字をあらかじめレンダリングしてキャッシュしておくと、ゲームの実行中の瞬間的な高負荷を防ぐことができます。";

	// text は .sorted_and_uniqued() で重複をあらかじめ除いておくと、プリロード時の負荷が軽減される
	font.preload(text.sorted_and_uniqued());

	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		const int32 count = (stopwatch.ms() / 30);

		font(text.substr(0, count)).draw(Rect{ 20, 20, 760, 240 }, ColorF{ 0.25 });

		font.getTexture().draw(20, 300).drawFrame(0, 1, Palette::Black);
	}
}
```


## 31.24 フォールバックフォントの設定
1 つの書体では、すべての文字をカバーできない場合があります。そこで、別の書体のフォントを**フォールバック**として登録しておくことで、メインの書体でカバーできない文字を別の書体で描画することができます。

フォールバックフォントを設定すると、基本のフォントで描けない文字が見つかったとき、もしフォールバックフォントで描けたら、そのフォントを使います。フォールバックフォントを設定するには、`.addFallback()` で作成済みの `Font` を渡します。フォールバックフォントは何個でも設定でき、先に設定したものが優先して使われます。また、カラー絵文字フォントをフォールバックフォントとして設定した場合、描画サイズは基本のフォントのサイズに合わせられます。

フォールバックフォントは、主にテキスト内に絵文字や複数の言語を含みたい場合に使用します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/24.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });

	const Font font0{ 36, Typeface::Regular };
	const Font font1{ 36, Typeface::Regular };
	const Font font2{ 36, Typeface::Regular };

	const Font fontJP{ 36, Typeface::CJK_Regular_JP };
	const Font fontEmoji{ 36, Typeface::ColorEmoji };

	// font1 にフォールバックフォントを 1 つ追加
	font1.addFallback(fontJP);

	// font2 にフォールバックフォントを 2 つ追加
	font2.addFallback(fontJP);
	font2.addFallback(fontEmoji);

	const String text = U"Hello! こんにちは 你好 안녕하세요 🐈🐕🚀";

	while (System::Update())
	{
		font0(text).draw(40, 40, ColorF{ 0.11 });
		font1(text).draw(40, 100, ColorF{ 0.11 });
		font2(text).draw(40, 160, ColorF{ 0.11 });
	}
}
```

## 31.25 文字を Polygon で取得する
`Font` のメンバ関数 `.renderPolygons()` を使うと、文字列を描画したときの各文字の `PolygonGlyph` を取得できます。これは文字を画像ではなく多角形で表現するものです。次のコードのようにすると、文字列を指定した位置に描画するときの各文字の `Polygon` を取得できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/25.png)

```cpp
# include <Siv3D.hpp>

// 文字列を描画したときの各文字の Polygon を返す関数
Array<Polygon> ToPolygons(const Vec2& basePos, const Array<PolygonGlyph>& glyphs)
{
	Array<Polygon> polygons;

	Vec2 penPos{ basePos };

	for (const auto& glyph : glyphs)
	{
		for (const auto& polygon : glyph.polygons)
		{
			polygons << polygon.movedBy(penPos + glyph.getOffset());
		}

		penPos.x += glyph.xAdvance;
	}

	return polygons;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });
	const Font font{ 80, Typeface::Bold };
	const String text = U"こんにちは、Siv3D!";
	const Array<Polygon> polygons = ToPolygons(Vec2{ 40, 40 }, font.renderPolygons(text));

	while (System::Update())
	{
		for (size_t i = 0; i < polygons.size(); ++i)
		{
			polygons[i].draw(HSV{ (i * 50) });
		}
	}
}
```

## 31.26 文字を LineString で取得する
`Font` のメンバ関数 `.renderOutlines()` を使うと、文字列を描画したときの各文字の `OutlineGlyph` を取得できます。これは文字を画像ではなく輪郭の `LineString` の集合で表現するものです。次のコードのようにすると、文字列を指定した位置に描画するときの各文字の `LineString` を取得できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/26.png)

```cpp
# include <Siv3D.hpp>

// 文字列を描画したときの各文字の LineString を返す関数
Array<LineString> ToLineStrings(const Vec2& basePos, const Array<OutlineGlyph>& glyphs)
{
	Array<LineString> lines;

	Vec2 penPos{ basePos };

	for (const auto& glyph : glyphs)
	{
		for (const auto& ring : glyph.rings)
		{
			lines << ring.movedBy(penPos + glyph.getOffset());
		}

		penPos.x += glyph.xAdvance;
	}

	return lines;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.7, 0.9, 0.8 });
	const Font font{ 80, Typeface::Bold };
	const String text = U"こんにちは、Siv3D!";
	const Array<LineString> lines = ToLineStrings(Vec2{ 40, 40 }, font.renderOutlines(text));

	while (System::Update())
	{
		for (size_t i = 0; i < lines.size(); ++i)
		{
			lines[i].drawClosed(2, HSV{ (i * 50) });
		}
	}
}
```