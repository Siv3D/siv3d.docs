# Web 向けの Siv3D プログラミングを始める
Web ブラウザで実行できる Siv3D が非公式で提供されています。Web 版にはいくつか制約や注意点があるため、**Siv3D の使用に慣れた中級者以上を対象**としています。利用にあたって困ったことがあれば、Siv3D Discord サーバの `#web` チャンネルで質問してください。

## 1. 開発環境のセットアップ

### 1.1 共通の準備
- [OpenSiv3D for Web :material-open-in-new:](https://siv3d.kamenokosoft.com/docs/ja/){:target="_blank"} を参照してください。
	- Visual Studio 2022 の場合は、全部で 3 つのソフトウェアをダウンロード・インストールします。

!!! info "有志による最新版を入手する"
	上記では Siv3D v0.6.6 相当の SDK が提供されていますが、より新しいバージョンの Siv3D への準拠度を高めた SDK が有志によって開発・提供されています。上記 [OpenSiv3D for Web のインストール手順 :material-open-in-new:](https://siv3d.kamenokosoft.com/docs/ja/){:target="_blank"} で導入するソフトウェアのうち、Siv3D SDK について下記リンクからダウンロードできる SDK に置き換えてください。

	- [OpenSiv3D for Web SDK :material-open-in-new:](https://gist.github.com/Raclamusi/449a2c2f5bc23a271eb8dc844bf24d54){:target="_blank"}


## 2. Web 版プロジェクトのビルド
- Web 版 Siv3D のビルド所用時間は、通常の Siv3D の数倍以上です。普段の開発は通常の Siv3D で行い、Web 版での動作確認は必要に応じて行うことが推奨されます。
- Visual Studio では、正しいコードであってもエディタ上に赤波線やエラーメッセージが表示されることがあります。
- Visual Studio では「▶」でビルド・実行ができますが、Google Chrome をインストールしていない場合、ビルド完了後に実行がされないことがあります。
- 生成された `.html` は、ローカル環境ではダブルクリックで実行することはできません。デバッガ経由で起動するか、VS Code 拡張の [Live Preview :material-open-in-new:](https://marketplace.visualstudio.com/items?itemName=ms-vscode.live-server){:target="_blank"} を利用してください。


## 3. プログラムの公開
- ビルドによって最終的に生成された 4 つのファイル（`.html`, `.js`, `.data`, `.wasm`）すべてを Web サーバにアップロードします。
	- GitHub Pages などの無料の Web サービスを利用すると便利です。
- `.html` の `<title>` タグを手作業で書き換えることで、Web ページのタイトルを変更できます。
- Web 版のビルドでは、デフォルトで `resource/engine/` と `example/` のすべてを最終生成ファイルに同梱するため、ファイルサイズは Release ビルドでも合計数十 MB と大きくなります。これらを Web で公開すると、アクセスした利用者がファイルのダウンロードに時間がかかってしまうため、実際にアプリケーションを公開する際は、**不要な同梱ファイルを除外**してください（参考: [チュートリアル 60. アプリの公開](../tutorial3/release.md){:target="_blank"}）。
- Emscripten リンカの設定において「追加の依存ファイル」からプログラムが使用していないライブラリ（例: `Siv3DScript`, `opencv_objdetect`, `opencv_photo`, `turbojpeg`, `gif`, `webp`, `opusfile`, `opus`, `tiff`）を削除することで、Web 版の出力ファイルのサイズをさらに減らすことができます。
- 最もコンパクトな場合、数 MB 以下のファイルサイズになります。

### Visual Studio での設定例
#### 同梱フォルダの指定
- `$(ProjectDir)\フォルダ名@/フォルダ名`
	- 例えば `asset` フォルダを同梱する場合は `$(ProjectDir)\asset@/asset` と指定します。
	- `example` フォルダを同梱しない場合は `$(ProjectDir)\example@/example` を削除します。
<div class="noshadow-75"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/download/web1.png"></div>

#### 依存ファイルの削除
- プログラムが使用していないライブラリをここから削除します。
<div class="noshadow-75"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/download/web2.png"></div>


## 4. Web 版のサポート
Web 版 Siv3D は非公式ですが、Siv3D Discord サーバの `#web` チャンネルで質問や報告を受け付けています。

