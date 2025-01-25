# 12. 模様を描く
円や長方形をループを使って並べ、模様を描く方法を学びます。

## 12.1 円を並べる
- `for` ループと図形描画を組み合わせて、短いコードでたくさんの図形を描くことができます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 i = 0; i < 6; ++i)
		{
			Circle{ (i * 100), 100, 30 }.draw(Palette::Skyblue);
		}

		for (int32 i = 0; i < 6; ++i)
		{
			Circle{ (50 + i * 100), 200, 30 }.draw(Palette::Seagreen);
		}
	}
}
```


## 12.2 二重ループで円を並べる
- 横に図形を並べるループを、さらに縦に並べるループで包むことで、縦横に図形を並べることができます
- 横方向のループ変数は `x`, 縦方向のループ変数は `y` という名前にするとわかりやすいです

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 y = 0; y < 4; ++y) // 縦方向
		{
			for (int32 x = 0; x < 6; ++x) // 横方向
			{
				Circle{ (x * 100), (y * 100), 30 }.draw(Palette::Skyblue);
			}
		}
	}
}
```


## 12.3 少し複雑にする
- 二重ループ内のループ変数 `x`, `y` に着目し、その和が奇数か偶数かで図形を変化させると、さらに複雑な模様を描くことができます
- ある整数 `n` が偶数であるかは `(n % 2) == 0` で判定できます
- 整数 `n` が偶数であるかを `bool` 型で返す `IsEven(n)` という関数も用意されています

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 y = 0; y < 4; ++y)
		{
			for (int32 x = 0; x < 6; ++x)
			{
				if (IsEven(x + y)) // x + y が偶数なら
				{
					// 大きい円を描く
					Circle{ (x * 100), (y * 100), 30 }.draw(Palette::Skyblue);
				}
				else // 奇数なら
				{
					// 小さい円を描く
					Circle{ (x * 100), (y * 100), 10 }.draw(Palette::Skyblue);
				}
			}
		}
	}
}
```


## 12.4 長方形を並べる
- 柱のように長方形を並べます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 6; ++x)
		{
			Rect{ (x * 100), 0, 80, 600 }.draw(Palette::Skyblue);
		}
	}
}
```


## 12.5 サイズを徐々に変える
- ループ変数 `x` が大きくなるにつれて、長方形の長さが徐々に大きくなるようにします

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 6; ++x)
		{
			Rect{ (x * 100), 0, 80, ((x + 1) * 100) }.draw(Palette::Skyblue);
		}
	}
}
```


## 12.6 色を徐々に変える
- ループ変数 `x` が大きくなるにつれて、色が徐々に変わるようにします

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 6; ++x)
		{
			Rect{ (x * 100), 0, 80, 600 }.draw(ColorF{ 0.0, (x * 0.15), (x * 0.15) });
		}
	}
}
```


## 12.7 色相を徐々に変える
- ループ変数 `x` が大きくなるにつれて、色相が徐々に変わるようにします
- 最も左の長方形と最も右の長方形は、色相がそれぞれ 0, 360 で同じ色です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 10; ++x)
		{
			Rect{ (x * 80), 0, 80, 600 }.draw(HSV{ (x * 40), 0.5, 1.0 });
		}
	}
}
```


## 🧩 練習
次のような模様を描いてみましょう

### 練習 ① 波紋

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/8-1.png)

??? info "ヒント"
	- `Circle` の半径を徐々に変えます

??? success "解答例"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(Palette::White);

		while (System::Update())
		{
			for (int32 i = 0; i < 5; ++i)
			{
				Circle{ 400, 300, ((i + 1) * 50) }.drawFrame(4, Palette::Skyblue);
			}
		}
	}
	```


### 練習 ② 市松模様

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/8-2.png)

??? info "ヒント"
	- 2 重ループを使います
	- `x` と `y` の偶奇で色を変えます

??? success "解答例"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(Palette::White);

		while (System::Update())
		{
			for (int32 x = 0; x < 8; ++x)
			{
				for (int32 y = 0; y < 6; ++y)
				{
					if (IsEven(x + y))
					{
						Rect{ (x * 100), (y * 100), 100 }.draw(Palette::Skyblue);
					}
				}
			}
		}
	}
	```


### 練習 ③ マス目

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/pattern/8-3.png)

??? info "ヒント"
	- 2 重ループを使います
	- `Rect` の `.drawFrame()` で枠を描きます
	- あるいは `Rect` の `.draw()` で内部を塗りつぶします

??? success "解答例 1"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(Palette::White);

		while (System::Update())
		{
			for (int32 x = 0; x < 8; ++x)
			{
				for (int32 y = 0; y < 6; ++y)
				{
					Rect{ (x * 100), (y * 100), 100 }.drawFrame(2, 0, Palette::Skyblue);
				}
			}
		}
	}
	```

??? success "解答例 2"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(Palette::Skyblue);

		while (System::Update())
		{
			for (int32 x = 0; x < 8; ++x)
			{
				for (int32 y = 0; y < 6; ++y)
				{
					Rect{ (2 + x * 100), (2 + y * 100), 96 }.draw();
				}
			}
		}
	}
	```


## 振り返りチェックリスト
- [x] ループと図形描画を組み合わせて、図形を並べて描く方法を学んだ
- [x] 二重ループを使って、縦横に図形を並べる方法を学んだ
- [x] ループ変数を活用して、サイズや色などに変化をつける方法を学んだ
