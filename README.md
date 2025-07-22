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

##  1.File Download & Setup Instructions
First, create a new empty Visual Studio project named **beit**. Inside the beit/ directory, create a folder named **3rd/**.
The final project structure should look like this:
```plaintext
beit/
├── 3rd/              # Third-party dependencies (e.g., QNN SDK, py3_wget)
├── model/            # Model files (e.g., .bin, .json)
├── qai_libs/         # QNN SDK runtime libraries (e.g., HTP backend)
├── beit.cpp          # C++ inference main program
├── input.jpg         # Sample input jpg
├── CMakeLists        # CMakeLists 
├── other             # Other files
└── README.md         # Project documentation
```

#### Step 1:Download QAI Launcher
Open a terminal and run:
```
mkdir C:\ai-hub\
cd C:\ai-hub\
```
Click here to download [QAI_Launcher_v1.0.0.zip](https://github.com/quic/ai-engine-direct-helper/releases/download/v2.34.0/QAI_Launcher_v1.0.0.zip).Unzip the downloaded file into the **C:\ai-hub** directory.
Run the following script to install all required dependencies and tools:
```
.\1.Install_QAI_AppBuilder.bat
```
#### Step 2:Launch Python Enviroment
Activate the pre-configured Python environment:
```
.\8.Start_PythonEnv.bat
pip install py3-wget
cd ../ai-engine-direct-helper/samples
python python\beit\beit.py
```
#### Step 3: Prepare Inference Resources
Download and organize the essential files required for BEiT model inference:
* Model Weights (.bin): Pre-trained BEiT model file (e.g., beit-beit-qualcomm_snapdragon_x_elite-float.bin) containing learned parameters.
* Label Mapping (imagenet_labels.txt): Maps model output indices to human-readable ImageNet class names.
* Sample Image (input.jpg): A test image used to verify the inference pipeline.

Place all files in the appropriate directories under your local beit/ project folder to ensure the inference script can access them correctly.

From the official sample repository:
```
ai-engine-direct-helper/samples/
```
Copy the entire **qai_libs/** folder into your local beit/ project directory.
Then navigate to:
```
ai-engine-direct-helper/samples/python/beit/
```
Run below command in Windows terminal:
```
python python\beit\beit.py
```
Copy the following files into your local beit/ directory:
* model/ folder (contains the BEIT model files)
* input.jpg (sample image for inference)

#### Step 4: Download QAI_AppBuilder

Click here to download [QAI_AppBuilder-win_arm64-QNN2.34.0-Release](https://github.com/quic/ai-engine-direct-helper/releases/download/v2.34.0/QAI_AppBuilder-win_arm64-QNN2.34.0-Release.zip).Unzip the contents of the archive into the 3rd/ folder inside your beit/ project directory. The structure should look like:
```plaintext
beit/
├── 3rd/
│   └── QAI_AppBuilder/
```



## 2.Install Dependencies
### 2.1 Setup vcpkg

#### 1.Clone the vcpkg Repository

```bash
git clone https://github.com/microsoft/vcpkg.git
cd vcpkg
.\bootstrap-vcpkg.bat
```
#### 2.Add to PATH
Add the full path to the vcpkg directory (e.g., C:\vcpkg) to your system PATH so you can run vcpkg from any terminal.

### 2.2 Set up xtensor
Run below command in Windows terminal:
```
vcpkg install xtensor
```
* If the download is too slow, you can download via the link.Put all the downloaded files in the the vcpkg directory (e.g., C:\vcpkg\dowloads).Then run the command again.


### 2.3 Set up OpenCV
#### Option 1：
Run below command in Windows terminal:
```
vcpkg install opencv4[calib3d,directml,dshow,fs,gapi,highgui,intrinsics,jpeg,msmf,png,quirc,thread,tiff,webp,win32ui]:arm64-windows
```

#### Option 2：
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


**Troubleshooting: wchar.h Compilation Error** 
If you encounter the following error during compilation:
```
error C2664: “__n64 __uint64x1_t_to_n64(uint64x1_t)”: cannot convert argument 1 from “uint16x4_t” to “uint64x1_t”
```
Then download wchar.h and replace the original one located at C:\Program Files (x86)\Windows Kits\10\Include\10.0.26100.0\ucrt\wchar.h.

### 3.Run Application
#### Step 1: Setup CMakeLists.txt and Source Code

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
* The OpenCV DLL files can be found at:
```
C:\path\to\opencv\build_msvc\install\ARM64\vc17\bin
```
* The OpenCV lib files can be found at:
```
C:\path\to\opencv\build_msvc\install\ARM64\vc17\lib
```

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
cmake --build build --config Release
```

#### Step 2: Copy Required DLLs to Release Folder
After building, copy the following DLL files to the **Release/** directory:
```
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
