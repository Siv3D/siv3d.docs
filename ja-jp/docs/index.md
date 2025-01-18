# 創造のための C++ フレームワーク Siv3D
<div class="logo"><img src="https://siv3d.jp/logo.png" width="1450" height="450"></div>

**Siv3D（シブスリーディー）**は、音や画像、AI を使ったゲームやアプリを、モダンな C++ コードで楽しく簡単に開発できるオープンソースのフレームワークです。豊富なサンプルコードとチュートリアルが用意され、オンラインのユーザコミュニティで気軽に質問や相談ができます。


## Siv3D をダウンロードする

[Windows :material-microsoft-windows:](download/windows.md){ .md-button .md-button--primary }[macOS :material-apple:](download/macos.md){ .md-button .md-button--primary }[Ubuntu :material-ubuntu:](download/ubuntu.md){ .md-button .md-button--primary }[Web * :material-web:](download/web.md){ .md-button .md-button--primary }

<small>* Web 版は有志による非公式の拡張機能で、設定がやや複雑なため Siv3D 中級者以上向けです。</small>

<div class="grid cards" markdown>

-   :material-laptop:{ .lg .middle }　__動作環境__

    ---

    Windows / macOS / Ubuntu / Web

-   :material-share-outline:{ .lg .middle }　__ライセンス__

    ---

    MIT ライセンス

-  :material-application-parentheses-outline:{ .lg .middle }　__開発言語__

    ---

    C++20 / HLSL / GLSL

-   :material-update:{ .lg .middle }　__バージョン__

    ---

    0.6.15（2024-07-04） / 0.8.0（開発中）

</div>

## コミュニティ

| 日付 | イベント | 対象者 |
| --- | --- | --- |
| 2024-12-15 | [Siv3D 勉強会 in 京都 2024 :material-open-in-new:](https://connpass.com/event/334897/){:target="_blank"} | だれでも |
| 2024-12-14 | [Siv3D 実装会 in 京都 2024 :material-open-in-new:](https://connpass.com/event/332833/){:target="_blank"} | だれでも |
| 2024-12-01～25 | [Siv3D Advent Calendar 2024 :material-open-in-new:](https://qiita.com/advent-calendar/2024/siv3d){:target="_blank"} | オンライン・だれでも |

[過去のイベント](community/history.md){ .md-button } [:fontawesome-brands-discord: Siv3D Discord サーバーに参加する](https://discord.gg/mzevvsY){ .md-button .md-button--primary }


## C++ によるゲーム・アプリ開発を効率化
2D/3D グラフィックス描画、音声再生、入力処理、物理演算、画像処理、AI、ネットワーク通信など、実用的なソフトウェアの開発に必要なクラスや関数が豊富に提供されます。

Unity や Unreal Engine などの商用ゲームエンジンと異なり、独自のエディタやスクリプトを用いず、純粋な C++ コードだけでゲームやアプリケーションを完成させられます。

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
				// 描く位置を現時点でのマウスカーソルの位置に変更する
				pos = Cursor::Pos();
			}

			// テクスチャを描く
			texture.drawAt(pos);
		}
	}
	```

=== "実行結果"

    ![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2024/index/emoji_cursor.gif)

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
	<iframe src="https://store.steampowered.com/widget/3382090/" frameborder="0" width="646" height="190"></iframe>
	<iframe src="https://store.steampowered.com/widget/2943760/" frameborder="0" width="646" height="190"></iframe>
	<iframe src="https://store.steampowered.com/widget/3328960/" frameborder="0" width="646" height="190"></iframe>


??? success "3. プログラミングコンテストにおける情報可視化・GUI（クリックで詳細）"
	C++ で問題を解く際に、Siv3D を使って情報の可視化や GUI を作成することで、問題解決の効率を向上させることができます。全国高等専門学校プログラミングコンテスト（高専プロコン）第 34 回大会（2023 年）の競技部門では、優勝、準優勝、3 位、特別賞を、Siv3D を活用したチームが独占しました。また、第 33 回大会、第 29 大会の優勝チームも Siv3D を使いました。直近の大会では出場校の 3 分の 1 以上が Siv3D を使っています。

??? success "4. 研究のためのソフトウェア開発（クリックで詳細）"
	Siv3D を使って実験用のアプリを作ったり、シミュレーションの結果をアニメーションで表現したりすることができます。Siv3D は、研究者が自分のアイデアを形にするための強力なツールとして活用されています。大学生や研究者による Siv3D の利用事例を紹介します。

	<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/Zk99iQE-TxE?si=EoWZOfbDDoCRRxFS" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

	<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/AAbWaGPMjg0?si=RC7OVc9PtSoLUi7x" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

??? success "5. オープンソース活動への参加（クリックで詳細）"
	プログラミングの経験や実装力を生かして、Siv3D 本体の開発やバグの報告、サンプルコードの作成、記事執筆など、Siv3D での開発体験を向上するためのオープンソース活動に参加できます。これまで中学生を含む 60 人以上が、Siv3D 本体にソースコードをコミットしています。

??? success "6. Web ブラウザ向けの作品配信（クリックで詳細）"
	有志ユーザによって提供されている Web 版（[OpenSiv3D for Web :material-open-in-new:](download/web.md){:target="_blank"}）を使うことで、Siv3D で作った C++ アプリを、ブラウザ上で動く Web アプリに移植できます。スマホやタブレットをターゲットにして、たくさんの人に作品を届けることができます。Siv3D 製の Web アプリの例を紹介します。

	<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">ゲーム「選挙で勝とう 2024」を作成しました！<br><br>舞台は日本の衆議院選挙。ある党の党首のつもりになって、12 日間の選挙を戦う全く新しいゲームです。ぜひ遊んでみてください！！！<br><br>URL: <a href="https://t.co/tnIySuWA4A">https://t.co/tnIySuWA4A</a> <a href="https://t.co/ZcNHVy61ex">pic.twitter.com/ZcNHVy61ex</a></p>&mdash; E869120 (@e869120) <a href="https://twitter.com/e869120/status/1849277131513053548?ref_src=twsrc%5Etfw">October 24, 2024</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

	<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">大学の講義で教えるために、浮動小数点数 (float 型) の仕組みを学べるアプリを Siv3D で作りました。<a href="https://t.co/BNSwkqAHzt">https://t.co/BNSwkqAHzt</a><br>(PC でのアクセス推奨) <a href="https://twitter.com/hashtag/Siv3D?src=hash&amp;ref_src=twsrc%5Etfw">#Siv3D</a> <a href="https://twitter.com/hashtag/OpenSiv3D?src=hash&amp;ref_src=twsrc%5Etfw">#OpenSiv3D</a> <a href="https://t.co/OAmZfo5R9D">pic.twitter.com/OAmZfo5R9D</a></p>&mdash; Ryo Suzuki (@Reputeless) <a href="https://twitter.com/Reputeless/status/1542160459658416129?ref_src=twsrc%5Etfw">June 29, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


## Siv3D を採用する理由

<div class="grid cards" markdown>

-   __1. オープンソースで安心__

    ---

    Siv3D は [オープンソース :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D){:target="_blank"} です。誰でも内部のコードを調べたり、改造したりできます。サードパーティ・ライブラリを含め、商用利用を妨げる条件はありません。開発したゲームやアプリケーションの収益は 100% 開発者が獲得できます。

-   __2. すぐに始められる__

    ---

    Windows 向けの Siv3D SDK インストーラはわずか 120 MB, インストールは数クリックで完了し、すぐに Visual Studio のメニューに表示されます。開発に必要な情報が公式のチュートリアルにそろっていて、書籍や入門記事を探す必要はありません。

-   __3. 非常に短いコード__

    ---

    描画や入出力を実現するための便利な関数とクラスが豊富に揃っていて、1 つの .cpp ファイルで簡単なアプリケーションが完成します。ソースコードを GitHub や GitHub Gist で瞬時にシェアして、世界中の Siv3D ユーザと技術を交換しましょう。

-  __4. 小さな学習、大きな力__

    ---

    Siv3D は 2,200 ファイルのソースコードと 90 のサードパーティ・ソフトウェアからなる大規模エンジンです。そのパワフルな機能を、一貫した Siv3D の API を覚えるだけで自在に扱えます。学習コストを最小限に抑え、作品開発に集中できます。

-  __5. 親切なコミュニティ__

    ---

    Siv3D で困ったことがあれば、Discord の [オンラインコミュニティ](community/community.md) が助けになります。学校への [無料訪問勉強会](community/community.md) も行っています。オープンソース開発に興味のある学生には、Siv3D を練習場とするサポートプログラムを提供しています。

-   __6. Web ブラウザで動く__

    ---

    Siv3D で作った C++ プログラムをほぼそのままで、ブラウザ上で動く Web アプリに移植できます。スマホやタブレットをターゲットにすることで、よりたくさんの人に作品を届けることができます。
</div>


## Siv3D で作られたゲームの例

| Mutable 50 \| sashi | マクスウェルのパズルな悪魔 \| muratsubo Games |
| --- | --- |
| <iframe src="https://www.youtube-nocookie.com/embed/UAZB_YyMgmQ?si=j50IwGk5kx2cTI7Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe> | <iframe src="https://www.youtube-nocookie.com/embed/mGzAnk1hgNU?si=BVSVHigjTNrtKj2J" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe> |

| For the GHOSTs \| サークル 原産国 | One week, My room \| サークル 常夜灯 |
| --- | --- |
| <iframe src="https://www.youtube-nocookie.com/embed/3ns8QqGWry4?si=v6JKWmueG9RVxVL0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe> | <iframe src="https://www.youtube-nocookie.com/embed/S--QI455r3Q?si=jEabVpHLloiLO_gy" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe> |


## 法人協賛
<div class="sponsor"><a href="https://www.bandainamcostudios.com/" target="_blank"><img src="https://siv3d.jp/sponsors/バンダイナムコスタジオ.png" alt="バンダイナムコスタジオ"></a></div>

### 過去のイベント
[バンダイナムコスタジオ杯 Siv3D ゲームジャム | 結果発表ページ](event/gamejam2023.md){ .md-button }


## 個人スポンサー

#### Gold Sponsor 
- [TOMOAKI12345](https://github.com/TOMOAKI12345){:target="_blank"}
- [CubeSoft, Inc.](https://www.cube-soft.jp/){:target="_blank"}

#### Silver Sponsor
- [sknjpn](https://twitter.com/sknjpn){:target="_blank"}
- 野菜ジュース

#### Bronze Sponsor
アゲハマ, Fuyutsubaki, 😊, 🐝, jacking75, Chris Ohk, qppon, ysaito, おおやま, ShivAlley, lamuda, 🌻, fal_rnd, As Project, IZUNA, 柏崎でぃすこ, nasatame, sashi

---

[協賛企業の募集について](sponsorship/corporate-sponsor.md){ .md-button } [Siv3D の個人スポンサーになる :material-github:](https://github.com/sponsors/Reputeless){:target="_blank" .md-button} 
