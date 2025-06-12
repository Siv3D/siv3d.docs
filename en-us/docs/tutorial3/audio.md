# 41. Audio Playback
Learn how to control the playback of sound effects and music.

## 41.1 Creating Audio
- Audio data to be played is managed by the `Audio` class
- There are several ways to create audio:
	- **41.2** Creating from audio files
	- **41.3** Creating from audio files in streaming mode
	- **41.4** Creating instrument sounds
	- **41.5** Creating from audio waveform data
- Creating audio is costly, so it's usually done before the main loop
- When creating in the main loop, control is needed to avoid creating every frame
- Up to 64 audio instances can be played simultaneously
	- Including mixing buses, it's actually around 60

## 41.2 Loading and Playing Audio Files
- Create `Audio` from an audio file with `Audio variableName{ U"filePath" };`
- File paths use relative paths from the folder where the executable is located (the `App` folder during development) or absolute paths
	- For example, `U"example/test.mp3"` refers to the `test.mp3` file in the `example/` folder of the folder where the executable is located (`App` folder)
- Siv3D supports loading the following audio formats:
	- Some audio formats have different support depending on the OS

| Format    | Extension             | Windows | macOS | Linux | Web |
|-----------|-----------------------|:-------:|:-----:|:-----:|:---:|
| WAVE      | .wav                  | ✅       | ✅     | ✅     | ✅   |
| MP3       | .mp3                  | ✅       | ✅     | ✅     | ✅*  |
| AAC       | .m4a                  | ✅       | ✅     | ✅     | ✅*  |
| OggVorbis | .ogg                  | ✅       | ✅     | ✅     | ✅   |
| Opus      | .opus                 | ✅       | ✅     | ✅     | ✅   |
| MIDI      | .mid                  | ✅       | ✅     | ✅     | ✅   |
| WMA       | .wma                  | ✅       |       |       |     |
| FLAC      | .flac                 | ✅       | ✅     |       |     |
| AIFF      | .aif, .aiff, .aifc    |         | ✅     |       |     |

<small>(*) Requires build option settings and use of special functions.</small>

- To play audio, call `Audio`'s `.play()`
- Calling `.play()` on audio that is already playing does nothing
- To play the same audio overlapping, use `.playOneShot()` from **41.6**

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Load audio file and create Audio
	const Audio audio{ U"example/test.mp3" };

	// Play audio
	audio.play();

	while (System::Update())
	{

	}
}
```

- Audio cannot be played if the audio object doesn't exist
- Code like `Audio{ U"example/test.mp3" }.play();` using temporary objects cannot play audio


## 41.3 Streaming Playback
- WAVE, MP3, OggVorbis, and FLAC format audio files support streaming playback
- Streaming playback doesn't load the entire file content at first, but loads only a portion to start audio playback while progressively loading the remaining parts
- Using streaming playback significantly reduces file loading time during audio creation and memory usage during playback
- To enable streaming playback, pass `Audio::Stream` to the `Audio` constructor to request streaming playback
- If the requested file doesn't support streaming playback, normal loading is performed
- You can check if streaming playback is enabled with `.isStreaming()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Load audio file and create Audio (request streaming playback)
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	// Returns true if streaming playback is enabled
	Print << audio.isStreaming();

	// Play audio
	audio.play();

	while (System::Update())
	{

	}
}
```

- Audio with streaming playback enabled has the following limitations, but they don't cause problems for normal audio playback:
	- In loop playback, the loop end position cannot be set to anything other than the audio end
	- Cannot access audio waveform data with `.getSamples()`
	- Fewer samples are available when performing FFT


## 41.4 Creating Instrument Sounds
- You can programmatically generate instrument sounds by specifying the instrument type, pitch, length, etc., and create audio
- Pass the instrument name enumerated in `GMInstrument`, the pitch enumerated in `PianoKey`, and the sound length to the `Audio` constructor
	- `Audio{ instrumentName, pitch, noteOnTime }`
		- Note-off time is set to 1.0 second
	- `Audio{ instrumentName, pitch, noteOnTime, noteOffTime }`

??? info "List of Instruments"
	| Code | Instrument Name |
	| --- | --- |
	| `GMInstrument::Piano1` | Acoustic Piano |
	| `GMInstrument::Piano2` | Bright Piano |
	| `GMInstrument::Piano3` | Electric Grand Piano |
	| `GMInstrument::Piano4` | Honky-tonk Piano |
	| `GMInstrument::ElectricPiano1` | Electric Grand Piano |
	| `GMInstrument::ElectricPiano2` | FM Electric Piano |
	| `GMInstrument::Harpsichord` | Harpsichord |
	| `GMInstrument::Clavinet` | Clavinet |
	| `GMInstrument::Celesta` | Celesta |
	| `GMInstrument::Glockenspiel` | Glockenspiel |
	| `GMInstrument::MusicBox` | Music Box |
	| `GMInstrument::Vibraphone` | Vibraphone |
	| `GMInstrument::Marimba` | Marimba |
	| `GMInstrument::Xylophone` | Xylophone |
	| `GMInstrument::TubularBells` | Tubular Bells |
	| `GMInstrument::Dulcimer` | Dulcimer |
	| `GMInstrument::DrawbarOrgan` | Drawbar Organ |
	| `GMInstrument::PercussiveOrgan` | Percussive Organ |
	| `GMInstrument::RockOrgan` | Rock Organ |
	| `GMInstrument::ChurchOrgan` | Church Organ |
	| `GMInstrument::ReedOrgan` | Reed Organ |
	| `GMInstrument::Accordion` | Accordion |
	| `GMInstrument::Harmonica` | Harmonica |
	| `GMInstrument::TangoAccordion` | Tango Accordion |
	| `GMInstrument::NylonGuitar` | Nylon Guitar |
	| `GMInstrument::SteelGuitar` | Steel Guitar |
	| `GMInstrument::JazzGuitar` | Jazz Guitar |
	| `GMInstrument::CleanGuitar` | Clean Guitar |
	| `GMInstrument::MutedGuitar` | Muted Guitar |
	| `GMInstrument::OverdrivenGuitar` | Overdriven Guitar |
	| `GMInstrument::DistortionGuitar` | Distortion Guitar |
	| `GMInstrument::GuitarHarmonics` | Guitar Harmonics |
	| `GMInstrument::AcousticBass` | Acoustic Bass |
	| `GMInstrument::FingeredBass` | Fingered Bass |
	| `GMInstrument::PickedBass` | Picked Bass |
	| `GMInstrument::FretlessBass` | Fretless Bass |
	| `GMInstrument::SlapBass1` | Slap Bass 1 |
	| `GMInstrument::SlapBass2` | Slap Bass 2 |
	| `GMInstrument::SynthBass1` | Synth Bass 1 |
	| `GMInstrument::SynthBass2` | Synth Bass 2 |
	| `GMInstrument::Violin` | Violin |
	| `GMInstrument::Viola` | Viola |
	| `GMInstrument::Cello` | Cello |
	| `GMInstrument::Contrabass` | Contrabass |
	| `GMInstrument::TremoloStrings` | Tremolo Strings |
	| `GMInstrument::PizzicatoStrings` | Pizzicato Strings |
	| `GMInstrument::OrchestralHarp` | Orchestral Harp |
	| `GMInstrument::Timpani` | Timpani |
	| `GMInstrument::StringEnsemble1` | String Ensemble 1 |
	| `GMInstrument::StringEnsemble2` | String Ensemble 2 |
	| `GMInstrument::SynthStrings1` | Synth Strings 1 |
	| `GMInstrument::SynthStrings2` | Synth Strings 2 |
	| `GMInstrument::ChoirAahs` | Choir Aahs |
	| `GMInstrument::VoiceOohs` | Voice Oohs |
	| `GMInstrument::SynthChoir` | Synth Choir |
	| `GMInstrument::OrchestraHit` | Orchestra Hit |
	| `GMInstrument::Trumpet` | Trumpet |
	| `GMInstrument::Trombone` | Trombone |
	| `GMInstrument::Tuba` | Tuba |
	| `GMInstrument::MutedTrumpet` | Muted Trumpet |
	| `GMInstrument::FrenchHorn` | French Horn |
	| `GMInstrument::BrassSection` | Brass Section |
	| `GMInstrument::SynthBrass1` | Synth Brass 1 |
	| `GMInstrument::SynthBrass2` | Synth Brass 2 |
	| `GMInstrument::SopranoSax` | Soprano Sax |
	| `GMInstrument::AltoSax` | Alto Sax |
	| `GMInstrument::TenorSax` | Tenor Sax |
	| `GMInstrument::BaritoneSax` | Baritone Sax |
	| `GMInstrument::Oboe` | Oboe |
	| `GMInstrument::EnglishHorn` | English Horn |
	| `GMInstrument::Bassoon` | Bassoon |
	| `GMInstrument::Clarinet` | Clarinet |
	| `GMInstrument::Piccolo` | Piccolo |
	| `GMInstrument::Flute` | Flute |
	| `GMInstrument::Recorder` | Recorder |
	| `GMInstrument::PanFlute` | Pan Flute |
	| `GMInstrument::BlownBottle` | Blown Bottle |
	| `GMInstrument::Shakuhachi` | Shakuhachi |
	| `GMInstrument::Whistle` | Whistle |
	| `GMInstrument::Ocarina` | Ocarina |
	| `GMInstrument::SquareWave` | Square Wave |
	| `GMInstrument::SawWave` | Saw Wave |
	| `GMInstrument::SynCalliope` | Calliope Lead |
	| `GMInstrument::ChifferLead` | Chiffer Lead |
	| `GMInstrument::Charang` | Charang Lead |
	| `GMInstrument::SoloVox` | Voice Lead |
	| `GMInstrument::FifthSawWave` | Fifths Lead |
	| `GMInstrument::BassAndLead` | Bass + Lead |
	| `GMInstrument::Fantasia` | Fantasia |
	| `GMInstrument::WarmPad` | Warm Pad |
	| `GMInstrument::Polysynth` | Polysynth |
	| `GMInstrument::SpaceVoice` | Space Voice |
	| `GMInstrument::BowedGlass` | Bowed Glass |
	| `GMInstrument::MetalPad` | Metal Pad |
	| `GMInstrument::HaloPad` | Halo Pad |
	| `GMInstrument::SweepPad` | Sweep Pad |
	| `GMInstrument::IceRain` | Rain |
	| `GMInstrument::Soundtrack` | Soundtrack |
	| `GMInstrument::Crystal` | Crystal |
	| `GMInstrument::Atmosphere` | Atmosphere |
	| `GMInstrument::Brightness` | Brightness |
	| `GMInstrument::Goblin` | Goblin |
	| `GMInstrument::EchoDrops` | Echo Drops |
	| `GMInstrument::StarTheme` | Star Theme |
	| `GMInstrument::Sitar` | Sitar |
	| `GMInstrument::Banjo` | Banjo |
	| `GMInstrument::Shamisen` | Shamisen |
	| `GMInstrument::Koto` | Koto |
	| `GMInstrument::Kalimba` | Kalimba |
	| `GMInstrument::Bagpipe` | Bagpipe |
	| `GMInstrument::Fiddle` | Fiddle |
	| `GMInstrument::Shanai` | Shanai |
	| `GMInstrument::TinkleBell` | Tinkle Bell |
	| `GMInstrument::Agogo` | Agogo |
	| `GMInstrument::SteelDrums` | Steel Drums |
	| `GMInstrument::Woodblock` | Woodblock |
	| `GMInstrument::TaikoDrum` | Taiko Drum |
	| `GMInstrument::MelodicTom` | Melodic Tom |
	| `GMInstrument::SynthDrum` | Synth Drum |
	| `GMInstrument::ReverseCymbal` | Reverse Cymbal |
	| `GMInstrument::GuitarFretNoise` | Guitar Fret Noise |
	| `GMInstrument::BreathNoise` | Breath Noise |
	| `GMInstrument::Seashore` | Seashore |
	| `GMInstrument::BirdTweet` | Bird Tweet |
	| `GMInstrument::TelephoneRing` | Telephone Ring |
	| `GMInstrument::Helicopter` | Helicopter |
	| `GMInstrument::Applause` | Applause |
	| `GMInstrument::Gunshot` | Gunshot |


```cpp
# include <Siv3D.hpp>

void Main()
{
	// Piano C4 (Do) sound
	const Audio piano{ GMInstrument::Piano1, PianoKey::C4, 0.5s };

	// Clarinet D4 (Re) sound
	const Audio clarinet{ GMInstrument::Clarinet, PianoKey::D4, 0.5s };

	// Trumpet E4 (Mi) sound. Note-on 2.0 seconds, note-off 0.5 seconds
	const Audio trumpet{ GMInstrument::Trumpet, PianoKey::E4, 2.0s, 0.5s };

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Piano", Vec2{ 20, 20 }))
		{
			piano.play();
		}

		if (SimpleGUI::Button(U"Clarinet", Vec2{ 20, 60 }))
		{
			clarinet.play();
		}

		if (SimpleGUI::Button(U"Trumpet", Vec2{ 20, 100 }))
		{
			trumpet.play();
		}
	}
}
```


## 41.5 Creating from Audio Waveform Data
- You can create audio from programmatically generated/processed audio waveform data (`Wave` class)
- See **Tutorial 70** for the `Wave` class

```cpp
# include <Siv3D.hpp>

Wave MakeWave()
{
	Wave wave{ Wave::DefaultSampleRate * 2 };

	for (size_t i = 0; i < wave.size(); ++i)
	{
		const double t = (static_cast<double>(i) / Wave::DefaultSampleRate);

		wave[i] = static_cast<float>(Math::Sin(t * 220.0 * 2_pi));
	}

	return wave;
}

void Main()
{
	// Create audio from audio waveform data
	const Audio audio{ MakeWave() };

	audio.play();

	while (System::Update())
	{

	}
}
```


## 41.6 Overlapping Playback of the Same Audio
- To play one `Audio` overlapping, use `.playOneShot()` instead of `.play()`
- In the arguments of `.playOneShot()`, you can set volume, pan, playback speed, and mixing bus

```cpp
void Audio::playOneShot(double volume = 1.0, double pan = 0.0, double speed = 1.0, MixBus = MixBus0) const;
```

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Piano C4 (Do) sound
	const Audio piano{ GMInstrument::Piano1, PianoKey::C4, 0.5s };

	// Clarinet D4 (Re) sound
	const Audio clarinet{ GMInstrument::Clarinet, PianoKey::D4, 0.5s };

	// Trumpet E4 (Mi) sound
	const Audio trumpet{ GMInstrument::Trumpet, PianoKey::E4, 0.5s };

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Piano", Vec2{ 20, 20 }))
		{
			// Play with volume 0.5
			piano.playOneShot(0.5);
		}

		if (SimpleGUI::Button(U"Clarinet", Vec2{ 20, 60 }))
		{
			clarinet.playOneShot(0.5);
		}

		if (SimpleGUI::Button(U"Trumpet", Vec2{ 20, 100 }))
		{
			trumpet.playOneShot(0.5);
		}
	}
}
```


## 41.7 Empty Audio
- `Audio` type objects hold **empty audio** by default
- Empty audio is a 0.5-second sound that makes a "whoosh" sound, and **can be treated the same as valid audio**, with no errors when played
- Audio file loading failures also result in empty audio
- To check if audio is empty, use `if (audio.isEmpty())`, `if (audio)`, or `if (not audio)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Without providing initial data, it becomes empty audio
	Audio audioA;

	if (not audioA)
	{
		Print << U"audioA is empty";
	}

	// If audio file loading fails, it becomes empty audio
	Audio audioB{ U"aaa/bbb.wav" };

	if (not audioB)
	{
		Print << U"audioB is empty";
	}

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Play A", Vec2{ 200, 20 }))
		{
			audioA.playOneShot();
		}

		if (SimpleGUI::Button(U"Play B", Vec2{ 200, 60 }))
		{
			audioB.playOneShot();
		}
	}
}
```


## 41.8 Play and Stop
- There are member functions for audio playback and stop operations:
- Audio paused with `.pause()` resumes playback from the paused position
- On the other hand, audio stopped with `.stop()` returns the playback position to the beginning

| Code | Description |
| --- | --- |
| `.play()` | Play audio |
| `.pause()` | Pause audio |
| `.stop()` | Stop audio and return playback position to the beginning |
| `.isPlaying()` | Returns whether audio is playing |
| `.isPaused()` | Returns whether audio is paused |
| `.playOneShot()` | Play audio (overlapping playback) |
| `.pauseAllShots()` | Pause all overlapping audio |
| `.resumeAllShots()` | Resume all paused overlapping audio |
| `.stopAllShots()` | Stop all overlapping audio |

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	while (System::Update())
	{
		ClearPrint();

		// Whether playing
		Print << U"isPlaying: " << audio.isPlaying();

		// Whether paused
		Print << U"isPaused: " << audio.isPaused();

		if (SimpleGUI::Button(U"Play", Vec2{ 200, 20 }))
		{
			// Play/resume
			audio.play();
		}

		if (SimpleGUI::Button(U"Pause", Vec2{ 200, 60 }))
		{
			// Pause
			audio.pause();
		}

		if (SimpleGUI::Button(U"Stop", Vec2{ 200, 100 }))
		{
			// Stop and return playback position to the beginning
			audio.stop();
		}
	}
}
```


## 41.9 Volume Control
- To change audio volume, use `.setVolume(volume)` to set a value from 0.0 to 1.0
- The default volume is 1.0
- You can get the set volume with `.getVolume()`
- `.setVolume(volume)` does not apply to audio played with `.playOneShot()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	double volume = 1.0;

	while (System::Update())
	{
		ClearPrint();

		// Get current volume
		Print << audio.getVolume();

		if (SimpleGUI::Slider(U"volume: {:.2f}"_fmt(volume), volume, Vec2{ 200, 20 }, 160, 140))
		{
			// Set volume
			audio.setVolume(volume);
		}
	}
}
```


## 41.10 Volume Control Considering Human Characteristics
- The human ear perceives sound loudness logarithmically rather than linearly relative to volume
- While 0.0 → 0.5 causes a large change in perceived loudness, 0.5 → 1.0 doesn't feel like the sound gets much louder
- When implementing volume sliders, you can make volume changes closer to human perception by doing the following:

```cpp
# include <Siv3D.hpp>

double LinearToLog(double value)
{
	if (value == 0.0)
	{
		return 0.0;
	}

	const double minDb = -40.0; // Adjust as needed
	const double db = (minDb * (1.0 - value));
	return Math::Pow(10.0, (db / 20.0));
}

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	size_t index = 0;

	double volume = 1.0;

	while (System::Update())
	{
		if (SimpleGUI::RadioButtons(index, { U"Linear", U"Log" }, Vec2{ 200, 40 }, 160, 20))
		{
			if (index == 0)
			{
				audio.setVolume(volume);
			}
			else
			{
				audio.setVolume(LinearToLog(volume));
			}
		}

		if (index == 0)
		{
			if (SimpleGUI::Slider(U"volume: {:.4f}"_fmt(volume), volume, Vec2{ 400, 40 }, 160, 140))
			{
				audio.setVolume(volume);
			}
		}
		else
		{
			if (SimpleGUI::Slider(U"volume: {:.4f}"_fmt(LinearToLog(volume)), volume, Vec2{ 400, 40 }, 160, 140))
			{
				audio.setVolume(LinearToLog(volume));
			}
		}
	}
}
```


## 41.11 Fade In/Fade Out
- Passing time as an argument to `.play()`, `.pause()`, `.stop()` causes the volume to fade in/out over that time
- Pause and stop are delayed until fade out is complete

| Code | Description |
| --- | --- |
| `.play(duration)` | Start playing from volume 0 and change volume to 1 over the specified time |
| `.pause(duration)` | Change volume from current volume to 0 over the specified time and pause |
| `.stop(duration)` | Change volume from current volume to 0 over the specified time, stop and return playback position to the beginning |

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	while (System::Update())
	{
		ClearPrint();

		// Whether playing
		Print << U"isPlaying: " << audio.isPlaying();

		// Whether paused
		Print << U"isPaused: " << audio.isPaused();

		// Current volume
		Print << audio.getVolume();

		if (SimpleGUI::Button(U"Play", Vec2{ 200, 20 }))
		{
			// Play/resume over 2 seconds
			audio.play(2s);
		}

		if (SimpleGUI::Button(U"Pause", Vec2{ 200, 60 }))
		{
			// Pause over 2 seconds
			audio.pause(2s);
		}

		if (SimpleGUI::Button(U"Stop", Vec2{ 200, 100 }))
		{
			// Stop over 2 seconds and return playback position to the beginning
			audio.stop(2s);
		}
	}
}
```


## 41.12 Playback Volume Fade Control
- Using `.fadeVolume(volume, duration)` gradually changes the volume to `volume` over the specified time `duration`

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// Current volume
		Print << audio.getVolume();

		if (SimpleGUI::Button(U"1.0", Vec2{ 200, 20 }))
		{
			// Change volume to 1.0 over 2 seconds
			audio.fadeVolume(1.0, 2s);
		}

		if (SimpleGUI::Button(U"0.5", Vec2{ 200, 60 }))
		{
			// Change volume to 0.5 over 1 second
			audio.fadeVolume(0.5, 1s);
		}

		if (SimpleGUI::Button(U"0.1", Vec2{ 200, 100 }))
		{
			// Change volume to 0.1 over 1.5 seconds
			audio.fadeVolume(0.1, 1.5s);
		}
	}
}
```


## 41.13 Playback Speed Control
- To change playback speed, use `.setSpeed(speed)` or `.fadeSpeed(speed, duration)` to set the playback speed multiplier
- The default playback speed is 1.0
- Increasing playback speed makes the sound higher, and decreasing it makes it lower
- To avoid pitch changes when changing speed, use **41.20** pitch shift
- You can get the current playback speed with `.getSpeed()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// Current speed
		Print << audio.getSpeed();

		if (SimpleGUI::Button(U"1.2", Vec2{ 200, 20 }))
		{
			// Change speed to 1.2 over 2 seconds
			audio.fadeSpeed(1.2, 2s);
		}

		if (SimpleGUI::Button(U"1.0", Vec2{ 200, 60 }))
		{
			// Change speed to 1.0 over 1 second
			audio.fadeSpeed(1.0, 1s);
		}

		if (SimpleGUI::Button(U"0.8", Vec2{ 200, 100 }))
		{
			// Change speed to 0.8 over 1.5 seconds
			audio.fadeSpeed(0.8, 1.5s);
		}
	}
}
```


## 41.14 Pan Control
- To change left-right volume balance (pan), use `.setPan(pan)` or `.fadePan(pan, duration)` to set pan in the range -1.0 to 1.0
- `-1.0` is far left, `1.0` is far right, and the default is center `0.0`
- You can get the current pan with `.getPan()`

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// Current pan
		Print << audio.getPan();

		if (SimpleGUI::Button(U"0.9", Vec2{ 200, 20 }))
		{
			// Change pan to 0.9 over 2 seconds
			audio.fadePan(0.9, 2s);
		}

		if (SimpleGUI::Button(U"0.0", Vec2{ 200, 60 }))
		{
			// Change pan to 0.0 over 1 second
			audio.fadePan(0.0, 1s);
		}

		if (SimpleGUI::Button(U"-0.9", Vec2{ 200, 100 }))
		{
			// Change pan to -0.9 over 1.5 seconds
			audio.fadePan(-0.9, 1.5s);
		}
	}
}
```


## 41.15 Getting Playback Position
- There are member functions to get audio playback time and playback position:

| Code | Description |
| --- | --- |
| `.lengthSec()` | Returns total audio playback time (seconds) |
| `.lengthSample()` | Returns total audio playback sample count |
| `.posSec()` | Returns current playback position (seconds) |
| `.posSample()` | Returns current playback position (sample count) |

- These values can be used for timing effects with music and rhythm game timing

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// Total song length
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());

		// Current playback position
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
	}
}
```


## 41.16 Changing Playback Position
- There are member functions to change audio playback position:
 
| Code | Description |
| --- | --- |
| `.seekSamples(samples)` | Move playback position to sample count `samples` |
| `.seekTime(time)` | Move playback position to time `time` (seconds) |

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// Total song
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());

		// Playback position
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());

		if (SimpleGUI::Button(U"0 samples", Vec2{ 300, 20 }))
		{
			// Move to sample 0 (beginning of song)
			audio.seekSamples(0);
		}

		if (SimpleGUI::Button(U"441,000 samples", Vec2{ 300, 60 }))
		{
			// Move to sample 441,000
			audio.seekSamples(441000);
		}

		if (SimpleGUI::Button(U"20.0 sec", Vec2{ 300, 100 }))
		{
			// Move to 20 second position
			audio.seekTime(20.0);
		}
	}
}
```


## 41.17 Loop Playback
- You can set loop playback for audio
	- When song playback reaches the end, it automatically returns to the beginning and continues playing
- To loop audio, specify `Loop::Yes` in the `Audio` constructor

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3", Loop::Yes };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// Whether loop is set
		Print << audio.isLoop();

		// Loop count
		Print << audio.loopCount();

		// Total song
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());

		// Playback position
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
	}
}
```


## 41.18 Section Loop
- You can set a section within audio data to loop
	- When audio playback reaches the end position of the loop section, it returns to the beginning position of the loop section and continues playing
	- Due to internal implementation, noise may occur at loop boundaries
- To loop a section of audio, specify the loop section start position with `Arg::loopBegin` and end position with `Arg::loopEnd` in the `Audio` constructor
- Position units can be sample count or time, but `begin` and `end` must use the same units
- Specifying `Arg::loopEnd` doesn't retain audio waveform data beyond that section, saving memory consumption
- Streaming playback has the limitation that `Arg::loopEnd` cannot be specified
	- If needed, the audio data needs to be pre-cut

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Loop section from 1.5 seconds to 44.5 seconds
	const Audio audio{ U"example/test.mp3", Arg::loopBegin = 1.5s, Arg::loopEnd = 44.5s };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// Whether loop is set
		Print << audio.isLoop();

		// Loop count
		Print << audio.loopCount();

		// Total song
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());

		// Playback position
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
	}
}
```

## 41.19 Mixing Bus and Global Audio

### 41.19.1 Overview of Mixing Bus and Global Audio
- You can classify audio played in applications into groups like "BGM", "environmental sounds", "character voices", etc., and control volume and filters for each group
- Each group is called a **mixing bus**
- All audio is processed through one of four groups: MixBus0 to MixBus3
	- Default is set to MixBus0
- Audio that passes through the mixing bus goes through **global audio** for playback
	- You can also control volume and filters for global audio
- The final output volume is calculated as follows:
	- **Volume set in `Audio` × Mixing bus volume × Global audio volume**

<div class="noshadow-90"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/audio/19-1.png"></div>

### 41.19.2 Operations on Mixing Bus
- Individual mixing buses can perform the following operations:
	- Volume adjustment
	- Getting waveform samples during playback
	- Getting FFT results of waveforms during playback
	- Applying audio filters (explained in the next section)
- There are functions to adjust mixing bus volume:

| Code | Description |
| --- | --- |
| `GlobalAudio::BusSetVolume(busIndex, volume)` | Set volume of mixing bus `busIndex` to `volume` |
| `GlobalAudio::BusFadeVolume(busIndex, volume, duration)` | Change volume of mixing bus `busIndex` to `volume` over `duration` seconds |

### 41.19.3 Operations on Global Audio
- Global audio can perform the following operations:
	- Stop/resume all audio
	- Volume adjustment
	- Getting waveform samples during playback
	- Getting FFT results of waveforms during playback
- There are functions to adjust global audio volume:

| Code | Description |
| --- | --- |
| `GlobalAudio::SetVolume(volume)` | Set global audio volume to `volume` |
| `GlobalAudio::FadeVolume(volume, duration)` | Change global audio volume to `volume` over `duration` seconds |

### 41.19.4 Sample Code

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Piano C4 (Do) sound
	const Audio pianoC{ GMInstrument::Piano1, PianoKey::C4, 0.5s };

	// Piano D4 (Re) sound
	const Audio pianoD{ GMInstrument::Piano1, PianoKey::D4, 0.5s };

	// Piano E4 (Mi) sound
	const Audio pianoE{ GMInstrument::Piano1, PianoKey::E4, 0.5s };

	double globalVolume = 1.0, mixBus0Volume = 1.0, mixBus1Volume = 1.0;

	while (System::Update())
	{
		if (SimpleGUI::Slider(U"Global Vol", globalVolume, Vec2{ 20, 20 }, 120, 200))
		{
			// Change global audio volume
			GlobalAudio::SetVolume(globalVolume);
		}

		if (SimpleGUI::Slider(U"Bus0 Vol", mixBus0Volume, Vec2{ 20, 60 }, 120, 120))
		{
			// Change MixBus0 volume
			GlobalAudio::BusSetVolume(MixBus0, mixBus0Volume);
		}

		if (SimpleGUI::Slider(U"Bus1 Vol", mixBus1Volume, Vec2{ 300, 60 }, 120, 120))
		{
			// Change MixBus1 volume
			GlobalAudio::BusSetVolume(MixBus1, mixBus1Volume);
		}

		if (SimpleGUI::Button(U"C Bus0", Vec2{ 20, 100 }))
		{
			pianoC.playOneShot(MixBus0, 0.5);
		}

		if (SimpleGUI::Button(U"D Bus0", Vec2{ 20, 140 }))
		{
			pianoD.playOneShot(MixBus0, 0.5);
		}

		if (SimpleGUI::Button(U"E Bus0", Vec2{ 20, 180 }))
		{
			pianoE.playOneShot(MixBus0, 0.5);
		}

		if (SimpleGUI::Button(U"C Bus1", Vec2{ 300, 100 }))
		{
			pianoC.playOneShot(MixBus1, 0.5);
		}

		if (SimpleGUI::Button(U"D Bus1", Vec2{ 300, 140 }))
		{
			pianoD.playOneShot(MixBus1, 0.5);
		}

		if (SimpleGUI::Button(U"E Bus1", Vec2{ 300, 180 }))
		{
			pianoE.playOneShot(MixBus1, 0.5);
		}
	}
}
```


## 41.20 Pitch Shift
- Pitch shift changes the pitch of audio without changing the speed
- You can apply pitch shift filters to mixing buses

| Code | Description |
| --- | --- |
| `GlobalAudio::BusSetPitchShiftFilter(mixbus, index, pitchShift)` | Set pitch shift to the `index`-th filter of mixing bus `mixbus` |

- `pitchShift` is specified in semitone units
	- 0.0 represents no pitch change
	- 12.0 represents raising by one octave
	- -12.0 represents lowering by one octave

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Load audio file and create Audio
	const Audio audio{ U"example/test.mp3" };

	// Play audio
	audio.play();

	// Amount of pitch shift
	double pitchShift = 0.0;

	while (System::Update())
	{
		if (SimpleGUI::Slider(U"pitchShift: {:.2f}"_fmt(pitchShift), pitchShift, -12.0, 12.0, Vec2{ 40, 40 }, 160, 300))
		{
			// Set pitch shift to the 0th filter of MixBus0
			GlobalAudio::BusSetPitchShiftFilter(MixBus0, 0, pitchShift);
		}
	}
}
```


## 41.21 Getting Audio Waveforms
- From non-streaming audio, you can access the entire audio waveform data with `.getSamples(channel)`
- `channel` represents 0 for left channel and 1 for right channel, returning a pointer `const float*` to the beginning of each channel's waveform

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ U"example/test.mp3" };

	audio.play();

	const float* pSamples = audio.getSamples(0);

	LineString lines(800);

	while (System::Update())
	{
		const int64 posSample = audio.posSample();

		// Get 800 samples of waveform from current playback position
		for (int64 i = posSample; i < (posSample + 800); ++i)
		{
			if (i < audio.samples())
			{
				lines[i - posSample].set((i - posSample), (300 + pSamples[i] * 200));
			}
			else
			{
				lines[i - posSample].set((i - posSample), 300);
			}
		}

		// Draw waveform
		lines.draw(2);
	}
}
```


## 41.22 Getting Recent Playback Waveforms
- You can get recent 256 samples of waveform data that passed through mixing buses as `Array<float>`
- You can get composite waveforms of all audio played through mixing buses regardless of streaming

| Code | Source |
| --- | --- |
| `GlobalAudio::GetSamples()` | Global audio |
| `GlobalAudio::BusGetSamples(mixbus)` | Mixing bus |

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio1{ U"example/test.mp3" };

	const Audio audio2{ GMInstrument::Trumpet, PianoKey::E4, 0.5s };

	audio1.play();

	LineString lines(256);

	Array<float> samples;

	while (System::Update())
	{
		if (KeySpace.down())
		{
			audio2.playOneShot();
		}

		// Get recent 256 samples of waveform that passed through global audio
		GlobalAudio::GetSamples(samples);

		for (size_t i = 0; i < samples.size(); ++i)
		{
			lines[i].set((i * 800.0 / 256.0), (300 + samples[i] * 200));
		}

		// Draw waveform
		lines.draw(2);
	}
}
```


## 41.23 Audio Filters
- You can set up to 8 audio filters on one mixing bus for real-time audio waveform processing during playback

| Function (arguments omitted) | Description |
|--|--|
|`GlobalAudio::BusClearFilter()`| Turn off set filters |
|`GlobalAudio::BusSetLowPassFilter()`| Set low-pass filter |
|`GlobalAudio::BusSetHighPassFilter()`| Set high-pass filter |
|`GlobalAudio::BusSetEchoFilter()`|  Set echo filter |
|`GlobalAudio::BusSetReverbFilter()`|  Set reverb filter |
|`GlobalAudio::BusSetPitchShiftFilter()`|  Set pitch shift filter |

- Pitch shift filters are not available in the Web version of Siv3D
- You can use `GlobalAudio::SupportsPitchShift()` to check if pitch shift filters are available in the current execution environment
- The following sample is a demo of audio filter functionality
	- Clicking "Open audio file" allows you to open audio files saved on your computer through a file dialog
	- See **Tutorial 61** for file dialogs

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/audio/23.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	Audio audio;
	double posSec = 0.0;
	double volume = 1.0;
	double pan = 0.0;
	double speed = 1.0;
	bool loop = false;

	Array<float> busSamples;
	Array<float> globalSamples;
	FFTResult busFFT;
	FFTResult globalFFT;
	LineString lines(256, Vec2{ 0, 0 });

	bool pitch = false;
	double pitchShift = 0.0;

	bool lpf = false;
	double lpfCutoffFrequency = 800.0;
	double lpfResonance = 0.5;
	double lpfWet = 1.0;

	bool hpf = false;
	double hpfCutoffFrequency = 800.0;
	double hpfResonance = 0.5;
	double hpfWet = 1.0;

	bool echo = false;
	double delay = 0.1;
	double decay = 0.5;
	double echoWet = 0.5;

	bool reverb = false;
	bool freeze = false;
	double roomSize = 0.5;
	double damp = 0.0;
	double width = 0.5;
	double reverbWet = 0.5;

	while (System::Update())
	{
		ClearPrint();
		Print << U"GlobalAudio::GetActiveVoiceCount(): " << GlobalAudio::GetActiveVoiceCount();
		Print << U"isEmpty : " << audio.isEmpty();
		Print << U"isStreaming : " << audio.isStreaming();
		Print << U"sampleRate : " << audio.sampleRate();
		Print << U"samples : " << audio.samples();
		Print << U"lengthSec : " << audio.lengthSec();
		Print << U"posSample : " << audio.posSample();
		Print << U"posSec : " << (posSec = audio.posSec());
		Print << U"isActive : " << audio.isActive();
		Print << U"isPlaying : " << audio.isPlaying();
		Print << U"isPaused : " << audio.isPaused();
		Print << U"samplesPlayed : " << audio.samplesPlayed();
		Print << U"isLoop : " << (loop = audio.isLoop());
		Print << U"getLoopTimingtLoop : " << audio.getLoopTiming().beginPos << U", " << audio.getLoopTiming().endPos;
		Print << U"loopCount : " << audio.loopCount();
		Print << U"getVolume : " << (volume = audio.getVolume());
		Print << U"getPan : " << (pan = audio.getPan());
		Print << U"getSpeed : " << (speed = audio.getSpeed());

		if (SimpleGUI::Button(U"Open audio file", Vec2{ 60, 560 }))
		{
			audio.stop(0.5s);
			audio = Dialog::OpenAudio(Audio::Stream);
		}

		{
			GlobalAudio::BusGetSamples(MixBus0, busSamples);
			GlobalAudio::BusGetFFT(MixBus0, busFFT);

			for (auto&& [i, s] : Indexed(busSamples))
			{
				lines[i].set((300.0 + i), (200.0 - s * 100.0));
			}

			if (busSamples)
			{
				lines.draw(2, Palette::Orange);
			}

			for (auto&& [i, s] : Indexed(busFFT.buffer))
			{
				RectF{ Arg::bottomLeft(300 + i, 300), 1, (s * 4) }.draw();
			}
		}

		{
			GlobalAudio::GetSamples(globalSamples);
			GlobalAudio::GetFFT(globalFFT);

			for (auto&& [i, s] : Indexed(globalSamples))
			{
				lines[i].set((300.0 + i), (550.0 - s * 100.0));
			}

			if (globalSamples)
			{
				lines.draw(2, Palette::Orange);
			}

			for (auto&& [i, s] : Indexed(globalFFT.buffer))
			{
				RectF{ Arg::bottomLeft(300 + i, 650), 1, (s * 4) }.draw();
			}
		}

		if (SimpleGUI::Button(U"Play", Vec2{ 600, 20 }, 80, !audio.isPlaying()))
		{
			audio.play();
		}

		if (SimpleGUI::Button(U"Pause", Vec2{ 690, 20 }, 80, (audio.isPlaying() && !audio.isPaused())))
		{
			audio.pause();
		}

		if (SimpleGUI::Button(U"Stop", Vec2{ 780, 20 }, 80, (audio.isPlaying() || audio.isPaused())))
		{
			audio.stop();
		}

		if (SimpleGUI::Button(U"Play in 2s", Vec2{ 870, 20 }, 120, !audio.isPlaying()))
		{
			audio.play(2s);
		}

		if (SimpleGUI::Button(U"Pause in 2s", Vec2{ 1000, 20 }, 120, (audio.isPlaying() && !audio.isPaused())))
		{
			audio.pause(2s);
		}

		if (SimpleGUI::Button(U"Stop in 2s", Vec2{ 1130, 20 }, 120, (audio.isPlaying() || audio.isPaused())))
		{
			audio.stop(2s);
		}

		if (SimpleGUI::Slider(U"{:.1f} / {:.1f}"_fmt(posSec, audio.lengthSec()), posSec, 0.0, audio.lengthSec(), Vec2{ 600, 60 }, 160, 360))
		{
			if (MouseL.down() || !Cursor::DeltaF().isZero()) // Prevent continuous seeking (cause of noise)
			{
				audio.seekTime(posSec);
			}
		}

		if (SimpleGUI::CheckBox(loop, U"Loop", Vec2{ 1130, 60 }))
		{
			audio.setLoop(loop);
		}

		if (SimpleGUI::Slider(U"volume: {:.2f}"_fmt(volume), volume, Vec2{ 600, 110 }, 140, 130))
		{
			audio.setVolume(volume);
		}

		if (SimpleGUI::Button(U"0.0 in 2s", Vec2{ 880, 110 }, 110, audio.isActive()))
		{
			audio.fadeVolume(0.0, 2s);
		}

		if (SimpleGUI::Button(U"0.5 in 2s", Vec2{ 1000, 110 }, 110, audio.isActive()))
		{
			audio.fadeVolume(0.5, 2s);
		}

		if (SimpleGUI::Button(U"1.0 in 2s", Vec2{ 1120, 110 }, 110, audio.isActive()))
		{
			audio.fadeVolume(1.0, 2s);
		}

		if (SimpleGUI::Slider(U"pan: {:.2f}"_fmt(pan), pan, -1.0, 1.0, Vec2{ 600, 150 }, 140, 130))
		{
			audio.setPan(pan);
		}

		if (SimpleGUI::Button(U"-1.0 in 2s", Vec2{ 880, 150 }, 110, audio.isActive()))
		{
			audio.fadePan(-1.0, 2s);
		}

		if (SimpleGUI::Button(U"0.0 in 2s", Vec2{ 1000, 150 }, 110, audio.isActive()))
		{
			audio.fadePan(0.0, 2s);
		}

		if (SimpleGUI::Button(U"1.0 in 2s", Vec2{ 1120, 150 }, 110, audio.isActive()))
		{
			audio.fadePan(1.0, 2s);
		}

		if (SimpleGUI::Slider(U"speed: {:.3f}"_fmt(speed), speed, 0.0, 4.0, Vec2{ 600, 190 }, 140, 130))
		{
			audio.setSpeed(speed);
		}

		if (SimpleGUI::Button(U"0.8 in 2s", Vec2{ 880, 190 }, 110, audio.isActive()))
		{
			audio.fadeSpeed(0.8, 2s);
		}

		if (SimpleGUI::Button(U"1.0 in 2s", Vec2{ 1000, 190 }, 110, audio.isActive()))
		{
			audio.fadeSpeed(1.0, 2s);
		}

		if (SimpleGUI::Button(U"1.2 in 2s", Vec2{ 1120, 190 }, 110, audio.isActive()))
		{
			audio.fadeSpeed(1.2, 2s);
		}

		bool updatePitch = false;
		bool updateLPF = false;
		bool updateHPF = false;
		bool updateEcho = false;
		bool updateReverb = false;

		if (SimpleGUI::CheckBox(pitch, U"Pitch", Vec2{ 600, 240 }, 120, GlobalAudio::SupportsPitchShift()))
		{
			if (pitch)
			{
				updatePitch = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 0);
			}
		}
		updatePitch |= SimpleGUI::Slider(U"pitchShift: {:.2f}"_fmt(pitchShift), pitchShift, -12.0, 12.0, Vec2{ 720, 240 }, 160, 300);

		if (SimpleGUI::CheckBox(lpf, U"LPF", Vec2{ 600, 280 }, 120))
		{
			if (lpf)
			{
				updateLPF = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 1);
			}
		}
		updateLPF |= SimpleGUI::Slider(U"cutoffFrequency: {:.0f}"_fmt(lpfCutoffFrequency), lpfCutoffFrequency, 10, 4000, Vec2{ 720, 280 }, 220, 240);
		updateLPF |= SimpleGUI::Slider(U"resonance: {:.2f}"_fmt(lpfResonance), lpfResonance, 0.1, 8.0, Vec2{ 720, 310 }, 220, 240);
		updateLPF |= SimpleGUI::Slider(U"wet: {:.2f}"_fmt(lpfWet), lpfWet, Vec2{ 720, 340 }, 220, 240);

		if (SimpleGUI::CheckBox(hpf, U"HPF", Vec2{ 600, 380 }, 120))
		{
			if (hpf)
			{
				updateHPF = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 2);
			}
		}
		updateHPF |= SimpleGUI::Slider(U"cutoffFrequency: {:.0f}"_fmt(hpfCutoffFrequency), hpfCutoffFrequency, 10, 4000, Vec2{ 720, 380 }, 220, 240);
		updateHPF |= SimpleGUI::Slider(U"resonance: {:.2f}"_fmt(hpfResonance), hpfResonance, 0.1, 8.0, Vec2{ 720, 410 }, 220, 240);
		updateHPF |= SimpleGUI::Slider(U"wet: {:.2f}"_fmt(hpfWet), hpfWet, Vec2{ 720, 440 }, 220, 240);

		if (SimpleGUI::CheckBox(echo, U"Echo", Vec2{ 600, 480 }, 120))
		{
			if (echo)
			{
				updateEcho = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 3);
			}
		}
		updateEcho |= SimpleGUI::Slider(U"delay: {:.2f}"_fmt(delay), delay, Vec2{ 720, 480 }, 220, 240);
		updateEcho |= SimpleGUI::Slider(U"decay: {:.2f}"_fmt(decay), decay, Vec2{ 720, 510 }, 220, 240);
		updateEcho |= SimpleGUI::Slider(U"wet: {:.2f}"_fmt(echoWet), echoWet, Vec2{ 720, 540 }, 220, 240);

		if (SimpleGUI::CheckBox(reverb, U"Reverb", Vec2{ 600, 580 }, 120))
		{
			if (reverb)
			{
				updateReverb = true;
			}
			else
			{
				GlobalAudio::BusClearFilter(MixBus0, 4);
			}
		}
		updateReverb |= SimpleGUI::CheckBox(freeze, U"freeze", Vec2{ 720, 580 }, 110);
		updateReverb |= SimpleGUI::Slider(U"roomSize: {:.2f}"_fmt(roomSize), roomSize, 0.001, 1.0, { 830, 580 }, 150, 200);
		updateReverb |= SimpleGUI::Slider(U"damp: {:.2f}"_fmt(damp), damp, Vec2{ 720, 610 }, 220, 240);
		updateReverb |= SimpleGUI::Slider(U"width: {:.2f}"_fmt(width), width, Vec2{ 720, 640 }, 220, 240);
		updateReverb |= SimpleGUI::Slider(U"wet: {:.2f}"_fmt(reverbWet), reverbWet, Vec2{ 720, 670 }, 220, 240);

		if (pitch && updatePitch)
		{
			GlobalAudio::BusSetPitchShiftFilter(MixBus0, 0, pitchShift);
		}

		if (lpf && updateLPF)
		{
			GlobalAudio::BusSetLowPassFilter(MixBus0, 1, lpfCutoffFrequency, lpfResonance, lpfWet);
		}

		if (hpf && updateHPF)
		{
			GlobalAudio::BusSetHighPassFilter(MixBus0, 2, hpfCutoffFrequency, hpfResonance, hpfWet);
		}

		if (echo && updateEcho)
		{
			GlobalAudio::BusSetEchoFilter(MixBus0, 3, delay, decay, echoWet);
		}

		if (reverb && updateReverb)
		{
			GlobalAudio::BusSetReverbFilter(MixBus0, 4, freeze, roomSize, damp, width, reverbWet);
		}
	}

	// If there are playing audio, fade out before exiting
	if (GlobalAudio::GetActiveVoiceCount())
	{
		GlobalAudio::FadeVolume(0.0, 0.5s);
		System::Sleep(0.5s);
	}
}
```


## 41.24 Simultaneous Playback of Multiple Audio
- There are cases where you want to play multiple audio files with perfect sample-level synchronization
- To play multiple audio files in perfect synchronization, use audio groups
- For detailed samples and sample audio files, see [Siv3D-Sample | BGM Crossfade :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/tree/main/Samples/AudioCrossfade){:target="_blank"}


## 41.25 Real-time Waveform Writing
- By creating a class that inherits from `IAudioStream`, you can perform real-time waveform writing
- `void getAudio(float* left, float* right, const size_t samplesToWrite) override` is a function for writing waveforms
	- `left` and `right` are buffers for writing left and right channel waveforms respectively, and the number of samples to write is passed by `samplesToWrite`
- To end playback, override `bool hasEnded() override` to return `true`
- When rewinding to the beginning is performed, `void rewind() override` is called

```cpp
# include <Siv3D.hpp>

class MyAudioStream : public IAudioStream
{
public:

	void setFrequency(int32 frequency)
	{
		m_oldFrequency = m_frequency.load();

		m_frequency.store(frequency);
	}

private:

	size_t m_pos = 0;

	std::atomic<int32> m_oldFrequency = 440;

	std::atomic<int32> m_frequency = 440;

	void getAudio(float* left, float* right, const size_t samplesToWrite) override
	{
		const int32 oldFrequency = m_oldFrequency;
		const int32 frequency = m_frequency;
		const float blend = (1.0f / samplesToWrite);

		for (size_t i = 0; i < samplesToWrite; ++i)
		{
			const float t0 = (2_piF * oldFrequency * (static_cast<float>(m_pos) / Wave::DefaultSampleRate));
			const float t1 = (2_piF * frequency * (static_cast<float>(m_pos) / Wave::DefaultSampleRate));
			const float a = (Math::Lerp(std::sin(t0), std::sin(t1), (blend * i))) * 0.5f;

			*left++ = *right++ = a;
			++m_pos;
		}

		m_oldFrequency = frequency;

		m_pos %= Math::LCM(frequency, Wave::DefaultSampleRate);
	}

	bool hasEnded() override
	{
		return false;
	}

	void rewind() override
	{
		m_pos = 0;
	}
};

void Main()
{
	std::shared_ptr<MyAudioStream> audioStream = std::make_shared<MyAudioStream>();

	Audio audio{ audioStream };

	audio.play();

	double frequency = 440.0;

	while (System::Update())
	{
		if (SimpleGUI::Slider(U"{}Hz"_fmt(static_cast<int32>(frequency)), frequency, 220.0, 880.0, Vec2{ 40, 40 }, 120, 200))
		{
			audioStream->setFrequency(static_cast<int32>(frequency));
		}
	}
}
```