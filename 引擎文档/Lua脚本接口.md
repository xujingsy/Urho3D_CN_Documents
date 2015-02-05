# Lua scripting

在 Urho3D 中 Lua脚本使用 LuaScript 子系统，要使用Lua脚本，必须先初始化LuaScript子系统。在默认的cmake构建选项中，Lua支持是默认不打开的，要启用Lua的支持，需要在cmake的命令中添加 -DURHO3D_LUA=1选项。更多详情，请参考构建选项。可以用下面的代码初始化LuaScript子系统：

```
context_->RegisterSubsystem(new LuaScript(context_));
```

Lua scripting in Urho3D has its dedicated LuaScript subsystem that must be instantiated before the scripting capabilities can be used. Lua support is not compiled in by default but must be enabled by the CMake build option -DURHO3D_LUA=1. For more details see Build options. Instantiating the subsystem is done like this:

```
context_->RegisterSubsystem(new LuaScript(context_));
```

Lua脚本支持立即编译并执行单行脚本代码，也可以加载一个脚本文件执行过程函数以及使用LuaScriptInstance组件来实例化脚本对象，这和AngelScript类似。

Like AngelScript, Lua scripting supports immediate compiling and execution of single script lines, loading script files and executing procedural functions from them, and instantiating script objects to scene nodes using the LuaScriptInstance component.

##　立即执行
使用ExecuteString来编并执行Lua脚本，如果程序对性能有要求，不建议这么做。

##　Immediate execution
Use ExecuteString() to compile and run a line of Lua script. This should not be used for performance-critical operations.

## 脚本文件和函数
在Lua中，所有被加载的函数和变量都所用同一个Lua State，这意味着你可以在Lua脚本的任何地方访问已经加载的函数和变量。这一点和AngelScript是有所区别的（在AngelScript中，所有的函数和变量是不共享的，除非标记了共享）。通过调用ExecuteFile函数来执行脚本文件。

## Script files and functions
In contrast to AngelScript modules, which exist as separate entities and do not share functions or variables unless explicitly marked shared, in the Lua subsystem everything is loaded and executed in one Lua state, so scripts can naturally access everything loaded so far. To load and execute a Lua script file, call ExecuteFile().

当脚本执行之后，可以用使用GetFunction()函数，指定函数的名称来得到某个函数，该函数返回一个LuaFunction对象，要调用该函数，先调用BeginCall()，然后使用PushXXX()传入参数，最后调用EndCall()来完成一次调用。
After that, the functions in the script file are available for calling. Use GetFunction() to get a Lua function by name. This returns a LuaFunction object, on which you should call BeginCall() first, followed by pushing the function parameters if any, and finally execute the function with EndCall().

## 脚本对象
可以通过LuaScriptInstance组件，将一个Lua脚本对象添加到场景节点。在这个组件创建之后，有两种方式来实例化脚本对象：一是指定脚本文件名称和类名称（这种方式会先加载并执行脚本）另一种方式是仅仅使用类名称（这种情况下要确保类定义已经加载并执行过）。在LuaIntegration例子中展示了如何通过C++代码来实例化Rotator脚本对象，该脚本对象在Rotator.lua文件中定义。

```
LuaScriptInstance* instance = node->CreateComponent<LuaScriptInstance>();
instance->CreateObject("LuaScripts/Rotator.lua", "Rotator");
```
## Script objects
By using the LuaScriptInstance component, Lua script objects can be added to scene nodes. After the component has been created, there are two ways to specify the object to instantiate: either specifying both the script file name and the object class name, in which case the script file is loaded and executed first, or specifying only the class name, in which case the Lua code containing the class definition must already have been executed. An example of creating a script object in C++ from the LuaIntegration sample, where a class called Rotator is instantiated from the script file Rotator.lua:

```
LuaScriptInstance* instance = node->CreateComponent<LuaScriptInstance>();
instance->CreateObject("LuaScripts/Rotator.lua", "Rotator");
```

在脚本对象实例化后，可以是用GetScriptObjectFunction()来得到对象的函数，使用上面讲述的方式调用。
After instantiation, use GetScriptObjectFunction() to get the object's functions by name; calling happens like above.

在脚本对象中可以定义一些函数，这些函数可以被LuaScriptInstance自动调用，例如初始化、 场景更新、保存加载等。下面列举出这些函数，这和AngelScript类似，更多详情可以参考AngelScript脚本。
Like their AngelScript counterparts, script object classes can define functions which are automatically called by LuaScriptInstance for operations like initialization, scene update, or load/save. These functions are listed below. Refer to the AngelScript scripting page for details.

- Start()
- Stop()
- Update(timeStep)
- PostUpdate(timeStep)
- FixedUpdate(timeStep)
- FixedPostUpdate(timeStep)
- Save(serializer)
- Load(deserializer)
- WriteNetworkUpdate(serializer)
- ReadNetworkUpdate(deserializer)
- ApplyAttributes()
- TransformChanged()

## 事件处理

## Event handling
Like in AngelScript, both procedural and object event handling is supported. In procedural event handling the LuaScript subsystem acts as the event receiver on the C++ side, and forwards the event to a Lua function. Use SubscribeToEvent and give the event name and the function to use as the handler. Optionally a specific sender object can be given as the first argument instead. For example, subscribing to the application-wide Update event, and getting its timestep parameter in the event handler function.

SubscribeToEvent("Update", "HandleUpdate")
...
function HandleUpdate(eventType, eventData)
    local timeStep = eventData:GetFloat("TimeStep")
    ...
end
When subscribing a script object to receive an event, use the form self:SubscribeToEvent instead. The function to use as the handler is given as "ClassName:FunctionName". For example subscribing to the NodeCollision physics event, and getting the participating other scene node and the contact point VectorBuffer in the handler function. Note that in Lua retrieving an object pointer from a VariantMap requires the object type as the first parameter:

CollisionDetector = ScriptObject()
function CollisionDetector:Start()
    self:SubscribeToEvent(self.node, "NodeCollision", "CollisionDetector:HandleNodeCollision")
end
function CollisionDetector:HandleNodeCollision(eventType, eventData)
    local otherNode = eventData:GetPtr("Node", "OtherNode")
    local contacts = eventData:GetBuffer("Contacts")
    ...
end
The script API
The binding of Urho3D C++ classes is accomplished with the tolua++ library, which for the most part binds the exact same function parameters as C++. Compared to the AngelScript API, you will always have the classes' Get / Set functions available, but in addition convenience properties also exist.

As seen above from the event handling examples, VariantMap handling has some differences to both C++ and AngelScript. To get a value, supply its key name as a string. To get a pointer to an object, supply first the object type, then the key name.

For the rest of the functions and classes, see the generated Lua script API reference. Also, look at the Lua counterparts of the sample applications in the Bin/Data/LuaScripts directory and compare them to the C++ and AngelScript versions to familiarize yourself with how things are done on the Lua side.

Object allocation & Lua garbage collection
There are two ways to allocate a C++ object in Lua scripting, which behave differently with respect to Lua's automatic garbage collection:

1) Call object's contructor:

local scene = Scene()
tolua++ will register this C++ object with garbage collection, and Lua will collect it eventually. Do not use this form if you will add the object to an object hierarchy that is kept alive on the C++ side with SharedPtr's, for example child scene nodes or UI child elements. Otherwise the object will be double-deleted, resulting in a crash.

2) Call the new function:

local text = Text:new()
When using this form the object will not collected by Lua, so it is safe to pass into C++ object hierarchies. Otherwise, to prevent memory leaks it needs to be deleted manually by calling the delete function on it:

text:delete()
When you call the GetFile() function of ResourceCache from Lua, the file you receive must also be manually deleted like described above once you are done with it.