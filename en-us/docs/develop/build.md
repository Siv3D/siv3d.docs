# Custom Build Instructions
This page explains the steps to build the Siv3D library from source code yourself. This page is for special users such as:

- Those who want to try the latest code of the development version
- Those who want to understand the internals of Siv3D
- Those who want to modify the internal code

## 1. Windows
### 1.1 Download Additional Third-Party Libraries
◆ Prepare the C++ library **"Boost"** required for building the Siv3D library itself.

Download and extract the compressed source code of `boost_1_83_0` from [https://www.boost.org/users/history/version_1_83_0.html :material-open-in-new:](https://www.boost.org/users/history/version_1_83_0.html){:target="_blank"}. The distributed file formats are `.7z` and `.zip`. If you can extract `.7z` files on your computer, `.7z` takes less time to extract. Since Boost consists of a large number of files, using the Windows OS standard ZIP extraction function may take several minutes to complete extraction.

??? info "What is Boost"
    [Boost :material-open-in-new:](https://www.boost.org/){:target="_blank"} is one of the most famous C++ libraries with over 20 years of history. It consists of various libraries of different sizes and purposes, created by various authors. `std::shared_ptr` that entered the standard library in C++11, `std::optional` and `<filesystem>` that entered the standard library in C++17 were designed based on Boost.SmartPtr, Boost.Optional, and Boost.Filesystem libraries respectively. Siv3D uses features from several Boost libraries: Boost.Geometry for geometric computation processing, Boost.Filesystem for file system processing in environments that don't support C++17, Boost.Process for creating and communicating with child processes, Boost.MultiPrecision for arbitrary precision arithmetic, and Boost.Tokenizer for CSV parsing.

??? info "Software for extracting .7z"
    [7-Zip :material-open-in-new:](https://sevenzip.osdn.jp/){:target="_blank"} is the most famous software that can extract `.7z` files.

### 1.2 Get Source Code from Siv3D Development Branch
◆ Get the latest Siv3D code from the official repository.

[The main branch of the official OpenSiv3D repository :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D){:target="_blank"} is the latest stable version. Clone the repository from "Code" or download the source code as a ZIP file ("Download ZIP").

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/ubuntu/repo.png)


### 1.3 Copy and Add Additional Third-Party Libraries
◆ Copy part of Boost to the downloaded project folder.

In the OpenSiv3D project folder obtained in 1.2, there is a `Dependencies/boost_1_83_0/` folder. Copy the `boost_1_83_0/boost/` folder (approximately 120 MB), which is part of the Boost library prepared in 1.1, into this folder. After copying, it becomes `Dependencies/boost_1_83_0/boost/`.

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/develop/boost.png)


### 1.4 Build Siv3D Library and Siv3D Apps
◆ Build the Siv3D library and Siv3D apps with Visual Studio.

Opening `WindowsDesktop/OpenSiv3D.sln` in the OpenSiv3D project folder obtained in 1.2 with Visual Studio opens a solution containing the Siv3D library main project "Siv3D" and the test app project "Siv3D-Test".

Build the "Siv3D-Test" project. Since the necessary library files don't exist for the first build, the build of the Siv3D library main project "Siv3D" will automatically start first. Building the library takes several minutes.

If you get the error `error C2039: '​CheckForDuplicateEntries': is not a member of 'Microsoft::WRL::Details'` when building the Windows version Siv3D library, it can be resolved by installing a newer Windows 10 SDK (version 10.0.18362.0 or later) using Visual Studio Installer.

### 1.5 (Optional) Overwrite Existing SDK Files
◆ Use the following steps if you want to use your self-built library from existing or new projects.

After building Siv3D yourself, `Siv3D.lib` (Release build) and `Siv3D_d.lib` (Debug build) are generated. By overwriting the files in your installed OpenSiv3D SDK with these static library files and the headers used for the build (`Siv3D/include/` folder), you can use your self-built version of Siv3D from existing and new projects. For safety, it is recommended to back up the library and header files before overwriting.


## 2. macOS

### 2.1 Download Additional Third-Party Libraries
◆ Prepare the C++ library **"Boost"** required for building the Siv3D library itself.

Download and extract the compressed source code of `boost_1_83_0` from [https://www.boost.org/users/history/version_1_83_0.html :material-open-in-new:](https://www.boost.org/users/history/version_1_83_0.html){:target="_blank"}.

??? info "What is Boost"
    [Boost :material-open-in-new:](https://www.boost.org/){:target="_blank"} is one of the most famous C++ libraries with over 20 years of history. It consists of various libraries of different sizes and purposes, created by various authors. `std::shared_ptr` that entered the standard library in C++11, `std::optional` and `<filesystem>` that entered the standard library in C++17 were designed based on Boost.SmartPtr, Boost.Optional, and Boost.Filesystem libraries respectively. Siv3D uses features from several Boost libraries: Boost.Geometry for geometric computation processing, Boost.Filesystem for file system processing in environments that don't support C++17, Boost.Process for creating and communicating with child processes, Boost.MultiPrecision for arbitrary precision arithmetic, and Boost.Tokenizer for CSV parsing.


### 2.2 Get Source Code from Siv3D Development Branch
◆ Get the latest Siv3D code from the official repository.

[The main branch of the official OpenSiv3D repository :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D){:target="_blank"} is the latest stable version. Clone the repository from "Code" or download the source code as a ZIP file ("Download ZIP").

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/v6/download/ubuntu/repo.png)


### 2.3 Copy and Add Additional Third-Party Libraries
◆ Copy part of Boost to the downloaded project folder.

In the OpenSiv3D project folder obtained in 2.2, there is a `Dependencies/boost_1_83_0/` folder. Copy the `boost_1_83_0/boost/` folder (approximately 120 MB), which is part of the Boost library prepared in 2.1, into this folder. After copying, it becomes `Dependencies/boost_1_83_0/boost/`.


### 2.4 Build Siv3D Library
◆ Build the Siv3D library with Xcode.

Open `macOS/OpenSiv3D.xcodeproj` in the OpenSiv3D project folder obtained in 2.2 with Xcode and build the target "Siv3D". A full build takes several minutes. When the build is complete, `libSiv3D.a` is generated.


### 2.5 Build Siv3D Apps
◆ Build Siv3D test apps with Xcode.

Next, build the target "Siv3D-Test". There is only one source code file: `macOS/Main.cpp`. The build takes a few seconds. When the build is complete, `Siv3D-Test.app` is generated.


### 2.6 (Optional) Overwrite Existing SDK Files

◆ Use the following steps if you want to use your self-built library from existing or new projects.

After building Siv3D yourself, the static library `libSiv3D.a` is generated. By overwriting the files in your installed OpenSiv3D SDK with this static library and the headers used for the build (`Siv3D/include/` folder), you can use your self-built version of Siv3D from existing and new projects. For safety, it is recommended to back up the library and header files before overwriting.


## 3. Linux
The normal setup procedure is the custom build procedure.