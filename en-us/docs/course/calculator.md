# Building a Calculator App

| | | | |
|:--:|:--:|:--:|:--:|
| **Difficulty** | Beginner | **Time** | 30 minutes~ |

## 1. Set the background color

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/calculator/1.png)

- Use `Scene::SetBackground(color);` to set the background color.

??? note "Code"
	```cpp hl_lines="5-6"
	# include <Siv3D.hpp>

	void Main()
	{
		// Set the background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		while (System::Update())
		{

		}
	}
	```


## 2. Prepare button information

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/calculator/2.png)

- Create a `Button` class that holds a `Rect` representing the button area and a `String` representing the text on the button.
- Set the information for the "1" button as the initial value of the `Button` class variable `button`.

??? note "Code"
	```cpp hl_lines="3-11 18-19"
	# include <Siv3D.hpp>

	/// @brief Button class
	struct Button
	{
		/// @brief Button rectangle
		Rect rect;

		/// @brief Button text
		String text;
	};

	void Main()
	{
		// Set the background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Button information
		Button button{ Rect{ 100, 200, 90, 90 }, U"1" };

		while (System::Update())
		{

		}
	}
	```


## 3. Draw the button background

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/calculator/3.png)

- Access the `.rect` member variable of the `Button` class and draw the button background.

??? note "Code"
	```cpp hl_lines="23-24"
	# include <Siv3D.hpp>

	/// @brief Button class
	struct Button
	{
		/// @brief Button rectangle
		Rect rect;

		/// @brief Button text
		String text;
	};

	void Main()
	{
		// Set the background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Button information
		Button button{ Rect{ 100, 200, 90, 90 }, U"1" };

		while (System::Update())
		{
			// Draw the button background
			button.rect.draw();
		}
	}
	```


## 4. Round the button corners

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/calculator/4.png)

- Use the `Rect` member function `.rounded(corner_radius)` to round the button corners.

??? note "Code"
	```cpp hl_lines="24"
	# include <Siv3D.hpp>

	/// @brief Button class
	struct Button
	{
		/// @brief Button rectangle
		Rect rect;

		/// @brief Button text
		String text;
	};

	void Main()
	{
		// Set the background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Button information
		Button button{ Rect{ 100, 200, 90, 90 }, U"1" };

		while (System::Update())
		{
			// Draw the button background
			button.rect.rounded(8).draw();
		}
	}
	```


## 5. Display text on the button

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/calculator/5.png)

- Prepare a `Font` for displaying text.
- Access the `.text` member variable of the `Button` and draw the button text.

!!! info "New Features"
	- `font(text).drawAt(font_size, position, color);` draws text centered at the specified position.
	- The `.center()` member function of `Rect` returns the coordinates of the rectangle's center.

??? note "Code"
	```cpp hl_lines="18-19 29-30"
	# include <Siv3D.hpp>

	/// @brief Button class
	struct Button
	{
		/// @brief Button rectangle
		Rect rect;

		/// @brief Button text
		String text;
	};

	void Main()
	{
		// Set the background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font for buttons
		const Font font{ FontMethod::MSDF, 40, Typeface::Bold };

		// Button information
		Button button{ Rect{ 100, 200, 90, 90 }, U"1" };

		while (System::Update())
		{
			// Draw the button background
			button.rect.rounded(8).draw();

			// Draw the button text
			font(button.text).drawAt(32, button.rect.center(), ColorF{ 0.1 });
		}
	}
	```


## 6. Change the mouse cursor to a hand over buttons

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/calculator/6.png)

- Use the `Rect` member function `.mouseOver()` to check if the mouse cursor is over the button.
- Use `Cursor::RequestStyle(CursorStyle::Hand);` to change the current mouse cursor style to a hand.

??? note "Code"
	```cpp hl_lines="32-37"
	# include <Siv3D.hpp>

	/// @brief Button class
	struct Button
	{
		/// @brief Button rectangle
		Rect rect;

		/// @brief Button text
		String text;
	};

	void Main()
	{
		// Set the background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font for buttons
		const Font font{ FontMethod::MSDF, 40, Typeface::Bold };

		// Button information
		Button button{ Rect{ 100, 200, 90, 90 }, U"1" };

		while (System::Update())
		{
			// Draw the button background
			button.rect.rounded(8).draw();

			// Draw the button text
			font(button.text).drawAt(32, button.rect.center(), ColorF{ 0.1 });

			// When the mouse is over the button
			if (button.rect.mouseOver())
			{
				// Change the mouse cursor to a hand
				Cursor::RequestStyle(CursorStyle::Hand);
			}
		}
	}
	```


## 7. Input formulas with buttons

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/calculator/7.png)

- Prepare a `String` type variable `expression` to manage the input formula for the calculator.
- When the button is clicked, add the button's text "U\"1\"" to `expression`.
- Use `Print` to display the contents of the input formula briefly.

??? note "Code"
	```cpp hl_lines="24-25 42-47 49-50"
	# include <Siv3D.hpp>

	/// @brief Button class
	struct Button
	{
		/// @brief Button rectangle
		Rect rect;

		/// @brief Button text
		String text;
	};

	void Main()
	{
		// Set the background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font for buttons
		const Font font{ FontMethod::MSDF, 40, Typeface::Bold };

		// Button information
		Button button{ Rect{ 100, 200, 90, 90 }, U"1" };

		// Input expression
		String expression;

		while (System::Update())
		{
			// Draw the button background
			button.rect.rounded(8).draw();

			// Draw the button text
			font(button.text).drawAt(32, button.rect.center(), ColorF{ 0.1 });

			// When the mouse is over the button
			if (button.rect.mouseOver())
			{
				// Change the mouse cursor to a hand
				Cursor::RequestStyle(CursorStyle::Hand);
			}

			// When the button is clicked
			if (button.rect.leftClicked())
			{
				// Add the button text to the expression
				expression += button.text;
			}

			ClearPrint();
			Print << expression; // Display the expression briefly
		}
	}
	```


## 8. Add more buttons

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/calculator/8.png)

- Use the `Array<Button>` array to manage more buttons.
- Write the processing for each button inside `for (const auto& button : buttons) { }`.
- When a button is clicked, update the input expression `expression` according to that button's text.

??? note "Code"
	```cpp hl_lines="21-28 35-73"
	# include <Siv3D.hpp>

	/// @brief Button class
	struct Button
	{
		/// @brief Button rectangle
		Rect rect;

		/// @brief Button text
		String text;
	};

	void Main()
	{
		// Set the background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font for buttons
		const Font font{ FontMethod::MSDF, 40, Typeface::Bold };

		// Array of button information
		Array<Button> buttons;
		buttons << Button{ Rect{ 100, 200, 90, 90 }, U"1" };
		buttons << Button{ Rect{ 200, 200, 90, 90 }, U"2" };
		buttons << Button{ Rect{ 300, 200, 90, 90 }, U"3" };
		buttons << Button{ Rect{ 400, 200, 90, 90 }, U"+" };
		buttons << Button{ Rect{ 400, 300, 90, 90 }, U"=" };
		buttons << Button{ Rect{ 400, 400, 90, 90 }, U"C" };

		// Input expression
		String expression;

		while (System::Update())
		{
			// For each button
			for (const auto& button : buttons)
			{
				// Draw the button background
				button.rect.rounded(8).draw(); 

				// Draw the button text
				font(button.text).drawAt(32, button.rect.center(), ColorF{ 0.1 });

				// When the mouse is over the button
				if (button.rect.mouseOver())
				{
					// Change the mouse cursor to a hand
					Cursor::RequestStyle(CursorStyle::Hand);
				}

				// When the button is clicked
				if (button.rect.leftClicked())
				{
					if (button.text == U"=")
					{
						// ToDo
					}
					else if (button.text == U"+")
					{
						expression += button.text;
					}
					else if (button.text == U"C")
					{
						// Clear the expression
						expression.clear();
					}
					else
					{
						// Add the button text to the expression
						expression += button.text;
					}
				}
			}

			ClearPrint();
			Print << expression; // Display the expression briefly
		}
	}
	```


## 9. Add an expression display area

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/calculator/9.png)

- Add an area to the calculator for displaying expressions.
- Display the expression right-aligned, shifted 10 pixels to the left from the display area.

!!! info "New Features"
	- `font(text).draw(font_size, Arg::rightCenter = position, color);` draws text right-aligned with the specified position as the center of the right edge of the text.
	- The `.rightCenter()` member function of `Rect` returns the coordinates of the center of the rectangle's right edge.

??? note "Code"
	```cpp hl_lines="21-28 84-88"
	# include <Siv3D.hpp>

	/// @brief Button class
	struct Button
	{
		/// @brief Button rectangle
		Rect rect;

		/// @brief Button text
		String text;
	};

	void Main()
	{
		// Set the background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font for buttons
		const Font font{ FontMethod::MSDF, 40, Typeface::Bold };

		// Result display area color
		const ColorF displayColor{ 0.8, 0.9, 0.8 };

		// Result display area text color
		const ColorF displayTextColor{ 0.1 };

		// Result display area rectangle
		const Rect displayRect{ 100, 100, 390, 90 };

		// Array of button information
		Array<Button> buttons;
		buttons << Button{ Rect{ 100, 200, 90, 90 }, U"1" };
		buttons << Button{ Rect{ 200, 200, 90, 90 }, U"2" };
		buttons << Button{ Rect{ 300, 200, 90, 90 }, U"3" };
		buttons << Button{ Rect{ 400, 200, 90, 90 }, U"+" };
		buttons << Button{ Rect{ 400, 300, 90, 90 }, U"=" };
		buttons << Button{ Rect{ 400, 400, 90, 90 }, U"C" };

		// Input expression
		String expression;

		while (System::Update())
		{
			// For each button
			for (const auto& button : buttons)
			{
				// Draw the button background
				button.rect.rounded(8).draw(); 

				// Draw the button text
				font(button.text).drawAt(32, button.rect.center(), ColorF{ 0.1 });

				// When the mouse is over the button
				if (button.rect.mouseOver())
				{
					// Change the mouse cursor to a hand
					Cursor::RequestStyle(CursorStyle::Hand);
				}

				// When the button is clicked
				if (button.rect.leftClicked())
				{
					if (button.text == U"=")
					{
						// ToDo
					}
					else if (button.text == U"+")
					{
						expression += button.text;
					}
					else if (button.text == U"C")
					{
						// Clear the expression
						expression.clear();
					}
					else
					{
						// Add the button text to the expression
						expression += button.text;
					}
				}
			}

			// Draw the result display area
			displayRect.draw(displayColor);

			// Draw the expression in the result display area
			font(expression).draw(36, Arg::rightCenter = (displayRect.rightCenter() + Vec2{ -10, 0 }), displayTextColor);
		}
	}
	```


## 10. Add calculation processing

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/calculator/10.png)

- Implement the `PushEqual` function to calculate the input expression when the "=" button is pressed and make the result the new input expression.
- Implement the `PushPlus` function to add "+" to the input expression when the "+" button is pressed.

!!! info "New Features"
	- The `!` (not) operator inverts a `bool` type value.
	- The `.isEmpty()` member function of a `String` type variable checks if the string is empty.
	- The `.back()` member function of a `String` type variable returns the last character of the string. It causes an error if empty.
	- The `IsDigit(char32 ch)` function checks if the argument character `ch` is a digit.
	- The `Eval(String expression)` function evaluates the expression `expression` and returns the result as a `double` type. Returns `NaN` if the expression is invalid.
		- You can check if it's `NaN` with the `IsNaN(double x)` function.
	- The `Format(double x)` function converts the `double` type value `x` to a `String` and returns it.

??? note "Code"
	```cpp hl_lines="13-57 111 115"
	# include <Siv3D.hpp>

	/// @brief Button class
	struct Button
	{
		/// @brief Button rectangle
		Rect rect;

		/// @brief Button text
		String text;
	};

	/// @brief Returns whether the expression ends with a digit.
	/// @param expression The expression
	/// @return true if it ends with a digit, false otherwise
	bool EndsWithDigit(String expression)
	{
		if (expression.isEmpty())
		{
			return false;
		}

		return IsDigit(expression.back());
	}

	/// @brief Processes when the = button is pressed.
	/// @param expression The current expression
	/// @return The new expression
	String PushEqual(String expression)
	{
		// Do nothing if it doesn't end with a digit
		if (!EndsWithDigit(expression))
		{
			return expression;
		}

		// Evaluate the expression with the expression parser
		double result = Eval(expression);

		// Convert the evaluation result to a string and return it
		return Format(result);
	}

	/// @brief Processes when the + button is pressed.
	/// @param expression The current expression
	/// @return The new expression
	String PushPlus(String expression)
	{
		// Do nothing if it doesn't end with a digit
		if (!EndsWithDigit(expression))
		{
			return expression;
		}

		// Add + to the end of the expression and return it
		return (expression + U"+");
	}

	void Main()
	{
		// Set the background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font for buttons
		const Font font{ FontMethod::MSDF, 40, Typeface::Bold };

		// Result display area color
		const ColorF displayColor{ 0.8, 0.9, 0.8 };

		// Result display area text color
		const ColorF displayTextColor{ 0.1 };

		// Result display area rectangle
		const Rect displayRect{ 100, 100, 390, 90 };

		// Array of button information
		Array<Button> buttons;
		buttons << Button{ Rect{ 100, 200, 90, 90 }, U"1" };
		buttons << Button{ Rect{ 200, 200, 90, 90 }, U"2" };
		buttons << Button{ Rect{ 300, 200, 90, 90 }, U"3" };
		buttons << Button{ Rect{ 400, 200, 90, 90 }, U"+" };
		buttons << Button{ Rect{ 400, 300, 90, 90 }, U"=" };
		buttons << Button{ Rect{ 400, 400, 90, 90 }, U"C" };

		// Input expression
		String expression;

		while (System::Update())
		{
			// For each button
			for (const auto& button : buttons)
			{
				// Draw the button background
				button.rect.rounded(8).draw(); 

				// Draw the button text
				font(button.text).drawAt(32, button.rect.center(), ColorF{ 0.1 });

				// When the mouse is over the button
				if (button.rect.mouseOver())
				{
					// Change the mouse cursor to a hand
					Cursor::RequestStyle(CursorStyle::Hand);
				}

				// When the button is clicked
				if (button.rect.leftClicked())
				{
					if (button.text == U"=")
					{
						expression = PushEqual(expression);
					}
					else if (button.text == U"+")
					{
						expression = PushPlus(expression);
					}
					else if (button.text == U"C")
					{
						// Clear the expression
						expression.clear();
					}
					else
					{
						// Add the button text to the expression
						expression += button.text;
					}
				}
			}

			// Draw the result display area
			displayRect.draw(displayColor);

			// Draw the expression in the result display area
			font(expression).draw(36, Arg::rightCenter = (displayRect.rightCenter() + Vec2{ -10, 0 }), displayTextColor);
		}
	}
	```


## 11. Customize button styles

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/course/calculator/11.png)

- Add `ColorF` type member variables to the `Button` class to represent the button's background color and text color.

??? note "Code"
	```cpp hl_lines="12-16 73-80 93-98 109 112"
	# include <Siv3D.hpp>

	/// @brief Button class
	struct Button
	{
		/// @brief Button rectangle
		Rect rect;

		/// @brief Button text
		String text;

		/// @brief Button color
		ColorF backgroundColor;

		/// @brief Text color
		ColorF textColor;
	};

	/// @brief Returns whether the expression ends with a digit.
	/// @param expression The expression
	/// @return true if it ends with a digit, false otherwise
	bool EndsWithDigit(String expression)
	{
		if (expression.isEmpty())
		{
			return false;
		}

		return IsDigit(expression.back());
	}

	/// @brief Processes when the = button is pressed.
	/// @param expression The current expression
	/// @return The new expression
	String PushEqual(String expression)
	{
		// Do nothing if it doesn't end with a digit
		if (!EndsWithDigit(expression))
		{
			return expression;
		}

		// Evaluate the expression with the expression parser
		double result = Eval(expression);

		// Convert the evaluation result to a string and return it
		return Format(result);
	}

	/// @brief Processes when the + button is pressed.
	/// @param expression The current expression
	/// @return The new expression
	String PushPlus(String expression)
	{
		// Do nothing if it doesn't end with a digit
		if (!EndsWithDigit(expression))
		{
			return expression;
		}

		// Add + to the end of the expression and return it
		return (expression + U"+");
	}

	void Main()
	{
		// Set the background color
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		// Font for buttons
		const Font font{ FontMethod::MSDF, 40, Typeface::Bold };

		// Number button color
		const ColorF numberButtonColor{ 0.9, 0.95, 1.0 };

		// Clear button color
		const ColorF clearButtonColor{ 1.0, 0.8, 0.8 };

		// Button text color
		const ColorF buttonTextColor{ 0.1 };

		// Result display area color
		const ColorF displayColor{ 0.8, 0.9, 0.8 };

		// Result display area text color
		const ColorF displayTextColor{ 0.1 };

		// Result display area rectangle
		const Rect displayRect{ 100, 100, 390, 90 };

		// Array of button information
		Array<Button> buttons;
		buttons << Button{ Rect{ 100, 200, 90, 90 }, U"1", numberButtonColor, buttonTextColor };
		buttons << Button{ Rect{ 200, 200, 90, 90 }, U"2", numberButtonColor, buttonTextColor };
		buttons << Button{ Rect{ 300, 200, 90, 90 }, U"3", numberButtonColor, buttonTextColor };
		buttons << Button{ Rect{ 400, 200, 90, 90 }, U"+", numberButtonColor, buttonTextColor };
		buttons << Button{ Rect{ 400, 300, 90, 90 }, U"=", numberButtonColor, buttonTextColor };
		buttons << Button{ Rect{ 400, 400, 90, 90 }, U"C", clearButtonColor, buttonTextColor };

		// Input expression
		String expression;

		while (System::Update())
		{
			// For each button
			for (const auto& button : buttons)
			{
				// Draw the button background
				button.rect.rounded(8).draw(button.backgroundColor);

				// Draw the button text
				font(button.text).drawAt(32, button.rect.center(), button.textColor);

				// When the mouse is over the button
				if (button.rect.mouseOver())
				{
					// Change the mouse cursor to a hand
					Cursor::RequestStyle(CursorStyle::Hand);
				}

				// When the button is clicked
				if (button.rect.leftClicked())
				{
					if (button.text == U"=")
					{
						expression = PushEqual(expression);
					}
					else if (button.text == U"+")
					{
						expression = PushPlus(expression);
					}
					else if (button.text == U"C")
					{
						// Clear the expression
						expression.clear();
					}
					else
					{
						// Add the button text to the expression
						expression += button.text;
					}
				}
			}

			// Draw the result display area
			displayRect.draw(displayColor);

			// Draw the expression in the result display area
			font(expression).draw(36, Arg::rightCenter = (displayRect.rightCenter() + Vec2{ -10, 0 }), displayTextColor);
		}
	}
	```


## Advanced Features
From here on, try improving the program by thinking for yourself.

### Feature Ideas
- Add the remaining numbers
- Add subtraction, multiplication, and division
	- `Eval()` also supports `-`, `*`, and `/`.
- Enable input of parentheses
- Add a backspace (delete one character) button
	- The `.pop_back()` member function of `String` deletes the last character of the string. It causes an error if the string is empty.
- Enable input of decimal numbers
- Enable number input with the keyboard
- Enable calculation of square roots
	- `Eval()` also supports expressions like `sqrt(25)`.
- Record and reuse previous calculation results
- Copy the expression to the clipboard when clicking the display area
	- `Clipboard::SetText(s);` copies the string `s` to the clipboard.

### Design Ideas
- Arrange the size and layout of buttons
- Change button colors on mouse hover