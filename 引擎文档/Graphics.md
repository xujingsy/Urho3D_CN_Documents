Much of the rendering functionality in Urho3D is built on two subsystems, Graphics and Renderer.

Graphics
Graphics implements the low-level functionality:

Creating the window and the rendering context
Setting the screen mode
Keeping track of GPU resources
Keeping track of rendering context state (current rendertarget, vertex and index buffers, textures, shaders and renderstates)
Loading shaders
Performing primitive rendering operations
Handling lost device
Screen resolution, fullscreen/windowed, vertical sync and hardware multisampling level are all set at once by calling Graphics's SetMode() function. There is also an experimental option of rendering to an existing window by passing its OS-specific handle to SetExternalWindow() before setting the initial screen mode.

When setting the initial screen mode, Graphics does a few checks:

For Direct3D9, the supported shader model is checked. 2 is minimum, but 3 will be used if available. For testing, SM2 can be forced by calling SetForceSM2() before setting the initial screen mode.
For OpenGL, version 2.0 with EXT_framebuffer_object, EXT_packed_depth_stencil and EXT_texture_filter_anisotropic extensions is checked for.
Is hardware instancing supported? This requires Shader Model 3 on Direct3D9 and the ARB_instanced_arrays extension on OpenGL.
Are hardware shadow maps supported? Both AMD & NVIDIA style shadow maps can be used. If neither are available, no shadows will be rendered.
Are light pre-pass and deferred rendering modes supported? These require sufficient multiple rendertarget support, and R32F texture format support.
Shader model 2 has the following limitations due to limited pixel shader instruction count:

Directional light shadows support a maximum of 3 cascade splits instead of 4.
Shadowed point lights do not support specular calculations in forward rendering.
Complex materials do not combine the ambient pass with the first forward light (lit base pass optimization.)
Complex forward rendered materials (eg. normal + specular map + alpha masking) combined with shadow mapping or height fog may not work.
Renderer
Renderer implements the actual rendering of 3D views each frame, and controls global settings such as texture quality, material quality, specular lighting and shadow map base resolution.

To render, it needs a Scene with an Octree component, and a Camera that does not necessarily have to belong to the scene. The octree stores all visible components (derived from Drawable) to allow querying for them in an accelerated manner. The needed information is collected in a Viewport object, which can be assigned with Renderer's SetViewport() function.

By default there is one viewport, but the amount can be increased with the function SetNumViewports(). The viewport(s) should cover the entire screen or otherwise hall-of-mirrors artifacts may occur. By specifying a zero screen rectangle the whole window will be used automatically. The viewports will be rendered in ascending order, so if you want for example to have a small overlay window on top of the main viewport, use viewport index 0 for the main view, and 1 for the overlay.

Viewports can also be defined for rendertarget textures. See Auxiliary views for details.

Each viewport defines a command sequence for rendering the scene, the render path. By default there exist forward, light pre-pass and deferred render paths in the Bin/CoreData/RenderPaths directory, see SetDefaultRenderPath() to set the default for new viewports. If not overridden from the command line, forward rendering is the default. Deferred rendering modes will be advantageous once there is a large number of per-pixel lights affecting each object, but their disadvantages are the lack of hardware multisampling and inability to choose the lighting model per material. In place of multisample antialiasing, a FXAA post-processing edge filter can be used, see the MultipleViewports sample application (Bin/Data/Scripts/09_MultipleViewports.as) for an example of how to use.

The steps for rendering each viewport on each frame are roughly the following:

Query the octree for visible objects and lights in the camera's view frustum.
Check the influence of each visible light on the objects. If the light casts shadows, query the octree for shadowcaster objects.
Construct render operations (batches) for the visible objects, according to the scene passes in the render path command sequence.
Perform the render path command sequence during the rendering step at the end of the frame.
If the scene has a DebugRenderer component and the viewport has debug rendering enabled, render debug geometry last. Can be controlled with SetDrawDebug(), default is enabled.
In the default render paths, the rendering operations proceed in the following order:

Opaque geometry ambient pass, or G-buffer pass in deferred rendering modes.
Opaque geometry per-pixel lighting passes. For shadow casting lights, the shadow map is rendered first.
(Light pre-pass only) Opaque geometry material pass, which renders the objects with accumulated per-pixel lighting.
Post-opaque pass for custom render ordering such as the skybox.
Refractive geometry pass.
Transparent geometry pass. Transparent, alpha-blended objects are sorted according to distance and rendered back-to-front to ensure correct blending.
Post-alpha pass, can be used for 3D overlays that should appear on top of everything else.
Rendering components
The rendering-related components defined by the Graphics and UI libraries are:

Octree: spatial partitioning of Drawables for accelerated visibility queries. Needs to be created to the Scene (root node.)
Camera: describes a viewpoint for rendering, including projection parameters (FOV, near/far distance, perspective/orthographic)
Drawable: Base class for anything visible.
StaticModel: non-skinned geometry. Can LOD transition according to distance.
StaticModelGroup: renders several object instances while culling and receiving light as one unit.
Skybox: a subclass of StaticModel that appears to always stay in place.
AnimatedModel: skinned geometry that can do skeletal and vertex morph animation.
AnimationController: drives animations forward automatically and controls animation fade-in/out.
BillboardSet: a group of camera-facing billboards, which can have varying sizes, rotations and texture coordinates.
ParticleEmitter: a subclass of BillboardSet that emits particle billboards.
Light: illuminates the scene. Can optionally cast shadows.
Terrain: renders heightmap terrain.
CustomGeometry: renders runtime-defined unindexed geometry. The geometry data is not serialized or replicated over the network.
DecalSet: renders decal geometry on top of objects.
Zone: defines ambient light and fog settings for objects inside the zone volume.
Text3D: text that is rendered into the 3D view.
Additionally there are 2D drawable components defined by the Urho2D sublibrary.

Optimizations
The following techniques will be used to reduce the amount of CPU and GPU work when rendering. By default they are all on:

Software rasterized occlusion: after the octree has been queried for visible objects, the objects that are marked as occluders are rendered on the CPU to a small hierarchical-depth buffer, and it will be used to test the non-occluders for visibility. Use SetMaxOccluderTriangles() and SetOccluderSizeThreshold() to configure the occlusion rendering.
Hardware instancing: rendering operations with the same geometry, material and light will be grouped together and performed as one draw call. Objects with a large amount of triangles will not be rendered as instanced, as that could actually be detrimental to performance. Use SetMaxInstanceTriangles() to set the threshold. Note that even when instancing is not available, or the triangle count of objects is too large, they still benefit from the grouping, as render state only needs to be set once before rendering each group, reducing the CPU cost.
Light stencil masking: in forward rendering, before objects lit by a spot or point light are re-rendered additively, the light's bounding shape is rendered to the stencil buffer to ensure pixels outside the light range are not processed.
Note that many more optimization opportunities are possible at the content level, for example using geometry & material LOD, grouping many static objects into one object for less draw calls, minimizing the amount of subgeometries (submeshes) per object for less draw calls, using texture atlases to avoid render state changes, using compressed (and smaller) textures, and setting maximum draw distances for objects, lights and shadows.

Handling GPU resource loss
On Direct3D9 and Android OpenGL ES 2.0 it is possible to lose the rendering context (and therefore GPU resources) due to the application window being minimized to the background. Also, to work around possible GPU driver bugs the desktop OpenGL context will be voluntarily destroyed and recreated when changing screen mode or toggling between fullscreen and windowed. Therefore, on all graphics APIs one must be prepared for losing GPU resources.

Textures that have been loaded from a file, as well as vertex & index buffers that have shadowing enabled will restore their contents automatically, the rest have to be restored manually. On Direct3D9 non-dynamic (managed) textures and buffers will never be lost, as the runtime automatically backs them up to system memory.

See IsDataLost() function in VertexBuffer, IndexBuffer, Texture2D, TextureCube and Texture3D classes for detecting data loss. Inbuilt classes such as Model, BillboardSet and Font already handle data loss for their internal GPU resources, so checking for it is only necessary for custom buffers and textures. Watch out especially for trying to render with an index buffer that has uninitialized data after a loss, as this can cause a crash inside the GPU driver due to referencing non-existent (garbage) vertices.

Further details
See also Materials, Shaders, Lights and shadows, Render path, Skeletal animation, Particle systems, Zones, and Auxiliary views.

See Rendering modes for detailed discussion on the forward, light pre-pass and deferred rendering modes.

See Differences between Direct3D9 and OpenGL for what to watch out for when using the low-level rendering functionality directly.