This page shows how to create a new C++ project linking against Urho3D library as external library. There are two approaches to do this. It is imperative to clear the CMake cache when changing from one approach to the other. In fact, you also need to clear the cache when any of the environment variables required by the CMake build script below change its value.


这篇文章说明怎样创建一个新的以外部库方式链接到Urho3D库的C++项目. 有两种方法来做这件事情. 当从一个方法改变到另一个时, 必须要清除CMake缓存. 实际上, 当下面任何CMake构建脚本需要的环境变量改变了值, 你也需要清除缓存.


First of all, structure your project similar to Urho3D project as below:


首先, 你的项目结构类似:


PROJECT_ROOT/

 ┝ Source/
 
 ┝ Build/
 
 ┝ Bin/
 
 ┕ *.bat or *.sh
 
 
 (目录结构不翻译)
 
 
The Source/ directory is where your project CMakeLists.txt and all your source codes should reside. The Bin/ directory is where all the executables output will be generated. In order to run the executable in this directory, you need to copy (or symlink) the CoreData/ and Data/ directories from Urho3D project Bin/ directory (or from Urho3D SDK installation) to here. You may also want to copy (or symlink) the batch files or shell script files from Urho3D project root directory (or from Urho3D SDK installation) to your project root directory. The Build/ directory will be created automatically by the Urho3D CMake module that will be discussed shortly below. The *.bat or *.sh scripts work together with the module to create a so-called out-of-source build tree to keep the Source/ directory clean from intermediate build files. Note that when you invoke one the *.bat or *.sh script, you can pass most of the build options supported by Urho3D project. In fact, you probably have to pass the similar build options that you use when building the Urho3D library to avoid any conflict.


Source/ 目录是放置你的项目CMakeLists.txt和所有源代码的地方. Bin/ 目录是将生成可执行文件的地方. 为了在这个目录运行可执行文件, 你需要从Urho3D项目的Bin/目录(或者Urho3D SDK安装目录) 拷贝(或者符号链接) CoreData/ 和 Data/ 目录到这里. 你可能还想从Urho3D项目根目录(或者Urho3D SDK安装目录) 拷贝(或者符号链接)批处理文件或者shell脚本文件到你的项目根目录. Build/ 目录由下面稍后讨论的Urho3D CMake自动创建. *.bat 或者 *.sh脚本和模块一起工作创建一个所谓的源代码外构建树, 来清理Source/目录的构建中间文件. 注意, 当你调用*.bat 或 *.sh脚本, 你可以传递大多数Urho3D项目支持的构建选项. 实际上, 你可能需要传递与你构建Urho3D库时类似的构建参数以避免冲突.


From Urho3D project root tree
This section assumes that you have already successfully build a static or shared library in the Urho3D project (separately from your own project). In order to find Urho3D library in Urho3D project library output directory, specify an environment variable called "URHO3D_HOME" which points to a Urho3D project root directory that you want to use (if you have more than one).


Urho3D项目根树. 

本节假设你已经成功的构建了Urho3D项目(不是你自己的项目)静态或共享库. 为了在Urho3D项目库的输出目录找到Urho3D库, 指定一个名字是"URHO3D_HOME"的环境变量指向你想要使用的(如果你有超过1个)Urho3D项目根目录.




In your own project Source/ directory, create a new CMakeLists.txt file and add the following lines: (replace MyProjectName and MyExecutableName with the actual names you want)

在你自己的Source/ 目录, 创建一个新的CMakeLists.txt文件 并添加以下几行: (将MyProjectName和MyExecutableName用你实际想要的名字替换)


# Set project name

设置项目名字

project (MyProjectName)

# Set minimum version

设置最低版本

cmake_minimum_required (VERSION 2.8.6)

if (COMMAND cmake_policy)

    cmake_policy (SET CMP0003 NEW)
    
    if (CMAKE_VERSION VERSION_GREATER 2.8.12 OR CMAKE_VERSION VERSION_EQUAL 2.8.12)
    
        cmake_policy (SET CMP0022 NEW) # INTERFACE_LINK_LIBRARIES defines the link interface
        
    endif ()
    
    if (CMAKE_VERSION VERSION_GREATER 3.0.0 OR CMAKE_VERSION VERSION_EQUAL 3.0.0)
    
        cmake_policy (SET CMP0026 OLD) # Disallow use of the LOCATION target property - therefore we set to OLD as we still need it
        
        cmake_policy (SET CMP0042 NEW) # MACOSX_RPATH is enabled by default
        
    endif ()
    
endif ()

# Set CMake modules search path

设置CMake模块搜索路径

set (CMAKE_MODULE_PATH $ENV{URHO3D_HOME}/Source/CMake/Modules CACHE PATH "Path to Urho3D-specific CMake modules")

# Include Urho3D Cmake common module

包含Urho3D Cmake常用模块

include (Urho3D-CMake-common)

# Find Urho3D library

查找Urho3D库

find_package (Urho3D REQUIRED)

include_directories (${URHO3D_INCLUDE_DIRS})

# Define target name

定义目标名字

set (TARGET_NAME MyExecutableName)

# Define source files

定义源代码文件

define_source_files ()

# Setup target with resource copying

安装目标拷贝资源

setup_main_executable ()



The CMAKE_MODULE_PATH is setup so that CMake can find the Urho3D-specific CMake modules provided by Urho3D project inside your own project. The Urho3D-CMake-common.cmake is the module where all the reusable commands and macros are defined. It also gives your project cross-platform build capability similar to Urho3D project.


CMAKE_MODULE_PATH已经设置, 以便你项目的CMake可以找到由Urho3D项目提供的Urho3D专用的CMake模块. Urho3D-CMake-common.cmake是定义所有可重复使用的命令和宏的模块. 它还给你的项目以类似Urho3D项目的交叉平台构建能力. 


When both Urho3D static and shared library are built and available in the Urho3D project's library output directory, the FindUrho3D.cmake module has precedence to first select static library type over over shared library type. However, you can use URHO3D_LIB_TYPE build option to explicitly specify which Urho3D library type to be selected. When linking statically against Urho3D static library, Urho3D-CMake-common.cmake module automatically set "URHO3D_STATIC_DEFINE" compiler define for your project. This compiler define is crucial for static linking, especially on MSVC.


当在Urho3D项目的库输出目录Urho3D静态库和共享库都构建并可用, FindUrho3D.cmake模块第一优先选择静态库而非共享库. 然而, 你可以使用URHO3D_LIB_TYPE构建选项明确指定选择哪种Urho3D库,  Urho3D-CMake-common.cmake模块为你的项目自动设置"URHO3D_STATIC_DEFINE"编译常量. 这个编译常量对于静态链接是很重要的, 尤其是在MSVC上.


The define_source_files() and setup_main_executable() are Urho3D-specific macros. The define_source_file() macro is a shorthand to glob all the source files in the current source directory. It is equivalent to these commands:


define_source_files() 和 setup_main_executable() 是Urho3D特有的宏. define_source_file()是一个表示当前源文件目录的所有的源文件的简写. 它等效于这些命令: 


file (GLOB CPP_FILES *.cpp)

file (GLOB H_FILES *.h)

set (SOURCE_FILES ${CPP_FILES} ${H_FILES})


The setup_main_executable() macro then uses SOURCE_FILES and TARGET_NAME variables to setup a new CMake target that would work on ALL platforms supported by Urho3D. Make sure both your project name and target name are not called 'Urho3D', as this name is already reserved by Urho3D project.


setup_main_executable()宏然后使用SOURCE_FILES和TARGET_NAME变量设置一个将工作在所有Urho3D支持的平台的新的CMake目标. 确认你的项目名和目标名都不叫做 'Urho3D', 因为这个名字已经被Urho3D项目保留.


From Urho3D SDK installation

This section assumes that you have already installed Urho3D SDK into your system. If you have installed the SDK in a non-default location then you need to use "CMAKE_PREFIX_PATH" environment variable to specify the prefix path of the non-default installation location.


从安装Urho3D SDK

本节假设你已经在你的系统安装了Urho3D SDK. 如果你已经不是在默认目录安装了SDK, 那么你需要使用"CMAKE_PREFIX_PATH"环境变量来指出这个非默认安装路径的路径前缀.



In your own project Source/ directory, create a new CMakeLists.txt file similar to the first approach but change the CMake module search path setup to read as follows on Windows platform:


在你自己的Source/ 目录, 用类似方法创建一个新的CMakeLists.txt文件, 但是但是在Windows平台修改CMake模块搜索路径, 按如下设置:


# Set CMake modules search path

set (CMAKE_MODULE_PATH $ENV{CMAKE_PREFIX_PATH}/share/CMake/Modules CACHE PATH "Path to Urho3D-specific CMake modules")


On non-Windows platform:

在非Windows平台:

# Set CMake modules search path

set (CMAKE_MODULE_PATH $ENV{CMAKE_PREFIX_PATH}/share/Urho3D/CMake/Modules CACHE PATH "Path to Urho3D-specific CMake modules")


Using pkg-config instead of CMake

使用pkg-config代替CMake


If for some reason you could not use CMake in your project, you could configure your project to compile and link against Urho3D library from SDK installation using 'pkg-config' tool with the help of 'Urho3D.pc' configuration file (which is installed as part of the SDK installation). If the Urho3D SDK is being installed into a local location (such as /usr/local) or a non-default location, then most likely you would need to specify PKG_CONFIG_PATH environment variable to point to the location of the configuration file for this to work. The 'Urho3D.pc' file contains important information on how to configure your compiler and linker correctly. You can use that information as a reference to configure your own project even when you do not use 'pkg-config' tool.

Below are a few invocation examples on a 64-bit RedHat-based distro with local installation:


如果因为某些原因你不能在你的项目中使用CMake, 你可以  配置你的项目 编译和链接到从SDK 安装的Urho3D库, 使用带'Urho3D.pc' 配置文件 (作为SDK的一部分安装)的帮助的'pkg-config' 工具. 如果Urho3D SDK被安装在本地(例如/usr/local)或非默认的位置, 那么你很可能需要为此指定PKG_CONFIG_PATH环境变量来指出这个配置文件的位置. 'Urho3D.pc'文件包含关于怎样正确地配置你的编译器和链接器的重要信息. 即使你不使用'pkg-config'工具, 你可以使用这些信息作为配置你自己的项目的参考. 

下面是一些安装在本地的基于64位 RedHat 发行版的调用例子:


# To get installed Urho3D version

获取安装的Urho3D版本

PKG_CONFIG_PATH=/usr/local/lib64/pkgconfig pkg-config --modversion Urho3D

# To compile and link natively in one liner

一步完成编译和链接本机应用

c++ -o Urho3DPlayer Urho3DPlayer.cpp `PKG_CONFIG_PATH=/usr/local/lib64/pkgconfig pkg-config --cflags --libs Urho3D`

# To compile and link natively but in separate steps

分步完成编译和链接本机应用

export PKG_CONFIG_PATH=/usr/local/lib64/pkgconfig

c++ -c Urho3DPlayer.cpp `pkg-config --cflags Urho3D`

c++ -o Urho3DPlayer Urho3DPlayer.o `pkg-config --libs Urho3D`

# To cross-compile and link for Raspberry Pi platform

交叉编译和链接生成Raspberry Pi平台应用

export CC=${RASPI_TOOL}/arm-linux-gnueabihf-c++

export PKG_CONFIG_SYSROOT_DIR=${RASPI_ROOT}

export PKG_CONFIG_PATH=${RASPI_ROOT}/usr/local/lib/pkgconfig

$CC -o Urho3DPlayer Urho3DPlayer.cpp `pkg-config --cflags --libs Urho3D`

# To cross-compile and link for Windows platform using MinGW cross-compiler (in Debug configuration)

使用MinGW交叉编译器交叉编译和链接生成Windows平台应用(Debug配置)

export CC=${MINGW_PREFIX}-c++

export PKG_CONFIG_SYSROOT_DIR=${MINGW_ROOT}

export PKG_CONFIG_PATH=${MINGW_ROOT}/usr/local/lib/pkgconfig

$CC -o Urho3DPlayer_d.exe Urho3DPlayer.cpp `pkg-config --variable CFLAGS_DEBUG Urho3D` `pkg-config --cflags --libs Urho3D`


Project scaffolding

With Rake ruby gem installed, you can quickly setup all the above by simply using the 'scaffolding' rake task.

Usage: 


项目手脚架

安装了Rake ruby gem, 通过简单的使用rake 任务'scaffolding', 你可以快速安装上面所有的内容.

用法:



rake scaffolding dir=/path/to/new/project/root [project=Scaffolding] [target=Main]


As its name implies, this task just creates a basic project structure for your new project. You may also pass optional "project" and "target" parameters besides the "dir" parameter. Otherwise, you will get the default project name (Scaffolding) and target name (Main) in the main CMakeLists.txt. This task copies the Urho3DPlayer.cpp and Urho3DPlayer.h as placeholders for the source files. Normally, you should replace these two files with your own project source files before invoking one of the CMake *.bat or *.sh script.


就像它的名字所暗示, 这个任务仅为你的新项目创建一个基本的项目结构. 除了"dir"参数, 你也可能传递选项"project"和"target"参数. 否则, 你会从主CMakeLists.txt得到默认的项目名字(Scaffolding)和目标名字(Main). 这个任务拷贝Urho3DPlayer.cpp和Urho3DPlayer.h作为源代码文件的占位符. 通常, 在调用一个CMake,  *.bat 或者 *.sh 脚本之前, 你会用你自己的项目源代码文件替换掉这两个文件.


On Windows host system, this task requires privilege to create symlinks by using mklink command. The task would fail when the Windows user account does not have that privilege.

在Windows主机系统, 这个任务需要拥有使用mklink命令创建符号链接的权限. 当Windows用户帐号不具有这个权限, 这个命令将会失败.



