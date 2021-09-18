
!!! warning "This is the documentation for an old version"
	This is the documentation for an old version of Siv3D (v0.4.3). See [Siv3D Reference v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation-en) for the latest version.

# Fullscreen

## Fundamental fullscreen mode

アプリケーションをフルスクリーンで実行したい場合、

1. シーンのサイズを決定
2. ウィンドウをリサイズ
3. フルスクリーン化

という手順を踏むのが基本的な方法です。

1. では任意のシーンサイズを指定できます。普及率の高い 16:9 のモニタに合わせて 1280x720 や 1366x768, 1920x1080 などを選ぶと、画面を有効に使えます。400x400 など変則的な解像度も選択可能です

2. は必ずしも必要ではありませんが、あらかじめウィンドウの基本サイズを変更しておくことで、フルスクリーンモードが解除された際に、そのサイズのウィンドウモードに復帰できるため、ユーザの混乱を減らせます

3. では `Window::SetFullscreen(true, unspecified, WindowResizeOption::KeepSceneSize);` を呼び出して、シーンのサイズはそのままに、枠無しのウィンドウを最大化してフルスクリーンを実現します

```C++
# include <Siv3D.hpp>

void Main()
{
	constexpr Size sceneSize = DisplayResolution::HD_1366x768;

	// シーンのサイズを 1366x768 に
	Scene::Resize(sceneSize);

	// ウィンドウのサイズを 1366x768 に
	Window::Resize(sceneSize, WindowResizeOption::KeepSceneSize);

	// ウィンドウを枠無しで最大化
	(void)Window::SetFullscreen(true, unspecified, WindowResizeOption::KeepSceneSize);

	while (System::Update())
	{
		// 100px 四方の正方形で画面を埋める
		for (auto p : step(Scene::Size() / 100 + Point(1, 1)))
		{
			if (IsOdd(p.x + p.y))
			{
				Rect(p * 100, 100).draw(Palette::Seagreen);
			}
		}

		Circle(Cursor::Pos(), 20).draw();
	}
}
```


## Selecting a native fullscreen resolution

ユーザのディスプレイ設定を変更し、ネイティブ解像度でフルスクリーンモードを実行したい場合は次のようにします。

!!! warning
	Windows かつ高 DPI に設定されたディスプレイでは、高 DPI 対応のウィンドウを作成しないと、最大解像度のフルスクリーンを正しく表示できません。`<Siv3D.hpp>` のインクルード前に `SIV3D_WINDOWS_HIGH_DPI` マクロを定義することで高 DPI 対応ウィンドウを作成できます。高 DPI ディスプレイにおいて、高 DPI 対応ウィンドウはドットバイドットで表示されるため、ウィンドウの見た目が小さくなります。

```C++
# define SIV3D_WINDOWS_HIGH_DPI // Windows で最大解像度のフルスクリーンを実現するのに必要
# include <Siv3D.hpp>

void Main()
{
	// 現在のモニタで使用可能なフルスクリーン解像度を取得
	const Array<Size> resolutions = Graphics::GetFullscreenResolutions();

	if (!resolutions)
	{
		throw Error(U"フルスクリーンモードを利用できません。");
	}

	// 選択肢を作成
	const Array<String> options = resolutions.map(Format);

	// 最大のフルスクリーン解像度にする
	size_t index = resolutions.size() - 1;
	if (!Window::SetFullscreen(true, resolutions[index]))
	{
		throw Error(U"フルスクリーンモードへの切り替えに失敗しました。");
	}

	while (System::Update())
	{
		// 100px 四方の正方形で画面を埋める
		for (auto p : step(Scene::Size() / 100 + Point(1, 1)))
		{
			if (IsOdd(p.x + p.y))
			{
				Rect(p * 100, 100).draw(Palette::Seagreen);
			}
		}

		Circle(Cursor::Pos(), 20).draw();

		// フルスクリーン解像度を変更する
		if (SimpleGUI::RadioButtons(index, options, Vec2(20, 20)))
		{
			if (!Window::SetFullscreen(true, resolutions[index]))
			{
				throw Error(U"フルスクリーン解像度の切り替えに失敗しました。");
			}
		}
	}
}
```
