# 68. AI 絵しりとり
OpenAI の Vision API を活用して、描いた絵でしりとりをするゲームを作ります。描いたイラストを AI が判定します。


## 68.1 ゲームのルール
- 指定されたアルファベット（例: A）から始まる言葉を考え、絵を描きます
- 描いた絵を AI が理解できれば OK, その言葉の最後の文字を使って次の言葉を考えます

### 完成イメージ（クリックで再生）

<blockquote class="twitter-tweet" data-media-max-width="560"><p lang="ja" dir="ltr">今日の <a href="https://twitter.com/hashtag/cppmix?src=hash&amp;ref_src=twsrc%5Etfw">#cppmix</a> で発表した AI 絵しりとり！<br>描いた絵を AI が判定して、AI がわからなかったらゲームオーバー。<a href="https://twitter.com/hashtag/Siv3D?src=hash&amp;ref_src=twsrc%5Etfw">#Siv3D</a> <a href="https://t.co/3IGEbZj9A4">pic.twitter.com/3IGEbZj9A4</a></p>&mdash; Ryo Suzuki (@Reputeless) <a href="https://twitter.com/Reputeless/status/1801640858263163192?ref_src=twsrc%5Etfw">June 14, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


## 68.2 画面のサイズと背景
- 画面のサイズと背景色を設定します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/2.png)

??? note "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		while (System::Update())
		{

		}
	}
	```

## 68.3 背景の市松模様
- 正方形を並べて、背景の市松模様を描画します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/3.png)

??? note "コード"
	```cpp hl_lines="3-20 32-33"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// 縦横のマス目の数
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// (x + y) が偶数のときだけ正方形を描く
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		while (System::Update())
		{
			// 背景の市松模様を描く
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });
		}
	}
	```

## 68.4 ペイント用画像
- ペイントのために、プログラムで編集可能な画像データ `Image` を用意します
- その画像をシーンに描画するための `DynamicTexture` を用意します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/4.png)

??? note "コード"
	```cpp hl_lines="22-25 35-42 49-50"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// 縦横のマス目の数
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// (x + y) が偶数のときだけ正方形を描く
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture)
	{
		texture.draw();
	}

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// キャンバスのサイズ
		const Size canvasSize{ 512, 512 };

		// ペイント用の画像
		Image image{ canvasSize, Palette::White };

		// ペイント用の画像からテクスチャを作成する
		DynamicTexture texture{ image };

		while (System::Update())
		{
			// 背景の市松模様を描く
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// キャンバスを描く
			DrawCanvas(texture);
		}
	}
	```


## 68.5 キャンバス
- `RoundRect` クラスを使って角丸長方形を用意し、それに沿ってペイントされたテクスチャを描画します
- `RoundRect` の `.drawFrame(内側方向の太さ, 外側方向の太さ, 色)` を使ってキャンバスの枠を描画します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/5.png)

??? note "コード"
	```cpp hl_lines="22 24-31 42-43 60"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// 縦横のマス目の数
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// (x + y) が偶数のときだけ正方形を描く
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// 角丸長方形
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// 角丸長方形に沿ってペイント結果を描く
		rrect(texture).draw();

		// 角丸長方形の枠を描く
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// キャンバスの左上の位置
		const Point canvasPos{ 100, 60 };

		// キャンバスのサイズ
		const Size canvasSize{ 512, 512 };

		// ペイント用の画像
		Image image{ canvasSize, Palette::White };

		// ペイント用の画像からテクスチャを作成する
		DynamicTexture texture{ image };

		while (System::Update())
		{
			// 背景の市松模様を描く
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// キャンバスを描く
			DrawCanvas(texture, canvasPos);
		}
	}
	```


## 68.6 ペイント
- マウスの左ボタンが押されている間、画像に線を書き込みます
- `Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);` で線を画像に書き込みます
- `.movedBy(-canvasPos)` は、キャンバスの位置と実際の画像上の位置を合わせるための処理です
- `texture.fill(image);` で `DynamicTexture` の内容を新しい画像に更新します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/6.png)

??? note "コード"
	```cpp hl_lines="34-45 69-70"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// 縦横のマス目の数
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// (x + y) が偶数のときだけ正方形を描く
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// 角丸長方形
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// 角丸長方形に沿ってペイント結果を描く
		rrect(texture).draw();

		// 角丸長方形の枠を描く
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// テクスチャの内容を更新する
			texture.fill(image);
		}
	}

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// キャンバスの左上の位置
		const Point canvasPos{ 100, 60 };

		// キャンバスのサイズ
		const Size canvasSize{ 512, 512 };

		// ペイント用の画像
		Image image{ canvasSize, Palette::White };

		// ペイント用の画像からテクスチャを作成する
		DynamicTexture texture{ image };

		while (System::Update())
		{
			// ペイントを行う
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// 背景の市松模様を描く
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// キャンバスを描く
			DrawCanvas(texture, canvasPos);
		}
	}
	```


## 68.7 キャンバスのクリア
- SimpleGUI を使って、画像のクリアボタンを作成します
- クリアボタンが押されたら、キャンバスをクリアします
- `image.fill(color);` で画像を指定した色で塗りつぶします

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/7.png)

??? note "コード"
	```cpp hl_lines="47-53 83-88"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// 縦横のマス目の数
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// (x + y) が偶数のときだけ正方形を描く
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// 角丸長方形
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// 角丸長方形に沿ってペイント結果を描く
		rrect(texture).draw();

		// 角丸長方形の枠を描く
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// テクスチャの内容を更新する
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// テクスチャの内容を更新する
		texture.fill(image);
	}

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// キャンバスの左上の位置
		const Point canvasPos{ 100, 60 };

		// キャンバスのサイズ
		const Size canvasSize{ 512, 512 };

		// ペイント用の画像
		Image image{ canvasSize, Palette::White };

		// ペイント用の画像からテクスチャを作成する
		DynamicTexture texture{ image };

		while (System::Update())
		{
			// ペイントを行う
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// 背景の市松模様を描く
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// クリアボタンが押されたら
			if (SimpleGUI::Button(U"クリア", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// キャンバスをクリアする
				ClearCanvas(image, texture, Palette::White);
			}

			// キャンバスを描く
			DrawCanvas(texture, canvasPos);
		}
	}
	```


## 68.8 お題の文字
- お題の文字を表す変数 `targetChar` を用意します
- 文字を描画するために使うフォントを用意します
- `font(文字またはテキスト).drawAt(サイズ、中心位置, 色);` を使って文字を描画します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/8.png)

??? note "コード"
	```cpp hl_lines="34-46 77-78 92-93 113-114"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// 縦横のマス目の数
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// (x + y) が偶数のときだけ正方形を描く
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// 角丸長方形
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// 角丸長方形に沿ってペイント結果を描く
		rrect(texture).draw();

		// 角丸長方形の枠を描く
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// お題表示用の円
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// 円を描く
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// お題の文字を描く
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// テクスチャの内容を更新する
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// テクスチャの内容を更新する
		texture.fill(image);
	}

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// フォントを用意する
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

		// キャンバスの左上の位置
		const Point canvasPos{ 100, 60 };

		// キャンバスのサイズ
		const Size canvasSize{ 512, 512 };

		// ペイント用の画像
		Image image{ canvasSize, Palette::White };

		// ペイント用の画像からテクスチャを作成する
		DynamicTexture texture{ image };

		// お題の文字
		char32 targetChar = U'C';

		while (System::Update())
		{
			// ペイントを行う
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// 背景の市松模様を描く
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// クリアボタンが押されたら
			if (SimpleGUI::Button(U"クリア", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// キャンバスをクリアする
				ClearCanvas(image, texture, Palette::White);
			}

			// キャンバスを描く
			DrawCanvas(texture, canvasPos);

			// お題の文字を描く
			DrawTargetCharacter(targetChar, canvasPos, font);
		}
	}
	```


## 68.9 通信の準備
- OpenAI のサーバーと非同期で通信するための `AsyncHTTPTask` クラスを用意します
- 絵を AI に判定してもらうための「判定」ボタンを配置します
- 判定ボタンは、通信中には押せないようにします

??? note "コード"
	```cpp hl_lines="95-96 106-111"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// 縦横のマス目の数
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// (x + y) が偶数のときだけ正方形を描く
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// 角丸長方形
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// 角丸長方形に沿ってペイント結果を描く
		rrect(texture).draw();

		// 角丸長方形の枠を描く
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// お題表示用の円
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// 円を描く
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// お題の文字を描く
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// テクスチャの内容を更新する
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// テクスチャの内容を更新する
		texture.fill(image);
	}

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// フォントを用意する
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

		// キャンバスの左上の位置
		const Point canvasPos{ 100, 60 };

		// キャンバスのサイズ
		const Size canvasSize{ 512, 512 };

		// ペイント用の画像
		Image image{ canvasSize, Palette::White };

		// ペイント用の画像からテクスチャを作成する
		DynamicTexture texture{ image };

		// お題の文字
		char32 targetChar = U'C';

		// 非同期タスク
		AsyncHTTPTask task;

		while (System::Update())
		{
			// ペイントを行う
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// 背景の市松模様を描く
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// 送信ボタンが押されたら
			if (SimpleGUI::Button(U"判定", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // 判定結果待機中のとき以外、ボタンを有効にする
			{

			}

			// クリアボタンが押されたら
			if (SimpleGUI::Button(U"クリア", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// キャンバスをクリアする
				ClearCanvas(image, texture, Palette::White);
			}

			// キャンバスを描く
			DrawCanvas(texture, canvasPos);

			// お題の文字を描く
			DrawTargetCharacter(targetChar, canvasPos, font);
		}
	}
	```


## 68.10 リクエストの作成
- OpenAI の Vision API に送るリクエスト `OpenAI::Vision::Request` を作成します
- 配列 `.images` に画像を追加します
- `.prompt` に、画像についての質問文を設定します

```txt title="プロンプト日本語訳"
画像に描かれているものは何ですか？ 答えは文字「{}」から始まります。
答えだけを出力してください。コンマやピリオドの使用は禁止です。わからない場合はクエスチョンマークだけを出力してください。
```

??? note "コード"
	```cpp hl_lines="110-123"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// 縦横のマス目の数
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// (x + y) が偶数のときだけ正方形を描く
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// 角丸長方形
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// 角丸長方形に沿ってペイント結果を描く
		rrect(texture).draw();

		// 角丸長方形の枠を描く
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// お題表示用の円
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// 円を描く
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// お題の文字を描く
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// テクスチャの内容を更新する
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// テクスチャの内容を更新する
		texture.fill(image);
	}

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// フォントを用意する
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

		// キャンバスの左上の位置
		const Point canvasPos{ 100, 60 };

		// キャンバスのサイズ
		const Size canvasSize{ 512, 512 };

		// ペイント用の画像
		Image image{ canvasSize, Palette::White };

		// ペイント用の画像からテクスチャを作成する
		DynamicTexture texture{ image };

		// お題の文字
		char32 targetChar = U'C';

		// 非同期タスク
		AsyncHTTPTask task;

		while (System::Update())
		{
			// ペイントを行う
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// 背景の市松模様を描く
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// 送信ボタンが押されたら
			if (SimpleGUI::Button(U"判定", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // 判定結果待機中のとき以外、ボタンを有効にする
			{
				// プロンプト
				String prompt = U"What is drawn in the image? The answer starts with the letter {}. "_fmt(targetChar);
				prompt += U"Write only the answer. Commas and periods are prohibited. If you don't know, output only a question mark.";

				// リクエスト
				OpenAI::Vision::Request request;

				// リクエストにプロンプトを設定
				request.questions = prompt;

				// リクエストに画像を添付
				request.images << OpenAI::Vision::ImageData::Base64FromImage(image);


			}

			// クリアボタンが押されたら
			if (SimpleGUI::Button(U"クリア", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// キャンバスをクリアする
				ClearCanvas(image, texture, Palette::White);
			}

			// キャンバスを描く
			DrawCanvas(texture, canvasPos);

			// お題の文字を描く
			DrawTargetCharacter(targetChar, canvasPos, font);
		}
	}
	```


## 68.11 AI とのやり取り
- `OpenAI::Vision::CompleteAsync` で非同期タスクを作成します
- タスクが正常に完了したら、結果を大文字にして取得します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/11.png)

??? note "コード"
	```cpp hl_lines="77-78 126-127 137-145"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// 縦横のマス目の数
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// (x + y) が偶数のときだけ正方形を描く
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// 角丸長方形
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// 角丸長方形に沿ってペイント結果を描く
		rrect(texture).draw();

		// 角丸長方形の枠を描く
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// お題表示用の円
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// 円を描く
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// お題の文字を描く
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// テクスチャの内容を更新する
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// テクスチャの内容を更新する
		texture.fill(image);
	}

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// OpenAI API キー
		const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

		// フォントを用意する
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

		// キャンバスの左上の位置
		const Point canvasPos{ 100, 60 };

		// キャンバスのサイズ
		const Size canvasSize{ 512, 512 };

		// ペイント用の画像
		Image image{ canvasSize, Palette::White };

		// ペイント用の画像からテクスチャを作成する
		DynamicTexture texture{ image };

		// お題の文字
		char32 targetChar = U'C';

		// 非同期タスク
		AsyncHTTPTask task;

		while (System::Update())
		{
			// ペイントを行う
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// 背景の市松模様を描く
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// 送信ボタンが押されたら
			if (SimpleGUI::Button(U"判定", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // 判定結果待機中のとき以外、ボタンを有効にする
			{
				// プロンプト
				String prompt = U"What is drawn in this image? The answer starts with the letter {}. "_fmt(targetChar);
				prompt += U"Write only the answer. Commas and periods are prohibited. If you don't know, output only a question mark.";

				// リクエスト
				OpenAI::Vision::Request request;

				// リクエストにプロンプトを設定
				request.questions = prompt;

				// リクエストに画像を添付
				request.images << OpenAI::Vision::ImageData::Base64FromImage(image);

				// タスクを作成する
				task = OpenAI::Vision::CompleteAsync(API_KEY, request);
			}

			// クリアボタンが押されたら
			if (SimpleGUI::Button(U"クリア", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// キャンバスをクリアする
				ClearCanvas(image, texture, Palette::White);
			}

			// 非同期処理が完了し、正常なレスポンスである場合
			if (task.isReady() && task.getResponse().isOK())
			{
				// 結果を取得する
				const String answer = OpenAI::Vision::GetContent(task.getAsJSON()).uppercase();

				// 結果を簡易表示する
				Print << answer;
			}

			// キャンバスを描く
			DrawCanvas(texture, canvasPos);

			// お題の文字を描く
			DrawTargetCharacter(targetChar, canvasPos, font);
		}
	}
	```


## 68.12 ゲームの進行
- 直近のしりとりの履歴を記録し、表示するようにします
- 正解したら、次の文字に進むようにします

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/12.png)

??? note "コード"
	```cpp hl_lines="48-54 109-110 154-171 180-181"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// 縦横のマス目の数
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// (x + y) が偶数のときだけ正方形を描く
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// 角丸長方形
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// 角丸長方形に沿ってペイント結果を描く
		rrect(texture).draw();

		// 角丸長方形の枠を描く
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// お題表示用の円
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// 円を描く
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// お題の文字を描く
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void DrawRecentHistory(const Array<String>& recentWords, const Font& font)
	{
		for (const auto& [i, answer] : Indexed(recentWords))
		{
			font(answer).draw(46, Vec2{ 736, (47 + i * 80) }, ColorF{ 0.1 });
		}
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// テクスチャの内容を更新する
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// テクスチャの内容を更新する
		texture.fill(image);
	}

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// OpenAI API キー
		const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

		// フォントを用意する
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

		// キャンバスの左上の位置
		const Point canvasPos{ 100, 60 };

		// キャンバスのサイズ
		const Size canvasSize{ 512, 512 };

		// ペイント用の画像
		Image image{ canvasSize, Palette::White };

		// ペイント用の画像からテクスチャを作成する
		DynamicTexture texture{ image };

		// お題の文字
		char32 targetChar = U'C';

		// 非同期タスク
		AsyncHTTPTask task;

		// 直近のしりとりの履歴を格納する配列
		Array<String> recentWords = { String(1, targetChar) };

		while (System::Update())
		{
			// ペイントを行う
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// 背景の市松模様を描く
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// 送信ボタンが押されたら
			if (SimpleGUI::Button(U"判定", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // 判定結果待機中のとき以外、ボタンを有効にする
			{
				// プロンプト
				String prompt = U"What is drawn in this image? The answer starts with the letter {}. "_fmt(targetChar);
				prompt += U"Write only the answer. Commas and periods are prohibited. If you don't know, output only a question mark.";

				// リクエスト
				OpenAI::Vision::Request request;

				// リクエストにプロンプトを設定
				request.questions = prompt;

				// リクエストに画像を添付
				request.images << OpenAI::Vision::ImageData::Base64FromImage(image);

				// タスクを作成する
				task = OpenAI::Vision::CompleteAsync(API_KEY, request);
			}

			// クリアボタンが押されたら
			if (SimpleGUI::Button(U"クリア", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// キャンバスをクリアする
				ClearCanvas(image, texture, Palette::White);
			}

			// 非同期処理が完了し、正常なレスポンスである場合
			if (task.isReady() && task.getResponse().isOK())
			{
				// 結果を取得する
				const String answer = OpenAI::Vision::GetContent(task.getAsJSON()).uppercase();

				// 履歴の末尾を更新する
				recentWords.back() = answer;

				// 正解したら
				if (answer != U"?")
				{
					targetChar = answer.back();
				}

				// 履歴に次の項目を追加する
				recentWords << String(1, targetChar);

				// 履歴の内容が 8 個より大きくなったら
				if (8 < recentWords.size())
				{
					// 先頭の項目を削除する
					recentWords.pop_front();
				}
			}

			// キャンバスを描く
			DrawCanvas(texture, canvasPos);

			// お題の文字を描く
			DrawTargetCharacter(targetChar, canvasPos, font);

			// しりとりの履歴を描く
			DrawRecentHistory(recentWords, font);
		}
	}
	```


## 68.13 履歴表示の改善
- 先頭の文字を強調表示します
- 履歴が満杯のとき、最初の項目は画面からはみ出るようにします

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/13.png)

??? note "コード"
	```cpp hl_lines="50-51 55-61"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// 縦横のマス目の数
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// (x + y) が偶数のときだけ正方形を描く
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// 角丸長方形
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// 角丸長方形に沿ってペイント結果を描く
		rrect(texture).draw();

		// 角丸長方形の枠を描く
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// お題表示用の円
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// 円を描く
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// お題の文字を描く
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void DrawRecentHistory(const Array<String>& recentWords, const Font& font)
	{
		// 履歴が満杯のときのあふれ処理
		const double yOffset = (recentWords.size() < 8) ? 0 : -70;

		for (const auto& [i, answer] : Indexed(recentWords))
		{
			// 1 文字目
			const Vec2 pos{ 700, (80 + i * 80 + yOffset) };
			Circle{ pos, 32 }.draw(ColorF{ 0.8, 0.9, 1.0 });
			font(answer.front()).drawAt(46, pos, ColorF{ 0.1 });

			// 1 文字目以降
			font(answer.substr(1)).draw(46, Vec2{ 736, (47 + i * 80 + yOffset) }, ColorF{ 0.1 });
		}
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// テクスチャの内容を更新する
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// テクスチャの内容を更新する
		texture.fill(image);
	}

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// OpenAI API キー
		const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

		// フォントを用意する
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };

		// キャンバスの左上の位置
		const Point canvasPos{ 100, 60 };

		// キャンバスのサイズ
		const Size canvasSize{ 512, 512 };

		// ペイント用の画像
		Image image{ canvasSize, Palette::White };

		// ペイント用の画像からテクスチャを作成する
		DynamicTexture texture{ image };

		// お題の文字
		char32 targetChar = U'C';

		// 非同期タスク
		AsyncHTTPTask task;

		// 直近のしりとりの履歴を格納する配列
		Array<String> recentWords = { String(1, targetChar) };

		while (System::Update())
		{
			// ペイントを行う
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// 背景の市松模様を描く
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// 送信ボタンが押されたら
			if (SimpleGUI::Button(U"判定", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // 判定結果待機中のとき以外、ボタンを有効にする
			{
				// プロンプト
				String prompt = U"What is drawn in this image? The answer starts with the letter {}. "_fmt(targetChar);
				prompt += U"Write only the answer. Commas and periods are prohibited. If you don't know, output only a question mark.";

				// リクエスト
				OpenAI::Vision::Request request;

				// リクエストにプロンプトを設定
				request.questions = prompt;

				// リクエストに画像を添付
				request.images << OpenAI::Vision::ImageData::Base64FromImage(image);

				// タスクを作成する
				task = OpenAI::Vision::CompleteAsync(API_KEY, request);
			}

			// クリアボタンが押されたら
			if (SimpleGUI::Button(U"クリア", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// キャンバスをクリアする
				ClearCanvas(image, texture, Palette::White);
			}

			// 非同期処理が完了し、正常なレスポンスである場合
			if (task.isReady() && task.getResponse().isOK())
			{
				// 結果を取得する
				const String answer = OpenAI::Vision::GetContent(task.getAsJSON()).uppercase();

				// 履歴の末尾を更新する
				recentWords.back() = answer;

				// 正解したら
				if (answer != U"?")
				{
					targetChar = answer.back();
				}

				// 履歴に次の項目を追加する
				recentWords << String(1, targetChar);

				// 履歴の内容が 8 個より大きくなったら
				if (8 < recentWords.size())
				{
					// 先頭の項目を削除する
					recentWords.pop_front();
				}
			}

			// キャンバスを描く
			DrawCanvas(texture, canvasPos);

			// お題の文字を描く
			DrawTargetCharacter(targetChar, canvasPos, font);

			// しりとりの履歴を描く
			DrawRecentHistory(recentWords, font);
		}
	}
	```


## 68.14 スコア
- スコアを記録し、表示します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/14.png)

??? note "コード"
	```cpp hl_lines="65-70 106 129-130 181 204-205"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// 縦横のマス目の数
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// (x + y) が偶数のときだけ正方形を描く
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// 角丸長方形
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// 角丸長方形に沿ってペイント結果を描く
		rrect(texture).draw();

		// 角丸長方形の枠を描く
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// お題表示用の円
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// 円を描く
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// お題の文字を描く
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void DrawRecentHistory(const Array<String>& recentWords, const Font& font)
	{
		// 履歴が満杯のときのあふれ処理
		const double yOffset = (recentWords.size() < 8) ? 0 : -70;

		for (const auto& [i, answer] : Indexed(recentWords))
		{
			// 1 文字目
			const Vec2 pos{ 700, (80 + i * 80 + yOffset) };
			Circle{ pos, 32 }.draw(ColorF{ 0.8, 0.9, 1.0 });
			font(answer.front()).drawAt(46, pos, ColorF{ 0.1 });

			// 1 文字目以降
			font(answer.substr(1)).draw(46, Vec2{ 736, (47 + i * 80 + yOffset) }, ColorF{ 0.1 });
		}
	}

	void DrawScore(int32 score, const Font& font)
	{
		const Vec2 center = font(score).region(140, Arg::topRight(1185, 15)).center();
		font(score).draw(TextStyle::OutlineShadow(0.2, ColorF{ 1.0 }, Vec2{ 2, 2 }, ColorF{ 0.0, 0.5 }), 140,
			Arg::topRight(1185, 15), ColorF{ 1.0, 0.6, 0.1 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// テクスチャの内容を更新する
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// テクスチャの内容を更新する
		texture.fill(image);
	}

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// OpenAI API キー
		const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

		// フォントを用意する
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };
		const Font font2 = Font{ FontMethod::MSDF, 40, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(4);

		// キャンバスの左上の位置
		const Point canvasPos{ 100, 60 };

		// キャンバスのサイズ
		const Size canvasSize{ 512, 512 };

		// ペイント用の画像
		Image image{ canvasSize, Palette::White };

		// ペイント用の画像からテクスチャを作成する
		DynamicTexture texture{ image };

		// お題の文字
		char32 targetChar = U'C';

		// 非同期タスク
		AsyncHTTPTask task;

		// 直近のしりとりの履歴を格納する配列
		Array<String> recentWords = { String(1, targetChar) };

		// スコア
		int32 score = 0;

		while (System::Update())
		{
			// ペイントを行う
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// 背景の市松模様を描く
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// 送信ボタンが押されたら
			if (SimpleGUI::Button(U"判定", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // 判定結果待機中のとき以外、ボタンを有効にする
			{
				// プロンプト
				String prompt = U"What is drawn in this image? The answer starts with the letter {}. "_fmt(targetChar);
				prompt += U"Write only the answer. Commas and periods are prohibited. If you don't know, output only a question mark.";

				// リクエスト
				OpenAI::Vision::Request request;

				// リクエストにプロンプトを設定
				request.questions = prompt;

				// リクエストに画像を添付
				request.images << OpenAI::Vision::ImageData::Base64FromImage(image);

				// タスクを作成する
				task = OpenAI::Vision::CompleteAsync(API_KEY, request);
			}

			// クリアボタンが押されたら
			if (SimpleGUI::Button(U"クリア", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// キャンバスをクリアする
				ClearCanvas(image, texture, Palette::White);
			}

			// 非同期処理が完了し、正常なレスポンスである場合
			if (task.isReady() && task.getResponse().isOK())
			{
				// 結果を取得する
				const String answer = OpenAI::Vision::GetContent(task.getAsJSON()).uppercase();

				// 履歴の末尾を更新する
				recentWords.back() = answer;

				// 正解したら
				if (answer != U"?")
				{
					targetChar = answer.back();
					++score;
				}

				// 履歴に次の項目を追加する
				recentWords << String(1, targetChar);

				// 履歴の内容が 8 個より大きくなったら
				if (8 < recentWords.size())
				{
					// 先頭の項目を削除する
					recentWords.pop_front();
				}
			}

			// キャンバスを描く
			DrawCanvas(texture, canvasPos);

			// お題の文字を描く
			DrawTargetCharacter(targetChar, canvasPos, font);

			// しりとりの履歴を描く
			DrawRecentHistory(recentWords, font);

			// スコアを描く
			DrawScore(score, font2);
		}
	}
	```


## 68.15 細かい改善
- 最初のお題の文字をランダムに選ぶようにします
- AI からのレスポンスの待機中に、回転するリングを表示します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/shiritori/15.png)

??? note "コード"
	```cpp hl_lines="48 63-71 131 212"
	# include <Siv3D.hpp>

	void DrawCheckerboard(int32 size, const ColorF& color)
	{
		// 縦横のマス目の数
		const int32 yCount = (720 / size + 1);
		const int32 xCount = (1280 / size + 1);

		for (int32 y = 0; y < yCount; ++y)
		{
			for (int32 x = 0; x < xCount; ++x)
			{
				// (x + y) が偶数のときだけ正方形を描く
				if (IsEven(x + y))
				{
					Rect{ (x * size), (y * size), size }.draw(color);
				}
			}
		}
	}

	void DrawCanvas(const Texture& texture, const Point& canvasPos)
	{
		// 角丸長方形
		const RoundRect rrect{ canvasPos, texture.size(), 20 };

		// 角丸長方形に沿ってペイント結果を描く
		rrect(texture).draw();

		// 角丸長方形の枠を描く
		rrect.drawFrame(1, 15, ColorF{ 0.6, 0.4, 0.2 });
	}

	void DrawTargetCharacter(char32 targetChar, const Point& canvasPos, const Font& font)
	{
		// お題表示用の円
		const Circle circle{ canvasPos.movedBy(30, 30), 70 };

		// 円を描く
		circle.drawShadow(Vec2{ 2, 2 }, 12, 2, ColorF{ 0.2, 0.4, 0.3, 0.5 })
			.draw(ColorF{ 0.8, 0.9, 1.0 })
			.stretched(-1.5).drawFrame(1, ColorF{ 1.0 });

		// お題の文字を描く
		font(targetChar).drawAt(70, circle.center, ColorF{ 0.1 });
	}

	void DrawRecentHistory(const Array<String>& recentWords, const Font& font, bool isWaiting)
	{
		// 履歴が満杯のときのあふれ処理
		const double yOffset = (recentWords.size() < 8) ? 0 : -70;

		for (const auto& [i, answer] : Indexed(recentWords))
		{
			// 1 文字目
			const Vec2 pos{ 700, (80 + i * 80 + yOffset) };
			Circle{ pos, 32 }.draw(ColorF{ 0.8, 0.9, 1.0 });
			font(answer.front()).drawAt(46, pos, ColorF{ 0.1 });

			// 1 文字目以降
			font(answer.substr(1)).draw(46, Vec2{ 736, (47 + i * 80 + yOffset) }, ColorF{ 0.1 });

			// レスポンス待機中のとき
			if (isWaiting)
			{
				// 最後の文字の周りに回転するリングを描く
				if (i == recentWords.size() - 1)
				{
					Circle{ pos, 42 }.drawArc((Scene::Time() * 240_deg), 300_deg, 5, 2, ColorF{ 0.8, 0.9, 1.0 });
				}
			}
		}
	}

	void DrawScore(int32 score, const Font& font)
	{
		const Vec2 center = font(score).region(140, Arg::topRight(1185, 15)).center();
		font(score).draw(TextStyle::OutlineShadow(0.2, ColorF{ 1.0 }, Vec2{ 2, 2 }, ColorF{ 0.0, 0.5 }), 140,
			Arg::topRight(1185, 15), ColorF{ 1.0, 0.6, 0.1 });
	}

	void PaintCanvas(Image& image, const Point& canvasPos, DynamicTexture& texture, int32 thickness, const ColorF& color)
	{
		if (MouseL.pressed())
		{
			const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
			const Point to = Cursor::Pos();
			Line{ from, to }.movedBy(-canvasPos).overwrite(image, thickness, color);

			// テクスチャの内容を更新する
			texture.fill(image);
		}
	}

	void ClearCanvas(Image& image, DynamicTexture& texture, const Color& color)
	{
		image.fill(color);

		// テクスチャの内容を更新する
		texture.fill(image);
	}

	void Main()
	{
		// ウィンドウを 1280x720 にリサイズする
		Window::Resize(1280, 720);

		// 背景色を設定する
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// OpenAI API キー
		const String API_KEY = EnvironmentVariable::Get(U"MY_OPENAI_API_KEY");

		// フォントを用意する
		const Font font{ FontMethod::MSDF, 40, Typeface::Heavy };
		const Font font2 = Font{ FontMethod::MSDF, 40, Typeface::Heavy, FontStyle::Italic }.setBufferThickness(4);

		// キャンバスの左上の位置
		const Point canvasPos{ 100, 60 };

		// キャンバスのサイズ
		const Size canvasSize{ 512, 512 };

		// ペイント用の画像
		Image image{ canvasSize, Palette::White };

		// ペイント用の画像からテクスチャを作成する
		DynamicTexture texture{ image };

		// お題の文字
		char32 targetChar = Random(U'A', U'Z');

		// 非同期タスク
		AsyncHTTPTask task;

		// 直近のしりとりの履歴を格納する配列
		Array<String> recentWords = { String(1, targetChar) };

		// スコア
		int32 score = 0;

		while (System::Update())
		{
			// ペイントを行う
			PaintCanvas(image, canvasPos, texture, 6, ColorF{ 0.0 });

			// 背景の市松模様を描く
			DrawCheckerboard(40, ColorF{ 0.55, 0.75, 0.65 });

			// 送信ボタンが押されたら
			if (SimpleGUI::Button(U"判定", Vec2{ (canvasPos.x + 100), 620 }, 120,
				(not task.isDownloading()))) // 判定結果待機中のとき以外、ボタンを有効にする
			{
				// プロンプト
				String prompt = U"What is drawn in this image? The answer starts with the letter {}. "_fmt(targetChar);
				prompt += U"Write only the answer. Commas and periods are prohibited. If you don't know, output only a question mark.";

				// リクエスト
				OpenAI::Vision::Request request;

				// リクエストにプロンプトを設定
				request.questions = prompt;

				// リクエストに画像を添付
				request.images << OpenAI::Vision::ImageData::Base64FromImage(image);

				// タスクを作成する
				task = OpenAI::Vision::CompleteAsync(API_KEY, request);
			}

			// クリアボタンが押されたら
			if (SimpleGUI::Button(U"クリア", Vec2{ (canvasPos.x + canvasSize.x - 220), 620 }, 120))
			{
				// キャンバスをクリアする
				ClearCanvas(image, texture, Palette::White);
			}

			// 非同期処理が完了し、正常なレスポンスである場合
			if (task.isReady() && task.getResponse().isOK())
			{
				// 結果を取得する
				const String answer = OpenAI::Vision::GetContent(task.getAsJSON()).uppercase();

				// 履歴の末尾を更新する
				recentWords.back() = answer;

				// 正解したら
				if (answer != U"?")
				{
					targetChar = answer.back();
					++score;
				}

				// 履歴に次の項目を追加する
				recentWords << String(1, targetChar);

				// 履歴の内容が 8 個より大きくなったら
				if (8 < recentWords.size())
				{
					// 先頭の項目を削除する
					recentWords.pop_front();
				}
			}

			// キャンバスを描く
			DrawCanvas(texture, canvasPos);

			// お題の文字を描く
			DrawTargetCharacter(targetChar, canvasPos, font);

			// しりとりの履歴を描く
			DrawRecentHistory(recentWords, font, task.isDownloading());

			// スコアを描く
			DrawScore(score, font2);
		}
	}
	```
