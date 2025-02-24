# 76. 非同期処理

## XX.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/xxxx/1.png)

```cpp

```


## 1. 概要

Siv3D の `Main()` 関数はシングルスレッドで実行されるため、メインループ内で計算に時間のかかる関数を呼んだ場合、関数の結果が返るまで処理がそこでストップし、画面の更新が停止したり、フレームレートが低下したりします。Siv3D の非同期 API を使うと、完了までに時間のかかる処理を非同期（基本的には別のスレッド）に実行させ、それが完了するまで `Main()` 関数内で別の処理を進めておくことができます。

#### 注意事項
- `Main()` 以外のスレッドでは、Siv3D の描画サブシステムに関連する API（`.draw()` やレンダーステート、シェーダ、テクスチャなどの作成や操作）を使用できません。唯一の例外として `Print` は使用できます。
- `Main()` 以外のスレッドでアセット (`Texture`, `Audio`, `Font`, `PixelShader` など）を作成したい場合は、**アセット管理機能** (`TextureAsset` など）が提供する非同期機能を使ってください。それ以外の方法では正常に動作しない場合があります。
- HTTP 通信によるファイルダウンロードを非同期に行いたい場合は `SimpleHTTP::SaveAsync()` を使ってください。
- `Array`, `Stopwatch`, `Image`, `Wave`, `BinaryReader`, `JSON`, `TextWriter`, `NavMesh` のように、Siv3D のコアシステムと密接にかかわらないスタンドアローンの機能の大部分はどのスレッドでも使用可能です。ただし複数のスレッドで同じオブジェクトを共有する場合は、データ競合が発生しないよう、適切な同期処理を実装してください。

#### Siv3D エンジン内の並行処理
Siv3D のエンジン部分は複数のスレッドを利用する構造になっていて、おもにオーディオ再生、Web カメラ処理、ネットワークなどはデフォルトで非同期で実行されています。


## 2. 非同期でのアセット作成

`Texture`, `Audio`, `Font`, `PixelShader` などのアセットを非同期で作成する場合、最も安全で効率的な方法は**アセット管理機能**が提供する非同期 API を利用することです。これ以外の方法では正常に動作しないことがあります。

各アセットの `::LoadAsync()` を使うと、そのアセットが未ロードである場合に、別スレッドを使ったアセットの非同期ロードを開始します。アセットの非同期ロードが完了したかは `::IsReady()` で取得できます。非同期ロード中にそのアセットの使用または `::Wait()` をすると、ロードが完了するまでメインスレッドの待機が発生します。

同時に非同期ロードできるアセット数に上限は設けられていません。

!!! warning "OpenGL での TextureAsset 非同期ロードにおける注意"
    OpenGL バックエンド (macOS と Linux のデフォルト, および Windows で選択した場合) では、`TextureAsset` の非同期ロードが `System::Update()` の呼び出し中に完了します。`TextureAsset` の非同期ロード中は必ず `System::Update()` を呼んでください。

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String preloadText = U"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";

	FontAsset::Register(U"MyFont", FontMethod::MSDF, 64, Typeface::Bold);
	TextureAsset::Register(U"MyTexture", U"example/bay.jpg");
	AudioAsset::Register(U"MyAudio", Audio::Stream, U"example/test.mp3");
	AudioAsset::Register(U"MyMIDI", U"example/midi/test.mid");

	// 非同期ロードを開始
	FontAsset::LoadAsync(U"MyFont", preloadText);
	TextureAsset::LoadAsync(U"MyTexture");
	AudioAsset::LoadAsync(U"MyAudio");
	AudioAsset::LoadAsync(U"MyMIDI");

	while (System::Update())
	{
		ClearPrint();

		// ロードが完了したか
		Print << FontAsset::IsReady(U"MyFont");
		Print << TextureAsset::IsReady(U"MyTexture");
		Print << AudioAsset::IsReady(U"MyAudio");
		Print << AudioAsset::IsReady(U"MyMIDI");
	}
}
```

## 非同期処理の API

### クラス

#### `AsyncTask<Type>`
ある関数を非同期実行し、その状態や結果を管理する、非同期タスククラスです。通常は `Async()` 関数によって作成します。

`AsyncTask` には以下の状態があります

1. 非同期処理を持っていない
2. 非同期処理を持っていて、タスクが実行中であり、結果はまだ返せない
3. 非同期処理を持っていて、タスクが完了しており、結果をすぐに返せる


-----------------------------------


```cpp title="非同期処理を持っているかを返します。"
bool AsyncTask<Type>::isValid() const;
```

- `AsyncTask<Type>::get()` を呼ぶと、非同期処理を持たない状態に戻ります。

`戻り値`
:   非同期処理を持っている場合 `true`, それ以外の場合は `false`


-----------------------------------


```cpp title="タスクが完了した非同期処理を持っていて、結果をすぐに返せる状態であるかを返します。"
bool AsyncTask<Type>::isReady() const;
```

- `AsyncTask<Type>::get()` を呼ぶと、非同期処理を持たない状態に戻ります。

`戻り値`
:   タスクが完了した非同期処理を持っていて、結果をすぐに返せる状態である場合 `true`, それ以外の場合は `false`


-----------------------------------


```cpp title="タスクが完了した非同期処理の結果を返します。"
Type AsyncTask<Type>::get();
```

- タスクが完了していない場合は完了まで待機します。

`戻り値`
:   タスクが完了した非同期処理の結果


-----------------------------------


```cpp title="非同期処理のタスク完了を待ちます。"
void AsyncTask<Type>::wait() const;
```

- 非同期処理を持っていない場合、この関数はすぐに制御を返します。


### 関数

```cpp title="非同期タスクを作成します。"
template <class Fty, class... Args>
auto Async(Fty&& f, Args&&... args);
```

`f`
:   非同期で実行する関数

`args`
:   関数 `f` に渡す引数

`戻り値`
:   関数 `f` の戻り値型の非同期タスク (`AsyncTask<Ret>`)


## 3. 非同期処理のサンプル

### 3.1 指定したフォルダに含まれる画像ファイルを非同期でロードして表示する

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

### 3.2 メインループを止めずに、実行に時間のかかる関数の結果を得る

```cpp
# include <Siv3D.hpp>

// n 秒後に n を返す関数（重い処理を Sleep で代わりに表現）
int32 F(int32 n)
{
	// n 秒スリープ
	System::Sleep(n * 1s);

	return n;
}

void Main()
{
	Scene::SetBackground(Palette::White);

	// 非同期タスク
	AsyncTask<int32> task;

	while (System::Update())
	{
		// 非同期タスクを持っていない時にボタンを押せる
		if (SimpleGUI::Button(U"Call", Vec2{ 600, 20 }, unspecified, (not task.isValid())))
		{
			// 関数 F を実行する非同期タスクを作成。F の第一引数に 5 を渡す
			task = Async(F, 5);
		}

		// 非同期タスクが完了したら
		if (task.isReady())
		{
			// 結果を取得する
			Print << task.get();
		}

		const double t = Scene::Time();

		for (auto i : step(12))
		{
			const double angle = (i * 30_deg) + (t * 30_deg);

			const Vec2 pos = OffsetCircular{ Scene::Center(), 160, angle };

			Circle{ pos, 20 }.draw(HSV{ (i * 30) });
		}
	}

	// 実行途中のタスクがあれば完了まで待つ。
    if (task.isValid())
    {
	    task.wait();
    }
}
```


### 3.3 非同期タスクを中断する
プログラムを中断する場合、`AsyncTask` は関数が戻り値を返すまで待機する必要があります。この待機時間を減らすため、3.2 のサンプルを拡張し、関数を中断できる仕組みを導入します。

```cpp
# include <Siv3D.hpp>

// n 秒後に n を返す関数（重い処理を Sleep で代わりに表現）
int32 F(int32 n, const std::atomic<bool>& abort)
{
	for (int i = 0; i < (n * 10); ++i)
	{
		// 処理の最中に中断フラグを確認する
		if (abort) // 中断フラグが true なら
		{
			// 完全に処理が完了する前にそこで中断する
			return -1;
		}

		System::Sleep(100ms);
	}

	return n;
}

void Main()
{
	Scene::SetBackground(Palette::White);

	// 非同期タスク
	AsyncTask<int32> task;

	// 中断フラグ
	std::atomic<bool> abort{ false };

	while (System::Update())
	{
		// 非同期タスクを持っていない時にボタンを押せる
		if (SimpleGUI::Button(U"Call", Vec2{ 600, 20 }, unspecified, (not task.isValid())))
		{
			abort = false;

			// 関数 F を実行する非同期タスクを作成。F の第一引数に 5 を、第二引数に中断指示の参照を渡す
			task = Async(F, 5, std::ref(abort));
		}

		// 非同期タスクの中断
		if (SimpleGUI::Button(U"Abort", Vec2{ 600, 60 }, unspecified, task.isValid()))
		{
			// 中断フラグを true に
			abort = true;
		}

		// 非同期タスクが完了したら
		if (task.isReady())
		{
			// 結果を取得する
			Print << task.get();
		}

		const double t = Scene::Time();

		for (auto i : step(12))
		{
			const double angle = (i * 30_deg) + (t * 30_deg);

			const Vec2 pos = OffsetCircular{ Scene::Center(), 160, angle };

			Circle{ pos, 20 }.draw(HSV{ (i * 30) });
		}
	}

	// 実行途中のタスクがあれば完了まで待つ。
	if (task.isValid())
	{
		// 中断指示を出す
		abort = true;

		// 完全に処理が完了する前に制御を返してくれる
		task.wait();
	}
}
```


### 3.4 別スレッドで生成している画像の途中結果を取得する

```cpp
# include <Siv3D.hpp>

class ImageTask
{
public:

	ImageTask()
		: m_processingImage(400, 400, Palette::White)
		, m_result{ m_processingImage }
	{
		m_task = Async(Update, this);
	}

	~ImageTask()
	{
		// 非同期タスクがあれば
		if (m_task.isValid())
		{
			// 中断フラグをオンに
			m_abort = true;

			// 制御を返すまで待つ
			m_task.wait();
		}
	}

	// 何行塗りつぶしたかを返す
	size_t getProgress() const
	{
		return m_processedLine;
	}

	// 結果画像を取得
	void get(DynamicTexture& texture)
	{
		std::lock_guard lock{ m_mutex };

		texture.fill(m_result);
	}

private:

	bool update()
	{
		const size_t y = m_processedLine;

		// 1 行塗る
		for (size_t x = 0; x < m_processingImage.width(); ++x)
		{
			m_processingImage[y][x] = Palette::Orange;
		}

		// 一行終わったら結果画像を更新
		{
			std::lock_guard lock{ m_mutex };

			// 処理中の内容を転送
			m_result = m_processingImage;

			++m_processedLine;
		}

		// 処理が完全に完了したら
		if (m_processedLine == m_processingImage.height())
		{
			// 処理用の画像を破棄し、メモリも開放する
			m_processingImage.release();

			return true;
		}

		return false;
	}

	static void Update(ImageTask* pImageTask)
	{
		// 中断フラグが経ったら未完了でも終了する
		while (not pImageTask->m_abort)
		{
			// 5 ミリ秒ごとに 1 行塗る
			System::Sleep(5ms);

			if (pImageTask->update())
			{
				break;
			}
		}
	}

	// 非同期タスク
	AsyncTask<void> m_task;

	// 処理を中断するかのフラグ
	std::atomic<bool> m_abort{ false };

	// 処理中の画像
	Image m_processingImage;

	// 処理が終わった行数
	std::atomic<size_t> m_processedLine = 0;

	////////
	//
	// ミューテックスで保護するデータ
	//
	std::mutex m_mutex;

	// 結果画像
	Image m_result;
	//
	////////
};

void Main()
{
	ImageTask imageTask;

	DynamicTexture texture;

	size_t currentProgress = 0;

	while (System::Update())
	{
		// 進捗があれば
		if (const size_t newProgress = imageTask.getProgress();
			currentProgress != newProgress)
		{
			// 結果画像を取得する
			imageTask.get(texture);

			currentProgress = newProgress;

			Print << currentProgress;
		}

		if (texture)
		{
			texture.draw();
		}
	}
}
```


