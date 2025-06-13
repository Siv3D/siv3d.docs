# 79. Text-to-Speech
Learn text-to-speech functionality.

## 79.1 Say
- Using the text-to-speech feature `Say`, you can make text speak with the same syntax as `Print`
- You can change the speaker language with `TextToSpeech::SetDefaultLanguage()`
- Depending on OS settings, certain languages may not be installed by default
- Does not work on Linux

```cpp
# include <Siv3D.hpp>

void Main()
{
	TextToSpeech::SetDefaultLanguage(LanguageCode::EnglishUS);
	//TextToSpeech::SetDefaultLanguage(LanguageCode::Japanese);

    Say << U"Hello, world!";
    
	while (System::Update())
	{

	}
}
```


## 79.2 Text-to-Speech Supported Languages
- You can check if text-to-speech supports a specified language with `TextToSpeech::HasLanguage(languageCode)`
- You can easily add supported languages by installing additional language packs on the OS

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << TextToSpeech::HasLanguage(LanguageCode::Japanese);
	Print << TextToSpeech::HasLanguage(LanguageCode::EnglishUS);
	Print << TextToSpeech::HasLanguage(LanguageCode::ChineseCN);

	while (System::Update())
	{

	}
}
```
```txt title="Output Example"
true
true
false
```


## 79.3 Detailed Text-to-Speech Settings
- Using `TextToSpeech::Speak(text, languageCode)` allows detailed speech settings
- Set volume with `TextToSpeech::SetVolume(volume)`
    - `volume` is specified in the range 0.0 to 1.0
- Set speech speed with `TextToSpeech::SetSpeed(speed)`
    - `speed` is specified in the range 0.0 to 2.0
- Use `TextToSpeech::Pause()` to pause speech and `TextToSpeech::Resume()` to resume

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/tts/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	double volume = 1.0;

	double speed = 1.0;

	while (System::Update())
	{
		if (SimpleGUI::Slider(U"Volume", volume, 0.0, 1.0, Vec2{ 40, 40 }))
		{
			TextToSpeech::SetVolume(volume);
		}

		if (SimpleGUI::Slider(U"Speed", speed, 0.5, 2.0, Vec2{ 40, 80 }))
		{
			TextToSpeech::SetSpeed(speed);
		}

		if (SimpleGUI::Button(U"Speak", Vec2{ 40, 120 }, 120))
		{
			TextToSpeech::Speak(U"We currently support multiple input and output file formats.", LanguageCode::EnglishUS);
		}

		if (TextToSpeech::IsSpeaking())
		{
			if (SimpleGUI::Button(U"Pause", Vec2{ 40, 160 }, 120))
			{
				TextToSpeech::Pause();
			}
		}
		else
		{
			if (SimpleGUI::Button(U"Resume", Vec2{ 40, 160 }, 120))
			{
				TextToSpeech::Resume();
			}
		}
	}
}
```