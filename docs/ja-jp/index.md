# 創造のための C++ フレームワーク Siv3D
<div class="noshadow-76"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/logo/logo.png"></div>

Siv3D (シブスリーディー) は、ゲームやアプリを **楽しく簡単な C++ コード** で開発できるフレームワークです。MIT ライセンスで配布され、Windows / macOS / Linux / Web で動作します。

#### Siv3D のダウンロード | v0.6.5

[Windows :material-microsoft-windows:](download/windows){ .md-button .md-button--primary }[macOS :material-apple:](download/macos){ .md-button .md-button--primary }[Ubuntu :material-ubuntu:](download/ubuntu){ .md-button .md-button--primary }

#### Web 向け Siv3D のダウンロード (試験的) | v0.6.5

[for Web (Windows + Visual Studio) :material-microsoft-visual-studio:](download/web-vs){ .md-button .md-button--primary }[for Web (Visual Studio Code) :material-microsoft-visual-studio-code:](download/web-vscode){ .md-button .md-button--primary }

## ゲームやアプリ開発を効率化する、圧倒的な機能

- 2D / 3D グラフィックス（図形、画像、テキスト、アイコン、動画、3Dモデルなど）
- オーディオ（BGM, 効果音、テキスト読み上げ、オーディオフィルタなど）
- 入力デバイス（マウス、キーボード、Webカメラ、マイク、ゲームパッドなど）
- ウィンドウ、ファイルシステム、ネットワーク
- 画像処理、音声処理、物理演算、経路探索、幾何などの計算

豊富なクラスや関数を組み合わせて、2D / 3D ゲーム、メディアアート、ビジュアライザ、シミュレータなどのアプリを、短いコードで効率的に開発できます。

[Siv3D の豊富な機能を詳しく見る](./features/){ .md-button }


## C++ コードだけで、完成までの最短距離
標準的な C++ の文法と、綿密に設計された Siv3D の便利な型や関数を組み合わせてアプリをプログラムします。次のような簡潔なコードで世界が動き始めます。

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 }); // 背景色を設定
	const Texture food{ U"🍿"_emoji }; // 絵文字からテクスチャを作成
	const Texture chick{ U"🐥"_emoji };	// 絵文字からテクスチャを作成

	while (System::Update()) // メインループ
	{
		Circle{ Scene::Center(), 100 }.draw(); // 画面の中心に円を描く
		food.drawAt(Scene::Center()); // 画面の中心にテクスチャを描く
		chick.drawAt(Cursor::Pos()); // マウスカーソルの位置にテクスチャを描く
	}
}
```

<div class="full"><img src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/demo/chick.gif"></div>


## Siv3D を使う 7 つの理由

###  1. ⚡ 非常に短いコード
Siv3D のコードは最短 2 行です。描画や入出力を実現するための便利な関数とクラスが揃っているため、アプリケーションのほとんどは 1 つの .cpp ファイルだけで完成します。あなたのアイデアを、[GitHub :material-open-in-new:](https://github.com/) や [GitHub Gist :material-open-in-new:](https://gist.github.com/) などのコード共有サイトを使って手軽に保存・シェアして、世界中の Siv3D ユーザと技術を交換し、学び合いましょう。

### 2. 🛸 最新の C++ が身につく
Siv3D のサンプルとライブラリ API は、最新の C++20 スタイルで書かれているため、Siv3D を使っているだけで、モダンな C++ の書き方やテクニックが自然と身に付きます。Siv3D の作者は、日本最大のゲーム開発カンファレンス CEDEC で [最新 C++ の活用に関する講演 :material-open-in-new:](https://speakerdeck.com/cpp/cedec2020) をしたり、[C++ の情報ポータル :material-open-in-new:](https://cppmap.github.io/) を作成したりするなど、最先端の C++ の普及活動に努めています。

### 3. 🏬 小さな学習、大きな力
Siv3D は 2,200 ファイルのソースコードと 90 のサードパーティ・ソフトウェアによって構成されている大規模なエンジンですが、利用者はそのパワフルな機能を、使いやすく一貫した Siv3D の API を覚えるだけで自在に扱うことができます。勉強のコストを減らし、自分の作りたいアプリの開発に専念できます。

### 4. ⛰️ オープンソース
Siv3D は MIT ライセンスのもと [GitHub 上で開発 :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D) されているため、いつでも内部コードを調べたり、改造したりできます。サードパーティ・ライブラリを含め、商用利用を妨げる条件はありません。開発したゲームやアプリケーションの収益は 100% 開発者が獲得できます。

### 5. 🛩️ 軽量で迅速にスタート
Siv3D プログラミングを始めるための OpenSiv3D SDK インストーラはわずか 120 MB です（Windows 版）。インストールは数クリックで完了し、Visual Studio を起動すればメニューに Siv3D プロジェクトの項目が追加されているので、すぐにプログラミングを始められます。

### 6. 💗 親切なコミュニティ
Siv3D で困ったことがあれば、[Siv3D のコミュニティ](community/community/) が役に立ちます。また、全国の学校や地域コミュニティへの [無料出張勉強会](community/study-meeting/) も行っています。オープンソースソフトウェア開発に興味のある学生には、Siv3D を練習場にしたサポートプログラムを毎年提供しています。

### 7. 🌐 Web ブラウザで動く
現在試験的に提供している Web 版（[OpenSiv3D for Web :material-open-in-new:](https://siv3d.kamenokosoft.com/ja/index)）を使うと、Siv3D で作った C++ アプリをブラウザ上で実行できる Web アプリに変換できます。スマホやタブレットでも動作するため、これまでよりもたくさんの人にあなたの作品を届けることができます。


## Siv3D のスポンサー
Siv3D のビジョンに共感し、開発や改善を応援してくださる方は、Siv3D へのスポンサー（個人・法人）を検討してください。いくつかの特典も用意しています。

!!! summary "Siv3D のスポンサー"

	#### Gold Sponsor 
	- [TOMOAKI12345](https://github.com/TOMOAKI12345)
	- [CubeSoft, Inc.](https://www.cube-soft.jp/)

	#### Silver Sponsor
	- [sknjpn](https://twitter.com/sknjpn)

	#### Bronze Sponsor
	アゲハマ, 😀, minachun, Fuyutsubaki, 😊, 🐝, 🐠, 野菜ジュース, MawkishWaffle, jacking75, Chris Ohk, IZUNA, qppon, k-sunako, ysaito, おおやま, tumf, 🍵, lamuda

	<small>（*匿名の方は絵文字）</small>

[GitHub Sponsors で Siv3D のスポンサーになる :material-github:](https://github.com/sponsors/Reputeless){ .md-button }
