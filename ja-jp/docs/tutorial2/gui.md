# 28. GUI
ボタンやスライダー、テキストボックスなどの GUI 機能を利用する方法を学びます。

## 28.1 ボタン
ボタンを作成するには `SimpleGUI::Button()` 関数を使うと便利です。関数ではボタンのテキストや位置、幅、状態を設定できます。ボタンの幅を省略するか、`unspecified` を指定すると、ボタンの幅はテキストの幅になります。この関数は自身が押されたときに `true` を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		if (SimpleGUI::Button(U"Red", Vec2{ 100, 100 }))
		{
			Scene::SetBackground(ColorF{ 0.8, 0.2, 0.2 });
		}

		if (SimpleGUI::Button(U"Green", Vec2{ 100, 150 }))
		{
			Scene::SetBackground(ColorF{ 0.2, 0.8, 0.2 });
		}

		if (SimpleGUI::Button(U"Blue", Vec2{ 100, 200 }))
		{
			Scene::SetBackground(ColorF{ 0.2, 0.2, 0.8 });
		}

		// ボタンの幅を 200px に指定する
		if (SimpleGUI::Button(U"White", Vec2{ 100, 250 }, 200))
		{
			Scene::SetBackground(ColorF{ 0.9 });
		}

		// ボタンの幅を 200px に指定する
		if (SimpleGUI::Button(U"Black", Vec2{ 100, 300 }, 200))
		{
			Scene::SetBackground(ColorF{ 0.1 });
		}

		// ボタンを無効化する
		if (SimpleGUI::Button(U"Gray", Vec2{ 100, 350 }, 200, false))
		{
			Scene::SetBackground(ColorF{ 0.5 });
		}

		// ボタンを無効化し、ボタンの幅はテキストに合わせる
		if (SimpleGUI::Button(U"Yellow", Vec2{ 100, 400 }, unspecified, false))
		{
			Scene::SetBackground(ColorF{ 0.8, 0.8, 0.1 });
		}
	}
}
```

## 28.2 スライダー
スライダーを作成するには `SimpleGUI::Slider()` 関数を使うと便利です。関数ではスライダーのテキストや位置、幅、値の範囲などを設定できます。テキストを持たない縦方向のスライダーは `SimpleGUI::VerticalSlider()` を使います。スライダーの値は `double` 型の変数で管理します。どちらの関数も、スライダーの指す値が変更されたときに `true` を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

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


## 28.3 GUI におけるアイコンの使用
GUI 機能のうち、`SimpleGUI::Button()` や `SimpleGUI::Slider()` のように、フォントを明示的に指定せず使える GUI 関数では、文字列に `\U000F0493` のようにアイコン ID を記述することで、アイコンを表示できます。使えるアイコンは [Material Design Icons :material-open-in-new:](https://pictogrammers.com/library/mdi/){:target="_blank"} に含まれているものです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });
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


## 28.4 チェックボックス
チェックボックスを作成するには `SimpleGUI::CheckBox()` 関数を使うと便利です。関数ではチェックボックスのテキストや位置、幅、状態などを設定できます。幅を省略するか、`unspecified` を指定すると、チェックボックスの幅はテキストに合わせた幅になります。チェックの状態は `bool` 型の変数で管理します。この関数は値が変更されたときに `true` を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	bool checked0 = false;
	bool checked1 = true;
	bool checked2 = false;
	bool checked3 = false;
	bool checked4 = false;
	bool checked5 = false;

	while (System::Update())
	{
		SimpleGUI::CheckBox(checked0, U"Label0", Vec2{ 100, 40 });
		SimpleGUI::CheckBox(checked1, U"Label1", Vec2{ 100, 80 });
		SimpleGUI::CheckBox(checked2, U"Label2", Vec2{ 100, 120 });

		// 幅 200px
		SimpleGUI::CheckBox(checked3, U"Label3", Vec2{ 100, 180 }, 200);

		// 無効化
		SimpleGUI::CheckBox(checked4, U"Label4", Vec2{ 100, 220 }, 200, false);

		// 幅はテキストに合わせる
		SimpleGUI::CheckBox(checked5, U"Label5", Vec2{ 100, 260 }, unspecified, false);
	}
}
```


## 28.5 ラジオボタン
ラジオボタン作成するには `SimpleGUI::RadioButtons()` 関数を使うと便利です。関数ではラジオボタンのテキストや位置、幅、状態などを設定できます。水平にアイテムが並ぶラジオボタンは `SimpleGUI::HorizontalRadioButtons()` を使います。ラジオボタンの選択項目は `size_t` 型の変数で管理します。どちらの関数も、ラジオボタンの選択項目が変更されたときに `true` を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/5.png)

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


## 28.6 テキストボックス
単一行のテキストボックスを作成するには `SimpleGUI::TextBox()` 関数を使うと便利です。関数ではテキストボックスの位置、幅、文字数の上限、状態などを設定できます。テキストボックスの状態（入力されている文字列など）は `TextEditState` 型のオブジェクトによって管理します。この関数はテキストが変更されたときに `true` を返します。

### 28.6.1 テキストボックスの基本

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/6.1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	TextEditState te0;
	TextEditState te1{ U"Siv3D" };// デフォルトのテキストを設定する
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


### 28.6.2 前後のテキストボックスへフォーカスを移動させる
`SimpleGUI::TextBox()` では、あるテキストボックスがアクティブな時、エンターキーや Tab キーを押したり、無関係な場所をクリックしたりすると、そのテキストボックスが非アクティブになります。あるテキストボックスが Tab キーによって非アクティブ化したことを検出し、前後のテキストボックスへフォーカスを移動させるには、次のようにします。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/6.2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

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


## 28.7 テキストエリア
複数行のテキストエリアを作成するには、`SimpleGUI::TextArea()` 関数を使うと便利です。関数ではテキストエリアの位置、幅と高さ、文字数の上限、状態などを設定できます。テキストボックスの状態（入力されている文字列など）は `TextAreaEditState` 型のオブジェクトによって管理します。この関数はテキストが変更されたときに `true` を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/7.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

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


## 28.8 カラーピッカー
カラーピッカーを作成するには、`SimpleGUI::ColorPicker()` 関数を使うと便利です。関数ではカラーピッカーの位置、状態などを設定できます。色は `HSV` 型の変数で管理します。この関数は色が変更されたときに `true` を返します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/8.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

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


## 28.9 リストボックス
リストボックスを作成するには、`SimpleGUI::ListBox()` 関数を使うと便利です。関数ではリストボックスの位置、幅と高さ、状態などを設定できます。リストボックスの状態は `ListBoxState` 型のオブジェクトによって管理します。この関数はリストボックスの選択が変更されたときに `true` を返します。

選択されている項目のインデックスは `ListBoxState` の `Optional<size_t>` 型のメンバ変数 `selectedItemIndex` に格納されます。選択肢の配列は `ListBoxState` の `Array<String>` 型のメンバ変数 `items` に格納されます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/9.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

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


## 28.10 見出し
GUI の各ウィジェットに見出しを付けたい場合、`SimpleGUI::Headline` を使うと便利です。関数では見出しの位置、幅、状態などを設定できます。見出しの高さは 40 ピクセルです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/10.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	bool checked0 = false;
	bool checked1 = true;
	bool checked2 = false;
	HSV color = Palette::Orange;

	while (System::Update())
	{
		SimpleGUI::Headline(U"Checkbox", Vec2{ 100, 60 });
		SimpleGUI::CheckBox(checked0, U"Label0", Vec2{ 100, 100 }, 160);
		SimpleGUI::CheckBox(checked1, U"Label1", Vec2{ 100, 140 }, 160);
		SimpleGUI::CheckBox(checked2, U"Label2", Vec2{ 100, 180 }, 160);

		SimpleGUI::Headline(U"ColorPicker", Vec2{ 300, 60 }, 160, false);
		SimpleGUI::ColorPicker(color, Vec2{ 300, 100 }, false);
	}
}
```


## 28.11 メニューバー
`SimpleMenuBar` クラスを使うと、簡易的なメニューバーを作成できます。メニューバーの項目は、`Array<std::pair<String, Array<String>>>` で指定します。`String` がメニューのタイトル、`Array<String>` がそのメニューに含まれる項目の名前です。

### 28.11.1 メニューバーの基本
`SimpleMenuBar` は、毎フレーム、状態の更新を行うメンバ関数 `.update()` と、描画を行うメンバ関数 `.draw()` を呼ぶ必要があります。

`.update()` は `Optional<MenuBarItemIndex>` を返します。メニューの項目が選択されると、その項目のインデックスを返します。項目が選択されなかった場合は `none` を返します。

`.draw()` はメニューバーを描画します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/11.1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Array<std::pair<String, Array<String>>> menus
	{
		{ U"ゲーム", { U"新規", U"スコア", U"終了" }},
		{ U"ヘルプ", { U"\U000F0625  遊び方", U"\U000F14F7  リリースノート", U"ライセンス" } },
	};

	SimpleMenuBar menuBar{ menus };

	while (System::Update())
	{
		if (const auto& item = menuBar.update())
		{
			// 「終了」が押されたら
			if (item == MenuBarItemIndex{ 0, 2 })
			{
				return;
			}

			// 「ライセンス」が押されたら
			if (item == MenuBarItemIndex{ 1, 2 })
			{
				LicenseManager::ShowInBrowser();
			}
		}

		menuBar.draw();
	}
}
```


### 28.11.2 チェック可能なメニュー項目
`SimpleMenuBar` のメンバ関数 `.setItemChecked()` を使うと、メニュー項目のチェック状態をオン/オフできます。`.setItemChecked()` には、`MenuBarItemIndex` と `bool` を渡します。`bool` が `true` の場合、その項目をチェック状態にし、`false` の場合はチェック状態を解除します。

ある項目がチェック状態であるかは、メンバ関数 `.getItemChecked()` で取得できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/11.2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	const Array<std::pair<String, Array<String>>> menus
	{
		{ U"ゲーム", { U"新規", U"スコア", U"終了" }},
		{ U"設定", { U"オプション A", U"オプション B", U"オプション C" } },
		{ U"ヘルプ", { U"\U000F0625遊び方", U"\U000F14F7リリースノート", U"\U000F05E6ライセンス" } },
	};

	SimpleMenuBar menuBar{ menus };

	while (System::Update())
	{
		ClearPrint();
		Print << menuBar.getItemChecked(MenuBarItemIndex{ 1, 0 });
		Print << menuBar.getItemChecked(MenuBarItemIndex{ 1, 1 });
		Print << menuBar.getItemChecked(MenuBarItemIndex{ 1, 2 });

		if (const auto& item = menuBar.update())
		{
			// 「終了」が押されたら
			if (item == MenuBarItemIndex{ 0, 2 })
			{
				return;
			}

			// 「ライセンス」が押されたら
			if (item == MenuBarItemIndex{ 2, 2 })
			{
				LicenseManager::ShowInBrowser();
			}

			if (item->menuIndex == 1)
			{
				// チェック状態を反転する
				menuBar.setItemChecked(*item, (not menuBar.getItemChecked(*item)));
			}
		}

		menuBar.draw();
	}
}
```


### 28.11.3 メニューバーの色の変更
`SimpleMenuBar::ColorPalette` クラスで設定したカラーパレットを使って、メニューバーの色を変更できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/11.3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	Vec2 pos = Scene::Center(), target = pos, velocity = Vec2::Zero();

	const Array<std::pair<String, Array<String>>> menus
	{
		{ U"ファイル", { U"新規作成", U"開く", U"名前を付けて保存", U"終了" }},
		{ U"編集", { U"元に戻す", U"切り取り", U"コピー", U"貼り付け", U"削除", U"検索する", U"次を検索", U"前を検索" } },
		{ U"表示", { U"拡大", U"縮小" } },
		{ U"ヘルプ", { U"\U000F0625  使い方", U"\U000F14F7  リリースノート", U"ライセンス" } },
	};

	SimpleMenuBar menuBar{ menus };
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
		if (const auto& item = menuBar.update())
		{
			Print << U"menuIndex: {}, itemIndex: {}"_fmt(item->menuIndex, item->itemIndex);
		}

		if (Scene::Rect().mouseOver() && (not MouseL.cleared()))
		{
			Cursor::RequestStyle(CursorStyle::Hand);

			if (MouseL.down())
			{
				target = Cursor::Pos();
			}
		}

		pos = Math::SmoothDamp(pos, target, velocity, 0.25);

		pos.asCircle(40).drawShadow(Vec2{ 2, 2 }, 8).draw();

		menuBar.draw();
	}
}
```


## 28.12 テーブル

### 28.12.1 テーブルの基本
`SimpleTable` クラスを使うと、テーブルを簡単に作成できます。コンストラクタで各列の幅を指定したあと、`push_back_row()` で行の内容を `Array<String>` で追加していきます。追加でアライメントを `Array<int32>` で指定することもできます。アライメントは -1 が左寄せ、0 が中央寄せ、1 が右寄せです。テーブルはメンバ関数 `.draw()` で描画できます。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/12.1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });

	// 各列の幅が 160, 100, 100 のテーブルを作成
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


### 28.12.2 テーブルのスタイルの変更
スタイルの設定をカスタマイズすることで、様々なバリエーションのテーブルを作成できます。サンプルを次に示します。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/tutorial2/gui/12.2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(1280, 720);
	Scene::SetBackground(ColorF{ 0.8, 0.9, 1.0 });
	const Font font1{ FontMethod::MSDF, 36 };
	const Font font2{ FontMethod::MSDF, 36, Typeface::Heavy };
	const Font font3{ FontMethod::MSDF, 36, Typeface::Bold };

	//
	// table 1
	//
	SimpleTable table1{ { 100, 200, 200 }, {
			.variableWidth = true,
			.font = font1,
			.columnHeaderFont = font3,
			.rowHeaderFont = font3,
		} };
	table1.push_back_row({ U"", U"日本", U"アメリカ" }, { 0, 0, 0 });
	table1.push_back_row({ U"面積", U"約 37 万 8 千平方キロメートル", U"約 983 万平方キロメートル" }, { 0, -1, -1 });
	table1.push_back_row({ U"人口", U"約 1 億 2 千万人", U"約 3 億 3 千万人" });
	table1.push_back_row({ U"言語", U"日本語", U"英語" });
	table1.push_back_row({ U"通貨", U"円 (JPY)", U"ドル (USD)" });

	//
	// table 2
	//
	SimpleTable table2{ { 160, 100, 100 }, {
			.cellHeight = 40,
			.borderThickness = 2,
			.backgroundColor = none,
			.textColor = ColorF{ 1.0 },
			.borderColor = ColorF{ 0.29, 0.33, 0.41 },
			.hasVerticalBorder = false,
			.hasOuterBorder = false,
			.font = font2,
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
	table2.push_back_row({ U"Player", U"Rank", U"Rate" }, { -1, 1, 1 });
	table2.push_back_row({ U"Alice", U"2", U"2832" });
	table2.push_back_row({ U"Bob", U"6", U"2540" });
	table2.push_back_row({ U"Carol", U"16", U"2315" });
	table2.push_back_row({ U"Eve", U"121", U"1874" });

	for (int32 y = 1; y < 3; ++y)
	{
		table2.setRowTextColor(y, ColorF{ 1.00, 0.7, 0.25 });
	}

	for (int32 y = 3; y < 5; ++y)
	{
		table2.setRowTextColor(y, ColorF{ 0.82, 0.56, 0.84 });
	}

	//
	// table 3
	//
	SimpleTable table3{ Array<double>(7, 60.0), {
			.borderThickness = 2,
			.backgroundColor = none,
			.borderColor = ColorF{ 1.0 },
			.hasOuterBorder = false,
			.font = font3,
			.hoveredCell = [](const Point& index) -> Optional<ColorF>
			{
				if (index.y != 0)
				{
					return ColorF{ 0.94 };
				}

				return none;
			},
		} };
	table3.push_back_row({ U"Sun", U"Mon", U"Tue", U"Wed", U"Thu", U"Fri", U"Sat" }, Array<int32>(7, 0));
	table3.setRowBackgroundColor(0, ColorF{ 1.00, 0.8, 0.7 });
	table3.push_back_row(Array<int32>(7, 1));
	table3.push_back_row();
	table3.push_back_row();
	table3.push_back_row();
	table3.push_back_row();
	table3.push_back_row();

	for (int32 i = 0; i < (7 * 6); ++i)
	{
		const auto date = Date{ 2023, 3, 26 } + Days{ i };
		const Point index{ (i % 7), (1 + (i / 7)) };
		table3.setText(index, Format(date.day));

		if (date.month != 4)
		{
			table3.setTextColor(index, ColorF{ 0.7 });
		}
	}

	//
	// table 4
	//
	SimpleTable table4{ { 100, 80, 80 }, {
			.cellHeight = 28.0,
			.hasHorizontalBorder = false,
			.font = font1,
			.fontSize = 16,
			.columnHeaderFont = font3,
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
	table4.push_back_row({ U"", U"こだま701", U"のぞみ5" }, { -1, 0, 0 });
	table4.push_back_row({ U"東　京　発", U"6:30", U"6:33" });
	table4.push_back_row({ U"品　川　〃", U"6:37", U"6:40" });
	table4.push_back_row({ U"新横浜　〃", U"6:48", U"6:51" });
	table4.push_back_row({ U"小田原　〃", U"7:05", U"ﾚ" });
	table4.push_back_row({ U"熱　海　〃", U"7:14", U"ﾚ" });
	table4.push_back_row({ U"三　島　〃", U"7:26", U"ﾚ" });
	table4.push_back_row({ U"新富士　〃", U"7:37", U"ﾚ" });
	table4.push_back_row({ U"静　岡　〃", U"7:51", U"ﾚ" });
	table4.push_back_row({ U"掛　川　〃", U"8:08", U"ﾚ" });
	table4.push_back_row({ U"浜　松　〃", U"8:23", U"ﾚ" });
	table4.push_back_row({ U"豊　橋　〃", U"8:39", U"ﾚ" });
	table4.push_back_row({ U"三河安城〃", U"8:56", U"ﾚ" });
	table4.push_back_row({ U"名古屋　着", U"9:06", U"8:10" });
	table4.push_back_row({ U"名古屋　発", U"", U"8:12" });

	//
	// table 5
	//
	SimpleTable table5{ { 80, 80, 80, 80 }, {
		.cellHeight = 26.0,
		.borderThickness = 2.0,
		.borderColor = ColorF{ 0.6 },
		.columnHeaderFont = font3,
		.columnHeaderFontSize = 15.0,
		.rowHeaderFont = font3,
		.rowHeaderFontSize = 15.0,
	} };
	table5.push_back_row({ U"", U"今日", U"明日", U"明後日" }, { 0, 0, 0, 0 });
	table5.push_back_row({ U"札幌", U"\U000F0597\U000F19B0\U000F0590", U"\U000F0590/\U000F0597", U"\U000F0590" });
	table5.push_back_row({ U"東京", U"\U000F0599\U000F19B0\U000F0590", U"\U000F0597/\U000F0590", U"\U000F0590\U000F19B0\U000F0597" });
	table5.push_back_row({ U"大阪", U"\U000F0590\U000F19B0\U000F0597", U"\U000F0597", U"\U000F0599/\U000F0590" });
	table5.push_back_row({ U"福岡", U"\U000F0597\U000F19B0\U000F0590", U"\U000F0590\U000F19B0\U000F0599", U"\U000F0599/\U000F0590" });
	table5.push_back_row({ U"沖縄", U"\U000F0590", U"\U000F0590/\U000F0597", U"\U000F0590\U000F19B0\U000F0599" });
	for (size_t y = 1; y < table5.rows(); ++y)
	{
		for (size_t x = 1; x < table5.columns(); ++x)
		{
			const bool isRainy = table5.getItem(y, x).text.includes(U'\U000F0597');
			const bool isSunny = table5.getItem(y, x).text.includes(U'\U000F0599');
			const bool isCloudy = table5.getItem(y, x).text.includes(U'\U000F0590');

			if (isRainy)
			{
				table5.setBackgroundColor(y, x, ColorF{ 0.7, 0.9, 1.0 });
			}
			else if (isSunny)
			{
				table5.setBackgroundColor(y, x, ColorF{ 1.0, 0.9, 0.7 });
			}
			else if (isCloudy)
			{
				table5.setBackgroundColor(y, x, ColorF{ 0.9 });
			}
		}
	}


	Optional<Point> table1ActiveIndex;

	while (System::Update())
	{
		// table1
		{
			if (SimpleGUI::Button(U"行を追加", Vec2{ 740, 40 }, 130))
			{
				table1.push_back_row({ 0, -1, -1 });
			}

			if (SimpleGUI::Button(U"行を削除", Vec2{ 740, 80 }, 130))
			{
				table1.pop_back_row();
			}

			constexpr Vec2 TablePos{ 40,40 };

			if (MouseL.down())
			{
				table1ActiveIndex = table1.cellIndex(TablePos, Cursor::Pos());
			}

			table1.draw(TablePos);

			if (table1ActiveIndex)
			{
				table1.cellRegion(TablePos, *table1ActiveIndex).drawFrame(2, 1, ColorF{ 0.11 });
			}
		}

		// table2
		{
			constexpr Vec2 TablePos{ 64, 372 };
			table2.region(TablePos).stretched(24, 12).rounded(10.0).draw(ColorF{ 0.18, 0.20, 0.35 });
			table2.draw(TablePos);
		}

		// table 3
		{
			constexpr Vec2 TablePos{ 500, 380 };
			table3.region(TablePos).stretched(10, 20).rounded(5).draw();
			table3.draw(TablePos);
		}

		// table 4
		{
			constexpr Vec2 TablePos{ 980, 220 };
			table4.draw(TablePos);
		}

		// table 5
		{
			constexpr Vec2 TablePos{ 914, 40 };
			table5.draw(TablePos);
		}
	}
}
```

