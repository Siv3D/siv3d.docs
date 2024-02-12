# 勉強会 9 コマ目（ゲーム開発）

### 1. カウンター
絵文字をクリックするとカウントが増えるアプリです。

- カウントをリセットする操作を追加してみましょう。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/13-8.1.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"🛎️"_emoji };

	const Font font{ FontMethod::MSDF, 48 };

    // 押した回数
	int32 count = 0;

	while (System::Update())
	{
		if (Circle{ 500, 300, 70 }.leftClicked())
		{
			++count;
		}

		font(U"{} 回"_fmt(count)).draw(50, Vec2{ 200, 300 }, Palette::Black);

		Circle{ 500, 300, 70 }.draw(ColorF{ 1.0, 1.0, 1.0, 0.5 });

		emoji.drawAt(500, 300);
	}
}
```


### 2. クッキークリック
クッキーをたくさんクリックするゲームです。

- クリックしてはいけないアイテムを追加で登場させてみましょう。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/sophia/image/13-8.2.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"🍪"_emoji };

	const Font font{ FontMethod::MSDF, 48 };

	int32 score = 0;

    // クッキーの座標
	Vec2 cookiePos{ 400, 300 };

	while (System::Update())
	{
		if (Circle{ cookiePos, 70 }.leftClicked())
		{
			score += 100;
			cookiePos = RandomVec2(Rect{ 50, 50, 700, 500 });
		}

		emoji.drawAt(cookiePos);

		font(U"スコア: {}"_fmt(score)).draw(50, Vec2{ 50, 50 }, Palette::Black);
	}
}
```


### 3. より本格的なゲーム
余裕のある方は、[16. クッキークリッカー風のゲームを作る](../tutorial/cookie-clicker.md){:target="_blank"} に挑戦してみましょう。
