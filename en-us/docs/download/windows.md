# Getting Started with Siv3D Programming on Windows

## 1. System Requirements
### 1.1 Developer System Requirements
- The development environment required for Siv3D programming on Windows PC is as follows:

|  |  |
|--|--|
| OS | Windows 10 (64-bit) / Windows 11 |
| CPU | Intel or AMD CPU |
| Video Output | Any video output device such as a monitor |
| Audio Output | Any audio output device |
| Development Environment | **Microsoft Visual C++ 2022 17.13 or later**<br>(Please add "Desktop development with C++" during installation) |

??? summary "Visual Studio Edition to Install"
	- For Siv3D programming on Windows, it's convenient to use **"Visual Studio Community 2022"**
	- This is the free version of "Visual Studio", an integrated development environment used by professional software developers worldwide
	- For students and individual developers, you can use the same features as the paid version of Visual Studio for free

??? summary "Visual Studio Installation Steps"
	- Download the installer for **"Visual Studio 2022 Community"** from the [Visual Studio download page :material-open-in-new:](https://visualstudio.microsoft.com/ja/downloads/){:target="_blank"} and run it
	- When you run the installer, a screen like the following will appear where you can select programming languages and development tools. From these options, select **"Desktop development with C++"** (the items displayed in "Installation details" on the right side may vary depending on your Visual Studio version, so you don't need to worry about them)

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/vs_installer_desktop.png)

	- Simply click the "Install" button in the bottom right to begin installing the tools necessary for C++ programming

### 1.2 App Runtime System Requirements
- The environment required to run applications developed with Siv3D v0.6.16 on Windows PC is as follows:

|  |  |
|--|--|
| OS | Windows 7 SP1 (64-bit) / Windows 8.1 (64-bit) / Windows 10 (64-bit) / Windows 11 |
| CPU | Intel or AMD CPU |
| Video Output | Any video output device such as a monitor |
| Audio Output | Any audio output device |

## 2. Install the SDK

1. Download and run **[OpenSiv3D v0.6.16 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.16_Installer.exe){:target="_blank"}**
1. If "Windows protected your PC" is displayed during execution, click **More info** and then click **Run**

??? info "If you don't have administrator privileges"
	- If you don't have administrator privileges and cannot run the installer, you can install the SDK using the "(Supplement) Manually Installing the SDK" method at the bottom of this page

??? summary "What the installer does automatically"
	- The Siv3D installer automatically performs the following:
		- SDK placement (default is Documents folder)
		- Setting user environment variables to the path where the SDK is placed
		- Copying Visual Studio project templates for Siv3D projects (usually `Documents/Visual Studio 2022/Templates/ProjectTemplates/`)
		- Registering the uninstaller

??? summary "To uninstall the SDK"
	- Uninstall it from Windows "Settings" like any regular Windows application

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/uninstall.png)

??? summary "Previous Versions"
	- Using previous versions is not recommended, but if necessary, they can be downloaded from the links below:
		- Due to compiler updates and other changes, building with previous versions may fail in the latest development environment
		- If you want to run old Siv3D projects, it's better to port the source code to the latest version project
	- [OpenSiv3D v0.6.15 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.15_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.14 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.14_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.13 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.13_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.12 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.12_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.11 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.11_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.10 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.10_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.9 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.9_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.8 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.8_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.7 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.7_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.6 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.6_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.5 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.5_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.4 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.4_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.3 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.3_Installer.exe){:target="_blank"}
	- [OpenSiv3D v0.6.2 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.2_Installer.exe){:target="_blank"}

## 3. Create a Siv3D Project

<video src="https://github.com/Siv3D/siv3d.site.resource/blob/main/v7/download/msvc.mp4?raw=true" autoplay loop muted playsinline></video>

1. Click **Create a new project** on the Visual Studio start screen
1. Select **OpenSiv3D** from the project template items and click **Next**
1. Enter the project name and save location (optional) and click **Create**

??? warning "If you can't find 'OpenSiv3D' in the project template items"
	- On Windows PCs with OneDrive enabled, there may be two "Documents" folders
	- In this case, the Documents folder that Visual Studio references may not match the Documents folder where the "Visual Studio project template for Siv3D projects" is placed, causing Visual Studio to not find the project template
	- First, verify that the **project template file** `OpenSiv3D_●.●.●.zip` exists in `C:\Users\●●●\Documents\Visual Studio 2022\Templates\ProjectTemplates`
	- Then choose one of the following two solutions:

	#### Solution A | Change the Documents folder that Visual Studio references

	- Launch Visual Studio and select **Options** from the **Tools** menu
	- Select **Projects and Solutions** > **Locations** and change **User project templates location** from the OneDrive Documents folder to the regular Documents folder
	- Before change:<br>`C:\Users\●●●\OneDrive\Documents\Visual Studio 2022\Templates\ProjectTemplates`
	- After change:<br>`C:\Users\●●●\Documents\Visual Studio 2022\Templates\ProjectTemplates`

	#### Solution B | Move the project template to the Documents folder that Visual Studio references

	- Move the project template file to the OneDrive folder indicated in "Solution A"
	- Before move:<br>`C:\Users\●●●\Documents\Visual Studio 2022\Templates\ProjectTemplates\OpenSiv3D_●.●.●.zip`
	- After move:<br>`C:\Users\●●●\OneDrive\Documents\Visual Studio 2022\Templates\ProjectTemplates\OpenSiv3D_●.●.●.zip`



## 4. Build Siv3D Apps
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/download/msvc.png)

1. When you create a project, sample code (Main.cpp) is provided from the start
1. Build the project from the **Build** menu
1. Click **Start Debugging** in the **Debug** menu to run the built program
1. You can exit the running program by pressing ++esc++ or closing the window

??? warning "If you get a 'Siv3D.hpp not found' error"
	- This is caused by the environment variable indicating the Siv3D SDK installation location set by the installer not being reflected in Visual Studio
	- Discard the created project, restart Windows, and then create a new Siv3D project again to resolve this issue

??? warning "If you get an error related to __std_min_element_d and cannot run"
	- This error occurs when the C++ standard library of the C++ build tools installed in Visual Studio is outdated
	- Use Visual Studio Installer to uninstall the old build tools and install the latest build tools
	- If you're unsure, reinstalling Visual Studio will resolve the issue

??? warning "If the program runs but build error-like messages appear in the 'Error List'"
	- The error messages displayed in the "Error List" include inaccurate information that isn't real build errors and warnings unrelated to the user's code
	- Please refer to the real error messages and warnings displayed in the "Output" window instead of the messages displayed in the "Error List" window


## (Supplement) Manually Installing the SDK
- If the SDK installer doesn't run properly, you can install Siv3D manually instead. The steps are as follows:

??? summary "Steps for manually installing the SDK"
	### SDK File Placement and Environment Variable Setup

	1. Download and extract [OpenSiv3D_SDK_0.6.16.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.16/OpenSiv3D_SDK_0.6.16.zip) and place the contents in the Documents folder (`.../Documents`) as follows:
		- `.../Documents/OpenSiv3D_SDK_0.6.16/addon`
		- `.../Documents/OpenSiv3D_SDK_0.6.16/include`
		- `.../Documents/OpenSiv3D_SDK_0.6.16/lib`
	2. Create a new user environment variable `SIV3D_0_6_16` and set the path to the OpenSiv3D SDK folder placed in step 1.
		- Example: If placed as `C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.16/include`, set `C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.16` to the environment variable `SIV3D_0_6_16`.

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/envvariable.png)  

	### Visual Studio Project Template Placement

	1. Download the Visual Studio project template [OpenSiv3D_0.6.16.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.16/OpenSiv3D_0.6.16.zip) (Size: approximately 63 MB) and place the file **as a ZIP file without extracting** in the `Visual Studio 2022/Templates/ProjectTemplates/` folder created in the Documents folder when Visual Studio 2022 was installed.

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/projecttemplate.png)  

	- This completes the manual installation steps
	- Restart your PC to ensure the environment variables are applied, then proceed to step 3 on this page