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
During the installation process, you will be asked to choose the workloads you want and customize your installation. 
**At a minimum, selmake sure to check：**
* **Desktop development with C++**.


## 2.Build OpenCV
### 2.1 Prerequisites
- Visual Studio 2022 with C++ Desktop Development tools
- CMake
- Git

### 2.2 Setup OpenCV
Run following commands in Windows terminal:
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
cmake --build . --target INSTALL --config Release
```
The OpenCV DLL files can be found at:
```
C:\path\to\opencv\build_msvc\install\ARM64\vc17\bin
```
## 3.Install Dependencies
### 3.1 Setup vcpkg

#### 1.Clone the vcpkg Repository

```bash
git clone https://github.com/microsoft/vcpkg.git
cd vckg
.\bootstrap-vcpkg.bat
```
#### 2.Add to PATH
Add the full path to the vcpkg directory (e.g., C:\vcpkg) to your system PATH so you can run vcpkg from any terminal.

### 3.2 Set up xtensor
Run below command in Windows terminal:
```
vcpkg install xtensor
```
* If the download is too slow, you can download via the link.Put all the downloaded files in the the vcpkg directory (e.g., C:\vcpkg\dowloads).Then run the command again.
### 3.2 Configure Visual Studio Project for OpenCV

##### 1.Configure Include Directories

1. From the top menu in Visual Studio, select **Project →  Properties**.
2. Navigate to:

   ```
   Configuration Properties → C/C++ → General → Additional Include Directories
   ```
3. Add the following path:
   ```
   C:\path\to\opencv\build_msvc\install\include
   C:\path\to\Beit\3rd\QAI_AppBuilder-win_arm64-QNN2.34.0-Release\include
   ```
![ Additional dependencies](https://learn.arm.com/learning-paths/laptops-and-desktops/win-opencv/msvc_include_dir.png)


##### 2. Set Library Directories

Go to:
```
Configuration Properties → Linker → General → Additional Library Directories
```
Add:
```
C:\path\to\opencv\build_msvc\install\ARM64\vc17\lib
C:\path\to\Beit\3rd\QAI_AppBuilder-win_arm64-QNN2.34.0-Release
```


##### 3. Set Additional Dependencies

Go to:
```
Configuration Properties → Linker → Input → Additional Dependencies
```
Add the libraries you need:
```
opencv_core4100.lib，opencv_imgcodecs4100.lib，opencv_imgproc4100.lib,libappbuilder.lib
```

##### 4. Add DLL Path to System Environment Variable

Add the directory containing the `.dll` files to your system `PATH` environment variable, for example:
```
C:\path\to\opencv\build_msvc\install\ARM64\vc17\bin
```

 Click **OK** to apply the changes.


### 4.Run Application
#### Step 1: Set Up CMakeLists.txt and Source Code

Ensure your beit directory contains:
* CMakeLists.txt file (configured as shown below)
* beit.cpp and any additional source files
* Correct **DLL paths, image paths, and other hardcoded paths** updated inside beit.cpp
  
##### Edit CMakeLists.txt — Update OpenCV Paths
Make sure to modify the following path settings in your CMakeLists.txt file to match your local environment:
```
# ❗ Replace these with the actual paths where your OpenCV is installed:
set(Xtensor_DIR            "C:/vcpkg/installed/arm64-windows/share/xtensor")
set(Xtl_DIR                "C:/vcpkg/installed/arm64-windows/share/xtl")
set(OpenCV_INCLUDE_DIRS    "C:/audio/opencv/opencv/include")
set(OpenCV_BIN_DIRS        "C:/audio/opencv/opencv/build_msvc/b05/bin/Release")
set(OpenCV_LIBRARY_DIRS    "C:/audio/opencv/opencv/build_msvc/b05/lib/Release")
```
* These paths configure the include directories, DLL binaries, and static library locations for OpenCV and Xtensor. If your setup differs, be sure to update accordingly.

###### Edit beit.cpp — Update Model and DLL Paths
Inside your beit.cpp file, modify the hardcoded paths to match the location of your model and runtime libraries. For example:
```
// ❗ Update these paths to reflect your actual file locations
std::string model_path  = "C:/test/beit/model/beit-beit-qualcomm_snapdragon_x_elite-float.bin";
std::string backendLib  = "C:/test/beit/qai_libs_2.34/QnnHtp.dll";
std::string systemLib   = "C:/test/beit/qai_libs_2.34/QnnSystem.dll";
std::string image_path  = "C:/test/beit/dog2.jpg";
std::string json_path   = "C:/test/beit/imagenet_labels.json";
```
* It's recommended to eventually replace absolute paths with relative paths or configuration arguments to improve portability across machines.

#####  Build the Project with CMake 
In the Windows terminal, run the following command from the project root to configure the build:
```bash
cmake -DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake -DCMAKE_BUILD_TYPE=Release
```

#### Step 2: Build the Project in Visual Studio

1. Open beit.sln in Visual Studio.
2. Set the build configuration to Release and ARM64.
3. Go to Build > Build Solution
   
* If beit.sln does not exist, you can manually create a new Visual Studio solution. Please ensure that the solution is named beit.
* Otherwise, if you wish to use a different name for your solution or executable, you must also modify all occurrences of beit in the CMakeLists.txt file to match your chosen name. This includes the project(beit), add_executable(beit ...), and target_link_libraries(beit ...) entries.


#### Step 3: Copy Required DLLs to Release Folder
After building, copy the following DLL files to the Release/ directory:
```
path\to\opencv\build_msvc\install\ARM64\vc17\bin\opencv_core4100.dll
path\to\opencv\build_msvc\install\ARM64\vc17\bin\opencv_imgcodecs4100.dll
path\to\opencv\build_msvc\install\ARM64\vc17\bin\opencv_imgproc4100.dll
3rd\QAI_AppBuilder-win_arm64-QNN2.34.0-Release\libappbuilder.dll
```
#### Step 4: Run the Application
In the Windows terminal, navigate to the Release folder and run:
```
cd Release
./beit.exe
```
#### Step 5: Verifying Successful Execution
If the service prints output similar to the following, it indicates that beit.exe has run successfully:
```
"Miniature Poodle",: 62.0111%
"Toy Poodle",: 28.6134%
"Standard Poodle",: 0.724766%
"Yorkshire Terrier",: 0.200868%
"Norwich Terrier",: 0.128179%
```
