---
layout: post
title: 'My Co-worker found a Pokémon in VR, in a Counterstrike map!'
excerpt_separator: <!--end-of-excerpt-->
tags:
- draft
---
- **Q:** What you get when crossing Interns and VR?
- **A:** Pokémon & Counterstrike!'

The write-up of our new interns explaining how they filled up the empty voids
of cyberspace with some, um, interesting contents.
<!--end-of-excerpt-->

## Follow this post and look what I made out of it!

...so when my internship started they gave me a HTC Vive, a highend PC and plenty of scope.

As every young, human being I like computer games a lot. But I should always keep in mind, that VR is not only about gaming, even though it's a very good use case! :D

So I experimented with moving, teleporting, jumping and a lot more.

My buddy (Chris) and me (Khaled) build some little showcases which felt great and we got addicted to it. 
There was only one thing, good 3D content makes every demo better, but it is either expensive or takes a long time for us to create.

### Why not take the good stuff from your youth and make it even better with nowadays technology?

I started to search for 3D models from games I liked a lot in my youth. And found a lot of them.

Things I liked were for example:
* Counter-Strike
* Quake
* Pokémon
* and literally any game you know from back in da days ;)

So I just needed to download a 3D models as .dae, .fbx, or .3ds file, or decompile maps from games you own and import them to my Assets in Unity.
    
> **Protip:** Import the textures first, and Unity will map them correctly onto the materials on the fly, otherwise you need to add them by hand.

You can read and comprehend the implementation at our Git Repository [sandboxVR](https://github.com/artcom/sandboxVR) .

If you enjoyed let us know, share this blog and stay tuned for more content to come.


_Made with :heart: in Berlin._

### Getting started:

#### List of things you need to get started:

 * Unity + SteamVR Plugin
 * Any toolkit, which lets you teleport in VR
    * I used the [Vive-Teleporter](https://github.com/Flafla2/Vive-Teleporter) from Flafla2 for teleporting in VR with the HTC Vive and creating a navigation mesh
 * A map you want to walk through as importable 3D file
    * If you already have a map as 3D file you don't need the following tools, but if you don't you need to decompile, export the maps and import it to Unity
    * For decompiling and exporting you need Blender, Hammer and some Plugins

#### Jump into Unity

* Open Unity and create a new 3D Project.
* Add the SteamVR Plugin.
    * You can do this either by placing a checkmark at SteamVR Plugin and press done in the "Add Asset Package" option in the "Create Project"- dialoge or after creating the project by opening the Asset Store and adding the SteamVR Plugin into your project.
    * In the SteamVR_Settings dialoge, press "Accept All", you will get a "You made the right choice!" ;)
* Kill the Main Camera, we don't need it anymore and add the [CameraRig] prefab from the SteamVR Plugin to your scene.
* Now import the map you want to walk through in VR.
    * "Import New Asset..." -> choose your map --> Drag & Drop from "Project" in your scene, align as you like, done.
    * Protip: Import the Textures first, and Unity will map the Textures correctly on your map right on the fly.
* Implement the teleporter from Flafla2 like in his repository described
* Open the "Navigation" window in Unity.
    * Add all parts you want to be able to teleport to or walk on to your navigation mesh, by placing a checkmark at the "walkable" checkbox and click on the "Bake" button.
    * Update NavMesh in your NavMesh GameObject.
* Move the [CameraRig] gameObject to a point you want to start at and press play.
* Press play and enjoy your favorite map in VR. :+1:
