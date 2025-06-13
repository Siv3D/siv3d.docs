# 60. Distributing Your Application
Learn the procedures for distributing the applications you've created.

## 60.1 Considering Various Environments
- The developer's computer and the user's computer have different environments. Consider the following:
	- The user's monitor resolution is 1366×768 and the window doesn't fit on screen
	- Provide different resolution options or run in fullscreen mode
- Gamepad `Gamepad` button indices differ by gamepad / special key usage
	- Provide key configuration
- Games and motion progress too quickly on high refresh rate monitors like 120Hz or 144Hz
	- Adopt time-based processing referring to **Tutorial 14**, **Tutorial 19**, and **Tutorial 30**
	- You can simulate high and low frame rate environments as follows:
		- Turn off VSync with `Graphics::SetVSyncEnabled(false)`
		- Create wait time every frame with `System::Sleep(20ms)`


## 60.2 Providing Usage Instructions
- Provide necessary information so users who download games or apps don't get confused about controls
	- Display sufficient explanations and tutorials within the app
		- Display manuals with `Texture`
		- Play videos with `VideoTexture`
	- Include manuals or README with the app
	- Prepare manuals or tutorial videos on the web
	- Make manuals or tutorial videos accessible from within the app
- To launch a web browser from a program and open a specified URL, do the following (be careful not to open it every frame):
	- It's good to open web pages explaining how to play or YouTube tutorial videos

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (SimpleGUI::Button(U"How to play", Vec2{ 40, 40 }))
		{
			// Open web page in browser
			System::LaunchBrowser(U"https://siv3d.github.io/ja-jp/");
		}
	}
}
```

- In particular, whether to use mouse or keyboard controls is a point of confusion for first-time users. It's good to display this within the app as well
- For clickable elements in the app, use `Cursor::RequestStyle(CursorStyle::Hand);` to change the cursor to hand shape, indicating to users that it's clickable


## 60.3 Window Title
- You can change the window title with `Window::SetTitle(title)`

```cpp
# include <Siv3D.hpp>

void Main()
{
	Window::SetTitle(U"My Game");

	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{

	}
}
```


## 60.4 App Icon

### For Windows
- Replace `icon.ico` in the project's `App` folder with a new icon file and rebuild the program to change the executable file (.exe) icon
- Windows OS caches old icon thumbnail images on the computer, so the appearance in Explorer may not change even after updating to a new icon
	- In that case, you can update the cache by changing the executable file name or cleaning up "Thumbnails" with Windows' "Disk Cleanup" feature

### For macOS
- Replace `icon.icns` in the project folder and rebuild the program to change the executable file (.app) icon


## 60.5 License Display
- When distributing apps, you need to display licenses for Siv3D and third-party software used by Siv3D
- Make license information accessible to users through the following means:
	- Include the HTML file displayed when F1 key is pressed with the distributed app
	- Describe the license information displayed when F1 key is pressed in the README
	- Clearly state in the README that license information can be displayed by pressing F1 key
	- Call `LicenseManager::ShowInBrowser()` to open licenses from within the app
	- Display license information obtained with `LicenseManager::EnumLicenses()` within the app
- See **Tutorial 5.4** for how to add the game's own license to HTML
- The following sample code displays license information in a web browser when the "Licenses" button is pressed

```cpp
# include <Siv3D.hpp>

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Licenses", Vec2{ 40, 40 }))
		{
			// Display license information in web browser
			LicenseManager::ShowInBrowser();
		}
	}
}
```


## 60.6 SNS Share Button
- Making it easy to share game progress and results on SNS can be expected to have advertising effects
- The following code implements a button to share specified text on Twitter (X)
- Include the game score, game-specific hashtags, and URLs where the game can be obtained

```cpp
# include <Siv3D.hpp>

void PostRsultTweet(int32 score)
{
	const String text = U"I got {} points in the game!\n#Siv3D\nhttps://siv3d.github.io/ja-jp/"_fmt(score);

	Twitter::OpenTweetWindow(text);
}

void Main()
{
	Scene::SetBackground(ColorF{ 0.6, 0.8, 0.7 });

	while (System::Update())
	{
		if (SimpleGUI::Button(U"Share Score", Vec2{ 40, 40 }))
		{
			PostRsultTweet(123);
		}
	}
}
```


## 60.7 Release Build
- Distribute executable files built as "Release build"
- Building a program as Release build generates an executable file that runs at high speed with maximum optimization applied
- File size also becomes smaller because debug information is not included
- In Visual Studio, change the build configuration to Release and build as shown in the following image:

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial3/release/6.png)


## 60.8 Resource Embedding
- By embedding images, audio, and text files used by the program into the executable file, you can distribute apps as a single executable file
- See **Tutorial 57**


## 60.9 Files to Include
- The files and folders that need to be included in the distribution files are:
	- Executable file (.exe or .app)
	- Images, audio, and text files used by the program that are not embedded
	- (Only when using `GlobalAudio::BusSetPitchShiftFilter()` on Windows) `dll` folder
	- (If necessary) Manual or README.txt describing the app
	- (If necessary) File describing license information (**60.5**)
- In the minimal configuration, distribution with just one executable file is possible


## 60.10 Files Not Needed for Distribution
- The following folders and files are not included in files distributed to users

| File or Folder | Reason not needed for distribution |
|--|--|
|`engine` folder | Automatically embedded in executable file during application build |
|`example` folder | No need to include if not used in the program |
| Icon files | Automatically embedded in executable file during application build |
| `AS_DEBUG` folder | Script feature debug log, unrelated to execution |
| `Screenshot` folder | Automatically created when users take screenshots |
| `Resource.rc` | Not necessary for app execution |
| `Info.plist` | Not necessary for app execution |
| `Intermediate/` folder | Unrelated to app execution |
| `*.exp`, `*.lib`, `*.pdb` and other build-generated files | Unrelated to app execution |


## 60.11 (Advanced) Minimizing File Size
- Some files in the `engine` folder contents are not required for embedding only when specific features are not used in the program
- You can reduce the size of distribution files by not embedding target files
- On Windows, you can cancel embedding by commenting out the relevant files from `Resource.rc`, and on macOS/Linux by deleting the relevant files from the engine folder
- If you mistakenly don't embed files that are actually needed, it can cause troubles like text not displaying or audio not playing when users run the app. Canceling embedding should be done carefully

| File | Cases where embedding can be canceled |
|--|--|
| `engine/font/mplus/mplus-1p-thin.ttf.zstdcmp` | When not using `Typeface::Thin` |
| `engine/font/mplus/mplus-1p-light.ttf.zstdcmp` | When not using `Typeface::Light` |
| `engine/font/mplus/mplus-1p-regular.ttf.zstdcmp` | When not using default font (`Typeface::Regular`) |
| `engine/font/mplus/mplus-1p-medium.ttf.zstdcmp` | When not using `Typeface::Medium` |
| `engine/font/mplus/mplus-1p-bold.ttf.zstdcmp` | When not using `Typeface::Bold` |
| `engine/font/mplus/mplus-1p-heavy.ttf.zstdcmp` | When not using `Typeface::Heavy` |
| `engine/font/mplus/mplus-1p-black.ttf.zstdcmp` | When not using `Typeface::Black` |
| `engine/font/noto-emoji/noto-cjk/NotoSansCJK-Regular.ttc.zstdcmp` | When not displaying Korean/Chinese in SimpleGUI, when not using `Typeface::CJK_Regular_**` (other than JP) |
| `engine/font/noto-emoji/noto-cjk/NotoSansJP-Regular.otf.zstdcmp` | When not displaying characters outside ASCII in SimpleGUI, when not using `Typeface::CJK_Regular_JP` |
| `engine/font/noto-emoji/NotoColorEmoji.ttf.zstdcmp` | When not using `Typeface::ColorEmoji` or `Emoji` |
| `engine/font/noto-emoji/NotoEmoji-Regular.ttf.zstdcmp` | When not using `Typeface::MonochromeEmoji` or emoji in `Print` |
| `engine/font/fontawesome/fontawesome-brands.otf.zstdcmp` | When not using `Typeface::Icon_Awesome_Brand` or `Icon` |
| `engine/font/fontawesome/fontawesome-solid.otf.zstdcmp` | When not using `Typeface::Icon_Awesome_Solid` or `Icon` |
| `engine/font/materialdesignicons/materialdesignicons-webfont.ttf.zstdcmp` | When not using icon display in SimpleGUI text, `Typeface::Icon_MaterialDesign`, or `Icon` |
| `engine/soundfont/GMGSx.sf2.zstdcmp` | When not using `GMInstrument` or `MIDI` file loading |

- Many engine files included with Siv3D apps are cached on the local PC during program execution
- It may appear that the program works normally even without embedding necessary files. This is because Siv3D apps run in the past have cached necessary files on the PC
- Siv3D app cache folder is created at `Username/AppData/Local/Siv3D/` (hidden folder). You can safely delete the contents of this Siv3D cache folder to simulate a first-time user environment


## 60.12 Sending Press Releases
- If you want media to cover your game or app release, send press kits (or press kit download URLs) to various media outlets
- Especially for commercial works expecting sales of hundreds of thousands of yen or more, sending press releases is essential

### 60.12.1 Press Kit Contents (Example)
1. Press release text
	- Both PDF and plain text versions for editor convenience
2. Screenshots and artwork
3. Promotional video (or YouTube URL)
4. Demo version (if available)
5. Distribution store page URL (if available)
6. Desired article release date (on or after XX/XX)

### 60.12.2 Notes When Using Online Storage
- When sharing files through online storage, be careful about permission settings (especially Google Drive), sharing periods, and file deletion
- Check if others can access files by opening the shared URL in a browser's private browsing mode

### 60.12.3 Press Release Timing
- From a web media perspective, articles like "trailer release", "release date announcement", "released today", and "release sale" tend to gather access
- It's desirable to send press releases with about 10 business days lead time so web media can publish such articles at appropriate times
- Press releases are not guaranteed to be published, but the promotional effect when published is significant, so you should thoroughly prepare format and time allowance until release date
- Sometimes articles just repost the press release text, and for works that seem to gather attention, writers may write articles with original editing

### 60.12.4 Press Release Text Example
- It's common to use yourself as the subject and format as "I/organization did/will do ~ announcement"
	- For organizations use the organization name, for individuals use "indie game developer XX"
	- Real names are not necessary; if you have an online activity name, you can use that without problems
- Write in a format that can be copied and made into articles as-is

```
XX (developer/organization name) started distribution of XX (game genre) "XX" on Steam on XX year XX month XX day. The price is XX yen (tax included).

◆ Overview
Title: 
Genre: 
Price: 
Distribution Platform: 
Supported Languages: 

◆ Game Features
1. [Main feature 1]
2. [Main feature 2]
3. [Main feature 3]

◆ Future Plans
[If there are update plans or DLC plans]

◆ Work Awards/Exhibition History
・～～～～～

◆ URLs
・Steam distribution page: 
・Work introduction page: 
・(If available) YouTube: 
・(If available) X (formerly Twitter):
```

- Examples of Siv3D works where press releases were published in media:
	- [4Gamer.net: フィクションの少女たちと会話をして，友だちになる。PC用ソフト「For the GHOSTs」，Steamで配信開始 :material-open-in-new:](https://www.4gamer.net/games/765/G076537/20240109010/){:target="_blank"}
	- [gamebiz: 個人ゲーム開発者sashi、25年発売予定の新作2D探索型アクション『Monad Tachyon』のSteamストアページを公開 :material-open-in-new:](https://gamebiz.jp/news/396584){:target="_blank"}
- Articles with experiences about sending press releases:
	- [自作ゲームのプレスリリースを送って掲載された話 :material-open-in-new:](https://note.com/m_hanakura/n/n07bb88589eba){:target="_blank"}
	- [穴埋めで作る、インディゲーム開発者のための簡単なプレスリリースの作り方 :material-open-in-new:](https://note.com/gamecast/n/nae90d756bf04){:target="_blank"}
	- Besides the above, web searches will find various articles
- Having generative AI proofread press releases is also effective
- Don't forget to include greetings to media and contact information separate from the press release text


## 60.13 App Release Preparation
- Prepare necessary files according to the rules of the distribution platform (store site)
- When releasing through online storage, organize in ZIP archives for easy download
- Conducting operation checks on collaborators' computers before major public release can reduce troubles


## 60.14 App Promotion
- To get many people to know about your app, you need to start promotion from the development stage
- It's effective to share **screenshots**, **videos**, and development episodes on SNS using consistent **hashtags**
- Tweeting with the #Siv3D hashtag on Twitter (X) may get retweeted by Siv3D developers or official accounts
- Uploading gameplay videos and promotional videos to YouTube is also important


## 60.15 Community Management
- Setting up user feedback collection systems can help improve apps
- Preparing Discord servers or Twitter (X) accounts for users and directing them from within apps or related websites can facilitate communication with users