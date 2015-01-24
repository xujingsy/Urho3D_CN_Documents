Scripting
To enable AngelScript scripting support, the Script subsystem needs to be created and registered after initializing the Engine. This is accomplished by the following code, seen eg. in Tools/Urho3DPlayer/Urho3DPlayer.cpp:

context_->RegisterSubsystem(new Script(context_));
There are three ways the AngelScript language can be interacted with in Urho3D:

Immediate execution
Immediate execution takes one line of AngelScript, compiles it, and executes. This is not recommended for anything that needs high performance, but can be used for example to implement a developer console. Call the Script subsystem's Execute() function to use. For example:

GetSubsystem<Script>()->Execute("Print(\"Hello World!\");");
It may be useful to be able to access a specific scene or a script file while executing immediate script code. These can be set on the Script subsystem by calling SetDefaultScene() and SetDefaultScriptFile().

Calling a function from a script file
This requires a successfully loaded ScriptFile resource, whose Execute() function will be used. To identify the function to be called, its full declaration is needed. Parameters are passed in a VariantVector. For example:

ScriptFile* file = GetSubsystem<ResourceCache>()->GetResource<ScriptFile>("Scripts/MyScript.as");
VariantVector parameters;
parameters.Push(Variant(100)); // Add an int parameter
file->Execute("void MyFunction(int)", parameters); // Execute
Execute() also has an overload which takes a function pointer instead of querying by declaration. Using a pointer is naturally faster than a query, but also more risky: in case the ScriptFile resource is unloaded or reloaded, any function pointers will be invalidated.

Instantiating a script object
The component ScriptInstance can be used to instantiate a specific class from within a script file. After instantiation, the the script object can respond to scene updates, events and serialization much like a component written in C++ would do, if it has the appropriate methods implemented. For example:

ScriptInstance* instance = node->CreateComponent<ScriptInstance>();
instance->CreateObject(GetSubsystem<ResourceCache>()->GetResource<ScriptFile>("Scripts/MyClass.as"), "MyClass");
The class must implement the empty interface ScriptObject to make its base class statically known. This enables accessing any script object in the scene using ScriptInstance's GetScriptObject() function.

The following methods that implement the component behaviour will be checked for. None of them are required.

void Start()
void Stop()
void DelayedStart()
void Update(float)
void PostUpdate(float)
void FixedUpdate(float)
void FixedPostUpdate(float)
void Save(Serializer&)
void Load(Deserializer&)
void WriteNetworkUpdate(Serializer&)
void ReadNetworkUpdate(Deserializer&)
void ApplyAttributes()
void TransformChanged()
The update methods above correspond to the variable timestep scene update and post-update, and the fixed timestep physics world update and post-update. The application-wide update events are not handled by default.

The Start() and Stop() methods do not have direct counterparts in C++ components. Start() is called just after the script object has been created. Stop() is called just before the script object is destroyed. This happens when the ScriptInstance is destroyed, or if the script class is changed.

When a scene node hierarchy with script objects is instantiated (such as when loading a scene) any child nodes may not have been created yet when Start() is executed, and can thus not be relied upon for initialization. The DelayedStart() method can be used in this case instead: if defined, it is called immediately before any of the Update() calls.

TransformChanged() is called whenever the scene node transform changes, similar to C++ components' OnMarkedDirty() function.

Subscribing to events in script behaves differently depending on whether SubscribeToEvent() is called from a script object's method, or from a procedural script function. If called from an instantiated script object, the ScriptInstance becomes the event receiver on the C++ side, and calls the specified handler method when the event arrives. If called from a function, the ScriptFile will be the event receiver and the handler must be a free function in the same script file. The third case is if the event is subscribed to from a script object that does not belong to a ScriptInstance. In that case the ScriptFile will create a proxy C++ object on demand to be able to forward the event to the script object.

The script object's enabled state can be controlled through the SetEnabled() function. When disabled, the scripted update methods or event handlers will not be called. This can be used to reduce CPU load in a large or densely populated scene.

There are shortcut methods on the script side for creating and accessing a node's script object: node.CreateScriptObject() and node.GetScriptObject(). Alternatively, if the node has only one ScriptInstance, and a specific class is not needed, the node's scriptObject property can also be used. CreateScriptObject() takes the script file name (or alternatively, a ScriptFile object handle) and class name as parameters and creates a ScriptInstance component automatically, then creates the script object. For example:

ScriptObject@ object = node.CreateScriptObject("Scripts/MyClass.as", "MyClass");
Note that these are not actual Node member functions on the C++ side, as the Scene classes are not allowed to depend on scripting.

Script object serialization
After instantiation, the script object's public member variables that can be converted into Variant, and that don't begin with an underscore are automatically available as attributes of the ScriptInstance, and will be serialized. Node and Component handles are also converted into nodeID and componentID attributes automatically. Note: this automatic attribute mechanism means that a ScriptInstance's attribute list changes dynamically depending on the class that has been instantiated.

If the script object contains more complex data structures, you can also serialize and deserialize into a binary buffer manually by implementing the Load() and Save() methods.

Network replication of the script object variables must be handled manually by implementing WriteNetworkUpdate() and ReadNetworkUpdate() methods, that also write and read a binary buffer. These methods should write/read all replicated of variables of the object. Additionally, the ScriptInstance must be marked for network replication by calling MarkNetworkUpdate() whenever the replicated data changes. Because this replication mechanism can not sync per variable, but always sends the whole binary buffer if even one bit of the data changes, also consider using the automatically replicated node user variables.

Delayed method calls
Delayed method calls can be used in script objects to implement time-delayed actions. Use the DelayedExecute() function in script object code to add a method to be executed later. The parameters are the delay in seconds, repeat flag, the full declaration of the function, and optionally parameters, which must be placed in a Variant array. For example:

class Test : ScriptObject
{
    void Start()
    {
        Array<Variant> parameters;
        parameters.Push(Variant(100));
        DelayedExecute(1.0, false, "void Trigger(int)", parameters);
    }
    void Trigger(int parameter)
    {
        Print("Delayed function triggered with parameter " + parameter);
    }
}
Delayed method calls can be removed by full declaration using the ClearDelayedExecute() function. If an empty declaration (default) is given as parameter, all delayed calls are removed.

When a scene is saved/loaded, any pending delayed calls are also saved and restored properly.

The script API
Much of the Urho3D classes are exposed to scripts, however things that require low-level access or high performance (like direct vertex buffer access) are not. Also for scripting convenience some things have been changed from the C++ API:

The template array and string classes are exposed as Array<type> and String.
Public member variables are exposed without the underscore appended. For example x, y, z in Vector3.
Whenever only a single parameter is needed, setter and getter functions are replaced with properties. Such properties start with a lowercase letter. If an index parameter is needed, the property will be indexed. Indexed properties are in plural.
The element count property of arrays and other dynamic structures such as VariantMap and ResourceRefList is called "length", though the corresponding C++ function is usually Size().
Subsystems exist as global properties: time, fileSystem, log, cache, network, input, ui, audio, engine, graphics, renderer, script, console, debugHud.
Additional global properties exist for accessing the script object's node, the scene and the scene-wide components: node, scene, octree, physicsWorld, debugRenderer. When an object method is not executing, these are null. An exception: when the default scene for immediate execution has been set by calling SetDefaultScene(), it is always available as "scene".
The currently executing script object's ScriptInstance component is available through the global property self.
The currently executing script file is available through the global property scriptFile.
The first script object created to a node is available as its scriptObject property.
Printing raw output to the log is simply called Print(). The rest of the logging functions are accessed by calling log.Debug(), log.Info(), log.Warning() and log.Error().
Functions that would take a StringHash parameter usually take a string instead. For example sending events, requesting resources and accessing components.
Most of StringUtils have been exposed as methods of the string class. For example String.ToBool().
Template functions for getting components or resources by type are not supported. Instead automatic type casts are performed as necessary.
Check the automatically built Scripting API documentation for the exact function signatures. Note that the API documentation can be regenerated to the Urho3D log file by calling DumpAPI() function on the Script subsystem or by using ScriptCompiler tool.

Precompiling scripts to bytecode
Instead of compiling scripts from source on-the-fly during startup, they can also be precompiled to bytecode, then loaded. Use the ScriptCompiler utility for this. In this case the resource request has to be pointed to the compiled file, which by default has the .asc extension:

ScriptFile* file = GetSubsystem<ResourceCache>()->GetResource<ScriptFile>("Scripts/MyScript.asc");
Limitations
There are some complexities of the scripting system one has to watch out for:

During the execution of the script object's constructor, the object is not yet associated with the ScriptInstance, and therefore subscribing to events, adding delayed method calls, or trying to access the node or scene will fail. The use of the constructor is best reserved for initializing member variables only.
When the resource request for a particular ScriptFile is initially made, the script file and the files it includes are compiled into an AngelScript script module. Each script module has its own class hierarchy that is not usable from other script modules, unless the classes are declared shared. See AngelScript documentation for more details.
If a ScriptFile resource is reloaded, all the script objects created from it will be destroyed, then recreated. They will lose any stored state as their constructors and Start() methods will be run again. This is rarely useful when running an actual game, but may be helpful during development.
AngelScript modifications
The following changes have been made to AngelScript in Urho3D:

For performance reasons and to guarantee immediate removal of expired objects, AngelScript garbage collection has been disabled for script classes and the Array type. This has the downside that circular references will not be detected. Therefore, whenever you have object handles in your script, think of them as if they were C++ shared pointers and avoid creating circular references with them. For safety, consider using the value type WeakHandle, which is a WeakPtr<RefCounted> exposed to script and can be used to point to any engine object (but not to script objects.) An example of using WeakHandle:
WeakHandle rigidBodyWeak = node.CreateComponent("RigidBody");
RigidBody@ rigidBodyShared = rigidBodyWeak.Get(); // Is null if expired
Object handle assignment can be done without the @ symbol if the object in question does not support value assignment. All exposed Urho3D C++ classes that derive from RefCounted never support value assignment. For example, when assigning the Model and Material of a StaticModel component:
object.model = cache.GetResource("Model", "Models/Mushroom.mdl");
object.material = cache.GetResource("Material", "Materials/Mushroom.xml");
In unmodified AngelScript, this would have to be written as:

@object.model = cache.GetResource("Model", "Models/Mushroom.mdl");
@object.material = cache.GetResource("Material", "Materials/Mushroom.xml");