description: OpenSiv3D 本体をソースコードからビルドする手順

# OpenSiv3D 本体のビルド手順

1. GitHub から [OpenSiv3D のソースコード](https://github.com/Siv3D/OpenSiv3D) をダウンロードします
2. [指定バージョンの Boost](https://github.com/Siv3D/OpenSiv3D/tree/master/Dependencies) をダウンロードします
3. `OpenSiv3D/Dependencies` フォルダに Boost をコピーします。ビルドに必要なのは `boost_1_7?_0/boost/` フォルダにあるヘッダ (125MB 程度）だけで、`OpenSiv3D/Dependencies/boost_1_7?_0/boost/...` のような配置になります
4. プロジェクトをビルドします

Windows の場合: `OpenSiv3D/WindowsDesktop/OpenSiv3D.sln`

macOS の場合: `OpenSiv3D/macOS/OpenSiv3D.xcodeproj`


