# 演習 C - 楽譜記述言語自作

テキストファイルに記述した楽譜を演奏するプログラムを作ります。


### 楽譜ファイル

あらかじめ次のように書かれた楽譜ファイル `score.txt` を、作成したプロジェクトの `App/` フォルダ内に配置しておきます。

```
ドレミドレミ
```

??? summary "プロジェクトのフォルダを簡単に開く方法 (Visual Studio)"
    Visual Studio のソリューション エクスプローラー上でプロジェクト名を右クリックし、「エクスプローラーでフォルダーを開く」を押すと、プロジェクトのフォルダが開きます。その中にある App フォルダの中に score.txt を配置します。

    ![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/make/folder.png)


テキストファイルから数値をパースする方法については、演習 E も参照してください。

### プログラム

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 楽譜を格納する変数
	String score;

	{
		// テキストファイル読み込み
		TextReader reader{ U"score.txt" };

		if (not reader)
		{
			throw Error{ U"score.txt が見つかりません" };
		}

		String line;

		// 一行ずつ読み込む
		while (reader.readLine(line))
		{
			// score の末尾に追加
			score += line;
		}
	}

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
		// ストップウォッチの経過時間（ミリ秒） / 1000 を newPos とする
		const int32 newPos = (stopwatch.ms() / 1000);

		if (pos != newPos)
		{
			pos = newPos;

			// 範囲内であれば
			if (pos < score.size())
			{
				// pos 番目の文字
				const char32 ch = score[pos];

				Print << pos << U": " << ch;

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