# CMake教程

## 一 安装CMake

```
sudo apt install cmake
```

## 二 编译执行main.c

terminal

```
vim main.c
```

main.c

```
#include <stdio.h>

int main(void)
{
	printf("Hello World\n");

	return 0;
}
```



CMakelist.txt

```
cmake_minimum_required (VERSION 2.8)

project (demo)

add_executable(main main.c)

```

## 三 同一目录下多个源文件

terminal

```
.
├── CMakeList.txt
├── main.c
├── testFunc.c
└── testFunc.h

```

testFunc.c

```
/*
** testFunc.c
*/

#include <stdio.h>
#include "testFunc.h"

void func(int data)
{
	printf("data is %d\n", data);
}

```

testFunc.h

```
/*
** testFunc.h
*/

#ifndef _TEST_FUNC_H_
#define _TEST_FUNC_H_

void func(int data);

#endif

```

main.c

```
#include <stdio.h>

#include "testFunc.h"

int main(void)
{
	func(100);

	return 0;
}

```

CMakeLists.txt

```
cmake_minimum_required (VERSION 2.8)

project (demo)

add_executable(main main.c testFunc.c)

```



添加变量SRC_LIST

terminal

```
.
├── CMakeList.txt
├── main.c
├── testFunc1.c
├── testFunc1.h
├── testFunc.c
└── testFunc.h

```

testFunc.c

```
/*
** testFunc.c
*/

#include <stdio.h>
#include "testFunc.h"

void func(int data)
{
	printf("data is %d\n", data);
}

```

testFunc.h

```
/*
** testFunc.h
*/

#ifndef _TEST_FUNC_H_
#define _TEST_FUNC_H_

void func(int data);

#endif

```

testFunc1.c

```
/*
** testFunc1.c
*/

#include <stdio.h>
#include "testFunc1.h"

void func1(int data)
{
	printf("data is %d\n", data);
}

```

testFunc1.h

```
/*
** testFunc1.h
*/

#ifndef _TEST_FUNC1_H_
#define _TEST_FUNC1_H_

void func1(int data);

#endif

```



main.c

```
#include <stdio.h>

#include "testFunc.h"
#include "testFunc1.h"

int main(void)
{
	func(100);
	func1(200);

	return 0;
}


```

CMakeLists.txt

```cmake
cmake_minimum_required (VERSION 2.8)

project (demo)

#a ux_source_directory(dir var)
#第一个参数dir是指定目录，第二个参数var是用于存放源文件列表的变量
#使用aux_source_directory把当前目录下的源文件存列表存放到变量SRC_LIST里，然后在add_executable里调用SRC_LIST（注意调用变量时的写法）

aux_source_directory(. SRC_LIST)

add_executable(main ${SRC_LIST})


```

```cmake
#a ux_source_directory()也存在弊端，它会把指定目录下的所有源文件都加进来，可能会加入一些我们不需要的文件，此时我们可以使用set命令去新建变量来存放需要的源文件

cmake_minimum_required (VERSION 2.8)

project (demo)

set( SRC_LIST
	 ./main.c
	 ./testFunc1.c
	 ./testFunc.c)

add_executable(main ${SRC_LIST})

```

## 四 不同目录下多个源文件

terminal

```
.
├── CMakeList.txt
├── main.c
├── test_func
│   ├── testFunc.c
│   └── testFunc.h
└── test_func1
    ├── testFunc1.c
    └── testFunc1.h

```

testFunc.c

```
/*
** testFunc.c
*/

#include <stdio.h>
#include "testFunc.h"

void func(int data)
{
	printf("data is %d\n", data);
}

```

testFunc.h

```
/*
** testFunc.h
*/

#ifndef _TEST_FUNC_H_
#define _TEST_FUNC_H_

void func(int data);

#endif

```

testFunc1.c

```
/*
** testFunc1.c
*/

#include <stdio.h>
#include "testFunc1.h"

void func1(int data)
{
	printf("data is %d\n", data);
}

```

testFunc1.h

```
/*
** testFunc1.h
*/

#ifndef _TEST_FUNC1_H_
#define _TEST_FUNC1_H_

void func1(int data);

#endif

```



main.c

```c
#include <stdio.h>

#include "testFunc.h"
#include "testFunc1.h"
/*
	#include "test_func/testFunc.h"
	#include "test_func1/testFunc1.h"
	CMakefile.txt未指定头文件，可以用指定头文件目录的方式解决
*/

int main(void)
{
	func(100);
	func1(200);

	return 0;
}


```

CMakeLists.txt

```cmake
cmake_minimum_required (VERSION 2.8)

project (demo)
#include_directories，该命令是用来向工程添加多个指定头文件的搜索路径，路径之间用空格分隔。
include_directories (test_func test_func1)

#使用了2次aux_source_directory，因为源文件分布在2个目录下，所以添加2次
aux_source_directory (test_func SRC_LIST)
aux_source_directory (test_func1 SRC_LIST1)

add_executable (main main.c ${SRC_LIST} ${SRC_LIST1})

```

## 五 正规一点的组织结构

正规一点来说，一般会把源文件放到src目录下，把头文件放入到include文件下，生成的对象文件放入到build目录下，最终输出的elf文件会放到bin目录下，这样整个结构更加清晰。让我们把前面的文件再次重新组织下

```
.
├── bin
├── build
├── CMakeLists.txt
├── include
│   ├── testFunc1.h
│   └── testFunc.h
└── src
    ├── CMakeLists.txt
    ├── main.c
    ├── testFunc1.c
    └── testFunc.c

```

在**最外层目录下新建一个CMakeLists.txt**，内容如下：

```
cmake_minimum_required (VERSION 2.8)

project (demo)

add_subdirectory (src)

```

这里出现一个新的命令add_subdirectory()，这个命令可以向当前工程添加存放源文件的子目录，并可以指定中间二进制和目标二进制的存放位置，具体用法可以百度。
这里指定src目录下存放了源文件，当执行cmake时，就会进入src目录下去找**src目录下的CMakeLists.txt**，所以在src目录下也建立一个CMakeLists.txt，内容如下：

```
aux_source_directory (. SRC_LIST)

include_directories (../include)

add_executable (main ${SRC_LIST})

set (EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

```

这里又出现一个新的命令set，是用于定义变量的，EXECUTABLE_OUT_PATH和PROJECT_SOURCE_DIR是CMake自带的预定义变量，其意义如下，

- EXECUTABLE_OUTPUT_PATH ：目标二进制可执行文件的存放位置
- PROJECT_SOURCE_DIR：工程的根目录

所以，这里set的意思是把存放elf文件的位置设置为工程根目录下的bin目录。（cmake有很多预定义变量，详细的可以网上搜索一下）



下面来运行cmake，**不过这次先让我们切到build目录下**，然后输入以下命令，

```bash
cmake ..
#这里解释一下为什么在build目录下运行cmake？从前面几个case中可以看到，如果不这样做，cmake运行时生成的附带文件就会跟源码文件混在一起，这样会对程序的目录结构造成污染，而在build目录下运行cmake，生成的附带文件就只会待在build目录下，如果我们不想要这些文件了就可以直接清空build目录，非常方便。
```

Makefile会在build目录下生成，然后在build目录下运行make



**另外一种写法：**
前面的工程使用了2个CMakeLists.txt，最外层的CMakeLists.txt用于掌控全局，使用add_subdirectory来控制其它目录下的CMakeLists.txt的运行。

上面的例子也可以只使用一个CMakeLists.txt，把最外层的CMakeLists.txt内容改成如下:

```cmake
#需要把src目录下的CMakeLists.txt删除
cmake_minimum_required (VERSION 2.8)

project (demo)

set (EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

aux_source_directory (src SRC_LIST)

include_directories (include)

add_executable (main ${SRC_LIST})

```

## 六 动态库和静态库的编译控制

terminal

```
.
├── bin
├── build
├── CMakeLists.txt
└── lib
    ├── testFunc.c
    └── testFunc.h

```

CMakeLists.txt

```cmake
cmake_minimum_required (VERSION 3.5)

project (demo)

set (SRC_LIST ${PROJECT_SOURCE_DIR}/testFunc/testFunc.c)

#生成动态库或静态库(第1个参数指定库的名字；第2个参数决定是动态还是静态，如果没有就默认静态；第3个参数指定生成库的源文件)
add_library (testFunc_shared SHARED ${SRC_LIST})
add_library (testFunc_static STATIC ${SRC_LIST})

#设置最终生成的库的名称，还有其它功能，如设置库的版本号等等
set_target_properties (testFunc_shared PROPERTIES OUTPUT_NAME "testFunc")
set_target_properties (testFunc_static PROPERTIES OUTPUT_NAME "testFunc")

#库文件的默认输出路径，这里设置为工程目录下的lib目录
set (LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

```

这里又出现了新的命令和预定义变量，

- add_library: 生成动态库或静态库(第1个参数指定库的名字；第2个参数决定是动态还是静态，如果没有就默认静态；第3个参数指定生成库的源文件)
- set_target_properties: 设置最终生成的库的名称，还有其它功能，如设置库的版本号等等
- LIBRARY_OUTPUT_PATH: 库文件的默认输出路径，这里设置为工程目录下的lib目录

```
PS：前面使用set_target_properties重新定义了库的输出名称，如果不使用set_target_properties也可以，那么库的名称就是add_library里定义的名称，只是连续2次使用add_library指定库名称时（第一个参数），这个名称不能相同，而set_target_properties可以把名称设置为相同，只是最终生成的库文件后缀不同（一个是.so，一个是.a），这样相对来说会好看点。
```

## 七 对库进行链接

把上节生成的库拷贝过来，然后在在工程目录下新建src目录和bin目录，在src目录下添加一个main.c，整体结构如下：

```
.
├── bin
├── build
├── CMakeLists.txt
├── src
│   └── main.c
└── testFunc
    ├── inc
    │   └── testFunc.h
    └── lib
        ├── libtestFunc.a
        └── libtestFunc.so

```

main.c

```
#include <stdio.h>

#include "testFunc.h"

int main(void)
{
    func(100);
    
    return 0;
}
```

CMakeLists.txt

```cmake
cmake_minimum_required (VERSION 3.5)

project (demo)


set (EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

set (SRC_LIST ${PROJECT_SOURCE_DIR}/src/main.c)

# find testFunc.h
include_directories (${PROJECT_SOURCE_DIR}/testFunc/inc)

find_library(TESTFUNC_LIB testFunc HINTS ${PROJECT_SOURCE_DIR}/testFunc/lib)

add_executable (main ${SRC_LIST})

target_link_libraries (main ${TESTFUNC_LIB})

```

这里出现2个新的命令，

- find_library: 在指定目录下查找指定库，并把库的绝对路径存放到变量里，其第一个参数是变量名称，第二个参数是库名称，第三个参数是HINTS，第4个参数是路径，其它用法可以参考cmake文档
- target_link_libraries: 把目标文件与库文件进行链接

使用find_library的好处是在执行cmake ..时就会去查找库是否存在，这样可以提前发现错误，不用等到链接时。

```
ps：在lib目录下有testFunc的静态库和动态库，find_library(TESTFUNC_LIB testFunc ...默认是查找动态库，如果想直接指定使用动态库还是静态库，可以写成find_library(TESTFUNC_LIB libtestFunc.so ...或者find_library(TESTFUNC_LIB libtestFunc.a ...

ps： 查看elf文件使用了哪些库，可以使用readelf -d ./xx来查看

```

## 八 添加编译选项

有时编译程序时想添加一些编译选项，如-Wall，-std=c++11等，就可以使用add_compile_options来进行操作。
这里以一个简单程序来做演示

terminal

```
.
├── bin
├── build
├── CMakeLists.txt
└── main.c

```

main.cpp

```
#include <iostream>

int main(void)
{
    auto data = 100;
    std::cout << "data: " << data << "\n";
    return 0;
}

```

CMakeLists.txt

```cmake
cmake_minimum_required (VERSION 2.8)

project (demo)

set (EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

#添加编译选项
add_compile_options(-std=c++11 -Wall) 

add_executable(main main.cpp)
```

然后cd到build目录下，执行`cmake .. && make`命令，就可以在bin目录下得到main的elf文件



## 九 添加控制选项

有时希望在编译代码时只编译一些指定的源码，可以使用cmake的option命令，主要遇到的情况分为2种：

本来要生成多个bin或库文件，现在只想生成部分指定的bin或库文件
对于同一个bin文件，只想编译其中部分代码（使用宏来控制）
第1种情况
假设我们现在的工程会生成2个bin文件，main1和main2，现在整体结构体如下，

```
.
├── bin
├── build
├── CMakeLists.txt
└── src
    ├── CMakeLists.txt
    ├── main1.c
    └── main2.c

```

外层的CMakeLists.txt内容如下：

```
cmake_minimum_required(VERSION 3.5)

project(demo)

option(MYDEBUG "enable debug compilation" OFF)

set (EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

add_subdirectory(src)

```

这里使用了option命令，其第一个参数是这个option的名字，第二个参数是字符串，用来描述这个option是来干嘛的，第三个是option的值，ON或OFF，也可以不写，不写就是默认OFF。

然后编写src目录下的CMakeLists.txt，如下

```
cmake_minimum_required (VERSION 3.5)

add_executable(main1 main1.c)

if (MYDEBUG)
    add_executable(main2 main2.c)
else()
    message(STATUS "Currently is not in debug mode")    
endif()

```

注意，这里使用了if-else来根据option来决定是否编译main2.c
其中main1.c和main2.c的内容如下，

```
// main1.c
#include <stdio.h>

int main(void)
{
    printf("hello, this main1\n");
    
    return 0;
}

```

```
// main2.c
#include <stdio.h>

int main(void)
{
    printf("hello, this main2\n");
    
    return 0;
}

```

然后cd到build目录下输入`cmake .. && make`就可以只编译出main1，如果想编译出main2，就把MYDEBUG设置为ON，再次输入`cmake .. && make`重新编译。



每次想改变MYDEBUG时都需要去修改CMakeLists.txt，有点麻烦，其实可以通过cmake的命令行去操作，例如我们想把MYDEBUG设置为OFF，先cd到build目录，然后输入cmake .. -DMYDEBUG=ON，这样就可以编译出main1和main2 （在bin目录下）





#### 第2种情况

假设我们有个main.c，其内容如下：

```
#include <stdio.h>

int main(void)
{
#ifdef WWW1
    printf("hello world1\n");
#endif    

#ifdef WWW2     
    printf("hello world2\n");
#endif

    return 0;
}
```

可以通过定义宏来控制打印的信息，我们CMakeLists.txt内容如下：

```
cmake_minimum_required(VERSION 3.5)

project(demo)

set (EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

option(WWW1 "print one message" OFF)
option(WWW2 "print another message" OFF)

if (WWW1)
    add_definitions(-DWWW1)
endif()

if (WWW2)
    add_definitions(-DWWW2)
endif()

add_executable(main main.c)

```

这里把option的名字保持和main.c里的宏名称一致，这样更加直观，也可以选择不同的名字。通过与add_definitions()的配合，就可以控制单个bin文件的打印输出了。

整体工程结构如下，

```
.
├── bin
├── build
├── CMakeLists.txt
└── main.c

```

cd到build目录下执行cmake .. && make，然后到bin目录下执行./main，可以看到打印为空，
接着分别按照下面指令去执行，然后查看打印效果，

- cmake .. -DWWW1=ON -DWWW2=OFF && make
- cmake .. -DWWW1=OFF -DWWW2=ON && make
- cmake .. -DWWW1=ON -DWWW2=ON && make

这里有个小坑要注意下：假设有2个options叫A和B，先调用cmake设置了A，下次再调用cmake去设置B，如果没有删除上次执行cmake时产生的缓存文件，那么这次虽然没设置A，也会默认使用A上次的option值。

所以如果option有变化，要么删除上次执行cmake时产生的缓存文件，要么把所有的option都显式的指定其值。

