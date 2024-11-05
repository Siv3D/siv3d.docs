# 創造のための C++ フレームワーク Siv3D
<div class="logo"><img src="https://siv3d.jp/logo.png" width="1450" height="450"></div>

**Siv3D（シブスリーディー）**は、音や画像、AI を使ったゲームやアプリを、モダンな C++ コードで楽しく簡単に開発できる無料の国産フレームワークです。豊富なサンプルコードとチュートリアルが用意され、オンラインのユーザコミュニティで気軽に質問や相談ができます。

<div class="grid cards" markdown>

-   :material-laptop:{ .lg .middle }　__動作環境__

    ---

    Windows / macOS / Ubuntu / Web

-   :material-share-outline:{ .lg .middle }　__オープンソース__

    ---

    MIT ライセンス

-  :material-application-parentheses-outline:{ .lg .middle }　__開発言語__

    ---

    C++20 / HLSL / GLSL

-   :material-update:{ .lg .middle }　__バージョン__

    ---

    0.6.15（2024-07-04） / 0.8.0（開発中）

</div>

## Siv3D をダウンロードする

[Windows :material-microsoft-windows:](download/windows.md){ .md-button .md-button--primary }[macOS :material-apple:](download/macos.md){ .md-button .md-button--primary }[Ubuntu :material-ubuntu:](download/ubuntu.md){ .md-button .md-button--primary }[Web * :material-web:](download/web.md){ .md-button .md-button--primary }

<small>* Web 版は有志による非公式の拡張機能で、Siv3D 中級者以上向けです。</small>

## 直近のイベント

| 日付 | イベント | 対象者 |
| --- | --- | --- |
| 2025-01-25 | Siv3D 勉強会 in 東京電機大学 2025 | 東京電機大学の学生・教職員 |
| 2024-12-15 | Siv3D 勉強会 in 京都 2024 | だれでも |
| 2024-12-14 | [Siv3D 実装会 in 京都 2024 :material-open-in-new:](https://connpass.com/event/332833/) | だれでも |
| 2024-12-01～25 | [Siv3D Advent Calendar 2024 :material-open-in-new:](https://qiita.com/advent-calendar/2024/siv3d) | だれでも |
| 2024-12-01 | Siv3D 実装会 in 熊本 2024 | だれでも |


## 基本情報

Siv3D には、2D/3D のグラフィックス描画、音声再生、入力処理、物理演算、画像処理、AI、ネットワーク通信など、実用的なソフトウェアの開発に必要な機能が豊富に揃っています。

すべての基本機能が C++ コードだけで完結するのが特徴で、Unity や Unreal Engine などの商用ゲームエンジンと異なり、独自のエディタやスクリプトを用いず、純粋な C++ コーディングだけでゲームやアプリケーションを完成させられます。

C++ のスキルを活かしたいプログラマーや、C++ での開発を学びたい人にとって、Siv3D は有力な選択肢です。

=== "ソースコードの例"

	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		// 背景色を水色に設定する
		Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

		// 絵文字からテクスチャを作成する
		const Texture texture{ U"🐥"_emoji };

		// テクスチャを描く位置
		Vec2 pos{ 400, 300 };

		// メインループ
		while (System::Update())
		{
			// もし左クリックされたら
			if (MouseL.down())
			{
				// 描く位置マウスカーソルの位置に変更する
				pos = Cursor::Pos();
			}

			// テクスチャを描く
			texture.drawAt(pos);
		}
	}
	```

=== "実行結果"

    ![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/demo/chick.gif)

## Siv3D の用途

??? success "1. 最新の C++ の学習（クリックで詳細）"
	Siv3D の作者は、日本最大のゲーム開発者カンファレンス CEDEC で最新 C++ の活用に関する講演を行うなど、最先端の C++ の普及活動に取り組んでいます。Siv3D の API とサンプルも最新の C++ 規格を活用して書かれているため、Siv3D を使うことで現代的な C++ の書き方が身に付きます。作品開発と C++ の学習を同時に進めましょう。

	<script async class="docswell-embed" src="https://www.docswell.com/assets/libs/docswell-embed/docswell-embed.min.js" data-src="https://www.docswell.com/slide/5XEY92/embed" data-aspect="0.5625"></script>

??? success "2. フリーゲーム・商用ゲームの開発（クリックで詳細）"

	Siv3D を使って本格的なゲームを開発し、Steam などのゲーム配信プラットフォームでリリースすることができます。Siv3D で作られたゲームの例をいくつか紹介します。

	<iframe src="https://store.steampowered.com/widget/2487390/" frameborder="0" width="646" height="190"></iframe>
	<iframe src="https://store.steampowered.com/widget/2770160/" frameborder="0" width="646" height="190"></iframe>
	<iframe src="https://store.steampowered.com/widget/3147480/" frameborder="0" width="646" height="190"></iframe>
	<iframe src="https://store.steampowered.com/widget/2492380/" frameborder="0" width="646" height="190"></iframe>
	<iframe src="https://store.steampowered.com/widget/2943760/" frameborder="0" width="646" height="190"></iframe>


??? success "3. プログラミングコンテストにおける情報可視化・GUI（クリックで詳細）"


??? success "4. 研究のためのソフトウェア開発（クリックで詳細）"


??? success "5. オープンソース活動への参加（クリックで詳細）"
	プログラミングの経験や実装力を生かして、Siv3D 本体の開発やバグの報告、サンプルコードの作成、記事執筆など、Siv3D での開発体験を向上するためのオープンソース活動に参加できます。これまで中学生を含む 60 人以上が、Siv3D 本体にソースコードをコミットしています。

??? success "6. Web ブラウザ向けの作品配信（クリックで詳細）"
	有志ユーザによって提供されている Web 版（[OpenSiv3D for Web :material-open-in-new:](download/web.md){:target="_blank"}）を使うと、Siv3D で作った C++ アプリをブラウザ上で動く Web アプリに移植できます。スマホやタブレットをターゲットにして、たくさんの人に作品を届けることができます。

	#### Siv3D 製の Web アプリの例
	<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">ゲーム「選挙で勝とう 2024」を作成しました！<br><br>舞台は日本の衆議院選挙。ある党の党首のつもりになって、12 日間の選挙を戦う全く新しいゲームです。ぜひ遊んでみてください！！！<br><br>URL: <a href="https://t.co/tnIySuWA4A">https://t.co/tnIySuWA4A</a> <a href="https://t.co/ZcNHVy61ex">pic.twitter.com/ZcNHVy61ex</a></p>&mdash; E869120 (@e869120) <a href="https://twitter.com/e869120/status/1849277131513053548?ref_src=twsrc%5Etfw">October 24, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

	<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">大学の講義で教えるために、浮動小数点数 (float 型) の仕組みを学べるアプリを Siv3D で作りました。<a href="https://t.co/BNSwkqAHzt">https://t.co/BNSwkqAHzt</a><br>(PC でのアクセス推奨) <a href="https://twitter.com/hashtag/Siv3D?src=hash&amp;ref_src=twsrc%5Etfw">#Siv3D</a> <a href="https://twitter.com/hashtag/OpenSiv3D?src=hash&amp;ref_src=twsrc%5Etfw">#OpenSiv3D</a> <a href="https://t.co/OAmZfo5R9D">pic.twitter.com/OAmZfo5R9D</a></p>&mdash; Ryo Suzuki (@Reputeless) <a href="https://twitter.com/Reputeless/status/1542160459658416129?ref_src=twsrc%5Etfw">June 29, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


## Siv3D を採用する理由

<div class="grid cards" markdown>

-   __1. オープンソースで安心__

    ---

    Siv3D は MIT ライセンスで [オープンソース開発 :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D){:target="_blank"} されているため、誰でも内部のコードを調べたり、改造したりできます。サードパーティ・ライブラリを含め、商用利用を妨げる条件はありません。開発したゲームやアプリケーションの収益は 100% 開発者が獲得できます。

-   __2. 非常に短いコード__

    ---

    Siv3D には描画や入出力を実現するための便利な関数とクラスが豊富に揃っています。1 つの .cpp ファイルだけで簡単なアプリケーションが完成します。作品のソースコードを GitHub や GitHub Gist で瞬時にシェアして、世界中の Siv3D ユーザと技術を交換し、学び合いましょう。

-  __3. 小さな学習、大きな力__

    ---

    Siv3D は 2,200 ファイルのソースコードと 90 のサードパーティ・ソフトウェアによって構成される大規模なエンジンです。そのパワフルな機能を、使いやすく一貫した Siv3D の API を覚えるだけで自在に扱うことができます。学習に必要なコストを最小限に抑え、作品開発に集中できます。

-   __4. 軽量で迅速なスタート__

    ---

    Siv3D プログラミングを始めるための Windows 用 OpenSiv3D SDK インストーラはわずか 120 MB です。インストールは数クリックで完了し、Visual Studio を起動するとメニューに表示される Siv3D プロジェクトの項目から、すぐにプログラミングを始められます。

-  __5. 親切なコミュニティ__

    ---

    Siv3D で困ったことがあれば、Discord などの [オンラインコミュニティ](community/community.md) が助けになります。学校や地域コミュニティへの [無料訪問勉強会](community/community.md) も行っています。オープンソースソフトウェア開発に興味のある学生には、Siv3D を練習場にしたサポートプログラムを毎年提供しています。

-   __6. Web ブラウザで動く__

    ---

    Siv3D で作った C++ プログラムをほぼそのままで、ブラウザ上で動く Web アプリに移植できます。スマホやタブレットをターゲットにすることで、よりたくさんの人に作品を届けることができます。
</div>

## 法人協賛
<div class="sponsor"><a href="https://www.bandainamcostudios.com/" target="_blank"><img src="https://siv3d.jp/sponsors/バンダイナムコスタジオ.png" alt="バンダイナムコスタジオ"></a></div>

### 過去のイベント
[バンダイナムコスタジオ杯 Siv3D ゲームジャム | 結果発表ページ](event/gamejam2023.md){ .md-button }

### 協賛企業の募集について
[協賛企業の募集について](sponsorship/corporate-sponsor.md){ .md-button } 

## 個人スポンサー

#### Gold Sponsor 
- [TOMOAKI12345](https://github.com/TOMOAKI12345)
- [CubeSoft, Inc.](https://www.cube-soft.jp/)

#### Silver Sponsor
- [sknjpn](https://twitter.com/sknjpn)
- 野菜ジュース

#### Bronze Sponsor
アゲハマ, Fuyutsubaki, 😊, 🐝, jacking75, Chris Ohk, qppon, ysaito, おおやま, ShivAlley, lamuda, 🌻, fal_rnd, As Project, IZUNA, 柏崎でぃすこ, nasatame


[Siv3D の個人スポンサーになる :material-github:](https://github.com/sponsors/Reputeless){:target="_blank" .md-button} 
