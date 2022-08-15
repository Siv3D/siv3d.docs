# 13. その他の機能

## 13.1 乱数
`Random()` は 0.0 以上 1.0 未満のランダムな `double` 型の数を、`Random(max)` は 0 から max までのランダムな数を、`Random<Type>(min, max)` は min から max の範囲でランダムな数を返します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"[0.0, 1.0)", Vec2{ 200, 20 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// 0.0 以上 1.0 未満のランダムな double 型
				Print << Random();
			}
		}

		if (SimpleGUI::Button(U"int32", Vec2{ 200, 60 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// 0～100 の範囲のランダムな整数
				Print << Random(100);
			}
		}

		if (SimpleGUI::Button(U"double", Vec2{ 200, 100 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// -100.0～100.0 の範囲のランダムな浮動小数点数
				Print << Random(-100.0, 100.0);
			}
		}

		if (SimpleGUI::Button(U"char32", Vec2{ 200, 140 }))
		{
			ClearPrint();

			for (int32 i = 0; i < 10; ++i)
			{
				// A～Z の範囲のランダムな文字
				Print << Random(U'A', U'Z');
			}
		}
	}
}
```


## 13.2 テキストファイルから 1 行ずつ読み込む
テキストファイルからテキストを読み込むには `TextReader` クラスの機能を使います。`TextReader` のコンストラクタ引数に、読み込みたいテキストファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は App フォルダ）を基準とする相対パスか、絶対パスを使用します。リリース用のアプリを作るときには、のちの章で説明する「リソース」パスの使用を推奨します。ファイルが存在しない場合など、オープンに失敗したかどうかは `if (not reader)` で調べられます。

オープン直後は、読み込み位置はファイルの先頭にセットされています。`.readLine()` に `String` 型の変数を参照で渡すと、読み込み位置を始点とし、次に見つかった改行またはファイル終端までの 1 行分を読み込んだ内容をその変数に格納し、読み込み位置をそこまで進めて `true` を返します。読み込み位置がすでにファイルの終端にあって、これ以上読み込めないときには `false` を返します。次のプログラムのように `while` ループと組み合わせると便利です。

ファイルはデストラクタでクローズされるので、明示的にクローズする必要はありません。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ファイルをオープンする
	TextReader reader{ U"example/txt/en.txt" };

	// オープンに失敗
	if (not reader)
	{
		// 例外を投げて終了
		throw Error{ U"Failed to open `en.txt`" };
	}

	// 行の内容を読み込む変数
	String line;

	// 行数の表示用のカウント
	size_t i = 0;

	// 終端に達するまで 1 行ずつ読み込む
	while (reader.readLine(line))
	{
		Print << i << U": " << line;
	}

	while (System::Update())
	{

	}

	// reader のデストラクタで自動的にファイルがクローズされる
}
```


## 13.3 ウィンドウのリサイズ
`Window::Resize(int 幅, int 高さ)` または `Window::Resize(Size サイズ)` で、ウィンドウサイズを変更できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1000, 600);

	while (System::Update())
	{

	}
}
```


## 13.4 マウスカーソルのスタイル
マウスカーソルのスタイルを変更したいときは、`Cursor::RequestStyle()` を通して、変更したいカーソルのスタイルをリクエストします。手のアイコンにしたい場合は `CursorStyle::Hand` を、カーソルを非表示にしたい場合は `CursorStyle::Hidden` を指定します。マウスカーソルのリクエストは、そのフレームにのみ適用されるます。変更を維持したい場合は毎フレームにリクエストをし続ける必要があります。

| CursorStyle                  | カーソルの形状   |
|------------------------------|-----------|
| CursorStyle::Arrow           | 矢印（通常）    |
| CursorStyle::IBeam           | I マーク     |
| CursorStyle::Cross           | 十字のマーク    |
| CursorStyle::Hand            | 手のアイコン    |
| CursorStyle::NotAllowed      | 禁止のマーク    |
| CursorStyle::ResizeUpDown    | 上下のリサイズ   |
| CursorStyle::ResizeLeftRight | 左右のリサイズ   |
| CursorStyle::ResizeNWSE      | 左上-右下のリサイズ   |
| CursorStyle::ResizeNESW      | 右上-左下のリサイズ   |
| CursorStyle::ResizeAll       | 上下左右方向のリサイズ   |
| CursorStyle::Hidden          | 非表示       |
| CursorStyle::Default         | Arrow と同じ |


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/mouse/10.gif)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	constexpr ColorF buttonColor{ 0.2, 0.6, 1.0 };
	constexpr Circle button{ 400, 300, 60 };
	Transition press{ 0.05s, 0.05s };

	while (System::Update())
	{
		const bool mouseOver = button.mouseOver();

		// 円の上にマウスカーソルがあれば
		if (mouseOver)
		{
			// マウスカーソルを手の形に
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		press.update(button.leftPressed());

		const double t = press.value();

		button.movedBy(Vec2{ 0, 0 }.lerp(Vec2{ 0, 4 }, t))
			.drawShadow(Vec2{ 0, 6 }.lerp(Vec2{ 0, 1 }, t), (12 - t * 7), (5 - t * 4))
			.draw(buttonColor);
	}
}
```
