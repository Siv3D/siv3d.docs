description: OpenSiv3D 本体をソースコードからビルドする手順

!!! warning "このページよりも新しいドキュメントがあります"
	このドキュメントは古い OpenSiv3D v0.4.3 向けです。2021 年9 月 18 日に最新の OpenSiv3D v0.6.0 がリリースされました。最新のドキュメントは [Siv3D リファレンス v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation) です。

# OpenSiv3D 本体のビルド手順

1. GitHub から [OpenSiv3D のソースコード](https://github.com/Siv3D/OpenSiv3D) をダウンロードします
2. [指定バージョンの Boost](https://github.com/Siv3D/OpenSiv3D/tree/master/Dependencies) をダウンロードします
3. `OpenSiv3D/Dependencies` フォルダに Boost をコピーします。ビルドに必要なのは `boost_1_7?_0/boost/` フォルダにあるヘッダ (125MB 程度）だけで、`OpenSiv3D/Dependencies/boost_1_7?_0/boost/...` のような配置になります
4. プロジェクトをビルドします

Windows の場合: `OpenSiv3D/WindowsDesktop/OpenSiv3D.sln`

macOS の場合: `OpenSiv3D/macOS/OpenSiv3D.xcodeproj`


