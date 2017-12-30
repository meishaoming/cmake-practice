# cmake 1：最简原型

分几篇来写

1. 编译生成一个应用
2. 安装
3. 编译生成一个静态库和动态库
4. 链接一个静态库和动态库
5. 一个项目生成多个应用
6. 交叉编译

## 生成一个应用

新建一个目录 t1，在其中建立 hello.c

```c
#include <stdio.h>

int main(void)
{
	printf("hello, world.\n");
	return 0;
}
```

新建 CMakeLists.txt

```
project(HELLO)

add_executable(hello hello.c)
```

当前目录下的文件：


```
➜  t1 ls
CMakeLists.txt hello.c
```

执行 `cmake .`

```
➜  t1 cmake .
-- The C compiler identification is AppleClang 9.0.0.9000039
-- The CXX compiler identification is AppleClang 9.0.0.9000039
-- Check for working C compiler: /Library/Developer/CommandLineTools/usr/bin/cc
-- Check for working C compiler: /Library/Developer/CommandLineTools/usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /Library/Developer/CommandLineTools/usr/bin/c++
-- Check for working CXX compiler: /Library/Developer/CommandLineTools/usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/meishaoming/Workspace/test/cmake/t1

```

目录下新增了一些文件：

```
➜  t1 ls
CMakeCache.txt      CMakeLists.txt      cmake_install.cmake
CMakeFiles          Makefile            hello.c
```

新增了三个文件一个目录：

- CMakeCache.txt
- CMakeFiles/
- Makefile
- cmake_install.cmake

Makefile 已经生成，可以直接执行 make 命令，生成可执行文件 hello:

```
➜  t1 make
Scanning dependencies of target hello
[ 50%] Building C object CMakeFiles/hello.dir/hello.c.o
[100%] Linking C executable hello
[100%] Built target hello
➜  t1 ls
CMakeCache.txt      CMakeLists.txt      cmake_install.cmake hello.c
CMakeFiles          Makefile            hello
```

上面这种直接执行 `cmake .` 的编译方式叫作 `in-source build`。cmake 生成 Makefile 时也会生成一些中间文件，这些文件跟代码放在一些显得太乱。

我们可以在一个单独的编译目录里执行 cmake 的过程，这就叫作 `out-source build`。生成的中间文件都在同一个目录中，清理起来也会比较方便。

```
➜  t1 rm -rf Makefile CMakeFiles CMakeCache.txt hello cmake_install.cmake
➜  t1 mkdir build
➜  t1 cd build
➜  build cmake ..
➜  build make
```

这里需要记住两个变量：

* PROJECT_BINARY_DIR - Full path to build directory for project.
* PROJECT_SOURCE_DIR - The path to the top level of the source tree.

一个是编译路径，一个是工程顶层目录。当执行 `in-source build` 时，这两个变量的值一样。而执行 `out-source buld` 时，它们的值不同。

把 CMakeLists.txt 改成下面的样子，执行输出，可以看到前三个变量是一样的，后三个变量的内容也是一样的：

```
project(HELLO)

add_executable(hello hello.c)

message(STATUS ${PROJECT_BINARY_DIR})
message(STATUS ${HELLO_BINARY_DIR})
message(STATUS ${CMAKE_BINARY_DIR})

message(STATUS ${PROJECT_SOURCE_DIR})
message(STATUS ${CMAKE_SOURCE_DIR})
message(STATUS ${HELLO_SOURCE_DIR})
```

执行的显示：

```
➜  build cmake ..
-- /Users/meishaoming/Workspace/test/cmake/t1/build
-- /Users/meishaoming/Workspace/test/cmake/t1/build
-- /Users/meishaoming/Workspace/test/cmake/t1/build
-- /Users/meishaoming/Workspace/test/cmake/t1
-- /Users/meishaoming/Workspace/test/cmake/t1
-- /Users/meishaoming/Workspace/test/cmake/t1
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/meishaoming/Workspace/test/cmake/t1/build
```

从终端输出可以看到：

PROJECT_BINARY_DIR, CMAKE_BINARY_DIR, HELLO_BINARY_DIR 的内容都是编译目录
PROJECT_SOURCE_DIR, CMAKE_SOURCE_DIR, HELLO_SOURCE_DIR 的内容都是工程顶层目录

如果没有定义 `prject(HELLO)`，则变量 HELLO_BINARY_DIR 和 HELLO_SOURCE_DIR 都为空。

## 小结

本篇了解了如何写一个最简单的 CMakeLists.txt 来编译一个应用。还了解了 `in-source build` 和 `out-source build` 的区别。实际编译过程中，我们通常都使用 `out-source build`，这样使得整个文件目录结构看起来更清晰，清理起来也很方便。

最后，我们还学习了两个变量，PROJECT_BINARY_DIR 和 
PROJECT_SOURCE_DIR。与这两个变量还有另外两个相等的变量 CMAKE_BINARY_DIR, <PROJECT_NAME>_BINARY_DIR 和 CMAKE_SOURCE_DIR， <PROJECT_NAME>_SOURCE_DIR。这些变量在以后复杂的系统中大有用处。






