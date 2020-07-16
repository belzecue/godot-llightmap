# godot-llightmap
Lightmap module for Godot Engine 3.x
Version 0.1 (work in progress, there may be bugs, but it is usable)

<img src="/pics/llightmap_01screen.png" alt="screenshot" width="400"/>

## About
While JFons is finishing off the new official core lightmapper for 3.2, I've spent some time playing making this toy lightmapper. It works so well I'm now going to be using it to replace the lightmapping functionality in my other module, lportal.

LLightmap is designed to be easy to use and produce nice soft shadows, with high performance at runtime. The existing core lightmappers produce a different lightmap for each object. This can be slow due to texture swapping. LLightmap instead can uv map your scene with multiple objects, unwrapping so that all your objects share the same texture space. Thus a single lightmap can be used.

### Features
* Forward and backward ray tracing options
* Volumetric lights for soft shadows
* Ambient occlusion
* Linear HDR exr intermediate files
* Linear HDR exr final texture or gamma corrected normalized png

### Still todo
* Albedo / roughness / metal from source textures for colored reflection
* Colored lights
* Option of multiple lightmaps
* Transparency support

## Instructions

### Initial setup
1) Make sure your scene to be lightmapped is under a spatial, name it e.g. 'level' (not the scene root).
2) Mark any static geometry inside the level to be lightmapped with the 'use in baked light' flag in the geometry section of the mesh.
3) Either place the lights as part of the level branch, or create another branch just for static lights.
4) Add the new 'LLightmap' node to the scene tree (preferably not in the level or lights branch).

If you click on the LLightmap inspector, we can now set things up for lightmapping.
1) Assign the 'meshes' to point to your level geometry branch.
2) Assign the 'light' to point to your lights branch.
3) Set the filenames you wish to use for lights intermediate, ambient occlusion intermediate, and the final combined lightmap.

### Unwrapping
Before we can lightmap anything, we need to make sure the level geometry is uvmapped, usually in the 2nd uv channel. The first uv channel is typically used for the main texture, and we will use the 2nd uv for the lightmap.

You can uv map scenes in a third party modelling program such as blender, but this is inconvenient, and LLightmap can use xatlas to do this for us.

1) In order to add the new UVs, we will need to modify the level scene, so it makes sense to save it as a new tscn file. You should set the filename as 'uv_filename' in the uv unwrap section of the inspector.
2) Set the bake_mode to 'UVMap'.
3) Once this output filename is set, and the meshes is assigned correctly, and the bake mode is correct, hit the 'Bake Lightmap' button above the main 3d window.
4) This does a number of things by magic. First it merges all the marked geometry into a single mesh, then it unwraps the mesh, then it 'unmerges' the wrapped mesh back to the original objects. This is quite a complex process and can result in added vertices. Finally it saves the new uvmapped scene into the file we specified, deletes the old scene from the scenetree, and replaces it with our new uvmapped scene.
5) As this step is potentially destructive, it creates a backup of your original level scene in the root of your project.

### Baking
Once the scene is uvmapped, we can move onto the fun stage, baking some lightmaps.

1) Change the bake_mode to 'lightmap'.
2) Once we are sure we have some lights, the meshes and lights are assigned, and the filenames are chosen, hit the 'Bake Lightmap' button.
3) This may take a while to complete, depending on the settings you use. The defaults should bake quite quickly for a very rough version.
4) If all goes well the final lightmap will be produced in your specified directory, check this and try opening it in an image editor.

### Showing the lightmap
In order to actually show the lightmap on the level geometry we have to do a little more work than the core lightmapper. The lightmap is simply a texture. In order to show it, we have to use a custom shader.



## Installation
* Get the latest godot engine 3.x source
* Create a folder called 'llightmap' in the modules directory
* Clone / download this repository into the folder
* Compile as normal (instructions at godot website)
* The new node should be available called 'LLightmap'

Due to using SSE2, this will probably only currently compile on x86_64, on linux and (hopefully) windows. I'll fix up the reference methods so it will compile on other platforms soon. However, as you only need it to create lightmaps on desktop as a preprocess, the lightmaps produced can be used with a standard build of the engine / standard templates.


