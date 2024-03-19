# 3D のボードゲームテンプレート

## 1. 基本のコード
- テーブルの上にボードがあります

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/study/advanced/4/1.png)

```cpp
# include <Siv3D.hpp>

// テーブルを描画する関数
void DrawTable(const Texture& tableTexture)
{
	Plane{ Vec3{ 0, -0.4, 0 }, 24 }.draw(tableTexture);
}

// 盤を描画する関数
void DrawBoard(const Mesh& mesh)
{
	const ColorF BoardColor = ColorF{ 0.9, 0.85, 0.75 }.removeSRGBCurve();

	mesh.draw(BoardColor);
}

// カメラの制御クラス
class CameraController
{
public:

	explicit CameraController(const Size& sceneSize)
		: m_camera{ sceneSize, m_verticalFOV, m_eyePosition, m_focusPosition } {}

	void update()
	{
		// 視点を球面座標系で計算する
		m_eyePosition = Spherical{ 24, m_theta, (270_deg - m_phi) };

		// カメラを更新する
		m_camera.setView(m_eyePosition, m_focusPosition);

		// シーンにカメラを設定する
		Graphics3D::SetCameraTransform(m_camera);
	}

private:

	// 縦方向の視野角（ラジアン）
	double m_verticalFOV = 25_deg;

	// カメラの視点の位置
	Vec3 m_eyePosition{ 16, 16, -16 };

	// カメラの注視点の位置
	Vec3 m_focusPosition{ 0, 0, 0 };

	double m_phi = -20_deg;

	double m_theta = 50_deg;

	// カメラ
	BasicCamera3D m_camera;
};

void Main()
{
	// ウインドウとシーンを 1280x720 にリサイズする
	Window::Resize(1280, 720);

	// 環境光を設定する
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.75, 0.75, 0.75 });

	// 太陽光を設定する
	Graphics3D::SetSunColor(ColorF{ 0.5, 0.5, 0.5 });

	// 太陽の方向を設定する
	Graphics3D::SetSunDirection(Vec3{ 0, 1, -0.3 }.normalized());

	// 背景色 (3D 用の色は .removeSRGBCurve() で sRGB カーブを除去）
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

	// 3D シーンを描く、マルチサンプリング対応レンダーテクスチャ
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

	// テーブル用のテクスチャ
	const Texture tableTexture{ U"example/texture/uv.png", TextureDesc::MippedSRGB};

	// 盤用の 3D メッシュ
	const Mesh meshBoard{ MeshData::RoundedBox(0.1, Vec3{ 10, 0.4, 10 }, 5).translate(0, -0.2, 0) };

	// カメラ制御
	CameraController cameraController{ renderTexture.size() };

	while (System::Update())
	{
		////////////////////////////////
		//
		//	状態の更新
		//
		////////////////////////////////
		{
			cameraController.update();
		}

		////////////////////////////////
		//
		//	3D 描画
		//
		////////////////////////////////
		{
			{
				// renderTexture を背景色で塗りつぶし、3D 描画のレンダーターゲットに
				const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

				DrawTable(tableTexture);
				DrawBoard(meshBoard);
			}

			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 2. 盤の線
- 3D の線分で盤の線を表示します

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/study/advanced/4/2.png)

```cpp hl_lines="13 17-26"
# include <Siv3D.hpp>

// テーブルを描画する関数
void DrawTable(const Texture& tableTexture)
{
	Plane{ Vec3{ 0, -0.4, 0 }, 24 }.draw(tableTexture);
}

// 盤を描画する関数
void DrawBoard(const Mesh& mesh)
{
	const ColorF BoardColor = ColorF{ 0.9, 0.85, 0.75 }.removeSRGBCurve();
	const ColorF LineColor = ColorF{ 0.3, 0.2, 0.0 }.removeSRGBCurve();

	mesh.draw(BoardColor);

	// 盤上の線
	for (int32 i = -4; i <= 4; ++i)
	{
		Line3D{ Vec3{ -4, 0.01, i }, Vec3{ 4, 0.01, i } }.draw(LineColor);
		Line3D{ Vec3{ i, 0.01, -4 }, Vec3{ i, 0.01, 4 } }.draw(LineColor);
	}
	Line3D{ Vec3{ -4.1, 0.01, -4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, 4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ -4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ 4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
}

// カメラの制御クラス
class CameraController
{
public:

	explicit CameraController(const Size& sceneSize)
		: m_camera{ sceneSize, m_verticalFOV, m_eyePosition, m_focusPosition } {}

	void update()
	{
		// 視点を球面座標系で計算する
		m_eyePosition = Spherical{ 24, m_theta, (270_deg - m_phi) };

		// カメラを更新する
		m_camera.setView(m_eyePosition, m_focusPosition);

		// シーンにカメラを設定する
		Graphics3D::SetCameraTransform(m_camera);
	}

private:

	// 縦方向の視野角（ラジアン）
	double m_verticalFOV = 25_deg;

	// カメラの視点の位置
	Vec3 m_eyePosition{ 16, 16, -16 };

	// カメラの注視点の位置
	Vec3 m_focusPosition{ 0, 0, 0 };

	double m_phi = -20_deg;

	double m_theta = 50_deg;

	// カメラ
	BasicCamera3D m_camera;
};

void Main()
{
	// ウインドウとシーンを 1280x720 にリサイズする
	Window::Resize(1280, 720);

	// 環境光を設定する
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.75, 0.75, 0.75 });

	// 太陽光を設定する
	Graphics3D::SetSunColor(ColorF{ 0.5, 0.5, 0.5 });

	// 太陽の方向を設定する
	Graphics3D::SetSunDirection(Vec3{ 0, 1, -0.3 }.normalized());

	// 背景色 (3D 用の色は .removeSRGBCurve() で sRGB カーブを除去）
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

	// 3D シーンを描く、マルチサンプリング対応レンダーテクスチャ
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

	// テーブル用のテクスチャ
	const Texture tableTexture{ U"example/texture/uv.png", TextureDesc::MippedSRGB};

	// 盤用の 3D メッシュ
	const Mesh meshBoard{ MeshData::RoundedBox(0.1, Vec3{ 10, 0.4, 10 }, 5).translate(0, -0.2, 0) };

	// カメラ制御
	CameraController cameraController{ renderTexture.size() };

	while (System::Update())
	{
		////////////////////////////////
		//
		//	状態の更新
		//
		////////////////////////////////
		{
			cameraController.update();
		}

		////////////////////////////////
		//
		//	3D 描画
		//
		////////////////////////////////
		{
			{
				// renderTexture を背景色で塗りつぶし、3D 描画のレンダーターゲットに
				const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

				DrawTable(tableTexture);
				DrawBoard(meshBoard);
			}

			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```

## 3. 盤面の状態
- 盤面にブロックを配置します

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/study/advanced/4/3.png)

```cpp hl_lines="29-93 157-158 166-169 194" 
# include <Siv3D.hpp>

// テーブルを描画する関数
void DrawTable(const Texture& tableTexture)
{
	Plane{ Vec3{ 0, -0.4, 0 }, 24 }.draw(tableTexture);
}

// 盤を描画する関数
void DrawBoard(const Mesh& mesh)
{
	const ColorF BoardColor = ColorF{ 0.9, 0.85, 0.75 }.removeSRGBCurve();
	const ColorF LineColor = ColorF{ 0.3, 0.2, 0.0 }.removeSRGBCurve();

	mesh.draw(BoardColor);

	// 盤上の線
	for (int32 i = -4; i <= 4; ++i)
	{
		Line3D{ Vec3{ -4, 0.01, i }, Vec3{ 4, 0.01, i } }.draw(LineColor);
		Line3D{ Vec3{ i, 0.01, -4 }, Vec3{ i, 0.01, 4 } }.draw(LineColor);
	}
	Line3D{ Vec3{ -4.1, 0.01, -4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, 4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ -4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ 4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
}

// 盤上のインデックス (x, y, z) から Box を作成する関数
Box MakeBox(int32 x, int32 y, int32 z)
{
	return Box::FromPoints(Vec3{ (x - 4), (y + 1), (4 - z) }, Vec3{ (x - 3), y, (3 - z) });
}

// ブロックを描く関数
void DrawBlock(int32 x, int32 y, int32 z, const ColorF& color, double scale = 1.0)
{
	MakeBox(x, y, z).scaled(scale).draw(color);
}

// ブロックを描く関数
void DrawBlock(int32 x, int32 y, int32 z, const ColorF& color, const Texture& blockTexture)
{
	MakeBox(x, y, z).draw(blockTexture, color);
}

// ゲームの状態
struct Game
{
	static constexpr int32 MaxY = 15;

	int32 s[8][(MaxY + 1)][8] = {};

	int32 getHeight(int32 x, int32 z) const
	{
		for (int32 y = MaxY; 0 <= y; --y)
		{
			if (s[x][y][z])
			{
				return (y + 1);
			}
		}

		return 0;
	}
};

// ゲームの状態に基づいてブロックを描く関数
void DrawGame(const Game& game, const Texture& blockTexture)
{
	const ColorF BlockColor1 = ColorF{ 1.0, 0.85, 0.6 }.removeSRGBCurve();
	const ColorF BlockColor2 = ColorF{ 0.4, 0.15, 0.15 }.removeSRGBCurve();

	for (int32 y = 0; y <= Game::MaxY; ++y)
	{
		for (int32 x = 0; x < 8; ++x)
		{
			for (int32 z = 0; z < 8; ++z)
			{
				const int32 s = game.s[x][y][z];

				if (s == 1)
				{
					DrawBlock(x, y, z, BlockColor1, blockTexture);
				}
				else if (s == 2)
				{
					DrawBlock(x, y, z, BlockColor2, blockTexture);
				}
			}
		}
	}
}

// カメラの制御クラス
class CameraController
{
public:

	explicit CameraController(const Size& sceneSize)
		: m_camera{ sceneSize, m_verticalFOV, m_eyePosition, m_focusPosition } {}

	void update()
	{
		// 視点を球面座標系で計算する
		m_eyePosition = Spherical{ 24, m_theta, (270_deg - m_phi) };

		// カメラを更新する
		m_camera.setView(m_eyePosition, m_focusPosition);

		// シーンにカメラを設定する
		Graphics3D::SetCameraTransform(m_camera);
	}

private:

	// 縦方向の視野角（ラジアン）
	double m_verticalFOV = 25_deg;

	// カメラの視点の位置
	Vec3 m_eyePosition{ 16, 16, -16 };

	// カメラの注視点の位置
	Vec3 m_focusPosition{ 0, 0, 0 };

	double m_phi = -20_deg;

	double m_theta = 50_deg;

	// カメラ
	BasicCamera3D m_camera;
};

void Main()
{
	// ウインドウとシーンを 1280x720 にリサイズする
	Window::Resize(1280, 720);

	// 環境光を設定する
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.75, 0.75, 0.75 });

	// 太陽光を設定する
	Graphics3D::SetSunColor(ColorF{ 0.5, 0.5, 0.5 });

	// 太陽の方向を設定する
	Graphics3D::SetSunDirection(Vec3{ 0, 1, -0.3 }.normalized());

	// 背景色 (3D 用の色は .removeSRGBCurve() で sRGB カーブを除去）
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

	// 3D シーンを描く、マルチサンプリング対応レンダーテクスチャ
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

	// テーブル用のテクスチャ
	const Texture tableTexture{ U"example/texture/uv.png", TextureDesc::MippedSRGB};

	// ブロック用のテクスチャ
	const Texture blockTexture{ U"example/texture/uv.png", TextureDesc::MippedSRGB };

	// 盤用の 3D メッシュ
	const Mesh meshBoard{ MeshData::RoundedBox(0.1, Vec3{ 10, 0.4, 10 }, 5).translate(0, -0.2, 0) };

	// カメラ制御
	CameraController cameraController{ renderTexture.size() };

	// ゲームの状態
	Game game;
	game.s[0][0][0] = game.s[1][0][1] = 1;
	game.s[4][0][4] = game.s[5][0][4] = 2;

	while (System::Update())
	{
		////////////////////////////////
		//
		//	状態の更新
		//
		////////////////////////////////
		{
			cameraController.update();
		}

		////////////////////////////////
		//
		//	3D 描画
		//
		////////////////////////////////
		{
			{
				// renderTexture を背景色で塗りつぶし、3D 描画のレンダーターゲットに
				const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

				DrawTable(tableTexture);
				DrawBoard(meshBoard);
				DrawGame(game, blockTexture);
			}

			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 4. カメラの操作
- ボードの端をつかんでカメラを回転できます

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/study/advanced/4/4.png)

```cpp hl_lines="105-142 154-162 182-184"
# include <Siv3D.hpp>

// テーブルを描画する関数
void DrawTable(const Texture& tableTexture)
{
	Plane{ Vec3{ 0, -0.4, 0 }, 24 }.draw(tableTexture);
}

// 盤を描画する関数
void DrawBoard(const Mesh& mesh)
{
	const ColorF BoardColor = ColorF{ 0.9, 0.85, 0.75 }.removeSRGBCurve();
	const ColorF LineColor = ColorF{ 0.3, 0.2, 0.0 }.removeSRGBCurve();

	mesh.draw(BoardColor);

	// 盤上の線
	for (int32 i = -4; i <= 4; ++i)
	{
		Line3D{ Vec3{ -4, 0.01, i }, Vec3{ 4, 0.01, i } }.draw(LineColor);
		Line3D{ Vec3{ i, 0.01, -4 }, Vec3{ i, 0.01, 4 } }.draw(LineColor);
	}
	Line3D{ Vec3{ -4.1, 0.01, -4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, 4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ -4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ 4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
}

// 盤上のインデックス (x, y, z) から Box を作成する関数
Box MakeBox(int32 x, int32 y, int32 z)
{
	return Box::FromPoints(Vec3{ (x - 4), (y + 1), (4 - z) }, Vec3{ (x - 3), y, (3 - z) });
}

// ブロックを描く関数
void DrawBlock(int32 x, int32 y, int32 z, const ColorF& color, double scale = 1.0)
{
	MakeBox(x, y, z).scaled(scale).draw(color);
}

// ブロックを描く関数
void DrawBlock(int32 x, int32 y, int32 z, const ColorF& color, const Texture& blockTexture)
{
	MakeBox(x, y, z).draw(blockTexture, color);
}

// ゲームの状態
struct Game
{
	static constexpr int32 MaxY = 15;

	int32 s[8][(MaxY + 1)][8] = {};

	int32 getHeight(int32 x, int32 z) const
	{
		for (int32 y = MaxY; 0 <= y; --y)
		{
			if (s[x][y][z])
			{
				return (y + 1);
			}
		}

		return 0;
	}
};

// ゲームの状態に基づいてブロックを描く関数
void DrawGame(const Game& game, const Texture& blockTexture)
{
	const ColorF BlockColor1 = ColorF{ 1.0, 0.85, 0.6 }.removeSRGBCurve();
	const ColorF BlockColor2 = ColorF{ 0.4, 0.15, 0.15 }.removeSRGBCurve();

	for (int32 y = 0; y <= Game::MaxY; ++y)
	{
		for (int32 x = 0; x < 8; ++x)
		{
			for (int32 z = 0; z < 8; ++z)
			{
				const int32 s = game.s[x][y][z];

				if (s == 1)
				{
					DrawBlock(x, y, z, BlockColor1, blockTexture);
				}
				else if (s == 2)
				{
					DrawBlock(x, y, z, BlockColor2, blockTexture);
				}
			}
		}
	}
}

// カメラの制御クラス
class CameraController
{
public:

	explicit CameraController(const Size& sceneSize)
		: m_camera{ sceneSize, m_verticalFOV, m_eyePosition, m_focusPosition } {}

	void update()
	{
		const Ray ray = getMouseRay();

		// 盤のまわり部分
		const std::array<Box, 4> boxes =
		{
			Box::FromPoints(Vec3{ -5, 0.0, 5 }, Vec3{ 5, -0.4, 4 }),
			Box::FromPoints(Vec3{ 4, 0.0, 5 }, Vec3{ 5, -0.4, -5 }),
			Box::FromPoints(Vec3{ -5, 0.0, -4 }, Vec3{ 5, -0.4, -5 }),
			Box::FromPoints(Vec3{ -5, 0.0, 5 }, Vec3{ -4, -0.4, -5 })
		};

		if (MouseL.up())
		{
			m_grabbed = false;
		}

		if (m_grabbed)
		{
			const double before = (m_cursorPos - Scene::Center()).getAngle();
			const double after = (Cursor::Pos() - Scene::Center()).getAngle();
			m_phi -= (after - before);
			m_cursorPos = Cursor::Pos();
		}

		for (const auto& box : boxes)
		{
			if (box.intersects(ray))
			{
				// マウスカーソルを手のアイコンにする
				Cursor::RequestStyle(CursorStyle::Hand);

				if ((not m_grabbed) && MouseL.down())
				{
					m_grabbed = true;
					m_cursorPos = Cursor::Pos();
				}
			}
		}

		// 視点を球面座標系で計算する
		m_eyePosition = Spherical{ 24, m_theta, (270_deg - m_phi) };

		// カメラを更新する
		m_camera.setView(m_eyePosition, m_focusPosition);

		// シーンにカメラを設定する
		Graphics3D::SetCameraTransform(m_camera);
	}

	Ray getMouseRay() const
	{
		return m_camera.screenToRay(Cursor::PosF());
	}

	bool isGrabbing() const
	{
		return m_grabbed;
	}

private:

	// 縦方向の視野角（ラジアン）
	double m_verticalFOV = 25_deg;

	// カメラの視点の位置
	Vec3 m_eyePosition{ 16, 16, -16 };

	// カメラの注視点の位置
	Vec3 m_focusPosition{ 0, 0, 0 };

	double m_phi = -20_deg;

	double m_theta = 50_deg;

	// カメラ
	BasicCamera3D m_camera;

	bool m_grabbed = false;

	Vec2 m_cursorPos = Scene::Center();
};

void Main()
{
	// ウインドウとシーンを 1280x720 にリサイズする
	Window::Resize(1280, 720);

	// 環境光を設定する
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.75, 0.75, 0.75 });

	// 太陽光を設定する
	Graphics3D::SetSunColor(ColorF{ 0.5, 0.5, 0.5 });

	// 太陽の方向を設定する
	Graphics3D::SetSunDirection(Vec3{ 0, 1, -0.3 }.normalized());

	// 背景色 (3D 用の色は .removeSRGBCurve() で sRGB カーブを除去）
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

	// 3D シーンを描く、マルチサンプリング対応レンダーテクスチャ
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

	// テーブル用のテクスチャ
	const Texture tableTexture{ U"example/texture/uv.png", TextureDesc::MippedSRGB};

	// ブロック用のテクスチャ
	const Texture blockTexture{ U"example/texture/uv.png", TextureDesc::MippedSRGB };

	// 盤用の 3D メッシュ
	const Mesh meshBoard{ MeshData::RoundedBox(0.1, Vec3{ 10, 0.4, 10 }, 5).translate(0, -0.2, 0) };

	// カメラ制御
	CameraController cameraController{ renderTexture.size() };

	// ゲームの状態
	Game game;
	game.s[0][0][0] = game.s[1][0][1] = 1;
	game.s[4][0][4] = game.s[5][0][4] = 2;

	while (System::Update())
	{
		////////////////////////////////
		//
		//	状態の更新
		//
		////////////////////////////////
		{
			cameraController.update();
		}

		////////////////////////////////
		//
		//	3D 描画
		//
		////////////////////////////////
		{
			{
				// renderTexture を背景色で塗りつぶし、3D 描画のレンダーターゲットに
				const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

				DrawTable(tableTexture);
				DrawBoard(meshBoard);
				DrawGame(game, blockTexture);
			}

			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```

## 5. プロシージャルテクスチャ
- `PerlinNoise` を用いてプログラムでテクスチャを作成します

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/study/advanced/4/5.png)

```cpp hl_lines="3-23 230 233"
# include <Siv3D.hpp>

// テーブル用のテクスチャを手続き的に生成する関数
Image CreateTableImage()
{
	PerlinNoise noise;
	return Image::Generate(Size{ 1024, 1024 }, [&](Point p)
	{
		const double x = Fraction(noise.octave2D0_1(p * Vec2{ 0.03, 0.0005 }, 2) * 25) * 0.3 + 0.55;
	return ColorF{ x, 0.85 * x, 0.7 * x }.removeSRGBCurve();
	}).gaussianBlurred(3);
}

// ブロック用のテクスチャを手続き的に生成する関数
Image CreateBlockImage()
{
	PerlinNoise noise;
	return Image::Generate(Size{ 256, 256 }, [&](Point p)
	{
		const double x = Fraction(noise.octave2D0_1(p * Vec2{ 0.05, 0.0005 }, 2) * 25) * 0.15 + 0.85;
	return ColorF{ x }.removeSRGBCurve();
	}).gaussianBlurred(2);
}

// テーブルを描画する関数
void DrawTable(const Texture& tableTexture)
{
	Plane{ Vec3{ 0, -0.4, 0 }, 24 }.draw(tableTexture);
}

// 盤を描画する関数
void DrawBoard(const Mesh& mesh)
{
	const ColorF BoardColor = ColorF{ 0.9, 0.85, 0.75 }.removeSRGBCurve();
	const ColorF LineColor = ColorF{ 0.3, 0.2, 0.0 }.removeSRGBCurve();

	mesh.draw(BoardColor);

	// 盤上の線
	for (int32 i = -4; i <= 4; ++i)
	{
		Line3D{ Vec3{ -4, 0.01, i }, Vec3{ 4, 0.01, i } }.draw(LineColor);
		Line3D{ Vec3{ i, 0.01, -4 }, Vec3{ i, 0.01, 4 } }.draw(LineColor);
	}
	Line3D{ Vec3{ -4.1, 0.01, -4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, 4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ -4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ 4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
}

// 盤上のインデックス (x, y, z) から Box を作成する関数
Box MakeBox(int32 x, int32 y, int32 z)
{
	return Box::FromPoints(Vec3{ (x - 4), (y + 1), (4 - z) }, Vec3{ (x - 3), y, (3 - z) });
}

// ブロックを描く関数
void DrawBlock(int32 x, int32 y, int32 z, const ColorF& color, double scale = 1.0)
{
	MakeBox(x, y, z).scaled(scale).draw(color);
}

// ブロックを描く関数
void DrawBlock(int32 x, int32 y, int32 z, const ColorF& color, const Texture& blockTexture)
{
	MakeBox(x, y, z).draw(blockTexture, color);
}

// ゲームの状態
struct Game
{
	static constexpr int32 MaxY = 15;

	int32 s[8][(MaxY + 1)][8] = {};

	int32 getHeight(int32 x, int32 z) const
	{
		for (int32 y = MaxY; 0 <= y; --y)
		{
			if (s[x][y][z])
			{
				return (y + 1);
			}
		}

		return 0;
	}
};

// ゲームの状態に基づいてブロックを描く関数
void DrawGame(const Game& game, const Texture& blockTexture)
{
	const ColorF BlockColor1 = ColorF{ 1.0, 0.85, 0.6 }.removeSRGBCurve();
	const ColorF BlockColor2 = ColorF{ 0.4, 0.15, 0.15 }.removeSRGBCurve();

	for (int32 y = 0; y <= Game::MaxY; ++y)
	{
		for (int32 x = 0; x < 8; ++x)
		{
			for (int32 z = 0; z < 8; ++z)
			{
				const int32 s = game.s[x][y][z];

				if (s == 1)
				{
					DrawBlock(x, y, z, BlockColor1, blockTexture);
				}
				else if (s == 2)
				{
					DrawBlock(x, y, z, BlockColor2, blockTexture);
				}
			}
		}
	}
}

// カメラの制御クラス
class CameraController
{
public:

	explicit CameraController(const Size& sceneSize)
		: m_camera{ sceneSize, m_verticalFOV, m_eyePosition, m_focusPosition } {}

	void update()
	{
		const Ray ray = getMouseRay();

		// 盤のまわり部分
		const std::array<Box, 4> boxes =
		{
			Box::FromPoints(Vec3{ -5, 0.0, 5 }, Vec3{ 5, -0.4, 4 }),
			Box::FromPoints(Vec3{ 4, 0.0, 5 }, Vec3{ 5, -0.4, -5 }),
			Box::FromPoints(Vec3{ -5, 0.0, -4 }, Vec3{ 5, -0.4, -5 }),
			Box::FromPoints(Vec3{ -5, 0.0, 5 }, Vec3{ -4, -0.4, -5 })
		};

		if (MouseL.up())
		{
			m_grabbed = false;
		}

		if (m_grabbed)
		{
			const double before = (m_cursorPos - Scene::Center()).getAngle();
			const double after = (Cursor::Pos() - Scene::Center()).getAngle();
			m_phi -= (after - before);
			m_cursorPos = Cursor::Pos();
		}

		for (const auto& box : boxes)
		{
			if (box.intersects(ray))
			{
				// マウスカーソルを手のアイコンにする
				Cursor::RequestStyle(CursorStyle::Hand);

				if ((not m_grabbed) && MouseL.down())
				{
					m_grabbed = true;
					m_cursorPos = Cursor::Pos();
				}
			}
		}

		// 視点を球面座標系で計算する
		m_eyePosition = Spherical{ 24, m_theta, (270_deg - m_phi) };

		// カメラを更新する
		m_camera.setView(m_eyePosition, m_focusPosition);

		// シーンにカメラを設定する
		Graphics3D::SetCameraTransform(m_camera);
	}

	Ray getMouseRay() const
	{
		return m_camera.screenToRay(Cursor::PosF());
	}

	bool isGrabbing() const
	{
		return m_grabbed;
	}

private:

	// 縦方向の視野角（ラジアン）
	double m_verticalFOV = 25_deg;

	// カメラの視点の位置
	Vec3 m_eyePosition{ 16, 16, -16 };

	// カメラの注視点の位置
	Vec3 m_focusPosition{ 0, 0, 0 };

	double m_phi = -20_deg;

	double m_theta = 50_deg;

	// カメラ
	BasicCamera3D m_camera;

	bool m_grabbed = false;

	Vec2 m_cursorPos = Scene::Center();
};

void Main()
{
	// ウインドウとシーンを 1280x720 にリサイズする
	Window::Resize(1280, 720);

	// 環境光を設定する
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.75, 0.75, 0.75 });

	// 太陽光を設定する
	Graphics3D::SetSunColor(ColorF{ 0.5, 0.5, 0.5 });

	// 太陽の方向を設定する
	Graphics3D::SetSunDirection(Vec3{ 0, 1, -0.3 }.normalized());

	// 背景色 (3D 用の色は .removeSRGBCurve() で sRGB カーブを除去）
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

	// 3D シーンを描く、マルチサンプリング対応レンダーテクスチャ
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

	// テーブル用のテクスチャ
	const Texture tableTexture{ CreateTableImage(), TextureDesc::MippedSRGB };

	// ブロック用のテクスチャ
	const Texture blockTexture{ CreateBlockImage(), TextureDesc::MippedSRGB };

	// 盤用の 3D メッシュ
	const Mesh meshBoard{ MeshData::RoundedBox(0.1, Vec3{ 10, 0.4, 10 }, 5).translate(0, -0.2, 0) };

	// カメラ制御
	CameraController cameraController{ renderTexture.size() };

	// ゲームの状態
	Game game;
	game.s[0][0][0] = game.s[1][0][1] = 1;
	game.s[4][0][4] = game.s[5][0][4] = 2;

	while (System::Update())
	{
		////////////////////////////////
		//
		//	状態の更新
		//
		////////////////////////////////
		{
			cameraController.update();
		}

		////////////////////////////////
		//
		//	3D 描画
		//
		////////////////////////////////
		{
			{
				// renderTexture を背景色で塗りつぶし、3D 描画のレンダーターゲットに
				const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

				DrawTable(tableTexture);
				DrawBoard(meshBoard);
				DrawGame(game, blockTexture);
			}

			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 6. 配置ガイド
- ブロックを配置できる場所に半透明のガイドを表示します

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/study/advanced/4/6.png)

```cpp hl_lines="248-249 257-280 299-311"
# include <Siv3D.hpp>

// テーブル用のテクスチャを手続き的に生成する関数
Image CreateTableImage()
{
	PerlinNoise noise;
	return Image::Generate(Size{ 1024, 1024 }, [&](Point p)
	{
		const double x = Fraction(noise.octave2D0_1(p * Vec2{ 0.03, 0.0005 }, 2) * 25) * 0.3 + 0.55;
	return ColorF{ x, 0.85 * x, 0.7 * x }.removeSRGBCurve();
	}).gaussianBlurred(3);
}

// ブロック用のテクスチャを手続き的に生成する関数
Image CreateBlockImage()
{
	PerlinNoise noise;
	return Image::Generate(Size{ 256, 256 }, [&](Point p)
	{
		const double x = Fraction(noise.octave2D0_1(p * Vec2{ 0.05, 0.0005 }, 2) * 25) * 0.15 + 0.85;
	return ColorF{ x }.removeSRGBCurve();
	}).gaussianBlurred(2);
}

// テーブルを描画する関数
void DrawTable(const Texture& tableTexture)
{
	Plane{ Vec3{ 0, -0.4, 0 }, 24 }.draw(tableTexture);
}

// 盤を描画する関数
void DrawBoard(const Mesh& mesh)
{
	const ColorF BoardColor = ColorF{ 0.9, 0.85, 0.75 }.removeSRGBCurve();
	const ColorF LineColor = ColorF{ 0.3, 0.2, 0.0 }.removeSRGBCurve();

	mesh.draw(BoardColor);

	// 盤上の線
	for (int32 i = -4; i <= 4; ++i)
	{
		Line3D{ Vec3{ -4, 0.01, i }, Vec3{ 4, 0.01, i } }.draw(LineColor);
		Line3D{ Vec3{ i, 0.01, -4 }, Vec3{ i, 0.01, 4 } }.draw(LineColor);
	}
	Line3D{ Vec3{ -4.1, 0.01, -4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, 4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ -4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ 4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
}

// 盤上のインデックス (x, y, z) から Box を作成する関数
Box MakeBox(int32 x, int32 y, int32 z)
{
	return Box::FromPoints(Vec3{ (x - 4), (y + 1), (4 - z) }, Vec3{ (x - 3), y, (3 - z) });
}

// ブロックを描く関数
void DrawBlock(int32 x, int32 y, int32 z, const ColorF& color, double scale = 1.0)
{
	MakeBox(x, y, z).scaled(scale).draw(color);
}

// ブロックを描く関数
void DrawBlock(int32 x, int32 y, int32 z, const ColorF& color, const Texture& blockTexture)
{
	MakeBox(x, y, z).draw(blockTexture, color);
}

// ゲームの状態
struct Game
{
	static constexpr int32 MaxY = 15;

	int32 s[8][(MaxY + 1)][8] = {};

	int32 getHeight(int32 x, int32 z) const
	{
		for (int32 y = MaxY; 0 <= y; --y)
		{
			if (s[x][y][z])
			{
				return (y + 1);
			}
		}

		return 0;
	}
};

// ゲームの状態に基づいてブロックを描く関数
void DrawGame(const Game& game, const Texture& blockTexture)
{
	const ColorF BlockColor1 = ColorF{ 1.0, 0.85, 0.6 }.removeSRGBCurve();
	const ColorF BlockColor2 = ColorF{ 0.4, 0.15, 0.15 }.removeSRGBCurve();

	for (int32 y = 0; y <= Game::MaxY; ++y)
	{
		for (int32 x = 0; x < 8; ++x)
		{
			for (int32 z = 0; z < 8; ++z)
			{
				const int32 s = game.s[x][y][z];

				if (s == 1)
				{
					DrawBlock(x, y, z, BlockColor1, blockTexture);
				}
				else if (s == 2)
				{
					DrawBlock(x, y, z, BlockColor2, blockTexture);
				}
			}
		}
	}
}

// カメラの制御クラス
class CameraController
{
public:

	explicit CameraController(const Size& sceneSize)
		: m_camera{ sceneSize, m_verticalFOV, m_eyePosition, m_focusPosition } {}

	void update()
	{
		const Ray ray = getMouseRay();

		// 盤のまわり部分
		const std::array<Box, 4> boxes =
		{
			Box::FromPoints(Vec3{ -5, 0.0, 5 }, Vec3{ 5, -0.4, 4 }),
			Box::FromPoints(Vec3{ 4, 0.0, 5 }, Vec3{ 5, -0.4, -5 }),
			Box::FromPoints(Vec3{ -5, 0.0, -4 }, Vec3{ 5, -0.4, -5 }),
			Box::FromPoints(Vec3{ -5, 0.0, 5 }, Vec3{ -4, -0.4, -5 })
		};

		if (MouseL.up())
		{
			m_grabbed = false;
		}

		if (m_grabbed)
		{
			const double before = (m_cursorPos - Scene::Center()).getAngle();
			const double after = (Cursor::Pos() - Scene::Center()).getAngle();
			m_phi -= (after - before);
			m_cursorPos = Cursor::Pos();
		}

		for (const auto& box : boxes)
		{
			if (box.intersects(ray))
			{
				// マウスカーソルを手のアイコンにする
				Cursor::RequestStyle(CursorStyle::Hand);

				if ((not m_grabbed) && MouseL.down())
				{
					m_grabbed = true;
					m_cursorPos = Cursor::Pos();
				}
			}
		}

		// 視点を球面座標系で計算する
		m_eyePosition = Spherical{ 24, m_theta, (270_deg - m_phi) };

		// カメラを更新する
		m_camera.setView(m_eyePosition, m_focusPosition);

		// シーンにカメラを設定する
		Graphics3D::SetCameraTransform(m_camera);
	}

	Ray getMouseRay() const
	{
		return m_camera.screenToRay(Cursor::PosF());
	}

	bool isGrabbing() const
	{
		return m_grabbed;
	}

private:

	// 縦方向の視野角（ラジアン）
	double m_verticalFOV = 25_deg;

	// カメラの視点の位置
	Vec3 m_eyePosition{ 16, 16, -16 };

	// カメラの注視点の位置
	Vec3 m_focusPosition{ 0, 0, 0 };

	double m_phi = -20_deg;

	double m_theta = 50_deg;

	// カメラ
	BasicCamera3D m_camera;

	bool m_grabbed = false;

	Vec2 m_cursorPos = Scene::Center();
};

void Main()
{
	// ウインドウとシーンを 1280x720 にリサイズする
	Window::Resize(1280, 720);

	// 環境光を設定する
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.75, 0.75, 0.75 });

	// 太陽光を設定する
	Graphics3D::SetSunColor(ColorF{ 0.5, 0.5, 0.5 });

	// 太陽の方向を設定する
	Graphics3D::SetSunDirection(Vec3{ 0, 1, -0.3 }.normalized());

	// 背景色 (3D 用の色は .removeSRGBCurve() で sRGB カーブを除去）
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

	// 3D シーンを描く、マルチサンプリング対応レンダーテクスチャ
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

	// テーブル用のテクスチャ
	const Texture tableTexture{ CreateTableImage(), TextureDesc::MippedSRGB };

	// ブロック用のテクスチャ
	const Texture blockTexture{ CreateBlockImage(), TextureDesc::MippedSRGB };

	// 盤用の 3D メッシュ
	const Mesh meshBoard{ MeshData::RoundedBox(0.1, Vec3{ 10, 0.4, 10 }, 5).translate(0, -0.2, 0) };

	// カメラ制御
	CameraController cameraController{ renderTexture.size() };

	// ゲームの状態
	Game game;
	game.s[0][0][0] = game.s[1][0][1] = 1;
	game.s[4][0][4] = game.s[5][0][4] = 2;

	while (System::Update())
	{
		// アクティブなボクセル
		Point activeVoxelXZ{ -1, -1 };

		////////////////////////////////
		//
		//	状態の更新
		//
		////////////////////////////////
		{
			if (not cameraController.isGrabbing())
			{
				const Ray ray = cameraController.getMouseRay();
				float minDistance = 99999.9f;

				for (int32 x = 0; x < 8; ++x)
				{
					for (int32 z = 0; z < 8; ++z)
					{
						const int32 height = game.getHeight(x, z);

						const Box box = MakeBox(x, height, z);

						if (Optional<float> distacne = box.intersects(ray))
						{
							if (*distacne < minDistance)
							{
								minDistance = *distacne;
								activeVoxelXZ.set(x, z);
							}
						}
					}
				}
			}

			cameraController.update();
		}

		////////////////////////////////
		//
		//	3D 描画
		//
		////////////////////////////////
		{
			{
				// renderTexture を背景色で塗りつぶし、3D 描画のレンダーターゲットに
				const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

				DrawTable(tableTexture);
				DrawBoard(meshBoard);
				DrawGame(game, blockTexture);

				{
					// 半透明を有効に
					const ScopedRenderStates3D blend{ BlendState::OpaqueAlphaToCoverage };

					for (int32 x = 0; x < 8; ++x)
					{
						for (int32 z = 0; z < 8; ++z)
						{
							const int32 height = game.getHeight(x, z);
							DrawBlock(x, height, z, ColorF{ 0.2, 0.8, 0.8, 0.5 }, ((activeVoxelXZ == Point{ x, z }) ? 1.0 : 0.25));
						}
					}
				}
			}

			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}

```


## 7. ブロックを置けるようにする
- 左クリックまたは右クリックでブロックを置けるようにします

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/study/advanced/4/7.png)

```cpp hl_lines="281-293"
# include <Siv3D.hpp>

// テーブル用のテクスチャを手続き的に生成する関数
Image CreateTableImage()
{
	PerlinNoise noise;
	return Image::Generate(Size{ 1024, 1024 }, [&](Point p)
	{
		const double x = Fraction(noise.octave2D0_1(p * Vec2{ 0.03, 0.0005 }, 2) * 25) * 0.3 + 0.55;
	return ColorF{ x, 0.85 * x, 0.7 * x }.removeSRGBCurve();
	}).gaussianBlurred(3);
}

// ブロック用のテクスチャを手続き的に生成する関数
Image CreateBlockImage()
{
	PerlinNoise noise;
	return Image::Generate(Size{ 256, 256 }, [&](Point p)
	{
		const double x = Fraction(noise.octave2D0_1(p * Vec2{ 0.05, 0.0005 }, 2) * 25) * 0.15 + 0.85;
	return ColorF{ x }.removeSRGBCurve();
	}).gaussianBlurred(2);
}

// テーブルを描画する関数
void DrawTable(const Texture& tableTexture)
{
	Plane{ Vec3{ 0, -0.4, 0 }, 24 }.draw(tableTexture);
}

// 盤を描画する関数
void DrawBoard(const Mesh& mesh)
{
	const ColorF BoardColor = ColorF{ 0.9, 0.85, 0.75 }.removeSRGBCurve();
	const ColorF LineColor = ColorF{ 0.3, 0.2, 0.0 }.removeSRGBCurve();

	mesh.draw(BoardColor);

	// 盤上の線
	for (int32 i = -4; i <= 4; ++i)
	{
		Line3D{ Vec3{ -4, 0.01, i }, Vec3{ 4, 0.01, i } }.draw(LineColor);
		Line3D{ Vec3{ i, 0.01, -4 }, Vec3{ i, 0.01, 4 } }.draw(LineColor);
	}
	Line3D{ Vec3{ -4.1, 0.01, -4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, 4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ -4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ 4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
}

// 盤上のインデックス (x, y, z) から Box を作成する関数
Box MakeBox(int32 x, int32 y, int32 z)
{
	return Box::FromPoints(Vec3{ (x - 4), (y + 1), (4 - z) }, Vec3{ (x - 3), y, (3 - z) });
}

// ブロックを描く関数
void DrawBlock(int32 x, int32 y, int32 z, const ColorF& color, double scale = 1.0)
{
	MakeBox(x, y, z).scaled(scale).draw(color);
}

// ブロックを描く関数
void DrawBlock(int32 x, int32 y, int32 z, const ColorF& color, const Texture& blockTexture)
{
	MakeBox(x, y, z).draw(blockTexture, color);
}

// ゲームの状態
struct Game
{
	static constexpr int32 MaxY = 15;

	int32 s[8][(MaxY + 1)][8] = {};

	int32 getHeight(int32 x, int32 z) const
	{
		for (int32 y = MaxY; 0 <= y; --y)
		{
			if (s[x][y][z])
			{
				return (y + 1);
			}
		}

		return 0;
	}
};

// ゲームの状態に基づいてブロックを描く関数
void DrawGame(const Game& game, const Texture& blockTexture)
{
	const ColorF BlockColor1 = ColorF{ 1.0, 0.85, 0.6 }.removeSRGBCurve();
	const ColorF BlockColor2 = ColorF{ 0.4, 0.15, 0.15 }.removeSRGBCurve();

	for (int32 y = 0; y <= Game::MaxY; ++y)
	{
		for (int32 x = 0; x < 8; ++x)
		{
			for (int32 z = 0; z < 8; ++z)
			{
				const int32 s = game.s[x][y][z];

				if (s == 1)
				{
					DrawBlock(x, y, z, BlockColor1, blockTexture);
				}
				else if (s == 2)
				{
					DrawBlock(x, y, z, BlockColor2, blockTexture);
				}
			}
		}
	}
}

// カメラの制御クラス
class CameraController
{
public:

	explicit CameraController(const Size& sceneSize)
		: m_camera{ sceneSize, m_verticalFOV, m_eyePosition, m_focusPosition } {}

	void update()
	{
		const Ray ray = getMouseRay();

		// 盤のまわり部分
		const std::array<Box, 4> boxes =
		{
			Box::FromPoints(Vec3{ -5, 0.0, 5 }, Vec3{ 5, -0.4, 4 }),
			Box::FromPoints(Vec3{ 4, 0.0, 5 }, Vec3{ 5, -0.4, -5 }),
			Box::FromPoints(Vec3{ -5, 0.0, -4 }, Vec3{ 5, -0.4, -5 }),
			Box::FromPoints(Vec3{ -5, 0.0, 5 }, Vec3{ -4, -0.4, -5 })
		};

		if (MouseL.up())
		{
			m_grabbed = false;
		}

		if (m_grabbed)
		{
			const double before = (m_cursorPos - Scene::Center()).getAngle();
			const double after = (Cursor::Pos() - Scene::Center()).getAngle();
			m_phi -= (after - before);
			m_cursorPos = Cursor::Pos();
		}

		for (const auto& box : boxes)
		{
			if (box.intersects(ray))
			{
				// マウスカーソルを手のアイコンにする
				Cursor::RequestStyle(CursorStyle::Hand);

				if ((not m_grabbed) && MouseL.down())
				{
					m_grabbed = true;
					m_cursorPos = Cursor::Pos();
				}
			}
		}

		// 視点を球面座標系で計算する
		m_eyePosition = Spherical{ 24, m_theta, (270_deg - m_phi) };

		// カメラを更新する
		m_camera.setView(m_eyePosition, m_focusPosition);

		// シーンにカメラを設定する
		Graphics3D::SetCameraTransform(m_camera);
	}

	Ray getMouseRay() const
	{
		return m_camera.screenToRay(Cursor::PosF());
	}

	bool isGrabbing() const
	{
		return m_grabbed;
	}

private:

	// 縦方向の視野角（ラジアン）
	double m_verticalFOV = 25_deg;

	// カメラの視点の位置
	Vec3 m_eyePosition{ 16, 16, -16 };

	// カメラの注視点の位置
	Vec3 m_focusPosition{ 0, 0, 0 };

	double m_phi = -20_deg;

	double m_theta = 50_deg;

	// カメラ
	BasicCamera3D m_camera;

	bool m_grabbed = false;

	Vec2 m_cursorPos = Scene::Center();
};

void Main()
{
	// ウインドウとシーンを 1280x720 にリサイズする
	Window::Resize(1280, 720);

	// 環境光を設定する
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.75, 0.75, 0.75 });

	// 太陽光を設定する
	Graphics3D::SetSunColor(ColorF{ 0.5, 0.5, 0.5 });

	// 太陽の方向を設定する
	Graphics3D::SetSunDirection(Vec3{ 0, 1, -0.3 }.normalized());

	// 背景色 (3D 用の色は .removeSRGBCurve() で sRGB カーブを除去）
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

	// 3D シーンを描く、マルチサンプリング対応レンダーテクスチャ
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

	// テーブル用のテクスチャ
	const Texture tableTexture{ CreateTableImage(), TextureDesc::MippedSRGB };

	// ブロック用のテクスチャ
	const Texture blockTexture{ CreateBlockImage(), TextureDesc::MippedSRGB };

	// 盤用の 3D メッシュ
	const Mesh meshBoard{ MeshData::RoundedBox(0.1, Vec3{ 10, 0.4, 10 }, 5).translate(0, -0.2, 0) };

	// カメラ制御
	CameraController cameraController{ renderTexture.size() };

	// ゲームの状態
	Game game;
	game.s[0][0][0] = game.s[1][0][1] = 1;
	game.s[4][0][4] = game.s[5][0][4] = 2;

	while (System::Update())
	{
		// アクティブなボクセル
		Point activeVoxelXZ{ -1, -1 };

		////////////////////////////////
		//
		//	状態の更新
		//
		////////////////////////////////
		{
			if (not cameraController.isGrabbing())
			{
				const Ray ray = cameraController.getMouseRay();
				float minDistance = 99999.9f;

				for (int32 x = 0; x < 8; ++x)
				{
					for (int32 z = 0; z < 8; ++z)
					{
						const int32 height = game.getHeight(x, z);

						const Box box = MakeBox(x, height, z);

						if (Optional<float> distacne = box.intersects(ray))
						{
							if (*distacne < minDistance)
							{
								minDistance = *distacne;
								activeVoxelXZ.set(x, z);
							}
						}
					}
				}

				if (activeVoxelXZ != Point{ -1, -1 })
				{
					auto& voxel = game.s[activeVoxelXZ.x][game.getHeight(activeVoxelXZ.x, activeVoxelXZ.y)][activeVoxelXZ.y];

					if (MouseL.down())
					{
						voxel = 1;
					}
					else if (MouseR.down())
					{
						voxel = 2;
					}
				}
			}

			cameraController.update();
		}

		////////////////////////////////
		//
		//	3D 描画
		//
		////////////////////////////////
		{
			{
				// renderTexture を背景色で塗りつぶし、3D 描画のレンダーターゲットに
				const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

				DrawTable(tableTexture);
				DrawBoard(meshBoard);
				DrawGame(game, blockTexture);

				{
					// 半透明を有効に
					const ScopedRenderStates3D blend{ BlendState::OpaqueAlphaToCoverage };

					for (int32 x = 0; x < 8; ++x)
					{
						for (int32 z = 0; z < 8; ++z)
						{
							const int32 height = game.getHeight(x, z);
							DrawBlock(x, height, z, ColorF{ 0.2, 0.8, 0.8, 0.5 }, ((activeVoxelXZ == Point{ x, z }) ? 1.0 : 0.25));
						}
					}
				}
			}

			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}
	}
}
```


## 8. 盤面の片付け機能
- 盤面の片付け機能を実装します

![](https://raw.githubusercontent.com/Reputeless/lecture-files/main/study/advanced/4/8.png)

```cpp hl_lines="333-343"
# include <Siv3D.hpp>

// テーブル用のテクスチャを手続き的に生成する関数
Image CreateTableImage()
{
	PerlinNoise noise;
	return Image::Generate(Size{ 1024, 1024 }, [&](Point p)
	{
		const double x = Fraction(noise.octave2D0_1(p * Vec2{ 0.03, 0.0005 }, 2) * 25) * 0.3 + 0.55;
	return ColorF{ x, 0.85 * x, 0.7 * x }.removeSRGBCurve();
	}).gaussianBlurred(3);
}

// ブロック用のテクスチャを手続き的に生成する関数
Image CreateBlockImage()
{
	PerlinNoise noise;
	return Image::Generate(Size{ 256, 256 }, [&](Point p)
	{
		const double x = Fraction(noise.octave2D0_1(p * Vec2{ 0.05, 0.0005 }, 2) * 25) * 0.15 + 0.85;
	return ColorF{ x }.removeSRGBCurve();
	}).gaussianBlurred(2);
}

// テーブルを描画する関数
void DrawTable(const Texture& tableTexture)
{
	Plane{ Vec3{ 0, -0.4, 0 }, 24 }.draw(tableTexture);
}

// 盤を描画する関数
void DrawBoard(const Mesh& mesh)
{
	const ColorF BoardColor = ColorF{ 0.9, 0.85, 0.75 }.removeSRGBCurve();
	const ColorF LineColor = ColorF{ 0.3, 0.2, 0.0 }.removeSRGBCurve();

	mesh.draw(BoardColor);

	// 盤上の線
	for (int32 i = -4; i <= 4; ++i)
	{
		Line3D{ Vec3{ -4, 0.01, i }, Vec3{ 4, 0.01, i } }.draw(LineColor);
		Line3D{ Vec3{ i, 0.01, -4 }, Vec3{ i, 0.01, 4 } }.draw(LineColor);
	}
	Line3D{ Vec3{ -4.1, 0.01, -4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, 4.1 } }.draw(LineColor);
	Line3D{ Vec3{ -4.1, 0.01, 4.1 }, Vec3{ -4.1, 0.01, -4.1 } }.draw(LineColor);
	Line3D{ Vec3{ 4.1, 0.01, 4.1 }, Vec3{ 4.1, 0.01, -4.1 } }.draw(LineColor);
}

// 盤上のインデックス (x, y, z) から Box を作成する関数
Box MakeBox(int32 x, int32 y, int32 z)
{
	return Box::FromPoints(Vec3{ (x - 4), (y + 1), (4 - z) }, Vec3{ (x - 3), y, (3 - z) });
}

// ブロックを描く関数
void DrawBlock(int32 x, int32 y, int32 z, const ColorF& color, double scale = 1.0)
{
	MakeBox(x, y, z).scaled(scale).draw(color);
}

// ブロックを描く関数
void DrawBlock(int32 x, int32 y, int32 z, const ColorF& color, const Texture& blockTexture)
{
	MakeBox(x, y, z).draw(blockTexture, color);
}

// ゲームの状態
struct Game
{
	static constexpr int32 MaxY = 15;

	int32 s[8][(MaxY + 1)][8] = {};

	int32 getHeight(int32 x, int32 z) const
	{
		for (int32 y = MaxY; 0 <= y; --y)
		{
			if (s[x][y][z])
			{
				return (y + 1);
			}
		}

		return 0;
	}
};

// ゲームの状態に基づいてブロックを描く関数
void DrawGame(const Game& game, const Texture& blockTexture)
{
	const ColorF BlockColor1 = ColorF{ 1.0, 0.85, 0.6 }.removeSRGBCurve();
	const ColorF BlockColor2 = ColorF{ 0.4, 0.15, 0.15 }.removeSRGBCurve();

	for (int32 y = 0; y <= Game::MaxY; ++y)
	{
		for (int32 x = 0; x < 8; ++x)
		{
			for (int32 z = 0; z < 8; ++z)
			{
				const int32 s = game.s[x][y][z];

				if (s == 1)
				{
					DrawBlock(x, y, z, BlockColor1, blockTexture);
				}
				else if (s == 2)
				{
					DrawBlock(x, y, z, BlockColor2, blockTexture);
				}
			}
		}
	}
}

// カメラの制御クラス
class CameraController
{
public:

	explicit CameraController(const Size& sceneSize)
		: m_camera{ sceneSize, m_verticalFOV, m_eyePosition, m_focusPosition } {}

	void update()
	{
		const Ray ray = getMouseRay();

		// 盤のまわり部分
		const std::array<Box, 4> boxes =
		{
			Box::FromPoints(Vec3{ -5, 0.0, 5 }, Vec3{ 5, -0.4, 4 }),
			Box::FromPoints(Vec3{ 4, 0.0, 5 }, Vec3{ 5, -0.4, -5 }),
			Box::FromPoints(Vec3{ -5, 0.0, -4 }, Vec3{ 5, -0.4, -5 }),
			Box::FromPoints(Vec3{ -5, 0.0, 5 }, Vec3{ -4, -0.4, -5 })
		};

		if (MouseL.up())
		{
			m_grabbed = false;
		}

		if (m_grabbed)
		{
			const double before = (m_cursorPos - Scene::Center()).getAngle();
			const double after = (Cursor::Pos() - Scene::Center()).getAngle();
			m_phi -= (after - before);
			m_cursorPos = Cursor::Pos();
		}

		for (const auto& box : boxes)
		{
			if (box.intersects(ray))
			{
				// マウスカーソルを手のアイコンにする
				Cursor::RequestStyle(CursorStyle::Hand);

				if ((not m_grabbed) && MouseL.down())
				{
					m_grabbed = true;
					m_cursorPos = Cursor::Pos();
				}
			}
		}

		// 視点を球面座標系で計算する
		m_eyePosition = Spherical{ 24, m_theta, (270_deg - m_phi) };

		// カメラを更新する
		m_camera.setView(m_eyePosition, m_focusPosition);

		// シーンにカメラを設定する
		Graphics3D::SetCameraTransform(m_camera);
	}

	Ray getMouseRay() const
	{
		return m_camera.screenToRay(Cursor::PosF());
	}

	bool isGrabbing() const
	{
		return m_grabbed;
	}

private:

	// 縦方向の視野角（ラジアン）
	double m_verticalFOV = 25_deg;

	// カメラの視点の位置
	Vec3 m_eyePosition{ 16, 16, -16 };

	// カメラの注視点の位置
	Vec3 m_focusPosition{ 0, 0, 0 };

	double m_phi = -20_deg;

	double m_theta = 50_deg;

	// カメラ
	BasicCamera3D m_camera;

	bool m_grabbed = false;

	Vec2 m_cursorPos = Scene::Center();
};

void Main()
{
	// ウインドウとシーンを 1280x720 にリサイズする
	Window::Resize(1280, 720);

	// 環境光を設定する
	Graphics3D::SetGlobalAmbientColor(ColorF{ 0.75, 0.75, 0.75 });

	// 太陽光を設定する
	Graphics3D::SetSunColor(ColorF{ 0.5, 0.5, 0.5 });

	// 太陽の方向を設定する
	Graphics3D::SetSunDirection(Vec3{ 0, 1, -0.3 }.normalized());

	// 背景色 (3D 用の色は .removeSRGBCurve() で sRGB カーブを除去）
	const ColorF backgroundColor = ColorF{ 0.4, 0.6, 0.8 }.removeSRGBCurve();

	// 3D シーンを描く、マルチサンプリング対応レンダーテクスチャ
	const MSRenderTexture renderTexture{ Scene::Size(), TextureFormat::R8G8B8A8_Unorm_SRGB, HasDepth::Yes };

	// テーブル用のテクスチャ
	const Texture tableTexture{ CreateTableImage(), TextureDesc::MippedSRGB };

	// ブロック用のテクスチャ
	const Texture blockTexture{ CreateBlockImage(), TextureDesc::MippedSRGB };

	// 盤用の 3D メッシュ
	const Mesh meshBoard{ MeshData::RoundedBox(0.1, Vec3{ 10, 0.4, 10 }, 5).translate(0, -0.2, 0) };

	// カメラ制御
	CameraController cameraController{ renderTexture.size() };

	// ゲームの状態
	Game game;
	game.s[0][0][0] = game.s[1][0][1] = 1;
	game.s[4][0][4] = game.s[5][0][4] = 2;

	while (System::Update())
	{
		// アクティブなボクセル
		Point activeVoxelXZ{ -1, -1 };

		////////////////////////////////
		//
		//	状態の更新
		//
		////////////////////////////////
		{
			if (not cameraController.isGrabbing())
			{
				const Ray ray = cameraController.getMouseRay();
				float minDistance = 99999.9f;

				for (int32 x = 0; x < 8; ++x)
				{
					for (int32 z = 0; z < 8; ++z)
					{
						const int32 height = game.getHeight(x, z);

						const Box box = MakeBox(x, height, z);

						if (Optional<float> distacne = box.intersects(ray))
						{
							if (*distacne < minDistance)
							{
								minDistance = *distacne;
								activeVoxelXZ.set(x, z);
							}
						}
					}
				}

				if (activeVoxelXZ != Point{ -1, -1 })
				{
					auto& voxel = game.s[activeVoxelXZ.x][game.getHeight(activeVoxelXZ.x, activeVoxelXZ.y)][activeVoxelXZ.y];

					if (MouseL.down())
					{
						voxel = 1;
					}
					else if (MouseR.down())
					{
						voxel = 2;
					}
				}
			}

			cameraController.update();
		}

		////////////////////////////////
		//
		//	3D 描画
		//
		////////////////////////////////
		{
			{
				// renderTexture を背景色で塗りつぶし、3D 描画のレンダーターゲットに
				const ScopedRenderTarget3D target{ renderTexture.clear(backgroundColor) };

				DrawTable(tableTexture);
				DrawBoard(meshBoard);
				DrawGame(game, blockTexture);

				{
					// 半透明を有効に
					const ScopedRenderStates3D blend{ BlendState::OpaqueAlphaToCoverage };

					for (int32 x = 0; x < 8; ++x)
					{
						for (int32 z = 0; z < 8; ++z)
						{
							const int32 height = game.getHeight(x, z);
							DrawBlock(x, height, z, ColorF{ 0.2, 0.8, 0.8, 0.5 }, ((activeVoxelXZ == Point{ x, z }) ? 1.0 : 0.25));
						}
					}
				}
			}

			Graphics3D::Flush();
			renderTexture.resolve();
			Shader::LinearToScreen(renderTexture);
		}

		////////////////////////////////
		//
		//	2D 描画
		//
		////////////////////////////////
		{
			if (SimpleGUI::Button(U"片づける", Vec2{ 1100, 20 }, 160))
			{
				game = Game{};
			}
		}
	}
}
```
