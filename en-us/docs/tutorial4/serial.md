# 72. Serial Communication
Learn how to send and receive data with external devices using serial communication.

## 72.1 Enumerating Serial Ports
- You can get a list of serial ports recognized by the PC with `System::EnumerateSerialPorts()`
- The result is returned as `Array<SerialPortInfo>` type
- The member variables of `SerialPortInfo` type are as follows:

| Code | Description |
|---|---|
| `String port` | Serial port name |
| `String description` | Serial port description |
| `String hardwareID` | Hardware ID |


```cpp
# include <Siv3D.hpp>

void Main()
{
	for (const auto& info : System::EnumerateSerialPorts())
	{
		Print << U"[{}] {}"_fmt(info.port, info.description);
	}

	while (System::Update())
	{

	}
}
```
```txt title="Output Example"
[COM3] USB Serial Device (COM3)
[COM4] Arduino Uno (COM4)
```


## 72.2 Selecting COM Port to Connect
- You can create a GUI to select the serial port to connect using functions like the following

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/serial/2.png)

```cpp
# include <Siv3D.hpp>

Array<String> GetSerialPortOptions(const Array<SerialPortInfo>& infos)
{
	Array<String> options = infos.map([](const SerialPortInfo& info)
	{
		return U"[{}] {}"_fmt(info.port, info.description);
	});

	options << U"None";
	return options;
}

void Main()
{
	const Array<SerialPortInfo> infos = System::EnumerateSerialPorts();
	const Array<String> options = GetSerialPortOptions(infos);
	size_t index = (options.size() - 1);

	while (System::Update())
	{
		if (SimpleGUI::RadioButtons(index, options, Vec2{ 200, 60 }))
		{

		}
	}
}
```


## 72.3 Serial Class Basics
- You can perform serial communication using the `Serial` class
- Main features of the `Serial` class:
	- Connect to and disconnect from serial ports
	- Send and receive data (byte units, binary data)
	- Buffer management and clearing
	- Check connection status
	- Control handshake signals (RTS, DTR)
	- Get line signal status (CTS, DSR, RI, CD)
- Read operations:
	- Read byte data
	- Read into arrays
	- Read into *trivially copyable* types
- Write operations:
	- Write byte data
	- Write binary data
	- Write *trivially copyable* types
- In the constructor, you can set detailed options as needed:
	- ByteSize (5-8 bits)
	- Parity (None, Odd, Even, Mark, Space)
	- StopBits (One, Two, OnePointFive)
	- FlowControl (None, Software, Hardware)


## 72.4 Serial Communication (1 Byte)
- This is a sample of bidirectional communication where the LED on Arduino is controlled from the PC side, while simultaneously sending data from Arduino to the PC side

!!! info "Troubleshooting Communication Issues"
	- If serial communication is unstable, opening and then closing the serial monitor in Arduino IDE may improve the situation

### Arduino Side Code
- Runs on the Arduino board and handles communication with PC and LED control
- **Initial Setup (setup function)**:
	- Set pin 13 to output mode (to control LED)
	- Start serial communication at 9600 bps baud rate
- **Main Loop (loop function)**:
	- Operates every 250 milliseconds
	- Send value of variable `i` to PC, then increment it
	- Receive and process data sent from PC side:
		- Receive `0`: Turn LED off (LOW)
		- Receive `1`: Turn LED on (HIGH)
		- Receive `2`: Reset counter `i`
	- If no data is received, do nothing and proceed to next loop

```c
void setup()
{
	// Set pin 13 to output mode
	pinMode(13, OUTPUT);

	// Open serial port at 9600 bps
	Serial.begin(9600);
}

uint8_t i = 0; // Value to send to PC (1 byte)

void loop()
{
	// Wait 250 milliseconds
	delay(250);

	// Output 1 byte to serial port
	Serial.write(i);

	++i;

	if (0 < Serial.available())
	{
		// Read data received via serial communication
		const int val = Serial.read();

		if (val == 0)
		{
			// Turn LED off
			digitalWrite(13, LOW);
		}
		else if (val == 1)
		{
			// Turn LED on
			digitalWrite(13, HIGH);
		}
		else if (val == 2)
		{
			// Reset counter
			i = 0;
		}
	}
}
```

### PC Side Code
- Communication with Arduino and GUI program
- **Main Processing**:
	- "Write 0" button: Send 0 to Arduino (turn LED off)
	- "Write 1" button: Send 1 to Arduino (turn LED on)
	- "Write 2" button: Send 2 to Arduino (reset counter)
	- Display data received from Arduino on screen

```cpp
# include <Siv3D.hpp>

Array<String> GetSerialPortOptions(const Array<SerialPortInfo>& infos)
{
	Array<String> options = infos.map([](const SerialPortInfo& info)
	{
		return U"[{}] {}"_fmt(info.port, info.description);
	});

	options << U"None";
	return options;
}

void Main()
{
	const Array<SerialPortInfo> infos = System::EnumerateSerialPorts();
	const Array<String> options = GetSerialPortOptions(infos);
	size_t index = (options.size() - 1);
	Serial serial;

	while (System::Update())
	{
		const bool isOpen = serial.isOpen();

		if (SimpleGUI::Button(U"Write 0", Vec2{ 200, 20 }, 120, isOpen))
		{
			// Write 1 byte of data (0)
			serial.writeByte(0);
		}

		if (SimpleGUI::Button(U"Write 1", Vec2{ 340, 20 }, 120, isOpen))
		{
			// Write 1 byte of data (1)
			serial.writeByte(1);
		}

		if (SimpleGUI::Button(U"Write 2", Vec2{ 480, 20 }, 120, isOpen))
		{
			// Write 1 byte of data (2)
			serial.writeByte(2);
		}

		if (SimpleGUI::RadioButtons(index, options, Vec2{ 200, 60 }))
		{
			ClearPrint();

			if (index == (options.size() - 1))
			{
				// Close serial port
				serial = Serial{};
			}
			else
			{
				Print << U"Open {}"_fmt(infos[index].port);

				// Open serial port
				if (serial.open(infos[index].port))
				{
					Print << U"Succeeded";
				}
				else
				{
					Print << U"Failed";
				}
			}
		}

		if (const size_t available = serial.available())
		{
			// Read and display data received via serial communication
			Print << U"READ: " << serial.readBytes();
		}
	}
}
```


## 72.5 Serial Communication (Multiple Bytes)
- This is a sample for multi-byte communication
- The PC side sends a 16-bit integer, which Arduino receives, adds 1 to, and sends back

### Arduino Side Code

```c
void setup()
{
	Serial.begin(9600);
}

void loop()
{
	delay(250);

	if (2 <= Serial.available())
	{
    	// Read 2 bytes of data
		int low = Serial.read();
		int high = Serial.read();
		
		// Convert to 16-bit integer and add 1
		uint16_t n = (high << 8) | low;
		n += 1;

		// Send result
		Serial.write(lowByte(n));
		Serial.write(highByte(n));
	}
}
```

### PC Side Code

```cpp
# include <Siv3D.hpp>

Array<String> GetSerialPortOptions(const Array<SerialPortInfo>& infos)
{
	Array<String> options = infos.map([](const SerialPortInfo& info)
	{
		return U"[{}] {}"_fmt(info.port, info.description);
	});

	options << U"None";
	return options;
}

void Main()
{
	const Array<SerialPortInfo> infos = System::EnumerateSerialPorts();
	const Array<String> options = GetSerialPortOptions(infos);
	size_t index = (options.size() - 1);
	Serial serial;

	while (System::Update())
	{
		const bool isOpen = serial.isOpen();

		if (SimpleGUI::Button(U"Write uint16", Vec2{ 200, 20 }, 160, isOpen))
		{
			// Write 2 bytes (uint16) of data
			const uint16 n = 12300;
			serial.write(n);
		}

		if (SimpleGUI::RadioButtons(index, options, Vec2{ 200, 60 }))
		{
			ClearPrint();

			if (index == (options.size() - 1))
			{
				// Close serial port
				serial = Serial{};
			}
			else
			{
				Print << U"Open {}"_fmt(infos[index].port);

				// Open serial port
				if (serial.open(infos[index].port))
				{
					Print << U"Succeeded";
				}
				else
				{
					Print << U"Failed";
				}
			}
		}

		if (const size_t available = serial.available();
			2 <= available)
		{
			// Read and display 2 bytes of data
			uint16 n;
			if (serial.read(n))
			{
				Print << U"READ: " << n;
			}
		}
	}
}
```


## 72.6 Serial Communication (String)
- This is a sample that modifies **72.5** to send strings from Arduino

### Arduino Side Code

```c
void setup()
{
	Serial.begin(9600);
}

void loop()
{
	delay(250);

	if (2 <= Serial.available())
	{
    	// Read 2 bytes of data
		int low = Serial.read();
		int high = Serial.read();
		
		// Convert to 16-bit integer and add 1
		uint16_t n = (high << 8) | low;
		n += 1;

		// Send result as string
		Serial.print("The answer is ");
		Serial.println(n);
	}
}
```

### PC Side Code

```cpp
# include <Siv3D.hpp>

Array<String> GetSerialPortOptions(const Array<SerialPortInfo>& infos)
{
	Array<String> options = infos.map([](const SerialPortInfo& info)
	{
		return U"[{}] {}"_fmt(info.port, info.description);
	});

	options << U"None";
	return options;
}

void Main()
{
	const Array<SerialPortInfo> infos = System::EnumerateSerialPorts();
	const Array<String> options = GetSerialPortOptions(infos);
	size_t index = (options.size() - 1);
	Serial serial;

	std::string buffer;

	while (System::Update())
	{
		const bool isOpen = serial.isOpen();

		if (SimpleGUI::Button(U"Write uint16", Vec2{ 200, 20 }, 160, isOpen))
		{
			// Write 2 bytes (uint16) of data
			const uint16 n = 12300;
			serial.write(n);
		}

		if (SimpleGUI::RadioButtons(index, options, Vec2{ 200, 60 }))
		{
			ClearPrint();

			if (index == (options.size() - 1))
			{
				// Close serial port
				serial = Serial{};
			}
			else
			{
				Print << U"Open {}"_fmt(infos[index].port);

				// Open serial port
				if (serial.open(infos[index].port))
				{
					Print << U"Succeeded";
				}
				else
				{
					Print << U"Failed";
				}
			}
		}

		if (const size_t available = serial.available())
		{
			const Array<uint8> bytes = serial.readBytes();

			for (const auto& ch : bytes)
			{
				if (ch == '\r') // Ignore CR
				{
					continue;
				}
				else if (ch == '\n') // End of line
				{
					// Display the string read for one line
					Print << Unicode::FromUTF8(buffer);
					buffer.clear();
				}
				else
				{
					buffer.push_back(ch);
				}
			}
		}
	}
}
```