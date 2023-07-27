# 30. 日付と時刻、時間
時間の計測や、日付、時刻に関する機能を学びます。

## 30.1 シーンの経過時間の取得

#### Scene::DeltaTime()
`Scene::DeltaTime()` は、**直前のフレームからの経過時間 (秒) **（ただし `Scene::GetMaxDeltaTime()` を超える場合は `Scene::GetMaxDeltaTime()` の値）を `double` 型の値で返します。モニタが 60Hz の場合は約 0.0166 になりますが、実行環境やプログラムの負荷によって変化します。

この値は `System::Update()` が呼ばれるたびに更新されます。同一フレーム内での `Scene::DeltaTime()` の呼び出しは同じ値を返します。

#### Scene::Time()
`Scene::Time()` はプログラムが起動されてからの **シーンの経過時間（秒）**を `double` 型の値で返します。シーンの経過時間とは、`Scene::DeltaTime()` の累積のことです。

この値は `System::Update()` が呼ばれるたびに更新されます。同一フレーム内での `Scene::Time()` の呼び出しは同じ値を返します。

#### Scene::GetMaxDeltaTime()
一般に、直前のフレームからの経過時間が大きすぎると、ゲーム内のアニメーションプログラムや物理シミュレーションの変化ステップが大きくなり、安定性が損なわれる場合があります。そのため、`Scene::DeltaTime()` は `Scene::GetMaxDeltaTime()` の値（デフォルトでは `0.1`）よりも大きくならないよう制限されます。

#### Scene::SetMaxDeltaTime(t)
`Scene::SetMaxDeltaTime(t)` は、`Scene::DeltaTime()` が返す値の最大値（秒）を設定します。`Scene::DeltaTime()` が返す値は、`Scene::SetMaxDeltaTime()` で設定した値よりも大きくなりません。


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

        Print << Scene::GetMaxDeltaTime();

		Print << Scene::Time();

		Print << Scene::DeltaTime();
	}
}
```


## 30.2 アプリケーションの起動時間の取得
`Scene::GetMaxDeltaTime()` に影響されない、厳密なアプリケーションの起動からの経過時間を取得するには次の関数を使います。戻り値は `uint64` 型です。

| 関数 | 戻り値の値 |
|:---|:---|
| `Time::GetSec()` | 秒 |
| `Time::GetMillisec()` | ミリ秒 |
| `Time::GetMicrosec()` | マイクロ秒 |
| `Time::GetNanosec()` | ナノ秒 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		Print << Time::GetSec();

		Print << Time::GetMillisec();

		Print << Time::GetMicrosec();

		Print << Time::GetNanosec();
	}
}
```


## 30.3 UNIX 時間の取得
UTC 時刻における 1970 年 1 月 1 日 午前 0 時 0 分 0 秒（UNIX エポック）からの経過時間は、次の関数を使って取得できます。戻り値は `uint64` 型です。

| 関数 | 戻り値の値 |
|:---|:---|
| `Time::GetSecSinceEpoch()` | 秒 |
| `Time::GetMillisecSinceEpoch()` | ミリ秒 |
| `Time::GetMicrosecSinceEpoch()` | マイクロ秒 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		Print << Time::GetSecSinceEpoch();

		Print << Time::GetMillisecSinceEpoch();

		Print << Time::GetMicrosecSinceEpoch();
	}
}
```


## 30.4 時間型
時間を表現する型が次のように用意されています。`F` の付く型は浮動小数点数で値を保持します。`SecondsF` 型のエイリアスとして `Duration` 型があります。

| 型 | 表現する時間 |
|--|--|
| `Days` または `DaysF` | 日 |
| `Hours` または `HoursF` | 時 |
| `Minutes` または `MinutesF` | 分 |
| `Seconds` または `SecondsF` | 秒 |
| `Milliseconds` または `MillisecondsF` | ミリ秒 |
| `Microseconds` または `MicrosecondsF` | マイクロ秒 |
| `Nanoseconds` または `NanosecondsF` | ナノ秒 |
| `Duration` | `SecondsF` と同じ |

整数や浮動小数点数リテラルに、次のようなサフィックスを付けて時間型を簡単に作成できます。

| サフィックス | 時間 |
|--|--|
|`_d` | 日 |
| `h` | 時 |
| `min` | 分 |
| `s` | 秒 |
| `ms` | ミリ秒 |
| `us` | マイクロ秒 |
| `ns` | ナノ秒 |

時間を表現する型は相互に変換可能です。浮動小数点数で表現する時間型から整数で表現する時間型への変換には `DurationCast<Type>()` が必要です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Hours t0{ 5 };
	Print << t0;
	Print << t0.count(); // 数値型として値を取得するには .count()

	const MinutesF t1 = (1h + 30min + 180s);
	Print << t1;
	Print << t1.count();

	const Duration t2 = (10min + 5.5s);
	Print << t2;
	Print << t2.count();

	const Seconds t3 = DurationCast<Seconds>(t2);
	Print << t3;
	Print << t3.count();

	while (System::Update())
	{

	}
}
```


## 30.5 Stopwatch クラス
`Stopwatch` は、経過時間の計測やリセットを便利に行えるクラスです。`Stopwatch` のコンストラクタ引数で `StartImmediately::Yes` を指定すると、作成と同時に計測を開始します。`Stopwatch::sF()` はその時点での経過時間（秒）を `double` 型で返します。`Stopwatch::restart()` すると、経過時間をリセットして再び 0 から計測を開始（リスタート）します。主なメンバ関数は次のとおりです。

| メンバ関数 | 説明 |
|--|--|
| `.isStarted()` | ストップウォッチが動作中であるかを示します（開始後の一時停止も動作中に含みます）。 |
| `.isPaused()` | ストップウォッチが一時停止中であるかを示します。 |
| `.isRunning()` | ストップウォッチが動作中であるかを示します。 |
| `.start()` | ストップウォッチを開始・再開します。 |
| `.pause()` | ストップウォッチを一時停止します。 |
| `.resume()` | ストップウォッチが一時停止中である場合、再開します。 |
| `.reset()` | ストップウォッチを停止し、経過時間を 0 にリセットします。 |
| `.restart()` | ストップウォッチを停止し、経過時間を 0 にリセットしてから再開します。 |    
| `.set(t)` | 経過時間を `t` に設定します。 |
| `.d()` | 経過時間（日）を返します。 |
| `.dF()` | 経過時間（日）を `double` 型で返します。 |
| `.h()` | 経過時間（時）を返します。 |
| `.hF()` | 経過時間（時）を `double` 型で返します。 |
| `.min()` | 経過時間（分）を返します。 |
| `.minF()` | 経過時間（分）を `double` 型で返します。 |
| `.s()` | 経過時間（秒）を返します。 |
| `.sF()` | 経過時間（秒）を `double` 型で返します。 |
| `.ms()` | 経過時間（ミリ秒）を返します。 |
| `.msF()` | 経過時間（ミリ秒）を `double` 型で返します。 |
| `.us()` | 経過時間（マイクロ秒）を返します。 |
| `.usF()` | 経過時間（マイクロ秒）を `double` 型で返します。 |
| `.elapsed()` | 経過時間を `Duration` 型で返します。 |

`Stopwatch` の経過時間は `Scene::GetMaxDeltaTime()` の影響を受けず、常に実時間で計測されます。同一フレーム内で `Stopwatch::sF()` を複数呼び出すと、時間の経過によって毎回異なる値が返ってきます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ストップウォッチ
	Stopwatch stopwatch;

	while (System::Update())
	{
		ClearPrint();
		Print << U"isStarted: " << stopwatch.isStarted();
		Print << U"isPaused: " << stopwatch.isPaused();
		Print << U"isRunning: " << stopwatch.isRunning();
		Print << stopwatch;

		if (SimpleGUI::Button(U"start", Vec2{ 200, 20 }))
		{
			stopwatch.start();
		}

		if (SimpleGUI::Button(U"pause", Vec2{ 200, 60 }))
		{
			stopwatch.pause();
		}

		if (SimpleGUI::Button(U"resume", Vec2{ 200, 100 }))
		{
			stopwatch.resume();
		}

		if (SimpleGUI::Button(U"reset", Vec2{ 200, 140 }))
		{
			stopwatch.reset();
		}

		if (SimpleGUI::Button(U"restart", Vec2{ 200, 180 }))
		{
			stopwatch.restart();
		}
	}
}
```


## 30.6 ストップウォッチの経過時間の表示
`Stopwatch` は `.format()` によって `String` に変換できます。書式を引数で指定することもできます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		ClearPrint();
		Print << stopwatch;
		Print << stopwatch.format(U"H:mm:ss.xx");
		Print << stopwatch.format(U"S.x");
	}
}
```


## 30.7 Stopwatch と時間型の比較
`Stopwatch` 型の変数は時間型の値と比較して、経過時間が特定の時間を超えたかどうかを調べることができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Stopwatch stopwatch{ StartImmediately::Yes };

	while (System::Update())
	{
		ClearPrint();
		Print << stopwatch;

		if (3s <= stopwatch)
		{
			Print << U"3 秒経過";
		}

		if (5.5s <= stopwatch)
		{
			Print << U"5.5 秒経過";
		}

		if (stopwatch < 20s)
		{
			Print << U"20 秒未満";
		}
	}
}
```


## 30.8 Timer クラス
`Timer` はカウントダウンによって経過時間を計測するクラスです。使い方はほぼ `Stopwatch` と同様です。コンストラクタでカウントダウン時間を指定します。指定したカウントダウン時間が経過した場合に `true` を返すメンバ関数 `reachedZero()` が追加されています。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Timer timer{ 15s };

	while (System::Update())
	{
		ClearPrint();
		Print << U"isStarted: " << timer.isStarted();
		Print << U"isPaused: " << timer.isPaused();
		Print << U"isRunning: " << timer.isRunning();
		Print << U"reachedZero: " << timer.reachedZero();
		Print << timer;

		if (SimpleGUI::Button(U"start", Vec2{ 200, 20 }))
		{
			timer.start();
		}

		if (SimpleGUI::Button(U"pause", Vec2{ 200, 60 }))
		{
			timer.pause();
		}

		if (SimpleGUI::Button(U"resume", Vec2{ 200, 100 }))
		{
			timer.resume();
		}

		if (SimpleGUI::Button(U"reset", Vec2{ 200, 140 }))
		{
			timer.reset();
		}

		if (SimpleGUI::Button(U"restart", Vec2{ 200, 180 }))
		{
			timer.restart();
		}
	}
}
```


## 30.9 日付クラス
`Date` は日付を表現するクラスで、`int32 year`, `int32 month`, `int32 day` をメンバ変数に持ちます。`Days` 型の値を使って加減算ができます。

`Date::Today()` は現在の日付を、`Date::Tomorrow()` は明日の日付を、`Date::Yesterday()` は昨日の日付を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 現在の日付を取得
	const Date date = Date::Today();
	Print << date;
	Print << date.year;
	Print << date.month;
	Print << date.day;

	// 10 日前の日付
	Print << (date - 10_d);

	// 10 日後の日付
	Print << (date + 10_d);

	// 日付の引き算
	Print << (Date::Tomorrow() - Date::Yesterday());

	while (System::Update())
	{

	}
}
```


## 30.10 日付と時刻クラス
`DateTime` は日付と時刻を表現するクラスで、`int32 year`, `int32 month`, `int32 day`, `int32 hour`, `int32 minute`, `int32 second`, `int32 milliseconds` をメンバ変数に持ちます。時間型の値を使って加減算ができます。

`DateTime::Now()` は現在の日付と時刻を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 現在の日付と時刻を取得
	const DateTime t = DateTime::Now();
	Print << t;
	Print << t.year;
	Print << t.month;
	Print << t.day;
	Print << t.hour;
	Print << t.minute;
	Print << t.second;
	Print << t.milliseconds;

	// 30 分前
	Print << (t - 30min);

	// 来週
	Print << (t + 7_d);

	// 2030 年まであと
	const Duration s = (DateTime{ 2030, 1, 1 } - t);
	Print << s;
	Print << DaysF{ s };
	Print << DurationCast<Days>(s);

	while (System::Update())
	{

	}
}
```


## 30.11 時差の取得
`Time::UTCOffsetMinutes()` は、使用しているコンピュータの、協定世界時 (UTC) との時差（分）を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/11.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << Time::UTCOffsetMinutes();
	}
}
```
