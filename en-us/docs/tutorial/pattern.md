# 9. 模様を描く
円や長方形を並べて模様を描く方法を学びます。

## 9.1 円を並べる
ループと図形描画を組み合わせると、模様やパターンを簡単に描くことができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 i = 0; i < 5; ++i)
		{
			Circle{ (i * 100), 100, 30 }.draw(Palette::Skyblue);
		}

		for (int32 i = 0; i < 5; ++i)
		{
			Circle{ (50 + i * 100), 200, 30 }.draw(Palette::Seagreen);
		}
	}
}
```


## 9.2 二重ループで円を並べる

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/2.png)

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


## 9.3 少し複雑にする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/3.png)

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
				if ((x + y) % 2 == 0)
				{
					Circle{ (x * 100), (y * 100), 10 }.draw(Palette::Skyblue);
				}
				else
				{
					Circle{ (x * 100), (y * 100), 30 }.draw(Palette::Skyblue);
				}
			}
		}
	}
}
```


## 9.4 長方形を並べる

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/4.png)

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


## 9.5 色を徐々に変化させる (1)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 6; ++x)
		{
			Rect{ (x * 100), 0, 80, 600 }.draw(ColorF{ 0.0, (x * 0.2), 1.0 });
		}
	}
}
```


## 9.6 色を徐々に変化させる (2)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/pattern/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(Palette::White);

	while (System::Update())
	{
		for (int32 x = 0; x < 10; ++x)
		{
			Rect{ (x * 80), 0, 80, 600 }.draw(HSV{ (x * 36), 0.5, 1.0 });
		}
	}
}
```


## 振り返りチェックリスト
- [x] ループと図形描画を組み合わせて模様を描画する方法を学んだ

