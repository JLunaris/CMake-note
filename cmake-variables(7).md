https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html

# Variables that Provide Information

### PROJECT_NAME

**最近一次**调用`project()`命令（在**当前目录或更上层目录**中）所指定的项目名称。

若要获取顶层项目的名称，请参阅`CMAKE_PROJECT_NAME`变量。

### CMAKE_PROJECT_NAME

**顶层项目**的名称。即顶层`CMakeLists.txt`文件中通过`project()`命令指定的项目名称。

若顶层`CMakeLists.txt`包含多个`project()`命令，则该文件中最近一次调用的命令将决定`CMAKE_PROJECT_NAME`。

要获取当前目录或更上层目录中最近一次`project()`调用的名称，请参阅`PROJECT_NAME`变量。

