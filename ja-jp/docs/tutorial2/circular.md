# XX. XXXXX

## XX.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/xxxx/1.png)

```cpp

```


## 26.23 円座標
座標の指定においては、X 軸 Y 軸を使った座標系ではなく、基準点からの角度と距離に基づいた円座標を使うと便利な場合があります。以下は `Vec2` 型に変換可能な円座標クラスです。

| クラス | 説明 |
|---|---|
| `Circular{ r, theta }` | 原点を中心とする半径 `double r` の円を考え、その円周上で 12 時の方向を 0° として時計回りに `double theta` （ラジアン）の位置を表します。`Vec2` に変換できます。 |
| `OffsetCircular{ offset, r, theta }` | `Vec2 offset` を中心とする半径 `double r` の円を考え、その円周上で 12 時の方向を 0° として時計回りに `double theta` （ラジアン）の位置を表します。`Vec2` に変換できます。 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/23a.png)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape/23b.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		const double t = Scene::Time();

		for (int32 i = 0; i < 12; ++i)
		{
			const double theta = (i * 30_deg);

			// (400, 300) を中心とする半径 200 の円周上で、角度 theta の位置にある点の座標を計算する
			const Vec2 pos = OffsetCircular{ Vec2{ 400, 300 }, 200, theta };

			Circle{ pos, 20 }.draw(HSV{ i * 30 });
		}
	}
}
```