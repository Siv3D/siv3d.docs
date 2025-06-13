# 32. Drawing Videos
Learn how to draw videos and GIF animations.

## 32.1 Video Drawing
- The `VideoTexture` class allows you to draw videos like textures
- `VideoTexture` has almost the same member functions as `Texture`, allowing you to treat each frame of the video like a `Texture`
- To advance video time, you need to call `.advance()` every frame to advance the playback position
	- Since background threads prefetch the next video frame, playing videos in the forward direction doesn't impose a significant load
	- You should avoid calling `.advance()` on videos that are not playing
- `VideoTexture` does not create mipmaps. If you need to draw videos at sizes smaller than their original resolution, it's advisable to prepare videos with smaller resolutions from a runtime performance and memory usage perspective
- Supported video formats vary by platform. MP4 files are supported on Windows, macOS, and Linux

!!! example "Embedding Video Files on Windows"
	- On Windows, there is a limitation where video files embedded using "resource file embedding" (explained in later tutorials) cannot be loaded by `VideoTexture`
	- As a workaround, there is a [method to write to temporary files :material-open-in-new:](https://gist.github.com/Reputeless/3d527302d459792f7a5e1094d30d0529){:target="_blank"}.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/video/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Loop::Yes for looping, Loop::No for non-looping
	const VideoTexture videoTexture{ U"example/video/river.mp4", Loop::Yes };

	while (System::Update())
	{
		ClearPrint();

		// Playback position (seconds) / Video length (seconds)
		Print << U"{:.2f}/{:.2f}"_fmt(videoTexture.posSec(), videoTexture.lengthSec());

		// Advance video time (default is Scene::DeltaTime() seconds)
		videoTexture.advance();

		videoTexture
			.scaled(0.5).draw();

		videoTexture
			.scaled(0.5)
			.rotated(Scene::Time() * 30_deg)
			.drawAt(Cursor::Pos());
	}
}
```


## 32.2 (Reference Implementation) Support for Videos with Audio
- There is no dedicated class for playing videos with audio
- You can play videos with audio by simultaneously creating `VideoTexture` and `Audio` from a video file with audio and synchronizing their playback
	- `Audio` is explained in detail in [**Tutorial 41. Audio Playback**](../tutorial3/audio.md){:target="_blank"}
	- Some platforms may not support loading video files with audio
- The following sample is an implementation example:
	- When the difference between video playback position and audio playback position exceeds 0.1 seconds, the audio playback position is adjusted to match the video playback position
	- This prevents desynchronization between video and audio, but may introduce noise in the audio
	
!!! warning "Preparing Video Files"
	- Siv3D projects do not include video files with audio
	- Please prepare your own video files before running the sample

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Path to video file with audio (not included in the project)
	const FilePath path = U"test.mp4";

	if (not FileSystem::Exists(path))
	{
		throw Error{ U"{} was not found"_fmt(path) };
	}

	// Loop::Yes for looping, Loop::No for non-looping
	const VideoTexture videoTexture{ path, Loop::Yes };

	// Audio object for playing the video's audio
	const Audio audio{ Audio::Stream, path, Loop::Yes };

	// If audio loading succeeded
	if (not audio)
	{
		throw Error{ U"Failed to load audio" };
	}

	// Play audio
	audio.play();

	while (System::Update())
	{
		ClearPrint();

		// Video playback position (seconds)
		const double videoTime = videoTexture.posSec();

		// Audio playback position (seconds)
		const double audioTime = audio.posSec();

		Print << U"{:.2f}/{:.2f}/{:.2f}"_fmt(videoTime, audioTime, videoTexture.lengthSec());

		// If the difference between video and audio playback positions exceeds 0.1 seconds
		if (0.1 < AbsDiff(audioTime, videoTime))
		{
			// Adjust audio playback position to match video playback position
			audio.seekTime(videoTime);
		}

		// Advance video time (default is Scene::DeltaTime() seconds)
		videoTexture.advance();

		videoTexture
			.scaled(0.5).draw();
	}
}
```


## 32.3 GIF Animation Drawing
- GIF animations can be drawn by creating an array of `Texture` from each frame and drawing them at appropriate timing
	- GIF animation itself is a legacy image format, so its use for game animations is not recommended
- `AnimatedGIFReader` allows you to get arrays of image data `Image` for each frame and delays (in milliseconds) to the next frame
- Create an array of `Texture` from the array of `Image` and draw frames at appropriate timing
- When given time `t`, you can determine which frame to draw using `AnimatedGIFReader::GetFrameIndex()`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/video/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Open GIF animation image
	const AnimatedGIFReader gif{ U"example/gif/test.gif" };

	// Load image and delay (milliseconds) to next frame for each frame
	Array<Image> images;
	Array<int32> delays;
	gif.read(images, delays);

	// Create Texture from each frame's Image
	const Array<Texture> textures = images.map([](const Image& image) { return Texture{ image }; });

	// Clear image data to reduce memory consumption as it's no longer needed
	images.clear();

	while (System::Update())
	{
		ClearPrint();

		// Number of frames
		Print << textures.size() << U" frames";

		// List of delays (milliseconds) for each frame
		Print << U"delays: " << delays;

		// Animation elapsed time
		const double t = Scene::Time();

		// Calculate which frame to draw based on elapsed time and frame delays
		const size_t frameIndex = AnimatedGIFReader::GetFrameIndex(t, delays);

		// Current frame number
		Print << U"frameIndex: " << frameIndex;

		textures[frameIndex].draw(200, 200);
	}
}
```