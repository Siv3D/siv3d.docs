# 18. 乱数を生成する
ランダムな数を生成する方法を学びます。

## 18.1 ランダムな整数を生成する
- `Random(a, b)` は、**`a` 以上 `b` 以下**の整数をランダムに生成します
	- `a` < `b` である必要があります
- 生成される乱数のパターンは、毎回異なります

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/random/1.png)

```cpp title="1 以上 6 以下の乱数を 10 個出力する" hl_lines="10"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	for (int32 i = 0; i < 10; ++i)
	{
		// 1 以上 6 以下の乱数を出力する
		Print << Random(1, 6);
	}

	while (System::Update())
	{

	}
}
```


## 18.2 占いプログラムを作る
- 乱数での値に応じて異なる結果を表示することで、占いアプリを作ることができます
- 次のプログラムは、マウスをクリックするたびに、4 つの絵文字のうち 1 つをランダムに表示します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/random/2.png)

```cpp title="クリックするたびに 4 つの中からランダムな絵文字を表示する" hl_lines="20"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji0{ U"😄"_emoji };
	const Texture emoji1{ U"😵‍💫"_emoji };
	const Texture emoji2{ U"😭"_emoji };
	const Texture emoji3{ U"😋"_emoji };

	// 絵文字の番号
	int32 emojiIndex = 0;

	while (System::Update())
	{
		if (MouseL.down())
		{
			// 新しい絵文字の番号をランダムに選ぶ
			emojiIndex = Random(0, 3);

			// 絵文字の番号を出力する
			Print << emojiIndex;
		}

		if (emojiIndex == 0)
		{
			emoji0.drawAt(400, 300);
		}
		else if (emojiIndex == 1)
		{
			emoji1.drawAt(400, 300);
		}
		else if (emojiIndex == 2)
		{
			emoji2.drawAt(400, 300);
		}
		else
		{
			emoji3.drawAt(400, 300);
		}
	}
}
```


## 18.3 ランダムな浮動小数点数を生成する
- `Random(a, b)` で、`a`, `b` が浮動小数点数である場合、**`a` 以上 `b` 未満**の浮動小数点数をランダムに生成します
	- `a` < `b` である必要があります
	- 整数の場合の「`a` 以上 `b` 以下」とは異なります
- 次のコードは、クリックするたびに絵文字の拡大縮小倍率をランダムに変更します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/random/3.png)

```cpp title="クリックするたびに絵文字の拡大縮小倍率をランダムに変更する" hl_lines="17"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🎁"_emoji };

	// 拡大縮小倍率
	double scale = 1.0;

	while (System::Update())
	{
		if (MouseL.down())
		{
			// 0.5 以上 2.0 未満の範囲でランダムな値を生成する
			scale = Random(0.5, 2.0);

			// 拡大縮小倍率を出力する
			Print << scale;
		}

		emoji.scaled(scale).drawAt(400, 300);
	}
}
```


## 18.4 ランダムな場所に移動する
- X 座標 Y 座標を乱数で決め、絵文字をランダムな場所に移動させます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/random/4.png)

```cpp title="クリックするたびに絵文字がランダムな場所に移動する" hl_lines="15"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🛸"_emoji };

	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		if (MouseL.down())
		{
			pos = Vec2{ Random(100, 700), Random(100, 500) };
		}

		emoji.drawAt(pos);
	}
}
```


## 🧩 練習
次のようなプログラムを作成してみましょう。

### 練習 ① ダイスを振る
- 回転している（数字が変化している）ダイスの目をクリックで止める
- もう一度クリックすると、再び回転を始める

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/random/5-1.mp4?raw=true" autoplay loop muted playsinline></video>

??? info "ヒント"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// ダイスの正方形領域
		const Rect diceRect{ Arg::center(400, 300), 200 };

		// ダイスの目
		int32 result = 1;

		while (System::Update())
		{
			// マウスカーソルがダイスの上にある場合
			if (diceRect.mouseOver())
			{
				// マウスカーソルを手の形に変更する
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// ダイスの正方形を描画する
			diceRect.draw();

			// ダイスの数字を描画する
			font(U"{}"_fmt(result)).drawAt(120, Vec2{ 400, 300 }, ColorF{ 0.1 });
		}
	}
	```

??? success "解答例"

	- `not isRolling` は `!isRolling` と同じ意味です。Siv3D では視認性のために `!` を `not` で書くスタイルを採用しています

	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// ダイスの正方形領域
		const Rect diceRect{ Arg::center(400, 300), 200 };

		// ダイスの目
		int32 result = 1;

		// 回転中かどうか
		bool isRolling = true;

		while (System::Update())
		{
			// 回転中であれば
			if (isRolling)
			{
				// ダイスの目をランダムな値に変更する
				result = Random(1, 6);
			}

			// マウスカーソルがダイスの上にある場合
			if (diceRect.mouseOver())
			{
				// マウスカーソルを手の形に変更する
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// ダイスが左クリックされたら
			if (diceRect.leftClicked())
			{
				// 回転中の状態を反転させる
				isRolling = (not isRolling);
			}

			// ダイスの正方形を描画する
			diceRect.draw();

			// ダイスの数字を描画する
			font(U"{}"_fmt(result)).drawAt(120, Vec2{ 400, 300 }, ColorF{ 0.1 });
		}
	}
	```

## 振り返りチェックリスト
- [x] `Random(a, b)` は、`a` 以上 `b` 以下の整数をランダムに生成することを学んだ
- [x] `Random(a, b)` は、`a`, `b` が浮動小数点数である場合、`a` 以上 `b` 未満の浮動小数点数をランダムに生成することを学んだ

