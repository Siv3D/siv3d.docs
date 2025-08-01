# 57. 埋め込みリソース
アプリケーションの実行ファイルに、画像や音声などのファイルを埋め込み、それをプログラムで読み込む方法を学びます。

## 57.1 埋め込みリソースの概要
- プログラムで読み込む画像や音声、テキストなどのファイルを、実行ファイル (.exe や .app) に埋め込み、ユーザから見てアプリケーションが単一のファイルになるようまとめることができます
- 実行ファイルに埋め込まれたファイルを **埋め込みリソース** と呼びます
- 埋め込みリソースを活用することで、アプリケーションの配布が簡単になります
- また、プログラムの実行に必要なファイルがユーザによって誤って削除されたり、変更されたりするトラブルを抑制できます
- 埋め込みリソースは **読み込み専用** です。プログラムから削除したり書き換えたりすることはできません

## 57.2 埋め込みの方法
- アプリケーションの実行ファイルにリソースファイルを埋め込む手順は、プラットフォームごとに異なります

### 57.2.1 Windows
- `App/Resource.rc` に、埋め込みたいファイルのパスを記述します
- Visual Studio のソリューション エクスプローラー上で `App/Resource.rc` を右クリックし、「コードの表示」で開き、埋め込みたいファイルのパスを、ファイルごとに `Resource(ファイルパス)` という形式で記述します
	- ダブルクォーテーションや空白の使用は禁止です
- 次の画像は、`example/windmill.png` を埋め込みリソースにする例です（130 行目）：

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/resource/2a.png)

- `App/Resource.rc` にはもともと、Siv3D の内部処理に必要な `engine/` フォルダの各種ファイルが記述されています
- それらに続けて、新しい埋め込みファイルを記述します
- .rc ファイルを更新したら、プロジェクトを再ビルドすることで .exe にファイルが埋め込まれます
	- 埋め込みによって、ビルド後に生成される実行ファイルのサイズが大きくなることも確認できます

!!! warning "Windows での埋め込みリソース利用の注意"
	- Windows 版の Siv3D では、一部の種類のファイルについて、埋め込みをすると正常に読み込めなくなるものがあります（`VideoTexture` で使う動画ファイルなど）
	- 埋め込みリソースを [一時ファイルに書き出す :material-open-in-new:](https://gist.github.com/Reputeless/3d527302d459792f7a5e1094d30d0529){:target="_blank"} というワークアラウンドがあります

### 57.2.2 macOS
- Xcode のプロジェクトナビゲータにフォルダをドラッグし「Create folder references」を選択すると、プロジェクトナビゲータ上で青いフォルダアイコンになって表示されます
- このようなフォルダ内のファイルはすべて .app に埋め込まれます
- プロジェクトの初期状態では Siv3D の内部処理に必要な `engine/` フォルダが同様の方法で埋め込みリソースになっています

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/resource/2b.png)

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/resource/2c.png)

### 57.2.3 Linux
- Linux 版では埋め込みリソースはサポートされていません
- 代わりに、`resources/` フォルダに必要なリソースファイルを格納し、アプリケーションに同梱します
- 初期状態では、Siv3D の内部処理に必要な `engine/` フォルダが `resources/` フォルダに格納されています
- この `resources/` フォルダは、実行時に必ず実行ファイルと同じディレクトリに存在している必要があります


## 57.3 埋め込みリソースの読み込み
- 埋め込みリソースをプログラムで読み込むには、埋め込みリソースのファイルパスを `Resource()` で囲みます
- 例えば、`U"example/windmill.png"` を埋め込んだ場合、`Resource(U"example/windmill.png")` のように書き換えます（Windows, macOS, Linux 共通）

```cpp title="埋め込みリソース example/windmill.png を読み込む"
# include <Siv3D.hpp>

void Main()
{
	// 埋め込みリソースから読み込む
	const Texture texture{ Resource(U"example/windmill.png") };

	while (System::Update())
	{
		texture.draw();
	}
}
```

- ファイルが正しく埋め込まれたことを確認したい場合、ビルドされた実行ファイル単体を別のフォルダにコピーして実行し、埋め込みリソースの画像が表示されることを確かめます


## 57.4 埋め込みリソースの一覧取得
- 埋め込みリソースの一覧は `EnumResourceFiles()` を使って `Array<FilePath>` で得られます
- この一覧に含まれるファイルが、`Resource(...)` によって読み込み可能な埋め込みリソースです
- 次のコードを実行すると、Siv3D エンジンによって埋め込まれているリソースおよび自分で埋め込んだリソースの一覧がコンソールに表示されます
	- Windows 版ではファイル名がすべて大文字になります

```cpp
# include <Siv3D.hpp>

void Main()
{
	for (const auto& path : EnumResourceFiles())
	{
		Console << path;
	}

	while (System::Update())
	{

	}
}
```


## 57.5 埋め込みリソースの制約
- 埋め込みリソースのファイルは実行時に書き換えできないため、セーブファイル等の用途で使うことはできません
- 埋め込みリソースのファイルは、ユーザが特殊な操作をすることで抽出できます。重要なファイルの隠蔽の用途には向きません。必要に応じて、難読化、暗号化などの処理を施してください
