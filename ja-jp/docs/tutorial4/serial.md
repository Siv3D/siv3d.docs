# 72. シリアル通信
シリアル通信を使って、外部デバイスとデータの送受信を行う方法を学びます。

## 72.1 シリアルポートの列挙
- PC に認識されているシリアルポートの一覧を `System::EnumerateSerialPorts()` で取得できます
- 結果は `Array<SerialPortInfo>` 型で返されます
- `SerialPortInfo` 型のメンバ変数は次のとおりです：

| コード | 説明 |
|---|---|
| `String port` | シリアルポート名 |
| `String description` | シリアルポートの説明 |
| `String hardwareID` | ハードウェア ID |


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
```txt title="出力例"
[COM3] USB シリアル デバイス (COM3)
[COM4] Arduino Uno (COM4)
```


## 72.2 接続する COM ポートの選択
- 次のような関数を使って、接続するシリアルポートを選択する GUI を作成できます

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/serial/2.png)

```cpp
# include <Siv3D.hpp>

Array<String> GetSerialPortOptions()
{
	const Array<SerialPortInfo> infos = System::EnumerateSerialPorts();
	Array<String> options = infos.map([](const SerialPortInfo& info)
	{
		return U"[{}] {}"_fmt(info.port, info.description);
	});

	options.push_front(U"None");
	return options;
}

void Main()
{
	const Array<String> options = GetSerialPortOptions();
	size_t index = 0;

	while (System::Update())
	{
		if (SimpleGUI::RadioButtons(index, options, Vec2{ 200, 40 }))
		{

		}
	}
}
```


## 72.3 Serial クラスの基本


```cpp

```


### 72.4 シリアル通信（1 バイト）

次のサンプルでは、Arduino UNO の LED の点灯/消灯を PC から制御し、1 バイトの数値データをやり取りするサンプルを示します。

#### Arduino UNO 側のコード

```c
void setup()
{
    pinMode(13, OUTPUT); // 13 ピン - LED - 抵抗 - GND

    // 9600bps でシリアルポートを開く
    Serial.begin(9600);
}

unsigned char i = 0; // テスト用に PC 側に送る値

void loop()
{
    // 250 ミリ秒止める
    delay(250);

    // シリアルポートに 1 バイト出力
    Serial.write(i);

    ++i;

    // シリアル通信で受信したデータを読み込む
    const int val = Serial.read();

    if (val == -1) // 受信したデータが無い
    {
        return;
    }

    if (val == 0)
    {
        digitalWrite(13, LOW); // LOW を出力
    }
    else if (val == 1)
    {
        digitalWrite(13, HIGH); // HIGH を出力
    }
    else if (val == 2)
    {
        i = 0;
    }
}
```

#### PC 側のコード

```cpp
# include <Siv3D.hpp>

void Main()
{
	// シリアルポートの一覧を取得
	const Array<SerialPortInfo> infos = System::EnumerateSerialPorts();
	const Array<String> options = infos.map([](const SerialPortInfo& info)
	{
		return U"{} ({})"_fmt(info.port, info.description);
	}) << U"none";

	Serial serial;
	size_t index = (options.size() - 1);

	while (System::Update())
	{
		const bool isOpen = serial.isOpen(); // OpenSiv3D v0.4.2 以前は serial.isOpened()

		if (SimpleGUI::Button(U"Write 0", Vec2{ 200, 20 }, 120, isOpen))
		{
			// 1 バイトのデータ (0) を書き込む
			serial.writeByte(0);
		}

		if (SimpleGUI::Button(U"Write 1", Vec2{ 340, 20 }, 120, isOpen))
		{
			// 1 バイトのデータ (1) を書き込む
			serial.writeByte(1);
		}

		if (SimpleGUI::Button(U"Write 2", Vec2{ 480, 20 }, 120, isOpen))
		{
			// 1 バイトのデータ (2) を書き込む
			serial.writeByte(2);
		}

		if (SimpleGUI::RadioButtons(index, options, Vec2{ 200, 60 }))
		{
			ClearPrint();

			if (index == (options.size() - 1))
			{
				serial = Serial{};
			}
			else
			{
				Print << U"Open {}"_fmt(infos[index].port);

				// シリアルポートをオープン
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
			// シリアル通信で受信したデータを読み込んで表示
			Print << U"READ: " << serial.readBytes();
		}
	}
}
```

### 72.4 シリアル通信（複数バイト）

#### Arduino UNO 側のコード
Arduino UNO では `int` 型は 2 バイトです。

```c
void setup()
{
  Serial.begin(9600);
}

unsigned char i = 0;

void loop()
{
  delay(250);

  if (2 <= Serial.available())
  {
      int low = Serial.read();
      int high = Serial.read();
      int n = high * 256 + low;

      n *= 2;
      Serial.write(lowByte(n));
      Serial.write(highByte(n));
  }
}
```

#### PC 側のコード

```cpp
# include <Siv3D.hpp>

void Main()
{
	// シリアルポートの一覧を取得
	const Array<SerialPortInfo> infos = System::EnumerateSerialPorts();
	const Array<String> options = infos.map([](const SerialPortInfo& info)
	{
		return U"{} ({})"_fmt(info.port, info.description);
	}) << U"none";

	Serial serial;
	size_t index = (options.size() - 1);

	while (System::Update())
	{
		const bool isOpen = serial.isOpen(); // OpenSiv3D v0.4.2 以前は serial.isOpened()

		if (SimpleGUI::Button(U"Write int16", Vec2{ 200, 20 }, 120, isOpen))
		{
			// 2 バイト (int16) のデータを書き込む
			const int16 n = 12300;

			serial.write(n);
		}

		if (SimpleGUI::RadioButtons(index, options, Vec2{ 200, 60 }))
		{
			ClearPrint();

			if (index == (options.size() - 1))
			{
				serial = Serial{};
			}
			else
			{
				Print << U"Open {}"_fmt(infos[index].port);

				// シリアルポートをオープン
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
			int16 n;

			if (serial.read(n)) // 2 バイト (int16) のデータを読み込んだら
			{
				Print << U"READ: " << n;
			}
		}
	}
}
```
