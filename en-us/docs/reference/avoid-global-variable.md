# Why Some Siv3D Classes Cannot Be Global

## Description

In Siv3D programming, the use of global variables is unnecessary and should be avoided.

Additionally, Siv3D has classes that cannot be created as global variables. Creating the following classes, or classes that contain the following classes as member variables, as global variables will cause runtime errors by attempting to access engine functionality before engine initialization.

- `Texture`
- `DynamicTexture`
- `RenderTexture`
- `MSRenderTexture`
- `Font`
- `Audio`
- `Effect`
- `Mesh`
- `Model`
- `VertexShader`
- `PixelShader`
- `Script`

## Alternative Method (1)
By making them member variables of classes instantiated within the Main function, or by passing them as function arguments, you can use the above classes in various parts of your program without using global variables.

```cpp
# include <Siv3D.hpp>

class Game
{
public:

	void draw() const
	{
		m_cat.drawAt(100, 100);
		m_dog.drawAt(300, 300);
	}

private:

	Texture m_cat = Texture{ U"🐈"_emoji };

	Texture m_dog = Texture{ U"🐕"_emoji };
};

struct Faces
{
	Array<Texture> textures;

	Faces()
	{
		textures << Texture{ U"😊"_emoji };
		textures << Texture{ U"🤔"_emoji };
		textures << Texture{ U"🤣"_emoji };
	}
};

void DrawFace(const Faces& faces)
{
	const int32 index = (Time::GetSec() % 3);

	faces.textures[index].drawAt(200, 200);
}

void Main()
{
	Game game; // Texture is created here

	Faces faces; // Texture is created here

	while (System::Update())
	{
		game.draw();

		DrawFace(faces);
	}
}
```

## Alternative Method (2)
For `Texture`, `Audio`, `Font`, `VertexShader`, and `PixelShader`, you can access specific assets from various parts of your program by using the asset management functionality.

```cpp
# include <Siv3D.hpp>

void Draw()
{
	// Use texture assets through the asset management functionality
	TextureAsset(U"Windmill").draw();
	TextureAsset(U"Siv3D-kun").scaled(0.8).drawAt(200, 200);
}

void Main()
{
	// Register texture assets with the asset management functionality
	TextureAsset::Register(U"Windmill", U"example/windmill.png", TextureDesc::Mipped);
	TextureAsset::Register(U"Siv3D-kun", U"example/siv3d-kun.png", TextureDesc::Mipped);

	while (System::Update())
	{
		Draw();
	}
}
```