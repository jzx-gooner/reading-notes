1. cmake_minimum_required(VERSION 2.8)           检查cmake的版本，至少为2.8

2. project(helloworld)                                            工程名为helloworld

3. aux_source_directory(.  SRC_LIST)                     查找当前目录下所有的源文件并保存到SRC_LIST变量中

4. include_directories(./include ./abc)                      将./include和./abc加入包含目录列表

5. link_directories(./lib)                                         将 ./lib加入编译器链接阶段的搜索目录列表

6. add_executable(hello  $(SRC_LIST})                  使用SRC_LIST源文件列表里的文件生成一个可执行文件hello

7. add_library(hello STATIC ${SRC_LIST})            使用SRC_LIST源文件列表里的文件生成一个静态链接库libhello.a

8. add_library(hello SHARD ${SRC_LIST})            使用SRC_LIST源文件列表里的文件生成一个动态链接库libhello.so

9. target_link_libraries(hello a b.a c.so)                  将若干库文件链接到目标hello中，target_link_libraries里的库文件的顺序符合gcc/g++链接顺序的规则，即被依赖的库放在依赖它的库的后面，如果顺序有错，链接时会报错。

10. 自定义链接选项

11. 自定义Makefile目标

 

 target_link_libraries(<target> ... <item> ... ...)  其中<target> 必须在当前目录中被add_executable()命令或add_library()命令创建出来。

小型程序的CMakeLists.txt模板：

cmake_minimum_required(VERSION 2.8)   //cmake的最低版本

project(example)    //项目名

include_directories("/usr/include")  //程序所需头文件的位置

link_directories("/usr/lib")   //程序所需库文件的位置

add_executable(example example.cpp)   //编译example.cpp生成example对象文件

target_link_libraries(example gsl)  //链接libgsl.a链接库文件到example对象文件中

target_link_libraries(example gslcblas) //链接libgslcblas.a链接库文件到example对象文件中

 

12. FIND_PATH(<VAR> name1 [path1 path2 ...]) 该命令在参数path1, path2...指示的目录中查找文件name1并将查找到的路径保存到变量VAR中

13. FIND_LIBRARY  同FIND_PATH类似，用于查找链接库并将结果保存在变量中

Example：在开发软件的时候我们会用到一些函数库，这些函数库在不同的系统中安装的位置可能不同，编译的时候需要首先找到这些头文件以及链接库所在的目录以便生成编译选项。

第一步，在项目的根目录中创建目录cmake/modules/, cmake/modules/下创建文件Findlibxx.cmake,内容如下：

MESSAGE(STATUS "Using budled findlibxx.cmake...")

FIND_PATH(

    LIB_XX_INCLUDE_DIR

    XX.h

    /usr/include/

   /use/local/include/

)

//在/usr/lib/ 和/usr/local/lib/目录下，寻找名为xx的链接库，并将结果保存在 LIB_XX_LIBRARIES中

FIND_LIBRARY(

    LIB_XX_LIBRARIES NAMES XX

    PATHS /usr/lib/  /usr/local/lib/

)

第二步，项目的根目录中的CmakeList.txt

project(main)

cmake_minimum_required(version 2.8)

set(CMAKE_SOURCE_DIR .)

set(CMAKE_MODULE_PATH ${CMAKE_ROOT}/Modules ${CMAKE_ROOT}/cmake/modules}

aux_source_directory(. DIR_SRCS)

add_executable(main ${DIR_SRCS})

find_package(XX required)

mark_as_advanced(

 LIB_XX_INCLUDE_DIR

LIB_XX_LIBRARIES

)
