---
layout: post
title: A Lesson in Scope.
description: This is a short post about how my goals for my Engine's 3D Renderer went from writing my own wrapper, to porting a library, back to writing my own, but with far stricter goals.
date: 2025-07-19
image: '/assets/PostImages/ALessonInScope1.png'
tags: [nessie, graphics]
projectTag: nessie
---

# The Initial Goal
At the end of my graphics class last semester, I had a basic Vulkan implementation that was able to render a 3D scene with [PBR](https://en.wikipedia.org/wiki/Physically_based_rendering) materials. It was super cool! However, since the class was just about learning the graphics techniques, architecture considerations took a bit of a back seat. All Renderering operations flowed through a single class setup by the instructor called the **Renderer Context**. It managed the device creation, swapchain management, command execution, resources, etc. If you had to interact with Vulkan for any reason, this was the sole class to use.

This was great for learning, but not great to maintain. *Here is what the initial plan was:*
* **Abstract the Vulkan API as much as possible.** Vulkan takes a lot of initialization code, so I'd like to streamline that as best I can.
* **Break up the Renderer Context class into individual objects.** Keeping class responsiblities low reduces the mental overhead, and makes debugging and refactoring easier.
* **Multithreading rendering commands.** I had seen a video from [The Cherno](https://www.youtube.com/@TheCherno) where he talks about how the renderer of his Hazel Engine maintains a render thread to record rendering commands on the next framebuffer while the main thread presents the previously recorded frame. Graphics operations can be expensive, and if I wanted to add a threading architecture, I thought it would be good to start it early (*foreshadowing*).

***

# An Expensive Detour
I began with [VkBootstrap](https://github.com/charles-lunarg/vk-bootstrap) to handle the Instance creation, Physical Device selection, Logical Device creation and the Swapchain. It is what the class code used, and I didn't see a reason to change it - until "Extension support" came up. Now, if you know **VkBootstrap**, you'll know that it can handle enabling extensions and features just fine. However, I decided to look around to see what others have done; afterall, this project's primary goal is to learn, so I am allowed to take time in research.

I mentioned in the last post that I was debating whether to use at NVIDIA's [NRI](https://github.com/NVIDIA-RTX/NRI) library. Well, I went down a rabbit hole reading through that codebase. The library supports Vulkan, D3D12 and D3D11, and can be used on Windows, Linux and Apple (through MoltenVK). My eyes lit up!

> Oooo they are loading the Vulkan library dynamically and creating their own dispatch table! And they are enabling all available features...

> Interesting, they are using function tables filled by the selected graphics API to get around an inheritance model and divide feature support up!

> Oh cool! That's the code for ray tracing support!

> So that's Direct 3D huh?
>
> <cite>(This went on for a while)</cite>

Pretty soon I found myself trying to replicate parts of that library. I started to translate their abstracted description classes and enums, worked on the loading the Vulkan library, found out that they used the [Vulkan-Headers](https://github.com/KhronosGroup/Vulkan-Headers) repo because the latest SDK version for Windows was out of date with some of the extensions they were querying, and then...

...a moment of clarity.

***

# Just use that Library!
The amount of work was getting crazy. Why was I trying to recreate what someone else has already done? So, I got to work on importing the NRI library into my engine. My engine is built with a few [Premake](https://premake.github.io/docs/) scripts. Unfortunately, NRI uses [CMake](https://cmake.org), which I didn't have any experience with, so it took a couple of days to figure out how to replicate the process of adding the individual projects into my solution, with all of the proper preprocessor commands. Eventually I got it working. Well, mostly. I was getting linker errors with a few D3D12 functions and I couldn't quite figure out why. After fighting it for a while, I decided to remove D3D support entirely and only enable Vulkan. It turned out the library wasn't meant to only supprt Vulkan so I had to tweak the code slightly get it to work. Finally, NRI was in.

It was now time to start refactoring the **Renderer Context** with my new backend!...

...now let me see how to do that with NRI...

***

# What were those goals again?
Every night, I try to write a short reflection on the day's work. Here is what I wrote after getting that library in:

![Space]({{site.baseurl}}/assets/PostImages/ALessonInScope3.png)


I started scaling back. I added these points as well soon after:
* I don't care about hiding Vulkan headers entirely. Just focus on breaking up the Renderer Context, and write the interface to be as friendly as possible.
* Get rid of the render thread for now. It's already complicated enough.

As of writing, I am back on track. I don't have anything really cool to show just yet, but I have been making steady progress *actually solving* my initial goals. 
I hope that by writing out this tale will internalize the fact that restricting scope is beneficial to making progress. I need to keep the amount work small, focused and consistent.

The detour wasn't all wasted effort. I was exposed to new rendering techniques like [Dynamic Rendering](https://docs.vulkan.org/tutorial/latest/03_Drawing_a_triangle/03_Drawing/00_Framebuffers.html), CMake, what it takes to support multiple graphics APIs, and much more.

The next post will be showing off my completed renderer!