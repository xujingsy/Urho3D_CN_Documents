Building prerequisites
Although all required third-party libraries are included as source code, there are system-level dependencies that must be satisfied before Urho3D can be built successfully:

For Windows, the June 2010 DirectX SDK needs to be installed.
For Linux, the following development packages need to be installed: libx11-dev, libxrandr-dev, libasound2-dev on Debian-based distros; libX11-devel, libXrandr-devel, alsa-lib-devel on RedHat-based distros. Also install the package libgl1-mesa-dev (Debian) or mesa-libGL-devel (RH) if your GPU driver does not include OpenGL headers & libs. Building as 32-bit on a 64-bit system requires installing also the 32-bit versions of the development libraries.
For Raspberry Pi, the following development packages need to be installed: libraspberrypi0, libraspberrypi-dev, libasound2-dev, libudev-dev on Raspbian; raspberrypi-vc-libs, raspberrypi-vc-libs-devel, alsa-lib-devel, systemd-devel on Pidora. The first two packages which contain the Broadcom VideoCore IV libraries and development headers should normally come preinstalled.
For Mac OS X, the Xcode developer tools package should include everything necessary.
For Android, the Android SDK and Android NDK (minimum API level 12) need to be installed. Optionally, also install Eclipse ADT plugin for building and deployment via Eclipse.
To run Urho3D, the minimum system requirements are:

Windows: CPU with SSE instructions support, Windows XP or newer, DirectX 9.0c, GPU with Shader Model 2 support (Shader Model 3 recommended.)
Linux & Mac OS X: CPU with SSE instructions support, GPU with OpenGL 2.0 support, EXT_framebuffer_object and EXT_packed_depth_stencil extensions.
Raspberry Pi: Model B revision 2.0 with at least 128 MB of 512 MB SDRAM allocated for GPU. OpenGL ES 2.0 capable GPU.
Android: OS version 2.3 or newer, OpenGL ES 2.0 capable GPU.
iOS: OpenGL ES 2.0 capable GPU.
SSE requirement can be eliminated by disabling the use of SSE instruction set, see Build options below.

Desktop build process
Urho3D uses CMake (http://www.cmake.org) to build. The process has two steps:

Run CMake in the root directory with your preferred toolchain specified to generate the build files. You can use the provided batch files or shell scripts on the respective platform.
Windows: cmake_vs2008.bat, cmake_vs2010.bat, cmake_vs2012.bat, or cmake_mingw.bat,
Linux: cmake_gcc.sh, cmake_eclipse.sh, or cmake_codeblocks.sh
Mac OS X: cmake_gcc.sh or cmake_macosx.sh.
Open the CMake's generated project file in the IDE of your choice. Change the build configuration (Debug/Release) and then build all the targets.
Visual Studio: open Urho3D.sln from the Build directory.
Xcode: open Urho3D.xcodeproj.
CodeBlocks: open Urho3D.cbp.
Eclipse: import project using File|Import "Existing Projects into Workspace".
GCC: execute make from the Build directory in a terminal/console.
Note that Eclipse requires CDT plugin to build C/C++ project.

If using MinGW to compile, DirectX headers may need to be acquired separately. They can be copied to the MinGW installation eg. from the following package: http://www.libsdl.org/extras/win32/common/directx-devel.tar.gz These will be missing some of the headers related to shader compilation, so a MinGW build will use OpenGL by default. To build in Direct3D9 mode, the MinGW-w64 port is necessary: http://mingw-w64.sourceforge.net/ Using it, Direct3D9 can be enabled with the CMake option -DURHO3D_OPENGL=0.

After the build is complete, the programs can be run from the Bin directory. These include the Urho3D player application, which can run application scripts, the tools, and C++ sample applications if they have been enabled.

To run the Urho3D player application from the Visual Studio debugger, set the Urho3DPlayer project as the startup project and enter its relative path and filename into Properties -> Debugging -> Command: ../../../Bin/Urho3DPlayer.exe. Additionally, entering -w into Debugging -> Command Arguments is highly recommended. This enables startup in windowed mode: without it running into an exception or breakpoint will be obnoxious as the mouse cursor will likely be hidden. To actually make the Urho3DPlayer application do something useful, it must be supplied with the name of the script file it should load and run. You can try for example the following arguments: Scripts/NinjaSnowWar.as -w

To run from Eclipse on Linux, locate and select the Urho3DPlayer executable in the Project Explorer. From the menu, choose "Run Configurations" to create a new launch configuration for "C/C++ Application". Switch to "Arguments" tab, specify the argument required by Urho3DPlayer executable.

To run from Xcode on Mac OS X, edit the Product Scheme to set "Run" setting to execute "Urho3DPlayer" in the "Info" tab. In the "Arguments" tab, specify the arguments required by Urho3DPlayer executable. Ensure the check boxes are ticked on the argument entries that you want to be active.

CMake caches some internal variables to speed up the subsequent invocation of the CMake build script. This is normally a good thing. However, there are cases when this is not desirable, for instance when switching CMake generators or after upgrading development software components. In such cases, it is recomended to first clean the CMake cache by invoking cmake_clean.bat or cmake_clean.sh.

Android build process
First, if you are building under Windows platform without MKLINK support then copy Bin/Data and Bin/CoreData directories to the Source/Android/assets directory (you can use the provided batch file CopyData.bat). This step is not necessary for Windows with MKLINK support and non-Windows platforms because the build script uses symbolic links for platforms that support it.

Set the ANDROID_NDK environment variable to point to your Android NDK. On Windows, ensure that make.exe from the Android NDK is included in the path and is executable from the command line.

On Windows, execute cmake_android.bat. If MKLINK support is available, provide build option "-DURHO3D_MKLINK=1" to generate out-of-source build. Then go to the build directory Source/Android (or android-Build if out-of-source build) and execute the following commands. On OS X or Linux, execute cmake_gcc.sh (the ANDROID_NDK environment variable distinguishes from a normal desktop build) then go to the android-Build directory (always an out-of-source build) and execute the following commands.

android update project -p . -t 1 (only needed on the first time, replace '-t 1' with desired target-id)
make -j8 (replace '-j8' with the number of logical CPU cores of the host/build system)
ant debug
After the commands finish successfully, the APK should have been generated to the build's "bin" subdirectory, from where it can be installed on a device or an emulator. The command "ant installd" can be used for this.

For a release build, use the "ant release" command instead of "ant debug" and follow the Android SDK instructions on how to sign your APK properly.

By default the Android package for Urho3D is com.googlecode.urho3d. For a real application you must replace this with your own package name. The Urho3D activity subclasses the SDLActivity from org.libsdl.app package, whose name (or the JNI code from SDL library) does not have to be changed.

Note that the native code is built by default for armeabi-v7a ABI. To make your program compatible also with old Android devices, build also an armeabi version by executing the CMake batch file again with the build option "-DANDROID_ABI=armeabi" added, then execute make again in the build directory. Similarly, the native code can be built using 64-bit ABI by changing the value of this build option. See Build options for all the possible values.

You can also build and deploy using Eclipse IDE with ADT plugin. To do that, after setting the ANDROID_NDK environment variable then run cmake_eclipse.sh. Import "Existing Android Code into Workspace" from the CMake generated Eclipse's project found in the android-Build directory. Switch Eclipse IDE to use Java Perspective. Update project properties to choose the desired Android API target and that's it. Just choose "Run" to let ADT automatically build and deploy the application to Android (virtual) device.

iOS build process
Run cmake_ios.sh. This generates an Xcode project named Urho3D.xcodeproj.

Open the Xcode project and check the properties for the Urho3D project (topmost in the Project Navigator.) In Architectures -> Base SDK, choose your iOS SDK (CMake would automatically select latest iOS when generating the Xcode project). In Code Signing, enter your developer identity as necessary.

The Urho3DPlayer target will actually build the application bundle and copy resources from Bin/Data and Bin/CoreData directories. Edit its build scheme to choose debug or release mode.

To run from Xcode on iPhone/iPad Simulator, edit the Product Scheme to set "Run" destination setting to "iPhone Simulator" or "iPad Simulator", and executable to "Urho3DPlayer.app".

Raspberry Pi build process
For native build on Raspberry Pi itself, use the similar process for Linux Desktop build described above.

For cross-compiling build on another build/host machine, firstly set the RASPI_TOOL environment variable to point to your Raspberry Pi Cross-Compiling tool where all the arm-linux-gnueabihf-* executables are located. You can setup the tool using crosstool-NG or just download one from https://github.com/raspberrypi/tools. Secondly, set the RASPI_ROOT environment variable to point to your Raspbian or Pidora system root. You must install the Urho3D prerequisites software development packages for Raspberry Pi (see Building prerequisites) in the system root before attempting to do the Urho3D cross-compiling build. You can download a Raspbian system root from https://github.com/urho3d/rpi-sysroot.

When running cmake_gcc.sh with RASPI_TOOL environment variable set, it tells build script to generate additional raspi-Build directory for cross-compiling. Go to this raspi-Build directory and proceed to execute make. After the build is complete, the ARM executables can be found in Bin-CC output directory.

You can also build, deploy, run/debug (as C/C++ Remote Application) using Eclipse IDE, if you run cmake_eclipse.sh to generate the project file. Import the CMake generated Eclipse project in the raspi-Build directory into Eclipse's workspace. Build the project as usual. Use the URHO3D_SCP_TO_TARGET build option to automatically deploy the ARM executables to target Raspberry Pi as part of every project build or configure Eclipse to perform a "download to target path" in the Run/Debug configuration for C/C++ Remote Application. Either way, you have to configure the Run/Debug configuration how to reach your target Raspberry Pi.

MinGW cross-compile build process
It is possible to cross-compile Urho3D for Windows using a Linux system. The process is largely the same as for the Linux Desktop build process described above.

To cross-compile, the MinGW tool-chain (compiler, linker and w32api) needs to be installed on the system. You will also need the DirectX header files, those can be downloaded and installed from the following packet: http://www.libsdl.org/extras/win32/common/directx-devel.tar.gz.

For activating the MinGW tool-chain, and to allow it to find the correct compiler, the MINGW_PREFIX environment variable needs to be set when running cmake_gcc.sh. This variable should be set to the prefix of the compiler name. So, if for example your MinGW compiler is named i686-pc-mingw32-gcc, the MINGW_PREFIX should read i686-pc. Most likely you also need to set MINGW_ROOT environment variable to point to your mingw32 system root.

Running cmake_gcc.sh with the MINGW_PREFIX environment variable set, produces an additional mingw-Build directory. Go to this directory and execute make to start the build process. When the build is complete, the Windows executables can be found in the mingw-Bin output directory.

Desktop 64bit build
When using MSVC compiler, the Urho3D CMake build script will configure the Urho3D project to be built in 32-bit by default. When using other non-MSVC compilers (like GCC or clang), the CMake build script will set the default to 32-bit or 64-bit based on the installed toolchain in the host system. You can use the build option "URHO3D_64BIT" to override the default, by setting the option to '0' (for 32-bit) and '1' (for 64-bit) explicitly. For MSVC on Windows platform, setting the option to '1' also instructs the build script to use a 64-bit solution generator.

Library build
As of V1.31, the build process first builds the Urho3D library target (either static or shared). The library is then linked against by other targets like tools and samples that reference Urho3D as one of the external libraries. The Urho3D library type is defaulted to static, so the build process would generate standalone executables as previous releases. The Urho3D library type can be changed using "URHO3D_LIB_TYPE" build option.

To install the Urho3D library (or should we call it SDK), use the usual 'make install' command when using Makefile. There is an equivalent command in Visual Studio and Xcode IDE to build 'install' target instead of the default 'all' target. This could be useful when you want your application to always link against a 'stable' installed version of the Urho3D library, while keeping your Urho3D project root tree in sync with origin/master. That is, install the newly built library after you have tested the changes do not break your application during development.

Refer to Using Urho3D as external library on how to setup your own project to use Urho3D as external library.

Documentation build
If URHO3D_DOCS build option is set then a normal (ALL) build would not only build Urho3D software but also Urho3D documentation automatically. If it is not then the documentation can be generated by manually invoking 'make doc' command or its equivalent command in IDE.

The prerequisites are Doxygen and Graphviz. Tools to dump the AngelScript API for the default scripting subsystem and the LuaScript API (when the LuaScript subsystem is also enabled) will be built internally when all the tools are being built.

Compiling Direct3D shaders
When building with the Windows 8 SDK, copy d3dcompiler_46.dll from C:/Program Files (x86)/Windows Kits/8.0/bin/x86 to Urho3D Bin directory so that Urho3D executables will run correctly.

Note that you can also force an OpenGL mode build on Windows by using the CMake option in the table below; OpenGL does not depend on a separate shader compiler DLL.

Build options
A number of build options can be defined explicitly when invoking the above cmake_xxxx batch files or shell scripts.
Build Option	V	Description
URHO3D_64BIT	-	Enable 64bit build, on MSVC default to 0, on other compilers the default is set based on the installed toolchain on host system
URHO3D_ANGELSCRIPT	1	Enable AngelScript scripting support
URHO3D_LUA	0	Enable Lua scripting support
URHO3D_LUAJIT	0	Enable Lua scripting support using LuaJIT (check LuaJIT's CMakeLists.txt for more options)
URHO3D_LUAJIT_AMALG	0	Enable LuaJIT amalgamated build (LuaJIT only)
URHO3D_SAFE_LUA	0	Enable Lua C++ wrapper safety checks (when Lua scripting support is enabled only)
URHO3D_NETWORK	1	Enable Networking support
URHO3D_PHYSICS	1	Enable Physics support
URHO3D_NAVIGATION	1	Enable Navigation support
URHO3D_URHO2D	1	Enable 2D rendering & physics support
URHO3D_SAMPLES	0	Build sample applications
URHO3D_TOOLS	1	Build standalone tools (Desktop and RPI only; on Android only build Lua standalone tools)
URHO3D_EXTRAS	0	Build extras (Desktop and RPI only)
URHO3D_DOCS	0	Generate documentation as part of normal build (the 'doc' builtin target can be used to generate documentation regardless of this option's value)
URHO3D_DOCS_QUIET	0	Generate documentation as part of normal build, suppress generation process from sending anything to stdout
URHO3D_SSE	1	Enable SSE instruction set
URHO3D_MINIDUMPS	1	Enable minidumps on crash (VS only)
URHO3D_FILEWATCHER	1	Enable filewatcher support
URHO3D_PROFILING	1	Enable profiling support
URHO3D_LOGGING	1	Enable logging support
URHO3D_TESTING	0	Enable testing support
URHO3D_TEST_TIME_OUT	5	Number of seconds to test run the executables (when testing support is enabled only)
URHO3D_OPENGL	0	Use OpenGL instead of Direct3D (Windows platform only)
URHO3D_MKLINK	0	Use mklink command to create symbolic links (Windows Vista and above only)
URHO3D_STATIC_RUNTIME	0	Use static C/C++ runtime libraries and eliminate the need for runtime DLLs installation (VS only)
URHO3D_LIB_TYPE	*	Specify Urho3D library type, possible values are STATIC (*default) and SHARED
URHO3D_SCP_TO_TARGET	-	Use scp to transfer executables to target system (non-Android cross-compiling build only), SSH digital key must be setup first for this to work, typical value has a pattern of usr:remote-loc
URHO3D_NDK_GDB	0	Enable ndk-gdb for debugging (Android build only)
CMAKE_BUILD_TYPE	*	Specify CMake build configuration to be generated (Makefile generator only), possible values are Release (*default), Debug, and RelWithDebInfo
CMAKE_OSX_DEPLOYMENT_TARGET	-	Specify Mac OS X deployment target (OSX build only); default to current running OS X if not specified
IPHONEOS_DEPLOYMENT_TARGET	-	Specify iPhone OS deployment target (iOS build only); default to latest installed iOS SDK if not specified
ANDROID_ABI	*	Specify target ABI (Android build only), possible values are armeabi, armeabi-v7a (*default), armeabi-v7a with NEON, armeabi-v7a with VFPV3, armeabi-v6 with VFP, arm64-v8a, x86, and x86_64
Note that build option values specified via command line are cached by CMake. The cached values will be used by CMake in the subsequent invocation. That is, the same build options are not required to be specified again and again. Once a non-default build option value is being cached, it can only be reverted back to its default value by explicitly setting it via command line. That is, simply by NOT passing the corresponding build option would not work. One way to revert all the build options to their default values is by clearing the CMake cache by calling cmake_clean.bat or cmake_clean.sh.

Using cmake-gui to configure and generate Urho3D project file
Instead of using one of the provided batch files or shell scripts, you can use cmake-gui to configure and generate Urho3D project file. However, you have to adhere to the current limitations:

The build directory must be located inside the Urho3D project root directory and sibling of the "Source" directory.
The build directory must be named accordingly based on the target platform.
On desktop/native platform: Build
On Windows platform using MinGW: mingw-Build
On iOS platform: ios-Build
On Android platform: android-Build
On Raspberry Pi platform: raspi-Build
The runtime and archive output directories are automatically set based on the target platform regardless of the build directory name (should you choose not to adhere with the above). For example on Android platform, they will be android-Bin and android-Lib, respectively.
If you choose not to adhere with this build directory naming convention then you will have to install the Urho3D library into your local filesystem in order to use the Urho3D library in your external project. See Library build and From Urho3D SDK installation.
All the post-CMake workaround and/or bug fixes that are scripted in the batch files or shell scripts are not applied to the generated project file.
Steps to configure:

In the Urho3D project root directory, invoke "cmake-gui Source".
Set the build directory name.
Configure and update the build options as many times as necessary until there are no more new options in red. For the first configuration, choose the generator you like to use. Click the Group check box to group the build options.
In the Ungrouped Entries: check IOS option when targeting it on Xcode.
In the URHO3D group: check any of the options you desire. Some of the options when checked may cause new options to be made available in the subsequent configuration loop.
Generate when all the configurations are done.
