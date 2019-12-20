
# 物体検出

## イラストからの顔検出
<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/reference/obj-detection/face-0.mp4?raw=true" autoplay loop muted></video>
<small>イラスト提供: 古古米さん</small>
```C++
# include <Siv3D.hpp>

void Main()
{
	Texture texture;

	double scale = 1.0;

	Array<Rect> detectedFaces;

	while (System::Update())
	{
		// ファイルがドロップされた
		if (DragDrop::HasNewFilePaths())
		{
			// ファイルを画像として読み込めた
			if (const Image image{ DragDrop::GetDroppedFilePaths().front().path })
			{
				// イラストの顔を検出
				detectedFaces = image.detectObjects(HaarCascade::AnimeFace);

				// 画面のサイズに合うように画像を拡大縮小
				texture = Texture(image.fitted(Scene::Size()));

				// 画像の拡大縮小率
				scale = static_cast<double>(texture.width()) / image.width();
			}
		}

		if (texture)
		{
			texture.draw(0, 0);

			// 顔の領域の座標を表示に合わせる
			Transformer2D t(Mat3x2::Scale(scale));

			for (const auto& detectedFace : detectedFaces)
			{
				detectedFace.drawFrame(4 / scale, ColorF(1.0, 0.0, 0.0, Periodic::Sine0_1(1.5s)));
			}
		}
	}
}
```


## カメラからの顔検出

```C++
# include <Siv3D.hpp>

void Main()
{
	Webcam webcam(0);

	// 大きいサイズの画像から顔を検出するのは時間がかかるので 640x480 前後で
	webcam.setResolution(Size(640, 480));
	const Size resolution = webcam.getResolution();
	if (!webcam.start())
	{
		throw Error(U"Webcam not available.");
	}

	Window::Resize(resolution);

	Image image;
	DynamicTexture texture;
	Array<Rect> detectedFaces;

	// 画像中の顔の最低サイズの目安
	const Size minimumFaceSize = Size::All(image.height() / 8);

	while (System::Update())
	{
		if (webcam.hasNewFrame())
		{
			webcam.getFrame(image);

			// 画像内の顔を検出
			detectedFaces = image.detectObjects(HaarCascade::Face, 3, minimumFaceSize);

			texture.fillIfNotBusy(image);
		}

		if (texture)
		{
			texture.draw();
		}

		for (const auto& detectedFace : detectedFaces)
		{
			detectedFace.drawFrame(4, ColorF(1.0, 0.0, 0.0, Periodic::Sine0_1(1.5s)));
		}
	}
}
```
