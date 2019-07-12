
# 17. Asset management

この章では、プログラムのあらゆるところから `Texture`, `Audio`, `Font` などのアセットデータにアクセスできる方法を学びます。

## 17.1 アセット管理の基本
Siv3D は `Texture` や `Aufio`, `Font` などのアセットのハンドルに名前をつけ、名前を通してプログラムのどこからでもアクセスできる「アセット管理」の機能を提供しています。

プログラムでアセット管理を扱う手順は以下の通りです。

1. アセットの「登録 (Register)」
2. アセットの「プリロード (Preload)」（省略可能）
3. アセットの「使用」
4. アセットの「リリース (Release)」（省略可能）
5. アセットの「登録解除 (Unregister)」（省略可能）

### 登録
アセットをエンジンに登録します。

アセットの種類（テクスチャであるか、オーディオであるかなど）を関数で指定し、アセットに一意の名前をつけ、ファイル名やプロパティなどの情報を登録します。

特に指定しない限り、この時点ではアセットデータは構築されないので、メモリの消費量が増えることはありません。

### プリロード
アセットデータを実際にロードします。

アセットの名前を指定すると、エンジンが該当アセットの登録時に与えられたファイル名やプロパティに従ってメモリにアセットデータを構築します。

指定されたアセットがすでにロードされている場合は何もしません。

### 使用
アセットの名前を指定して、`Texture` や `Audio` を取得します。

これを使って、前章までのプログラムのように `.draw()` したり、`.play()` したりすることができます。

指定されたアセットが登録されていなかった場合は空の `Texture` や `Audio` を返します。プリロードされていなかった場合はこのタイミングでロードを行います。

### リリース
アセットデータをメモリ上から解放します。

リリース後もアセットの登録情報は残っているため、再度プリロードしたり、使用したりすることができます。

一度ロードしたアセットをこの先しばらく使わず、メモリ消費を抑えたい場合にアセットのリリースをしましょう。

### 登録解除
アセットの登録情報と名前をアセット管理から削除します。

該当アセットがリリースされていなかった場合はリリースします。

アプリケーション終了時にはすべてのアセットが自動でリリース、登録解除されるため、アセットの登録解除を明示的に行う必要はありません。

## 17.2 Texture アセットのサンプル

```C++
# include <Siv3D.hpp>

void Draw()
{
	// アセットの使用
	TextureAsset(U"Windmill").draw();
	TextureAsset(U"Siv3D-kun").scaled(0.8).drawAt(200, 200);
}

void Main()
{
	// アセットの登録
	TextureAsset::Register(U"Windmill", U"example/windmill.png");
	TextureAsset::Register(U"Siv3D-kun", U"example/siv3d-kun.png", TextureDesc::Mipped);

	while (System::Update())
	{
		Draw();
	}
}
```


## 17.3 Audio アセットのサンプル

```C++
# include <Siv3D.hpp>

void MakeSound()
{
	// アセットの使用
	AudioAsset(U"Sound").playOneShot();
}

void Main()
{
	// アセットの登録
	AudioAsset::Register(U"BGM", U"example/test.mp3");
	AudioAsset::Register(U"Sound", GMInstrument::Piano1, PianoKey::A4, 0.5s);

	// アセットの使用
	AudioAsset(U"BGM").setVolume(0.3);
	AudioAsset(U"BGM").play();

	while (System::Update())
	{
		if (MouseL.down())
		{
			MakeSound();
		}
	}
}
```


## 17.4 Font アセットのサンプル

```C++
# include <Siv3D.hpp>

void Draw()
{
	// アセットの使用
	FontAsset(U"Title")(U"My Game").drawAt(400, 100);
	FontAsset(U"Menu")(U"Play").drawAt(400, 400);
	FontAsset(U"Menu")(U"Exit").drawAt(400, 500);
}

void Main()
{
	// アセットの登録
	FontAsset::Register(U"Title", 60, U"example/font/AnnyantRoman/AnnyantRoman.ttf");
	FontAsset::Register(U"Menu", 40, Typeface::Medium);

	while (System::Update())
	{
		Draw();
	}
}
```

## 17.5 アセットの登録時ロード
アセットの登録時に `AssetParameter::LoadImmediately()` をパラメータとして渡すと、登録後にアセットのプリロードが行われます。ゲームの実行中にアセットのロードが発生してフレームレートが低下するのを防ぎたいときに、このパラメータを使います。このパラメータは `TextureAsset` と `AudioAsset` にのみ有効です。

```C++
# include <Siv3D.hpp>

void Draw()
{
	// アセットの使用
	TextureAsset(U"Windmill").draw();
	TextureAsset(U"Siv3D-kun").scaled(0.8).drawAt(200, 200);
}

void Main()
{
	// アセットの登録と同時にプリロード
	TextureAsset::Register(U"Windmill", U"example/windmill.png", AssetParameter::LoadImmediately());
	TextureAsset::Register(U"Siv3D-kun", U"example/siv3d-kun.png", TextureDesc::Mipped, AssetParameter::LoadImmediately());

	while (System::Update())
	{
		Draw();
	}
}
```


## 17.6 アセットの非同期ロード
アセットの登録時に `AssetParameter::LoadAsync()` をパラメータとして渡すと、登録後に別スレッドを使ったアセットの非同期ロードが行われます。複数のアセットを同時に読み込んで、ロード時間を減らしたいときに、このパラメータを使います。非同期ロードが完了すると `TextureAsset::IsReady()` が `true` を返します。それより前にアセットを使用しようとすると、空の `Texture` や `Audio` が返されます。このパラメータは `TextureAsset` と `AudioAsset` にのみ有効です。

次のプログラムでは、非同期ロードが完了するまでループで待機していますが、メインループ内でロードの進捗を可視化しながら待機するといった実装も可能です。

```C++
# include <Siv3D.hpp>

void Draw()
{
	// アセットの使用
	TextureAsset(U"Windmill").draw();
	TextureAsset(U"Siv3D-kun").scaled(0.8).drawAt(200, 200);
}

void Main()
{
	// アセットの登録と同時にプリロード
	TextureAsset::Register(U"Windmill", U"example/windmill.png", AssetParameter::LoadAsync());
	TextureAsset::Register(U"Siv3D-kun", U"example/siv3d-kun.png", TextureDesc::Mipped, AssetParameter::LoadAsync());

	// 両方ロードされるまで待機
	for (;;)
	{
		const bool r1 = TextureAsset::IsReady(U"Windmill");
		const bool r2 = TextureAsset::IsReady(U"Siv3D-kun");
		Print << r1 << U" " << r2;

		if (r1 && r2)
		{
			break;
		}
	}

	while (System::Update())
	{
		Draw();
	}
}
```


