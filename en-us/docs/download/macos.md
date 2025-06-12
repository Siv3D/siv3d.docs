# Getting Started with Siv3D Programming on macOS

## 1. System Requirements
### 1.1 Developer System Requirements
- The development environment required for Siv3D programming on macOS is as follows:

|  |  |
|--|--|
| OS | macOS Ventura / Sonoma / Sequoia |
| CPU | Intel CPU / Apple Silicon (Rosetta mode) |
| GPU | OpenGL 4.1 support |
| Video Output | Any video output device such as a monitor |
| Development Environment | Xcode 14.3 or later |

- Apple Silicon (M1 - M4) will have native support starting from Siv3D v0.8.0, which is currently in development
- Until then, it operates in Rosetta mode

??? summary "If you cannot install Xcode"
	- If the macOS version you're using is not the latest, you may not be able to install Xcode via the App Store
	- In that case, download and install previous versions of Xcode, such as Xcode 14.3, from the [Apple Developer site :material-open-in-new:](https://developer.apple.com/download/more/){:target="_blank"}

### 1.2 App Runtime System Requirements
- The environment required to run applications developed with Siv3D v0.6.16 on macOS is as follows:

|  |  |
|--|--|
| OS | macOS Mojave / Catalina / Big Sur / Monterey / Ventura / Sonoma / Sequoia |
| CPU | Intel CPU / Apple Silicon (Rosetta mode) |
| GPU | OpenGL 4.1 support |
| Video Output | Any video output device such as a monitor |

- Apple Silicon (M1 - M4) will have native support starting from Siv3D v0.8.0, which is currently in development
- Until then, it operates in Rosetta mode


## 2. Download Project Templates
1. Download **[OpenSiv3D v0.6.16 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.16_macOS.zip){:target="_blank"}** and extract the files
1. On macOS Catalina and later, file access permission dialogs will appear every time you run a program. To avoid this, move the project folder to the `(username)/Applications` folder (the Applications folder in the user home, not the root Applications folder) instead of the `(username)/Desktop` or `(username)/Downloads` folders

??? summary "Previous Versions"
	- Using previous versions is not recommended, but if necessary, they can be downloaded from the links below:
		- Due to compiler updates and other changes, building with previous versions may fail in the latest development environment
		- If you want to run old Siv3D projects, it's better to port the source code to the latest version project

	- [OpenSiv3D v0.6.15 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.15_macOS.zip){:target="_blank"}
	- [OpenSiv3D v0.6.14 Project Templates for macOS](https://siv3d.jp/downloads/Siv3D/siv3d_v0.6.14_macOS.zip){:target="_blank"}
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


## 3. Build Siv3D Apps
1. Open the project file `examples/empty/empty.xcodeproj` in the project templates with Xcode
1. Sample program (Main.cpp) is provided from the start
1. For M1 - M4 Macs, enable Rosetta mode following the steps described below
1. Press the **Run button ▶️** to build and run the program
1. You can exit the running program by pressing ++esc++ or closing the window

??? summary "Enabling Rosetta Mode on M1 - M4 Macs"
	- The method to display Rosetta options in Xcode varies depending on the Xcode version:
		- Xcode 15.3 and later: Select **Product &gt; Destination &gt; Show All Run Destinations** from the menu bar
		- Xcode 15.2 and earlier: Select **Show Rosetta Destinations** from **Product &gt; Destination &gt; Destination Architectures** in the menu bar
	- Once the Rosetta option is displayed, select it

??? summary "Avoiding File Access Permission Dialogs When Running Sample Programs"
	- On macOS Catalina and later, file access permission dialogs may appear every time you run the program
	- This can be avoided by moving the entire project folder to the `(username)/Applications` folder (the Applications folder in the user home, not the root Applications folder) instead of the `(username)/Desktop` or `(username)/Downloads` folders

??? summary "If you want to add new projects"
	- Copy the `empty` folder in the project template folder to the same level
	- A project generator for Xcode is planned for future release

??? warning "If the program runs but red error-like displays appear in the editor"
	- This is a known issue with recent versions of Xcode. It does not affect execution
	- This will be resolved in Siv3D v0.8, which is currently in development. Please wait a little longer