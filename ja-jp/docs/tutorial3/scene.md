# 44.  シーンとウィンドウ
Siv3D のシーンとウィンドウのカスタマイズ方法を学びます。

## 44.1 シーンとウィンドウの概要
- Siv3D では、図形やテクスチャ、テキストなどを `.draw()` すると、「シーン」と呼ばれる仮想の画面に描画されます
- その後、`System::Update()` でシーンの画像がウィンドウに転送されることで、ユーザは描画結果をウィンドウ上で目にすることができます

<div class="noshadow-90"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene/1.png"></div>

- これらの処理は自動的に行われるため、ここまでは特に意識することなく `.draw()` した内容をウィンドウで確認することができました
- この章ではシーンやウィンドウの仕組みを掘り下げます

## 44.2 「3 つのサイズ」
- Siv3D プログラムにおける画面表示を正しく把握するには、次の「3 つのサイズ」を理解することが重要です：
	- ① シーンのサイズ
	- ② 仮想ウィンドウサイズ
	- ③ 実ウィンドウサイズ（フレームバッファサイズ）

### ① シーンのサイズ
- `.draw()` による描画や `Cursor::Pos()` のマウスカーソル座標などの基準になる、独立した 1 つのシーンのサイズです
- デフォルトでは 800 × 600 で、「② 仮想ウィンドウサイズ」と一致します
- Siv3D の機能を使ってスクリーンショットを撮影したときは、この解像度で保存されます
- シーンサイズの取得に関連する関数は次のとおりです：

| 関数 | 戻り値 | 説明 |
|--|--|--|
| `Scene::Size()` | `Size` | シーンのサイズを返す |
| `Scene::Width()` | `int32` | シーンの幅を返す |
| `Scene::Height()` | `int32` | シーンの高さを返す |
| `Scene::Center()` | `Point` | シーンの中心座標を返す<br>`Scene::Size() / 2` と同じ |
| `Scene::CenterF()` | `Vec2` | シーンの中心座標を返す<br>`Scene::Size() / 2.0` と同じ |
| `Scene::Rect()` | `Rect` | シーンの矩形を返す<br>`Rect{ 0, 0, Scene::Size() }` と同じ |

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

### ② 仮想ウィンドウサイズ
- ユーザのデスクトップ上における、ウィンドウの**クライアント領域**（タイトルバーやフレームを除く、描画が行われる領域）の見かけのサイズです
- デフォルトでは 800 × 600 です
- `Window::GetState().virtualSize` で取得できます
- この仮想ウィンドウサイズに、OS で設定されている拡大縮小倍率（150 %, 200 % など）を乗算したものが「③ 実ウィンドウサイズ」になります

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
	}
}
```

!!! warning "Linux 版での注意"
	- 現在の Siv3D Linux 版は OS 設定の拡大縮小倍率に未対応で、② 仮想ウィンドウサイズと ③ 実ウィンドウサイズが等しくなります

### ③ 実ウィンドウサイズ（フレームバッファサイズ）
- モニタ上の実ピクセル数で計測した、ウィンドウのクライアント領域のサイズです
- デフォルトでは 800 × 600 に、OS の拡大縮小倍率を乗算した値になります
- `Window::GetState().frameBufferSize` で取得できます

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;
	}
}
```

### OS 設定の拡大縮小倍率
- 現在のウィンドウが表示されているモニタの、OS 設定の拡大縮小倍率は `Window::GetState().scaling` で取得できます
- `1.0` が 100 %、`1.25` が 125 %、`1.5` が 150 %、`2.0` が 200 % です

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;
		Print << U"Scaling: " << Window::GetState().scaling;
	}
}
```

#### 3 つのサイズの関係
- シーンへの描画結果は、アスペクト比を保ったまま、実ウィンドウサイズにフィットするよう拡大縮小されてウィンドウに転送されます
- これにより、自動的にユーザのモニタ環境に最適なサイズでコンテンツを表示できます
	- 例えば OS 設定の拡大縮小倍率が 200 % の高 DPI 環境では、800 × 600 で描画したシーンが 1600 × 1200 に拡大されて表示されるため、「文字が小さすぎてゲームがプレイできない」といった問題は起こりません
- さまざまな OS 設定の拡大縮小倍率における 3 つのサイズの関係を次の表にまとめました：

| OS 設定の拡大縮小倍率 | ① シーンのサイズ | ② 仮想ウィンドウサイズ | ③ 実ウィンドウサイズ |
|--|--|--|--|
| 100% | 800x600 | 800x600 | 800x600 |
| 125% | 800x600 | 800x600 | 1000x750 |
| 150% | 800x600 | 800x600 | 1200x900 |
| 200% | 800x600 | 800x600 | 1600x1200 |

- 一方で、高 DPI 環境では、それに応じた高解像度で、高精細にシーンの描画を行いたい場合があります
- 例えば OS 設定の拡大縮小倍率が 200 % の場合、800 × 600 のシーンを 1600 × 1200 で描画して、実ウィンドウサイズと一致させることが考えられます
- シーンのサイズを実ウィンドウサイズに合わせたい場合、**44.3** で説明するリサイズモード `ResizeMode::Actual` を使用します


## 44.3 シーンのリサイズモード
- 次のような操作を行うと、実ウィンドウサイズや仮想ウィンドウサイズが変化します
	- ウィンドウを異なるモニタに移動させる
	- ウィンドウをリサイズする関数（**44.4**）を呼ぶ
	- リサイズ可能なウィンドウ（**44.5**）をユーザがマウスでリサイズする
- これらのサイズの変更に伴い、シーンのサイズも更新が必要になります
- このとき新しいシーンのサイズを決定するのが、シーンの**リサイズモード**です
- リサイズモードは次の 3 種類です：

| リサイズモード | 説明 |
|--|--|
|`ResizeMode::Virtual` | 仮想ウィンドウサイズを新しいシーンのサイズにする（デフォルト） |
|`ResizeMode::Actual` | 実ウィンドウサイズを新しいシーンのサイズにする |
|`ResizeMode::Keep` | シーンのサイズを変更しない |

- 現在のリサイズモードは `Scene::GetResizeMode()` で取得できます
- リサイズモードは `Scene::SetResizeMode(ResizeMode)` で設定します
	- 設定すると、直ちにシーンのサイズも新しいリサイズモードに合わせて更新されます
- 次のサンプルでは、プログラムの起動直後にリサイズモードを `ResizeMode::Actual` に変更しています
- OS 設定の拡大縮小倍率が 100 % より大きい場合、シーンのサイズが 800 × 600 よりも大きくなり、そのサイズのシーンにより高精細な描画ができるようになります

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Actual);

	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene::Size(): " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;

		// シーンのサイズを確認するための 100 px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```


## 44.4 ウィンドウのリサイズ
- `Window::Resize(幅, 高さ)` または `Window::Resize(サイズ)` で、仮想ウィンドウサイズを変更できます
- 仮想ウィンドウサイズの変更に伴い、実ウィンドウサイズが変更され、リサイズモードに応じてシーンのサイズも更新されます
- デフォルトのリサイズモード `ResizeMode::Virtual` を設定している場合、`Window::Resize()` で指定した新しい仮想ウィンドウサイズが新しいシーンのサイズになるため、そこまで難しいことはありません

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1000, 600);

	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;
		
		// シーンのサイズを確認するための 100 px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```

- リサイズモード `ResizeMode::Keep` を設定している場合、`Window::Resize()` を行ってもシーンのサイズは変化しません
- シーンのサイズと実ウィンドウサイズのアスペクト比が異なる場合、クライアント領域の左右もしくは上下に**レターボックス**と呼ばれる余白領域が生じます

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Keep);
	Window::Resize(1000, 600);

	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;

		// シーンのサイズを確認するための 100 px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```


## 44.5 手動リサイズ
- ウィンドウを手動でリサイズできるようにするには、`Window::SetStyle(WindowStyle::Sizable)` を設定します
- ユーザの操作によって実ウィンドウサイズ・仮想ウィンドウサイズが変更されると、リサイズモードに応じてシーンのサイズも更新されます
- デフォルトのリサイズモード `ResizeMode::Virtual` を設定している場合、新しい仮想ウィンドウサイズが新しいシーンのサイズになります

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::SetStyle(WindowStyle::Sizable);

	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;

		// シーンのサイズを確認するための 100 px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```

- リサイズモード `ResizeMode::Keep` を設定している場合、ウィンドウを手動でリサイズしてもシーンのサイズは変化しません
- シーンのサイズと実ウィンドウサイズのアスペクト比が異なる場合、クライアント領域の左右もしくは上下にレターボックスが生じます

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetResizeMode(ResizeMode::Keep);
	Window::SetStyle(WindowStyle::Sizable);

	while (System::Update())
	{
		ClearPrint();
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;

		// シーンのサイズを確認するための 100 px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```


## 44.6 レターボックスの色の変更
- レターボックスの色を変更するには `Scene::SetLetterbox(color)` で色を指定します
- `Scene::SetBackground(color)` と同様に、一度変更したレターボックスの色は、再度変更するまでそのままです

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
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;

		// シーンのサイズを確認するための 100 px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```


## 44.7 シーンのサイズのみ変更
- リサイズモードを `ResizeMode::Keep` に設定している場合、シーンのサイズはウィンドウの操作によって変更されません
- 代わりに `Scene::Resize(幅, 高さ)` または `Scene::Resize(サイズ)` を使って、シーンのサイズを変更します
- シーンと実ウィンドウサイズが異なるときは、`Cursor::Pos()` の代わりに `Cursor::PosF()` を使うと、より情報量の多い `Vec2` 型でマウスカーソル座標を取得できます

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
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;

		// マウスカーソルの座標を Vec2 型で取得する
		Print << Cursor::PosF();

		// シーンのサイズを確認するための 100 px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
				}
			}
		}
	}
}
```


## 44.8 シーン拡大縮小フィルタ
- シーンをウィンドウに転送する際、シーンのサイズと実ウィンドウサイズが異なる場合、シーン画像はアスペクト比を保ったまま、クライアント領域にフィットするよう拡大または縮小されます
- 拡大縮小時に用いるテクスチャフィルタは 2 種類の選択肢があり、`Scene::SetTextureFilter(テクスチャフィルタ)` で変更できます
- デフォルトでは、バイリニア法による補間 `TextureFilter::Linear` が使用されます
	- これは、画像を滑らかにフィルタリングして拡大縮小する補間方法です
- 一方、低解像度のシーンを、最近傍法による補間 `TextureFilter::Nearest` フィルタで拡大すると、フィルタリングされずにドット感を維持できます

| テクスチャフィルタ | 説明 |
|--|--|
|`TextureFilter::Linear`| 画像をバイリニア補間（デフォルト） |
|`TextureFilter::Nearest`| 画像を最近傍法で補間 |

=== "バイリニア法（デフォルト）"
	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene/7a.png)


	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetResizeMode(ResizeMode::Keep);

		// シーンのサイズを 200x150 にする
		Scene::Resize(200, 150);

		const Texture texture{ U"🐈"_emoji };

		while (System::Update())
		{
			Circle{ 120, 75, 50 }.draw();

			texture.draw();
		}
	}
	```


=== "最近傍法"
	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/scene/7b.png)


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


## 44.9 ウィンドウ枠の非表示
- ウィンドウの枠を非表示にするには、`Window::SetStyle()` で `WindowStyle::Frameless` を設定します
- 手動でのウィンドウの移動、サイズ変更、閉じる操作などはできなくなります

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


## 44.10 タイトルの変更
- ウィンドウのタイトルを変更するには、`Window::SetTitle()` に文字列や値を渡します
- デバッグビルド時は、タイトルのほかに、`(Debug Build)` という文字列と、フレームレート、ウィンドウのサイズ、シーンのサイズなどの情報が合わせて表示されます

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
	- ウィンドウタイトルの変更は時間のかかる処理です
	- 毎フレーム `Window::SetTitle()` で異なるタイトルを設定することは避けてください
	- 既に設定されているタイトルと同じタイトルを `Window::SetTitle()` に渡した場合は、何もしないためコストは発生しません


## 44.11 ウィンドウの移動（1）
- `Window::Centering()` は、ウィンドウを、現在のモニタのワークエリアの中心に移動させます

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


## 44.12 ウィンドウの移動（2）
- `Window::SetPos(x, y)` または `Window::SetPos(pos)` は、ウィンドウを指定した位置に移動させます
- 現在のウィンドウの位置は `Window::GetPos()` で取得できます

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


## 44.13 最小化・最大化
- プログラムによってウィンドウを最小化するには `Window::Minimize()` を呼びます
- 最大化するには `Window::Maximize()` を呼びます
- ウィンドウを最大化するには、ウィンドウスタイルが `WindowStyle::Sizable` である必要があります
- 最小化 / 最大化したウィンドウを以前のサイズに戻すときは `Window::Restore()` を呼びます
- ウィンドウが最小化されているかは `Window::GetState().minimized` で取得できます
- 最大化されているかは `Window::GetState().maximized` で取得できます

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
		Print << U"Scene Size: " << Scene::Size();
		Print << U"Virtual Size: " << Window::GetState().virtualSize;
		Print << U"Frame Buffer Size: " << Window::GetState().frameBufferSize;
		Print << U"Minimized Frame Count: " << count;
		Print << U"Maximized: " << Window::GetState().maximized;

		if (Window::GetState().minimized)
		{
			++count;
		}

		// シーンのサイズを確認するための 100 px サイズの市松模様
		for (int32 y = 0; y < 50; ++y)
		{
			for (int32 x = 0; x < 50; ++x)
			{
				if (IsEven(x + y))
				{
					Rect{ (x * 100), (y * 100), 100 }.draw(ColorF{ 0.4 });
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


## 44.14 モニタの情報
- 接続されているモニタの情報の一覧を取得するには `System::EnumerateMonitors()` を使います
- 結果は `Array<MonitorInfo>` 型で得られます
- `MonitorInfo` 型のメンバ変数は次のとおりです

| メンバ変数 | 説明 |
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

- 現在のプログラムのウィンドウが存在する**モニタのインデックス**を取得するには `System::GetCurrentMonitorIndex()` を使います
- このインデックスは `System::EnumerateMonitors()` が返す配列に対応します

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


## 44.15 フルスクリーンモード
- アプリケーションを**フルスクリーンモード**にするには `Window::SetFullscreen(true)` を呼びます
- ウィンドウモードに戻すには `Window::SetFullscreen(false)` を呼びます
- `Window::SetFullscreen(true)` の第 2 引数には、フルスクリーンで表示する先のモニタのインデックスを指定できます
- アプリケーションがフルスクリーンモードであるかは `Window::GetState().fullscreen` で取得できます

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


## 44.16 全画面モード（Windows）
- Windows では、アプリケーションの実行中に ++alt+enter++ を押すことで**全画面モード**にできます（**チュートリアル 5.5**）
- 挙動としてはフルスクリーンモードに近いですが、シーンのリサイズモードは `Resize::Keep` に設定され、シーンのサイズは変化しません
- フルスクリーンモードや解像度の変更に対応していないアプリケーションを、全画面で大きく表示して実行したいときに役立ちます
- このキー操作を無効にするには `Window::SetToggleFullscreenEnabled(false)` を呼びます
