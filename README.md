# Vertex Animation Textures (for Blender)

Vertex Animation Textures are a standard format to export a vertex-based animations. See [Why](#why) for clarification on what they are and why they're useful.

This is a Blender plugin primarily meant to export mesh and animation-textures from Blender into Unreal.

Tested with **Blender 3.6**, but this plugin generally found to work with **2.9+**.

### Installing as an Add-on

**Edit > Preferences > Add-Ons**

* "Install" button
* Select `vertex_animation.py`

### Usage

After installation, a toolbar will be placed on the side of the 3d view (sibling to "Tool" and "Item") called Unreal Tools. That offers a range of frames, and a button to process them into a VAT.

To create a vertex animation in the first place, install the "AnimAll" plugin, and use the "Animate" tab (sibling to "Tool" and "Item") to create keyframes for selected vertices for a given point in the timeline, just like you would for creating keyframes on bones.

Be sure to set these scenewide settings in Blender before export;
* Scene -> Units: must be 0.01cm (to match Unreal units)
* Output -> Format -> Frame Rate
	* This greatly affects exported VAT size and fidelity. 
	* In Unreal you'll probably be linearly interpolating between keys, so depending on the complexity of your model and animation, you will need far fewer keys than you might expect; 13fps (the same as a flipbook or anime) works surprisingly well for non-character animations.

After export, you'll have an `export_mesh` which contains UV's necessary for the proper functioning of a VAT material in Unreal - use _that mesh_ as your static mesh. You'll also have an `offsets.exr` and a `normals.png` which are used by the material. If your usage doesn't require normals, you can safely exclude them and just use the offsets.

For use within Unreal, refer to the [Unreal doc on setup](https://docs.unrealengine.com/5.0/en-US/vertex-animation-tool---timeline-meshes-in-unreal-engine/). But in general;

**Both textures**
* Mipmaps: none
* texgroup: UI
* SRGB: false
* Filter: nearest

**Offsets texture**
* Compression: HDR

**Normals texture**
* Compression: Vector Displacement Map

## Why?

In the early days of 3d games, all animations were vertex animations. Each frame had to be manually posed by an animator and saved, which was excruciatingly hard to do properly, since it's hard to maintain the volume and proportion of characters when you're manipulating them. Eventually, cpu's caught up enough that realtime skeletal animations became viable - leading to foot IK, head targeting, ragdolls, VO-matching, mocap, and all the things we've seen for 20 years -- so vertex animations fell deeply out of favor.

But, that's not to say VA is useless. Skeletal animations are _expensive_ on CPU, and are the primary reason why it's hard to have hundreds or thousands of animated characters on-screen in realtime. Vertex animations are _cheap_ on everything at realtime, and only modestly heavier on storage size. Skeletal is also ill-suited to complex meshes without obvious articulating limbs; like a plant swaying in the wind, a fiery explosion pushing outwards, or a lava flow pulsing downstream. And of course, "cinematic physics" as demonstrated in a few titles are just baked vertex animations, in some form or fashion. And, VAT are much more efficient than traditional vertex animations, since they occur in a geometry shader -- meaning every vertex can be offset _in parallel_ (a dramatic improvement over what was being done in the 90s, where a cpu did them in sequence). 

It's not a universal technique, but in situations where they shine, they _really_ shine.

## Acknowledgments

* Original concept adapted for Blender from 3ds Max scripts created by **Jonathan Lindquist** at **Epic Games**.
* Original Blender scripts forked from [Josh Bogart's unreal_tools](https://github.com/JoshRBogart/unreal_tools), taking some image exports from [yanorax's fork](https://github.com/yanorax/unreal_tools).

This repo largely customizes the workflow of Bogart's version, auto-exporting images and preventing copies being made.