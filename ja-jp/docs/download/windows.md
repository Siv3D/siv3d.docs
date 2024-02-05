# Windows で Siv3D プログラミングを始める

## 1. システム要件
### 1.1 開発者システム要件
Windows で Siv3D プログラミングをするのに必要な開発環境は次のとおりです。

|  |  |
|--|--|
| OS | Windows 10 (64-bit) /  Windows 11 |
| CPU | Intel または AMD 製の CPU |
| 映像出力 | モニタなど、何らかの映像出力装置があること |
| 音声出力 | 何らかの音声出力装置があること |
| 開発環境 | Microsoft Visual C++ 2022 17.7<br>(インストーラ内で「C++ によるデスクトップ開発」を追加インストールしてください) |

??? summary "Visual Studio のエディションについて"
	Windows 10, Windows 11 のパソコンで Siv3D プログラミングをする場合は**「Visual Studio Community 2022（ビジュアル・スタジオ・コミュニティ 2022）」**を使うのが便利です。これは世界中のプロフェッショナルのソフトウェア開発者が使う「Visual Studio」という統合開発環境の無料版です。学生、個人、少規模の開発であれば、Visual Studio の有料版と同じ機能を無料で使えます。

??? summary "Visual Studio のインストールについて"
	[Visual Studio ダウンロードページ :material-open-in-new:](https://visualstudio.microsoft.com/ja/downloads/){:target="_blank"} から**「Visual Studio 2022 コミュニティ」**のインストーラをダウンロードし実行します。

	インストーラを実行すると、プログラミング言語や開発ツールを選択する、次のような画面が出てきます。この中から**「C++ によるデスクトップ開発」**を選択します（右側の「インストールの詳細」に表示される項目は Visual Studio のバージョンによって異なるため、気にする必要はありません）。

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/vs_installer_desktop.png)

	そのまま右下の 「インストール」 ボタンを押せば、C++ プログラミングに必要なツールのインストールが始まります。

### 1.2 アプリ動作システム要件
Windows で Siv3D v0.6.14 を使って開発されたアプリケーションを実行するのに必要な環境は次のとおりです。ゲームやアプリを配布するときの説明書に記載すると良いでしょう。

|  |  |
|--|--|
| OS | Windows 7 SP1 (64-bit) / Windows 8.1 (64-bit) / Windows 10 (64-bit) /  Windows 11 |
| CPU | Intel または AMD 製の CPU |
| 映像出力 | モニタなど、何らかの映像出力装置があること |
| 音声出力 | 何らかの音声出力装置があること |

## 2. SDK をインストールする

1. **[OpenSiv3D v0.6.14 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.14_Installer.exe){:target="_blank"}** をダウンロードして実行します。
1. 実行時に「Windows によって PC が保護されました」と表示された場合は、**詳細情報**を押して**実行**を押します。

!!! info "インストール後に Windows の再起動を推奨"
	インストーラによって設定された環境変数を Visual Studio に確実に反映させるため、インストール後に Windows を再起動することを推奨します。

??? warning "どうしても失敗する場合は"
	インストーラの実行に失敗する場合は、このページの「(補足) SDK を手動インストールする」の方法で SDK をインストールしてください。

??? summary "インストーラが自動的に行うこと"
	- SDK の配置（デフォルトではドキュメントフォルダ）
	- SDK を配置したパスへのユーザ環境変数の設定
	- Siv3D プロジェクト用の Visual Studio プロジェクトテンプレートのコピー (通常は `ドキュメント/Visual Studio 2022/Templates/ProjectTemplates/`)
	- アンインストーラの登録

??? summary "インストールした OpenSiv3D SDK を削除するには"
	OpenSiv3D SDK は、通常のアプリケーションと同様、Windows の「設定」からアンインストールします。

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/uninstall.png)

??? summary "過去のバージョン"
	過去のバージョンの利用は非推奨です。必要な場合に限り、下記からダウンロードしてください。  
	コンパイラの更新等により、最新の開発環境では過去のバージョンを利用できないことがあります。古い Siv3D プロジェクトをビルドしたい場合は、そのソースコードを最新版のプロジェクトへ移植するのが良い方法です。

	- [OpenSiv3D v0.6.13 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.13_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.12 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.12_Installer.exe){:target="_blank"}
		- [Visual Studio 2022 17.8 以降でのコンパイルエラーを消す手順](https://github.com/Siv3D/OpenSiv3D/issues/1136){:target="_blank"}
	- [OpenSiv3D v0.6.11 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.11_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.10 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.10_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.9 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.9_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.8 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.8_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.7 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.7_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.6 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.6_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.5 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.5_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.4 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.4_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.3 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.3_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.2 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.2_Installer.exe){:target="_blank"}

## 3. Siv3D プロジェクトを作成する

<video src="https://github.com/Siv3D/siv3d.site.resource/blob/main/v7/download/msvc.mp4?raw=true" autoplay loop muted playsinline></video>

1. Visual Studio のスタート画面で **新しいプロジェクトの作成** をクリックします。
1. プロジェクト テンプレートの項目から **OpenSiv3D** を選択し、**次へ** を押します。
1. プロジェクト名と保存場所を入力し（任意）、**作成** を押します。


## 4. Siv3D アプリをビルドする
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/download/msvc.png)

1. プロジェクトを作成すると、サンプルプログラム (Main.cpp) が最初から用意されています。
1. **ビルド** メニューからプロジェクトをビルドします。
1. **デバッグ** メニューの **デバッグの開始** を押すと、ビルドしたプログラムが実行されます。
1. 実行中のプログラムは、++esc++ を押すか、ウィンドウを閉じると終了します。

??? warning "「Siv3D.hpp が見つからない」エラーが出る場合"
	インストーラによって設定された Siv3D SDK のインストール先の情報が、Visual Studio に反映されていないのが原因です。Windows を再起動し、再度新しい Siv3D プロジェクトを作成すると解決します。

## （補足）SDK を手動インストールする
OpenSiv3D インストーラが正常に実行されない場合、代わりに手作業で OpenSiv3D をインストールできます。手順は次のとおりです。

??? summary "SDK を手動インストールする場合の手順"
	### SDK ファイルの配置と環境変数の設定

	1. [OpenSiv3D_SDK_0.6.14.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.14/OpenSiv3D_SDK_0.6.14.zip) をダウンロードして展開し、中身をドキュメントフォルダ（`.../Documents`）に次のように配置します。
		- `.../Documents/OpenSiv3D_SDK_0.6.14/addon`
		- `.../Documents/OpenSiv3D_SDK_0.6.14/include`
		- `.../Documents/OpenSiv3D_SDK_0.6.14/lib`
	2. ユーザー環境変数 `SIV3D_0_6_14` を新規作成し、1. で配置した OpenSiv3D SDK のフォルダのパスを設定します。
		- 例: `C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.14/include` のように配置した場合、`C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.14` を環境変数 `SIV3D_0_6_14` に設定します。

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/envvariable.png)  

	### Visual Studio プロジェクトテンプレートの配置

	1. Visual Studio 用プロジェクトテンプレート [OpenSiv3D_0.6.14.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.14/OpenSiv3D_0.6.14.zip) (サイズ: 約 63 MB) をダウンロードし、そのファイルを**展開せず ZIP ファイルのまま**、Visual Studio 2022 インストール時にドキュメントフォルダに作成される `Visual Studio 2022/Templates/ProjectTemplates/` フォルダの中に配置します。  

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/projecttemplate.png)  

	以上で手動インストールの手順は完了です。環境変数の適用を確実にするために PC を再起動したのち、本ページの 3. の手順に進んでください。
