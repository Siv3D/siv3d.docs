description: OpenSiv3D のチュートリアル

!!! warning "このページよりも新しいドキュメントがあります"
	このドキュメントは古い OpenSiv3D v0.4.3 向けです。2021 年9 月 18 日に最新の OpenSiv3D v0.6.0 がリリースされました。最新のドキュメントは [Siv3D リファレンス v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation) です。

# 13. オーディオ再生

この章では、効果音や音楽の再生を制御する方法を学びます。

## 13.1 音声ファイルの読み込みと再生

オーディオを再生したいときは `Audio` を作成し、`.play()` で再生します。再生中のオーディオに `.play()` をしても何も起こりません。重ねて再生したい場合は、この章の後半に出てくる `.playOneShot()` を使います。

音声ファイルから `Audio` を作成するには、`Audio` のコンストラクタ引数に、読み込みたい音声ファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します。リリース用のアプリを作るときには、のちの章で説明する「リソース」パスの使用を推奨します。

`Texture` や `Font` と同じように、`Audio` の作成にはメモリ確保などの実行時負荷がかかります。メインループの中で毎フレーム新しい `Audio` を作成するのは避け、作成が 1 回だけで済むようにしましょう。

```C++
# include <Siv3D.hpp>

void Main()
{
	// 音声ファイルを読み込んで Audio を作成
	const Audio audio(U"example/test.mp3");

	// オーディオを再生
	audio.play();

	while (System::Update())
	{

	}
}
```

### 対応している音声フォーマット
OpenSiv3D v0.4.0 では、4 種類の音声フォーマットの読み込みがサポートされています。

| フォーマット     | 拡張子  | 対応状況       |
|------------|------|------------|
| WAVE       | wav  | ✔          |
| MP3        | mp3  | ✔          |
| AAC        | m4a  | ✔ (Windows / macOS のみ) |
| Ogg Vorbis | ogg  | ✔          |
| Opus       | opus | (将来のバージョン) |


## 13.2 一時停止と巻き戻し
再生中のオーディオを一時停止するには `.pause()`, 停止して再生位置を最初に戻すには `.stop()` を呼びます。

```C++
# include <Siv3D.hpp>

void Main()
{
	const Audio audio(U"example/test.mp3");

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Play", Vec2(20, 20)))
		{
			// 再生
			audio.play();
		}

		if (SimpleGUI::Button(U"Pause", Vec2(20, 60)))
		{
			// 一時停止
			audio.pause();
		}

		if (SimpleGUI::Button(U"Stop", Vec2(20, 100)))
		{
			// 停止して再生位置を最初に戻す
			audio.stop();
		}
	}
}
```


## 13.3 音量を変える
音量を変えるには `.setVolume()` に 0.0～1.0 の値を設定します。デフォルトでは最大の 1.0 です。

```C++
# include <Siv3D.hpp>

void Main()
{
	const Audio audio(U"example/test.mp3");
	
	audio.play();

	double volume = 1.0;

	while (System::Update())
	{
		if (SimpleGUI::Slider(volume, Vec2(20, 20)))
		{
			// 音量を設定 (0.0 - 1.0)
			audio.setVolume(volume);
		}
	}
}
```


## 13.4 再生スピードを変える
再生スピードを変えるには `.setSpeed()` に、スピードの倍率にあたる 0.1～2.0 の値を設定します。デフォルトでは 1.0 です。再生スピードが速くなると音は高く聞こえ、遅くなると低く聞こえます。

```C++
# include <Siv3D.hpp>

void Main()
{
	const Audio audio(U"example/test.mp3");
	
	audio.play();

	double speed = 1.0;

	while (System::Update())
	{
		if (SimpleGUI::Slider(U"{:.2f}"_fmt(speed), speed, 0.1, 2.0, Vec2(20, 20)))
		{
			// 再生スピードを設定 (0.1 - 2.0)
			audio.setSpeed(speed);
		}
	}
}
```


## 13.5 再生位置を取得する
オーディオの合計再生時間（秒）は `.lengthSec()`, 合計再生サンプルは `.samples()` で取得できます。現在の再生位置を `.posSec()` では秒、 `.posSample()` ではサンプル数で取得できます。再生位置より少し先行するバッファ送信済みのサンプル位置を `.streamPosSample()` で取得できます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/13/5-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Audio audio(U"example/test.mp3");
	
	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 曲全体の長さ
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());
		
		// 現在の再生位置
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
		
		// 現在のバッファ送信済み位置
		Print << U"stream: {} samples"_fmt(audio.streamPosSample());
	}
}
```


## 13.6 再生位置を変更する
再生位置を変更するには、`.setPosSample()` で移動先の位置をサンプル単位で指定するか、`.setPosSec()` 移動先の位置を時間（秒）で指定します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/13/6-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Audio audio(U"example/test.mp3");
	
	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 曲全体
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());
		
		// 再生位置
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
		
		// バッファ送信済み位置
		Print << U"stream: {} samples"_fmt(audio.streamPosSample());

		if (SimpleGUI::Button(U"0 samples", Vec2(300, 20)))
		{
			// 0 サンプル目（曲の先頭）に移動
			audio.setPosSample(0);
		}

		if (SimpleGUI::Button(U"441,000 samples", Vec2(300, 60)))
		{
			// 441,000 サンプル目に移動
			audio.setPosSample(441000);
		}

		if (SimpleGUI::Button(U"20.0 sec", Vec2(300, 100)))
		{
			// 20 秒の位置に移動
			audio.setPosSec(20.0);
		}
	}
}
```


## 13.7 ループ再生する
曲の再生が終端に到達したとき、自動的に先頭からループ再生させたい場合は、`Audio` のコンストラクタに `Arg::loop = true` を指定します。

```C++
# include <Siv3D.hpp>

void Main()
{
	// ループ再生を有効にする
	const Audio audio(U"example/test.mp3", Arg::loop = true);
	
	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 曲全体
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());
		
		// 再生位置
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
		
		// バッファ送信済み位置
		Print << U"stream: {} samples"_fmt(audio.streamPosSample());

		// ループが有効か
		Print << U"loop: {}"_fmt(audio.isLoop());
	}
}
```

`.setLoop()` に `true` を設定することでも同様の効果があります。

```C++
# include <Siv3D.hpp>

void Main()
{
	Audio audio(U"example/test.mp3");
	
	// ループ再生を有効にする
	audio.setLoop(true);

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 曲全体
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());
		
		// 再生位置
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
		
		// バッファ送信済み位置
		Print << U"stream: {} samples"_fmt(audio.streamPosSample());

		// ループが有効か
		Print << U"loop: {}"_fmt(audio.isLoop());
	}
}
```


## 13.8 範囲を指定してループ再生する
再生時は曲の先頭から開始し、指定したループ終端位置に到達したときに、指定したループ先頭位置からループ再生させるには、先頭位置を `Arg;;loopBegin`, 終端位置を `Arg::loopEnd` で指定します。

```C++
# include <Siv3D.hpp>

void Main()
{
    // 再生位置（秒）を基準にループの始点と終点を設定
	const Audio audio(U"example/test.mp3", Arg::loopBegin = 1.5s, Arg::loopEnd = 44.5s);

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 曲全体
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());
		
		// 再生位置
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
		
		// バッファ送信済み位置
		Print << U"stream: {} samples"_fmt(audio.streamPosSample());

		// ループが有効か
		Print << U"loop: {}"_fmt(audio.isLoop());

		if (const auto loop = audio.getLoop())
		{
			// ループの開始位置（サンプル）
			Print << U"loopBegin: {} samples"_fmt(loop->beginPos);

			// ループの終了位置（サンプル）
			Print << U"loopEnd: {} samples"_fmt(loop->endPos);
		}
	}
}
```

ループ位置はサンプル数でも指定できます。

```C++
# include <Siv3D.hpp>

void Main()
{
    // サンプル数を基準にループの始点と終点を設定
	const Audio audio(U"example/test.mp3", Arg::loopBegin = 66150, Arg::loopEnd = 1962450);

	audio.play();

	while (System::Update())
	{

	}
}
```

`.setLoop()` でも同様の効果があります。

```C++
# include <Siv3D.hpp>

void Main()
{
	Audio audio(U"example/test.mp3");

	audio.setLoop(Arg::loopBegin = 1.5s, Arg::loopEnd = 44.5s);

	audio.play();

	while (System::Update())
	{

	}
}
```


## 13.9 楽器の音を生成する
楽器の種類と音の高さ、長さを指定して、プログラムで楽器の音を生成し、そこから `Audio` を作成できます。`Audio` のコンストラクタに `GMInstrument` で列挙されている楽器名、`PianoKey` で列挙されている音の高さ、音の長さを渡します。

!!! info
	音の長さは 0.5 秒なら `0.5s`, 2 秒なら `2s` のように指定します。ここで指定する音の長さは目安であり、実際に作成される `Audio` はこれよりも長くなる場合があります。

```C++
# include <Siv3D.hpp>

void Main()
{
	// ピアノの C4 (ド) の音
	const Audio piano(GMInstrument::Piano1, PianoKey::C4, 0.5s);

	// クラリネットの D4 (レ) の音
	const Audio clarinet(GMInstrument::Clarinet, PianoKey::D4, 0.5s);

	// トランペットの E4 (ミ) の音
	const Audio trumpet(GMInstrument::Trumpet, PianoKey::E4, 0.5s);

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Piano", Vec2(20, 20)))
		{
			piano.play();
		}

		if (SimpleGUI::Button(U"Clarinet", Vec2(20, 60)))
		{
			clarinet.play();
		}

		if (SimpleGUI::Button(U"Trumpet", Vec2(20, 100)))
		{
			trumpet.play();
		}
	}
}
```


## 13.10 同じ音声を重ねて再生する
1 つの `Audio` を重ねて再生したい場合には `.play()` の代わりに `.playOneShot()` を使います。

```C++
# include <Siv3D.hpp>

void Main()
{
	// ピアノの C4 (ド) の音
	const Audio piano(GMInstrument::Piano1, PianoKey::C4, 0.5s);

	// クラリネットの D4 (レ) の音
	const Audio clarinet(GMInstrument::Clarinet, PianoKey::D4, 0.5s);

	// トランペットの E4 (ミ) の音
	const Audio trumpet(GMInstrument::Trumpet, PianoKey::E4, 0.5s);

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Piano", Vec2(20, 20)))
		{
			// 音量 0.5 で再生
			piano.playOneShot(0.5);
		}

		if (SimpleGUI::Button(U"Clarinet", Vec2(20, 60)))
		{
			clarinet.playOneShot(0.5);
		}

		if (SimpleGUI::Button(U"Trumpet", Vec2(20, 100)))
		{
			trumpet.playOneShot(0.5);
		}
	}
}
```


## 13.11 オーディオのプログラムあれこれ

### ファイルの読み込みチェック
`if (audio)` で、オーディオが作成されているかどうかをチェックできます。

```C++
# include <Siv3D.hpp>

void Main()
{
	// 存在しない音声ファイルを開こうとする
	const Audio audio(U"aaa/bbb.mp3");

	// オーディオの作成ができていなかったら
	if (!audio)
	{
		// 例外を投げて終了
		throw Error(U"Failed to create Audio");
	}

	while (System::Update())
	{

	}
}
```

### 空のオーディオ
空（から）の `Audio` を再生すると、「ポワー」と鳴るダミーの効果音になります。

```C++
# include <Siv3D.hpp>

void Main()
{
	// 音声ファイルの読み込みに失敗すると、空のオーディオになる
	const Audio audio(U"aaa/bbb.mp3");

    // ダミーの効果音が鳴る
	audio.play();

	while (System::Update())
	{

	}
}
```

### 再生中かをチェック
再生中のオーディオは `.isPlaying()` が `true` を返します。

```C++
# include <Siv3D.hpp>

void Main()
{
	const Audio audio(U"example/test.mp3");

	audio.play();

	while (System::Update())
	{
		// 再生中なら「Pause」ボタン
		if (audio.isPlaying())
		{
			if (SimpleGUI::Button(U"Pause", Vec2(20, 20), 120))
			{
				audio.pause();
			}
		}
		else // 停止中なら「Play」ボタン
		{
			if (SimpleGUI::Button(U"Play", Vec2(20, 20), 120))
			{
				audio.play();
			}
		}
	}
}
```


