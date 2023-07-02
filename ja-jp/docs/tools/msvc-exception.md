# 例外の発生箇所の表示

## 設定方法
Windows 版の Siv3D は、サブスレッドで `Main()` を実行するという設計のため、デフォルトの設定では、例外の発生した行がコードエディタ上に表示されません。

例外が発生した箇所をエディタ上で確認できるようにするには、Visual Studio メニューの「デバッグ」→「ウィンドウ」→「例外設定」を開き、「スローされたときに中断」のリストに `s3d::Error` を加えます。すると、その種類の例外が発生した次の行でプログラムが中断するようになり、例外発生個所の特定がしやすくなります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tools/msvc-exception.png)

## サンプルコード

```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 a = 10;

	if (a != 10)
	{
		throw Error{ U"A" };
	}

	int32 b = 20;

	if (b != 10)
	{
		throw Error{ U"B" }; // 例外が投げられる
	}

	Print << a << b; // この行で中断される

	while (System::Update())
	{

	}
}
```
