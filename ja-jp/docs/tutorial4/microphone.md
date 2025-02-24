# 70. マイクと音声波形
音声波形を扱うためのクラスと、パソコンに内蔵・接続されたマイクから音声波形を取得しプログラムで活用する方法を学びます。

## 70.1 音声波形を扱うクラス
- 音声波形データを扱うときは `Wave` クラスを使います
- `Wave` クラスは、音声波形データを `Array<WaveSample>` のようなインタフェースで扱います
	- `Wave wave{ (44100 * 5), Arg::sampleRate = 44100 }` で、サンプリング周波数 44100 Hz, 5 秒間の音声波形データを作成します
	- `wave[i]` で、i 番目のサンプルにアクセスできます
- `WaveSample` 型は次のような構造体です:

```cpp
struct WaveSample
{
	float left;
	float right;
};
```

- `left`, `right` の値は、それぞれ左チャンネル、右チャンネルの振幅を表します。値の範囲は -1.0 から 1.0 です
- `Wave` クラスは、音声波形データの読み書き、波形の加工、波形の再生などを行うためのメンバ関数を提供します


## 70.2 音声波形の保存
- `Wave` クラスの `.save()` メンバ関数を使って、音声波形データをファイルに保存できます
- `.saveWithDialog()` を使うと、保存ダイアログを表示してユーザーに保存先を選択させることができます
	- 保存形式は WAVE 形式（.wav）または Ogg Vorbis 形式（.ogg）を選択できます
- `Wave::DefaultSampleRate` は `Wave` クラスのデフォルトのサンプリング周波数を表す定数で、`44100` です
- 次のサンプルコードは、5 秒間の 440 Hz のサイン波を生成してファイルに保存します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Wave wave{ (Wave::DefaultSampleRate * 5), Arg::sampleRate = Wave::DefaultSampleRate };

	// 440 Hz のサイン波を生成する
	for (size_t i = 0; i < wave.size(); ++i)
	{
		const double t = (static_cast<double>(i) / wave.sampleRate() * 440.0 * 2_pi);
		const float value = static_cast<float>(std::sin(t));
		wave[i].left = wave[i].right = value;
	}

	// 保存ダイアログを表示して保存する
	wave.saveWithDialog();

	while (System::Update())
	{

	}
}
```


## 70.3 接続されているマイクの列挙
- PC に接続されているマイクの一覧を `System::EnumerateMicrophones()` で取得できます
- 結果は `Array<MicrophoneInfo>` 型で返されます
- `MicrophoneInfo` 型のメンバ変数は次の通りです：

| コード | 説明 |
|--|--|
| `uint32 microphoneIndex` | `Microphone` で使うデバイスインデックス |
| `String name` | 名前 |
| `Array<uint32> sampleRates` | 対応するサンプルレートの一覧 |
| `uint32 preferredSampleRate` | 推奨サンプルレート |

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
```txt title="出力例"
[3] マイク配列 (デジタルマイク向けインテルR スマート・サウンド・テクノロジー) {4000, 5512, 8000, 9600, 11025, 16000, 22050, 32000, 44100, 48000, 88200, 96000, 176400, 192000} 48000
[4] マイク (Yeti Stereo Microphone) {4000, 5512, 8000, 9600, 11025, 16000, 22050, 32000, 44100, 48000, 88200, 96000, 176400, 192000} 48000
```


## 70.4 マイク入力の取得
- `Microphone` クラスを通して、マイクに入力された音声波形やその分析結果を取得できます
- `.getBuffer()` で、マイクが持つバッファの長さ分の音声波形データを取得できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/microphone/4.png)

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
	const Microphone mic{ 5s, StartImmediately::Yes };

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


## 70.5 マイク入力波形のスペクトラム
- `Microphone` クラスの `.fft(fftResult, sampleLength)` メンバ関数を使って、マイクからの入力波形のスペクトラムを取得できます
- 結果は `FFTResult` 型の変数に格納されます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/microphone/5.png)

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
	const Microphone mic{ 5s, StartImmediately::Yes };

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
		for (int32 i = 0; i < 800; ++i)
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
