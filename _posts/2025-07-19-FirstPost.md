---
layout: post
title: First Nessie Update!
description: I describe where the project is at currently, and talk about the Renderer overhaul that I am working on!
date: 2025-07-19
image: '/assets/PostImages/RendererGoal.png'
tags: [nessie, graphics]
projectTag: nessie
---

This is the first development log for my 3D game engine, Nessie! I am going to try and write these dev logs every so often to talk about the problems that I am solving in the engine.

## Why write your Engine?
First and foremost - I want work on the engine and tools side of the games industry, so writing my own engine seems like a reasonable portfolio piece.
Secondly, I love 3D platformers (starting back with Donkey Kong 64), so I want to bolster my understanding of some of the technical challenges when creating a 3D worlds: Physics simulation and Graphics.

## The Current State of the Project:
I began this project in January of 2025, from the ground up. In the first month, it was about writing the base utility features like logging, the Application class, basic input, as well as a build system utilizing Premake. Then, the goal for Spring was to dive deep into **math, physics and graphics**!

For physics, I primarily started with the book *"Real Time Collision Detection"* by Christer Ericson. Here I was introduced to the nitty gritty of calculating collisions between different primitives, what the heck a "Minkowski Sum/Difference" is, the "Separating Axis Theorem", and much more. This was a lot of information to to ingest on my own, so as I was writing out the basic implementations of the math and geometric primitives, I was looking at a few different libraries to see what others have done, including [glm](https://github.com/icaven/glm), [pbrt](https://github.com/mmp/pbrt-v4), and **Unreal Engine 5**. Most of the time, I was in a state of information overload, until I found my way to the [Jolt Physics](https://github.com/jrouwe/JoltPhysics) library written by Jorrit Rouwe. This was a game changer, as he wrote the code base with a ton of inline documentation and informative comments!

From here on my objective was to write "Jolt Physics Lite". I wanted to go line by line, recreating the parts of the library in order to get a better understanding of the different pieces. I have learned a great deal from this exercise, and as of writing, I have the system where I want it: enough to simulate some boxes falling on one other.

> Going through the library in this way had a cool side effect: I submitted a bug fix that was pulled in! This was my first contribution to a large codebase! I'm a real programmer now! 😊
>
> <cite>(It was a really small bug, but awesome nonetheless)</cite>

However, I ran into a bug where the physics bodies are failing to fall asleep. While I did have [ImGui](https://github.com/ocornut/imgui) for some basic UI, I didn't have many rendering features necessary to figure out what was going on, so midway through the summer I had to switch gears...

## The Renderer Overhaul!
While working on the engine in the spring, I was porting over pieces of a Vulkan Renderer project that I was building as a part of my graphics class. The title image was the fruits of that labor - a physically based renderer. The only issue was, most of the implementation was "hacked in" and very specific for what I needed to accomplish. 

So now I am taking the time to abstract the Vulkan API and utilize a separate thread to process the rendering commands to the GPU. This snowballed into a refactor of some of the core systems: 
- The ``Application`` class's rendering and input responsibilies were removed and placed into a ``Platform`` class.
- A ``DeviceManager`` is now handling the ``RenderDevice`` creation (which for Vulkan is the Instance, Physical and Logical devices). This was previously handled by the ``ApplicationWindow``.
- The ``Renderer`` will now handle the ``Swapchain`` management, which was previously with the ``ApplcationWindow``.
- Dependent classes like the ``Scene`` and ``World`` that I had previously have largely been commented out as I change the rendering architecture.

As of writing, I am working on the Device Queue selection process and then to the Swapchain. Now that the architecture is more clear, I have been debating whether to utilize a library like NVIDIA's [NRI](https://github.com/NVIDIA-RTX/NRI) library to handle most of this abstraction for me. I go back and forth, but the main reasons for not importing the library are the fact that I just want to deal with supporting Vulkan, and I would have to still do some of the abstraction work for that library, so I will carry on for now.

## And that's it for now!
Hopefully next time I can talk more specifically about the rendering work that I am doing, but I wanted this first dev log to give a kind of overview as to what I am doing. Feel free to check out the GitHub page. I am working from the Physics branch: [GitHub](https://github.com/nicholaszollinger/Nessie/tree/Physics).