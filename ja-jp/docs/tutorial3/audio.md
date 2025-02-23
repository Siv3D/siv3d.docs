# 41. オーディオ再生
効果音や音楽の再生を制御する方法を学びます。

## 41.1 オーディオの作成
- 再生する音声データはオーディオクラス `Audio` で管理します
- オーディオの作成にはいくつかの方法があります
	- **41.2** 音声ファイルから作成
	- **41.3** 音声ファイルからストリーミングモードで作成
	- **41.4** 楽器の音の作成
	- **41.5** 音声波形データから作成
- オーディオの作成にはコストがかかるため、通常はメインループの前で行います
- メインループ内で作成する場合には、毎フレーム作成されないよう制御が必要です
- 同時に再生できるオーディオは最大で 64 です
	- ミキシングバスを含めるため、実際は 60 前後です

## 41.2 音声ファイルの読み込みと再生
- `Audio 変数名{ U"ファイルパス" };` で、音声ファイルから `Audio` を作成します
- ファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します
	- 例えば `U"example/test.mp3"` は、実行ファイルがあるフォルダ（`App` フォルダ）の `example/` フォルダにある `test.mp3` というファイルを指します
- Siv3D は次の音声フォーマットの読み込みをサポートします
	- 一部の音声フォーマットは OS によって対応状況が異なります

| フォーマット    | 拡張子                | Windows | macOS | Linux | Web |
|-----------|--------------------|:-------:|:-----:|:-----:|:---:|
| WAVE      | .wav               | ✅       | ✅     | ✅     | ✅   |
| MP3       | .mp3               | ✅       | ✅     | ✅     | ✅*  |
| AAC       | .m4a               | ✅       | ✅     | ✅     | ✅*  |
| OggVorbis | .ogg               | ✅       | ✅     | ✅     | ✅   |
| Opus      | .opus              | ✅       | ✅     | ✅     | ✅   |
| MIDI      | .mid               | ✅       | ✅     | ✅     | ✅   |
| WMA       | .wma               | ✅       |       |       |     |
| FLAC      | .flac              | ✅       | ✅     |       |     |
| AIFF      | .aif, .aiff, .aifc |         | ✅     |       |     |

<small>(*) ビルドオプションの設定と、特別な関数の使用が必要です。</small>

- オーディオを再生するには `Audio` の `.play()` を呼びます
- すでに再生中のオーディオに対して `.play()` をしても何も起こりません
- 同じオーディオを重ねて再生したい場合は、**41.6** の `.playOneShot()` を使います

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 音声ファイルを読み込んで Audio を作成する
	const Audio audio{ U"example/test.mp3" };

	// オーディオを再生する
	audio.play();

	while (System::Update())
	{

	}
}
```

- オーディオは、オーディオオブジェクトが存在していなければ再生ができません
- `Audio{ U"example/test.mp3" }.play();` のように一時オブジェクトを使うコードでは、オーディオの再生はできません


## 41.3 ストリーミング再生
- WAVE, MP3, OggVorbis, FLAC 形式の音声ファイルは、ストリーミング再生に対応します
- ストリーミング再生は、最初にファイル内容の全部を読み込むのではなく、一部だけを読み込んでオーディオを再生しながら、続く部分を逐次読み込む方式です
- ストリーミング再生を使うと、オーディオ作成時のファイルロード時間が大幅に短縮し、再生中のメモリ使用量も少なくなります
- ストリーミング再生を有効にするには、`Audio` のコンストラクタに `Audio::Stream` を渡してストリーミング再生をリクエストします
- もし、ストリーミング再生をリクエストしたファイルがストリーミング再生をサポートしていなかった場合は、通常の読み込みが行われます
- ストリーミング再生が有効になっているかは、`.isStreaming()` で調べられます

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 音声ファイルを読み込んで Audio を作成（ストリーミング再生をリクエスト）
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	// ストリーミング再生になる場合は true
	Print << audio.isStreaming();

	// オーディオを再生する
	audio.play();

	while (System::Update())
	{

	}
}
```

- ストリーミング再生を有効にしたオーディオには次のような制約がありますが、通常のオーディオ再生用途では問題になりません
	- 区間ループ再生において、ループ末尾位置をオーディオ終端以外に設定できない
	- `.getSamples()` でオーディオの音声波形データにアクセスできない
	- FFT を行うときのサンプル数が少なくなる


## 41.4 楽器の音の作成
- 楽器の種類と音の高さ、長さなどを指定してプログラムで楽器の音を生成し、オーディオを作成できます
- `Audio` のコンストラクタに `GMInstrument` で列挙される楽器名、`PianoKey` で列挙される音の高さ、音の長さを渡します
	- `Audio{ 楽器名, 音の高さ, ノート・オン時間 }`
		- ノート・オフ時間は 1.0 秒に設定されます
	- `Audio{ 楽器名, 音の高さ, ノート・オン時間, ノート・オフ時間 }`

??? info "楽器の一覧"
	| コード | 楽器名 |
	| --- | --- |
	| `GMInstrument::Piano1` | アコースティックピアノ |
	| `GMInstrument::Piano2` | ブライトピアノ |
	| `GMInstrument::Piano3` | エレクトリック・グランドピアノ |
	| `GMInstrument::Piano4` | ホンキートンクピアノ |
	| `GMInstrument::ElectricPiano1` | エレクトリック・グランド・ピアノ |
	| `GMInstrument::ElectricPiano2` | 	FM エレクトリックピアノ |
	| `GMInstrument::Harpsichord` | ハープシコード |
	| `GMInstrument::Clavinet` | クラビネット |
	| `GMInstrument::Celesta` | チェレスタ |
	| `GMInstrument::Glockenspiel` | グロッケンシュピール |
	| `GMInstrument::MusicBox` | オルゴール |
	| `GMInstrument::Vibraphone` | ヴィブラフォン |
	| `GMInstrument::Marimba` | マリンバ |
	| `GMInstrument::Xylophone` | シロフォン |
	| `GMInstrument::TubularBells` | チューブラーベル |
	| `GMInstrument::Dulcimer` | ダルシマー |
	| `GMInstrument::DrawbarOrgan` | ドローバー・オルガン |
	| `GMInstrument::PercussiveOrgan` | パーカッシブ・オルガン |
	| `GMInstrument::RockOrgan` | ロック・オルガン |
	| `GMInstrument::ChurchOrgan` | チャーチ・オルガン |
	| `GMInstrument::ReedOrgan` | リード・オルガン |
	| `GMInstrument::Accordion` | アコーディオン |
	| `GMInstrument::Harmonica` | ハーモニカ |
	| `GMInstrument::TangoAccordion` | タンゴ・アコーディオン |
	| `GMInstrument::NylonGuitar` | ナイロン・ギター |
	| `GMInstrument::SteelGuitar` | スティール・ギター |
	| `GMInstrument::JazzGuitar` | ジャズ・ギター |
	| `GMInstrument::CleanGuitar` | クリーン・ギター |
	| `GMInstrument::MutedGuitar` | ミュート・ギター |
	| `GMInstrument::OverdrivenGuitar` | オーバードライブ・ギター |
	| `GMInstrument::DistortionGuitar` | ディストーション・ギター |
	| `GMInstrument::GuitarHarmonics` | ギター・ハーモニクス |
	| `GMInstrument::AcousticBass` | アコースティック・ベース |
	| `GMInstrument::FingeredBass` | フィンガー・ベース |
	| `GMInstrument::PickedBass` | ピック・ベース |
	| `GMInstrument::FretlessBass` | フレットレス・ベース |
	| `GMInstrument::SlapBass1` | スラップ・ベース 1 |
	| `GMInstrument::SlapBass2` | スラップ・ベース 2 |
	| `GMInstrument::SynthBass1` | シンセ・ベース 1 |
	| `GMInstrument::SynthBass2` | シンセ・ベース 2 |
	| `GMInstrument::Violin` | ヴァイオリン |
	| `GMInstrument::Viola` | ヴィオラ |
	| `GMInstrument::Cello` | チェロ |
	| `GMInstrument::Contrabass` | コントラバス |
	| `GMInstrument::TremoloStrings` | トレモロ・ストリングス |
	| `GMInstrument::PizzicatoStrings` | ピッチカート・ストリングス |
	| `GMInstrument::OrchestralHarp` | オーケストラ・ハープ |
	| `GMInstrument::Timpani` | ティンパニ |
	| `GMInstrument::StringEnsemble1` | ストリング・アンサンブル 1 |
	| `GMInstrument::StringEnsemble2` | ストリング・アンサンブル 2 |
	| `GMInstrument::SynthStrings1` | シンセ・ストリングス 1 |
	| `GMInstrument::SynthStrings2` | シンセ・ストリングス 2 |
	| `GMInstrument::ChoirAahs` | 声「アー」 |
	| `GMInstrument::VoiceOohs` | 声「オー」 |
	| `GMInstrument::SynthChoir` | シンセヴォイス |
	| `GMInstrument::OrchestraHit` | オーケストラ・ヒット |
	| `GMInstrument::Trumpet` | トランペット |
	| `GMInstrument::Trombone` | トロンボーン |
	| `GMInstrument::Tuba` | チューバ |
	| `GMInstrument::MutedTrumpet` | ミュート・トランペット |
	| `GMInstrument::FrenchHorn` | フレンチ・ホルン |
	| `GMInstrument::BrassSection` | ブラス・セクション |
	| `GMInstrument::SynthBrass1` | シンセ・ブラス 1 |
	| `GMInstrument::SynthBrass2` | シンセ・ブラス 2 |
	| `GMInstrument::SopranoSax` | ソプラノ・サックス |
	| `GMInstrument::AltoSax` | アルト・サックス |
	| `GMInstrument::TenorSax` | テナー・サックス |
	| `GMInstrument::BaritoneSax` | バリトン・サックス |
	| `GMInstrument::Oboe` | オーボエ |
	| `GMInstrument::EnglishHorn` | イングリッシュ・ホルン |
	| `GMInstrument::Bassoon` | ファゴット |
	| `GMInstrument::Clarinet` | クラリネット |
	| `GMInstrument::Piccolo` | ピッコロ |
	| `GMInstrument::Flute` | フルート |
	| `GMInstrument::Recorder` | リコーダー |
	| `GMInstrument::PanFlute` | パン・フルート |
	| `GMInstrument::BlownBottle` | ブロウン・ボトル |
	| `GMInstrument::Shakuhachi` | 尺八 |
	| `GMInstrument::Whistle` | 口笛 |
	| `GMInstrument::Ocarina` | オカリナ |
	| `GMInstrument::SquareWave` | 矩形波 |
	| `GMInstrument::SawWave` | ノコギリ波 |
	| `GMInstrument::SynCalliope` | カリオペリード |
	| `GMInstrument::ChifferLead` | チフリード |
	| `GMInstrument::Charang` | チャランゴリード |
	| `GMInstrument::SoloVox` | 声リード |
	| `GMInstrument::FifthSawWave` | フィフスズリード |
	| `GMInstrument::BassAndLead` | ベース + リード |
	| `GMInstrument::Fantasia` | ファンタジア |
	| `GMInstrument::WarmPad` | ウォーム・パッド |
	| `GMInstrument::Polysynth` | ポリシンセ |
	| `GMInstrument::SpaceVoice` | スペース・ヴォイス |
	| `GMInstrument::BowedGlass` | ボウド・グラス |
	| `GMInstrument::MetalPad` | メタル・パッド |
	| `GMInstrument::HaloPad` | ハロー・パッド |
	| `GMInstrument::SweepPad` | スイープ・パッド |
	| `GMInstrument::IceRain` | 雨 |
	| `GMInstrument::Soundtrack` | サウンドトラック |
	| `GMInstrument::Crystal` | クリスタル |
	| `GMInstrument::Atmosphere` | アトモスフィア |
	| `GMInstrument::Brightness` | ブライトネス |
	| `GMInstrument::Goblin` | ゴブリン |
	| `GMInstrument::EchoDrops` | エコー・ドロップス |
	| `GMInstrument::StarTheme` | スター・テーマ |
	| `GMInstrument::Sitar` | シタール |
	| `GMInstrument::Banjo` | バンジョー |
	| `GMInstrument::Shamisen` | 三味線 |
	| `GMInstrument::Koto` | 琴 |
	| `GMInstrument::Kalimba` | カリンバ |
	| `GMInstrument::Bagpipe` | バグパイプ |
	| `GMInstrument::Fiddle` | フィドル |
	| `GMInstrument::Shanai` | シャハナイ |
	| `GMInstrument::TinkleBell` | ティンクル・ベル |
	| `GMInstrument::Agogo` | アゴゴ |
	| `GMInstrument::SteelDrums` | スチール・ドラム |
	| `GMInstrument::Woodblock` | ウッドブロック |
	| `GMInstrument::TaikoDrum` | 太鼓 |
	| `GMInstrument::MelodicTom` | メロディック・タム |
	| `GMInstrument::SynthDrum` | シンセ・ドラム |
	| `GMInstrument::ReverseCymbal` | リバース・シンバル |
	| `GMInstrument::GuitarFretNoise` | ギター・フレット・ノイズ |
	| `GMInstrument::BreathNoise` | ブレス・ノイズ |
	| `GMInstrument::Seashore` | 海岸 |
	| `GMInstrument::BirdTweet` | 鳥のさえずり |
	| `GMInstrument::TelephoneRing` | 電話のベル |
	| `GMInstrument::Helicopter` | ヘリコプター |
	| `GMInstrument::Applause` | 拍手 |
	| `GMInstrument::Gunshot` | 銃声 |


```cpp
# include <Siv3D.hpp>

void Main()
{
	// ピアノの C4 (ド) の音
	const Audio piano{ GMInstrument::Piano1, PianoKey::C4, 0.5s };

	// クラリネットの D4 (レ) の音
	const Audio clarinet{ GMInstrument::Clarinet, PianoKey::D4, 0.5s };

	// トランペットの E4 (ミ) の音。ノート・オン 2.0 秒、ノート・オフ 0.5 秒
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


## 41.5 音声波形データから作成
- プログラムで生成・加工した音声波形データ（`Wave` クラス）からオーディオを作成できます
- `Wave` クラスについては **チュートリアル 70** を参照してください

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
	// 音声波形データからオーディオを作成する
	const Audio audio{ MakeWave() };

	audio.play();

	while (System::Update())
	{

	}
}
```


## 41.6 同じオーディオの多重再生
- 1 つの `Audio` を重ねて再生する場合、`.play()` の代わりに `.playOneShot()` を使います
- `.playOneShot()` の引数では、音量、パン、再生スピード、ミキシングバスを設定できます

```cpp
void Audio::playOneShot(double volume = 1.0, double pan = 0.0, double speed = 1.0, MixBus = MixBus0) const;
```

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ピアノの C4 (ド) の音。
	const Audio piano{ GMInstrument::Piano1, PianoKey::C4, 0.5s };

	// クラリネットの D4 (レ) の音
	const Audio clarinet{ GMInstrument::Clarinet, PianoKey::D4, 0.5s };

	// トランペットの E4 (ミ) の音
	const Audio trumpet{ GMInstrument::Trumpet, PianoKey::E4, 0.5s };

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Piano", Vec2{ 20, 20 }))
		{
			// 音量 0.5 で再生する
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


## 41.7 空のオーディオ
- `Audio` 型のオブジェクトは、デフォルトでは**空のオーディオ**を持っています
- 空のオーディオは、「フワ」と鳴る 0.5 秒間の音声で、**有効なオーディオと同じように扱うことができ**、再生してもエラーは発生しません
- 音声ファイルのロードに失敗した場合も空のオーディオになります
- 空のオーディオであるかを調べるには、`if (audio.isEmpty())`, `if (audio)`, `if (not audio)` を使います

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 初期データを与えないと、空のオーディオになる
	Audio audioA;

	if (not audioA)
	{
		Print << U"audioA is empty";
	}

	// 音声ファイルの読み込みに失敗すると、空のオーディオになる
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


## 41.8 再生と停止
- オーディオの再生や停止操作を行う、次のようなメンバ関数があります
- `.pause()` で一時停止したオーディオは、一時停止した位置から再生を再開します
- 一方、`.stop()` で停止したオーディオは、再生位置が先頭に戻ります

| コード | 説明 |
| --- | --- |
| `.play()` | オーディオを再生します |
| `.pause()` | オーディオを一時停止します |
| `.stop()` | オーディオを停止して再生位置を先頭に戻します |
| `.isPlaying()` | オーディオが再生中であるかを返します |
| `.isPaused()` | オーディオが一時停止中であるかを返します |
| `.playOneShot()` | オーディオを再生します（重ねて再生） |
| `.pauseAllShots()` | 重ねて再生しているオーディオをすべて一時停止します |
| `.resumeAllShots()` | 一時停止中の重ねて再生しているオーディオをすべて再開します |
| `.stopAllShots()` | 重ねて再生しているオーディオをすべて停止します |

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	while (System::Update())
	{
		ClearPrint();

		// 再生されているか
		Print << U"isPlaying: " << audio.isPlaying();

		// 一時停止中であるか
		Print << U"isPaused: " << audio.isPaused();

		if (SimpleGUI::Button(U"Play", Vec2{ 200, 20 }))
		{
			// 再生・再開
			audio.play();
		}

		if (SimpleGUI::Button(U"Pause", Vec2{ 200, 60 }))
		{
			// 一時停止
			audio.pause();
		}

		if (SimpleGUI::Button(U"Stop", Vec2{ 200, 100 }))
		{
			// 停止して再生位置を最初に戻す
			audio.stop();
		}
	}
}
```


## 41.9 音量の変更
- オーディオの音量を変更するには `.setVolume(volume)` で 0.0～1.0 の値を設定します
- デフォルトの音量は 1.0 です
- 設定されている音量は `.getVolume()` で取得できます
- `.setVolume(volume)` は、`.playOneShot()` で再生している音声には適用されません

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

		// 現在の音量を取得
		Print << audio.getVolume();

		if (SimpleGUI::Slider(U"volume: {:.2f}"_fmt(volume), volume, Vec2{ 200, 20 }, 160, 140))
		{
			// 音量を設定
			audio.setVolume(volume);
		}
	}
}
```


## 41.10 人の特性を考慮した音量変更
- 人間の耳が感じる音の大きさは、音量に対して線形ではなく対数的です
- 0.0 → 0.5 は大きく音の大きさが変化する一方、0.5 → 1.0 はそれほど音が大きくならないように感じます
- 音量スライダーを実装する場合は、次のようにすることで、音量の変化を人間の感じ方に近づけることができます

```cpp
# include <Siv3D.hpp>

double LinearToLog(double value)
{
	if (value == 0.0)
	{
		return 0.0;
	}

	const double minDb = -40.0; // 必要に応じて調整
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


## 41.11 フェードイン・フェードアウト
- `.play()`, `.pause()`, `.stop()` の引数に時間を渡すと、その時間をかけて音量がフェードイン・フェードアウトします
- 一時停止と停止は、フェードアウトが完了するまで遅延されます

| コード | 説明 |
| --- | --- |
| `.play(duration)` | 音量 0 から再生し、指定した時間をかけて音量を 1 に変化させます |
| `.pause(duration)` | 現在の音量から指定した時間をかけて音量を 0 に変化させ、一時停止します |
| `.stop(duration)` | 現在の音量から指定した時間をかけて音量を 0 に変化させ、停止して再生位置を先頭に戻します |

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	while (System::Update())
	{
		ClearPrint();

		// 再生されているか
		Print << U"isPlaying: " << audio.isPlaying();

		// 一時停止中であるか
		Print << U"isPaused: " << audio.isPaused();

		// 現在の音量
		Print << audio.getVolume();

		if (SimpleGUI::Button(U"Play", Vec2{ 200, 20 }))
		{
			// 2 秒かけて再生・再開
			audio.play(2s);
		}

		if (SimpleGUI::Button(U"Pause", Vec2{ 200, 60 }))
		{
			// 2 秒かけて一時停止
			audio.pause(2s);
		}

		if (SimpleGUI::Button(U"Stop", Vec2{ 200, 100 }))
		{
			// 2 秒かけて停止して再生位置を最初に戻す
			audio.stop(2s);
		}
	}
}
```


## 41.12 再生音量のフェード制御
- `.fadeVolume(volume, duration)` を使うと、指定した時間 `duration` だけかけて、音量が徐々に `volume` へ変化します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 現在の音量
		Print << audio.getVolume();

		if (SimpleGUI::Button(U"1.0", Vec2{ 200, 20 }))
		{
			// 2 秒かけて音量を 1.0 に
			audio.fadeVolume(1.0, 2s);
		}

		if (SimpleGUI::Button(U"0.5", Vec2{ 200, 60 }))
		{
			// 1 秒かけて音量を 0.5 に
			audio.fadeVolume(0.5, 1s);
		}

		if (SimpleGUI::Button(U"0.1", Vec2{ 200, 100 }))
		{
			// 1.5 秒かけて音量を 0.1 に
			audio.fadeVolume(0.1, 1.5s);
		}
	}
}
```


## 41.13 再生スピードの変更
- 再生スピードを変えるには `.setSpeed(speed)` または `.fadeSpeed(speed, duration)` を使って、再生スピードの倍率を設定します
- デフォルトの再生スピードは 1.0 です
- 再生スピードを速めると音は高く聞こえ、遅くすると低く聞こえます
- スピードの増減時に音の高低が発生しないようにするには、**41.20** ピッチシフトを使います
- 現在の再生スピードは `.getSpeed()` で取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 現在のスピード
		Print << audio.getSpeed();

		if (SimpleGUI::Button(U"1.2", Vec2{ 200, 20 }))
		{
			// 2 秒かけてスピードを 1.2 に
			audio.fadeSpeed(1.2, 2s);
		}

		if (SimpleGUI::Button(U"1.0", Vec2{ 200, 60 }))
		{
			// 1 秒かけてスピードを 1.0 に
			audio.fadeSpeed(1.0, 1s);
		}

		if (SimpleGUI::Button(U"0.8", Vec2{ 200, 100 }))
		{
			// 1.5 秒かけてスピードを 0.8 に
			audio.fadeSpeed(0.8, 1.5s);
		}
	}
}
```


## 41.14 パンの変更
- 左右の音量バランス（パン）を変えるには `.setPan(pan)` または `.fadePan(pan, duration)` を使って、パンを -1.0～1.0 の範囲で設定します
- `-1.0` は最も左、`1.0` は最も右、デフォルトは中央の `0.0` です
- 現在のパンは `.getPan()` で取得できます

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 現在のパン
		Print << audio.getPan();

		if (SimpleGUI::Button(U"0.9", Vec2{ 200, 20 }))
		{
			// 2 秒かけてパンを 0.9 に
			audio.fadePan(0.9, 2s);
		}

		if (SimpleGUI::Button(U"0.0", Vec2{ 200, 60 }))
		{
			// 1 秒かけてパンを 0.0 に
			audio.fadePan(0.0, 1s);
		}

		if (SimpleGUI::Button(U"-0.9", Vec2{ 200, 100 }))
		{
			// 1.5 秒かけてパンを -0.9 に
			audio.fadePan(-0.9, 1.5s);
		}
	}
}
```


## 41.15 再生位置の取得
- オーディオの再生時間や再生位置を取得する次のようなメンバ関数があります

| コード | 説明 |
| --- | --- |
| `.lengthSec()` | オーディオの合計再生時間（秒）を返す |
| `.lengthSample()` | オーディオの合計再生サンプル数を返す |
| `.posSec()` | 現在の再生位置（秒）を返す |
| `.posSample()` | 現在の再生位置（サンプル数）を返す |

- 音楽にタイミングを合わせる演出や、音楽ゲームの判定に、これらの値を活用できます

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 曲全体の長さ
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());

		// 現在の再生位置
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
	}
}
```


## 41.16 再生位置の変更
- オーディオの再生位置を変更する次のようなメンバ関数があります
 
| コード | 説明 |
| --- | --- |
| `.seekSamples(samples)` | 再生位置をサンプル数 `samples`（サンプル）に移動する |
| `.seekTime(time)` | 再生位置を時間 `time`（秒）に移動する |

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3" };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 曲全体
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());

		// 再生位置
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());

		if (SimpleGUI::Button(U"0 samples", Vec2{ 300, 20 }))
		{
			// 0 サンプル目（曲の先頭）に移動
			audio.seekSamples(0);
		}

		if (SimpleGUI::Button(U"441,000 samples", Vec2{ 300, 60 }))
		{
			// 441,000 サンプル目に移動
			audio.seekSamples(441000);
		}

		if (SimpleGUI::Button(U"20.0 sec", Vec2{ 300, 100 }))
		{
			// 20 秒の位置に移動
			audio.seekTime(20.0);
		}
	}
}
```


## 41.17 ループ再生
- オーディオにはループ再生を設定できます
	- 曲の再生が終端に到達したとき、自動的に先頭に戻って再生を続けます
- オーディオをループさせたい場合、`Audio` のコンストラクタに `Loop::Yes` を指定します

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Audio audio{ Audio::Stream, U"example/test.mp3", Loop::Yes };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// ループが設定されているか
		Print << audio.isLoop();

		// ループ回数
		Print << audio.loopCount();

		// 曲全体
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());

		// 再生位置
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
	}
}
```


## 41.18 区間ループ
- 音声データのなかでループする区間を設定できます
	- オーディオの再生がループ区間の終端位置に到達したとき、ループ区間の先頭位置に戻って再生を続けます
	- 内部の仕様の都合で、ループの境界でノイズ音が生じる場合があります
- オーディオを区間ループさせるには、`Audio` コンストラクタにおいて、ループ区間の先頭位置を `Arg::loopBegin`, 終端位置を `Arg::loopEnd` で指定します
- 位置の単位は、サンプル数または時間を選べますが、`begin` と `end` で揃える必要があります
- `Arg::loopEnd` を指定すると、その区間以降の音声波形データは保持されず、メモリの消費量が節約されます
- ストリーミング再生では、`Arg::loopEnd` を指定できない制約があります
	- 必要な場合は音声データをあらかじめ事前にカットする加工が必要です

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 1.5 秒～44.5 秒の区間をループ
	const Audio audio{ U"example/test.mp3", Arg::loopBegin = 1.5s, Arg::loopEnd = 44.5s };

	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// ループが設定されているか
		Print << audio.isLoop();

		// ループ回数
		Print << audio.loopCount();

		// 曲全体
		Print << U"all: {:.1f} sec ({} samples)"_fmt(audio.lengthSec(), audio.samples());

		// 再生位置
		Print << U"play: {:.1f} sec ({} samples)"_fmt(audio.posSec(), audio.posSample());
	}
}
```

## 41.19 ミキシングバスとグローバルオーディオ

### 41.19.1 ミキシングバスとグローバルオーディオの概要
- アプリケーションで再生するオーディオを「BGM」「環境音」「キャラクターボイス」などのグループに分類し、グループごとに音量やフィルタなどを制御できます
- 各グループを**ミキシングバス**と呼びます
- すべてのオーディオは MixBus0 ～ MixBus3 の 4 つのグループのいずれかのミキシングバスで処理されます
	- デフォルトでは MixBus0 に設定されています
- ミキシングバスを通過したオーディオは、**グローバルオーディオ**を通って再生されます
	- グローバルオーディオについても、音量やフィルタなどを制御できます
- 最終的に出力される音量は次のように計算されます：
	- **`Audio` で設定された音量 × ミキシングバスの音量 × グローバルオーディオの音量**

<div class="noshadow-90"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/audio/19-1.png"></div>

### 41.19.2 ミキシングバスでの操作
- 個々のミキシングバスでは次のような操作ができます。
	- 音量の調整
	- 再生中の波形サンプルの取得
	- 再生中の波形の FFT の結果取得
	- オーディオフィルタの適用（次節で説明）
- ミキシングバスの音量を調整する次のような関数があります

| コード | 説明 |
| --- | --- |
| `GlobalAudio::BusSetVolume(busIndex, volume)` | ミキシングバス `busIndex` の音量を `volume` に設定する |
| `GlobalAudio::BusFadeVolume(busIndex, volume, duration)` | ミキシングバス `busIndex` の音量を `volume` に `duration` 秒かけて変化させる |

### 41.19.3 グローバルオーディオでの操作
- グローバルオーディオでは次のような操作ができます。
	- オーディオの一斉停止・再開
	- 音量の調整
	- 再生中の波形サンプルの取得
	- 再生中の波形の FFT の結果取得
- グローバルオーディオの音量を調整する次のような関数があります

| コード | 説明 |
| --- | --- |
| `GlobalAudio::SetVolume(volume)` | グローバルオーディオの音量を `volume` に設定する |
| `GlobalAudio::FadeVolume(volume, duration)` | グローバルオーディオの音量を `volume` に `duration` 秒かけて変化させる |

### 41.19.4 サンプルコード

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ピアノの C4 (ド) の音
	const Audio pianoC{ GMInstrument::Piano1, PianoKey::C4, 0.5s };

	// ピアノの D4 (レ) の音
	const Audio pianoD{ GMInstrument::Piano1, PianoKey::D4, 0.5s };

	// ピアノの E4 (ミ) の音
	const Audio pianoE{ GMInstrument::Piano1, PianoKey::E4, 0.5s };

	double globalVolume = 1.0, mixBus0Volume = 1.0, mixBus1Volume = 1.0;

	while (System::Update())
	{
		if (SimpleGUI::Slider(U"Global Vol", globalVolume, Vec2{ 20, 20 }, 120, 200))
		{
			// グローバルオーディオの音量を変更
			GlobalAudio::SetVolume(globalVolume);
		}

		if (SimpleGUI::Slider(U"Bus0 Vol", mixBus0Volume, Vec2{ 20, 60 }, 120, 120))
		{
			// MixBus0 の音量を変更
			GlobalAudio::BusSetVolume(MixBus0, mixBus0Volume);
		}

		if (SimpleGUI::Slider(U"Bus1 Vol", mixBus1Volume, Vec2{ 300, 60 }, 120, 120))
		{
			// MixBus1 の音量を変更
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


## 41.20 ピッチシフト
- ピッチシフトは、オーディオの速度を変えることなく、音の高さを変えることです
- ミキシングバスに、ピッチシフトフィルタを適用できます

| コード | 説明 |
| --- | --- |
| `GlobalAudio::BusSetPitchShiftFilter(mixbus, index, pitchShift)` | ミキシングバス `mixbus` の `index` 番目のフィルタにピッチシフトを設定する |

- `pitchShift` はセミトーン単位で指定します
	- 0.0 がピッチを変えないことを表します
	- 12.0 は 1 オクターブ上げることを表します
	- -12.0 は 1 オクターブ下げることを表します

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 音声ファイルを読み込んで Audio を作成する
	const Audio audio{ U"example/test.mp3" };

	// オーディオを再生する
	audio.play();

	// ピッチシフトの量
	double pitchShift = 0.0;

	while (System::Update())
	{
		if (SimpleGUI::Slider(U"pitchShift: {:.2f}"_fmt(pitchShift), pitchShift, -12.0, 12.0, Vec2{ 40, 40 }, 160, 300))
		{
			// MixBus0 の 0 番目のフィルタにピッチシフトを設定する
			GlobalAudio::BusSetPitchShiftFilter(MixBus0, 0, pitchShift);
		}
	}
}
```


## 41.21 オーディオ波形の取得
- ストリーミングでないオーディオからは、`.getSamples(channel)` でオーディオ全体の波形データにアクセスできます
- `channel` は、0 が左チャンネル、1 が右チャンネルを表し、それぞれのチャンネルの波形の先頭のポインタ `const float*` を返します

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

		// 現在の再生位置から 800 サンプル分の波形を取得する
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

		// 波形を描画する
		lines.draw(2);
	}
}
```


## 41.22 直近の再生波形の取得
- ミキシングバスを通過した直近 256 サンプルの波形データを `Array<float>` で取得できます
- ストリーミング等の有無にかかわらず、ミキシングバスを通して再生されるすべてのオーディオの合成波形を取得できます

| コード | 取得先 |
| --- | --- |
| `GlobalAudio::GetSamples()` | グローバルオーディオ |
| `GlobalAudio::BusGetSamples(mixbus)` | ミキシングバス |

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

		// グローバルオーディオを通過した直近 256 サンプル分の波形を取得する
		GlobalAudio::GetSamples(samples);

		for (size_t i = 0; i < samples.size(); ++i)
		{
			lines[i].set((i * 800.0 / 256.0), (300 + samples[i] * 200));
		}

		// 波形を描画する
		lines.draw(2);
	}
}
```


## 41.23 オーディオフィルタ
- 1 つのミキシングバスに最大 8 つのオーディオフィルタを設定し、オーディオの再生中に、リアルタイムでの音声波形加工ができます

| 関数（引数は省略） | 説明 |
|--|--|
|`GlobalAudio::BusClearFilter()`| 設定されているフィルタをオフにする |
|`GlobalAudio::BusSetLowPassFilter()`| ローパスフィルタを設定する |
|`GlobalAudio::BusSetHighPassFilter()`| ハイパスフィルタを設定する |
|`GlobalAudio::BusSetEchoFilter()`|  エコーフィルタを設定する |
|`GlobalAudio::BusSetReverbFilter()`|  リバーブフィルタを設定する |
|`GlobalAudio::BusSetPitchShiftFilter()`|  ピッチシフトフィルタを設定する |

- Web 版の Siv3D では、ピッチシフトフィルタを利用できません
- `GlobalAudio::SupportsPitchShift()` を使うと、現在の実行環境でピッチシフトフィルタを利用できるかを取得できます
- 次のサンプルは、オーディオフィルタ機能のデモです
	- 「Open audio file」をクリックすると、パソコンに保存されているオーディオファイルをファイルダイアログからオープンできます
	- ファイルダイアログについては、**チュートリアル 61** を参照してください

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/audio/21.png)

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
			if (MouseL.down() || !Cursor::DeltaF().isZero()) // シークの連続（ノイズの原因）を防ぐ
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

	// 再生中の音声があれば、フェードアウトさせてから終了
	if (GlobalAudio::GetActiveVoiceCount())
	{
		GlobalAudio::FadeVolume(0.0, 0.5s);
		System::Sleep(0.5s);
	}
}
```


## 41.24 複数オーディオの同時再生
- 複数のオーディオをサンプル単位で完全に同期させて再生させたいケースがあります
- 複数のオーディオを完全に同期して再生するには、オーディオグループを使用します
- 詳しいサンプルとサンプル用の音声ファイルは、[Siv3D-Sample | BGM クロスフェード :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/tree/main/Samples/AudioCrossfade){:target="_blank"} を参照してください


## 41.25 波形のリアルタイム書き込み
- `IAudioStream` を継承したクラスを作成することで、波形のリアルタイム書き込みを行うことができます
- `void getAudio(float* left, float* right, const size_t samplesToWrite) override` は、波形の書き込みを行うための関数です
	- `left` と `right` は、それぞれ左右のチャンネルの波形を書き込むためのバッファで、書き込む必要があるサンプル数が `samplesToWrite` によって渡されます
- 再生を終了する場合 `bool hasEnded() override` をオーバーライドして `true` を返します
- 先頭への巻き戻しが行われる場合に `void rewind() override` が呼ばれます

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
