# 12. 変数と動き
時間の経過を使って変数を変化させてモーション（動き）を作る方法を学びます。

## 12.1 前フレームからの経過時間を調べる
`Scene::DeltaTime()` は、前フレームからの経過時間（秒）を `double` 型で返します。モニタが 60Hz の場合は約 0.0166 です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/motion/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// 60 Hz の場合, 1/60 秒（約 0.0166）
		const double deltaTime = Scene::DeltaTime();

		Print << deltaTime;
	}
}
```

## 12.2 絵文字を動かす
メインループの繰り返しのたびに位置をずらすことで、移動のモーションができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/motion/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"☃️"_emoji };

	// 移動速度（ピクセル / 秒）
	const double velocity = 20;

	// 絵文字の X 座標
	double x = 100;

	while (System::Update())
	{
		x += (Scene::DeltaTime() * velocity);

		emoji.drawAt(x, 300);
	}
}
```


## 12.3 絵文字を回転させる
メインループの繰り返しのたびに回転角度をずらすことで、回転のモーションができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/motion/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Texture emoji{ U"🍣"_emoji };

	// 回転速度（ラジアン / 秒）
	const double angularVelocity = 90_deg;

	// 回転角度
	double angle = 0_deg;

	while (System::Update())
	{
		angle += (Scene::DeltaTime() * angularVelocity);

		emoji.rotated(angle).drawAt(400, 300);
	}
}
```

## 12.4 図形の変数
`Circle` 型や `Rect`, `RectF` 型の変数を作れます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/motion/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	Circle circle{ 200, 200, 100 };

	RectF rect{ 400, 300, 300, 200 };

	while (System::Update())
	{
		circle.draw(Palette::Orange);

		circle.drawFrame(2, 2, Palette::Red);

		rect.draw(ColorF{ 0.5 });

		RectF{ rect.x, rect.y, (rect.w * 0.5), rect.h }.draw(ColorF{ 0.3, 0.9, 0.6 });

		rect.drawFrame(4, 4, ColorF{ 0.2 });
	}
}
```

## 12.5 図形を動かす
図形のメンバ変数を時間の経過に応じて変更することで、図形の位置や大きさをアニメーションできます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/motion/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	Circle circle{ 200, 300, 0 };

	RectF rect{ 300, 200, 300, 200 };

	while (System::Update())
	{
		double deltaTime = Scene::DeltaTime();

		circle.r += (deltaTime * 20);

		rect.x += (deltaTime * 10);

		circle.draw();

		rect.draw(ColorF{ 0.5 });
	}
}
```


## 12.6 経過時間を蓄積する
前フレームからの経過時間を蓄積することで、時間を測定できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/motion/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	// 経過時間の蓄積（秒）
	double accumulatedTime = 0.0;

	while (System::Update())
	{
		accumulatedTime += Scene::DeltaTime();

		font(U"経過時間: {:.2f}"_fmt(accumulatedTime)).draw(40, 20, 20, Palette::Black);
	}
}
```


## 12.7 残り時間をカウントダウンする
残り時間から `Scene::DeltaTime()` の値を引いていくことで、時間のカウントダウンができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/motion/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

	// 残り時間（秒）
	double timeLeft = 5.0;

	while (System::Update())
	{
		timeLeft -= Scene::DeltaTime();

		if (0.0 < timeLeft)
		{
			font(U"残り時間: {:.2f}"_fmt(timeLeft)).draw(40, 20, 20, Palette::Black);
		}
		else
		{
			font(U"ゲームオーバー").draw(40, 20, 20, Palette::Black);
		}
	}
}
```


## 12.8 一定時間ごとにイベントを発生させる
12.6 を応用すると、一定時間ごとにイベントを発生させることができます。蓄積時間が一定時間を超えたらイベントを発生させ、蓄積時間をその時間だけ減らします。

次のコードでは、3 秒ごとに `Print << U"Hello!"` を実行します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/motion/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ FontMethod::MSDF, 48 };

    // 周期（秒）
    const double interval = 3.0;

    // 蓄積時間（秒）
	double accumulatedTime = 0.0;

	while (System::Update())
	{
		accumulatedTime += Scene::DeltaTime();

		font(U"accumulatedTime: {:.2f}"_fmt(accumulatedTime)).draw(40, 200, 20, Palette::Black);

		// 蓄積時間が一定時間を超えたら
		if (interval <= accumulatedTime)
		{
			Print << U"Hello!";

			// 蓄積時間からマイナス
			accumulatedTime -= interval;
		}
	}
}
```

!!! info "蓄積時間を完全に 0 にリセットしない理由"
	蓄積時間を `accumulatedTime = 0.0 ` でリセットしないのは、例えば頻度が 3 秒ごとで、蓄積時間が 3.02 秒だった場合、イベントを発生させたあとに、余りの 0.02 秒を次のイベントの蓄積時間に使うためです。これを無視してしまうと、イベントの発生頻度が 3 秒よりも長くなってしまいます。


## 振り返りチェックリスト
- [x] `Scene::DeltaTime()` で前フレームからの経過時間（秒）を取得できることを学んだ
- [x] 経過時間を使って絵文字を動かす方法を学んだ
- [x] 経過時間を使って図形を動かす方法を学んだ
- [x] 経過時間を蓄積して時間を測定する方法を学んだ
- [x] 経過時間を利用して残り時間をカウントダウンする方法を学んだ
- [x] 経過時間の蓄積を利用して、一定時間ごとにイベントを発生させる方法を学んだ
