# Web Service Integration Samples


## 1. Earn Items by Answering Surveys

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/web/1.png)

??? memo "Code"
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
				if (SimpleGUI::Button(U"Answer survey and get secret code", Vec2{ 60, 60 }, 440))
				{
					pushed = true;
					System::LaunchBrowser(U"https://forms.gle/vyiwgwNFSvZPZ8fu5");
				}

				SimpleGUI::Headline(U"Enter code", Vec2{ 60, 118 }, unspecified, pushed);

				SimpleGUI::TextBox(textEditState, Vec2{ 220, 120 }, 160, 8, pushed);

				if (SimpleGUI::Button(U"Confirm", Vec2{ 400, 120 }, 60, pushed))
				{
					if (textEditState.text == U"123")
					{
						received = true;
						Print << U"Item acquired.";
					}
					else
					{
						Print << U"Invalid code.";
					}
				}
			}
			else
			{
				SimpleGUI::Headline(U"Thank you for answering the survey.", Vec2{ 60, 90 });
			}
		}
	}
	```


## 2. Tweet Game Score

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/web/2.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>

	void Main()
	{
		Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

		int32 score = 12345;

		while (System::Update())
		{
			if (SimpleGUI::Button(U"Tweet score", Vec2{ 40, 40 }))
			{
				// Including hashtags and URLs helps spread the word.
				const String text = U"I scored {} points in the game!\n#Test #Siv3D\nhttps://github.com/Siv3D/OpenSiv3D"_fmt(ThousandsSeparate(score));

				// Open tweet posting screen
				Twitter::OpenTweetWindow(text);
			}
		}
	}
	```


## 3. Chat
For how to integrate with Photon, see [Tutorial 75. Multiplayer](../tutorial4/multiplayer.md).  
This is a sample for sending and receiving data between players who joined the created room.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/samples/web/3.png)

??? memo "Code"
	```cpp
	# include <Siv3D.hpp>
	# include "Multiplayer_Photon.hpp"
	# include "PHOTON_APP_ID.SECRET"

	// User-defined type
	struct MyData
	{
		String word;

		Point pos;

		// Define member function for serialization support
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
				Print << U"MyNetwork::connectReturn() [Process result of attempting to connect to server]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[Server connection failed] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[Server connection successful]";
				Print << U"[region: {}]"_fmt(region);
				Print << U"[Username: {}]"_fmt(getUserName());
				Print << U"[User ID: {}]"_fmt(getUserID());
			}

			Scene::SetBackground(ColorF{ 0.4, 0.5, 0.6 });
		}

		void disconnectReturn() override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::disconnectReturn() [Called when disconnected from server]";
			}

			m_localPlayers.clear();

			Scene::SetBackground(Palette::DefaultBackground);
		}

		void joinRandomRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRandomRoomReturn() [Process result of attempting to join existing random room]";
			}

			if (errorCode == NoRandomMatchFound)
			{
				const RoomName roomName = (getUserName() + U"'s room-" + ToHex(RandomUint32()));

				if (m_verbose)
				{
					Print << U"[No available random room found]";
					Print << U"[Creating new room " << roomName << U"]";
				}

				createRoom(roomName, MaxPlayers);

				return;
			}
			else if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[Error joining existing random room] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[Successfully joined existing random room]";
			}
		}

		void createRoomReturn([[maybe_unused]] const LocalPlayerID playerID, const int32 errorCode, const String& errorString) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::createRoomReturn() [Process result of creating new room]";
			}

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[Error creating new room] " << errorString;
				}

				return;
			}

			if (m_verbose)
			{
				Print << U"[Successfully created room " << getCurrentRoomName() << U"]";
			}
		}

		void joinRoomEventAction(const LocalPlayer& newPlayer, [[maybe_unused]] const Array<LocalPlayerID>& playerIDs, const bool isSelf) override
		{
			if (m_verbose)
			{
				Print << U"MyNetwork::joinRoomEventAction() [Called when someone (including yourself) joins the current room]";
			}

			if (m_verbose)
			{
				Print << U"[{} (ID: {}) joined the room. Local ID: {}] {}"_fmt(newPlayer.userName, newPlayer.userID, newPlayer.localID, (isSelf ? U"(yourself)" : U""));

				m_localPlayers = getLocalPlayers();

				Print << U"Current room members of " << getCurrentRoomName();

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
				Print << U"MyNetwork::joinRoomEventAction() [Called when someone leaves the room]";
			}

			if (m_verbose)
			{
				for (const auto& player : m_localPlayers)
				{
					if (player.localID == playerID)
					{
						Print << U"[{} (ID: {}, Local ID: {}) left the room]"_fmt(player.userName, player.userID, player.localID);
					}
				}

				m_localPlayers = getLocalPlayers();

				Print << U"Current room members of " << getCurrentRoomName();

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
				Print << U"MyNetwork::leaveRoomReturn() [Called when leaving room]";
			}

			m_localPlayers.clear();

			if (errorCode)
			{
				if (m_verbose)
				{
					Print << U"[Error leaving room] " << errorString;
				}

				return;
			}
		}

		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const int32 data) override
		{
			Print << U"<<< Received from [" << playerID << U"] eventCode: " << eventCode << U", data: int32(" << data << U")";
		}

		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const String& data) override
		{
			Print << U"<<< Received from [" << playerID << U"] eventCode: " << eventCode << U", data: String(" << data << U")";
		}

		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const Point& data) override
		{
			Print << U"<<< Received from [" << playerID << U"] eventCode: " << eventCode << U", data: Point" << data;
		}

		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const Array<int32>& data) override
		{
			Print << U"<<< Received from [" << playerID << U"] eventCode: " << eventCode << U", data: Array<int32>" << data;
		}

		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, const Array<String>& data) override
		{
			Print << U"<<< Received from [" << playerID << U"] eventCode: " << eventCode << U", data: Array<String>" << data;
		}

		// Override function called when serialized data is received for customization
		void customEventAction(const LocalPlayerID playerID, const uint8 eventCode, Deserializer<MemoryViewReader>& reader) override
		{
			if (eventCode == 123)
			{
				MyData mydata;
				reader(mydata);
				Print << U"<<< Received MyData(" << mydata.word << U", " << mydata.pos << U") from [" << playerID << U"]";
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
				Print << U"Sending eventCode: 0, int32(" << n << U") >>>";
				network.sendEvent(0, n);
			}

			if (SimpleGUI::Button(U"Send String", Vec2{ 1000, 220 }, 200, isInRoom))
			{
				const String s = Sample({ U"Hello!", U"Thank you!", U"Nice!" });
				Print << U"Sending eventCode: 0, String(" << s << U") >>>";
				network.sendEvent(0, s);
			}

			if (SimpleGUI::Button(U"Send Point", Vec2{ 1000, 260 }, 200, isInRoom))
			{
				const Point pos = RandomPoint(Scene::Rect());
				Print << U"Sending eventCode: 0, Point" << pos << U" >>>";
				network.sendEvent(0, pos);
			}

			if (SimpleGUI::Button(U"Send Array<int32>", Vec2{ 1000, 300 }, 200, isInRoom))
			{
				Array<int32> v(3);
				for (auto& n : v)
				{
					n = Random(0, 1000);
				}
				Print << U"Sending eventCode: 0, Array<int32>" << v << U" >>>";
				network.sendEvent(0, v);
			}

			if (SimpleGUI::Button(U"Send Array<String>", Vec2{ 1000, 340 }, 200, isInRoom))
			{
				Array<String> words(3);
				for (auto& word : words)
				{
					word = Sample({ U"apple", U"bird", U"cat", U"dog" });
				}
				Print << U"Sending eventCode: 0, Array<String>" << words << U" >>>";
				network.sendEvent(0, words);
			}

			// Button to send random MyData
			if (SimpleGUI::Button(U"Send MyData", Vec2{ 1000, 380 }, 200, isInRoom))
			{
				MyData myData;
				myData.word = Sample({ U"apple", U"bird", U"cat", U"dog" });
				myData.pos = RandomPoint(Scene::Rect());

				Print << U"Sending eventCode: 123, MyData(" << myData.word << U", " << myData.pos << U") >>>";
				network.sendEvent(123, Serializer<MemoryWriter>{}(myData));
			}
		}
	}
	```


## 4. Online Leaderboard

![](https://raw.githubusercontent.com/Siv3D/Siv3D-Samples/main/Samples/Leaderboard/Screenshot/1.png)

[Siv3D-Sample | Online Leaderboard :material-open-in-new:](https://github.com/Siv3D/Siv3D-Samples/tree/main/Samples/Leaderboard){:target="_blank" .md-button}