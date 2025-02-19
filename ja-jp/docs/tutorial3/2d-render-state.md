# 48. 2D レンダーステート
2D 描画の設定（レンダーステート）をカスタマイズして、さまざまな画面効果を得る方法を学びます。

## 48.1 Scoped～ のはたらき
- この章では `Scoped～` 型のオブジェクトが登場します
- ソースコード上では何も働いていないように見えますが、実際はコンストラクタの中で、渡されたレンダーステートをエンジンに設定し、自身が破棄されるとき（スコープが終了するとき）に、デストラクタで設定を以前の状態に戻す処理を行います
- 次のサンプルコードでは、1 つ目の円はワイヤフレーム表示モードで描画され、2 つ目の円は通常の描画モードで描画されます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/2d-render-state/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		{
			// レンダーステートを変更する（以前の状態も保存）
			const ScopedRenderStates2D rasterizer{ RasterizerState::WireframeCullNone };

			Circle{ 200, 300, 150 }.draw(ColorF{ 0.1 });

		} // ここで rasterizer のデストラクタが呼び出され、レンダーステートを以前の状態に戻す

		Circle{ 600, 300, 150 }.draw(ColorF{ 0.1 });
	}
}
```


## 48.2 加算ブレンドで描く
- `ScopedRenderStates2D` オブジェクトのコンストラクタに `BlendState::Additive` を渡すと、そのオブジェクトが有効な間、図形や画像が加算ブレンドで描画されます
- 加算ブレンドでは、背景色に RGB 成分を加算するように描画されるため、重ねて描画した部分が明るくなります
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/2d-render-state/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Array<Vec2> points;

	for (int32 i = 0; i < 400; ++i)
	{
		points << RandomVec2(Scene::Rect());
	}

	// 加算ブレンドを有効にするか
	bool enabled = true;

	while (System::Update())
	{
		if (enabled)
		{
			// 加算ブレンド有効
			const ScopedRenderStates2D blend{ BlendState::Additive };

			for (const auto& point : points)
			{
				Circle{ point, 20 }.draw(HSV{ (point.y * 100 + point.x * 100), 0.5 });
			}
		}
		else
		{
			// 通常のブレンドモード

			for (const auto& point : points)
			{
				Circle{ point, 20 }.draw(HSV{ (point.y * 100 + point.x * 100), 0.5 });
			}
		}

		SimpleGUI::CheckBox(enabled, U"AdditiveBlend", Vec2{ 40, 40 });
	}
}
```


## 48.3 描画色を加算する
- 画像や図形を描くときに、本来の色に RGBA 成分を加算して描画するには、`ScopedColorAdd2D` オブジェクトのコンストラクタに、加算したい値を設定します
- そのオブジェクトが有効な間、描画に対して RGBA 値が加算されます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/2d-render-state/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture textureWindmill{ U"example/windmill.png" };
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };
	ColorF color{ 0.0, 0.0, 0.0, 0.0 };

	while (System::Update())
	{
		{
			// 描画時に色を加算するように設定する
			const ScopedColorAdd2D colorAdd{ color };

			textureWindmill.draw(40, 40);
			textureSiv3DKun.draw(400, 100);
		}

		SimpleGUI::Slider(U"R", color.r, Vec2{ 620, 40 }, 40);
		SimpleGUI::Slider(U"G", color.g, Vec2{ 620, 80 }, 40);
		SimpleGUI::Slider(U"B", color.b, Vec2{ 620, 120 }, 40);
	}
}
```


## 48.4 描画色を乗算する
- 画像や図形を描くときに、本来の色に RGBA 成分を乗算して描画するには、`ScopedColorMul2D` オブジェクトのコンストラクタに、乗算したい値を設定します
- そのオブジェクトが有効な間、描画の RGBA 値が乗算されます
- `Texture` の `.draw()` に色を渡すことで乗算の色を設定することもできます（**チュートリアル 31.12**）。`ScopedColorMul2D` はその設定を一括して行えるものです
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/2d-render-state/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture textureWindmill{ U"example/windmill.png" };
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };
	ColorF color{ 1.0, 1.0, 1.0, 1.0 };

	while (System::Update())
	{
		{
			// 描画時に色を乗算するように設定する
			const ScopedColorMul2D colorMul{ color };

			textureWindmill.draw(40, 40);
			textureSiv3DKun.draw(400, 100);
		}

		SimpleGUI::Slider(U"R", color.r, Vec2{ 620, 40 }, 40);
		SimpleGUI::Slider(U"G", color.g, Vec2{ 620, 80 }, 40);
		SimpleGUI::Slider(U"B", color.b, Vec2{ 620, 120 }, 40);
		SimpleGUI::Slider(U"A", color.a, Vec2{ 620, 160 }, 40);
	}
}
```


## 48.5 テクスチャを黒塗り・白塗りする
- `ScopedColorAdd2D` や `ScopedColorMul2D` を応用して、テクスチャを黒塗り・白塗りできます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/2d-render-state/5.png)

```cpp

```


## 48.6 テクスチャ拡大縮小時のフィルタ
- テクスチャを拡大縮小して描画する際の補間方法は次の 2 種類があります

| 設定名 | 説明 |
|---|---|
| Linear | バイリニア補間（デフォルト） |
| Nearest | 最近傍補間 |

- デフォルトではバイリニア補間によって色が補間されます
- ドット感を維持したままテクスチャを拡大描画したいときには、サンプラーステート `SamplerState::ClampNearest` を `ScopedRenderStates2D` に設定します
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/2d-render-state/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture texture{ U"🐈"_emoji };
	bool bilinear = true;
	double scale = 1.0;

	while (System::Update())
	{
		if (bilinear)
		{
			// バイリニア補間（デフォルト）
			texture.scaled(scale).drawAt(Scene::Center());
		}
		else
		{
			// 最近傍補間
			const ScopedRenderStates2D sampler{ SamplerState::ClampNearest };
			texture.scaled(scale).drawAt(Scene::Center());
		}

		SimpleGUI::Slider(scale, 0.5, 12.0, Vec2{ 40, 40 }, 200);
		SimpleGUI::CheckBox(bilinear, U"Bilinear", Vec2{ 40, 80 });
	}
}
```


## 48.7 テクスチャのくり返し
- テクスチャ描画時に UV 座標が 0.0～1.0 の範囲を超えたときの処理の方法をカスタマイズできます

| 設定名 | 説明 |
|---|---|
| Clamp | テクスチャの端の色をそのまま描画する |
| Repeat | テクスチャの端の色が反対側の端から続くように描画する |
| Mirror | テクスチャの端の色が反対側の端から反転して続くように描画する |

- デフォルトは Clamp で、テクスチャフィルタリングのデフォルト値 Linear と組み合わせた `SamplerState::ClampLinear` が適用されています
- テクスチャの繰り返し描画については **チュートリアル 31.18** も参照してください
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/2d-render-state/7.png)

```cpp

```


## 48.8 ワイヤフレーム表示
- 描画時に、図形や画像を構成する三角形のワイヤフレームのみを描画するモードがあります

| 設定名 | 説明 |
|---|---|
| Wireframe| ワイヤフレーム表示 |
| Solid | 通常の描画モード |

- `ScopedRenderStates2D` オブジェクトのコンストラクタに `RasterizerState::WireframeCullNone` を渡すと、そのオブジェクトが有効な間、ワイヤフレーム表示モードになります
- ワイヤフレームモードは Web 版では利用できません

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/2d-render-state/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	const Texture textureWindmill{ U"example/windmill.png" };

	while (System::Update())
	{
		{
			// ワイヤフレーム表示モードに設定する
			const ScopedRenderStates2D rasterizer{ RasterizerState::WireframeCullNone };

			textureWindmill.draw(40, 40);

			Circle{ Scene::Center(), 100 }.draw();

			Shape2D::Star(100, Vec2{ 160, 400 }).draw(Palette::Yellow);
		}
	}
}
```



## 48.9 シザー矩形
- シザー矩形を設定すると、長方形領域以外への描画を行わないようにできます
- `Graphics2D::SetScissorRect()` でシザー矩形の領域を登録し、`.scissorEnable` を `true` にした `RasterizerState` を `ScopedRenderStates2D` で適用することで、シザー矩形が有効になります
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/2d-render-state/9.png)

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture textureWindmill{ U"example/windmill.png" };
	const Texture textureSiv3DKun{ U"example/siv3d-kun.png" };

	// シザー矩形の範囲を登録する
	Graphics2D::SetScissorRect(Rect{ 100, 100, 300, 200 });

	while (System::Update())
	{
		{
			RasterizerState rs = RasterizerState::Default2D;
			rs.scissorEnable = true;

			// シザー矩形を有効化する
			const ScopedRenderStates2D rasterizer{ rs };

			textureWindmill.draw(40, 40);
			textureSiv3DKun.draw(160, 100);
		}
	}
}
```


## 48.10 ビューポート
- `ScopedViewport2D` オブジェクトを作成すると、シーン内に仮想のシーンを作り、新しい描画領域を定義できます
- 描画時にはビューポートの長方形の左上が (0, 0) の描画座標になり、長方形の範囲外にはみ出たものは描画されなくなります
- ビューポートは描画の座標にしか影響を及ぼしません。マウスカーソルの座標も同様に移動させたい場合には、**チュートリアル 49** で学ぶ `Transformer2D` と組み合わせます
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/2d-render-state/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture cat{ U"🐈"_emoji };

	while (System::Update())
	{
		{
			// ビューポートを適用する
			const ScopedViewport2D viewport{ 400, 300, 400, 300 };

			Circle{ 200, 150, 200 }.draw();

			cat.draw(0, 0);
		}
	}
}
```

