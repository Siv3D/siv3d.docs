# 画像のサンプル

## 1. スケッチ

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/1.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// キャンバスのサイズ
		constexpr Size CanvasSize{ 600, 600 };

		// ペンの太さ
		constexpr int32 PenThickness = 8;

		// ペンの色
		constexpr Color PenColor = Palette::Orange;

		// 書き込み用の画像データを用意する
		Image image{ CanvasSize, Palette::White };

		// 表示用のテクスチャ（内容を更新するので DynamicTexture）
		DynamicTexture texture{ image };

		while (System::Update())
		{
			if (MouseL.pressed())
			{
				// 書き込む線の始点は直前のフレームのマウスカーソル座標
				// （初回はタッチ操作時の座標のジャンプを防ぐため、現在のマウスカーソル座標にする）
				const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());

				// 書き込む線の終点は現在のマウスカーソル座標
				const Point to = Cursor::Pos();

				// image に線を書き込む
				Line{ from, to }.overwrite(image, PenThickness, PenColor);

				// 書き込み終わった image でテクスチャを更新
				texture.fill(image);
			}

			// 描いたものを消去するボタンが押されたら
			if (SimpleGUI::Button(U"Clear", Vec2{ 640, 40 }, 120))
			{
				// 画像を白で塗りつぶす
				image.fill(Palette::White);

				// 塗りつぶし終わった image でテクスチャを更新する
				texture.fill(image);
			}

			// テクスチャを表示
			texture.draw();
		}
	}
	```

## 2. 万華鏡スケッチ

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/2.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// キャンバスのサイズ
		constexpr Size CanvasSize{ 600, 600 };

		// 分割数
		constexpr int32 N = 12;

		// 背景色
		constexpr Color BackgroundColor{ 20, 40, 60 };

		// ウィンドウをキャンバスのサイズにする
		Window::Resize(CanvasSize);

		// 書き込み用の画像
		Image image{ CanvasSize, BackgroundColor };

		// 画像を表示するための動的テクスチャ
		DynamicTexture texture{ image };

		while (System::Update())
		{
			if (MouseL.pressed())
			{
				// 画面の中心が (0, 0) になるようにマウスカーソルの座標を移動させる
				const Vec2 begin = ((MouseL.down() ? Cursor::PosF() : Cursor::PreviousPosF()) - CanvasSize / 2);
				const Vec2 end = (Cursor::PosF() - CanvasSize / 2);

				// 時間に応じて色を変化させる
				const ColorF color = HSV{ (Scene::Time() * 60.0), 0.5, 1.0 };

				for (int32 i = 0; i < N; ++i)
				{
					// 円座標に変換する
					std::array<Circular, 2> cs = { begin, end };

					for (auto& c : cs)
					{
						// 角度をずらす
						if (IsEven(i))
						{
							c.theta = (-c.theta - 2_pi / N * (i - 1));
						}
						else
						{
							c.theta = (c.theta + 2_pi / N * i);
						}
					}

					// ずらした位置をもとに、画像に線を書き込む
					Line{ cs[0], cs[1] }.moveBy(CanvasSize / 2)
						.overwrite(image, 2, color);
				}

				// 書き込んだ画像でテクスチャを更新する
				texture.fillIfNotBusy(image);
			}

			if (MouseR.down()) // 右クリックでリセットする
			{
				// 画像を塗りつぶす
				image.fill(BackgroundColor);

				// 塗りつぶした画像でテクスチャを更新
				texture.fill(image);
			}

			// テクスチャを描く
			texture.draw();
		}
	}
	```

## 3. ペイント

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/3.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// キャンバスのサイズ
		constexpr Size CanvasSize{ 600, 600 };

		// ペンの太さ
		double penThickness = 8;

		// ペンの色
		HSV penColor = Palette::Orange;

		// 書き込み用の画像データを用意する
		Image image{ CanvasSize, Palette::White };

		// 表示用のテクスチャ（内容を更新するので DynamicTexture）
		DynamicTexture texture{ image };

		const Array<String> modes = { U"Draw", U"Fill", U"Pick" };

		size_t modeIndex = 0;

		while (System::Update())
		{
			if (modeIndex == 0) // ペン
			{
				if (MouseL.pressed())
				{
					// 書き込む線の始点は直前のフレームのマウスカーソル座標
					// （初回はタッチ操作時の座標のジャンプを防ぐため、現在のマウスカーソル座標にする）
					const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());

					// 書き込む線の終点は現在のマウスカーソル座標
					const Point to = Cursor::Pos();

					// image に線を書き込む
					Line{ from, to }.overwrite(image, static_cast<int32>(penThickness), penColor, Antialiased::No);

					// 書き込み終わった image でテクスチャを更新
					texture.fill(image);
				}
				else if (MouseR.pressed())
				{
					const Point from = (MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos());
					const Point to = Cursor::Pos();
					Line{ from, to }.overwrite(image, static_cast<int32>(penThickness), Palette::White, Antialiased::No);
					texture.fill(image);
				}
			}
			else if (modeIndex == 1) // 塗りつぶし
			{
				if (MouseL.down())
				{
					image.floodFill(Cursor::Pos(), penColor);
					texture.fill(image);
				}
				else if (MouseR.down())
				{
					image.floodFill(Cursor::Pos(), Palette::White);
					texture.fill(image);
				}
			}
			else // ピッカー
			{
				if (MouseL.down())
				{
					const Point cursorPos = Cursor::Pos();

					if (InRange(cursorPos.x, 0, (image.width() - 1))
						&& InRange(cursorPos.y, 0, (image.height() - 1)))
					{
						penColor = image[cursorPos];
					}
				}
			}

			if (SimpleGUI::Button(U"Save", Vec2{ 620, 40 }, 160))
			{
				image.saveWithDialog();
			}

			// 描いたものを消去するボタンが押されたら
			if (SimpleGUI::Button(U"Clear", Vec2{ 620, 100 }, 160))
			{
				// 画像を白で塗りつぶす
				image.fill(Palette::White);

				// 塗りつぶし終わった image でテクスチャを更新する
				texture.fill(image);
			}

			// 色の選択
			SimpleGUI::ColorPicker(penColor, Vec2{ 620, 160 });

			// ペンの太さ
			SimpleGUI::Slider(penThickness, 1.0, 30.0, Vec2{ 620, 300 }, 160);

			// モードの選択
			SimpleGUI::RadioButtons(modeIndex, modes, Vec2{ 620, 360 });

			// テクスチャを表示
			texture.draw();
		}
	}
	```


## 4. Image to Polygon

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/4.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// 使用する画像
		const Image image{ U"example/siv3d-kun.png" };

		// テクスチャの表示位置
		constexpr Vec2 BasePos{ 40, 80 };

		// テクスチャ
		const Texture texture{ image };

		// アルファ値 1 以上の領域を Polygon 化する
		const Polygon polygon = image.alphaToPolygon(1, AllowHoles::No);

		// Polygon 単純化の許容距離（ピクセル）
		double maxDistance = 4.0;

		// 単純化した Polygon
		Polygon simplifiedPolygon = polygon.simplified(maxDistance);

		while (System::Update())
		{
			// 単純化した Polygon の三角形数を表示する
			ClearPrint();
			Print << U"{} triangles"_fmt(simplifiedPolygon.num_triangles());

			texture.draw(BasePos);

			// 単純化した Polygon をテクスチャ上に表示する
			simplifiedPolygon.movedBy(BasePos)
				.draw(ColorF{ 1.0, 1.0, 0.0, 0.2 })
				.drawWireframe(2, Palette::Yellow);

			// 単純化した Polygon をテクスチャの横に表示する
			simplifiedPolygon.movedBy(BasePos.movedBy(320, 0))
				.draw(ColorF{ 0.5 });

			// Polygon 単純化の許容距離を設定するスライダー
			if (SimpleGUI::Slider(U"{:.1f}"_fmt(maxDistance), maxDistance, 0, 50, Vec2{ 400, 40 }, 60, 240))
			{
				// スライダーに変更があれば、単純化した Polygon を新しい許容距離で再作成
				simplifiedPolygon = polygon.simplified(maxDistance);
			}
		}
	}
	```


## 5. JPEG Glitch

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/5.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// 画像
		const Image image{ U"example/windmill.png" };

		// 表示用の動的テクスチャ
		DynamicTexture texture{ image };

		// JPEG のバイナリデータ
		const Blob originalBlob = image.encodeJPEG();

		// 改変するデータの個数
		const size_t noiseCount = (image.num_pixels() / 4000);

		while (System::Update())
		{
			if (SimpleGUI::Button(U"Glitch", Vec2{ 40, 40 }))
			{
				// Array を作成
				Blob modifiedBlob = originalBlob;

				for (size_t i = 0; i < noiseCount; ++i)
				{
					// ランダムな位置の 1 バイトについて、ランダムな値に書き換える。
					// ヘッダ部分（先頭）は改変しない。
					const size_t index = Random<size_t>(630, (modifiedBlob.size() - 1));

					modifiedBlob[index] = Byte{ RandomUint8() };
				}

				// JPEG データとして読み込んで画像を作成、動的テクスチャに転送
				texture.fill(Image{ MemoryReader{ modifiedBlob }, ImageFormat::JPEG });
			}

			texture.drawAt(Scene::Center());
		}
	}
	```


## 6. 模写アプリ

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/6.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// 2 つの画像の距離を計算する関数
	double Diff(const Image& a, const Image& b)
	{
		const Color* pA = a.data();
		const Color* pB = b.data();
		const Color* const pAEnd = (pA + a.num_pixels());
		double d = 0.0;

		// すべてのピクセルに対して
		while (pA != pAEnd)
		{
			d += (AbsDiff(pA->r, pB->r) + AbsDiff(pA->g, pB->g) + AbsDiff(pA->b, pB->b));
			++pA;
			++pB;
		}

		return d;
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// 目標とする画像をファイルダイアログで選択、シーンのサイズにフィットするようリサイズ
		const Image target = Dialog::OpenImage().fit(Scene::Size());

		// 現在の画像
		Image image{ target.size(), Palette::White };

		// 直前の画像
		Image old = image;

		// 現在の画像を表示するための動的テクスチャ
		DynamicTexture texture{ image };

		// 目標との距離
		double d1 = Diff(target, image);

		while (System::Update())
		{
			for (int32 i = 0; i < 100; ++i)
			{
				// ランダムな座標
				const Point pos = RandomPoint(Rect{ image.size() });

				// ランダムな色
				const ColorF color{ Random(), Random(), Random(), Random() };

				// ランダムな半径
				const int32 size = Random(1, 10);

				// 円を現在の画像に書き込む
				Circle{ pos, size }.paint(image, color);

				// 目標との距離を計算
				const double d2 = Diff(target, image);

				if (d2 < d1) // 目標に近づいていたら採用
				{
					d1 = d2;
					old = image;
				}
				else // 近づいていなかったら元に戻す
				{
					image = old;
				}
			}

			// 動的テクスチャを更新する
			texture.fill(image);

			// テクスチャを画面の中心に描画する
			texture.drawAt(Scene::Center());

			// 保存ボタン
			if (SimpleGUI::Button(U"Save", Vec2{ 660, 550 }))
			{
				// 現在の画像をファイルダイアログ経由で保存する
				image.saveWithDialog();
			}
		}
	}
	```


## 7. GrabCut による背景分離と Inpaint による修復

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/VfhFdJOdWw0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.8, 1.0, 0.9 });

		const Image image = Dialog::OpenImage().fit(Size{ 480, 320 });
		const Texture texture{ image };

		GrabCut grabcut{ image };
		Image mask{ image.size(), Color{0, 0} };
		Image background{ image.size(), Palette::Black };
		Image foreground{ image.size(), Palette::Black };
		Image inpaint;
		DynamicTexture maskTexture{ mask };
		Grid<GrabCutClass> result;
		DynamicTexture classTexture;
		DynamicTexture backgroundTexture{ background };
		DynamicTexture foregroundTexture{ foreground };
		DynamicTexture inpaintTexture{ foreground };

		constexpr Color BackgroundColor{ 0, 0, 255 };
		constexpr Color ForegroundColor{ 250, 100, 50 };

		while (System::Update())
		{
			if ((not classTexture) || MouseL.up() || MouseR.up())
			{
				grabcut.update(mask, ForegroundColor, BackgroundColor);
				grabcut.getResult(result);
				classTexture.fill(Image(result, [](GrabCutClass c) { return Color(80 * FromEnum(c)); }));

				for (auto p : step(image.size()))
				{
					const bool isBackground = (GrabCutClass::PossibleBackground <= result[p]);

					if (isBackground)
					{
						background[p] = image[p];
						foreground[p] = Color{ 0,0 };
					}
					else
					{
						foreground[p] = image[p];
						background[p] = Color{ 0,0 };
					}
				}

				ImageProcessing::Inpaint(background, background, Color{ 0, 0 }, inpaint);
				inpaint.gaussianBlur(3);

				foregroundTexture.fill(foreground);
				backgroundTexture.fill(background);
				inpaintTexture.fill(inpaint);
			}

			if (MouseL.pressed())
			{
				const Point from = MouseL.down() ? Cursor::Pos() : Cursor::PreviousPos();
				const Point to = Cursor::Pos();
				Line{ from, to }.overwrite(mask, 4, ForegroundColor, Antialiased::No);
				maskTexture.fill(mask);
			}
			else if (MouseR.pressed())
			{
				const Point from = MouseR.down() ? Cursor::Pos() : Cursor::PreviousPos();
				const Point to = Cursor::Pos();
				Line{ from, to }.overwrite(mask, 4, BackgroundColor, Antialiased::No);
				maskTexture.fill(mask);
			}

			texture.draw();
			maskTexture.draw();
			classTexture.draw(600, 0);

			backgroundTexture.scaled(0.7).regionAt(200, 520).draw(ColorF{ 0 });
			backgroundTexture.scaled(0.7).drawAt(200, 520);

			foregroundTexture.scaled(0.7).regionAt(1080, 520).draw(ColorF{ 0 });
			foregroundTexture.scaled(0.7).drawAt(1080, 520);

			inpaintTexture.drawAt(640, 520);
			{
				const Transformer2D transformer{ Mat3x2::Scale(1.1, Vec2{640, 520}.movedBy(0, image.height() / 2)).translated((Scene::Center() - Cursor::Pos()) * 0.04) };
				foregroundTexture.drawAt(640, 520);
			}
		}
	}
	```


## 8. ドロップされたイラストから顔を検出

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Texture texture;

		double scale = 1.0;

		// 検出器。正面を向いた顔の学習データを使用して分類
		const CascadeClassifier animeFaceDetector{ U"example/objdetect/haarcascade/face_anime.xml" };

		Array<Rect> detectedFaces;

		while (System::Update())
		{
			// ファイルがドロップされた
			if (DragDrop::HasNewFilePaths())
			{
				// ファイルを画像として読み込めた
				if (const Image image{ DragDrop::GetDroppedFilePaths().front().path })
				{
					// イラスト内の顔を検出する
					detectedFaces = animeFaceDetector.detectObjects(image);

					// 画面のサイズに合うように画像を拡大縮小
					texture = Texture{ image.fitted(Scene::Size()) };

					// 画像の拡大縮小率
					scale = (static_cast<double>(texture.width()) / image.width());
				}
			}

			if (texture)
			{
				texture.draw(0, 0);

				// 顔の領域の座標を表示に合わせる
				const Transformer2D transformer{ Mat3x2::Scale(scale) };

				for (const auto& detectedFace : detectedFaces)
				{
					detectedFace.drawFrame((4 / scale), ColorF{ 1.0, 0.0, 0.0, Periodic::Sine0_1(1.5s) });
				}
			}
		}
	}
	```


## 9. マンデルブロ集合

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/image/9.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	int32 Mandelbrot(double x, double y)
	{
		double a = 0.0, b = 0.0;

		for (int32 n = 0; n < 360; ++n)
		{
			const double t = (a * a - b * b + x);
			const double u = (2.0 * a * b + y);

			if (4.0 < (t * t + u * u))
			{
				return n;
			}

			a = t;
			b = u;
		}

		return 0;
	}

	void Main()
	{
		constexpr Size SceneSize{ 640, 480 };
		Window::Resize(SceneSize);

		Vec2 center(0, 0);
		double scale = -4.0;

		// 結果を保存する画像
		Image image{ SceneSize, Palette::Black };

		// 描画用の動的テクスチャ
		DynamicTexture texture(image);

		while (System::Update())
		{
			const double wheel = Mouse::Wheel();
			const bool clicked = (MouseL | MouseR).down();

			// 最初のフレームか、操作があったときだけ更新する
			if (wheel || clicked || (Scene::FrameCount() == 1))
			{
				scale -= wheel;

				const double s = Pow(1.25, scale);
				const double d = ((1.0 / s) / SceneSize.x);

				if (clicked)
				{
					center += (Cursor::PosF() - SceneSize / 2) * d;
				}

				const double xb = (center.x - d * (SceneSize.x * 0.5));
				const double yb = (center.y - d * (SceneSize.y * 0.5));

				for (int32 y = 0; y < SceneSize.y; ++y)
				{
					const double yPos = yb + (d * y);

					for (int32 x = 0; x < SceneSize.x; ++x)
					{
						const double xPos = xb + (d * x);

						if (const int32 m = Mandelbrot(xPos, yPos))
						{
							image[y][x] = HSV{ (240 - m), 0.8, 1.0 };
						}
						else
						{
							image[y][x] = Palette::Black;
						}
					}
				}

				// 動的テクスチャの中身を image で更新する
				texture.fill(image);
			}

			// テクスチャを描画する
			texture.draw();
		}
	}
	```

## 10. 万華鏡ランダムウォーク

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/RandomWalkKaleidoscope/Screenshot/2.png)

[Siv3D-Sample | 万華鏡ランダムウォーク :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/blob/main/Samples/RandomWalkKaleidoscope){:target="_blank" .md-button}

