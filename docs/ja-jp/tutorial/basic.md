# 2. Siv3D の基本
この章では、Siv3D プログラミングの基本的な作法を学びます。

## 2.1 インクルードするヘッダ
Siv3D のプログラムを書くときは `<Siv3D.hpp>` ヘッダをインクルードします。  
これだけで、Siv3D の関数やクラスを使ってプログラムを書けるようになります。  

```C++
# include <Siv3D.hpp>
```

経験のある C++ のプログラマである場合、ほかにも `<iostream>` や `<vector>` などの C++ 標準ライブラリをインクルードしたくなるかもしれませんが、それは不要です。すでに `<Siv3D.hpp>` の中で、Siv3D プログラミングでよく使われる主要な C++ 標準ライブラリがインクルードされているためです。また、標準ライブラリの機能の多くは、より便利な Siv3D の関数やクラスで置き換えられます。Siv3D の学習の序盤で C++ 標準ライブラリを使うことはめったにありません。

??? summary "インクルードが不要なヘッダの例"
	#### `<string>`
	Siv3D.hpp 内ですでにインクルードされています。  
	`std::string` の置き換えとして `String` があります。
	
	#### `<vector>`
	Siv3D.hpp 内ですでにインクルードされています。  
	`std::vector` の置き換えとして `Array` があります。
	
	#### `<fstream>`
	`std::ofstream` の置き換えとして `TextWriter` や `BinaryWriter` があります。  
	`std::ifstream` の置き換えとして `TextReader` や `BinaryReader` があります。
	
	#### `<cmath>`
	Siv3D.hpp 内ですでにインクルードされています。  
	`Math::` 名前空間に主要な数学関数が用意されています。
	
	#### `<filesystem>`
	ファイルシステムに関する機能が `FileSystem::` 名前空間に用意されています。

??? summary "C++ の文法復習「include」"
    `# include <ファイルパス>` または `# include "ファイルパス"` で、ヘッダ `パス` をインクルードします。前者の書き方では、標準ライブラリや、プロジェクトの設定でインクルードパスに設定されているフォルダのファイルが対象になり、後者では相対パスまたは絶対パスでファイルを検索します。Siv3D のプロジェクトでは、Siv3D のヘッダがあるディレクトリがインクルードパスに設定されているため、`# include <Siv3D.hpp>` で Siv3D のヘッダをインクルードできます。Siv3D 以外のプロジェクトで同じように書いても `Siv3D.hpp` を見つけられずコンパイルエラーになります。


## 2.2 エントリーポイント
通常の C++ のエントリーポイントは `int main()` ですが、Siv3D では `main()` 関数はライブラリ内にすでに実装済みで、次のようにウィンドウやグラフィックスの初期化処理を行うプログラムが用意されています。Siv3D のユーザが実装するのは `Main()` 関数です。

```C++
// Siv3D ライブラリの中にあるコード（説明のために簡略化しています）
int main()
{
    Siv3D の初期化...

    Main(); // この関数をユーザがプログラムする

    Siv3D の終了処理...
}
```

`Main()` 関数の前で初期化処理が完了しているおかげで、`Main()` 関数の中では何もせずいきなり Siv3D の機能を使うことができます。また、`Main()` 関数の実行が終了したあとには、ウィンドウの後片付けなどサブシステムの終了処理を Siv3D が自動的に行ってくれます。


## 2.3 最小のプログラム
これが Siv3D の最小のプログラムです。
```C++
# include <Siv3D.hpp>

void Main()
{

}
```
この `Main()` 関数は一瞬で終了してしまうので、このプログラムを実行してもウィンドウは表示されず、何も起こっていないように見えるでしょう。

??? summary "C++ の文法復習「関数の定義」"
    これは関数を定義するプログラムです。`Main` は関数の名前を、`void` はこの関数が結果の値を返さないことを表します。`{ }` 内には、この関数で実行したいプログラムを上から順に記述します。途中で `return` するか、終端までたどり着くと関数の実行は終了します。


## 2.4 ウィンドウを表示し続ける
プログラムがすぐに終了してしまっては、ユーザとインタラクションをするアプリケーションを作れません。`Main()` がずっと続くように **メインループ** を実装します。次のプログラムを実行するとウィンドウが表示され続けます。

```C++
# include <Siv3D.hpp>

void Main()
{
    while (System::Update())
    {

    }
}
```

`while` 文によってプログラムが半永久的に続きます。くり返しのたびに `System::Update()` がウィンドウの表示や音楽の再生、マウスやキーボードの入力情報などを更新することで、グラフィックスの表示やユーザの入力の取得などを継続的に処理できるようになります。

`System::Update()` は普段は `true` を返すため、メインループはいつまでも続きますが、ウィンドウが閉じられたり、++esc++ が押されたりするなど、アプリケーションを終了させる特別な **ユーザアクション** が実行されると、以降はずっと `false` を返すようになります。 アプリケーションはこの状態になったら速やかにメインループから抜け、`Main()` を終了させる必要があります。上記のプログラムのように書いていれば、自然にこの通りに動作します。

デフォルトでは、以下の 3 つのユーザアクションが、アプリケーションを終了させる操作として設定されています

- ウィンドウを閉じる
- ++esc++ を押す
- プログラムが `System::Exit()` を呼ぶ

この設定は、`System::SetTerminationTriggers()` に `UserAction` フラグの組み合わせを渡すことでカスタマイズできます。エスケープキーを押しても終了しないようにしたいときは、次のように設定をカスタマイズします。

```cpp
# include <Siv3D.hpp>

void Main()
{
	// ウィンドウの閉じるボタンを押すか、System::Exit() を呼ぶ操作を終了操作に設定,
	// エスケープキーを押しても終了しないようになる
	System::SetTerminationTriggers(UserAction::CloseButtonClicked);

	while (System::Update())
	{

	}
}
```

メインループは、特に指定しない限り、使用しているモニターのリフレッシュレートと同じ速度で繰り返されます。一般的なモニタのリフレッシュレートは毎秒 60, 120, または 144 フレームです。

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		// ウィンドウタイトルに直近のフレームレートを表示
		Window::SetTitle(Profiler::FPS());
	}
}
```


## 2.5 デバッグ表示
画面にメッセージを表示してみましょう。`Print` に向かって、出力の記号 `<<` でテキストを送ると、そのテキストが画面に表示されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/2/5.png)

```C++
# include <Siv3D.hpp>

void Main()
{
	Print << U"Hello, Siv3D!";

	while (System::Update())
	{
		
	}
}
```

テキストをプログラムに登場させるときは `U" "` で囲みます。Siv3D では UTF-32 という、日本語を扱いやすい形式でテキストデータを扱うため、ダブルクォーテーション `"` の先頭には常に `U` というプレフィックスを付け、`U"Hello"` のように記述します。


## 2.6 さまざまな値の表示
Siv3D で提供される型のほとんどは `Print` で値の内容を表示できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/2/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 文字列リテラル
	Print << U"Hello, Siv3D!";

	// << の連続
	Print << U"This" << U" is " << U"fun!";

	// 文字リテラル
	Print << U'あ';

	// 整数
	Print << 100 * 5 + 5;
	
	// 浮動小数点数
	Print << U"π = " << Math::Pi;

	// bool
	Print << Math::IsPrime(65537);

	// 時間型
	Print << 1h + 3s;

	// 数式パーサの結果 (double 型）
	Print << Eval(U"10^3 + sqrt(81) + 0.001");

	// 範囲
	Print << Range(0, 10);

	// 日付と時刻クラス
	Print << DateTime::Now();

	while (System::Update())
	{

	}
}
```


## 2.7 デバッグ表示のあふれ
次のように `Print` をメインループの中で使うと、毎フレーム新しいメッセージが追加され、古いメッセージは画面の外に追いやられます。画面外に出た古いメッセージは自動的に消去されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/2/7.png)
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


## 2.8 デバッグ表示の消去
`ClearPrint()` を使うと、`Print` でデバッグ表示した内容を即座に消去します。  
メインループの先頭で `ClearPrint()` すると、現在のフレームで `Print` した内容だけが表示されるようになります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/2/8.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	int32 count = 0;

	while (System::Update())
	{
		// Print したメッセージを消去
		ClearPrint();

		Print << count;

		++count;
	}
}
```


## 2.9 基本的なデータ型
Siv3D の基本的なデータ型は次のとおりです。よく使う重要なものに ★ を付けています。

#### 数値

| 型名        | 説明                                                                      |
|-----------|-------------------------------------------------------------------------|
| bool      | ★ ブーリアン型（`false` または `true`）                                            |
| int8      | 符号付き 8-bit 整数型（-128 ～ 127）                                              |
| uint8     | 符号無し 8-bit 整数型（0 ～ 255）                                                 |
| int16     | 符号付き 16-bit 整数型（-32,768 ～ 32,767）                                       |
| uint16    | 符号無し 16-bit 整数型（0 ～ 65,535）                                             |
| int32     | ★ 符号付き 32-bit 整数型（-2,147,483,648 ～ 2,147,483,647）                       |
| uint32    | ★ 符号無し 32-bit 整数型（0 ～ 4,294,967,295）                                    |
| int64     | 符号付き 64-bit 整数型（-9,223,372,036,854,775,808 ～ 9,223,372,036,854,775,807） |
| uint64    | 符号無し 64-bit 整数型（0 ～ 18,446,744,073,709,551,615）                         |
| int128    | 符号付き 128-bit 整数型                                                        |
| uint128   | 符号無し 128-bit 整数型                                                        |
| float     | 単精度浮動小数点数型                                                              |
| double    | ★ 倍精度浮動小数点数型                                                            |
| size_t    | ★ オブジェクトのサイズを表現する符号無し 64-bit 整数型（0 ～ 18,446,744,073,709,551,615）        |
| BigInt    | 任意精度多倍長整数型                                                              |
| HalfFloat | 半精度浮動小数点数型                                                              |
| BigFloat  | 有効数字 100 桁の浮動小数点数型                                                      |


#### 文字や文字列

| 型名           | 説明                              |
|--------------|---------------------------------|
| char8        | UTF-8 の 1 要素（`char` の別名）        |
| char16       | UTF-16 の 1 要素（`char16_t` の別名）   |
| char32       | ★ UTF-32 の 1 要素（`char32_t` の別名） |
| String       | ★ 文字列クラス。要素は `char32`           |
| StringView   | 文字列のビュークラス                      |
| FilePath     | ★ ファイルパス文字列（`String` の別名）       |
| FilePathView | ファイルパス文字列のビュー（`StringView` の別名） |


#### データ構造

| 型名                                                        | 説明                                                         |
|-----------------------------------------------------------|------------------------------------------------------------|
| Array&lt;Type, Allocator&gt;                              | ★ 動的配列（C++ 標準ライブラリの `std::vector` の置き換え）                   |
| Grid&lt;Type, Allocator&gt;                               | ★ 動的な二次元配列                                                 |
| HashSet&lt;Type, Hash, Eq, Alloc&gt;                      | ★ ハッシュテーブルによる Set（C++ 標準ライブラリの `std::unordered_set` の置き換え） |
| HashTable&lt;Key, Value, Hash, Eq, Alloc&gt;              | ★ ハッシュテーブルによる Map（C++ 標準ライブラリの `std::unordered_map` の置き換え） |
| None_t                                                    | `Optional` 型で無効値を表現する型（`std::nullopt_t` の別名）               |
| Optional&lt;Type&gt;                                      | ★ 無効値を表現できる型（C++ 標準ライブラリの `std::optional` の置き換え）           |
| std::array&lt;Type, size_t&gt;                            | ★ 固定長配列                                                    |

Siv3D で整数を扱うときは、`int32`, `uint64` のような明示的なサイズを持つ型名を使い、`int`, `unsigned long long` のような標準の型名は使いません。前者のような型名を使うことで、プラットフォーム間での移植性が高まり、一貫性のある読みやすいコードになります。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/2/9.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	bool a = true;
	int32 b = 123;
	double c = 0.5;
	BigInt d = 111222333444555666777888999000_big;
	BigFloat e = 0.1234567890123456789_bigF;
	Byte f{ 0xF7 };
	char32 g = U'あ';
	String h = U"Hello!";
	Array<int32> i = { 10, 20, 30, 40 };
	Array<String> j = { U"aaa", U"bbb" };
	HalfFloat k = 3.333333f;

	Print << a;
	Print << b;
	Print << c;
	Print << d;
	Print << e;
	Print << f;
	Print << g;
	Print << h;
	Print << i << U" : " << i.size();
	Print << j << U" : " << j.size();
	Print << k << U" : " << sizeof(k);

	while (System::Update())
	{

	}
}
```


## 2.10 `Print` 以外の出力
`Print` 以外にも、コンソール出力 `Console`, ログ出力 `Logger`, 音声読み上げ出力 `Say` を使えます。
```cpp
# include <Siv3D.hpp>

void Main()
{
	// コンソール画面に出力
	Console << U"Hello, Console!";

	// ログに出力 (Visual Studio の場合「出力」ウィンドウ）
	Logger << U"Hello, Logger!";

	// 必要に応じて読み上げ言語を設定（OS に読み上げエンジンがインストールされていない場合は使えない）
	TextToSpeech::SetDefaultLanguage(LanguageCode::EnglishUS);
	//TextToSpeech::SetDefaultLanguage(LanguageCode::Japanese);

	// 音声読み上げ出力
	Say << U"Hello, Say!";

	while (System::Update())
	{

	}
}
```


## 2.11 画面の座標系
ウィンドウ内の黒い部分が **画面（シーン）** です。Siv3D はこの領域に文字や図形、画像を表示できます。

画面のサイズは、基本の状態では **幅 800 ピクセル、高さ 600 ピクセル** です。画面上の位置を表す座標は、一番左上のピクセルを「X 座標が 0」「Y 座標が 0」を表す `(0, 0)` と表記し、右に進むと X 座標が大きく、下に進むと Y 座標が大きくなります。画面の一番右下のピクセルの座標は `(799, 599)` です。 

`Cursor::Pos()` を使うと、現在のマウスカーソルの座標を `Point` 型で取得できます。`Point` 型の値は X 座標を表す `int32 x` と Y 座標を表す `int32 y` の 2 つの成分を持っています。`Point` 型の値をそのまま丸ごと `Print` に送って表示することもできます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/tutorial/2/11.png)
```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		Print << Cursor::Pos(); // 現在のマウスカーソル座標を表示

		Print << U"X: " << Cursor::Pos().x; // X 座標だけを表示

		Print << U"Y: " << Cursor::Pos().y; // Y 座標だけを表示
	}
}
```


## 2.12 アプリケーションの基本操作

### プログラムを終了してウィンドウを閉じる

- ウィンドウを閉じる
- ++esc++ を押す
- プログラムが `System::Exit()` を呼ぶ

この動作をカスタマイズする方法は、チュートリアル 2.4 を参照してください。

### スクリーンショットを保存する

- ++print-screen++ を押す
- ++f12++ を押す

実行ファイルと同じディレクトリの `Screenshot` フォルダ、またはピクチャフォルダに実行中の画面のスクリーンショットが保存されます。

!!! tip "上級者向け"
	`ScreenCapture::SetShortcutKeys()` を使って、ショートカットキーをカスタマイズできます。

	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// [A] キーが押されたときにスクリーンショットを保存するよう設定
		ScreenCapture::SetShortcutKeys({ KeyA });

		while (System::Update())
		{
			Circle{ Scene::Center(), 100 }.draw();
		}
	}
	```


### ライセンス情報を表示する

- ++f1++ を押す

アプリケーションで使われているサードパーティ・ソフトウェアのライセンス情報を Web ブラウザで表示します。

!!! tip "上級者向け"
	`LicenseManager::` 名前空間の関数を使い、ライセンス情報を追加したり、一覧を取得したりできます。`LicenseManager::ShowInBrowser()` を呼ぶことで明示的にオープンすることもできます。

	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		while (System::Update())
		{
			// "Licenses" ボタンが押されたら
			if (SimpleGUI::Button(U"Licenses", Vec2{ 20, 20 }))
			{
				// ライセンス情報を表示
				LicenseManager::ShowInBrowser();
			}
		}
	}
	```

