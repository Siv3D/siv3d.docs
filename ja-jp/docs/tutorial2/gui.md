# 38. GUI
ボタンやスライダー、テキストボックスなどの GUI 機能を利用する方法を学びます。

## 38.1 SimpleGUI の概要
- SimpleGUI は、よく使われる GUI ウィジェットを最小限のコードで実装できる機能です
- サポートしている GUI ウィジェットは次のとおりです：
	- **38.2** ボタン
	- **38.4** スライダー
	- **38.5** チェックボックス
	- **38.6** ラジオボタン
	- **38.7** テキストボックス
	- **38.8** テキストエリア
	- **38.9** カラーピッカー
	- **38.10** リストボックス
	- **38.12** メニューバー
	- **38.13** テーブル
- コードの簡単さを優先するため、色やフォントなど、デザインの柔軟性には制約があります
- 将来の Siv3D バージョンでは、SimpleGUI の上位版となる、より高度で複雑な GUI 機能を提供する予定です


## 38.2 ボタン
- ボタンは `SimpleGUI::Button()` 関数を使います
- テキストや位置、幅、状態などを設定できます

```cpp
bool SimpleGUI::Button(StringView label, const Vec2& pos, const Optional<double>& width = unspecified, bool enabled = true);
```

- 引数：
	- `label` : ボタンに表示するテキスト
	- `pos` : ボタンの左上の座標
	- `width` : ボタンの幅（`unspecified` を指定するとテキスト幅に合わせます）
	- `enabled` : ボタンが有効かどうか
- 戻り値：
	- ボタンが押されたら `true`, そうでなければ `false`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Red", Vec2{ 100, 50 }))
		{
			Scene::SetBackground(ColorF{ 0.8, 0.2, 0.2 });
		}

		if (SimpleGUI::Button(U"Green", Vec2{ 100, 100 }))
		{
			Scene::SetBackground(ColorF{ 0.2, 0.8, 0.2 });
		}

		if (SimpleGUI::Button(U"Blue", Vec2{ 100, 150 }))
		{
			Scene::SetBackground(ColorF{ 0.2, 0.2, 0.8 });
		}

		// ボタンの幅を 200px に指定する
		if (SimpleGUI::Button(U"White", Vec2{ 100, 300 }, 200))
		{
			Scene::SetBackground(ColorF{ 0.9 });
		}

		// ボタンの幅を 200px に指定する
		if (SimpleGUI::Button(U"Black", Vec2{ 100, 350 }, 200))
		{
			Scene::SetBackground(ColorF{ 0.1 });
		}

		// ボタンを無効化する
		if (SimpleGUI::Button(U"Gray", Vec2{ 100, 400 }, 200, false))
		{
			Scene::SetBackground(ColorF{ 0.5 });
		}

		// ボタンを無効化し、ボタンの幅はテキストに合わせる
		if (SimpleGUI::Button(U"Yellow", Vec2{ 100, 450 }, unspecified, false))
		{
			Scene::SetBackground(ColorF{ 0.8, 0.8, 0.1 });
		}
	}
}
```


## 38.3 スライダー
- スライダーは `SimpleGUI::Slider()` （水平方向）または `SimpleGUI::VerticalSlider()` 縦方向）を使います
- テキストや位置、幅、値の範囲などを設定できます
	
```cpp
bool SimpleGUI::Slider(double& value, double min, double max, const Vec2& pos, double sliderWidth = 120.0, bool enabled = true);
bool SimpleGUI::Slider(StringView label, double& value, const Vec2& pos, double labelWidth = 80.0, double sliderWidth = 120.0, bool enabled = true);
bool SimpleGUI::Slider(StringView label, double& value, double min, double max, const Vec2& pos, double labelWidth = 80.0, double sliderWidth = 120.0, bool enabled = true);
bool SimpleGUI::VerticalSlider(double& value, const Vec2& pos, double sliderHeight = 120.0, bool enabled = true);
bool SimpleGUI::VerticalSlider(double& value, double min, double max, const Vec2& pos, double sliderHeight = 120.0, bool enabled = true);
```

- 引数：
	- `value` : スライダーの値
	- `min` : スライダーの最小値
	- `max` : スライダーの最大値
	- `label` : スライダーのラベル
	- `pos` : スライダーの左上の座標
	- `labelWidth` : ラベルの幅
	- `sliderWidth` : スライダーの幅
	- `sliderHeight` : 縦方向スライダーの高さ
	- `enabled` : スライダーが有効かどうか
- 最小値・最大値指定がないものは、値の範囲は 0.0 ～ 1.0 です
- 戻り値：
	- スライダーの値が変更されたら `true`, そうでなければ `false`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	ColorF color1{ 1.0 };
	ColorF color2{ 1.0, 0.5, 0.0 };
	ColorF color3{ 0.2, 0.6, 0.9 };

	double value1 = 5.0;
	double value2 = 7.0;
	double value3 = 2.0;
	double value4 = 4.0;

	while (System::Update())
	{
		SimpleGUI::Slider(color1.r, Vec2{ 100, 40 });
		SimpleGUI::Slider(color1.g, Vec2{ 100, 80 });
		SimpleGUI::Slider(color1.b, Vec2{ 100, 120 });
		Circle{ 50, 100, 30 }.draw(color1);

		SimpleGUI::Slider(U"Red", color2.r, Vec2{ 100, 200 });
		SimpleGUI::Slider(U"Green", color2.g, Vec2{ 100, 240 });
		SimpleGUI::Slider(U"Blue", color2.b, Vec2{ 100, 280 });
		Circle{ 50, 260, 30 }.draw(color2);

		// スライダーの値を表示する。ラベルの幅 100px, スライダーの幅 200px
		SimpleGUI::Slider(U"R {:.2f}"_fmt(color3.r), color3.r, Vec2{ 100, 360 }, 100, 200);
		SimpleGUI::Slider(U"G {:.2f}"_fmt(color3.g), color3.g, Vec2{ 100, 400 }, 100, 200);
		SimpleGUI::Slider(U"B {:.2f}"_fmt(color3.b), color3.b, Vec2{ 100, 440 }, 100, 200);
		Circle{ 50, 420, 30 }.draw(color3);

		// 値の範囲が 0.0～10.0
		SimpleGUI::Slider(U"{:.2f}"_fmt(value1), value1, 0.0, 10.0, Vec2{ 500, 40 }, 60, 150);

		// スライダーを無効化する
		SimpleGUI::Slider(U"{:.2f}"_fmt(value2), value2, 0.0, 10.0, Vec2{ 500, 100 }, 60, 150, false);

		// 縦のスライダー
		SimpleGUI::VerticalSlider(value3, 0.0, 10.0, Vec2{ 500, 160 }, 200);
		SimpleGUI::VerticalSlider(value4, 0.0, 10.0, Vec2{ 560, 160 }, 200, false);
	}
}
```


## 38.4 アイコンの使用
- SimpleGUI で使われるフォントは、`Typeface::CJK_Regular_JP` に `Typeface::Icon_MaterialDesign` をフォールバックとして追加したものです
- 文字列に `\U000F0493` のようにアイコンのコードポイントを含めることで、SimpleGUI 上のテキストにアイコンを表示できます
- アイコンのコードポイントは、[Material Design Icons :material-open-in-new:](https://pictogrammers.com/library/mdi/){:target="_blank"} から確認できます
- SimpleGUI のフォントは `SimpleGUI::GetFont()` で取得でき、SimpleGUI 以外の用途で使うこともできます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	int32 up = 0, down = 0;
	double volume = 1.0;

	while (System::Update())
	{
		SimpleGUI::Button(U"\U000F0493 設定", Vec2{ 20, 40 }, 160);
		SimpleGUI::Button(U"\U000F1398 中断する", Vec2{ 20, 80 }, 160);
		SimpleGUI::Button(U"\U000F0E1E OK", Vec2{ 20, 120 }, 160);
		SimpleGUI::Button(U"\U000F0193 保存", Vec2{ 20, 160 }, 160);

		// Undo / Redo
		SimpleGUI::Button(U"\U000F054C", Vec2{ 200, 40 }, 40);
		SimpleGUI::Button(U"\U000F044E", Vec2{ 250, 40 }, 40);

		// 音量調整
		SimpleGUI::Slider((0.5 < volume) ? U"\U000F057E"
			: (0.0 < volume) ? U"\U000F0580" : U"\U000F0581", volume, Vec2{ 200, 100 }, 30, 170);

		// upvote
		if (SimpleGUI::Button(U"\U000F0513  {}"_fmt(up), Vec2{ 200, 160 }, 90))
		{
			++up;
		}

		// downvote
		if (SimpleGUI::Button(U"\U000F0511  {}"_fmt(down), Vec2{ 310, 160 }, 90))
		{
			++down;
		}
	}
}
```


## 38.5 チェックボックス
- チェックボックスは `SimpleGUI::CheckBox()` 関数を使います
- テキストや位置、幅、状態などを設定できます

```cpp
bool SimpleGUI::CheckBox(bool& checked, StringView label, const Vec2& pos, const Optional<double>& width = unspecified, bool enabled = true);
```

- 引数：
	- `checked` : チェックの状態
	- `label` : チェックボックスのラベル
	- `pos` : チェックボックスの左上の座標
	- `width` : チェックボックスの幅（`unspecified` を指定するとテキスト幅に合わせます）
	- `enabled` : チェックボックスが有効かどうか
- 戻り値：
	- チェックボックスの状態が変更されたら `true`, そうでなければ `false`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	bool checked0 = false;
	bool checked1 = true;
	bool checked2 = false;
	bool checked3 = false;
	bool checked4 = false;
	bool checked5 = false;

	while (System::Update())
	{
		SimpleGUI::CheckBox(checked0, U"Label 0", Vec2{ 100, 40 });
		SimpleGUI::CheckBox(checked1, U"Label 1", Vec2{ 100, 80 });
		SimpleGUI::CheckBox(checked2, U"Label 2", Vec2{ 100, 120 });

		// 幅 200px
		SimpleGUI::CheckBox(checked3, U"Label 3", Vec2{ 100, 180 }, 200);

		// 無効化
		SimpleGUI::CheckBox(checked4, U"Label 4", Vec2{ 100, 220 }, 200, false);

		// 幅はテキストに合わせる
		SimpleGUI::CheckBox(checked5, U"Label 5", Vec2{ 100, 260 }, unspecified, false);
	}
}
```


## 38.6 ラジオボタン
- ラジオボタンは `SimpleGUI::RadioButtons()` （垂直方向）または `SimpleGUI::HorizontalRadioButtons()` （水平方向）を使います
- テキストや位置、幅、状態などを設定できます

```cpp
bool SimpleGUI::RadioButtons(size_t& index, const Array<String>& options, const Vec2& pos, const Optional<double>& width = unspecified, bool enabled = true);
bool SimpleGUI::HorizontalRadioButtons(size_t& index, const Array<String>& options, const Vec2& pos, const Optional<double>& itemWidth = unspecified, bool enabled = true);
```

- 引数：
	- `index` : 選択されている項目のインデックス
	- `options` : 選択肢の配列
	- `pos` : ラジオボタンの左上の座標
	- `width` : ラジオボタンの幅（`unspecified` を指定するとテキスト幅に合わせます）
	- `itemWidth` : 水平ラジオボタンのアイテムの幅（`unspecified` を指定するとテキスト幅に合わせます）
	- `enabled` : ラジオボタンが有効かどうか
- 戻り値：
	- ラジオボタンの選択が変更されたら `true`, そうでなければ `false`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	size_t index0 = 0;
	size_t index1 = 2;
	size_t index2 = 0;
	size_t index3 = 1;
	size_t index4 = 0;
	size_t index5 = 0;

	const Array<String> options = { U"Red", U"Green", U"Blue" };
	const std::array<ColorF, 3> colors = { ColorF{ 0.8, 0.2, 0.2 }, ColorF{ 0.2, 0.8, 0.2 }, ColorF{ 0.2, 0.2, 0.8 } };

	Scene::SetBackground(colors[index1]);

	while (System::Update())
	{
		SimpleGUI::RadioButtons(index0, { U"Option1", U"Option2", U"Option3" }, Vec2{ 100, 40 });

		// 選択肢を Array<String> で指定
		if (SimpleGUI::RadioButtons(index1, options, Vec2{ 100, 180 }))
		{
			Scene::SetBackground(colors[index1]);
		}

		// 幅 200px
		SimpleGUI::RadioButtons(index2, { U"A", U"B" }, Vec2{ 400, 40 }, 200);

		// 無効化
		SimpleGUI::RadioButtons(index3, { U"A", U"B" }, Vec2{ 400, 140 }, 200, false);

		// 幅はテキストに合わせる
		SimpleGUI::RadioButtons(index4, { U"A", U"B" }, Vec2{ 400, 240 }, unspecified, false);

		// 水平ラジオボタン
		SimpleGUI::HorizontalRadioButtons(index5, { U"Apple", U"Bird", U"Cat", U"Dog" }, Vec2{ 100, 400 });
	}
}
```


## 38.7 テキストボックス
- 単一行のテキストボックスは、`TextEditState` クラスと `SimpleGUI::TextBox()` 関数を使います
- テキストボックスの位置、幅、文字数の上限、状態などを設定できます

```cpp
bool SimpleGUI::TextBox(TextEditState& text, const Vec2& pos, double width = 200.0, const Optional<size_t>& maxChars = unspecified, bool enabled = true);
```

- 引数：
	- `text` : `TextEditState` オブジェクト
	- `pos` : テキストボックスの左上の座標
	- `width` : テキストボックスの幅
	- `maxChars` : 入力可能な文字数の上限（`unspecified` を指定すると制限なし）
	- `enabled` : テキストボックスが有効かどうか
- 戻り値：
	- テキストが変更されたら `true`, そうでなければ `false`

### 38.7.1 テキストボックスの基本
- テキストボックスの初期文字列は `TextEditState`のコンストラクタで指定します
- テキストボックスの内容は `TextEditState` の `.text` メンバ変数で取得できます
- テキストボックスがアクティブかどうかは `TextEditState` の `.active` メンバ変数で取得できます
- テキストボックスの内容を消去するには `TextEditState` の `.clear()` メンバ関数を使います

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/7.1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	TextEditState te0;
	TextEditState te1{ U"Siv3D" }; // デフォルトのテキストを設定する
	TextEditState te2;
	TextEditState te3;

	while (System::Update())
	{
		ClearPrint();
		Print << te0.active; // アクティブかどうか
		Print << te0.text; // 入力されたテキスト (String)

		SimpleGUI::TextBox(te0, Vec2{ 100, 140 });

		SimpleGUI::TextBox(te1, Vec2{ 100, 200 });

		if (SimpleGUI::Button(U"Clear", Vec2{ 320, 200 }))
		{
			// テキストを消去する
			te1.clear();
		}

		// 幅 100px, 文字数を 4 文字までに制限する
		SimpleGUI::TextBox(te2, Vec2{ 100, 260 }, 100, 4);

		// 無効化
		SimpleGUI::TextBox(te3, Vec2{ 100, 320 }, 100, 4, false);
	}
}
```

- 文字列の範囲選択など、高度なテキスト編集機能は現在の SimpleGUI には実装されていません
- テキストボックスは、文字数がテキストボックスの幅を超える場合、テキストボックスの外側にあふれて表示されます
	- この挙動を避けたい場合は、文字数の上限を設定するか、**38.8** テキストエリアを使うことを検討してください


### 38.7.2 前後のテキストボックスへフォーカスを移動させる
- `SimpleGUI::TextBox()` では、あるテキストボックスがアクティブな時、エンターキーや Tab キーを押したり、無関係な場所をクリックしたりすると、そのテキストボックスが非アクティブになります
- テキストボックスが Tab キーによって非アクティブ化されたかは、`TextEditState` の `.tabKey` メンバ変数が `true` であるかどうかで検出できます
- テキストボックスが Tab キーによって非アクティブ化したことを検出し、前後のテキストボックスへフォーカスを移動させる仕組みは、次のように実装できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/7.2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	std::array<TextEditState, 3> textEditStates;

	Optional<int32> nextTextBox;

	while (System::Update())
	{
		// Tab キーの押下と同じフレームで次のテキストボックスをアクティブ化してしまうと
		// その Tab キーの押下で次のテキストボックスも非アクティブ化してしまうため、1 フレーム後にアクティブ化する
		if (nextTextBox)
		{
			if (*nextTextBox < textEditStates.size())
			{
				textEditStates[*nextTextBox].active = true;
			}

			nextTextBox.reset();
		}

		for (int32 i = 0; i < 3; ++i)
		{
			auto& state = textEditStates[i];

			const bool previous = state.active;

			SimpleGUI::TextBox(state, Vec2{ 100, (100 + i * 60) });

			// Tab キーによって非アクティブ化された
			if (previous && (state.active == false) && state.tabKey)
			{
				if (KeyShift.pressed()) // Shift キーが押されていたら前のテキストボックスへ
				{
					nextTextBox = (i - 1);
				}
				else
				{
					nextTextBox = (i + 1);
				}
			}
		}
	}
}
```


## 38.8 テキストエリア
- 複数行にわたるテキストを入力するためのテキストエリアは、`TextAreaEditState` クラスと `SimpleGUI::TextArea()` 関数を使います
- テキストエリアの位置、幅と高さ、文字数の上限、状態などを設定できます

```cpp
bool SimpleGUI::TextArea(TextAreaEditState& text, const Vec2& pos, const SizeF& size = SizeF{ 200, 100 }, size_t maxChars = PreferredTextAreaMaxChars, bool enabled = true);
```

- 引数：
	- `text` : `TextAreaEditState` オブジェクト
	- `pos` : テキストエリアの左上の座標
	- `size` : テキストエリアの幅と高さ
	- `maxChars` : 入力可能な文字数の上限
	- `enabled` : テキストエリアが有効かどうか
- 戻り値：
	- テキストが変更されたら `true`, そうでなければ `false`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	TextAreaEditState textAreaEditState;

	bool enabled = true;

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Clear", Vec2{ 40, 40 }, 100, TextInput::GetEditingText().isEmpty()))
		{
			textAreaEditState.clear();
		}

		SimpleGUI::CheckBox(enabled, U"enabled", Vec2{ 160, 40 });

		SimpleGUI::TextArea(textAreaEditState, Vec2{ 40, 90 }, SizeF{ 720, 300 }, SimpleGUI::PreferredTextAreaMaxChars, enabled);

		// テキストの内容には textAreaEditState.text でアクセスできる
	}
}
```

- 文字列の範囲選択など、高度なテキスト編集機能は現在の SimpleGUI には実装されていません


## 38.9 カラーピッカー
- カラーピッカーは `SimpleGUI::ColorPicker()` 関数を使います
- カラーピッカーの位置、状態などを設定できます
- 色は `HSV` 型の変数で管理します
- アルファ成分は操作できません

```cpp
bool SimpleGUI::ColorPicker(HSV& hsv, const Vec2& pos, bool enabled = true);
```

- 引数：
	- `hsv` : `HSV` 型の変数
	- `pos` : カラーピッカーの左上の座標
	- `enabled` : カラーピッカーが有効かどうか
- 戻り値：
	- 色が変更されたら `true`, そうでなければ `false`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	HSV color0 = Palette::Orange;
	HSV color1 = Palette::Skyblue;

	while (System::Update())
	{
		SimpleGUI::ColorPicker(color0, Vec2{ 100, 100 });
		Rect{ 100, 300, 100 }.draw(color0);

		// 無効化されているカラーピッカー
		SimpleGUI::ColorPicker(color1, Vec2{ 300, 100 }, false);
		Rect{ 300, 300, 100 }.draw(color1);
	}
}
```


## 38.10 リストボックス
- リストボックスは、`ListBoxState` クラスと `SimpleGUI::ListBox()` 関数を使います
- リストボックスの位置、幅と高さ、状態などを設定できます

```cpp
bool SimpleGUI::ListBox(ListBoxState& state, const Vec2& pos, double width = 160.0, double height = 156.0, bool enabled = true);
```

- 引数：
	- `state` : `ListBoxState` オブジェクト
	- `pos` : リストボックスの左上の座標
	- `width` : リストボックスの幅
	- `height` : リストボックスの高さ
	- `enabled` : リストボックスが有効かどうか
- 戻り値：
	- リストボックスの選択が変更されたら `true`, そうでなければ `false`

---

- リストボックスの状態は `ListBoxState` 型のオブジェクトによって管理します
- 選択肢一覧は `ListBoxState` の `Array<String>` 型のメンバ変数 `.items` に格納されます
- 選択されている項目のインデックスは `ListBoxState` の `Optional<size_t>` 型のメンバ変数 `.selectedItemIndex` に格納されます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	ListBoxState listBoxState1{
		{
			U"北海道", U"青森県", U"岩手県", U"宮城県", U"秋田県", U"山形県", U"福島県", U"茨城県",
			U"栃木県", U"群馬県", U"埼玉県", U"千葉県", U"東京都", U"神奈川県", U"新潟県", U"富山県",
			U"石川県", U"福井県", U"山梨県", U"長野県", U"岐阜県", U"静岡県", U"愛知県", U"三重県",
			U"滋賀県", U"京都府", U"大阪府", U"兵庫県", U"奈良県", U"和歌山県", U"鳥取県", U"島根県",
			U"岡山県", U"広島県", U"山口県", U"徳島県", U"香川県", U"愛媛県", U"高知県", U"福岡県",
			U"佐賀県", U"長崎県", U"熊本県", U"大分県", U"宮崎県", U"鹿児島県", U"沖縄県",
		}
	};

	ListBoxState listBoxState2{
		{
			U"吾輩は猫である（1905年1月 - 1906年8月、『ホトトギス』/1905年10月 - 1907年5月、大倉書店・服部書店）",
			U"坊っちゃん（1906年4月、『ホトトギス』/1907年、春陽堂刊『鶉籠』収録）",
			U"草枕（1906年9月、『新小説』/『鶉籠』収録）",
			U"二百十日（1906年10月、『中央公論』/『鶉籠』収録）",
			U"野分（1907年1月、『ホトトギス』/1908年、春陽堂刊『草合』収録）",
			U"虞美人草（1907年6月 - 10月、『朝日新聞』/1908年1月、春陽堂）",
			U"坑夫（1908年1月 - 4月、『朝日新聞』/『草合』収録）",
			U"三四郎（1908年9 - 12月、『朝日新聞』/1909年5月、春陽堂）",
			U"それから（1909年6 - 10月、『朝日新聞』/1910年1月、春陽堂）",
			U"門（1910年3月 - 6月、『朝日新聞』/1911年1月、春陽堂）",
			U"彼岸過迄（1912年1月 - 4月、『朝日新聞』/1912年9月、春陽堂）",
			U"行人（1912年12月 - 1913年11月、『朝日新聞』/1914年1月、大倉書店）",
			U"こゝろ（1914年4月 - 8月、『朝日新聞』/1914年9月、岩波書店）",
			U"道草（1915年6月 - 9月、『朝日新聞』/1915年10月、岩波書店）",
			U"明暗（1916年5月 - 12月、『朝日新聞』/1917年1月、岩波書店）",
		}
	};

	listBoxState2.selectedItemIndex = 3;

	ListBoxState listBoxState3 = listBoxState2;

	while (System::Update())
	{
		ClearPrint();

		if (listBoxState1.selectedItemIndex)
		{
			Print << listBoxState1.items[*listBoxState1.selectedItemIndex];
		}

		if (listBoxState2.selectedItemIndex)
		{
			Print << listBoxState2.items[*listBoxState2.selectedItemIndex];
		}

		if (listBoxState3.selectedItemIndex)
		{
			Print << listBoxState3.items[*listBoxState3.selectedItemIndex];
		}

		SimpleGUI::ListBox(listBoxState1, Vec2{ 620, 20 }, 120, 156);

		SimpleGUI::ListBox(listBoxState2, Vec2{ 780, 20 }, 240, 156, false);

		SimpleGUI::ListBox(listBoxState3, Vec2{ 20, 200 }, 1020, 480);
	}
}
```


## 38.11 見出し
- 各ウィジェットに見出しを付ける場合は `SimpleGUI::Headline()` 関数を使います
- 見出しの位置、幅、状態などを設定できます
- 見出しの高さは 40 ピクセルです

```cpp
void SimpleGUI::Headline(StringView text, const Vec2& pos, const Optional<double>& width = unspecified, bool enabled = true);
```

- 引数：
	- `text` : 見出しのテキスト
	- `pos` : 見出しの左上の座標
	- `width` : 見出しの幅（`unspecified` を指定するとテキスト幅に合わせます）
	- `enabled` : 見出しが有効かどうか

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/11.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	bool checked0 = false;
	bool checked1 = true;
	bool checked2 = false;
	HSV color = Palette::Orange;

	while (System::Update())
	{
		SimpleGUI::Headline(U"Checkbox", Vec2{ 100, 60 });
		SimpleGUI::CheckBox(checked0, U"Label 0", Vec2{ 100, 100 }, 160);
		SimpleGUI::CheckBox(checked1, U"Label 1", Vec2{ 100, 140 }, 160);
		SimpleGUI::CheckBox(checked2, U"Label 2", Vec2{ 100, 180 }, 160);

		SimpleGUI::Headline(U"ColorPicker", Vec2{ 300, 60 }, 160, false);
		SimpleGUI::ColorPicker(color, Vec2{ 300, 100 }, false);
	}
}
```


## 38.12 メニューバー
- `SimpleMenuBar` クラスを使うと、簡易的なメニューバーを作成できます
- メニューバーの項目は、`Array<std::pair<String, Array<String>>>` で設定します
	- `String` がメニューのタイトル、`Array<String>` がそのメニューに含まれる項目の名前です

### 38.12.1 メニューバーの基本
- `SimpleMenuBar` クラスには、状態の更新を行うメンバ関数 `.update()` と、描画を行うメンバ関数 `.draw()` があり、毎フレーム 2 つの関数を呼ぶ必要があります
	- `.update()` の戻り値は `Optional<MenuBarItemIndex>` 型です。メニューの項目が選択された場合はその項目のインデックス、選択されなかった場合は無効値です
	- `.draw()` はメニューバーを描画します
- 項目のインデックスは `MenuBarItemIndex{ メニューのインデックス, メニュー項目のインデックス }` で表されます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/12.1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Array<std::pair<String, Array<String>>> items
	{
		{ U"ゲーム", { U"新規", U"スコア", U"終了" }},
		{ U"ヘルプ", { U"\U000F0625  遊び方", U"\U000F14F7  リリースノート", U"ライセンス" } },
	};

	SimpleMenuBar menuBar{ items };

	while (System::Update())
	{
		if (const auto item = menuBar.update())
		{
			// 「ゲーム > 終了」が押されたら
			if (item == MenuBarItemIndex{ 0, 2 })
			{
				System::Exit();
			}

			// 「ヘルプ > ライセンス」が押されたら
			if (item == MenuBarItemIndex{ 1, 2 })
			{
				LicenseManager::ShowInBrowser();
			}
		}

		menuBar.draw();
	}
}
```


### 38.12.2 チェック可能なメニュー項目
- `SimpleMenuBar` のメンバ関数 `.setItemChecked()` を使うと、メニュー項目のチェック状態をオン/オフできます
- `.setItemChecked()` には、`MenuBarItemIndex` と `bool` を渡します
	- `true` の場合、その項目をチェック状態にし、`false` の場合はチェック状態を解除します
- ある項目がチェック状態であるかは、メンバ関数 `.getItemChecked(MenuBarItemIndex)` で取得します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/12.2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });
	const Font font{ FontMethod::MSDF, 48, Typeface::Bold };

	const Array<std::pair<String, Array<String>>> items
	{
		{ U"ゲーム", { U"新規", U"スコア", U"終了" }},
		{ U"設定", { U"オプション A", U"オプション B", U"オプション C" } },
		{ U"ヘルプ", { U"\U000F0625遊び方", U"\U000F14F7リリースノート", U"\U000F05E6ライセンス" } },
	};

	SimpleMenuBar menuBar{ items };

	while (System::Update())
	{
		if (const auto& item = menuBar.update())
		{
			// 「ゲーム > 終了」が押されたら
			if (item == MenuBarItemIndex{ 0, 2 })
			{
				System::Exit();
			}

			// 「設定 > オプション」が押されたら
			if (item->menuIndex == 1)
			{
				// チェック状態を反転する
				menuBar.setItemChecked(*item, (not menuBar.getItemChecked(*item)));
			}

			// 「ヘルプ > ライセンス」が押されたら
			if (item == MenuBarItemIndex{ 2, 2 })
			{
				LicenseManager::ShowInBrowser();
			}
		}

		menuBar.draw();

		font(U"A: {}"_fmt(menuBar.getItemChecked(MenuBarItemIndex{ 1, 0 })))
			.draw(30, Vec2{ 400, 100 }, ColorF{ 0.2 });

		font(U"B: {}"_fmt(menuBar.getItemChecked(MenuBarItemIndex{ 1, 1 })))
			.draw(30, Vec2{ 400, 140 }, ColorF{ 0.2 });

		font(U"C: {}"_fmt(menuBar.getItemChecked(MenuBarItemIndex{ 1, 2 })))
			.draw(30, Vec2{ 400, 180 }, ColorF{ 0.2 });
	}
}
```


### 38.12.3 メニューバーの色の変更
- `SimpleMenuBar::ColorPalette` クラスを使い、メニューバーの色をカスタマイズできます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/12.3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Array<std::pair<String, Array<String>>> items
	{
		{ U"ファイル", { U"新規作成", U"開く", U"名前を付けて保存", U"終了" }},
		{ U"編集", { U"元に戻す", U"切り取り", U"コピー", U"貼り付け", U"削除", U"検索する", U"次を検索", U"前を検索" } },
		{ U"表示", { U"拡大", U"縮小" } },
		{ U"ヘルプ", { U"\U000F0625  使い方", U"\U000F14F7  リリースノート", U"ライセンス" } },
	};

	SimpleMenuBar menuBar{ items };
	menuBar
		.setItemEnabled(1, 0, false)
		.setItemEnabled(1, 1, false)
		.setItemEnabled(1, 2, false)
		.setItemEnabled(1, 4, false)
		.setItemEnabled(1, 5, false)
		.setItemEnabled(1, 6, false)
		.setItemEnabled(1, 7, false);

	const SimpleMenuBar::ColorPalette palette
	{
		.menuBarColor = ColorF{ 0.82, 0.92, 0.86 },
			.activeMenuColor = ColorF{ 0.78, 0.88, 0.82 },
			.menuTextColor = ColorF{ 0.11 },
			.itemBoxColor = ColorF{ 0.99 },
			.itemMouseoverColor = ColorF{ 0.1, 0.4, 0.3 },
			.itemTextColor = ColorF{ 0.11 },
			.itemMouseoverTextColor = ColorF{ 1.0 },
			.itemDisabledTextColor = ColorF{ 0.8 },
			.itemRectShadowColor = ColorF{ 0.0, 0.5 }
	};

	menuBar.setColorPalette(palette);

	while (System::Update())
	{
		menuBar.update();

		menuBar.draw();
	}
}
```


## 38.13 テーブル
- `SimpleTable` クラスを使うと、縦横に項目が並んだテーブルを簡単に作成できます

### 38.13.1 テーブルの基本
- コンストラクタで各列の幅を指定します
- `push_back_row()` で行の内容を `Array<String>` で追加していきます
- 第 2 引数でアライメントを `Array<int32>` で指定することもできます
	- `-1` が左寄せ、`0` が中央寄せ、`1` が右寄せです
- テーブルはメンバ関数 `.draw(pos)` で描画します

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/13.1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 各列の幅が | 160 | 100 | 100 | のテーブルを作成する
	SimpleTable table{ { 160, 100, 100 } };

	// 行を追加する
	table.push_back_row({ U"Player", U"Rank", U"Rate" }, { -1, 1, 1 });
	table.push_back_row({ U"Alice", U"2", U"2832" });
	table.push_back_row({ U"Bob", U"6", U"2540" });
	table.push_back_row({ U"Carol", U"16", U"2315" });
	table.push_back_row({ U"Eve", U"121", U"1874" });

	while (System::Update())
	{
		table.draw(Vec2{ 40, 40 });
	}
}
```

### 38.13.2 テーブルのサンプル（1）
- 行を増減可能なテーブルのサンプルです

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/13.2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 36 };
	const Font fontBold{ FontMethod::MSDF, 36, Typeface::Bold };
	const Vec2 tablePos{ 40,40 };

	SimpleTable table{ { 100, 200, 200 }, {
			.variableWidth = true,
			.font = font,
			.columnHeaderFont = fontBold,
			.rowHeaderFont = fontBold,
		} };
	table.push_back_row({ U"", U"日本", U"アメリカ" }, { 0, 0, 0 });
	table.push_back_row({ U"面積", U"約 37 万 8 千平方キロメートル", U"約 983 万平方キロメートル" }, { 0, -1, -1 });
	table.push_back_row({ U"人口", U"約 1 億 2 千万人", U"約 3 億 3 千万人" });
	table.push_back_row({ U"言語", U"日本語", U"英語" });
	table.push_back_row({ U"通貨", U"円 (JPY)", U"ドル (USD)" });

	Optional<Point> activeIndex;

	while (System::Update())
	{
		if (SimpleGUI::Button(U"行を追加", Vec2{ 740, 40 }, 130))
		{
			table.push_back_row({ 0, -1, -1 });
		}

		if (SimpleGUI::Button(U"行を削除", Vec2{ 740, 80 }, 130))
		{
			table.pop_back_row();
		}

		if (MouseL.down())
		{
			activeIndex = table.cellIndex(tablePos, Cursor::Pos());
		}

		table.draw(tablePos);

		if (activeIndex)
		{
			table.cellRegion(tablePos, *activeIndex).drawFrame(2, 1, ColorF{ 0.1 });
		}
	}
}
```

### 38.13.3 テーブルのサンプル（2）
- マウスオーバー時に行の背景色を変更するテーブルのサンプルです

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/13.2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font fontHeavy{ FontMethod::MSDF, 36, Typeface::Heavy };
	const Vec2 tablePos{ 40,40 };

	SimpleTable table{ { 160, 100, 100 }, {
			.cellHeight = 40,
			.borderThickness = 2,
			.backgroundColor = none,
			.textColor = ColorF{ 1.0 },
			.borderColor = ColorF{ 0.29, 0.33, 0.41 },
			.hasVerticalBorder = false,
			.hasOuterBorder = false,
			.font = fontHeavy,
			.fontSize = 24,
			.hoveredRow = [](const Point& index) -> Optional<ColorF>
			{
				if (index.y != 0)
				{
					return ColorF{ 1.0, 0.2 };
				}

				return none;
			},
		} };
	table.push_back_row({ U"Player", U"Rank", U"Rate" }, { -1, 1, 1 });
	table.push_back_row({ U"Alice", U"2", U"2832" });
	table.push_back_row({ U"Bob", U"6", U"2540" });
	table.push_back_row({ U"Carol", U"16", U"2315" });
	table.push_back_row({ U"Eve", U"121", U"1874" });

	for (int32 y = 1; y < 3; ++y)
	{
		table.setRowTextColor(y, ColorF{ 1.00, 0.7, 0.25 });
	}

	for (int32 y = 3; y < 5; ++y)
	{
		table.setRowTextColor(y, ColorF{ 0.82, 0.56, 0.84 });
	}

	while (System::Update())
	{
		table.region(tablePos).stretched(24, 12).rounded(10.0).draw(ColorF{ 0.18, 0.20, 0.35 });
		table.draw(tablePos);
	}
}
```

### 38.13.4 テーブルのサンプル（3）
- 2025 年 12 月のカレンダーのサンプルです

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/13.3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font fontBold{ FontMethod::MSDF, 36, Typeface::Bold };
	const Vec2 tablePos{ 40,40 };

	SimpleTable table{ Array<double>(7, 60.0), {
			.borderThickness = 2,
			.backgroundColor = none,
			.borderColor = ColorF{ 1.0 },
			.hasOuterBorder = false,
			.font = fontBold,
			.hoveredCell = [](const Point& index) -> Optional<ColorF>
			{
				if (index.y != 0)
				{
					return ColorF{ 0.94 };
				}

				return none;
			},
		} };
	table.push_back_row({ U"Sun", U"Mon", U"Tue", U"Wed", U"Thu", U"Fri", U"Sat" }, Array<int32>(7, 0));
	table.setRowBackgroundColor(0, ColorF{ 1.00, 0.8, 0.7 });
	table.push_back_row(Array<int32>(7, 1));
	table.push_back_row();
	table.push_back_row();
	table.push_back_row();
	table.push_back_row();

	for (int32 i = 0; i < (7 * 5); ++i)
	{
		const auto date = Date{ 2025, 11, 30 } + Days{ i };
		const Point index{ (i % 7), (1 + (i / 7)) };
		table.setText(index, Format(date.day));

		if (date.month != 12)
		{
			table.setTextColor(index, ColorF{ 0.7 });
		}
	}

	while (System::Update())
	{
		table.region(tablePos).stretched(10, 20).rounded(5).draw();
		table.draw(tablePos);
	}
}
```

### 38.13.5 テーブルのサンプル（4）
- 時刻表のサンプルです

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/13.4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font font{ FontMethod::MSDF, 36 };
	const Font fontBold{ FontMethod::MSDF, 36, Typeface::Bold };
	const Vec2 tablePos{ 40,40 };

	SimpleTable table{ { 100, 80, 80 }, {
			.cellHeight = 28.0,
			.hasHorizontalBorder = false,
			.font = font,
			.fontSize = 16,
			.columnHeaderFont = fontBold,
			.columnHeaderFontSize = 14,
			.hoveredRow = [](const Point& index) -> Optional<ColorF>
			{
				if (index.y != 0)
				{
					return ColorF{ 1.0, 0.95, 0.90 };
				}

				return none;
			},
		} };
	table.push_back_row({ U"", U"こだま701", U"のぞみ5" }, { -1, 0, 0 });
	table.push_back_row({ U"東　京　発", U"6:30", U"6:33" });
	table.push_back_row({ U"品　川　〃", U"6:37", U"6:40" });
	table.push_back_row({ U"新横浜　〃", U"6:48", U"6:51" });
	table.push_back_row({ U"小田原　〃", U"7:05", U"ﾚ" });
	table.push_back_row({ U"熱　海　〃", U"7:14", U"ﾚ" });
	table.push_back_row({ U"三　島　〃", U"7:26", U"ﾚ" });
	table.push_back_row({ U"新富士　〃", U"7:37", U"ﾚ" });
	table.push_back_row({ U"静　岡　〃", U"7:51", U"ﾚ" });
	table.push_back_row({ U"掛　川　〃", U"8:08", U"ﾚ" });
	table.push_back_row({ U"浜　松　〃", U"8:23", U"ﾚ" });
	table.push_back_row({ U"豊　橋　〃", U"8:39", U"ﾚ" });
	table.push_back_row({ U"三河安城〃", U"8:56", U"ﾚ" });
	table.push_back_row({ U"名古屋　着", U"9:06", U"8:10" });
	table.push_back_row({ U"名古屋　発", U"", U"8:12" });

	while (System::Update())
	{
		table.draw(tablePos);
	}
}
```

### 38.13.6 テーブルのサンプル（5）
- 天気予報のサンプルです

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/13.5.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Font fontBold{ FontMethod::MSDF, 36, Typeface::Bold };
	const Vec2 tablePos{ 40,40 };

	SimpleTable table{ { 80, 80, 80, 80 }, {
		.cellHeight = 26.0,
		.borderThickness = 2.0,
		.borderColor = ColorF{ 0.6 },
		.columnHeaderFont = fontBold,
		.columnHeaderFontSize = 15.0,
		.rowHeaderFont = fontBold,
		.rowHeaderFontSize = 15.0,
	} };

	table.push_back_row({ U"", U"今日", U"明日", U"明後日" }, { 0, 0, 0, 0 });
	table.push_back_row({ U"札幌", U"\U000F0597\U000F19B0\U000F0590", U"\U000F0590/\U000F0597", U"\U000F0590" });
	table.push_back_row({ U"東京", U"\U000F0599\U000F19B0\U000F0590", U"\U000F0597/\U000F0590", U"\U000F0590\U000F19B0\U000F0597" });
	table.push_back_row({ U"大阪", U"\U000F0590\U000F19B0\U000F0597", U"\U000F0597", U"\U000F0599/\U000F0590" });
	table.push_back_row({ U"福岡", U"\U000F0597\U000F19B0\U000F0590", U"\U000F0590\U000F19B0\U000F0599", U"\U000F0599/\U000F0590" });
	table.push_back_row({ U"沖縄", U"\U000F0590", U"\U000F0590/\U000F0597", U"\U000F0590\U000F19B0\U000F0599" });

	for (size_t y = 1; y < table.rows(); ++y)
	{
		for (size_t x = 1; x < table.columns(); ++x)
		{
			const bool isRainy = table.getItem(y, x).text.includes(U'\U000F0597');
			const bool isSunny = table.getItem(y, x).text.includes(U'\U000F0599');
			const bool isCloudy = table.getItem(y, x).text.includes(U'\U000F0590');

			if (isRainy)
			{
				table.setBackgroundColor(y, x, ColorF{ 0.7, 0.9, 1.0 });
			}
			else if (isSunny)
			{
				table.setBackgroundColor(y, x, ColorF{ 1.0, 0.9, 0.7 });
			}
			else if (isCloudy)
			{
				table.setBackgroundColor(y, x, ColorF{ 0.9 });
			}
		}
	}

	while (System::Update())
	{
		table.draw(tablePos);
	}
}
```

