
!!! warning "このページよりも新しいドキュメントがあります"
	このドキュメントは古い OpenSiv3D v0.4.3 向けです。2021 年9 月 18 日に最新の OpenSiv3D v0.6.0 がリリースされました。最新のドキュメントは [Siv3D リファレンス v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation) です。

# ネットワーク

## 1 対 1 の TCP 通信

### サーバ側

```C++
# include <Siv3D.hpp>

void Main()
{
	constexpr uint16 port = 50000;
	bool connected = false;

	TCPServer server;
	server.startAccept(port);
	Window::SetTitle(U"TCPServer: Waiting for connection...");

	Point clientPlayerPos(0, 0);

	while (System::Update())
	{
		const Point serverPlayerPos = Cursor::Pos();

		if (server.hasSession())
		{
			if (!connected) // クライアントが接続
			{
				connected = true;

				Window::SetTitle(U"TCPServer: Connection established!");
			}

			// 送信
			server.send(serverPlayerPos);

			// 受信
			while (server.read(clientPlayerPos));
		}

		// クライアントが切断
		if (connected && !server.hasSession())
		{
			// 切断
			server.disconnect();

			connected = false;

			Window::SetTitle(U"TCPServer: Waiting for connection...");

			server.startAccept(port);
		}

		Circle(serverPlayerPos, 30).draw(Palette::Skyblue);

		Circle(clientPlayerPos, 10).draw(Palette::Orange);
	}
}
```

### クライアント側

```C++
# include <Siv3D.hpp>

void Main()
{
	const IPv4 ip = IPv4::Localhost();
	constexpr uint16 port = 50000;
	bool connected = false;

	TCPClient client;
	client.connect(ip, port);
	Window::SetTitle(U"TCPClient: Waiting for connection...");

	Point serverPlayerPos(0, 0);

	while (System::Update())
	{
		const Point clientPlayerPos = Cursor::Pos();

		if (client.isConnected())
		{
			if (!connected)
			{
				connected = true;

				Window::SetTitle(U"TCPClient: Connection established!");
			}

			// 送信
			client.send(clientPlayerPos);

			// 受信
			while (client.read(serverPlayerPos));
		}

		if (client.hasError()) // 切断/接続エラー
		{
			client.disconnect();

			connected = false;

			Window::SetTitle(U"TCPClient: Waiting for connection...");

			client.connect(ip, port);
		}

		Circle(clientPlayerPos, 30).draw(Palette::Skyblue);

		Circle(serverPlayerPos, 30).draw(Palette::Orange);
	}
}
```

