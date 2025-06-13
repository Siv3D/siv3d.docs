# Creating a Perspective UI with QuadWarp

| | | | |
|:--:|:--:|:--:|:--:|
| **Difficulty** | Intermediate | **Time** | 60 minutes~ |

## 1. Draw UI to a render texture
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/quadwarp/1.png)

- To use QuadWarp, first draw the UI to a render texture.

??? note "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// Change window size to 1000x600
		Window::Resize(1000, 600);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font for UI
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Theme color
		const ColorF PrimaryColor{ 0.98, 0.96, 0.94 };

		// Rectangle for each UI element
		const Rect BaseRect{ 0, 0, 600, 600 };
		const Rect Button1{ 40, 40, 560, 200 };
		const Rect Button2{ 100, 260, 240, 100 };
		const Rect Button3{ 360, 260, 240, 100 };
		const Rect Button4{ 160, 380, 440, 120 };

		// Render texture for drawing UI
		const MSRenderTexture renderTexture{ BaseRect.size };

		while (System::Update())
		{
			// Draw UI to render texture
			{
				// Clear renderTexture with ColorF{ 0.5 } and
				// set renderTexture as render target
				const ScopedRenderTarget2D renderTarget{ renderTexture.clear(ColorF{ 0.5 }) };

				// Draw UI
				{
					// Explore button
					Button1.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button1.draw(PrimaryColor);
					font(U"Explore").draw(88, Arg::leftCenter(80, 140), ColorF{ 0.4, 0.3, 0.2 });

					// Mission button
					Button2.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button2.draw(PrimaryColor);
					font(U"Mission").draw(44, Arg::leftCenter(120, 310), ColorF{ 0.4, 0.3, 0.2 });

					// Formation button
					Button3.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button3.draw(PrimaryColor);
					font(U"Formation").draw(44, Arg::leftCenter(380, 310), ColorF{ 0.4, 0.3, 0.2 });

					// Event area
					Button4.draw(ColorF{ 0.2, 0.4, 0.6 });
					font(U"Event").draw(33, Arg::leftCenter(180, 415));

					// Footer
					Rect{ 60, 540, 540, 60 }.draw(ColorF{ 0.0, 0.6 });
				}

				// Complete the render texture contents by issuing 2D draw commands (Flush)
				// and resolving MSAA (Resolve)
				Graphics2D::Flush();
				renderTexture.resolve();
			}

			// Draw render texture to screen
			renderTexture.scaled(0.75).draw(Vec2{ 300, 40 });
		}
	}
	```


## 2. Draw to a transparent render texture
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/quadwarp/2.png)

- When you want to make areas other than the UI transparent, draw the UI to a render texture cleared with a transparent color like `ColorF{ 1.0, 0.0 }`.
- However, since all alpha values in the render texture are 0 as is, apply a blend state that writes the maximum alpha value.

??? note "Code"
	```cpp hl_lines="3-11 41 43 45-46"
	# include <Siv3D.hpp>

	/// @brief Returns a blend state that writes the maximum alpha value.
	BlendState MaxAlphaBlend()
	{
		BlendState blend = BlendState::Default2D;
		blend.opAlpha = BlendOp::Max;
		blend.dstAlpha = Blend::DestAlpha;
		blend.srcAlpha = Blend::SrcAlpha;
		return blend;
	}

	void Main()
	{
		// Change window size to 1000x600
		Window::Resize(1000, 600);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font for UI
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Theme color
		const ColorF PrimaryColor{ 0.98, 0.96, 0.94 };

		// Rectangle for each UI element
		const Rect BaseRect{ 0, 0, 600, 600 };
		const Rect Button1{ 40, 40, 560, 200 };
		const Rect Button2{ 100, 260, 240, 100 };
		const Rect Button3{ 360, 260, 240, 100 };
		const Rect Button4{ 160, 380, 440, 120 };

		// Render texture for drawing UI
		const MSRenderTexture renderTexture{ BaseRect.size };

		while (System::Update())
		{
			// Draw UI to render texture
			{
				// Clear renderTexture with ColorF{ 1.0, 0.0 } and
				// set renderTexture as render target
				const ScopedRenderTarget2D renderTarget{ renderTexture.clear(ColorF{ 1.0, 0.0 }) };

				// Apply blend state that writes maximum alpha value
				const ScopedRenderStates2D renderState{ MaxAlphaBlend() };

				// Draw UI
				{
					// Explore button
					Button1.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button1.draw(PrimaryColor);
					font(U"Explore").draw(88, Arg::leftCenter(80, 140), ColorF{ 0.4, 0.3, 0.2 });

					// Mission button
					Button2.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button2.draw(PrimaryColor);
					font(U"Mission").draw(44, Arg::leftCenter(120, 310), ColorF{ 0.4, 0.3, 0.2 });

					// Formation button
					Button3.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button3.draw(PrimaryColor);
					font(U"Formation").draw(44, Arg::leftCenter(380, 310), ColorF{ 0.4, 0.3, 0.2 });

					// Event area
					Button4.draw(ColorF{ 0.2, 0.4, 0.6 });
					font(U"Event").draw(33, Arg::leftCenter(180, 415));

					// Footer
					Rect{ 60, 540, 540, 60 }.draw(ColorF{ 0.0, 0.6 });
				}

				// Complete the render texture contents by issuing 2D draw commands (Flush)
				// and resolving MSAA (Resolve)
				Graphics2D::Flush();
				renderTexture.resolve();
			}

			// Draw render texture to screen
			renderTexture.scaled(0.75).draw(Vec2{ 300, 40 });
		}
	}
	```


## 3. Prepare the target Quad for projection
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/quadwarp/3.png)

- Prepare a Quad that specifies how to project and draw the render texture.

??? note "Code"
	```cpp hl_lines="37-38 85-86"
	# include <Siv3D.hpp>

	/// @brief Returns a blend state that writes the maximum alpha value.
	BlendState MaxAlphaBlend()
	{
		BlendState blend = BlendState::Default2D;
		blend.opAlpha = BlendOp::Max;
		blend.dstAlpha = Blend::DestAlpha;
		blend.srcAlpha = Blend::SrcAlpha;
		return blend;
	}

	void Main()
	{
		// Change window size to 1000x600
		Window::Resize(1000, 600);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font for UI
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Theme color
		const ColorF PrimaryColor{ 0.98, 0.96, 0.94 };

		// Rectangle for each UI element
		const Rect BaseRect{ 0, 0, 600, 600 };
		const Rect Button1{ 40, 40, 560, 200 };
		const Rect Button2{ 100, 260, 240, 100 };
		const Rect Button3{ 360, 260, 240, 100 };
		const Rect Button4{ 160, 380, 440, 120 };

		// Render texture for drawing UI
		const MSRenderTexture renderTexture{ BaseRect.size };

		// Target quadrilateral for projection
		const Quad TargetQuad{ 500, 60, 1000, 0, 1000, 600, 480, 520 };

		while (System::Update())
		{
			// Draw UI to render texture
			{
				// Clear renderTexture with ColorF{ 1.0, 0.0 } and
				// set renderTexture as render target
				const ScopedRenderTarget2D renderTarget{ renderTexture.clear(ColorF{ 1.0, 0.0 }) };

				// Apply blend state that writes maximum alpha value
				const ScopedRenderStates2D renderState{ MaxAlphaBlend() };

				// Draw UI
				{
					// Explore button
					Button1.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button1.draw(PrimaryColor);
					font(U"Explore").draw(88, Arg::leftCenter(80, 140), ColorF{ 0.4, 0.3, 0.2 });

					// Mission button
					Button2.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button2.draw(PrimaryColor);
					font(U"Mission").draw(44, Arg::leftCenter(120, 310), ColorF{ 0.4, 0.3, 0.2 });

					// Formation button
					Button3.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button3.draw(PrimaryColor);
					font(U"Formation").draw(44, Arg::leftCenter(380, 310), ColorF{ 0.4, 0.3, 0.2 });

					// Event area
					Button4.draw(ColorF{ 0.2, 0.4, 0.6 });
					font(U"Event").draw(33, Arg::leftCenter(180, 415));

					// Footer
					Rect{ 60, 540, 540, 60 }.draw(ColorF{ 0.0, 0.6 });
				}

				// Complete the render texture contents by issuing 2D draw commands (Flush)
				// and resolving MSAA (Resolve)
				Graphics2D::Flush();
				renderTexture.resolve();
			}

			// Draw render texture to screen
			renderTexture.scaled(0.75).draw(Vec2{ 300, 40 });

			// Draw the target quadrilateral
			TargetQuad.draw(ColorF{ 1.0, 0.0, 0.0, 0.5 });
		}
	}
	```


## 4. Using QuadWarp
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/quadwarp/4.png)

- Using QuadWarp, you can project and draw textures to areas specified by `Quad`.

??? note "Code"
	```cpp hl_lines="82-85 87-88"
	# include <Siv3D.hpp>

	/// @brief Returns a blend state that writes the maximum alpha value.
	BlendState MaxAlphaBlend()
	{
		BlendState blend = BlendState::Default2D;
		blend.opAlpha = BlendOp::Max;
		blend.dstAlpha = Blend::DestAlpha;
		blend.srcAlpha = Blend::SrcAlpha;
		return blend;
	}

	void Main()
	{
		// Change window size to 1000x600
		Window::Resize(1000, 600);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font for UI
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Theme color
		const ColorF PrimaryColor{ 0.98, 0.96, 0.94 };

		// Rectangle for each UI element
		const Rect BaseRect{ 0, 0, 600, 600 };
		const Rect Button1{ 40, 40, 560, 200 };
		const Rect Button2{ 100, 260, 240, 100 };
		const Rect Button3{ 360, 260, 240, 100 };
		const Rect Button4{ 160, 380, 440, 120 };

		// Render texture for drawing UI
		const MSRenderTexture renderTexture{ BaseRect.size };

		// Target quadrilateral for projection
		const Quad TargetQuad{ 500, 60, 1000, 0, 1000, 600, 480, 520 };

		while (System::Update())
		{
			// Draw UI to render texture
			{
				// Clear renderTexture with ColorF{ 1.0, 0.0 } and
				// set renderTexture as render target
				const ScopedRenderTarget2D renderTarget{ renderTexture.clear(ColorF{ 1.0, 0.0 }) };

				// Apply blend state that writes maximum alpha value
				const ScopedRenderStates2D renderState{ MaxAlphaBlend() };

				// Draw UI
				{
					// Explore button
					Button1.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button1.draw(PrimaryColor);
					font(U"Explore").draw(88, Arg::leftCenter(80, 140), ColorF{ 0.4, 0.3, 0.2 });

					// Mission button
					Button2.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button2.draw(PrimaryColor);
					font(U"Mission").draw(44, Arg::leftCenter(120, 310), ColorF{ 0.4, 0.3, 0.2 });

					// Formation button
					Button3.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
					Button3.draw(PrimaryColor);
					font(U"Formation").draw(44, Arg::leftCenter(380, 310), ColorF{ 0.4, 0.3, 0.2 });

					// Event area
					Button4.draw(ColorF{ 0.2, 0.4, 0.6 });
					font(U"Event").draw(33, Arg::leftCenter(180, 415));

					// Footer
					Rect{ 60, 540, 540, 60 }.draw(ColorF{ 0.0, 0.6 });
				}

				// Complete the render texture contents by issuing 2D draw commands (Flush)
				// and resolving MSAA (Resolve)
				Graphics2D::Flush();
				renderTexture.resolve();
			}

			// Draw depth-style UI
			{
				// Apply sampler state suitable for QuadWarp (prevents roughness in reduced areas)
				const ScopedRenderStates2D sampler{ SamplerState::ClampAniso };

				// Project and draw render texture to TargetQuad
				Shader::QuadWarp(TargetQuad, renderTexture);
			}
		}
	}
	```

## 5. Hit detection for transformed buttons
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/quadwarp/5.png)

- Use `Mat3x3`'s `.transformRect()` to convert `Rect` to `Quad` after coordinate transformation.

??? note "Code"
	```cpp hl_lines="27-28 43-50 66-74 77-85 88-96 99-106 109-111"
	# include <Siv3D.hpp>

	/// @brief Returns a blend state that writes the maximum alpha value.
	BlendState MaxAlphaBlend()
	{
		BlendState blend = BlendState::Default2D;
		blend.opAlpha = BlendOp::Max;
		blend.dstAlpha = Blend::DestAlpha;
		blend.srcAlpha = Blend::SrcAlpha;
		return blend;
	}

	void Main()
	{
		// Change window size to 1000x600
		Window::Resize(1000, 600);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font for UI
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Theme color
		const ColorF PrimaryColor{ 0.98, 0.96, 0.94 };

		// Hover color
		const ColorF HoverColor{ 1.0, 0.96, 0.8 };

		// Rectangle for each UI element
		const Rect BaseRect{ 0, 0, 600, 600 };
		const Rect Button1{ 40, 40, 560, 200 };
		const Rect Button2{ 100, 260, 240, 100 };
		const Rect Button3{ 360, 260, 240, 100 };
		const Rect Button4{ 160, 380, 440, 120 };

		// Render texture for drawing UI
		const MSRenderTexture renderTexture{ BaseRect.size };

		// Target quadrilateral for projection
		const Quad TargetQuad{ 500, 60, 1000, 0, 1000, 600, 480, 520 };

		// Get QuadWarp transformation matrix (BaseRect → TargetQuad)
		const Mat3x3 projection = Mat3x3::Homography(BaseRect, TargetQuad);

		// Projected quadrilaterals for each button
		const Quad Button1Quad = projection.transformRect(Button1);
		const Quad Button2Quad = projection.transformRect(Button2);
		const Quad Button3Quad = projection.transformRect(Button3);
		const Quad Button4Quad = projection.transformRect(Button4);

		while (System::Update())
		{
			// Draw UI to render texture
			{
				// Clear renderTexture with ColorF{ 1.0, 0.0 } and
				// set renderTexture as render target
				const ScopedRenderTarget2D renderTarget{ renderTexture.clear(ColorF{ 1.0, 0.0 }) };

				// Apply blend state that writes maximum alpha value
				const ScopedRenderStates2D renderState{ MaxAlphaBlend() };

				// Draw UI
				{
					// Explore button
					{
						Button1.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
						Button1.draw(Button1Quad.mouseOver() ? HoverColor : PrimaryColor);
						font(U"Explore").draw(88, Arg::leftCenter(80, 140), ColorF{ 0.4, 0.3, 0.2 });
						if (Button1Quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}
					}

					// Mission button
					{
						Button2.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
						Button2.draw(Button2Quad.mouseOver() ? HoverColor : PrimaryColor);
						font(U"Mission").draw(44, Arg::leftCenter(120, 310), ColorF{ 0.4, 0.3, 0.2 });
						if (Button2Quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}
					}

					// Formation button
					{
						Button3.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
						Button3.draw(Button3Quad.mouseOver() ? HoverColor : PrimaryColor);
						font(U"Formation").draw(44, Arg::leftCenter(380, 310), ColorF{ 0.4, 0.3, 0.2 });
						if (Button3Quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}
					}

					// Event area
					{
						Button4.draw(ColorF{ 0.2, 0.4, 0.6 });
						font(U"Event").draw(33, Arg::leftCenter(180, 415));
						if (Button4Quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}
					}

					// Footer
					{
						Rect{ 60, 540, 540, 60 }.draw(ColorF{ 0.0, 0.6 });
					}
				}

				// Complete the render texture contents by issuing 2D draw commands (Flush)
				// and resolving MSAA (Resolve)
				Graphics2D::Flush();
				renderTexture.resolve();
			}

			// Draw depth-style UI
			{
				// Apply sampler state suitable for QuadWarp (prevents roughness in reduced areas)
				const ScopedRenderStates2D sampler{ SamplerState::ClampAniso };

				// Project and draw render texture to TargetQuad
				Shader::QuadWarp(TargetQuad, renderTexture);
			}
		}
	}
	```


## 6. Completion
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/quadwarp/6.png)

- Make the UI more lively.

??? note "Code"
	```cpp hl_lines="36 38-43 59 90 102 122-133 145-146"
	# include <Siv3D.hpp>

	/// @brief Returns a blend state that writes the maximum alpha value.
	BlendState MaxAlphaBlend()
	{
		BlendState blend = BlendState::Default2D;
		blend.opAlpha = BlendOp::Max;
		blend.dstAlpha = Blend::DestAlpha;
		blend.srcAlpha = Blend::SrcAlpha;
		return blend;
	}

	void Main()
	{
		// Change window size to 1000x600
		Window::Resize(1000, 600);

		// Set background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font for UI
		const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

		// Theme color
		const ColorF PrimaryColor{ 0.98, 0.96, 0.94 };

		// Hover color
		const ColorF HoverColor{ 1.0, 0.96, 0.8 };

		// Rectangle for each UI element
		const Rect BaseRect{ 0, 0, 600, 600 };
		const Rect Button1{ 40, 40, 560, 200 };
		const Rect Button2{ 100, 260, 240, 100 };
		const Rect Button3{ 360, 260, 240, 100 };
		const Rect Button4{ 160, 380, 440, 120 };
		const Rect Button5{ Arg::center(230, 570), 40 };

		// Icons and emojis
		const Texture compassIcon{ 0xF018B_icon, 90 };
		const Texture swordIcon{ 0xF18BE_icon, 90 };
		const Texture plusIcon{ 0xF0417_icon, 42 };
		const Texture moneyEmoji{ U"💰"_emoji };
		const Texture gemEmoji{ U"💎"_emoji };

		// Render texture for drawing UI
		const MSRenderTexture renderTexture{ BaseRect.size };

		// Target quadrilateral for projection
		const Quad TargetQuad{ 500, 60, 1000, 0, 1000, 600, 480, 520 };

		// Get QuadWarp transformation matrix (BaseRect → TargetQuad)
		const Mat3x3 projection = Mat3x3::Homography(BaseRect, TargetQuad);

		// Projected quadrilaterals for each button
		const Quad Button1Quad = projection.transformRect(Button1);
		const Quad Button2Quad = projection.transformRect(Button2);
		const Quad Button3Quad = projection.transformRect(Button3);
		const Quad Button4Quad = projection.transformRect(Button4);
		const Quad Button5Quad = projection.transformRect(Button5);

		while (System::Update())
		{
			// Draw UI to render texture
			{
				// Clear renderTexture with ColorF{ 1.0, 0.0 } and
				// set renderTexture as render target
				const ScopedRenderTarget2D renderTarget{ renderTexture.clear(ColorF{ 1.0, 0.0 }) };

				// Apply blend state that writes maximum alpha value
				const ScopedRenderStates2D renderState{ MaxAlphaBlend() };

				// Draw UI
				{
					// Explore button
					{
						Button1.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
						Button1.draw(Button1Quad.mouseOver() ? HoverColor : PrimaryColor);
						font(U"Explore").draw(88, Arg::leftCenter(80, 140), ColorF{ 0.4, 0.3, 0.2 });
						if (Button1Quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}
					}

					// Mission button
					{
						Button2.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
						Button2.draw(Button2Quad.mouseOver() ? HoverColor : PrimaryColor);
						font(U"Mission").draw(44, Arg::leftCenter(120, 310), ColorF{ 0.4, 0.3, 0.2 });
						compassIcon.drawAt(280, 310, ColorF{ 0.8 });
						if (Button2Quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}
					}

					// Formation button
					{
						Button3.movedBy(12, 10).draw(ColorF{ 0.5, 0.4, 0.3 });
						Button3.draw(Button3Quad.mouseOver() ? HoverColor : PrimaryColor);
						font(U"Formation").draw(44, Arg::leftCenter(380, 310), ColorF{ 0.4, 0.3, 0.2 });
						swordIcon.drawAt(540, 310, ColorF{ 0.8 });
						if (Button3Quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}
					}

					// Event area
					{
						Button4.draw(ColorF{ 0.2, 0.4, 0.6 });
						font(U"Event").draw(33, Arg::leftCenter(180, 415));
						if (Button4Quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}
					}

					// Footer
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

				// Complete the render texture contents by issuing 2D draw commands (Flush)
				// and resolving MSAA (Resolve)
				Graphics2D::Flush();
				renderTexture.resolve();
			}

			// Draw depth-style UI
			{
				// Shadow effect toward the right edge
				Rect{ 460, 0, 540, 600 }.draw(Arg::left = ColorF{ 0.0, 0.0 }, Arg::right = ColorF{ 0.0, 0.2 });

				// Apply sampler state suitable for QuadWarp (prevents roughness in reduced areas)
				const ScopedRenderStates2D sampler{ SamplerState::ClampAniso };

				// Project and draw render texture to TargetQuad
				Shader::QuadWarp(TargetQuad, renderTexture);
			}
		}
	}
	```