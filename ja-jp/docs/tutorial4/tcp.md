# 74. TCP 通信
TCP 通信を使ったネットワークプログラミングの方法を学びます。

## 74.1 TCP 通信の基本
- `TCPServer` は、指定したポートで接続を待ち受けます
- `TCPClient` は、指定した IP アドレスとポートに接続します
- いずれも `.send()` および `.read()` を使ってデータを送受信します
- 次のサンプルでは、2 つのプログラム間で、互いのマウスカーソルの座標を共有します
	- サーバ側とクライアント側、2 つのプログラムを用意します

### サーバ側

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	// 利用するポート番号（50000 番ポートで接続待機）
	const uint16 port = 50000;

	// クライアントとの接続状態を管理するフラグ
	bool connected = false;

	// TCPサーバーオブジェクトを作成し、指定ポートで接続受付を開始する
	TCPServer server;
	server.startAccept(port);

	// ウィンドウタイトルに初期状態（接続待機中）を表示
	Window::SetTitle(U"TCPServer: Waiting for connection...");

	// クライアントから受信したプレイヤーの座標
	Point clientPlayerPos{ 0, 0 };

	while (System::Update())
	{
		// サーバー側プレイヤーの現在座標
		const Point serverPlayerPos = Cursor::Pos();

		// クライアントからの接続セッションが存在する場合
		if (server.hasSession())
		{
			// 初回の接続確立時に接続状態フラグを更新し、ウィンドウタイトルを変更する
			if (not connected)
			{
				connected = true;
				Window::SetTitle(U"TCPServer: Connection established!");
			}

			// サーバー側のプレイヤー座標をクライアントへ送信する
			server.send(serverPlayerPos);

			// クライアント側から送信されたプレイヤー座標を受信する
			// （複数データが同時に送られている可能性に備え、すべてのデータを読み込む）
			while (server.read(clientPlayerPos));
		}

		// 接続済みだが、セッションが切断された場合の処理
		if (connected && (not server.hasSession()))
		{
			// セッション切断処理を実行する
			server.disconnect();
			connected = false;

			// ウィンドウタイトルを接続待機中に変更し、再度接続受付を開始する
			Window::SetTitle(U"TCPServer: Waiting for connection...");
			server.startAccept(port);
		}

		// サーバー側プレイヤーを大きな黒の円で描画する
		Circle{ serverPlayerPos, 30 }.draw(ColorF{ 0.1 });

		// クライアント側プレイヤーを小さな白い円で描画する
		Circle{ clientPlayerPos, 10 }.draw();
	}
}
```


### クライアント側

```cpp
# include <Siv3D.hpp>

void Main()
{
	// 接続先のIPv4アドレス（この例ではローカルホスト：自分自身の PC）
	const IPv4Address ip = IPv4Address::Localhost();

	// 利用するポート番号（サーバと同じ50000番）
	constexpr uint16 port = 50000;

	// サーバとの接続状態を管理するフラグ
	bool connected = false;

	// TCPクライアントオブジェクトを作成
	TCPClient client;

	// サーバへの接続を試行する
	client.connect(ip, port);

	// ウィンドウタイトルに初期状態（接続待機中）を表示する
	Window::SetTitle(U"TCPClient: Waiting for connection...");

	// サーバから受信したプレイヤーの座標
	Point serverPlayerPos{ 0, 0 };

	while (System::Update())
	{
		// クライアント側プレイヤーの現在座標
		const Point clientPlayerPos = Cursor::Pos();

		// サーバへ正常に接続できている場合の処理
		if (client.isConnected())
		{
			// 初回の接続確立時に接続状態フラグを更新し、ウィンドウタイトルを変更する
			if (not connected) // 初めてサーバに接続できた場合
			{
				connected = true;
				Window::SetTitle(U"TCPClient: Connection established!");
			}

			// クライアント側のプレイヤー座標をサーバへ送信する
			client.send(clientPlayerPos);

			// サーバ側から送信されたプレイヤー座標を受信する
			// （受信可能なデータが全て処理されるまでループ）
			while (client.read(serverPlayerPos));
		}

		// エラーが発生した場合（切断や接続エラーなど）
		if (client.hasError())
		{
			// 現在の接続を切断し、接続状態フラグをリセットする
			client.disconnect();
			connected = false;

			// ウィンドウタイトルを接続待機中に変更し、再度サーバへの接続を試行する
			Window::SetTitle(U"TCPClient: Waiting for connection...");
			client.connect(ip, port);
		}

		// クライアント側プレイヤーを大きな白い円で描画する
		Circle{ clientPlayerPos, 30 }.draw();

		// クライアント側プレイヤーを小さな黒い円で描画する
		Circle{ serverPlayerPos, 10 }.draw(ColorF{ 0.1 });
	}
}
```
