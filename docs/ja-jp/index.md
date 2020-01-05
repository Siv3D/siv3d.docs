description: ゲームやクリエイティブコーディングのための C++17/C++20 フレームワーク OpenSiv3D

# Siv3D: クリエイターのための C++ ライブラリ

![](images/demo.gif)

```C++
# include <Siv3D.hpp>

void Main()
{
	// 背景を水色にする
	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));

	// 大きさ 60 のフォントを用意
	const Font font(60);

	// 猫のテクスチャを用意
	const Texture cat(Emoji(U"🐈"));

	// 猫の座標
	Vec2 catPos(640, 450);

	while (System::Update())
	{
		// テキストを画面の中心に描く
		font(U"Hello, Siv3D!🐣").drawAt(Scene::Center(), Palette::Black);

		// 大きさをアニメーションさせて猫を表示する
		cat.resized(100 + Periodic::Sine0_1(1s) * 20).drawAt(catPos);

		// マウスカーソルに追従する半透明の赤い円を描く
		Circle(Cursor::Pos(), 40).draw(ColorF(1, 0, 0, 0.5));

		// [A] キーが押されたら
		if (KeyA.down())
		{
			// Hello とデバッグ表示する
			Print << U"Hello!";
		}

		// ボタンが押されたら
		if (SimpleGUI::Button(U"Move the cat", Vec2(600, 20)))
		{
			// 猫の座標を画面内のランダムな位置に移動する
			catPos = RandomVec2(Scene::Rect());
		}
	}
}
```

# Siv3D をはじめよう
## 必要な環境
### Windows
- Windows 7 SP1 / 8.1 / 10 (64-bit)
- **Visual Studio 2019 version 16.3**
    - Visual Studio Installer で **C++ によるデスクトップ開発** をインストールしてください

### macOS
- macOS High Sierra v10.13 以降
- Xcode 10.1 以降

### Linux
OpenSiv3D Linux 版は、ソースコードからビルドする必要があります。依存しているライブラリやビルド方法については [Linux/README](https://github.com/Siv3D/OpenSiv3D/blob/master/Linux/README_JP.md) を参照してください。

## OpenSiv3D SDK v0.4.2 のインストール
### Windows
1. **[OpenSiv3D Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D(0.4.2)Installer.exe)** をダウンロードして実行します。

!!! note
    OpenSiv3D SDK を削除するには、コントロールパネルからアンインストールします。

### macOS
1. **[OpenSiv3D Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.4.2_macOS.zip)** をダウンロードしてファイルを展開します。
2. （macOS Catalina の場合）プログラム実行時に、ファイルアクセス許可のダイアログが出現するのを防ぐため、`ユーザ/デスクトップ` や `ユーザ/ダウンロード` フォルダではなく、`ユーザ/アプリケーション` フォルダへ移動させます。

## OpenSiv3D アプリのビルド
### Windows
1. Visual Studio 2019 のスタート画面で **新しいプロジェクトの作成** をクリックし、**新しいプロジェクトの作成** ダイアログに進みます。
2. プロジェクトテンプレート一覧の中から **OpenSiv3D(インストールしたバージョン)** を選択し、**次へ** を押します。
3. プロジェクト名を入力します。
4. 入力した設定でプロジェクトを作成するために、**作成** を押します。
5. **ビルド** メニューからプロジェクトをビルドします。
6. **デバッグ** メニューの **デバッグの開始** でビルドしたプログラムを実行します。

### macOS
1. プロジェクトファイル (siv3d_vX.X.X_macOS/examples/empty/empty.xcodeproj) を Xcode で開きます。
2. **実行ボタン ▶️** を押すと、プログラムをビルドして実行します。
3. （macOS Catalina の場合）ファイルアクセス許可のダイアログが出現する場合、プロジェクトフォルダ全体を、`ユーザ/アプリケーション` フォルダ以下へ移動させることで解決できます。


## 💗 [スポンサー](https://github.com/sponsors/Reputeless)
- [sknjpn](https://twitter.com/sknjpn)
- アゲハマ
- chobby75
- papparappara
- (匿名 😀)
