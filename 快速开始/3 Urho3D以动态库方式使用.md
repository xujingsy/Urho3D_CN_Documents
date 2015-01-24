This page shows how to create a new C++ project linking against Urho3D library as external library. There are two approaches to do this. It is imperative to clear the CMake cache when changing from one approach to the other. In fact, you also need to clear the cache when any of the environment variables required by the CMake build script below change its value.

First of all, structure your project similar to Urho3D project as below:

PROJECT_ROOT/
 ┝ Source/
 ┝ Build/
 ┝ Bin/
 ┕ *.bat or *.sh
The Source/ directory is where your project CMakeLists.txt and all your source codes should reside. The Bin/ directory is where all the executables output will be generated. In order to run the executable in this directory, you need to copy (or symlink) the CoreData/ and Data/ directories from Urho3D project Bin/ directory (or from Urho3D SDK installation) to here. You may also want to copy (or symlink) the batch files or shell script files from Urho3D project root directory (or from Urho3D SDK installation) to your project root directory. The Build/ directory will be created automatically by the Urho3D CMake module that will be discussed shortly below. The *.bat or *.sh scripts work together with the module to create a so-called out-of-source build tree to keep the Source/ directory clean from intermediate build files. Note that when you invoke one the *.bat or *.sh script, you can pass most of the build options supported by Urho3D project. In fact, you probably have to pass the similar build options that you use when building the Urho3D library to avoid any conflict.

From Urho3D project root tree
This section assumes that you have already successfully build a static or shared library in the Urho3D project (separately from your own project). In order to find Urho3D library in Urho3D project library output directory, specify an environment variable called "URHO3D_HOME" which points to a Urho3D project root directory that you want to use (if you have more than one).

In your own project Source/ directory, create a new CMakeLists.txt file and add the following lines: (replace MyProjectName and MyExecutableName with the actual names you want)

# Set project name
project (MyProjectName)
# Set minimum version
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
set (CMAKE_MODULE_PATH $ENV{URHO3D_HOME}/Source/CMake/Modules CACHE PATH "Path to Urho3D-specific CMake modules")
# Include Urho3D Cmake common module
include (Urho3D-CMake-common)
# Find Urho3D library
find_package (Urho3D REQUIRED)
include_directories (${URHO3D_INCLUDE_DIRS})
# Define target name
set (TARGET_NAME MyExecutableName)
# Define source files
define_source_files ()
# Setup target with resource copying
setup_main_executable ()
The CMAKE_MODULE_PATH is setup so that CMake can find the Urho3D-specific CMake modules provided by Urho3D project inside your own project. The Urho3D-CMake-common.cmake is the module where all the reusable commands and macros are defined. It also gives your project cross-platform build capability similar to Urho3D project.

When both Urho3D static and shared library are built and available in the Urho3D project's library output directory, the FindUrho3D.cmake module has precedence to first select static library type over over shared library type. However, you can use URHO3D_LIB_TYPE build option to explicitly specify which Urho3D library type to be selected. When linking statically against Urho3D static library, Urho3D-CMake-common.cmake module automatically set "URHO3D_STATIC_DEFINE" compiler define for your project. This compiler define is crucial for static linking, especially on MSVC.

The define_source_files() and setup_main_executable() are Urho3D-specific macros. The define_source_file() macro is a shorthand to glob all the source files in the current source directory. It is equivalent to these commands:

file (GLOB CPP_FILES *.cpp)
file (GLOB H_FILES *.h)
set (SOURCE_FILES ${CPP_FILES} ${H_FILES})
The setup_main_executable() macro then uses SOURCE_FILES and TARGET_NAME variables to setup a new CMake target that would work on ALL platforms supported by Urho3D. Make sure both your project name and target name are not called 'Urho3D', as this name is already reserved by Urho3D project.

From Urho3D SDK installation
This section assumes that you have already installed Urho3D SDK into your system. If you have installed the SDK in a non-default location then you need to use "CMAKE_PREFIX_PATH" environment variable to specify the prefix path of the non-default installation location.

In your own project Source/ directory, create a new CMakeLists.txt file similar to the first approach but change the CMake module search path setup to read as follows on Windows platform:

# Set CMake modules search path
set (CMAKE_MODULE_PATH $ENV{CMAKE_PREFIX_PATH}/share/CMake/Modules CACHE PATH "Path to Urho3D-specific CMake modules")
On non-Windows platform:

# Set CMake modules search path
set (CMAKE_MODULE_PATH $ENV{CMAKE_PREFIX_PATH}/share/Urho3D/CMake/Modules CACHE PATH "Path to Urho3D-specific CMake modules")
Using pkg-config instead of CMake

If for some reason you could not use CMake in your project, you could configure your project to compile and link against Urho3D library from SDK installation using 'pkg-config' tool with the help of 'Urho3D.pc' configuration file (which is installed as part of the SDK installation). If the Urho3D SDK is being installed into a local location (such as /usr/local) or a non-default location, then most likely you would need to specify PKG_CONFIG_PATH environment variable to point to the location of the configuration file for this to work. The 'Urho3D.pc' file contains important information on how to configure your compiler and linker correctly. You can use that information as a reference to configure your own project even when you do not use 'pkg-config' tool.

Below are a few invocation examples on a 64-bit RedHat-based distro with local installation:

# To get installed Urho3D version
PKG_CONFIG_PATH=/usr/local/lib64/pkgconfig pkg-config --modversion Urho3D
# To compile and link natively in one liner
c++ -o Urho3DPlayer Urho3DPlayer.cpp `PKG_CONFIG_PATH=/usr/local/lib64/pkgconfig pkg-config --cflags --libs Urho3D`
# To compile and link natively but in separate steps
export PKG_CONFIG_PATH=/usr/local/lib64/pkgconfig
c++ -c Urho3DPlayer.cpp `pkg-config --cflags Urho3D`
c++ -o Urho3DPlayer Urho3DPlayer.o `pkg-config --libs Urho3D`
# To cross-compile and link for Raspberry Pi platform
export CC=${RASPI_TOOL}/arm-linux-gnueabihf-c++
export PKG_CONFIG_SYSROOT_DIR=${RASPI_ROOT}
export PKG_CONFIG_PATH=${RASPI_ROOT}/usr/local/lib/pkgconfig
$CC -o Urho3DPlayer Urho3DPlayer.cpp `pkg-config --cflags --libs Urho3D`
# To cross-compile and link for Windows platform using MinGW cross-compiler (in Debug configuration)
export CC=${MINGW_PREFIX}-c++
export PKG_CONFIG_SYSROOT_DIR=${MINGW_ROOT}
export PKG_CONFIG_PATH=${MINGW_ROOT}/usr/local/lib/pkgconfig
$CC -o Urho3DPlayer_d.exe Urho3DPlayer.cpp `pkg-config --variable CFLAGS_DEBUG Urho3D` `pkg-config --cflags --libs Urho3D`
Project scaffolding
With Rake ruby gem installed, you can quickly setup all the above by simply using the 'scaffolding' rake task.

Usage: rake scaffolding dir=/path/to/new/project/root [project=Scaffolding] [target=Main]
As its name implies, this task just creates a basic project structure for your new project. You may also pass optional "project" and "target" parameters besides the "dir" parameter. Otherwise, you will get the default project name (Scaffolding) and target name (Main) in the main CMakeLists.txt. This task copies the Urho3DPlayer.cpp and Urho3DPlayer.h as placeholders for the source files. Normally, you should replace these two files with your own project source files before invoking one of the CMake *.bat or *.sh script.

On Windows host system, this task requires privilege to create symlinks by using mklink command. The task would fail when the Windows user account does not have that privilege.
