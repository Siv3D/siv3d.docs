# 60. アプリの公開
作成したアプリケーションを配布するための手順を学びます。

## 60.1 さまざまな環境の想定
- 開発者のパソコンと、ユーザのパソコンは異なる環境です。次のようなことを想定します：
	- ユーザのモニタの解像度が 1366×768 で、ウィンドウが画面に収まらない
	- 異なる解像度オプションを用意するか、フルスクリーンモードで実行する
- ゲームパッドによって `Gamepad` のボタンのインデックスが異なる / 特殊なキーの使用
	- キーコンフィグを用意する
- 120Hz や 144Hz など、高リフレッシュレートのモニタでゲームやモーションが早く進行してしまう
	- **チュートリアル 14** や **チュートリアル 19**, **チュートリアル 30** を参考にして、時間ベースの処理を採用する
	- 次のようにして、フレームレートが高い環境、低い環境を再現できます：
		- `Graphics::SetVSyncEnabled(false);` で VSync をオフにする
		- 毎フレーム、`System::Sleep(20ms)` によって待ち時間を発生させる


## 60.2 使い方の提示
- ゲームやアプリをダウンロードした利用者が操作に困らないよう、必要な情報を提供します
	- アプリ内で十分な説明やチュートリアルを表示する
		- `Texture` でマニュアルを表示する
		- `VideoTexture` で動画を再生する
	- アプリにマニュアルや README を同梱する
	- Web 上にマニュアルや解説動画を用意する
	- Web 上のマニュアルや解説動画に、アプリからアクセスできるようにする
- プログラムから Web ブラウザを起動して指定した URL を開くには、次のようにします（毎フレーム開かないように注意してください）：
	- 使い方を説明する Web ページや YouTube の解説動画を開くようにすると良いでしょう

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (SimpleGUI::Button(U"How to play", Vec2{ 40, 40 }))
		{
			// Web ページをブラウザで開く
			System::LaunchBrowser(U"https://siv3d.github.io/ja-jp/");
		}
	}
}
```

- とくに、マウスを使って操作するのか、キーボードを使って操作するのかは、初めてのユーザが戸惑うポイントです。アプリ内でも表示をするのが良いでしょう
- アプリ内のクリック可能な要素上では `Cursor::RequestStyle(CursorStyle::Hand);` を使って、カーソルを手の形に変更すると、ユーザにクリック可能であることを示すことができます


## 60.3 ウィンドウタイトル
- `Window::SetTitle(title)` でウィンドウのタイトルを変更できます

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::SetTitle(U"My Game");

	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{

	}
}
```


## 60.4 アプリのアイコン

### Windows の場合
- プロジェクトの `App` フォルダにある `icon.ico` を新しいアイコンファイルに置き換えてプログラムを再ビルドすると、実行ファイル (.exe) のアイコンを変更できます
- Windows OS は古いアイコンのサムネイル画像をパソコンにキャッシュしているため、新しいアイコンに更新されたあとも、エクスプローラー上での見た目が変わらない場合があります
	- その場合、実行ファイル名を変更したり、Windows 機能の「ディスク クリーンアップ」で「縮小表示」をクリーンアップすることで、キャッシュを更新できます

### macOS の場合
- プロジェクトフォルダにある `icon.icns` を置き換えてプログラムを再ビルドすると、実行ファイル (.app) のアイコンを変更できます


## 60.5 ライセンスの表示
- アプリを配布するときは、Siv3D および Siv3D が使用しているサードパーティ・ソフトウェアのライセンス表示が必要です
- 次のような手段で、ユーザがライセンス情報にアクセスできるようにしましょう
	- F1 キーを押したときに表示される HTML ファイルを配布アプリに同梱する
	- F1 キーを押したときに表示されるライセンス情報を README 内に記述する
	- F1 キーを押すことでライセンス情報を表示できることを README に明記する
	- `LicenseManager::ShowInBrowser()` を呼んで、アプリ内からライセンスをオープンできるようにする
	- `LicenseManager::EnumLicenses()` で取得できるライセンス情報をアプリ内で表示する
- HTML にゲーム自体のライセンスを追加する方法は **チュートリアル 5.4** を参照してください
- 次のサンプルコードでは、「Licenses」ボタンを押すとライセンス情報を Web ブラウザで表示します

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Licenses", Vec2{ 40, 40 }))
		{
			// Web ブラウザでライセンス情報を表示する
			LicenseManager::ShowInBrowser();
		}
	}
}
```


## 60.6 SNS シェアボタン
- ゲームの進捗や結果を SNS で手軽にシェアできるようにすると、広告効果が期待できます
- 次のようなコードで、指定したテキストを Twitter（X）にシェアするボタンを実装できます
- ゲームのスコア、ゲーム専用のハッシュタグ、ゲームを入手できる URL を含めましょう

```cpp
# include <Siv3D.hpp>

void PostRsultTweet(int32 score)
{
	const String text = U"I got {} points in the game!\n#Siv3D\nhttps://siv3d.github.io/ja-jp/"_fmt(score);

	Twitter::OpenTweetWindow(text);
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Share Score", Vec2{ 40, 40 }))
		{
			PostRsultTweet(123);
		}
	}
}
```


## 60.7 Release ビルド
- 配布する実行ファイルは「Release ビルド」でビルドでします
- プログラムを Release ビルドすることで、最大限の最適化が適用された、高速に動作する実行ファイルが生成されます
- デバッグ情報を含まないため、ファイルサイズも小さくなります
- Visual Studio では、次の画像のように、ビルド構成を Release に変更してビルドします：

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/release/6.png)


## 60.8 リソースの埋め込み
- プログラムで使う画像や音声、テキストファイルを実行ファイルに埋め込むことで、実行ファイル単体でのアプリの配布が可能になります
- **チュートリアル 57** を参照してください


## 60.9 同梱するファイル
- 配布ファイルに含める必要があるのは、次のファイルやフォルダです。
	- 実行ファイル (.exe または .app)
	- プログラムで使う、埋め込みをしていない画像や音声・テキストファイル
	- （Windows で `GlobalAudio::BusSetPitchShiftFilter()` を使用している場合のみ）`dll` フォルダ
	- （必要に応じて）アプリの説明等を記述したマニュアルや README.txt
	- （必要に応じて）ライセンス情報を記述したファイル（**60.5**）
- 最小の構成では、実行ファイル 1 つだけでの配布が可能です


## 60.10 同梱不要なファイル
- 次のフォルダやファイルは、ユーザに配布するファイルに含めません

| ファイルやフォルダ | 同梱しなくてよい理由 |
|--|--|
|`engine` フォルダ | アプリケーションビルド時に、自動で実行ファイルに埋め込まれる |
|`example` フォルダ | プログラムで使用していない場合、同梱する必要はない |
| アイコンファイル | アプリケーションビルド時に、自動で実行ファイルに埋め込まれる |
| `AS_DEBUG` フォルダ | スクリプト機能のデバッグログで、実行には無関係 |
| `Screenshot` フォルダ | ユーザがスクリーンショットを撮影したときに自動で作成される |
| `Resource.rc` | アプリの実行には不要 |
| `Info.plist` | アプリの実行には不要 |
| `Intermediate/` フォルダ | アプリの実行には無関係 |
| `*.exp`, `*.lib`, `*.pdb` などビルド時生成ファイル | アプリの実行には無関係 |


## 60.11 （上級者向け）ファイルサイズの最小化
- `engine` フォルダのファイルの内容のうち、いくつかのファイルは、プログラムで特定の機能を使用しない場合に限り、埋め込み不要です
- 対象のファイルを埋め込まないことで、配布ファイルのサイズを小さくできます
- Windows では　`Resource.rc` から該当ファイルをコメントアウトすることで、macOS / Linux では engine フォルダから該当ファイルを削除することで埋め込みをキャンセルできます
- 本来必要なファイルを誤って埋め込まなかった場合、ユーザがアプリを実行した際にテキストが表示されない、音声が再生されないなどのトラブルの原因になります。埋め込みのキャンセルは慎重に行う必要があります

| ファイル | 埋め込みをキャンセルできるケース |
|--|--|
| `engine/font/mplus/mplus-1p-thin.ttf.zstdcmp` | `Typeface::Thin` を使用していない場合 |
| `engine/font/mplus/mplus-1p-light.ttf.zstdcmp` | `Typeface::Light` を使用していない場合 |
| `engine/font/mplus/mplus-1p-regular.ttf.zstdcmp` | デフォルトのフォント (`Typeface::Regular`) を使用していない場合 |
| `engine/font/mplus/mplus-1p-medium.ttf.zstdcmp` | `Typeface::Medium` を使用していない場合 |
| `engine/font/mplus/mplus-1p-bold.ttf.zstdcmp` | `Typeface::Bold` を使用していない場合 |
| `engine/font/mplus/mplus-1p-heavy.ttf.zstdcmp` | `Typeface::Heavy` を使用していない場合 |
| `engine/font/mplus/mplus-1p-black.ttf.zstdcmp` | `Typeface::Black` を使用していない場合 |
| `engine/font/noto-emoji/noto-cjk/NotoSansCJK-Regular.ttc.zstdcmp` | SimpleGUI でハングル・中国語を表示しない場合、`Typeface::CJK_Regular_**`（JP 以外）を使用していない場合 |
| `engine/font/noto-emoji/noto-cjk/NotoSansJP-Regular.otf.zstdcmp` | SimpleGUI で ASCII 表以外の文字を表示しない場合、`Typeface::CJK_Regular_JP` を使用していない場合 |
| `engine/font/noto-emoji/NotoColorEmoji.ttf.zstdcmp` | `Typeface::ColorEmoji` や `Emoji` を使用していない場合 |
| `engine/font/noto-emoji/NotoEmoji-Regular.ttf.zstdcmp` | `Typeface::MonochromeEmoji` や `Print` 内での絵文字を使用していない場合 |
| `engine/font/fontawesome/fontawesome-brands.otf.zstdcmp` | `Typeface::Icon_Awesome_Brand` や `Icon` を使用していない場合 |
| `engine/font/fontawesome/fontawesome-solid.otf.zstdcmp` | `Typeface::Icon_Awesome_Solid` や `Icon` を使用していない場合 |
| `engine/font/materialdesignicons/materialdesignicons-webfont.ttf.zstdcmp` | SimpleGUI 内のテキストでアイコン表示や、`Typeface::Icon_MaterialDesign`, `Icon` を使用していない場合 |
| `engine/soundfont/GMGSx.sf2.zstdcmp` | `GMInstrument` や `MIDI` ファイルの読み込みを使用していない場合 |

- Siv3D アプリに同梱された engine ファイルの多くは、プログラムの実行時にローカルの PC にキャッシュされます
- 一見、必要なファイルを埋め込まなくても正常に動作するように見えることがあります。それは過去に起動した Siv3D アプリが、必要なファイルを PC にキャッシュしているためです
- Siv3D アプリのキャッシュフォルダは `ユーザ名/AppData/Local/Siv3D/` (隠しフォルダ) に作成されます。初使用のユーザ環境を再現する目的で、この Siv3D キャッシュフォルダの中身を安全に削除することができます


## 60.12 プレスリリースの送付
- ゲームやアプリのリリースについて、Web メディアに取り上げてもらいたい場合は、各種メディアにプレスリリース用のプレスキット（あるいはプレスキットのダウンロード URL）を送付します
- とくに数十万円以上の売り上げを見込む商業作品の場合、プレスリリースの送付は必須です

### 60.12.1 プレスキットの内容（一例）
1. プレスリリース本文
	- PDF および、編集者の都合を考え、プレーンテキストの 2 パターン
2. スクリーンショットやアートワーク
3. プロモーション用の動画（または YouTube の URL）
4. 体験版（あれば）
5. 配布ストアページの URL（あれば）
6. 記事の配信希望日（〇月〇日以降）

### 60.12.2 オンラインストレージ利用時の注意
- オンラインスストレージでファイルを共有する場合は、権限設定の誤り（とくに Google Drive）、共有期間、ファイルの削除などに注意します
- Web ブラウザのプライベートブラウズモードで共有 URL を開くことで、他人がファイルにアクセスできるかを確認します

### 60.12.3 プレスリリースのタイミング
- Web メディアの立場になって考えると、「トレーラー公開」「リリース日決定」「本日リリース」「発売記念セール」といった記事がアクセスを集めやすいです
- Web メディアがそうした記事を適切なタイミングで掲載できるよう、10 営業日程度の余裕を持ってプレスリリースを送付することが望ましいです
- プレスリリースは必ず掲載してもらえるわけではありませんが、掲載による宣伝効果は大きいため、形式やリリース日までの時間的余裕など、対策をしっかり行うべきです
- プレスリリース本文を転載しただけの記事になる場合もありますし、注目が集まりそうな作品の場合は、ライターが独自に編集した記事を書いてくれることもあります

### 60.12.4 プレスリリース本文の例
- 自身を主語にし「自身が ～ をした / をすることを発表」という形式にすることが一般的です
	- 団体であれば団体名を、個人の場合は「インディーゲーム開発者の 〇〇」のようにします
	- 実名の必要はなく、インターネット上での活動名があれば、それを使っても問題ありません
- そのままコピーして記事にできるような体裁で記述します

```
〇〇（開発者/団体名）は、〇 年 〇 月 〇 日、〇〇（ゲームジャンル）「〇〇」の配信を Steam で開始しました。価格は 〇 円（税込）です。

◆ 概要
タイトル: 
ジャンル: 
価格: 
配信プラットフォーム: 
対応言語: 

◆ ゲームの特徴
1. [主要な特徴1]
2. [主要な特徴2]
3. [主要な特徴3]

◆ 今後の展開
[アップデート予定や DLC 計画があれば]

◆ 作品の受賞・出展歴
・～～～～～

◆ URL
・Steam 配信ページ: 
・作品紹介ページ: 
・（あれば）YouTube: 
・（あれば）X（旧 Twitter）:
```

- Siv3D 作品でプレスリリースがメディアに掲載された例：
	- [4Gamer.net: フィクションの少女たちと会話をして，友だちになる。PC用ソフト「For the GHOSTs」，Steamで配信開始 :material-open-in-new:](https://www.4gamer.net/games/765/G076537/20240109010/){:target="_blank"}
	- [gamebiz: 個人ゲーム開発者sashi、25年発売予定の新作2D探索型アクション『Monad Tachyon』のSteamストアページを公開 :material-open-in-new:](https://gamebiz.jp/news/396584){:target="_blank"}
- プレスリリース送付に関する経験が書かれている記事：
	- [自作ゲームのプレスリリースを送って掲載された話 :material-open-in-new:](https://note.com/m_hanakura/n/n07bb88589eba){:target="_blank"}
	- [穴埋めで作る、インディゲーム開発者のための簡単なプレスリリースの作り方 :material-open-in-new:](https://note.com/gamecast/n/nae90d756bf04){:target="_blank"}
	- 上記以外にも、Web 検索をするとさまざまな記事が見つかります
- 生成 AI にプレスリリースを推敲してもらうことも有効です
- プレスリリース本文とは別に、メディアへのあいさつ文、問い合わせ先の記載なども忘れないようにしましょう


## 60.13 アプリの公開準備
- 配布するプラットフォーム（ストアサイト）のルールにしたがって必要なファイルを準備します
- オンラインストレージで公開する場合は、ZIP アーカイブでまとめてダウンロードしやすい形式にします
- 大々的に公開する前に、協力者のパソコンで動作確認などをしておくと、トラブルを減らすことができます


## 60.14 アプリの宣伝
- アプリをたくさんの人に知ってもらうためには、開発段階から宣伝を始める必要があります
- SNS 上で、**スクリーンショット** や **映像**、開発のエピソードなどを一貫した **ハッシュタグ** を使って発信することが効果的です
- Twitter（X）で #Siv3D ハッシュタグを使ったツイートをすると、Siv3D の開発者や公式アカウントがリツイートする場合があります
- プレイ動画、プロモーション動画を YouTube にアップロードすることも重要です


## 60.15 コミュニティ運営
- ユーザーフィードバックの収集システムを用意することで、アプリの改善に役立てることができます
- ユーザのための Discord サーバーや Twitter（X）アカウントを用意し、アプリ内や関連 Web サイトから誘導することで、ユーザとのコミュニケーションを図ることができます
