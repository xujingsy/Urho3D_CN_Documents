Urho3D implements a simple, hierarchical user interface system based on rectangular elements. The elements provided are:

BorderImage: a texture image with an optional border
Button: a pushbutton
CheckBox: a button that can be toggled on/off
Cursor: a mouse cursor
DropDownList: shows a vertical list of items (optionally scrollable) as a popup
LineEdit: a single-line text editor
ListView: shows a scrollable vertical list of items
Menu: a button which can show a popup element
ScrollBar: a slider with back and forward buttons
ScrollView: a scrollable view of child elements
Slider: a horizontal or vertical slider bar
Sprite: a texture image which supports subpixel positioning, scaling and rotating.
Text: static text that can be multiline
ToolTip: a popup which automatically displays itself when the cursor hovers on its parent element.
UIElement: container for other elements, renders nothing by itself
View3D: a window that renders a 3D viewport
Window: a movable and resizable window
The root UI element can be queried from the UI subsystem. It is an empty canvas (UIElement) as large as the application window, into which other elements can be added.

Elements are added into each other similarly as scene nodes, using the AddChild() and RemoveChild() functions. Each UI element has also a user variables VariantMap for storing custom data.

To allow the elements react to mouse input, either a mouse cursor element must be defined using SetCursor() or the operating system mouse cursor must be set visible from the Input subsystem.

UI textures
The BorderImage and elements deriving from it specify a texture and an absolute pixel rect within it to use for rendering; see SetTexture() and SetImageRect(). The texture is modulated with the element's color. To allow for more versatile scaling the element can be divided into 9 sub-quads or patches by specifying the width of each of its borders, see SetBorder(). Setting zero borders (the default) causes the element to be drawn as one quad.

The absolute pixel rects interact poorly with the Renderer's texture quality setting, which reduces texture sizes by skipping the topmost mipmaps. Generating mipmaps is also often unnecessary for UI textures, as they are usually displayed with 1:1 ratio on the screen. Therefore it's a good practice to use the following accompanying settings XML file for UI textures to disable quality reduction and mipmaps:

<texture>
    <mipmap enable="false" />
    <quality low="0" />
</texture>
Defining UI elements in XML
User interface elements derive from Serializable, so they can be serialized to/from XML using their attributes. There are two distinct use cases for UI definition files: either defining just the UI element style and leaving the actual position and dimensions to be filled in later, or fully defining an UI element layout. The default element style definitions, used for example by the editor and the debug console, are in the file Bin/Data/UI/DefaultStyle.xml.

The function LoadLayout() in UI will take an XML file and instantiate the elements defined in it. To be valid XML, there should be one root-level UI element. An optional style XML file can be specified; the idea is to first read the element's style from that file, then fill in the rest from the actual layout XML file. This way the layout file can be relatively simple, as the majority of the data is already defined.

Note that a style can not be easily applied recursively to the loaded elements afterward. Therefore remember to specify the style file already when loading, or alternatively assign a default style file to the UI root element, which will then be picked up by all loaded layouts. This works because the UI subsystem searches the style file by going up the parental chain starting from target parent UI element. The search stops immediately when a style file is found or when it has reached the root element. Also note that Urho3D does not limit the number of style files being used at the same time in an application. You may have different style file set along the UI parental hierarchy, if your application needs that.

See the elements' C++ code for all supported attributes, and look at the editor's user interface layouts in the Bin/Data/UI directory for examples. You can also use the Editor application to create UI layouts. The serialization format is similar to scene XML serialization, with three important differences:

1) The element type to instantiate, and the style to use for it can be set separately. For example the following element definition

<element type="Button" style="CloseButton" />
tells to instantiate a Button element, and that it should use the style "CloseButton" defined in the style XML file.

2) Internal child elements, for example the scroll bars of a ScrollView, need to be marked as such to avoid instantiating them as duplicates. This is done by adding the attribute internal="true" to the XML element, and is required in both layout and style XML files. Furthermore, the elements must be listed in the order they have been added as children of the parent element (if in doubt, see the element's C++ constructor code. Omitting elements in the middle is OK.) For example:

<element type="ScrollView" />
    <element type="ScrollBar" internal="true" />
        ...customize the horizontal scroll bar attributes here...
    </element>
    <element type="ScrollBar" internal="true" />
        ...customize the vertical scroll bar attributes here...
    </element>
</element>
3) The popup element shown by Menu and DropDownList is not an actual child element. In XML serialization, it is nevertheless stored as a child element, but is marked with the attribute popup="true".

UI element layout
By default UI elements operate in a "free" layout mode, where child elements' positions can be specified relative to any of the parent element corners, but they are not automatically positioned or resized.

To create automatically adjusting layouts, the layout mode can be switched to either "horizontal" or "vertical". Now the child elements will be positioned left to right or top to bottom, based on the order in which they were added. They will be preferably resized to fit the parent element, taking into account their minimum and maximum sizes, but failing to do that, the parent element will be resized.

Left, top, right & bottom border widths and spacing between elements can also be specified for the layout. A grid layout is not directly supported, but it can be manually created with a horizontal layout inside a vertical layout, or vice versa.

Fonts
Urho3D supports both FreeType (.ttf, .otf) and http://www.angelcode.com/products/bmfont/ "bitmap" fonts.

For FreeType fonts, it is possible to adjust the positioning of the font glyphs. See SetAbsoluteGlyphOffset() to set a fixed pixel offset for all point sizes, or SetScaledGlyphOffset() to set a floating point offset that will be multiplied with the point size before applying. The offset information can be also stored in an accompanying XML file next to the font file, which is formatted in the following way: (it is legal to specify either or both of absolute and scaled offsets, and either or both of X & Y coordinates)

<font>
    <absoluteoffset x="xInt" y="yInt" />
    <scaledoffset x="xFloat" y="yFloat" />
</font>
Sprites
Sprites are a special kind of UI element that allow subpixel (float) positioning and scaling, as well as rotation, while the other elements use integer positioning for pixel-perfect display. Sprites can be used to implement rotating HUD elements such as minimaps or speedometer needles.

Due to the free transformability, sprites can not be reliably queried with GetElementAt(). Also, only other sprites should be parented to sprites, as the other elements do not support scaling and rotation.