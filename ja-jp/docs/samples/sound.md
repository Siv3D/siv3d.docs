# 音のサンプル

## 1. ピアノ

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/sound/1.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// 白鍵の大きさ
		constexpr Size KeySize{ 55, 400 };

		// ウインドウをリサイズする
		Window::Resize((12 * KeySize.x), KeySize.y);

		// 楽器の種類
		constexpr GMInstrument Instrument = GMInstrument::Piano1;

		// 鍵盤の数
		constexpr int32 NumKeys = 20;

		// 音を作成
		std::array<Audio, NumKeys> sounds;
		for (int32 i = 0; i < NumKeys; ++i)
		{
			sounds[i] = Audio{ Instrument, static_cast<uint8>(PianoKey::A3 + i), 0.5s };
		}

		// 対応するキー
		constexpr std::array<Input, NumKeys> Keys =
		{
			KeyTab, Key1, KeyQ,
			KeyW, Key3, KeyE, Key4, KeyR, KeyT, Key6, KeyY, Key7, KeyU, Key8, KeyI,
			KeyO, Key0, KeyP, KeyMinus, KeyEnter,
		};

		// 描画位置計算用のオフセット値（白鍵は偶数、黒鍵は奇数）
		constexpr std::array<int32, NumKeys> KeyPositions =
		{
			0, 1, 2, 4, 5, 6, 7, 8, 10, 11, 12, 13, 14, 15, 16, 18, 19, 20, 21, 22
		};

		while (System::Update())
		{
			// キーが押されたら対応する音を再生
			for (int32 i = 0; i < NumKeys; ++i)
			{
				if (Keys[i].down())
				{
					sounds[i].playOneShot(0.5);
				}
			}

			// 白鍵を描画する
			for (int32 i = 0; i < NumKeys; ++i)
			{
				// オフセット値が偶数であるものが白鍵
				if (IsEven(KeyPositions[i]))
				{
					RectF{ (KeyPositions[i] / 2 * KeySize.x), 0, KeySize.x, KeySize.y }
						.stretched(-1).draw(Keys[i].pressed() ? Palette::Pink : Palette::White);
				}
			}

			// 黒鍵を描画する
			for (int32 i = 0; i < NumKeys; ++i)
			{
				// オフセット値が奇数であるものが黒鍵
				if (IsOdd(KeyPositions[i]))
				{
					RectF{ (KeySize.x * 0.68 + KeyPositions[i] / 2 * KeySize.x), 0, (KeySize.x * 0.58), (KeySize.y * 0.62) }
						.draw(Keys[i].pressed() ? Palette::Pink : Color{ 24 });
				}
			}
		}
	}
	```


## 2. 音楽プレイヤー

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/sound/2.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	double ConvertVolume(double volume)
	{
		return ((volume == 0.0) ? 0.0 : Math::Eerp(0.01, 1.0, volume));
	}

	void Main()
	{
		// 音楽
		Audio audio;

		// 音量
		double volume = 1.0;

		// FFT の結果
		FFTResult fft;

		// 再生位置の変更の有無
		bool seeking = false;

		while (System::Update())
		{
			ClearPrint();

			// 再生・演奏時間
			const String time = FormatTime(SecondsF{ audio.posSec() }, U"M:ss")
				+ U" / " + FormatTime(SecondsF{ audio.lengthSec() }, U"M:ss");

			// プログレスバーの進み具合
			double progress = static_cast<double>(audio.posSample()) / audio.samples();

			if (audio.isPlaying())
			{
				// FFT 解析
				FFT::Analyze(fft, audio);

				// 結果を可視化
				for (int32 i = 0; i < Min(Scene::Width(), static_cast<int32>(fft.buffer.size())); ++i)
				{
					const double size = Pow(fft.buffer[i], 0.6f) * 1000;
					RectF{ Arg::bottomLeft(i, 480), 1, size }.draw(HSV{ 240.0 - i });
				}

				// 周波数表示
				if (InRange(Cursor::Pos().x, 0, 800))
				{
					Rect{ Cursor::Pos().x, 0, 1, 480 }.draw();
					Print << U"{:.1f} Hz"_fmt(Cursor::Pos().x * fft.resolution);
				}
			}

			Rect{ 0, 480, Scene::Width(), 120 }.draw(ColorF{ 0.5 });

			// フォルダから音楽ファイルを開く
			if (SimpleGUI::Button(U"Open", Vec2{ 40, 500 }, 100))
			{
				audio.stop(0.5s);
				audio = Dialog::OpenAudio();
				audio.setVolume(ConvertVolume(volume));
				audio.play();
			}

			// 再生
			if (SimpleGUI::Button(U"\U000F040A", Vec2{ 150, 500 }, 60, (audio && (not audio.isPlaying()))))
			{
				audio.setVolume(ConvertVolume(volume));
				audio.play(0.2s);
			}

			// 一時停止
			if (SimpleGUI::Button(U"\U000F03E4", Vec2{ 220, 500 }, 60, audio.isPlaying()))
			{
				audio.pause(0.2s);
			}

			// 音量
			if (SimpleGUI::Slider(((volume == 0.0) ? U"\U000F075F" : (volume < 0.5) ? U"\U000F0580" : U"\U000F057E"),
				volume, Vec2{ 40, 540 }, 30, 120, (not audio.isEmpty())))
			{
				audio.setVolume(ConvertVolume(volume));
			}

			// スライダー
			if (SimpleGUI::Slider(time, progress, Vec2{ 200, 540 }, 130, 420, (not audio.isEmpty())))
			{
				audio.pause(0.05s);

				while (audio.isPlaying()) // 再生が止まるまで待機
				{
					System::Sleep(0.01s);
				}

				// 再生位置を変更
				audio.seekSamples(static_cast<size_t>(audio.samples() * progress));

				// ノイズを避けるため、スライダーから手を離すまで再生は再開しない
				seeking = true;
			}
			else if (seeking && MouseL.up())
			{
				// 再生を再開
				audio.play(0.05s);
				seeking = false;
			}
		}

		// 終了時に再生中の場合、音量をフェードアウト
		if (audio.isPlaying())
		{
			audio.fadeVolume(0.0, 0.3s);
			System::Sleep(0.3s);
		}
	}
	```


## 3. マイクで入力した音の周波数解析

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/sound/3.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// マイクをセットアップ（ただちに録音をスタート）
		Microphone mic{ StartImmediately::Yes };

		if (not mic)
		{
			// マイクを利用できない場合、終了
			throw Error{ U"Microphone not available" };
		}

		FFTResult fft;

		while (System::Update())
		{
			// FFT の結果を取得
			mic.fft(fft);

			ClearPrint();

			if (InRange(Cursor::Pos().x, 0, 800))
			{
				Print << U"{:.1f} Hz"_fmt(Cursor::Pos().x * fft.resolution);
			}

			// 結果を可視化
			for (int32 i = 0; i < 800; ++i)
			{
				const double size = (Pow(fft.buffer[i], 0.6f) * 1200);
				RectF{ Arg::bottomLeft(i, 600), 1, size }.draw(HSV{ 240 - i });
			}

			// 周波数表示
			Rect{ Cursor::Pos().x, 0, 1, Scene::Height() }.draw();

			ClearPrint();
			Print << U"{:.1f} Hz"_fmt(Cursor::Pos().x * fft.resolution);
		}
	}
	```


## 4. ZIP ファイルに含まれる音声ファイルを再生する
事前に `music/test.mp3` を `music.zip` に圧縮しておきます。

??? memo "コード"
	=== "非ストリーミング再生"
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

	=== "ストリーミング再生"
		ファイルを一時ファイルに展開してからストリーミング再生します。
		```cpp
		# include <Siv3D.hpp>

		void Main()
		{
			ZIPReader zip{ U"music.zip" };

			Print << zip.enumPaths();

			FilePath temporaryFilePath;

			if (const Blob blob = zip.extractToBlob(U"music/test.mp3"))
			{
				Print << U"ZIP データ展開完了";

				temporaryFilePath = FileSystem::UniqueFilePath();

				Print << temporaryFilePath << U" に保存";

				if (blob.save(temporaryFilePath))
				{
					Print << U"保存に成功";
				}
				else
				{
					Print << U"保存に失敗";
				}
			}
			else
			{
				Print << U"データ展開に失敗";
			}

			Audio audio{ Audio::Stream, temporaryFilePath };

			Print << U"isStreaming: " << audio.isStreaming();

			audio.play();

			while (System::Update())
			{

			}

			// Audio をリリース
			audio.release();

			// どの Audio からも参照されていなければファイル削除可能
			FileSystem::Remove(temporaryFilePath);
		}
		```


## 5. 楽譜記述言語の自作

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/sound/5.png)

??? memo "コード"
	```txt title="score.txt"
	ドレミドレミ
	```

	```cpp
	# include <Siv3D.hpp>

	String LoadScore(const FilePath& path)
	{
		// テキストファイル読み込み
		TextReader reader{ path };

		if (not reader)
		{
			throw Error{ U"score.txt が見つかりません" };
		}

		String result;

		String line;

		// 一行ずつ読み込む
		while (reader.readLine(line))
		{
			// score の末尾に追加
			result += line;
		}

		return result;
	}

	void Main()
	{
		// 楽譜を格納する変数
		const String score = LoadScore(U"score.txt");

		Print << U"読み込んだ楽譜: " << score;

		// ド、レ、ミの音を用意
		const Audio soundDo{ s3d::GMInstrument::Piano1, PianoKey::C4, 0.5s };
		const Audio soundRe{ s3d::GMInstrument::Piano1, PianoKey::D4, 0.5s };
		const Audio soundMi{ s3d::GMInstrument::Piano1, PianoKey::E4, 0.5s };
		// 参考
		// ド: C4, レ: D4, ミ: E4, ファ: F4, ソ: G4, ラ: A4, シ: B4, ド: C5, ...
		// ド#: CS4, レ#: DS4, ...

		// 再生位置
		int32 pos = -1;

		// 音量
		double volume = 0.5;

		// 即座に開始するストップウォッチ
		Stopwatch stopwatch{ StartImmediately::Yes };

		while (System::Update())
		{
			// ストップウォッチの経過時間（ミリ秒）/ 1000 を newPos とする
			const int32 newPos = (stopwatch.ms() / 1000);

			if (pos != newPos)
			{
				pos = newPos;

				// 範囲内であれば
				if (pos < score.size())
				{
					// pos 番目の文字
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

## 6. 音の高さを変えずに再生速度を変更する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/sound/6.png)

??? memo "コード"
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
				// 再生速度の変更
				audio.setSpeed(speed);

				// ピッチシフトの計算
				pitchShift = -(Math::Log2(speed) * 12);

				// ピッチシフトの適用
				GlobalAudio::BusSetPitchShiftFilter(MixBus0, 0, pitchShift);
			}

			// ピッチシフトに連動して動くスライダー
			if (SimpleGUI::Slider(U"PitchShift: {:.2f}"_fmt(pitchShift), pitchShift, -24, 24, Vec2{ 40, 80 }, 180, 240, false)) {}
		}
	}
	```

## 7. BGM クロスフェード

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/AudioCrossfade/Screenshot/2.png)

[Siv3D-Sample | BGM クロスフェード :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/blob/main/Samples/AudioCrossfade){:target="_blank" .md-button}

