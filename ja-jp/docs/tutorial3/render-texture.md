# 41. レンダーテクスチャ
図形やテクスチャ、フォントの描画先をシーンではなくテクスチャにする方法を学びます。

!!! info
    準備中です。以前の記事を下記から読むことができます。  
    [Siv3D リファレンス | レンダーテクスチャ :material-open-in-new:](https://zenn.dev/reputeless/books/siv3d-documentation/viewer/tutorial-rendertexture){:target="_blank"}


## 41.1 xxxxxxxxxxxxx


## 41.2 xxxxxxxxxxxxxx


## 41.3 xxxxxxxxxxxxx


## 41.4 xxxxxxxxxxxxxx


## 41.5 xxxxxxxxxxxxx


## 41.6 xxxxxxxxxxxxxx


## 41.7 xxxxxxxxxxxxx


## 41.8 xxxxxxxxxxxxxx


## 41.9 xxxxxxxxxxxxx


## 41.10 xxxxxxxxxxxxxx


## 41.11 xxxxxxxxxxxxxx


## 41.12 xxxxxxxxxxxxxx


## 41.13 xxxxxxxxxxxxxx


## 41.14 xxxxxxxxxxxxxx

<!--
```cpp
# include <Siv3D.hpp>

BlendState GetCustomBlend()
{
	BlendState bs = BlendState::Default2D;
	bs.srcAlpha = Blend::SrcAlpha;
	bs.dstAlpha = Blend::DestAlpha;
	bs.opAlpha = BlendOp::Max;
	return bs;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	const Font font{ 48, Typeface::Heavy };
	const Texture texture{ U"example/siv3d-kun.png" };
	MSRenderTexture renderTexture{ 600, 600 };

	while (System::Update())
	{
		{
			renderTexture.clear(ColorF{ 0.8, 0.9, 1.0, 0.0 });
			const ScopedRenderStates2D blend{ GetCustomBlend() };
			const ScopedRenderTarget2D target{ renderTexture };

			Circle{ 300, 200, 100 }.draw(ColorF{ 0.1, 0.3, 0.5, 0.4 });
			texture.draw();
			font(U"Hello, Siv3D!").draw(40, 120, ColorF{ 0.1, 0.3, 0.5 });
		}

		{
			Graphics2D::Flush();
			renderTexture.resolve();

			for (int32 x : Range(-2, 10))
			{
				Rect{ x * 100, 0, 20, 600 }.shearedX(200).draw();
			}

			renderTexture.draw();
		}
	}
}
```
-->

