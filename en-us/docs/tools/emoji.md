# Emoji and Icon Search

## Emoji Search

- For finding emojis, [emojipedia :material-open-in-new:](https://emojipedia.org/){:target="_blank"} is convenient.
- On Windows, you can also use the emoji input menu that appears with ++windows+period++.

## Icon Search

- For icons, use hexadecimal codes found on [Material Design Icons :material-open-in-new:](https://pictogrammers.com/library/mdi/){:target="_blank"} or [Font Awesome :material-open-in-new:](https://fontawesome.com/v5/search?o=r&m=free){:target="_blank"} with `_icon` appended.

## Sample Code

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.92 });

	// Emoji
	const Texture t1{ U"🍔"_emoji };

	// Icon
	const Texture t2{ 0xF0431_icon, 80 };

	while (System::Update())
	{
		t1.drawAt(300, 300);

		t2.drawAt(500, 300, ColorF{ 0.25 });

		SimpleGUI::Button(U"\U000F0493", Vec2{ 40, 40 });
	}
}
```