---
title: "Motion Matching from Scratch: Debugging the database"
date: ""
excerpt: "A bit of my "
category: "Game Dev"
tags: ["Unity", "Motion Matching", "Mocap", "Animaiton"]
featured: false
bannerImage: ""
---



Where I work I got assigned the simple task of creating our own motion matching system for our super specific case. If you want to learn about how I tackle the task you can read the whole series, or at lease start on [Motion Matching from Scratch: Was It Worth It?](https://www.loart.dev/blog/motion-matching-from-scratch-was-it-worth-it)

For our use case I created the system so it takes long raw mocap recordings. While this mocap recordings are good for plenty of things and help make the system behave better, they also make analyzing the data much more difficult. So I had to come up with interesting ways of testing the data and get the answers to why my "perfect" system was failing.  

Is it me or is the data?

One big problem you will find when doing your own system is the big question: Does my code suck or the data sucks?

And to find out you have to do a lot of trial and error, but due to constrain like the fact we cannot be doing mocap all the time to see if the problem is the code or the data, I had to make tooling for verifying this. 

## Database analysis

So lets check what data the system had... While I could do this manually, that would have taken me months… So I did the prudent thing and make a tool that does that job for me. Creating easier to understand data, and Graphs... Also, how else am I supposed to show this to you, guys… Excel sheet?



Therefore, figure out why the system was failing sometimes I needed a couple of things, a constant test system (I had done before). Data from those runs, and a way to visualize the Database data. Therefore, I came up with a Python tool that can go over the database, not the animations, just the raw data that I was sampling from the animations themselves, and then visualize it in a clean and helpful way.



### Data distribution

Ok, we have data... but what kind? Well, we could just go over it and see things like how much of the data of each we have by looking at the animation and writing notes. But let's be honest, we could do that the first or second time, but when we have this amount of data and running tests to get the system running... you don't really want to go over all that.



The tool took all the database and baked samples containing everything the system considers. And then sorted the data allowing us to preview what things were happening, how much of each thing we had, and how to compare it.



![](https://res.cloudinary.com/loartdev/image/upload/v1785269124/loartdev-media/blog/jo2y0l8v2mvggrbid2en.png align="center")





&nbsp;

### Data relations

Ok We know how much data we have to go in different directions and speeds. But, how does that data relations with the other?

Well, this is when you use some point cloud system... Yup, the same you will use for neural networks.

Here we took the basics, first we analyzed how the poses were related, the closer the points are the easier it is for our motion matching system to jump between animations. This is because we consider the poses of the characters when matching the movement.



![](https://res.cloudinary.com/loartdev/image/upload/v1785269149/loartdev-media/blog/wdqwlazk3u7jjjvpklrr.png align="center")





To make things easier the data was coloured coded by clips, and as you can see, there is one clip, the pink one, that is far apart from the other data... this is because that is crouching animation clip, and thanks to this we discovered why it was so challenging to make the character enter or leave the crouching state. So yea I wasn't crazy… The system just works properly.



But ok, we now know how the poses' relation... what about the rest... or better, what about the whole database?

That is what this graph shows, same as before, the closer the dots the easier it is to transition between the animations. When a big cluster exists it means that the transition is cheap.



![](https://res.cloudinary.com/loartdev/image/upload/v1785269169/loartdev-media/blog/igylybsn5ehfmtokjxbq.png align="center")





&nbsp;

Let's take that and display it in a comparison table,  We are comparing how many transitions and how costly it would be to transition between clips. More specific how costly it is to get from the animation on the Y to the animation on the X, and how many clips can transition.

![](https://res.cloudinary.com/loartdev/image/upload/v1785269201/loartdev-media/blog/vadyslyusvm1qs4qzbl6.png align="center")





### Locomotion data

Ok, we can transition, we can walk, we look spectacular.... but why is the character not reaching the waypoints correctly? Well, we needed to analyze even more data. This time movement... so we took the data of how far the charter will move in x seconds and compute it all creating a graph showing future trajectory. This allows us to fin why we are not getting the best accuracy on the movement itself.

![](https://res.cloudinary.com/loartdev/image/upload/v1785269188/loartdev-media/blog/rcx84ldjgt0mjfngtjog.png align="center")





&nbsp;

&nbsp;

&nbsp;

### Refining the data

One thing that became clear, is that we needed to track a lot of data, but too much data is not good, and too little data is also not good. So balancing it was important, we wanted to have enough high-quality data.

I had 3 paths for this, each one helped improve the data, allowing me to get better results, without killing performance or the memory.

#### Sampled Frames

This is the basic one. It is about what you are tracking. Increasing or reducing the tracked data is important to reduce the number of problems, and fine tune the system.

But do you need all the velocities? Or positions, or the data for 1 second in the future?

Well, this is where you start to go over and change what data you are baking, test if the system works or not, and then reconsider your life choices. 

The idea is to track only what you really need, not every single bone.

#### Mocap Animations

This is where things get expensive. Sometimes the only way to improve data is to get more, or get better mocap, and that is expensive as it is time from other people, then clean up processes, and plenty of time-consuming tasks. But this is where you are going to get the best benefit.  

When you do the mocap, don't do a generic fits all solution, do one that fits your use case. If you don't need people running, then don't record people running. The Mocap should contain the data you will use, and reducing the size of unnecessary data helps to have a smaller database without loosing quality. 

Another thing is how you pack your dances. The more data you pack into it the more data you have to load into memory. And while this might not be a problem for a small-scale game, or the main character. We don't want to load 200 Mb of animations when we could just have 20 Mb... Pack your animations by use case, you can have a full strafing animation set, and the only load it when you need it making your data set smaller and your use of memory too.

#### Animation Tags

I implemented the animation tag system. This was a feature meant to improve the animation selection. It allowed us to have animations that were too costly be played, and also to do things like crouching.

But using the animation tags also allowed us to avoid animations, and improve the others. By adding proper things like strafing into the tags, we were able to force the character to use those animations, or avoid them.