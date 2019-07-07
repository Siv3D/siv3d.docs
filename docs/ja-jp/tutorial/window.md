
# 10. シーンとウィンドウ

Siv3D では、図形やテクスチャ、テキストなどを `.draw()` すると、「シーン」と呼ばれる仮想の画面に描画されます。そして、シーンの画像がウィンドウに転送されることで、ユーザは描画された結果を目にすることができます。

![](images/10-0-0.png)

これらの処理は自動的に行われるため、前章までは特に意識することなく `.draw()` した内容を表示していました。この章ではシーンの設定やウィンドウへの転送をカスタマイズする方法を学びます。


## 10.1 シーンとウィンドウのサイズを変更する
シーンの基本サイズとウィンドウの基本サイズは `Scene::DefaultSceneSize` と `Window::DefaultClientSize` でそれぞれ定義されており、どちらも幅 800 ピクセル、高さ 600 ピクセルです。シーンとウィンドウのサイズを変更するには、`Window::Resize()` で新しいサイズを指定します。

シーンのサイズは `Scene::Size()`, シーンの幅は `Scene::Width()`, シーンの高さは `Scene::Height()`, ウィンドウのサイズは `Window::ClientSize()`, ウィンドウの幅は `Window::ClientWidth()`, ウィンドウの高さは `Window::Height()` で取得できます。

![](images/10-1-0.png)

```C++ hl_lines="24"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));
	const Texture texture(Emoji(U"🐈"));
	const Font font(30, Typeface::Bold);

	constexpr std::array<Size, 4> resolutions =
	{
		Size(400, 600), Size(640, 480), Size(800, 600), Size(1280, 640)
	};
	size_t index = 2;

	while (System::Update())
	{
		Triangle(300, 150, 200).draw(Palette::Cornflowerblue);
		Circle(200, 200, 80).draw(Palette::Seagreen);
		texture.resized(200).drawAt(600, 400);

		// ラジオボタンが変更されたら
		if (SimpleGUI::RadioButtons(index, { U"400x600", U"640x480", U"800x600", U"1280x640" }, Vec2(20, 20)))
		{
			// ウィンドウサイズを変更（シーンのサイズも同時に変更）
			Window::Resize(resolutions[index]);
		}

		// シーンのサイズとウィンドウのサイズを表示
		font(U"Scene: {}\nWindow: {}"_fmt(Scene::Size(), Window::ClientSize())).draw(20, 200, ColorF(0.25));
	}
}
```


## 10.2 ウィンドウのサイズだけを変更する
ウィンドウのサイズが変更されても、シーンのサイズはそのままにしておきたいケースもあるでしょう。その場合は `Window::Resize()` に `WindowResizeOption::KeepSceneSize` パラメータを指定します。こうすると、`Window::Resize()` してもシーンのサイズは保持されます。シーンは必要に応じて縮小・拡大し、縦横比を保持したままウィンドウに転送され、余った領域（レターボックス）は黒く塗りつぶされます。

![](images/10-2-0.png)

```C++ hl_lines="24"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));
	const Texture texture(Emoji(U"🐈"));
	const Font font(30, Typeface::Bold);

	constexpr std::array<Size, 4> resolutions =
	{
		Size(400, 600), Size(640, 480), Size(800, 600), Size(1280, 640)
	};
	size_t index = 2;

	while (System::Update())
	{
		Triangle(300, 150, 200).draw(Palette::Cornflowerblue);
		Circle(200, 200, 80).draw(Palette::Seagreen);
		texture.resized(200).drawAt(600, 400);

		if (SimpleGUI::RadioButtons(index, { U"400x600", U"640x480", U"800x600", U"1280x640" }, Vec2(20, 20)))
		{
			// ウィンドウサイズを変更（シーンのサイズは保持）
			Window::Resize(resolutions[index], WindowResizeOption::KeepSceneSize);
		}

		// シーンのサイズとウィンドウのサイズを表示
		font(U"Scene: {}\nWindow: {}"_fmt(Scene::Size(), Window::ClientSize())).draw(20, 200, ColorF(0.25));
	}
}
```


## 10.3 レターボックスの色を変更する
シーンが縦横比を保持したままウィンドウに転送されるとき、ウィンドウ内の余った領域（レターボックス）を塗りつぶす色を変更するには、`Scene::SetLetterbox()` で色を設定します。

![](images/10-3-0.png)

```C++ hl_lines="6"
# include <Siv3D.hpp>

void Main()
{
	// レターボックスの色を設定
	Scene::SetLetterbox(ColorF(0.4, 0.6, 0.5));

	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));
	const Texture texture(Emoji(U"🐈"));
	const Font font(30, Typeface::Bold);

	constexpr std::array<Size, 4> resolutions =
	{
		Size(400, 600), Size(640, 480), Size(800, 600), Size(1280, 640)
	};
	size_t index = 2;

	while (System::Update())
	{
		Triangle(300, 150, 200).draw(Palette::Cornflowerblue);
		Circle(200, 200, 80).draw(Palette::Seagreen);
		texture.resized(200).drawAt(600, 400);

		if (SimpleGUI::RadioButtons(index, { U"400x600", U"640x480", U"800x600", U"1280x640" }, Vec2(20, 20)))
		{
			// ウィンドウサイズを変更（シーンのサイズは保持）
			Window::Resize(resolutions[index], WindowResizeOption::KeepSceneSize);
		}

		font(U"Scene: {}\nWindow: {}"_fmt(Scene::Size(), Window::ClientSize())).draw(20, 200, ColorF(0.25));
	}
}
```


## 10.4 シーンのサイズだけを変更する
`Scene::Resize()` でシーンのサイズだけを変更できます。例えば、シーンのサイズをウィンドウのサイズよりも小さくすると、画面が粗くなります。シーンをウィンドウに転送する際の拡大縮小フィルタは、デフォルトでは線形補間あり（なめらか）ですが、`Scene::SetTextureFilter()` に `TextureFilter::Nearest` を指定することで、線形補間無しを選択できます。線形補間無しの設定は、ドット感を保ちたいときには有効ですが、それ以外のケースでは画質を低下させます。

![](images/10-4-0.png)

```C++ hl_lines="8 11"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));

	// シーンのサイズを 100x75 に
	Scene::Resize(100, 75);

	// シーンをウィンドウに転送する際の拡大縮小フィルタを「補間なし」に
	Scene::SetTextureFilter(TextureFilter::Nearest);
	
	const Texture texture(Emoji(U"🐈"));
	
	const Font font(10, Typeface::Bold);

	while (System::Update())
	{
		Triangle(20, 30, 20).draw(Palette::Cornflowerblue);
		
		Circle(50, 50, 25).draw(Palette::Seagreen);
		
		texture.resized(64).drawAt(65, 35);

		font(U"Scene: {}\nWindow: {}"_fmt(Scene::Size(), Window::ClientSize())).draw(2, 40, ColorF(0.25));
	}
}
```


## 10.5 ウィンドウを手動でリサイズできるようにする
`Window::SetStyle()` で `WindowStyle::Sizable` を設定すると、ウィンドウの境界をドラッグしてサイズを変更できるようになります。また、これによってウィンドウの最大化ボタンが使えるようになります。

<video src="../images/10-5-0.mp4" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを手動リサイズ可能にする
	Window::SetStyle(WindowStyle::Sizable);

	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));
	const Texture texture(Emoji(U"🐈"));
	const Font font(30, Typeface::Bold);

	while (System::Update())
	{
		Triangle(300, 150, 200).draw(Palette::Cornflowerblue);
		Circle(200, 200, 80).draw(Palette::Seagreen);
		texture.resized(200).drawAt(600, 400);

		// シーンのサイズとウィンドウのサイズを表示
		font(U"Scene: {}\nWindow: {}"_fmt(Scene::Size(), Window::ClientSize())).draw(20, 200, ColorF(0.25));
	}
}
```

手動でリサイズしたときには、シーンのサイズは変更されないようになっていますが、`Scene::SetScaleMode()` で `ScaleMode::ResizeFill` を設定することで、シーンのサイズもリサイズに追従するようになります。

<video src="../images/10-5-1.mp4" autoplay loop muted></video>

```C++ hl_lines="11"
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウを手動リサイズ可能にする
	Window::SetStyle(WindowStyle::Sizable);

	// 手動リサイズ時に、シーンのサイズも合わせて変更する
	Scene::SetScaleMode(ScaleMode::ResizeFill);

	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));
	const Texture texture(Emoji(U"🐈"));
	const Font font(30, Typeface::Bold);

	while (System::Update())
	{
		Triangle(300, 150, 200).draw(Palette::Cornflowerblue);
		Circle(200, 200, 80).draw(Palette::Seagreen);
		texture.resized(200).drawAt(600, 400);

		// シーンのサイズとウィンドウのサイズを表示
		font(U"Scene: {}\nWindow: {}"_fmt(Scene::Size(), Window::ClientSize())).draw(20, 200, ColorF(0.25));
	}
}

```


## 10.6 ウィンドウのタイトルを変更する
ウィンドウのタイトルを変更するには、`Window::SetTitle()` に文字列や値を渡します。なお、デバッグビルド時には、タイトルのほかに、"(Debug Build)" という文字列と、フレームレート、ウィンドウのサイズ、シーンのサイズが合わせて表示されます。

!!! warning
    ウィンドウタイトルの変更は時間のかかる処理なので、毎フレーム `Window::SetTitle()` に異なる文字列を設定することは避けてください。既に設定されているタイトルと同じ場合には何もしないので、コストは発生しません。

![](images/10-6-0.png)

```C++
# include <Siv3D.hpp>

void Main()
{
	Window::SetTitle(U"Hello, Siv3D!");

	while (System::Update())
	{

	}
}
```


## 10.7 ウィンドウを最小化 / 最大化する
プログラムによってウィンドウを最小化するには `Window::Minimize()` を、最大化するには `Window::Maximize()` を呼びます。最大化をするにはウィンドウが手動リサイズ可能になっている必要があります。

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));

	// ウィンドウを手動リサイズ可能にする
	Window::SetStyle(WindowStyle::Sizable);

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Minimize", Vec2(20, 20)))
		{
			// ウィンドウを最小化
			Window::Minimize();
		}

		if (SimpleGUI::Button(U"Maximize", Vec2(20, 60)))
		{
			// ウィンドウを最大化
			Window::Maximize();
		}
	}
}
```


## 10.8 ウィンドウの枠を非表示にする
ウィンドウの枠を非表示にするには、`Window::SetStyle()` で `WindowStyle::Frameless` を設定します。

![](images/10-8-0.png)

```C++
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(600, 400);

	// ウィンドウの枠を非表示にする
	Window::SetStyle(WindowStyle::Frameless);

	while (System::Update())
	{
		Circle(Cursor::Pos(), 100).draw();
	}
}
```

