https://cmake.org/cmake/help/latest/manual/cmake-language.7.html

# 句法

### 变量引用

##### 变量引用（variable reference）

格式为==`${<变量>}`==，可在[Quoted Argument](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#quoted-argument)或[Unquoted Argument](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#unquoted-argument)中进行求值。变量引用会被替换为指定**变量**或**缓存项**的值。变量引用可以嵌套，将从内向外进行求值，例如`${outer_${inner_variable}_variable}`。

字面量变量引用可以包含：字母和数字字符、字符`/_.+-`、[转义序列](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#escape-sequences)。嵌套引用可对任意名称的变量求值。

变量名的作用域以及如何设置它们的值，见[Variables](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#variables)。

##### 环境变量引用（environment variable reference）

格式为==`$ENV{<变量>}`==。更多信息见[Environment Variables](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#environment-variables)。

##### 缓存变量引用（cache variable reference）

格式为==`$CACHE{<变量>}`==。它会被替换为指定**缓存项**的值，而不会去检查是否存在同名的**普通变量**。更多信息见[`CACHE`](https://cmake.org/cmake/help/latest/variable/CACHE.html#variable:CACHE "CACHE")。

---

`if()`命令具有特殊的条件句法——**变量引用**允许使用短形式`<variable>`（而非`${<variable>}`），但是**环境变量**始终需要以`$ENV{<variable>}`的形式引用。

# 变量

变量（variable）是 CMake 语言中最基本的存储单元。==它们的值总是**字符串类型**，尽管某些命令可能会将字符串解释为其他类型的值。==[`set()`](https://cmake.org/cmake/help/latest/command/set.html#command:set "set")和[`unset()`](https://cmake.org/cmake/help/latest/command/unset.html#command:unset "unset")命令显式地 set 或 unset 变量，但其他命令也可能会以某种方式修改变量。**变量名区分大小写**，可以由几乎任何文本组成，但建议仅使用==字母、数字`_`和`-`==。

变量具有动态作用域。每个 “set” 或 “unset” 都会在当前作用域中创建绑定：

- **块作用域**（Block Scope）：[`block()`](https://cmake.org/cmake/help/latest/command/block.html#command:block "block")命令可以为变量绑定创建新的作用域。
- **函数作用域**（Function Scope）：由[`function()`](https://cmake.org/cmake/help/latest/command/function.html#command:function "function")命令创建的[命令定义](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#command-definitions)，会在调用时在新的变量绑定作用域中处理其中命令。在该作用域内 “set” 或 “unset” 的变量在当前函数及其内部嵌套调用中可见。
- **目录作用域**（Directory Scope）：源码树中的每个[目录](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#directories)都有自己的**变量绑定**。在处理某个目录的`CMakeLists.txt`文件之前，CMake 会将父目录中当前定义的所有**变量绑定**（if any）复制到新的目录作用域中。不在函数调用内部的 “set” 或 “unset” 变量会绑定到当前目录作用域。
- **持久缓存**（Persistent Cache）：CMake 会维护一套独立的“缓存”变量或“缓存项”，其值在项目构建树的多次运行中保持不变。缓存项有孤立的绑定作用域，仅可通过显式请求修改，例如通过`set()`或`unset()`命令的`CACHE`选项。

在求值**变量引用**时，CMake 会首先在**函数调用栈**中查找绑定（if any），然后回退到当前**目录作用域**中查找绑定（if any）。如果找到一个“set”绑定，则使用其值；如果找到一个“unset”绑定或未找到绑定，则会搜索**缓存项**。如果找到缓存项，则使用其值；否则变量引用将求值为空字符串。可以使用`$CACHE{VAR}`语法直接查找缓存项。

The [`cmake-variables(7)`](https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html#manual:cmake-variables\(7\) "cmake-variables(7)") manual documents the many variables that are provided by CMake or have meaning to CMake when set by project code.

> **注意**：CMake 保留以下标识符：
> ① 以`CMAKE_`开头（大小写不限）
> ② 以`_CMAKE_`开头（大小写不限）
> ③ 以`_`开头且后跟任一[`CMake Command`](https://cmake.org/cmake/help/latest/manual/cmake-commands.7.html#manual:cmake-commands\(7\) "cmake-commands(7)")

# 环境变量

环境变量（Environment Variable）与普通的 CMake 变量类似，但存在以下区别：

- 作用域：环境变量在一个 CMake 进程中具有**全局作用域**。它们不会被缓存。
- 引用：变量引用的形式为`ENV{变量}`。
- 初始化：CMake 环境变量的初始值来自**调用 CMake 的进程的环境**。可以使用`set()`和`unset()`修改环境变量的值，只会影响**当前正在运行的 CMake 进程**，不会影响系统环境变量。修改的值不会写回到调用进程中，后续的构建或测试进程不会看到这些修改的值。
- 查看：使用[`cmake -E environment`](https://cmake.org/cmake/help/latest/manual/cmake.1.html#cmdoption-cmake-E-arg-environment)显示当前所有环境变量。

The [`cmake-env-variables(7)`](https://cmake.org/cmake/help/latest/manual/cmake-env-variables.7.html#manual:cmake-env-variables\(7\) "cmake-env-variables(7)") manual documents environment variables that have special meaning to CMake.

# 列表

尽管 CMake 中所有的值都存储为**字符串**，但在某些上下文中，字符串会被视为**列表**（例如在求值[Unquoted Argument](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#unquoted-argument)时）。此时，一个字符串会==以`;`为分隔符==被分割为多个列表元素。但当`;`位于未闭合的`[]`中的时（如==`[a;b;c]`==），不会被视为分隔符；转义序列==`\;`==中的`;`也不会被视为分隔符。

列表的本质是==以`;`分隔各元素的字符串==。例如，`set()`命令将多个值以**列表**的形式存储到目标变量：

```
set(srcs a.c b.c c.c)  # 将变量 srcs 设置为 "a.c;b.c;c.c"
```

列表适用于简单的场景（如“源文件列表”），不应该用于复杂的数据处理任务。大多数构造列表的命令不会对列表元素中的`;`字符进行转义，因此嵌套列表会被扁平化：

```
set(x a "b;c")  # 将 x 设置为 "a;b;c"，而非 "a;b\;c"
```
