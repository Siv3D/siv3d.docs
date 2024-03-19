# 奥行き型 UI の作成

## 1. QuadWarp
- レンダーテクスチャに描画した UI を QuadWarp を使って描画することで、3D の奥行き感のある UI を実現できます。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/study/advanced/3/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1000, 600);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const ColorF PrimaryColor{ 0.98, 0.96, 0.94 };

	const Rect BaseRect{ 0, 0, 600, 600 };
	const Rect Button1{ 40, 40, 560, 200 };
	const Rect Button2{ 100, 260, 240, 100 };
	const Rect Button3{ 360, 260, 240, 100 };
	const Rect Button4{ 160, 380, 440, 140 };

	// UI の描画先のレンダーテクスチャ
	MSRenderTexture renderTexture{ BaseRect.size };

	Quad quad{ Vec2{ 40, 40 }, Vec2{ 560, 40 }, Vec2{ 560, 560 }, Vec2{ 40, 560 } };
	Optional<size_t> grabbedIndex;

	while (System::Update())
	{
		// レンダーテクスチャに UI を描く
		{
			// renderTexture を ColorF{ 1.0, 0.0 } でクリアし,
			// renderTexture をレンダーターゲットにする
			const ScopedRenderTarget2D renderTarget{ renderTexture.clear(ColorF{ 1.0, 0.0 }) };

			// renderTexture のアルファ値がすべて 0 なので、最大のアルファ値を書き込むようなブレンドステートを適用する
			BlendState blend = BlendState::Default2D;
			blend.opAlpha = BlendOp::Max;
			blend.dstAlpha = Blend::DestAlpha;
			blend.srcAlpha = Blend::SrcAlpha;
			const ScopedRenderStates2D renderState{ blend };

			// UI を描画する
			{
				Button1.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
				Button1.draw(PrimaryColor);
				font(U"探索").draw(88, Arg::leftCenter(80, 140), ColorF{ 0.4, 0.3, 0.2 });

				Button2.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
				Button2.draw(PrimaryColor);
				font(U"任務").draw(44, Arg::leftCenter(120, 310), ColorF{ 0.4, 0.3, 0.2 });

				Button3.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
				Button3.draw(PrimaryColor);
				font(U"編成").draw(44, Arg::leftCenter(380, 310), ColorF{ 0.4, 0.3, 0.2 });

				Button4.draw(ColorF{ 0.2, 0.4, 0.6 });
				font(U"イベント").draw(33, Arg::leftCenter(180, 415));

				Rect{ 60, 540, 540, 60 }.draw(ColorF{ 0.0, 0.6 });
			}

			// MSRenderTexture の完成には
			// 2D 描画命令の発行 (Flush) + MSAA の解決 (Resolve) が必要
			Graphics2D::Flush();
			renderTexture.resolve();
		}

		// 奥行き型の UI を描く
		{
			const ScopedRenderStates2D sampler{ SamplerState::ClampAniso };
			Shader::QuadWarp(quad, renderTexture);
		}

		// 4 つの頂点の操作と表示
		{
			if (grabbedIndex)
			{
				if (not MouseL.pressed())
				{
					grabbedIndex.reset();
				}
				else
				{
					quad.p(*grabbedIndex).moveBy(Cursor::DeltaF());
				}
			}
			else
			{
				for (int32 i = 0; i < 4; ++i)
				{
					const Circle circle = quad.p(i).asCircle(10);

					if (circle.mouseOver())
					{
						Cursor::RequestStyle(CursorStyle::Hand);
					}

					if (circle.leftClicked())
					{
						grabbedIndex = i;
						break;
					}
				}
			}

			for (int32 i = 0; i < 4; ++i)
			{
				quad.p(i).asCircle(10).draw(ColorF{ 1.0, 0.3, 0.3, 0.5 });
			}
		}
	}
}
```

## 2. 無関係なコードを除去
- 1. のコードから、4 頂点の操作に関連するコードを除去しました

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/study/advanced/3/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1000, 600);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const ColorF PrimaryColor{ 0.98, 0.96, 0.94 };

	const Rect BaseRect{ 0, 0, 600, 600 };
	const Rect Button1{ 40, 40, 560, 200 };
	const Rect Button2{ 100, 260, 240, 100 };
	const Rect Button3{ 360, 260, 240, 100 };
	const Rect Button4{ 160, 380, 440, 140 };

	// UI の描画先のレンダーテクスチャ
	MSRenderTexture renderTexture{ BaseRect.size };

	// 変換後の四角形
	const Quad TargetQuad{ 500, 60, 1000, 0, 1000, 600, 480, 520 };

	while (System::Update())
	{
		// レンダーテクスチャに UI を描く
		{
			// renderTexture を ColorF{ 1.0, 0.0 } でクリアし,
			// renderTexture をレンダーターゲットにする
			const ScopedRenderTarget2D renderTarget{ renderTexture.clear(ColorF{ 1.0, 0.0 }) };

			// renderTexture のアルファ値がすべて 0 なので、最大のアルファ値を書き込むようなブレンドステートを適用する
			BlendState blend = BlendState::Default2D;
			blend.opAlpha = BlendOp::Max;
			blend.dstAlpha = Blend::DestAlpha;
			blend.srcAlpha = Blend::SrcAlpha;
			const ScopedRenderStates2D renderState{ blend };

			// UI を描画する
			{
				Button1.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
				Button1.draw(PrimaryColor);
				font(U"探索").draw(88, Arg::leftCenter(80, 140), ColorF{ 0.4, 0.3, 0.2 });

				Button2.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
				Button2.draw(PrimaryColor);
				font(U"任務").draw(44, Arg::leftCenter(120, 310), ColorF{ 0.4, 0.3, 0.2 });

				Button3.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
				Button3.draw(PrimaryColor);
				font(U"編成").draw(44, Arg::leftCenter(380, 310), ColorF{ 0.4, 0.3, 0.2 });

				Button4.draw(ColorF{ 0.2, 0.4, 0.6 });
				font(U"イベント").draw(33, Arg::leftCenter(180, 415));

				Rect{ 60, 540, 540, 60 }.draw(ColorF{ 0.0, 0.6 });
			}

			// MSRenderTexture の完成には
			// 2D 描画命令の発行 (Flush) + MSAA の解決 (Resolve) が必要
			Graphics2D::Flush();
			renderTexture.resolve();
		}

		// 奥行き型の UI を描く
		{
			const ScopedRenderStates2D sampler{ SamplerState::ClampAniso };
			Shader::QuadWarp(TargetQuad, renderTexture);
		}
	}
}
```

## 3. 変形後のボタンのあたり判定
- 各ボタンの長方形を、変換後の四角形に変換して、マウスがボタンの上にあるかどうかを判定します。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/study/advanced/3/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1000, 600);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const ColorF PrimaryColor{ 0.98, 0.96, 0.94 };
	const ColorF HoverColor{ 1.0, 0.96, 0.8 };

	const Rect BaseRect{ 0, 0, 600, 600 };
	const Rect Button1{ 40, 40, 560, 200 };
	const Rect Button2{ 100, 260, 240, 100 };
	const Rect Button3{ 360, 260, 240, 100 };
	const Rect Button4{ 160, 380, 440, 140 };

	// UI の描画先のレンダーテクスチャ
	MSRenderTexture renderTexture{ BaseRect.size };

	// 変換後の四角形
	const Quad TargetQuad{ 500, 60, 1000, 0, 1000, 600, 480, 520 };

	// QuadWarp 変換の行列を得る
	const Mat3x3 projection = Mat3x3::Homography(Rect{ 600 }.asQuad(), TargetQuad);

	// 射影後の各ボタンの四角形
	const Quad Button1Quad = projection.transformRect(Button1);
	const Quad Button2Quad = projection.transformRect(Button2);
	const Quad Button3Quad = projection.transformRect(Button3);
	const Quad Button4Quad = projection.transformRect(Button4);

	while (System::Update())
	{
		// レンダーテクスチャに UI を描く
		{
			// renderTexture を ColorF{ 1.0, 0.0 } でクリアし,
			// renderTexture をレンダーターゲットにする
			const ScopedRenderTarget2D renderTarget{ renderTexture.clear(ColorF{ 1.0, 0.0 }) };

			// renderTexture のアルファ値がすべて 0 なので、最大のアルファ値を書き込むようなブレンドステートを適用する
			BlendState blend = BlendState::Default2D;
			blend.opAlpha = BlendOp::Max;
			blend.dstAlpha = Blend::DestAlpha;
			blend.srcAlpha = Blend::SrcAlpha;
			const ScopedRenderStates2D renderState{ blend };

			// UI を描画する
			{
				Button1.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
				Button1.draw(Button1Quad.mouseOver() ? HoverColor : PrimaryColor);
				font(U"探索").draw(88, Arg::leftCenter(80, 140), ColorF{ 0.4, 0.3, 0.2 });
				if (Button1Quad.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);
				}

				Button2.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
				Button2.draw(Button2Quad.mouseOver() ? HoverColor : PrimaryColor);
				font(U"任務").draw(44, Arg::leftCenter(120, 310), ColorF{ 0.4, 0.3, 0.2 });
				if (Button2Quad.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);
				}

				Button3.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
				Button3.draw(Button3Quad.mouseOver() ? HoverColor : PrimaryColor);
				font(U"編成").draw(44, Arg::leftCenter(380, 310), ColorF{ 0.4, 0.3, 0.2 });
				if (Button3Quad.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);
				}

				Button4.draw(ColorF{ 0.2, 0.4, 0.6 });
				font(U"イベント").draw(33, Arg::leftCenter(180, 415));
				if (Button4Quad.mouseOver())
				{
					Cursor::RequestStyle(CursorStyle::Hand);
				}

				Rect{ 60, 540, 540, 60 }.draw(ColorF{ 0.0, 0.6 });
			}

			// MSRenderTexture の完成には
			// 2D 描画命令の発行 (Flush) + MSAA の解決 (Resolve) が必要
			Graphics2D::Flush();
			renderTexture.resolve();
		}

		// 奥行き型の UI を描く
		{
			const ScopedRenderStates2D sampler{ SamplerState::ClampAniso };
			Shader::QuadWarp(TargetQuad, renderTexture);
		}
	}
}
```

## 4. 完成
- UI をにぎやかにします。

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/study/advanced/3/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1000, 600);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };
	const Texture compassIcon{ 0xF018B_icon, 90 };
	const Texture swordIcon{ 0xF18BE_icon, 90 };
	const Texture plusIcon{ 0xF0417_icon, 42 };
	const Texture moneyEmoji{ U"💰"_emoji };
	const Texture gemEmoji{ U"💎"_emoji };
	const ColorF PrimaryColor{ 0.98, 0.96, 0.94 };
	const ColorF HoverColor{ 1.0, 0.96, 0.8 };

	const Rect BaseRect{ 0, 0, 600, 600 };
	const Rect Button1{ 40, 40, 560, 200 };
	const Rect Button2{ 100, 260, 240, 100 };
	const Rect Button3{ 360, 260, 240, 100 };
	const Rect Button4{ 160, 380, 440, 140 };
	const Rect Button5{ Arg::center(230, 570), 40 };

	// UI の描画先のレンダーテクスチャ
	MSRenderTexture renderTexture{ BaseRect.size };

	// 変換後の四角形
	const Quad TargetQuad{ 500, 60, 1000, 0, 1000, 600, 480, 520 };

	// QuadWarp 変換の行列を得る
	const Mat3x3 projection = Mat3x3::Homography(Rect{ 600 }.asQuad(), TargetQuad);

	// 射影後の各ボタンの四角形
	const Quad Button1Quad = projection.transformRect(Button1);
	const Quad Button2Quad = projection.transformRect(Button2);
	const Quad Button3Quad = projection.transformRect(Button3);
	const Quad Button4Quad = projection.transformRect(Button4);
	const Quad Button5Quad = projection.transformRect(Button5);

	while (System::Update())
	{
		// レンダーテクスチャに UI を描く
		{
			// renderTexture を ColorF{ 1.0, 0.0 } でクリアし,
			// renderTexture をレンダーターゲットにする
			const ScopedRenderTarget2D renderTarget{ renderTexture.clear(ColorF{ 1.0, 0.0 }) };

			// renderTexture のアルファ値がすべて 0 なので、最大のアルファ値を書き込むようなブレンドステートを適用する
			BlendState blend = BlendState::Default2D;
			blend.opAlpha = BlendOp::Max;
			blend.dstAlpha = Blend::DestAlpha;
			blend.srcAlpha = Blend::SrcAlpha;
			const ScopedRenderStates2D renderState{ blend };

			// UI を描画する
			{
				// 探索
				{
					Button1.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button1.draw(Button1Quad.mouseOver() ? HoverColor : PrimaryColor);
					font(U"探索").draw(88, Arg::leftCenter(80, 140), ColorF{ 0.4, 0.3, 0.2 });
					if (Button1Quad.mouseOver())
					{
						Cursor::RequestStyle(CursorStyle::Hand);
					}
				}

				// 任務
				{
					Button2.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button2.draw(Button2Quad.mouseOver() ? HoverColor : PrimaryColor);
					font(U"任務").draw(44, Arg::leftCenter(120, 310), ColorF{ 0.4, 0.3, 0.2 });
					compassIcon.drawAt(280, 310, ColorF{ 0.8 });
					if (Button2Quad.mouseOver())
					{
						Cursor::RequestStyle(CursorStyle::Hand);
					}
				}

				// 編成
				{
					Button3.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button3.draw(Button3Quad.mouseOver() ? HoverColor : PrimaryColor);
					font(U"編成").draw(44, Arg::leftCenter(380, 310), ColorF{ 0.4, 0.3, 0.2 });
					swordIcon.drawAt(540, 310, ColorF{ 0.8 });
					if (Button3Quad.mouseOver())
					{
						Cursor::RequestStyle(CursorStyle::Hand);
					}
				}

				// イベント
				{
					Button4.draw(ColorF{ 0.2, 0.4, 0.6 });
					font(U"イベント").draw(33, Arg::leftCenter(180, 415));
					if (Button4Quad.mouseOver())
					{
						Cursor::RequestStyle(CursorStyle::Hand);
					}
				}

				// ジェムとお金
				{
					Rect{ 60, 540, 540, 60 }.draw(ColorF{ 0.0, 0.6 });
					gemEmoji.scaled(0.36).drawAt(120, 570);
					font(U"67").draw(TextStyle::Outline(0.0, 0.2, ColorF{ 0.1 }), 36, Arg::leftCenter(150, 570));

					Circle{ Button5.center(), 20 }.draw(ColorF{ 0.2, 0.8 });
					plusIcon.drawAt(Button5.center(), Button5Quad.mouseOver() ? HoverColor : PrimaryColor);
					if (Button5Quad.mouseOver())
					{
						Cursor::RequestStyle(CursorStyle::Hand);
					}

					moneyEmoji.scaled(0.36).drawAt(300, 570);
					font(ThousandsSeparate(12345)).draw(TextStyle::Outline(0.0, 0.2, ColorF{ 0.1 }), 36, Arg::leftCenter(330, 570));
				}
			}

			// MSRenderTexture の完成には
			// 2D 描画命令の発行 (Flush) + MSAA の解決 (Resolve) が必要
			Graphics2D::Flush();
			renderTexture.resolve();
		}

		// 奥行き型の UI を描く
		{
			// 右端に向かって影の効果
			Rect{ 460, 0, 540, 600 }.draw(Arg::left = ColorF{ 0.0, 0.0 }, Arg::right = ColorF{ 0.0, 0.2 });

			const ScopedRenderStates2D sampler{ SamplerState::ClampAniso };
			Shader::QuadWarp(TargetQuad, renderTexture);
		}
	}
}
```
