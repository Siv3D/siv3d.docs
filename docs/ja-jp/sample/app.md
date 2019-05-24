
# アプリ

## スケッチ

![](images/app-sketch.gif)

```C++
# include <Siv3D.hpp>

void Main()
{
	// キャンバスのサイズ
	constexpr Size canvasSize(600, 600);

	// ペンの太さ
	constexpr int32 thickness = 8;

	// ペンの色
	constexpr Color penColor = Palette::Orange;

	// 書き込み用の画像データを用意
	Image image(canvasSize, Palette::White);

	// 表示用のテクスチャ（内容を更新するので DynamicTexture）
	DynamicTexture texture(image);

	while (System::Update())
	{
		if (MouseL.pressed())
		{
			// 書き込む線の始点は直前のフレームのマウスカーソル座標
			// （初回はタッチ操作時の座標のジャンプを防ぐため、現在のマウスカーソル座標にする）
			const Point from = MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos();

			// 書き込む線の終点は現在のマウスカーソル座標
			const Point to = Cursor::Pos();

			// image に線を書き込む
			Line(from, to).overwrite(image, thickness, penColor);

			// 書き込み終わった image でテクスチャを更新
			texture.fill(image);
		}

		// 描いたものを消去するボタンが押されたら
		if (SimpleGUI::Button(U"Clear", Vec2(640, 40), 120))
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
