https://cmake.org/cmake/help/latest/manual/cmake-commands.7.html#project-commands

These commands are available only in CMake projects.


# project

用于设置项目名称。

```
project(<项目名>)
```

设置项目名称，并将其存储在变量`PROJECT_NAME`中。当从顶层`CMakeLists.txt`调用时，还会将项目名称存储在`CMAKE_PROJECT_NAME`变量中。

# add_executable

向项目添加一个可执行文件，使用指定的源文件。

```
add_executable(<name> <options>... <sources>...)
```

添加一个名为`<name>`的可执行文件目标，它将由命令中列出的**源文件**构建而成。（个人笔记：仅需列出源文件(.cpp)，==无需列出头文件(.h)==）

- `<name>`对应逻辑目标名称，在项目内必须全局唯一。实际构建的可执行文件名取决于平台（例如`<name>.exe`或直接为`<name>`）。

- `<options>`有3个可选项：
   ①`WIN32`：将目标属性[`WIN32_EXECUTABLE`](https://cmake.org/cmake/help/latest/prop_tgt/WIN32_EXECUTABLE.html#prop_tgt:WIN32_EXECUTABLE "WIN32_EXECUTABLE")设为`true`。
   ②`MACOSX_BUNDLE`：略
   ③`EXCLUDE_FROM_ALL`：略

- `sources`：如果源文件在稍后使用`target_sources()`指令给出，则源文件可以省略。

# add_library

使用指定的源文件，向项目添加一个库。

### Normal Libraries

```
add_library(<名称> [<类型>] <源文件>...)
```

添加一个名为`<名称>`的库目标，该库将由命令中列出的`<源文件>...`构建。

可选的`<类型>`用于指定要创建的库类型：
- `STATIC`：[Static Library](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#static-libraries)：==an archive of object files for use when linking other targets==.
- `SHARED`：[Shared Library](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#shared-libraries)：==一个动态库，可被其他目标链接，并在运行时加载==。
- `MODULE`：[Module Library](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#module-libraries)：==一个插件（plugin），可能无法被其他目标链接，但可以在运行时使用类似`dlopen`的方式动态加载==。
如果未指定`<类型>`，则默认为`STATIC`或`SHARED`，具体取决于变量[`BUILD_SHARED_LIBS`](https://cmake.org/cmake/help/latest/variable/BUILD_SHARED_LIBS.html#variable:BUILD_SHARED_LIBS "BUILD_SHARED_LIBS")的取值。

`<名称>`是逻辑目标名，必须在整个项目中全局唯一。实际构建出的库的文件名由平台规则决定（如==`lib<名称>.a`==或==`<名称>.lib`==），要修改最终文件名的`<名称>`部分，见目标属性[`OUTPUT_NAME`](https://cmake.org/cmake/help/latest/prop_tgt/OUTPUT_NAME.html#prop_tgt:OUTPUT_NAME "OUTPUT_NAME")。

`<源文件>...`可以省略——如果它们在之后通过[`target_sources()`](https://cmake.org/cmake/help/latest/command/target_sources.html#command:target_sources "target_sources")添加。

---

1. 如果一个库不导出任何符号，则不能将其声明为`SHARED`库。例如，一个 Windows resource DLL 或一个不导出任何 unmanaged symbols 的 managed C++/CLI DLL 就需要被声明为`MODULE`库。这是因为==在 Windows 上，CMake 期望`SHARED`库必须具有对应的导入库（import library）==。

2. 默认情况下，库文件会被创建在构建树中**与该 add_library 命令所在的源码树目录相对应**的目录下。要修改该位置，见[`ARCHIVE_OUTPUT_DIRECTORY`](https://cmake.org/cmake/help/latest/prop_tgt/ARCHIVE_OUTPUT_DIRECTORY.html#prop_tgt:ARCHIVE_OUTPUT_DIRECTORY "ARCHIVE_OUTPUT_DIRECTORY")、[`LIBRARY_OUTPUT_DIRECTORY`](https://cmake.org/cmake/help/latest/prop_tgt/LIBRARY_OUTPUT_DIRECTORY.html#prop_tgt:LIBRARY_OUTPUT_DIRECTORY "LIBRARY_OUTPUT_DIRECTORY")、[`RUNTIME_OUTPUT_DIRECTORY`](https://cmake.org/cmake/help/latest/prop_tgt/RUNTIME_OUTPUT_DIRECTORY.html#prop_tgt:RUNTIME_OUTPUT_DIRECTORY "RUNTIME_OUTPUT_DIRECTORY")。


# add_subdirectory

将==子目录==添加到构建中。

```
add_subdirectory(source_dir [binary_dir])
```

`source_dir`指定源目录，其中包含`CMakeLists.txt`和代码文件。它常常是**相对路径**（==典型用法==），此时会根据当前目录进行求值(evaluate)，也可以是**绝对路径**。

`binary_dir`指定**用于存放输出文件的目录**。如果它是相对路径，则会根据当前的**输出目录**进行求值，也可以是绝对路径。如果未指定，则会使用`source_dir`的值（==典型用法==）。

`source_dir`中的`CMakeLists.txt`文件会**立即**被 CMake 处理，然后才处理当前输入文件中`add_subdirectory`之后的命令。


# add_compile_definitions

向源文件的编译过程添加==预处理定义==。

```
add_compile_definitions(<definition> ...)
```

【案例】

```
add_compile_definitions(QT_NO_DEBUG_OUTPUT)
```

上述代码向源文件的编译过程添加了一个宏定义`QT_NO_DEBUG_OUTPUT`。

# target_include_directories

```
target_include_directories(<target> [SYSTEM]
  <INTERFACE|PUBLIC|PRIVATE> [items1...]
  [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```

指定在编译`<target>`时==要使用的 include 目录==。

`<INTERFACE|PUBLIC|PRIVATE>`必不可少，用于指定后续实参的[作用域](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#target-command-scope)：
- `PRIVATE`和`PUBLIC`的`item`会被写入`<target>`的[`INCLUDE_DIRECTORIES`](https://cmake.org/cmake/help/latest/prop_tgt/INCLUDE_DIRECTORIES.html#prop_tgt:INCLUDE_DIRECTORIES "INCLUDE_DIRECTORIES")属性
- `PUBLIC`和`INTERFACE`的`item`会被写入`<target>`的[`INTERFACE_INCLUDE_DIRECTORIES`](https://cmake.org/cmake/help/latest/prop_tgt/INTERFACE_INCLUDE_DIRECTORIES.html#prop_tgt:INTERFACE_INCLUDE_DIRECTORIES "INTERFACE_INCLUDE_DIRECTORIES")属性。

后面的实参指定 include 目录。

指定的 include 目录可以是**绝对路径**，也可以是**相对路径**。==相对路径将被解释为==相对于当前源目录（即==[`CMAKE_CURRENT_SOURCE_DIR`](https://cmake.org/cmake/help/latest/variable/CMAKE_CURRENT_SOURCE_DIR.html#variable:CMAKE_CURRENT_SOURCE_DIR "CMAKE_CURRENT_SOURCE_DIR")==），并在存入关联目标属性之前转换为绝对路径。

对一个`<target>`多次调用该命令，会按顺序依次**追加**这些`item`。

---

如果指定了`SYSTEM`，在某些平台上 CMake 会通知编译器：这些目录是**系统include目录**。这可能会带来以下效果（取决于编译器）：比如抑制警告，或者在依赖分析中跳过目录中的头文件。此外，无论指定了怎样的顺序，**系统include目录**都会在普通include目录**之后**被搜索。

如果`SYSTEM`与`PUBLIC`或`INTERFACE`一起使用，那么这样的目录还会被写入`<target>`的[`INTERFACE_SYSTEM_INCLUDE_DIRECTORIES`](https://cmake.org/cmake/help/latest/prop_tgt/INTERFACE_SYSTEM_INCLUDE_DIRECTORIES.html#prop_tgt:INTERFACE_SYSTEM_INCLUDE_DIRECTORIES "INTERFACE_SYSTEM_INCLUDE_DIRECTORIES")属性。

---

传递给`target_include_directories`的实参可以使用形如`$<...>`的生成器表达式，见[`cmake-generator-expressions(7)`](https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html#manual:cmake-generator-expressions\(7\) "cmake-generator-expressions(7)")。

# target_link_libraries

指定在链接**目标**和/或**其依赖目标**时要使用的库。链接库目标的 [Usage requirements](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#target-usage-requirements) 会被传递。一个目标的依赖项的 [Usage requirements](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#target-usage-requirements) 会影响目标自身源文件的编译。换句话说：

> 假设你有一个目标`A`，它通过`target_link_libraries(A libX)`链接了库`libX`。`libX`可能有自己的 usage requirements，那么这些 usage requirements 会传递给`A`，影响`A`的编译。

### 概述

该命令有几种形式，详见下面的各小节。它们的一般形式为：

```
target_link_libraries(<目标> ... <item>... ...)
```

`<目标>`必须已被创建（使用[`add_executable()`](https://cmake.org/cmake/help/latest/command/add_executable.html#command:add_executable "add_executable")或[`add_library()`](https://cmake.org/cmake/help/latest/command/add_library.html#command:add_library "add_library")等创建）。如果策略[`CMP0079`](https://cmake.org/cmake/help/latest/policy/CMP0079.html#policy:CMP0079 "CMP0079")未设置为`NEW`，则`<目标>`必须是在**当前目录**中创建的。对同一个`<目标>`的多次调用会按调用顺序将`<item>`追加到该目标的链接列表中。

`<item>`可以是：

- **库目标名**：==库目标==必须通过**项目内的**[`add_library()`](https://cmake.org/cmake/help/latest/command/add_library.html#command:add_library "add_library")命令创建，或作为一个[导入库](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#imported-targets)。如果库目标是在项目中创建的，==构建系统会确保在`<目标>`链接库目标之前，库目标是最新的==。

  生成的链接命令将含有与该库目标关联的可链接库文件的完整路径；但如果一个[导入库](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#imported-targets)设置了[`IMPORTED_NO_SONAME`](https://cmake.org/cmake/help/latest/prop_tgt/IMPORTED_NO_SONAME.html#prop_tgt:IMPORTED_NO_SONAME "IMPORTED_NO_SONAME")目标属性，CMake 可能会让链接器去搜索该库，而不是使用完整路径（例如`/usr/lib/libfoo.so`会变成`-lfoo`）。
  
  ==如果库文件发生变化，构建系统会重新链接`<目标>`。==

- **库文件的完整路径**：生成的链接命令通常会保留库文件的完整路径；但有些情况下，CMake 可能会让链接器去搜索库（例如`/usr/lib/libfoo.so`会变成`-lfoo`），比如当`SHARED`库被检测到没有`SONAME`字段时。==如果库文件发生变化，构建系统会重新链接`<目标>`。==

- **直接库名**：生成的链接命令会让链接器搜索该库（例如`foo`会变成`-lfoo`或`foo.lib`）。库名/链接标志会被视为命令行字符串片段。

- **生成器表达式**：略

包含`::`的`<item>`（例如==`Foo::Bar`==）被认定为[导入](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#imported-targets)或[别名](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#alias-targets)库目标名称，如果不存在这样的目标，会导致错误。见策略[`CMP0028`](https://cmake.org/cmake/help/latest/policy/CMP0028.html#policy:CMP0028 "CMP0028")。

### Libraries for a Target and/or its Dependents

```
target_link_libraries(<target>
                      <PRIVATE|PUBLIC|INTERFACE> <item>...
                     [<PRIVATE|PUBLIC|INTERFACE> <item>...]...)
```

`PUBLIC`/`PRIVATE`/`INTERFACE`[作用域](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html#target-command-scope)关键字可以在一条命令中同时指定**目标自身需要链接的库**，以及**这些库是否成为链接接口的一部分**。

- 位于`PUBLIC`后的`<item>`：会被链接到`<target>`，也会成为链接接口的一部分。
- 位于`PRIVATE`后的`<item>`：会被链接到`<target>`，但不会成为链接接口的一部分。
- 位于`INTERFACE`后的`<item>`：只会成为链接接口的一部分，不会用于链接`<target>`。

### Libraries for both a Target and its Dependents

```
target_link_libraries(<target> <item>...)
```

使用这个形式时，库依赖默认是**传递的**。也就是说，当此目标被链接到另一个目标时，链接到此目标的库也将出现在另一目标的链接命令行中。
