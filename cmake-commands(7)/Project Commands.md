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

# add_subdirectory

将==子目录==添加到构建中。

```
add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL] [SYSTEM])
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
