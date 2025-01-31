# 30. 時間と動き
Siv3D で時間や動きを扱う方法を学びます。

## 30.1 経過時間の計測
- `Scene::DeltaTime()` は、**前フレームからの経過時間（秒）**を `double` 型で返します
    - この値を使って、フレームレートに左右されないモーションを作ることができます
    - 詳しくは [**チュートリアル 14**](../tutorial/motion.md) を参照してください
- 一般に、直前のフレームからの経過時間が大きすぎると、ゲーム内のアニメーションや物理シミュレーションの変化ステップが大きくなり、安定性が損なわれる場合があります
- そのため、`Scene::DeltaTime()` は `Scene::GetMaxDeltaTime()` の値（デフォルトでは `0.1`）よりも大きくならないよう制限されます


## 30.2 経過時間の蓄積
- `Scene::Time()` は **プログラムが起動されてからの経過時間（秒）**を `double` 型で返します
- `System::Update()` を呼び出した際に更新されるため、同一フレーム内での `Scene::Time()` の呼び出しは同じ値を返します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/2.png)

```cpp

```

- `Scene::Time()` が返す値は `Scene::DeltaTime()` の累積です
- 前述したように、`Scene::DeltaTime()` は実際のフレーム経過時間より短い場合があるため、`Scene::Time()` は現実の時間経過よりも短い場合があります
- 現実と同期した時間が必要な場合は、次のような方法があります
    - `Stopwatch` を使って時間を計測する
    - `Timer` を使って時間を計測する
    - `Time::GetMillisec()` を使って現実のタイムポイントを取得する


## 30.3 時間に基づくモーション
- 位置・大きさ・角度などを時間の経過に応じて変化させて、モーションを表現できます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/3.png)

```cpp

```


## 30.4 一定時間おきに何かをする
- イベントの周期を決めておき、蓄積時間（秒）がその周期（秒）を超えたらイベントを発生させます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/4-1.png)

```cpp

```

- イベント周期が短い（1 フレームの時間よりも短い）場合、1 フレーム内で複数回イベントを発生させる必要があります
- そのような状況に対処するには、`if` の代わりに `while (eventPeriod <= accumulatedTime)` を使います
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/4-2.png)

```cpp

```


## 30.5 ストップウォッチ
- `Stopwatch` は、経過時間の計測やリセットを便利に行えるクラスです
- `Stopwatch` のコンストラクタ引数で `StartImmediately::Yes` を指定すると、作成と同時に計測を開始します
- `Stopwatch` の主要なメンバ関数は次のとおりです

| コード | 説明 |
|---|---|
| `.start()` | 計測を開始・再開する |
| `.pause()` | 計測を一時停止する |
| `.resume()` | 一時停止中の計測を再開する |
| `.reset()` | 計測を停止して経過時間を 0 にリセットする |
| `.restart()` | 計測をリセットして再び 0 から計測を開始する |
| `.isRunning()` | 計測中かどうかを `bool` 型で返す |
| `.isPaused()` | 一時停止中かどうかを `bool` 型で返す |
| `.isStarted()` | 計測が開始されているかどうかを `bool` 型で返す |
| `.min()` | 経過時間を分単位で `int32` 型で返す |
| `.s()` | 経過時間を分単位で `int32` 型で返す |
| `.s64()` | 経過時間を秒単位で `int64` 型で返す |
| `.sF()` | 経過時間を秒単位で `double` 型で返す |
| `.ms()` | 経過時間をミリ秒単位で `int32` 型で返す |
| `.ms64()` | 経過時間をミリ秒単位で `int64` 型で返す |
| `.msF()` | 経過時間をミリ秒単位で `double` 型で返す |
| `.us()` | 経過時間をマイクロ秒単位で `int32` 型で返す |
| `.us64()` | 経過時間をマイクロ秒単位で `int64` 型で返す |
| `.usF()` | 経過時間をマイクロ秒単位で `double` 型で返す |

- 経過時間は `Scene::GetMaxDeltaTime()` の影響を受けず、常に現実の時間で計測されます
- 経過時間を単位ごとに取得する必要はありません
	- 経過時間が 65.4 秒の時、`s()` は `65` を、`sF()` は `65.4` を、`ms()` は `65400` を返します
- 同一フレーム内で複数回呼び出すと、タイミングによって経過時間は変わります
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/5.png)

```cpp

```


## 30.6 時間型
- 時間を表現する次のような型が用意されています
	- `F` の付く型は浮動小数点数で値を保持します
	- `Duration` は `SecondsF` のエイリアスです

| 型 | 表現する時間 |
|--|--|
| `Days` または `DaysF` | 日 |
| `Hours` または `HoursF` | 時 |
| `Minutes` または `MinutesF` | 分 |
| `Seconds` または `SecondsF` | 秒 |
| `Milliseconds` または `MillisecondsF` | ミリ秒 |
| `Microseconds` または `MicrosecondsF` | マイクロ秒 |
| `Nanoseconds` または `NanosecondsF` | ナノ秒 |
| `Duration` | `SecondsF` の別名 |
	
- 整数や浮動小数点数リテラルに、次のような時間リテラルのサフィックスを付けて時間型を簡単に作成できます
	- 例えば `10s` は `Seconds{ 10 }`, 0.5s は `SecondsF{ 0.5 }` と同じです

| サフィックス | 時間 |
|--|--|
|`_d` | 日 |
| `h` | 時 |
| `min` | 分 |
| `s` | 秒 |
| `ms` | ミリ秒 |
| `us` | マイクロ秒 |
| `ns` | ナノ秒 |

- 時間型は、算術演算や比較演算が可能です
- 時間型は相互に変換可能です
- 浮動小数点数で表現する時間型 → 整数で表現する時間型の変換には `DurationCast<Type>()` が必要です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/6.png)

```cpp

```


## 30.7 タイマー
- `Timer` は指定した時間からのカウントダウンで残り時間を計測するクラスです
- `Timer` のコンストラクタ引数で `StartImmediately::Yes` を指定すると、作成と同時に計測を開始します
- `Timer` の主要なメンバ関数は次のとおりです

| コード | 説明 |
|---|---|
| `.start()` | タイマーを開始・再開する |
| `.pause()` | タイマーを一時停止する |
| `.resume()` | 一時停止中のタイマーを再開する |
| `.reset()` | タイマーを停止して残り時間をリセットする |
| `.restart()` | タイマーをリセットして再び開始する |
| `.isRunning()` | 計測中かどうかを `bool` 型で返す |
| `.isPaused()` | 一時停止中かどうかを `bool` 型で返す |
| `.isStarted()` | 計測が開始されているかどうかを `bool` 型で返す |
| `.reachedZero()` | 残り時間が 0 に達したかどうかを `bool` 型で返す |
| `.min()` | 残り時間を分単位で `int32` 型で返す |
| `.s()` | 残り時間を秒単位で `int32` 型で返す |
| `.s64()` | 残り時間を秒単位で `int64` 型で返す |
| `.sF()` | 残り時間を秒単位で `double` 型で返す |
| `.ms()` | 残り時間をミリ秒単位で `int32` 型で返す |
| `.ms64()` | 残り時間をミリ秒単位で `int64` 型で返す |
| `.msF()` | 残り時間をミリ秒単位で `double` 型で返す |
| `.us()` | 残り時間をマイクロ秒単位で `int32` 型で返す |
| `.us64()` | 残り時間をマイクロ秒単位で `int64` 型で返す |
| `.usF()` | 残り時間をマイクロ秒単位で `double` 型で返す |
| `.progress1_0()` | タイマーの進み具合（1.0 で始まり 0.0 で終わる）を `double` 型で返す |
| `.progress0_1()` | タイマーの進み具合（0.0 で始まり 1.0 で終わる）を `double` 型で返す |

- 残り時間は `Scene::GetMaxDeltaTime()` の影響を受けず、常に現実の時間で計測されます
- 残り時間を単位ごとに取得する必要はありません
	- 残り時間が 65.4 秒の時、`s()` は `65` を、`sF()` は `65.4` を、`ms()` は `65400` を返します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/7.png)

```cpp

```


## 30.8 時間の比較
- `Stopwatch` や `Timer` オブジェクトと時間型の値を比較することができます
- `if (3 <= stopwatch.s())` の代わりに `if (3s <= stopwatch)` で、ストップウォッチが 3 秒以上経過したかどうかを判定できます
- `if (timer.sF() < 10.0)` の代わりに `if (timer < 10s)` で、タイマーが残り 10 秒未満であるかどうかを判定できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/8.png)

```cpp

```


## 30.9 周期的なモーション
- 周期的に 0 ↔ 1 または -1 ↔ 1 の間で変化する値が必要な場合、`Periodic::` 名前空間に用意されている周期関数を使うと便利です
- これらの関数は、時間経過に応じて特定の周期とパターンで 0～1, あるいは -1～1 の範囲の値を返します
- 周期は `2s` (2 秒) や `0.5s` (0.5 秒) のように時間リテラルを使って指定します

=== "0～1 の周期関数"
	| 周期関数 | 動き |
	|--|--|
	|`Periodic::Square0_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/sq01.png"></div>|
	|`Periodic::Triangle0_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/tri01.png"></div>|
	|`Periodic::Sine0_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/sin01.png"></div>|
	|`Periodic::Sawtooth0_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/saw01.png"></div>|
	|`Periodic::Jump0_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/jum01.png"></div>|

	#### Periodic::Square0_1()
	- 指定した周期で 0.0 か 1.0 を交互に返します
	- 周期の前半では 1.0 を、残りの半分では 0.0 を返します

	#### Periodic::Triangle0_1()
	- 0.0 から一定の速度で値が大きくなって 1.0 に、そこから一定の速度で小さくなって 0.0 に、という変化を指定した周期で繰り返します

	#### Periodic::Sine0_1()
	- 指定した周期で、0.0～1.0 の範囲で正弦波（サインカーブ）を描く数値の変化を返します

	#### Periodic::Sawtooth0_1()
	- 指定した周期で、0.0 → 1.0 への変化を繰り返します

	#### Periodic::Jump0_1()
	- 指定した周期で、地面からジャンプしたときの速度のような数値変化を繰り返します

	#### サンプルコード

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/9-1.png)

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
	|`Periodic::Square1_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/sq11.png"></div>|
	|`Periodic::Triangle1_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/tri11.png"></div>|
	|`Periodic::Sine1_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/sin11.png"></div>|
	|`Periodic::Sawtooth1_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/saw11.png"></div>|
	|`Periodic::Jump1_1`|<div class="noshadow-100"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/jum11.png"></div>|

	#### Periodic::Square1_1()
	- 指定した周期で -1.0 か 1.0 を交互に返します
	- 周期の前半では 1.0 を、残りの半分では -1.0 を返します

	#### Periodic::Triangle1_1()
	- -1.0 から一定の速度で値が大きくなって 1.0 に、そこから一定の速度で小さくなって -1.0 に、という変化を指定した周期で繰り返します

	#### Periodic::Sine1_1()
	- 指定した周期で、-1.0～1.0 の範囲で正弦波（サインカーブ）を描く数値の変化を返します

	#### Periodic::Sawtooth1_1()
	- 指定した周期で、-1.0 → 1.0 への変化を繰り返します

	#### Periodic::Jump1_1()
	- 指定した周期で、地面からジャンプしたときの速度のような数値変化を繰り返します

	#### サンプルコード

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/9-2.png)

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


## 30.10 トランジション
- 「条件を満たしている間、値を少しずつ 1.0 に近づける。条件を満たしていなければ徐々に 0.0 に戻す」という処理を行う場合、`Transition` クラスを使うと便利です
- `Transition` のコンストラクタには、0.0 から 1.0 に達するまでの最短所要時間と、最大値から最小値に減少するまでの最短所要時間を設定します
- 毎フレーム `.update(state)` に、増加の場合は `true` を、減少の場合は `false` を渡せば、設定された速度で値が増加・減少します
- `.value()` で現在値を取得できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/10.png)

```cpp

```


## 30.11 線形補間
- ある状態 A と B があり、その間を補間係数 `t` で補間したい場合、`A.lerp(B, t)` を使います
- 補間係数は　`t` は、通常 0.0 ～ 1.0 の範囲です
- 次のようなクラスがメンバ関数 `.lerp()` を持っています

| 要素 | クラス |
|--|--|
| 色 | `ColorF`, `HSV` |
| ベクトル | `Point`, `Vec2`, `Vec3`, `Vec4` |
| 図形 | `Line`, `Circle`, `Rect`, `RectF`, `Triangle`, `Quad`, `Ellipse`, `RoundRect` |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/11.png)

```cpp

```


## 30.12 イージング
- 0.0 から 1.0 へ線形に（一定の速度で）値を増加させるだけでは、単調な動きになってしまいます
- はじめは少しずつ加速し、ゴールに近づくとゆっくりになるといったように、速度に変化を与えると、洗練された視覚効果を実現できます
- 0.0 ↔ 1.0 の移動を、特徴を持ったカーブに変換できる **イージング関数** を使い、モーションの印象を改善できます
- イージング関数は全部で約 30 種類用意されていて、一覧を [Easing Functions Cheat Sheet :material-open-in-new:](https://easings.net/){:target="_blank"} で確認できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/12.png)

```cpp

```


## 30.13 SmoothDamp 
- 線形補間とイージングは、開始値と終了値（目標値）が固定されているケースに適しています
- 一方で、移動中に目標値が変更された場合、移動の速さや方向が急に変化して不自然な印象を与えてしまいます
- 目標値が変更されても、現在の速度を考慮してなめらかに移動・変化し続けるには、`Math::SmoothDamp` 関数を使います
- `Math::SmoothDamp` 関数は、現在位置と目標位置、そして現在の速度から、時間ベースで次の位置を計算する、**非常に便利で強力な補間関数**です
- 次のような型が `Math::SmoothDamp` 関数に対応しています

| 要素 | 型・クラス |
|--|--|
| 数値型 | `float`, `double` |
| ベクトル | `Vec2`, `Vec3`, `Vec4` |
| 色 | `ColorF` |

- Siv3D v0.8 ではより多くのクラスがサポートされる予定です

### 関数の概要
- `Vec2` 用の `Math::SmoothDamp` 関数は次のとおりです。

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
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/13.png)

```cpp

```


## 30.14 アプリケーションの起動時間の取得
- アプリケーションの起動からの経過時間を現実の時間で取得するには、次のような関数を使います
	- 戻り値は `uint64` 型です

| コード | 説明 |
|:---|:---|
| `Time::GetSec()` | アプリケーションの起動からの経過時間（秒）を返す |
| `Time::GetMillisec()` | アプリケーションの起動からの経過時間（ミリ秒）を返す |
| `Time::GetMicrosec()` | アプリケーションの起動からの経過時間（マイクロ秒）を返す |
| `Time::GetNanosec()` | アプリケーションの起動からの経過時間（ナノ秒）を返す |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/14.png)

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


## 30.15 UNIX 時間の取得
- 1970 年 1 月 1 日午前 0 時 0 分 0 秒（UNIX エポック）からの経過時間（UNIX 時間）を取得するには、次の関数を使います
	- 戻り値は `uint64` 型です

| コード | 説明 |
|:---|:---|
| `Time::GetSecSinceEpoch()` | 現在の UNIX 時間（秒）を返す |
| `Time::GetMillisecSinceEpoch()` | 現在の UNIX 時間（ミリ秒）を返す |
| `Time::GetMicrosecSinceEpoch()` | 現在の UNIX 時間（マイクロ秒）を返す |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/time/15.png)

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





## 18.7 トランジション
値を少しずつ増加させて最大値に到達させる。そこから徐々に減少させて最小値に戻す、という挙動をプログラムするときには `Transition` クラスを使うと便利です。

`Transition` のコンストラクタには、最小値から最大値に増加するまでの所要時間と、最大値から最小値に減少するまでの所要時間を設定します。あとは毎フレーム、`Transition::update()` に、増加の場合は `true` を、減少の場合は `false` を渡せば、設定された速度で値が増加、あるいは減少します。`Transition::value()` で現在の値を取得できます。

次のサンプルでは、マウスの左ボタンが押されていると円が大きくなり、離されていると小さくなります。最大は 200 ピクセル、最小は 0 ピクセルとしています。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/7.png)

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

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/9.png)

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

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/time/10.png)

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