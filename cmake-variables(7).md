https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html

# Variables that Provide Information

### PROJECT_NAME

**最近一次**调用`project()`命令（在**当前目录或更上层目录**中）所指定的项目名称。

若要获取顶层项目的名称，请参阅`CMAKE_PROJECT_NAME`变量。

### CMAKE_PROJECT_NAME

**顶层项目**的名称。即顶层`CMakeLists.txt`文件中通过`project()`命令指定的项目名称。

若顶层`CMakeLists.txt`包含多个`project()`命令，则该文件中最近一次调用的命令将决定`CMAKE_PROJECT_NAME`。

要获取当前目录或更上层目录中最近一次`project()`调用的名称，请参阅`PROJECT_NAME`变量。


# Variables that Change Behavior

### CMAKE_BUILD_TYPE

指定在单配置生成器（如[Makefile Generators](https://cmake.org/cmake/help/latest/manual/cmake-generators.7.html#makefile-generators)或[`Ninja`](https://cmake.org/cmake/help/latest/generator/Ninja.html#generator:Ninja "Ninja")）中使用的**构建类型**。典型值包括`Debug`、`Release`、`RelWithDebInfo`和`MinSizeRel`，但也可以自定义名称。

当项目第一次创建新的构建目录时，这个变量会在第一个调用`project()`或`enable_language()`命令时初始化。如果设置了环境变量[`CMAKE_BUILD_TYPE`](https://cmake.org/cmake/help/latest/envvar/CMAKE_BUILD_TYPE.html#envvar:CMAKE_BUILD_TYPE "CMAKE_BUILD_TYPE")，则会使用该环境变量的值。否则，当启用某种语言时，会选择一个与工具链相关的默认值。默认值通常是一个空字符串，但这通常并不是理想的做法，因为通常使用标准构建类型之一会更合适。

# Variables that Describe the System

### 操作系统判定

- ==`WIN32`==：当目标系统是 Windows（包括Win64）时，该变量被设为`True`。
- ==`LINUX`==：当目标系统是 Linux 时，该变量被设为`True`。
- ==`IOS`==：当目标系统是 iOS 时，该变量被设为`True`。
- ==`UNIX`==：当目标系统是 UNIX 或 UNIX-like（如[`APPLE`](https://cmake.org/cmake/help/latest/variable/APPLE.html#variable:APPLE "APPLE")和[`CYGWIN`](https://cmake.org/cmake/help/latest/variable/CYGWIN.html#variable:CYGWIN "CYGWIN")) 时，该变量被设为`True`。

### 编译器判定

- ==`MSVC`==：当编译器是 **Microsoft Visual C++** 的某个版本，或者是“模拟 Visual C++ `cl`命令行语法”的其他编译器时，该变量被设为`True`。