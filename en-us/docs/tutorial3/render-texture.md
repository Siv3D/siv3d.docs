# 41. レンダーテクスチャ
図形やテクスチャ、フォントの描画先をシーンではなくテクスチャにする方法を学びます。

## 41.1 レンダーテクスチャの基本
これまで、図形やテクスチャ、フォントの描画はシーンに対して行われていましたが、**レンダーテクスチャ**機能を使うことで、プログラムで用意した別のテクスチャ（レンダーテクスチャ）に対して描画できるようになります。そのテクスチャを描画に再利用することで、より高度で複雑なグラフィックス処理を実現できます。

`RenderTexture` を作成し、`ScopedRenderTarget2D` オブジェクトのコンストラクタにレンダーテクスチャを渡すと、`ScopedRenderTarget2D` オブジェクトのスコープが有効な間、図形やテクスチャ、フォントがそのレンダーテクスチャに描画されます（**レンダーターゲット（描画先）の変更**）。描画先になったレンダーテクスチャは、**レンダーターゲットから解除されたあと**に、通常のテクスチャのように描画に使うことができます。`RenderTexture` は `Texture` と同じ描画系のメンバ関数を持ちます。

`RenderTexture` の作成にはコストがかかるため、毎フレーム新しく作成するのではなく、事前に作成しておき、使い回すようにしてください。

図形やテクスチャ、フォントの `.draw()` による描画は、チュートリアル 53. で学ぶ `Image` への書き込み (`.paint()` や `.overwrite()`) と異なり、GPU 上で実行されるため高速です。レンダーステートも適用されるため、通常のシーンへの描画と同様に柔軟な描画ができます。

`RenderTexture` は `.clear(color)` によって、内容を指定した色にクリアできます。クリアしない場合は、それまで描いた内容が残り続けます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/render-texture/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🐈"_emoji };

	// 200x200 のサイズのレンダーテクスチャを作成する。初期状態は白色
	const RenderTexture renderTexture{ Size{ 200, 200 }, Palette::White };

	while (System::Update())
	{
		// レンダーテクスチャを白色でクリアする
		renderTexture.clear(Palette::White);

		{
			// レンダーターゲットを renderTexture に変更する
			const ScopedRenderTarget2D target{ renderTexture };

			Circle{ 200, 200, 160 }.draw(ColorF{ 0.8, 0.9, 1.0 });

			emoji.rotated(Scene::Time() * 30_deg).drawAt(100, 100);
		} // ここで target のスコープが終了し、レンダーターゲットがシーンに戻る

		// レンダーテクスチャを描画する
		renderTexture.draw(0, 0);
		renderTexture.draw(200, 200);
		renderTexture.draw(400, 400);
	}
}
```

`RenderTexture` の `.clear()` は自身の参照を返すため、クリアと `ScopedRenderTarget2D` への設定を 1 行に短くまとめて記述することができます。

```cpp hl_lines="15-16"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🐈"_emoji };

	// 200x200 のサイズのレンダーテクスチャを作成する。初期状態は白色
	const RenderTexture renderTexture{ 200, 200, Palette::White };

	while (System::Update())
	{
		{
			// renderTexture をクリアし、レンダーターゲットを renderTexture に変更する
			const ScopedRenderTarget2D target{ renderTexture.clear(Palette::White) };

			Circle{ 200, 200, 160 }.draw(ColorF{ 0.8, 0.9, 1.0 });

			emoji.rotated(Scene::Time() * 30_deg).drawAt(100, 100);
		}

		renderTexture.draw(0, 0);
		renderTexture.draw(200, 200);
		renderTexture.draw(400, 400);
	}
}
```


## 41.2 クリアをしない使い方
描画内容が変わらない場合、描画コストの削減のために `RenderTexture` のクリアおよび `RenderTexture` への描画を省略するのも有効です。

下記のサンプルでは、最初のフレームで `RenderTexture` に描画を行い、以降は `RenderTexture` を描画することで、毎フレームの描画コストを削減しています。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/render-texture/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🔥"_emoji };

	// 200x200 のサイズのレンダーテクスチャを作成する。初期状態は白色
	const RenderTexture renderTexture{ Size{ 400, 400 }, Palette::White };
	{
		// レンダーターゲットを renderTexture に変更する
		const ScopedRenderTarget2D target{ renderTexture };

		for (int32 i = 0; i < 30; ++i)
		{
			emoji.drawAt(RandomVec2(Rect{ 0, 0, 400, 400 }));
		}
	}

	while (System::Update())
	{
		// レンダーテクスチャを描画する
		renderTexture.draw(0, 0);
		renderTexture.draw(400, 200);
	}
}
```


## 41.3 透過レンダーテクスチャへの書き込み
デフォルトのブレンドステートでは、初期状態が透過色の `RenderTexture` に描画を行うと、`RenderTexture` の内容の RGB 成分は更新されますがアルファチャン成分は更新されません。つまり全体が透過状態なので、そのまま描画しても何も表示されません。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/render-texture/3a.png)

```cpp hl_lines="19-20"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8 });

	const int32 cellSize = 20;

	const Texture emoji{ U"🔥"_emoji };

	// 400x400 のサイズのレンダーテクスチャを作成する。初期状態は ColorF{ 0.5, 0.0 }
	const RenderTexture renderTexture{ Size{ 400, 400 }, ColorF{ 0.5, 0.0 } };
	{
		// レンダーターゲットを renderTexture に変更する
		const ScopedRenderTarget2D target{ renderTexture };

		for (int32 i = 0; i < 30; ++i)
		{
			// この描画はレンダーテクスチャのアルファ成分を更新しない
			emoji.drawAt(RandomVec2(Rect{ 0, 0, 400, 400 }));
		}
	}

	while (System::Update())
	{
		for (int32 y = 0; y < (Scene::Height() / cellSize); ++y)
		{
			for (int32 x = 0; x < (Scene::Width() / cellSize); ++x)
			{
				if (IsEven(y + x))
				{
					Rect{ (x * cellSize), (y * cellSize), cellSize }.draw(ColorF{ 0.75 });
				}
			}
		}

		// レンダーテクスチャを描画する
		renderTexture.draw(0, 0);
	}
}
```

そこで、描画された最大のアルファ成分を保持するブレンドステートを設定します。これによって、`RenderTexture` のアルファ成分が更新されるようになります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/render-texture/3b.png)

```cpp hl_lines="27-28 32-33"
# include <Siv3D.hpp>

// 描画された最大のアルファ成分を保持するブレンドステートを作成する
BlendState MakeBlendState()
{
	BlendState blendState = BlendState::Default2D;
	blendState.srcAlpha = Blend::SrcAlpha;
	blendState.dstAlpha = Blend::DestAlpha;
	blendState.opAlpha = BlendOp::Max;
	return blendState;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.8 });

	const int32 cellSize = 20;

	const Texture emoji{ U"🔥"_emoji };

	// 400x400 のサイズのレンダーテクスチャを作成する。初期状態は ColorF{ 0.5, 0.0 }
	const RenderTexture renderTexture{ Size{ 400, 400 }, ColorF{ 0.5, 0.0 } };
	{
		// レンダーターゲットを renderTexture に変更する
		const ScopedRenderTarget2D target{ renderTexture };

		// 描画された最大のアルファ成分を保持するブレンドステート
		const ScopedRenderStates2D blend{ MakeBlendState() };

		for (int32 i = 0; i < 30; ++i)
		{
			// この描画はレンダーテクスチャのアルファ成分を更新する
			emoji.drawAt(RandomVec2(Rect{ 0, 0, 400, 400 }));
		}
	}

	while (System::Update())
	{
		for (int32 y = 0; y < (Scene::Height() / cellSize); ++y)
		{
			for (int32 x = 0; x < (Scene::Width() / cellSize); ++x)
			{
				if (IsEven(y + x))
				{
					Rect{ (x * cellSize), (y * cellSize), cellSize }.draw(ColorF{ 0.75 });
				}
			}
		}

		// レンダーテクスチャを描画する
		renderTexture.draw(0, 0);
	}
}
```


## 41.4 マルチサンプル・レンダーテクスチャ
`RenderTexture` への描画では、**マルチサンプル・アンチエイリアシング**が適用されず、斜めの線を含むような図形を描画した際にジャギーが生じます。マルチサンプル・アンチエイリアシングを適用したい場合は `MSRenderTexture` を使います。

| 描画対象 | マルチサンプル・アンチエイリアシング |
| :--- | :--- |
| 通常のシーン | 有効 |
| `RenderTexture` | 無効 |
| `MSRenderTexture` | 有効 |

`MSRenderTexture` をレンダーターゲットに設定する方法は `RenderTexture` と同様ですが、その描画結果を使う際には下記の 2 つの追加の手順が必要になります。

1. `Graphics2D::Flush()` を呼び、その時点までの 2D 描画処理をすべて実行（フラッシュ）して `MSRenderTexture` のマルチサンプル・テクスチャに対して描画内容を確実に書き込む
2. `MSRenderTexture` の `.resolve()` によって、`MSRenderTexture` 内のマルチサンプル・テクスチャを、描画で使用可能な通常のテクスチャに変換（リゾルブ）する

この手順が必要な理由は、Siv3D における `.draw()` は「予約」で、`.resolve()` は「即時実行」であり、`Graphics2D::Flush()` を行わないと、レンダーテクスチャに何も描かれていない状態で `resolve()` が実行されてしまうためです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/render-texture/4.png)

```cpp hl_lines="31-35"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// レンダーテクスチャ
	const RenderTexture renderTexture{ 200, 200, Palette::White };

	// マルチサンプル・レンダーテクスチャ
	const MSRenderTexture msRenderTexture{ 200, 200, Palette::White };

	while (System::Update())
	{
		// レンダーテクスチャ
		{
			const ScopedRenderTarget2D target{ renderTexture.clear(Palette::Black) };
			Rect{ Arg::center(100, 100), 80 }
				.rotated(Scene::Time() * 30_deg).draw();
		}

		renderTexture.draw(100, 0);

		// マルチサンプル・レンダーテクスチャ
		{
			const ScopedRenderTarget2D target{ msRenderTexture.clear(Palette::Black) };
			Rect{ Arg::center(100, 100), 80 }
				.rotated(Scene::Time() * 30_deg).draw();
		}

		// 2D 描画をフラッシュする
		Graphics2D::Flush();

		// マルチサンプル・テクスチャをリゾルブする
		msRenderTexture.resolve();

		msRenderTexture.draw(400, 0);
	}
}
```


## 41.5 RenderTexture に対する便利な操作
`RenderTexture` を使った、次のような高速な画像処理機能が提供されています。

#### void Shader::Downsample(const TextureRegion& from, const RenderTexture& to);
- `from`: 入力テクスチャ
- `to`: 出力テクスチャ

`from` のテクスチャの内容を拡大縮小して `to` に描画します。`from` と `to` はともに有効なテクスチャで、互いに異なるテクスチャでなければなりません。


#### void Shader::GaussianBlur(const TextureRegion& from, const RenderTexture& internalBuffer, const RenderTexture& to);
- `from`: 入力テクスチャ
- `internalBuffer`: 中間テクスチャ
- `to`: 出力テクスチャ

`from` のテクスチャに縦方向と横方向のガウスブラーをかけて `to` に描画します。`from`, `internalBuffer`, `to` はいずれも有効なテクスチャで、領域のサイズが同じでなければなりません。`from` と `to` は同じテクスチャにできます。

1 回のガウスブラー処理で得られる効果はそれほど大きくありません。大きなガウスブラー効果を得るには、ダウンサンプルしたテクスチャにガウスブラーをかけたあと、それを元のサイズに拡大描画する方法が有効です。


#### void Shader::Copy(const TextureRegion& from, const RenderTexture& to);
- `from`: 入力テクスチャ
- `to`: 出力テクスチャ

`from` のテクスチャの内容を `to` に描画します。`from` と `to` はともに有効なテクスチャで、互いに異なり、領域のサイズが同じでなければなりません。

通常は `Texture` 型や `TextureRegion` 型の変数への代入で間に合うため `Shader::Copy()` が必要になるのは特殊なケースです。例えば大きいレンダーテクスチャから一部の領域だけを切り出して使う場合、`Shader::Copy()` の実行後に大きいレンダーテクスチャを破棄することで、メモリを節約できます。


### 41.5.1 ダウンサンプル

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/render-texture/5a.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture texture{ U"example/windmill.png" };

	// 縦、横が 4 分の 1 サイズのレンダーテクスチャ
	const RenderTexture renderTexture{ texture.size() / 4 };

	// ダウンサンプルを実行する
	Shader::Downsample(texture, renderTexture);

	while (System::Update())
	{
		renderTexture.draw();
	}
}
```

??? info "画像のダウンサンプルの別の方法"
	`Image` を使ったダウンサンプルも可能です。高品質な結果を得られますが、CPU で処理するため `RenderTexture` を使ったダウンサンプルよりも時間がかかり、毎フレームの実行などリアルタイム処理には向きません。

	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// 元画像から縦、横を 4 分の 1 サイズにしてテクスチャを作成する
		const Texture texture{ Image{ U"example/windmill.png" }.scaled(0.25) };

		while (System::Update())
		{
			texture.draw();
		}
	}
	```


### 41.5.2 ガウスぼかし

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/render-texture/5b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture texture{ U"example/windmill.png" };
	const RenderTexture internalTexture{ texture.size() };
	const RenderTexture renderTexture{ texture.size() };

	Shader::GaussianBlur(texture, internalTexture, renderTexture);

	while (System::Update())
	{
		renderTexture.draw();
	}
}
```


## 41.6 強いガウスぼかし
ガウスぼかしを重ねて適用するよりも、ダウンサンプルしたテクスチャにガウスぼかしをかけたあと元のサイズで拡大描画するほうが、より低いコストで大きなぼかし効果を実現できます (3), (4)。ダウンサンプル前にガウスぼかしをかけると、コストは増えますが、ぼかしの品質が向上します (5)。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/render-texture/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// (0) オリジナル
	const Texture original{ U"example/windmill.png" };

	// (1) ガウスぼかし 1 回
	const RenderTexture blur1{ original.size() };
	const RenderTexture internalTexture{ original.size() };
	Shader::GaussianBlur(original, internalTexture, blur1);

	// (2) ガウスぼかし 2 回
	const RenderTexture blur2{ original.size() };
	Shader::GaussianBlur(blur1, internalTexture, blur2);

	// (3) 1/2 ダウンサンプル + ガウスぼかし 1 回
	const RenderTexture downsample2{ original.size() / 2 };
	const RenderTexture internalTexture2{ original.size() / 2 };
	Shader::Downsample(original, downsample2);
	Shader::GaussianBlur(downsample2, internalTexture2, downsample2);

	// (4) 1/4 ダウンサンプル + ガウスぼかし 1 回
	const RenderTexture downsample4{ original.size() / 4 };
	const RenderTexture internalTexture4{ original.size() / 4 };
	Shader::Downsample(original, downsample4);
	Shader::GaussianBlur(downsample4, internalTexture4, downsample4);

	// (5) ガウスぼかし + 1/2 ダウンサンプル + ガウスぼかし + 1/2 ダウンサンプル + ガウスぼかし
	const RenderTexture downsampleB2{ original.size() / 2 };
	const RenderTexture downsampleB4{ original.size() / 4 };
	Shader::Downsample(blur1, downsampleB2);
	Shader::GaussianBlur(downsampleB2, internalTexture2, downsampleB2);
	Shader::Downsample(downsampleB2, downsampleB4);
	Shader::GaussianBlur(downsampleB4, internalTexture4, downsampleB4);

	size_t index = 0;

	while (System::Update())
	{
		if (index == 0)
		{
			original.draw(40, 40);
		}
		else if (index == 1)
		{
			blur1.draw(40, 40);
		}
		else if (index == 2)
		{
			blur2.draw(40, 40);
		}
		else if (index == 3)
		{
			downsample2.scaled(2.0).draw(40, 40);
		}
		else if (index == 4)
		{
			downsample4.scaled(4.0).draw(40, 40);
		}
		else if (index == 5)
		{
			downsampleB4.scaled(4.0).draw(40, 40);
		}

		SimpleGUI::RadioButtons(index, { U"original", U"1x blur", U"2x blur", U"1/2 scale + 1x blur", U"1/4 scale + 1x blur", U"1x + 1/2 + 1x + 1/2 + 1x" }, Vec2{ 530, 40 });
	}
}
```


## 41.7 指定領域にガウスぼかしをかける
背景がぼけて透過するような効果を実現するために、シーン全体をぼかしたレンダーテクスチャを用意して、その一部を切り出して描画するという方法があります。

下記のサンプルコードではシーン全体をぼかしていますが、ぼかす領域が固定である場合、最小限の領域だけにぼかしをかけることで、より高速に処理することができるでしょう。

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/render-texture/7.mp4?raw=true" autoplay loop muted playsinline></video>

```cpp
# include <Siv3D.hpp>

// シーン全体のテクスチャのうち一部領域の UV を計算する関数
RectF CalculateUVRect(const Size& scenceSize, const RectF& region)
{
	return{ (region.pos / scenceSize), (region.size / scenceSize) };
}

void Main()
{
	// シーンサイズ
	const Size sceneSize{ 1280, 720 };

	// ウィンドウをリサイズする
	Window::Resize(sceneSize);

	// bay.jpg は 2560x1440 なのでサイズを小さくしてロードする
	const Texture texture{ Image{ U"example/bay.jpg" }.scale(1280, 720) };
	const Texture emoji1{ U"🚢"_emoji };
	const Texture emoji2{ U"🐟"_emoji };

	// メイン描画用のレンダーテクスチャ
	const MSRenderTexture msRenderTexture{ sceneSize };

	// ガウスぼかし用のテクスチャ
	const RenderTexture internalTexture{ sceneSize };
	const RenderTexture blur1{ sceneSize };
	const RenderTexture blur4{ sceneSize / 4 };
	const RenderTexture internalTexture4{ sceneSize / 4 };

	while (System::Update())
	{
		// レンダーテクスチャにテクスチャや絵文字を描画する
		{
			const ScopedRenderTarget2D target{ msRenderTexture.clear(ColorF{ 0.6, 0.8, 0.7 })};
			texture.draw();
			emoji1.drawAt(Vec2{ (640 + Periodic::Sine1_1(4s) * 300.0), (200.0 + Periodic::Sine1_1(3s) * 100.0) });
			emoji2.drawAt(Vec2{ (640 + Periodic::Sine1_1(5s) * 300.0), (500.0 + Periodic::Sine1_1(2s) * 100.0) });
		}

		// レンダーテクスチャをリゾルブする
		{
			// 2D 描画をフラッシュする
			Graphics2D::Flush();

			// マルチサンプル・テクスチャをリゾルブする
			msRenderTexture.resolve();
		}

		// ぼかしテクスチャを用意する
		{
			Shader::GaussianBlur(msRenderTexture, internalTexture, blur1);
			Shader::Downsample(blur1, blur4);
			Shader::GaussianBlur(blur4, internalTexture4, blur4);
		}

		// レンダーテクスチャをシーンに描画する
		msRenderTexture.draw();

		// ミニウィンドウの描画領域
		const RoundRect miniWindow{ Arg::center = Cursor::Pos(), 480, 360 , 24 };

		// ミニウィンドウにぼかしテクスチャの指定領域を貼り付けて描画する
		miniWindow(blur4.uv(CalculateUVRect(SceneSize, miniWindow.rect))).draw();

		// ミニウィンドウを描画する
		miniWindow.draw(ColorF{ 1.0, 0.85 });
	}
}
```


## 41.8 任意形状のシャドウ
シャドウ用のテクスチャを用意して、それをぼかしたものを描画することで、任意形状のシャドウを実現できます。

レンダーテクスチャを `ColorF{ 1.0, 0.0 }` でクリアしてから、ブレンドステート `BlendState::MaxAlpha` を適用して描き込みをすると、RGB 値は無視され、描画された最大のアルファ値を保持するようにできます。色の付いた絵文字の RGB 成分を無視しできるため影用の形状だけを描いたテクスチャの作成に最適なブレンドステートです。

下記サンプルコードでは、マウスを左クリックしている間はぼかし済みの影テクスチャ `blur4` のみを可視化します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/render-texture/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Texture emoji{ U"🐈"_emoji };

	// 影用のレンダーテクスチャ
	const RenderTexture shadowTexture{ Scene::Size(), ColorF{ 1.0, 0.0 } };
	const RenderTexture blur4{ shadowTexture.size() / 4 };
	const RenderTexture internal4{ shadowTexture.size() / 4 };

	while (System::Update())
	{
		const double angle = (Scene::Time() * 10_deg);

		// 影の形状を描く
		{
			const ScopedRenderTarget2D target{ shadowTexture.clear(ColorF{ 1.0, 0.0 }) };

			// RGB 値は無視して、描画された最大のアルファ値を保持するブレンドステートを適用する
			const ScopedRenderStates2D blend{ BlendState::MaxAlpha };

			// 影を右下方向に落とすため、描画位置をずらす
			const Transformer2D transform{ Mat3x2::Translate(2, 2) };

			Shape2D::Hexagon(100, Vec2{ 200, 200 }).draw();
			Shape2D::Star(120, Vec2{ 400, 400 }, angle).draw();
			Shape2D::RectBalloon(Rect{ 500, 103, 200, 100 }, Vec2{ 480, 240 }).drawFrame(10);
			emoji.rotated(angle).drawAt(600, 500);
		}

		// shadowTexture をダウンサンプリング + ガウスぼかし
		{
			Shader::Downsample(shadowTexture, blur4);
			Shader::GaussianBlur(blur4, internal4, blur4);
		}

		// ぼかした影を描く
		blur4.resized(Scene::Size()).draw(ColorF{ 0.0, 0.5 });

		// 通常の形状を描く
		if (not MouseL.pressed())
		{
			Shape2D::Hexagon(100, Vec2{ 200, 200 }).draw();
			Shape2D::Star(120, Vec2{ 400, 400 }, angle).draw(Palette::Yellow);
			Shape2D::RectBalloon(Rect{ 500, 100, 200, 100 }, Vec2{ 480, 240 })
				.drawFrame(10, Palette::Seagreen);
			emoji.rotated(angle).drawAt(600, 500);
		}
	}
}
```


## 41.9 アイコンのシャドウ
41.8 を応用して、シャドウ付きのアイコンテクスチャクラスを作成します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/render-texture/9.png)

```cpp
# include <Siv3D.hpp>

class IconWithShadow
{
public:

	IconWithShadow() = default;

	explicit IconWithShadow(const Texture& texture)
		: m_texture{ texture }
		, m_shadowTexture{ m_texture.size()/2, ColorF{ 1.0, 0.0 } }
	{
		RenderTexture m_internalTexture{ m_texture.size() / 2 };

		// 影用テクスチャを用意する
		{
			const ScopedRenderTarget2D target{ m_shadowTexture };

			// RGB 値は無視して、描画された最大のアルファ値を保持するブレンドステートを適用する
			const ScopedRenderStates2D blend{ BlendState::MaxAlpha };

			// ぼかしのはみ出しを防ぐため、縮小して描画する
			m_texture.scaled(0.3).drawAt(m_shadowTexture.size() * 0.5);
		}

		// ガウスぼかしを行う
		Shader::GaussianBlur(m_shadowTexture, m_internalTexture, m_shadowTexture);
	}

	// アイコンを描画する
	void drawIconAt(const Vec2& center, const ColorF& color = ColorF{ 1.0 }) const
	{
		m_texture.drawAt(center, color);
	}

	// 影を描画する
	void drawShadowAt(const Vec2& center, const ColorF& shadowColor = ColorF{ 0.0, 0.5 }) const
	{
		// 縮小分より少し大きめに描画する
		m_shadowTexture.scaled(3.6).drawAt(center, shadowColor);
	}

	// 影とアイコンを描画する
	void drawWithShadowAt(const Vec2& center, const ColorF& color = ColorF{ 1.0 }, const ColorF& shadowColor = ColorF{ 0.0, 0.5 }) const
	{
		drawShadowAt(center, shadowColor);
		drawIconAt(center, color);
	}

private:

	Texture m_texture;

	RenderTexture m_shadowTexture;
};


void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Array<IconWithShadow> icons =
	{
		IconWithShadow{ Texture{ 0xF064C_icon, 80 } },
		IconWithShadow{ Texture{ 0xF0493_icon, 80 } },
		IconWithShadow{ Texture{ 0xF100D_icon, 80 } },
		IconWithShadow{ Texture{ 0xF06ED_icon, 80 } },
		IconWithShadow{ Texture{ 0xF01F0_icon, 80 } },
		IconWithShadow{ Texture{ 0xF034E_icon, 80 } },
		IconWithShadow{ Texture{ 0xF1C6A_icon, 80 } },
	};

	bool showShadow = true;
	bool showIcon = true;

	while (System::Update())
	{
		if (showShadow)
		{
			for (size_t i = 0; i < icons.size(); ++i)
			{
				icons[i].drawShadowAt(Vec2{ (100 + i * 100), 200 });
			}

			for (size_t i = 0; i < icons.size(); ++i)
			{
				icons[i].drawShadowAt(Vec2{ (100 + i * 100), 340 });
			}

			for (size_t i = 0; i < icons.size(); ++i)
			{
				icons[i].drawShadowAt(Vec2{ (100 + i * 100), 480 }, HSV{ (i * 25.0), 0.3, 1.0 });
			}
		}

		if (showIcon)
		{
			for (size_t i = 0; i < icons.size(); ++i)
			{
				icons[i].drawIconAt(Vec2{ (100 + i * 100), 200 });
			}

			for (size_t i = 0; i < icons.size(); ++i)
			{
				icons[i].drawIconAt(Vec2{ (100 + i * 100), 340 }, HSV{ (i * 25.0), 0.3, 1.0 });
			}

			for (size_t i = 0; i < icons.size(); ++i)
			{
				icons[i].drawIconAt(Vec2{ (100 + i * 100), 480 }, HSV{ (i * 25.0), 0.3, 1.0 });
			}
		}

		SimpleGUI::CheckBox(showShadow, U"show shadow", Vec2{ 560, 40 }, 200);
		SimpleGUI::CheckBox(showIcon, U"show icon", Vec2{ 560, 80 }, 200);
	}
}
```


## 41.10 2D ライトブルーム
ガウスぼかしの結果を加算ブレンドで描画することで、ライトブルームの表現を実現できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial3/render-texture/10.png)

```cpp
# include <Siv3D.hpp>

void DrawScene(const Texture& emoji)
{
	Circle{ 680, 40, 20 }.draw();
	Rect{ Arg::center(680, 110), 30 }.draw();
	Triangle{ 680, 180, 40 }.draw();

	Circle{ 740, 40, 20 }.draw(HSV{ 0 });
	Rect{ Arg::center(740, 110), 30 }.draw(HSV{ 120 });
	Triangle{ 740, 180, 40 }.draw(HSV{ 240 });

	Circle{ 50, 200, 300 }.drawFrame(4);
	Circle{ 550, 450, 200 }.drawFrame(4);

	for (auto i : step(12))
	{
		const double angle = (i * 30_deg + Scene::Time() * 5_deg);
		const Vec2 pos = OffsetCircular{ Scene::Center(), 200, angle };
		Circle{ pos, 8 }.draw(HSV{ i * 30 });
	}

	emoji.drawAt(400, 300);
}

void Main()
{
	const Size sceneSize{ 800, 600 };
	const Texture emoji{ U"🐈"_emoji };

	// ブルーム用のテクスチャ
	const RenderTexture blur1{ sceneSize };
	const RenderTexture internal1{ sceneSize };
	const RenderTexture blur4{ sceneSize / 4 };
	const RenderTexture internal4{ sceneSize / 4 };
	const RenderTexture blur8{ sceneSize / 8 };
	const RenderTexture internal8{ sceneSize / 8 };

	// 3 種類のぼかしテクスチャの寄与度
	double a1 = 0.0, a4 = 0.0, a8 = 0.0;

	while (System::Update())
	{
		// 通常のシーン描画
		{
			DrawScene(emoji);
		}

		// ブルーム用テクスチャを用意する
		{
			// シーンを描く
			{
				// ブルーム用テクスチャをレンダーターゲットにする
				const ScopedRenderTarget2D target{ blur1.clear(ColorF{ 0.0 }) };

				// シーンを描く
				DrawScene(emoji);
			} // blur1 のレンダーターゲットが解除される

			// (1) blur1: 1x blur
			Shader::GaussianBlur(blur1, internal1, blur1);

			// (2) blur4: 1x blur + 1/4 scale + 1x blur 
			Shader::Downsample(blur1, blur4);
			Shader::GaussianBlur(blur4, internal4, blur4);

			// (3) blur8: 1x blur + 1/4 scale + 1x blur + 1/2 scale + 1x blur
			Shader::Downsample(blur4, blur8);
			Shader::GaussianBlur(blur8, internal8, blur8);
		}

		{
			const ScopedRenderStates2D blend{ BlendState::Additive };

			if (a1)
			{
				blur1.resized(sceneSize).draw(ColorF{ a1 });
			}

			if (a4)
			{
				blur4.resized(sceneSize).draw(ColorF{ a4 });
			}

			if (a8)
			{
				blur8.resized(sceneSize).draw(ColorF{ a8 });
			}
		}

		SimpleGUI::Slider(U"a1: {:.1f}"_fmt(a1), a1, 0.0, 4.0, Vec2{ 40, 40 });
		SimpleGUI::Slider(U"a4: {:.1f}"_fmt(a4), a4, 0.0, 4.0, Vec2{ 40, 80 });
		SimpleGUI::Slider(U"a8: {:.1f}"_fmt(a8), a8, 0.0, 4.0, Vec2{ 40, 120 });

		if (SimpleGUI::Button(U"0, 0, 0", Vec2{ 40, 160 }))
		{
			a1 = a4 = a8 = 0.0;
		}

		if (SimpleGUI::Button(U"0, 0, 1", Vec2{ 40, 200 }))
		{
			a1 = a4 = 0.0;
			a8 = 1.0;
		}

		if (SimpleGUI::Button(U"0, 1, 1", Vec2{ 40, 240 }))
		{
			a1 = 0.0;
			a8 = a4 = 1.0;
		}

		if (SimpleGUI::Button(U"1, 1, 1", Vec2{ 40, 280 }))
		{
			a1 = a4 = a8 = 1.0;
		}
	}
}
```

