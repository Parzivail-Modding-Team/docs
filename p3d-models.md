# P3D Models

P3D models, short for Parzi 3D models, are a compact and Minecraft version-agnostic way to represent a 3D model with an extended set of features that make it preferable to formats like JSON.

## How to create a P3D model

P3D files can be obtained by compiling P3Di (P3D Intermediary) models which can currently be exported by the following software:

* Blender (using addon: [io_scene_p3di](https://raw.githubusercontent.com/Parzivail-Modding-Team/GalaxiesParzisStarWarsMod/master/resources/blender_addons/io_scene_p3di.py))

Models can be made in whatever software you prefer, as long as that software can then export your model in a format that Blender can import. For example, models made in Blockbench can be exported to an OBJ model, and Blender can import that model to re-export it as a P3Di model.

<details>
<summary>Q&A: Why is an intermediary format required?</summary>

Since P3D is ultimately a format that PSWG will need to read from a file, it made sense for PSWG, through Aurek, to be the one to write that format to a file as well. In doing do, there is only one place that needs to be updated if changes are required to the format, instead of having to then update the Blender exporter, a theoretical Blockbench exporter, and so on.

This way, if we need to make change to the underlying P3D format, nobody needs to update their exporter, we just need to change how we convert the intermediary file to a compiled model in Aurek.

</details>

## Blender Conventions

When working in Blender, sticking to these rules of thumb will make model authoring a lot easier. These conventions use terminology that assumes you're familiar with the basics of Blender.

* Up is +Z. This is automatically converted to Minecraft's +Y during compilation.
* Forward is +Y. This is automatically converted to Minecraft's +Z during compilation.
* One pixel is 0.1m (10cm).
* The name of the Object becomes the name of the submesh (or "part").
* All Objects **must** have their *rotation* and *scale* transformations **applied** _(Ctrl+A, Rotation & Scale)_ before export.
    * The origin of an Object becomes the rotation point of a submesh in a compiled model.
* **Sockets** are defined by adding Empties of type Arrows _(Add, Empty, Arrows)_.
    * The name of the Empty becomes the name of the socket.
    * The axes as drawn directly correspond to the local coordinate space of the in-game object.
* Objects may use the scene hierarchy to define rotation-based parenting.
    * This applies to both Empties (sockets) and Objects (submeshes).
* All polygons must either be triangles (3 vertices) or quads (4 vertices). No polygon may be an "n-gon" (more than 4 vertices).
  * A quick way to ensure this is to enter Edit mode _(Tab)_, select all _(A)_, Triangulate Faces _(Ctrl+T_ or _Face, Triangulate Faces)_, then convert to quads as desired _(Alt-J_ or _Face, Tris to Quads)_.
* Every material used on an exported Object must be named one of the following:
  * `MAT_DIFFUSE_OPAQUE`: No transparency will be applied. Uses standard diffuse lighting.
  * `MAT_DIFFUSE_CUTOUT`: Pixels will be rounded to full or zero opacity (cutout transparency). Uses standard diffuse lighting.
  * `MAT_DIFFUSE_TRANSLUCENT`: Partially translucent pixels will be rendered accordingly. Uses standard diffuse lighting.
  * `MAT_EMISSIVE`: Cutout transparency. All pixels will be rendered at full brightness regardless of lighting condition.

#### Tips

* If you're exporting your model from Blockbench or any other program where the units are in "Minecraft world space", i.e. one pixel is 1/16th of a block, and therefore 1/16th of a meter, scale your model by `1.6` in all dimensions, and your model will now have one pixel as 10cm, or 1/10th of a meter, which is the convention used in "rules of thumb".

## Exporting a P3Di model from Blender

With the **io_scene_p3di** addon installed and enabled, simply _(File, Export, "Parzi 3D Intermediary (.p3di)")_, select a filename and location for the exported model, and export. Only visible Objects will be exported.

## Compiling a P3Di model into a P3D model

P3Di models can be compiled into P3D models through Aurek.

TODO