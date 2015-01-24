Resources include most things in Urho3D that are loaded from mass storage during initialization or runtime:
在Urho3D中，资源是指可以在初始化或者运行过程中加载的对象，包括如下类型：

Animation (动画)
Image (图像)
Model (模型)
Material (材质)
ParticleEffect (粒子特效)
ScriptFile (脚本文件)
Shader
Sound (声音)
Technique
Texture2D (2D纹理)
Texture3D (3d纹理)
TextureCube (立方纹理)
XMLFile (XML文件)
They are managed and loaded by the ResourceCache subsystem. Like with all other typed objects, resource types are identified by 32-bit type name hashes (C++) or type names (script). An object factory must be registered for each resource type.

The resources themselves are identified by their file paths, relative to the registered resource directories or package files. By default, the engine registers the resource directories Data and CoreData, or the packages Data.pak and CoreData.pak if they exist.

If loading a resource fails, an error will be logged and a null pointer is returned.

Typical C++ example of requesting a resource from the cache, in this case, a texture for a UI element. Note the use of a convenience template argument to specify the resource type, instead of using the type hash.

healthBar->SetTexture(GetSubsystem<ResourceCache>()->GetResource<Texture2D>("Textures/HealthBarBorder.png"));
The same in script would look like this (note the use of a property instead of a setter function):

healthBar.texture = cache.GetResource("Texture2D", "Textures/HealthBarBorder.png");
Resources can also be created manually and stored to the resource cache as if they had been loaded from disk.

Memory budgets can be set per resource type: if resources consume more memory than allowed, the oldest resources will be removed from the cache if not in use anymore. By default the memory budgets are set to unlimited.

Background loading of resources
Normally, when requesting resources using GetResource(), they are loaded immediately in the main thread, which may take several milliseconds for all the required steps (load file from disk, parse data, upload to GPU if necessary) and can therefore result in framerate drops.

If you know in advance what resources you need, you can request them to be loaded in a background thread by calling BackgroundLoadResource(). The event E_RESOURCEBACKGROUNDLOADED will be sent after the loading is complete; it will tell if the loading actually was a success or a failure. Depending on the resource, only a part of the loading process may be moved to a background thread, for example the finishing GPU upload step always needs to happen in the main thread. Note that if you call GetResource() for a resource that is queued for background loading, the main thread will stall until its loading is complete.

The asynchronous scene loading functionality LoadAsync() and LoadAsyncXML() has the option to background load the resources first before proceeding to load the scene content. It can also be used to only load the resources without modifying the scene, by specifying the LOAD_RESOURCES_ONLY mode. This allows to prepare a scene or object prefab file for fast instantiation.

Finally the maximum time (in milliseconds) spent each frame on finishing background loaded resources can be configured, see SetFinishBackgroundResourcesMs().

Implementing background loading
When writing new resource types, the background loading mechanism requires implementing two functions: BeginLoad() and EndLoad(). BeginLoad() is potentially called in a background thread and should do as much work (such as file I/O) as possible without violating the multithreading rules. EndLoad() should perform the main thread finishing step, such as GPU upload. Either step can return false to indicate failure to load the resource.

If a resource depends on other resources, writing efficient threaded loading for it can be hard, as calling GetResource() is not allowed inside BeginLoad() when background loading. There are a few options: it is allowed to queue new background load requests by calling BackgroundLoadResource() within BeginLoad(), or if the needed resource does not need to be permanently stored in the cache and is safe to load outside the main thread (for example Image or XMLFile, which do not possess any GPU-side data), GetTempResource() can be called inside BeginLoad.
