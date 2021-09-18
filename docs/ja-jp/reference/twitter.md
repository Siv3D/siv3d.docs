
!!! warning "このページよりも新しいドキュメントがあります"
	このドキュメントは古い OpenSiv3D v0.4.3 向けです。2021 年9 月 18 日に最新の OpenSiv3D v0.6.0 がリリースされました。最新のドキュメントは [Siv3D リファレンス v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation) です。

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


