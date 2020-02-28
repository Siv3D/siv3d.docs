
# 千葉 (2020) 資料

## はじめに

### 勉強会中の補足資料

- 勉強会中に追加で紹介した内容や URL を貼ります
- https://scrapbox.io/siv3d-chiba/補足資料
- 休憩時間のあとに LT タイム（発表者がいれば）

### Siv3D とは

- Windows や macOS, Linux で動くインタラクティブなアプリケーションを開発するための C++ フレームワーク
- 完全に無料、オープンソース、ゆるいライセンス (MIT)
- 用途: ゲーム、研究用プロジェクト、メディアアート制作、画像・音声処理、競技プログラミング

### Siv3D の特長

- 先進: 最先端の C++17 規格で設計されている（現在 C++20 へ移行中）
- シンプル: 1 つのことをするのに、たいてい 1 行で済む
- 一貫: 基本を覚えれば、あらゆる機能を同じルールで使える
- 高速: 実行時性能に優れた技術を多数採用（Zstandard, SFMT, OpenCV, libjpeg-turbo...）

### Siv3D を使うメリット
- 少ない勉強時間で幅広い分野の技術にアクセス
- 非常に短いプログラムでアプリを作れる。コードのシェアが楽（数十行コピペするだけ）
- 最新の C++ の書き方や使い方を学べる
- 困ったことがあれば Slack や Twitter でサポート
    - https://twitter.com/Reputeless/status/1178609247279468544

### 2 種類ある Siv3D
- Siv3D
    - 2016 年まで開発、Windows 版のみ
    - Web 上にサンプルや解説の記事が多い
- OpenSiv3D (今日使う！)
    - 2016 年から開発
    - オープンソース、Windows/macOS/Linux 対応
    - Siv3D を 100 とすると 移植済み機能 75, 新機能 90

### Siv3D の開発への参加
- [ソースコード](https://github.com/Siv3D/OpenSiv3D/graphs/contributors) やドキュメントのコントリビュータ 15 人以上
- [実装会](https://siv3d.github.io/ja-jp/community/dev-day/) を毎月開催

### おすすめ Siv3D ゲーム作品
- [One week, My room](https://www.google.com/search?q=One+week%2C+My+room)
- [CORGI JUMP](https://voidproc.itch.io/corgi-jump)

### さまざまな活用例
- [Siv3D に関するツイートを検索](https://twitter.com/search?q=Siv3D%20OR%20OpenSiv3D&src=typed_query&f=live)
- [高専プロコン](https://twitter.com/Reputeless/status/1184847931892850688)
- 中高の部活（[早稲田](https://twitter.com/waseda_pcp/status/1176817389255610370), [海城](https://twitter.com/KaijoComputer/status/1118433731155357696), [麻布](https://twitter.com/Reputeless/status/1173060995519877123), [筑駒](https://twitter.com/Reputeless/status/1191314798790635525))
- 大学での研究 (多数)

## コードを動かそう

### 0. 事前準備

- Windows の場合
    - [プロジェクトを作成](https://siv3d.github.io/ja-jp/#opensiv3d)
    - Visual Studio のビルドモードを Debug から Release に変更（処理が早くなる）<br><img src="https://github.com/Siv3D/siv3d.docs.images/blob/master/article/event/vs-release-mode.gif?raw=true" style="display: inline; margin:none;" width="400">
- macOS の場合
    - プロジェクトテンプレート `examples/empty/empty.xcodeproj` を[開く](https://siv3d.github.io/ja-jp/#macos_2)

### 1. 基本サンプル

- 適当に改造してみる
- [OpenSiv3D で使える絵文字の一覧](https://siv3d.github.io/ja-jp/reference/emojis/)

```C++
# include <Siv3D.hpp>

void Main()
{
    // 背景を水色にする
    Scene::SetBackground(ColorF(0.8, 0.9, 1.0));

    // 大きさ 60 のフォントを用意
    const Font font(60);

    // 猫のテクスチャを用意
    const Texture cat(Emoji(U"🐈"));

    // 猫の座標
    Vec2 catPos(640, 450);

    while (System::Update())
    {
        // テキストを画面の中心に描く
        font(U"Hello, Siv3D!🐣").drawAt(Scene::Center(), Palette::Black);

        // 大きさをアニメーションさせて猫を表示する
        cat.resized(100 + Periodic::Sine0_1(1s) * 20).drawAt(catPos);

        // マウスカーソルに追従する半透明の赤い円を描く
        Circle(Cursor::Pos(), 40).draw(ColorF(1, 0, 0, 0.5));

        // [A] キーが押されたら
        if (KeyA.down())
        {
            // Hello とデバッグ表示する
            Print << U"Hello!";
        }

        // ボタンが押されたら
        if (SimpleGUI::Button(U"Move the cat", Vec2(600, 20)))
        {
            // 猫の座標を画面内のランダムな位置に移動する
            catPos = RandomVec2(Scene::Rect());
        }
    }
}
```

### 2. サンプルコードを動かしてみる (50 分)

- [ゲーム](https://siv3d.github.io/ja-jp/sample/game/)
- [アプリ](https://siv3d.github.io/ja-jp/sample/app/)
    - マイクのサンプルは MacBook で動かない場合も (v0.4.3 で修正)

### 小休憩 (10 分)

### 3. チュートリアルを読んでみる (60 分)

- 1 章 ～ 14 章の知識があれば簡単なゲームが作れる

### 休憩 (20 分)

- https://scrapbox.io/siv3d-chiba/補足資料

## 作ってみよう

### 4. アプリ開発練習「おみくじ」

#### 4.1 基本

```C++
# include <Siv3D.hpp>

void Main()
{
	Array<String> items =
	{
		U"大吉", U"吉", U"中吉", U"小吉", U"末吉", U"凶"
	};

	bool active = true;
	String currentItem;

	while (System::Update())
	{
		if (active)
		{
			// ランダムに 1 個選択
			currentItem = items.choice();
		}

		if (SimpleGUI::Button(currentItem, Vec2(220, 100), 150))
		{
			active = !active;
		}
	}
}
```

#### 4.2 確率に重みづけ

```C++
# include <Siv3D.hpp>

void Main()
{
	Array<String> items =
	{
		U"大吉", U"吉", U"中吉", U"小吉", U"末吉", U"凶"
	};

	// 確率の重みづけ（合計はどんな数になってもよい）
	Array<double> weights =
	{
		5, 30, 100, 60, 20, 10
	};
	DiscreteDistribution di(weights);

	// エラーチェック
	if (items.size() != di.size())
	{
		throw Error(U"アイテムの数と weight の数が違います。");
	}

	bool active = true;
	String currentItem;

	while (System::Update())
	{
		if (active)
		{
			// 重みづけをもとにランダムに 1 個選択
			currentItem = DiscreteSample(items, di);
		}

		if (SimpleGUI::Button(currentItem, Vec2(220, 100), 150))
		{
			active = !active;
		}
	}
}
```

#### 4.3 テキストファイルからのロードとパース

- カレントディレクトリは `"App"` ("example" や "engine" フォルダがあるフォルダ)
- カレントディレクトリに `data.txt` を以下の内容で用意する

```txt
大吉=5
吉=30
中吉=100
小吉=60
末吉=20
凶=10
```

```C++
# include <Siv3D.hpp>

void Main()
{
	Array<String> items;
	Array<double> weights;

	// テキストファイルをオープン
	TextReader reader(U"data.txt");
	if (!reader)
	{
		throw Error(U"ファイルの読み込みに失敗");
	}

	// 各行から読み込む
	{
		String line;
		while (reader.readLine(line))
		{
			// 空白行はスキップ
			if (line.isEmpty())
			{
				continue;
			}

			// = の前と後ろで分ける
			Array<String> elements = line.split(U'=');

			if (elements.size() != 2)
			{
				throw Error(U"不正な書式！");
			}

			items << elements[0];
			weights << Parse<double>(elements[1]);
		}
	}

	DiscreteDistribution di(weights);

	// 配列の中身を確認
	Print << U"items: " << items;
	Print << U"weights: " << weights;
	Print << U"確率: " << di.probabilities();

	// エラーチェック
	if (items.isEmpty())
	{
		throw Error(U"アイテムがありません。");
	}

	// エラーチェック
	if (items.size() != di.size())
	{
		throw Error(U"アイテムの数と weight の数が違います。");
	}

	bool active = true;
	String currentItem;

	while (System::Update())
	{
		if (active)
		{
			// 重みづけをもとにランダムに 1 個選択
			currentItem = DiscreteSample(items, di);
		}

		if (SimpleGUI::Button(currentItem, Vec2(220, 100), 150))
		{
			active = !active;
		}
	}
}
```

### 5. アプリ開発練習「落ちてくるアイテムを集めるゲーム」

#### 5.1 移動の基本

```C++
# include <Siv3D.hpp>

void Main()
{
    // プレイヤーの絵文字テクスチャ
    const Texture playerTexture(Emoji(U"😃"));

    // プレイヤーのスピード（ピクセル / 秒)
    const double playerSpeed = 500.0;

    // プレイヤーの座標
    Vec2 playerPos(400, 500);

    while (System::Update())
    {
        //
        // 更新
        //

        // 前のフレームからの経過時間 (秒)
        const double deltaTime = Scene::DeltaTime();

        if (KeyLeft.pressed()) // ← キーが押されていたら
        {
            playerPos.x -= playerSpeed * deltaTime;
        }
        else if (KeyRight.pressed()) // → キーが押されていたら
        {
            playerPos.x += playerSpeed * deltaTime;
        }

        // 壁の外に出ないようにする
        playerPos.x = Clamp(playerPos.x, 0.0, 800.0);

        //
        // 描画
        //

        // 背景はグラデーションの Rect
        Scene::Rect()
            .draw(Arg::top = ColorF(0.1, 0.4, 0.8), Arg::bottom = ColorF(0.3, 0.7, 1.0));

        // プレイヤーのテクスチャの描画
        playerTexture.drawAt(playerPos);
    }
}
```

#### 5.2 落ちてくるアイテム

```C++
# include <Siv3D.hpp>

// アイテムの情報
struct Item
{
    Vec2 pos;
    int32 score;
};

void Main()
{
    const Texture playerTexture(Emoji(U"😃"));

    const double playerSpeed = 500.0;

    Vec2 playerPos(400, 500);

    // アイテムのテクスチャ
    const Texture itemTexture(Emoji(U"🍰"));

    // アイテムの配列
    Array<Item> items;

    // アイテムの出現間隔
    const double spawnTime = 0.5;

    // アイテム出現までの累積時間
    double time = 0.0;

    // アイテムの落下スピード
    const double itemSpeed = 200.0;

    while (System::Update())
    {
        // デバッグ用
        ClearPrint();
        Print << U"アイテムの個数: " << items.size();

        //
        // 更新
        //

        const double deltaTime = Scene::DeltaTime();

        if (KeyLeft.pressed())
        {
            playerPos.x -= playerSpeed * deltaTime;
        }
        else if (KeyRight.pressed())
        {
            playerPos.x += playerSpeed * deltaTime;
        }

        playerPos.x = Clamp(playerPos.x, 0.0, 800.0);

        // アイテムの出現と移動と消滅
        {
            time += deltaTime;

            // spawnTime が経過するごとに追加
            while (time >= spawnTime)
            {
                Item item;
                item.pos.x = Random(100, 700);
                item.pos.y = -100;
                item.score = 10;

                items << item;

                time -= spawnTime;
            }

            for (auto& item : items)
            {
                item.pos.y += deltaTime * itemSpeed;
            }

            // 画面外に出たら消去
            items.remove_if([](const Item& item) { return item.pos.y > 700; });
        }

        //
        // 描画
        //

        Scene::Rect()
            .draw(Arg::top = ColorF(0.1, 0.4, 0.8), Arg::bottom = ColorF(0.3, 0.7, 1.0));

        playerTexture.drawAt(playerPos);

        // アイテムの描画
        for (const auto& item : items)
        {
            itemTexture.drawAt(item.pos);
        }
    }
}
```

#### 5.3 アイテムとの当たり判定

```C++
# include <Siv3D.hpp>

struct Item
{
    Vec2 pos;
    int32 score;
};

void Main()
{
    const Texture playerTexture(Emoji(U"😃"));

    const double playerSpeed = 500.0;

    Vec2 playerPos(400, 500);

    const Texture itemTexture(Emoji(U"🍰"));

    Array<Item> items;

    const double spawnTime = 0.5;

    double time = 0.0;

    const double itemSpeed = 200.0;

    // スコア
    int32 score = 0;

    while (System::Update())
    {
        ClearPrint();
        Print << U"アイテムの個数: " << items.size();
        Print << U"スコア: " << score;

        //
        // 更新
        //

        const double deltaTime = Scene::DeltaTime();

        if (KeyLeft.pressed())
        {
            playerPos.x -= playerSpeed * deltaTime;
        }
        else if (KeyRight.pressed())
        {
            playerPos.x += playerSpeed * deltaTime;
        }

        playerPos.x = Clamp(playerPos.x, 0.0, 800.0);

        {
            time += deltaTime;

            while (time >= spawnTime)
            {
                Item item;
                item.pos.x = Random(100, 700);
                item.pos.y = -100;
                item.score = 10;

                items << item;

                time -= spawnTime;
            }

            for (auto& item : items)
            {
                item.pos.y += deltaTime * itemSpeed;
            }

            // プレイヤーのあたり判定の円
            const Circle playerCirlce(playerPos, 50);

            // アイテムのあたり判定と回収したアイテムの削除
            for (auto it = items.begin(); it != items.end();)
            {
                // アイテムのあたり判定の円
                const Circle itemCircle(it->pos, 50);

                // デバッグ表示
                itemCircle.drawFrame(5, Palette::Red);

                // 交差したらアイテムを削除
                if (playerCirlce.intersects(itemCircle))
                {
                    // (削除する前に) スコアを加算
                    score += it->score;

                    // アイテムを削除
                    it = items.erase(it);
                }
                else
                {
                    ++it;
                }
            }

            items.remove_if([](const Item& item) { return item.pos.y > 700; });
        }

        //
        // 描画
        //

        Scene::Rect()
            .draw(Arg::top = ColorF(0.1, 0.4, 0.8), Arg::bottom = ColorF(0.3, 0.7, 1.0));

        playerTexture.drawAt(playerPos);

        // デバッグ描画
        Circle(playerPos, 50).drawFrame(1, Palette::Red);

        for (const auto& item : items)
        {
            itemTexture.drawAt(item.pos);

            // デバッグ描画
            Circle(item.pos, 50).drawFrame(1, Palette::Red);
        }
    }
}
```


### 6. アプリ開発練習「Slack 用 GIF 絵文字作成ツール」

#### 6.1 描画した内容の保存

```C+
# include <Siv3D.hpp>

void Main()
{
    // レンダーテクスチャ
    MSRenderTexture renderTexture(128, 128);

    // フォント
    const Font font(60, Typeface::Heavy);

    while (System::Update())
    {
        // renderTexture を白でクリア
        renderTexture.clear(Palette::White);
        {
            // 描画先を renderTexture にする
            ScopedRenderTarget2D renderTarget(renderTexture);

            font(U"高専").drawAt(64, 64, Palette::Orange);
        }

        // レンダーテクスチャへの描画を完了
        {
            Graphics2D::Flush();
            renderTexture.resolve();
        }

        if (MouseL.down()) // 左クリックしたら
        {
            // レンダーテクスチャの中身を Image で取得
            Image image;
            renderTexture.readAsImage(image);

            // Image を 64x64 に縮小し、PNG 形式で保存
            image.scale(64, 64).save(U"frame.png");
        }

        // レンダーテクスチャの描画
        renderTexture.draw(20, 20);
    }
}
```

#### 6.2 GIF アニメの保存

```C++
# include <Siv3D.hpp>

void Main()
{
    MSRenderTexture renderTexture(128, 128);

    const Font font(60, Typeface::Heavy);

    // GIF アニメ出力クラス
    AnimatedGIFWriter gif(U"emoji.gif", 64, 64);

    // 書き出したフレーム数
    int32 frameCount = 0;

    while (System::Update())
    {
        renderTexture.clear(Palette::White);
        {
            ScopedRenderTarget2D renderTarget(renderTexture);

            // (64, 64) を中心に回転する座標変換を適用
            Transformer2D transform(Mat3x2::Rotate(18_deg * frameCount, Vec2(64, 64)));

            font(U"千葉").drawAt(64, 64, Palette::Orange);
        }
   
        {
            Graphics2D::Flush();
            renderTexture.resolve();
        }

        if (frameCount < 20) // 20 フレーム書き出し
        {
            Image image;
            renderTexture.readAsImage(image);

            // GIF アニメのフレームとして書き出し
            gif.writeFrame(image.scaled(64, 64), 0.1s);

            ++frameCount;
        }
        else // 20 フレーム書き出したら終了
        {
            break;
        }

        // レンダーテクスチャの描画
        renderTexture.draw(20, 20);
    }
}
```

### 7. ライブコーディング（時間があれば）

- https://scrapbox.io/siv3d-chiba/ライブコーディング

### 8. Siv3D に興味を持ったら

- [OpenSiv3D のリポジトリ](https://github.com/Siv3D/OpenSiv3D) を ★ 登録
    - [全ヘッダ](https://github.com/Siv3D/OpenSiv3D/tree/master/Siv3D/include/Siv3D) が読める
- [Siv3D ユーザコミュニティ Slack](https://siv3d.github.io/ja-jp/community/community/) に登録
- チュートリアルを進める
- チュートリアルをよく読む
- チュートリアルを（略
- 古いバージョンのサンプルに気を付ける
    - 最新の公式ドキュメントは https://siv3d.github.io/ja-jp/ (OpenSiv3D v0.4.0 以降)
    - https://scrapbox.io/Siv3D/ は古い (OpenSiv3D v0.3.x 以前)
    - https://github.com/Siv3D/Reference-JP/wiki は OpenSiv3D ではなく旧 Siv3D
- 作ったものを[ハッシュタグ付きでツイート](https://twitter.com/search?q=Siv3D%20OR%20OpenSiv3D&src=typed_query&f=live)

