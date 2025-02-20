# 50. アセット管理
プログラムのあらゆるところから `Texture`, `Font`, `Audio` などのアセットデータにアクセスできる機能を学びます。

## 50.1 アセット管理の概要
- Siv3D には `Texture` や `Font`, `Audio` などのアセットのハンドルに名前をつけ、その名前を通してプログラムのどこからでもグローバル変数のようにアクセスできる「アセット管理」の機能があります

### 50.1.1 アセット管理を扱う手順

1. アセットの「**登録 (Register)**」
2. アセットの「**ロード (Load)**」（省略可能）
3. アセットの「**使用**」
4. アセットの「**リリース (Release)**」（省略可能）
5. アセットの「**登録解除 (Unregister)**」（省略可能）

### 50.1.2 登録
- アセットをエンジンに登録します
- アセットの種類（テクスチャであるか、オーディオであるかなど）に応じた関数を呼び出し、アセットに一意の名前をつけ、ファイル名やプロパティなどの情報を登録します
- とくに指定しない限り、この時点ではアセットデータは構築されないため、登録によってメモリの消費量が増えることはありません

### 50.1.3 ロード
- アセットデータを実際にロードします
- アセットの名前を指定し、エンジンが該当アセットの登録時に与えられたファイル名やプロパティに従って、メモリ上にアセットデータを構築します
- 指定されたアセットがすでにロードされている場合は何もしません
- 非同期でロードを行うオプションも提供されています

### 50.1.4 使用
- アセットの名前を指定して、`Texture` や `Audio` を取得し、これを使って通常どおり `.draw()` したり、`.play()` したりします
- 該当アセットが未ロードである場合、このタイミングで自動的にロードを行います
- 指定されたアセットが登録されていなかった場合や、非同期ロード中である場合、空の `Texture` や `Audio` を返します

### 50.1.5 リリース
- 登録情報を残したまま、アセットデータをメモリ上から解放します
- リリース後もアセットの登録情報は残っているため、再度ロードしたり、使用したりすることができます
- 一度ロードしたアセットをしばらく使わず、メモリ消費を減らしたい場合にアセットをリリースするとよいでしょう

### 50.1.6 登録解除
- アセットの登録情報と名前をアセット管理から削除します
- 該当アセットが未リリースである場合、自動的にリリースします
- アプリケーション終了時には、すべてのアセットが自動でリリースされ登録解除されるため、登録解除を明示的に行う必要はありません

### 50.1.7 各種操作の関数

| コード | 説明 |
|---|---|
| `Register(name, ...)` | アセットを登録する |
| `IsRegistered(name)` | アセットが登録されているかを返す |
| `Load(name)` | アセットをロードする |
| `LoadAsync(name)` | アセットの非同期ロードを開始する |
| `Wait(name)` | アセットの非同期ロードが完了するまで待機する |
| `IsReady(name)` | アセットがロードが（成否にかかわらず）完了しているかを返す |
| `Release(name)` | アセットをリリースする |
| `Unregister(name)` | アセットを登録解除する |
| `ReleaseAll()` | 登録されているすべてのアセットをリリースする |
| `UnregisterAll()` | 登録されているすべてのアセットを登録解除する |
| `Enumerate()` | 登録されているすべてのアセットの情報一覧を列挙する |


## 50.2 Texture アセット
- アセット管理を通して `Texture` を扱う場合、`TextureAsset::` から始まる関数を使います
- `Texture` アセットには `TextureAsset(名前)` でアクセスします
- 次のサンプルコードでは、`"Windmill"` という名前で `example/windmill.png` を登録し、`"Siv3D-kun"` という名前で `example/siv3d-kun.png` を登録し、`"Cat"` という名前で絵文字 🐈 を登録します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/asset/2.png)

```cpp
# include <Siv3D.hpp>

void Draw()
{
	// Texture アセットを使用する
	TextureAsset(U"Windmill").draw(40, 40);
	TextureAsset(U"Siv3D-kun").scaled(0.8).drawAt(300, 300);
	TextureAsset(U"Cat").drawAt(600, 400);
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Texture アセットを登録する
	TextureAsset::Register(U"Windmill", U"example/windmill.png");
	TextureAsset::Register(U"Siv3D-kun", U"example/siv3d-kun.png", TextureDesc::Mipped);
	TextureAsset::Register(U"Cat", U"🐈"_emoji);

	while (System::Update())
	{
		Draw();
	}
}
```


## 50.3 複雑な Texture アセットの登録
- `Image` からテクスチャを作成したり、ロード時に前処理を行ったりするような複雑な Texture アセットの登録を行う場合は、`TextureAssetData` を使います
- 空の `TextureAssetData` を作成し、`onLoad` メンバ変数に、ロード時の処理を記述した `bool(TextureAssetData& asset, const String&)` のシグネチャを持つ関数オブジェクトを設定します
- この関数オブジェクトは、アセットのロード時に自動的に呼び出されます
- `asset.texture` にテクスチャを代入してアセットデータを構築することが求められます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/asset/3.png)

```cpp
# include <Siv3D.hpp>

std::unique_ptr<TextureAssetData> MakeTextureAssetData1()
{
	// 空のテクスチャアセットデータを作成する
	std::unique_ptr<TextureAssetData> assetData = std::make_unique<TextureAssetData>();

	// ロード時の仕事を設定する
	assetData->onLoad = [](TextureAssetData& asset, [[maybe_unused]] const String& hint)
	{
		// アセットデータにテクスチャを代入する
		asset.texture = Texture{ Image{ 256, 256, Palette::Skyblue },  TextureDesc::Mipped };
		return (not asset.texture.isEmpty());
	};

	return assetData;
}

std::unique_ptr<TextureAssetData> MakeTextureAssetData2(const FilePath& path, TextureDesc textureDesc)
{
	// 空のテクスチャアセットデータを作成する
	std::unique_ptr<TextureAssetData> assetData = std::make_unique<TextureAssetData>();

	// ファイルパスを代入する
	assetData->path = path;

	// テクスチャの設定を代入する
	assetData->desc = textureDesc;

	// ロード時の仕事を設定する
	assetData->onLoad = [](TextureAssetData& asset, [[maybe_unused]] const String& hint)
	{
		// 指定されたファイルパスの画像を 0.5 倍に縮小してテクスチャにする
		asset.texture = Texture{ Image{ asset.path }.scaled(0.5), asset.desc };
		return (not asset.texture.isEmpty());
	};

	return assetData;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// アセットを登録する（カスタムのテクスチャアセットデータを使用）
	TextureAsset::Register(U"MyTexture1", MakeTextureAssetData1());
	TextureAsset::Register(U"MyTexture2", MakeTextureAssetData2(U"example/windmill.png", TextureDesc::Mipped));
	TextureAsset::Register(U"MyTexture3", MakeTextureAssetData2(U"example/siv3d-kun.png", TextureDesc::Mipped));

	while (System::Update())
	{
		TextureAsset(U"MyTexture1").draw(100, 100);
		TextureAsset(U"MyTexture2").draw(300, 300);
		TextureAsset(U"MyTexture3").draw(400, 200);
	}
}
```


## 50.4 Font アセット
- アセット管理を通して `Font` を扱う場合、`FontAsset::` から始まる関数を使います
- `Font` アセットには `FontAsset(名前)` でアクセスします
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/asset/4.png)

```cpp
# include <Siv3D.hpp>

void Draw()
{
	// アセットを使用する
	FontAsset(U"Title")(U"My Game").drawAt(Vec2{ 400, 100 });
	FontAsset(U"Menu")(U"Play").drawAt(40, Vec2{ 400, 400 });
	FontAsset(U"Menu")(U"Exit").drawAt(40, Vec2{ 400, 500 });
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// アセットを登録する
	FontAsset::Register(U"Title", 60, U"example/font/RocknRoll/RocknRollOne-Regular.ttf");
	FontAsset::Register(U"Menu", FontMethod::MSDF, 48, Typeface::Medium);

	while (System::Update())
	{
		Draw();
	}
}
```


## 50.5 Audio アセット
- アセット管理を通して `Audio` を扱う場合、`AudioAsset::` から始まる関数を使います
- `Audio` アセットには `AudioAsset(名前)` でアクセスします

```cpp
# include <Siv3D.hpp>

void PlayPiano()
{
	// アセットを使用する
	AudioAsset(U"Piano").playOneShot();
}

void PlayShot()
{
	// アセットを使用する
	AudioAsset(U"SE").playOneShot();
}

void Main()
{
	// アセットを登録する
	AudioAsset::Register(U"BGM", Audio::Stream, U"example/test.mp3");
	AudioAsset::Register(U"SE", U"example/shot.mp3");
	AudioAsset::Register(U"Piano", GMInstrument::Piano1, PianoKey::A4, 0.5s);

	// アセットを使用する
	AudioAsset(U"BGM").setVolume(0.2);
	AudioAsset(U"BGM").play();

	while (System::Update())
	{
		if (MouseL.down())
		{
			PlayPiano();
		}

		if (MouseR.down())
		{
			PlayShot();
		}
	}
}
```


## 50.6 アセットの事前ロード
- 各アセットの `Load()` を使うと、そのアセットが未ロードである場合にロードを即座に行います
- ゲームの進行中にアセットのロードを行ってフレーム時間のスパイクが発生するのを防ぎたい場合、この関数を使ってロード画面などで事前にロードを行うことができます
- `FontAsset::Load()` では、プリロードするテキストを渡すこともできます
- アセットのロードが完了しているかは `IsReady()` で確認できます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/asset/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const String preloadText = U"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";

	FontAsset::Register(U"MyFont", FontMethod::MSDF, 48, Typeface::Bold);
	TextureAsset::Register(U"MyTexture", U"example/bay.jpg");
	AudioAsset::Register(U"MyAudio", Audio::Stream, U"example/test.mp3");
	AudioAsset::Register(U"MyMIDI", U"example/midi/test.mid");

	// 事前ロードする
	FontAsset::Load(U"MyFont", preloadText);
	TextureAsset::Load(U"MyTexture");
	AudioAsset::Load(U"MyAudio");
	AudioAsset::Load(U"MyMIDI");

	// ロードが完了していることを確認する
	Print << FontAsset::IsReady(U"MyFont");
	Print << TextureAsset::IsReady(U"MyTexture");
	Print << AudioAsset::IsReady(U"MyAudio");
	Print << AudioAsset::IsReady(U"MyMIDI");

	while (System::Update())
	{

	}
}
```


## 50.7 アセットの非同期ロード
- 各アセットの `LoadAsync()` を使うと、そのアセットが未ロードである場合に、別スレッドを使ったアセットの非同期ロードを開始します
- アセットのロード中にメインスレッドの処理が止まるのを避けることができます
- アセットの非同期ロードが完了したかは `IsReady()` で確認できます
- `Wait()` をすると、ロードが完了するまでメインスレッドの待機を発生させます
- 非同期ロード中にそのアセットにアクセスした場合は、空のアセットが返ってきます
	- とくに Audio アセットでは、空のアセットの再生により予期しない音（**チュートリアル 41.7**）を再生してしまうため、注意が必要です

!!! warning "OpenGL バックエンドでの注意"
	- OpenGL バックエンド（macOS と Linux のデフォルト, および Windows で選択した場合）では、`TextureAsset` の非同期ロードが `System::Update()` 内で進行します
	- `TextureAsset` の非同期ロード中は `System::Update()` の呼び出しを通常どおりの頻度で行ってください
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/asset/7.png)

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


## 50.8 アセット一覧の取得とタグ
- `Register()` では、`{ asstName, { assetTag, ... } }` によって、アセット名とアセットの**タグ**を登録できます
- 登録されているアセットの一覧を取得する `::Enumerate()` と組み合わせることで、特定のタグを持つアセットをロード、リリースするなど、アセットの管理が便利になります
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/asset/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	AudioAsset::Register({ U"BGM-0", { U"BGM" } }, Audio::Stream, U"example/test.mp3");
	AudioAsset::Register({ U"BGM-1", { U"BGM" } }, U"example/midi/test.mid");
	AudioAsset::Register({ U"PianoC", { U"SE", U"Piano" } }, GMInstrument::Piano1, PianoKey::C4, 0.5s);
	AudioAsset::Register({ U"PianoD", { U"SE", U"Piano" } }, GMInstrument::Piano1, PianoKey::D4, 0.5s);
	AudioAsset::Register({ U"PianoE", { U"SE", U"Piano" } }, GMInstrument::Piano1, PianoKey::E4, 0.5s);
	AudioAsset::Register({ U"TrumpetC", { U"SE", U"Trumpet" } }, GMInstrument::Trumpet, PianoKey::C4, 0.5s);
	AudioAsset::Register({ U"TrumpetD", { U"SE", U"Trumpet" } }, GMInstrument::Trumpet, PianoKey::D4, 0.5s);
	AudioAsset::Register({ U"TrumpetE", { U"SE", U"Trumpet" } }, GMInstrument::Trumpet, PianoKey::E4, 0.5s);

	for (auto&& [name, info] : AudioAsset::Enumerate())
	{
		Print << name << U": " << info.tags;

		// "SE" というタグを持つアセットだけロードする
		if (info.tags.includes(U"SE"))
		{
			AudioAsset::Load(name);
		}
	}

	Print << U"---";
	Print << AudioAsset::IsReady(U"BGM-0");
	Print << AudioAsset::IsReady(U"BGM-1");
	Print << AudioAsset::IsReady(U"PianoC");
	Print << AudioAsset::IsReady(U"PianoD");
	Print << AudioAsset::IsReady(U"PianoE");
	Print << AudioAsset::IsReady(U"TrumpetC");
	Print << AudioAsset::IsReady(U"TrumpetD");
	Print << AudioAsset::IsReady(U"TrumpetE");

	while (System::Update())
	{

	}
}
```
