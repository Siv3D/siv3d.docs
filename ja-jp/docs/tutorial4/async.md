# 76. 非同期処理

## 76.1 非同期処理の概要
- Siv3D の `Main()` 関数はシングルスレッドで実行されるため、メインループ内で計算に時間のかかる関数を呼んだ場合、関数の結果が返るまで処理がそこでストップし、画面の更新が停止したり、フレームレートが低下したりします
- Siv3D の非同期 API を使うと、完了までに時間のかかる処理を非同期（基本的には別のスレッド）に実行させ、それが完了するまで `Main()` 関数内で別の処理やフレームの更新を進めることができます

### 注意事項
- `Main()` 以外のスレッドでは、Siv3D の描画に関連する API（`.draw()` やレンダーステート、シェーダ、テクスチャなどの作成や操作）を使用できません。唯一の例外として `Print` は使用できます
- `Main()` 以外のスレッドでアセット（`Texture`, `Audio`, `Font`, `PixelShader` など）を作成したい場合は、アセット管理（**チュートリアル 50**）が提供する非同期機能を使います。それ以外の方法では正常に動作しない場合があります
- HTTP クライアント（**チュートリアル 62**）や OpenAI API（**チュートリアル 67**）のように、明示的な非同期用の関数が提供されている機能については、それらを使うことが推奨されます
- `Array`, `Stopwatch`, `Image`, `Wave`, `BinaryReader`, `JSON`, `TextWriter`, `NavMesh` のように、Siv3D のコアシステムと密接にかかわらないスタンドアローンの機能の多くはメインスレッド以外でも使用可能です
- 複数のスレッドで同じオブジェクトを共有する場合は、データ競合が発生しないよう、適切な同期処理を実装してください

### エンジン内の並行処理
- Siv3D エンジンは、さまざまな内部処理についてデフォルトで複数のスレッドを活用する構造になっています
- おもにオーディオ再生、Web カメラ処理、通信処理などは非同期で実行されます


## 76.2 非同期でのアセット作成
- `Texture`, `Audio`, `Font`, `PixelShader` などのアセットを非同期で作成する場合、適切な方法は **アセット管理** が提供する非同期 API を利用することです
- アセットの非同期ロードが完了したかは `IsReady()` で確認できます
- `Wait()` をすると、ロードが完了するまでメインスレッドの待機を発生させます
- 非同期ロード中にそのアセットにアクセスした場合は、空のアセットが返ってきます
- 同時に非同期ロードできるアセット数に上限は設けられていません

!!! warning "OpenGL バックエンドでの注意"
	- OpenGL バックエンド（macOS と Linux のデフォルト, および Windows で選択した場合）では、`TextureAsset` の非同期ロードが `System::Update()` 内で進行します
	- `TextureAsset` の非同期ロード中は `System::Update()` の呼び出しを通常どおりの頻度で行ってください
	
```cpp
# include <Siv3D.hpp>

void Main()
{
	const String preloadText = U"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";

	FontAsset::Register(U"MyFont", FontMethod::MSDF, 48, Typeface::Bold);
	TextureAsset::Register(U"MyTexture", U"example/bay.jpg");
	AudioAsset::Register(U"MyAudio", Audio::Stream, U"example/test.mp3");
	AudioAsset::Register(U"MyMIDI", U"example/midi/test.mid");

	// 非同期ロードを開始する
	FontAsset::LoadAsync(U"MyFont", preloadText);
	TextureAsset::LoadAsync(U"MyTexture");
	AudioAsset::LoadAsync(U"MyAudio");
	AudioAsset::LoadAsync(U"MyMIDI");

	while (System::Update())
	{
		ClearPrint();

		// ロードが完了したかを調べる
		Print << FontAsset::IsReady(U"MyFont");
		Print << TextureAsset::IsReady(U"MyTexture");
		Print << AudioAsset::IsReady(U"MyAudio");
		Print << AudioAsset::IsReady(U"MyMIDI");
	}
}
```


## 76.3 （サンプル）複数画像ファイルの非同期ロード
- アセット管理機能の非同期 API を利用して、指定したフォルダに含まれる複数の画像ファイルを非同期でロードして表示するサンプルです
- マルチスレッドで画像の読み込みを行うため、シングルスレッドに比べて数分の一以下の時間ですべての読み込みが完了することが期待されます

```cpp
# include <Siv3D.hpp>

// ファイルパスが画像ファイルであるかを返す関数（簡易的な実装）
bool IsImageFilePath(const FilePath& path)
{
	const String extension = FileSystem::Extension(path);

	// 拡張子 png, jpg, jpeg のファイルを画像ファイルと見なす 
	return (extension == U"png")
		|| (extension == U"jpg")
		|| (extension == U"jpeg");
}

// フォルダ選択ダイアログで選択したフォルダ内の画像ファイルのパス一覧を返す関数
Array<FilePath> GetImageFilePaths()
{
	// フォルダ選択ダイアログでフォルダが選択されたら
	if (const auto directory = Dialog::SelectFolder())
	{
		// そのフォルダに含まれる画像ファイルのパス一覧を返す
		return FileSystem::DirectoryContents(*directory, Recursive::No)
			.filter(IsImageFilePath);
	}

	return{};
}

void Main()
{
	Window::Resize(1200, 800);

	const Array<FilePath> imageFilePaths = GetImageFilePaths() // 画像ファイルのパス一覧のうち
		.take(24); // 最大 24 ファイルを取得

    // テクスチャアセット名を記録する配列
	Array<AssetName> assetNames(imageFilePaths.size());

    // 各ファイルについて
	for (size_t i = 0; i < imageFilePaths.size(); ++i)
	{
		// テクスチャアセット名（任意）
		const AssetName assetName = U"texture_{}"_fmt(i);

		// 画像ファイルパス
		const FilePath path = imageFilePaths[i];

        // テクスチャアセット名を記録する
		assetNames[i] = assetName;

		// アセットを登録する
		TextureAsset::Register(assetName, path, TextureDesc::Mipped);

		// 非同期での読み込みを開始する
		TextureAsset::LoadAsync(assetName);
	}

	while (System::Update())
	{
		// 各アセットについて
		for (size_t i = 0; i < imageFilePaths.size(); ++i)
		{
			const double x = (100.0 + (i % 6) * 200.0);
			const double y = (100.0 + (i / 6) * 200.0);

			if (TextureAsset::IsReady(assetNames[i])) // 非同期ロードが完了していれば
			{
				// テクスチャを表示する
				TextureAsset(assetNames[i]).resized(200).drawAt(x, y);
			}
			else
			{
				// ロード中であれば、代わりに円を表示する
				Circle{ x, y, 50 }.drawFrame(20, ColorF{ 0.75 });
			}
		}
	}
}
```


## 76.4 非同期処理の API
- Siv3D で非同期処理を記述する場合、`AsyncTask<Type>` クラスと `Async()` 関数を使います
- C++ 標準の `std::future<Type>` や `std::async()` に似た使い方ができます

### 76.4.1 `AsyncTask<Type>`
- ある関数を非同期実行し、その状態や結果を管理する、非同期タスククラスです
- 通常は `Async()` 関数によって作成します
- `AsyncTask` は次のいずれかの状態を持ちます：
	- ① 非同期処理を持っていない
	- ② 非同期処理を持っていて、タスクが実行中であり、結果はまだ返せない
	- ③ 非同期処理を持っていて、タスクが完了しており、結果をすぐに返せる

#### メンバ関数

```cpp title="非同期処理を持っているかを返す"
bool AsyncTask<Type>::isValid() const;
```

- 戻り値: 非同期処理を持っている場合 `true`, それ以外の場合は `false`
- `AsyncTask<Type>::get()` を呼ぶと、非同期処理を持たない状態に戻ります

-----------------------------------

```cpp title="タスクが完了した非同期処理を持っていて、結果をすぐに返せる状態であるかを返す"
bool AsyncTask<Type>::isReady() const;
```

- 戻り値: タスクが完了した非同期処理を持っていて、結果をすぐに返せる状態である場合 `true`, それ以外の場合は `false`
- `AsyncTask<Type>::get()` を呼ぶと、非同期処理を持たない状態に戻ります

-----------------------------------

```cpp title="タスクが完了した非同期処理の結果を返す"
Type AsyncTask<Type>::get();
```

- 戻り値: タスクが完了した非同期処理の結果
- タスクが完了していない場合は完了まで待機します

-----------------------------------

```cpp title="非同期処理のタスク完了を待つ"
void AsyncTask<Type>::wait() const;
```

- 非同期処理を持っていない場合、この関数はすぐに制御を返します


### 76.4.2 Async() 関数

```cpp title="非同期タスクを作成する"
template <class Fty, class... Args>
auto Async(Fty&& f, Args&&... args);
```

- `f`: 非同期で実行する関数
- `args`: 関数 `f` に渡す引数
	- 参照渡しの場合は `std::ref()` を使って渡します
- 戻り値: 関数 `f` の戻り値型の非同期タスク (`AsyncTask<f の戻り値型>`)


## 76.5 非同期タスクの基本
- `AsyncTask` と `Async()` を使って、非同期処理を実行する基本的な使い方を示します
- 次のサンプルコードでは、ユーザーがボタンを押すと、画面のアニメーションを止めることなく 5 秒間の重い処理（`F(5)`）が裏で実行され、完了すると結果（5）を表示します
- 長時間かかる処理中でも UI が応答可能な状態を維持できることを確認できます
- プログラム終了時には、もし実行中の非同期タスクがあれば、その完了を待機します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/async/5.png)

```cpp
# include <Siv3D.hpp>

// n 秒後に n を返す関数（重い処理を Sleep で表現）
int32 F(int32 n)
{
	// n 秒スリープ
	System::Sleep(n * 1s);

	return n;
}

void Main()
{
	Scene::SetBackground(ColorF{ 1.0, 0.98, 0.96 });

	// 非同期タスク
	AsyncTask<int32> task;

	while (System::Update())
	{
		// 非同期タスクを持っていない時にボタンを押せる
		if (SimpleGUI::Button(U"Call", Vec2{ 600, 20 }, unspecified, (not task.isValid())))
		{
			// 関数 F を実行する非同期タスクを作成。F の引数に 5 を渡す
			task = Async(F, 5);
		}

		// 非同期タスクが完了したら
		if (task.isReady())
		{
			// 結果を取得する
			Print << task.get();
		}

		const double angle = (Scene::Time() * 30_deg);

		for (int32 i = 0; i < 12; ++i)
		{
			const double theta = (i * 30_deg + angle);

			const Vec2 pos = OffsetCircular{ Vec2{ 400, 300 }, 200, theta };

			pos.asCircle(28)
				.drawShadow(Vec2{ 0, 4 }, 12, 4)
				.draw(HSV{ (i * 30), 0.8, 1.0 })
				.drawFrame(3, 2, ColorF{ 1.0 });
		}
	}

	// 実行途中のタスクがあれば完了まで待つ
    if (task.isValid())
    {
	    task.wait();
    }
}
```


## 76.6 非同期タスクの中断
- 非同期タスクの処理を途中で安全に中断できる仕組みを実装できます
- 中断機能は、長時間実行される処理をユーザー操作によって制御するために重要です
- **76.5** のサンプルを拡張し、「Abort」ボタンを押すことで実行中の非同期処理を即座に中断します

### 中断のしくみ
- `std::atomic<bool>` を使用して中断フラグを実装します
	- アトミック型を使用することでスレッド間の安全な値の共有が保証されます
	- 参照渡しで非同期処理とメインスレッド間でフラグを共有します
- 非同期処理内では定期的に中断フラグを確認し、フラグが立っていれば処理を中止します
- 中断時は特別な戻り値（この例では `-1`）を返すことで、中断されたことを呼び出し側に通知します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/async/6.png)

```cpp
# include <Siv3D.hpp>

// n 秒後に n を返す関数（中断に対応）
int32 F(int32 n, const std::atomic<bool>& abort)
{
	for (int i = 0; i < (n * 10); ++i)
	{
		// 100 ミリ秒ごとに中断フラグを確認する
		if (abort) // 中断フラグが true なら
		{
			// 完全に処理が完了する前にそこで中断する
			return -1; // 中断された場合は特別な値を返す
		}

		System::Sleep(100ms);
	}

	return n; // 正常終了時は引数をそのまま返す
}

void Main()
{
	Scene::SetBackground(ColorF{ 1.0, 0.98, 0.96 });

	// 非同期タスク
	AsyncTask<int32> task;

	// 中断フラグ（初期値は false）
	std::atomic<bool> abort{ false };

	while (System::Update())
	{
		// 非同期タスクを持っていない時にのみ Call ボタンを有効化
		if (SimpleGUI::Button(U"Call", Vec2{ 600, 20 }, unspecified, (not task.isValid())))
		{
			// 関数 F を実行する非同期タスクを作成する
			// F の第 1 引数に 5 を、第 2 引数に中断フラグの参照を渡す
			task = Async(F, 5, std::ref(abort));
		}

		// 非同期タスクが実行中の場合のみ Abort ボタンを有効化
		if (SimpleGUI::Button(U"Abort", Vec2{ 600, 60 }, unspecified, task.isValid()))
		{
			// 中断フラグを true にして非同期処理に中断を指示する
			abort = true;
		}

		// 非同期タスクが完了したら
		if (task.isReady())
		{
			// 結果を取得する
			Print << task.get();

			// 中断フラグを false に戻す
			abort = false;
		}

		const double angle = (Scene::Time() * 30_deg);

		for (int32 i = 0; i < 12; ++i)
		{
			const double theta = (i * 30_deg + angle);

			const Vec2 pos = OffsetCircular{ Vec2{ 400, 300 }, 200, theta };

			pos.asCircle(28)
				.drawShadow(Vec2{ 0, 4 }, 12, 4)
				.draw(HSV{ (i * 30), 0.8, 1.0 })
				.drawFrame(3, 2, ColorF{ 1.0 });
		}
	}

	// 実行途中のタスクがあれば中断して完了を待つ
	if (task.isValid())
	{
		// 中断指示を出す
		abort = true;

		// タスクの完了を待つ（中断指示を出しているため短時間で完了する）
		task.wait();
	}
}
```

#### ポイント
- `std::atomic<bool>` を、複数スレッド間で安全に値を共有するために使用します
- `std::ref` を使って中断フラグを参照として渡すことで、メインスレッドからの中断信号を非同期処理に伝えられます
- 非同期処理内では定期的（この例では 100 ミリ秒ごと）に中断フラグを確認します
- アプリケーション終了時には実行中のタスクを適切に中断し、完了を待つことでリソースリークを防ぎます


## 76.7 （サンプル）別スレッドで生成中の画像の途中状態取得
- 次のサンプルコードでは、画像を 1 行ずつオレンジ色に塗りつぶす処理を非同期で実行し、その進捗状況と途中結果をメインスレッドに安全に提供します
- 次のような特徴があります：
	- **スレッドセーフな設計**：
		- `std::atomic<bool>` による中断フラグ
		- `std::atomic<size_t>` による進捗状況の管理
		- `std::mutex` による共有データの保護
	- **リソース管理**：
		- デストラクタでの非同期タスクの安全な終了
		- 処理完了後の不要なリソースの解放
	- **段階的な結果取得**：
		- 1 行ごとに結果画像を更新
		- メインスレッドからいつでも現在の結果を取得可能
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/async/7.png)

```cpp
# include <Siv3D.hpp>

// 画像処理を非同期で行うクラス
// 画像を 1 行ずつ塗りつぶす処理を非同期で実行し、
// 途中経過を安全に取得できる機能を提供します
class ImageTask
{
public:

	ImageTask()
		: m_processingImage{ Size{ 400, 400 }, Palette::White } // 処理用の白い画像を作成
		, m_result{ m_processingImage } // 結果画像を初期化
	{
		// 非同期タスクを開始する
		m_task = Async(Update, this);
	}

	// デストラクタ
	// 実行中の非同期タスクがあれば安全に終了します
	~ImageTask()
	{
		// 非同期タスクがあれば
		if (m_task.isValid())
		{
			// 中断フラグをオンにして処理の中断を指示する
			m_abort = true;

			// タスクが制御を返すまで待機する
			m_task.wait();
		}
	}

	// 処理の進捗状況を取得
	size_t getProgress() const
	{
		return m_processedLine;
	}

	// 現在の結果画像をテクスチャに反映
	void get(DynamicTexture& texture)
	{
		// 結果画像へのアクセスをミューテックスで保護する
		std::lock_guard lock{ m_mutex };

		// 現在の結果画像をテクスチャに反映する
		texture.fill(m_result);
	}

private:

	// 1 行分の画像処理を実行
	bool update()
	{
		const size_t y = m_processedLine;

		// 現在の行を塗りつぶす
		for (size_t x = 0; x < m_processingImage.width(); ++x)
		{
			m_processingImage[y][x] = Palette::Orange;
		}

		// 結果画像を更新する（ミューテックスで保護）
		{
			std::lock_guard lock{ m_mutex };

			// 処理中の内容を結果画像に反映する
			m_result = m_processingImage;

			// 処理完了行数をインクリメント
			++m_processedLine;
		}

		// すべての行の処理が完了したか確認
		if (m_processedLine == m_processingImage.height())
		{
			// 処理用の画像を解放してメモリを節約
			m_processingImage.release();

			// 処理完了を通知する
			return true;
		}

		// 処理継続を通知する
		return false;
	}

	// 非同期処理を行う静的関数
	static void Update(ImageTask* pImageTask)
	{
		// 中断フラグが立っていなければ処理を継続
		while (not pImageTask->m_abort)
		{
			// 5 ミリ秒待機して処理の負荷を調整する
			System::Sleep(5ms);

			// 1 行分の処理を実行し、完了したらループを抜ける
			if (pImageTask->update())
			{
				break;
			}
		}
	}

	// 非同期タスク
	AsyncTask<void> m_task;

	// 処理中断フラグ（スレッド間で安全に共有するため atomic 型を使用）
	std::atomic<bool> m_abort{ false };

	// 処理中の画像データ
	Image m_processingImage;

	// 処理が完了した行数（スレッド間で安全に共有するため atomic 型を使用）
	std::atomic<size_t> m_processedLine = 0;

	////////
	//
	// ミューテックスで保護するデータ領域
	//
	std::mutex m_mutex;

	// 結果画像（メインスレッドに提供するデータ）
	Image m_result;
	//
	////////
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 非同期画像処理タスクを作成
	ImageTask imageTask;

	// 結果表示用のテクスチャ
	DynamicTexture texture;

	// 現在の進捗状況
	size_t currentProgress = 0;

	while (System::Update())
	{
		// 進捗状況に変化があるか確認する
		if (const size_t newProgress = imageTask.getProgress();
			currentProgress != newProgress)
		{
			// 最新の結果画像を取得してテクスチャに反映する
			imageTask.get(texture);

			// 進捗状況を更新する
			currentProgress = newProgress;

			// 現在の進捗を表示する
			Print << currentProgress;
		}

		if (texture)
		{
			texture.draw();
		}
	}
}
```
