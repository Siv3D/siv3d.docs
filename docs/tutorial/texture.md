
!!! warning "This is the documentation for an old version"
	This is the documentation for an old version of Siv3D (v0.4.3). See [Siv3D Reference v0.6.0](https://zenn.dev/reputeless/books/siv3d-documentation-en) for the latest version.

# 5. Drawing images

この章では、絵文字やアイコン、画像ファイルから読み込んだ画像を画面に描く方法を学びます。

## 5.1 絵文字を描画する
画面に画像を描きたいときは `Texture` を作成し、`.draw()` または `.drawAt()` します。`Texture` は、画像ファイルから画像データを読み込んで作成したり、プログラムで作成した画像データから作成したり、Siv3D が標準で提供している絵文字やアイコンコレクションから作成することができます。

この章の最初では、絵文字コレクションから好きな絵文字を選んで `Texture` を作成し、それを描画するプログラムを書いてみましょう。`Texture` の作成にはメモリ確保などの実行時負荷がかかります。メインループの中で毎フレーム新しい `Texture` を作成するのは避け、作成が 1 回だけで済むようにしましょう。

絵文字コレクションから `Texture` を作成するには、`Texture` のコンストラクタ引数に `Emoji()` を渡します。

### Texture::drawAt()
`.drawAt()` では、テクスチャの中心をどこに据えるかを画面の座標で指定します。絵文字やアイコンを描く場合はこちらが便利です。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/1-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	// 🐈 の絵文字からテクスチャを作成
	const Texture texture(Emoji(U"🐈"));

	while (System::Update())
	{
		// テクスチャを座標 (0, 0) を中心に描画
		texture.drawAt(0, 0);

		// テクスチャを画面中央を中心に描画
		texture.drawAt(Scene::Center());
	}
}
```

### Texture::draw()
`.draw()` では、テクスチャの左上をどこに据えるかを画面の座標で指定します。背景画像や UI などを描くときにはこちらが便利な場合があります。サンプル画像では、わかりやすいよう座標を赤丸で表示し、テクスチャの境界線を白線で表示しています。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/1-1.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	// 🐈 の絵文字からテクスチャを作成
	const Texture texture(Emoji(U"🐈"));

	while (System::Update())
	{
		// テクスチャを座標 (0, 0) から描画
		texture.draw(0, 0);

		// テクスチャを画面中心から描画
		texture.draw(Scene::Center());
	}
}
```

### Siv3D で使える絵文字の一覧
OpenSiv3D で使える絵文字は約 2,200 種類あります。ここからプログラムにコピーして使えます。

**感情・人**  
😀 😁 😂 🤣 😃 😄 😅 😆 😉 😊 😋 😎 😍 😘 😗 😙 😚 ☺ 🙂 🤗 🤔 😐 😑 😶 🙄 😏 😣 😥 😮 🤐 😯 😪 😫 😴 😌 🤓 😛 😜 😝 🤤 😒 😓 😔 😕 🙃 🤑 😲 ☹ 🙁 😖 😞 😟 😤 😢 😭 😦 😧 😨 😩 😬 😰 😱 😳 😵 😡 😠 😇 🤠 🤡 🤥 😷 🤒 🤕 🤢 🤧 😈 👿 👹 👺 💀 ☠ 👻 👽 👾 🤖 💩 😺 😸 😹 😻 😼 😽 🙀 😿 😾 🙈 🙉 🙊 👦 👦🏻 👦🏼 👦🏽 👦🏾 👦🏿 👧 👧🏻 👧🏼 👧🏽 👧🏾 👧🏿 👨 👨🏻 👨🏼 👨🏽 👨🏾 👨🏿 👩 👩🏻 👩🏼 👩🏽 👩🏾 👩🏿 👴 👴🏻 👴🏼 👴🏽 👴🏾 👴🏿 👵 👵🏻 👵🏼 👵🏽 👵🏾 👵🏿 👶 👶🏻 👶🏼 👶🏽 👶🏾 👶🏿 👼 👼🏻 👼🏼 👼🏽 👼🏾 👼🏿 👨‍⚕ 👨🏻‍⚕ 👨🏼‍⚕ 👨🏽‍⚕ 👨🏾‍⚕ 👨🏿‍⚕ 👩‍⚕ 👩🏻‍⚕ 👩🏼‍⚕ 👩🏽‍⚕ 👩🏾‍⚕ 👩🏿‍⚕ 👨‍🎓 👨🏻‍🎓 👨🏼‍🎓 👨🏽‍🎓 👨🏾‍🎓 👨🏿‍🎓 👩‍🎓 👩🏻‍🎓 👩🏼‍🎓 👩🏽‍🎓 👩🏾‍🎓 👩🏿‍🎓 👨‍🏫 👨🏻‍🏫 👨🏼‍🏫 👨🏽‍🏫 👨🏾‍🏫 👨🏿‍🏫 👩‍🏫 👩🏻‍🏫 👩🏼‍🏫 👩🏽‍🏫 👩🏾‍🏫 👩🏿‍🏫 👨‍⚖ 👨🏻‍⚖ 👨🏼‍⚖ 👨🏽‍⚖ 👨🏾‍⚖ 👨🏿‍⚖ 👩‍⚖ 👩🏻‍⚖ 👩🏼‍⚖ 👩🏽‍⚖ 👩🏾‍⚖ 👩🏿‍⚖ 👨‍🌾 👨🏻‍🌾 👨🏼‍🌾 👨🏽‍🌾 👨🏾‍🌾 👨🏿‍🌾 👩‍🌾 👩🏻‍🌾 👩🏼‍🌾 👩🏽‍🌾 👩🏾‍🌾 👩🏿‍🌾 👨‍🍳 👨🏻‍🍳 👨🏼‍🍳 👨🏽‍🍳 👨🏾‍🍳 👨🏿‍🍳 👩‍🍳 👩🏻‍🍳 👩🏼‍🍳 👩🏽‍🍳 👩🏾‍🍳 👩🏿‍🍳 👨‍🔧 👨🏻‍🔧 👨🏼‍🔧 👨🏽‍🔧 👨🏾‍🔧 👨🏿‍🔧 👩‍🔧 👩🏻‍🔧 👩🏼‍🔧 👩🏽‍🔧 👩🏾‍🔧 👩🏿‍🔧 👨‍🏭 👨🏻‍🏭 👨🏼‍🏭 👨🏽‍🏭 👨🏾‍🏭 👨🏿‍🏭 👩‍🏭 👩🏻‍🏭 👩🏼‍🏭 👩🏽‍🏭 👩🏾‍🏭 👩🏿‍🏭 👨‍💼 👨🏻‍💼 👨🏼‍💼 👨🏽‍💼 👨🏾‍💼 👨🏿‍💼 👩‍💼 👩🏻‍💼 👩🏼‍💼 👩🏽‍💼 👩🏾‍💼 👩🏿‍💼 👨‍🔬 👨🏻‍🔬 👨🏼‍🔬 👨🏽‍🔬 👨🏾‍🔬 👨🏿‍🔬 👩‍🔬 👩🏻‍🔬 👩🏼‍🔬 👩🏽‍🔬 👩🏾‍🔬 👩🏿‍🔬 👨‍💻 👨🏻‍💻 👨🏼‍💻 👨🏽‍💻 👨🏾‍💻 👨🏿‍💻 👩‍💻 👩🏻‍💻 👩🏼‍💻 👩🏽‍💻 👩🏾‍💻 👩🏿‍💻 👨‍🎤 👨🏻‍🎤 👨🏼‍🎤 👨🏽‍🎤 👨🏾‍🎤 👨🏿‍🎤 👩‍🎤 👩🏻‍🎤 👩🏼‍🎤 👩🏽‍🎤 👩🏾‍🎤 👩🏿‍🎤 👨‍🎨 👨🏻‍🎨 👨🏼‍🎨 👨🏽‍🎨 👨🏾‍🎨 👨🏿‍🎨 👩‍🎨 👩🏻‍🎨 👩🏼‍🎨 👩🏽‍🎨 👩🏾‍🎨 👩🏿‍🎨 👨‍✈ 👨🏻‍✈ 👨🏼‍✈ 👨🏽‍✈ 👨🏾‍✈ 👨🏿‍✈ 👩‍✈ 👩🏻‍✈ 👩🏼‍✈ 👩🏽‍✈ 👩🏾‍✈ 👩🏿‍✈ 👨‍🚀 👨🏻‍🚀 👨🏼‍🚀 👨🏽‍🚀 👨🏾‍🚀 👨🏿‍🚀 👩‍🚀 👩🏻‍🚀 👩🏼‍🚀 👩🏽‍🚀 👩🏾‍🚀 👩🏿‍🚀 👨‍🚒 👨🏻‍🚒 👨🏼‍🚒 👨🏽‍🚒 👨🏾‍🚒 👨🏿‍🚒 👩‍🚒 👩🏻‍🚒 👩🏼‍🚒 👩🏽‍🚒 👩🏾‍🚒 👩🏿‍🚒 👮‍♂ 👮🏻‍♂ 👮🏼‍♂ 👮🏽‍♂ 👮🏾‍♂ 👮🏿‍♂ 👮‍♀ 👮🏻‍♀ 👮🏼‍♀ 👮🏽‍♀ 👮🏾‍♀ 👮🏿‍♀ 🕵‍♂ 🕵🏻‍♂ 🕵🏼‍♂ 🕵🏽‍♂ 🕵🏾‍♂ 🕵🏿‍♂ 🕵‍♀ 🕵🏻‍♀ 🕵🏼‍♀ 🕵🏽‍♀ 🕵🏾‍♀ 🕵🏿‍♀ 💂‍♂ 💂🏻‍♂ 💂🏼‍♂ 💂🏽‍♂ 💂🏾‍♂ 💂🏿‍♂ 💂‍♀ 💂🏻‍♀ 💂🏼‍♀ 💂🏽‍♀ 💂🏾‍♀ 💂🏿‍♀ 👷‍♂ 👷🏻‍♂ 👷🏼‍♂ 👷🏽‍♂ 👷🏾‍♂ 👷🏿‍♂ 👷‍♀ 👷🏻‍♀ 👷🏼‍♀ 👷🏽‍♀ 👷🏾‍♀ 👷🏿‍♀ 👳‍♂ 👳🏻‍♂ 👳🏼‍♂ 👳🏽‍♂ 👳🏾‍♂ 👳🏿‍♂ 👳‍♀ 👳🏻‍♀ 👳🏼‍♀ 👳🏽‍♀ 👳🏾‍♀ 👳🏿‍♀ 👱‍♂ 👱🏻‍♂ 👱🏼‍♂ 👱🏽‍♂ 👱🏾‍♂ 👱🏿‍♂ 👱‍♀ 👱🏻‍♀ 👱🏼‍♀ 👱🏽‍♀ 👱🏾‍♀ 👱🏿‍♀ 🎅 🎅🏻 🎅🏼 🎅🏽 🎅🏾 🎅🏿 🤶 🤶🏻 🤶🏼 🤶🏽 🤶🏾 🤶🏿 👸 👸🏻 👸🏼 👸🏽 👸🏾 👸🏿 🤴 🤴🏻 🤴🏼 🤴🏽 🤴🏾 🤴🏿 👰 👰🏻 👰🏼 👰🏽 👰🏾 👰🏿 🤵 🤵🏻 🤵🏼 🤵🏽 🤵🏾 🤵🏿 🤰 🤰🏻 🤰🏼 🤰🏽 🤰🏾 🤰🏿 👲 👲🏻 👲🏼 👲🏽 👲🏾 👲🏿 🙍‍♂ 🙍🏻‍♂ 🙍🏼‍♂ 🙍🏽‍♂ 🙍🏾‍♂ 🙍🏿‍♂ 🙍‍♀ 🙍🏻‍♀ 🙍🏼‍♀ 🙍🏽‍♀ 🙍🏾‍♀ 🙍🏿‍♀ 🙎‍♂ 🙎🏻‍♂ 🙎🏼‍♂ 🙎🏽‍♂ 🙎🏾‍♂ 🙎🏿‍♂ 🙎‍♀ 🙎🏻‍♀ 🙎🏼‍♀ 🙎🏽‍♀ 🙎🏾‍♀ 🙎🏿‍♀ 🙅‍♂ 🙅🏻‍♂ 🙅🏼‍♂ 🙅🏽‍♂ 🙅🏾‍♂ 🙅🏿‍♂ 🙅‍♀ 🙅🏻‍♀ 🙅🏼‍♀ 🙅🏽‍♀ 🙅🏾‍♀ 🙅🏿‍♀ 🙆‍♂ 🙆🏻‍♂ 🙆🏼‍♂ 🙆🏽‍♂ 🙆🏾‍♂ 🙆🏿‍♂ 🙆‍♀ 🙆🏻‍♀ 🙆🏼‍♀ 🙆🏽‍♀ 🙆🏾‍♀ 🙆🏿‍♀ 💁‍♂ 💁🏻‍♂ 💁🏼‍♂ 💁🏽‍♂ 💁🏾‍♂ 💁🏿‍♂ 💁‍♀ 💁🏻‍♀ 💁🏼‍♀ 💁🏽‍♀ 💁🏾‍♀ 💁🏿‍♀ 🙋‍♂ 🙋🏻‍♂ 🙋🏼‍♂ 🙋🏽‍♂ 🙋🏾‍♂ 🙋🏿‍♂ 🙋‍♀ 🙋🏻‍♀ 🙋🏼‍♀ 🙋🏽‍♀ 🙋🏾‍♀ 🙋🏿‍♀ 🙇‍♂ 🙇🏻‍♂ 🙇🏼‍♂ 🙇🏽‍♂ 🙇🏾‍♂ 🙇🏿‍♂ 🙇‍♀ 🙇🏻‍♀ 🙇🏼‍♀ 🙇🏽‍♀ 🙇🏾‍♀ 🙇🏿‍♀ 🤦‍♂ 🤦🏻‍♂ 🤦🏼‍♂ 🤦🏽‍♂ 🤦🏾‍♂ 🤦🏿‍♂ 🤦‍♀ 🤦🏻‍♀ 🤦🏼‍♀ 🤦🏽‍♀ 🤦🏾‍♀ 🤦🏿‍♀ 🤷‍♂ 🤷🏻‍♂ 🤷🏼‍♂ 🤷🏽‍♂ 🤷🏾‍♂ 🤷🏿‍♂ 🤷‍♀ 🤷🏻‍♀ 🤷🏼‍♀ 🤷🏽‍♀ 🤷🏾‍♀ 🤷🏿‍♀ 💆‍♂ 💆🏻‍♂ 💆🏼‍♂ 💆🏽‍♂ 💆🏾‍♂ 💆🏿‍♂ 💆‍♀ 💆🏻‍♀ 💆🏼‍♀ 💆🏽‍♀ 💆🏾‍♀ 💆🏿‍♀ 💇‍♂ 💇🏻‍♂ 💇🏼‍♂ 💇🏽‍♂ 💇🏾‍♂ 💇🏿‍♂ 💇‍♀ 💇🏻‍♀ 💇🏼‍♀ 💇🏽‍♀ 💇🏾‍♀ 💇🏿‍♀ 🚶‍♂ 🚶🏻‍♂ 🚶🏼‍♂ 🚶🏽‍♂ 🚶🏾‍♂ 🚶🏿‍♂ 🚶‍♀ 🚶🏻‍♀ 🚶🏼‍♀ 🚶🏽‍♀ 🚶🏾‍♀ 🚶🏿‍♀ 🏃‍♂ 🏃🏻‍♂ 🏃🏼‍♂ 🏃🏽‍♂ 🏃🏾‍♂ 🏃🏿‍♂ 🏃‍♀ 🏃🏻‍♀ 🏃🏼‍♀ 🏃🏽‍♀ 🏃🏾‍♀ 🏃🏿‍♀ 💃 💃🏻 💃🏼 💃🏽 💃🏾 💃🏿 🕺 🕺🏻 🕺🏼 🕺🏽 🕺🏾 🕺🏿 👯‍♂ 👯‍♀ 🕴 🗣 👤 👥 🤺 🏇 ⛷ 🏂 🏌‍♂ 🏌‍♀ 🏄‍♂ 🏄🏻‍♂ 🏄🏼‍♂ 🏄🏽‍♂ 🏄🏾‍♂ 🏄🏿‍♂ 🏄‍♀ 🏄🏻‍♀ 🏄🏼‍♀ 🏄🏽‍♀ 🏄🏾‍♀ 🏄🏿‍♀ 🚣‍♂ 🚣🏻‍♂ 🚣🏼‍♂ 🚣🏽‍♂ 🚣🏾‍♂ 🚣🏿‍♂ 🚣‍♀ 🚣🏻‍♀ 🚣🏼‍♀ 🚣🏽‍♀ 🚣🏾‍♀ 🚣🏿‍♀ 🏊‍♂ 🏊🏻‍♂ 🏊🏼‍♂ 🏊🏽‍♂ 🏊🏾‍♂ 🏊🏿‍♂ 🏊‍♀ 🏊🏻‍♀ 🏊🏼‍♀ 🏊🏽‍♀ 🏊🏾‍♀ 🏊🏿‍♀ ⛹‍♂ ⛹🏻‍♂ ⛹🏼‍♂ ⛹🏽‍♂ ⛹🏾‍♂ ⛹🏿‍♂ ⛹‍♀ ⛹🏻‍♀ ⛹🏼‍♀ ⛹🏽‍♀ ⛹🏾‍♀ ⛹🏿‍♀ 🏋‍♂ 🏋🏻‍♂ 🏋🏼‍♂ 🏋🏽‍♂ 🏋🏾‍♂ 🏋🏿‍♂ 🏋‍♀ 🏋🏻‍♀ 🏋🏼‍♀ 🏋🏽‍♀ 🏋🏾‍♀ 🏋🏿‍♀ 🚴‍♂ 🚴🏻‍♂ 🚴🏼‍♂ 🚴🏽‍♂ 🚴🏾‍♂ 🚴🏿‍♂ 🚴‍♀ 🚴🏻‍♀ 🚴🏼‍♀ 🚴🏽‍♀ 🚴🏾‍♀ 🚴🏿‍♀ 🚵‍♂ 🚵🏻‍♂ 🚵🏼‍♂ 🚵🏽‍♂ 🚵🏾‍♂ 🚵🏿‍♂ 🚵‍♀ 🚵🏻‍♀ 🚵🏼‍♀ 🚵🏽‍♀ 🚵🏾‍♀ 🚵🏿‍♀ 🏎 🏍 🤸‍♂ 🤸🏻‍♂ 🤸🏼‍♂ 🤸🏽‍♂ 🤸🏾‍♂ 🤸🏿‍♂ 🤸‍♀ 🤸🏻‍♀ 🤸🏼‍♀ 🤸🏽‍♀ 🤸🏾‍♀ 🤸🏿‍♀ 🤼‍♂ 🤼‍♀ 🤽‍♂ 🤽🏻‍♂ 🤽🏼‍♂ 🤽🏽‍♂ 🤽🏾‍♂ 🤽🏿‍♂ 🤽‍♀ 🤽🏻‍♀ 🤽🏼‍♀ 🤽🏽‍♀ 🤽🏾‍♀ 🤽🏿‍♀ 🤾‍♂ 🤾🏻‍♂ 🤾🏼‍♂ 🤾🏽‍♂ 🤾🏾‍♂ 🤾🏿‍♂ 🤾‍♀ 🤾🏻‍♀ 🤾🏼‍♀ 🤾🏽‍♀ 🤾🏾‍♀ 🤾🏿‍♀ 🤹‍♂ 🤹🏻‍♂ 🤹🏼‍♂ 🤹🏽‍♂ 🤹🏾‍♂ 🤹🏿‍♂ 🤹‍♀ 🤹🏻‍♀ 🤹🏼‍♀ 🤹🏽‍♀ 🤹🏾‍♀ 🤹🏿‍♀ 👫 👬 👭 👩‍❤‍💋‍👨 👩‍❤‍👨 👪 👨‍👩‍👦 👨‍👩‍👧 👨‍👩‍👧‍👦 👨‍👩‍👦‍👦 👨‍👩‍👧‍👧 👨‍👨‍👦 👨‍👨‍👧 👨‍👨‍👧‍👦 👨‍👨‍👦‍👦 👨‍👨‍👧‍👧 👩‍👩‍👦 👩‍👩‍👧 👩‍👩‍👧‍👦 👩‍👩‍👦‍👦 👩‍👩‍👧‍👧 👨‍👦 👨‍👦‍👦 👨‍👧 👨‍👧‍👦 👨‍👧‍👧 👩‍👦 👩‍👦‍👦 👩‍👧 👩‍👧‍👦 👩‍👧‍👧

**体・装飾**  
🏻 🏼 🏽 🏾 🏿 💪 💪🏻 💪🏼 💪🏽 💪🏾 💪🏿 🤳 🤳🏻 🤳🏼 🤳🏽 🤳🏾 🤳🏿 👈 👈🏻 👈🏼 👈🏽 👈🏾 👈🏿 👉 👉🏻 👉🏼 👉🏽 👉🏾 👉🏿 ☝ ☝🏻 ☝🏼 ☝🏽 ☝🏾 ☝🏿 👆 👆🏻 👆🏼 👆🏽 👆🏾 👆🏿 🖕 🖕🏻 🖕🏼 🖕🏽 🖕🏾 🖕🏿 👇 👇🏻 👇🏼 👇🏽 👇🏾 👇🏿 ✌ ✌🏻 ✌🏼 ✌🏽 ✌🏾 ✌🏿 🤞 🤞🏻 🤞🏼 🤞🏽 🤞🏾 🤞🏿 🖖 🖖🏻 🖖🏼 🖖🏽 🖖🏾 🖖🏿 🤘 🤘🏻 🤘🏼 🤘🏽 🤘🏾 🤘🏿 🤙 🤙🏻 🤙🏼 🤙🏽 🤙🏾 🤙🏿 🖐 🖐🏻 🖐🏼 🖐🏽 🖐🏾 🖐🏿 ✋ ✋🏻 ✋🏼 ✋🏽 ✋🏾 ✋🏿 👌 👌🏻 👌🏼 👌🏽 👌🏾 👌🏿 👍 👍🏻 👍🏼 👍🏽 👍🏾 👍🏿 👎 👎🏻 👎🏼 👎🏽 👎🏾 👎🏿 ✊ ✊🏻 ✊🏼 ✊🏽 ✊🏾 ✊🏿 👊 👊🏻 👊🏼 👊🏽 👊🏾 👊🏿 🤛 🤛🏻 🤛🏼 🤛🏽 🤛🏾 🤛🏿 🤜 🤜🏻 🤜🏼 🤜🏽 🤜🏾 🤜🏿 🤚 🤚🏻 🤚🏼 🤚🏽 🤚🏾 🤚🏿 👋 👋🏻 👋🏼 👋🏽 👋🏾 👋🏿 👏 👏🏻 👏🏼 👏🏽 👏🏾 👏🏿 ✍ ✍🏻 ✍🏼 ✍🏽 ✍🏾 ✍🏿 👐 👐🏻 👐🏼 👐🏽 👐🏾 👐🏿 🙌 🙌🏻 🙌🏼 🙌🏽 🙌🏾 🙌🏿 🙏 🙏🏻 🙏🏼 🙏🏽 🙏🏾 🙏🏿 🤝 💅 💅🏻 💅🏼 💅🏽 💅🏾 💅🏿 👂 👂🏻 👂🏼 👂🏽 👂🏾 👂🏿 👃 👃🏻 👃🏼 👃🏽 👃🏾 👃🏿 👣 👀 👁 👁‍🗨 👅 👄 💋 💘 ❤ 💓 💔 💕 💖 💗 💙 💚 💛 💜 🖤 💝 💞 💟 ❣ 💌 💤 💢 💣 💥 💦 💨 💫 💬 🗨 🗯 💭 🕳 👓 🕶 👔 👕 👖 👗 👘 👙 👚 👛 👜 👝 🛍 🎒 👞 👟 👠 👡 👢 👑 👒 🎩 🎓 ⛑ 📿 💄 💍 💎

**動物**  
🐵 🐒 🦍 🐶 🐕 🐩 🐺 🦊 🐱 🐈 🦁 🐯 🐅 🐆 🐴 🐎 🦌 🦄 🐮 🐂 🐃 🐄 🐷 🐖 🐗 🐽 🐏 🐑 🐐 🐪 🐫 🐘 🦏 🐭 🐁 🐀 🐹 🐰 🐇 🐿 🦇 🐻 🐨 🐼 🐾 🦃 🐔 🐓 🐣 🐤 🐥 🐦 🐧 🕊 🦅 🦆 🦉 🐸 🐊 🐢 🦎 🐍 🐲 🐉 🐳 🐋 🐬 🐟 🐠 🐡 🦈 🐙 🐚 🦀 🦐 🦑 🦋 🐌 🐛 🐜 🐝 🐞 🕷 🕸 🦂

**植物**  
💐 🌸 💮 🏵 🌹 🥀 🌺 🌻 🌼 🌷 🌱 🌲 🌳 🌴 🌵 🌾 🌿 ☘ 🍀 🍁 🍂 🍃

**食べ物・食事**  
🍇 🍈 🍉 🍊 🍋 🍌 🍍 🍎 🍏 🍐 🍑 🍒 🍓 🥝 🍅 🥑 🍆 🥔 🥕 🌽 🌶 🥒 🍄 🥜 🌰 🍞 🥐 🥖 🥞 🧀 🍖 🍗 🥓 🍔 🍟 🍕 🌭 🌮 🌯 🥙 🥚 🍳 🥘 🍲 🥗 🍿 🍱 🍘 🍙 🍚 🍛 🍜 🍝 🍠 🍢 🍣 🍤 🍥 🍡 🍦 🍧 🍨 🍩 🍪 🎂 🍰 🍫 🍬 🍭 🍮 🍯 🍼 🥛 ☕ 🍵 🍶 🍾 🍷 🍸 🍹 🍺 🍻 🥂 🥃 🍽 🍴 🥄 🔪 🏺

**旅行・場所**  
🌍 🌎 🌏 🌐 🗺 🗾 🏔 ⛰ 🌋 🗻 🏕 🏖 🏜 🏝 🏞 🏟 🏛 🏗 🏘 🏙 🏚 🏠 🏡 🏢 🏣 🏤 🏥 🏦 🏨 🏩 🏪 🏫 🏬 🏭 🏯 🏰 💒 🗼 🗽 ⛪ 🕌 🕍 ⛩ 🕋 ⛲ ⛺ 🌁 🌃 🌄 🌅 🌆 🌇 🌉 ♨ 🌌 🎠 🎡 🎢 💈 🎪 🎭 🖼 🎨 🎰

**乗り物**  
🚂 🚃 🚄 🚅 🚆 🚇 🚈 🚉 🚊 🚝 🚞 🚋 🚌 🚍 🚎 🚐 🚑 🚒 🚓 🚔 🚕 🚖 🚗 🚘 🚙 🚚 🚛 🚜 🚲 🛴 🛵 🚏 🛣 🛤 ⛽ 🚨 🚥 🚦 🚧 🛑 ⚓ ⛵ 🛶 🚤 🛳 ⛴ 🛥 🚢 ✈ 🛩 🛫 🛬 💺 🚁 🚟 🚠 🚡 🚀 🛰

**オブジェクト**  
🛎 🚪 🛌 🛏 🛋 🚽 🚿 🛀 🛀🏻 🛀🏼 🛀🏽 🛀🏾 🛀🏿 🛁 ⌛ ⏳ ⌚ ⏰ ⏱ ⏲ 🕰 🕛 🕧 🕐 🕜 🕑 🕝 🕒 🕞 🕓 🕟 🕔 🕠 🕕 🕡 🕖 🕢 🕗 🕣 🕘 🕤 🕙 🕥 🕚 🕦 🌑 🌒 🌓 🌔 🌕 🌖 🌗 🌘 🌙 🌚 🌛 🌜 🌡 ☀ 🌝 🌞 ⭐ 🌟 🌠 ☁ ⛅ ⛈ 🌤 🌥 🌦 🌧 🌨 🌩 🌪 🌫 🌬 🌀 🌈 🌂 ☂ ☔ ⛱ ⚡ ❄ ☃ ⛄ ☄ 🔥 💧 🌊 🎃 🎄 🎆 🎇 ✨ 🎈 🎉 🎊 🎋 🎍 🎎 🎏 🎐 🎑 🎀 🎁 🎗 🎟 🎫 🎖 🏆 🏅 🥇 🥈 🥉 ⚽ ⚾ 🏀 🏐 🏈 🏉 🎾 🎱 🎳 🏏 🏑 🏒 🏓 🏸 🥊 🥋 🥅 🎯 ⛳ ⛸ 🎣 🎽 🎿 🎮 🕹 🎲 ♠ ♥ ♦ ♣ 🃏 🀄 🎴 🔇 🔈 🔉 🔊 📢 📣 📯 🔔 🔕 🎼 🎵 🎶 🎙 🎚 🎛 🎤 🎧 📻 🎷 🎸 🎹 🎺 🎻 🥁 📱 📲 ☎ 📞 📟 📠 🔋 🔌 💻 🖥 🖨 ⌨ 🖱 🖲 💽 💾 💿 📀 🎥 🎞 📽 🎬 📺 📷 📸 📹 📼 🔍 🔎 🔬 🔭 📡 🕯 💡 🔦 🏮 📔 📕 📖 📗 📘 📙 📚 📓 📒 📃 📜 📄 📰 🗞 📑 🔖 🏷 💰 💴 💵 💶 💷 💸 💳 💹 💱 💲 ✉ 📧 📨 📩 📤 📥 📦 📫 📪 📬 📭 📮 🗳 ✏ ✒ 🖋 🖊 🖌 🖍 📝 💼 📁 📂 🗂 📅 📆 🗒 🗓 📇 📈 📉 📊 📋 📌 📍 📎 🖇 📏 📐 ✂ 🗃 🗄 🗑 🔒 🔓 🔏 🔐 🔑 🗝 🔨 ⛏ ⚒ 🛠 🗡 ⚔ 🔫 🏹 🛡 🔧 🔩 ⚙ 🗜 ⚗ ⚖ 🔗 ⛓ 💉 💊 🚬 ⚰ ⚱ 🗿 🛢 🔮 🛒

**シンボル**  
🏧 🚮 🚰 ♿ 🚹 🚺 🚻 🚼 🚾 🛂 🛃 🛄 🛅 ⚠ 🚸 ⛔ 🚫 🚳 🚭 🚯 🚱 🚷 📵 🔞 ☢ ☣ ⬆ ↗ ➡ ↘ ⬇ ↙ ⬅ ↖ ↕ ↔ ↩ ↪ ⤴ ⤵ 🔃 🔄 🔙 🔚 🔛 🔜 🔝 🛐 ⚛ 🕉 ✡ ☸ ☯ ✝ ☦ ☪ ☮ 🕎 🔯 ♈ ♉ ♊ ♋ ♌ ♍ ♎ ♏ ♐ ♑ ♒ ♓ ⛎ 🔀 🔁 🔂 ▶ ⏩ ⏭ ⏯ ◀ ⏪ ⏮ 🔼 ⏫ 🔽 ⏬ ⏸ ⏹ ⏺ ⏏ 🎦 🔅 🔆 📶 📳 📴 ♻ 📛 ⚜ 🔰 🔱 ⭕ ✅ ☑ ✔ ✖ ❌ ❎ ➕ ♀ ♂ ⚕ ➖ ➗ ➰ ➿ 〽 ✳ ✴ ❇ ‼ ⁉ ❓ ❔ ❕ ❗ 〰 © ® ™ #⃣ *⃣ 0⃣ 1⃣ 2⃣ 3⃣ 4⃣ 5⃣ 6⃣ 7⃣ 8⃣ 9⃣ 🔟 💯 🔠 🔡 🔢 🔣 🔤 🅰 🆎 🅱 🆑 🆒 🆓 ℹ 🆔 Ⓜ 🆕 🆖 🅾 🆗 🅿 🆘 🆙 🆚 🈁 🈂 🈷 🈶 🈯 🉐 🈹 🈚 🈲 🉑 🈸 🈴 🈳 ㊗ ㊙ 🈺 🈵 ▪ ▫ ◻ ◼ ◽ ◾ ⬛ ⬜ 🔶 🔷 🔸 🔹 🔺 🔻 💠 🔘 🔲 🔳 ⚪ ⚫ 🔴 🔵

**旗**  
🏁 🚩 🎌 🏴 🏳 🏳‍🌈 🇦🇨 🇦🇩 🇦🇪 🇦🇫 🇦🇬 🇦🇮 🇦🇱 🇦🇲 🇦🇴 🇦🇶 🇦🇷 🇦🇸 🇦🇹 🇦🇺 🇦🇼 🇦🇽 🇦🇿 🇧🇦 🇧🇧 🇧🇩 🇧🇪 🇧🇫 🇧🇬 🇧🇭 🇧🇮 🇧🇯 🇧🇲 🇧🇳 🇧🇴 🇧🇷 🇧🇸 🇧🇹 🇧🇻 🇧🇼 🇧🇾 🇧🇿 🇨🇦 🇨🇨 🇨🇩 🇨🇫 🇨🇬 🇨🇭 🇨🇮 🇨🇰 🇨🇱 🇨🇲 🇨🇳 🇨🇴 🇨🇵 🇨🇷 🇨🇺 🇨🇻 🇨🇼 🇨🇽 🇨🇾 🇨🇿 🇩🇪 🇩🇯 🇩🇰 🇩🇲 🇩🇴 🇩🇿 🇪🇨 🇪🇪 🇪🇬 🇪🇷 🇪🇸 🇪🇹 🇪🇺 🇫🇮 🇫🇯 🇫🇲 🇫🇴 🇫🇷 🇬🇦 🇬🇧 🇬🇩 🇬🇪 🇬🇬 🇬🇭 🇬🇮 🇬🇱 🇬🇲 🇬🇳 🇬🇶 🇬🇷 🇬🇹 🇬🇺 🇬🇼 🇬🇾 🇭🇰 🇭🇲 🇭🇳 🇭🇷 🇭🇹 🇭🇺 🇮🇨 🇮🇩 🇮🇪 🇮🇱 🇮🇲 🇮🇳 🇮🇴 🇮🇶 🇮🇷 🇮🇸 🇮🇹 🇯🇪 🇯🇲 🇯🇴 🇯🇵 🇰🇪 🇰🇬 🇰🇭 🇰🇮 🇰🇲 🇰🇳 🇰🇵 🇰🇷 🇰🇼 🇰🇾 🇰🇿 🇱🇦 🇱🇧 🇱🇨 🇱🇮 🇱🇰 🇱🇷 🇱🇸 🇱🇹 🇱🇺 🇱🇻 🇱🇾 🇲🇦 🇲🇨 🇲🇩 🇲🇪 🇲🇬 🇲🇭 🇲🇰 🇲🇱 🇲🇲 🇲🇳 🇲🇴 🇲🇵 🇲🇷 🇲🇸 🇲🇹 🇲🇺 🇲🇻 🇲🇼 🇲🇽 🇲🇾 🇲🇿 🇳🇦 🇳🇪 🇳🇫 🇳🇬 🇳🇮 🇳🇱 🇳🇴 🇳🇵 🇳🇷 🇳🇺 🇳🇿 🇴🇲 🇵🇦 🇵🇪 🇵🇫 🇵🇬 🇵🇭 🇵🇰 🇵🇱 🇵🇳 🇵🇷 🇵🇸 🇵🇹 🇵🇼 🇵🇾 🇶🇦 🇷🇴 🇷🇸 🇷🇺 🇷🇼 🇸🇦 🇸🇧 🇸🇨 🇸🇩 🇸🇪 🇸🇬 🇸🇭 🇸🇮 🇸🇯 🇸🇰 🇸🇱 🇸🇲 🇸🇳 🇸🇴 🇸🇷 🇸🇸 🇸🇹 🇸🇻 🇸🇽 🇸🇾 🇸🇿 🇹🇦 🇹🇨 🇹🇩 🇹🇬 🇹🇭 🇹🇯 🇹🇰 🇹🇱 🇹🇲 🇹🇳 🇹🇴 🇹🇷 🇹🇹 🇹🇻 🇹🇼 🇹🇿 🇺🇦 🇺🇬 🇺🇲 🇺🇳 🇺🇸 🇺🇾 🇺🇿 🇻🇦 🇻🇨 🇻🇪 🇻🇬 🇻🇮 🇻🇳 🇻🇺 🇼🇸 🇾🇪 🇿🇦 🇿🇲 🇿🇼

!!! Info
    OpenSiv3D はオープンソースの絵文字フォント Noto Color Emoji (Android 7.1 版) を内蔵しているので、Siv3D アプリはどのプラットフォームでも同じ見た目の絵文字を表示できます。

## 5.2 テクスチャを拡大縮小して描画する

### Texture::scaled()
`Texture::scaled()` に拡大縮小倍率を指定すると、`Texture` にサイズ情報が付加された `TextureRegion` を作成できます。`TextureRegion` は `Texture` のように `.draw()` または `.drawAt()` できます。`Texture` を作成するのと異なり、既存の `Texture` から `TextureRegion` を作成するのは非常に軽い実行時負荷です。サンプルでは示していませんが、縦横で異なる倍率に設定することもできます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/2-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture cat(Emoji(U"🐈"));

	const Texture dog(Emoji(U"🐕"));

	while (System::Update())
	{
		// 2 倍に拡大して描画
		cat.scaled(2.0).drawAt(200, 300);

		// 1.5 倍に拡大して描画
		cat.scaled(1.5).drawAt(400, 300);

		// 半分のサイズに縮小して描画
		dog.scaled(0.5).drawAt(600, 300);
	}
}
```

### Texture::resized()
`Texture::resized()` は、拡大縮小後のサイズを倍率ではなくピクセル単位で指定します。Siv3D 標準の絵文字のサイズは幅が 136 ピクセルなので、136 より大きい数を指定すると拡大、小さい数を指定すると縮小表示になります。サンプルでは示していませんが、縦横で異なるサイズに設定することもできます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/2-1.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture cat(Emoji(U"🐈"));

	const Texture dog(Emoji(U"🐕"));

	while (System::Update())
	{
		// 300px に拡大して描画
		cat.resized(300).drawAt(200, 300);

		// 200px に拡大して描画
		cat.resized(200).drawAt(400, 300);

		// 20px に縮小して描画
		dog.resized(20).drawAt(600, 300);
	}
}
```

## 5.3 テクスチャを回転して描画する
`Texture::rotated()` または `Texture::rotatedAt()` によって、テクスチャに回転情報を付加した `TexturedQuad` を作成できます。`TexturedQuad` は `Texture` のように `.draw()` または `.drawAt()` できます。`TextureRegion` と同様、`TexturedQuad` を作成するのは非常に軽い実行時負荷です。

### Texture::rotated()
テクスチャの中心を軸にして回転します。回転角度はラジアンで指定します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/3-0.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture cat(Emoji(U"🐈"));

	while (System::Update())
	{
		// 毎秒 90° 時計回りに回転
		cat.rotated(Scene::Time() * 90_deg).drawAt(Scene::Center());
	}
}
```

### Texture::roatedAt()
テクスチャ上の指定した座標を軸にして回転します。Vec2(0, 0) を指定すればテクスチャの左上が軸になります。回転角度はラジアンで指定します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/3-1.gif?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture cat(Emoji(U"🐈"));

	while (System::Update())
	{
		// 画像中の (40, 20) を軸に回転させて描画
		cat.rotatedAt(Vec2(40, 20), Scene::Time() * 90_deg).draw(Scene::Center());
	}
}
```

## 5.4 テクスチャを上下・左右反転して描画する
`Texture::flipped()` で上下反転、`Texture::mirrored()` で左右反転した `TextureRegion` を作成できます。それぞれ、引数をとらずに反転する関数、引数の `bool` 値が `true` のときだけ反転する関数の 2 種類のオーバーロードがあります。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/4-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture cat(Emoji(U"🐈"));

	while (System::Update())
	{
		// 上下反転
		cat.flipped().drawAt(200, 300);

		// 左右反転
		cat.mirrored().drawAt(400, 300);

		// 反転を bool 値で指定するオーバーロード
		cat.mirrored(false).drawAt(600, 300);
	}
}
```

## 5.5 アイコンを描画する
アイコンコレクションから `Texture` を作成するには、`Texture` のコンストラクタ引数に `Icon()` を渡します。アイコンは全部で約 1,300 種類用意されています。

`Icon` のコンストラクタには、 [アイコン一覧](https://fontawesome.com/icons?d=gallery&s=brands,solid&m=free) で調べられる 16 進数コードと、アイコンのサイズ（ピクセル単位）を渡します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/5-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.92));

	// 歯車 (f013) のアイコン
	const Texture iconConfig(Icon(0xf013, 80));

	// 南京錠 (f023) のアイコン
	const Texture iconLock(Icon(0xf023, 80));

	// 拡大鏡 (f00e) のアイコン
	const Texture iconZoom(Icon(0xf00e, 80));

	while (System::Update())
	{
		iconConfig.drawAt(200, 300, ColorF(0.25));

		iconLock.scaled(1.5).drawAt(400, 300, ColorF(0.25));

		iconZoom.drawAt(600, 300, ColorF(0.25));
	}
}
```

テクスチャを描く際、`.draw()` や `.drawAt()` に、左上、右上、右下、左下の 4 つの頂点の色を渡してグラデーションで描くこともできます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/5-1.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.92));

	constexpr ColorF colorTop(0.6);
	
    constexpr ColorF colorBottom(0.1);

	// 歯車 (f013) のアイコン
	const Texture iconConfig(Icon(0xf013, 80));

	// 南京錠 (f023) のアイコン
	const Texture iconLock(Icon(0xf023, 80));

	// 拡大鏡 (f00e) のアイコン
	const Texture iconZoom(Icon(0xf00e, 80));

	while (System::Update())
	{
		iconConfig.drawAt(200, 300, colorTop, colorTop, colorBottom, colorBottom);

		iconLock.scaled(1.5).drawAt(400, 300, colorTop, colorTop, colorBottom, colorBottom);

		iconZoom.drawAt(600, 300, colorTop, colorTop, colorBottom, colorBottom);
	}
}
```


## 5.6 画像ファイルを読み込んで描画する
画像ファイルから `Texture` を作成するには、`Texture` のコンストラクタ引数に、読み込みたい画像ファイルのパスを渡します。このファイルパスは、実行ファイルがあるフォルダ（開発中は `App` フォルダ）を基準とする相対パスか、絶対パスを使用します。リリース用のアプリを作るときには、のちの章で説明する「リソース」パスの使用を推奨します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/6-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));

	// 風車の画像
	const Texture textureWindmill(U"example/windmill.png");

	// Siv3D くんの画像
	const Texture textureSiv3DKun(U"example/siv3d-kun.png");

	while (System::Update())
	{
		textureWindmill.draw(40, 20);

		textureSiv3DKun.draw(400, 100);
	}
}
```

!!! Info
    「Siv3D くん」は Siv3D の公式マスコットキャラクターです。

### 対応している画像フォーマット
OpenSiv3D v0.4.0 では、7 種類の画像フォーマットの読み込みがサポートされています。

| フォーマット   | 拡張子             | 対応状況       |
|----------|-----------------|:----------:|
| PNG      | png             | ✔          |
| JPEG     | jpg/jpeg        | ✔          |
| BMP      | bmp             | ✔          |
| GIF      | gif             | ✔          |
| TGA      | tga             | ✔          |
| PPM      | ppm/pgm/pbm/pnm | ✔          |
| WebP     | webp            | ✔          |
| JPEG2000 | jp2             | (将来のバージョン) |
| DDS      | dds             | (将来のバージョン) |
| TIFF     | tif/tiff        | (将来のバージョン) |


## 5.7 ミップマップの作成
テクスチャの作成時に **ミップマップ** を作成するオプションを設定していると、そのテクスチャを縮小して表示する際の画質や実行時性能が改善されます。ミップマップとは、きれいに縮小された画像を、テクスチャ作成時にあらかじめ生成しておく技術のことです。プログラムがテクスチャを縮小表示する際に、必要に応じてミップマップテクスチャが参照されます。画像の縮小表示をする多くのケースで、ミップマップ作成は最適な選択肢になります。なお、ミップマップを作成すると、テクスチャによって消費されるメモリは 30% ほど増加します。

画像ファイルから `Texture` を作成するコンストラクタでは、第 2 引数でミップマップのありなしを選択できます。デフォルトではミップマップを作成しない `TextureDesc::Unmipped` になっていて、明示的に `TextureDesc::Mipped` を指定したときにだけミップマップを作成します。なお、`Emoji` や `Icon` から `Texture` を作る場合にはデフォルトで `TextureDesc::Mipped` になっています。

次のサンプルで、ミップマップの効果を確認できます。ミップマップを作成していない左のテクスチャは、サイズの変化に伴いちらちらしたノイズが発生します。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/7-0.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	// ミップマップを作成しない設定
	const Texture textureUnmipped(U"example/windmill.png");

	// ミップマップを作成する設定
	const Texture textureMipped(U"example/windmill.png", TextureDesc::Mipped);

	while (System::Update())
	{
		const double scale = 0.02 + Periodic::Triangle0_1(10s) * 0.4;

		textureUnmipped.scaled(scale).drawAt(300, 300);

		textureMipped.scaled(scale).drawAt(500, 300);
	}
}
```

## 5.8 テクスチャの一部を描画する
テクスチャの全部ではなく、特定の長方形の領域だけを描画したい場合は `Texture::operator()` で、テクスチャ上の描画したい領域を指定して `TextureRegion` を作成します。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/8-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));

	const Texture textureWindmill(U"example/windmill.png");

	const Texture textureSiv3DKun(U"example/siv3d-kun.png");

	while (System::Update())
	{
		// 画像の (250, 100) から幅 200, 高さ 150 の長方形部分
		textureWindmill(250, 100, 200, 150).draw(40, 20);

		// 画像の (100, 0) から幅 100, 高さ 150 の長方形部分
		textureSiv3DKun(100, 0, 100, 150).draw(400, 100);
	}
}
```

サンプルでは示していませんが、1 枚の画像にアニメーションの複数のコマを記録し、それぞれのコマを順に表示していくことで、1 つのテクスチャでアニメーションを実現することもできます。

## 5.9 図形の形に合わせてテクスチャを描く
`Rect` や `RectF`, `Circle`, `Quad`, `RoundRect` に合わせる形で、テクスチャ全体やテクスチャの一部領域を描くことができます。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/9-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture textureWindmill(U"example/windmill.png", TextureDesc::Mipped);
	const Texture textureSiv3DKun(U"example/siv3d-kun.png", TextureDesc::Mipped);

	const RoundRect roundRect(430, 50, 100, 100, 25);
	const Circle circle(480, 240, 50);

	while (System::Update())
	{
		// テクスチャを長方形に貼り付けて描画
		Rect(50, 50, 350, 400)(textureWindmill).draw();

		roundRect.draw(HSV(0, 0.5, 1.0));
		// テクスチャの (90, 5) から幅 110 高さ 110 の領域を、roundRect に貼り付けて描画
		roundRect(textureSiv3DKun(90, 5, 110, 110)).draw();

		circle.draw(HSV(120, 0.5, 1.0));
		// テクスチャの (85, 0) から幅 120 高さ 120 の領域を、circle に貼り付けて描画
		circle(textureSiv3DKun(85, 0, 120, 120)).draw();
	}
}
```

## 5.10 テクスチャを透過させる
テクスチャの `.draw()`, `.drawAt()` の引数に色を渡すとその色で乗算して描画されます。色のアルファ成分に応じてテクスチャは透過します。

<video src="https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/10-0.mp4?raw=true" autoplay loop muted></video>

```C++
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF(0.8, 0.9, 1.0));

	const Texture textureWindmill(U"example/windmill.png");

	const Texture textureSiv3DKun(U"example/siv3d-kun.png");

	while (System::Update())
	{
		textureWindmill.draw(40, 20);

		// アルファ成分を 0.0～1.0 の間で変化させる
		textureSiv3DKun.draw(400, 100, ColorF(1.0, Periodic::Sine0_1(2s)));
	}
}
```

## 5.11 テクスチャのプログラムあれこれ

### ファイルの読み込みチェック
`if (texture)` で、テクスチャが作成されているかどうかをチェックできます。

```C++
# include <Siv3D.hpp>

void Main()
{
	// 存在しない画像ファイルを開こうとする
	const Texture texture(U"aaa/bbb.png");

	// テクスチャの作成ができていなかったら
	if (!texture)
	{
		// 例外を投げて終了
		throw Error(U"Failed to create a texture");
	}

	while (System::Update())
	{

	}
}
```

### 空のテクスチャ
空（から）の `Texture` を描画すると、16x16 の黄色いダミー画像になります。

![](https://github.com/Siv3D/siv3d.docs.images/blob/master/tutorial/5/11-0.png?raw=true)

```C++
# include <Siv3D.hpp>

void Main()
{
	// 画像ファイルの読み込みに失敗すると、空のテクスチャになる
	const Texture texture(U"aaa/bbb.png");

	if (!texture)
	{
		Print << U"!texture";
	}

	// 初期データを与えなかったときも、空のテクスチャになる
	Texture texture2;

	if (!texture2)
	{
		Print << U"!texture2";
	}

	while (System::Update())
	{
		texture.draw(0, 0);

		texture2.draw(400, 0);
	}
}
```

### テクスチャのサイズ
テクスチャの画像データの縦横のサイズを `Size` 型（`Point` 型と同じ) で取得するには `Texture::size()` を使います。横のサイズ、縦のサイズはそれぞれ `Texture::width()`, `Texture::height()` でも取得できます。

```C++
# include <Siv3D.hpp>

void Main()
{
	const Texture texture(U"example/windmill.png");

	Print << texture.size();

	Print << texture.width();

	Print << texture.height();

	while (System::Update())
	{
		texture.draw(200, 200);
	}
}
```

### テクスチャの代入
`Texture` は次のように `=` 演算子を使って代入できます。

```C++ hl_lines="13"
# include <Siv3D.hpp>

void Main()
{
	Texture texture;

	while (System::Update())
	{
		// テクスチャが空の状態で、左クリックされたら
		if (!texture && MouseL.down())
		{
			// テクスチャを作成
			texture = Texture(U"example/windmill.png");
		}
		
		if (texture)
		{
			texture.draw();
		}
	}
}
```
