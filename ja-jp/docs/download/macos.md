# macOS で Siv3D プログラミングを始める

## 1. システム要件
### 1.1 開発者システム要件
- macOS で Siv3D プログラミングをするのに必要な開発環境は次のとおりです：

|  |  |
|--|--|
| OS | macOS Ventura / Sonoma / Sequoia |
| CPU | Intel 製の CPU / Apple Silicon（Rosetta モード） |
| GPU | OpenGL 4.1 サポート |
| 映像出力 | モニタなど、何らかの映像出力装置があること |
| 開発環境 | Xcode 14.3 以降 |

- Apple Silicon（M1 - M4）には、現在開発中の Siv3D v0.8.0 からネイティブ対応します
- それまでは Rosetta モードで動作します

??? summary "Xcode をインストールできない場合"
	- 使用している macOS の OS バージョンが最新でない場合、App Store 経由で Xcode をインストールできないことがあります
	- その場合は [Apple Developer サイト :material-open-in-new:](https://developer.apple.com/download/more/){:target="_blank"} から、Xcode 14.3 など過去のバージョンの Xcode をダウンロードしてインストールします

### 1.2 アプリ動作システム要件
- Siv3D v0.6.16 で開発されたアプリケーションを macOS で実行するのに必要な環境は次のとおりです：

|  |  |
|--|--|
| OS | macOS Mojave / Catalina / Big Sur / Monterey / Ventura / Sonoma / Sequoia |
| CPU | Intel 製の CPU / Apple Silicon（Rosetta モード） |
| GPU | OpenGL 4.1 サポート |
| 映像出力 | モニタなど、何らかの映像出力装置があること |

- Apple Silicon（M1 - M4）には、現在開発中の Siv3D v0.8.0 からネイティブ対応します
- それまでは Rosetta モードで動作します


## 2. プロジェクトテンプレートをダウンロードする
1. **[OpenSiv3D v0.6.16 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.16_macOS.zip){:target="_blank"}** をダウンロードしてファイルを展開します
1. macOS Catalina 以降の場合、プログラム実行時に、毎回ファイルアクセス許可のダイアログが出現します。これを回避するには、プロジェクトフォルダを `(ユーザ名)/デスクトップ` や `(ユーザ名)/ダウンロード` フォルダではなく、`(ユーザ名)/アプリケーション` フォルダ（root のアプリケーションフォルダではなく、ユーザホームのアプリケーションフォルダ）へ移動させます

??? summary "過去のバージョン"
	- 過去のバージョンの利用は非推奨ですが、必要な場合に限り、下記からダウンロードできます：
		- コンパイラの更新等により、最新の開発環境では過去のバージョンでのビルドに失敗することがあります
		- 古い Siv3D プロジェクトを動かしたい場合は、そのソースコードを最新版のプロジェクトへ移植するのが良い方法です

	- [OpenSiv3D v0.6.15 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.15_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.14 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.14_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.13 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.13_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.12 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.12_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.11 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.11_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.10 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.10_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.9 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.9_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.8 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.8_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.7 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.7_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.6 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.6_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.5 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.5_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.4 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.4_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.3 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.3_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.2 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.2_macOS.zip){:target="_blank"}


## 3. Siv3D アプリをビルドする
1. プロジェクトテンプレートの中にあるプロジェクトファイル `examples/empty/empty.xcodeproj` を Xcode で開きます
1. サンプルプログラム（Main.cpp）が最初から用意されています
1. M1 - M4 Mac の場合は、後述の手順で Rosetta モードを有効にします
1. **実行ボタン ▶️** を押すと、プログラムをビルドして実行します
1. 実行中のプログラムは、++esc++ を押すか、ウィンドウを閉じると終了します

??? summary "M1 - M4 Mac における Rosetta モードの有効化"
	- Xcode で Rosetta オプションを表示する方法は、Xcode のバージョンによって異なります：
		- Xcode 15.3 以降： メニューバーから **Product &gt; Destination &gt; Show All Run Destinations** を押します
		- Xcode 15.2 以前： メニューバーから **Product &gt; Destination &gt; Destination Architectures** から、**Show Rosetta Destinations** を選択します
	- Rosetta オプションが表示されたら、それを選択します

??? summary "サンプルプログラムを実行するときのファイルアクセス許可のダイアログの回避"
	- macOS Catalina 以降で実行のたびにファイルアクセス許可のダイアログが出現することがあります
	- プロジェクトフォルダ全体を、`(ユーザ名)/デスクトップ` や `(ユーザ名)/ダウンロード` フォルダではなく、`(ユーザ名)/アプリケーション` フォルダ（root のアプリケーションフォルダではなく、ユーザホームのアプリケーションフォルダ）以下へ移動させることで回避できます

??? summary "新しいプロジェクトを増やしたい場合は"
	- プロジェクトテンプレートフォルダ内にある `empty` フォルダを同じ階層にコピーしてください
	- Xcode 用のプロジェクトジェネレータは将来提供予定です

??? warning "実行はできたが、エラーのような赤い表示がエディタに出る場合"
	- 最近のバージョンの Xcode で確認されている不具合です。実行には影響ありません
	- 現在開発中の Siv3D v0.8 では解消されます。しばらくお待ちください
