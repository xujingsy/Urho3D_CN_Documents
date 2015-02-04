Building prerequisites Although all required third-party libraries are included as source code, there are system-level dependencies that must be satisfied before Urho3D can be built successfully:

尽管构建的先决条件需要的所有第三方库都包含在源代码里, 但是在Urho3D成功建构之前
还必须满足的系统级依赖有:


For Windows, the June 2010 DirectX SDK needs to be installed. For Linux, the following development packages need to be installed: libx11-dev, libxrandr-dev, libasound2-dev on Debian-based distros; libX11-devel, libXrandr-devel, alsa-lib-devel on RedHat-based distros. Also install the package libgl1-mesa-dev (Debian) or mesa-libGL-devel (RH) if your GPU driver does not include OpenGL headers & libs. Building as 32-bit on a 64-bit system requires installing also the 32-bit versions of the development libraries. For Raspberry Pi, the following development packages need to be installed: libraspberrypi0, libraspberrypi-dev, libasound2-dev, libudev-dev on Raspbian; raspberrypi-vc-libs, raspberrypi-vc-libs-devel, alsa-lib-devel, systemd-devel on Pidora. The first two packages which contain the Broadcom VideoCore IV libraries and development headers should normally come preinstalled. For Mac OS X, the Xcode developer tools package should include everything necessary. For Android, the Android SDK and Android NDK (minimum API level 12) need to be installed. Optionally, also install Eclipse ADT plugin for building and deployment via Eclipse. To run Urho3D, the minimum system requirements are:

对于Windows, 必须安装June 2010 DirectX SDK. 对于Linux, 以下的开发包必须安装: libx11-dev, libxrandr-dev, libasound2-dev(Debian系统); libX11-devel, libXrandr-devel, alsa-lib-devel(RedHat系统). 如果你的GPU驱动没有提供OpenGL的头文件和库, 还需要安装libgl1-mesa-dev (Debian) 或者mesa-libGL-devel (RH). 要在64位系统上构建32位应用, 还需要安装32位版本的开发库. 对于Raspberry Pi, 需要安装以下的开发包: libraspberrypi0, libraspberrypi-dev, libasound2-dev, libudev-dev( Raspbian系统); raspberrypi-vc-libs, raspberrypi-vc-libs-devel, alsa-lib-devel, systemd-devel(Pidora系统). 头两个包包含了Broadcom VideoCore IV的库和开发头文件通常已经预装. 对于Mac OS X, Xcode开发工具包包含了所有必须的组件.对于Android, 需要安装Android SDK和Android NDK(最低版本API 12). 可选的, 用Eclipse构建和开发, 还需要安装Eclipse ADT插件. 运行Urho3D的最低系统要求是:


Windows: CPU with SSE instructions support, Windows XP or newer, DirectX 9.0c, GPU with Shader Model 2 support (Shader Model 3 recommended.) Linux & Mac OS X: CPU with SSE instructions support, GPU with OpenGL 2.0 support, EXT_framebuffer_object and EXT_packed_depth_stencil extensions. Raspberry Pi: Model B revision 2.0 with at least 128 MB of 512 MB SDRAM allocated for GPU. OpenGL ES 2.0 capable GPU. Android: OS version 2.3 or newer, OpenGL ES 2.0 capable GPU. iOS: OpenGL ES 2.0 capable GPU. SSE requirement can be eliminated by disabling the use of SSE instruction set, see Build options below.

Windows: CPU需要支持SSE指令, 操作系统版本不低于WindowsXP, DirectX 9.0c, GPU需要支持Shader Model 2(推荐Shader Model 3). Linux 和 Mac OS X: CPU需要支持SSE指令, GPU需要支持OpenGL 2.0, EXT_framebuffer_object和 EXT_packed_depth_stencil扩展. Raspberry Pi: Model B revision 2.0版本至少512MB SDRAM内存并为GPU分配128MB. GPU支持OpenGL ES 2.0. Android: 系统版本不低于2.3, GPU需要支持OpenGL ES 2.0. iOS: GPU需要支持OpenGL ES 2.0, 用过禁用SSE指令集,SSE可以不是必须的, 见下面的构建选项.


Desktop build process Urho3D uses CMake (http://www.cmake.org) to build. The process has two steps:

构建Urho3D使用Cmake(http://www.cmake.org). 构建过程分为两步:


Run CMake in the root directory with your preferred toolchain specified to generate the build files. You can use the provided batch files or shell scripts on the respective platform. Windows: cmake_vs2008.bat, cmake_vs2010.bat, cmake_vs2012.bat, or cmake_mingw.bat, Linux: cmake_gcc.sh, cmake_eclipse.sh, or cmake_codeblocks.sh Mac OS X: cmake_gcc.sh or cmake_macosx.sh. Open the CMake's generated project file in the IDE of your choice. Change the build configuration (Debug/Release) and then build all the targets. Visual Studio: open Urho3D.sln from the Build directory. Xcode: open Urho3D.xcodeproj. CodeBlocks: open Urho3D.cbp. Eclipse: import project using File|Import "Existing Projects into Workspace". GCC: execute make from the Build directory in a terminal/console. Note that Eclipse requires CDT plugin to build C/C++ project.
在指定生成构建文件的根目录运行CMake.  可以使用提供的各自平台的批处理文件或者shell脚本.  Windows: cmake_vs2008.bat, cmake_vs2010.bat, cmake_vs2012.bat 或者 cmake_mingw.bat. Linux: cmake_gcc.sh, cmake_eclipse.sh 或者 cmake_codeblocks.sh. Mac OS X: cmake_gcc.sh 或者 cmake_macosx.sh. 在你选择的IDE(集成开发环境)中打开CMake生成的项目文件然后构建所有目标. Visual Studio: 从构建目录打开Urho3D.sln. Xcode: 打开Urho3D.xcodeproj. CodeBlocks: 打开Urho3D.cbp. Eclipse: 使用 File|Import "Existing Projects into Workspace" 导入项目. GCC: 在终端或者控制台在构建目录执行make. 注意Eclipse构建C/C++项目需要CDT插件.


If using MinGW to compile, DirectX headers may need to be acquired separately. They can be copied to the MinGW installation eg. from the following package: http://www.libsdl.org/extras/win32/common/directx-devel.tar.gz These will be missing some of the headers related to shader compilation, so a MinGW build will use OpenGL by default. To build in Direct3D9 mode, the MinGW-w64 port is necessary: http://mingw-w64.sourceforge.net/ Using it, Direct3D9 can be enabled with the CMake option -DURHO3D_OPENGL=0.

如果使用MinGW来编译, 可能需要单独获得DirectX头文件. 它们可以被拷贝到MinGW安装. 比如从下面的包http://www.libsdl.org/extras/win32/common/directx-devel.tar.gz 这里会缺少缺少一些编译shader相关的头文件, 因为MinGW默认安装是使用OpenGL. 为以Direct3D9模式进行构建, 需要使用MinGW-w64: http://mingw-w64.sourceforge.net/, 支持Direct3D9需要CMake选项-DURHO3D_OPENGL=0.


After the build is complete, the programs can be run from the Bin directory. These include the Urho3D player application, which can run application scripts, the tools, and C++ sample applications if they have been enabled.

构建完成后, 可以在二进制目录运行程序. 那里包括Urho3D player应用, 可运行应用脚本,工具和C++示例程序. 


To run the Urho3D player application from the Visual Studio debugger, set the Urho3DPlayer project as the startup project and enter its relative path and filename into Properties -> Debugging -> Command: ../../../Bin/Urho3DPlayer.exe. Additionally, entering -w into Debugging -> Command Arguments is highly recommended. This enables startup in windowed mode: without it running into an exception or breakpoint will be obnoxious as the mouse cursor will likely be hidden. To actually make the Urho3DPlayer application do something useful, it must be supplied with the name of the script file it should load and run. You can try for example the following arguments: Scripts/NinjaSnowWar.as -w

要在Visual Studio调试运行Urho3D player程序, 将Urho3DPlayer项目的何止为启动项目然后在项目属性Properties -> Debugging -> Command设置为相对路径文件名../../../Bin/Urho3DPlayer.exe. 另外, 极力推荐在项目属性Debugging -> Command Arguments输入选项 –w. 要以窗口化模式启动: 除非运行时发生异常或者断点, 讨厌的鼠标可能被隐藏. 要实际构建Urho3DPlayer应用做一些有用的事情, 必须支持根据名字载入和运行脚本文件. 你可以试着用这个的参数来运行例子: Scripts/NinjaSnowWar.as -w


To run from Eclipse on Linux, locate and select the Urho3DPlayer executable in the Project Explorer. From the menu, choose "Run Configurations" to create a new launch configuration for "C/C++ Application". Switch to "Arguments" tab, specify the argument required by Urho3DPlayer executable.

在Linux系统的Eclipse中运行程序, 在项目浏览器窗口找到并选中Urho3DPlayer可执行文件. 选择菜单"Run Configurations"为"C/C++ Application"创建启动配置. 切换到"Arguments"选项卡, 指定Urho3DPlayer执行需要的参数.


To run from Xcode on Mac OS X, edit the Product Scheme to set "Run" setting to execute "Urho3DPlayer" in the "Info" tab. In the "Arguments" tab, specify the arguments required by Urho3DPlayer executable. Ensure the check boxes are ticked on the argument entries that you want to be active.

在Mac OS X系统的Xcode, 编辑项目方案在"Run"的"Info"选项卡设置"Urho3DPlayer"来执行. 在"Arguments"选项卡, 为Urho3DPlayer指定执行需要的参数. 确保你想要激活的参数选择框是选中的.


CMake caches some internal variables to speed up the subsequent invocation of the CMake build script. This is normally a good thing. However, there are cases when this is not desirable, for instance when switching CMake generators or after upgrading development software components. In such cases, it is recomended to first clean the CMake cache by invoking cmake_clean.bat or cmake_clean.sh.

CMake为提速随后的CMake构建脚本调用缓存了中间变量. 这样通常是一件好事. 然而有时这样做是不对的, 例如换了CMake生成器或者升级了开发软件组件. 这个时候, 要记得先调用cmake_clean.bat或者cmake_clean.sh来清除CMake的缓存.


Android build process First, if you are building under Windows platform without MKLINK support then copy Bin/Data and Bin/CoreData directories to the Source/Android/assets directory (you can use the provided batch file CopyData.bat). This step is not necessary for Windows with MKLINK support and non-Windows platforms because the build script uses symbolic links for platforms that support it.

Android构建第一步, 如果你是在没有MKLINK支持的Windows平台, 那么将 Bin/Data 和 Bin/CoreData目录拷贝到Source/Android/assets目录(可以使用提供的批处理文件CopyData.bat). 因为构建脚本在支持符号链接的平台上使用符号链接, 因此在支持MKLINK命令的Windows和非Windows平台上, 这一步是不需要的.


Set the ANDROID_NDK environment variable to point to your Android NDK. On Windows, ensure that make.exe from the Android NDK is included in the path and is executable from the command line.

设置环境变量ANDROID_NDK指向你的Android NDK. 在Windows, 确保Android NDK的make.exe被包含在path路径并可以在命令行执行.


On Windows, execute cmake_android.bat. If MKLINK support is available, provide build option "-DURHO3D_MKLINK=1" to generate out-of-source build. Then go to the build directory Source/Android (or android-Build if out-of-source build) and execute the following commands. On OS X or Linux, execute cmake_gcc.sh (the ANDROID_NDK environment variable distinguishes from a normal desktop build) then go to the android-Build directory (always an out-of-source build) and execute the following commands.

在Windows, 执行cmake_android.bat. 如果支持MKLINK名命令, 使用参数"-DURHO3D_MKLINK=1" 来生成非源代码构建. 然后到构建目录Source/Android(如果在源代码外构建则是android-Build)然后执行下面的命令. 在OS X或者Linux, 执行cmake_gcc.sh(由ANDROID_NDK环境变量区分从正常的桌面构建), 然后到android-Build目录(总是在源代码外构建),执行下面的命令.


android update project -p . -t 1 (only needed on the first time, replace '-t 1' with desired target-id) make -j8 (replace '-j8' with the number of logical CPU cores of the host/build system) ant debug After the commands finish successfully, the APK should have been generated to the build's "bin" subdirectory, from where it can be installed on a device or an emulator. The command "ant installd" can be used for this.

android update project -p . -t 1  (只在第一次需要, 用所需的目标ID替换掉”-t 1”)
make -j8  (用构建系统的逻辑CPU数替换 “-j8” 的数字)
ant debug
这些命令成功执行后, APK就生成了构建用的”bin”子目录, 那里可以安装设备或者模拟器.这里可以使用 "ant installd" 命令.


For a release build, use the "ant release" command instead of "ant debug" and follow the Android SDK instructions on how to sign your APK properly.

要构建release, 使用"ant release"替换"ant debug"命令, 并且按照Android SDK的指示适当的选择APK签名.


By default the Android package for Urho3D is com.googlecode.urho3d. For a real application you must replace this with your own package name. The Urho3D activity subclasses the SDLActivity from org.libsdl.app package, whose name (or the JNI code from SDL library) does not have to be changed.

默认Android的Urho3D 包是com.googlecode.urho3d. 在实际的应用中, 你需要用你自己的包名替换它. Urho3D的活动子类是SDLActivity在org.libsdl.app包, 不必修改它的名字(或者SDL库的JNI代码)


Note that the native code is built by default for armeabi-v7a ABI. To make your program compatible also with old Android devices, build also an armeabi version by executing the CMake batch file again with the build option "-DANDROID_ABI=armeabi" added, then execute make again in the build directory. Similarly, the native code can be built using 64-bit ABI by changing the value of this build option. See Build options for all the possible values.

注意,原生代码构建默认是用armeabi-v7a ABI. 要使你的程序也兼容旧的Android设备, 带构建参数"-DANDROID_ABI=armeabi"再次执行CMake批处理文件构建arm abi版本, 然后在构建目录再次执行make. 同样, 通过修改构建选项,原生代码可以使用64位ABI来构建. 参见所有构建选项的取值.


You can also build and deploy using Eclipse IDE with ADT plugin. To do that, after setting the ANDROID_NDK environment variable then run cmake_eclipse.sh. Import "Existing Android Code into Workspace" from the CMake generated Eclipse's project found in the android-Build directory. Switch Eclipse IDE to use Java Perspective. Update project properties to choose the desired Android API target and that's it. Just choose "Run" to let ADT automatically build and deploy the application to Android (virtual) device.

你也可以使用 Eclipse IDE 和ADT插件来构建和部署. 这样,先设置ANDROID_NDK环境变量,然后运行cmake_eclipse.sh. 从android-Build目录找到CMake生成的Eclipse项目导入"Existing Android Code into Workspace". 将Eclipse IDE切换到Java视图.  选择希望的Android API目标更新项目属性.  要让ADT自动构建和部署应用到Android(虚拟)设备,只要选择"Run". 


iOS build process Run cmake_ios.sh. This generates an Xcode project named Urho3D.xcodeproj.

在iOS构建,运行cmake_ios.sh. 这将创建一个命名为Urho3D.xcodeproj的Xcode项目. 


Open the Xcode project and check the properties for the Urho3D project (topmost in the Project Navigator.) In Architectures -> Base SDK, choose your iOS SDK (CMake would automatically select latest iOS when generating the Xcode project). In Code Signing, enter your developer identity as necessary.

打开Xcode项目检查 Urho3D的项目属性 (在项目导航的上面). 在Architectures -> Base SDK, 选择你的iOS SDK(CMake会自动选择生成Xcode项目所需的最低iOS). 必须在Code Signing输入你的开发者ID.


The Urho3DPlayer target will actually build the application bundle and copy resources from Bin/Data and Bin/CoreData directories. Edit its build scheme to choose debug or release mode.

Urho3DPlayer目标会自动构建应用包, 并从Bin/Data 和 Bin/CoreData目录拷贝资源. 编辑构建方案选择debug或者release模式.


To run from Xcode on iPhone/iPad Simulator, edit the Product Scheme to set "Run" destination setting to "iPhone Simulator" or "iPad Simulator", and executable to "Urho3DPlayer.app".

在iPhone/iPad模拟器上运行Xcode, 编辑Product Scheme设置"Run"目标为"iPhone Simulator" 或者 "iPad Simulator", 然后执行"Urho3DPlayer.app".


Raspberry Pi build process For native build on Raspberry Pi itself, use the process for Linux Desktop build described above.

在Raspberry Pi为Raspberry Pi自身构建原生应用的构建过程, 与上面Linux桌面构建类似.


For cross-compiling build on another build/host machine, firstly set the RASPI_TOOL environment variable to point to your Raspberry Pi Cross-Compiling tool where all the arm-linux-gnueabihf-* executables are located. You can setup the tool using crosstool-NG or just download one from https://github.com/raspberrypi/tools. Secondly, set the RASPI_ROOT environment variable to point to your Raspbian or Pidora system root. You must install the Urho3D prerequisites software development packages for Raspberry Pi (see Building prerequisites) in the system root before attempting to do the Urho3D cross-compiling build. You can download a Raspbian system root from https://github.com/urho3d/rpi-sysroot.

在另外的机器上交叉编译构建, 首先设置环境变量RASPI_TOOL指向你的所有 arm-linux-gnueabihf-* 可执行Raspberry Pi交叉编译工具的位置. 你可以使用crosstool-NG安装工具或者直接从 https://github.com/raspberrypi/tools 下载一个. 第二步,  设置环境变变量RASPI_ROOT指向你的Raspbian或者Pidora的系统根. 在试图进行交叉编译构建之前,你必须将Urho3D先决软件开发包(参见构建先决条件) 安装在Raspberry Pi的系统根.  你可以从https://github.com/urho3d/rpi-sysroot下载一个Raspbian系统根.


When running cmake_gcc.sh with RASPI_TOOL environment variable set, it tells build script to generate additional raspi-Build directory for cross-compiling. Go to this raspi-Build directory and proceed to execute make. After the build is complete, the ARM executables can be found in Bin-CC output directory.

当在RASPI_TOOL环境变量设置下运行cmake_gcc.sh, 它调用构建脚本为交叉编译生成附加的raspi-Build目录. 进入raspi-Build目录继续执行make. 当构建完成后, 在Bin-CC输出目录可以找到ARM可执行文件.


You can also build, deploy, run/debug (as C/C++ Remote Application) using Eclipse IDE, if you run cmake_eclipse.sh to generate the project file. Import the CMake generated Eclipse project in the raspi-Build directory into Eclipse's workspace. Build the project as usual. Use the URHO3D_SCP_TO_TARGET build option to automatically deploy the ARM executables to target Raspberry Pi as part of every project build or configure Eclipse to perform a "download to target path" in the Run/Debug configuration for C/C++ Remote Application. Either way, you have to configure the Run/Debug configuration how to reach your target Raspberry Pi.

如果你是运行cmake_eclipse.sh来生成项目文件, 你也可以使用Eclipse IDE老来构建,部署,运行/调试(作为C/C++远程应用). 从raspi-Build目录导入CMake生成的Eclipse项目. 构建项目. 使用URHO3D_SCP_TO_TARGET构建选项来将 ARM可执行文件作为每个项目构建的一部分自动部署到目标Raspberry Pi, 或者在Eclipsed的Run/Debug为C/C++远程应用配置 "download to target path". 无论哪种方式, 你必须配置Run/Debug来配置怎样到达你的目标Raspberry Pi.


MinGW cross-compile build process It is possible to cross-compile Urho3D for Windows using a Linux system. The process is largely the same as for the Linux Desktop build process described above.

MinGW交叉编译过程可能使用Linux系统为Windows交叉编译Urho3D. 这个过程很大程度上与上面的Linux桌面构建过程相同.


To cross-compile, the MinGW tool-chain (compiler, linker and w32api) needs to be installed on the system. You will also need the DirectX header files, those can be downloaded and installed from the following packet: http://www.libsdl.org/extras/win32/common/directx-devel.tar.gz.

交叉编译系统需要安装MinGW工具链(编译器,链接器和w32api). 你还需要DirectX头文件. 这些可以从下面的安装包下载安装:
 http://www.libsdl.org/extras/win32/common/directx-devel.tar.gz.


For activating the MinGW tool-chain, and to allow it to find the correct compiler, the MINGW_PREFIX environment variable needs to be set when running cmake_gcc.sh. This variable should be set to the prefix of the compiler name. So, if for example your MinGW compiler is named i686-pc-mingw32-gcc, the MINGW_PREFIX should read i686-pc. Most likely you also need to set MINGW_ROOT environment variable to point to your mingw32 system root.

要激活MinGW工具链, 让它找到正确的编译器,需要设置MINGW_PREFIX环境变量, 然后运行cmake_gcc.sh. 这个变量应该设置为编译器名的前缀. 这样, 例如你的MinGW编译器名字是i686-pc-mingw32-gcc, 那么MINGW_PREFIX应该设置为i686-pc. 很可能你还需要设置MINGW_ROOT环境变量指向你的mingw32系统根.


Running cmake_gcc.sh with the MINGW_PREFIX environment variable set, produces an additional mingw-Build directory. Go to this directory and execute make to start the build process. When the build is complete, the Windows executables can be found in the mingw-Bin output directory.

设置MINGW_PREFIX环境变量运行cmake_gcc.sh生成一个附加的mingw-Build目录. 进入这个目录执行make开始构建过程. 当构建完成, 可以在mingw-Bin输出目录找到Windows可执行文件.


Desktop 64bit build When using MSVC compiler, the Urho3D CMake build script will configure the Urho3D project to be built in 32-bit by default. When using other non-MSVC compilers (like GCC or clang), the CMake build script will set the default to 32-bit or 64-bit based on the installed toolchain in the host system. You can use the build option "URHO3D_64BIT" to override the default, by setting the option to '0' (for 32-bit) and '1' (for 64-bit) explicitly. For MSVC on Windows platform, setting the option to '1' also instructs the build script to use a 64-bit solution generator.

当使用MSVC编译器构建64位桌面应用, Urho3D的CMake构建脚本默认配置Urho3D项目是32位. 当使用其他非MSVC编译器(例如GCC或者Clang), CMake构建脚本将根据系统安装的工具链设置默认为32位还是64位. 你可以将构建选项"URHO3D_64BIT"明确设置选项值为’0’(32位)或者’1’(64位)来重写默认值. 对于Windows平台的MSVC, 设置为’1’同时指示构建脚本使用64位的解决方案生成器.


Library build As of V1.31, the build process first builds the Urho3D library target (either static or shared). The library is then linked against by other targets like tools and samples that reference Urho3D as one of the external libraries. The Urho3D library type is defaulted to static, so the build process would generate standalone executables as previous releases. The Urho3D library type can be changed using "URHO3D_LIB_TYPE" build option.

构建V1.31库, 构建过程首先构建Urho3D库目标(静态的和共享的). 然后库将作为一个外部库链接到其他目标比如工具链和Urho3D例子参考. Urho3D库默认是静态库, 所以先前的发布构建过程会生成单独的可执行程序. Urho3D库可以通过修改"URHO3D_LIB_TYPE"参数来改变. 


To install the Urho3D library (or should we call it SDK), use the usual 'make install' command when using Makefile. There is an equivalent command in Visual Studio and Xcode IDE to build 'install' target instead of the default 'all' target. This could be useful when you want your application to always link against a 'stable' installed version of the Urho3D library, while keeping your Urho3D project root tree in sync with origin/master. That is, install the newly built library after you have tested the changes do not break your application during development.

通过Makefile文件安装Urho3D库(我们将调用它的SDK), 使用平常的'make install'命令. 这个命令等效于Visual Studio 和 Xcode IDE的对’All’目标执行构建命令'install'. 这可用于将你的应用总是链接到一个已经安装的稳定版本的Urho3D库. 期间可保持你的Urho3D项目根与原主干的同步. 这样, 当你测试改变后安装较新的构件库,不会中断你开发中的应用.


Refer to Using Urho3D as external library on how to setup your own project to use Urho3D as external library.

关于作为外部库使用Urho3D怎样安装你自己项目, 参考Urho3D作为外部库.


Documentation build If URHO3D_DOCS build option is set then a normal (ALL) build would not only build Urho3D software but also Urho3D documentation automatically. If it is not then the documentation can be generated by manually invoking 'make doc' command or its equivalent command in IDE.

在通常(ALL)构建时如果设置了URHO3D_DOCS构建选项, 不仅构建Urho3D软件而且Urho3D文档也自动地构建. 否则可以手动调用'make doc'命令或者在IDE中调用与之等效的命令来生成文档.


The prerequisites are Doxygen and Graphviz. Tools to dump the AngelScript API for the default scripting subsystem and the LuaScript API (when the LuaScript subsystem is also enabled) will be built internally when all the tools are being built.

构建文档需要Doxygen和Graphviz. 当所有工具被构建,转储默认脚本子系统AngelScript API和LuaScript API(当LuaScript子系统也被允许)工具是内建的. 


Compiling Direct3D shaders When building with the Windows 8 SDK, copy d3dcompiler_46.dll from C:/Program Files (x86)/Windows Kits/8.0/bin/x86 to Urho3D Bin directory so that Urho3D executables will run correctly.

当在Windows 8 SDK构建编译Direct3D shaders, 从C:/Program Files (x86)/Windows Kits/8.0/bin/x86拷贝d3dcompiler_46.dll到Urho3D的Bin目录, 以便Urho3D可正确运行.


Note that you can also force an OpenGL mode build on Windows by using the CMake option in the table below; OpenGL does not depend on a separate shader compiler DLL.

注意,在Windows下你还可以通过使用下表的CMake选项强制使用OpenGL模式; OpenGL不依赖于独立的shader编译器DLL.

Build options A number of build options can be defined explicitly when invoking the above cmake_xxxx batch files or shell scripts. 
构建选项A:  当调用上面cmake_xxxx批处理脚本火shell脚本时候可明确定义的构建选项数字

Build Option V Description 
构建选项V: 描述

URHO3D_64BIT - Enable 64bit build, on MSVC default to 0, on other compilers the default is set based on the installed toolchain on host system
URHO3D_64BIT – 启用64位构建, 在MSVC默认是0, 在其他编译器默认值由机器安装的工具链决定.

URHO3D_ANGELSCRIPT 1 Enable AngelScript scripting support 
URHO3D_ANGELSCRIPT – 1 启用AngelScript脚本支持

URHO3D_LUA 0 Enable Lua scripting support
URHO3D_LUA – 0 启用Lua脚本支持

URHO3D_LUAJIT 0 Enable Lua scripting support using LuaJIT (check LuaJIT's CMakeLists.txt for more options) 
URHO3D_LUAJIT – 0 启用Lua的LuaJIT支持(更多选项查看LuaJIT的CMakeLists.txt)

URHO3D_LUAJIT_AMALG 0 Enable LuaJIT amalgamated build (LuaJIT only) 
URHO3D_LUAJIT_AMALG – 0 启用LuaJIT合并构建(仅LuaJIT)

URHO3D_SAFE_LUA 0 Enable Lua C++ wrapper safety checks (when Lua scripting support is enabled only) 
URHO3D_SAFE_LUA – 0 启用Lua的C++包装的安全检查(仅当启用了Lua脚本支持)

URHO3D_NETWORK 1 Enable Networking support 
URHO3D_NETWORK – 1 启用网络支持

URHO3D_PHYSICS 1 Enable Physics support 
URHO3D_PHYSICS – 1 启用物理支持

URHO3D_NAVIGATION 1 Enable Navigation support 
URHO3D_NAVIGATION – 1 启用导航支持

URHO3D_URHO2D 1 Enable 2D rendering & physics support 
URHO3D_URHO2D – 1 启用2D渲染和物理支持

URHO3D_SAMPLES 0 Build sample applications 
URHO3D_SAMPLES – 0 构建例子应用

URHO3D_TOOLS 1 Build standalone tools (Desktop and RPI only; on Android only build Lua standalone tools) 
URHO3D_TOOLS – 1 构建独立工具(仅桌面和RPI; Android仅构建独立的Lua工具)

URHO3D_EXTRAS 0 Build extras (Desktop and RPI only) 
URHO3D_EXTRAS – 0 构建附加设备(仅桌面和RPI)

URHO3D_DOCS 0 Generate documentation as part of normal build (the 'doc' builtin target can be used to generate documentation regardless of this option's value) 
URHO3D_DOCS – 0 作为正常构建的一部分生成文档(‘doc’内建目标可忽略这个选项值生成文档)

URHO3D_DOCS_QUIET 0 Generate documentation as part of normal build, suppress generation process from sending anything to stdout 
URHO3D_DOCS_QUIET – 0 作为正常构建的一部分生成文档, 禁止生成过程中的信息显示在标准输出

URHO3D_SSE 1 Enable SSE instruction set 
URHO3D_SSE – 1 启用SSE指令集

URHO3D_MINIDUMPS 1 Enable minidumps on crash (VS only) 
URHO3D_MINIDUMPS – 1 启用崩溃时小型转储(仅VS)

URHO3D_FILEWATCHER 1 Enable filewatcher support 
URHO3D_FILEWATCHER – 1 启用文件监视支持

URHO3D_PROFILING 1 Enable profiling support 
URHO3D_PROFILING – 1 启用性能分析支持

URHO3D_LOGGING 1 Enable logging support 
URHO3D_LOGGING – 1 启用日志支持

URHO3D_TESTING 0 Enable testing support 
URHO3D_TESTING – 1 启用测试支持

URHO3D_TEST_TIME_OUT 5 Number of seconds to test run the executables (when testing support is enabled only) 
URHO3D_TEST_TIME_OUT – 5 测试可执行文件运行的秒数(仅当启用测试支持)

URHO3D_OPENGL 0 Use OpenGL instead of Direct3D (Windows platform only) 
URHO3D_OPENGL – 0 用OpenGL代替Direct3D(仅Windows平台)

URHO3D_MKLINK 0 Use mklink command to create symbolic links (Windows Vista and above only) 
URHO3D_MKLINK – 0 使用mklink命令来创建符号链接(仅Windows Vista及以上版本)

URHO3D_STATIC_RUNTIME 0 Use static C/C++ runtime libraries and eliminate the need for runtime DLLs installation (VS only) 
URHO3D_STATIC_RUNTIME – 0 使用静态C/C++运行库并且排除需要运行时DLL的部件(仅VS)

URHO3D_LIB_TYPE * Specify Urho3D library type, possible values are STATIC (default) and SHARED 
URHO3D_LIB_TYPE * - 指定Urho3D库类型, 可能的取值是STATIC(默认)和SHARED

URHO3D_SCP_TO_TARGET - Use scp to transfer executables to target system (non-Android cross-compiling build only), SSH digital key must be setup first for this to work, typical value has a pattern of usr:remote-loc 
URHO3D_SCP_TO_TARGET – 使用scp传输可执行文件到目标系统(仅交叉编译构建非Android平台), 在次工作之前必须先安装SSH数字钥, 有一个典型的取值模式 usr:remote-loc

URHO3D_NDK_GDB 0 Enable ndk-gdb for debugging (Android build only) 
URHO3D_NDK_GDB – 0 启用ndk-gdb调试器(仅Android构建)

CMAKE_BUILD_TYPE * Specify CMake build configuration to be generated (Makefile generator only), possible values are Release (default), Debug, and RelWithDebInfo
CMAKE_BUILD_TYPE * - 指定CMake构建生成配置(仅Makefile生成), 可能的取值是Release(默认), Debug和RelWithDebInfo

CMAKE_OSX_DEPLOYMENT_TARGET - Specify Mac OS X deployment target (OSX build only); default to current running OS X if not specified 
CMAKE_OSX_DEPLOYMENT_TARGET – 指定Mac OS X部署目标(仅OSX构建); 如果没有指定默认是当前运行的OS X.

IPHONEOS_DEPLOYMENT_TARGET - Specify iPhone OS deployment target (iOS build only); default to latest installed iOS SDK if not specified 
IPHONEOS_DEPLOYMENT_TARGET – 指定iPhone OS部署目标(仅iOS构建); 如果没有指定默认是安装的最低版本iOS SDK.

ANDROID_ABI * Specify target ABI (Android build only), possible values are armeabi, armeabi-v7a (*default), armeabi-v7a with NEON, armeabi-v7a with VFPV3, armeabi-v6 with VFP, arm64-v8a, x86, and x86_64 
ANDROID_ABI * - 指定目标ABI(仅Android构建), 可能的取值是armeabi, armeabi-v7a (默认), armeabi-v7a(NEON), armeabi-v7a(VFPV3), armeabi-v6(VFP), arm64-v8a, x86 和 x86_64

Note that build option values specified via command line are cached by CMake. The cached values will be used by CMake in the subsequent invocation. That is, the same build options are not required to be specified again and again. Once a non-default build option value is being cached, it can only be reverted back to its default value by explicitly setting it via command line. That is, simply by NOT passing the corresponding build option would not work. One way to revert all the build options to their default values is by clearing the CMake cache by calling cmake_clean.bat or cmake_clean.sh.

注意, 通过命令行指定的构建参数会被CMake缓存. 这些缓存的值会被后来的调用使用. 这就是说, 相同的构建参数不需要反复的指定.一旦一个非默认的构建参数值被缓存, 只能通过在命令行明确设定才能恢复默认值. 这就是说, 简单的不传递相应的构建选项将无法工作. 一个恢复所有构建选项默认值的方法是使用cmake_clean.bat或者cmake_clean.sh清除CMake缓存.


Using cmake-gui to configure and generate Urho3D project file Instead of using one of the provided batch files or shell scripts, you can use cmake-gui to configure and generate Urho3D project file. However, you have to adhere to the current limitations:

使用cmake-gui代替批处理文件或者shell脚本来配置和生成Urho3D项目文件, 你可以使用cmake-gui来配置和生成Urho3D项目文件. 然而, 你必须遵守这些限制条件:


The build directory must be located inside the Urho3D project root directory and sibling of the "Source" directory. The build directory must be named accordingly based on the target platform. On desktop/native platform: Build On Windows platform using MinGW: mingw-Build On iOS platform: ios-Build On Android platform: android-Build On Raspberry Pi platform: raspi-Build The runtime and archive output directories are automatically set based on the target platform regardless of the build directory name (should you choose not to adhere with the above). For example on Android platform, they will be android-Bin and android-Lib, respectively. If you choose not to adhere with this build directory naming convention then you will have to install the Urho3D library into your local filesystem in order to use the Urho3D library in your external project. See Library build and From Urho3D SDK installation. All the post-CMake workaround and/or bug fixes that are scripted in the batch files or shell scripts are not applied to the generated project file. Steps to configure:

构建目录必须位于Urho3D项目根之内并且和"Source"在同一目录. 构建目录必须依据目标平台的名字来命名.桌面和原生平台: 
在Windows的MinGW上构建: mingw-Build
在iOS平台上构建: ios-Build
在Android平台上构建: android-Build
在Raspberry Pi平台上构建: raspi-Build
运行时和文档输出目录根据目标平台构建目录名自动设置(你选择不坚持上面的). 例如在Android平台, 它们分别是android-Bin和android-Lib. 如果你选择不坚持上面的目录命名约定, 那么为了在你的外部项目中使用Urho3D,你必须将Urho3D库安装在你本地文件系统. 参见库的构建和从Urho3D SDK安装. 所有CMake后的工作区和/或批处理文件脚本或shell脚本的bug修复不适用于已经创建的项目文件.配置步骤:


In the Urho3D project root directory, invoke "cmake-gui Source". Set the build directory name. Configure and update the build options as many times as necessary until there are no more new options in red. For the first configuration, choose the generator you like to use. Click the Group check box to group the build options. In the Ungrouped Entries: check IOS option when targeting it on Xcode. In the URHO3D group: check any of the options you desire. Some of the options when checked may cause new options to be made available in the subsequent configuration loop. Generate when all the configurations are done.

在Urho3D项目根目录, 调用"cmake-gui Source". 设置构建目录名. 如果必要,配置和更新构建选项直到没有选项显示红色. 第一次配置, 选择你喜欢使用的生成器. 点击复选框选择构建选项. 未分组的条目: 当目标是Xcode选择IOS. URHO3D复选框: 选择所有你希望的选项. 有一些选项当选中的时候可能引出新的选项来激活随后的配置, 如此循环. 所有配置完成后执行生成.


