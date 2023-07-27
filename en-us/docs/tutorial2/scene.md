# 32. シーンとウィンドウ
Siv3D のシーンとウィンドウのカスタマイズ方法を学びます。

Siv3D では、図形やテクスチャ、テキストなどを `.draw()` すると、「シーン」と呼ばれる仮想の画面に描画されます。Siv3D は `System::Update()` 内でシーンの画像をウィンドウに転送されることで、ユーザは描画結果をウィンドウ上で目にすることができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/scene/0.png)

これらの処理は自動的に行われるため、前章までは特に意識することなく `.draw()` した内容をウィンドウに表示していました。この章ではシーンやウィンドウの仕組みを掘り下げます。

この章では Siv3D の内部の仕組みを説明するため、やや難しい内容になっています。開発におけるほとんどのニーズは 32.3 の `Window::Resize()` だけで満たせると思われます。すべてを理解する必要はありません。

## 32.1 三つのサイズ
Siv3D プログラムにおける画面表示を理解するには、「3 つのサイズ」を知る必要があります。

#### ① シーンのサイズ
`.draw()` による描画や `Cursor::Pos()` のマウスカーソル座標などの基準になる、独立した 1 つのシーンのサイズです。下記の関数で取得できます。デフォルトでは後述する仮想ウィンドウサイズと一致します。Siv3D の機能を使ってスクリーンショットを撮影したときは、この解像度で保存されます。

| 関数 | 戻り値 | 説明 |
|--|--|--|
| `Scene::Size()` | `Size` | シーンのサイズを返します。 |
| `Scene::Width()` | `int32` | シーンの幅を返します。 |
| `Scene::Height()` | `int32` | シーンの高さを返します。 |
| `Scene::Center()` | `Point` | シーンの中心座標を返します。<br>`Scene::Size() / 2` と同じです。 |
| `Scene::CenterF()` | `Vec2` | シーンの中心座標を返します。<br>`Scene::Size() / 2.0` と同じです。 |
| `Scene::Rect()` | `Rect` | シーンの矩形を返します。<br>`Rect{ 0, 0, Scene::Size() }` と同じです。 |

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene::Size(): " << Scene::Size();
		Print << U"Scene::Width(): " << Scene::Width();
		Print << U"Scene::Height(): " << Scene::Height();
		Print << U"Scene::Center(): " << Scene::Center();
		Print << U"Scene::CenterF(): " << Scene::CenterF();
		Print << U"Scene::Rect(): " << Scene::Rect();
	}
}
```

#### ② 仮想ウィンドウサイズ
ユーザのデスクトップ上における、ウィンドウの**クライアント領域**（タイトルバーやフレームを除く、描画が行われる領域）の見かけのサイズです。`Window::GetState().virtualSize` で取得できます。この仮想ウィンドウサイズに OS 設定の拡大縮小倍率 (150%, 200% など) を乗算したものが、後述する実ウィンドウサイズになります。

!!! warning "Linux 版での注意"
	現在の Siv3D Linux 版は OS 設定の拡大縮小倍率に未対応です。仮想ウィンドウサイズと実ウィンドウサイズは同じになります。

#### ③ 実ウィンドウサイズ（フレームバッファサイズ）
モニタ上の実ピクセル数で計測した、ウィンドウのクライアント領域のサイズです。`Window::GetState().frameBufferSize` で取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"scene size: " << Scene::Size();
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;
	}
}
```

#### 三つのサイズの関係
デフォルトの設定では、シーンのサイズは仮想ウィンドウサイズと連動します。

シーンのサイズと実ウィンドウサイズが異なる場合でも、Siv3D はシーン画像をクライアント領域に転送する際に自動的に拡大縮小を行い、アスペクト比を保ったまま、クライアント領域にフィットするよう表示するため、ユーザは実ウィンドウサイズを意識する必要はありません。

例えば OS 設定の拡大縮小倍率が 200% で、実ウィンドウサイズが 1600x1200 であっても、シーンのサイズは 800x600 を前提として描画することができます。

| OS 設定の拡大縮小倍率 | ① シーンのサイズ | ② 仮想ウィンドウサイズ | ③ 実ウィンドウサイズ |
|--|--|--|--|
| 100% | 800x600 | 800x600 | 800x600 |
| 125% | 800x600 | 800x600 | 1000x750 |
| 150% | 800x600 | 800x600 | 1200x900 |
| 200% | 800x600 | 800x600 | 1600x1200 |

実ウィンドウサイズに沿ったシーンのサイズで高精細な描画を行いたい場合は、32.2 で説明するリサイズモード `ResizeMode::Actual` を使用します。


## 32.2 シーンのリサイズモード
ユーザがウィンドウをマウスでリサイズしたり、ウィンドウをリサイズする関数を呼んだりすると、仮想ウィンドウサイズと実ウィンドウサイズの 2 つが変化します。そして、これらのウィンドウサイズに応じてシーンのサイズも更新されます。どのようにシーンのサイズを更新するかを決めるのが、シーンの**リサイズモード**です。リサイズモードは次の 3 種類があります。デフォルトでは、仮想ウィンドウサイズと一致するようにシーンのサイズを更新するモード `ResizeMode::Virtual` になっています。

| リサイズモード | 説明 |
|--|--|
|`ResizeMode::Virtual` | 仮想ウィンドウサイズと一致するようにシーンのサイズを更新します（デフォルト）。 |
|`ResizeMode::Actual` | 実ウィンドウサイズと一致するようにシーンのサイズを更新します。 |
|`ResizeMode::Keep` | シーンのサイズを更新しません。 |

リサイズモードは `Scene::SetResizeMode(ResizeMode)` で設定します。現在のリサイズモードは `Scene::GetResizeMode()` で取得できます。


## 32.3 ウィンドウのリサイズ
`Window::Resize(幅, 高さ)` または `Window::Resize(サイズ)` で、仮想ウィンドウサイズを変更できます。これにともない実ウィンドウサイズも変更され、リサイズモードに応じてシーンのサイズも更新されます。

デフォルトのリサイズモード `ResizeMode::Virtual` が適用されている場合、`Window::Resize()` で指定した仮想ウィンドウサイズが新しいシーンのサイズになります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1000, 600);

	while (System::Update())
	{
		ClearPrint();
		Print << U"scene size: " << Scene::Size();
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;

		// 100px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}
	}
}
```

リサイズモード `ResizeMode::Keep` が適用されている場合、`Window::Resize()` を行ってもシーンのサイズは変化しません。シーンのサイズと実ウィンドウサイズのアスペクト比が異なる場合、クライアント領域の左右もしくは上下に**レターボックス**と呼ばれる余白領域が生じます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Keep);
	Window::Resize(1000, 600);

	while (System::Update())
	{
		ClearPrint();
		Print << U"scene size: " << Scene::Size();
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;

		// 100px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}
	}
}
```


## 32.4 ウィンドウを手動でリサイズできるようにする
`Window::SetStyle(WindowStyle::Sizable)` を設定すると、ウィンドウをつかんでリサイズできるようになります。ユーザの操作によって仮想ウィンドウサイズ / 実ウィンドウサイズが変更されたとき、リサイズモードに応じてシーンのサイズも更新されます。

デフォルトのリサイズモード `ResizeMode::Virtual` が適用されている場合、仮想ウィンドウサイズが新しいシーンのサイズになります。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::SetStyle(WindowStyle::Sizable);

	while (System::Update())
	{
		ClearPrint();
		Print << U"scene size: " << Scene::Size();
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;

		// 100px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}
	}
}
```

リサイズモード `ResizeMode::Keep` が適用されている場合、ウィンドウを手動でリサイズしてもシーンのサイズは変化しません。シーンのサイズと実ウィンドウサイズのアスペクト比が異なる場合、クライアント領域の左右もしくは上下にレターボックスが生じます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Keep);
	Window::SetStyle(WindowStyle::Sizable);

	while (System::Update())
	{
		ClearPrint();
		Print << U"scene size: " << Scene::Size();
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;

		// 100px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}
	}
}
```


## 32.5 レターボックスの色を変更する
シーンのサイズと実ウィンドウサイズのアスペクト比が異なる際に、クライアント領域の左右もしくは上下に生じる余白領域をレターボックスと言います。レターボックスの色を変更するには `Scene::SetLetterbox()` で色を指定します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// レターボックスの色を変更する
	Scene::SetLetterbox(ColorF{ 0.8, 0.9, 1.0 });

	Scene::SetResizeMode(ResizeMode::Keep);
	Window::SetStyle(WindowStyle::Sizable);

	while (System::Update())
	{
		ClearPrint();
		Print << U"scene size: " << Scene::Size();
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;

		// 100px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}
	}
}
```


## 32.6 シーンのサイズだけを変更する
リサイズモードを `ResizeMode::Keep` にした状態で、`Scene::Resize()` を使うと、ウィンドウサイズはそのままでシーンのサイズだけを変更できます。

シーンと実ウィンドウサイズが異なるときは、`Cursor::Pos()` の代わりに `Cursor::PosF()` を使うと、シーンの拡大縮小に合わせた `Vec2` 型のマウスカーソル座標を取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Keep);

	// シーンを 1600x1200 にリサイズ
	Scene::Resize(1600, 1200);

	while (System::Update())
	{
		ClearPrint();
		Print << U"scene size: " << Scene::Size();
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;

		// マウスカーソルの座標を Vec2 型で取得する
		Print << Cursor::PosF();

		// 100px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}
	}
}
```


## 32.7 シーンが実ウィンドウに転送される際のフィルタを変更する
シーンのサイズと実ウィンドウサイズが異なる場合、シーン画像はアスペクト比を保ったまま、クライアント領域にフィットするよう拡大または縮小されて表示されますが、拡大縮小時に用いるテクスチャフィルタは 2 種類の選択肢があり、`Scene::SetTextureFilter()` で変更できます。

デフォルトでは、バイリニア法による補間 `TextureFilter::Linear` が使用されます。これは、画像を滑らかに拡大縮小するための補間方法です。一方、低解像度のシーンを、最近傍法による補間 `TextureFilter::Nearest` フィルタで拡大すると、フィルタリングされずにドット感を保ったまま拡大できます。

| テクスチャフィルタ | 説明 |
|--|--|
|`TextureFilter::Linear`| 画像をバイリニア補間します（デフォルト） |
|`TextureFilter::Nearest`| 画像を最近傍法で補間します |

=== "バイリニア法（デフォルト）"
	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/scene/7a.png)

=== "最近傍法"
	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/scene/7b.png)


```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Keep);

	// シーンのサイズを 200x150 にする
	Scene::Resize(200, 150);

	// シーン転送時の拡大縮小方法を最近傍法にする
	Scene::SetTextureFilter(TextureFilter::Nearest);

	const Texture texture{ U"🐈"_emoji };

	while (System::Update())
	{
		Circle{ 120, 75, 50 }.draw();

		texture.draw();
	}
}
```


## 32.8 ウィンドウの枠を消す
ウィンドウの枠を非表示にするには、`Window::SetStyle()` で `WindowStyle::Frameless` を設定します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウの枠を非表示にする
	Window::SetStyle(WindowStyle::Frameless);

	while (System::Update())
	{
		Circle{ Cursor::Pos(), 100 }.draw();
	}
}
```

ウィンドウの枠を非表示にすると、ユーザは手動でのウィンドウの移動、サイズ変更、閉じる操作などができなくなります。


## 32.9 ウィンドウのタイトルを変更する
ウィンドウのタイトルを変更するには、`Window::SetTitle()` に文字列や値を渡します。デバッグビルド時は、タイトルのほかに、`(Debug Build)` という文字列や、フレームレート、ウィンドウのサイズ、シーンのサイズなどの情報が合わせて表示されます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウのタイトルを変更する
	Window::SetTitle(U"My Game");

	while (System::Update())
	{

	}
}
```

!!! warning "実行中にタイトル変更を頻繁に行わない"
	ウィンドウタイトルの変更は時間のかかる処理であるため、毎フレーム `Window::SetTitle()` で異なるタイトルを設定することは避けてください。既に設定されているタイトルと同じタイトルを `Window::SetTitle()` に渡した場合には何もしないため、コストは発生しません。


## 32.10 ウィンドウをスクリーンの中心に移動する
`Window::Centering()` を使うと、ウィンドウを、現在ウィンドウがあるモニタのワークエリアの中心に移動できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Center", Vec2{ 20, 20 }))
		{
			// ウィンドウを中心に移動させる
			Window::Centering();
		}
	}
}
```


## 32.11 ウィンドウを移動させる
`Window::SetPos()` を使うと、ウィンドウを指定した位置に移動できます。現在のウィンドウの位置は `Window::GetPos()` で取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// スクリーン上のウィンドウの位置を表示する
		Print << Window::GetPos();

		if (SimpleGUI::Button(U"(0, 0)", Vec2{ 200, 20 }))
		{
			// ウィンドウをスクリーンの (0, 0) に移動させる
			Window::SetPos(0, 0);
		}

		if (SimpleGUI::Button(U"(200, 200)", Vec2{ 300, 20 }))
		{
			// ウィンドウをスクリーンの (200, 200) に移動させる
			Window::SetPos(200, 200);
		}
	}
}
```


## 32.12 ウィンドウを最小化 / 最大化する
プログラムによってウィンドウを最小化するには `Window::Minimize()` を、最大化するには `Window::Maximize()` を呼びます。ウィンドウを最大化する場合、ウィンドウスタイルが `WindowStyle::Sizable` である必要があります。最小化 / 最大化したウィンドウを以前のサイズに戻すときは `Window::Restore()` を呼びます。

ウィンドウが最小化されているかは `Window::GetState().minimized` で、最大化されているかは `Window::GetState().maximized` で取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウの最大化を可能にする
	Window::SetStyle(WindowStyle::Sizable);

	// 最小化中にカウントする変数
	int32 count = 0;

	while (System::Update())
	{
		ClearPrint();
		Print << U"scene size: " << Scene::Size();
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;
		Print << U"minimized: " << count;
		Print << U"maximized: " << Window::GetState().maximized;

		if (Window::GetState().minimized)
		{
			++count;
		}

		// 100px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ x * 100, y * 100, 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}

		if (SimpleGUI::Button(U"Minimize", Vec2{ 300, 40 }))
		{
			// ウィンドウを最小化する
			Window::Minimize();
		}

		if (SimpleGUI::Button(U"Maximize", Vec2{ 300, 80 }))
		{
			// ウィンドウを最大化する
			Window::Maximize();
		}

		if (SimpleGUI::Button(U"Restore", Vec2{ 300, 120 }))
		{
			// 最小化 / 最大化されたウィンドウを元のサイズに戻す
			Window::Restore();
		}
	}
}
```


## 32.13 モニタの情報を得る
接続されているモニタの情報の一覧を取得するには `System::EnumerateMonitors()` を使います。結果は `Array<MonitorInfo>` 型で得られます。

`MonitorInfo` 型のメンバ変数は次のとおりです。

| 変数 | 説明 |
|--|--|
| `String name` | ディスプレイの名前 |
| `String id` | ディスプレイ ID |
| `String displayDeviceName` | 内部的に使われているディスプレイの名前 |
| `Rect displayRect` | ディスプレイ全体の位置とサイズ |
| `Rect workArea` | タスクバーなどを除いた利用可能な領域の位置とサイズ |
| `Size fullscreenResolution` | フルスクリーン時の解像度 |
| `bool isPrimary` | メインディスプレイである場合 `true`, それ以外の場合は `false` |
| `Optional<Size> sizeMillimeter` | 物理的なサイズ (mm), 取得できなかった場合は `none` |
| `Optional<double> scaling` | UI の拡大倍率。取得できなかった場合は `none` |
| `Optional<double> refreshRate` | リフレッシュレート。取得できなかった場合は `none` |

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 接続されているモニタの情報一覧を取得
	const Array<MonitorInfo> monitors = System::EnumerateMonitors();

	for (const auto& monitor : monitors)
	{
		Print << U"name: " << monitor.name;
		Print << U"displayRect: " << monitor.displayRect << U" workArea: " << monitor.workArea;
		Print << U"fullscreenResolution: " << monitor.fullscreenResolution << U" sizeMillimeter: " << monitor.sizeMillimeter;
		Print << U"scaling: " << monitor.scaling << U" refreshRate: " << monitor.refreshRate;
		Print << U"isPrimary: " << monitor.isPrimary;
		Print << U"-----";
	}

	while (System::Update())
	{

	}
}
```

現在のプログラムのウィンドウが存在する**モニタのインデックス**を取得するには `System::GetCurrentMonitorIndex()` を使います。このインデックスは `System::EnumerateMonitors()` の戻り値の配列に対応します。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << System::GetCurrentMonitorIndex();
	}
}
```


## 32.14 フルスクリーンモードにする
アプリケーションを**フルスクリーンモード**にするには `Window::SetFullscreen(true)` を呼びます。ウィンドウモードに戻すには `Window::SetFullscreen(false)` を呼びます。サンプルコードでは扱っていませんが、`Window::SetFullscreen(true)` の第 2 引数には、フルスクリーンで表示する先のモニタのインデックスを指定できます。

アプリケーションがフルスクリーンモードであるかは `Window::GetState().fullscreen` で取得できます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"scene size: " << Scene::Size();
		Print << U"virtualSize: " << Window::GetState().virtualSize;
		Print << U"frameBufferSize: " << Window::GetState().frameBufferSize;
		Print << U"fullscreen: " << Window::GetState().fullscreen;

		// 100px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if ((x + y) % 2)
				{
					Rect{ x * 100, y * 100, 100 }.draw(ColorF{ 0.2, 0.3, 0.4 });
				}
			}
		}

		if (Window::GetState().fullscreen)
		{
			if (SimpleGUI::Button(U"Window mode", Vec2{ 300, 20 }))
			{
				// ウィンドウモードにする
				Window::SetFullscreen(false);
			}
		}
		else
		{
			if (SimpleGUI::Button(U"Fullscreen mode", Vec2{ 300, 20 }))
			{
				// フルスクリーンモードにする
				Window::SetFullscreen(true);
			}
		}
	}
}
```


## 32.15 全画面モードにする（Windows 版）
Windows 版では、アプリケーションの実行中に ++alt+enter++ を押すことで**全画面モード**にできます。挙動としてはフルスクリーンモードに近いですが、シーンのリサイズモードは `Resize::Keep` に設定され、シーンのサイズが変化しません。フルスクリーンモードや解像度の変更に対応していないアプリケーションを、全画面で大きく表示して実行したいときに役立ちます。このキー操作を無効にするには `Window::SetToggleFullscreenEnabled(false)` を呼びます。
