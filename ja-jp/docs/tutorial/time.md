# 19. 時間を扱う
「前フレームからの経過時間」を使って時間管理をする方法を学びます。

## 19.1 経過時間の計測
- `Scene::DeltaTime` は、前フレームからの経過時間（秒）を `double` 型で返します
- この経過時間を加算することで、プログラム開始からの経過時間（秒）を計測できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/time/1.png)

```cpp hl_lines="10 15"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	// プログラム開始からの経過時間（秒）
	double time = 0.0;

	while (System::Update())
	{
		// 前フレームからの経過時間（秒）
		const double deltaTime = Scene::DeltaTime();

		// time に deltaTime を加算する
		time += deltaTime;

		// time を表示する
		font(U"time: {:.2f}"_fmt(time)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });

		// deltaTime を表示する
		font(U"deltaTime: {:.4f}"_fmt(deltaTime)).draw(40, Vec2{ 40, 100 }, ColorF{ 0.1 });
	}
}
```

- 経過時間の計測に便利なクラス `Stopwatch` については、**チュートリアル 30** で詳しく説明します


## 19.2 残り時間のカウントダウン
- **19.1** を応用して、残り時間（秒）を 10 からカウントダウンするプログラムを作成します
- 残り時間が 0 になったら「Time's up!」と表示します
- ++enter++ を押すと、残り時間をリセットして再びカウントダウンを始めるようにします

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/time/2.png)

```cpp title="10 秒カウントダウン" hl_lines="10 13 21"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	// カウントダウン時間（秒）
	const double countdownTime = 10.0;

	// 残り時間（秒）
	double remainingTime = countdownTime;

	while (System::Update())
	{
		// 前フレームからの経過時間（秒）
		const double deltaTime = Scene::DeltaTime();

		// 残り時間を減らす
		remainingTime -= deltaTime;

		if (0.0 < remainingTime) // 残り時間がある場合
		{
			font(U"time: {:.2f}"_fmt(remainingTime)).draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });
		}
		else // 残り時間がなくなった場合
		{
			font(U"Time's up!").draw(40, Vec2{ 40, 40 }, ColorF{ 0.1 });
			font(U"Press Enter to restart").draw(30, Vec2{ 40, 100 }, ColorF{ 0.1 });

			// Enter キーを押したら
			if (KeyEnter.down())
			{
				// 残り時間をリセットして再スタート
				remainingTime = countdownTime;
			}
		}
	}
}
```

- 残り時間のカウントダウンに便利なクラス `Timer` については、**チュートリアル 30** で詳しく説明します


## 19.3 一定時間おきに何かをする（1）
- 時間を蓄積し、一定の時間がたまったら何かをするプログラムを作成します
- イベントの周期を決めておき、蓄積時間（秒）がその周期（秒）を超えたらイベントを発生させます
- イベントを発生させたあとに、蓄積時間からその周期を引きます
- 次のコードでは、2 秒ごとにレベルを上げていきます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/time/3.png)

```cpp title="2 秒ごとにレベルを上げる" hl_lines="10 13 24 27 33"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	// レベルが上がる周期（秒）
	const double levelUpInterval = 2.0;

	// 蓄積時間（秒）
	double accumulatedTime = 0.0;

	// レベル
	int32 level = 0;

	while (System::Update())
	{
		// 前フレームからの経過時間（秒）
		const double deltaTime = Scene::DeltaTime();

		// 蓄積時間を増やす
		accumulatedTime += deltaTime;

		// 蓄積時間が周期を超えたら
		if (levelUpInterval < accumulatedTime)
		{
			// レベルを上げる
			++level;

			// 蓄積時間を周期分減らす
			accumulatedTime -= levelUpInterval;

			Print << U"Level up!";
		}

		// 現在のレベルを表示する
		font(U"Level: {}"_fmt(level)).draw(40, Vec2{ 200, 40 }, ColorF{ 0.1 });

		// 蓄積時間を表示する
		font(U"accumulatedTime: {:.2f}"_fmt(accumulatedTime)).draw(30, Vec2{ 200, 100 }, ColorF{ 0.1 });
	}
}
```


## 19.4 一定時間おきに何かをする（2）
- **19.3** では、プログラムを起動したあと、初回のイベントの発生まで待つ必要がありました
- 起動後直ちに初回のイベントを発生させたい場合は、蓄積時間の初期値を周期と同じ値に増やしておきます
- 次のコードでは、開始直後とそれ以降 2 秒おきに敵の数が増えていきます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/time/4.png)

```cpp title="開始直後と、以降 2 秒ごとに敵の数を増やす" hl_lines="13"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48 };

	// 敵が増える周期（秒）
	const double levelUpInterval = 2.0;

	// 蓄積時間（秒）
	double accumulatedTime = levelUpInterval;

	// 敵の数
	int32 enemyCount = 0;

	while (System::Update())
	{
		// 前フレームからの経過時間（秒）
		const double deltaTime = Scene::DeltaTime();

		// 蓄積時間を増やす
		accumulatedTime += deltaTime;

		// 蓄積時間が周期を超えたら
		if (levelUpInterval < accumulatedTime)
		{
			// 敵の数を増やす
			++enemyCount;

			// 蓄積時間を周期分減らす
			accumulatedTime -= levelUpInterval;

			Print << U"Enemy appeared!";
		}

		// 現在の敵の数を表示する
		font(U"Enemy count:	{}"_fmt(enemyCount)).draw(40, Vec2{ 200, 40 }, ColorF{ 0.1 });

		// 蓄積時間を表示する
		font(U"accumulatedTime: {:.2f}"_fmt(accumulatedTime)).draw(30, Vec2{ 200, 100 }, ColorF{ 0.1 });
	}
}
```

## 振り返りチェックリスト
- [x] `Scene::DeltaTime` は、前フレームからの経過時間（秒）を `double` 型で返すことを学んだ
- [x] 経過時間を加算することで、経過時間（秒）を計測できることを学んだ
- [x] 残り時間をカウントダウンする方法を学んだ
- [x] 一定時間ごとに何かをする方法を学んだ
