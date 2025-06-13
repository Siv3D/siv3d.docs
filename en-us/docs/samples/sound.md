# Sound Samples

## 1. Piano

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/sound/1.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// White key size
		constexpr Size KeySize{ 55, 400 };

		// Resize window
		Window::Resize((12 * KeySize.x), KeySize.y);

		// Instrument type
		constexpr GMInstrument Instrument = GMInstrument::Piano1;

		// Number of keys
		constexpr int32 NumKeys = 20;

		// Create sounds
		std::array<Audio, NumKeys> sounds;
		for (int32 i = 0; i < NumKeys; ++i)
		{
			sounds[i] = Audio{ Instrument, static_cast<uint8>(PianoKey::A3 + i), 0.5s };
		}

		// Corresponding keys
		constexpr std::array<Input, NumKeys> Keys =
		{
			KeyTab, Key1, KeyQ,
			KeyW, Key3, KeyE, Key4, KeyR, KeyT, Key6, KeyY, Key7, KeyU, Key8, KeyI,
			KeyO, Key0, KeyP, KeyMinus, KeyEnter,
		};

		// Offset values for calculating drawing positions (white keys are even, black keys are odd)
		constexpr std::array<int32, NumKeys> KeyPositions =
		{
			0, 1, 2, 4, 5, 6, 7, 8, 10, 11, 12, 13, 14, 15, 16, 18, 19, 20, 21, 22
		};

		while (System::Update())
		{
			// Play corresponding sound when key is pressed
			for (int32 i = 0; i < NumKeys; ++i)
			{
				if (Keys[i].down())
				{
					sounds[i].playOneShot(0.5);
				}
			}

			// Draw white keys
			for (int32 i = 0; i < NumKeys; ++i)
			{
				// Those with even offset values are white keys
				if (IsEven(KeyPositions[i]))
				{
					RectF{ (KeyPositions[i] / 2 * KeySize.x), 0, KeySize.x, KeySize.y }
						.stretched(-1).draw(Keys[i].pressed() ? Palette::Pink : Palette::White);
				}
			}

			// Draw black keys
			for (int32 i = 0; i < NumKeys; ++i)
			{
				// Those with odd offset values are black keys
				if (IsOdd(KeyPositions[i]))
				{
					RectF{ (KeySize.x * 0.68 + KeyPositions[i] / 2 * KeySize.x), 0, (KeySize.x * 0.58), (KeySize.y * 0.62) }
						.draw(Keys[i].pressed() ? Palette::Pink : Color{ 24 });
				}
			}
		}
	}
	```


## 2. Music Player

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/sound/2.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	double ConvertVolume(double volume)
	{
		return ((volume == 0.0) ? 0.0 : Math::Eerp(0.01, 1.0, volume));
	}

	void Main()
	{
		// Music
		Audio audio;

		// Volume
		double volume = 1.0;

		// FFT result
		FFTResult fft;

		// Whether playback position has changed
		bool seeking = false;

		while (System::Update())
		{
			ClearPrint();

			// Playback and total time
			const String time = FormatTime(SecondsF{ audio.posSec() }, U"M:ss")
				+ U" / " + FormatTime(SecondsF{ audio.lengthSec() }, U"M:ss");

			// Progress bar progress
			double progress = static_cast<double>(audio.posSample()) / audio.samples();

			if (audio.isPlaying())
			{
				// FFT analysis
				FFT::Analyze(fft, audio);

				// Visualize results
				for (int32 i = 0; i < Min(Scene::Width(), static_cast<int32>(fft.buffer.size())); ++i)
				{
					const double size = Pow(fft.buffer[i], 0.6f) * 1000;
					RectF{ Arg::bottomLeft(i, 480), 1, size }.draw(HSV{ 240.0 - i });
				}

				// Frequency display
				if (InRange(Cursor::Pos().x, 0, 800))
				{
					Rect{ Cursor::Pos().x, 0, 1, 480 }.draw();
					Print << U"{:.1f} Hz"_fmt(Cursor::Pos().x * fft.resolution);
				}
			}

			Rect{ 0, 480, Scene::Width(), 120 }.draw(ColorF{ 0.5 });

			// Open music file from folder
			if (SimpleGUI::Button(U"Open", Vec2{ 40, 500 }, 100))
			{
				audio.stop(0.5s);
				audio = Dialog::OpenAudio();
				audio.setVolume(ConvertVolume(volume));
				audio.play();
			}

			// Play
			if (SimpleGUI::Button(U"\U000F040A", Vec2{ 150, 500 }, 60, (audio && (not audio.isPlaying()))))
			{
				audio.setVolume(ConvertVolume(volume));
				audio.play(0.2s);
			}

			// Pause
			if (SimpleGUI::Button(U"\U000F03E4", Vec2{ 220, 500 }, 60, audio.isPlaying()))
			{
				audio.pause(0.2s);
			}

			// Volume
			if (SimpleGUI::Slider(((volume == 0.0) ? U"\U000F075F" : (volume < 0.5) ? U"\U000F0580" : U"\U000F057E"),
				volume, Vec2{ 40, 540 }, 30, 120, (not audio.isEmpty())))
			{
				audio.setVolume(ConvertVolume(volume));
			}

			// Slider
			if (SimpleGUI::Slider(time, progress, Vec2{ 200, 540 }, 130, 420, (not audio.isEmpty())))
			{
				audio.pause(0.05s);

				while (audio.isPlaying()) // Wait until playback stops
				{
					System::Sleep(0.01s);
				}

				// Change playback position
				audio.seekSamples(static_cast<size_t>(audio.samples() * progress));

				// To avoid noise, don't resume playback until the slider is released
				seeking = true;
			}
			else if (seeking && MouseL.up())
			{
				// Resume playback
				audio.play(0.05s);
				seeking = false;
			}
		}

		// If playing when exiting, fade out volume
		if (audio.isPlaying())
		{
			audio.fadeVolume(0.0, 0.3s);
			System::Sleep(0.3s);
		}
	}
	```


## 3. Frequency Analysis of Microphone Input

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/sound/3.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Set up microphone (start recording immediately)
		Microphone mic{ StartImmediately::Yes };

		if (not mic)
		{
			// Exit if microphone is not available
			throw Error{ U"Microphone not available" };
		}

		FFTResult fft;

		while (System::Update())
		{
			// Get FFT results
			mic.fft(fft);

			ClearPrint();

			if (InRange(Cursor::Pos().x, 0, 800))
			{
				Print << U"{:.1f} Hz"_fmt(Cursor::Pos().x * fft.resolution);
			}

			// Visualize results
			for (int32 i = 0; i < 800; ++i)
			{
				const double size = (Pow(fft.buffer[i], 0.6f) * 1200);
				RectF{ Arg::bottomLeft(i, 600), 1, size }.draw(HSV{ 240 - i });
			}

			// Frequency display
			Rect{ Cursor::Pos().x, 0, 1, Scene::Height() }.draw();

			ClearPrint();
			Print << U"{:.1f} Hz"_fmt(Cursor::Pos().x * fft.resolution);
		}
	}
	```


## 4. Playing Audio Files from ZIP
Prepare by compressing `music/test.mp3` into `music.zip` beforehand.

??? memo "Code"
	=== "Non-streaming playback"
		```cpp
		# include <Siv3D.hpp>

		void Main()
		{
			ZIPReader zip{ U"music.zip" };

			Print << zip.enumPaths();

			const Audio audio{ zip.extract(U"music/test.mp3") };

			audio.play();

			while (System::Update())
			{

			}
		}
		```

	=== "Streaming playback"
		Extract the file to a temporary file and then stream playback.
		```cpp
		# include <Siv3D.hpp>

		void Main()
		{
			ZIPReader zip{ U"music.zip" };

			Print << zip.enumPaths();

			FilePath temporaryFilePath;

			if (const Blob blob = zip.extractToBlob(U"music/test.mp3"))
			{
				Print << U"ZIP data extraction complete";

				temporaryFilePath = FileSystem::UniqueFilePath();

				Print << temporaryFilePath << U" saved to";

				if (blob.save(temporaryFilePath))
				{
					Print << U"Save successful";
				}
				else
				{
					Print << U"Save failed";
				}
			}
			else
			{
				Print << U"Data extraction failed";
			}

			Audio audio{ Audio::Stream, temporaryFilePath };

			Print << U"isStreaming: " << audio.isStreaming();

			audio.play();

			while (System::Update())
			{

			}

			// Release Audio
			audio.release();

			// Delete file if no Audio references it
			FileSystem::Remove(temporaryFilePath);
		}
		```


## 5. Creating a Custom Musical Notation Language

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/sound/5.png)

??? memo "Code"
	```txt title="score.txt"
	ドレミドレミ
	```

	```cpp
	# include <Siv3D.hpp>

	String LoadScore(const FilePath& path)
	{
		// Load text file
		TextReader reader{ path };

		if (not reader)
		{
			throw Error{ U"score.txt not found" };
		}

		String result;

		String line;

		// Read line by line
		while (reader.readLine(line))
		{
			// Add to end of score
			result += line;
		}

		return result;
	}

	void Main()
	{
		// Variable to store the score
		const String score = LoadScore(U"score.txt");

		Print << U"Loaded score: " << score;

		// Prepare Do, Re, Mi sounds
		const Audio soundDo{ s3d::GMInstrument::Piano1, PianoKey::C4, 0.5s };
		const Audio soundRe{ s3d::GMInstrument::Piano1, PianoKey::D4, 0.5s };
		const Audio soundMi{ s3d::GMInstrument::Piano1, PianoKey::E4, 0.5s };
		// Reference
		// Do: C4, Re: D4, Mi: E4, Fa: F4, So: G4, La: A4, Ti: B4, Do: C5, ...
		// Do#: CS4, Re#: DS4, ...

		// Playback position
		int32 pos = -1;

		// Volume
		double volume = 0.5;

		// Stopwatch that starts immediately
		Stopwatch stopwatch{ StartImmediately::Yes };

		while (System::Update())
		{
			// Set newPos to elapsed time (milliseconds) / 1000
			const int32 newPos = (stopwatch.ms() / 1000);

			if (pos != newPos)
			{
				pos = newPos;

				// If within range
				if (pos < score.size())
				{
					// Character at position pos
					const char32 ch = score[pos];

					Print << U"{}: {}"_fmt(pos, ch);

					if (ch == U'ド')
					{
						soundDo.playOneShot(volume);
					}
					else if (ch == U'レ')
					{
						soundRe.playOneShot(volume);
					}
					else if (ch == U'ミ')
					{
						soundMi.playOneShot(volume);
					}
				}
			}
		}
	}
	```

## 6. Changing Playback Speed Without Changing Pitch

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/sound/6.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8,0.7 });

		const Audio audio = Dialog::OpenAudio(Audio::Stream);

		if (not audio)
		{
			return;
		}

		double speed = 1.0;
		double pitchShift = 0.0;

		audio.play();

		while (System::Update())
		{
			if (SimpleGUI::Slider(U"Speed: {:.2f}"_fmt(speed), speed, 0.25, 4.0, Vec2{ 40, 40 }, 180, 240))
			{
				// Change playback speed
				audio.setSpeed(speed);

				// Calculate pitch shift
				pitchShift = -(Math::Log2(speed) * 12);

				// Apply pitch shift
				GlobalAudio::BusSetPitchShiftFilter(MixBus0, 0, pitchShift);
			}

			// Slider that moves in conjunction with pitch shift
			if (SimpleGUI::Slider(U"PitchShift: {:.2f}"_fmt(pitchShift), pitchShift, -24, 24, Vec2{ 40, 80 }, 180, 240, false)) {}
		}
	}
	```

## 7. BGM Crossfade

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/AudioCrossfade/Screenshot/2.png)

[Siv3D-Sample | BGM Crossfade :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/blob/main/Samples/AudioCrossfade){:target="_blank" .md-button}
