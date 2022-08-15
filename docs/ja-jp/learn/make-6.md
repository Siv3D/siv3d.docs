# 演習 F - 脱出ゲーム

脱出ゲームの基本を作ります。


## 1. 基本

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.9, 0.85, 0.8 });

	const Texture computerTexture{ U"🖥️"_emoji };
	const Texture boxTexture{ U"🗃️"_emoji };
	const Texture doorTexture{ U"🚪"_emoji };

	const Vec2 computerPos{ 200, 300 };
	const Vec2 boxPos{ 400, 300 };
	const Vec2 doorPos{ 600, 300 };

	while (System::Update())
	{
		computerTexture.drawAt(computerPos);
		boxTexture.drawAt(boxPos);
		doorTexture.drawAt(doorPos);

		if (const Circle circle{ computerPos, 80 };
			circle.mouseOver())
		{
			circle.draw(ColorF{ 1.0, 0.2 });
			Cursor::RequestStyle(CursorStyle::Hand);

			if (circle.leftClicked())
			{
				Print << U"パソコンだ。電源は入らない。";
			}
		}

		if (const Circle circle{ boxPos, 80 };
			circle.mouseOver())
		{
			circle.draw(ColorF{ 1.0, 0.2 });
			Cursor::RequestStyle(CursorStyle::Hand);

			if (circle.leftClicked())
			{
				Print << U"箱の中に何か入っている。部屋の鍵を見つけた！";
			}
		}

		if (const Circle circle{ doorPos, 80 };
			circle.mouseOver())
		{
			circle.draw(ColorF{ 1.0, 0.2 });
			Cursor::RequestStyle(CursorStyle::Hand);

			if (circle.leftClicked())
			{
				Print << U"ドアには鍵がかかっている。出られない。";
			}
		}
	}
}
```


## 2. クラス化

```cpp hl_lines="3-55 61-63 67-84"
# include <Siv3D.hpp>

class Item
{
public:

	// クラスの初期化のためのメンバ関数（コンストラクタ）
	Item(const Emoji& emoji, const Vec2& pos, bool visible)
		: m_texture{ emoji }
		, m_circle{ pos, 80 }
		, m_visible{ visible } {}

	// 可視性を変更する
	void setVisibility(bool visible)
	{
		m_visible = visible;
	}

	// クリックされたかを返す
	bool clicked() const
	{
		if (not m_visible)
		{
			return false;
		}

		return m_circle.leftClicked();
	}

	// 描画する
	void draw() const
	{
		if (not m_visible)
		{
			return;
		}

		m_texture.drawAt(m_circle.center);

		if (m_circle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);

			m_circle.draw(ColorF{ 1.0, 0.2 });
		}
	}

private:

	Texture m_texture;

	Circle m_circle;

	bool m_visible;
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.9, 0.85, 0.8 });

	Item computer{ U"🖥️"_emoji, Vec2{ 200, 300 }, true };
	Item box{ U"🗃️"_emoji, Vec2{ 400, 300 }, true };
	Item door{ U"🚪"_emoji, Vec2{ 600, 300 }, true };

	while (System::Update())
	{
		computer.draw();
		box.draw();
		door.draw();

		if (computer.clicked())
		{
			Print << U"パソコンだ。電源は入らない。";
		}

		if (box.clicked())
		{
			Print << U"箱の中に何か入っている。部屋の鍵を見つけた！";
		}

		if (door.clicked())
		{
			Print << U"ドアには鍵がかかっている。出られない。";
		}
	}
}
```

## 3. イベントの管理

```cpp hl_lines="64 66-67 74 83-94 99-111 114-117"
# include <Siv3D.hpp>

class Item
{
public:

	// クラスの初期化のためのメンバ関数（コンストラクタ）
	Item(const Emoji& emoji, const Vec2& pos, bool visible)
		: m_texture{ emoji }
		, m_circle{ pos, 80 }
		, m_visible{ visible } {}

	// 可視性を変更する
	void setVisibility(bool visible)
	{
		m_visible = visible;
	}

	// クリックされたかを返す
	bool clicked() const
	{
		if (not m_visible)
		{
			return false;
		}

		return m_circle.leftClicked();
	}

	// 描画する
	void draw() const
	{
		if (not m_visible)
		{
			return;
		}

		m_texture.drawAt(m_circle.center);

		if (m_circle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);

			m_circle.draw(ColorF{ 1.0, 0.2 });
		}
	}

private:

	Texture m_texture;

	Circle m_circle;

	bool m_visible;
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.9, 0.85, 0.8 });

	Item computer{ U"🖥️"_emoji, Vec2{ 200, 300 }, true };
	Item box{ U"🗃️"_emoji, Vec2{ 400, 300 }, true };
	Item door{ U"🚪"_emoji, Vec2{ 600, 300 }, true };
	Item food{ U"🍲"_emoji, Vec2{ 400, 300 }, false };

    // 鍵を持っているかのフラグ
	bool hasKey = false;

	while (System::Update())
	{
		computer.draw();
		box.draw();
		door.draw();
		food.draw();

		if (computer.clicked())
		{
			Print << U"パソコンだ。電源は入らない。";
		}

		if (box.clicked())
		{
			if (not hasKey)
			{
				Print << U"箱の中に何か入っている。";

				Print << U"[鍵を手に入れた！]";

				hasKey = true;
			}
			else
			{
				Print << U"箱の中には重要そうなものは入っていない。";
			}
		}

		if (door.clicked())
		{
			if (not hasKey)
			{
				Print << U"ドアには鍵がかかっている。出られない。";
			}
			else
			{
				Print << U"鍵を使ってドアを開けた！";

				computer.setVisibility(false);
				box.setVisibility(false);
				door.setVisibility(false);
				food.setVisibility(true);
			}
		}

		if (food.clicked())
		{
			Print << U"おいしそうな料理が置かれている。まだ温かい。";
		}
	}
}
```


## 4. メッセージウィンドウの実装

```cpp hl_lines="57-83 107 114 120 128 132 143 146"
# include <Siv3D.hpp>

class Item
{
public:

	// クラスの初期化のためのメンバ関数（コンストラクタ）
	Item(const Emoji& emoji, const Vec2& pos, bool visible)
		: m_texture{ emoji }
		, m_circle{ pos, 80 }
		, m_visible{ visible } {}

	// 可視性を変更する
	void setVisibility(bool visible)
	{
		m_visible = visible;
	}

	// クリックされたかを返す
	bool clicked() const
	{
		if (not m_visible)
		{
			return false;
		}

		return m_circle.leftClicked();
	}

	// 描画する
	void draw() const
	{
		if (not m_visible)
		{
			return;
		}

		m_texture.drawAt(m_circle.center);

		if (m_circle.mouseOver())
		{
			Cursor::RequestStyle(CursorStyle::Hand);

			m_circle.draw(ColorF{ 1.0, 0.2 });
		}
	}

private:

	Texture m_texture;

	Circle m_circle;

	bool m_visible;
};

class MessageWindow
{
public:

	void setText(const String& text)
	{
		m_text = text;
		m_stopwatch.restart(); // ストップウォッチを 0 からスタート
	}

	void draw() const
	{
		Rect{ 20, 440, 760, 140 }.draw(ColorF{ 0.5, 0.5, 0.5 });
		RoundRect{ 30, 450, 740, 120, 10 }.draw();

		const int32 count = (m_stopwatch.ms() / 50); // 50 ミリ秒につき 1 文字 
		m_font(m_text.substr(0, count)).draw(40, 460, ColorF{ 0.1 });
	}

private:

	Font m_font{ 25 };

	String m_text;

	Stopwatch m_stopwatch;
};

void Main()
{
	Scene::SetBackground(ColorF{ 0.9, 0.85, 0.8 });

	Item computer{ U"🖥️"_emoji, Vec2{ 200, 300 }, true };
	Item box{ U"🗃️"_emoji, Vec2{ 400, 300 }, true };
	Item door{ U"🚪"_emoji, Vec2{ 600, 300 }, true };
	Item food{ U"🍲"_emoji, Vec2{ 400, 300 }, false };

	MessageWindow messageWindow;

	bool hasKey = false;

	while (System::Update())
	{
		computer.draw();
		box.draw();
		door.draw();
		food.draw();

		if (computer.clicked())
		{
			messageWindow.setText(U"パソコンだ。電源は入らない。");
		}

		if (box.clicked())
		{
			if (not hasKey)
			{
				messageWindow.setText(U"箱の中に何か入っている。\n[鍵を手に入れた！]");

				hasKey = true;
			}
			else
			{
				messageWindow.setText(U"箱の中には重要そうなものは入っていない。");
			}
		}

		if (door.clicked())
		{
			if (not hasKey)
			{
				messageWindow.setText(U"ドアには鍵がかかっている。出られない。");
			}
			else
			{
				messageWindow.setText(U"鍵を使ってドアを開けた！");

				computer.setVisibility(false);
				box.setVisibility(false);
				door.setVisibility(false);
				food.setVisibility(true);
			}
		}

		if (food.clicked())
		{
			messageWindow.setText(U"おいしそうな料理が置かれている。まだ温かい。");
		}

		messageWindow.draw();
	}
}
```
