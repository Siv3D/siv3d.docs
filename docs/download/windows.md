# Getting Started with Siv3D on Windows

## 1. System requirements
### 1.1 System requirements for development
Here are the system requirements for OpenSiv3D v0.6.7 programming on Windows.

|  |  |
|--|--|
| OS | Windows 10 (64-bit) /  Windows 11 |
| CPU | Intel / AMD CPU |
| Output Devices | Monitors and speakers |
| IDE | Microsoft Visual C++ 2022 17.4<br>(Install "Desktop development with C++" from the Visual Studio Installer) |

??? summary "Visual Studio のエディションについて"
	Windows 10, Windows 11 のパソコンで Siv3D プログラミングをする場合は**「Visual Studio Community 2022（ビジュアル・スタジオ・コミュニティ 2022）」**を使うのが便利です。これは世界中のプロフェッショナルのソフトウェア開発者が使う「Visual Studio」という統合開発環境の無料版です。学生、個人、少規模の開発であれば、Visual Studio の有料版と同じ機能を無料で使えます。

??? summary "Visual Studio のインストール手順について"
	[Visual Studio ダウンロードページ :material-open-in-new:](https://visualstudio.microsoft.com/ja/downloads/) から**「Visual Studio 2022 コミュニティ」**のインストーラをダウンロードし実行します。

	インストーラを実行すると、インストールするプログラミング言語や開発ツールを選択する次のような画面が出てきます。インストール項目の選択画面から**「C++ によるデスクトップ開発」**を選択します（右側の「インストールの詳細」に表示される項目は Visual Studio のバージョンによって異なるため、気にする必要はありません）。

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/vs_installer_desktop.png)

	そのまま右下の 「インストール」 ボタンを押せば、C++ プログラミングに必要なツールのインストールがはじまります。

### 1.2 System requirements for running Siv3D application
This is the environment required to run applications developed with OpenSiv3D v0.6.7 on Windows. You may want to include it in your instructions when distributing your game or application.

|  |  |
|--|--|
| OS | Windows 7 SP1 (64-bit) / Windows 8.1 (64-bit) / Windows 10 (64-bit) /  Windows 11 |
| CPU | Intel / AMD |
| Output Devices | Monitors and speakers |

## 2. Installing the Siv3D SDK

1. Download and run **[OpenSiv3D v0.6.7 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.7_Installer.exe)**.

??? warning "どうしても失敗する場合は"
	インストーラの実行に失敗する場合は、このページの「(補足) SDK を手動インストールする」の方法で SDK をインストールしてください。

??? summary "The installer will automatically do the following:"
	- Create a SDK folder (The default location is `Documents`).
	- Set a user environment variable "SIV3D_0_6_7" with the path to the SDK folder.
	- Copy the Visual Studio project template for the Siv3D project (The default locations is `Documents/Visual Studio 2022/Templates/ProjectTemplates/`).
	- Register the uninstaller.

??? summary "If you want to uninstall"
	OpenSiv3D SDK は、通常のアプリケーションと同様、Windows の「設定」からアンインストールします。

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/uninstall.png)


## 3. Creating a new Siv3D project
<video src="https://github.com/Siv3D/siv3d.site.resource/blob/main/v6/download/windows/create_project.mp4?raw=true" autoplay loop muted></video>

1. Lanuch Visual Studio and open a **New Project Dialog** by clicking **Create a new project**.
1. Select **OpenSiv3D(X.X.X)** project and then click **Next**.
1. Type a name for the project and click **OK** to create the project.


## 4. Building your first application with Siv3D
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/hellosiv3d.png)

1. After creating a project, a sample code (Main.cpp) will appear.
1. On the **Build** menu, click **Build Solution**.
1. On the **Debug** menu, click **Start Debugging**.
1. The running program can be terminated by pressing the ++esc++ key or by closing the window.

## (Appendix) Manually installing the Siv3D SDK
If you have trouble with the SDK installer in Windows, you can manually install the Siv3D SDK.

??? summary "SDK を手動インストールする場合の手順"
	### Getting the Siv3D SDK and setting the SDK folder path to the environment variable

	1. Download and extract [OpenSiv3D_SDK_0.6.7.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.7/OpenSiv3D_SDK_0.6.7.zip) (File size: 88 MB), and place the contents in your documents folder as follows:
		- `.../Documents/OpenSiv3D_SDK_0.6.7/addon`
		- `.../Documents/OpenSiv3D_SDK_0.6.7/include`
		- `.../Documents/OpenSiv3D_SDK_0.6.7/lib`
	2. Create a new environment variable `SIV3D_0_6_7` and set the path to the SDK folder (the parent folder of the `addon/`, `include/`, and `lib/` folders)
		- Example: If you have placed `C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.7/include`, set `C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.7` to the environment variable `SIV3D_0_6_7`.

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/envvariable.png)  

	### Deploying the OpenSiv3D project template (ZIP)

	1. Visual Studio 用プロジェクトテンプレート [OpenSiv3D_0.6.7.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.7/OpenSiv3D_0.6.7.zip) (サイズ: 約 63 MB) をダウンロードし、そのファイルを**展開せず ZIP ファイルのまま**、Visual Studio 2022 インストール時にドキュメントフォルダに作成される `Visual Studio 2022/Templates/ProjectTemplates/` フォルダの中に配置します  

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/projecttemplate.png)  

	以上で手動インストールの手順は完了です。環境変数の適用を確実にするために PC を再起動したのち、本ページの 3. の手順に進んでください
