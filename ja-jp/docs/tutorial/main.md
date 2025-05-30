# 4. Main 関数とメインループ
Main 関数の構造とメインループを学びます。

## 4.1 Main 関数の 3 つのパート
- `Main` 関数は、大きく 3 つのパートに分けられます

| パート | 説明 | やること |
|--|--|--|
| メインループの前 | プログラム起動後、最初に実行される部分 | 画面の設定、テクスチャの作成、フォントの初期化など |
| メインループの中 | 毎秒数十回以上繰り返されるループの中の部分 | 入力処理や描画 |
| メインループの後 | プログラムが終了するときに実行される部分 | （必要に応じて）ゲームのセーブなど |

=== "メインループの前"
	```cpp hl_lines="5-6"
	# include <Siv3D.hpp>

	void Main()
	{


		while (System::Update())
		{

		}
	}
	```

=== "メインループの中"
	```cpp hl_lines="7-9"
	# include <Siv3D.hpp>

	void Main()
	{
		while (System::Update())
		{



		}
	}
	```

=== "メインループの後"
	```cpp hl_lines="9-10"
	# include <Siv3D.hpp>

	void Main()
	{
		while (System::Update())
		{

		}


	}
	```

- 本格的に開発を始めると、**メインループの中**に大半の仕事を書きます
- **メインループの後**に何かを書くことは少ないです

## 4.2 メインループの実行頻度
- **メインループ**は、プログラムを実行しているモニタ 🖥️ のリフレッシュレートに合わせて繰り返されます。一般に毎秒 60 回や 120 回です
- 通常の C++ プログラムで `for(;;)` のようなループを書くと、性能が許す限り 1 秒間に数万回以上ループしてしまいますが、`while (System::Update())` は違います
- `System::Update()` 関数が内部で**モニタの表示タイミングに合わせた待機**を行い、モニタの表示タイミングに合わせた頻度のループ（1 秒間に数十回～）になるよう制御してくれます
	- どんなときもモニタのリフレッシュレートに合わせるわけではありません。タイミングによってはリフレッシュレートを若干下回ることがあります
	- メインループ内で非常に重い処理を行うと、さらに大きく下回ることがあります
- 次のプログラムを実行すると、FPS を画面の左上に表示して確認できます
	- **FPS（Frames Per Second）**は、1 秒間に何回画面が更新されるかを表す指標です

```cpp title="メインループが毎秒何回繰り返されているか（FPS）を、画面の左上に表示する"
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();
		Print << Profiler::FPS();
	}
}
```

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/main/2.png)


## 4.3 メインループの前にやること
- プログラムで画像を表示したいとき、「画像を読み込む仕事」は**メインループの前**に書くようにします
- **メインループの前**で画像を 1 度だけ読み込んでテクスチャを作成し、あとはそれを**メインループの中**で描画するようにすれば、最も少ない仕事量で目的を達成できるためです

!!! success "OK"
	- メインループの前で、1 度だけ画像を読み込んでテクスチャを作成します
	- そのあとは、作成済みのテクスチャをメインループの中で描画するだけです

	```cpp hl_lines="6"
	# include <Siv3D.hpp>

	void Main()
	{
		// 画像ファイルから画像データを読み込んでテクスチャを作成する
		const Texture texture{ U"example/windmill.png" };

		while (System::Update())
		{
			// テクスチャを描画する
			texture.draw();
		}
	}
	```

- テクスチャの作成を**メインループの中**に書いてしまうと、ループの終端でそのテクスチャが破棄されるため、作成しては破棄、作成しては破棄という無駄な処理が毎フレーム繰り返されます

!!! failure "作成しては破棄の繰り返し"
	- メインループの中で、毎ループ画像を読み込んでテクスチャを作成しては破棄しています
	- Siv3D はこのような問題を検知すると、警告のメッセージを出力してメインループを終了します

	```cpp hl_lines="8"
	# include <Siv3D.hpp>

	void Main()
	{
		while (System::Update())
		{
			// 画像ファイルから画像データを読み込んでテクスチャを作成する
			const Texture texture{ U"example/windmill.png" };

			// テクスチャを描画する
			texture.draw();
		} // ループの終端でテクスチャが破棄される
	}
	```

- 万が一そのようなコードを書いてしまっても安心です。Siv3D は自動的に「作成しては破棄の繰り返し」を検出し、メッセージボックスを表示してプログラムを終了してくれます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/main/3.png)


## 振り返りチェックリスト
- [x] Main 関数の 3 つのパートを学んだ
- [x] `System::Update()` がメインループの実行頻度を制御していることを学んだ
- [x] 「画像を読み込む」のような重い仕事はメインループの前に書くことを学んだ
- [x] 誤ってメインループの中で「作成しては破棄」を繰り返しても、Siv3D が自動的に問題を検出してプログラムを終了してくれることを学んだ
