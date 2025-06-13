# 73. OSC Communication
Learn how to exchange data with external software and devices using OSC (Open Sound Control) communication.

## 73.1 Basic OSC Communication
- `OSCSender` sends OSC messages to a specified port
- `OSCReceiver` receives OSC messages on a specified port
- The following sample code sends and receives OSC messages within the same program for testing purposes

```cpp
# include <Siv3D.hpp>

void Main()
{
	const int16 port = 9000;
	OSCSender sender{ IPv4Address::Localhost(), port };
	OSCReceiver receiver{ IPv4Address::Localhost(), port };

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Send", Vec2{ 600, 20 }))
		{
			OSCMessage messsage;
			messsage
				.beginBundle()
				.beginMessage(U"/siv3d")
				.addInt32(12345)
				.addString(U"Hello")
				.addBool(true)
				.endMessage()
				.endBundle();

			sender.send(messsage);
		}

		while (receiver.hasMessages())
		{
			const auto message = receiver.pop();

			Print << message.addressPattern;

			for (const auto& argument : message.arguments)
			{
				switch (argument.tag)
				{
				case OSCTypeTag::False:
				case OSCTypeTag::True:
					Print << argument.getBool();
					break;
				case OSCTypeTag::Char:
					Print << argument.getChar();
					break;
				case OSCTypeTag::Int32:
					Print << argument.getInt32();
					break;
				case OSCTypeTag::Int64:
					Print << argument.getInt64();
					break;
				case OSCTypeTag::Float:
					Print << argument.getFloat();
					break;
				case OSCTypeTag::Double:
					Print << argument.getDouble();
					break;
				case OSCTypeTag::MIDIMessage:
					Print << argument.getMIDIMessage();
					break;
				case OSCTypeTag::TimeTag:
					Print << argument.getTimeTag();
					break;
				case OSCTypeTag::RGBAColor:
					Print << argument.getColor();
					break;
				case OSCTypeTag::String:
				case OSCTypeTag::Symbol:
					Print << argument.getString();
					break;
				case OSCTypeTag::Blob:
					Print << argument.getBlob().asArray();
					break;
				case OSCTypeTag::Nil:
					Print << U"Nil";
					break;
				case OSCTypeTag::Infinitum:
					Print << U"Infinitum";
					break;
				case OSCTypeTag::ArrayBegin:
					Print << U"[";
					break;
				case OSCTypeTag::ArrayEnd:
					Print << U"]";
					break;
				}
			}
		}
	}
}
```