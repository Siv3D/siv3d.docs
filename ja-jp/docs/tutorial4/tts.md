# 79. テキスト読み上げ
テキストを読み上げる機能を学びます。

## 79.1 Say
- テキスト読み上げ機能 `Say` を使うと、`Print` と同じような書き方で、テキストを読み上げることができます
- 読み上げ話者の言語は `TextToSpeech::SetDefaultLanguage()` で変更できます
- OS の設定によっては特定の言語がデフォルトでインストールされていない場合があります
- Linux 版では動作しません

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


## 79.2 テキスト読み上げの対応言語
- `TextToSpeech::HasLanguage(languageCode)` で、指定した言語の読み上げに対応しているかを確認できます
- OS に追加の言語パックをインストールすることで、対応言語を簡単に追加できます

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
```txt title="出力例"
true
true
false
```


## 79.3 文章読み上げの詳細設定
- `TextToSpeech::Speak(text, languageCode)` を使うと、読み上げの詳細設定ができます
- 音量を `TextToSpeech::SetVolume(volume)` で設定できます
    - `volume` は 0.0 から 1.0 の範囲で指定します
- 読み上げ速度を `TextToSpeech::SetSpeed(speed)` で設定できます
    - `speed` は 0.0 から 2.0 の範囲で指定します
- 読み上げを一時停止するには `TextToSpeech::Pause()` を、再開するには `TextToSpeech::Resume()` を使います

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/tts/3.png)

```cpp

```
