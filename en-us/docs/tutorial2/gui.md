# 38. GUI
Learn how to use GUI features such as buttons, sliders, and text boxes.

## 38.1 SimpleGUI Overview
- Siv3D's SimpleGUI provides the functionality to implement common GUI widgets with simple code
- The supported GUI widgets are as follows:
	- **38.2** Button
	- **38.4** Slider
	- **38.5** Checkbox
	- **38.6** Radio Button
	- **38.7** Text Box
	- **38.8** Text Area
	- **38.9** Color Picker
	- **38.10** List Box
	- **38.12** Menu Bar
	- **38.13** Table
- SimpleGUI prioritizes code simplicity, so there are limitations in design flexibility such as colors and fonts
- Future Siv3D versions plan to provide more advanced and complex GUI functionality as a higher-level version of SimpleGUI


## 38.2 Button
- Buttons use the `SimpleGUI::Button()` function
- You can set text, position, width, state, etc.

```cpp
bool SimpleGUI::Button(StringView label, const Vec2& pos, const Optional<double>& width = unspecified, bool enabled = true);
```

- Arguments:
	- `label` : Text to display on the button
	- `pos` : Top-left coordinates of the button
	- `width` : Button width (`unspecified` adjusts to text width)
	- `enabled` : Whether the button is enabled
- Return value:
	- Returns `true` if the button is pressed, `false` otherwise

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

		// Specify button width as 200px
		if (SimpleGUI::Button(U"White", Vec2{ 100, 300 }, 200))
		{
			Scene::SetBackground(ColorF{ 0.9 });
		}

		// Specify button width as 200px
		if (SimpleGUI::Button(U"Black", Vec2{ 100, 350 }, 200))
		{
			Scene::SetBackground(ColorF{ 0.1 });
		}

		// Disable the button
		if (SimpleGUI::Button(U"Gray", Vec2{ 100, 400 }, 200, false))
		{
			Scene::SetBackground(ColorF{ 0.5 });
		}

		// Disable the button and adjust width to text
		if (SimpleGUI::Button(U"Yellow", Vec2{ 100, 450 }, unspecified, false))
		{
			Scene::SetBackground(ColorF{ 0.8, 0.8, 0.1 });
		}
	}
}
```


## 38.3 Slider
- Sliders use `SimpleGUI::Slider()` (horizontal) or `SimpleGUI::VerticalSlider()` (vertical)
- You can set text, position, width, value range, etc.
	
```cpp
bool SimpleGUI::Slider(double& value, double min, double max, const Vec2& pos, double sliderWidth = 120.0, bool enabled = true);
bool SimpleGUI::Slider(StringView label, double& value, const Vec2& pos, double labelWidth = 80.0, double sliderWidth = 120.0, bool enabled = true);
bool SimpleGUI::Slider(StringView label, double& value, double min, double max, const Vec2& pos, double labelWidth = 80.0, double sliderWidth = 120.0, bool enabled = true);
bool SimpleGUI::VerticalSlider(double& value, const Vec2& pos, double sliderHeight = 120.0, bool enabled = true);
bool SimpleGUI::VerticalSlider(double& value, double min, double max, const Vec2& pos, double sliderHeight = 120.0, bool enabled = true);
```

- Arguments:
	- `value` : Slider value
	- `min` : Minimum slider value
	- `max` : Maximum slider value
	- `label` : Slider label
	- `pos` : Top-left coordinates of the slider
	- `labelWidth` : Label width
	- `sliderWidth` : Slider width
	- `sliderHeight` : Vertical slider height
	- `enabled` : Whether the slider is enabled
- For those without min/max specification, the value range is 0.0 to 1.0
- Return value:
	- Returns `true` if the slider value changed, `false` otherwise

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

		// Display slider value. Label width 100px, slider width 200px
		SimpleGUI::Slider(U"R {:.2f}"_fmt(color3.r), color3.r, Vec2{ 100, 360 }, 100, 200);
		SimpleGUI::Slider(U"G {:.2f}"_fmt(color3.g), color3.g, Vec2{ 100, 400 }, 100, 200);
		SimpleGUI::Slider(U"B {:.2f}"_fmt(color3.b), color3.b, Vec2{ 100, 440 }, 100, 200);
		Circle{ 50, 420, 30 }.draw(color3);

		// Value range 0.0 to 10.0
		SimpleGUI::Slider(U"{:.2f}"_fmt(value1), value1, 0.0, 10.0, Vec2{ 500, 40 }, 60, 150);

		// Disable the slider
		SimpleGUI::Slider(U"{:.2f}"_fmt(value2), value2, 0.0, 10.0, Vec2{ 500, 100 }, 60, 150, false);

		// Vertical sliders
		SimpleGUI::VerticalSlider(value3, 0.0, 10.0, Vec2{ 500, 160 }, 200);
		SimpleGUI::VerticalSlider(value4, 0.0, 10.0, Vec2{ 560, 160 }, 200, false);
	}
}
```


## 38.4 Using Icons
- The font used by SimpleGUI is `Typeface::CJK_Regular_JP` with `Typeface::Icon_MaterialDesign` added as fallback
- You can display icons in SimpleGUI text by including icon code points like `\U000F0493` in strings
- Icon code points can be found at [Material Design Icons :material-open-in-new:](https://pictogrammers.com/library/mdi/){:target="_blank"}
- SimpleGUI's font can be obtained with `SimpleGUI::GetFont()` and used for purposes other than SimpleGUI

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
		SimpleGUI::Button(U"\U000F0493 Settings", Vec2{ 20, 40 }, 160);
		SimpleGUI::Button(U"\U000F1398 Pause", Vec2{ 20, 80 }, 160);
		SimpleGUI::Button(U"\U000F0E1E OK", Vec2{ 20, 120 }, 160);
		SimpleGUI::Button(U"\U000F0193 Save", Vec2{ 20, 160 }, 160);

		// Undo / Redo
		SimpleGUI::Button(U"\U000F054C", Vec2{ 200, 40 }, 40);
		SimpleGUI::Button(U"\U000F044E", Vec2{ 250, 40 }, 40);

		// Volume control
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


## 38.5 Checkbox
- Checkboxes use the `SimpleGUI::CheckBox()` function
- You can set text, position, width, state, etc.

```cpp
bool SimpleGUI::CheckBox(bool& checked, StringView label, const Vec2& pos, const Optional<double>& width = unspecified, bool enabled = true);
```

- Arguments:
	- `checked` : Checkbox state
	- `label` : Checkbox label
	- `pos` : Top-left coordinates of the checkbox
	- `width` : Checkbox width (`unspecified` adjusts to text width)
	- `enabled` : Whether the checkbox is enabled
- Return value:
	- Returns `true` if the checkbox state changed, `false` otherwise

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

		// Width 200px
		SimpleGUI::CheckBox(checked3, U"Label 3", Vec2{ 100, 180 }, 200);

		// Disabled
		SimpleGUI::CheckBox(checked4, U"Label 4", Vec2{ 100, 220 }, 200, false);

		// Width adjusts to text
		SimpleGUI::CheckBox(checked5, U"Label 5", Vec2{ 100, 260 }, unspecified, false);
	}
}
```


## 38.6 Radio Button
- Radio buttons use `SimpleGUI::RadioButtons()` (vertical) or `SimpleGUI::HorizontalRadioButtons()` (horizontal)
- You can set text, position, width, state, etc.

```cpp
bool SimpleGUI::RadioButtons(size_t& index, const Array<String>& options, const Vec2& pos, const Optional<double>& width = unspecified, bool enabled = true);
bool SimpleGUI::HorizontalRadioButtons(size_t& index, const Array<String>& options, const Vec2& pos, const Optional<double>& itemWidth = unspecified, bool enabled = true);
```

- Arguments:
	- `index` : Index of the selected item
	- `options` : Array of options
	- `pos` : Top-left coordinates of the radio buttons
	- `width` : Radio button width (`unspecified` adjusts to text width)
	- `itemWidth` : Horizontal radio button item width (`unspecified` adjusts to text width)
	- `enabled` : Whether the radio buttons are enabled
- Return value:
	- Returns `true` if the radio button selection changed, `false` otherwise

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

		// Specify options with Array<String>
		if (SimpleGUI::RadioButtons(index1, options, Vec2{ 100, 180 }))
		{
			Scene::SetBackground(colors[index1]);
		}

		// Width 200px
		SimpleGUI::RadioButtons(index2, { U"A", U"B" }, Vec2{ 400, 40 }, 200);

		// Disabled
		SimpleGUI::RadioButtons(index3, { U"A", U"B" }, Vec2{ 400, 140 }, 200, false);

		// Width adjusts to text
		SimpleGUI::RadioButtons(index4, { U"A", U"B" }, Vec2{ 400, 240 }, unspecified, false);

		// Horizontal radio buttons
		SimpleGUI::HorizontalRadioButtons(index5, { U"Apple", U"Bird", U"Cat", U"Dog" }, Vec2{ 100, 400 });
	}
}
```


## 38.7 Text Box
- Single-line text boxes use the `TextEditState` class and `SimpleGUI::TextBox()` function
- You can set text box position, width, character limit, state, etc.

```cpp
bool SimpleGUI::TextBox(TextEditState& text, const Vec2& pos, double width = 200.0, const Optional<size_t>& maxChars = unspecified, bool enabled = true);
```

- Arguments:
	- `text` : `TextEditState` object
	- `pos` : Top-left coordinates of the text box
	- `width` : Text box width
	- `maxChars` : Maximum number of input characters (`unspecified` for no limit)
	- `enabled` : Whether the text box is enabled
- Return value:
	- Returns `true` if text changed, `false` otherwise

### 38.7.1 Text Box Basics
- The initial string for the text box is specified in the `TextEditState` constructor
- Text box contents can be obtained with the `.text` member variable of `TextEditState`
- Whether the text box is active can be obtained with the `.active` member variable of `TextEditState`
- To clear text box contents, use the `.clear()` member function of `TextEditState`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/7-1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	TextEditState te0;
	TextEditState te1{ U"Siv3D" }; // Set default text
	TextEditState te2;
	TextEditState te3;

	while (System::Update())
	{
		ClearPrint();
		Print << te0.active; // Whether active
		Print << te0.text; // Input text (String)

		SimpleGUI::TextBox(te0, Vec2{ 100, 140 });

		SimpleGUI::TextBox(te1, Vec2{ 100, 200 });

		if (SimpleGUI::Button(U"Clear", Vec2{ 320, 200 }))
		{
			// Clear text
			te1.clear();
		}

		// Width 100px, limit to 4 characters
		SimpleGUI::TextBox(te2, Vec2{ 100, 260 }, 100, 4);

		// Disabled
		SimpleGUI::TextBox(te3, Vec2{ 100, 320 }, 100, 4, false);
	}
}
```

- Advanced text editing features like string range selection are not currently implemented in SimpleGUI
- Text boxes display overflow outside the box when character count exceeds the text box width
	- To avoid this behavior, consider setting a character limit or using **38.8** Text Area


### 38.7.2 Moving Focus Between Text Boxes
- In `SimpleGUI::TextBox()`, when a text box is active and Enter, Tab is pressed, or an unrelated area is clicked, that text box becomes inactive
- Whether a text box was deactivated by Tab can be detected by checking if the `TextEditState`'s `.tabKey` member variable is `true`
- A system to detect Tab key deactivation and move focus to previous/next text boxes can be implemented as follows:

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/7-2.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	std::array<TextEditState, 3> textEditStates;

	Optional<int32> nextTextBox;

	while (System::Update())
	{
		// Activate next text box one frame later to avoid Tab key press
		// activating and immediately deactivating the next text box
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

			// Deactivated by Tab key
			if (previous && (state.active == false) && state.tabKey)
			{
				if (KeyShift.pressed()) // Go to previous text box if Shift is pressed
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


## 38.8 Text Area
- For multi-line text input, use the `TextAreaEditState` class and `SimpleGUI::TextArea()` function
- You can set text area position, width and height, character limit, state, etc.

```cpp
bool SimpleGUI::TextArea(TextAreaEditState& text, const Vec2& pos, const SizeF& size = SizeF{ 200, 100 }, size_t maxChars = PreferredTextAreaMaxChars, bool enabled = true);
```

- Arguments:
	- `text` : `TextAreaEditState` object
	- `pos` : Top-left coordinates of the text area
	- `size` : Text area width and height
	- `maxChars` : Maximum number of input characters
	- `enabled` : Whether the text area is enabled
- Return value:
	- Returns `true` if text changed, `false` otherwise

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

		// Text content can be accessed with textAreaEditState.text
	}
}
```

- Advanced text editing features like string range selection are not currently implemented in SimpleGUI


## 38.9 Color Picker
- Color pickers use the `SimpleGUI::ColorPicker()` function
- You can set color picker position, state, etc.
- Colors are managed with `HSV` type variables
- Alpha component cannot be manipulated

```cpp
bool SimpleGUI::ColorPicker(HSV& hsv, const Vec2& pos, bool enabled = true);
```

- Arguments:
	- `hsv` : `HSV` type variable
	- `pos` : Top-left coordinates of the color picker
	- `enabled` : Whether the color picker is enabled
- Return value:
	- Returns `true` if color changed, `false` otherwise

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

		// Disabled color picker
		SimpleGUI::ColorPicker(color1, Vec2{ 300, 100 }, false);
		Rect{ 300, 300, 100 }.draw(color1);
	}
}
```


## 38.10 List Box
- List boxes use the `ListBoxState` class and `SimpleGUI::ListBox()` function
- You can set list box position, width and height, state, etc.

```cpp
bool SimpleGUI::ListBox(ListBoxState& state, const Vec2& pos, double width = 160.0, double height = 156.0, bool enabled = true);
```

- Arguments:
	- `state` : `ListBoxState` object
	- `pos` : Top-left coordinates of the list box
	- `width` : List box width
	- `height` : List box height
	- `enabled` : Whether the list box is enabled
- Return value:
	- Returns `true` if list box selection changed, `false` otherwise

---

- List box state is managed by a `ListBoxState` type object
- The list of options is stored in the `Array<String>` type member variable `.items` of `ListBoxState`
- The index of the selected item is stored in the `Optional<size_t>` type member variable `.selectedItemIndex` of `ListBoxState`

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


## 38.11 Headline
- To add headlines to widgets, use the `SimpleGUI::Headline()` function
- You can set headline position, width, state, etc.
- Headline height is 40 pixels

```cpp
void SimpleGUI::Headline(StringView text, const Vec2& pos, const Optional<double>& width = unspecified, bool enabled = true);
```

- Arguments:
	- `text` : Headline text
	- `pos` : Top-left coordinates of the headline
	- `width` : Headline width (`unspecified` adjusts to text width)
	- `enabled` : Whether the headline is enabled

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


## 38.12 Menu Bar
- You can create a simple menu bar using the `SimpleMenuBar` class
- Menu bar items are set with `Array<std::pair<String, Array<String>>>`
	- `String` is the menu title, `Array<String>` is the array of item names contained in the menu

### 38.12.1 Menu Bar Basics
- The `SimpleMenuBar` class has a `.update()` member function for state updates and a `.draw()` member function for drawing, both must be called every frame
	- The return value of `.update()` is `Optional<MenuBarItemIndex>` type. If a menu item is selected, it returns that item index; if not selected, it returns an invalid value
	- `.draw()` draws the menu bar
- Item index is represented as `MenuBarItemIndex{ menu index, menu item index }`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/12-1.png)

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
			// If "Game > Exit" is pressed
			if (item == MenuBarItemIndex{ 0, 2 })
			{
				System::Exit();
			}

			// If "Help > License" is pressed
			if (item == MenuBarItemIndex{ 1, 2 })
			{
				LicenseManager::ShowInBrowser();
			}
		}

		menuBar.draw();
	}
}
```


### 38.12.2 Checkable Menu Items
- You can turn menu item check states on/off using the `.setItemChecked()` member function of `SimpleMenuBar`
- Pass `MenuBarItemIndex` and `bool` to `.setItemChecked()`
	- If `true`, that item becomes checked; if `false`, the check state is removed
- Whether an item is checked can be obtained with the member function `.getItemChecked(MenuBarItemIndex)`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/12-2.png)

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
			// If "Game > Exit" is pressed
			if (item == MenuBarItemIndex{ 0, 2 })
			{
				System::Exit();
			}

			// If "Settings > Option" is pressed
			if (item->menuIndex == 1)
			{
				// Toggle check state
				menuBar.setItemChecked(*item, (not menuBar.getItemChecked(*item)));
			}

			// If "Help > License" is pressed
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


### 38.12.3 Changing Menu Bar Colors
- You can customize menu bar colors using the `SimpleMenuBar::ColorPalette` class

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/12-3.png)

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


## 38.13 Table
- You can easily create tables with items arranged vertically and horizontally using the `SimpleTable` class

### 38.13.1 Table Basics
- Specify the width of each column in the constructor
- Add row contents as `Array<String>` with `push_back_row()`
- You can also specify alignment as `Array<int32>` with the second argument
	- `-1` is left-aligned, `0` is center-aligned, `1` is right-aligned
- Tables are drawn with the member function `.draw(pos)`

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/13-1.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Create table with column widths | 160 | 100 | 100 |
	SimpleTable table{ { 160, 100, 100 } };

	// Add rows
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

### 38.13.2 Table Sample (1)
- Sample of a table with addable/removable rows

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/13-2.png)

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

### 38.13.3 Table Sample (2)
- Sample of a table that changes row background color on mouse over

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/13-3.png)

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

### 38.13.4 Table Sample (3)
- December 2025 calendar sample

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/13-4.png)

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

### 38.13.5 Table Sample (4)
- Timetable sample

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/13-5.png)

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

### 38.13.6 Table Sample (5)
- Weather forecast sample

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/13-6.png)

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


## 38.14 IME Candidate Window
- On Windows 11, you can display the IME candidate window using `SimpleGUI::IMECandidateWindow(pos)`
- On platforms other than Windows 11, nothing happens

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/gui/14.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	TextEditState state;

	while (System::Update())
	{
		SimpleGUI::TextBox(state, Vec2{ 40, 40 }, 600);

		if (state.active)
		{
			SimpleGUI::IMECandidateWindow(Vec2{ 40, 80 });
		}
	}
}
```