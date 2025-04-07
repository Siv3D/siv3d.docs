# 75. マルチプレイヤー
Photon SDK を使用してマルチプレイヤーゲームを作成するための、基本的な手順を説明します。

## 75.1 Photon SDK の準備

### 75.1.1 Photon SDK のダウンロード
1. 開発環境に応じた [Photon Realtime SDK :material-open-in-new:](https://www.photonengine.com/ja/sdks#realtime-cpp){:target="_blank"} (7z 形式で圧縮) をダウンロードします。Siv3D v0.6.16 で検証済みの SDK バージョンは `v5.0.12` です
2. ダウンロードしたファイルを展開し、適当な場所に配置します（これ以降の手順でプロジェクトのインクルード / ライブラリパスをこのフォルダパスに対して設定するため、これ以降は移動させません）

### 75.1.2 Siv3D プロジェクトの準備
1. 通常どおり Siv3D アプリケーションプロジェクトを作成します
2. Siv3D SDK フォルダ内* の `Aaddon/Multiplayer_Photon` フォルダから 3 つのファイル `Multiplayer_Photon.hpp`, `Multiplayer_Photon.cpp`, `PHOTON_APP_ID.SECRET` をコピーして、プロジェクトの Main.cpp があるフォルダに配置します
3. Multiplayer_Photon ライブラリを自分のプロジェクトで使うために、コピーした `Multiplayer_Photon.hpp` と `Multiplayer_Photon.cpp` をプロジェクトに追加し、ビルド対象に含むようにします。ただし、このままでは Photon SDK へのインクルード・ライブラリパスが通っていないため、ビルドには失敗します
4. （Windows の場合）プロジェクトの設定で、**インクルードディレクトリ**と**ライブラリディレクトリ**それぞれに、ダウンロードした Photon SDK フォルダのパス（例: `C:/Users/siv3d/Desktop/libs/Photon-Windows-Sdk_v5-0-12-0s`）を追加します
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1a.png)
5. （macOS の場合）プロジェクトの設定で、ダウンロードした Photon SDK フォルダのパスを Build Settings の **Header Search Paths** に追加し、**Library Search Paths** に `●●●/Common-cpp/lib`, `●●●/LoadBalancing-cpp/lib`, `●●●/Photon-cpp/lib`, `●●●/3rdparty/lib/apple` の 4 つのパスを追加 (●●● は Photon SDK フォルダのパス) したうえで、Build Phases の **Link Binary With Libraries** に、それらのフォルダの中身のうち `libCommon-cpp_release_macosx.a`, `libLoadBalancing-cpp_release_macosx.a`, `libPhoton-cpp_release_macosx.a`, `libcrypto_release_macosx.a` の 4 ファイルを追加します 
6. これでビルドができればプロジェクトの準備は完了です

!!! info "Siv3D SDK フォルダ"
    - Siv3D をインストールしたときに作成されるフォルダです
        - macOS の場合は、ダウンロードした Siv3D SDK 自身です
        - Windows の場合は、デフォルトでドキュメントフォルダに `OpenSiv3D_0.6.*` という名前で作成されます

### 75.1.3 Photon App ID の設定
1. Photon Web サイトログイン後、ダッシュボード画面を開きます
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1b.png)
2. ダッシュボード画面の **CREATE A NEW APP** を押して情報を入力し、**CREATE** から新しい Photon App ID を発行します。**Photon Type** は **Realtime** を選択します。それ以外の入力項目は任意です
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1c.png)
3. 発行される Photon App ID は `"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` のようなランダムな英数字です
4. **75.1.2** でプロジェクトに追加した `PHOTON_APP_ID.SECRET` に書かれているプレースホルダーの App ID `"00000000-0000-0000-0000-000000000000"` を、発行された Photon App ID で置き換えます
5. （プロジェクトを git 管理している場合）この Photon App ID は第三者に知られてはいけません。`.gitignore` を用いて、`PHOTON_APP_ID.SECRET` を管理対象から外すようにしましょう

## 75.2 最初のプログラム

### インクルード
- `<Siv3D.hpp>` に続いて、`"Multiplayer_Photon.hpp"` と `"PHOTON_APP_ID.SECRET"` をインクルードします。

### Multiplayer_Photon の継承
- `Multiplayer_Photon` を継承したクラス `MyNetwork`（名前は任意）を作成し、`using Multiplayer_Photon::Multiplayer_Photon;` で `Multiplayer_Photon` のコンストラクタも継承します。

### Photon App ID の格納
- `const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };` とすることで、実行時に Photon App ID が `secretAppID` に格納されます
- 直接 `const std::string secretAppID{ PHOTON_APP_ID };` とすると、ビルドした実行ファイルのバイナリを解析した際に、Photon App ID がそのまま表れてしまいますが、`SIV3D_OBFUSCATE()` で包むことで、多少の難読化が施されます

### MyNetwork の作成
- `MyNetwork` オブジェクトを作成します。コンストラクタには Photon App ID, アプリケーションのバージョン、詳細なデバッグ表示の有無、の 3 つのパラメータを渡します
- Photon App ID が同じでも、アプリケーションのバージョンが異なるプログラムとは通信ができません。ゲームのバージョンアップ後に、新旧のバージョン同士で通信してしまうことを防ぐことができます
- 詳細なデバッグ表示を有効 (`Verbose::Yes`) にすると、`Multiplayer_Photon` クラスの protected メンバ変数 `m_verbose` が `true` になり、`Multiplayer_Photon` の各種コールバック関数が呼ばれた際に、詳細な情報を `Print` 経由で出力するようになります
	- 開発中は有効にしておくとデバッグに便利です。リリース時には `Verbose::No` を選択すると、出力をオフにできます

```cpp
# include <Siv3D.hpp>
# include "Multiplayer_Photon.hpp"
# include "PHOTON_APP_ID.SECRET"

// Multiplayer_Photon を継承したクラス
class MyNetwork : public Multiplayer_Photon
{
public:

	// Multiplayer_Photon のコンストラクタを継承
	using Multiplayer_Photon::Multiplayer_Photon;
};

void Main()
{
	// ウィンドウを　1280x720 にリサイズ
	Window::Resize(1280, 720);

	// Photon App ID
	// 実行ファイルに App ID が直接埋め込まれないよう、SIV3D_OBFUSCATE() でラップ
	const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };

	// サーバーと通信するためのクラス
	// - Photon App ID
	// - 今回作ったアプリケーションのバージョン（これが異なるプログラムとは通信できない）
	// - Print によるデバッグ出力の有無
	MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

	while (System::Update())
	{

	}
}
```


## 75.3 サーバーに接続する

### サーバーに接続する
- `MyNetwork` の `.connect()` でサーバーに接続します
- 引数として自身の名前（ユーザ名）を設定します
- このあと、ユーザ名にランダムな数字をつなげた文字列がユーザ ID として自動的に割り当てられます

### サーバーと同期する
- `.connect()` すると `.isActive()` が `true` を返すようになります
- この間は 60 FPS の頻度で `.update()` を呼び、サーバと同期をとり続ける必要があります
- `.update()` が数秒以上呼ばれないと、サーバから切断される場合があります
- サーバと接続していない時に `.update()` を呼んでも何も起こりません
- 以降の節で登場する「～すると呼ばれる関数」は、基本的に `.update()` のタイミングで呼ばれます

### サーバから切断する
- `MyNetwork ` のデストラクタが、自動的にサーバとの切断を処理するため、明示的な `.disconnect()` は不要です

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	class MyNetwork : public Multiplayer_Photon
	{
	public:

		using Multiplayer_Photon::Multiplayer_Photon;
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };
		MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

		while (System::Update())
		{
			// サーバーと接続している場合、サーバーと同期する
			network.update();

			// サーバーに接続するボタン
			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not network.isActive())))
			{
				// ユーザ名
				const String userName = U"Siv";

				// サーバーに接続する
				network.connect(userName);
			}
		}

		// サーバーから切断する
		// Multiplayer_Photon のデストラクタで自動的に切断されるため、明示的に呼ぶ必要はない
		// network.disconnect();
	}
	```


## 75.4 サーバへの接続を試みた結果を処理する関数のカスタマイズ

### connectReturn() のオーバーライド
- `Multiplayer_Photon::connectReturn()` は、サーバへの接続を試みた結果を処理する関数です
- これをオーバーライドすることで、処理をカスタマイズできます。

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	class MyNetwork : public Multiplayer_Photon
	{
	public:

		using Multiplayer_Photon::Multiplayer_Photon;

	private:

		// サーバへの接続を試みた結果を処理する関数をオーバーライドしてカスタマイズする
		void connectReturn([[maybe_unused]] const int32 errorCode, const String& errorString, const String& region, [[maybe_unused]] const String& cluster) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::connectReturn() [サーバへの接続を試みた結果を処理する]";
			}

			if (errorCode) // サーバーへの接続に失敗していたら errorCode が 0 以外の値
			{
				if (m_verbose)
				{
					Print << U"[サーバへの接続に失敗] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[サーバへの接続に成功]";
				Print << U"[region: {}]"_fmt(region);
				Print << U"[ユーザ名: {}]"_fmt(getUserName());
				Print << U"[ユーザ ID: {}]"_fmt(getUserID());
			}

			// 背景色を青色に
			Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };
		MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

		while (System::Update())
		{
			network.update();

			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not network.isActive())))
			{
				const String userName = U"Siv";
				network.connect(userName);
			}
		}
	}
	```


## 75.5 サーバから切断したときに呼ばれる関数のカスタマイズ

### サーバから切断する
- `MyNetwork` の `.disconnect()` でサーバーから切断します
- この操作を行うと、のちの節で説明する、参加しているルームからも強制的に退出し、`leaveRoomReturn()` コールバックが呼ばれません

### disconnectReturn() のオーバーライド
- `Multiplayer_Photon::disconnectReturn()` は、サーバから切断したときに呼ばれる関数です
- これをオーバーライドすることで、処理をカスタマイズできます

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	class MyNetwork : public Multiplayer_Photon
	{
	public:

		using Multiplayer_Photon::Multiplayer_Photon;

	private:

		void connectReturn([[maybe_unused]] const int32 errorCode, const String& errorString, const String& region, [[maybe_unused]] const String& cluster) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::connectReturn() [サーバへの接続を試みた結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[サーバへの接続に失敗] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[サーバへの接続に成功]";
				Print << U"[region: {}]"_fmt(region);
				Print << U"[ユーザ名: {}]"_fmt(getUserName());
				Print << U"[ユーザ ID: {}]"_fmt(getUserID());
			}

			Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });
		}

		// サーバから切断したときに呼ばれる関数をオーバーライドしてカスタマイズする
		void disconnectReturn() override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::disconnectReturn() [サーバから切断したときに呼ばれる]";
			}

			// 背景色をデフォルトの色に
			Scene::SetBackground(Palette::DefaultBackground);
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };
		MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

		while (System::Update())
		{
			network.update();

			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not network.isActive())))
			{
				const String userName = U"Siv";
				network.connect(userName);
			}

			// サーバーから切断するボタン
			if (SimpleGUI::Button(U"Disconnect", Vec2{ 1000, 60 }, 160, network.isActive()))
			{
				// サーバーから切断する（ルームからも強制的に退出し、leaveRoomReturn() は呼ばれない）
				network.disconnect();
			}
		}
	}
	```


## 75.6 既存のランダムなルームに参加を試みる

### 既存のランダムなルームに参加を試みる
- `MyNetwork` の `.joinRandomRoom()` で、既存の参加可能なランダムなルームに参加を試みます
	- 引数にはルームの定員を指定します
	- 無料版の Photon Realtime では最大 20 人です。
- このプログラムを実行しても、誰もルームを作成していないので、まだマッチングは成立しません

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	class MyNetwork : public Multiplayer_Photon
	{
	public:

		// ルームの定員（無料の Photon Realtime では最大 20）
		static constexpr int32 MaxPlayers = 3;

		using Multiplayer_Photon::Multiplayer_Photon;

	private:

		void connectReturn([[maybe_unused]] const int32 errorCode, const String& errorString, const String& region, [[maybe_unused]] const String& cluster) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::connectReturn() [サーバへの接続を試みた結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[サーバへの接続に失敗] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[サーバへの接続に成功]";
				Print << U"[region: {}]"_fmt(region);
				Print << U"[ユーザ名: {}]"_fmt(getUserName());
				Print << U"[ユーザ ID: {}]"_fmt(getUserID());
			}

			Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });
		}

		void disconnectReturn() override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::disconnectReturn() [サーバから切断したときに呼ばれる]";
			}

			Scene::SetBackground(Palette::DefaultBackground);
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };
		MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

		while (System::Update())
		{
			network.update();

			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not network.isActive())))
			{
				const String userName = U"Siv";
				network.connect(userName);
			}

			if (SimpleGUI::Button(U"Disconnect", Vec2{ 1000, 60 }, 160, network.isActive()))
			{
				network.disconnect();
			}

			// ルームに参加するボタン
			if (SimpleGUI::Button(U"Join Room", Vec2{ 1000, 100 }, 160, network.isInLobby()))
			{
				// 既存のランダムなルームに参加を試みる
				network.joinRandomRoom(MyNetwork::MaxPlayers);
			}
		}
	}
	```


## 75.7 既存のランダムなルームへの参加を試みた結果を処理する関数のカスタマイズ

### joinRandomRoomReturn() のオーバーライド
- `Multiplayer_Photon::joinRandomRoomReturn()` は、ランダムなルームへの参加を試みた結果が通知されるときに呼ばれる関数です
- これをオーバーライドすることで、処理をカスタマイズできます。
- まだ誰もルームを作成していないので、このプログラムを実行してもマッチングは成立しません

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	class MyNetwork : public Multiplayer_Photon
	{
	public:

		static constexpr int32 MaxPlayers = 3;

		using Multiplayer_Photon::Multiplayer_Photon;

	private:

		void connectReturn([[maybe_unused]] const int32 errorCode, const String& errorString, const String& region, [[maybe_unused]] const String& cluster) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::connectReturn() [サーバへの接続を試みた結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[サーバへの接続に失敗] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[サーバへの接続に成功]";
				Print << U"[region: {}]"_fmt(region);
				Print << U"[ユーザ名: {}]"_fmt(getUserName());
				Print << U"[ユーザ ID: {}]"_fmt(getUserID());
			}

			Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });
		}

		void disconnectReturn() override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::disconnectReturn() [サーバから切断したときに呼ばれる]";
			}

			Scene::SetBackground(Palette::DefaultBackground);
		}

		// 既存のランダムなルームに参加を試みた結果を処理する関数をオーバーライドしてカスタマイズする
		void joinRandomRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRandomRoomReturn() [既存のランダムなルームに参加を試みた結果を処理する]";
			}

			// 参加可能なランダムなルームが無い場合、NoRandomMatchFound エラーになる
			if (errorCode == NoRandomMatchFound)
			{
				if (m_verbose)
				{
					Print << U"[参加可能なランダムなルームが無かった]";
				}

				return;
			}
			else if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[既存のランダムなルームへの参加でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[既存のランダムなルームに参加できた]";
			}
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };
		MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

		while (System::Update())
		{
			network.update();

			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not network.isActive())))
			{
				const String userName = U"Siv";
				network.connect(userName);
			}

			if (SimpleGUI::Button(U"Disconnect", Vec2{ 1000, 60 }, 160, network.isActive()))
			{
				network.disconnect();
			}

			if (SimpleGUI::Button(U"Join Room", Vec2{ 1000, 100 }, 160, network.isInLobby()))
			{
				network.joinRandomRoom(MyNetwork::MaxPlayers);
			}
		}
	}
	```


## 75.8 既存のランダムなルームが見つからなかったときに、自分でルームを新規作成する

### ルームの新規作成を試みる
- 参加可能なランダムなルームが見つからなかったときに、`MyNetwork ` の `.createRoom()` で新しいルームの作成を試みます
- 第 1 引数にはルームの名前、第 2 引数にはルームの定員を指定します
- ルームの名前は既存のルーム名とは重複できません

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	class MyNetwork : public Multiplayer_Photon
	{
	public:

		static constexpr int32 MaxPlayers = 3;

		using Multiplayer_Photon::Multiplayer_Photon;

	private:

		void connectReturn([[maybe_unused]] const int32 errorCode, const String& errorString, const String& region, [[maybe_unused]] const String& cluster) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::connectReturn() [サーバへの接続を試みた結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[サーバへの接続に失敗] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[サーバへの接続に成功]";
				Print << U"[region: {}]"_fmt(region);
				Print << U"[ユーザ名: {}]"_fmt(getUserName());
				Print << U"[ユーザ ID: {}]"_fmt(getUserID());
			}

			Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });
		}

		void disconnectReturn() override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::disconnectReturn() [サーバから切断したときに呼ばれる]";
			}

			Scene::SetBackground(Palette::DefaultBackground);
		}

		void joinRandomRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRandomRoomReturn() [既存のランダムなルームに参加を試みた結果を処理する]";
			}

			if (errorCode == NoRandomMatchFound)
			{
				// 新しく作るルーム名（既存のルーム名とは重複できない）
				const RoomName roomName = (getUserName() + U"'s room-" + ToHex(RandomUint32()));

				if (m_verbose)
				{
					Print << U"[参加可能なランダムなルームが見つからなかった]";
					Print << U"[自分でルーム " << roomName << U" を新規作成する]";
				}

				// 自分でルームの新規作成を試みる
				createRoom(roomName, MaxPlayers);

				return;
			}
			else if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[既存のランダムなルームへの参加でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[既存のランダムなルームに参加できた]";
			}
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };
		MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

		while (System::Update())
		{
			network.update();

			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not network.isActive())))
			{
				const String userName = U"Siv";
				network.connect(userName);
			}

			if (SimpleGUI::Button(U"Disconnect", Vec2{ 1000, 60 }, 160, network.isActive()))
			{
				network.disconnect();
			}

			if (SimpleGUI::Button(U"Join Room", Vec2{ 1000, 100 }, 160, network.isInLobby()))
			{
				network.joinRandomRoom(MyNetwork::MaxPlayers);
			}
		}
	}
	```


## 75.9 ルームを新規作成した結果を処理する関数のカスタマイズ

### createRoomReturn() のオーバーライド
- `Multiplayer_Photon::createRoomReturn()` は、ルームの作成を試みた結果が通知されるときに呼ばれる関数です
- これをオーバーライドすることで、処理をカスタマイズできます

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	class MyNetwork : public Multiplayer_Photon
	{
	public:

		static constexpr int32 MaxPlayers = 3;

		using Multiplayer_Photon::Multiplayer_Photon;

	private:

		void connectReturn([[maybe_unused]] const int32 errorCode, const String& errorString, const String& region, [[maybe_unused]] const String& cluster) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::connectReturn() [サーバへの接続を試みた結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[サーバへの接続に失敗] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[サーバへの接続に成功]";
				Print << U"[region: {}]"_fmt(region);
				Print << U"[ユーザ名: {}]"_fmt(getUserName());
				Print << U"[ユーザ ID: {}]"_fmt(getUserID());
			}

			Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });
		}

		void disconnectReturn() override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::disconnectReturn() [サーバから切断したときに呼ばれる]";
			}

			Scene::SetBackground(Palette::DefaultBackground);
		}

		void joinRandomRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRandomRoomReturn() [既存のランダムなルームに参加を試みた結果を処理する]";
			}

			if (errorCode == NoRandomMatchFound)
			{
				const RoomName roomName = (getUserName() + U"'s room-" + ToHex(RandomUint32()));

				if (m_verbose)
				{
					Print << U"[参加可能なランダムなルームが見つからなかった]";
					Print << U"[自分でルーム " << roomName << U" を新規作成する]";
				}

				createRoom(roomName, MaxPlayers);

				return;
			}
			else if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[既存のランダムなルームへの参加でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[既存のランダムなルームに参加できた]";
			}
		}

		// ルームを新規作成した結果を処理する関数をオーバーライドしてカスタマイズする
		void createRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::createRoomReturn() [ルームを新規作成した結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[ルームの新規作成でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[ルーム " << getCurrentRoomName() << U" の作成に成功]";
			}
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };
		MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

		while (System::Update())
		{
			network.update();

			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not network.isActive())))
			{
				const String userName = U"Siv";
				network.connect(userName);
			}

			if (SimpleGUI::Button(U"Disconnect", Vec2{ 1000, 60 }, 160, network.isActive()))
			{
				network.disconnect();
			}

			if (SimpleGUI::Button(U"Join Room", Vec2{ 1000, 100 }, 160, network.isInLobby()))
			{
				network.joinRandomRoom(MyNetwork::MaxPlayers);
			}
		}
	}
	```


## 75.10 誰か（自分を含む）が現在のルームに参加したら呼ばれる関数のカスタマイズ

### joinRoomEventAction() のオーバーライド
- `Multiplayer_Photon::joinRoomEventAction()` は、誰か（自分を含む）が現在のルームに参加したときに呼ばれる関数です
- これをオーバーライドすることで、処理をカスタマイズできます
- 自分自身の参加に対して呼ばれたものであるかは、引数 `isSelf` で確認できます

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	class MyNetwork : public Multiplayer_Photon
	{
	public:

		static constexpr int32 MaxPlayers = 3;

		using Multiplayer_Photon::Multiplayer_Photon;

	private:

		void connectReturn([[maybe_unused]] const int32 errorCode, const String& errorString, const String& region, [[maybe_unused]] const String& cluster) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::connectReturn() [サーバへの接続を試みた結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[サーバへの接続に失敗] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[サーバへの接続に成功]";
				Print << U"[region: {}]"_fmt(region);
				Print << U"[ユーザ名: {}]"_fmt(getUserName());
				Print << U"[ユーザ ID: {}]"_fmt(getUserID());
			}

			Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });
		}

		void disconnectReturn() override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::disconnectReturn() [サーバから切断したときに呼ばれる]";
			}

			Scene::SetBackground(Palette::DefaultBackground);
		}

		void joinRandomRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRandomRoomReturn() [既存のランダムなルームに参加を試みた結果を処理する]";
			}

			if (errorCode == NoRandomMatchFound)
			{
				const RoomName roomName = (getUserName() + U"'s room-" + ToHex(RandomUint32()));

				if (m_verbose)
				{
					Print << U"[参加可能なランダムなルームが見つからなかった]";
					Print << U"[自分でルーム " << roomName << U" を新規作成する]";
				}

				createRoom(roomName, MaxPlayers);

				return;
			}
			else if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[既存のランダムなルームへの参加でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[既存のランダムなルームに参加できた]";
			}
		}

		void createRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::createRoomReturn() [ルームを新規作成した結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[ルームの新規作成でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[ルーム " << getCurrentRoomName() << U" の作成に成功]";
			}
		}

		// 誰か（自分を含む）が現在のルームに参加したときに呼ばれる関数をオーバーライドしてカスタマイズする
		void joinRoomEventAction(const LocalPlayer& newPlayer, [[maybe_unused]] const Array<LocalPlayerID>& playerIDs, const bool isSelf) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRoomEventAction() [誰か（自分を含む）が現在のルームに参加したときに呼ばれる]";
			}

			if (m_verbose)
			{
				// 自分自身の参加である場合 isSelf が true
				Print << U"[{} (ID: {}) がルームに参加した。ローカル ID: {}] {}"_fmt(newPlayer.userName, newPlayer.userID, newPlayer.localID, (isSelf ? U"(自分自身)" : U""));
			}
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };
		MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

		while (System::Update())
		{
			network.update();

			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not network.isActive())))
			{
				const String userName = U"Siv";
				network.connect(userName);
			}

			if (SimpleGUI::Button(U"Disconnect", Vec2{ 1000, 60 }, 160, network.isActive()))
			{
				network.disconnect();
			}

			if (SimpleGUI::Button(U"Join Room", Vec2{ 1000, 100 }, 160, network.isInLobby()))
			{
				network.joinRandomRoom(MyNetwork::MaxPlayers);
			}
		}
	}
	```


## 75.11 ルームのメンバーの管理

### ルームのメンバー管理
- ルームのメンバー管理のために、メンバ変数 `Array<LocalPlayer> m_localPlayers;` を用意します
- `joinRoomEventAction` において `getLocalPlayers()` を通してメンバーの情報を取得し、切断時には `.clear()` で中身を消去します

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	class MyNetwork : public Multiplayer_Photon
	{
	public:

		static constexpr int32 MaxPlayers = 3;

		using Multiplayer_Photon::Multiplayer_Photon;

	private:

		// 現在のルームのメンバー
		Array<LocalPlayer> m_localPlayers;

		void connectReturn([[maybe_unused]] const int32 errorCode, const String& errorString, const String& region, [[maybe_unused]] const String& cluster) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::connectReturn() [サーバへの接続を試みた結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[サーバへの接続に失敗] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[サーバへの接続に成功]";
				Print << U"[region: {}]"_fmt(region);
				Print << U"[ユーザ名: {}]"_fmt(getUserName());
				Print << U"[ユーザ ID: {}]"_fmt(getUserID());
			}

			Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });
		}

		void disconnectReturn() override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::disconnectReturn() [サーバから切断したときに呼ばれる]";
			}

			// ルームメンバー情報をクリア
			m_localPlayers.clear();

			Scene::SetBackground(Palette::DefaultBackground);
		}

		void joinRandomRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRandomRoomReturn() [既存のランダムなルームに参加を試みた結果を処理する]";
			}

			if (errorCode == NoRandomMatchFound)
			{
				const RoomName roomName = (getUserName() + U"'s room-" + ToHex(RandomUint32()));

				if (m_verbose)
				{
					Print << U"[参加可能なランダムなルームが見つからなかった]";
					Print << U"[自分でルーム " << roomName << U" を新規作成する]";
				}

				createRoom(roomName, MaxPlayers);

				return;
			}
			else if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[既存のランダムなルームへの参加でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[既存のランダムなルームに参加できた]";
			}
		}

		void createRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::createRoomReturn() [ルームを新規作成した結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[ルームの新規作成でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[ルーム " << getCurrentRoomName() << U" の作成に成功]";
			}
		}

		void joinRoomEventAction(const LocalPlayer& newPlayer, [[maybe_unused]] const Array<LocalPlayerID>& playerIDs, const bool isSelf) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRoomEventAction() [誰か（自分を含む）が現在のルームに参加したときに呼ばれる]";
			}

			// （今回参加した人を含む）現在ルームに参加しているプレイヤーの情報一覧を取得
			m_localPlayers = getLocalPlayers();

			if (m_verbose)
			{
				Print << U"[{} (ID: {}) がルームに参加した。ローカル ID: {}] {}"_fmt(newPlayer.userName, newPlayer.userID, newPlayer.localID, (isSelf ? U"(自分自身)" : U""));

				Print << U"現在の " << getCurrentRoomName() << U" のルームメンバー";

				for (const auto& player : m_localPlayers)
				{
					Print << U"- [{}] {} (id: {}) {}"_fmt(player.localID, player.userName, player.userID, player.isHost ? U"(host)" : U"");
				}
			}
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };
		MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

		while (System::Update())
		{
			network.update();

			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not network.isActive())))
			{
				const String userName = U"Siv";
				network.connect(userName);
			}

			if (SimpleGUI::Button(U"Disconnect", Vec2{ 1000, 60 }, 160, network.isActive()))
			{
				network.disconnect();
			}

			if (SimpleGUI::Button(U"Join Room", Vec2{ 1000, 100 }, 160, network.isInLobby()))
			{
				network.joinRandomRoom(MyNetwork::MaxPlayers);
			}
		}
	}
	```


## 75.12 ルームからの退出

### ルームから退出する
- `MyNetwork` の `.leaveRoom()` を呼ぶと、現在参加しているルームからの退出を試みます

### leaveRoomEventAction() のオーバーライド
- `Multiplayer_Photon::leaveRoomEventAction()` は、現在参加しているルームから誰かが退出したときに呼ばれる関数です
- これをオーバーライドすることで、処理をカスタマイズできます

### leaveRoomReturn() のオーバーライド
- `Multiplayer_Photon::leaveRoomReturn()` は、自身がルームから退出したときに呼ばれる関数です
- これをオーバーライドすることで、処理をカスタマイズできます

### マルチプレイを試す
- ここまでくれば、マルチプレイを実際に試せるようになります
- 実行ファイルを複数用意して同時に起動したり、ネットワークにつながっている異なるパソコンからプログラムを実行したりしてみましょう

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	class MyNetwork : public Multiplayer_Photon
	{
	public:

		static constexpr int32 MaxPlayers = 3;

		using Multiplayer_Photon::Multiplayer_Photon;

	private:

		Array<LocalPlayer> m_localPlayers;

		void connectReturn([[maybe_unused]] const int32 errorCode, const String& errorString, const String& region, [[maybe_unused]] const String& cluster) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::connectReturn() [サーバへの接続を試みた結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[サーバへの接続に失敗] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[サーバへの接続に成功]";
				Print << U"[region: {}]"_fmt(region);
				Print << U"[ユーザ名: {}]"_fmt(getUserName());
				Print << U"[ユーザ ID: {}]"_fmt(getUserID());
			}

			Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });
		}

		void disconnectReturn() override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::disconnectReturn() [サーバから切断したときに呼ばれる]";
			}

			m_localPlayers.clear();

			Scene::SetBackground(Palette::DefaultBackground);
		}

		void joinRandomRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRandomRoomReturn() [既存のランダムなルームに参加を試みた結果を処理する]";
			}

			if (errorCode == NoRandomMatchFound)
			{
				const RoomName roomName = (getUserName() + U"'s room-" + ToHex(RandomUint32()));

				if (m_verbose)
				{
					Print << U"[参加可能なランダムなルームが見つからなかった]";
					Print << U"[自分でルーム " << roomName << U" を新規作成する]";
				}

				createRoom(roomName, MaxPlayers);

				return;
			}
			else if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[既存のランダムなルームへの参加でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[既存のランダムなルームに参加できた]";
			}
		}

		void createRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::createRoomReturn() [ルームを新規作成した結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[ルームの新規作成でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[ルーム " << getCurrentRoomName() << U" の作成に成功]";
			}
		}

		void joinRoomEventAction(const LocalPlayer& newPlayer, [[maybe_unused]] const Array<LocalPlayerID>& playerIDs, const bool isSelf) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRoomEventAction() [誰か（自分を含む）が現在のルームに参加したときに呼ばれる]";
			}

			m_localPlayers = getLocalPlayers();

			if (m_verbose)
			{
				Print << U"[{} (ID: {}) がルームに参加した。ローカル ID: {}] {}"_fmt(newPlayer.userName, newPlayer.userID, newPlayer.localID, (isSelf ? U"(自分自身)" : U""));

				Print << U"現在の " << getCurrentRoomName() << U" のルームメンバー";

				for (const auto& player : m_localPlayers)
				{
					Print << U"- [{}] {} (id: {}) {}"_fmt(player.localID, player.userName, player.userID, player.isHost ? U"(host)" : U"");
				}
			}
		}

		// 現在のルームから誰かが退出したときに呼ばれる関数をオーバーライドしてカスタマイズする
		void leaveRoomEventAction(const LocalPlayerID playerID, [[maybe_unused]] const bool isInactive) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::leaveRoomEventAction() [誰かがルームから退出したら呼ばれる]";
			}

			// 現在ルームに残っているプレイヤーの情報一覧を更新
			m_localPlayers = getLocalPlayers();

			if (m_verbose)
			{
				for (const auto& player : m_localPlayers)
				{
					// 退出した人の ID と一致したら
					if (player.localID == playerID)
					{
						Print << U"[{} (ID: {}, ローカル ID: {}) がルームから退出した]"_fmt(player.userName, player.userID, player.localID);
					}
				}

				Print << U"現在の " << getCurrentRoomName() << U" のルームメンバー";

				for (const auto& player : m_localPlayers)
				{
					Print << U"- [{}] {} (ID: {}) {}"_fmt(player.localID, player.userName, player.userID, player.isHost ? U"(host)" : U"");
				}
			}
		}

		// ルームから退出したときに呼ばれる関数をオーバーライドしてカスタマイズする
		void leaveRoomReturn(int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::leaveRoomReturn() [ルームから退出したときに呼ばれる]";
			}

			// ルームメンバー情報をクリア
			m_localPlayers.clear();

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[ルームからの退出でエラーが発生] " << errorString;
				}

				return;
			}
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };
		MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

		while (System::Update())
		{
			network.update();

			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not network.isActive())))
			{
				const String userName = U"Siv";
				network.connect(userName);
			}

			if (SimpleGUI::Button(U"Disconnect", Vec2{ 1000, 60 }, 160, network.isActive()))
			{
				network.disconnect();
			}

			if (SimpleGUI::Button(U"Join Room", Vec2{ 1000, 100 }, 160, network.isInLobby()))
			{
				network.joinRandomRoom(MyNetwork::MaxPlayers);
			}

			// 現在のルームから退出するボタン
			if (SimpleGUI::Button(U"Leave Room", Vec2{ 1000, 140 }, 160, network.isInRoom()))
			{
				// 現在のルームから退出を試みる
				network.leaveRoom();
			}
		}
	}
	```


## 75.13 イベントの送信

### ルームにイベントを送信する
- `MyNetwork` の `.sendEvent()` を使うと、現在参加しているルームにイベントを送信できます
- イベントは「イベントコード」と「値」から構成されます
- イベントコードは `uint8` 型で、イベントの種類の識別に使います
- 値は次のような型が使えます：
	- `bool`, `uint8`, `int16`, `int32`, `int64`, `float`, `double`, `String`
	- `Array<bool>`, `Array<uint8>`, `Array<int16>`, `Array<int32>`, `Array<int64>`, `Array<float>`, `Array<double>`, `Array<String>`
	- `Color`, `ColorF`, `HSV`, `Point`, `Vec2`, `Vec3`, `Vec4`, `Float2`, `Float3`, `Float4`, `Mat3x2`
	- `Line`, `Rect`, `RectF`, `Circle`, `Triangle`, `Quad`, `Ellipse`, `RoundRect`
- 上記以外の型の値を送信したい場合、**75.15** のようにシリアライザを使います
- Photon Realtime にはイベントの送信頻度やサイズに制限があるため、送信するデータは最小限にする必要があります。詳しくは Photon のドキュメントを参照してください

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	class MyNetwork : public Multiplayer_Photon
	{
	public:

		static constexpr int32 MaxPlayers = 3;

		using Multiplayer_Photon::Multiplayer_Photon;

	private:

		Array<LocalPlayer> m_localPlayers;

		void connectReturn([[maybe_unused]] const int32 errorCode, const String& errorString, const String& region, [[maybe_unused]] const String& cluster) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::connectReturn() [サーバへの接続を試みた結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[サーバへの接続に失敗] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[サーバへの接続に成功]";
				Print << U"[region: {}]"_fmt(region);
				Print << U"[ユーザ名: {}]"_fmt(getUserName());
				Print << U"[ユーザ ID: {}]"_fmt(getUserID());
			}

			Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });
		}

		void disconnectReturn() override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::disconnectReturn() [サーバから切断したときに呼ばれる]";
			}

			m_localPlayers.clear();

			Scene::SetBackground(Palette::DefaultBackground);
		}

		void joinRandomRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRandomRoomReturn() [既存のランダムなルームに参加を試みた結果を処理する]";
			}

			if (errorCode == NoRandomMatchFound)
			{
				const RoomName roomName = (getUserName() + U"'s room-" + ToHex(RandomUint32()));

				if (m_verbose)
				{
					Print << U"[参加可能なランダムなルームが見つからなかった]";
					Print << U"[自分でルーム " << roomName << U" を新規作成する]";
				}

				createRoom(roomName, MaxPlayers);

				return;
			}
			else if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[既存のランダムなルームへの参加でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[既存のランダムなルームに参加できた]";
			}
		}

		void createRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::createRoomReturn() [ルームを新規作成した結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[ルームの新規作成でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[ルーム " << getCurrentRoomName() << U" の作成に成功]";
			}
		}

		void joinRoomEventAction(const LocalPlayer& newPlayer, [[maybe_unused]] const Array<LocalPlayerID>& playerIDs, const bool isSelf) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRoomEventAction() [誰か（自分を含む）が現在のルームに参加したときに呼ばれる]";
			}

			m_localPlayers = getLocalPlayers();

			if (m_verbose)
			{
				Print << U"[{} (ID: {}) がルームに参加した。ローカル ID: {}] {}"_fmt(newPlayer.userName, newPlayer.userID, newPlayer.localID, (isSelf ? U"(自分自身)" : U""));

				Print << U"現在の " << getCurrentRoomName() << U" のルームメンバー";

				for (const auto& player : m_localPlayers)
				{
					Print << U"- [{}] {} (id: {}) {}"_fmt(player.localID, player.userName, player.userID, player.isHost ? U"(host)" : U"");
				}
			}
		}

		void leaveRoomEventAction(const LocalPlayerID playerID, [[maybe_unused]] const bool isInactive) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::leaveRoomEventAction() [誰かがルームから退出したら呼ばれる]";
			}

			m_localPlayers = getLocalPlayers();

			if (m_verbose)
			{
				for (const auto& player : m_localPlayers)
				{
					if (player.localID == playerID)
					{
						Print << U"[{} (ID: {}, ローカル ID: {}) がルームから退出した]"_fmt(player.userName, player.userID, player.localID);
					}
				}

				Print << U"現在の " << getCurrentRoomName() << U" のルームメンバー";

				for (const auto& player : m_localPlayers)
				{
					Print << U"- [{}] {} (ID: {}) {}"_fmt(player.localID, player.userName, player.userID, player.isHost ? U"(host)" : U"");
				}
			}
		}

		void leaveRoomReturn(int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::leaveRoomReturn() [ルームから退出したときに呼ばれる]";
			}

			m_localPlayers.clear();

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[ルームからの退出でエラーが発生] " << errorString;
				}

				return;
			}
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };
		MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

		while (System::Update())
		{
			network.update();

			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not network.isActive())))
			{
				const String userName = U"Siv";
				network.connect(userName);
			}

			if (SimpleGUI::Button(U"Disconnect", Vec2{ 1000, 60 }, 160, network.isActive()))
			{
				network.disconnect();
			}

			if (SimpleGUI::Button(U"Join Room", Vec2{ 1000, 100 }, 160, network.isInLobby()))
			{
				network.joinRandomRoom(MyNetwork::MaxPlayers);
			}

			if (SimpleGUI::Button(U"Leave Room", Vec2{ 1000, 140 }, 160, network.isInRoom()))
			{
				network.leaveRoom();
			}

			// ランダムな int32 を送るボタン
			if (SimpleGUI::Button(U"Send int32", Vec2{ 1000, 180 }, 200, network.isInRoom()))
			{
				const int32 n = Random(0, 10000);
				Print << U"eventCode: 0, int32(" << n << U") を送信 >>>";
				network.sendEvent(0, n);
			}

			// ランダムなテキストを送るボタン
			if (SimpleGUI::Button(U"Send String", Vec2{ 1000, 220 }, 200, network.isInRoom()))
			{
				const String s = Sample({ U"Hello!", U"Thank you!", U"Nice!" });
				Print << U"eventCode: 0, String(" << s << U") を送信 >>>";
				network.sendEvent(0, s);
			}

			// ランダムな Point を送るボタン
			if (SimpleGUI::Button(U"Send Point", Vec2{ 1000, 260 }, 200, network.isInRoom()))
			{
				const Point pos = RandomPoint(Rect{ 1280, 720 });
				Print << U"eventCode: 0, Point" << pos << U" を送信 >>>";
				network.sendEvent(0, pos);
			}

			// ランダムな Array<int32> を送るボタン
			if (SimpleGUI::Button(U"Send Array<int32>", Vec2{ 1000, 300 }, 200, network.isInRoom()))
			{
				Array<int32> v(3);
				for (auto& n : v)
				{
					n = Random(0, 1000);
				}
				Print << U"eventCode: 0, Array<int32>" << v << U" を送信 >>>";
				network.sendEvent(0, v);
			}

			// ランダムな Array<String> を送るボタン
			if (SimpleGUI::Button(U"Send Array<String>", Vec2{ 1000, 340 }, 200, network.isInRoom()))
			{
				Array<String> words(3);
				for (auto& word : words)
				{
					word = Sample({ U"apple", U"bird", U"cat", U"dog" });
				}
				Print << U"eventCode: 0, Array<String>" << words << U" を送信 >>>";
				network.sendEvent(0, words);
			}
		}
	}
	```


## 75.14 イベントの受信

### ルームのイベントを受信する
- `Multiplayer_Photon::customEventAction()` は、現在参加しているルームのイベントを受信した際に呼ばれる関数です
- これをオーバーライドすることで、処理をカスタマイズできます
- **75.13** で挙げられているそれぞれの型に応じてオーバーロードが用意されています

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	class MyNetwork : public Multiplayer_Photon
	{
	public:

		static constexpr int32 MaxPlayers = 3;

		using Multiplayer_Photon::Multiplayer_Photon;

	private:

		Array<LocalPlayer> m_localPlayers;

		void connectReturn([[maybe_unused]] const int32 errorCode, const String& errorString, const String& region, [[maybe_unused]] const String& cluster) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::connectReturn() [サーバへの接続を試みた結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[サーバへの接続に失敗] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[サーバへの接続に成功]";
				Print << U"[region: {}]"_fmt(region);
				Print << U"[ユーザ名: {}]"_fmt(getUserName());
				Print << U"[ユーザ ID: {}]"_fmt(getUserID());
			}

			Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });
		}

		void disconnectReturn() override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::disconnectReturn() [サーバから切断したときに呼ばれる]";
			}

			m_localPlayers.clear();

			Scene::SetBackground(Palette::DefaultBackground);
		}

		void joinRandomRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRandomRoomReturn() [既存のランダムなルームに参加を試みた結果を処理する]";
			}

			if (errorCode == NoRandomMatchFound)
			{
				const RoomName roomName = (getUserName() + U"'s room-" + ToHex(RandomUint32()));

				if (m_verbose)
				{
					Print << U"[参加可能なランダムなルームが見つからなかった]";
					Print << U"[自分でルーム " << roomName << U" を新規作成する]";
				}

				createRoom(roomName, MaxPlayers);

				return;
			}
			else if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[既存のランダムなルームへの参加でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[既存のランダムなルームに参加できた]";
			}
		}

		void createRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::createRoomReturn() [ルームを新規作成した結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[ルームの新規作成でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[ルーム " << getCurrentRoomName() << U" の作成に成功]";
			}
		}

		void joinRoomEventAction(const LocalPlayer& newPlayer, [[maybe_unused]] const Array<LocalPlayerID>& playerIDs, const bool isSelf) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRoomEventAction() [誰か（自分を含む）が現在のルームに参加したときに呼ばれる]";
			}

			m_localPlayers = getLocalPlayers();

			if (m_verbose)
			{
				Print << U"[{} (ID: {}) がルームに参加した。ローカル ID: {}] {}"_fmt(newPlayer.userName, newPlayer.userID, newPlayer.localID, (isSelf ? U"(自分自身)" : U""));

				Print << U"現在の " << getCurrentRoomName() << U" のルームメンバー";

				for (const auto& player : m_localPlayers)
				{
					Print << U"- [{}] {} (id: {}) {}"_fmt(player.localID, player.userName, player.userID, player.isHost ? U"(host)" : U"");
				}
			}
		}

		void leaveRoomEventAction(const LocalPlayerID playerID, [[maybe_unused]] const bool isInactive) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::leaveRoomEventAction() [誰かがルームから退出したら呼ばれる]";
			}

			m_localPlayers = getLocalPlayers();

			if (m_verbose)
			{
				for (const auto& player : m_localPlayers)
				{
					if (player.localID == playerID)
					{
						Print << U"[{} (ID: {}, ローカル ID: {}) がルームから退出した]"_fmt(player.userName, player.userID, player.localID);
					}
				}

				Print << U"現在の " << getCurrentRoomName() << U" のルームメンバー";

				for (const auto& player : m_localPlayers)
				{
					Print << U"- [{}] {} (ID: {}) {}"_fmt(player.localID, player.userName, player.userID, player.isHost ? U"(host)" : U"");
				}
			}
		}

		void leaveRoomReturn(int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::leaveRoomReturn() [ルームから退出したときに呼ばれる]";
			}

			m_localPlayers.clear();

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[ルームからの退出でエラーが発生] " << errorString;
				}

				return;
			}
		}

		// int32 を受信したときに呼ばれる関数をオーバーライドしてカスタマイズする
		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const int32 data) override
		{
			Print << U"<<< [" << playerID << U"] からの eventCode: " << eventCode << U", data: int32(" << data << U") を受信";
		}

		// String を受信したときに呼ばれる関数をオーバーライドしてカスタマイズする
		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const String& data) override
		{
			Print << U"<<< [" << playerID << U"] からの eventCode: " << eventCode << U", data: String(" << data << U") を受信";
		}

		// Point を受信したときに呼ばれる関数をオーバーライドしてカスタマイズする
		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const Point& data) override
		{
			Print << U"<<< [" << playerID << U"] からの eventCode: " << eventCode << U", data: Point" << data << U" を受信";
		}

		// Array<int32> を受信したときに呼ばれる関数をオーバーライドしてカスタマイズする
		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const Array<int32>& data) override
		{
			Print << U"<<< [" << playerID << U"] からの eventCode: " << eventCode << U", data: Array<int32>" << data << U" を受信";
		}

		// Array<String> を受信したときに呼ばれる関数をオーバーライドしてカスタマイズする
		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const Array<String>& data) override
		{
			Print << U"<<< [" << playerID << U"] からの eventCode: " << eventCode << U", data: Array<String>" << data << U" を受信";
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };
		MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

		while (System::Update())
		{
			network.update();

			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not network.isActive())))
			{
				const String userName = U"Siv";
				network.connect(userName);
			}

			if (SimpleGUI::Button(U"Disconnect", Vec2{ 1000, 60 }, 160, network.isActive()))
			{
				network.disconnect();
			}

			if (SimpleGUI::Button(U"Join Room", Vec2{ 1000, 100 }, 160, network.isInLobby()))
			{
				network.joinRandomRoom(MyNetwork::MaxPlayers);
			}

			if (SimpleGUI::Button(U"Leave Room", Vec2{ 1000, 140 }, 160, network.isInRoom()))
			{
				network.leaveRoom();
			}

			if (SimpleGUI::Button(U"Send int32", Vec2{ 1000, 180 }, 200, network.isInRoom()))
			{
				const int32 n = Random(0, 10000);
				Print << U"eventCode: 0, int32(" << n << U") を送信 >>>";
				network.sendEvent(0, n);
			}

			if (SimpleGUI::Button(U"Send String", Vec2{ 1000, 220 }, 200, network.isInRoom()))
			{
				const String s = Sample({ U"Hello!", U"Thank you!", U"Nice!" });
				Print << U"eventCode: 0, String(" << s << U") を送信 >>>";
				network.sendEvent(0, s);
			}

			if (SimpleGUI::Button(U"Send Point", Vec2{ 1000, 260 }, 200, network.isInRoom()))
			{
				const Point pos = RandomPoint(Rect{ 1280, 720 });
				Print << U"eventCode: 0, Point" << pos << U" を送信 >>>";
				network.sendEvent(0, pos);
			}

			if (SimpleGUI::Button(U"Send Array<int32>", Vec2{ 1000, 300 }, 200, network.isInRoom()))
			{
				Array<int32> v(3);
				for (auto& n : v)
				{
					n = Random(0, 1000);
				}
				Print << U"eventCode: 0, Array<int32>" << v << U" を送信 >>>";
				network.sendEvent(0, v);
			}

			if (SimpleGUI::Button(U"Send Array<String>", Vec2{ 1000, 340 }, 200, network.isInRoom()))
			{
				Array<String> words(3);
				for (auto& word : words)
				{
					word = Sample({ U"apple", U"bird", U"cat", U"dog" });
				}
				Print << U"eventCode: 0, Array<String>" << words << U" を送信 >>>";
				network.sendEvent(0, words);
			}
		}
	}
	```


## 75.15 ユーザ定義型の送受信

### ユーザ定義型の送受信
- **75.13** で挙げられている以外の型を送受信する場合、その型をシリアライズ可能にすると、`Serializer<MemoryWriter>` で送信、`Deserializer<MemoryViewReader>` で受信できます

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	// ユーザ定義型
	struct MyData
	{
		String word;

		Point pos;

		// シリアライズに対応させるためのメンバ関数を定義する
		template <class Archive>
		void SIV3D_SERIALIZE(Archive& archive)
		{
			archive(word, pos);
		}
	};

	class MyNetwork : public Multiplayer_Photon
	{
	public:

		static constexpr int32 MaxPlayers = 3;

		using Multiplayer_Photon::Multiplayer_Photon;

	private:

		Array<LocalPlayer> m_localPlayers;

		void connectReturn([[maybe_unused]] const int32 errorCode, const String& errorString, const String& region, [[maybe_unused]] const String& cluster) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::connectReturn() [サーバへの接続を試みた結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[サーバへの接続に失敗] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[サーバへの接続に成功]";
				Print << U"[region: {}]"_fmt(region);
				Print << U"[ユーザ名: {}]"_fmt(getUserName());
				Print << U"[ユーザ ID: {}]"_fmt(getUserID());
			}

			Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });
		}

		void disconnectReturn() override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::disconnectReturn() [サーバから切断したときに呼ばれる]";
			}

			m_localPlayers.clear();

			Scene::SetBackground(Palette::DefaultBackground);
		}

		void joinRandomRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRandomRoomReturn() [既存のランダムなルームに参加を試みた結果を処理する]";
			}

			if (errorCode == NoRandomMatchFound)
			{
				const RoomName roomName = (getUserName() + U"'s room-" + ToHex(RandomUint32()));

				if (m_verbose)
				{
					Print << U"[参加可能なランダムなルームが見つからなかった]";
					Print << U"[自分でルーム " << roomName << U" を新規作成する]";
				}

				createRoom(roomName, MaxPlayers);

				return;
			}
			else if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[既存のランダムなルームへの参加でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[既存のランダムなルームに参加できた]";
			}
		}

		void createRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::createRoomReturn() [ルームを新規作成した結果を処理する]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[ルームの新規作成でエラーが発生] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[ルーム " << getCurrentRoomName() << U" の作成に成功]";
			}
		}

		void joinRoomEventAction(const LocalPlayer& newPlayer, [[maybe_unused]] const Array<LocalPlayerID>& playerIDs, const bool isSelf) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRoomEventAction() [誰か（自分を含む）が現在のルームに参加したときに呼ばれる]";
			}

			m_localPlayers = getLocalPlayers();

			if (m_verbose)
			{
				Print << U"[{} (ID: {}) がルームに参加した。ローカル ID: {}] {}"_fmt(newPlayer.userName, newPlayer.userID, newPlayer.localID, (isSelf ? U"(自分自身)" : U""));

				Print << U"現在の " << getCurrentRoomName() << U" のルームメンバー";

				for (const auto& player : m_localPlayers)
				{
					Print << U"- [{}] {} (id: {}) {}"_fmt(player.localID, player.userName, player.userID, player.isHost ? U"(host)" : U"");
				}
			}
		}

		void leaveRoomEventAction(const LocalPlayerID playerID, [[maybe_unused]] const bool isInactive) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::leaveRoomEventAction() [誰かがルームから退出したら呼ばれる]";
			}

			m_localPlayers = getLocalPlayers();

			if (m_verbose)
			{
				for (const auto& player : m_localPlayers)
				{
					if (player.localID == playerID)
					{
						Print << U"[{} (ID: {}, ローカル ID: {}) がルームから退出した]"_fmt(player.userName, player.userID, player.localID);
					}
				}

				Print << U"現在の " << getCurrentRoomName() << U" のルームメンバー";

				for (const auto& player : m_localPlayers)
				{
					Print << U"- [{}] {} (ID: {}) {}"_fmt(player.localID, player.userName, player.userID, player.isHost ? U"(host)" : U"");
				}
			}
		}

		void leaveRoomReturn(int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::leaveRoomReturn() [ルームから退出したときに呼ばれる]";
			}

			m_localPlayers.clear();

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[ルームからの退出でエラーが発生] " << errorString;
				}

				return;
			}
		}

		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const int32 data) override
		{
			Print << U"<<< [" << playerID << U"] からの eventCode: " << eventCode << U", data: int32(" << data << U") を受信";
		}

		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const String& data) override
		{
			Print << U"<<< [" << playerID << U"] からの eventCode: " << eventCode << U", data: String(" << data << U") を受信";
		}

		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const Point& data) override
		{
			Print << U"<<< [" << playerID << U"] からの eventCode: " << eventCode << U", data: Point" << data << U" を受信";
		}

		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const Array<int32>& data) override
		{
			Print << U"<<< [" << playerID << U"] からの eventCode: " << eventCode << U", data: Array<int32>" << data << U" を受信";
		}

		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const Array<String>& data) override
		{
			Print << U"<<< [" << playerID << U"] からの eventCode: " << eventCode << U", data: Array<String>" << data << U" を受信";
		}

		// シリアライズデータを受信したときに呼ばれる関数をオーバーライドしてカスタマイズする
		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, Deserializer<MemoryViewReader>& reader) override
		{
			if (eventCode == 123)
			{
				MyData mydata;
				reader(mydata);
				Print << U"<<< [" << playerID << U"] からの MyData(" << mydata.word << U", " << mydata.pos << U") を受信";
			}
		}
	};

	void Main()
	{
		Window::Resize(1280, 720);
		const std::string secretAppID{ SIV3D_OBFUSCATE(PHOTON_APP_ID) };
		MyNetwork network{ secretAppID, U"1.0", Verbose::Yes };

		while (System::Update())
		{
			network.update();

			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not network.isActive())))
			{
				const String userName = U"Siv";
				network.connect(userName);
			}

			if (SimpleGUI::Button(U"Disconnect", Vec2{ 1000, 60 }, 160, network.isActive()))
			{
				network.disconnect();
			}

			if (SimpleGUI::Button(U"Join Room", Vec2{ 1000, 100 }, 160, network.isInLobby()))
			{
				network.joinRandomRoom(MyNetwork::MaxPlayers);
			}

			if (SimpleGUI::Button(U"Leave Room", Vec2{ 1000, 140 }, 160, network.isInRoom()))
			{
				network.leaveRoom();
			}

			if (SimpleGUI::Button(U"Send int32", Vec2{ 1000, 180 }, 200, network.isInRoom()))
			{
				const int32 n = Random(0, 10000);
				Print << U"eventCode: 0, int32(" << n << U") を送信 >>>";
				network.sendEvent(0, n);
			}

			if (SimpleGUI::Button(U"Send String", Vec2{ 1000, 220 }, 200, network.isInRoom()))
			{
				const String s = Sample({ U"Hello!", U"Thank you!", U"Nice!" });
				Print << U"eventCode: 0, String(" << s << U") を送信 >>>";
				network.sendEvent(0, s);
			}

			if (SimpleGUI::Button(U"Send Point", Vec2{ 1000, 260 }, 200, network.isInRoom()))
			{
				const Point pos = RandomPoint(Rect{ 1280, 720 });
				Print << U"eventCode: 0, Point" << pos << U" を送信 >>>";
				network.sendEvent(0, pos);
			}

			if (SimpleGUI::Button(U"Send Array<int32>", Vec2{ 1000, 300 }, 200, network.isInRoom()))
			{
				Array<int32> v(3);
				for (auto& n : v)
				{
					n = Random(0, 1000);
				}
				Print << U"eventCode: 0, Array<int32>" << v << U" を送信 >>>";
				network.sendEvent(0, v);
			}

			if (SimpleGUI::Button(U"Send Array<String>", Vec2{ 1000, 340 }, 200, network.isInRoom()))
			{
				Array<String> words(3);
				for (auto& word : words)
				{
					word = Sample({ U"apple", U"bird", U"cat", U"dog" });
				}
				Print << U"eventCode: 0, Array<String>" << words << U" を送信 >>>";
				network.sendEvent(0, words);
			}

			// ランダムな MyData を送るボタン
			if (SimpleGUI::Button(U"Send MyData", Vec2{ 1000, 380 }, 200, network.isInRoom()))
			{
				MyData myData;
				myData.word = Sample({ U"apple", U"bird", U"cat", U"dog" });
				myData.pos = RandomPoint(Rect{ 1280, 720 });

				Print << U"eventCode: 123, MyData(" << myData.word << U", " << myData.pos << U") を送信 >>>";
				network.sendEvent(123, Serializer<MemoryWriter>{}(myData));
			}
		}
	}
	```
