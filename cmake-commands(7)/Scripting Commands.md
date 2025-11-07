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

# Basic Signature

```
find_package(<PackageName> [<version>] [EXACT] [REQUIRED] [COMPONENTS <components>...])
```

`<version>`实参用于指定所查找的包**应该兼容的版本**。它有两种形式：
- **single version**：格式为`major[.minor[.patch[.tweak]]]`，其中每个部分都是数字。
- **range version**：格式为`versionMin...[<]versionMax`，其中`versionMin`和`versionMax`的格式与 single version 相同。默认情况下，range version 的两个端点都包含在内。如果在`...`前加`<`，则上限端点将被排除。

`EXACT`选项表示指定的版本必须**完全匹配**。该选项不能与 range version 一起使用。