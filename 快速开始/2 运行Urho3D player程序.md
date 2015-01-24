The Urho3D player application in the Bin directory contains all the engine runtime functionality. However, it does not contain any inbuilt logic, and therefore must be supplied with the name of the application script file it should run:

Urho3DPlayer <scriptfilename> [options]
The scripting language supported by default is AngelScript (http://www.angelcode.com/angelscript); the script files have .as extension and need to be placed under either the Bin/Data or Bin/CoreData subdirectories so that Urho3DPlayer can find them. An application script is required to have the function void Start(), which will be executed before starting the engine main loop. It is this function's responsibility to initialize the application and to hook up to any necessary events, such as the update that happens every frame.

Lua language support can optionally be built in, see Build options.

On Android and iOS the command line can not be entered, so it is instead read from the file Bin/Data/CommandLine.txt. By default the NinjaSnowWar example will be run.

Command line options
The engine can be configured using the following command line options.

-x <res>     Horizontal resolution
-y <res>     Vertical resolution
-m <level>   Enable hardware multisampling
-v           Enable vertical sync
-t           Enable triple buffering
-w           Start in windowed mode
-s           Enable resizing when in windowed mode
-q           Enable quiet mode which does not log to standard output stream
-b <length>  Sound buffer length in milliseconds
-r <freq>    Sound mixing frequency in Hz
-p <paths>   Resource path(s) to use, separated by semicolons
-ap <paths>  Autoload resource path(s) to use, separated by semicolons
-log <level> Change the log level, valid 'level' values are 'debug', 'info', 'warning', 'error'
-ds <file>   Dump used shader variations to a file for precaching
-mq <level>  Material quality level, default 2 (high)
-tq <level>  Texture quality level, default 2 (high)
-tf <level>  Texture filter mode, default 2 (trilinear)
-af <level>  Texture anisotropy level, default 4. Also sets anisotropic filter mode
-flushgpu    Flush GPU command queue each frame. Effective only on Direct3D9
-borderless  Borderless window mode
-headless    Headless mode. No application window will be created
-landscape   Use landscape orientations (iOS only, default)
-portrait    Use portrait orientations (iOS only)
-prepass     Use light pre-pass rendering
-deferred    Use deferred rendering
-lqshadows   Use low-quality (1-sample) shadow filtering
-noshadows   Disable shadow rendering
-nolimit     Disable frame limiter
-nothreads   Disable worker threads
-nosound     Disable sound output
-noip        Disable sound mixing interpolation
-sm2         Force SM2.0 rendering
-touch       Touch emulation on desktop platform
Mac OS X specific - How to view/edit AngelScript within Xcode
By default Mac OS X recognizes file having extension .as as 'AppleSingle Archive'. So, even after associating this file type to always open with Xcode, Xcode is still not able to view/edit the content of the file correctly. In order to view/edit the scripts, after launching the Urho3D project in Xcode, select the .as file(s) in the Project Navigator and then in the File Inspector (right panel) change the file type from 'Default - AppleSingle archive' to 'C++ Source' in the File Type drop down list. The current editor view usually does not refresh its content after this change. Selecting another file in the Project Navigator then reselecting the .as file should force the editor to reload and show the .as file correctly afterwards.

The drawback of the above approach is, Xcode does not remember it. The steps need to be carried out each time Xcode is relaunched.

To solve this permanently, we need to 'hack' the system a little bit to 'fool' Xcode to always treat .as file as one of the C++ source files. Execute the following commands in a terminal as normal user. These commands have been verified to work with Xcode 4.x on Lion and Xcode 5.x on Mountain Lion.

$ cd /System/Library/CoreServices/CoreTypes.bundle/Contents
$ plutil -convert xml1 Info.plist -o /tmp/Info.plist.xml
$ sed -i.bak "s/<string>cxx<\/string>/<string>cxx<\/string>\\`echo -e '\n\r'`<string>as<\/string>/g" /tmp/Info.plist.xml
$ sudo cp -p Info.plist{,.ori}
$ sudo plutil -convert binary1 /tmp/Info.plist.xml -o Info.plist
$ find /System/Library/Frameworks -type f -name lsregister -exec {} -kill -r -domain local -domain system -domain user -domain network \;
The last command resets the launch service database and rebuilds it, so the changes should take effect immediately when Xcode restarts.
