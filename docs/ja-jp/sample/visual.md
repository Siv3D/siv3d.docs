
# ビジュアル表現

## 紙から切り抜いたような描画
![](images/visual-paper-cut.png)
```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(1.0, 0.9, 0.7));

	constexpr Vec2 pos(220, 60);

	const Image image(U"example/siv3d-kun.png");

	const Texture texture(image);

	// 画像の輪郭から Polygon を作成
	const Polygon polygon = image.alphaToPolygon(160, false);

	// 凸包を計算
	const Polygon convexHull = polygon.calculateConvexHull();

	// Polygon を太らせる
	const Polygon largeConvex = convexHull.calculateBuffer(20);

	// 影用の画像
	Image shadowImage(Scene::Size(), Color(255, 0));

	// 影のもとになる図形を書き込む
	convexHull.calculateBuffer(10).movedBy(pos + Vec2(10, 10)).overwrite(shadowImage, Color(255));

	// それをぼかしたものを影用テクスチャにする
	const Texture shadow(shadowImage.gaussianBlurred(40, 40));

	while (System::Update())
	{
		shadow.draw(ColorF(0.0, 0.5));

		largeConvex.draw(pos, ColorF(0.96, 0.98, 1.0));

		texture.draw(pos);
	}
}
```


