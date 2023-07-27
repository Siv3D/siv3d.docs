# 18. 動きを作る
動きの表現に役立つ Siv3D の機能を学びます。

## 18.1 経過時間を使ったモーション

#### Scene::Time()
`Scene::Time()` はプログラムが起動されてからの **シーンの経過時間（秒）**を `double` 型の値で返します。この値を使って簡単なモーションを作成できます。

この値は `System::Update()` を呼び出すたびに更新されるため、同一フレーム内での `Scene::Time()` の呼び出しは同じ値を返します。

!!! warning "Scene::Time() はプログラムの実行時間とは異なる"
    `Scene::Time()` が返す値は `Scene::DeltaTime()` の累積です。後述するように `Scene::DeltaTime()` は実際のフレーム経過時間より短い場合があるため、`Scene::Time()` は実際のプログラムの実行時間より短くなる場合があります。正確なプログラムの実行時間を計測するには `Time::GetMillisec()` を使います。ただし、ゲーム内のアニメーションなど、ほとんどの場合は `Scene::Time()` を使うほうが適切です。

#### Scene::DeltaTime()
`Scene::DeltaTime()` は、**直前のフレームからの経過時間 (秒) **（ただし `Scene::GetMaxDeltaTime()` を超える場合は `Scene::GetMaxDeltaTime()` の値）を `double` 型の値で返します。

一般に、直前のフレームからの経過時間が大きすぎると、ゲーム内のアニメーションプログラムや物理シミュレーションの変化ステップが大きくなり、安定性が損なわれる場合があります。そのため、`Scene::DeltaTime()` は `Scene::GetMaxDeltaTime()` の値（デフォルトでは `0.1`）よりも大きくならないよう制限されます。

#### Scene::Center()
`Scene::Center()` はシーンの中心座標を `Point` 型で返します。画面のサイズが 800x600 のときには `Point{ 400, 300 }` を返します。

#### サンプルプログラム
プログラムが起動されてからの時間に基づいて円の半径を変化させるプログラムは次のとおりです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		const double t = Scene::Time();

		// 円の半径が、時間の経過に伴って大きくなる
		Circle{ Scene::Center(), (t * 50) }.draw(ColorF{ 0.25 });
	}
}
```

途中からの経過時間を使いたい場合、`Scene::DeltaTime()` の累積を用いると便利です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	double t = 0.0;

	while (System::Update())
	{
        // 左クリックで経過時間をリセット
        if (MouseL.down())
        {
            t = 0.0;
        }

		// 経過時間を加算
		t += Scene::DeltaTime();

		Circle{ Scene::Center(), (t * 50) }.draw(ColorF{ 0.25 });
	}
}
```


## 18.2 毎フレーム固定値を加算してはいけない
`Scene::Time()` や `Scene::DeltaTime()` を使わなくても、フレームごとに固定の値を足していけばモーションを作れそうですが、それは**大きな間違い**です。

なぜなら、プログラムが実行されるパソコンのモニタのリフレッシュレートによって、メインループが毎秒何回実行されるかが異なるためです。一般的なモニタのリフレッシュレートは 60Hz で、毎秒 60 回メインループが実行されますが、近年は 120Hz や 144Hz, 240Hz など、より高頻度のリフレッシュレートを持つモニタが増えています。

次のように「毎フレーム 3px ずつ移動」するプログラムを実行すると、60Hz のモニタ上では毎秒 180px の速さで移動しますが、120Hz のモニタで実行すると、その倍の毎秒 360px の速さで移動します。もしこれがゲームの敵キャラクターだったら、実行するパソコンによって移動スピードが変わり、ゲームバランスが壊れてしまいます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	double x = 0.0;

	while (System::Update())
	{
		// 毎フレーム 3px 移動（時間ベースでないため不適切！）
		x += 3;

		Circle{ x, 300, 50 }.draw(ColorF{ 0.25 });
	}
}
```

こうした問題を避けるため、モーションはフレームではなく時間をベースに計算する必要があります。上記のコードを時間ベースに直したコードは次のとおりです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

    const double speed = 180.0; // 毎秒 180px 移動

	double x = 0.0;

	while (System::Update())
	{
		x += (Scene::DeltaTime() * speed);

		Circle{ x, 300, 50 }.draw(ColorF{ 0.25 });
	}
}
```


## 18.3 一定時間ごとにイベントを起こす
一定の頻度でイベントを発生させる処理を書くときは、次のようにします。1 秒経過するたびに、円が 100px ずつ右に移動します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/3a.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// 周期（秒）
	const double eventInterval = 1.0;

	// 蓄積時間（秒）
	double accumulatedTime = 0.0;

	double x = 0.0;

	while (System::Update())
	{
		accumulatedTime += Scene::DeltaTime();

		// 蓄積時間が周期を超えたら
		if (eventInterval <= accumulatedTime)
		{
			x += 100.0;

			accumulatedTime -= eventInterval;
		}

		Circle{ x, 300, 50 }.draw(ColorF{ 0.25 });
	}
}
```

イベント周期が非常に短い（1 フレームの時間やそれよりも短い）場合、1 フレームで複数回イベントを発生させる必要が生じます。そのような状況に対処するには、`if` の代わりに `while (eventPeriod <= accumulatedTime)` を使います。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/3b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 周期（秒）
	const double eventInterval = (1.0 / 240.0); // 毎秒 240 回

	// 蓄積時間（秒）
	double accumulatedTime = 0.0;

	int32 count = 0;

	while (System::Update())
	{
		accumulatedTime += Scene::DeltaTime();

		// 蓄積時間が周期を超えたら
		while (eventInterval <= accumulatedTime)
		{
			Print << count++;

			accumulatedTime -= eventInterval;
		}
	}
}
```


## 18.4 ストップウォッチ
`Stopwatch` は、経過時間の計測やリセットを便利に行えるクラスです。`Stopwatch` のコンストラクタ引数で `StartImmediately::Yes` を指定すると、作成と同時に計測を開始します。`Stopwatch::sF()` はその時点での経過時間（秒）を `double` 型で返します。`Stopwatch::restart()` すると、経過時間をリセットして再び 0 から計測を開始（リスタート）します。

`Stopwatch` の経過時間は `Scene::GetMaxDeltaTime()` の影響を受けず、常に実時間で計測されます。同一フレーム内で `Stopwatch::sF()` を複数呼び出すと、時間の経過によって毎回異なる値が返ってきます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// ストップウォッチ（作成と同時に計測を開始する）
	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		// もし左クリックされたら
		if (MouseL.down())
		{
			// ストップウォッチをリセットして再び 0 から始める
			stopwatch.restart();
		}

		// ストップウォッチの経過時間（秒）を double 型で取得する
		const double t = stopwatch.sF();

		Circle{ Scene::Center(), (t * 50) }.draw(ColorF{ 0.25 });
	}
}
```


## 18.5 ストップウォッチの一時停止と再開
ストップウォッチが計測中であるかどうかは `if (Stopwatch::isRunning())` で調べられます。ストップウォッチの計測を一時停止するには `Stopwatch::pause()`, 一時停止を解除して計測を再開するには `Stopwatch::resume()` します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// ストップウォッチ（作成と同時に計測を開始する）
	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		// もし左クリックされたら
		if (MouseL.down())
		{
			// ストップウォッチが計測中なら
			if (stopwatch.isRunning())
			{
				// ストップウォッチを一時停止する
				stopwatch.pause();
			}
			else // ストップウォッチが一時停止中なら
			{
				// ストップウォッチを再開する
				stopwatch.resume();
			}
		}

		// ストップウォッチの経過時間（秒）を double 型で取得する
		const double t = stopwatch.sF();

		Circle{ Scene::Center(), (t * 50) }.draw(ColorF{ 0.25 });
	}
}
```


## 18.6 周期的なモーション
周期的に移動・点滅・拡大縮小するようなモーションを作るときには、`Periodic::` 名前空間に用意されている周期関数を使うと便利です。これらの関数は、指定した周期で 0～1, あるいは -1～1 の範囲の値を返します。周期は `2s` (2 秒) や `0.5s` (0.5 秒) のように時間リテラル `s` を使って指定します。経過時間は第 2 引数で指定でき、デフォルトでは `Scene::Time()` が使われます。

=== "0～1 の周期関数"
	| 周期関数 | 動き |
	|--|--|
	|`Periodic::Square0_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/sq01.png"></div>|
	|`Periodic::Triangle0_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/tri01.png"></div>|
	|`Periodic::Sine0_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/sin01.png"></div>|
	|`Periodic::Sawtooth0_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/saw01.png"></div>|
	|`Periodic::Jump0_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/jum01.png"></div>|

	#### Periodic::Square0_1()
	指定した周期で 0.0 か 1.0 を交互に返す関数です。周期の前半では 1.0 を、残りの半分では 0.0 を返します。

	#### Periodic::Triangle0_1()
	0.0 から一定の速度で値が大きくなって 1.0 に、そして一定の速度で小さくなって 0.0 に、という変化を指定した周期で繰り返す関数です。

	#### Periodic::Sine0_1()
	指定した周期で、0.0～1.0 の範囲で正弦波（サインカーブ）を描く数値の変化を返す関数です。

	#### Periodic::Sawtooth0_1()
	指定した周期で、0.0 → 1.0 への変化を繰り返す関数です。

	#### Periodic::Jump0_1()
	指定した周期で、地面からジャンプしたときの速度のような数値変化を繰り返す関数です。

	#### サンプルコード

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/6.png)

	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(Palette::White);

		while (System::Update())
		{
			const double p0 = Periodic::Square0_1(2s);
			const double p1 = Periodic::Triangle0_1(2s);
			const double p2 = Periodic::Sine0_1(2s);
			const double p3 = Periodic::Sawtooth0_1(2s);
			const double p4 = Periodic::Jump0_1(2s);

			Line{ 100, 0, 100, 600 }.draw(2, ColorF{ 0.8 });
			Line{ 700, 0, 700, 600 }.draw(2, ColorF{ 0.8 });

			Circle{ (100 + p0 * 600), 100, 20 }.draw(ColorF{ 0.25 });
			Circle{ (100 + p1 * 600), 200, 20 }.draw(ColorF{ 0.25 });
			Circle{ (100 + p2 * 600), 300, 20 }.draw(ColorF{ 0.25 });
			Circle{ (100 + p3 * 600), 400, 20 }.draw(ColorF{ 0.25 });
			Circle{ (100 + p4 * 600), 500, 20 }.draw(ColorF{ 0.25 });
		}
	}
	```

=== "-1～1 の周期関数"
	| 周期関数 | 動き |
	|--|--|
	|`Periodic::Square1_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/sq11.png"></div>|
	|`Periodic::Triangle1_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/tri11.png"></div>|
	|`Periodic::Sine1_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/sin11.png"></div>|
	|`Periodic::Sawtooth1_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/saw11.png"></div>|
	|`Periodic::Jump1_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/jum11.png"></div>|

	#### Periodic::Square1_1()
	指定した周期で -1.0 か 1.0 を交互に返す関数です。周期の前半では 1.0 を、残りの半分では -1.0 を返します。

	#### Periodic::Triangle1_1()
	-1.0 から一定の速度で値が大きくなって 1.0 に、そして一定の速度で小さくなって -1.0 に、という変化を指定した周期で繰り返す関数です。

	#### Periodic::Sine1_1()
	指定した周期で、-1.0～1.0 の範囲で正弦波（サインカーブ）を描く数値の変化を返す関数です。

	#### Periodic::Sawtooth1_1()
	指定した周期で、-1.0 → 1.0 への変化を繰り返す関数です。

	#### Periodic::Jump1_1()
	指定した周期で、地面からジャンプしたときの速度のような数値変化を繰り返す関数です。

	#### サンプルコード

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/6.png)

	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(Palette::White);

		while (System::Update())
		{
			const double p0 = Periodic::Square1_1(2s);
			const double p1 = Periodic::Triangle1_1(2s);
			const double p2 = Periodic::Sine1_1(2s);
			const double p3 = Periodic::Sawtooth1_1(2s);
			const double p4 = Periodic::Jump1_1(2s);

			Line{ 100, 0, 100, 600 }.draw(2, ColorF{ 0.8 });
			Line{ 700, 0, 700, 600 }.draw(2, ColorF{ 0.8 });

			Circle{ (400 + p0 * 300), 100, 20 }.draw(ColorF{ 0.25 });
			Circle{ (400 + p1 * 300), 200, 20 }.draw(ColorF{ 0.25 });
			Circle{ (400 + p2 * 300), 300, 20 }.draw(ColorF{ 0.25 });
			Circle{ (400 + p3 * 300), 400, 20 }.draw(ColorF{ 0.25 });
			Circle{ (400 + p4 * 300), 500, 20 }.draw(ColorF{ 0.25 });
		}
	}
	```


## 18.7 トランジション
値を少しずつ増加させて最大値に到達させる。そこから徐々に減少させて最小値に戻す、という挙動をプログラムするときには `Transition` クラスを使うと便利です。

`Transition` のコンストラクタには、最小値から最大値に増加するまでの所要時間と、最大値から最小値に減少するまでの所要時間を設定します。あとは毎フレーム、`Transition::update()` に、増加の場合は `true` を、減少の場合は `false` を渡せば、設定された速度で値が増加、あるいは減少します。`Transition::value()` で現在の値を取得できます。

次のサンプルでは、マウスの左ボタンが押されていると円が大きくなり、離されていると小さくなります。最大は 200 ピクセル、最小は 0 ピクセルとしています。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// 2.0 秒かけて 0.0 から 1.0 になる速度で増加し
	// 0.5 秒かけて 1.0 から 0.0 になる速度で減少するトランジション
	Transition transition{ 2.0s, 0.5s };

	while (System::Update())
	{
		if (MouseL.pressed())
		{
			// マウスの左ボタンが押されていたら増加
			transition.update(true);
		}
		else
		{
			// 押されていなかったら減少
			transition.update(false);
		}

		const double t = transition.value();

		Circle{ Scene::Center(), (t * 200) }.draw(ColorF{ 0.25 });
	}
}
```

`MouseL.pressed()` は `bool` 型の値を返すため、上記のコードは次のように短くできます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// 2.0 秒かけて 0.0 から 1.0 になる速度で増加し
	// 0.5 秒かけて 1.0 から 0.0 になる速度で減少するトランジション
	Transition transition{ 2.0s, 0.5s };

	while (System::Update())
	{
		// マウスの左ボタンが押されていたら増加、押されていなかったら減少
		transition.update(MouseL.pressed());

		const double t = transition.value();

		Circle{ Scene::Center(), (t * 200) }.draw(ColorF{ 0.25 });
	}
}
```


## 18.8 線形補間
ある状態 A と B があり、その間を `t` という値で補間したいときには `A.lerp(B, t)` を使います。`t` は 0.0 ～ 1.0 の値です。

次のようなクラスがメンバ関数 `.lerp()` を持っています。

- 色
	- `ColorF`, `HSV`
- ベクトル
	- `Point`, `Vec2`, `Vec3`, `Vec4`
- 図形
	- `Line`, `Circle`, `Rect`, `RectF`, `Triangle`, `Quad`, `Ellipse`, `RoundRect`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		const double t = Periodic::Triangle0_1(3s);

		RectF{ 200, 50, 400, 80 }.draw(HSV{ 180.0, 0.5, 1.0 }.lerp(HSV{ 240.0, 1.0, 1.0 }, t));

		Circle{ 100, 200, 20 }.lerp(Circle{ 700, 200, 40 }, t).draw(ColorF{ 0.25 });

		RectF{ Arg::center(100, 300), 80 }.lerp(RectF{ Arg::center(700, 300), 40 }, t).draw(ColorF{ 0.25 });

		Triangle{ 100, 400, 100, 0_deg }.lerp(Triangle{ 700, 400, 100, 120_deg }, t).draw(ColorF{ 0.25 });

		Line{ 50, 450, 150, 550 }.lerp(Line{ 750, 450, 650, 550 }, t).draw(2, ColorF{ 0.25 });
	}
}
```


## 18.9 イージング
0.0 から 1.0 に一定の速度で値を増加させるだけでは単調な動きになってしまいます。はじめは少しずつ加速し、ゴールに近づくとゆっくりになるといったように、速度に変化を与えると、洗練された視覚効果を実現できます。0.0 → 1.0 の単調増加を、このような特徴的なカーブに変換できる **イージング関数** を使うと、モーションの印象を改善できます。

イージング関数は全部で約 30 種類用意されています。一覧は [Easing Functions Cheat Sheet :material-open-in-new:](https://easings.net/){:target="_blank"} で確認できます。次のサンプルでは `EaseInOutExpo()` を使っています。ほかにも `EaseOutBounce()` や `EaseInOutBack()` など様々なイージング関数を試してみましょう。

次のサンプルに登場する`Min()` は、渡された引数のうち最小値を返す関数です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// スタート位置
	Vec2 from{ 100, 100 };

	// ゴール位置
	Vec2 to{ 700, 500 };

	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		// 移動の割合 0.0～1.0
		const double t = Min(stopwatch.sF(), 1.0);

		// イージング関数を適用
		const double e = EaseInOutExpo(t);

		// スタート位置からゴール位置へ e の割合だけ進んだ位置
		const Vec2 pos = from.lerp(to, e);

		if (MouseL.down())
		{
			// スタート位置を現在の位置に
			from = pos;

			// ゴール位置をマウスカーソルの位置に
			to = Cursor::Pos();

			stopwatch.restart();
		}

		pos.asCircle(40).draw(ColorF{ 0.25 });
		to.asCircle(50).drawFrame(5, ColorF{ 0.25 });
	}
}
```


## 18.10 目標に向かってなめらかに移動・変化する
線形補間とイージングは、開始位置と終了位置が固定されている場合に使います。線形補間やイージングの最中に終了位置が変更された場合、移動の速さや方向が急に変化して不自然な印象を与えてしまいます。

終了位置が変化しても、なめらかに移動・変化し続けるには、`Math::SmoothDamp` 関数を使います。`Math::SmoothDamp` 関数は、現在位置と目標位置、そして現在の速度から、時間ベースで次の位置を計算する、**非常に便利で強力な補間関数**です。

次のような型が `Math::SmoothDamp` 関数に対応しています。

- 数値
	- `float`, `double`
- ベクトル
	- `Vec2`, `Vec3`, `Vec4`
- 色
	- `ColorF`

#### 関数の概要

`Vec2` 用の `Math::SmoothDamp` 関数の概要とサンプルコードは次のとおりです。

```cpp
Vec2 Math::SmoothDamp(const Vec2& from, const Vec2& to, Vec2& velocity, double smoothTime, const Optional<double>& maxSpeed = unspecified, double deltaTime = Scene::DeltaTime());`
```

- `from`: 現在位置
- `to`: 目標位置
- `velocity`: 現在の速度（速度を保存している変数を参照で渡す）
- `smoothTime`: 平滑化時間（最大速度で目標に向かうときに期待される所要時間）。動く目標を追いかけるときの遅延時間で、小さいと目標に早く到達する
- `maxSpeed`: 最大速度。無制限の場合は `unspecified` を指定する
- `deltaTime`: 前回からの経過時間（デフォルトは `Scene::DeltaTime()`）
- 戻り値: 次の位置

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/motion/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// 現在位置
	Vec2 pos{ 400, 300 };

	// 速度
	Vec2 velocity{ 0, 0 };

	while (System::Update())
	{
		const Vec2 target = Cursor::Pos();

		pos = Math::SmoothDamp(pos, target, velocity, 0.5);

		pos.asCircle(40).draw(ColorF{ 0.25 });

		target.asCircle(50).drawFrame(4, ColorF{ 0.25 });
	}
}
```
