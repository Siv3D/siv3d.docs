
!!! warning "このページよりも新しいドキュメントがあります"
	このドキュメントは古い OpenSiv3D v0.4.3 向けです。2021 年9 月 18 日に最新の OpenSiv3D v0.6.0 がリリースされました。最新のドキュメントは [Siv3D リファレンス v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation) です。

# JSON ファイル
## JSON ファイルからデータセットを読み込む
```C++
# include <Siv3D.hpp>

struct Country
{
	String code;
	String name;
	String native;
	String phone;
	String continent;
	String capital;
	String currency;
	Array<String> languages;
};

void CodeToFlag(const String& code, DynamicTexture& texture)
{
	if (code.size() != 2)
	{
		return;
	}

	const String emoji = { char32(0x1F1E6 + (code[0] - U'A')), char32(0x1F1E6 + (code[1] - U'A')) };
	texture.fill(Emoji::CreateImage(emoji));
}

void Main()
{
	const JSONReader json(U"example/countries/countries.json");

	if (!json)
	{
		throw Error(U"Failed to load `countries.json`");
	}

	Array<Country> countries;

	for (const auto& object : json.objectView())
	{
		Country country;
		country.code		= object.name;
		country.name		= object.value[U"name"].getString();
		country.native		= object.value[U"native"].getString();
		country.phone		= object.value[U"phone"].getString();
		country.continent	= object.value[U"continent"].getString();
		country.capital		= object.value[U"capital"].getString();
		country.currency	= object.value[U"currency"].getString();
		for (const auto& language : object.value[U"languages"].arrayView())
		{
			country.languages << language.getString();
		}

		countries << country;
	}

	constexpr ColorF textColor(0.15);
	const Font fontBig(32, Typeface::Heavy);
	const Font fontMedium(26);
	DynamicTexture flagTexture;
	CodeToFlag(countries.front().code, flagTexture);

	size_t index = 0;

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Previous", Vec2(40, 420)))
		{
			index = (index + (countries.size() - 1)) % countries.size();
			CodeToFlag(countries[index].code, flagTexture);
		}

		if (SimpleGUI::Button(U"Next", Vec2(680, 420)))
		{
			++index %= countries.size();
			CodeToFlag(countries[index].code, flagTexture);
		}

		const Country& country = countries[index];

		Rect(10, 10, 780, 400).draw();
		flagTexture.draw(20, 20);
		fontBig(country.name).draw(170, 60, textColor);
		fontMedium(U"native: ", country.native).draw(30, 140, textColor);
		fontMedium(U"phone: ", country.phone).draw(30, 180, textColor);
		fontMedium(U"continent: ", country.continent).draw(30, 220, textColor);
		fontMedium(U"capital: ", country.capital).draw(30, 260, textColor);
		fontMedium(U"currency: ", country.currency).draw(30, 300, textColor);
		fontMedium(U"languages: ", country.languages).draw(30, 340, textColor);
	}
}
```
