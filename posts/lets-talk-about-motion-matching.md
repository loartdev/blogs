---
title: "Let's talk about motion matching"
date: ""
excerpt: ""
category: "Game Dev"
tags: ["Motion Matching"]
featured: false
bannerImage: ""
---

Motion Matching, the magical technology the changed how we animated games. But what is it? How does it work? Why is it important?



Well, let's do a deep dive about it. Shall we?



## What is motion matching?

Motion matching is a "new" animation technology for games. You see, before motion matching the only actual way of developing an animation system for games was the old Blend Trees and Animation State machines.

This is a perfect technique for simple, and even complex systems. But it takes time to develop, the bigger the system becomes. When the system becomes big enough you end up spending more time resolving problems, splitting the system, or even adjusting the animations so they can blend properly.

For AAA games this becomes a hard system to develop, maintain, and expand, big animation systems are too complex and costly, requiring significant effort from animators to clean the mocap, and this end up destroying the reason why we are doing the mocap on the first place.

So some teams decided to start researching a better way. Motion Matching, initially the system came from Ubisoft. They wanted to make a motion system that require less set up, increased the quality of the animations, and allowed for more complex systems... without making a complex setup.

And this is where we get to the real thing. Ubisoft designed a system where they can upload what they called Dance Cards, each contained a set of movement providing all the animation data that was needed.

You can learn more about the "Dance Cards" and in the video from Kristjan Zadziuk: [https://www.youtube.com/watch?v=\_Bd2T7uP9VA](https://www.youtube.com/watch?v=_Bd2T7uP9VA)

But the main idea is you want to record the movements you need in a compacted clip, normally from 1 minute to 5 minutes of raw mocap data. And the idea is that you don't clean it. You take it from you mocap software and then retarget it to the character, then take it to the engine.

Then the system uses an offline system to sample the animation, generating features that are used to create the motion database. With this, we can query a motion trajectory and get the best pose to match that query.

That's all... for real, now here is how you blend animations, and how much data you need and store, but on simple terms that is what motion matching is. Hence, why it has become so adopted.





