# グローバル変数として作成できないクラス

## 説明

Siv3D プログラミングにおいて、グローバル変数の使用は不必要なので避けるべきです。

また、Siv3D にはグローバル変数として作成できないクラスがあります。以下のクラスや、以下のクラスをメンバ変数に含むクラスをグローバル変数にすると、エンジンの初期化前にエンジン機能にアクセスしようとして実行時エラーが発生します。

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

## 代わりの方法 (1)
Main 関数内でインスタンス化するクラスのメンバ変数としたり、関数の引数として渡したりすることで、グローバル変数を使用することなく、プログラムの様々な場所で上記のクラスを利用できます。

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
	Game game; // ここで Texture が作成される

	Faces faces; // ここで Texture が作成される

	while (System::Update())
	{
		game.draw();

		DrawFace(faces);
	}
}
```

## 代わりの方法 (2)
`Texture`, `Audio`, `Font`, `VertexShader`, `PixelShader` に関しては、アセット管理機能を使うことで、プログラムの様々な場所で特定のアセットにアクセスすることができます。

```cpp
# include <Siv3D.hpp>

void Draw()
{
	// アセット管理機能を通してテクスチャアセットを使用する
	TextureAsset(U"Windmill").draw();
	TextureAsset(U"Siv3D-kun").scaled(0.8).drawAt(200, 200);
}

void Main()
{
	// アセット管理機能にテクスチャアセットを登録する
	TextureAsset::Register(U"Windmill", U"example/windmill.png", TextureDesc::Mipped);
	TextureAsset::Register(U"Siv3D-kun", U"example/siv3d-kun.png", TextureDesc::Mipped);

	while (System::Update())
	{
		Draw();
	}
}
```
