https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html

# Binary Targets

==可执行文件（Executables）==和==库（libraries）==使用`add_executable()`和`add_library()`命令定义。生成的二进制文件会根据目标平台自动添加适当的前缀（[`PREFIX`](https://cmake.org/cmake/help/latest/prop_tgt/PREFIX.html#prop_tgt:PREFIX "PREFIX")）、后缀（[`SUFFIX`](https://cmake.org/cmake/help/latest/prop_tgt/SUFFIX.html#prop_tgt:SUFFIX "SUFFIX")）和扩展名。二进制目标之间的依赖关系使用`target_link_libraries()`命令表示。

案例：
```cmake
add_library(archive archive.cpp zip.cpp lzma.cpp)
add_executable(zipapp zipapp.cpp)
target_link_libraries(zipapp archive)
```

`archive`被定义为一个**静态库**——一个包含“由`archive.cpp`、`zip.cpp`和`lzma.cpp`编译得到的目标”的归档文件。`zipapp`被定义为一个**可执行文件**，由编译和链接`zipapp.cpp`生成。在链接可执行文件`zipapp`时，会将静态库`archive`链接进去。

### Executables

**可执行文件**（Executables）是由多个**目标文件**链接而成的二进制文件，这些目标文件中至少有一个要包含程序的入口点（例如`main`函数）。

[`add_executable()`](https://cmake.org/cmake/help/latest/command/add_executable.html#command:add_executable "add_executable")命令定义一个可执行目标：`add_executable(mytool mytool.cpp)`。

CMake 会生成相应的构建规则，==将源文件编译为目标文件，并将这些目标文件链接成一个可执行文件==。

通过[`target_link_libraries()`](https://cmake.org/cmake/help/latest/command/target_link_libraries.html#command:target_link_libraries "target_link_libraries")命令链接可执行文件的依赖。链接器首先会从“由可执行文件自身的源文件编译得到的目标文件”开始，然后在链接的库中查找来解决剩余的符号依赖。


