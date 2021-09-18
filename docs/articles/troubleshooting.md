description: OpenSiv3D のトラブルシューティング

!!! warning "This is the documentation for an old version"
	This is the documentation for an old version of Siv3D (v0.4.3). See [Siv3D Reference v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation-en) for the latest version.

# トラブルシューティング

### インストール編

#### Visual Studio のプロジェクトテンプレート一覧に OpenSiv3D が表示されない

**原因 1: ユーザプロジェクト テンプレートの場所の設定が正しくありません**

Visual Studio のメニューで、「ツール」→「オプション」→「プロジェクトおよびソリューション」→「全般」→「場所」→「**ユーザプロジェクトテンプレートの場所**」が `~\Documents\Visual Studio 2019\Templates\ProjectTemplates` のように、ドキュメントフォルダ内の ProjectTemplates フォルダに設定されているかを確認してください。

**原因 2: Visual Studio が新しいプロジェクトテンプレートを認識していません**

Visual Studio のプロジェクト一覧を、再スキャンによって更新する必要があります。コマンドプロンプトを起動して `Program Files (x86)/Microsoft Visual Studio/2019/Community/Common7/IDE` へ移動し、`devenv /InstallVSTemplates` コマンドを実行すると、プロジェクトテンプレート一覧が更新されます。  

<img src="https://github.com/Siv3D/siv3d.docs.images/blob/master/article/devenv.png?raw=true" style="display: inline; margin:none;" width="400">

#### Linux にうまく導入できない

Siv3D ユーザコミュニティ Slack の `#linux` チャンネルでご相談ください。


### ビルド編

#### サンプルコードでビルドエラーが出る

原因: サンプルコードが想定しているバージョンと、インストールしている OpenSiv3D のバージョンが異なります

インストールしている OpenSiv3D のバージョンが最新であるか確認してください。また、サンプルコードが古い記事のものでないか確認してください。当サイトにあるサンプルプログラムは最新版の OpenSiv3D で動作します。

### 実行編

#### macOS 10.13 で動作しない

原因: Deployment Target が 10.14 になっています

Xcode の設定で、最低 OS 要件を示す Deployment Target は、デフォルトでは 10.14 に設定されています。10.13 に引き下げることで警告が発生することがありますが、macOS 10.13 で動作するアプリをビルドできます。
