# 70. Microphone and Audio Waveforms
Learn about classes for handling audio waveforms and how to get audio waveforms from built-in or connected microphones on your computer and use them in programs.

## 70.1 Class for Handling Audio Waveforms
- Use the `Wave` class when handling audio waveform data
- The `Wave` class handles audio waveform data with an interface like `Array<WaveSample>`
	- `Wave wave{ (44100 * 5), Arg::sampleRate = 44100 }` creates 5 seconds of audio waveform data with sampling frequency 44100 Hz
	- `wave[i]` can access the i-th sample
- The `WaveSample` type is a structure like this:

```cpp
struct WaveSample
{
	float left;
	float right;
};
```

- The values of `left` and `right` represent the amplitude of the left and right channels respectively. The value range is from -1.0 to 1.0
- The `Wave` class provides member functions for reading/writing audio waveform data, processing waveforms, and playing waveforms


## 70.2 Saving Audio Waveforms
- You can save audio waveform data to a file using the `Wave` class's `.save()` member function
- Using `.saveWithDialog()` displays a save dialog and lets the user choose the save destination
	- You can choose between WAVE format (.wav) or Ogg Vorbis format (.ogg) as the save format
- `Wave::DefaultSampleRate` is a constant representing the default sampling frequency of the `Wave` class, which is `44100`
- The following sample code generates a 440 Hz sine wave for 5 seconds and saves it to a file

```cpp
# include <Siv3D.hpp>

void Main()
{
	Wave wave{ (Wave::DefaultSampleRate * 5), Arg::sampleRate = Wave::DefaultSampleRate };

	// Generate 440 Hz sine wave
	for (size_t i = 0; i < wave.size(); ++i)
	{
		const double t = (static_cast<double>(i) / wave.sampleRate() * 440.0 * 2_pi);
		const float value = static_cast<float>(std::sin(t));
		wave[i].left = wave[i].right = value;
	}

	// Display save dialog and save
	wave.saveWithDialog();

	while (System::Update())
	{

	}
}
```


## 70.3 Enumerating Connected Microphones
- You can get a list of microphones connected to the PC with `System::EnumerateMicrophones()`
- The result is returned as `Array<MicrophoneInfo>` type
- The member variables of `MicrophoneInfo` type are as follows:

| Code | Description |
|--|--|
| `uint32 microphoneIndex` | Device index to use with `Microphone` |
| `String name` | Name |
| `Array<uint32> sampleRates` | List of supported sample rates |
| `uint32 preferredSampleRate` | Recommended sample rate |

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
```txt title="Output example"
[3] マイク配列 (デジタルマイク向けインテルR スマート・サウンド・テクノロジー) {4000, 5512, 8000, 9600, 11025, 16000, 22050, 32000, 44100, 48000, 88200, 96000, 176400, 192000} 48000
[4] マイク (Yeti Stereo Microphone) {4000, 5512, 8000, 9600, 11025, 16000, 22050, 32000, 44100, 48000, 88200, 96000, 176400, 192000} 48000
```


## 70.4 Getting Microphone Input
- Through the `Microphone` class, you can get audio waveforms input to the microphone and their analysis results
- `.getBuffer()` can get audio waveform data for the length of the buffer that the microphone has

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/microphone/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	if (System::EnumerateMicrophones().isEmpty())
	{
		throw Error{ U"No microphone is connected" };
	}

	// Audio buffer 5 seconds, loop from beginning when reaching buffer end, start recording immediately
	// Use default audio input device (microphone) and recommended sampling rate
	const Microphone mic{ 5s, StartImmediately::Yes };

	if (not mic.isRecording())
	{
		throw Error{ U"Failed to start recording" };
	}

	LineString points(1600, Vec2{ 0, 300 });

	while (System::Update())
	{
		// Access audio waveform (array of samples)
		const Wave& wave = mic.getBuffer();

		// Get waveform of the most recent 1600 samples based on current buffer position
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

		// Average amplitude of audio waveform [0.0, 1.0]
		const double mean = mic.mean();

		// Root mean square of audio waveform amplitude [0.0, 1.0]
		const double rootMeanSquare = mic.rootMeanSquare();

		// Peak of audio waveform [0.0, 1.0]
		const double peak = mic.peak();

		// Draw waveform
		points.draw(2);

		Line{ 0, (300 - mean * 280), Arg::direction(800, 0) }.draw(2, HSV{ 200 });
		Line{ 0, (300 - rootMeanSquare * 280), Arg::direction(800, 0) }.draw(2, HSV{ 120 });
		Line{ 0, (300 - peak * 280), Arg::direction(800, 0) }.draw(2, HSV{ 40 });
	}
}
```


## 70.5 Spectrum of Microphone Input Waveform
- You can get the spectrum of input waveforms from the microphone using the `Microphone` class's `.fft(fftResult, sampleLength)` member function
- The result is stored in a variable of `FFTResult` type

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/microphone/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	if (System::EnumerateMicrophones().isEmpty())
	{
		throw Error{ U"No microphone is connected" };
	}

	// Audio buffer 5 seconds, loop from beginning when reaching buffer end, start recording immediately
	// Use default audio input device (microphone) and recommended sampling rate
	const Microphone mic{ 5s, StartImmediately::Yes };

	if (not mic.isRecording())
	{
		throw Error{ U"Failed to start recording" };
	}

	FFTResult fft;

	while (System::Update())
	{
		// Get FFT results
		mic.fft(fft);

		// Visualize results
		for (int32 i = 0; i < 800; ++i)
		{
			const double size = Pow(fft.buffer[i], 0.6f) * 1200;
			RectF{ Arg::bottomLeft(i, 600), 1, size }.draw(HSV{ 240 - i });
		}

		// Frequency display
		Rect{ Cursor::Pos().x, 0, 1, 600 }.draw();
		ClearPrint();
		Print << U"{:.2f} Hz"_fmt(Cursor::Pos().x * fft.resolution);
	}
}
```