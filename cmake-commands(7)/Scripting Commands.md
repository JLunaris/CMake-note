https://cmake.org/cmake/help/latest/manual/cmake-commands.7.html#scripting-commands

These commands are always available.


# find_package

> [`Using Dependencies Guide`](https://cmake.org/cmake/help/latest/guide/using-dependencies/index.html#guide:Using%20Dependencies%20Guide "Using Dependencies Guide")对这一主题提供了一个高层次的介绍。它更全面地说明了`find_package()`命令在整个依赖管理体系中的位置，包括它与[`FetchContent`](https://cmake.org/cmake/help/latest/module/FetchContent.html#module:FetchContent "FetchContent")模块之间的关系。建议在阅读下面的详细内容之前，先阅读该指南。

查找一个包（通常由项目外部提供），并加载该包的特定信息。对该命令的调用也可以被[dependency providers](https://cmake.org/cmake/help/latest/command/cmake_language.html#dependency-providers)拦截。

### Typical Usage

对`find_package()`的大多数调用通常具有以下形式：

```
find_package(<PackageName> [<version>] [REQUIRED] [COMPONENTS <components>...])
```

==`<PackageName>`==是唯一必需的参数；==`<version>`==通常省略；如果项目没有该包就无法成功配置，则应使用==`REQUIRED`==。某些更复杂的包支持**组件**（components），可以通过==`COMPONENTS`==关键字选择所需组件，但大多数包并没有那种复杂程度。

以上是[基本语法](https://cmake.org/cmake/help/latest/command/find_package.html#basic-signature)的简化形式，项目应尽可能使用这种简化形式，这样能降低复杂性，并**使包能够以更多不同的方式被找到或被提供**。

### Basic Signature

```
find_package(<PackageName> [<version>] [EXACT] [REQUIRED] [COMPONENTS <components>...])
```

`<version>`实参用于指定所查找的包**应该兼容的版本**。它有两种形式：
- **single version**：格式为`major[.minor[.patch[.tweak]]]`，其中每个部分都是数字。
- **range version**：格式为`versionMin...[<]versionMax`，其中`versionMin`和`versionMax`的格式与 single version 相同。默认情况下，range version 的两个端点都包含在内。如果在`...`前加`<`，则上限端点将被排除。

`EXACT`选项表示指定的版本必须**完全匹配**。该选项不能与 range version 一起使用。


# set

将一个**普通变量**、**环境变量**或**缓存项**设置为给定的值。有关变量的说明见[[cmake-language(7)]]。

本小节的所有命令格式中，占位符 ==`<值>...`== 表示该位置可以接受**零个或多个实参**。如果提供多个实参，它们会被连成一个[semicolon-separated list](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#cmake-language-lists)，以形成要 set 的实际变量值。

### 设置普通变量

```
set(<变量> <值>... [PARENT_SCOPE])
```

在当前**函数作用域**或**目录作用域**中 set 或 unset `<变量>`：
- 如果提供了至少一个`<值>`，则将变量设置为该值。
- 如果没有提供值，则 unset 该变量。这等价于[`unset(<变量>)`](https://cmake.org/cmake/help/latest/command/unset.html#command:unset "unset")。

如果指定了`[PARENT_SCOPE]`，则变量会被设置到当前作用域的**上一层作用域**（即父目录/外层调用函数/外层作用域）中。变量在**当前作用域**中的先前状态保持不变（如果它之前是未定义的，它仍然未定义；如果它之前有值，它仍然保持那个值）。

> 创建作用域的方式有 3 种：
> ①每个==新目录==会创建一个新作用域。
> ②每个==[`function()`](https://cmake.org/cmake/help/latest/command/function.html#command:function "function")命令==会创建一个新作用域。
> ③可以通过==[`block()`](https://cmake.org/cmake/help/latest/command/block.html#command:block "block")命令==显式创建作用域。

要更新父作用域的变量，除了使用`set()`命令的`[PARENT_SCOPE]`选项外，还可以使用[`block()`](https://cmake.org/cmake/help/latest/command/block.html#command:block "block(propagate)")命令和[`return()`](https://cmake.org/cmake/help/latest/command/return.html#command:return "return(propagate)")命令的`[PROPAGATE]`选项。

> 注意：求值**变量引用**（`${VAR}`）时，CMake 会先查找具有该名称的**普通变量**；如果不存在，CMake 将查找具有该名称的**缓存变量**。因此，unset 一个普通变量可能会暴露出一个之前被隐藏的缓存变量。为了强制`${VAR}`返回空字符串，可使用`set(<变量> "")`，这会清空该普通变量但保留其“defined”状态。

### 设置环境变量

```
set(ENV{<变量>} [<值>])
```

将一个[`环境变量`](https://cmake.org/cmake/help/latest/manual/cmake-env-variables.7.html#manual:cmake-env-variables\(7\) "cmake-env-variables(7)")设置为给定的值。之后的`$ENV{<变量>}`都会返回这个新的值。

此命令**只影响当前 CMake 进程**，不会影响调用 CMake 的那个外部进程，更不会影响系统环境变量，也不会影响后续的构建或测试进程的环境。

如果在`ENV{<变量>}`的后面没有给出任何值，或者`<值>`是空字符串，则该命令会清空该环境变量的已有值。

### 设置缓存项

略
# set_property

```
set_property(<GLOBAL                      |
              DIRECTORY [<dir>]           |
              TARGET    [<target1> ...]   |
              SOURCE    [<src1> ...]
                        [DIRECTORY <dirs> ...]
                        [TARGET_DIRECTORY <targets> ...] |
              INSTALL   [<file1> ...]     |
              TEST      [<test1> ...]
                        [DIRECTORY <dir>] |
              CACHE     [<entry1> ...]    >
             [APPEND] [APPEND_STRING]
             PROPERTY <name> [<value1> ...])
```

在指定的作用域中设置一个具名属性。第一个实参用于指定作用域，必须是以下之一：

- `GLOBAL`：作用域是独一无二的，因此不能指定名称。
- `DIRECTORY`：略
- `TARGET`：可指定多个已存在的`<target>`。
- `SOURCE`：略
- `INSTALL`：略
- `TEST`：略
- `CACHE`：略

`PROPERTY`是必需的，其后跟随==要设置的属性的名称==，再后面的参数会被用来组成==属性的值==，若有多个值则会被存储为**用分号(`;`)分隔的列表**。

如果使用了`APPEND`，则该**列表**会被追加到现有的**属性值**后面（但空值会被忽略，不会被追加）。如果使用了`APPEND_STRING`，则该**字符串**会被追加到现有的**字符串属性值**后面，即形成一个更长的字符串，而不是字符串列表。

[`cmake-properties(7)`](https://cmake.org/cmake/help/latest/manual/cmake-properties.7.html#manual:cmake-properties\(7\) "cmake-properties(7)")列出了每个作用域中的全部属性。

# if

```cmake
if(<条件>)
  <命令>
elseif(<条件>) # 可选，可以重复
  <命令>
else() # 可选
  <命令>
endif()
```

对`if`子句的`<条件>`实参进行求值（求值依据见[Condition syntax](https://cmake.org/cmake/help/latest/command/if.html#condition-syntax)）。如果结果为true，则执行`if`子句中的`<命令>`；否则，会以同样的方式处理可选的`elseif`块。最终，如果没有`<条件>`为真，则执行可选的`else`块中的`<命令>`。

### 基本表达式

##### `if(<常量>)`

当常量是下列之一时为**真（True）**：`1`、`ON`、`YES`、`TRUE`、`Y`、一个非零数（包括浮点数）
当常量是下列之一时为**假（False）**：`0`、`OFF`、`NO`、`FALSE`、`N`、`IGNORE`、`NOTFOUND`、空字符串、以`-NOTFOUND`结尾。

这些常量大小写不敏感。如果实参不是上述常量，则被当作**变量**或**字符串**处理（见[Variable Expansion](https://cmake.org/cmake/help/latest/command/if.html#variable-expansion)和下面两个形式）。

##### `if(<变量>)`

当`<变量>`==已被定义==，且==定义的值不是false常量==时为**真（True）**，否则为**假（False）**（包括该变量未被定义的情况）。

注意：宏实参（macro arguments）不是变量。[Environment Variables](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#cmake-language-environment-variables)也不能用这种方式检测，例如对`if(ENV{some_var})`的求值始终为假。

##### `if(<字符串>)`

一个带引号的字符串总是被求值为 false，除非字符串的值是某个 true 常量（即`"ON"`、`"TRUE"`、`"YES"`、`"1"`等）。

### 逻辑运算

##### `if(NOT <条件>)`

##### `if(<条件1> AND <条件2>)`

##### `if(<条件1> OR <条件2>)`

### 存在性检查

##### `if(TARGET <target-name>)`

判断名为`<target-name>`的目标（target）是否存在。目标（target）可以由[`add_executable()`](https://cmake.org/cmake/help/latest/command/add_executable.html#command:add_executable "add_executable")、[`add_library()`](https://cmake.org/cmake/help/latest/command/add_library.html#command:add_library "add_library")或[`add_custom_target()`](https://cmake.org/cmake/help/latest/command/add_custom_target.html#command:add_custom_target "add_custom_target")创建。

##### `if(DEFINED <名称>|ENV{<名称>}|CACHE{<名称>})`

如果定义了名为`<名称>`的==变量/环境变量/缓存变量==，则为真。

注意：
1. 宏实参不是变量。
2. 无法直接测试`<name>`是否是非缓存变量。对于表达式`if(DEFINED someName)`，在`someName`作为**缓存变量**或**非缓存变量**存在时都返回真。相比之下，表达式`if(DEFINED CACHE{someName})`仅在`someName`作为**缓存变量**存在时才返回真。如果你需要判断一个非缓存变量是否存在，需要同时使用这两个表达式：`if(DEFINED someName AND NOT DEFINED CACHE{someName})`

##### `if(<变量|字符串> IN_LIST <变量>)`

如果给定的元素包含在指定的**列表变量**中，则为真。

### 文件操作

##### `if(EXISTS <path-to-file-or-directory>)`

如果指定的==文件(file)==或==目录(directory)==存在且可读，则为真。

路径必须是**显式的完整路径**时才是良定义的，如`"C:\Program Files"`、`"${CMAKE_CURRENT_SOURCE_DIR}/main.cpp"`。前导`~/`不会被展开为home目录，而是被视为相对路径。

该判断能够解析**符号链接（symbolic link）**。

##### `if(IS_DIRECTORY <path>)`

判断`<path>`是否是目录。路径必须是**显式的完整路径**时才是良定义的。

### 比较
#### 正则匹配
##### `if(<变量|字符串> MATCHES <regex>)`

如果`<字符串>`或`<变量>`匹配正则表达式`<regex>`，则为真。正则表达式格式见[Regex Specification](https://cmake.org/cmake/help/latest/command/string.html#regex-specification)。

`()` groups are captured in [`CMAKE_MATCH_<n>`](https://cmake.org/cmake/help/latest/variable/CMAKE_MATCH_n.html#variable:CMAKE_MATCH_%3Cn%3E "CMAKE_MATCH_<n>") variables.

#### 数字比较

变量/字符串都会被解析为实数（类似于C语言的`double`）（如字符串`"123"`会被解析为实数`123`），然后进行比较。

##### `if(<变量|字符串> LESS <变量|字符串>)`

##### `if(<变量|字符串> GREATER <变量|字符串>)`

##### `if(<变量|字符串> EQUAL <变量|字符串>)`

##### `if(<变量|字符串> LESS_EQUAL <变量|字符串>)`

##### `if(<变量|字符串> GREATER_EQUAL <变量|字符串>)`

#### 字符串比较

按**字典序**进行比较。

##### `if(<变量|字符串> STRLESS <变量|字符串>)`

##### `if(<变量|字符串> STRGREATER <变量|字符串>)`

##### `if(<变量|字符串> STREQUAL <变量|字符串>)`

##### `if(<变量|字符串> STRLESS_EQUAL <变量|字符串>)`

##### `if(<变量|字符串> STRGREATER_EQUAL <变量|字符串>)`

### 版本比较

按版本号的各组成部分**逐段**进行整数比较（版本号格式为：`major[.minor[.patch[.tweak]]]`，其中缺失的部分按 0 处理）。

任何非整数的版本段，或版本段中的非整数尾部，都会使字符串从该点开始截断。

##### `if(<变量|字符串> VERSION_LESS <变量|字符串>)`

##### `if(<变量|字符串> VERSION_GREATER <变量|字符串>)`

##### `if(<变量|字符串> VERSION_EQUAL <变量|字符串>)`

##### `if(<变量|字符串> VERSION_LESS_EQUAL <变量|字符串>)`

##### `if(<变量|字符串> VERSION_GREATER_EQUAL <变量|字符串>)`

### 路径比较

##### `if(<变量|字符串> PATH_EQUAL <变量|字符串>)`

按字典序逐个比较两个 CMake 路径的“路径组件”，不会访问文件系统。仅当两个路径的每个组件都完全相同时，这两个路径才被视为相等。

多个路径分隔符会被视为单个分隔符。不会进行其他的路径归一化处理（[path normalization](https://cmake.org/cmake/help/latest/command/cmake_path.html#normalization)）。 尾部的斜杠会被保留，因此`/a/b`和 `/a/b/`不相等。

逐个路径组件的比较要优于基于字符串的比较，因为它能正确处理多个路径分隔符。例如：

```
# 使用PATH_EQUAL，结果为TRUE
if ("/a//b/c" PATH_EQUAL "/a/b/c")
   ...
endif()
```

```
# 使用STREQUAL，结果为FALSE
if ("/a//b/c" STREQUAL "/a/b/c")
   ...
endif()
```

更多细节见[cmake_path(COMPARE)](https://cmake.org/cmake/help/latest/command/cmake_path.html#path-comparison)。

# message（待完善）

Log a message.

### General messages

```
message([<mode>] "message text" ...)
```

将指定的消息文本记录到日志中。如果提供了**多个消息字符串**，它们会被连接成一条消息，中间不会插入任何分隔符。

可选的`<mode>`关键字用于指定消息的类型，它会影响消息被处理的方式：

- `FATAL_ERROR`：CMake 错误，停止**处理**和**生成**。[`cmake(1)`](https://cmake.org/cmake/help/latest/manual/cmake.1.html#manual:cmake\(1\) "cmake(1)")可执行程序将返回一个非零[退出码](https://cmake.org/cmake/help/latest/manual/cmake.1.html#cmake-exit-code)。
- `STATUS`：项目使用者可能感兴趣的主要提示信息。理想情况下，这些信息应当简洁（最好不超过一行）。