# 20. クリックゲーム
チュートリアル 3 ～ 19 の内容を使って、アイテムをクリックするゲームを作ります。

## 20.1 アイテムの描画とクリック判定
- クリック対象の絵文字と、クリック判定用の円を用意します
- クリックすされた場合、ランダムな位置に移動します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/click/1.png)

```cpp
#include <Siv3D.hpp>

// ランダムな座標を返す関数
Vec2 GetRandomPos()
{
	return{ Random(60, 740), Random(60, 540) };
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// クリック目標の絵文字
	const Texture targetEmoji{ U"🍎"_emoji };

	// クリック目標の円
	Circle targetCircle{ 400, 300, 60 };

	while (System::Update())
	{
		// クリック目標をクリックしたら
		if (targetCircle.leftClicked())
		{
			// クリック目標の位置をランダムな位置に変更する
			targetCircle.center = GetRandomPos();
		}

		// クリック目標を描く
		targetEmoji.drawAt(targetCircle.center);
	}
}
```


## 20.2 マウスオーバー判定
- クリック対象がマウスオーバーされている場合、マウスカーソルを手の形に変更します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/click/2.png)

```cpp hl_lines="21-26"
#include <Siv3D.hpp>

// ランダムな座標を返す関数
Vec2 GetRandomPos()
{
	return{ Random(60, 740), Random(60, 540) };
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// クリック目標の絵文字
	const Texture targetEmoji{ U"🍎"_emoji };

	// クリック目標の円
	Circle targetCircle{ 400, 300, 60 };

	while (System::Update())
	{
		// クリック目標がマウスカーソルに重なっていたら
		if (targetCircle.mouseOver())
		{
			// マウスカーソルを手の形に変更する
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クリック目標をクリックしたら
		if (targetCircle.leftClicked())
		{
			// クリック目標の位置をランダムな位置に変更する
			targetCircle.center = GetRandomPos();
		}

		// クリック目標を描く
		targetEmoji.drawAt(targetCircle.center);
	}
}
```


## 20.3 スコアの表示
- アイテムをクリックするとスコアが加算されるようにします
- スコアを画面に表示します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/click/3.png)

```cpp hl_lines="13 21-22 36 45-46"
#include <Siv3D.hpp>

// ランダムな座標を返す関数
Vec2 GetRandomPos()
{
	return{ Random(60, 740), Random(60, 540) };
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クリック目標の絵文字
	const Texture targetEmoji{ U"🍎"_emoji };

	// クリック目標の円
	Circle targetCircle{ 400, 300, 60 };

	// スコア
	int32 score = 0;

	while (System::Update())
	{
		// クリック目標がマウスカーソルに重なっていたら
		if (targetCircle.mouseOver())
		{
			// マウスカーソルを手の形に変更する
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クリック目標をクリックしたら
		if (targetCircle.leftClicked())
		{
			score += 100;

			// クリック目標の位置をランダムな位置に変更する
			targetCircle.center = GetRandomPos();
		}

		// クリック目標を描く
		targetEmoji.drawAt(targetCircle.center);

		// スコアを表示する
		font(U"SCORE: {}"_fmt(score)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });
	}
}
```

## 20.4 妨害アイテムの追加
- クリックすると減点されてしまう妨害アイテムを追加します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/click/4.png)

```cpp hl_lines="18-19 24-25 33 43-44 47-53 58-59"
#include <Siv3D.hpp>

// ランダムな座標を返す関数
Vec2 GetRandomPos()
{
	return{ Random(60, 740), Random(60, 540) };
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クリック目標の絵文字
	const Texture targetEmoji{ U"🍎"_emoji };

	// 妨害アイテムの絵文字
	const Texture trapEmoji{ U"🌶"_emoji };

	// クリック目標の円
	Circle targetCircle{ 400, 300, 60 };

	// 妨害アイテムの円
	Circle trapCircle{ 200, 150, 60 };

	// スコア
	int32 score = 0;

	while (System::Update())
	{
		// クリック目標がマウスカーソルに重なっていたら
		if (targetCircle.mouseOver() || trapCircle.mouseOver())
		{
			// マウスカーソルを手の形に変更する
			Cursor::RequestStyle(CursorStyle::Hand);
		}

		// クリック目標をクリックしたら
		if (targetCircle.leftClicked())
		{
			score += 100;
			targetCircle.center = GetRandomPos();
			trapCircle.center = GetRandomPos();
		}

		// 妨害アイテムをクリックしたら
		if (trapCircle.leftClicked())
		{
			score -= 200;
			targetCircle.center = GetRandomPos();
			trapCircle.center = GetRandomPos();
		}

		// クリック目標を描く
		targetEmoji.drawAt(targetCircle.center);

		// 妨害アイテムを描く
		trapEmoji.drawAt(trapCircle.center);

		// スコアを表示する
		font(U"SCORE: {}"_fmt(score)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });
	}
}
```


## 20.5 残り時間
- ゲームに制限時間を設け、時間切れになるとアイテムをクリックできないようにします

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/click/5.png)

```cpp hl_lines="30-31 35 37-38 40-41 64 75-84"
#include <Siv3D.hpp>

// ランダムな座標を返す関数
Vec2 GetRandomPos()
{
	return{ Random(60, 740), Random(60, 540) };
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クリック目標の絵文字
	const Texture targetEmoji{ U"🍎"_emoji };

	// 妨害アイテムの絵文字
	const Texture trapEmoji{ U"🌶"_emoji };

	// クリック目標の円
	Circle targetCircle{ 400, 300, 60 };

	// 妨害アイテムの円
	Circle trapCircle{ 200, 150, 60 };

	// スコア
	int32 score = 0;

	// 残り時間
	double remainingTime = 10.0;

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		// 残り時間を減らす
		remainingTime -= deltaTime;

		if (0 < remainingTime) // 残り時間がある場合
		{
			// クリック目標がマウスカーソルに重なっていたら
			if (targetCircle.mouseOver() || trapCircle.mouseOver())
			{
				// マウスカーソルを手の形に変更する
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// クリック目標をクリックしたら
			if (targetCircle.leftClicked())
			{
				score += 100;
				targetCircle.center = GetRandomPos();
				trapCircle.center = GetRandomPos();
			}

			// 妨害アイテムをクリックしたら
			if (trapCircle.leftClicked())
			{
				score -= 200;
				targetCircle.center = GetRandomPos();
				trapCircle.center = GetRandomPos();
			}
		}

		// クリック目標を描く
		targetEmoji.drawAt(targetCircle.center);

		// 妨害アイテムを描く
		trapEmoji.drawAt(trapCircle.center);

		// スコアを表示する
		font(U"SCORE: {}"_fmt(score)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });

		if (0 < remainingTime) // 残り時間がある場合
		{
			// 残り時間を表示する
			font(U"TIME: {:.1f}"_fmt(remainingTime)).draw(40, Arg::topRight(760, 40), ColorF{ 0.1 });
		}
		else // 時間切れの場合
		{
			// 時間切れのメッセージを表示する
			font(U"TIME'S UP!").drawAt(60, Vec2{ 400, 300 }, ColorF{ 0.1 });
		}
	}
}
```


## 20.6 ゲームのリセット
- ゲーム終了画面で ++enter++ キーを押すと、スコアと残り時間をリセットしてゲームを再プレイできるようにします

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/click/6.png)

```cpp hl_lines="65-74 94"
#include <Siv3D.hpp>

// ランダムな座標を返す関数
Vec2 GetRandomPos()
{
	return{ Random(60, 740), Random(60, 540) };
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	// クリック目標の絵文字
	const Texture targetEmoji{ U"🍎"_emoji };

	// 妨害アイテムの絵文字
	const Texture trapEmoji{ U"🌶"_emoji };

	// クリック目標の円
	Circle targetCircle{ 400, 300, 60 };

	// 妨害アイテムの円
	Circle trapCircle{ 200, 150, 60 };

	// スコア
	int32 score = 0;

	// 残り時間
	double remainingTime = 10.0;

	while (System::Update())
	{
		const double deltaTime = Scene::DeltaTime();

		// 残り時間を減らす
		remainingTime -= deltaTime;

		if (0 < remainingTime) // 残り時間がある場合
		{
			// クリック目標がマウスカーソルに重なっていたら
			if (targetCircle.mouseOver() || trapCircle.mouseOver())
			{
				// マウスカーソルを手の形に変更する
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// クリック目標をクリックしたら
			if (targetCircle.leftClicked())
			{
				score += 100;
				targetCircle.center = GetRandomPos();
				trapCircle.center = GetRandomPos();
			}

			// 妨害アイテムをクリックしたら
			if (trapCircle.leftClicked())
			{
				score -= 200;
				targetCircle.center = GetRandomPos();
				trapCircle.center = GetRandomPos();
			}
		}
		else // 時間切れの場合
		{
			if (KeyEnter.down())
			{
				score = 0;
				remainingTime = 15.0;
				targetCircle.center = GetRandomPos();
				trapCircle.center = GetRandomPos();
			}
		}

		// クリック目標を描く
		targetEmoji.drawAt(targetCircle.center);

		// 妨害アイテムを描く
		trapEmoji.drawAt(trapCircle.center);

		// スコアを表示する
		font(U"SCORE: {}"_fmt(score)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });

		if (0 < remainingTime) // 残り時間がある場合
		{
			// 残り時間を表示する
			font(U"TIME: {:.1f}"_fmt(remainingTime)).draw(40, Arg::topRight(760, 40), ColorF{ 0.1 });
		}
		else // 時間切れの場合
		{
			// 時間切れのメッセージを表示する
			font(U"TIME'S UP!").drawAt(60, Vec2{ 400, 300 }, ColorF{ 0.1 });
			font(U"Press [Enter] to restart").drawAt(40, Vec2{ 400, 400 }, ColorF{ 0.1 });
		}
	}
}
```



