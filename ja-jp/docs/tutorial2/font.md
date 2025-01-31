# 33. 文字を描く
フォントを使って様々なスタイルのテキストを描く方法を学びます。

## 33.1 フォントの基本
- 画面にテキストを描画するときのフォントは `Font` クラスで管理します
- フォントはいくつかの方法・方式で作成できます
- フォントの作成にはコストがかかるため、通常はメインループの前で行います
- メインループ内で作成する場合には、毎フレーム作成されないような制御が必要です

### 描画方式
- ほとんどのフォントは、**描画方式 `FontMethod`** を次の 3 つから選ぶことができます
- 描画方式を指定しない場合は、**ビットマップ方式** が使われます

| 方式 | 説明 | 適した用途 |
| --- | --- | --- |
| ビットマップ方式 | 軽量ですが、基本サイズ以上に拡大描画すると品質が低下します | 常に固定サイズでフォントを描画する場合や、複雑な字形の書体を用いる場合 |
| SDF 方式 | 基本サイズ以上に拡大描画しても品質が維持されます。影や輪郭エフェクトを追加できます。文字の角が少し丸くなる副作用があります | 文字に影や輪郭エフェクトを追加したりする場合 |
| MSDF 方式 | 基本サイズ以上に拡大描画しても品質が維持されます。影や輪郭エフェクトを追加できます | 文字を様々なサイズに拡大縮小したり、文字に影や輪郭エフェクトを追加したりする場合 |

### フォントの基本サイズ
- 個々の文字画像データはエンジン内部で作成され、メモリ上にキャッシュ（保存）されます
- このときキャッシュされる文字画像のサイズを **基本サイズ** と呼びます
- 基本サイズは、フォントを作成するときに指定します
- 基本サイズと異なるサイズでテキストを描画すると、拡大縮小が行われます

### 描画方式と基本サイズ
- ビットマップ方式で拡大が行われると、解像度の低い画像を拡大したときのように、文字の見た目が荒くなります
    - ビットマップ方式では、基本サイズと同じ大きさで描画することが想定されています
- SDF / MSDF 方式では、基本サイズ以上に拡大してテキストを描画しても品質が維持されます
- SDF / MSDF 方式では、ある程度大きな基本サイズが必要です
    - 複雑な文字で基本サイズが小さいと、品質が低下して字形が崩れることがあります
    - SDF / MSDF 方式では、英数字は `40`, 日本語フォントは `48` が基本サイズとして推奨されます
    - 一方で、大きな基本サイズは、メモリの消費量と文字画像キャッシュ作成時間を増加させるため、バランスを考えて選ぶ必要があります

### 書体
- 「メイリオ」「Arial」など、フォントの種類を**書体**と呼びます
- 書体はフォントを作成するときに指定します
- 書体を指定しない場合は、Siv3D に同梱されているデフォルトの書体 `Typeface::Regular` が使われます

### フォントスタイル
- 一部のフォントは、**フォントスタイル `FontStyle`** を指定することで、太字や斜体、ビットマップフォントなどのスタイルを変更できます
- フォントスタイルは、フォントを作成するときに指定します
- フォントスタイルを指定しない場合は、通常のフォントが作成されます

### テキストスタイル
- SDF / MSDF 方式のフォントでは、**テキストスタイル `TextStyle`** を指定して、文字の描画時に影や輪郭エフェクトを追加できます
- テキストスタイルは、テキストを描画するときに指定します
- テキストスタイルを指定しない場合は、通常のスタイルで描画されます

### フォントのフォールバック
- 1 つの書体では、すべての文字をカバーできない場合があります
- そこで、別の書体のフォントを**フォールバック**として登録し、メインの書体でカバーできない文字を別の書体で描画することができます
- おもにテキスト内に絵文字や複数の言語を含む場合に使用します


## 33.2 フォントの作成と描画

### フォントの作成
- フォントの作成にはいくつかの方法があります
    - 標準書体から作成
    - フォントファイルから作成
    - PC にインストールされているフォントファイルから作成

### テキストの描画
- `Font` オブジェクトの `()` 演算子にテキストを渡すと `DrawableText` オブジェクトが得られます
- 実際にテキストを描画するには、`DrawableText` のメンバ関数を使います
    - 左上座標を指定した描画 `.draw()`
    - 中心座標を指定した描画 `.drawAt()`
    - それ以外の座標を指定した描画 `.draw(Args::...)`

```cpp
font(U"Hello, Siv3D!").draw(40, Vec2{ 40, 40 });
```


## 33.3 フォントを作成する
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


## 33.4 標準書体
- Siv3D には異なる太さの 7 種類の日本語書体と、5 地域向けの CJK（中国語・韓国語・日本語対応）書体、白黒絵文字書体、カラー絵文字書体が標準書体として同梱されています
- `Font` のコンストラクタにおいて `Typeface::` で書体を指定することで、それらの書体からフォントを作成できます

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
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/5.png)

```cpp

```


## 33.6 PC にインストールされているフォントファイルから作成
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/6.png)

```cpp

```


## 33.7 空のフォント
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/7.png)

```cpp

```


## 33.8 左上座標を指定した描画
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/8.png)

```cpp

```


## 33.9 中心座標を指定した描画
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/9.png)

```cpp

```


## 33.10 ベースラインを指定した描画
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/10.png)

```cpp

```


## 33.11 それ以外の座標を指定した描画
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/11.png)

```cpp

```


## 33.12 長方形の中に収めた描画
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/12.png)

```cpp

```


## 33.13 描画される領域の取得
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/13.png)

```cpp

```


## 33.14 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/14.png)

```cpp

```


## 33.15 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/15.png)

```cpp

```


## 33.16 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/16.png)

```cpp

```


## 33.17 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/17.png)

```cpp

```


## 33.18 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/18.png)

```cpp

```


## 33.19 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/19.png)

```cpp

```


## 33.20 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/20.png)

```cpp

```


## 33.21 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/21.png)

```cpp

```


## 33.22 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/22.png)

```cpp

```


## 33.23 XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/font/23.png)

```cpp

```











## 31.1 Font
フォントは `Font` クラスで管理します。`Font 変数名{ フォントサイズ };` と書くことで**ビットマップ方式**のフォントを作成します。フォントの作成はコストがかかるため、**メインループの前**で行います。

作成したフォント `font` を使って、

- `font(テキスト).draw(x, y, color);`
- `font(テキスト).draw(pos, color);`

のようにして、テキストを、位置、色を指定して表示します。`color` を省略すると白色になります。

`font(テキスト)` のテキストの部分には、文字列だけでなく、フォーマット可能な値も記述できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 基本サイズ 50 のフォントを作成する
	const Font font{ 50 };

	while (System::Update())
	{
		// 左上位置 (40, 40) からテキストを描く
		font(U"Hello, Siv3D!").draw(40, 40);

		// 文字列以外を渡すとフォーマット（文字列化）される
		font(Cursor::Pos()).draw(50, 300);

		// 複数渡すと、それぞれをフォーマットした文字列をつなげる
		font(123, U"ABC").draw(50, 400, ColorF{ 0.5, 1.0, 0.5 });

		font(U"{}/{}/{}"_fmt(2023, 12, 31)).draw(50, 500, ColorF{ 1.0, 0.5, 0.0 });
	}
}
```


## 31.2 改行する
テキストの中に改行文字 `'\n'` が含まれていると、そこで改行されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 基本サイズ 50 のフォントを作成する
	const Font font{ 50 };

	while (System::Update())
	{
		font(U"Hello,\nSiv3D\n\n!!!").draw(40, 40);
	}
}
```

## 31.3 フォントの基本サイズ
`Font` のコンストラクタの第 1 引数にはフォントの基本サイズを指定します。単位はピクセルです。基本サイズはあとから変更できません。

!!! info "1 つの Font からさまざまなサイズの文字を描くには"
    ビットマップ方式のフォントは、基本サイズと同じ大きさでテキストを描画することが想定されているため、サイズ別にフォントを作成する必要があります。1 つの `Font` からさまざまなサイズの文字を描くには、後述する SDF / MSDF 方式のフォントを使用します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 基本サイズ 20 のフォント
	const Font font20{ 20 };

	// 基本サイズ 40 のフォント
	const Font font40{ 40 };

	// 基本サイズ 60 のフォント
	const Font font60{ 60 };

	// 基本サイズ 80 のフォント
	const Font font80{ 80 };

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font20(text).draw(40, 40);

		font40(text).draw(40, 80);

		font60(text).draw(40, 140);

		font80(text).draw(40, 220);
	}
}
```

## 31.4 標準書体
Siv3D には異なる太さの 7 種類の日本語フォントと、5 地域向けの CJK（中国語・韓国語・日本語対応）フォント、白黒絵文字フォント、カラー絵文字フォントが同梱されています。`Font` のコンストラクタにおいて `Typeface::` で書体を指定することで、それらの書体を利用できます。何も指定しなかった場合 `Typeface::Regular` が選択されます。

|Typeface|説明|
|--|--|
|`Typeface::Thin`|細い日本語フォント|
|`Typeface::Light`|やや細い日本語フォント|
|`Typeface::Regular`|通常日本語フォント|
|`Typeface::Medium`|やや太い日本語フォント|
|`Typeface::Bold`|太い日本語フォント|
|`Typeface::Heavy`|とても太い日本語フォント|
|`Typeface::Black`|最も太い日本語フォント|
|`Typeface::CJK_Regular_JP`|日本語デザインの CJK フォント|
|`Typeface::CJK_Regular_KR`|韓国語デザインの CJK フォント|
|`Typeface::CJK_Regular_SC`|簡体字デザインの CJK フォント|
|`Typeface::CJK_Regular_TC`|台湾繁体字デザインの CJK フォント|
|`Typeface::CJK_Regular_HK`|香港繁体字デザインの CJK フォント|
|`Typeface::MonochromeEmoji`|モノクロ絵文字フォント|
|`Typeface::ColorEmoji`|カラー絵文字フォント|

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font fontThin{ 36, Typeface::Thin };
	const Font fontLight{ 36, Typeface::Light };
	const Font fontRegular{ 36, Typeface::Regular };
	const Font fontMedium{ 36, Typeface::Medium };
	const Font fontBold{ 36, Typeface::Bold };
	const Font fontHeavy{ 36, Typeface::Heavy };
	const Font fontBlack{ 36, Typeface::Black };

	const Font fontJP{ 36, Typeface::CJK_Regular_JP };
	const Font fontKR{ 36, Typeface::CJK_Regular_KR };
	const Font fontSC{ 36, Typeface::CJK_Regular_SC };
	const Font fontTC{ 36, Typeface::CJK_Regular_TC };
	const Font fontHK{ 36, Typeface::CJK_Regular_HK };

	const Font fontMono{ 36, Typeface::MonochromeEmoji };

	// カラー絵文字フォントは、サイズの指定が無視されます
	const Font fontEmoji{ 36, Typeface::ColorEmoji };

	const String s0 = U"Hello, Siv3D!";
	const String s1 = U"こんにちは 你好 안녕하세요 骨曜喝愛遙扇";
	const String s2 = U"🐈🐕🚀";

	while (System::Update())
	{
		fontThin(s0).draw(40, 20);
		fontLight(s0).draw(40, 60);
		fontRegular(s0).draw(40, 100);
		fontMedium(s0).draw(40, 140);
		fontBold(s0).draw(40, 180);
		fontHeavy(s0).draw(40, 220);
		fontBlack(s0).draw(40, 260);

		fontJP(s1).draw(40, 300);
		fontKR(s1).draw(40, 340);
		fontSC(s1).draw(40, 380);
		fontTC(s1).draw(40, 420);
		fontHK(s1).draw(40, 460);

		fontMono(s2).draw(340, 20);
		fontEmoji(s2).draw(340, 60);
	}
}
```

## 31.5 フォントファイルからフォントを作成する
コンピュータ上にあるフォントファイルから `Font` を作成するには、`Font` のコンストラクタに、読み込みたいフォントファイルのパスを渡します。ファイルパスは、実行ファイルがあるフォルダ（`App` フォルダ）を基準とする相対パスか、絶対パスを使用します。

例えば `U"example/font/RocknRoll/RocknRollOne-Regular.ttf"` とすると、実行ファイルがあるフォルダ（開発中は `App` フォルダ）の `example/font/RocknRoll` フォルダの `RocknRollOne-Regular.ttf` というファイルを指します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// RocknRollOne-Regular.ttf をロードして使う
	const Font font{ 50, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };

	while (System::Update())
	{
		font(U"Hello, Siv3D!\nこんにちは！").draw(40, 40);
	}
}
```

## 31.6 PC にインストールされているフォントを使う
PC にインストールされているフォントは OS ごとに特殊なフォルダに保存されています。そのフォルダのパスを `FileSystem::GetFolderPath()` で取得し、フォントファイル名とつなげることで、ファイルパスを構築できます。`FileSystem::GetFolderPath()` に渡す `SpecialFolder` の種類と OS によって取得できるパスの対応表は次のとおりです。

|                            | Windows             | macOS                  | Linux       |
|----------------------------|:---------------------:|:------------------------:|:-------------:|
| `SpecialFolder::SystemFonts` | (OS):/WINDOWS/Fonts/ | /System/Library/Fonts/ | /usr/share/fonts/ |
| `SpecialFolder::LocalFonts`  | (OS):/WINDOWS/Fonts/ | /Library/Fonts/        | /usr/local/share/fonts/<br>(存在する場合) |
| `SpecialFolder::UserFonts`   | (OS):/WINDOWS/Fonts/ | ~/Library/Fonts/       | /usr/local/share/fonts/<br>(存在する場合) |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/6.png)

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

	const Font font{ 60, path };

	while (System::Update())
	{
# if SIV3D_PLATFORM(WINDOWS)

		font(U"Arial").draw(40, 40);

# elif SIV3D_PLATFORM(MACOS)

		font(U"Helvetica").draw(40, 40);

# endif
	}
}
```

`FilePath` は `String` の別名です。`String` と同じように扱えます。

`SIV3D_PLATFORM(WINDOWS)` や `SIV3D_PLATFORM(MACOS)` は Siv3D でプラットフォーム別のコードを書くときに使えるマクロです。


## 31.7 空のフォント
`Font` 型の変数は、デフォルトでは**空のフォント**を持っています。フォントの作成やロードに失敗した場合も空のフォントになります。

空のフォントは、**使用してもエラーにはなりませんが、描画しても何も表示されません。**

空のフォントであるかを調べるには、`if (font.isEmpty())`, `if (font)`, `if (not font)` を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Font font1;

	Print << font1.isEmpty();

	// テクスチャを代入する
	font1 = Font{ 40 };

	Print << font1.isEmpty();

	// 存在しないフォントファイルを指定する
	const Font font2{ 40, U"example/aaa.ttf" };

	if (not font2)
	{
		Print << U"Failed to load a font";
	}

	while (System::Update())
	{
		// 空のフォントを使って描画する
		font2(U"Hello, Siv3D!").draw(40, 40);
	}
}
```


## 31.8 フォントスタイルを変える
`Font` のコンストラクタに `FontStyle` を指定することで、イタリックやボールドなどのスタイルをフォントに適用できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ 50, Typeface::Regular };

	// ボールド
	const Font fontBold{ 50, Typeface::Regular, FontStyle::Bold };

	// イタリック
	const Font fontItalic{ 50, Typeface::Regular, FontStyle::Italic };

	// ボールド・イタリック
	const Font fontBoldItalic{ 50, Typeface::Regular, FontStyle::BoldItalic };

	const String text = U"Hello, Siv3D! こんにちは。";

	while (System::Update())
	{
		font(text).draw(40, 40);

		fontBold(text).draw(40, 100);

		fontItalic(text).draw(40, 160);

		fontBoldItalic(text).draw(40, 220);
	}
}
```


## 31.9 ビットマップフォントを使う
書体がビットマップフォントに対応している場合、フォントスタイルに `FontStyle::Bitmap` を指定することで、フィルタリングされずドット感を保った文字を描画できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font1{ 32, U"example/font/DotGothic16/DotGothic16-Regular.ttf" };
	const Font font2{ 32, U"example/font/DotGothic16/DotGothic16-Regular.ttf", FontStyle::Bitmap };
	const Font font3{ 60, U"example/font/DotGothic16/DotGothic16-Regular.ttf" };
	const Font font4{ 60, U"example/font/DotGothic16/DotGothic16-Regular.ttf", FontStyle::Bitmap };

# if SIV3D_PLATFORM(WINDOWS)

	const FilePath path = (FileSystem::GetFolderPath(SpecialFolder::SystemFonts) + U"msgothic.ttc");
	const Font font5{ 16, path };
	const Font font6{ 16, path, FontStyle::Bitmap };

# endif

	const String text = U"Hello, Siv3D! こんにちは。";

	while (System::Update())
	{
		font1(text).draw(40, 40, Palette::Black);
		font2(text).draw(40, 100, Palette::Black);
		font3(text).draw(40, 160, Palette::Black);
		font4(text).draw(40, 240, Palette::Black);

# if SIV3D_PLATFORM(WINDOWS)

		font5(text).draw(40, 360, Palette::Black);
		font6(text).draw(40, 400, Palette::Black);

# endif
	}
}
```

## 31.10 自由に拡大縮小できる SDF / MSDF 方式のフォントを使う
**SDF 方式** / **MSDF 方式**を使うと、文字ごとの Distance field 画像を生成し、基本サイズ以上に拡大しても画質が粗くならない手法で文字をレンダリングできます。さらに、SDF / MSDF には影や輪郭などのエフェクトを 1 回の draw 内で行える仕組みも用意されています。

各方式の利点と欠点は次のとおりです。

| 描画方式 | 縮小 | 拡大 | 影 | 輪郭 | 実行時負荷 | 備考 |
|--|:--:|:--:|:--:|:--:|:--:|:--|
| ビットマップ方式<br>`FontMethod::Bitmap`| 〇 | △ | 〇<br>(2 回 draw) | × | 低 | デフォルトの手法 |
| SDF 方式<br>`FontMethod::SDF`| 〇 | 〇 | ◎ | ◎ | 中 | 文字の角が丸くなるなど、細部の情報が失われやすい |
| MSDF 方式<br>`FontMethod::MSDF`| ◎ | ◎ | 〇 | 〇 | 高 | SDF より高品質 |

SDF / MSDF 方式時に設定する基本サイズは描画する字形の複雑さに応じて決める必要があります。画数の少ない数字やアルファベット、曲線的でシンプルな字形であれば、基本サイズが 40 以下でもきれいな文字をレンダリングできますが、複雑な字形になるほど、小さな Distance Field では描画結果が乱れたり、ノイズが目立つことがあります。一方で、大きすぎると描画に時間がかかってしまいます。SDF / MSDF をアプリケーションで使用する際は、テキストの描画結果を確認し、書体に応じて適切な基本サイズを選択しましょう。

`.draw()` の第 1 引数で文字の描画サイズを指定できます。各方式について、基本サイズより大きい描画サイズでテキストを描いたときの結果を見てみましょう。

- `font(テキスト).draw(描画サイズ, x, y, color);`
- `font(テキスト).draw(描画サイズ, pos, color);`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 基本サイズ
	const int32 baseSize = 40;

	const Font font{ baseSize, Typeface::Bold };
	const Font fontSDF{ FontMethod::SDF, baseSize, Typeface::Bold };
	const Font fontMSDF{ FontMethod::MSDF, baseSize, Typeface::Bold };
	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		// 文字のサイズ（指定しない場合は基本サイズで描かれる）
		const double fontSize = 120;
		const ColorF color{ 0.1 };

		// ビットマップ方式
		font(text).draw(20, 20, color);
		font(text).draw(fontSize, 20, 50, color);

		// SDF 方式
		fontSDF(text).draw(20, 220, color);
		fontSDF(text).draw(fontSize, 20, 250, color);

		// MSDF 方式
		fontMSDF(text).draw(20, 420, color);
		fontMSDF(text).draw(fontSize, 20, 450, color);
	}
}
```

## 31.11 ベースラインを指定してテキストを描く
文字のベースラインの開始位置を指定して描画したい場合は `.drawBase()` を使います。異なるサイズや種類のフォントを、ベースラインをそろえて描画できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/11.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font1{ 30, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };
	const Font font2{ FontMethod::MSDF, 40, Typeface::Bold };
	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		// ベースラインがそろわない
		font1(text).draw(40, 100);
		font2(text).draw(20, 280, 100);
		font2(text).draw(50, 440, 100);

		Rect{ 0, 400, 800, 10 }.draw(Palette::Skyblue);

		// (40, 400) がベースラインの開始位置になるようテキストを描画
		font1(text).drawBase(40, 400);
		Circle{ 40, 400 , 5 }.drawFrame(2, Palette::Red);

		// (280, 400) がベースラインの開始位置になるようテキストを描画
		font2(text).drawBase(20, 280, 400);
		Circle{ 280, 400 , 5 }.drawFrame(2, Palette::Red);

		// (440, 400) がベースラインの開始位置になるようテキストを描画
		font2(text).drawBase(50, 440, 400);
		Circle{ 440, 400 , 5 }.drawFrame(2, Palette::Red);
	}
}
```

## 31.12 中心座標を指定してテキストを描画する
テキストの左上位置ではなく、中心座標を指定して描画するには、`.drawAt(x, y)` または `.drawAt(pos)` を使います。

- `font(テキスト).drawAt(x, y, color);`
- `font(テキスト).drawAt(pos, color);`
- `font(テキスト).drawAt(描画サイズ, x, y, color);`
- `font(テキスト).drawAt(描画サイズ, pos, color);`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/12.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	const Rect rect{ 100, 300, 160, 80 };

	while (System::Update())
	{
		font(U"C++").drawAt(60, Vec2{ 400, 100 }, ColorF{ 0.11 });

		font(U"Hello, Siv3D!").drawAt(30, Vec2{ 400, 200 }, ColorF{ 0.11 });

		rect.draw();

		// 長方形の中心にテキストを描く
		font(U"Siv3D").drawAt(30, rect.center(), ColorF{ 0.11 });
	}
}
```


## 31.13 描画の基準位置をカスタマイズする
左上、中心以外を基準座標とする場合は、次の表のパターンを使って、`.draw(Arg::bottomRight(x, y))` あるいは `.draw(Arg::bottomRight = pos)` のようにします。この場合、テキストの右下が `x, y` または `pos` で指定した位置になるように描画されます。

| 座標指定 | 説明 |
|---|---|
| `Arg::topLeft` | テキストの左上の位置を指定する（通常の `.draw()` と同じ） |
| `Arg::topCenter` | テキストの上辺の中央を指定する |
| `Arg::topRight` | テキストの右上の位置を指定する |
| `Arg::leftCenter` | テキストの左辺の中央を指定する |
| `Arg::center` | テキストの中心を指定する（通常の `.drawAt()` と同じ） |
| `Arg::rightCenter` | テキストの右辺の中央を指定する |
| `Arg::bottomLeft` | テキストの左下の位置を指定する |
| `Arg::bottomCenter` | テキストの下辺の中央を指定する |
| `Arg::bottomRight` | テキストの右下の位置を指定する |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/13.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	const Rect rect{ 100, 300, 160, 80 };

	while (System::Update())
	{
		font(U"C++").draw(60, Arg::topCenter = Vec2{ 400, 20 }, ColorF{ 0.11 });

		font(U"Hello, Siv3D!").draw(30, Arg::topRight(780, 20), ColorF{ 0.11 });

		rect.draw();

		// 長方形に右揃えでテキストを描く
		font(U"Siv3D").draw(30, Arg::rightCenter = rect.rightCenter(), ColorF{ 0.11 });
	}
}
```

## 31.14 テキストが描画される領域を調べる
`Font` の `.draw()` や `.drawAt()` は、描画された領域を `RectF` 型で返します。`.region()` や `.regionAt()` を使うと、描画を伴わずにその領域を取得できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/14.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"Hello, Siv3D!";
	const Vec2 pos{ 40, 40 };

	// font を使って text を pos の位置に描画したときのテキストの領域を取得
	const RectF rect = font(text).region(pos);

	while (System::Update())
	{
		// 描画領域の長方形を事前に塗りつぶす
		rect.draw(Palette::Skyblue);

		// 長方形の上にテキストを描く
		font(text).draw(pos, ColorF{ 0.25 });

		// テキストの領域を
		font(text)
			.drawAt(80, Scene::Center())
			.stretched(40, 0)	// 横に広げて
			.shearedX(20)		// 平行四辺形にして
			.drawFrame(2);		// 枠を描く
	}
}
```

## 31.15 指定した長方形の中にテキストを描く
`Font::draw()` に、座標の代わりに `Rect` または `RectF` を渡すと、テキストをその長方形の中に収まるように描画します。テキストのすべての文字が長方形内に収まった場合、関数は `true` を返します。一方、テキストがあふれる場合、最後の文字が `…` に置き換えられ、関数は `false` を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/font/15.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const String text = U"The quick brown fox jumps over the lazy dog.";

	const Rect rect1{ 50, 20, 200, 100 };
	const Rect rect2{ 50, 160, 300, 100 };
	const Rect rect3{ 50, 300, 400, 100 };

	while (System::Update())
	{
		rect1.draw();
		if (not font(text).draw(24, rect1.stretched(-10), ColorF{ 0.11 }))
		{
			// 文字が省略されたら赤枠を描く
			rect1.drawFrame(0, 5, Palette::Red);
		}

		rect2.draw();
		if (not font(text).draw(24, rect2.stretched(-10), ColorF{ 0.11 }))
		{
			// 文字が省略されたら赤枠を描く
			rect2.drawFrame(0, 5, Palette::Red);
		}

		rect3.stretched(10).draw();
		if (not font(text).draw(24, rect3.stretched(-10), ColorF{ 0.11 }))
		{
			// 文字が省略されたら赤枠を描く
			rect3.drawFrame(0, 5, Palette::Red);
		}
	}
}
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