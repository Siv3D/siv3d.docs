# 70. マイクと音声波形
音声波形を扱うためのクラスと、パソコンに内蔵・接続されたマイクから音声波形を取得しプログラムで活用する方法を学びます。

## 70.1 接続されているマイクを列挙する
PC に接続されているマイクの一覧を `System::EnumerateMicrophones()` で取得できます。結果は `Array<MicrophoneInfo>` 型で返されます。

`MicrophoneInfo` 型のメンバ変数は次の通りです。

| メンバ変数 | 説明 |
|--|--|
| `uint32 microphoneIndex` | `Microphone` で使うデバイスインデックス |
| `String name` | 名前 |
| `Array<uint32> sampleRates` | 対応するサンプルレートの一覧 |
| `uint32 preferredSampleRate` | 推奨サンプルレート |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/microphone/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	for (const auto& info : System::EnumerateMicrophones())
	{
		Print << U"[{}] {} {} {}"_fmt(info.microphoneIndex, info.name, info.sampleRates, info.preferredSampleRate);
	}

	while (System::Update())
	{

	}
}
```


## 70.2 マイクから波形情報を得る
`Microphone` クラスを通して、マイクに入力された音声波形やその分析結果を取得できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/microphone/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	if (System::EnumerateMicrophones().isEmpty())
	{
		throw Error{ U"No microphone is connected" };
	}

	// 音声バッファ 5 秒、バッファ終端まで到達したら最初からループ、即座に録音開始
	// デフォルトの音声入力デバイス（マイク）、推奨サンプリングレートを使用
	const Microphone mic{ 5s, Loop::Yes, StartImmediately::Yes };

	if (not mic.isRecording())
	{
		throw Error{ U"Failed to start recording" };
	}

	LineString points(1600, Vec2{ 0, 300 });

	while (System::Update())
	{
		// 音声波形（サンプルの配列）にアクセス
		const Wave& wave = mic.getBuffer();

		// 現在のバッファ位置をもとに、直近の 1600 サンプルの波形を取得
		const size_t writePos = mic.posSample();
		{
			constexpr size_t samples_show = 1600;
			const size_t headLength = Min(writePos, samples_show);
			const size_t tailLength = (samples_show - headLength);
			size_t pos = 0;

			for (size_t i = 0; i < tailLength; ++i)
			{
				const float a = wave[wave.size() - tailLength + i].left;
				points[pos].set(pos * 0.5, 300 + a * 280);
				++pos;
			}

			for (size_t i = 0; i < headLength; ++i)
			{
				const float a = wave[writePos - headLength + i].left;
				points[pos].set(pos * 0.5, 300 + a * 280);
				++pos;
			}
		}

		// 音の波形の平均振幅 [0.0, 1.0]
		const double mean = mic.mean();

		// 音の波形の振幅の二乗平均平方根 [0.0, 1.0]
		const double rootMeanSquare = mic.rootMeanSquare();

		// 音の波形のピーク [0.0, 1.0]
		const double peak = mic.peak();

		// 波形の描画
		points.draw(2);

		Line{ 0, (300 - mean * 280), Arg::direction(800, 0) }.draw(2, HSV{ 200 });
		Line{ 0, (300 - rootMeanSquare * 280), Arg::direction(800, 0) }.draw(2, HSV{ 120 });
		Line{ 0, (300 - peak * 280), Arg::direction(800, 0) }.draw(2, HSV{ 40 });
	}
}
```


## 70.3 マイクからの入力波形のスペクトラムを表示する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/microphone/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	if (System::EnumerateMicrophones().isEmpty())
	{
		throw Error{ U"No microphone is connected" };
	}

	// デフォルトの音声入力デバイス（マイク）、推奨サンプリングレート、5 秒間のバッファを使用
	const Microphone mic{ StartImmediately::Yes };

	if (not mic.isRecording())
	{
		throw Error{ U"Failed to start recording" };
	}

	FFTResult fft;

	while (System::Update())
	{
		// FFT の結果を取得
		mic.fft(fft);

		// 結果を可視化
		for (auto i : step(800))
		{
			const double size = Pow(fft.buffer[i], 0.6f) * 1200;
			RectF{ Arg::bottomLeft(i, 600), 1, size }.draw(HSV{ 240 - i });
		}

		// 周波数表示
		Rect{ Cursor::Pos().x, 0, 1, Scene::Height() }.draw();
		ClearPrint();
		Print << U"{:.2f} Hz"_fmt(Cursor::Pos().x * fft.resolution);
	}
}
```
