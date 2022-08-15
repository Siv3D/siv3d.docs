# 演習 E - 音ゲー基礎

タイミングよくノートを押す、音ゲーの基本プログラムを作ります。  
お手本のプログラムでは、左から順に ++1++ ++2++ ++3++ ++4++ キーでノートを狙います。  
実際のゲームでは `Audio` の `.posSec()` や `.posSample()` から経過時間を取得しますが、ここでは音声ファイルを使わずに `Stopwatch` で経過時間を決めています。


### 譜面ファイル

あらかじめ次のように書かれた譜面ファイル `notes.txt` を、作成したプロジェクトの `App/` フォルダ内に配置しておきます。

```
2000 0
2500 1
3000 2
3500 3
4000 3
4500 2
5000 1
5500 0
6000 0
6500 1
7000 2
7500 3
8000 3
8500 2
9000 1
9500 0
```

??? summary "プロジェクトのフォルダを簡単に開く方法 (Visual Studio)"
    Visual Studio のソリューション エクスプローラー上でプロジェクト名を右クリックし、「エクスプローラーでフォルダーを開く」を押すと、プロジェクトのフォルダが開きます。その中にある App フォルダの中に notes.txt を配置します。

    ![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/learn/make/folder.png)


### プログラム

```cpp
# include <Siv3D.hpp>

// ノート
struct Note
{
	// ノートの時刻
	int32 time;

	// 押すべきキーのインデックス (0, 1, 2, 3)
	int32 key;

	// 消えたら false
	bool active = true;
};

// ノート情報を譜面ファイルからロードする関数
Array<Note> LoadNotes(const FilePath& path)
{
	TextReader reader{ path };

	if (not reader)
	{
		throw Error{ U"譜面 {} が見つかりません。"_fmt(path) };
	}

	Array<Note> notes;

	String line;

	// 1 行ずつ読み込む
	while (reader.readLine(line))
	{
		// 空白行はスキップ
		if (line.isEmpty())
		{
			continue;
		}

		// 読み込んだ行を半角スペースで分割
		const Array<String> params = line.split(U' ');

		// 分割した結果が 2 要素出ない場合不正な譜面
		if (params.size() != 2)
		{
			throw Error{ U"不正な譜面です。" };
		}

		// 分割した結果をそれぞれ int32 型に変換
		notes.emplace_back(Parse<int32>(params[0]), Parse<int32>(params[1]));
	}

	return notes;
}

// ノートの座標を計算する関数
Vec2 GetNotePos(const Note& note, int32 time)
{
	const double x = (250 + note.key * 100);
	const double y = (500 - (note.time - time) * 0.25);
	return{ x, y };
}

// ノートを押したときのエフェクト
struct NoteEffect : IEffect
{
	Vec2 m_start;

	String m_text;

	Font m_font;

	NoteEffect(const Vec2& start, const String& text, const Font& font)
		: m_start{ start }
		, m_text{ text }
		, m_font{ font } {}

	bool update(double t) override
	{
		Circle{ m_start, (30 + t * 30) }.drawFrame(20 * (0.5 - t));

		m_font(m_text).drawAt(m_start.movedBy(0, t * -120), Palette::Orange);

		return (t < 0.5);
	}
};

void Main()
{
	// ノート配列
	Array<Note> notes = LoadNotes(U"notes.txt");

	// 判定キー
	const Array<Input> Keys = { Key1, Key2, Key3, Key4 };

	// 時間測定用ストップウォッチ
	Stopwatch stopwatch{ StartImmediately::Yes };

	// フォント
	const Font font{ 40, Typeface::Heavy };

	// エフェクト管理
	Effect effect;

	while (System::Update())
	{
		// 経過時間（ミリ秒）
		const int32 time = stopwatch.ms();

		ClearPrint();

		Print << time;

		////////////////////////////////
		//
		//	状態更新
		//
		////////////////////////////////

		for (auto& note : notes)
		{
			// 消えているノートはスキップ
			if (not note.active)
			{
				continue;
			}

			// 現在のタイムとノートのタイムとの差（ミリ秒）
			const int32 diffMillisec = (time - note.time);

			// 差の絶対値が 250 ミリ秒未満なら
			if (Abs(diffMillisec) < 250)
			{
				// ノートに対応するキーが押されていたら
				if (Keys[note.key].down())
				{
					// ノートを消す
					note.active = false;

					// ノートの座標
					const Vec2 notePos = GetNotePos(note, time);

					// エフェクトを追加
					effect.add<NoteEffect>(Vec2{ notePos.x, 500 }, U"Good", font);
				}
			}

			// 250 ミリ秒以上の遅れはミス
			if (note.active && (250 <= diffMillisec))
			{
				// ノートを消す
				note.active = false;
			}
		}

		////////////////////////////////
		//
		//	描画
		//
		////////////////////////////////

		// 長方形を描画
		Rect{ 0, 480, 800, 40 }.draw(ColorF{ 0.5 });

		// ノートの描画
		for (const auto& note : notes)
		{
			// 消えているノートはスキップ
			if (not note.active)
			{
				continue;
			}

			// ノートの座標
			const Vec2 notePos = GetNotePos(note, time);

			// 画面内にあるノートのみ描画する
			if (-100.0  < notePos.y)
			{
				Circle{ notePos, 30 }.draw();
			}
		}

		// エフェクトの描画
		effect.update();
	}
}
```
