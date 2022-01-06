---
date: 2022-01-01
---

# List of projects

## Main projects

### Quick Avatar Setup

Link : https://github.com/vr-voyage/quick-avatar-setup

A simple tool that allows you to take a GLB model,
play with the blendshapes and generate an "Emotion"
that can quickly be imported into other places,
like VRChat Avatars.

The goal is to be able to setup most of the animations
directly within the editor, but at the moment, this
is focused on Blendshapes based emotes only.

I should be able to release a new version very
soon.  
Before hand, I had to use a special version of 
Godot to get in-game GLB import support, but now
that "Fire" GLTF in-game import tools have been
integrated to Godot 3.4 (but are not available in
the Web version, for unknown reasons), I should be
able to release a version that anyone can edit.

I actually started to develop Remote Logix to
be able to import the JSON files generated with
this tool.

Still, I'd like to support VRM emotions out-of-the-box
with that version.  
And also support basic camera handling...  
Along with blendshapes automatic categorization,
in order to disable some systems when setting up
the appropriate animations on the avatar, either
on VRChat or NeosVR.  
For example, when using emotes involving the eyes,
you generally want to disable the blinking, or
make a blending animation between the two.  
You also need blending animations when using emotes
that open the mouth, to avoid abominations like
the mouth going through the jaw when talking while
using the animation.

### SharpLogix : C# to Logix converter for NeosVR

Link : https://github.com/vr-voyage/SharpLogix

This is starting to work, but there's still a lot
to do. I'm still wondering how I'm going to adapt
the script system I'm outputing and reading back
inside Neos, though there's multiple ways to do it.

The next steps for this editor will be :

* Support for Input/Outputs lists.  
  This is required to support things like "Sequence",
  which allows you to replicate the execution of
  code blocks executions sequences.

* If/Else For/While  
  Aka, basic branching, which is required to import
  90% of existing programs.
  
* Methods to nodes mapping database  
  In order to easily map any external method to
  Logix nodes. I'm not going to hardcode everything,
  that doesn't make any sense.  
  The first mappings should be done around the
  FrooxEngine.DLL functions (easiest) and Unity
  methods (slightly harder)

### VRChat in-game map maker

Link : https://github.com/vr-voyage/vrchat-map-maker

If you're not used to VRChat, there's no real way
to export anything... unless you take a photo, or
use the video player.  
So I was recently able to showcase that how you can make
a tile-based map maker, use it in-game, save the
result by taking a special photo, and reimport the
tiling setup inside other editors to replicate your
creation, as long as the loader has the same shaders
and textures presets.

The next step for this editor is :

* Proper normal maps  
  Right now, this map editor use Texture Arrays, but
  for some reasons I cannot generate a Texture Array
  with normal maps. The generated textures provide
  ugly normals.  
  So I'm trying to figure out how I'm going to solve this one.
  I'm pretty sure the DX5 format is not replicated
  correctly, so I'll have to figure out a way to
  replicate this, or at least to replicate texture
  data that are clearly readable my Normal specific
  Unity shader functions.

* Proper walls setup  
  The current system was rushed for the Virtual Market
  2021 (and I still didn't do it in time), and so
  drawing walls is a pain.  
  I'll try to put in place a better version.


### Remote Logix : Remote 2D Logix editor for NeosVR

Link : https://github.com/vr-voyage/remote-logix

This is basically a remote Logix editor, using a
2D interface, for NeosVR.

The first draft was done using Godot, however after
the first release, the more I advanced on that
editor, the more I thought that I wasn't using the
right tools for the job.

The default node graph system provided with Godot
is kind of clunky, and have a weird programming
pattern which I'm **still** not used to.  
This is especially true when you have to support
for auto-casting, and make decisions based on these
casts.  
Godot default node graph system handles all the
connection logic within the graph, but handles the
input/output inside the nodes.  
That makes it very difficult to managing nodes
input/output depending on the connections.  
On NeosVR, if you connect an integer to a "Add Float"
node, the node becomes a "Add Int" node, and the
input/outputs become "Int" I/O.  
If you then connect a second "double" node, then
the game will try to cast the first Int to Double,
and convert "Add Int" to "Add Double", meaning that
I/O are now of type "Double".  
Dealing with these kind of things, if the node handles
the connection is not very hard.  
It's another story when all the connections are handled
through the graph !  
The internal node system they're using for their
node-based Shader programming seems to be the most
adequate to me, however the base is only available
through the native API.  
I then have to figure out how to use this native
API while exporting to the Web through emscripten.  
Ugh...

I started to use the node-based programming inteface
of BabylonJS recently, and I was like... "Maybe I
could reuse this instead ? It already has a node
import/export system and a script generation
mechanism for shaders, and seems to be using the
same licence too...".  

So the next step will be to try to rewrite this either :

* With a modified version of the node-based programming
  inteface of BabylonJS

* With a modified version of Godot node-based Shader
  programming system.

## Little projects

### VRChat World lock autosetup

Link : https://github.com/vr-voyage/vrchat-worldlock-autosetup

This is just a simple Unity editor tool, to setup
World locked items on avatars.
Items are always parented to the avatar, on VRChat,
so you need special setup (Constraints, Particle Emitters,
...) to immobilize them.  
Since the setup is kinda long and boring, I made
this automation tool.

I might improve it to add "Seat" support in the
future.  
However, this overlaps with another idea.

### Unity "Array modifier"

Link : https://github.com/vr-voyage/unity-array-modifier

A little Unity editor script to repeat a mesh
and "bake it" afterwards. This makes it easier
to generate simple staircases.  
This is still not release ready, since there's
still a few manual work to do here and there.

I'd still like to support Circular arrays, in order to
make spiral staircases, since this was one of the
first question I got asked when making this
plugin.
