The Urho3D player application in the Bin directory contains all the engine runtime functionality. However, it does not contain any inbuilt logic, and therefore must be supplied with the name of the application script file it should run:

Bin目录的Urho3D player应用包含了所有的引擎运行时功能. 然而它没有包含任何内在逻辑, 因此必须提供应用脚本文件名来运行它.


Urho3DPlayer [options] The scripting language supported by default is AngelScript (http://www.angelcode.com/angelscript); the script files have .as extension and need to be placed under either the Bin/Data or Bin/CoreData subdirectories so that Urho3DPlayer can find them. An application script is required to have the function void Start(), which will be executed before starting the engine main loop. It is this function's responsibility to initialize the application and to hook up to any necessary events, such as the update that happens every frame.

Urho3DPlayer默认使用AngelScript(http://www.angelcode.com/angelscript) 脚本语言;  脚本文件使用.as 做扩展名并且必须放在Bin/Data或者Bin/CoreData子目录下, 以便Urho3DPlayer找到它们. 应用脚本需要包含函数 void Start() , 这个函数会在引擎主循环开始前被执行. 这个函数的责任是初始化应用和挂钩(hook)任何需要的事件, 比如每帧的update.


Lua language support can optionally be built in, see Build options.

内建Lua语言支持是可选的, 参见构建选项.


On Android and iOS the command line can not be entered, so it is instead read from the file Bin/Data/CommandLine.txt. By default the NinjaSnowWar example will be run.

在Android和iOS上, 不能进入命令行, 因此作为替代, 从Bin/Data/CommandLine.txt文件读取. 默认将运行NinjaSnowWar示例.


Command line options The engine can be configured using the following command line options.

# 命令行参数. 引擎可配置使用下面的命令行参数.

参数 | 描述
----|----
-x | Horizontal resolution
-x | 水平分辨率
-y | Vertical resolution
-y | 垂直分辨率
-m | Enable hardware multisampling
-m | 启用硬件多重采样
-v | Enable vertical sync
-v | 启用垂直同步
-t | Enable triple buffering 
-t | 启用三重缓冲
-w | Start in windowed mode 
-w | 以窗口模式启动
-s | Enable resizing when in windowed mode 
-s | 启用在窗口模式可改变窗口大小
-q | Enable quiet mode which does not log to standard output stream 
-q | 启用安静模式, 不向标准输出流写日志
-b | Sound buffer length in milliseconds 
-b | 声音缓冲区长度(毫秒)
-r | Sound mixing frequency in Hz 
-r | 声音混合频率(Hz)
-p | Resource path(s) to use, separated by semicolons 
-p | 资源路径, 多个之间用分号分隔
-ap | Autoload resource path(s) to use, separated by semicolons 
-ap | 自动载入资源的路径, 多个之间用分号分隔
-log | Change the log level, valid 'level' values are 'debug', 'info', 'warning', 'error' 
-log | 改变日志级别, 合法的级别(level)值是 'debug', 'info', 'warning', 'error'.
-ds | Dump used shader variations to a file for precaching 
-ds | 转储shader变量到预先高速缓存文件
-mq | Material quality level, default 2 (high) 
-mq | 材质质量水平, 默认是2(high).
-tq | Texture quality level, default 2 (high) 
-tq | 纹理质量水平, 默认是2(high).
-tf | Texture filter mode, default 2 (trilinear) 
-tf | 纹理过滤模式, 默认是2(trilinear)
-af | Texture anisotropy level, default 4. Also sets anisotropic filter mode 
-af | 纹理各向异性水平, 默认是4. 也是各向异性过滤模式.
-flushgpu | Flush GPU command queue each frame. Effective only on Direct3D9 
-flushgpu | 每帧刷新GPU命令队列. 仅在Direct3D9有效.
-borderless | Borderless window mode 
-borderless | 无边框的窗口模式
-headless | Headless mode. No application window will be created 
-headless | 无头模式. 不创建应用程序窗口.
-landscape | Use landscape orientations (iOS only, default) 
-landscape | 使用景观方向(仅iOS, 默认值)
-portrait | Use portrait orientations (iOS only) 
-portrait | 使用纵向方向(仅iOS)
-prepass | Use light pre-pass rendering 
-prepass | 使用预渲染灯光
-deferred | Use deferred rendering 
-deferred | 使用延迟渲染 
-lqshadows | Use low-quality (1-sample) shadow filtering 
-lqshadows | 使用低质量(1-sample)阴影过滤
-noshadows | Disable shadow rendering 
-noshadows | 禁用阴影渲染
-nolimit | Disable frame limiter 
-nolimit | 禁用帧数限制
-nothreads | Disable worker threads 
-nothreads | 禁用工作线程
-nosound | Disable sound output 
-nosound | 禁用声音输出
-noip | Disable sound mixing interpolation 
-noip | 禁用声音混合插值
-sm2 | Force SM2.0 rendering 
-sm2 | 强制SM2.0渲染
-touch | Touch emulation on desktop platform 
-touch | 在桌面平台仿真触摸


Mac OS X specific - How to view/edit AngelScript within Xcode By default Mac OS X recognizes file having extension .as as 'AppleSingle Archive'. So, even after associating this file type to always open with Xcode, Xcode is still not able to view/edit the content of the file correctly. In order to view/edit the scripts, after launching the Urho3D project in Xcode, select the .as file(s) in the Project Navigator and then in the File Inspector (right panel) change the file type from 'Default - AppleSingle archive' to 'C++ Source' in the File Type drop down list. The current editor view usually does not refresh its content after this change. Selecting another file in the Project Navigator then reselecting the .as file should force the editor to reload and show the .as file correctly afterwards.

Mac OS X 特有 – 怎样在Xcode中查看/编辑AngelScript. 默认的Mac OS X将具有扩展名.as的文件识别为'AppleSingle Archive'. 所以, 即使将这种文件关联为始终用Xcode打开, Xcode还是不能正确的查看/编辑文件内容. 为了在Xcode中启动 Urho3D项目后能够查看/编辑脚本, 在Project Navigator中选中 .as 文件然后在File Inspector(右边的面板)中的文件类型下拉列表中把文件类型从'Default - AppleSingle archive'改成'C++ Source'. 修改之后, 当前编辑器查看通常不刷新内容. 在Project Navigator选中另外一个.as文件然后重新选择要强制编辑器之后要正确重新载入显示的 .as文件. 


The drawback of the above approach is, Xcode does not remember it. The steps need to be carried out each time Xcode is relaunched.

上述方法的缺点是, Xcode不会记住它.  这个步骤需要每次Xcode重新开启都要进行一次.


To solve this permanently, we need to 'hack' the system a little bit to 'fool' Xcode to always treat .as file as one of the C++ source files. Execute the following commands in a terminal as normal user. These commands have been verified to work with Xcode 4.x on Lion and Xcode 5.x on Mountain Lion.

要永久解决这个问题, 我们需要对这个系统弄一点”黑客”技术来”愚弄”Xcode,让它将.as文件当作一个C++源文件处理.以普通用户在终端执行下面的命令. 这些命令在Lion的Xcode 4.x和Mountain Lion的Xcode 5.x 通过验证.


 $ cd /System/Library/CoreServices/CoreTypes.bundle/Contents 
 
 $ plutil -convert xml1 Info.plist -o /tmp/Info.plist.xml 
 
 $ sed -i.bak "s/cxx<\/string>/cxx<\/string>\echo -e '\n\r'as<\/string>/g" /tmp/Info.plist.xml 
 
 $ sudo cp -p Info.plist{,.ori} 
 
 $ sudo plutil -convert binary1 /tmp/Info.plist.xml -o Info.plist 
 
 $ find /System/Library/Frameworks -type f -name lsregister -exec {} -kill -r -domain local -domain system -domain user -domain network \; 

(上面是指令列表, 不做翻译)


The last command resets the launch service database and rebuilds it, so the changes should take effect immediately when Xcode restarts.

最后一条命令重置启动服务数据库并重建它, 因此当Xcode重启后这些改变会立刻生效.



