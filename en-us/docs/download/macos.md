# Getting Started with Siv3D on macOS

## 1. System Requirements
### 1.1 Developer System Requirements
The necessary development environment for Siv3D programming on macOS is as follows:

|  |  |
|--|--|
| OS | macOS Big Sur / Monterey / Ventura |
| CPU | Intel CPU / Apple Silicon (Rosetta mode)* |
| GPU | OpenGL 4.1 compatible hardware |
| Output Devices | Monitors |
| IDE | Xcode 12.5 or newer |

- Native support for Apple Silicon (M1 / M2) will be added in the currently under development Siv3D v0.8.0.

??? summary "If you cannot install Xcode"
	If your macOS OS version is not the latest, you may not be able to install Xcode via the App Store. In that case, please download a previous version of Xcode, such as Xcode 13.2, from the [Apple Developer site :material-open-in-new:](https://developer.apple.com/download/more/){:target="_blank"}.


### 1.2 System Requirements for Running Siv3D Application
The necessary environment to run applications developed with Siv3D v0.6.14 on macOS is as follows.

|  |  |
|--|--|
| OS | macOS Mojave / Catalina / Big Sur / Monterey / Ventura |
| CPU | Intel CPU / Apple Silicon (Rosetta mode)* |
| GPU | OpenGL 4.1 compatible hardware |
| Output Devices | Monitors |

- Native support for Apple Silicon (M1 / M2) will be added in the currently under development Siv3D v0.8.0.


## 2. Downloading the Siv3D Project Template
1. Download and extract **[OpenSiv3D v0.6.14 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.14_macOS.zip){:target="_blank"}**.
1. If you are on macOS Catalina or later, a dialog box for file access permission appears every time you run the program. To avoid this, move the project folder to `(Username)/Applications` folder (not the root's Applications folder, but the Applications folder in user's home), rather than `(Username)/Desktop` or `(Username)/Downloads` folders.

??? summary "Previous versions"
	The use of past versions is discouraged. If necessary, download them from the following links.
	Due to updates to the compiler, etc., you may not be able to use past versions in the latest development environment. If you want to build an old Siv3D project, a good way is to port its source code to a project with the latest version.

	- [OpenSiv3D v0.6.13 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.13_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.12 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.12_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.11 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.11_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.10 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.10_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.9 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.9_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.8 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.8_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.7 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.7_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.6 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.6_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.5 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.5_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.4 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.4_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.3 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.3_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.2 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.2_macOS.zip){:target="_blank"}

## 3. Building a Siv3D App
1. Open the project file `examples/empty/empty.xcodeproj` in Xcode.
1. Open `Main.cpp` from the project menu
1. Click **Run button ▶️** to build and execute the application.
1. To exit a running program, press ++esc++ or close the window.


??? summary "Enabling Rosetta mode on M1 / M2 Mac"
	If the Rosetta option is not displayed in Xcode, select *Show Rosetta Destinations* from Product &gt; Destination &gt; Destination Architectures. From Xcode 15.3 onwards, the Rosetta option can be displayed by pressing Product &gt; Destination &gt; Show All Run Destinations.

??? summary "Avoiding file access permission dialog when running the sample program"
	If a file access permission dialog appears every time you run on macOS Catalina or later, you can avoid this by moving the entire project folder to the `(Username)/Applications` folder (not the root's Applications folder, but the Applications folder in the user's home), rather than the `(Username)/Desktop` or `(Username)/Downloads` folders.

??? summary "If you want to create another project"
	Please copy the `empty` folder located within the project template folder at the same level. A project generator for Xcode is planned for future release.

