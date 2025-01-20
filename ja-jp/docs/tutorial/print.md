# 6. 簡易的な出力
プログラム内でテキストや数値を簡易表示する方法を学びます。簡易表示では、フォントや位置、色を指定できませんが、最小限のコードで文字列や数値を画面に表示できます。

## 6.1 文字列や数値を簡易表示する
- `Print` に対して、文字列や数値を `<<` 演算子で渡すと、画面の左上に簡易表示できます
- Siv3D のプログラムで文字列を扱うときは、ダブルクォーテーションの前に `U` を付けます
    - 文字列を Unicode (UTF-32) 文字列として扱うための記法です

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/print/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Print << U"C++";

	Print << U"Hello, " << U"Siv3D"; // 複数に分けても OK

	Print << 123;

	Print << 4.567;

	while (System::Update())
	{

	}
}
```


## 6.2 簡易表示をたくさん行う
- 簡易出力したものは画面に残り続けます
- 画面に収まらなくなった場合、古いものから順に消えていきます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/print/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	while (System::Update())
	{
		Print << count;

		++count;
	}
}
```


## 6.3 簡易表示を消去する
- 画面の簡易表示をすべて消去するには、`ClearPrint()` を使います
- メインループの先頭で常に `ClearPrint()` すると、現在のフレーム内で出力した内容だけを画面に表示することができます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial/print/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	while (System::Update())
	{
		// 古い出力（以前のフレームの出力）を消去する
		ClearPrint();

		Print << count;

		++count;
	}
}
```
