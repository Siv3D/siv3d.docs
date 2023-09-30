# ゲーム開発のヒント集
この記事では、[Siv3D ゲームジャム :material-open-in-new:](https://bandainamcostudios.connpass.com/event/295239/){:target="_blank"} の参加者に向けて、Siv3D でゲームを制作する際のヒントを紹介します。高品質でユニークなゲーム開発に役立つヒントが見つかるかもしれません。ゲームジャムの参加人数と同じ数のヒントを用意する予定です。

## 1. ゲームのイメージにあったフォントを選ぶ
Siv3D v0.6.12 から、MSDF 形式の Font についても、複雑な字形を美しく描画できるようになりました。ここでは、ゲーム開発に使えそうないくつかのユニークなフォントを紹介します。

| フォント名 |ライセンス |
| --- | --- |
| [玉ねぎ楷書激無料版v7改 :material-open-in-new:](https://booth.pm/ja/items/2929647){:target="_blank"} | 独自ライセンス |
| [赤薔薇シンデレラ :material-open-in-new:](http://modi.jpn.org/font_kurobara-cinderella.php){:target="_blank"} | 独自ライセンス |
| [Dela Gothic One :material-open-in-new:](https://fonts.google.com/specimen/Dela+Gothic+One){:target="_blank"} | SIL Open Font License |
| [851チカラヅヨク-かなA :material-open-in-new:](http://pm85122.onamae.jp/851ch-dz.html){:target="_blank"} | 独自ライセンス |
| [07ロゴたいぷゴシックCondense :material-open-in-new:](http://www.fontna.com/blog/1345/){:target="_blank"} | M+ FONT LICENSE |
| [x12y12pxMaruMinya :material-open-in-new:](https://booth.pm/ja/items/4927023){:target="_blank"} | 独自ライセンス |
| [Rounded-X Mgen+ 1pp heavy :material-open-in-new:](http://jikasei.me/font/rounded-mgenplus/) | SIL Open Font License |
| [めもわーる-しかく :material-open-in-new:](http://modi.jpn.org/font_memoir.php){:target="_blank"} | 独自ライセンス |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/reference/game_tips/1.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Window::Resize(1280, 720);
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// 玉ねぎ楷書激無料版v7改
		const Font font01{ FontMethod::MSDF, 48, U"fonts/玉ねぎ楷書激無料版v7改.ttf" };

		// 赤薔薇シンデレラ
		const Font font02{ FontMethod::MSDF, 48, U"fonts/akabara-cinderella.ttf" };

		// Dela Gothic One
		const Font font03{ FontMethod::MSDF, 48, U"fonts/DelaGothicOne-Regular.ttf" };

		// 851チカラヅヨク-かなA
		const Font font04{ FontMethod::MSDF, 48, U"fonts/851CHIKARA-DZUYOKU_kanaA_004.ttf" };

		// 07ロゴたいぷゴシックCondense
		const Font font05{ FontMethod::MSDF, 48, U"fonts/ロゴたいぷゴシックCondense.otf" };

		// x12y12pxMaruMinya
		const Font font06{ FontMethod::MSDF, 48, U"fonts/x12y12pxMaruMinya.ttf" };

		// Rounded-X Mgen+ 1pp heavy
		const Font font07{ FontMethod::MSDF, 48, U"fonts/rounded-x-mgenplus-1pp-heavy.ttf" };

		// めもわーる-しかく
		const Font font08{ FontMethod::MSDF, 48, U"fonts/memoir-square.otf" };

		while (System::Update())
		{
			font01(U"宣戦布告 異論あり 勝利 恐怖 追放 破壊 決定").draw(55, Vec2{ 40, 40 }, ColorF{ 0.11 });
			font02(U"喫茶店 執務室 異世界 締め切り コンピュータ ねこ ミステリー").draw(55, Vec2{ 40, 120 }, ColorF{ 0.11 });
			font03(U"メニュー 変更 レイアウト 3日目 操作方法 OK").draw(55, Vec2{ 40, 180 }, ColorF{ 0.11 });
			font04(U"野菜 レストラン 日記 夏休み ごちそうさま 旅立ち").draw(55, Vec2{ 40, 270 }, ColorF{ 0.11 });
			font05(U"シンフォニー 目標売り上げ 博物館 プレイ記録").draw(55, Vec2{ 40, 350 }, ColorF{ 0.11 });
			font06(U"ハイスコア 1234 セーブ 対戦 小説 音楽 舞台").draw(55, Vec2{ 40, 430 }, ColorF{ 0.11 });
			font07(U"一覧 新着ニュース 接続中 つづきから メッセージ").draw(55, Vec2{ 40, 510 }, ColorF{ 0.11 });
			font08(U"おすすめ ワールド 03 クリア ゲームオーバー").draw(55, Vec2{ 40, 600 }, ColorF{ 0.11 });
		}
	}
	```


## 2. ウィンドウのサイズを変更する
Siv3D のデフォルトのウィンドウサイズは 800 x 600 ですが、特殊なサイズに変更することでユニークな制約がうまれ、斬新なゲームを作れるかもしれません。

| 例 | ウィンドウサイズ |
|:---:|:---:|
| <img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/reference/game_tips/2-1.png" width="600"> | 800 x 160 |
| <img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/reference/game_tips/2-2.png" width="300"> | 400 x 600 |
| <img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/reference/game_tips/2-3.png" width="450"> | 600 x 600 |


## 3. 背景にひと手間加える

単色の背景ではなく、グラデーションや模様を加えることで、ゲームの雰囲気をより引き立たせることができます。

#### グラデーション

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/reference/game_tips/3-1.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	/// @brief 上下方向のグラデーションの背景を描画します。
	/// @param topColor 上部の色
	/// @param bottomColor 下部の色
	void DrawVerticalGradientBackground(const ColorF& topColor, const ColorF& bottomColor)
	{
		Scene::Rect()
			.draw(Arg::top = topColor, Arg::bottom = bottomColor);
	}

	void Main()
	{
		while (System::Update())
		{
			DrawVerticalGradientBackground(ColorF{ 0.2, 0.5, 1.0 }, ColorF{ 0.5, 0.8, 1.0 });
		}
	}
	```


#### 放射状のグラデーション

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/reference/game_tips/3-2.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	/// @brief 放射状のグラデーションの背景を描画します。
	/// @param centerColor 中心の色
	/// @param outerColor 外周の色
	void DrawRadialGradientBackground(const ColorF& centerColor, const ColorF& outerColor)
	{
		Circle{ Scene::Center(), (Scene::Size().length() * 0.5) }
			.draw(centerColor, outerColor);
	}

	void Main()
	{
		while (System::Update())
		{
			DrawRadialGradientBackground(ColorF{ 0.98, 0.95, 0.92 }, ColorF{ 0.8, 0.77, 0.74 });
		}
	}
	```


#### 市松模様

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/reference/game_tips/3-3.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	// @brief 市松模様の背景を描画します。
	// @param cellSize セルのサイズ
	// @param cellColor セルの色
	void DrawCheckerboardBackground(int32 cellSize, const ColorF& cellColor)
	{
		for (int32 y = 0; y < (Scene::Height() / cellSize); ++y)
		{
			for (int32 x = 0; x < (Scene::Width() / cellSize); ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (Point{ x, y } *cellSize), cellSize }.draw(cellColor);
				}
			}
		}
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.4 });

		while (System::Update())
		{
			DrawCheckerboardBackground(40, ColorF{ 0.45 });
		}
	}
	```


#### 水玉模様

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/reference/game_tips/3-4.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	/// @brief 水玉模様の背景を描画します。
	/// @param cellSize セルのサイズ
	/// @param circleScale 円のスケール
	/// @param color 色
	void DrawPolkaDotBackground(int32 cellSize, double circleScale, const ColorF& color)
	{
		for (int32 y = 0; y < (Scene::Height() / cellSize); ++y)
		{
			for (int32 x = 0; x < (Scene::Width() / cellSize); ++x)
			{
				if (IsEven(x + y))
				{
					Circle{ (Vec2{ (x + 0.5), (y + 0.5) } *cellSize), (cellSize * circleScale) }.draw(color);
				}
			}
		}
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.82, 0.9, 0.98 });

		while (System::Update())
		{
			DrawPolkaDotBackground(40, 0.2, ColorF{ 0.98 });
		}
	}
	```


#### 斜めのストライプ

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/reference/game_tips/3-5.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	/// @brief 斜めのストライプの背景を描画します。
	/// @param width ストライプの幅
	/// @param angle ストライプの角度
	/// @param color ストライプの色
	void DrawStripedBackground(int32 width, double angle, const ColorF& color)
	{
		for (int32 x = -Scene::Height(); x < (Scene::Width() + Scene::Height()); x += (width * 2))
		{
			Rect{ x, 0, width, Scene::Height() }.skewedX(angle).draw(color);
		}
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.88 });

		while (System::Update())
		{
			DrawStripedBackground(40, 45_deg, ColorF{ 0.84 });
		}
	}
	```

## 4. 大きい数字を桁区切りで表示する
桁数の多い数字を表示するときは、桁区切りを入れると読みやすくなります。`ThousandsSeparate(x)` は数値 `x` を桁区切りした文字列を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/reference/game_tips/4-1.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font1{ FontMethod::MSDF, 48, Typeface::Bold };
		const Font font2{ FontMethod::MSDF, 48, U"example/font/RocknRoll/RocknRollOne-Regular.ttf"};

		int32 money = 886644;
		int32 highScore = 123456789;

		while (System::Update())
		{
			font1(U"所持金: {} 円"_fmt(money)).draw(30, Vec2{ 40, 40 }, ColorF{ 0.11 });
			font1(U"所持金: {} 円"_fmt(ThousandsSeparate(money))).draw(30, Vec2{ 40, 100 }, ColorF{ 0.11 });

			font2(U"ハイスコア").draw(40, Vec2{ 160, 200 }, ColorF{ 0.11 });
			font2(highScore).draw(40, Arg::topRight(720, 200), ColorF{ 0.11 });

			font2(U"ハイスコア").draw(40, Vec2{ 160, 280 }, ColorF{ 0.11 });
			font2(ThousandsSeparate(highScore)).draw(40, Arg::topRight(720, 280), ColorF{ 0.11 });
		}
	}
	```


## 5. 小数点以下の桁数を制御する
`_fmt()` の変換指定子で `{:.Nf}` とすると、`double` 型など浮動小数点数型の値の小数点以下の桁数を `N` に設定できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/reference/game_tips/5-1.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font1{ FontMethod::MSDF, 48, Typeface::Bold };
		const Font font2{ FontMethod::MSDF, 48, U"example/font/RocknRoll/RocknRollOne-Regular.ttf" };

		double distance = 142.76542;
		double similarity = 0.9876543;

		while (System::Update())
		{
			font1(U"飛距離: {} m"_fmt(distance)).draw(30, Vec2{ 40, 40 }, ColorF{ 0.11 });
			font1(U"飛距離: {:.2f} m"_fmt(distance)).draw(30, Vec2{ 40, 100 }, ColorF{ 0.11 });

			font2(U"一致度").draw(40, Vec2{ 260, 200 }, ColorF{ 0.11 });
			font2(U"{}%"_fmt(similarity * 100)).draw(40, Arg::topRight(720, 200), ColorF{0.11});

			font2(U"一致度").draw(40, Vec2{ 260, 280 }, ColorF{ 0.11 });
			font2(U"{:.1f}%"_fmt(similarity * 100)).draw(40, Arg::topRight(720, 280), ColorF{ 0.11 });
		}
	}
	```

## 6.

## 7.

## 8.

## 9.

## 10.

## 11.

## 12.

## 13.

## 14.

## 15.

## 16.

## 17.

## 18.

## 19.

## 20.

## 21.

## 22.

## 23.

## 24.

## 25.

## 26.

## 27.

## 28.

## 29.

## 30.

## 31.

## 32.

## 33.

## 34.
