# 17. キーボード入力を扱う

## 17.1 キーが押されたかを調べる
- `キー名.donw()` は、キーが押されると `true` を返します
- おもなキー名は次の表のとおりです

| キー | キー名 |
| --- | --- |
| A, B, C, ... | `KeyA`, `KeyB`, `KeyC`, ... |
| 1, 2, 3, ... | `Key1`, `Key2`, `Key3`, ... |
| F1, F2, F3, ... | `KeyF1`, `KeyF2`, `KeyF3`, ... |
| ↑, ↓, ←, → | `KeyUp`, `KeyDown`, `KeyLeft`, `KeyRight` |
| スペースキー | `KeySpace` |
| エンターキー | `KeyEnter` |
| バックスペースキー | `KeyBackspace` |
| Tab キー | `KeyTab` |
| エスケープキー | `KeyEscape` |
| Page up, Page down | `KeyPageUp`, `KeyPageDown` |
| Delete キー | `KeyDelete` |
| テンキーの 0, 1, 2, ... | `KeyNum0`, `KeyNum1`, `KeyNum2`, ... |
| Shift キー | `KeyShift` |
| 左シフト, 右シフト | `KeyLShift`, `KeyRShift` |
| Ctrl キー | `KeyControl` |
| (macOS) コマンドキー | `KeyCommand` |
| カンマ, ピリオド, スラッシュ | `KeyComma`, `KeyPeriod`, `KeySlash` |


![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/keyboard/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		// A キーが押されたら
		if (KeyA.down())
		{
			Print << U"A";
		}

		// スペースキーが押されたら
		if (KeySpace.down())
		{
			Print << U"Space";
		}

		// 1 キーが押されたら
		if (Key1.down())
		{
			Print << U"1";
		}
	}
}
```

## 17.2 キーが押されているかを調べる
- `.down()` と異なり、`.pressed()` は押されている間ずっと `true` を返します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/keyboard/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		// A キーが押されていたら
		if (KeyA.pressed())
		{
			Print << U"A";
		}

		// スペースキーが押されていたら
		if (KeySpace.pressed())
		{
			Print << U"Space";
		}

		// 1 キーが押されていたら
		if (Key1.pressed())
		{
			Print << U"1";
		}
	}
}

```

## 17.3 キーで絵文字を左右に動かす
- 矢印キーを使って絵文字を左右に動かすプログラムを作成します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/keyboard/3.png)

```cpp
# include <Siv3D.hpp>

// 現在のフレームでの移動量を計算する関数
Vec2 GetMovement(double speed)
{
	Vec2 move{ 0, 0 };

	if (KeyLeft.pressed()) // [←] キー
	{
		move.x -= speed;
	}

	if (KeyRight.pressed()) // [→] キー
	{
		move.x += speed;
	}

	return move;
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🐥"_emoji };

	// 絵文字の位置
	Vec2 pos{ 400, 300 };

	while (System::Update())
	{
		// 前フレームからの経過時間（秒）* 200
		const double move = (Scene::DeltaTime() * 200);

		pos += GetMovement(move);

		emoji.drawAt(pos);
	}
}
```

## 🧩 練習
次のようなプログラムを作成してみましょう。

### 練習 ① キーで絵文字を上下左右に動かす
- ++left++ キーで絵文字を左に移動
- ++right++ キーで絵文字を右に移動
- ++up++ キーで絵文字を上に移動
- ++down++ キーで絵文字を下に移動

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/keyboard/4-1.mp4?raw=true" autoplay loop muted playsinline></video>

??? info "ヒント"
	- 17.3 のコードを拡張します
	- ++up++ は `KeyUp`、++down++ は `KeyDown` です

??? success "解答例"
	```cpp
	# include <Siv3D.hpp>

	// 現在のフレームでの移動量を計算する関数
	Vec2 GetMovement(double speed)
	{
		Vec2 move{ 0, 0 };

		if (KeyLeft.pressed()) // [←] キー
		{
			move.x -= speed;
		}

		if (KeyRight.pressed()) // [→] キー
		{
			move.x += speed;
		}

		if (KeyUp.pressed()) // [↑] キー
		{
			move.y -= speed;
		}

		if (KeyDown.pressed()) // [↓] キー
		{
			move.y += speed;
		}

		return move;
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Texture emoji{ U"🐥"_emoji };

		// 絵文字の位置
		Vec2 pos{ 400, 300 };

		while (System::Update())
		{
			// 前フレームからの経過時間（秒）* 200
			const double move = (Scene::DeltaTime() * 200);

			pos += GetMovement(move);

			emoji.drawAt(pos);
		}
	}
	```


### 練習 ② 4 つの選択肢をキーで切り替える
- ++left++ キーで選択肢を左に移動
- ++right++ キーで選択肢を右に移動
- 最も左の項目の選択中に ++left++ キーを押しても選択肢は変わらない
- 最も右の項目の選択中に ++right++ キーを押しても選択肢は変わらない

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial/keyboard/4-2.mp4?raw=true" autoplay loop muted playsinline></video>

??? info "ヒント"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Texture emoji0{ U"🍣"_emoji };
		const Texture emoji1{ U"🍜"_emoji };
		const Texture emoji2{ U"🍔"_emoji };
		const Texture emoji3{ U"🍛"_emoji };

		int32 itemIndex = 2;

		while (System::Update())
		{
			emoji0.drawAt(100, 200);
			emoji1.drawAt(300, 200);
			emoji2.drawAt(500, 200);
			emoji3.drawAt(700, 200);

			Rect{ Arg::center((100 + 200 * itemIndex), 200), 150 }
				.drawFrame(6, ColorF{ 0.2 });
		}
	}
	```

??? success "解答例"
	```cpp
	# include <Siv3D.hpp>

	// キー入力によって選択中のアイテムインデックスを変更する関数
	int32 UpdateSelectIndex(int32 itemIndex, int32 maxIndex)
	{
		// 一番左でない状態で [←] キーが押されたら、インデックスを 1 減らす
		if ((0 < itemIndex) && KeyLeft.down())
		{
			--itemIndex;
		}

		// 一番右でない状態で [→] キーが押されたら、インデックスを 1 増やす
		if ((itemIndex < maxIndex) && KeyRight.down())
		{
			++itemIndex;
		}

		// 新しいインデックスを返す
		return itemIndex;
	}

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		const Texture emoji0{ U"🍣"_emoji };
		const Texture emoji1{ U"🍜"_emoji };
		const Texture emoji2{ U"🍔"_emoji };
		const Texture emoji3{ U"🍛"_emoji };

		int32 itemIndex = 0;

		while (System::Update())
		{
			emoji0.drawAt(100, 200);
			emoji1.drawAt(300, 200);
			emoji2.drawAt(500, 200);
			emoji3.drawAt(700, 200);

			itemIndex = UpdateSelectIndex(itemIndex, 3);

			Rect{ Arg::center((100 + 200 * itemIndex), 200), 150 }
				.drawFrame(6, ColorF{ 0.2 });
		}
	}
	```

## 振り返りチェックリスト
- [x] 主要なキーのキー名を学んだ
- [x] `キー名.down()` は、そのキーが押されると `true` を返すことを学んだ
- [x] `キー名.pressed()` は、そのキーが押されている間ずっと `true` を返すことを学んだ
- [x] キー入力を使って絵文字を動かすプログラムを作成した
- [x] キー入力を使って選択肢を切り替えるプログラムを作成した
