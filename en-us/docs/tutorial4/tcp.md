# 74. TCP Communication
Learn network programming methods using TCP communication.

## 74.1 TCP Communication Basics
- `TCPServer` waits for connections on a specified port
- `TCPClient` connects to a specified IP address and port
- Both use `.send()` and `.read()` to send and receive data
- The following sample shares mouse cursor coordinates between two programs
	- Prepare two programs: server side and client side

### Server Side

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// Port number to use (wait for connections on port 50000)
	const uint16 port = 50000;

	// Flag to manage connection state with client
	bool connected = false;

	// Create TCP server object and start accepting connections on specified port
	TCPServer server;
	server.startAccept(port);

	// Display initial state (waiting for connection) in window title
	Window::SetTitle(U"TCPServer: Waiting for connection...");

	// Player coordinates received from client
	Point clientPlayerPos{ 0, 0 };

	while (System::Update())
	{
		// Current coordinates of server-side player
		const Point serverPlayerPos = Cursor::Pos();

		// If there is a connection session from client
		if (server.hasSession())
		{
			// Update connection state flag and change window title on first connection establishment
			if (not connected)
			{
				connected = true;
				Window::SetTitle(U"TCPServer: Connection established!");
			}

			// Send server-side player coordinates to client
			server.send(serverPlayerPos);

			// Receive player coordinates sent from client side
			// (Read all data in case multiple data are sent simultaneously)
			while (server.read(clientPlayerPos));
		}

		// Handle case when connected but session is disconnected
		if (connected && (not server.hasSession()))
		{
			// Execute session disconnect processing
			server.disconnect();
			connected = false;

			// Change window title to waiting for connection and start accepting connections again
			Window::SetTitle(U"TCPServer: Waiting for connection...");
			server.startAccept(port);
		}

		// Draw server-side player as large black circle
		Circle{ serverPlayerPos, 30 }.draw(ColorF{ 0.1 });

		// Draw client-side player as small white circle
		Circle{ clientPlayerPos, 10 }.draw();
	}
}
```


### Client Side

```cpp
# include <Siv3D.hpp>

void Main()
{
	// Connection destination IPv4 address (in this example, localhost: own PC)
	const IPv4Address ip = IPv4Address::Localhost();

	// Port number to use (same 50000 as server)
	constexpr uint16 port = 50000;

	// Flag to manage connection state with server
	bool connected = false;

	// Create TCP client object
	TCPClient client;

	// Attempt to connect to server
	client.connect(ip, port);

	// Display initial state (waiting for connection) in window title
	Window::SetTitle(U"TCPClient: Waiting for connection...");

	// Player coordinates received from server
	Point serverPlayerPos{ 0, 0 };

	while (System::Update())
	{
		// Current coordinates of client-side player
		const Point clientPlayerPos = Cursor::Pos();

		// Processing when successfully connected to server
		if (client.isConnected())
		{
			// Update connection state flag and change window title on first connection to server
			if (not connected) // First time successfully connecting to server
			{
				connected = true;
				Window::SetTitle(U"TCPClient: Connection established!");
			}

			// Send client-side player coordinates to server
			client.send(clientPlayerPos);

			// Receive player coordinates sent from server side
			// (Loop until all receivable data is processed)
			while (client.read(serverPlayerPos));
		}

		// When error occurs (disconnect or connection error, etc.)
		if (client.hasError())
		{
			// Disconnect current connection and reset connection state flag
			client.disconnect();
			connected = false;

			// Change window title to waiting for connection and attempt to connect to server again
			Window::SetTitle(U"TCPClient: Waiting for connection...");
			client.connect(ip, port);
		}

		// Draw client-side player as large white circle
		Circle{ clientPlayerPos, 30 }.draw();

		// Draw client-side player as small black circle
		Circle{ serverPlayerPos, 10 }.draw(ColorF{ 0.1 });
	}
}
```