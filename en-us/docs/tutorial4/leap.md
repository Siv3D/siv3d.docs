
<!--

```cpp
# include <Siv3D.hpp>

void Main()
{
	Leap::Connection leap{ Leap::TrackingMode::Desktop };
	// ウインドウとシーンを 1280x720 にリサイズ
	Window::Resize(1280, 720);

	// 背景色 (リニアレンダリング用なので removeSRGBCurve() で sRGB カーブを除去）
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

	// UV チェック用テクスチャ (ミップマップ使用。リニアレンダリング時に正しく扱われるよう、sRGB テクスチャであると明示）
	const Texture uvChecker{ U"example/texture/uv.png", TextureDesc::MippedSRGB };

	// 3D シーンを描く、マルチサンプリング対応レンダーテクスチャ
	// リニア色空間のレンダリング用に TextureFormat::R8G8B8A8_Unorm_SRGB
	// 奥行きの比較のための深度バッファも使うので HasDepth::Yes
	// マルチサンプル・レンダーテクスチャなので、描画内容を使う前に resolve() が必要
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

	// 3D シーンのデバッグ用カメラ
	// 縦方向の視野角 30°, カメラの位置 (10, 16, -32)
	// 前後移動: [W][S], 左右移動: [A][D], 上下移動: [E][X], 注視点移動: アローキー, 加速: [Shift][Ctrl]
	DebugCamera3D camera{ renderTexture.size(), 30_deg, Vec3{ 0, 32, -28 } };

	while (System::Update())
	{
		ClearPrint();
		leap.update();
		// デバッグカメラの更新 (カメラの移動スピード: 2.0)
		camera.update(2.0);

		// 3D シーンにカメラを設定
		Graphics3D::SetCameraTransform(camera);

		// 3D 描画
		{
			// renderTexture を背景色で塗りつぶし、
			// renderTexture を 3D 描画のレンダーターゲットに
			const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

			// 床を描画
			Plane{ 64 }.draw(uvChecker);

			// ボックスを描画
			Box{ -8,2,10,4 }.draw(ColorF{ 0.8, 0.6, 0.4 }.removeSRGBCurve());

			// 球を描画
			Sphere{ 0,2,10,2 }.draw(ColorF{ 0.4, 0.8, 0.6 }.removeSRGBCurve());

			// 円柱を描画
			Cylinder{ 8, 2, 10, 2, 4 }.draw(ColorF{ 0.6, 0.4, 0.8 }.removeSRGBCurve());

			constexpr double scale = 0.07;

			for (auto x : Range(-8, 8))
			{
				const Box box{ x * 2, 8, 10, 1.8, 1, 10 };

				bool intersect = false;

				for (const auto& hand : leap.getHands())
				{
					for (auto fingerIndex : step(5))
					{
						for (auto boneIndex : step(4))
						{
							const Vec3 to = hand.fingerBone(fingerIndex, boneIndex).to * scale;
							const Sphere sphere{ to, 0.4 };

							if (sphere.intersects(box))
							{
								intersect = true;
								break;
							}
						}

						if (intersect)
							break;
					}

					if (intersect)
						break;
				}

				box
					.draw(HSV{ x * 40, intersect ? 1.0 : 0.2, 1.0 }.removeSRGBCurve());
			}

			for (const auto& hand : leap.getHands())
			{
				const auto handID = hand.id();

				//Sphere{ hand.palmPosition() * 0.05, 0.2 }
				//	.draw();

				for (auto fingerIndex : step(5))
				{
					for (auto boneIndex : step(4))
					{
						const Vec3 from = hand.fingerBone(fingerIndex, boneIndex).from * scale;
						const Vec3 to = hand.fingerBone(fingerIndex, boneIndex).to * scale;

						if (fingerIndex == 4 && boneIndex == 0)
						{
							Sphere{ from, 0.4 }
								.draw(HSV{ handID * 60 }.removeSRGBCurve());
						}

						Sphere{ to, 0.4 }
							.draw(HSV{ handID * 60 }.removeSRGBCurve());

						if ((fingerIndex != 0 && fingerIndex != 4) && boneIndex == 0)
						{
							continue;
						}

						Cylinder{ from, to, 0.2 }.draw();
					}
				}

				{
					const Vec3 from = hand.fingerBone(0, 0).from * scale;
					const Vec3 to = hand.fingerBone(1, 1).from * scale;
					Cylinder{ from, to, 0.2 }.draw();
				}
				{
					const Vec3 from = hand.fingerBone(1, 1).from * scale;
					const Vec3 to = hand.fingerBone(2, 1).from * scale;
					Cylinder{ from, to, 0.2 }.draw();
				}
				{
					const Vec3 from = hand.fingerBone(2, 1).from * scale;
					const Vec3 to = hand.fingerBone(3, 1).from * scale;
					Cylinder{ from, to, 0.2 }.draw();
				}
				{
					const Vec3 from = hand.fingerBone(3, 1).from * scale;
					const Vec3 to = hand.fingerBone(4, 1).from * scale;
					Cylinder{ from, to, 0.2 }.draw();
				}
				{
					const Vec3 from = hand.fingerBone(0, 0).from * scale;
					const Vec3 to = hand.fingerBone(4, 0).from * scale;
					Cylinder{ from, to, 0.2 }.draw();
				}
			}
		}

		// 3D シーンを 2D シーンに描画
		{
			// renderTexture を resolve する前に 3D 描画を実行する
			Graphics3D::Flush();

			// マルチサンプル・テクスチャのリゾルブ
			renderTexture.resolve();

			// リニアレンダリングされた renderTexture をシーンに転送
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```

-->