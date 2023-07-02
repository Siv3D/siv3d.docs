# 絵文字とアイコンの検索

## 絵文字の検索

- 絵文字は [emojipedia :material-open-in-new:](https://emojipedia.org/){:target="_blank"} で探すと便利です。
- Windows の場合は、++windows+period++ で出てくる絵文字入力メニューも使えます。

## アイコンの検索

- アイコンは [Material Design Icons :material-open-in-new:](https://pictogrammers.com/library/mdi/){:target="_blank"} または [Font Awesome :material-open-in-new:](https://fontawesome.com/v5/search?o=r&m=free){:target="_blank"} で調べられる 16 進数コードに `_icon` を付けた値を使います。

## サンプルコード

```cpp
#include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.92 });

	// 絵文字
	const Texture t1{ U"🍔"_emoji };

	// アイコン
	const Texture t2{ 0xF0431_icon, 80 };

	while (System::Update())
	{
		t1.drawAt(300, 300);

		t2.drawAt(500, 300, ColorF{ 0.25 });

		SimpleGUI::Button(U"\U000F0493", Vec2{ 40, 40 });
	}
}
```