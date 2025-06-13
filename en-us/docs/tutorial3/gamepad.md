# 59. Gamepad
Learn how to handle gamepad input.

## 59.1 XInput Compatible Controllers
- XInput compatible controllers connected to Windows PC can be accessed through `XInput`
- Up to 4 controllers can be handled simultaneously
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/gamepad/1.png)

```cpp
# include <Siv3D.hpp>

Polygon MakeGamePadPolygon()
{
	Polygon polygon = Ellipse{ 400, 480, 300, 440 }.asPolygon(64);
	polygon = Geometry2D::Subtract(polygon, Ellipse{ 400, 40, 220, 120 }.asPolygon(48)).front();
	polygon = Geometry2D::Subtract(polygon, Circle{ 400, 660, 240 }.asPolygon(48)).front();
	polygon = Geometry2D::Subtract(polygon, Rect{ 0, 540, 800, 60 }.asPolygon()).front();
	return polygon;
}

void Main()
{
	constexpr ColorF backgroundColor{ 0.6, 0.8, 0.7 };
	Scene::SetBackground(backgroundColor);

	constexpr Ellipse buttonLB{ 210, 150, 50, 24 };
	constexpr Ellipse buttonRB{ 590, 150, 50, 24 };
	const Polygon gamepadPolygon = MakeGamePadPolygon();
	constexpr Circle logo{ 400, 250, 25 };
	constexpr RectF leftTrigger{ 210, 16, 40, 100 };
	constexpr RectF rightTrigger{ 550, 16, 40, 100 };
	constexpr Circle leftThumb{ 230, 250, 35 };
	constexpr Circle rightThumb{ 480, 350, 35 };
	constexpr Circle dPad{ 320, 350, 40 };
	constexpr Circle buttonA{ 570, 300, 20 };
	constexpr Circle buttonB{ 620, 250, 20 };
	constexpr Circle buttonX{ 520, 250, 20 };
	constexpr Circle buttonY{ 570, 200, 20 };
	constexpr Circle buttonView{ 330, 250, 15 };
	constexpr Circle buttonMenu{ 470, 250, 15 };

	// Player index (0 - 3)
	size_t playerIndex = 0;
	const Array<String> options = Range(1, 4).map([](int32 i) {return U"{}P"_fmt(i); });

	// Whether to enable dead zone
	bool enableDeadZone = false;

	// Vibration (0.0 - 1.0)
	XInputVibration vibration;

	while (System::Update())
	{
		// Get XInput controller for specified player index
		auto controller = XInput(playerIndex);

		// Dead zone
		if (enableDeadZone)
		{
			// Set default values for each
			controller.setLeftTriggerDeadZone();
			controller.setRightTriggerDeadZone();
			controller.setLeftThumbDeadZone();
			controller.setRightThumbDeadZone();
		}
		else
		{
			// Disable dead zone
			controller.setLeftTriggerDeadZone(DeadZone{});
			controller.setRightTriggerDeadZone(DeadZone{});
			controller.setLeftThumbDeadZone(DeadZone{});
			controller.setRightThumbDeadZone(DeadZone{});
		}

		// Vibration
		controller.setVibration(vibration);

		// L button, R button
		{
			buttonLB.draw(ColorF{ controller.buttonLB.pressed() ? 1.0 : 0.7 });
			buttonRB.draw(ColorF{ controller.buttonRB.pressed() ? 1.0 : 0.7 });
		}

		// Main body
		gamepadPolygon.draw(ColorF{ 0.9 });

		// Xbox button
		{
			if (controller.isConnected())
			{
				Circle{ logo.center, 32 }
				.drawPie((-0.5_pi + 0.5_pi * controller.playerIndex), 0.5_pi, ColorF{ 0.6, 0.9, 0.3 });
			}

			logo.draw(ColorF{ 0.6 });
		}

		// Left trigger
		{
			leftTrigger.draw(ColorF{ 1.0, 0.25 });
			leftTrigger.stretched((controller.leftTrigger - 1.0) * leftTrigger.h, 0, 0, 0).draw();
		}

		// Right trigger
		{
			rightTrigger.draw(ColorF{ 1.0, 0.25 });
			rightTrigger.stretched((controller.rightTrigger - 1.0) * rightTrigger.h, 0, 0, 0).draw();
		}

		// Left stick
		{
			leftThumb.draw(ColorF{ controller.buttonLThumb.pressed() ? 0.85 : 0.5 });
			Circle{ leftThumb.center + 25 * Vec2{ controller.leftThumbX, -controller.leftThumbY }, 20 }.draw();
		}

		// Right stick
		{
			rightThumb.draw(ColorF{ controller.buttonRThumb.pressed() ? 0.85 : 0.5 });
			Circle{ rightThumb.center + 25 * Vec2{ controller.rightThumbX, -controller.rightThumbY }, 20 }.draw();
		}

		// Direction pad
		{
			dPad.draw(ColorF{ 0.75 });
			Shape2D::Plus(dPad.r * 0.9, 25, dPad.center).draw(ColorF{ 0.5 });

			const Vec2 direction{
				controller.buttonRight.pressed() - controller.buttonLeft.pressed(),
				controller.buttonDown.pressed() - controller.buttonUp.pressed() };

			if (!direction.isZero())
			{
				Circle{ dPad.center + direction.withLength(25), 15 }.draw();
			}
		}

		// A, B, X, Y buttons
		{
			buttonA.draw(ColorF{ 0.0, 1.0, 0.3, controller.buttonA.pressed() ? 1.0 : 0.3 });
			buttonB.draw(ColorF{ 1.0, 0.0, 0.3, controller.buttonB.pressed() ? 1.0 : 0.3 });
			buttonX.draw(ColorF{ 0.0, 0.3, 1.0, controller.buttonX.pressed() ? 1.0 : 0.3 });
			buttonY.draw(ColorF{ 1.0, 0.5, 0.0, controller.buttonY.pressed() ? 1.0 : 0.3 });
		}

		// View (Back), Menu (Start) buttons 
		{
			buttonView.draw(ColorF(controller.buttonView.pressed() ? 1.0 : 0.7));
			buttonMenu.draw(ColorF(controller.buttonMenu.pressed() ? 1.0 : 0.7));
		}

		SimpleGUI::RadioButtons(playerIndex, options, Vec2{ 20, 20 });
		SimpleGUI::CheckBox(enableDeadZone, U"DeadZone", Vec2{ 320, 20 });
		SimpleGUI::Slider(U"leftMotor", vibration.leftMotor, Vec2{ 280, 420 }, 120, 120);
		SimpleGUI::Slider(U"rightMotor", vibration.rightMotor, Vec2{ 280, 460 }, 120, 120);
	}
}
```


## 59.2 Joy-Con
- Information about Nintendo Switch Joy-Con connected via Bluetooth to PC can be obtained through `JoyConL` or `JoyConR`
- May not work properly on macOS

<video src="https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/gamepad/2.mp4?raw=true" autoplay loop muted playsinline></video>

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.9 });
	Window::Resize(1280, 720);
	Effect effect;

	Vec2 left{ 640 - 100, 100 }, right{ 640 + 100, 100 };
	double angle = 0_deg;
	double scale = 400.0;
	bool covered = true;

	while (System::Update())
	{
		Circle{ Vec2{ 640 - 300, 450 }, scale / 2 }.drawFrame(scale * 0.1);
		Circle{ Vec2{ 640 + 300, 450 }, scale / 2 }.drawFrame(scale * 0.1);

		// Get Joy-Con (L)
		if (const auto joy = JoyConL(0))
		{
			joy.drawAt(Vec2{ 640 - 300, 450 }, scale, -90_deg - angle, covered);

			if (auto d = joy.povD8())
			{
				left += Circular{ 4, *d * 45_deg };
			}

			if (joy.button2.down())
			{
				effect.add([center = left](double t) {
					Circle{ center, 20 + t * 200 }.drawFrame(10, 0, ColorF{ 1.0, (1.0 - t) });
					return t < 1.0;
					});
			}
		}

		// Get Joy-Con (R)
		if (const auto joy = JoyConR(0))
		{
			joy.drawAt(Vec2{ 640 + 300, 450 }, scale, 90_deg + angle, covered);

			if (auto d = joy.povD8())
			{
				right += Circular{ 4, *d * 45_deg };
			}

			if (joy.button2.down())
			{
				effect.add([center = right](double t) {
					Circle{ center, 20 + t * 200 }.drawFrame(10, 0, ColorF{ 1.0, (1.0 - t) });
					return t < 1.0;
					});
			}
		}

		Circle{ left, 30 }.draw(ColorF{ 0.0, 0.75, 0.9 });
		Circle{ right, 30 }.draw(ColorF{ 1.0, 0.4, 0.3 });
		effect.update();

		SimpleGUI::Slider(U"Rotation: ", angle, -180_deg, 180_deg, Vec2{ 20, 20 }, 120, 200);
		SimpleGUI::Slider(U"Scale: ", scale, 100.0, 600.0, Vec2{ 20, 60 }, 120, 200);
		SimpleGUI::CheckBox(covered, U"Covered", Vec2{ 20, 100 });
	}
}
```


## 59.3 Handling Pro Controller Input
- Information about Nintendo Switch Pro Controller connected via Bluetooth to PC can be obtained through `ProController`
- May not work properly on macOS
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/gamepad/3.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	while (System::Update())
	{
		ClearPrint();

		// Get Pro Controller
		if (const auto pro = ProController(0))
		{
			// Display button states
			Print << U"A: " << pro.buttonA.pressed();
			Print << U"B: " << pro.buttonB.pressed();
			Print << U"X: " << pro.buttonX.pressed();
			Print << U"Y: " << pro.buttonY.pressed();
			Print << U"L: " << pro.buttonL.pressed();
			Print << U"R: " << pro.buttonR.pressed();
			Print << U"ZL: " << pro.buttonZL.pressed();
			Print << U"ZR: " << pro.buttonZR.pressed();
			Print << U"-: " << pro.buttonMinus.pressed();
			Print << U"+: " << pro.buttonPlus.pressed();
			Print << U"LS: " << pro.buttonLStick.pressed();
			Print << U"RS: " << pro.buttonRStick.pressed();
			Print << U"Screenshot: " << pro.buttonScreenshot.pressed();
			Print << U"Home: " << pro.buttonHome.pressed();
			Print << U"LStick: " << pro.LStick();
			Print << U"RStick: " << pro.RStick();
			Print << U"POV: " << pro.povD8();
		}
		else
		{
			Print << U"No Pro Controller found";
		}
	}
}
```


## 59.4 Handling Gamepad Input
- `Gamepad` is a generic class that can obtain information from any type of gamepad
- Up to 16 controllers can be handled simultaneously
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/gamepad/4.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::Resize(800, 800);

	const Array<String> indices = Range(0, (Gamepad.MaxPlayerCount - 1)).map(Format);

	// Gamepad player index
	size_t playerIndex = 0;

	while (System::Update())
	{
		ClearPrint();

		if (const auto gamepad = Gamepad(playerIndex)) // If connected
		{
			const auto& info = gamepad.getInfo();

			Print << U"{} (VID: {}, PID: {})"_fmt(info.name, info.vendorID, info.productID);

			for (auto&& [i, button] : Indexed(gamepad.buttons))
			{
				Print << U"button{}: {}"_fmt(i, button.pressed());
			}

			for (auto&& [i, axe] : Indexed(gamepad.axes))
			{
				Print << U"axe{}: {}"_fmt(i, axe);
			}

			Print << U"POV: " << gamepad.povD8();
		}

		SimpleGUI::RadioButtons(playerIndex, indices, Vec2{ 500, 20 });
	}
}
```


## 59.5 Enumerating Connected Gamepads
- A list of gamepads connected to the PC can be obtained with `System::EnumerateGamepads()`
- The result is returned as `Array<GamepadInfo>` type
- The member variables of `GamepadInfo` type are as follows:

| Code | Description |
|--|--|
| `.playerIndex` | Player index used by `Gamepad` |
| `.vendorID` | Vendor ID |
| `.productID` | Product ID |
| `.name` | Gamepad name |

```cpp
# include <Siv3D.hpp>

void Main()
{
	for (const auto& info : System::EnumerateGamepads())
	{
		Print << U"[{}] {} ({:#x} {:#x})"_fmt(info.playerIndex, info.name, info.vendorID, info.productID);
	}

	while (System::Update())
	{

	}
}
```
```txt title="Example output"
[0] Controller (XBOX 360 For Windows) (0x45e 0x28e)
[1] Wireless Gamepad (0x57e 0x2006)
[2] Wireless Gamepad (0x57e 0x2007)
[3] Wireless Controller (0x54c 0x9cc)
[4] Wireless Gamepad (0x57e 0x2009)
```


## 59.6 Integration with Input Type
- The `buttons` elements of `Gamepad` and the various buttons of XInput and JoyCon are all `Input` types
- They can also be integrated into the key configuration system in **Tutorial 42**
- The operations in the following sample code are as follows:
	- Draw a circle if any of the ← key, left mouse button, or XInput A button are pressed
	- Draw a square if any of the → key, right mouse button, or XInput B button are pressed
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/gamepad/6.png)

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Operations to display circle
	const InputGroup input1 = (KeyLeft | MouseL | XInput(0).buttonA);

	// Operations to display square
	const InputGroup input2 = (KeyRight | MouseR | XInput(0).buttonB);

	while (System::Update())
	{
		if (input1.pressed())
		{
			Circle{ 200, 300, 100 }.draw(ColorF{ 0.1 });
		}

		if (input2.pressed())
		{
			RectF{ Arg::center(600, 300), 200 }.draw(ColorF{ 0.1 });
		}
	}
}
```