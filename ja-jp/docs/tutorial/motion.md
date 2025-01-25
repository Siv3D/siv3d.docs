# 14. 図形や絵文字を動かす
時間の経過を使って変数を変化させ、図形や絵文字にモーション（動き）を作る方法を学びます。

## 14.1 モーションの基本
- 図形や絵文字の位置・大きさ・角度などを時間の経過に応じて変化させることで、モーションを表現できます
- 具体的には、モーションの状態を管理する変数を用意し、時間とともに変数の値を変化させます

### 固定値を加算するモーションの問題点
- 次のコードのように**毎フレーム固定値を加算**すると、メインループの実行頻度（[**チュートリアル 4.3**](./main.md)）によってモーションの速さが変わってしまいます

```cpp title="毎フレーム 1 ピクセルずつ移動させるプログラム" hl_lines="10"
# include <Siv3D.hpp>

void Main()
{
	// 円の X 座標
	double x = 0.0;

	while (System::Update())
	{
		x += 1.0;

		Circle{ x, 300, 50 }.draw();
	}
}
```

- 毎フレーム 1 ピクセルずつ移動させるプログラムでは
	- 60 FPS の環境では、1 秒間に 60 ピクセル移動します
	- 120 FPS の環境では、1 秒間に 120 ピクセル移動します
- これにより、異なるパソコンで**意図しないアニメーション結果になったり、ゲームの難易度が変わってしまったりする問題**が生じます

### 時間ベースのモーションの作り方
- フレームレートに左右されないモーションは、**前フレームからの経過時間**を使います
- `Scene::DeltaTime()` は、前フレームからの経過時間（秒）を `double` 型で返します
	- 60 FPS の場合、1 フレームあたり 1/60 秒（約 0.016 秒）です
	- 120 FPS の場合、1 フレームあたり 1/120 秒（約 0.008 秒）です
- この値に「毎秒移動したいピクセル数」をかけることで、フレームレートに応じた適切な加算値を得ることができます
- 例えば毎秒 100 ピクセル移動させる場合、次のようにします

```cpp title="毎秒 100 ピクセル移動させるプログラム" hl_lines="10"
# include <Siv3D.hpp>

void Main()
{
	// 円の X 座標
	double x = 0.0;

	while (System::Update())
	{
		x += (Scene::DeltaTime() * 100.0);

		Circle{ x, 300, 50 }.draw();
	}
}
```

- このプログラムでは、メインループの実行頻度が 60 FPS でも 120 FPS でも、**安定して 1 秒間に 100 ピクセルの速度で移動**します


## 14.2 絵文字を動かす
- `Vec2` 型の変数を使って、絵文字を動かします
- 絵文字は毎秒 100 ピクセルの速度で右に移動します

```cpp title="絵文字が毎秒 100 ピクセルの速度で右に移動するプログラム" hl_lines="10 15"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🐥"_emoji };

	// 絵文字を描画する位置
	Vec2 pos{ 100, 300 };

	while (System::Update())
	{
		// 位置を毎秒 100 ピクセルの速度で右に移動させる
		pos.x += (Scene::DeltaTime() * 100.0);

		// 絵文字を現在の位置に描画する
		emoji.drawAt(pos);
	}
}
```


## 14.3 絵文字を往復させる
- 14.2 のプログラムを拡張し、絵文字が画面端に到達したら反対方向に移動するようにします
- 移動速度を表す変数 `double velocity` を導入し、右に進む場合は `velocity` が正の値、左に進む場合は負の値とします
- 右に進んでいる最中に画面の右端（実際の 800 ピクセルから絵文字のサイズを考慮して 60 ピクセル引いた位置）に到達したら、`velocity` を負の値に変更します
- 同様に、左に進んでいる最中に画面の左端（60 ピクセル）に到達したら、`velocity` を再び正の値に戻します

```cpp title="絵文字が往復するプログラム" hl_lines="13"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🐥"_emoji };

	// 絵文字を描画する位置
	Vec2 pos{ 100, 300 };

	// 絵文字の移動速度
	double velocity = 100.0;

	while (System::Update())
	{
		// 位置を更新する
		pos.x += (Scene::DeltaTime() * velocity);

		if (((0.0 < velocity) && (740 < pos.x)) // 画面右端に到達するか
			|| ((velocity < 0.0) && (pos.x < 60))) // 画面左端に到達したら
		{
			// 速度を反転する
			velocity *= -1;
		}

		emoji.drawAt(pos);
	}
}
```


## 14.4 絵文字をバウンドさせる
- 14.3 のプログラムをさらに拡張し、上下左右方向で移動と跳ね返りを行います
- 各方向の移動速度を表す変数 `Vec2 velocity` を導入します
	- x 成分に左右方向の速度、y 成分に上下方向の速度を格納します
- `Vec2` は `+=` や `*` などの演算子を使って、x 成分と y 成分をまとめて操作できます
- `pos += (Scene::DeltaTime() * velocity);` のようなコードで簡潔に記述できます

```cpp title="絵文字がバウンドするプログラム" hl_lines="13 18"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🐥"_emoji };

	// 絵文字を描画する位置
	Vec2 pos{ 100, 300 };

	// 絵文字の移動速度
	Vec2 velocity{ 100.0, 100.0 };

	while (System::Update())
	{
		// 位置を更新する
		pos += (Scene::DeltaTime() * velocity);

		if (((0.0 < velocity.x) && (740 < pos.x)) // 画面右端に到達するか
			|| ((velocity.x < 0.0) && (pos.x < 60))) // 画面左端に到達したら
		{
			// x 方向の速度を反転する
			velocity.x *= -1;
		}

		if (((0.0 < velocity.y) && (540 < pos.y)) // 画面下端に到達するか
			|| ((velocity.y < 0.0) && (pos.y < 60))) // 画面上端に到達したら
		{
			// y 方向の速度を反転する
			velocity.y *= -1;
		}

		emoji.drawAt(pos);
	}
}
```


## 14.5 絵文字を回転させる
- 回転角度を表す変数 `double angle` を導入し、毎秒 180 度の速度で回転させます

```cpp title="絵文字が毎秒 180 度の速度で回転するプログラム" hl_lines="10"
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Texture emoji{ U"🍣"_emoji };

	// 絵文字の回転角度
	double angle = 0_deg;

	while (System::Update())
	{
		// 角度を更新する
		angle += (Scene::DeltaTime() * 180_deg);

		// 絵文字を現在の位置と角度で描画する
		emoji.rotated(angle).drawAt(400, 300);
	}
}
```


## 14.6 図形の変数

### `Circle` クラス
- `Circle` クラスは次のようなメンバ変数を持っています

```cpp
struct Circle
{
	union
	{
		Vec2 center;
		struct { double x, y; };
	};

	double r;
};
```

- 円 `circle` の中心の X 座標は、`circle.center.x` でも `circle.x` でもアクセスできることを意味します

### `Rect` クラス
- `Rect` クラスは次のようなメンバ変数を持っています

```cpp
struct Rect
{
	union
	{
		Point pos;
		struct { int32 x, y; };
	};

	union
	{
		Point size;
		struct { int32 w, h; };
	};
};
```

- 長方形 `rect` の左上の座標の X 座標は、`rect.pos.x` でも `rect.x` でもアクセスできることを意味します
- 長方形 `rect` の幅は、`rect.size.x` でも `rect.w` でもアクセスできることを意味します

### `RectF` クラス
- `RectF` クラスは次のようなメンバ変数を持っています

```cpp
struct RectF
{
	union
	{
		Vec2 pos;
		struct { double x, y; };
	};

	union
	{
		Vec2 size;
		struct { double w, h; };
	};
};
```

- `RectF` クラスは `Rect` クラスと同様のメンバ変数を持っていますが、`int32` ではなく `double` 型で座標やサイズを扱います


## 14.7 図形を動かす
- 14.6 で説明したメンバ変数を活用して図形を動かします

```cpp

```


## 振り返りチェックリスト

