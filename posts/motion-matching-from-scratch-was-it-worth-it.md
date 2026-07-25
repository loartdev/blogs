---
title: "Motion Matching from Scratch: Was It Worth It?"
date: "2026-08-01"
excerpt: "My experience building my company's motion-matching system from scratch."
category: "Game Dev"
tags: ["Unity", "MotionMatching", "Animation", "Mocap"]
featured: true
bannerImage: ""
---

I know you’ve heard of motion matching, or at least I hope you have. As every AAA game and Game Engine has been implementing this.



Everyone describes as this magical solution that you feed animations too, and it just works. No need of complex blend trees, state machines, and so. Just full animation and root motion.



But you know what people don’t talk about?

HOW PAINFUL it is to get right. Mostly when your boss just comes to you and mentions it, and you just go and say:



> Pff, I can do that with my eyes closed.



Now I have to implement it... and before you think I had any idea on how these systems work, I didn’t. So I had to get my ass on my desk and read papers, and more papers, and yea, long GDC talks. And at the end of all I learned how hard these systems actually are.



## What is Motion Matching really?

As the name suggest we are matching the movement of the character using the animation. Now that might be challenging to imagine when you know that we use root motion, so the animation moves the character....  

However, the core is you have a motion you want the character to do, so the system goes over the animations and gets the best animation to match that movement. To do the search of the pose there are multiple ways, but most of them use the same basis:



```plaintext
> Position
> Speed
> Desire trajectory
> Foot position
> Facing direction
```

With that and more data, we assign a cost to each candidate, and we just choose the one with the smaller cost.

That sounds easy, and it is. That part is simple, as long as you have the data, the database, and something that allows you to switch to the winner candidate.

The problem comes from the next questions: what to track? How important each feature is? How precise you want the system to be? Where do the feet come into place? Are we overshooting or undershooting the movement? .... and many more.

Existing tools do a great job at considering many of these things, and allow you to add more into the place to fit your systems. So if you have the change to work using one of those I highly suggest you do. Otherwise, you will have to match that movement the painful way... yea doing math.

## Why me?

Well, I love doing tools, pipelines, automations and so, and my boss is getting quite comfortable asking insane stuff to me... It doesn’t help that I keep getting this insane stuff done, that only fuels his next request.



## Why not a pre-built system?

In simple terms there are 2 main reasons:

1. I was done with my project, so I had time to play around.
2. We are on an outdated version of Unity and we have a Mocap Studio.

So with that in mind we wanted a clean pipeline, and adapted to our use case, not a generic system made to work and maintain by experts. Also job security.

Now for real, we also need a system that was made to fit the mocap data, without the need to doing heavy post-processing, chopping, and clean up. This because we don’t have that kind of time… Yet.

Then this system has to work on the outdated version of Unity, and be fast enough we can use it for VR applications, without killing the frame rate.



## What was the initial scope?

Let’s solve this question, the scope was ~~over-scope the shit out of this.~~

Now for real, I focus a lot of my time to test and read a lot before I choose one scope. I figured it was faster to just go within a dev environment, learn and then nuke that code, before doing the next prototype.

Then when I had learned I did a simple scope:

1. **Simple locomotion:** No falling, jumping, or random violence.
2. **Long raw mocap clips:** I was not cleaning that, and making loops or clean starts and stops.
3. **Offline baking of data:** Extracting data into flat arrays had to be done in the editor, not at runtime.
4. Simple to test and iterate: I was not going to depend on code changes or manual work, so I added tooling and exposed development data to accelerate my development.

With that in mind, now we need some test Mocap, good news I am one of the Mocap "experts" and capable of doing almost everything. But I needed the help of someone else as I was the one in the skin-tight bodysuit cover in reflective balls... 

I wanted to have enough data so we didn't have to do another session for this too soon. So we recorded the following:

1. **Walking:** starts, stops, idles, turns, strafing, and changes in speed.
2. **Running:**  starts, stops, turns, strafing, walk-to-run, and run-to-walk transitions.
3. **Turning:** turning in place, turning while moving, and different turning speeds.
4. **Crouching:** same as the other ones plus transitions between crouch and standing.
5. **Violence:** we do not talk about this one, or HR may start asking questions.

And with that, we had a good base set to make out first scope. Yup, a system that allowed us to use several motion types, and toggle between them.

## Building the database

With that in mind, we are ready to match our motion.

Well... almost. Because Unity does not understand questions like:

> "Can you find the part where Simon is walking forward, starting to turn left, with one foot planted and the body already leaning into the turn?"

It sees an `AnimationClip` and a bunch of key frames with vectors and quaternions. So before searching anything at runtime, I needed to turn the raw mocap into data the computer could compare.

So we needed to define our data.

```
Frame:
> Current time
> Tag
> Close to the end of the clip?

Root Motion:
> Current position and rotation
> Future trajectory
> Speed

Feet:
> Current Position and Rotation
> Speed
> Grounded?

Hands, Chest, Hips, Head:
> Current Positions and Rotation
> Speed
```



## Baking the mocap data

For every mocap clip, I create a baked pose asset that contains the data for every sampled frame. Yea the data I explained before.

For our system I bake the data at 10Hz using the editor tools, then our team (For now just me) can go and place tags on the animation to make the system more accurate, block sections that make no sense, and tag sections that might just be a different locomotion type.

The source clip is still one long recording. I do not cut it into a clean walk loop, run loop, turn-left clip, and thirty-seven transition clips. That was one of the main reasons for building this system.

The matcher should be able to jump directly into any useful point in the recording at runtime.

The baked data is stored in flat arrays. Not because flat arrays are beautiful, but because searching thousands of tiny objects every few milliseconds is a good way to make the profiler start sending threatening messages, and me to get motion sick on VR.



## Filling the database

Now that we have all of our data in a nice clean asset, and we have a database. We need to fill the database with the assets. But here is where things become a bit more complex.  

Each asset contains multiple frames, more specific, we have 10 times as many seconds in a clip. That doesn't sound too big until you do the math.

```
WalkingClip:
  Length: 180 seconds,
  FrameCount: 5400 (30 fps),
  SampledFrames: 1800

RunningClip:
  Length: 204 seconds,
  FrameCount: 6120 (30 fps),
  SampledFrames; 2040
```

Just in those 2 clips we have almost 4000 sampled frames containing all the data we need to check if that animation will work for us. While the data is fantastic, and we try to minimize the amount of it, that is still too much, so we want to have only the necessary data.

There are multiple ways to do this, but the way we chose to do this is to have a Dynamic Database. This means we can add and remove clips from it at runtime, allowing us save on searches by reducing the amount of sampled frames.

There might be better ways, but due to this system being done by me, and I don't know everything in this world, this is the way we are doing it now.

## What does the matcher need?

The matcher is the system that we use for choosing the best animation, It is responsible for taking all the values at runtime and then searching the database to get an animation.

At runtime the matcher needs two things:

1. What is the character doing now?
2. What does the character want to do next?

A live pose sampler captures the current character in the same format as the baked frames. Then a trajectory source predicts the future movement.

For the current version, the trajectory comes from a `NavMeshAgent`. It provides the desired speed, facing direction, and several future points along the real navigation path. Giving the matcher a more complex enigma so solve.

That means the matcher is not simply asking:

> "Which animation moves forward?"

It is asking:

> "Which animation starts from a similar pose and follows this actual corner without running into a wall?"

We obviously had to make this system work with the future. So instead of locking everyone to use the nav agent, we just have a cool interface that allows use to improve the system in the future to add things like player movement too. (Yea... we are using it for NPCs only, we all know VR characters have no legs)

## The cost function

And here we go, the way to calculate what is the current cost of any sampled frame. This is what helps us to define and adjust what we want the matcher to focus on.

We use a simple cost analysis, the smaller the value the better the animation... But how? Well, the way we use it, is that we have defined what each thing cost, the higher the value of something the more important it becomes. 

The total cost has three main parts:

```text
Total Cost =
    Current Pose Cost
  + History Cost        
  + Trajectory Cost     

```

### Current pose

This compares the candidate frame with the character currently on screen. It checks root movement, body pose, hands, head, feet, and foot contacts.

My first simple version mostly cared about trajectory. It moved in the right direction, which was nice. The legs, however, were making their own decisions.

A frame can have the perfect future movement while having a completely incompatible pose. So the system also needs to ask whether switching into that frame will look reasonable.

### History

A single pose is not always enough. Two frames can look almost identical, but one may be entering the pose while the other is leaving it.

So I also compare a small sample from the recent past. That helps preserve the motion phase and reduces transitions where the current frame matches, but the direction of movement does not.

### Trajectory

The trajectory cost compares where the desired path says the character should go with where the candidate animation would actually take it.

For each future sample, I compare:

- Position.
- Facing.
- Speed.

Getting the right future trajectory matters a lot because a frame that looks perfect for the next tenth of a second but runs into a wall half a second later is not actually a good match.



### Adjusting the cost

Now that we know what we are tracking, we need to adjust the cost of each thing. So I exposed all the costs to the editor, so I can run tests, make changes, and exports fast, and without changing the code.  

Here you are trying to balance between plenty of things. How accurate it is to follow the trajectory? How important are the feet? Is the speed more important than the path? Do we care about the current tag more than the position of the feet? And so on. 

Now there is always the question of how much of the problem is my values on the cost function and how much of it is lack of animation data. So keep that in mind if you are having problems with it.

## Searching the database

Ok, now we have cost, a database, a matcher, and so. But we haven't talked about searching the database.

The matcher has a fixed search Hz rate. We don't want to read the database every frame, we might want to read it every couple of frames, but then we are dependent on the frame rate. So what we do is search it X times per second. As an example 10 Hz, or 10 times per second.

Now how do we go over all the data? Some people have filtered data sets that they can search by filtering important data, and then only going over that. But we are not that fancy. 

We just go for it.... Yup, Brutforcing our away into motion matching. 

This means that every search tick we go over all the data. That why it was important for us to not have too much data on the database. Otherwise, the performance would have been pretty bad

Now, I expected this to be the part where I needed a very clever search structure with clustering, nearest neighbours, and enough math to make the system look important.

But with a few long clips sampled at a reasonable rate, the database only contains a few thousand candidates, flat data, no allocations, and searching around ten times per second was fast enough... just with one caveat, we had to multi-thread the search so when we have 10-15-40-60-90 character in the scene, the motion matching didn't kill our frames.

Now before anyone tells me this is the wrong way because it is too simple and inefficient, A complicated solution is not automatically a better solution. Sometimes it is only a more complicated bug. And remember that I have to maintain this system, So a simple and elegant brute force system was perfect. Also thanks to the simplicity we can multi-thread the search and math.

## Preventing the matcher from panicking

Mathematically the best frame is not always worth switching to. If the matcher changes animation whenever it finds a slightly cheaper frame, the character constantly cross-fades between nearly identical options. It looks less like natural locomotion and more like the character is reconsidering every life decision.

To prevent this, I had gone for a time locking feature to prevent switching too fast. This was working ok… but had its caveats, first, if we switched to another pose we were locked for 0.3 seconds, that doesn't sound as much, but it affects how the character moves. So I needed a better solution.

First we remove the time lock. Then I uncap the animation tracks for blending, to allow the system to use as many animations as it needed to blend into something that worked for it.

After this, we were having pretty good results, but sometimes we were getting quite a few animations... around 20 being blended to match something. This is not that bad, but we could do better. So we added a minimum improvement from current value.   

To explain this, we are going to assume that the current pose value is 0, so we only care about how good it follows the trajectory, and matches the speed. Then we will compare that value with the best candidate to switch,  if the best candidate is more than 20% better we switch. 

```
Valid Switch:
> CurrentPose Cost: 30 // cost of cotinuing on this animation
> BestCandidatePose Cost: 24 // Cost of switching + trajectory and speed

Invalid Swith:
> CurrentPose Cost: 30
> BestCandidatePose Cost: 28
```



## Playing the selected frame

The winner may be any frame inside a multi-minute clip. To play it, I use Unity's Playables API with an n-slot mixer. We used to have only 2 channels for mixing, but we update it to around 15, and we can add more any time, to prevent hitches when having animation switches happening too fast.

When switching:

1. The new clip starts at the selected time.
2. The previous clip keeps moving.
3. Both animations cross-fade to the new clip.
4. The old slot is cleaned and ready for the next switch.

Keeping the outgoing clips moving is important. Freezing it during the blend makes the character look like it briefly got caught on invisible furniture.

The blend duration changes slightly depending on the pose difference. A close match can blend quickly. A worse match gets a little more time to hide the evidence.

## Root motion

I do not use Mecanim's root-motion delta as the main movement source.

Our Motion matching jumps to arbitrary timestamps, and asking the normal root-motion system to behave perfectly after teleporting a playable into the middle of a long clip felt a bit optimistic.

Instead, the baker stores root velocity and rotation data. Every frame, the system samples that data and moves the actual character transform. During a cross-fade, the outgoing and incoming root velocities are blended with the same weight as the visible pose. That keeps the animation and the actual movement from disagreeing about which clip is currently in control.

## Feet, obviously

At this point, the character can select animations and follow the path. So naturally, the feet are still wrong.

So to correct that the system stores whether each foot is grounded. At runtime, grounded feet are checked against the real terrain and receive a small vertical correction through two-bone IK.

The correction is intentionally minimal. I previously experimented with more advanced stride retargeting, predicted footsteps, hard locking, and hip compensation. Those versions were smarter. [(See what Valve did for Half-Life Alyx)](https://media.steampowered.com/apps/valve/2021/Half-Life_Alyx_Locomotion_Slides.pdf)

They also created drifting feet, floating characters, snapping, and knees exploring exciting new directions. Not that the other people who did similar system were wrong, I am just not that intelligent to make it work at scale.

Our final system keeps the original mocap motion and only corrects the difference between the animation's ground and the real ground. On flat terrain, almost nothing changes. On a slope or step, the leg adjusts just enough to reach it.

## The tools mattered as much as the matcher

I ended up making many editor tools during this time. Some to debug, some to preview, some help set up data, others to import the data, and many more.

But not all stayed with us. I just used them to get the data I needed at the time. Except for 2.

The first one previews, allows tagging data, and bakes the mocap clips. It can display trajectories, velocities, foot contacts, tags, and other useful information from the mocap clips.

The second is the Test runner, and I mean it, it creates the perfect environments for testing. Allowing me to analyze data, check how consistent things are, and how small changes can affect the matcher. It also makes it really simple for me to showcase the system to my coworkers.

It tracked:

- Which frames were selected.
- When switches happened.
- The path the character followed.
- Any obvious movement or animation problems.
- The matcher settings.
- IK and Pose data.

And you may ask why spend time making that instead of making the system better? Well, without that information, tuning becomes:

1. Change a number.
2. Watch the character.
3. Decide it feels better.
4. Try to figure out if the problems are me.
5. Forget what it looked like before.
6. Repeat until confidence replaces evidence.

The debugger made the system understandable, while making testing simple and repeatable. That may have been more important than any single feature in the cost function.

## So, was it worth it?

For most projects, building motion matching from scratch is probably not worth it. If an existing tool supports your engine version, animation pipeline, and target platform, use it. For us, the situation was different.

We had:

- Our own mocap studio.
- Long raw recordings we wanted to preserve.
- An older Unity version.
- VR performance requirements.
- Several future simulator projects that could reuse the technology.

So yes, for us, it was worth it. Not because motion matching is magical.

It is not.

It is a large collection of small systems that must agree about bones, spaces, timing, movement, blending, contacts, and what "good" even means. But now we can add new mocap, bake it, place it in the database, and let the matcher use it without rebuilding an entire locomotion state machine.

And more importantly, when the character makes a terrible decision, I can now see exactly why. Which is useful because it still makes terrible decisions sometimes. Just like me.



## References and Useful links:

- [https://www.gdcvault.com/play/1023280/Motion-Matching-and-The-Road](https://www.gdcvault.com/play/1023280/Motion-Matching-and-The-Road)
- [https://www.ubisoft.com/en-us/studio/laforge/news/6xXL85Q3bF2vEj76xmnmIu/introducing-learned-motion-matching](https://www.ubisoft.com/en-us/studio/laforge/news/6xXL85Q3bF2vEj76xmnmIu/introducing-learned-motion-matching)
- [https://github.com/ubisoft/ubisoft-laforge-animation-dataset](https://github.com/ubisoft/ubisoft-laforge-animation-dataset)
- [https://www.gameanim.com/2021/10/09/character-locomotion-in-half-life-alyx/](https://www.gameanim.com/2021/10/09/character-locomotion-in-half-life-alyx/)
- [https://www.ubisoft.com/en-us/studio/laforge/news/1ERUZiYmmtUBYZ4KvVvmFP/robust-motion-inbetweening](https://www.ubisoft.com/en-us/studio/laforge/news/1ERUZiYmmtUBYZ4KvVvmFP/robust-motion-inbetweening)
- [https://static-wordpress.ubisoft.com/montreal.ubisoft.com/wp-content/uploads/2020/07/09154101/Learned\_Motion\_Matching.pdf](https://static-wordpress.ubisoft.com/montreal.ubisoft.com/wp-content/uploads/2020/07/09154101/Learned_Motion_Matching.pdf)



&nbsp;

&nbsp;