# 創造のための C++ フレームワーク Siv3D
<div class="logo"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/logo/logo.png" width="1450" height="450"></div>

**Siv3D（シブスリーディー）**は、音や画像、AI を使ったゲームやアプリを、**モダンな C++ コードで楽しく簡単にプログラミング**できるオープンソースのフレームワークです。豊富なチュートリアルが用意され、オンラインのユーザコミュニティで気軽に質問や相談ができます。動作環境は Windows / macOS / Linux / Web です。

#### Siv3D のダウンロード | v0.6.15

[Windows :material-microsoft-windows:](download/windows.md){ .md-button .md-button--primary }[macOS (Intel / Rosetta) :material-apple:](download/macos.md){ .md-button .md-button--primary }[Ubuntu :material-ubuntu:](download/ubuntu.md){ .md-button .md-button--primary }

<small>Apple Silicon (M1 - M3) は、現在開発中の Siv3D v0.8.0 からネイティブサポートされます。</small>

#### Web 向け Siv3D のダウンロード（非公式）

[for Web (Windows + Visual Studio) :material-microsoft-visual-studio:](download/web.md){ .md-button .md-button--primary }[for Web (Visual Studio Code) :material-microsoft-visual-studio-code:](download/web.md){ .md-button .md-button--primary }


## バンダイナムコスタジオ杯 Siv3D ゲームジャム【結果発表】

[結果発表ページ](event/gamejam2023.md){ .md-button }


## ゲームやアプリ開発を効率化する、圧倒的な機能
2D / 3D ゲーム、メディアアート、ビジュアライザ、シミュレータを効率的に開発するための、**便利なクラスや関数**が用意されています。

- 2D / 3D グラフィックス（図形、画像、テキスト、アイコン、動画、3Dモデルなど）
- オーディオ（BGM, 効果音、テキスト読み上げ、オーディオフィルタなど）
- 入力デバイス（マウス、キーボード、Webカメラ、マイク、ゲームパッドなど）
- ウィンドウ、ファイルシステム、ネットワーク
- 画像処理、音声処理、物理演算、経路探索、幾何などの計算
- AI (OpenAI API へのアクセス)

[Siv3D の機能を詳しく見る](features.md){ .md-button }


## 完成までの最短距離
Siv3D では、**C++ の文法**を使って、わずか数行のコードで世界が動き始めます。たくさんの夢やアイデアを C++ で実現しましょう。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 }); // 背景色を設定する
	const Texture food{ U"🍿"_emoji }; // 絵文字からテクスチャを作成する
	const Texture chick{ U"🐥"_emoji };	// 絵文字からテクスチャを作成する

	while (System::Update()) // メインループ
	{
		Circle{ Scene::Center(), 100 }.draw(); // 画面の中心に円を描く
		food.drawAt(Scene::Center()); // 画面の中心にテクスチャを描く
		chick.drawAt(Cursor::Pos()); // マウスカーソルの位置にテクスチャを描く
	}
}
```

??? summary "実行結果"
    ![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/demo/chick.gif)


## Siv3D を使う 7 つの理由

??? success "1. 非常に短いコードで書ける"
	Siv3D には描画や入出力を実現するための便利な関数とクラスが豊富に揃っていて、1 つの .cpp ファイルだけで簡単なアプリケーションが完成します。作品のソースコードを [GitHub :material-open-in-new:](https://github.com/){:target="_blank"} や [GitHub Gist :material-open-in-new:](https://gist.github.com/){:target="_blank"} などのコード共有サイトでシェアして、世界中の Siv3D ユーザと技術を交換し、学び合いましょう。

??? success "2. 最新の C++ の書き方が身につく"
	Siv3D の API とサンプルは、最新の C++ 規格「C++20」で書かれています。Siv3D を使っているだけで、現代的な C++ の書き方が身に付きます。Siv3D の作者は、日本最大のゲーム開発カンファレンス CEDEC で [最新 C++ の活用に関する講演 :material-open-in-new:](https://speakerdeck.com/cpp/cedec2020){:target="_blank"} をしたり、[C++ の情報ポータル :material-open-in-new:](https://cppmap.github.io/){:target="_blank"} を作成したりするなど、最先端の C++ の普及活動に努めています。作品開発と、C++ の学習を同時に進めましょう。

??? success "3. 小さな学習が大きな力につながる"
	Siv3D の裏側は 2,200 ファイルのソースコードと 90 のサードパーティ・ソフトウェアによって構成される大規模なエンジンです。利用者はそのパワフルな機能を、使いやすく一貫した Siv3D の API を覚えるだけで自在に扱うことができます。学習に必要なコストを最小限に抑え、作品開発に集中しましょう。

??? success "4. オープンソースで公開されている"
	Siv3D は MIT ライセンスで [オープンソース開発 :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D){:target="_blank"} されているため、いつでも内部コードを調べたり、改造したりできます。サードパーティ・ライブラリを含め、商用利用を妨げる条件はありません。開発したゲームやアプリケーションの収益は 100% 開発者が獲得できます。

??? success "5. 軽量で迅速にスタートできる"
	Siv3D プログラミングを始めるための OpenSiv3D SDK インストーラはわずか 120 MB です（Windows 版）。インストールは数クリックで完了し、Visual Studio を起動すればメニューに Siv3D プロジェクトの項目が追加されていて、すぐにプログラミングを始められます。

??? success "6. 親切なコミュニティに参加できる"
	Siv3D で困ったことがあれば、Discord などの [Siv3D オンラインコミュニティ](community/community.md)が役に立ちます。また、学校や地域コミュニティへの[無料出張勉強会](community/community.md)も行っています。オープンソースソフトウェア開発に興味のある学生には、Siv3D を練習場にしたサポートプログラムを毎年提供しています。仲間とともにより良い作品を作りましょう。

??? success "7. Web ブラウザで動く"
	非公式で提供されている Web 版（[OpenSiv3D for Web :material-open-in-new:](https://siv3d.kamenokosoft.com/docs/ja/){:target="_blank"}）を使うと、Siv3D で作った C++ アプリをブラウザ上で動く Web アプリに移植できます。スマホやタブレットをターゲットにして、これまでよりもたくさんの人に作品を届けることができます。


## 法人協賛
<div class="sponsor"><a href="https://www.bandainamcostudios.com/" target="_blank"><img src="https://siv3d.jp/sponsors/バンダイナムコスタジオ.png" alt="バンダイナムコスタジオ"></a></div>

## 個人スポンサー

#### Gold Sponsor 
- [TOMOAKI12345](https://github.com/TOMOAKI12345)
- [CubeSoft, Inc.](https://www.cube-soft.jp/)

#### Silver Sponsor
- [sknjpn](https://twitter.com/sknjpn)
- 野菜ジュース

#### Bronze Sponsor
アゲハマ, Fuyutsubaki, 😊, 🐝, jacking75, Chris Ohk, qppon, ysaito, おおやま, ShivAlley, lamuda, 🌻, fal_rnd, As Project, IZUNA, 柏崎でぃすこ


[協賛企業の募集について](sponsorship/corporate-sponsor.md){ .md-button } [Siv3D の個人スポンサーになる :material-github:](https://github.com/sponsors/Reputeless){:target="_blank" .md-button} 
