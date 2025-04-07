# Getting Started with Siv3D on Windows

## 1. System Requirements
### 1.1 Developer System Requirements
The necessary development environment for Siv3D programming on Windows is as follows:

|  |  |
|--|--|
| OS | Windows 10 (64-bit) /  Windows 11 |
| CPU | Intel / AMD CPU |
| Output Devices | Monitors and speakers |
| IDE | Microsoft Visual C++ 2022 17.11<br>(Please install "Desktop development with C++" during the installation process) |

??? summary "About Visual Studio Editions"
	For Siv3D programming on Windows 10 or Windows 11, it is convenient to use **Visual Studio Community 2022**. This is a free version of the integrated development environment "Visual Studio" that professional software developers worldwide use. Students, individuals, and small-scale developers can use the same features as the paid version of Visual Studio for free.

??? summary "About Visual Studio Installation"
	Download and execute the installer for **Visual Studio 2022 Community** from the [Visual Studio download page :material-open-in-new:](https://visualstudio.microsoft.com/downloads/){:target="_blank"}.

	When you run the installer, a screen will appear where you can choose programming languages and development tools. From here, select "Desktop development with C++".

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/vs_installer_desktop.png)

	After that, just press the "Install" button at the bottom right to start installing the necessary tools for C++ programming.


### 1.2 System Requirements for Running Siv3D Application
The necessary environment to run applications developed with Siv3D v0.6.16 on Windows is as follows. You may want to include this in the instructions when distributing your game or app.

|  |  |
|--|--|
| OS | Windows 7 SP1 (64-bit) / Windows 8.1 (64-bit) / Windows 10 (64-bit) /  Windows 11 |
| CPU | CPU manufactured by Intel or AMD |
| Video Output | Some sort of video output device, such as a monitor |
| Audio Output | Some sort of audio output device |


## 2. Installing the Siv3D SDK

1. Download and run **[OpenSiv3D v0.6.16 Installer for Windows Desktop](https://siv3d.jp/downloads/Siv3D/OpenSiv3D_0.6.16_Installer.exe){:target="_blank"}** .
1. If you see a message that says "Windows Protected Your PC" when you try to execute it, press **More info** and then press **Run anyway**.

!!! info "Recommended to reboot Windows after installation"
	To ensure that the environment variables set by the installer are reflected correctly in Visual Studio, it is recommended that you reboot Windows after installation.

??? warning "If you still fail"
	If you fail to execute the installer, install the SDK by following the "Manual SDK Installation" method on this page.

??? summary "The installer will automatically do the following:"
	- Create a SDK folder (The default location is `Documents`).
	- Set a user environment variable "SIV3D_0_6_16" with the path to the SDK folder.
	- Copy the Visual Studio project template for the Siv3D project (The default locations is `Documents/Visual Studio 2022/Templates/ProjectTemplates/`).
	- Register the uninstaller.

??? summary "How to uninstall the installed OpenSiv3D SDK"
	The OpenSiv3D SDK can be uninstalled from the "Settings" in Windows, similar to regular applications.

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/uninstall.png)


??? summary "Previous versions"
	The use of previous versions is not recommended. If necessary, please download them from the links below. 
	Due to updates to the compiler and others, you may not be able to use past versions in the latest development environment. If you want to build an old Siv3D project, it is best to port its source code to a project with the latest version.

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

## 3. Creating a Siv3D project

<video src="https://github.com/Siv3D/siv3d.site.resource/blob/main/v7/download/msvc.mp4?raw=true" autoplay loop muted playsinline></video>

1. Lanuch Visual Studio and open a **New Project Dialog** by clicking **Create a new project**.
1. Select **OpenSiv3D** project and then click **Next**.
1. Type a name for the project and click **OK** to create the project.


## 4. Building a Siv3D app
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v7/download/msvc.png)

1. After creating a project, a sample code (Main.cpp) will appear.
1. On the **Build** menu, click **Build Solution**.
1. On the **Debug** menu, click **Start Debugging**.
1. The running program can be terminated by pressing the ++esc++ key or by closing the window.

??? warning "If you get the error 'Cannot find Siv3D.hpp'"
	The cause is that the installation information of the Siv3D SDK set by the installer is not reflected in Visual Studio. Restart Windows and create a new Siv3D project again to resolve this issue.


## (Supplement) Manually Installing the SDK
If the OpenSiv3D installer doesn't run correctly, you can manually install OpenSiv3D instead. The steps are as follows.

??? summary "Steps for manually installing the Siv3D SDK"
	### Placing SDK files and setting environment variables

	1. Download and extract [OpenSiv3D_SDK_0.6.16.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.16/OpenSiv3D_SDK_0.6.16.zip) (File size: 90 MB), and place the contents in your documents folder as follows:
		- `.../Documents/OpenSiv3D_SDK_0.6.16/addon`
		- `.../Documents/OpenSiv3D_SDK_0.6.16/include`
		- `.../Documents/OpenSiv3D_SDK_0.6.16/lib`
	2. Create a new environment variable `SIV3D_0_6_16` and set the path to the SDK folder (the parent folder of the `addon/`, `include/`, and `lib/` folders)
		- Example: If you have placed `C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.16/include`, set `C:/Users/Siv3D/Documents/OpenSiv3D_SDK_0.6.16` to the environment variable `SIV3D_0_6_16`.

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/envvariable.png)  

	### Placing the OpenSiv3D project template (ZIP)

	1. Download the Visual Studio project template [OpenSiv3D_0.6.16.zip](https://siv3d.jp/downloads/Siv3D/manual/0.6.16/OpenSiv3D_0.6.16.zip) (size: about 63 MB), and without unzipping the file, place the ZIP file in the `Visual Studio 2022/Templates/ProjectTemplates/` folder created in the documents folder during the Visual Studio 2022 installation.  

	![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/windows/projecttemplate.png)  

	After the manual installation steps are complete, reboot your PC to ensure the application of the environment variables. Then, proceed to step 3 on this page.

	