# 68. 2D 物理演算
2D 物理演算機能を使う方法を学びます。


複数のクラスが登場するので、最初に整理します。

| クラス | 説明 |
| --- | --- |
| `P2World` | 2D 物理演算のワールドです。通常は 1 つだけ作成します。 |
| `P2Body` | ワールドに存在する物体です。0 個以上（通常は 1 個以上）の部品 `P2Shape` から構成されます。 |
| `P2BodyID` | 物体 `P2Body` に発行される一意な ID です。 |
| `P2BodyType` | 物体が動的か静的かを表す列挙型です。 |
| `P2Shape` | 物体 `P2Body` を構成する部品のインタフェースです。 |
| `P2ShapeType` | 部品 `P2Shape` の種類を表す列挙型です。 |
| `P2Circle` | 部品 `P2Shape` の 1 つで、円を表します。 |
| `P2Rect` | 部品 `P2Shape` の 1 つで、長方形を表します。 |
| `P2Triangle` | 部品 `P2Shape` の 1 つで、三角形を表します。 |
| `P2Quad` | 部品 `P2Shape` の 1 つで、凸な四角形を表します。 |
| `P2Polygon` | 部品 `P2Shape` の 1 つで、多角形を表します。 |
| `P2Line` | 部品 `P2Shape` の 1 つで、線分を表します。 |
| `P2LineString` | 部品 `P2Shape` の 1 つで、連続する線分の集合を表します。 |
| `P2Material` | 部品 `P2Shape` の材質（物理的特性）を表すクラスです。 |
| `P2Filter` | 部品 `P2Shape` にカテゴリビットフラグを指定し、特定のビットフラグを持つ部品と干渉しないようにできます。 |
| `P2Collision` | 2 つの物体にはたらく全ての接触に関する情報です。最大 2 つの `P2Contact` を持ちます。 |
| `P2Contact` | 2 つの物体にはたらく接触に関する情報です。 |
| `P2ContactPair` | 2 つの物体が接触しているときのそれらの `P2BodyID` のペアです。 |
| `P2PivotJoint` | 2 つの物体を接続するジョイントの一種です。 |
| `P2DistanceJoint` | 2 つの物体を接続するジョイントの一種です。 |
| `P2SliderJoint` | 2 つの物体を接続するジョイントの一種です。 |
| `P2WheelJoint` | 2 つの物体を接続するジョイントの一種です。 |
| `P2MouseJoint` | 2 つの物体を接続するジョイントの一種です。 |


## 68.1 ワールドと更新
物理演算を行う仮想のワールド `P2World` を作成します。ワールドの状態は `.update()` で更新できます。更新頻度が高いほど、物理演算の精度が上がりますが、より計算コストが高くなります。通常は 200 回/秒で更新するのが良いでしょう。

シーンが 60FPS で更新される場合、1 フレームで 2 回以上のワールドの更新をするということになります。

```cpp hl_lines="7-14 18-22"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double StepTime = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatedTime = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			// 2D 物理演算のワールドを StepTime 秒進める
			world.update(StepTime);
		}
	}
}
```


## 68.2 動的な物体
`world.createCircle(type, center, r)` で、ワールドの `center` cm の位置に半径 `r` cm の円を部品とする物体を作成します。戻り値は `P2Body` で、これを通して物体の状態を取得したり、変更したりします。

`type` では物体の種類を表します。力の影響を受ける動的な物体を作成する場合は `P2Dynamic` を指定します。今回は重力の影響を受けるように `P2Dynamic` を指定します。

重力加速度は、デフォルトでは地球と同じ `Vec2(0, 980)` cm/s^2 です。

ワールドの座標の単位は cm です。また、描画と同じで下に行くほど y 座標が大きくなるため、高さ 300 cm の位置に物体を作成するには、`Vec2(0, -300)` を指定します。

次のコードを実行すると、時間の経過とともに物体が落下していく様子が確認できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/2.png)

```cpp hl_lines="16-17 21-24"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double StepTime = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatedTime = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	// 300 cm の高さに物体（半径 10cm の円）を作成する
	P2Body body = world.createCircle(P2Dynamic, Vec2{ 0, -300 }, 10);

	while (System::Update())
	{
		ClearPrint();

		// 物体の座標を出力する
		Print << U"{:.1f} cm"_fmt(body.getPos());

		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			// 2D 物理演算のワールドを StepTime 秒進める
			world.update(StepTime);
		}
	}
}
```


## 68.3 物体の削除 (1)
ワールドに存在する物体が多くなると、CPU の計算コストやメモリの使用量が増えていきます。ゲームのエリア外に出た物体は、ワールドから削除するようにしましょう。

`P2Body` の `.release()` で物体をワールドから削除できます。削除された物体は、以降の更新で無視されます。

`P2Body` は `bool` に暗黙的に変換できます。物体が存在する場合は `true` に、存在しない場合は `false` になります。

物体の状態のチェックは、次のコードのように、1 回の `world.update()` ごとに行うことが望ましいです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/3.png)

```cpp hl_lines="23-28 35-40"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double StepTime = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatedTime = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	// 300 cm の高さに物体（半径 10cm の円）を作成する
	P2Body body = world.createCircle(P2Dynamic, Vec2{ 0, -300 }, 10);

	while (System::Update())
	{
		ClearPrint();

		// 物体が存在する場合
		if (body)
		{
			// 物体の座標を出力する
			Print << U"{:.1f} cm"_fmt(body.getPos());
		}

		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			// 2D 物理演算のワールドを StepTime 秒進める
			world.update(StepTime);

			// 物体が存在し、物体が地面の下に 500 cm 以上落下した場合
			if (body && (500 < body.getPos().y))
			{
				// 物体をワールドから削除する
				body.release();
			}
		}
	}
}
```


## 68.4 物体の削除 (2)
複数の物体を扱う場合は `Array<P2Body>` を使うと便利です。配列から削除された `P2Body` は自動的にワールドから削除されます。

物体には一意の ID が割り振られています。`.id()` で ID を取得できます。

次のコードを実行すると、時間の経過とともにゲームのエリア外に出た物体が削除されていく様子が確認できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/4.png)

```cpp hl_lines="16-20 26-29 36-37"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double StepTime = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatedTime = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	// 物体（半径 10cm の円）を 3 つ作成する
	Array<P2Body> bodies;
	bodies << world.createCircle(P2Dynamic, Vec2{ -100, -300 }, 10);
	bodies << world.createCircle(P2Dynamic, Vec2{ 0, -600 }, 10);
	bodies << world.createCircle(P2Dynamic, Vec2{ 100, -900 }, 10);

	while (System::Update())
	{
		ClearPrint();

		for (const auto& body : bodies)
		{
			Print << U"ID: {}, {:.1f} cm"_fmt(body.id(), body.getPos());
		}

		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			// 2D 物理演算のワールドを StepTime 秒進める
			world.update(StepTime);

			// 地面の下に 500 cm 以上落下した物体を削除する
			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}
	}
}
```


## 68.5 物体の描画と 2D カメラ
`P2Body` を `.draw()` すると、形状と状態（位置など）に基づき、物体を画面に描画できます。

39 章で学んだ 2D カメラと組み合わせると、ワールドを柔軟な視点（中心座標、拡大率）で描画でき便利です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/5.png)

```cpp hl_lines="22-23 43-57"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double StepTime = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatedTime = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	// 物体（半径 10cm の円）を 3 つ作成する
	Array<P2Body> bodies;
	bodies << world.createCircle(P2Dynamic, Vec2{ -100, -300 }, 10);
	bodies << world.createCircle(P2Dynamic, Vec2{ 0, -600 }, 10);
	bodies << world.createCircle(P2Dynamic, Vec2{ 100, -900 }, 10);

	// 2D カメラ（中心座標 (0, -300), 拡大率 1.0）
	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		ClearPrint();

		for (const auto& body : bodies)
		{
			Print << U"ID: {}, {:.1f} cm"_fmt(body.id(), body.getPos());
		}

		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			// 2D 物理演算のワールドを StepTime 秒進める
			world.update(StepTime);

			// 地面の下に 500 cm 以上落下した物体を削除する
			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}

		// 2D カメラを更新する
		camera.update();
		{
			// 2D カメラから Transformer2D を作成する
			const auto t = camera.createTransformer();

			// すべてのボディを描画する
			for (const auto& body : bodies)
			{
				body.draw(HSV{ body.id() * 10.0 });
			}
		}

		// 2D カメラの操作を描画する
		camera.draw(Palette::Orange);
	}
}
```


## 68.6 静的な物体
ワールドに固定の床を作成します。`world.createRect(type, center, size);` で、ワールドの `center` cm を中心としサイズが `size` cm である長方形を部品とする物体を作成します。

`type` では物体の種類を表します。常に固定され、力の影響を受けない床や壁のような物体を作成する場合は `P2Static` を指定します。今回は固定の床を作るため `P2Static` を指定します。

次のコードを実行すると、落下した円は、原点からの高さが -15.1 cm 前後のところで止まります。床は原点から上方向に厚みが 5 cm あり、円の半径は 10 cm なので -15 cm の位置になります。さらに、物体間にはシミュレーションを安定化させるための小さな隙間が自動的に挿入されるため、実際には -15.1 cm 前後になります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/6.png)

```cpp hl_lines="16-17 52-53"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double StepTime = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatedTime = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	// 地面 (幅 1000 cm, 高さ 10 cm の長方形）
	const P2Body ground = world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });

	// 物体（半径 10cm の円）を 3 つ作成する
	Array<P2Body> bodies;
	bodies << world.createCircle(P2Dynamic, Vec2{ -100, -300 }, 10);
	bodies << world.createCircle(P2Dynamic, Vec2{ 0, -600 }, 10);
	bodies << world.createCircle(P2Dynamic, Vec2{ 100, -900 }, 10);

	// 2D カメラ（中心座標 (0, -300), 拡大率 1.0）
	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		ClearPrint();

		for (const auto& body : bodies)
		{
			Print << U"ID: {}, {:.1f} cm"_fmt(body.id(), body.getPos());
		}

		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			// 2D 物理演算のワールドを StepTime 秒進める
			world.update(StepTime);

			// 地面の下に 500 cm 以上落下した物体を削除する
			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}

		// 2D カメラを更新する
		camera.update();
		{
			// 2D カメラから Transformer2D を作成する
			const auto t = camera.createTransformer();

			// 地面を描画する
			ground.draw(Palette::Gray);

			// すべてのボディを描画する
			for (const auto& body : bodies)
			{
				body.draw(HSV{ body.id() * 10.0 });
			}
		}

		// 2D カメラの操作を描画する
		camera.draw(Palette::Orange);
	}
}
```


## 68.7 様々な形の部品
`Circle`, `RectF`, `Triangle`, `Quad`, `Polygon` を部品とする物体を作成できます。また、`P2Static` 専用で、`Line`, `LineString` 形状を部品とする物体も作成できます。

| 部品の形状 | 物体作成関数 | P2Dynamic にできるか |
| --- | --- |:---:|
| 円 | `world.createCircle(type, center, r)` | :material-check: |
| 長方形 | `world.createRect(type, center, size)` | :material-check: |
| 三角形 | `world.createTriangle(type, center, triangle)` | :material-check: |
| 凸な四角形 | `world.createQuad(type, center, quad)` | :material-check: |
| 多角形 | `world.createPolygon(type, center, polygon)` | :material-check: |
| 線分 | `world.createLine(type, center, line)` |  |
| 線分の集合 | `world.createLineString(type, center, lineString)` |  |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/7.png)

```cpp hl_lines="16-22 29-30 37-38 47-57 63-87"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double StepTime = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatedTime = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	// 地面
	Array<P2Body> grounds;
	grounds << world.createRect(P2Static, Vec2{ 0, -200 }, SizeF{ 600, 20 });
	grounds << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -500, -150, -300, -50 });
	grounds << world.createLineString(P2Static, Vec2{ 0, 0 }, LineString{ Vec2{ 100, -50 }, Vec2{ 200, -50 }, Vec2{ 600, -150 } });

	Array<P2Body> bodies;

	// 2D カメラ（中心座標 (0, -300), 拡大率 1.0）
	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		ClearPrint();
		Print << U"bodies.size(): " << bodies.size() << U"\n";

		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			// 2D 物理演算のワールドを StepTime 秒進める
			world.update(StepTime);

			// 地面の下に 500 cm 以上落下した物体を削除する
			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}

		// 2D カメラを更新する
		camera.update();
		{
			// 2D カメラから Transformer2D を作成する
			const auto t = camera.createTransformer();

			// すべての地面を描画する
			for (const auto& ground : grounds)
			{
				ground.draw(Palette::Gray);
			}

			// すべてのボディを描画する
			for (const auto& body : bodies)
			{
				body.draw(HSV{ body.id() * 10.0 });
			}
		}

		// 2D カメラの操作を描画する
		camera.draw(Palette::Orange);

		if (SimpleGUI::Button(U"Circle", Vec2{ 40, 80 }, 120))
		{
			bodies << world.createCircle(P2Dynamic, Vec2{ Random(-400, 400), -600 }, 20);
		}

		if (SimpleGUI::Button(U"Rect", Vec2{ 40, 120 }, 120))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ Random(-400, 400), -600}, Size{20, 60});
		}

		if (SimpleGUI::Button(U"Triangle", Vec2{ 40, 160 }, 120))
		{
			bodies << world.createTriangle(P2Dynamic, Vec2{ Random(-400, 400), -600 }, Triangle{ 40 });
		}

		if (SimpleGUI::Button(U"Quad", Vec2{ 40, 200 }, 120))
		{
			bodies << world.createQuad(P2Dynamic, Vec2{ Random(-400, 400), -600 }, RectF{ Arg::center(0, 0), 40 }.skewedX(45_deg) );
		}

		if (SimpleGUI::Button(U"Polygon", Vec2{ 40, 240 }, 120))
		{
			const Polygon polygon = Shape2D::NStar(5, 30, 20);
			bodies << world.createPolygon(P2Dynamic, Vec2{ Random(-400, 400), -600 }, polygon);
		}
	}
}
```


## 68.8 物体から 2D 図形を取得する
物体は通常 1 個以上の部品からなります。物体の部品の参照を `body.shape(index)` で取得し、それを適切な部品の形状クラスにキャストすることで、ワールドに存在する物体の部品の状態を `Circle` や `Quad` などの 2D 図形として取得できます。

| 作成関数 | P2ShapeType | 部品の形状クラス | 得られる 2D 図形 |
| --- | --- | --- | --- |
| `world.createCircle(type, center, r)` | `P2ShapeType::Circle` | `P2Circle` | `Circle` |
| `world.createRect(type, center, size)` | `P2ShapeType::Rect` | `P2Rect` | `Quad`（回転があるため） |
| `world.createTriangle(type, center, triangle)` | `P2ShapeType::Triangle` | `P2Triangle` | `Triangle` |
| `world.createQuad(type, center, quad)` | `P2ShapeType::Quad` | `P2Quad` | `Quad` |
| `world.createPolygon(type, center, polygon)` | `P2ShapeType::Polygon` | `P2Polygon` | `Polygon` |
| `world.createLine(type, center, line)` | `P2ShapeType::Line` | `P2Line` | `Line` |
| `world.createLineString(type, center, lineString)` | `P2ShapeType::LineString` | `P2LineString` | `LineString` |

次のコードでは、最後に追加された物体の部品に輪郭を描画し、その部品にマウスオーバーしている場合はカーソルを手の形に変更します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/8.png)

```cpp hl_lines="59-123"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double StepTime = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatedTime = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	// 地面
	Array<P2Body> grounds;
	grounds << world.createRect(P2Static, Vec2{ 0, -200 }, SizeF{ 600, 20 });
	grounds << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -500, -150, -300, -50 });
	grounds << world.createLineString(P2Static, Vec2{ 0, 0 }, LineString{ Vec2{ 100, -50 }, Vec2{ 200, -50 }, Vec2{ 600, -150 } });

	Array<P2Body> bodies;

	// 2D カメラ（中心座標 (0, -300), 拡大率 1.0）
	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		ClearPrint();
		Print << U"bodies.size(): " << bodies.size() << U"\n";

		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			// 2D 物理演算のワールドを StepTime 秒進める
			world.update(StepTime);

			// 地面の下に 500 cm 以上落下した物体を削除する
			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}

		// 2D カメラを更新する
		camera.update();
		{
			// 2D カメラから Transformer2D を作成する
			const auto t = camera.createTransformer();

			// すべての地面を描画する
			for (const auto& ground : grounds)
			{
				ground.draw(Palette::Gray);
			}

			// すべてのボディを描画する
			for (const auto& body : bodies)
			{
				body.draw(HSV{ body.id() * 10.0 });
			}

			if (bodies)
			{
				const auto& body = bodies.back();
				const P2Shape& shape = body.shape(0);
				shape.drawFrame(4, ColorF{ 1.0 });

				switch (shape.getShapeType())
				{
				case P2ShapeType::Circle:
					{
						const P2Circle& circleShape = static_cast<const P2Circle&>(shape);
						
						if (const Circle circle = circleShape.getCircle(); circle.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}

						break;
					}
				case P2ShapeType::Rect:
					{
						const P2Rect& rectShape = static_cast<const P2Rect&>(shape);
						
						if (const Quad quad = rectShape.getQuad(); quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}

						break;
					}
				case P2ShapeType::Triangle:
					{
						const P2Triangle& triangleShape = static_cast<const P2Triangle&>(shape);
						
						if (const Triangle triangle = triangleShape.getTriangle(); triangle.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}

						break;
					}
				case P2ShapeType::Quad:
					{
						const P2Quad& quadShape = static_cast<const P2Quad&>(shape);
						
						if (const Quad quad = quadShape.getQuad(); quad.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}

						break;
					}
				case P2ShapeType::Polygon:
					{
						const P2Polygon& polygonShape = static_cast<const P2Polygon&>(shape);
						
						if (const Polygon polygon = polygonShape.getPolygon(); polygon.mouseOver())
						{
							Cursor::RequestStyle(CursorStyle::Hand);
						}

						break;
					}
				}
			}
		}

		// 2D カメラの操作を描画する
		camera.draw(Palette::Orange);

		if (SimpleGUI::Button(U"Circle", Vec2{ 40, 80 }, 120))
		{
			bodies << world.createCircle(P2Dynamic, Vec2{ Random(-400, 400), -600 }, 20);
		}

		if (SimpleGUI::Button(U"Rect", Vec2{ 40, 120 }, 120))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ Random(-400, 400), -600}, Size{20, 60});
		}

		if (SimpleGUI::Button(U"Triangle", Vec2{ 40, 160 }, 120))
		{
			bodies << world.createTriangle(P2Dynamic, Vec2{ Random(-400, 400), -600 }, Triangle{ 40 });
		}

		if (SimpleGUI::Button(U"Quad", Vec2{ 40, 200 }, 120))
		{
			bodies << world.createQuad(P2Dynamic, Vec2{ Random(-400, 400), -600 }, RectF{ Arg::center(0, 0), 40 }.skewedX(45_deg) );
		}

		if (SimpleGUI::Button(U"Polygon", Vec2{ 40, 240 }, 120))
		{
			const Polygon polygon = Shape2D::NStar(5, 30, 20);
			bodies << world.createPolygon(P2Dynamic, Vec2{ Random(-400, 400), -600 }, polygon);
		}
	}
}
```


## 68.9 部品の材質
物体の部品を作成する際に、`P2Material` で材質を指定することができます。

| パラメータ | 説明 | デフォルトの値 |
| --- | --- | --- |
| `density` | 部品の密度 (kg / m^2) です。大きいほど面積当たりの重さが大きくなります。 | `1.0` |
| `restitution` | 部品の反発係数です。大きいほど反発しやすくなります。通常は [0.0, 1.0] の範囲です。 | `0.1` |
| `friction` | 部品の摩擦係数です。大きいほど摩擦が働きます。通常は [0.0, 1.0] の範囲です。 | `0.2` |
| `restitutionThreshold` | 反発が発生する速度の下限 (m/s) です。部品がこれ以上の速さでぶつかると反発します。 | `1.0` |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/9.png)

```cpp hl_lines="22-31"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double StepTime = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatedTime = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	// 地面
	Array<P2Body> grounds;
	grounds << world.createRect(P2Static, Vec2{ -200, 0 }, SizeF{ 600, 10 });
	grounds << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ 0, -150, 800, -250 });

	Array<P2Body> bodies;
	bodies << world.createCircle(P2Dynamic, Vec2{ -300, -600 }, 10, P2Material{ .restitution = 0.0 }); // 反発しない
	bodies << world.createCircle(P2Dynamic, Vec2{ -200, -600 }, 10, P2Material{ .restitution = 0.5 }); // 少し反発する
	bodies << world.createCircle(P2Dynamic, Vec2{ -100, -600 }, 10, P2Material{ .restitution = 0.9 }); // 反発する

	bodies << world.createRect(P2Dynamic, Vec2{ 200, -600 }, SizeF{ 30, 20 }, P2Material{ .restitution = 0.1, .friction = 0.0 }); // 摩擦しない
	bodies << world.createRect(P2Dynamic, Vec2{ 300, -600 }, SizeF{ 30, 20 }, P2Material{ .restitution = 0.1, .friction = 0.3 }); // 少し摩擦する
	bodies << world.createRect(P2Dynamic, Vec2{ 400, -600 }, SizeF{ 30, 20 }, P2Material{ .restitution = 0.1, .friction = 0.9 }); // 摩擦する

	bodies << world.createRect(P2Dynamic, Vec2{ -400, -600 }, SizeF{ 10, 80 }, P2Material{ .density = 10.0 }); // 高密度
	bodies << world.createRect(P2Dynamic, Vec2{ -350, -600 }, SizeF{ 10, 80 }, P2Material{ .density = 0.01 }); // 低密度

	// 2D カメラ（中心座標 (0, -300), 拡大率 1.0）
	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			// 2D 物理演算のワールドを StepTime 秒進める
			world.update(StepTime);

			// 地面の下に 500 cm 以上落下した物体を削除する
			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}

		// 2D カメラを更新する
		camera.update();
		{
			// 2D カメラから Transformer2D を作成する
			const auto t = camera.createTransformer();

			// すべての地面を描画する
			for (const auto& ground : grounds)
			{
				ground.draw(Palette::Gray);
			}

			// すべてのボディを描画する
			for (const auto& body : bodies)
			{
				body.draw(HSV{ body.id() * 10.0 });
			}
		}

		// 2D カメラの操作を描画する
		camera.draw(Palette::Orange);
	}
}
```


## 68.10 物体の初期状態（初速、回転角度、角速度）
`P2Body` には次のようなメンバ関数で初期状態を設定できます。

| メンバ関数 | 説明 |
| --- | --- |
| `.setVelocity(velocity)` | 物体の初速 (cm/s) を設定します。 |
| `.setAngle(angle)` | 物体の回転角度 (rad) を設定します。 |
| `.setAngularVelocity(angularVelocity)` | 物体の角速度 (rad/s) を設定します。 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/10.png)

```cpp hl_lines="53-81"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	// 2D 物理演算のシミュレーションステップ（秒）
	constexpr double StepTime = (1.0 / 200.0);

	// 2D 物理演算のシミュレーション蓄積時間（秒）
	double accumulatedTime = 0.0;

	// 2D 物理演算のワールド
	P2World world;

	const P2Body ground = world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });

	Array<P2Body> bodies;

	// 2D カメラ（中心座標 (0, -300), 拡大率 1.0）
	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			// 2D 物理演算のワールドを StepTime 秒進める
			world.update(StepTime);

			// 地面の下に 500 cm 以上落下した物体を削除する
			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}

		// 2D カメラを更新する
		camera.update();
		{
			// 2D カメラから Transformer2D を作成する
			const auto t = camera.createTransformer();

			// 地面を描画する
			ground.draw(Palette::Gray);

			// すべてのボディを描画する
			for (const auto& body : bodies)
			{
				body.draw(HSV{ body.id() * 10.0 });
			}
		}

		// 2D カメラの操作を描画する
		camera.draw(Palette::Orange);

		if (SimpleGUI::Button(U"通常の長方形", Vec2{ 40, 40 }, 300))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ -250, -400 }, SizeF{ 40, 20 });
		}

		if (SimpleGUI::Button(U"初速のある長方形", Vec2{ 40, 80 }, 300))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ -250, -400 }, SizeF{ 40, 20 })
				.setVelocity(Vec2{ 300, -300 });
		}

		if (SimpleGUI::Button(U"回転角度のある長方形", Vec2{ 40, 120 }, 300))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ -250, -400 }, SizeF{ 40, 20 })
				.setAngle(30_deg);
		}

		if (SimpleGUI::Button(U"角速度のある長方形", Vec2{ 40, 160 }, 300))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ -250, -400 }, SizeF{ 40, 20 })
				.setAngularVelocity(180_deg);
		}

		if (SimpleGUI::Button(U"初速と角速度のある長方形", Vec2{ 40, 200 }, 300))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ -250, -400 }, SizeF{ 40, 20 })
				.setVelocity(Vec2{ 300, -300 })
				.setAngularVelocity(180_deg);
		}
	}
}
```


## 68.11 物体に力を与える
`P2Body` に対して、`.applyForce(v)` でベクトル `v` の力を与えることができます。力は時間の経過とともに徐々に作用して物体の速度を変化させます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/11.png)

```cpp hl_lines="27-45"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	constexpr double StepTime = (1.0 / 200.0);

	double accumulatedTime = 0.0;

	P2World world;

	const P2Body ground = world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });

	P2Body body = world.createRect(P2Dynamic, Vec2{ 0, -100 }, SizeF{ 50, 50 });

	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	size_t index = 2;

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			world.update(StepTime);

			// 物体に力を与える
			switch (index)
			{
			case 0:
				body.applyForce(Vec2{ -100, 0 });
				break;
			case 1:
				body.applyForce(Vec2{ -50, 0 });
				break;
			case 2:
				body.applyForce(Vec2{ 0, 0 });
				break;
			case 3:
				body.applyForce(Vec2{ 50, 0 });
				break;
			case 4:
				body.applyForce(Vec2{ 100, 0 });
				break;
			}
		}

		camera.update();
		{
			const auto t = camera.createTransformer();

			ground.draw(Palette::Gray);

			body.draw(ColorF{ 0.96 });
		}

		// 2D カメラの操作を描画する
		camera.draw(Palette::Orange);

		SimpleGUI::RadioButtons(index, { U"(-100, 0)", U"(-50, 0)", U"(0, 0)", U"(50, 0)", U"(100, 0)" }, Vec2{ 40, 40 });
	}
}
```


## 68.12 物体に衝撃を加える
`P2Body` に対して、`.applyLinearImpulse(v)` でベクトル `v` の衝撃を与えることができます。衝撃は物体の速度を即座に変化させます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/12.png)

```cpp hl_lines="38-51"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	constexpr double StepTime = (1.0 / 200.0);

	double accumulatedTime = 0.0;

	P2World world;

	const P2Body ground = world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });

	P2Body body = world.createRect(P2Dynamic, Vec2{ 0, -100 }, SizeF{ 50, 50 });

	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			world.update(StepTime);
		}

		camera.update();
		{
			const auto t = camera.createTransformer();

			ground.draw(Palette::Gray);

			body.draw(ColorF{ 0.96 });
		}

		// 2D カメラの操作を描画する
		camera.draw(Palette::Orange);

		if (SimpleGUI::Button(U"Left", Vec2{ 40, 40 }, 120))
		{
			body.applyLinearImpulse(Vec2{ -100, 0 });
		}

		if (SimpleGUI::Button(U"Right", Vec2{ 40, 80 }, 120))
		{
			body.applyLinearImpulse(Vec2{ 100, 0 });
		}

		if (SimpleGUI::Button(U"Up", Vec2{ 40, 120 }, 120))
		{
			body.applyLinearImpulse(Vec2{ 0, -100 });
		}
	}
}
```


## 68.13 物体のスリープ
ワールド内で物体が安定状態に入ると、物体はスリープ状態になり、計算を省略してシミュレーションを高速化します。スリープ状態の物体は、他の物体と衝突したり、力を与えられたりすると自動的に起こされます。

物体を明示的にスリープさせることで、物体間の干渉を抑制し、物体を積み重ねたタワーの初期状態を安定させることもできます。

次のコードは、スリープ状態の物体を淡色で表示します。また、スリープした物体を積み重ねたタワーが安定していることを確認できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/13.png)

```cpp hl_lines="15-28 47-58"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	constexpr double StepTime = (1.0 / 200.0);

	double accumulatedTime = 0.0;

	P2World world;

	const P2Body ground = world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });

	Array<P2Body> bodies;
	bodies << world.createRect(P2Dynamic, Vec2{ -400, -400 }, SizeF{ 60, 40 });
	bodies << world.createRect(P2Dynamic, Vec2{ -300, -600 }, SizeF{ 60, 40 });

	for (int32 i = 0; i < 10; ++i)
	{
		bodies << world.createRect(P2Dynamic, Vec2{ -100, (-30 - i * 60) }, SizeF{ 8, 60 });
	}

	for (int32 i = 0; i < 10; ++i)
	{
		// 明示的にスリープさせる
		bodies << world.createRect(P2Dynamic, Vec2{ 300, (-30 - i * 60) }, SizeF{ 8, 60 }).setAwake(false);
	}

	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			world.update(StepTime);

			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}

		camera.update();
		{
			const auto t = camera.createTransformer();

			ground.draw(Palette::Gray);

			for (const auto& body : bodies)
			{
				if (body.isAwake())
				{
					body.draw(HSV{ body.id() * 10.0 });
				}
				else
				{
					// スリープした物体は淡色で表示
					body.draw(HSV{ body.id() * 10.0, 0.2, 1.0 });
				}
			}
		}

		camera.draw(Palette::Orange);
	}
}
```


## 68.14 重力の設定
`P2World` の `.setGravity(v)` で重力を設定できます。スリープ中の物体は重力の変更に気付かないため、重力を変更した場合はすべての物体を起こす必要があります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/14.png)

```cpp hl_lines="3-10 55-77"
# include <Siv3D.hpp>

// すべての物体を起こす
void AwakeAll(Array<P2Body>& bodies)
{
	for (auto& body : bodies)
	{
		body.setAwake(true);
	}
}

void Main()
{
	Window::Resize(1280, 720);

	constexpr double StepTime = (1.0 / 200.0);

	double accumulatedTime = 0.0;

	P2World world;

	const P2Body ground = world.createClosedLineString(P2Static, Vec2{ 0, 0 },
		LineString{ Vec2{ -400, -600 }, Vec2{ 400, -600 }, Vec2{ 400, 0 }, Vec2{ -400, 0 } });

	Array<P2Body> bodies;
	bodies << world.createRect(P2Dynamic, Vec2{ -200, -200 }, SizeF{ 50, 50 });
	bodies << world.createRect(P2Dynamic, Vec2{ -100, -200 }, SizeF{ 50, 50 });
	bodies << world.createCircle(P2Dynamic, Vec2{ 100, -200 }, 20);
	bodies << world.createCircle(P2Dynamic, Vec2{ 200, -200 }, 20);

	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			world.update(StepTime);
		}

		camera.update();
		{
			const auto t = camera.createTransformer();

			ground.draw(Palette::Gray);

			for (const auto& body : bodies)
			{
				body.draw(HSV{ body.id() * 10.0 });
			}
		}

		// 2D カメラの操作を描画する
		camera.draw(Palette::Orange);

		if (SimpleGUI::Button(U"Left", Vec2{ 40, 40 }, 120))
		{
			world.setGravity(Vec2{ -980, 0 });
			AwakeAll(bodies);
		}

		if (SimpleGUI::Button(U"Right", Vec2{ 40, 80 }, 120))
		{
			world.setGravity(Vec2{ 980, 0 });
			AwakeAll(bodies);
		}

		if (SimpleGUI::Button(U"Up", Vec2{ 40, 120 }, 120))
		{
			world.setGravity(Vec2{ 0, -980 });
			AwakeAll(bodies);
		}

		if (SimpleGUI::Button(U"Down", Vec2{ 40, 160 }, 120))
		{
			world.setGravity(Vec2{ 0, 980 });
			AwakeAll(bodies);
		}

		Line{ Scene::Center(), (Scene::Center() + world.getGravity() * 0.1) }.drawArrow(20, SizeF{ 30, 30 });
	}
}
```


## 68.15 衝突の検出
ワールドを更新するたび、物体間の衝突が検出されます。`P2World` の `.getCollisions()` で最新の衝突のリストを取得できます。戻り値は `HashTable<P2ContactPair, P2Collision>` です。

`P2ContactPair` は衝突した物体のペアで、`.a` と `.b` に衝突した物体の ID が格納されています。

次のコードでは、地面と接触している物体を白く描画しています。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/15.png)

```cpp hl_lines="15-16 24-25 33-45 56-67"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	constexpr double StepTime = (1.0 / 200.0);

	double accumulatedTime = 0.0;

	P2World world;

	const P2Body ground = world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });

	// 地面の ID
	const P2BodyID groundID = ground.id();

	Array<P2Body> bodies;

	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		// 地面と接触しているボディの ID のリスト
		HashSet<P2BodyID> groundContacts;

		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			world.update(StepTime);

			groundContacts.clear();

			// 衝突のリストを走査する
			for (auto&& [pair, collision] : world.getCollisions())
			{
				// 衝突のうち片方が地面の ID であれば、もう片方が地面と接触しているボディ
				if (pair.a == groundID)
				{
					groundContacts.insert(pair.b);
				}
				else if (pair.b == groundID)
				{
					groundContacts.insert(pair.a);
				}
			}

			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}

		camera.update();
		{
			const auto t = camera.createTransformer();

			ground.draw(Palette::Gray);

			for (const auto& body : bodies)
			{
				// 地面と接触しているボディは白く描画する
				if (groundContacts.contains(body.id()))
				{
					body.draw(Palette::White);
				}
				else
				{
					body.draw(HSV{ body.id() * 10.0 });
				}
			}
		}

		camera.draw(Palette::Orange);

		if (SimpleGUI::Button(U"Rect", Vec2{ 40, 40 }))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ Random(-200, 200), -300 }, SizeF{ 60, 40 });
		}
	}
}
```


## 68.16 ピボットジョイント
ピボットジョイント `P2PivotJoint` は、2 つの物体を 1 箇所の回転軸（アンカー）で接続するジョイントです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/16.png)

```cpp hl_lines="18-31 55-56 71-76"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	constexpr double StepTime = (1.0 / 200.0);

	double accumulatedTime = 0.0;

	P2World world;

	Array<P2Body> grounds;
	grounds << world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });
	grounds << world.createRect(P2Static, Vec2{ 200, -150 }, SizeF{ 100, 20 });
	grounds << world.createRect(P2Static, Vec2{ -300, -550 }, SizeF{ 40, 40 });

	// フリッパー
	const Vec2 flipperAnchor = Vec2{ 150, -150 };
	P2Body flipper = world.createRect(P2Dynamic, flipperAnchor, RectF{ -100, -5, 100, 10 });
	// flipper と grounds[1] を接続するピボットジョイントを作成する
	const P2PivotJoint flipperJoint = world.createPivotJoint(grounds[1], flipper, flipperAnchor)
		.setLimits(-10_deg, 30_deg) // 回転の制限角度を設定する
		.setLimitsEnabled(true); // 回転の制限を有効にする

	// 振り子
	const Vec2 pendulumAnchor = Vec2{ -300, -550 };
	const P2Body pendulum = world.createRect(P2Dynamic, pendulumAnchor, RectF{ -5, 0, 10, 200 })
		.setAngularDamping(0.2); // 回転を減衰させるパラメータ
	// pendulum と grounds[2] を接続するピボットジョイントを作成する
	const P2PivotJoint pendulumJoint = world.createPivotJoint(grounds[2], pendulum, pendulumAnchor);

	Array<P2Body> bodies;

	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			world.update(StepTime);

			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}

		camera.update();
		{
			const auto t = camera.createTransformer();

			for (const auto& ground : grounds)
			{
				ground.draw(Palette::Gray);
			}

			flipper.draw();
			pendulum.draw();

			for (const auto& body : bodies)
			{
				body.draw(HSV{ body.id() * 10.0 });
			}
		}

		camera.draw(Palette::Orange);

		if (SimpleGUI::Button(U"Rect", Vec2{ 40, 40 }, 100))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ Random(20, 100), -600 }, SizeF{ 60, 40 }, P2Material{ .density = 0.1 });
		}

		// フリッパーの操作
		if (SimpleGUI::Button(U"Flipper", Vec2{ 40, 80 }, 100))
		{
			// フリッパーに回転の衝撃を与える
			flipper.applyAngularImpulse(5000);
		}
	}
}
```



## 68.17 距離ジョイント
距離ジョイント `P2DistanceJoint` は、2 つの物体のアンカーを一定の距離、あるいは一定の距離の範囲に保つジョイントです。

次のコードでは、左の振り子は空中の天井からの距離を 200 cm に保ち、右の振り子は空中の天井からの距離を 180～220 cm の範囲に保ちます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/17.png)

```cpp hl_lines="18-25 49-53 68-78"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	constexpr double StepTime = (1.0 / 200.0);

	double accumulatedTime = 0.0;

	P2World world;

	Array<P2Body> grounds;
	grounds << world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });
	grounds << world.createRect(P2Static, Vec2{ -300, -300 }, SizeF{ 40, 40 });
	grounds << world.createRect(P2Static, Vec2{ 300, -300 }, SizeF{ 40, 40 });

	// 左の振り子
	P2Body leftBall = world.createCircle(P2Dynamic, Vec2{ -300, -100 }, 20);
	const P2DistanceJoint leftJoint = world.createDistanceJoint(grounds[1], Vec2{ -300, -300 }, leftBall, Vec2{ -300, -100 }, 200);

	// 右の振り子
	P2Body rightBall = world.createCircle(P2Dynamic, Vec2{ 300, -100 }, 20);
	const P2DistanceJoint rightJoint = world.createDistanceJoint(grounds[2], Vec2{ 300, -300 }, rightBall, Vec2{ 300, -100 }, 200)
		.setMinLength(180).setMaxLength(220); // 180～220 の距離を設定する

	Array<P2Body> bodies;

	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			world.update(StepTime);

			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}

		camera.update();
		{
			const auto t = camera.createTransformer();

			for (const auto& ground : grounds)
			{
				ground.draw(Palette::Gray);
			}

			leftBall.draw();
			rightBall.draw();

			Line{ leftJoint.getAnchorPosA(), leftJoint.getAnchorPosB() }.draw(LineStyle::SquareDot, 4.0, Palette::Orange);
			Line{ rightJoint.getAnchorPosA(), rightJoint.getAnchorPosB() }.draw(LineStyle::SquareDot, 4.0, Palette::Orange);

			for (const auto& body : bodies)
			{
				body.draw(HSV{ body.id() * 10.0 });
			}
		}

		camera.draw(Palette::Orange);

		if (SimpleGUI::Button(U"Rect", Vec2{ 40, 40 }, 100))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ Random(-200, 200), -600 }, SizeF{ 40, 40 }, P2Material{ .density = 0.1 });
		}

		if (SimpleGUI::Button(U"Left", Vec2{ 40, 80 }, 100))
		{
			// 左の振り子に右方向への衝撃を与える
			leftBall.applyLinearImpulse(Vec2{ 100, 0 });
		}

		if (SimpleGUI::Button(U"Right", Vec2{ 40, 120 }, 100))
		{
			// 右の振り子に左方向への衝撃を与える
			rightBall.applyLinearImpulse(Vec2{ -100, 0 });
		}
	}
}
```


## 68.18 スライダージョイント
スライダージョイント `P2SliderJoint` は、2 つの物体のうち一方が直線上を移動できるよう接続するジョイントです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/18.png)

```cpp hl_lines="18-30 54-55 62-63 73-102"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	constexpr double StepTime = (1.0 / 200.0);

	double accumulatedTime = 0.0;

	P2World world;

	Array<P2Body> grounds;
	grounds << world.createRect(P2Static, Vec2{ -200, 0 }, SizeF{ 700, 10 });
	grounds << world.createCircle(P2Static, Vec2{ -500, -200 }, 20);
	grounds << world.createCircle(P2Static, Vec2{ 300, -400 }, 20);

	// 右方向のスライダー
	const P2Body wall = world.createRect(P2Dynamic, Vec2{ -500, -200 }, SizeF{ 20, 320 });
	P2SliderJoint wallJoint = world.createSliderJoint(grounds[1], wall, Vec2{ -500, -200 }, Vec2{ 1, 0 })
		.setLimits(20, 400).setLimitEnabled(true) // 移動可能範囲を設定する
		.setMaxMotorForce(1000) // モーターの最大の力を設定する。これが小さいと動かせない場合がある
		.setMotorEnabled(true); // モーターを有効にする

	// 下方向のスライダー
	const P2Body floor = world.createRect(P2Dynamic, Vec2{ 300, -400 }, SizeF{ 250, 10 });
	P2SliderJoint floorJoint = world.createSliderJoint(grounds[2], floor, Vec2{ 300, -400 }, Vec2{ 0, 1 })
		.setLimits(100, 410).setLimitEnabled(true) // 移動可能範囲を設定する
		.setMaxMotorForce(1000) // モーターの最大の力を設定する。これが小さいと動かせない場合がある
		.setMotorEnabled(true); // モーターを有効にする

	Array<P2Body> bodies;

	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			world.update(StepTime);

			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}

		camera.update();
		{
			const auto t = camera.createTransformer();

			for (const auto& ground : grounds)
			{
				ground.draw(Palette::Gray);
			}

			wall.draw();
			floor.draw();

			for (const auto& body : bodies)
			{
				body.draw(HSV{ body.id() * 10.0 });
			}

			Line{ wallJoint.getAnchorPosA(), wallJoint.getAnchorPosB() }.draw(LineStyle::SquareDot, 4.0, Palette::Orange);
			Line{ floorJoint.getAnchorPosA(), floorJoint.getAnchorPosB() }.draw(LineStyle::SquareDot, 4.0, Palette::Orange);
		}

		camera.draw(Palette::Orange);

		if (SimpleGUI::Button(U"Rect", Vec2{ 40, 40 }, 120))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ Random(-400, 200), -600 }, SizeF{ 40, 40 }, P2Material{ .density = 0.1 });
		}

		if (SimpleGUI::Button(U"Wall ←", Vec2{ 40, 80 }, 120))
		{
			// モーターの速さを設定する
			wallJoint.setMotorSpeed(-100);
		}

		if (SimpleGUI::Button(U"Wall Stop", Vec2{ 40, 120 }, 120))
		{
			wallJoint.setMotorSpeed(0);
		}

		if (SimpleGUI::Button(U"Wall →", Vec2{ 40, 160 }, 120))
		{
			wallJoint.setMotorSpeed(100);
		}

		if (SimpleGUI::Button(U"Floor ↑", Vec2{ 40, 200 }, 120))
		{
			floorJoint.setMotorSpeed(-100);
		}

		if (SimpleGUI::Button(U"Floor Stop", Vec2{ 40, 240 }, 120))
		{
			floorJoint.setMotorSpeed(0);
		}

		if (SimpleGUI::Button(U"Floor ↓", Vec2{ 40, 280 }, 120))
		{
			floorJoint.setMotorSpeed(100);
		}
	}
}
```



## 68.19 ホイールジョイント
ホイールジョイント `P2WheelJoint` は、車の車輪のように、2 つの物体を 1 箇所の回転軸で接続するジョイントです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/19.png)

```cpp hl_lines="3-42 64 72 86-89 99-102 109-132"
# include <Siv3D.hpp>

struct Car
{
	P2Body body;
	P2Body wheelL;
	P2Body wheelR;
	P2WheelJoint wheelJointL;
	P2WheelJoint wheelJointR;

	void draw() const
	{
		body.draw();
		wheelL.draw(ColorF{ 0.25 }).drawWireframe(2, Palette::Orange);
		wheelR.draw(ColorF{ 0.25 }).drawWireframe(2, Palette::Orange);
	}

	void setMotorSpeed(double speed)
	{
		wheelJointL.setMotorSpeed(speed);
		wheelJointR.setMotorSpeed(speed);
	}
};

Car CreateCar(P2World& world, const Vec2& pos, double dampingRatio)
{
	Car car;
	car.body = world.createRect(P2Dynamic, pos, SizeF{ 200, 40 });
	car.wheelL = world.createCircle(P2Dynamic, pos + Vec2{ -50, 20 }, 30)
		.setAngularDamping(1.5); // 回転の減衰
	car.wheelR = world.createCircle(P2Dynamic, pos + Vec2{ 50, 20 }, 30)
		.setAngularDamping(1.5); // 回転の減衰
	car.wheelJointL = world.createWheelJoint(car.body, car.wheelL, car.wheelL.getPos(), Vec2{ 0, -1 })
		.setLinearStiffness(4.0, dampingRatio)
		.setLimits(-5, 5).setLimitsEnabled(true)
		.setMaxMotorTorque(1000).setMotorEnabled(true);
	car.wheelJointR = world.createWheelJoint(car.body, car.wheelR, car.wheelR.getPos(), Vec2{ 0, -1 })
		.setLinearStiffness(4.0, dampingRatio)
		.setLimits(-5, 5).setLimitsEnabled(true)
		.setMaxMotorTorque(1000).setMotorEnabled(true);
	return car;
}

void Main()
{
	Window::Resize(1280, 720);

	constexpr double StepTime = (1.0 / 200.0);

	double accumulatedTime = 0.0;

	P2World world;

	Array<P2Body> grounds;
	grounds << world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 1000, 10 });
	grounds << world.createLine(P2Static, Vec2{ 0, 0 }, Line{ -800, -200, -300, -100 });

	Array<Car> cars;

	Array<P2Body> bodies;

	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	double motorSpeed = 0.0;

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			world.update(StepTime);

			cars.remove_if([](const Car& car) { return (500 < car.body.getPos().y); });

			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}

		camera.update();
		{
			const auto t = camera.createTransformer();

			for (const auto& ground : grounds)
			{
				ground.draw(Palette::Gray);
			}

			for (const auto& car : cars)
			{
				car.draw();
			}

			for (const auto& body : bodies)
			{
				body.draw(HSV{ body.id() * 10.0 });
			}
		}

		camera.draw(Palette::Orange);

		for (auto& car : cars)
		{
			car.setMotorSpeed(motorSpeed);
		}

		if (SimpleGUI::Button(U"Rect", Vec2{ 40, 40 }, 240))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ Random(-200, 200), -600 }, SizeF{ 40, 40 }, P2Material{ .density = 0.1 });
		}

		if (SimpleGUI::Button(U"Car (low damping)", Vec2{ 40, 80 }, 240))
		{
			cars << CreateCar(world, Vec2{ Random(-700, 200), -600 }, 0.05);
		}

		if (SimpleGUI::Button(U"Car (high damping)", Vec2{ 40, 120 }, 240))
		{
			cars << CreateCar(world, Vec2{ Random(-700, 200), -600 }, 1.0);
		}

		if (SimpleGUI::Button(U"Motor (-500)", Vec2{ 40, 160 }, 240))
		{
			motorSpeed = -500;
		}

		if (SimpleGUI::Button(U"Motor (0)", Vec2{ 40, 200 }, 240))
		{
			motorSpeed = 0;
		}

		if (SimpleGUI::Button(U"Motor (500)", Vec2{ 40, 240 }, 240))
		{
			motorSpeed = 500;
		}

		if (SimpleGUI::Button(U"Reset", Vec2{ 40, 280 }, 240))
		{
			cars.clear();
			bodies.clear();
		}
	}
}
```


## 68.20 マウスジョイント
マウスジョイント `P2MouseJoint` は、マウスの位置をターゲット位置として、物体を移動させるためのジョイントです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/20.png)

```cpp hl_lines="21-22 47-64"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	constexpr double StepTime = (1.0 / 200.0);

	double accumulatedTime = 0.0;

	P2World world;

	Array<P2Body> grounds;
	grounds << world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 800, 10 });

	const P2Body box = world.createPolygon(P2Dynamic, Vec2{ 0, -200 },
		LineString{ Vec2{ -100, 0 }, Vec2{ -100, 100 }, Vec2{ 100, 100 }, { Vec2{ 100, 0 }} }.calculateBuffer(4));

	Array<P2Body> bodies;

	// マウスジョイント
	P2MouseJoint mouseJoint;

	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	int32 stepCount = 0;

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			world.update(StepTime);

			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });

			// 一定間隔で円を追加する
			if (++stepCount % 4 == 0)
			{
				bodies << world.createCircle(P2Dynamic, Vec2{ Random(-200, 200), -600 }, 5, P2Material{ .density = 0.1 });
			}
		}

		camera.update();
		{
			const auto t = camera.createTransformer();

			if (MouseL.down())
			{
				// マウスジョイントを作成する
				mouseJoint = world.createMouseJoint(box, Cursor::PosF())
					.setMaxForce(box.getMass() * 5000.0)
					.setLinearStiffness(2.0, 0.8);
			}
			else if (MouseL.pressed())
			{
				// マウスジョイントのターゲット位置を更新する
				mouseJoint.setTargetPos(Cursor::PosF());
				Line{ mouseJoint.getAnchorPos(), mouseJoint.getTargetPos() }.draw(LineStyle::SquareDot, 4.0, Palette::Orange);
			}
			else if (MouseL.up())
			{
				// マウスジョイントを破棄する
				mouseJoint.release();
			}

			for (const auto& ground : grounds)
			{
				ground.draw(Palette::Gray);
			}

			box.draw();

			for (const auto& body : bodies)
			{
				body.draw(HSV{ body.id() * 10.0 });
			}
		}

		camera.draw(Palette::Orange);

		if (SimpleGUI::Button(U"Reset", Vec2{ 40, 40 }))
		{
			bodies.clear();
		}
	}
}
```


## 68.21 物体とテクスチャの連動
物理演算の結果をテクスチャを使って表現するには、いくつかの方法があります。

- `P2Body` をそのままテクスチャに置き換える
- テクスチャから `Polygon` あるいは `MultiPolygon` を作成し、`P2Body` として追加する
- `Buffer2D` を作成し、`P2Body` の状態を `Transformer2D` に反映させて描画する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/21.png)

```cpp hl_lines="8-18 28-30 39 47-60 93-121 126"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// パターン 1: 円を絵文字で置き換える
	const Texture appleTexture{ U"🍎"_emoji };

	// パターン 2: 絵文字から作成した多角形を使う
	const Texture penguinTexture{ U"🐧"_emoji };
	const Texture woodTexture{ U"example/texture/wood.jpg", TextureDesc::Mipped };
	const MultiPolygon penguinPolygon = Emoji::CreateImage(U"🐧").alphaToPolygonsCentered().simplified(2.0);

	// パターン 3: Buffer2D を使う
	const Polygon boxPolygon = LineString{ Vec2{ -100, 0 }, Vec2{ -100, 100 }, Vec2{ 100, 100 }, { Vec2{ 100, 0 }} }.calculateBuffer(8);
	const Buffer2D boxObject = boxPolygon.toBuffer2D(Arg::center(0, 50), SizeF{ 200, 200 });

	constexpr double StepTime = (1.0 / 200.0);
	double accumulatedTime = 0.0;

	P2World world;

	Array<P2Body> grounds;
	grounds << world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 800, 10 });

	Array<P2Body> apples;
	Array<P2Body> penguins;
	const P2Body box = world.createPolygon(P2Dynamic, Vec2{ 0, -200 }, boxPolygon);

	// マウスジョイント
	P2MouseJoint mouseJoint;

	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	int32 stepCount = 0;

	bool showBodyOutline = true;

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			world.update(StepTime);

			apples.remove_if([](const P2Body& apple) { return (500 < apple.getPos().y); });
			penguins.remove_if([](const P2Body& penguin) { return (500 < penguin.getPos().y); });

			// 一定間隔で円を追加する
			if (stepCount % 200 == 0)
			{
				apples << world.createCircle(P2Dynamic, Vec2{ Random(-300, -100), -600 }, 30, P2Material{ .density = 0.1 });
			}

			// 一定間隔でペンギンを追加する
			if (stepCount % 200 == 100)
			{
				penguins << world.createPolygons(P2Dynamic, Vec2{ Random(100, 300), -600 }, penguinPolygon, P2Material{ .density = 0.1 });
			}

			++stepCount;
		}

		camera.update();
		{
			const auto t = camera.createTransformer();

			if (MouseL.down())
			{
				// マウスジョイントを作成する
				mouseJoint = world.createMouseJoint(box, Cursor::PosF())
					.setMaxForce(box.getMass() * 5000.0)
					.setLinearStiffness(2.0, 0.8);
			}
			else if (MouseL.pressed())
			{
				// マウスジョイントのターゲット位置を更新する
				mouseJoint.setTargetPos(Cursor::PosF());
				Line{ mouseJoint.getAnchorPos(), mouseJoint.getTargetPos() }.draw(LineStyle::SquareDot, 4.0, Palette::Orange);
			}
			else if (MouseL.up())
			{
				// マウスジョイントを破棄する
				mouseJoint.release();
			}

			for (const auto& ground : grounds)
			{
				ground.draw(Palette::Gray);
			}

			{
				if (showBodyOutline)
				{
					box.drawFrame(2.0);
				}

				const Transformer2D t{ Mat3x2::Rotate(box.getAngle()).translated(box.getPos()) };
				boxObject.draw(woodTexture);
			}

			for (const auto& apple : apples)
			{
				appleTexture.resized(68).rotated(apple.getAngle()).drawAt(apple.getPos());

				if (showBodyOutline)
				{
					apple.drawFrame(2.0);
				}
			}

			for (const auto& penguin : penguins)
			{
				penguinTexture.rotated(penguin.getAngle()).drawAt(penguin.getPos());

				if (showBodyOutline)
				{
					penguin.drawFrame(2.0);
				}
			}
		}

		camera.draw(Palette::Orange);

		SimpleGUI::CheckBox(showBodyOutline, U"show outline", Vec2{ 40, 40 });

		if (SimpleGUI::Button(U"Reset", Vec2{ 40, 80 }))
		{
			apples.clear();
			penguins.clear();
		}
	}
}
```


## 68.22 干渉フィルタ
部品は干渉フィルタ `P2Filter` を持ちます。自身が所属するカテゴリービットフラグを指定し、特定のビットフラグを持つ他の部品と干渉しないようにできます。

部品 A, B があるとき、`((A.maskBits & B.categoryBits) != 0) && ((B.maskBits & A.categoryBits) != 0)` のときのみ干渉が発生します。デフォルトでは、部品は `categoryBits = 0x0001`、`maskBits = 0xFFFF` となっており、すべての部品が互いに干渉します。

`groupIndex` による追加の干渉制御もありますが、サンプルコード内では扱っていません。

| メンバ変数 | 説明 |
|---|---|
| `uint16 categoryBits` | 自身が所属するカテゴリーを表すビットフラグ |
| `uint16 maskBits` | 物理的に干渉する相手のカテゴリーを表すビットフラグ |
| `int16 groupIndex` | 2 つの部品のうちいずれかの `groupIndex` が `0` の場合、`categoryBits` と `maskBits` によって干渉の有無が決まる。<br>2 つの部品の両方の `groupIndex` が 非 `0` で、互いに異なる場合、`categoryBits` と `maskBits` によって干渉の有無が決まる。<br>2 つの部品の `groupIndex` が `1` 以上で、互いに等しい場合、必ず干渉する。<br>2 つの部品の `groupIndex` が `-1` 以下で、互いに等しい場合、必ず干渉しない。 |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/physics2d/22.png)

```cpp hl_lines="12-19 25-27 48-49 61 66"
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);

	constexpr double StepTime = (1.0 / 200.0);
	double accumulatedTime = 0.0;

	P2World world;

	// デフォルトの干渉フィルタ
	constexpr P2Filter WallFilter{ .categoryBits = 0b0000'0000'0000'0001, .maskBits = 0b1111'1111'1111'1111 };

	// チーム 1 の干渉フィルタ（チーム 1 どうしは干渉しない）
	constexpr P2Filter Team1Filter{ .categoryBits = 0b0000'0000'0000'0010, .maskBits = 0b0000'0000'0000'0101 };

	// チーム 2 の干渉フィルタ（チーム 2 どうしは干渉しない）
	constexpr P2Filter Team2Filter{ .categoryBits = 0b0000'0000'0000'0100, .maskBits = 0b0000'0000'0000'0011 };

	constexpr ColorF Team1Color{ 0.4, 1.0, 0.2 };
	constexpr ColorF Team2Color{ 0.4, 0.2, 1.0 };

	Array<P2Body> grounds;
	grounds << world.createRect(P2Static, Vec2{ 0, 0 }, SizeF{ 800, 10 });
	grounds << world.createRect(P2Static, Vec2{ -200, -200 }, SizeF{ 300, 10 }, {}, Team1Filter);
	grounds << world.createRect(P2Static, Vec2{ 200, -200 }, SizeF{ 300, 10 }, {}, Team2Filter);

	Array<P2Body> bodies;

	Camera2D camera{ Vec2{ 0, -300 }, 1.0 };

	while (System::Update())
	{
		for (accumulatedTime += Scene::DeltaTime(); StepTime <= accumulatedTime; accumulatedTime -= StepTime)
		{
			world.update(StepTime);

			bodies.remove_if([](const P2Body& body) { return (500 < body.getPos().y); });
		}

		camera.update();
		{
			const auto t = camera.createTransformer();

			for (const auto& body : bodies)
			{
				const bool isTeam1 = (body.shape(0).getFilter().categoryBits == Team1Filter.categoryBits);
				body.draw(isTeam1 ? Team1Color : Team2Color);
			}

			grounds[0].draw(Palette::Gray);
			grounds[1].draw(ColorF{ Team1Color, 0.75 });
			grounds[2].draw(ColorF{ Team2Color, 0.75 });
		}

		camera.draw(Palette::Orange);

		if (SimpleGUI::Button(U"Team 1", Vec2{ 40, 40 }, 120))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ Random(-400, 400), -600 }, SizeF{ 40, 40 }, P2Material{ .density = 0.1 }, Team1Filter);
		}

		if (SimpleGUI::Button(U"Team 2", Vec2{ 40, 80 }, 120))
		{
			bodies << world.createRect(P2Dynamic, Vec2{ Random(-400, 400), -600 }, SizeF{ 40, 20 }, P2Material{ .density = 0.1 }, Team2Filter);
		}

		if (SimpleGUI::Button(U"Reset", Vec2{ 40, 120 }, 120))
		{
			bodies.clear();
		}
	}
}
```

