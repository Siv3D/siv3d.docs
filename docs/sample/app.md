
# Application

## Sketch
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

## Piano
![](images/app-piano.gif)
```C++
# include <Siv3D.hpp>

void Main()
{
	// 白鍵の大きさ
	constexpr Size keySize(55, 400);

	// 楽器の種類
	constexpr GMInstrument instrument = GMInstrument::Piano1;

	// ウインドウをリサイズ
	Window::Resize(12 * keySize.x, keySize.y);

	// 鍵盤の数
	constexpr int32 NumKeys = 20;

	// 音を作成
	std::array<Audio, NumKeys> sounds;
	for (auto i : step(NumKeys))
	{
		sounds[i] = Audio(Wave(instrument, static_cast<uint8>(PianoKey::A3 + i), 0.5s));
	}

	// 対応するキー
	constexpr std::array<Key, NumKeys> keys =
	{
		KeyTab, Key1, KeyQ,
		KeyW, Key3, KeyE, Key4, KeyR, KeyT, Key6, KeyY, Key7, KeyU, Key8, KeyI,
		KeyO, Key0, KeyP, KeyMinus, KeyGraveAccent,
	};

	// 描画位置計算用のオフセット値
	constexpr std::array<int32, NumKeys> keyPositions =
	{
		0, 1, 2, 4, 5, 6, 7, 8, 10, 11, 12, 13, 14, 15, 16, 18, 19, 20, 21, 22
	};

	while (System::Update())
	{
		// キーが押されたら対応する音を再生
		for (auto i : step(NumKeys))
		{
			if (keys[i].down())
			{
				sounds[i].playOneShot(0.5);
			}
		}

		// 白鍵を描画
		for (auto i : step(NumKeys))
		{
			// オフセット値が偶数
			if (IsEven(keyPositions[i]))
			{
				RectF(keyPositions[i] / 2 * keySize.x, 0, keySize.x, keySize.y)
					.stretched(-1).draw(keys[i].pressed() ? Palette::Pink : Palette::White);
			}
		}

		// 黒鍵を描画
		for (auto i : step(NumKeys))
		{
			// オフセット値が奇数
			if (IsOdd(keyPositions[i]))
			{
				RectF(keySize.x * 0.68 + keyPositions[i] / 2 * keySize.x, 0, keySize.x * 0.58, keySize.y * 0.62)
					.draw(keys[i].pressed() ? Palette::Pink : Color(24));
			}
		}
	}
}
```


## Kaleidoscope sketch
![](images/app-kaleidoscope-paint.gif)
```C++
# include <Siv3D.hpp>

void Main()
{
	// キャンバスのサイズ
	constexpr Size canvasSize(600, 600);

	// 分割数
	constexpr int32 N = 12;

	// 背景色
	constexpr Color backgroundColor(20, 40, 60);

	// ウィンドウをキャンバスのサイズに
	Window::Resize(canvasSize);

	// 書き込み用の画像
	Image image(canvasSize, backgroundColor);

	// 画像を表示するための動的テクスチャ
	DynamicTexture texture(image);

	while (System::Update())
	{
		if (MouseL.pressed())
		{
			// 画面の中心が (0, 0) になるようにマウスカーソルの座標を移動
			const Vec2 begin = (MouseL.down() ? Cursor::PosF() : Cursor::PreviousPosF()) - Scene::Center();
			const Vec2 end = Cursor::PosF() - Scene::Center();

			for (auto i : step(N))
			{
				// 円座標に変換
				std::array<Circular, 2> cs = { begin, end };

				for (auto& c : cs)
				{
					// 角度をずらす
					c.theta = IsEven(i) ? (-c.theta - 2_pi / N * (i - 1)) : (c.theta + 2_pi / N * i);
				}

				// ずらした位置をもとに、画像に線を書き込む
				Line(cs[0], cs[1]).moveBy(Scene::Center())
					.paint(image, 2, HSV(Scene::Time() * 60.0, 0.5, 1.0));
			}

			// 書き込んだ画像でテクスチャを更新
			texture.fillIfNotBusy(image);
		}
		else if (MouseR.down()) // 右クリックされたら
		{
			// 画像を塗りつぶす
			image.fill(backgroundColor);

			// 塗りつぶした画像でテクスチャを更新
			texture.fill(image);
		}

		// テクスチャを描く
		texture.draw();
	}
}
```