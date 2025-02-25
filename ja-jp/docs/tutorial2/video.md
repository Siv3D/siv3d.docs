# 32. 動画を描く
動画や GIF アニメーションを描画する方法を学びます。

## 32.1 動画の描画
- 動画をテクスチャのように描画できる `VideoTexture` クラスがあります
- `VideoTexture` は `Texture` とほぼ同じメンバ関数を持ち、動画の各フレームを `Texture` のように扱えます
- 動画の時間を進める場合、毎フレーム `.advance()` を呼んで再生位置を進める必要があります
	- バックグラウンドのスレッドで動画の次のフレームの先読みを行うため、時間を進める方向への動画の再生は、そこまで大きな負荷にはなりません
	- 再生していない動画を `.advance()` するのは避けるべきです
- `VideoTexture` ではミップマップが作成されません。動画を本来のサイズより縮小して描く場合、あらかじめ小さい解像度の動画を用意しておくことが実行時性能やメモリ使用量の観点から望ましいです
- 対応する動画フォーマットはプラットフォームによって異なります。MP4 ファイルであれば Windows, macOS, Linux でサポートされます

!!! example "Windows における動画ファイルの埋め込み"
	- Windows では、のちのチュートリアルで説明する「リソースファイルの埋め込み」で埋め込んだ動画ファイルを `VideoTexture` で読み込めない制約があります
	- ワークアラウンドとして、[一時ファイルに書き出す方法 :material-open-in-new:](https://gist.github.com/Reputeless/3d527302d459792f7a5e1094d30d0529){:target="_blank"} があります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/video/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// ループする場合は Loop::Yes, ループしない場合は Loop::No
	const VideoTexture videoTexture{ U"example/video/river.mp4", Loop::Yes };

	while (System::Update())
	{
		ClearPrint();

		// 再生位置（秒） / 動画の長さ（秒）
		Print << U"{:.2f}/{:.2f}"_fmt(videoTexture.posSec(), videoTexture.lengthSec());

		// 動画の時間を進める (デフォルトでは Scece::DeltaTime() 秒)
		videoTexture.advance();

		videoTexture
			.scaled(0.5).draw();

		videoTexture
			.scaled(0.5)
			.rotated(Scene::Time() * 30_deg)
			.drawAt(Cursor::Pos());
	}
}
```


## 32.2 （参考実装）音声付きの動画への対応
- 音声付きの動画を再生する専用のクラスはありません
- 音声付きの動画ファイルから `VideoTexture` と `Audio` を同時に作成し、タイミングを合わせて再生することで、音声付きの動画を再生することができます
	- `Audio` については [**チュートリアル 41. オーディオ再生**](../tutorial3/audio.md) で詳しく説明します
	- プラットフォームによっては、音声付きの動画ファイルの読み込みに対応していない場合があります
- 次のサンプルは実装例です
	- 動画の再生位置と音声の再生位置の差が 0.1 秒以上ある場合、音声の再生位置を動画の再生位置に合わせるようにしています
	- これにより、動画と音声の再生位置がずれることを防げますが、音声にノイズが入る可能性があります
	
!!! warning "動画ファイルの用意"
	- Siv3D のプロジェクトには音声付きの動画ファイルは同梱されていません
	- サンプルを実行する前に動画ファイルを自前で用意してください

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 音声付きの動画ファイルのパス（プロジェクトには同梱されていません）
	const FilePath path = U"test.mp4";

	if (not FileSystem::Exists(path))
	{
		throw Error{ U"{} が見つかりませんでした"_fmt(path) };
	}

	// ループする場合は Loop::Yes, ループしない場合は Loop::No
	const VideoTexture videoTexture{ path, Loop::Yes };

	// 動画の音声を再生するための Audio オブジェクト
	const Audio audio{ Audio::Stream, path, Loop::Yes };

	// 音声の読み込みに成功したら
	if (not audio)
	{
		throw Error{ U"音声の読み込みに失敗しました" };
	}

	// 音声を再生する
	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// 動画の再生位置（秒）
		const double videoTime = videoTexture.posSec();

		// 音声の再生位置（秒）
		const double audioTime = audio.posSec();

		Print << U"{:.2f}/{:.2f}/{:.2f}"_fmt(videoTime, audioTime, videoTexture.lengthSec());

		// 動画の再生位置と音声の再生位置の差が 0.1 秒以上ある場合
		if (0.1 < AbsDiff(audioTime, videoTime))
		{
			// 音声の再生位置を動画の再生位置に合わせる
			audio.seekTime(videoTime);
		}

		// 動画の時間を進める (デフォルトでは Scece::DeltaTime() 秒)
		videoTexture.advance();

		videoTexture
			.scaled(0.5).draw();
	}
}
```


## 32.3 GIF アニメーションの描画
- GIF アニメーションは、各フレームから `Texture` の配列を作成し、適切なタイミングで描くことで描画できます
	- GIF アニメーション自体はレガシーな画像形式であるため、ゲームのアニメーションとしての使用は非推奨です
- `AnimatedGIFReader` を使うと、各フレームの画像データ `Image` と、次のフレームまでのディレイ（ミリ秒）の配列を取得できます
- `Image` の配列から `Texture` の配列を作成し、適切なタイミングでフレームを描きます
- 時間 `t` が与えられたとき、何番目のフレームを描けばよいかは `AnimatedGIFReader::GetFrameIndex()` を使って求めることができます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/video/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// GIF アニメーション画像を開く
	const AnimatedGIFReader gif{ U"example/gif/test.gif" };

	// 各フレームの画像と、次のフレームへのディレイ（ミリ秒）をロードする
	Array<Image> images;
	Array<int32> delays;
	gif.read(images, delays);

	// 各フレームの Image から Texure を作成する
	const Array<Texture> textures = images.map([](const Image& image) { return Texture{ image }; });

	// 画像データはもう使わないため、消去してメモリ消費を減らす
	images.clear();

	while (System::Update())
	{
		ClearPrint();

		// フレーム数
		Print << textures.size() << U" frames";

		// 各フレームのディレイ（ミリ秒）一覧
		Print << U"delays: " << delays;

		// アニメーションの経過時間
		const double t = Scene::Time();

		// 経過時間と各フレームのディレイに基づいて、何番目のフレームを描けばよいかを計算する
		const size_t frameIndex = AnimatedGIFReader::GetFrameIndex(t, delays);

		// 現在のフレーム番号
		Print << U"frameIndex: " << frameIndex;

		textures[frameIndex].draw(200, 200);
	}
}
```
