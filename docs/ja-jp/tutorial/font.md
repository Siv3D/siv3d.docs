
# 8. フォントを使う

この章では、フォントを使って様々なスタイルのテキストを描く方法を学びます。

## 8.1 Font
前章までテキストの表示に使ってきた `Print` は、フォントのサイズや種類、描画位置に自由度がありませんでした。自由にカスタマイズしたフォントを使ってテキストを描きたいときは `Font` を作成し、描画したい内容を `()` でつなげたあと、`.draw()` または `.drawAt()` します。

`Texture` と同じように、`Font` の作成にはメモリ確保などの実行時負荷がかかります。メインループの中で毎フレーム新しい `Font` を作成するのは避け、作成が 1 回だけで済むようにしましょう。

![](images/8010.png)

```C++
# include <Siv3D.hpp>

void Main()
{
	// サイズ 50 のフォントを作成
	const Font font(50);

	while (System::Update())
	{
		// 左上位置 (20, 20) からテキストを描く
		font(U"Hello, Siv3D!").draw(20, 20);

		// テキストの中心座標が画面の中心になるようにテキストを描く
		font(U"C++").drawAt(Scene::Center(), Palette::Skyblue);

        // 文字列以外を渡すと Format される
		font(Cursor::Pos()).draw(50, 300);

        // 複数渡すと、Format でつなげられる
		font(123, U"ABC").draw(50, 400);

		font(U"{}/{}/{}"_fmt(2020, 12, 31)).draw(50, 500);
	}
}
```


## 8.2 改行する
テキストの中に改行文字 `\n` が含まれていると、そこで改行されます。

![](images/8020.png)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Font font(50);

	while (System::Update())
	{
		font(U"Hello,\nSiv3D\n\n!!!").draw(20, 20);
	}
}
```


## 8.3 フォントのサイズ
`Font` のコンストラクタの第一引数にはフォントのサイズを指定します。単位はピクセルです。

![](images/8030.png)

```C++
# include <Siv3D.hpp>

void Main()
{
	// 大きさ 20 のフォント
	const Font font20(20);

	// 大きさ 40 のフォント
	const Font font40(40);

	// 大きさ 60 のフォント
	const Font font60(60);

	// 大きさ 80 のフォント
	const Font font80(80);

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font20(text).draw(20, 20);

		font40(text).draw(20, 60);

		font60(text).draw(20, 120);

		font80(text).draw(20, 200);
	}
}
```


## 8.4 フォントの種類
Siv3D には異なる太さの 7 種類の書体が同梱されています。`Font` のコンストラクタの第二引数において `Typeface::` で太さを指定することで、それらの書体を利用できます。何も指定しなかった場合 `Typeface::Regular` が選択されます。

!!! info
    Siv3D には「M+ 1p」という書体の 7 種類のウエイトのフォントファイルが同梱されています。

![](images/8040.png)

```C++
# include <Siv3D.hpp>

void Main()
{
	// 細いフォント
	const Font fontThin(50, Typeface::Thin);

	// やや細いフォント
	const Font fontLight(50, Typeface::Light);

	// 通常のフォント（デフォルト）
	const Font fontRegular(50, Typeface::Regular);

	// やや太いフォント
	const Font fontMedium(50, Typeface::Medium);

	// 太いフォント
	const Font fontBold(50, Typeface::Bold);

	// とても太いフォント
	const Font fontHeavy(50, Typeface::Heavy);

	// 非常に太いフォント
	const Font fontBlack(50, Typeface::Black);

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		fontThin(text).draw(20, 20);
		fontLight(text).draw(20, 70);
		fontRegular(text).draw(20, 120);
		fontMedium(text).draw(20, 170);
		fontBold(text).draw(20, 220);
		fontHeavy(text).draw(20, 270);
		fontBlack(text).draw(20, 320);
	}
}
```


## 8.5 フォントファイルからフォントを読み込んで使う
PC 上にあるフォントファイルから `Font` を作成するには、`Font` のコンストラクタの第二引数に、読み込みたいフォントファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。リリース用のアプリを作るときには、のちの章で説明する「リソース」パスの使用を推奨します。

![](images/8050.png)

```C++
# include <Siv3D.hpp>

void Main()
{
	// Pecita.otf をロードして使う
	const Font font1(50, U"example/font/Pecita/Pecita.otf");
	
	// toroman.ttf をロードして使う
	const Font font2(50, U"example/font/toroman/toroman.ttf");

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font1(text).draw(20, 20);

		font2(text).draw(20, 70);
	}
}
```


## 8.6 PC にインストールされているフォントを使う
PC にインストールされているフォントは OS ごとに特殊なフォルダに保存されています。そのフォルダのパスを `FileSystem::SpecialFolderPath()` で取得し、フォントファイル名とつなげることで、ファイルパスを構築できます。`FileSystem::SpecialFolderPath()` に渡す `SpecialFolder` の種類と OS によって取得できるパスの対応表は次の通りです。macOS のみ 3 つの戻り値が異なります。

|                            | Windows             | macOS                  | Linux       |
|----------------------------|---------------------|------------------------|-------------|
| SpecialFolder::SystemFonts | (OS):/WINDOWS/Fonts/ | /System/Library/Fonts/ | (Documents) |
| SpecialFolder::LocalFonts  | (OS):/WINDOWS/Fonts/ | /Library/Fonts/        | (Documents) |
| SpecialFolder::UserFonts   | (OS):/WINDOWS/Fonts/ | ~/Library/Fonts/       | (Documents) |

![](images/8060.png)

```C++
# include <Siv3D.hpp>

void Main()
{
# if SIV3D_PLATFORM(WINDOWS)

	const Font font(60, FileSystem::SpecialFolderPath(SpecialFolder::SystemFonts) + U"arial.ttf");

# elif SIV3D_PLATFORM(MACOS)

	const Font font(60, FileSystem::SpecialFolderPath(SpecialFolder::SystemFonts) + U"Helvetica.dfont");

# endif

	while (System::Update())
	{
	# if SIV3D_PLATFORM(WINDOWS)

		font(U"Arial").draw(20, 40);

	# elif SIV3D_PLATFORM(MACOS)

		font(U"Helvetica").draw(20, 40);

	# endif
	}
}
```

!!! info
    `SIV3D_PLATFORM(WINDOWS)` や `SIV3D_PLATFORM(MACOS)` は Siv3D でプラットフォーム別のコードを書くときに使えるマクロです。


## 8.7 テキストが表示される領域を調べる
`Font` の `.draw()` や `.drawAt()` は、描画された領域を `Rect` または `RectF` 型で返します。また、`.region()` や `.regionAt()` を使うと、描画することなくその領域を取得できます。

![](images/8070.png)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Font font(50);

	const String text = U"Hello, Siv3D!";

	constexpr Point pos(20, 20);

	// font を使って text を pos の位置に描画したときのテキストの領域を取得
	const Rect rect = font(text).region(pos);

	while (System::Update())
	{
		// 描画領域の長方形を事前に塗りつぶす
		rect.draw(Palette::Skyblue);

		// 長方形の上にテキストを描く
		font(text).draw(pos, ColorF(0.25));
	}
}
```


## 8.8 フォントのスタイルを変える

![](images/8080.png)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Font font(50, Typeface::Default);

	// ボールド
	const Font fontBold(50, Typeface::Default, FontStyle::Bold);

	// イタリック
	const Font fontItalic(50, Typeface::Default, FontStyle::Italic);

	// ボールド・イタリック
	const Font fontBoldItalic(50, Typeface::Default, FontStyle::BoldItalic);

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		font(text).draw(20, 20);

		fontBold(text).draw(20, 70);

		fontItalic(text).draw(20, 120);

		fontBoldItalic(text).draw(20, 170);
	}
}
```


## 8.9 異なるフォントでベースラインをそろえる

![](images/8090.png)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Font font20(20);

	const Font font40(40);

	const Font font60(60);

	const String text = U"Hello, Siv3D!";

	while (System::Update())
	{
		// ベースラインがそろわない
		font20(text).draw(20, 100);
		font40(text).draw(160, 100);
		font60(text).draw(420, 100);

		Rect(0, 400, 800, 200).draw(ColorF(0.4));

		// (20, 100) がベースラインの開始位置になるようテキストを描画
		font20(text).drawBase(20, 400);

		// (160, 100) がベースラインの開始位置になるようテキストを描画
		font40(text).drawBase(160, 400);

		// (420, 100) がベースラインの開始位置になるようテキストを描画
		font60(text).drawBase(420, 400);
	}
}
```


## 8.10 テキスト描画の基準位置をカスタマイズする

![](images/8100.png)

```C++

```


## 8.11 文字に影を付ける

![](images/8110.png)

```C++

```


## 8.12 文字単位で描画を制御する

![](images/8120.png)

```C++

```


## 8.13 縦書きでテキストを描画する

![](images/8140.png)

```C++

```


## 8.14 フォントあれこれ
フォントのサイズを動的に変更することはできません。ただし、のちの章で登場する `Transformer2D` を使ってフォントを描画することで、見た目の大きさを変更できます。


