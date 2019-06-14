
# 3. 動きを作る

この章では、「動き」の表現に役立つ Siv3D の機能を学びます。

## 3.1 経過時間を使ったアニメーション

### Scene::Time()
`Scene::Time()` はプログラムが起動されてからの経過時間（秒）を `double` 型の値で返します。この値を使って簡単なアニメーションを作成できます。

### Scene::Center()
画面の中心座標を返す関数です。画面のサイズが 800x600 のときには `Point(400, 300)` を返します。

<video src="../images/3010.mp4" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		const double t = Scene::Time();

		// 円の半径が、時間の経過に伴って大きくなる
		Circle(Scene::Center(), t * 50).draw(ColorF(0.25));
	}
}
```

### step
Siv3D に用意されている、ループを短く書ける機能です。`for (auto i : step(N))` は `for (int i = 0; i < N; ++n)`と同じ働きです。

<video src="../images/3011.mp4" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		const double t = Scene::Time();

		for (auto i : step(12))
		{
			// 長方形が、時間の経過に伴って横に大きくなる
			RectF(-50 * i, i * 50, t * 100, 50).draw(ColorF(0.25));
		}
	}
}
```

### OffsetCircular
円周に沿った動きをつくるのに最適な円座標クラスです。オフセット `offset` と円座標系の動径座標 `r`, 角度座標 θ (`theta`) の 3 つの要素で位置を表現します。シーン上の座標 `offset` を中心とする半径 `r` の円を考え、その円周上で 12 時の方向を 0° として時計回りに `theta` の位置が `OffsetCircular(offset, r, theta)` です。

![](images/3012.png)

![](images/3013.gif)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	// 中心位置のオフセット
	const Vec2 center = Scene::Center();
	
	// 円座標系における動径座標
	constexpr double r = 200.0;

	while (System::Update())
	{
		const double t = Scene::Time();

		for (auto i : step(12))
		{
			// 円座標系における角度座標
			const double theta = i * 30_deg + t * 30_deg;

			const Vec2 pos = OffsetCircular(center, r, theta);

			Circle(pos, 20).draw(ColorF(0.25));
		}
	}
}
```

## 3.2 ストップウォッチ
`Scene::Time()` の欠点は、時間を止めることやリセットができないことです。好きなタイミングで経過時間を測定するには `Stopwatch` クラスを使います。

### Stopwatch の経過時間とリスタート

`Stopwatch` のコンストラクタ引数に `true` を渡すと、作成と同時に計測を開始します。`Stopwatch::sF()` はその時点での経過時間（秒）を `double` 型で返します。`Stopwatch::restart()` すると、経過時間をリセットして再び 0 から計測を開始（リスタート）します。

### MouseL.down()
マウスの左ボタンがクリック（タッチディスプレイの場合は画面がタッチ）されたかを、`if (MouseL.down())` で調べられます。次のサンプルでは、画面上をマウスでクリックするたびに `Stopwatch` をリスタートします。

<video src="../images/3020.mp4" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Vec2 center = Scene::Center();

	// ストップウォッチ（作成と同時に計測開始）
	Stopwatch stopwatch(true);

	while (System::Update())
	{
		// もし左クリックされたら
		if (MouseL.down())
		{
			// ストップウォッチをリセットして再び 0 から計測
			stopwatch.restart();
		}

		// ストップウォッチの経過時間（秒）を double 型で取得 
		const double t = stopwatch.sF();

		// シーンの中心の円の半径が、時間の経過に伴って大きくなる
		Circle(center, t * 50).draw(ColorF(0.25));
	}
}
```

### Stopwatch の一時停止と再開

ストップウォッチが計測中かどうかは `if (Stopwatch::isRunning())` で調べられます。ストップウォッチの計測を一時停止するには `Stopwatch::pause()`, 一時停止を解除して計測を再開するには `Stopwatch::resume()` します。

<video src="../images/3021.mp4" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	const Vec2 center = Scene::Center();

	// ストップウォッチ（作成と同時に計測開始）
	Stopwatch stopwatch(true);

	while (System::Update())
	{
		if (MouseL.down())
		{
			// ストップウォッチが計測中なら
			if (stopwatch.isRunning())
			{
				// ストップウォッチを一時停止
				stopwatch.pause();
			}
			else // ストップウォッチが一時停止中なら
			{
				// ストップウォッチを再開
				stopwatch.resume();
			}
		}

		// ストップウォッチの経過時間（秒）を double 型で取得 
		const double t = stopwatch.sF();

		Circle(center, 120).drawArc(t * 140_deg, 240_deg, 60, 0, ColorF(0.4));

		Circle(center, 180).drawArc(t * 90_deg, 160_deg, 60, 0, ColorF(0.6));

		Circle(center, 240).drawArc(t * 50_deg, 120_deg, 60, 0, ColorF(0.8));
	}
}
```

## 3.3 周期的なアニメーション
Siv3D で周期的に移動・点滅・拡大縮小するようなアニメーションを作るときには、`Periodic::` 名前空間に用意されている関数群を使うと便利です。

### Periodic::Square0_1()
指定した周期で 0.0 か 1.0 を交互に返す関数です。周期は `2s` (2 秒) や `0.5s` (0.5 秒) のように時間リテラルを使って記述します。周期の前半では 1.0 を、残りの半分では 0.0 を返します。

<video src="../images/3030.mp4" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.25));

	while (System::Update())
	{
		// 2 秒周期（1 秒点灯、1 秒消灯）で明滅を繰り返す
		if (Periodic::Square0_1(2s))
		{
			Circle(Scene::Center(), 200).draw();
		}
	}
}
```

### Periodic::Tringle0_1()
0.0 から一定の速度で値が大きくなって 1.0 に、そして一定の速度で小さくなって 0.0 に、という変化を指定した周期で繰り返す関数です。

![](images/3031.gif)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.25));

	while (System::Update())
	{
		// 2 秒周期で、一定速度での左右移動を繰り返す
		const double x = 50 + 700 * Periodic::Tringle0_1(2s);
		
		Circle(x, 300, 50).draw();
	}
}
```

### Periodic::Sine0_1()

```C++


```

### Periodic::Sawtooth0_1()

```C++


```

### Periodic::Jump0_1()

```C++


```

## 3.4 イージング




## 3.5 トランジション


