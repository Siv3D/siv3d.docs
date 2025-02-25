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


## 79.2 XXXXX
- XXX

```cpp

```


## 79.3 XXXXX
- XXX

```cpp

```


## 79.4 XXXXX
- XXX

```cpp

```

