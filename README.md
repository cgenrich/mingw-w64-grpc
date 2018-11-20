# mingw-w64-benchmark
mingw arch linux build.

## Quick Start - Command Prompt
```bash
mkdir mingw-w64-grpc
cd mingw-w64-grpc
wget https://raw.githubusercontent.com/cgenrich/mingw-w64-grpc/master/PKGBUILD
yaourt -Pi .
```
## Dependencies
* Protocol Buffers
* GRPC
> These must be native tools due as they are used during cross-compilation and must be locally executable.

## Hacks applied
### CMakeLists.txt changes
The build environment is Linux but cross-compilation will create windows DLL and EXE files.  During the build .proto files are used to generate source code.  The correct Linux binary *protoc* is used but the **gRPC Plugin** was just built for *Windows* and can't be used.
Additionally a missing -lws2_32 flag is required to build the third party address_sorting module.
```bash
 sed -i 's!\$<TARGET_FILE:grpc_cpp_plugin>!/usr/bin/grpc_cpp_plugin!' CMakeLists.txt
  sed -i 's/target_link_libraries(address_sorting/target_link_libraries(address_sorting\n  ws2_32/' CMakeLists.txt

```
### CMake Hacks
*gRPC* requires _WIN32_WINNT to be specified to at least Windows Vista level.  This should be changed if you require a specific target.
```bash
      -DCMAKE_CXX_FLAGS=" -D_WIN32_WINNT=0x0600" \
      -DCMAKE_C_FLAGS=" -D_WIN32_WINNT=0x0600" \
      -DCMAKE_CPP_FLAGS=" -D_WIN32_WINNT=0x0600"
```
