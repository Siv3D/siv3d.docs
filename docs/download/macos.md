# Getting Started with Siv3D on macOS

## 1. システム要件
### 1.1 開発者システム要件
macOS で OpenSiv3D v0.6.4 プログラミングをするのに必要な開発環境です。

|  |  |
|--|--|
| OS | macOS Mojave / Catalina / Big Sur / Monterey |
| CPU | Intel 製の CPU / Apple Silicon (Rosetta モード) |
| GPU | OpenGL 4.1 サポート |
| 映像出力 | モニタなど、何らかの映像出力装置があること |
| 開発環境 | Xcode 11.3.1 以降 (Big Sur 以降の場合は Xcode 12.5 以降) |

- Apple Silicon (M1 / M2) のネイティブサポートは将来のバージョンで追加されます
- 2012 年以前の Mac 製品では GPU が OpenGL 4.1 をサポートしていない場合があります

??? summary "Xcode をインストールできない場合"
	使用している macOS の OS バージョンが最新でない場合、App Store 経由で Xcode をインストールできないことがあります。その場合は [Apple Developer サイト :material-open-in-new:](https://developer.apple.com/download/more/) から、Xcode 12.4 など過去のバージョンの Xcode をダウンロードしてください。


### 1.2 アプリ動作システム要件
macOS で OpenSiv3D v0.6.4 を使って開発されたアプリケーションを実行するのに必要な環境です。ゲームやアプリを配布するときの説明書に記載すると良いでしょう。

|  |  |
|--|--|
| OS | macOS Mojave / Catalina / Big Sur / Monterey |
| CPU | Intel 製の CPU / Apple Silicon (Rosetta モード) |
| GPU | OpenGL 4.1 サポート |
| 映像出力 | モニタなど、何らかの映像出力装置があること |

- Apple Silicon (M1 / M2) のネイティブサポートは将来のバージョンで追加されます
- 2012 年以前の Mac 製品では GPU が OpenGL 4.1 をサポートしていない場合があります


## 2. プロジェクトテンプレートのダウンロード
1. **[OpenSiv3D v0.6.4 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.4_macOS.zip)** をダウンロードしてファイルを展開します
1. macOS Catalina 以降の場合、プログラム実行時に、毎回ファイルアクセス許可のダイアログが出現します。これを回避するには、プロジェクトフォルダを `(ユーザ名)/デスクトップ` や `(ユーザ名)/ダウンロード` フォルダではなく、`(ユーザ名)/アプリケーション` フォルダ（root のアプリケーションフォルダではなく、ユーザホームのアプリケーションフォルダ）へ移動させます

## 3. Siv3D アプリのビルド
1. プロジェクトテンプレートの中にあるプロジェクトファイル `examples/empty/empty.xcodeproj` を Xcode で開きます
1. サンプルプログラム (Main.cpp) が最初から用意されています
1. **実行ボタン ▶️** を押すと、プログラムをビルドして実行します
1. 実行中のプログラムは、++esc++ を押すか、ウィンドウを閉じると終了します

??? summary "サンプルプログラムを実行するときのファイルアクセス許可のダイアログの回避"
    macOS Catalina 以降で実行のたびにファイルアクセス許可のダイアログが出現する場合、プロジェクトフォルダ全体を、`(ユーザ名)/デスクトップ` や `(ユーザ名)/ダウンロード` フォルダではなく、`(ユーザ名)/アプリケーション` フォルダ（root のアプリケーションフォルダではなく、ユーザホームのアプリケーションフォルダ）以下へ移動させることで回避できます。

??? summary "新しいプロジェクトを増やしたい場合は"
    プロジェクトテンプレートフォルダ内にある `empty` フォルダを同じ階層にコピーしてください。Xcode 用のプロジェクトジェネレータは将来提供予定です。
