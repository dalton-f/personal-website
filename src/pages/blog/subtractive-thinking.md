---
layout: ../../layouts/MarkdownPostLayout.astro
title: "Subtractive Thinking"
publicationDate: "12-03-2026"
description: "The complexities behind performantly subtracting geometry"
---

Recently, I stumbled upon a game called **“Get in the Hole”**, which, as the name suggests, is all about shoving things into a hole. What started as a simple and oddly satisfying game quickly sent me spiralling down a rabbit hole about — quite literally — making holes.

**Donut County** is a similar game where you play as a hole, swallowing objects as they fall into you. In that game, the hole moves and grows as you play — something that was especially impressive back in **2018**, when generating dynamic geometry would have been much slower on the hardware of the time. Having a static hole in your game is one thing, but handling interactions with moving negative space almost feels like magic.

Since I’ve long wanted to build a game where a moving hole is the core mechanic, understanding how this “magic” works became a bit of an obsession.

In more technical terms, I’ve become completely obsessed with the **surprisingly complex problem of subtracting geometry efficiently.** In this post, I’ll walk through some of the approaches I’ve found while exploring this problem, in the hope that it helps anyone looking to subtract their own geometry.

## The Requirements

As the core mechanic of the games mentioned revolves around the **hole itself**, it must function as a fully dynamic element within the scene. The hole should be able to move freely across surfaces while maintaining a valid opening in the surrounding geometry.

In addition to translation, it must support rotation and scaling so that it can **adjust its size during gameplay**.

The system should also support multiple hole shapes, such as circles, rectangles, **or more irregular forms.** Allowing different shapes increases gameplay variety and enables mechanics where the player must match the shape of the hole to specific objects.

Beyond simple transformations, the hole should also support complex animations. These may include smoothly changing size, morphing between shapes, or reacting dynamically to gameplay events. Because these changes occur during gameplay, the surrounding geometry and collision data must be updated in real time while avoiding visual artifacts and **maintaining reliable collision detection.**

## The Approaches

### Static Geometry

The simplest approach to creating any geometry with a hole in it is to simply create the mesh in Blender and subtract any shape from it using the boolean modifiers. Importing the result into any game engine is straightforward, and collisions can be generated easily. While effective for static geometry, this method presents **several notable limitations** when more complex or dynamic functionality is required.

### Constructive Solid Geometry

A slightly less obvious way to create holes at runtime is by using **Constructive Solid Geometry (CSG)**. Think of it like Blender’s boolean modifiers: you subtract shapes to build complex geometry. Thankfully, CSG nodes handle collisions automatically, so you don’t have to worry about that.

You can move, scale, or rotate the nodes on the fly, letting you tweak the hole’s size and shape in real time. You can also stack multiple subtractive nodes to shape the hole exactly how you want. This method is usually faster than manually modelling the meshes in Blender, giving you more flexibility.

Unfortunately, the CSG approach isn’t quite as magical as it seems. Godot’s own documentation warns: “Moving a CSG node within another CSG node also has a significant CPU cost, so it should be avoided during gameplay.”

To make any of these dynamically changes, you _literally_ have to move a CSG node inside another at runtime. Needless to say, that’s not great for performance, especially if you need things to run smoothly during gameplay when targeting mobile or older hardware. Even on beefy hardware, any complex animations could make your game completely unplayable for some users.

#### Project P.I.T.T

While researching for this blog, I came across **Project P.I.T.T** and reached out to its solo developer, Fr0ke. He explained that for his hole implementation, “if it were a fixed hole, I would probably have modelled it in Blender and imported it directly.” Instead, he opted for this CSG subtraction approach to tackle the visuals, while using “12 cube shapes, one for each side of the cylinder" to manage collisions.

Separating the rendered geometry from collision detection is one of the key factors in enabling performant geometry subtraction. In Fr0ke's case, this approach was a compromise between precision and performance, as he found that CSG collisions became unreliable with fast-moving rigid bodies and high-impact forces, sometimes causing objects to phase through the ground.

### The Genius of Donut County

At this stage of implementation, it’s common for developers to hit a roadblock. If static geometry isn’t feasible and CSG nodes are too CPU-intensive, is it even possible to create a hole that meets such dynamic requirements? As noted earlier, Donut County somehow managed to achieve this, even as far back as 2018. So how did they do it?

Well, as Fr0ke partially discovered, separating the rendered geometry from collision detection is one of the key factors in enabling performant geometry subtraction. Treating the hole (or pit!) as one entity is the wrong approach. Technical details around the implementation of the hole in Donut County are few and far between, but an article from Rock Paper Shotgun gives us enough details to recreate the approach in Godot.

As the article states, the secret of the hole is that "Donut County runs two physics universes at once, the hole’s world and the real one. When an object gets within range of the hole, it stops respecting the normal ground and starts respecting the hole’s physics.".

This sounds pretty complicated, but just means that seeing through the hole and falling through the hole are completely seperated. In fact, the hole really isn't a hole at all.

The ground can remain as a continuous, infinite plane, with a transparent circle representing the hole. With Godot’s recent support for stencil buffers, we can write the circle’s shape into the stencil buffer. By updating the ground’s material to read from this buffer, pixels are only drawn where there is no stencil data. In other words, the area of the circle won’t be rendered, creating the visual effect of a hole while keeping the ground’s geometry fully intact. Carving out a circle like this means no horrible CSG wireframes or runtime subtraction, just refusal to paint certain pixels. Of course, a circle is two-dimensional, so the hole at this point lacks any depth, but this is easily fixable.

If your hole doesn't need any interactivity, you can stop here - essentially, we've just changed the graphics, and objects will still treat the "hole" as solid ground. But for games where putting things in a hole is the aim, we have to do something about handling collisions.

To solve this, when an object comes near the hole, it should stop obeying the normal ground physics. In Godot, this can be done by changing the object’s collision layer and mask at runtime so it no longer interacts with the ground’s collision layer, essentially allowing it to pass through the “hole.”

```gdscript
extends Area3D

func _on_body_entered(body: Node3D) -> void:
    if body is RigidBody3D:
        body.collision_layer = 8
        body.collision_mask = 8

func _on_body_exited(body: Node3D) -> void:
    if body is RigidBody3D:
        body.collision_layer = 1
        body.collision_mask = 1
```

To make this even safer and more visually coherent, you can add a rim around the hole using a baked CSG subtraction. The rim serves as a thin edge around the hole, so objects won’t accidentally slide off areas outside the visual hole. This way, the geometry guides objects naturally into the hole, keeping gameplay predictable while preserving the illusion of an open gap. Essentially, the ground remains intact, but the combination of stencil visuals and subtle collision adjustments ensures both aesthetic and functional integrity.

To learn more about this approach, I highly recommend a video by Jeremy that explains it in detail which will be linked at the end of the blog.

#### A Note about Performance

Because this method avoids runtime CSG subtraction, it is far more CPU-efficient and can handle dynamic animations and interactions smoothly, even on less powerful hardware. Any 2D shape can be used as a mask to define the visual hole, and the same CSG approach can generate corresponding collision “donuts.”

When adjusting the hole’s size or rotation during gameplay, you’re simply scaling a pre-baked mesh and its collision geometry, rather than recalculating subtraction in real time.

```gdscript
func resize_hole(new_radius):
    $HoleMesh.scale = Vector3(new_radius, 1, new_radius)
    $HoleCollision.scale = Vector3(new_radius, 1, new_radius)
```

Ultimately, this approach can boost performance by around 30% compared to moving CSG nodes inside of each other.

## Final Thoughts

Subtractive geometry, especially in the context of a dynamic hole, is deceptively tricky. At first glance, you might think it’s just about carving shapes out of a mesh but as we’ve seen, doing it efficiently at runtime while maintaining accurate physics and collision is far more nuanced.

The journey from static Blender meshes, through CSG nodes, to Donut County’s elegant “illusion of a hole” demonstrates a key lesson in game development: sometimes the best solution isn’t about manipulating geometry directly, it’s about cleverly separating visuals from physics.

By thinking a little differently, you can turn what seems like a heavy computational problem into something elegant, performant, and fun to play.

## Acknowledgements + Further Reading

Huge thanks to Jeremy for his original research and detailed explanation of implementing Donut County–style holes in Godot. You can watch his video here: https://www.youtube.com/watch?v=QNixyhV4Mgw

Rock Paper Shotgun's article about How Donut County's hole works: https://www.rockpapershotgun.com/how-donut-countys-hole-works

Games mentioned in this blog:

- Get in the Hole: https://store.steampowered.com/app/4174950/Get_in_the_Hole/ | https://discord.gg/gXYkQBqsvQ
- Project P.I.T.T: https://store.steampowered.com/app/4026250/Project_PITT/ | https://discord.gg/pxjc72PZKc
- Donut County: https://store.steampowered.com/app/702670/Donut_County/
