# 34. 文字を描く
フォントを使って様々なスタイルのテキストを描く方法を学びます。

## 34.1 フォントの基本
- 画面にテキストを描画するときのフォントは `Font` クラスで管理します

### 34.1.1 描画方式
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

### 34.1.2 基本サイズ
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
- 文字画像データは、実行中に必要に応じて作成され、キャッシュされます（**34.27** 参照）
- あるフレームで、キャッシュに存在しないたくさんの新しい字種を描画する場合、それらの文字画像データを一度に作成する必要があるため、そのフレームの処理時間が増加します
- とくに SDF / MSDF 方式では、1 つあたりの文字画像データの作成に時間がかかるため、実行時に目立つ遅延が発生する可能性があります
- **34.28** のプリロード機能を使って、あらかじめ必要な文字画像データを作成しておくことで、実行時の遅延を軽減できます

### 34.1.3 書体
- 「メイリオ」「Arial」など、フォントの種類を**書体**と呼びます
- 書体はフォントを作成するときに指定し、あとから変更できません
- 書体を指定しない場合は、Siv3D に同梱されている標準書体（レギュラー）が使われます

### 34.1.4 フォントスタイル
- 一部の書体は、**フォントスタイル** を指定することで、太字や斜体、ビットマップフォントなどのスタイルを変更できます
- フォントスタイルはフォントを作成するときに指定し、あとから変更できません
- フォントスタイルを指定しない場合は、通常のフォントが作成されます

### 34.1.5 テキストスタイル
- SDF / MSDF 方式のフォントでは、次のような **テキストスタイル** をテキスト描画時に適用できます
	- **影：**任意方向に影を追加します
	- **輪郭：**文字に輪郭を追加します
- テキストスタイルは、フォントを使った個々のテキスト描画時に指定します
- テキストスタイルを指定しない場合は、通常のスタイルで描画されます

### 34.1.6 画像バッファ幅
- 画像バッファ幅は、文字画像データを作成するときの、文字の周囲の余白の幅です
- デフォルトでは `2` が使われます
- SDF / MSDF 方式で大きな影や輪郭を作りたい場合、大きめの画像バッファ幅を指定しないと、影や輪郭が切れてしまうことがあります
- 画像バッファ幅はフォント作成後に `.setBufferThickness()` で指定します
- 大きな画像バッファ幅は、メモリの消費量と、実行時の文字画像キャッシュ作成時間を増加させるため、バランスを考えて選ぶ必要があります

### 34.1.7 フォントのフォールバック
- 1 つの書体では、必要なすべての文字をカバーできない場合があります
- そこで、別の書体のフォントを**フォールバック**として登録し、メインの書体でカバーできない文字を別のフォントでカバーすることができます
- おもにテキスト内に複数の言語や絵文字を含む場合に使用します

## 34.2 フォントの作成と描画

### フォントの作成
- フォントの作成にはいくつかの方法があります
    - **34.3, 34.4** 標準書体から作成
    - **34.5** フォントファイルから作成
    - **34.6** PC にインストールされているフォントファイルから作成
- フォントの作成にはコストがかかるため、通常はメインループの前で行います
- メインループ内で作成する場合には、毎フレーム作成されないよう制御が必要です

### テキストの描画
- `Font` オブジェクトの `()` 演算子にテキストを渡すと `DrawableText` オブジェクトが得られます
- 実際にテキストを描画するには、`DrawableText` のメンバ関数を使います
    - **34.10** 左上座標を指定した描画 `.draw()`
    - **34.11** 中心座標を指定した描画 `.drawAt()`
	- **34.12** ベースラインを指定した描画 `.drawBase()`
	- **34.13** それ以外の座標を指定した描画 `.draw(Args::...)`
    - **34.14** 長方形を指定した描画 `.draw(rect)`

```cpp
font(U"Hello, Siv3D!").draw(40, Vec2{ 40, 40 });
```


## 34.3 標準書体（1）
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


## 34.4 標準書体（2）
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


## 34.5 フォントファイルから作成
- 独自に用意したフォントファイルをからフォントを作成するには、`Font` のコンストラクタにフォントファイルのパスを指定します
- ファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します
	- 例えば `U"example/font/RocknRoll/RocknRollOne-Regular.ttf"` は、実行ファイルがあるフォルダ（`App` フォルダ）の `example/font/RocknRoll/` フォルダにある `RocknRollOne-Regular.ttf` というファイルを指します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// RocknRollOne-Regular.ttf をロードして使う
	const Font font{ FontMethod::MSDF, 48, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"Hello, Siv3D!\nこんにちは！").draw(60, Vec2{ 40, 40 }, ColorF{ 0.2 });
	}
}
```


## 34.6 PC にインストールされているフォントファイルから作成
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
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

# if SIV3D_PLATFORM(WINDOWS)

	const FilePath path = (FileSystem::GetFolderPath(SpecialFolder::SystemFonts) + U"arial.ttf");

# elif SIV3D_PLATFORM(MACOS)

	const FilePath path = (FileSystem::GetFolderPath(SpecialFolder::SystemFonts) + U"Helvetica.dfont");

# endif

	Print << path;

	const Font font{ FontMethod::MSDF, 48, path };

	while (System::Update())
	{
	# if SIV3D_PLATFORM(WINDOWS)

		font(U"Arial").draw(80, Vec2{ 40, 40 }, ColorF{ 0.2 });

	# elif SIV3D_PLATFORM(MACOS)

		font(U"Helvetica").draw(80, Vec2{ 40, 40 }, ColorF{ 0.2 });

	# endif
	}
}
```


## 34.7 空のフォント
- `Font` 型のオブジェクトは、デフォルトでは**空のフォント**を持っています
- 空のフォントを使用してもエラーにはなりませんが、何も描画されません
- フォントファイルのロードに失敗した場合にも空のテクスチャになります
- 空のフォントであるかを調べるには、`if (font.isEmpty())`, `if (font)`, `if (not font)` を使います
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Font font1;

	Print << font1.isEmpty();

	// テクスチャを代入する
	font1 = Font{ FontMethod::MSDF, 48 };

	Print << font1.isEmpty();

	// 存在しないフォントファイルを指定する
	const Font font2{ FontMethod::MSDF, 48, U"example/aaa.ttf" };

	if (not font2)
	{
		Print << U"Failed to load the font file";
	}

	while (System::Update())
	{
		// 空のフォントを使って描画する
		font2(U"Arial").draw(80, Vec2{ 40, 40 }, ColorF{ 0.2 });
	}
}
```


## 34.8 文字列のフォーマット
- 作成したフォント `font` を使い、`font(テキスト).draw(サイズ, pos, 色);` のようにして、サイズ・位置・色を指定してテキストを表示します
- `font(テキスト)` のテキストの部分には、文字列だけでなく、`Print` 可能な値（数値や `Point` など）をいくつでも記述できます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		font(U"Hello, Siv3D!").draw(60, Vec2{ 40, 40 }, ColorF{ 1.0 });

		// 文字列以外を渡すとフォーマット（文字列化）される
		font(Cursor::Pos()).draw(60, Vec2{ 40, 200 }, ColorF{ 0.2 });

		// 複数渡すと、それぞれをフォーマットした文字列をつなげる
		font(123, U"ABC").draw(40, Vec2{ 40, 360 }, Palette::Seagreen);

		font(U"{}/{}/{}"_fmt(2025, 12, 31)).draw(40, Vec2{ 40, 420 }, Palette::Deepskyblue);
	}
}
```


## 34.9 改行
- テキストの中に改行文字 `'\n'` が含まれていると、そこで改行されます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		font(U"Hello,\nSiv3D!\n\n!!").draw(60, Vec2{ 40, 40 }, ColorF{ 0.2 });
	}
}
```


## 34.10 左上座標を指定した描画
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
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	while (System::Update())
	{
		font(U"Siv3D").draw(80, Vec2{ 0, 0 }, ColorF{ 0.2 });

		font(U"Siv3D").draw(60, Vec2{ 200, 200 }, ColorF{ 0.2 });

		font(U"Siv3D").draw(40, Cursor::Pos(), ColorF{0.2});
	}
}
```


## 34.11 中心座標を指定した描画
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
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	const Rect rect{ 100, 300, 280, 80 };

	const Circle circle{ 600, 400, 80 };

	while (System::Update())
	{
		font(U"Siv3D").drawAt(80, Vec2{ 400, 60 }, ColorF{ 0.2 });

		rect.draw();
		font(U"Siv3D").drawAt(60, rect.center(), ColorF{ 0.2 });

		circle.draw();
		font(U"Siv3D").drawAt(40, circle.center, ColorF{ 0.2 });
	}
}
```


## 34.12 ベースラインを指定した描画
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
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font1{ FontMethod::MSDF, 48, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };
	const Font font2{ FontMethod::MSDF, 48, Typeface::Bold };

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		// ベースラインがそろわない
		font1(text).draw(30, Vec2{ 40, 100 }, ColorF{ 0.2 });
		font2(text).draw(20, Vec2{ 280, 100 }, ColorF{ 0.2 });
		font2(text).draw(50, Vec2{ 440, 100 }, ColorF{ 0.2 });

		Rect{ 0, 400, 800, 10 }.draw(Palette::Skyblue);

		// (40, 400) がベースラインの開始位置になるようテキストを描画
		font1(text).drawBase(30, Vec2{ 40, 400 }, ColorF{ 0.2 });
		Circle{ 40, 400 , 5 }.drawFrame(2, Palette::Red);

		// (280, 400) がベースラインの開始位置になるようテキストを描画
		font2(text).drawBase(20, Vec2{ 280, 400 }, ColorF{ 0.2 });
		Circle{ 280, 400 , 5 }.drawFrame(2, Palette::Red);

		// (440, 400) がベースラインの開始位置になるようテキストを描画
		font2(text).drawBase(50, Vec2{ 440, 400 }, ColorF{ 0.2 });
		Circle{ 440, 400 , 5 }.drawFrame(2, Palette::Red);
	}
}
```


## 34.13 それ以外の座標を指定した描画
- - **右端の中心位置**を指定し、それに合わせてテキストを描画するには、次の方法を使います
	- `.draw(Arg::topRight = pos, ...)`
	- `.draw(Arg::topRight(x, y), ...)`
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
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	const Rect rect{ 100, 300, 280, 80 };

	while (System::Update())
	{
		font(U"C++").draw(60, Arg::topCenter = Vec2{ 400, 20 }, ColorF{ 0.2 });

		font(U"Hello, Siv3D!").draw(30, Arg::topRight(780, 20), ColorF{ 0.2 });

		rect.draw();

		// 長方形に右揃えでテキストを描く
		font(U"Siv3D").draw(32, Arg::rightCenter = rect.rightCenter().movedBy(-20, 0), ColorF{0.2});
	}
}
```


## 34.14 長方形の中に収めた描画
- テキストを指定した長方形の中に収まるように描画するには、`font(テキスト).draw(rect)` を使います
- テキストのすべての文字が長方形内に収まった場合、関数は `true` を返します
- 一方、テキストがあふれる場合、あふれる部分が「`…`」に置き換えられ、関数は `false` を返します

| コード | 説明 |
| --- | --- |
| `.draw(rect, color);` | 長方形 `rect` の中に収まるようにテキストを描画 |
| `.draw(fontSize, rect, color);` | フォントサイズ `fontSize` で、長方形 `rect` の中に収まるようにテキストを描画 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/14.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"The quick brown fox jumps over the lazy dog.";

	const Rect rect1{ 60, 40, 200, 100 };
	const Rect rect2{ 60, 200, 300, 100 };
	const Rect rect3{ 60, 360, 420, 120 };

	while (System::Update())
	{
		{
			rect1.draw();

			const bool ok = font(text).draw(24, rect1.stretched(-10), ColorF{ 0.2 });

			if (not ok)
			{
				rect1.drawFrame(8, ColorF{ 1.0, 0.0, 0.0, 0.5 });
			}
		}

		{
			rect2.draw();

			const bool ok = font(text).draw(24, rect2.stretched(-10), ColorF{ 0.2 });

			if (not ok)
			{
				rect2.drawFrame(8, ColorF{ 1.0, 0.0, 0.0, 0.5 });
			}
		}

		{
			rect3.draw();

			const bool ok = font(text).draw(24, rect3.stretched(-20), ColorF{ 0.2 });

			if (not ok)
			{
				rect3.drawFrame(8, ColorF{ 1.0, 0.0, 0.0, 0.5 });
			}
		}
	}
}
```


## 34.15 描画される領域の取得
- 実際に描画を行わずに、描画される領域を取得するには、`font(テキスト)` の次のようなメンバ関数を使います

| コード | 説明 |
| --- | --- |
| `.region(x, y);` | `(x, y)` からテキストを描画したときの領域を `RectF` で返す |
| `.region(pos);` | `pos` からテキストを描画したときの領域を `RectF` で返す |
| `.region(fontSize, x, y);` | フォントサイズ `fontSize` で、`(x, y)` からテキストを描画したときの領域を `RectF` で返す |
| `.region(fontSize, pos);` | フォントサイズ `fontSize` で、`pos` からテキストを描画したときの領域を `RectF` で返す |
| `.regionAt(x, y);` | `(x, y)` を中心としてテキストを描画したときの領域を `RectF` で返す |
| `.regionAt(pos);` | `pos` を中心としてテキストを描画したときの領域を `RectF` で返す |
| `.regionAt(fontSize, x, y);` | フォントサイズ `fontSize` で、`(x, y)` を中心としてテキストを描画したときの領域を `RectF` で返す |
| `.regionAt(fontSize, pos);` | フォントサイズ `fontSize` で、`pos` を中心としてテキストを描画したときの領域を `RectF` で返す |
| `.regionBase(x, y);` | `(x, y)` をベースラインの開始位置としてテキストを描画したときの領域を `RectF` で返す |
| `.regionBase(pos);` | `pos` をベースラインの開始位置としてテキストを描画したときの領域を `RectF` で返す |
| `.regionBase(fontSize, x, y);` | フォントサイズ `fontSize` で、`(x, y)` をベースラインの開始位置としてテキストを描画したときの領域を `RectF` で返す |
| `.regionBase(fontSize, pos);` | フォントサイズ `fontSize` で、`pos` をベースラインの開始位置としてテキストを描画したときの領域を `RectF` で返す |


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/15.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"Hello, Siv3D!";
	const Vec2 pos{ 40, 40 };

	// font を使って text を pos の位置に描画したときのテキストの領域を取得
	const RectF rect = font(text).region(60,  pos);

	while (System::Update())
	{
		// 描画領域の長方形を事前に塗りつぶす
		rect.draw(Palette::Skyblue);

		// 長方形の上にテキストを描く
		font(text).draw(60, pos, ColorF{ 0.2 });

		// テキストの領域を
		font(text)
			.drawAt(80, Vec2{ 400, 300 }, ColorF{ 1.0 })
			.stretched(40, 0)	// 横に広げて
			.shearedX(20)		// 平行四辺形にして
			.drawFrame(2);		// 枠を描く
	}
}
```


## 34.16 フォントスタイル（太字・斜体）
- `Font` のコンストラクタで次のような `FontStyle` を指定することで、太字や斜体などのスタイルをフォントに適用できます
- 書体が対応していない場合、太字・斜体を疑似的に再現するため、SDF / MSDF 方式では字形に異常が生じることがあります

| コード | 説明 |
| --- | --- |
| `FontStyle::Bold` | 太字 |
| `FontStyle::Italic` | 斜体 |
| `FontStyle::BoldItalic` | 太字・斜体 |

	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/16.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 48, Typeface::CJK_Regular_JP };
	const Font fontBold{ 48, Typeface::CJK_Regular_JP, FontStyle::Bold };
	const Font fontItalic{ 48, Typeface::CJK_Regular_JP, FontStyle::Italic };
	const Font fontBoldItalic{ 48, Typeface::CJK_Regular_JP, FontStyle::BoldItalic };

	const String text = U"Hello, Siv3D! こんにちは。";

	while (System::Update())
	{
		font(text).draw(48, Vec2{ 40, 40 }, ColorF{ 0.2 });
		fontBold(text).draw(48, Vec2{ 40, 100 }, ColorF{ 0.2 });
		fontItalic(text).draw(48, Vec2{ 40, 160 }, ColorF{ 0.2 });
		fontBoldItalic(text).draw(48, Vec2{ 40, 220 }, ColorF{ 0.2 });
	}
}
```


## 34.17 フォントスタイル（ビットマップ）
- 書体がビットマップフォントに対応している場合、`Font` のコンストラクタで次のような `FontStyle` を指定することで、ドット感を保った文字を描画できます
- 描画方式としてビットマップ方式を使います

| コード | 説明 |
| --- | --- |
| `FontStyle::Bitmap` | ビットマップフォント |
| `FontStyle::BoldBitmap` | 太字のビットマップフォント |
| `FontStyle::ItalicBitmap` | 斜体のビットマップフォント |
| `FontStyle::BoldItalicBitmap` | 太字・斜体のビットマップフォント |
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/17.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font1{ 32, U"example/font/DotGothic16/DotGothic16-Regular.ttf", FontStyle::Bitmap };
	const Font font2{ 32, U"example/font/DotGothic16/DotGothic16-Regular.ttf", FontStyle::ItalicBitmap };
	const Font font3{ 60, U"example/font/DotGothic16/DotGothic16-Regular.ttf", FontStyle::Bitmap };
	const Font font4{ 60, U"example/font/DotGothic16/DotGothic16-Regular.ttf", FontStyle::ItalicBitmap };

# if SIV3D_PLATFORM(WINDOWS)

	const FilePath path = (FileSystem::GetFolderPath(SpecialFolder::SystemFonts) + U"msgothic.ttc");
	const Font font5{ 16, path, FontStyle::Bitmap };

# endif

	const String text = U"こんにちは、Siv3D!";

	while (System::Update())
	{
		font1(text).draw(32, Vec2{ 40, 40 }, ColorF{ 0.2 });
		font2(text).draw(32, Vec2{ 40, 100 }, ColorF{ 0.2 });
		font3(text).draw(60, Vec2{ 40, 160 }, ColorF{ 0.2 });
		font4(text).draw(60, Vec2{ 40, 240 }, ColorF{ 0.2 });

	# if SIV3D_PLATFORM(WINDOWS)

		{
			// ドット感を保って拡大表示する
			const ScopedRenderStates2D states{ SamplerState::ClampNearest };

			font5(text).draw(64, Vec2{ 40, 360 }, ColorF{ 0.2 });
		}

	# endif
	}
}
```


## 34.18 文字に影を付ける（2 回描画）
- 座標をずらして 2 回 テキストを描くことで、影の効果を作成できます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/18.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	
	const Vec2 pos{ 40, 40 };

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font(text).draw(100, pos.movedBy(4, 4), ColorF{ 0.2, 0.4, 0.3 });
		font(text).draw(100, pos, ColorF{ 1.0 });
	}
}
```


## 34.19 文字に影を付ける（テキストスタイル）
- SDF / MSDF 方式のフォントは、描画時に `TextStyle::Shadow(影のオフセット, 影の色)` を指定することで、影の効果を付与できます
- オフセット値は基本サイズに対する相対的な値です
- 画像バッファ幅が十分でない場合、影のオフセットが大きいと影が途切れることがあります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/19.png)

```cpp hl_lines="7 13"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font = Font{ FontMethod::MSDF, 48, Typeface::Bold }.setBufferThickness(4);

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font(text).draw(TextStyle::Shadow(Vec2{ 2, 2 }, ColorF{ 0.2, 0.4, 0.3 }), 100, Vec2{ 40, 40 }, ColorF{ 1.0 });
	}
}
```


## 34.20 文字に輪郭を付ける
- SDF / MSDF 方式のフォントは、描画時に次のスタイルを指定することで、輪郭の効果を付与できます
	- `TextStyle::Outline(輪郭スケール, 輪郭の色)`
	- `TextStyle::Outline(内側方向の輪郭スケール, 外側方向の輪郭スケール, 輪郭の色)`
- 輪郭スケールが大きすぎると、描画結果にノイズが生じます
- 書体によって異なりますが、最大で 0.2〜0.25 が目安です
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/20.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font = Font{ FontMethod::MSDF, 48, Typeface::Bold }.setBufferThickness(4);

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font(text).draw(TextStyle::Outline(0.2, ColorF{ 0.0 }), 100, Vec2{ 40, 40 }, ColorF{ 1.0 });
	}
}
```


## 34.21 文字に影と輪郭を付ける
- SDF / MSDF 方式のフォントは、描画時に次のスタイルを指定することで、影と輪郭の効果を付与できます
	- `TextStyle::OutlineShadow(輪郭スケール, 輪郭の色, 影のオフセット, 影の色)`
	- `TextStyle::OutlineShadow(内側方向の輪郭スケール, 外側方向の輪郭スケール, 輪郭の色, 影のオフセット, 影の色)`
- 輪郭スケールが大きすぎると、描画結果にノイズが生じます
- 書体によって異なりますが、最大で 0.2〜0.25 が目安です
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/21.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font = Font{ FontMethod::MSDF, 48, Typeface::Bold }.setBufferThickness(4);

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font(text).draw(TextStyle::OutlineShadow(0.2, ColorF{ 0.0 }, Vec2{ 2, 2 }, ColorF{ 0.2, 0.4, 0.3 }), 100, Vec2{ 40, 40 }, ColorF{ 1.0 });

		font(text).draw(TextStyle::OutlineShadow(0.15, ColorF{ 0.0 }, Vec2{ 1.0, 1.5 }, ColorF{ 0.0 }), 80, Vec2{ 40, 200 }, ColorF{ 1.0 });
	}
}
```


## 34.22 （参考）テキストスタイルのプレビュー
- 各方式におけるテキストスタイルの効果をプレビューするサンプルです
- マウスの右クリックやホイールで視点の移動・拡大ができます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/22.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 基本サイズ: 大きいと拡大描画時にきれいになるが、実行時のコストが増える
	const int32 baseSize = 70;

	// 画像バッファ幅: 大きいと大きな影のテキストスタイルを適用できるが、実行時のコストが増える
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
	HSV background = ColorF{ 0.6, 0.8, 0.7 };

	Camera2D camera{ Vec2{ 640, 360 }, 1.0 };

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


## 34.23 テキストを 1 文字ずつ描画
- 文字列クラス `String` のメンバ関数 `.substr(0, count)` で、文字列の先頭から `count` 文字の部分文字列を作成できます
	- `count` が実際の文字列の長さより大きい場合は、ちょうど末尾までの部分文字列を作成します
	- **チュートリアル 33.20** 参照
- ストップウォッチなどを使い `count` を増やしていくことで、文字列を 1 文字ずつ表示することができます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/23.png)

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

		font(text.substr(0, count)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.2 });
	}
}
```


## 34.24 文字単位での自由描画
- 通常のテキスト描画では、文字単位で色や位置、大きさや回転を制御することができません
- 文字単位で自由な描画を行いたい場合、`Font` の `.getGlyphs(text)` を使用して得られる `Array<Glyph>` を使います
- `Glyph` には、個々の文字を自由に制御して描画するために必要な情報が用意されています

### 34.24.1 自由描画の基本（ビットマップ方式）
- `Glyph` は次のようなメンバを持っています

| コード | 説明 |
| --- | --- |
| `.codePoint` | その文字の UTF-32 コードポイント |
| `.texture` | 文字画像の `TextureRegion` |
| `.getOffset(scale)` | ペンの位置からさらに必要なオフセット（拡大倍率指定） |
| `.xAdvance` | 現在の文字で進む X 座標の距離 |

- 次のようなコードで、`Glyph` の情報を使って文字単位で自由描画しつつも、通常のテキスト描画を再現できます

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Font& font, const String& text, const double fontSize, const Vec2& basePos, const ColorF& color)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);
	const double scale = (fontSize / font.fontSize());
	const double fontHeight = (font.height() * scale);

	Vec2 penPos{ basePos };

	// 文字単位で描画を制御するためのループ
	for (const auto& glyph : glyphs)
	{
		// 改行文字なら
		if (glyph.codePoint == U'\n')
		{
			// ペンの X 座標をリセットする
			penPos.x = basePos.x;

			// ペンの Y 座標をフォントの高さ分進める
			penPos.y += fontHeight;

			continue;
		}

		// penPos を可視化したい場合はコメントを外す
		//penPos.asCircle(3).drawFrame(1, Palette::Red);
		//(penPos + glyph.getOffset(scale)).asCircle(3).drawFrame(1, Palette::Green);

		// 文字のテクスチャをペンの位置に文字ごとのオフセットを加算して描画する
		if (scale == 1.0)
		{
			// 等倍で描画するビットマップ方式に限り、Math::Round() で整数座標に調整すると品質が向上する
			glyph.texture.draw(Math::Round(penPos + glyph.getOffset()), color);
		}
		else
		{
			glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale)), color);
		}

		// ペンの X 座標を文字の幅の分進める
		penPos.x += (glyph.xAdvance * scale);
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ 48, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		DrawGlyphs(font, text, 48, Vec2{ 40, 40 }, ColorF{ 0.2 });

		DrawGlyphs(font, text, 36, Vec2{ 40, 240 }, ColorF{ 1.0 });

		DrawGlyphs(font, text, 24, Vec2{ 40, 440 }, Palette::Seagreen);
	}
}
```

### 34.24.2 自由描画の基本（SDF / MSDF 方式）
- SDF / MSDF 方式では特殊なシェーダの適用が必要になるため、次のようなコードを使います
	- シェーダの適用中は図形描画などが使えないことに注意してください

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Font& font, const String& text, const double fontSize, const Vec2& basePos, const ColorF& color)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);
	const double scale = (fontSize / font.fontSize());
	const double fontHeight = (font.height() * scale);

	// このオブジェクトが存在する間、すべての 2D 描画に SDF / MSDF シェーダが適用される
	const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method()) };

	Vec2 penPos{ basePos };

	// 文字単位で描画を制御するためのループ
	for (const auto& glyph : glyphs)
	{
		// 改行文字なら
		if (glyph.codePoint == U'\n')
		{
			// ペンの X 座標をリセットする
			penPos.x = basePos.x;

			// ペンの Y 座標をフォントの高さ分進める
			penPos.y += fontHeight;

			continue;
		}

		// 文字のテクスチャをペンの位置に文字ごとのオフセットを加算して描画する
		glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale)), color);

		// ペンの X 座標を文字の幅の分進める
		penPos.x += (glyph.xAdvance * scale);
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		DrawGlyphs(font, text, 48, Vec2{ 40, 40 }, ColorF{ 0.2 });

		DrawGlyphs(font, text, 36, Vec2{ 40, 240 }, ColorF{ 1.0 });

		DrawGlyphs(font, text, 24, Vec2{ 40, 440 }, Palette::Seagreen);
	}
}
```

### 34.24.3 自由描画の応用
- 文字単位で座標や色を制御するサンプルです

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Font& font, const String& text, const double fontSize, const Vec2& basePos)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);
	const double scale = (fontSize / font.fontSize());
	const double fontHeight = (font.height() * scale);

	const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method()) };

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

		const Vec2 offset{ 0, (Periodic::Sine1_1(2s, (Scene::Time() + index * 0.3)) * 8.0) };

		glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale) + offset), HSV{ (index * 10) });
		penPos.x += (glyph.xAdvance * scale);

		++index;
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		DrawGlyphs(font, text, 55, Vec2{ 40, 40 });
	}
}
```

### 34.24.4 テキストスタイル対応
- SDF / MSDF 方式のフォントの自由描画でテキストスタイルに対応するには、次のようにします

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Font& font, const TextStyle& textStyle, const String& text, const double fontSize, const Vec2& basePos)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);
	const double scale = (fontSize / font.fontSize());
	const double fontHeight = (font.height() * scale);

	const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method(), textStyle.type) };
	Graphics2D::SetSDFParameters(textStyle);

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

		const Vec2 offset{ 0, (Periodic::Sine1_1(2s, (Scene::Time() + index * 0.3)) * 8.0) };

		glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale) + offset), HSV{ (index * 10) });
		penPos.x += (glyph.xAdvance * scale);

		++index;
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"The quick brown fox\njumps over the lazy dog.";

	while (System::Update())
	{
		DrawGlyphs(font, TextStyle::Default(), text, 55, Vec2{ 40, 40 });

		DrawGlyphs(font, TextStyle::OutlineShadow(0.2, ColorF{ 0.0 }, Vec2{ 2, 2 }, ColorF{ 0.0 }), text, 55, Vec2{ 40, 240 });
	}
}
```


## 34.25 縦書き
- テキストの縦書きに関する機能は未実装です。将来のバージョンで実装予定です
- 自由描画で次のように再現できますが、「」や句読点などが縦書きスタイルにならない制約があります
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/25.png)

```cpp
# include <Siv3D.hpp>

void DrawGlyphs(const Font& font, const String& text, const double fontSize, const Vec2& basePos, const ColorF& color)
{
	const Array<Glyph> glyphs = font.getGlyphs(text);
	const double scale = (fontSize / font.fontSize());
	const double fontHeight = (font.height() * scale);

	// このオブジェクトが存在する間、すべての 2D 描画に SDF / MSDF シェーダが適用される
	const ScopedCustomShader2D shader{ Font::GetPixelShader(font.method()) };

	Vec2 penPos{ basePos };

	// 文字単位で描画を制御するためのループ
	for (const auto& glyph : glyphs)
	{
		// 改行文字なら
		if (glyph.codePoint == U'\n')
		{
			// ペンの Y 座標をリセットする
			penPos.y = basePos.y;

			// ペンの X 座標を進める
			penPos.x -= fontHeight;

			continue;
		}

		// 文字のテクスチャをペンの位置に文字ごとのオフセットを加算して描画する
		glyph.texture.scaled(scale).draw((penPos + glyph.getOffset(scale)), color);

		// ペンの Y 座標を文字の高さの分進める
		penPos.y += (glyph.yAdvance * scale);
	}
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"古池や\n蛙飛び込む\n水の音";

	while (System::Update())
	{
		DrawGlyphs(font, text, 48, Vec2{ 600, 40 }, ColorF{ 0.2 });

		DrawGlyphs(font, text, 36, Vec2{ 400, 40 }, ColorF{ 1.0 });

		DrawGlyphs(font, text, 24, Vec2{ 200, 40 }, Palette::Seagreen);
	}
}
```


## 34.26 フォールバックフォント
- 1 つの書体では、テキストに登場するすべての文字をカバーできない場合があります
- そこで、別の書体のフォントを**フォールバック**として登録しておくことで、メインの書体でカバーできない文字を別の書体で描画できます
- フォールバックフォントを設定すると、基本のフォントで描けない文字があり、フォールバックフォントを使うと描ける場合に、そのフォントを使います
- フォールバックフォントを設定するには、`.addFallback()` で作成済みの `Font` を渡します
- フォールバックフォントは何個でも設定でき、先に設定したものが優先して使われます
- カラー絵文字フォントをフォールバックフォントとして設定した場合、描画サイズは登録先のフォントのサイズに合わせられます
- フォールバックフォントは、主にテキスト内に絵文字や複数の言語を含みたい場合に使用します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font0{ FontMethod::MSDF, 48, Typeface::Regular };
	const Font font1{ FontMethod::MSDF, 48, Typeface::Regular };
	const Font font2{ FontMethod::MSDF, 48, Typeface::Regular };

	const Font fontCJK{ FontMethod::MSDF, 48, Typeface::CJK_Regular_JP };
	const Font fontEmoji{ 48, Typeface::ColorEmoji };

	// font1 にフォールバックフォントを 1 つ追加する
	font1.addFallback(fontCJK);

	// font2 にフォールバックフォントを 2 つ追加する
	font2.addFallback(fontCJK);
	font2.addFallback(fontEmoji);

	const String text = U"Hello! こんにちは 你好 안녕하세요 🐈🐕🚀";

	while (System::Update())
	{
		font0(U"font0:\n" + text).draw(36, Vec2{40, 40}, ColorF{ 0.2 });
		font1(U"font1:\n" + text).draw(36, Vec2{ 40, 200 }, ColorF{ 0.2 });
		font2(U"font2:\n" + text).draw(36, Vec2{ 40, 360 }, ColorF{ 0.2 });
	}
}
```


## 34.27 フォントのキャッシュへのアクセス
- フォントは、初めて描く文字画像データを内部でレンダリングしてキャッシュします
- `.getTexture()` を使うと、フォントのキャッシュを `Texture` 形式で取得し、内容を確認できます
- ビットマップ方式では白 + アルファチャンネルの画像、SDF / MSDF 方式では Distance field 形式の画像です
- 次のサンプルを実行すると、フォントのキャッシュに随時文字が追加されていく様子を確認できます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/27.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 24, Typeface::Bold };

	const String text = U"Siv3D（シブスリーディー）は、音や画像、AI を使ったゲームやアプリを、モダンな C++ コードで楽しく簡単に開発できるオープンソースのフレームワークです。豊富なサンプルコードとチュートリアルが用意され、オンラインのユーザコミュニティで気軽に質問や相談ができます。";

	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		const int32 count = (stopwatch.ms() / 50);

		font(text.substr(0, count)).draw(Rect{ 20, 20, 760, 240 }, Palette::Seagreen);

		Rect{ 20, 300, font.getTexture().size() }.draw(ColorF{ 0.0 });

		font.getTexture().draw(20, 300);
	}
}
```


## 34.28 フォントのプリロード
- リアルタイムで動作するゲームの途中で大量のテキストを初めて表示すると、一度にレンダリング・キャッシュすべき文字が多くなります
- すると、フレーム時間にスパイク（そのフレームの処理時間だけ極端に長くなること）が生じ、プレイ体験に影響を与えることがあります
- `.preload(text)` を使うと、`text` に含まれる文字をあらかじめ内部でレンダリングしてキャッシュできます
- ゲームの起動時やロード画面でプリロードを行うことで、ゲーム実行中のフレーム時間のスパイクを防ぐことができます
- `String` のメンバ関数 `.sorted_and_uniqued()` は、文字列中の文字をソートして重複を除去した文字列を返します。プリロード文字列に対してこの前処理を行うと、プリロードの負荷が軽減されます
- 次のサンプルでは、起動時にすべての文字をプリロードします

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/28.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 24, Typeface::Bold };

	const String text = U"Siv3D（シブスリーディー）は、音や画像、AI を使ったゲームやアプリを、モダンな C++ コードで楽しく簡単に開発できるオープンソースのフレームワークです。豊富なサンプルコードとチュートリアルが用意され、オンラインのユーザコミュニティで気軽に質問や相談ができます。";

	// text は .sorted_and_uniqued() で重複をあらかじめ除いておくと、プリロード時の負荷が軽減される
	font.preload(text.sorted_and_uniqued());

	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		const int32 count = (stopwatch.ms() / 50);

		font(text.substr(0, count)).draw(Rect{ 20, 20, 760, 240 }, Palette::Seagreen);

		Rect{ 20, 300, font.getTexture().size() }.draw(ColorF{ 0.0 });

		font.getTexture().draw(20, 300);
	}
}
```


## 34.29 文字を Polygon で取得
- 文字を画像形式ではなく、`Polygon` 形式で取得して描画することができます
- 頂点単位での加工や、文字の形状を利用した演出などに活用できます
- `Font` のメンバ関数 `.renderPolygons()` を使うと、文字列を描画したときの各文字の `PolygonGlyph` を取得できます
	- 描画方式はこの関数には影響しません
- フォントの基本サイズを大きくすると、多角形の頂点数も増加します（より高品質な多角形が得られます）
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/29.png)

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
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 80, Typeface::Bold };

	const String text = U"こんにちは、Siv3D!";

	const Array<Polygon> polygons = ToPolygons(Vec2{ 20, 20 }, font.renderPolygons(text));

	while (System::Update())
	{
		for (size_t i = 0; i < polygons.size(); ++i)
		{
			polygons[i].draw(HSV{ (i * 50) });

			polygons[i].drawWireframe(1, ColorF{ 0.2, Periodic::Square0_1(2s) });
		}
	}
}
```


## 34.30 文字を LineString で取得
- 文字を `LineString` 形式で取得して描画することができます
- 輪郭に対する処理や、文字の形状を利用した演出などに活用できます
- `Font` のメンバ関数 `.renderOutlines()` を使うと、文字列を描画したときの各文字の `OutlineGlyph` を取得できます
	- 描画方式はこの関数には影響しません
- フォントの基本サイズを大きくすると、頂点数も増加します（より高品質な線分集合が得られます）
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/30.png)

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
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 80, Typeface::Bold };

	const String text = U"こんにちは、Siv3D!";

	const Array<LineString> lines = ToLineStrings(Vec2{ 20, 20 }, font.renderOutlines(text));

	while (System::Update())
	{
		for (size_t i = 0; i < lines.size(); ++i)
		{
			lines[i].drawClosed(2, HSV{ (i * 50) });
		}
	}
}
```

