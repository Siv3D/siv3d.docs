
# Twitter

## ツイートの投稿画面を表示

```C++
# include <Siv3D.hpp>

void Main()
{
	const String text = U"Hello, Siv3D! #Siv3D";

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Tweet", Vec2(20, 20)))
		{
			// text をつぶやくツイート投稿画面を開く
			Twitter::OpenTweetWindow(text);
		}
	}
}
```


