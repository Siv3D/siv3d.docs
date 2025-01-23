# 9. 画面と座標

## 9.1 画面
- ウィンドウ内で、背景色を変えられる部分を **画面（シーン）**と呼びます
- Siv3D はこの領域に文字や図形、画像を表示できます
- 画面のサイズは、基本の状態では**幅 800 ピクセル**、**高さ 600 ピクセル**です
- 画面のサイズを「ウィンドウのサイズ」と呼ぶこともあります
	- タイトルバーなど、画面の周囲の枠も含めたウィンドウのサイズは「ウィンドウ領域のサイズ」と呼んで区別します
- 通常の Siv3D プログラミングでは画面のサイズ（ウィンドウサイズ）だけを意識していれば十分です


## 9.2 画面のサイズを変更する
- 画面のサイズは、`Window::Resize(幅, 高さ)` で変更します
- 一度変更するとそのサイズは維持されます。通常はメインループの前の先頭で設定します
- 次のコードは、画面のサイズを幅 1280 ピクセル、高さ 720 ピクセルに変更します

```cpp title="画面のサイズを変更する" hl_lines="5-6"
# include <Siv3D.hpp>

void Main()
{
	// 画面を 1280x720 にリサイズする
	Window::Resize(1280, 720);

	while (System::Update())
	{

	}
}
```


## 9.3 座標
- 図形や画像を画面に表示するときには、画面上のピクセルの位置を表す**座標**を指定します
- 座標は X 座標と Y 座標の 2 つの数値を使って `(X, Y)` で表します
- 画面の最も左上のピクセルが「X 座標：0」「Y 座標：0」で `(0, 0)` です
- 右に進むと X 座標が大きくなり、下に進むと Y 座標が大きくなります
- 画面のサイズが 800 × 600 のとき、画面の最も右下のピクセルの座標は `(799, 599)` です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/circle-rect/1.png)

- 次のコードは、マウスカーソルがどの座標にあるかを、画面の左上に簡易表示します

```cpp title="マウスカーソルの座標を簡易表示する"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// 現在のマウスカーソル座標を表示する
		Print << Cursor::Pos();
	}
}
```


## 9.4 座標を表現するクラス
- Siv3D では、座標を表現するためのクラスとして `Point` と `Vec2` が用意されています
- `Point` は整数で座標を表します

```cpp
struct Point
{
	int32 x;
	int32 y;
};
```

- `Vec2` は浮動小数点数で座標を表します

```cpp
struct Vec2
{
	double x;
	double y;
};
```

- 9.3 で登場した `Cursor::Pos()` は、マウスカーソルの座標を `Point` 型で返す関数です

```cpp title="マウスカーソルの座標を簡易表示する" hl_lines="9 12-13"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		const Point pos = Cursor::Pos();

		// 現在のマウスカーソル座標を X 座標と Y 座標で分けて表示する
		Print << U"X: " << pos.x;
		Print << U"Y: " << pos.y;
	}
}
```


## 9.5 `Point` → `Vec2` の変換
- `Point` → `Vec2` は、自然に（暗黙的に）変換できます

```cpp hl_lines="9"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		const Vec2 pos = Cursor::Pos();

		// 現在のマウスカーソル座標を X 座標と Y 座標で分けて表示する
		Print << U"X: " << pos.x;
		Print << U"Y: " << pos.y;
	}
}
```


## 9.6 `Vec2` → `Point` の変換
- `Vec2` → `Point` の変換は情報が失われるため、`.asPoint()` を使って明示的に変換する必要があります
- 小数点以下の情報が切り捨てられます

```cpp hl_lines="9-10"
# include <Siv3D.hpp>

void Main()
{
	const Vec2 pos1{ 123.4, 567.8 };

	Print << pos1;

	// Vec2 → Point に変換する
	const Point pos2 = pos1.asPoint();

	Print << pos2;

	while (System::Update())
	{

	}
}
```


## 振り返りチェックリスト
- [x] 画面の基本の画面サイズ（800x600）を理解した
- [x] `Window::Resize(幅, 高さ)` で画面のサイズを変更できることを学んだ
- [x] 座標は X 座標と Y 座標の 2 つの数値で表され、画面の左上が `(0, 0)` であることを理解した
- [x] `Point` と `Vec2` は座標を表現するクラスであり、`Point` は整数、`Vec2` は浮動小数点数で座標を表すことを学んだ
- [x] `Point` → `Vec2` は暗黙的に変換できることを学んだ
- [x] `Vec2` → `Point` は、`.asPoint()` を使って明示的に変換することを学んだ
