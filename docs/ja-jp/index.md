description: ゲームやクリエイティブコーディングのための C++20 フレームワーク Siv3D

# Siv3D: クリエイターのための C++ ライブラリ

**Siv3D** (シブスリーディー) は、2D / 3D ゲーム、メディアアート、ビジュアライザ、シミュレータなど、**可視化**や**インタラクション**に関わる C++ プログラムを、**非常に短い、楽しく簡単なコード**で記述できる**モダンなフレームワーク**です。MIT ライセンスで配布され、2021 年 8 月時点における直近 1 年間の SDK ダウンロード数は約 1 万回です。動作プラットフォームは Windows / macOS / Linux / Web です。

## Siv3D を始める

SDK のインストール方法や最新のチュートリアル、サンプルを解説しています。

- 日本語ドキュメント: https://zenn.dev/reputeless/books/siv3d-documentation
- English documentation (WIP): https://zenn.dev/reputeless/books/siv3d-documentation-en


## 機能の概要
Siv3D を導入することで、次のような操作を組み合わせたアプリケーションを非常に短いコードで記述できます。

- 図形や画像、テキスト、動画、3Dモデルなど、グラフィックスの描画 (Direct3D 11 / OpenGL 4.1 / WebGL 2.0)
- マウスやキーボード、Webカメラ、マイク、ゲームパッドなど、ヒューマンインタフェースデバイス (HID) の使用
- ウィンドウ処理、ファイルシステム、ネットワーク
- 画像処理や音声処理
- 物理演算や経路探索、幾何などの計算
- データ構造やアルゴリズム

Siv3D は標準的な C++ で書かれていますが、ユーザはほとんどのプログラムを、Siv3D が提供する便利な型や関数を使って記述します。そのため、描画やインタラクションのための DSL (domain-specific language) としての性格が強いです。例えると Java にとっての [Processing](https://processing.org/) です。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });
	const Texture food{ U"🍿"_emoji };
	const Texture chick{ U"🐥"_emoji };

	while (System::Update())
	{
		Circle{ Scene::Center(), 100 }.draw();
		food.drawAt(Scene::Center());
		chick.drawAt(Cursor::Pos());
	}
}
```

![](https://github.com/Reputeless/Zenn.Public/blob/main/images/doc_v6/demo/chick.gif?raw=true)

[Unity](https://unity.com/ja) のようなゲームエンジンとの一番の違いは、Siv3D はビジュアルツールを持たず、すべてコードで完結するということです（Siv3D で独自のビジュアルツールを組むことはできます）。

## Siv3D のここが最高

### ⚡ 非常に短いコード
Siv3D のコードは最短で 2 行です。様々なインタラクションを実現する便利な機能が揃っているため、アプリケーションのほとんどは 1 つの .cpp ファイルだけで完成します。思いついたアイデアや成果物のソースコードを、[GitHub Gist](https://gist.github.com/) などのコード共有サイトを使って手軽に保存・シェアできるため、世界中の Siv3D ユーザと技術を学び合うことができます。

### 🛸 最新の C++ を学べる
Siv3D のサンプルコードとライブラリ API は、すべて最新の C++20 を活用して書かれています。Siv3D を使っているだけで、モダンな C++ の書き方やテクニックを自然と身につけることができます。Siv3D の作者は、日本最大のゲーム開発カンファレンス CEDEC で[最新 C++ の活用に関する講演](https://speakerdeck.com/cpp/cedec2020)をしたり、[C++ の情報ポータル](https://cppmap.github.io/)を作成したりするなど、最先端の C++ の普及に努めています。

### 🏬 豊富な機能を Siv3D API に統合
画像処理、音楽再生、物理演算、テキストファイル読み込み、シリアル通信・・・。たくさんの処理のために、ライブラリを探し回り、ドキュメントを読んで使い方を調べ、API の要求に従ってデータ形式を変換するコードをひたすら書く必要はもうありません。Siv3D は 2,200 ファイルのソースコードと 90 のサードパーティ・ソフトウェアが実現する、可視化やインタラクションの様々な機能を、一貫した API で提供します。Siv3D のルールを覚えるだけで、ありとあらゆる機能が思いのままです。

### ⛰️ オープンソース
Siv3D は MIT ライセンスのもと [GitHub 上でホスティング](https://github.com/Siv3D/OpenSiv3D)されています。内部のコードが気になったらいつでも調べることができ、改造することもできます。サードパーティ・ライブラリを含め商用利用を妨げる条件はありません。Siv3D で開発した Windows, macOS, Linux, Web 向けのゲームやアプリケーションを販売して得た収益は 100% 開発者が獲得できます。Siv3D のビジョンに共感し、より優れた開発体験を心待ちにしている方は [Siv3D の個人スポンサー](https://github.com/sponsors/Reputeless)として Siv3D プロジェクトを応援してください。

### 🐦 軽量・迅速
Windows 版の OpenSiv3D SDK のインストーラのサイズは 120 MB 未満です。秒でダウンロードが終わり、インストールはわずかなクリックで自動的に終わります。Visual Studio を起動すると、メニューには Siv3D プロジェクトを作成するアイテムが追加済みです。あっという間に、楽しい C++ プログラミングの世界への入り口があなたのパソコンにセットアップされます。アンインストールしたいときは、通常のアプリケーションと同様に OS の設定からワンクリックです。

### 💗 親切なユーザコミュニティ
Siv3D を使って困ったことがあったら、[Siv3D ユーザコミュニティ Slack](https://zenn.dev/reputeless/books/siv3d-documentation/viewer/community) で質問しましょう。匿名で質問したい場合は BBS も利用できます。毎月オンラインで開催される Siv3D 実装会では、Siv3D の熱心なユーザ達や Siv3D の作者と、Discord 上で雑談や技術的な相談をすることができます。Twitter では定期的に #Siv3D, #OpenSiv3D ハッシュタグを巡回しています。ユーザコミュニティが、作品の宣伝やシェアに協力してくれるでしょう。OSS (オープンソースソフトウェア) 開発に貢献したい学生に対して、Siv3D を練習場にしてサポートするプログラムも毎年実施しています。

### 🌐 Web ブラウザ上で動く（試験的）
現在試験的に提供される Web 版 ([OpenSiv3D for Web](https://siv3d.kamenokosoft.com/ja/index)) を使うと、Siv3D で作った C++ アプリケーションを、WebGL2 をサポートするモダンな Web ブラウザ上で実行可能なプログラムに変換できます。スマホやタブレットから、多くの人があたなの作品を体験できます。


## 💗 Siv3D の [スポンサー](https://github.com/sponsors/Reputeless)

|Sponsor tier| |
|--|--|
|🌳 Gold Sponsor |<ul><li>[TOMOAKI12345](https://github.com/TOMOAKI12345)</li><li>[デジタルライフ](https://lifedigitalwiki.org/ja/)</li></ul>|
|🌴 Silver Sponsor |[sknjpn](https://twitter.com/sknjpn)|
|🌷 Bronze Sponsor |アゲハマ, anonymous 😀, minachun, Fuyutsubaki, anonymous 😊, anonymous 🐝, anonymous 🐠, 野菜ジュース, MawkishWaffle, jacking75, Chris Ohk, IZUNA, qppon, k-sunako, ysaito, totono, おおやま|