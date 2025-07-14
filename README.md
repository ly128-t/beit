# beit
1.[visual studio install]

2.[build opencv]

[precoditon]
-- install git
-- install Visual Studio 17 2022
-- install cmake 

[step 1]
git clone https://github.com/opencv/opencv
cd opencv
git checkout tags/4.10.0
mkdir build_msvc
cd build_msvc
cmake -S .. -G "Visual Studio 17 2022" -A ARM64 -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=ON -DBUILD_EXAMPLES=OFF -DBUILD_TESTS=OFF -DBUILD_PERF_TESTS=OFF -DBUILD_DOCS=OFF -DBUILD_opencv_world=OFF -DWITH_CUDA=OFF -DWITH_OPENCL=OFF -DWITH_OPENVINO=OFF -DWITH_TBB=OFF
cmake --build . --config Release

[check opencv build result]
cd bin\Release
dir /a/s *.dll 
you should find opencv_core4100.dll,opencv_imgcodecs4100.dll,opencv_imgproc4100.dll




3.[app step]
1. cmake -DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake -DCMAKE_BUILD_TYPE=Release
2. open beit.sln with visual studio. Select Release/ARM64. Then Build-build solution
3. Copy follow file to Release folder:
3rd\opencv_core4100.dll
3rd\opencv_imgcodecs4100.dll
3rd\opencv_imgproc4100.dll
3rd\QAI_AppBuilder-win_arm64-QNN2.34.0-Release\libappbuilder.dll
4. cd Release and run beit.exe
