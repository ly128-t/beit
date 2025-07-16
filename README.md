<br>

<div align="center">
  <h3>Run the model locally on NPU, deploy the AI-Hub model quickly.</h3>
  <p><i> SIMPLE | EASY | FAST </i></p>
</div>
<br>

## Disclaimer
This software is provided “as is,” without any express or implied warranties. The authors and contributors shall not be held liable for any damages arising from its use. The code may be incomplete or insufficiently tested. Users are solely responsible for evaluating its suitability and assume all associated risks.
Note: Contributions are welcome. Please ensure thorough testing before deploying in critical systems.

## Introduction
This guide helps developers setup C++ environment for using QAI AppBuilder to run sample code on Windows on Snapdragon (WoS) platforms.

## 1.Install Visual Studio 
You should install [Visual Studio 2022 or higher](https://visualstudio.microsoft.com/vs/). 
###
During the installation process, you will be asked to choose the workloads you want and customize your installation. At a minimum, select Desktop development with C++.


To build native Windows Applications using the LLVM toolchain in Visual Studio, you need to install additional components.

<!--
In the installer, select the Individual components tab. Enter clang in the search bar.Check:
✅ LLVM compiler toolchain
✅ MSBuild support for LLVM
-->
## 2.Build OpenCV

### 2.1Prerequisites

- Visual Studio 2022 with C++ Desktop Development tools
- CMake
- Git

### 2.2Steps

```bash
# Clone the OpenCV repository
git clone https://github.com/opencv/opencv
cd opencv

# Checkout the specific version
git checkout tags/4.10.0

# Create and enter the build directory
mkdir build_msvc
cd build_msvc

# Configure the build with CMake
cmake -S .. -G "Visual Studio 17 2022" -A ARM64 -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=ON -DBUILD_EXAMPLES=OFF -DBUILD_TESTS=OFF -DBUILD_PERF_TESTS=OFF -DBUILD_DOCS=OFF -DBUILD_opencv_world=OFF -DWITH_CUDA=OFF -DWITH_OPENCL=OFF -DWITH_OPENVINO=OFF -DWITH_TBB=OFF

# Build the project
cmake --build . --config Release
```

## 3.Build App
Install Dependencies：
vcpkg，xtensor

### Set Up vcpkg

#### 1. Clone the vcpkg Repository

```bash
git clone https://github.com/microsoft/vcpkg.git
option1：
cd vckg
.\bootstrap-vcpkg.bat

option2：
./bootstrap-vcpkg.sh
.\vcpkg integrate install
```
#### Set Environment Variables (Recommended)
To make vcpkg easier to use and integrate with Visual Studio:

##### Add to PATH
Add the full path to the vcpkg directory (e.g., C:\vcpkg) to your system PATH so you can run vcpkg from any terminal.Reboot the computer.

###### Set Up xtonsor

vcpkg install xtensor
#### Configure Visual Studio Project for OpenCV
Select Project from the top menu and click on TestOpenCV properties.
Edit Include directories, Library directories, and Additional dependencies as shown in the images below, and then click OK.
![ Additional dependencies](https://learn.arm.com/learning-paths/laptops-and-desktops/win-opencv/msvc_include_dir.png)


1. Run below commands in Windows terminal:
```bash
   cmake -DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake -DCMAKE_BUILD_TYPE=Release
```
2. Open beit.sln with Visual Studio. Select Release and ARM64 as the build configuration. Then go to Build > Build Solution.
If beit.sln does not exist, you can create a new Visual Studio solution manually and add the necessary source files and CMake project.
3. Copy follow file to Release folder:
3rd\opencv_core4100.dll
3rd\opencv_imgcodecs4100.dll
3rd\opencv_imgproc4100.dll
3rd\QAI_AppBuilder-win_arm64-QNN2.34.0-Release\libappbuilder.dll
4. cd Release and run beit.exe
