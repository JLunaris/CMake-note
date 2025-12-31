https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html

# Variables that Provide Information

### PROJECT_NAME

**最近一次**调用`project()`命令（在**当前目录或更上层目录**中）所指定的项目名称。

若要获取顶层项目的名称，请参阅`CMAKE_PROJECT_NAME`变量。

### CMAKE_PROJECT_NAME

**顶层项目**的名称。即顶层`CMakeLists.txt`文件中通过`project()`命令指定的项目名称。

若顶层`CMakeLists.txt`包含多个`project()`命令，则该文件中最近一次调用的命令将决定`CMAKE_PROJECT_NAME`。

要获取当前目录或更上层目录中最近一次`project()`调用的名称，请参阅`PROJECT_NAME`变量。

### CMAKE_CURRENT_SOURCE_DIR

cmake 当前正在处理的源目录的完整路径。

当在[`cmake -P`](https://cmake.org/cmake/help/latest/manual/cmake.1.html#cmdoption-cmake-P)脚本模式运行时，CMake 会把以下变量设置为当前工作目录：
- [`CMAKE_BINARY_DIR`](https://cmake.org/cmake/help/latest/variable/CMAKE_BINARY_DIR.html#variable:CMAKE_BINARY_DIR "CMAKE_BINARY_DIR")
- [`CMAKE_SOURCE_DIR`](https://cmake.org/cmake/help/latest/variable/CMAKE_SOURCE_DIR.html#variable:CMAKE_SOURCE_DIR "CMAKE_SOURCE_DIR")
- [`CMAKE_CURRENT_BINARY_DIR`](https://cmake.org/cmake/help/latest/variable/CMAKE_CURRENT_BINARY_DIR.html#variable:CMAKE_CURRENT_BINARY_DIR "CMAKE_CURRENT_BINARY_DIR")
- `CMAKE_CURRENT_SOURCE_DIR`

对`CMAKE_CURRENT_SOURCE_DIR`进行修改会产生**未定义行为**。

# Variables that Change Behavior

### CMAKE_BUILD_TYPE

指定在单配置生成器（如[Makefile Generators](https://cmake.org/cmake/help/latest/manual/cmake-generators.7.html#makefile-generators)或[`Ninja`](https://cmake.org/cmake/help/latest/generator/Ninja.html#generator:Ninja "Ninja")）中使用的**构建类型**。典型值包括`Debug`、`Release`、`RelWithDebInfo`和`MinSizeRel`，但也可以自定义名称。

当项目第一次创建新的构建目录时，这个变量会在第一个调用`project()`或`enable_language()`命令时初始化。如果设置了环境变量[`CMAKE_BUILD_TYPE`](https://cmake.org/cmake/help/latest/envvar/CMAKE_BUILD_TYPE.html#envvar:CMAKE_BUILD_TYPE "CMAKE_BUILD_TYPE")，则会使用该环境变量的值。否则，当启用某种语言时，会选择一个与工具链相关的默认值。默认值通常是一个空字符串，但这通常并不是理想的做法，因为通常使用标准构建类型之一会更合适。

### CMAKE_PREFIX_PATH

用分号分隔的目录列表（[[cmake-language(7)#列表]]），用于指定[`find_package()`](https://cmake.org/cmake/help/latest/command/find_package.html#command:find_package "find_package"), [`find_program()`](https://cmake.org/cmake/help/latest/command/find_program.html#command:find_program "find_program"), [`find_library()`](https://cmake.org/cmake/help/latest/command/find_library.html#command:find_library "find_library"), [`find_file()`](https://cmake.org/cmake/help/latest/command/find_file.html#command:find_file "find_file"), [`find_path()`](https://cmake.org/cmake/help/latest/command/find_path.html#command:find_path "find_path")命令的搜索**前缀**。这些命令会按照其自身文档中的说明，==在这些前缀后面自动拼接合适的子目录==（如`bin`、`lib`、`include`）。例如：

```
/opt/mylib + lib → /opt/mylib/lib
/opt/mylib + bin → /opt/mylib/bin
```

默认情况下该变量为空。通常由项目自行设置。

另外还存在一个同名的环境变量[`CMAKE_PREFIX_PATH`](https://cmake.org/cmake/help/latest/envvar/CMAKE_PREFIX_PATH.html#envvar:CMAKE_PREFIX_PATH "CMAKE_PREFIX_PATH")，它会作为额外的搜索前缀列表被使用。

# Variables that Describe the System

### 操作系统判定

- ==`WIN32`==：当目标系统是 Windows（包括Win64）时，该变量被设为`True`。
- ==`LINUX`==：当目标系统是 Linux 时，该变量被设为`True`。
- ==`IOS`==：当目标系统是 iOS 时，该变量被设为`True`。
- ==`UNIX`==：当目标系统是 UNIX 或 UNIX-like（如[`APPLE`](https://cmake.org/cmake/help/latest/variable/APPLE.html#variable:APPLE "APPLE")和[`CYGWIN`](https://cmake.org/cmake/help/latest/variable/CYGWIN.html#variable:CYGWIN "CYGWIN")) 时，该变量被设为`True`。

### 编译器判定

- ==`MSVC`==：当编译器是 **Microsoft Visual C++** 的某个版本，或者是“模拟 Visual C++ `cl`命令行语法”的其他编译器时，该变量被设为`True`。