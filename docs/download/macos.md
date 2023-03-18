# Getting Started with Siv3D on macOS

## 1. System requirements
### 1.1 System requirements for development
Here are the system requirements for OpenSiv3D v0.6.7 programming on macOS.

|  |  |
|--|--|
| OS | macOS Mojave / Catalina / Big Sur / Monterey |
| CPU | Intel CPU / Apple Silicon (Rosetta mode)* |
| GPU | OpenGL 4.1 compatible hardware |
| Output Devices | Monitors |
| IDE | Xcode 11.3 -13.2 (Big Sur requires Xcode 12.5 - 13.2) |

- Native Apple Silicon support will be added in the future release. You can build and run Siv3D in Rosetta mode
- 2012 年以前の Mac 製品では GPU が OpenGL 4.1 をサポートしていない場合があります

??? summary "Xcode をインストールできない場合"
	使用している macOS の OS バージョンが最新でない場合、App Store 経由で Xcode をインストールできないことがあります。その場合は [Apple Developer サイト :material-open-in-new:](https://developer.apple.com/download/more/) から、Xcode 12.4 など過去のバージョンの Xcode をダウンロードしてください。


### 1.2 System requirements for running Siv3D application
This is the environment required to run applications developed with OpenSiv3D v0.6.7 on macOS. You may want to include it in your instructions when distributing your game or application.

|  |  |
|--|--|
| OS | macOS Mojave / Catalina / Big Sur / Monterey |
| CPU | Intel CPU / Apple Silicon (Rosetta mode)* |
| GPU | OpenGL 4.1 compatible hardware |
| Output Devices | Monitors |

- Native Apple Silicon support will be added in the future release. You can run Siv3D Application in Rosetta mode
- 2012 年以前の Mac 製品では GPU が OpenGL 4.1 をサポートしていない場合があります


## 2. Downloading the Siv3D project template
1. Download and extract **[OpenSiv3D v0.6.7 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.7_macOS.zip)**.
1.  (On macOS Catalina or later) Move the SDK folder into `(User name)/Applications` folder to prevent a file access permissions dialog from being displayed when your program launches. Some folders such as `(User name)/Desktop` and `(User name)/Downloads` require an access permission for every build.

## 3. Building your first application with Siv3D
1. Open the project file `examples/empty/empty.xcodeproj` in Xcode.
1. Open `Main.cpp` from the project menu
1. Click **Run button ▶️** to build and run the application.
1. The running program can be terminated by pressing the ++esc++ key or by closing the window.

??? summary "サンプルプログラムを実行するときのファイルアクセス許可のダイアログの回避"
    macOS Catalina 以降で実行のたびにファイルアクセス許可のダイアログが出現する場合、プロジェクトフォルダ全体を、`(ユーザ名)/デスクトップ` や `(ユーザ名)/ダウンロード` フォルダではなく、`(ユーザ名)/アプリケーション` フォルダ（root のアプリケーションフォルダではなく、ユーザホームのアプリケーションフォルダ）以下へ移動させることで回避できます。

??? summary "新しいプロジェクトを増やしたい場合は"
    プロジェクトテンプレートフォルダ内にある `empty` フォルダを同じ階層にコピーしてください。Xcode 用のプロジェクトジェネレータは将来提供予定です。
