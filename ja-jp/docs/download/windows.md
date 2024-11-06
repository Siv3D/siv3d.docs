# Windows で Siv3D プログラミングを始める

## 1. システム要件
### 1.1 開発者システム要件
Windows PC での Siv3D プログラミングに必要な開発環境は次のとおりです。

|  |  |
|--|--|
| OS | Windows 10 (64-bit) /  Windows 11 |
| CPU | Intel または AMD 製の CPU |
| 映像出力 | モニタなど、何らかの映像出力装置があること |
| 音声出力 | 何らかの音声出力装置があること |
| 開発環境 | Microsoft Visual C++ 2022 17.11<br>（インストーラ内で「C++ によるデスクトップ開発」を追加インストールしてください） |

??? summary "インストールする Visual Studio のエディション"
	Windows で Siv3D プログラミングをする場合、**「Visual Studio Community 2022（ビジュアル・スタジオ・コミュニティ 2022）」**を使うのが便利です。これは世界中のプロのソフトウェア開発者が使う「Visual Studio」という統合開発環境の無料版です。学生や個人の開発であれば、Visual Studio の有料版と同じ機能を無料で使えます。

??? summary "Visual Studio のインストール手順"
	- [Visual Studio ダウンロードページ :material-open-in-new:](https://visualstudio.microsoft.com/ja/downloads/){:target="_blank"} から**「Visual Studio 2022 コミュニティ」**のインストーラをダウンロードし、実行します。
	- インストーラを実行すると、プログラミング言語や開発ツールを選択する、次のような画面が出てきます。この中から**「C++ によるデスクトップ開発」**を選択します（右側の「インストールの詳細」に表示される項目は Visual Studio のバージョンによって異なるため、気にする必要はありません）。

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/vs_installer_desktop.png)

	- そのまま右下の 「インストール」 ボタンを押せば、C++ プログラミングに必要なツールのインストールが始まります。

### 1.2 アプリ動作システム要件
Siv3D v0.6.15 を使って開発されたアプリケーションを Windows PC で実行するのに必要な環境は次のとおりです。ゲームやアプリを配布するときの説明書に記載すると良いでしょう。

|  |  |
|--|--|
| OS | Windows 7 SP1 (64-bit) / Windows 8.1 (64-bit) / Windows 10 (64-bit) /  Windows 11 |
| CPU | Intel または AMD 製の CPU |
| 映像出力 | モニタなど、何らかの映像出力装置があること |
| 音声出力 | 何らかの音声出力装置があること |

## 2. SDK をインストールする

1. **[OpenSiv3D v0.6.15 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.15_Installer.exe){:target="_blank"}** をダウンロードして実行します。
1. 実行時に「Windows によって PC が保護されました」と表示された場合は、**詳細情報**を押して**実行**を押します。

??? info "管理者権限がない場合"
	管理者権限がなく、インストーラの実行ができない場合、このページ下部の「(補足) SDK を手動インストールする」の方法で SDK をインストールしてください。

??? summary "インストーラが自動的に行うこと"
	Siv3D のインストーラは、以下の作業を行います。

	- SDK の配置（デフォルトではドキュメントフォルダ）
	- SDK を配置したパスへのユーザ環境変数の設定
	- Siv3D プロジェクト用の Visual Studio プロジェクトテンプレートのコピー (通常は `ドキュメント/Visual Studio 2022/Templates/ProjectTemplates/`)
	- アンインストーラの登録

??? summary "インストールした SDK を削除するには"
	OpenSiv3D SDK は、通常の Windows アプリケーションと同様、Windows の「設定」からアンインストールします。

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/uninstall.png)

??? summary "過去のバージョン"
	過去のバージョンの利用は非推奨ですが、必要な場合に限り、下記からダウンロードしてください。

	コンパイラの更新等により、最新の開発環境では過去のバージョンでのビルドに失敗することがあります。古い Siv3D プロジェクトを動かしたい場合は、そのソースコードを最新版のプロジェクトへ移植するのが良い方法です。

	- [OpenSiv3D v0.6.14 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.14_Installer.exe){:target="_blank"}
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

??? warning "プロジェクト テンプレートの項目に「OpenSiv3D」が見つからない場合"
	OneDrive が有効化されている最近の Windows PC では「ドキュメント」フォルダが 2 つ存在することがあります。その場合、Visual Studio が参照するドキュメントフォルダと、「Siv3D プロジェクト用の Visual Studio プロジェクトテンプレート」の配置されたドキュメントフォルダが一致せず、Visual Studio がプロジェクトテンプレートを見つけられない問題が発生します。
	
	まずは、`OpenSiv3D_●.●.●.zip` という**プロジェクトテンプレートファイル**が、`C:\Users\●●●\Documents\Visual Studio 2022\Templates\ProjectTemplates` に存在することを確認します。その後、以下の 2 つの解決法のいずれかを選びます。

	#### 解決法 A | Visual Studio が参照するドキュメントフォルダを変更する

	Visual Studio を起動し、**ツール** メニューから **オプション** を選択します。**プロジェクトおよびソリューション** > **場所** を選び、**ユーザー プロジェクト テンプレートの場所** を、OneDrive のドキュメントフォルダから、通常のドキュメントフォルダに変更します。

	- 変更前: `C:\Users\●●●\OneDrive\Documents\Visual Studio 2022\Templates\ProjectTemplates`
	- 変更後: `C:\Users\●●●\Documents\Visual Studio 2022\Templates\ProjectTemplates`

	#### 解決法 B | プロジェクトテンプレートを、Visual Studio が参照するドキュメントフォルダに移動させる

	「解決法 A」で指している OneDrive のフォルダへ、プロジェクトテンプレートファイルを移動させます。

	- 移動前: `C:\Users\●●●\Documents\Visual Studio 2022\Templates\ProjectTemplates\OpenSiv3D_●.●.●.zip`
	- 移動後: `C:\Users\●●●\OneDrive\Documents\Visual Studio 2022\Templates\ProjectTemplates\OpenSiv3D_●.●.●.zip`



## 4. Siv3D アプリをビルドする
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/download/msvc.png)

1. プロジェクトを作成すると、サンプルプログラム（Main.cpp）が最初から用意されています。
1. **ビルド** メニューからプロジェクトをビルドします。
1. **デバッグ** メニューの **デバッグの開始** を押すと、ビルドしたプログラムを実行します。
1. 実行中のプログラムは、++esc++ を押すか、ウィンドウを閉じると終了します。

??? warning "「Siv3D.hpp が見つからない」エラーが出る場合"
	インストーラによって設定された Siv3D SDK のインストール先を示す環境変数が、Visual Studio に反映されていないことが原因です。作成したプロジェクトは破棄し、Windows を再起動したのち、再度新しい Siv3D プロジェクトを作成することで解決します。

## （補足）SDK を手動インストールする
OpenSiv3D インストーラが正常に実行されない場合、代わりに手作業で OpenSiv3D をインストールできます。手順は次のとおりです。

??? summary "SDK を手動インストールする場合の手順"
	### SDK ファイルの配置と環境変数の設定

	1. [OpenSiv3D_SDK_0.6.15.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.15/OpenSiv3D_SDK_0.6.15.zip) をダウンロードして展開し、中身をドキュメントフォルダ（`.../Documents`）に次のように配置します。
		- `.../Documents/OpenSiv3D_SDK_0.6.15/addon`
		- `.../Documents/OpenSiv3D_SDK_0.6.15/include`
		- `.../Documents/OpenSiv3D_SDK_0.6.15/lib`
	2. ユーザー環境変数 `SIV3D_0_6_15` を新規作成し、1. で配置した OpenSiv3D SDK のフォルダのパスを設定します。
		- 例: `C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.15/include` のように配置した場合、`C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.15` を環境変数 `SIV3D_0_6_15` に設定します。

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/envvariable.png)  

	### Visual Studio プロジェクトテンプレートの配置

	1. Visual Studio 用プロジェクトテンプレート [OpenSiv3D_0.6.15.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.15/OpenSiv3D_0.6.15.zip) (サイズ: 約 63 MB) をダウンロードし、そのファイルを**展開せず ZIP ファイルのまま**、Visual Studio 2022 インストール時にドキュメントフォルダに作成される `Visual Studio 2022/Templates/ProjectTemplates/` フォルダの中に配置します。  

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/projecttemplate.png)  

	以上で手動インストールの手順は完了です。環境変数の適用を確実にするために PC を再起動したのち、本ページの 3. の手順に進んでください。
