# Web サービスとの連係サンプル


## 1. アンケート回答でアイテムを獲得する

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/web/1.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		TextEditState textEditState;

		bool received = false;

		bool pushed = false;

		while (System::Update())
		{
			Rect{ 40, 40, 480, 140 }.rounded(10).drawShadow(Vec2{ 2, 2 }, 12, 0).draw();

			if (not received)
			{
				if (SimpleGUI::Button(U"アンケートに回答して秘密のコードを入手", Vec2{ 60, 60 }, 440))
				{
					pushed = true;
					System::LaunchBrowser(U"https://forms.gle/vyiwgwNFSvZPZ8fu5");
				}

				SimpleGUI::Headline(U"コードを入力", Vec2{ 60, 118 }, unspecified, pushed);

				SimpleGUI::TextBox(textEditState, Vec2{ 220, 120 }, 160, 8, pushed);

				if (SimpleGUI::Button(U"確認", Vec2{ 400, 120 }, 60, pushed))
				{
					if (textEditState.text == U"123")
					{
						received = true;
						Print << U"アイテムを獲得しました。";
					}
					else
					{
						Print << U"無効なコードです。";
					}
				}
			}
			else
			{
				SimpleGUI::Headline(U"アンケートへの回答ありがとうございました。", Vec2{ 60, 90 });
			}
		}
	}
	```


## 2. ゲームのスコアをツイートする

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/web/2.png)

??? memo "コード"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		int32 score = 12345;

		while (System::Update())
		{
			if (SimpleGUI::Button(U"スコアをツイート", Vec2{ 40, 40 }))
			{
				// ハッシュタグや URL を含めると広まりやすいです。
				const String text = U"ゲームで {} 点取ったよ！\n#Test #Siv3D\nhttps://github.com/Siv3D/OpenSiv3D"_fmt(ThousandsSeparate(score));

				// ツイート投稿画面を開く
				Twitter::OpenTweetWindow(text);
			}
		}
	}
	```


## 3. チャット
Photon との連係方法は、[チュートリアル 66. マルチプレイヤー](../../tutorial4/multiplayer/) を参照してください。  
作成したルームに参加したプレイヤー同士でデータの送受信を行うサンプルです。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/web/3.png)

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

			if (m_verbose)
			{
				Print << U"[{} (ID: {}) がルームに参加した。ローカル ID: {}] {}"_fmt(newPlayer.userName, newPlayer.userID, newPlayer.localID, (isSelf ? U"(自分自身)" : U""));

				m_localPlayers = getLocalPlayers();

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
				Print << U"MyNetwork::joinRoomEventAction() [誰かがルームから退出したら呼ばれる]";
			}

			if (m_verbose)
			{
				for (const auto& player : m_localPlayers)
				{
					if (player.localID == playerID)
					{
						Print << U"[{} (ID: {}, ローカル ID: {}) がルームから退出した]"_fmt(player.userName, player.userID, player.localID);
					}
				}

				m_localPlayers = getLocalPlayers();

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

			const bool isActive = network.isActive();
			const bool isInLobby = network.isInLobby();
			const bool isInRoom = network.isInRoom();

			if (SimpleGUI::Button(U"Connect", Vec2{ 1000, 20 }, 160, (not isActive)))
			{
				const String userName = U"Siv";
				network.connect(userName);
			}

			if (SimpleGUI::Button(U"Disconnect", Vec2{ 1000, 60 }, 160, isActive))
			{
				network.disconnect();
			}

			if (SimpleGUI::Button(U"Join Room", Vec2{ 1000, 100 }, 160, isInLobby))
			{
				network.joinRandomRoom(MyNetwork::MaxPlayers);
			}

			if (SimpleGUI::Button(U"Leave Room", Vec2{ 1000, 140 }, 160, isInRoom))
			{
				network.leaveRoom();
			}

			if (SimpleGUI::Button(U"Send int32", Vec2{ 1000, 180 }, 200, isInRoom))
			{
				const int32 n = Random(0, 10000);
				Print << U"eventCode: 0, int32(" << n << U") を送信 >>>";
				network.sendEvent(0, n);
			}

			if (SimpleGUI::Button(U"Send String", Vec2{ 1000, 220 }, 200, isInRoom))
			{
				const String s = Sample({ U"Hello!", U"Thank you!", U"Nice!" });
				Print << U"eventCode: 0, String(" << s << U") を送信 >>>";
				network.sendEvent(0, s);
			}

			if (SimpleGUI::Button(U"Send Point", Vec2{ 1000, 260 }, 200, isInRoom))
			{
				const Point pos = RandomPoint(Scene::Rect());
				Print << U"eventCode: 0, Point" << pos << U" を送信 >>>";
				network.sendEvent(0, pos);
			}

			if (SimpleGUI::Button(U"Send Array<int32>", Vec2{ 1000, 300 }, 200, isInRoom))
			{
				Array<int32> v(3);
				for (auto& n : v)
				{
					n = Random(0, 1000);
				}
				Print << U"eventCode: 0, Array<int32>" << v << U" を送信 >>>";
				network.sendEvent(0, v);
			}

			if (SimpleGUI::Button(U"Send Array<String>", Vec2{ 1000, 340 }, 200, isInRoom))
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
			if (SimpleGUI::Button(U"Send MyData", Vec2{ 1000, 380 }, 200, isInRoom))
			{
				MyData myData;
				myData.word = Sample({ U"apple", U"bird", U"cat", U"dog" });
				myData.pos = RandomPoint(Scene::Rect());

				Print << U"eventCode: 123, MyData(" << myData.word << U", " << myData.pos << U") を送信 >>>";
				network.sendEvent(123, Serializer<MemoryWriter>{}(myData));
			}
		}
	}
	```


## 4. オンライン リーダーボード

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/Leaderboard/Screenshot/1.png)

[Siv3D-Sample | オンライン リーダーボード :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/tree/main/Samples/Leaderboard){:target="_blank" .md-button}
