In order to make 2D games in Urho3D, the Urho2D sublibrary is provided. Urho2D includes 2D graphics and 2D physics.

A typical 2D game setup would consist of the following:

Create an orthographic camera
Create some sprites
Use physics and constraints to interact with the scene
Orthographic camera
In order to use Urho2D we need to set camera to orthographic mode first; it can be done with following code:

C++:

// Create camera node
Node* cameraNode = scene_->CreateChild("Camera");
// Create camera
Camera* camera = cameraNode->CreateComponent<Camera>();
// Set camera orthographic
camera->SetOrthographic(true);
// Set camera ortho size (the value of PIXEL_SIZE is 0.01)
camera->SetOrthoSize((float)graphics->GetHeight() * PIXEL_SIZE);
AngelScript:

// Create camera node
Node@ cameraNode = scene_.CreateChild("Camera");
// Create camera
Camera@ camera = cameraNode.CreateComponent("Camera");
// Set camera orthographic
camera.orthographic = true;
// Set camera ortho size (the value of PIXEL_SIZE is 0.01)
camera.orthoSize = graphics.height * PIXEL_SIZE;
Lua:

-- Create camera node
cameraNode = scene_:CreateChild("Camera")
-- Create camera
local camera = cameraNode:CreateComponent("Camera")
-- Set camera orthographic
camera.orthographic = true
-- Set camera ortho size (the value of PIXEL_SIZE is 0.01)
camera.orthoSize = graphics.height * PIXEL_SIZE
To zoom in/out, use SetZoom().

Sprites
Urho2D provides a handful of classes for loading/drawing the kind of sprite required by your game. You can chose from animated sprites, 2D particle emitters and static sprites.

Animated sprites
Workflow for creating animated sprites in Urho2D relies on Spriter (c). Spriter is a crossplatform tool for creating 2D animations. It comes both as an almost fully featured free version and a more advanced 'pro' version. Free version is available at http://www.brashmonkey.com/spriter.htm. To get started, scml files from Bin/Data/Urho2D folder can be loaded in Spriter. Note that although currently Spriter doesn't support spritesheets/texture atlases, Urho2D does: you just have to use the same name for your scml file and your spritesheet's xml file (see SpriteSheet2D below for details on how to generate this file). Example 33_Urho2DSpriterAnimation is a good demonstration of this feature (scml file and xml spritesheet are both named 'imp' to instruct Urho2D to use the atlas instead of the individual files). You could remove every image files in the 'imp' folder and just keep 'imp_all.png' to test it out. However, keep your individual image files as they are still required if you want to later edit your scml project in Spriter.

A *.scml file is loaded using AnimationSet2D class (Resource) and rendered using AnimatedSprite2D class (Drawable component):

AnimationSet2D: a Spriter *.scml file including one or more animations. Each Spriter animation (Animation2D) contained in an AnimationSet2D can be accessed by its index and by its name (GetAnimation()).
AnimatedSprite2D: used to display a Spriter animation (Animation2D from an AnimationSet2D). Equivalent to a 3D AnimatedModel. Animation2D animations inside the AnimationSet2D are accessed by their name (String) using SetAnimation(). Playback animation speed can be controlled using SetSpeed(). Loop mode can be controlled using SetLoopMode() (you can use the default value set in Spriter or make the animation repeat or clamp). One interesting feature is the ability to flip/mirror animations on both axes, using SetFlip(), SetFlipX() or SetFlipY(). Once flipped, the animation remains in that state until boolean state is restored to false. It is recommended to build your sprites centered in Spriter if you want to easily flip their animations (otherwise sprite position will seem to diverge).
Animation2D (RefCounted): a Spriter animation from an AnimationSet2D.
For a demonstration, check examples 33_Urho2DSpriterAnimation and 24_Urho2DSprite.

Particle emitters
A 2D particle emitter is built from a *.pex file (a format used by many 2D engines). A *.pex file is loaded using ParticleEffect2D class (Resource) and rendered using AnimatedSprite2D class (Drawable component):

ParticleEffect2D: a *.pex file defining the behavior and texture of a 2D particle (ParticleEmitter2D). For an example, see Bin/Data/Urho2D/greenspiral.pex
ParticleEmitter2D: used to display a ParticleEffect2D. Equivalent to a 3D ParticleEmitter.
For a demonstration, check example 25_Urho2DParticle.

ParticleEditor2D tool (https://github.com/aster2013/ParticleEditor2D) can be used to easily create pex files.

Static sprites
Static sprites are built from single image files or from spritesheets/texture atlases. Single image files are loaded using Sprite2D class (Resource) and spritesheets/texture atlases are loaded using SpriteSheet2D class (Resource). Both are rendered using StaticSprite2D class (Drawable component):

StaticSprite2D: used to display a Sprite2D. Equivalent to a 3D StaticModel.
Sprite2D: an image defined with texture, texture rectangle and hot spot.
SpriteSheet2D: a texture atlas image (that packs multiple Sprite2D images). Spritesheets can be created using tools like ShoeBox (http://renderhjs.net/shoebox/), darkFunction Editor (http://darkfunction.com/editor/), SpriteHelper (http://www.gamedevhelper.com/spriteHelper2Info.php), TexturePacker (http://www.codeandweb.com/texturepacker), ... These tools will generate an image file and a xml file mapping coordinates and size for each individual image. Note that Urho2D uses same xml file format as Sparrow/Starling engines.
You can assign a material to an image by creating a xml parameter file named as the image and located in the same folder. For example, to make the box sprite (Bin/Data/Urho2D/Box.png) nearest filtered, create a file Box.xml next to it, with the following content:

<texture>
    <filter mode="nearest" />
</texture>
The full list of texture parameters is documented here.

Physics
Physics in Urho2D uses Box2D. You can refer to Box2D manual at http://box2d.org/manual.pdf for full reference (just substitute "joints" for "constraints").

PhysicsWorld2D: implements 2D physics simulation. Mandatory for 2D physics components such as RigidBody2D, CollisionShape2D or Constraint2D.
Rigid bodies components
RigidBody2D: a 2D physics object instance. Available BodyType2Ds are:
BT_STATIC: A static body does not move under simulation and behaves as if it has infinite mass. Internally, Box2D stores zero for the mass and the inverse mass. Static bodies can be moved manually by the user. A static body has zero velocity. Static bodies do not collide with other static or kinematic bodies.
BT_DYNAMIC: A dynamic body is fully simulated. It can be moved manually by the user, but normally they move according to forces. A dynamic body can collide with all body types. A dynamic body always has finite, non-zero mass. If you try to set the mass of a dynamic body to zero, it will automatically acquire a mass of one kilogram.
BT_KINEMATIC: A kinematic body moves under simulation according to its velocity. Kinematic bodies do not respond to forces. They can be moved manually by the user, but normally a kinematic body is moved by setting its velocity. A kinematic body behaves as if it has infinite mass, however, Box2D stores zero for the mass and the inverse mass. Kinematic bodies do not collide with other static or kinematic bodies.
You should establish the body type at creation, using SetBodyType(), because changing the body type later is expensive.

Collision shapes components
Use DrawDebugGeometry() to display the shapes.

CollisionShape2D: base class for 2D physics collision shapes.
CollisionBox2D: defines 2D physics collision box.
CollisionCircle2D: defines 2D physics collision circle. Circle shapes have a position (SetCenter()) and radius (SetRadius()). Circles are solid, you cannot make a hollow circle using the circle shape.
CollisionEdge2D: defines 2D physics collision edge. Edge shapes are line segments defined by 2 vertices (SetVertex1() and SetVertex2() or globaly SetVertices()). They are provided to assist in making a free-form static environment for your game. A major limitation of edge shapes is that they can collide with circles and polygons but not with themselves. The collision algorithms used by Box2D require that at least one of two colliding shapes have volume. Edge shapes have no volume, so edge-edge collision is not possible.
CollisionChain2D: defines 2D physics collision chain. The chain shape provides an efficient way to connect many edges together (SetVertices()) to construct your static game worlds. You can connect chains together using ghost vertices. Self-intersection of chain shapes is not supported.
CollisionPolygon2D: defines 2D physics collision polygon. Polygon shapes are solid convex polygons. A polygon is convex when all line segments connecting two points in the interior do not cross any edge of the polygon. A polygon must have 3 or more vertices (SetVertices()). Polygons vertices are stored with a counter clockwise winding.
Important: collision shapes must match your textures in order to be accurate. You can use tools like Physics Body Editor (https://code.google.com/p/box2d-editor/), RUBE (https://www.iforce2d.net/rube/), LevelHelper (http://www.gamedevhelper.com/levelhelper/), PhysicsEditor (http://www.codeandweb.com/physicseditor), ... to help you. Other interesting tool is BisonKick (https://bisonkick.com/app/518195d06927101d38a83b66/).

Constraints components
Apply a constraint to a node (called 'ownerBody') and use SetOtherBody() to set the other node's body to be constrained to the ownerBody. Use SetCollideConnected() to switch collision on/off between the bodies. Use SetDrawJoint() in combination with DrawDebugGeometry() to display the joints. See 32_Urho2DConstraints sample for detailed examples and to help selecting the appropriate constraint.

Constraint2D: base class for 2D physics constraints.
ConstraintDistance2D: defines 2D physics distance constraint. The distance between two anchor points (SetOwnerBodyAnchor() and SetOtherBodyAnchor()) on two bodies is kept constant. The constraint can also be made soft, like a spring-damper connection. Softness is achieved by tuning frequency (SetFrequencyHz() is below half of the timestep) and damping ratio (SetDampingRatio()).
ConstraintFriction2D: defines 2D physics friction constraint. This constraint is used for top-down friction. It provides 2D translational friction (SetMaxForce()) and angular friction (SetMaxTorque()).
ConstraintGear2D: defines 2D physics gear constraint. Used to create sophisticated mechanisms and saves from using compound shapes. This constraint can only connect ConstraintRevolute2Ds and/or ConstraintPrismatic2Ds (SetOwnerConstraint() and SetOtherConstraint()). Like the pulley ratio, you can specify a gear ratio (SetRatio()). However, in this case the gear ratio can be negative.
ConstraintMotor2D: defines 2D physics motor constraint. This constraint lets you control the motion of a body by specifying target position (SetLinearOffset()) and rotation offsets (SetAngularOffset()). You can set the maximum motor force (SetMaxForce()) and torque (SetMaxTorque()) that will be applied to reach the target position and rotation. If the body is blocked, it will stop and the contact forces will be proportional to the maximum motor force and torque.
ConstraintMouse2D: defines 2D physics mouse constraint. Used to manipulate bodies with the mouse, this constraint is almost used in every Box2D tutorial available on the net, to allow interacting with the 2D scene. It attempts to drive a point on a body towards the current position of the cursor. There is no restriction on rotation. This constraint has a target point, maximum force, frequency, and damping ratio. The target point (SetTarget()) initially coincides with the body¡¯s anchor point. The maximum force (SetMaxForce()) is used to prevent violent reactions when multiple dynamic bodies interact. You can make this as large as you like. The frequency (SetFrequencyHz()) and damping ratio (SetDampingRatio()) are used to create a spring/damper effect similar to the ConstraintDistance2D. Many users have tried to adapt the ConstraintMouse2D for game play. Users often want to achieve precise positioning and instantaneous response. The ConstraintMouse2D doesn¡¯t work very well in that context. You may wish to consider using kinematic bodies instead.
ConstraintPrismatic2D: defines 2D physics prismatic constraint. This constraint allows for relative translation of two bodies along a specified axis (SetAxis()). There's no rotation applied. This constraint definition is similar to ConstraintRevolute2D description; just substitute translation for angle and force for torque.
ConstraintPulley2D: defines 2D physics pulley constraint. The pulley connects two bodies to ground (SetOwnerBodyGroundAnchor() and SetOtherBodyGroundAnchor()) and to each other (SetOwnerBodyAnchor() and SetOtherBodyAnchor()). As one body goes up, the other goes down. You can supply a ratio (SetRatio()) that simulates a block and tackle. This causes one side of the pulley to extend faster than the other. At the same time the constraint force is smaller on one side than the other. You can use this to create mechanical leverage.
ConstraintRevolute2D: defines 2D physics revolute constraint. This constraint forces two bodies to share a common hinge anchor point (SetAnchor()). You can control the relative rotation of the two bodies (the constraint angle) using a limit and/or a motor. A limit (SetEnableLimit()) forces the joint angle to remain between a lower (SetLowerAngle()) and upper (SetUpperAngle()) bound. The limit will apply as much torque as needed to make this happen. The limit range should include zero, otherwise the constraint will lurch when the simulation begins. A motor (SetEnableMotor()) allows you to specify the constraint speed (the time derivative of the angle). The speed (SetMotorSpeed()) can be negative or positive. When the maximum torque (SetMaxMotorTorque()) is exceeded, the joint will slow down and can even reverse. You can use a motor to simulate friction. Just set the joint speed to zero, and set the maximum torque to some small, but significant value. The motor will try to prevent the constraint from rotating, but will yield to a significant load.
ConstraintRope2D: defines 2D physics rope constraint. This constraint restricts the maximum distance (SetMaxLength()) between two points (SetOwnerBodyAnchor() and SetOtherBodyAnchor()). This can be useful to prevent chains of bodies from stretching, even under high load.
ConstraintWeld2D: defines 2D physics weld constraint. This constraint attempts to constrain all relative motion between two bodies.
ConstraintWheel2D: defines 2D physics wheel constraint. This constraint restricts a point on bodyB (SetAnchor()) to a line on bodyA (SetAxis()). It also provides a suspension spring.
Background and layers
To set the background color for the scene, use GetDefaultZone().fogColor

You can use different layers in order to simulate perspective. In this case you can use SetLayer() and SetOrderInLayer() to organise your sprites and arrange thir display order.

Finally, note that you can easily mix both 2D and 3D resources. 3D assets' position need to be slightly offset on the Z axis (z=1 is enough), Camera's position needs to be slightly offset (on the Z axis) from 3D assets' max girth and a Light is required.

Tile maps
Tile maps workflow relies on the tmx file format, which is the native format of Tiled, a free app available at http://www.mapeditor.org/. It is strongly recommended to only use the latest stable release (currently 0.9.1). Do not use daily builds or older revisions, otherwise results may be unpredictable.

Check example 36_Urho2DTileMap for a basic demonstration.

You can use tile maps for the design of the whole scene/level, or in adjunction to other 2D resources.

"Loading" a TMX tile map file

A tmx file is loaded using TmxFile2D resource class and rendered using TileMap2D component class. You just have to create a TileMap2D component inside a node and then assign the tmx resource file to it.

C++:

SharedPtr<Node> tileMapNode(scene_->CreateChild("TileMap")); // Create a standard Urho3D node
tileMapNode->SetPosition(Vector3(0.0f, 0.0f, -1.0f));
TileMap2D* tileMap = tileMapNode->CreateComponent<TileMap2D>(); // Create the TileMap2D component
tileMap->SetTmxFile(cache->GetResource<TmxFile2D>("Urho2D/isometric_grass_and_water.tmx")); // Assign tmx resource file to component
AngelScript:

Node@ tileMapNode = scene_.CreateChild("TileMap"); // Create a standard Urho3D node
tileMapNode.position = Vector3(0.0f, 0.0f, -1.0f);
TileMap2D@ tileMap = tileMapNode.CreateComponent("TileMap2D"); // Create the TileMap2D component
tileMap.tmxFile = cache.GetResource("TmxFile2D", "Urho2D/isometric_grass_and_water.tmx"); // Assign the tmx resource file to component
Lua:

local tileMapNode = scene_:CreateChild("TileMap") -- Create a standard Urho3D node
tileMapNode.position = Vector3(0, 0, -1)
local tileMap = tileMapNode:CreateComponent("TileMap2D") -- Create the TileMap2D component
tileMap.tmxFile = cache:GetResource("TmxFile2D", "Urho2D/isometric_grass_and_water.tmx") -- Assign tmx resource file to component
Note that currently only XML Layer Format is supported (Base64 and CSV are not). In Tiled, go to Maps > Map Properties to set 'Layer Format' to 'XML'.

TMX tile maps

Once a tmx file is loaded in Urho, use GetInfo() to access the map properties through TileMapInfo2D class.

A map is defined by its:

orientation: Urho2D supports both orthogonal (flat) and isometric (strict iso 2.5D and staggered iso) tile maps. Orientation can be retrieved with orientation_ attribute (returns 0 for ortho, 1 for iso and 2 for staggered).
width and height expressed as a number of tiles in the map: use width_ and height_ attributes to access these values
width and height expressed in Urho2D space: use GetMapWidth() and GetMapHeight() to access these values which are useful to set the camera's position for example
tile width and tile height as the size in pixels of the tiles in the map (expressed a percentage): use tileWidth_ and tileHeight_ attributes to access these values
Two convenient functions are provided to convert Tiled index to/from Urho2D space:

TileIndexToPosition() to convert tile index to Urho position
PositionToTileIndex() to convert Urho position to tile index (returns false if position is outside of the map)
TMX tile map layers

A tile map is composed of a mix of ordered layers.

Accessing layers : from a TileMap2D component, layers are accessed by their index from bottom to top using GetLayer() function. GetNumLayers() returns the number of layers contained in the tmx file. GetLayerType() returns the type of layer (Tile, Object or Image).

A layer is characterized by its:

name: currently not accessible
width and height expressed as a number of tiles: use GetWidth() and GetHeight() to access these values
Layer visibility can be toggled using SetVisible() (and visibility state can be accessed with IsVisible())

TMX tile map objects

Tiled objects are wire shapes (Rectangle, Ellipse, Polygon, Polyline) and sprites (Tile) that are freely positionable in the tile map.

Accessing Tiled objects : from a TileMapLayer2D layer, objects are accessed by their index using GetObject(). GetNumObjects() returns the number of objects contained in the object layer (tile and image layers will return 0 as they don't hold objects).

Use GetObjectType() to get the nature (TileMapObjectType2D) of the selected object.

Objects' properties (Name and Type) can be accessed using respectively GetName() and GetType(). Type can be useful to flag categories of objects in Tiled.

Except Tile, object layers are not visible. They can be used:

to easily design polygon sprites and Box2D shapes using the object's vertices: use GetNumPoints() to get the number of vertices and GetPoint() to iterate through the vertices
as placeholders to easily set the position and size of entities in the world, using GetPosition() and GetSize()
to display Tile objects as sprites
to create a background from Tile sprites
etc.
Additionaly Sprite2D resource from a Tile object is retrieved using GetTileSprite().

If need be you can access the grid id (relative to the tilesets used) of a Tile object using GetTileGid().