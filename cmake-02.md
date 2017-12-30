# cmake 2: 子目录

在前一个例子里，我们把代码文件 hello.c 和 CMakeLists.txt 放在同一个目录下：

```
➜  t1 ls
CMakeLists.txt hello.c
```

在更大一点的项目中，我们通常会把代码放到一个单独的子目录中，甚至代码也会按逻辑功能划分到不同的子目录中。这里，我们把 hello.c 放到一个单独的 `src` 目录中：

```
➜  t1 mkdir src
➜  t1 mv hello.c src
➜  t1 ls
CMakeLists.txt src
```

为了让例子更真实一些，我们把 `hello.c` 中的内容分成两个源文件来写：

src/main.c

```
extern void hello(void);

int main(void)
{
	hello();
	return 0;
}
```

src/hello.c

```c
#include <stdio.h>

void hello(void)
{
	printf("hello, world.\n");
}
```

此时，我们只有顶层的 CMakeLists.txt，其内容改为：

```
project(HELLO)

add_executable(hello src/main.c src/hello.c)
```

此时我们就可以编译了：

```
➜  t2 git:(master) ✗ mkdir build
➜  t2 git:(master) ✗ cd build
➜  build git:(master) ✗ cmake ..
➜  build git:(master) ✗ make
```

## 新增变量

```
add_executable(hello src/main.c src/hello.c)
```

随着源文件的增多，这一句会不断增加。我们可以使用一个变量来表示所有的源文件，或都使用多个变量来表示不同的模块的源文件。


```
project(HELLO)

set(SRC_FILES src/main.c src/hello.c)

add_executable(hello ${SRC_FILES})
```

*TODO：解释 set 指令*
*解释 cmake 中变量，以及如何使用*

## 增加子目录

上面的例子中，我们在顶层的 CMakeLists.txt 中描述 src 目录里的所有，描述了 src 目录中要生成的可执行文件，以及由哪些源文件生成。但如果目录多了，我们不会想在顶层 CMakeLists.txt 中描述所有子目录中的细节，而且一旦子目录中的情况比较复杂，也不适合都写在顶层。

解决一个问题的最基本方法就是「分治法」。我们只需要在顶层中加上一句话：

```
add_subdirectory(src)
```

而至于 src 这个目录想干什么，就由其中自己描述去吧。于是在 src 中我们也添加一个 CMakeLists.txt，其内容为：

```
set(SRC_FILES main.c hello.c)

add_executable(hello ${SRC_FILES})
```

此时再在 build 目录中执行命令：

```
cmake ..
make
```

可以看到编译结果：


```
➜  build git:(master) ✗ ls
CMakeCache.txt      CMakeFiles          Makefile            cmake_install.cmake src
➜  build git:(master) ✗ ls src
CMakeFiles          Makefile            cmake_install.cmake hello
```

发现一个最明显的区别，可执行文件 hello 生成在 `src/` 目录下了。而且生成的 Makefile 也分成了两级。

中间文件 hello.o, main.o 在哪呢？ 在 `src/CMakeFiles/hello.dir` 中。

## 变量

在 顶层和 src/ 下的 CMakeLists.txt 中打印下面几个变量，看看它们的值分别是什么：


```
message(STATUS PROJECT_BINARY_DIR == ${PROJECT_BINARY_DIR})
message(STATUS CMAKE_BINARY_DIR   == ${CMAKE_BINARY_DIR})
message(STATUS HELLO_BINARY_DIR   == ${HELLO_BINARY_DIR})

message(STATUS CMAKE_CURRENT_BINARY_DIR ${CMAKE_CURRENT_BINARY_DIR})
message(STATUS CMAKE_CURRENT_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})
```

在顶层目录下的 CMakeLists.txt 中 CMAKE_CURRENT_BINARY_DIR 的值与 CMAKE_BINARY_DIR 相同，CMAKE_CURRENT_SOURCE_DIR 与 CMAKE_SOURCE_DIR 相同。

而在 src/CMakeLists.txt 中，它们的值就不同了。


```
## 下面几行是 src/CMakeLists.txt 中打印的变量
-- PROJECT_BINARY_DIR/Users/meishaoming/Workspace/test/cmake-practice/t2/build
-- CMAKE_BINARY_DIR/Users/meishaoming/Workspace/test/cmake-practice/t2/build
-- HELLO_BINARY_DIR/Users/meishaoming/Workspace/test/cmake-practice/t2/build
-- CMAKE_CURRENT_BINARY_DIR/Users/meishaoming/Workspace/test/cmake-practice/t2/build/src
-- CMAKE_CURRENT_SOURCE_DIR/Users/meishaoming/Workspace/test/cmake-practice/t2/src

## 下面几行是顶层 CMakeLists.txt 中打印的变量
-- PROJECT_BINARY_DIR/Users/meishaoming/Workspace/test/cmake-practice/t2/build
-- CMAKE_BINARY_DIR/Users/meishaoming/Workspace/test/cmake-practice/t2/build
-- HELLO_BINARY_DIR/Users/meishaoming/Workspace/test/cmake-practice/t2/build
-- CMAKE_CURRENT_BINARY_DIR/Users/meishaoming/Workspace/test/cmake-practice/t2/build
-- CMAKE_CURRENT_SOURCE_DIR/Users/meishaoming/Workspace/test/cmake-practice/t2
```

子细体会这几个变量，大概也能猜到它们的用法。

## 高级用法

上面的例子中。源码子目录名字为 src，则在编译时 build 中生成对应的目录名字也为 src。

```
➜  build git:(master) ✗ ls -F
CMakeCache.txt       CMakeFiles/          Makefile             cmake_install.cmake  src/
```

如果想在 build 里叫其它的名字怎么办？
*TODO 什么情况下需要叫另外的名字？*

```
add_subdirectory(source_dir [binary_dir]
                 [EXCLUDE_FROM_ALL])
```

把顶层 CMakeLists.txt 内容改为：


```

project(HELLO)

add_subdirectory(src src_bin)
```

然后再执行一次编译过程：


```
➜  build git:(master) ✗ ls -F
CMakeCache.txt       CMakeFiles/          Makefile             cmake_install.cmake  src_bin/
➜  build git:(master) ✗ ls -F src_bin
CMakeFiles/          Makefile             cmake_install.cmake  hello*
```

在 build 下面，发现源码子目录 src 生成的中间文件和可执行文件都生成到了 src_bin 目录中。

整个编译过程，最后一条执行 make 就能直接生成了可执行文件。而我们有时候希望某个目标需特殊指定执行。在此例中，我们希望执行 `make hello` 才去编译 src/ 下的目标。

这种情况，只需把顶层 add_subdirectory 这话句改一下就行：

```
add_subdirectory(src EXCLUDE_FROM_ALL)
```

EXCLUDE_FROM_ALL 使得 src 下的所有目标不会加入到默认的 all 目标里。需要明确指定 `make hello` 才会编译。这一点读读生成的 Makefile 就会明白。

实际操作一遍，细细体会下。

