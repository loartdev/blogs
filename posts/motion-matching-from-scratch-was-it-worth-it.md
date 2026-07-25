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



Everyone describes as this magical solution that you feed animations too and it just works. No need of complex blend trees, state machines, and so. Just full animation and root motion.



But you know what people don’t talk about?

HOW PAINFUL it is to get right. Mostly when your boss just comes to you and mentions it and you just go and say:



> Pff, I can do that with my eyes closed.



Now I have to implement it... and before you think I had any idea on how these systems work, I didn’t. So I had to get my ass on my desk and read papers, and more papers, and yea, long GDC talks. And at the end of all the, I learned how hard this systems actually are.



## What is Motion Matching really?

As the name suggest we are matching the movement of the character using the animation. Now that might be hard to imagine when you know that we use root motion, so the animation moves the character....  

How ever, the core is you have a motion you want the character to do, so the system goes over the animations and gets the best animation to match that movement. To do the search of the pose there is multiple ways, but most of them use the same basis:



```plaintext
> Position
> Speed
> Desire trajectory
> Foot position
> Facing direction
```



With that and more data, we assign a cost to each candidate, and we just choose the one with the smaller cost.

That sounds easy, and it is. That part is simple, as long as you have the data, the database, and something that allows you to switch to the winner candidate.

The problem comes from the next questions: what to track? how important each feature is? how precise you want the system to be? Where do the feet come into place? are we overshooting or undershooting the movement? .... and many more.

Existing tools do a great job at taking into account many of this things, and allow you to add more into the place to fit you systems. So if you have the change to work using one of those I highly suggest you do. Otherwise you will have to match that movement the painful way... yea doing math.

## Why me?

Well I love doing tools, pipelines, automations and so, and my boss is getting quite comfortable with asking insane stuff to me... I doesn’t help that I keep getting this insane stuff done, that only fuels his next request.



## Why not a pre-built system?

In simple terms there is 2 main reasons:

1. I was done with my project, so I had time to play around.
2. We are on an old version of Unity and we have a Mocap Studio.

So with that in mind we wanted a pipeline that was clean, and adapted to our use case, not a generic system made to work and maintain by experts. Also job security.

Now for real, we also need a system that was made to fit the mocap data, without the need to doing heavy post-processing, chopping, and clean up. This because we don’t have that kind of time.. yet.

Then this system has to work on the outdated version of Unity, and be fast enough we can use it for VR applications, without killing the framerate.



## What was the initial scope?

Let’s solve this question, the scope was ~~over-scope the shit out of this.~~

Now for real, I focus a lot of my time to test and read a lot before I choose one scope. I figured it was faster to just go with in a dev **environment**, learn and then nuke that code, before doing the next prototype.

Then when I had learned i did a simple scope:

1. Simple locomotion: No falling, jumping, or random violence.
2. Long raw mocap clips: I was not cleaning that, and making loops or clean starts and stops.
3. Offline baking of data: Extracting data into flat arrays had to be done in the editor, not at runtime.
4. Simple to test and iterate: I was not going to depend on code changes or manual work, so i added tooling and exposed development data to speed up my development.

With that in mind, now we need some test Mocap, good new I am one of the Mocap "experts" and capable of doing almost everything. But I needed the help of someone else as I was the one in the skin tight body suit cover in reflective balls... 

I wanted to have enough data so we didn't had to do another session for this too soon. So we recorded the following:

1. **Walking:** starts, stops, idles, turns, strafing, and changes in speed.
2. **Running:**  starts, stops, turns, strafing, walk-to-run, and run-to-walk transitions.
3. **Turning:** turning in place, turning while moving, and different turning speeds.
4. **Crouching:** same as the other ones plus transitions between crouch and standing.
5. **Violence:** we do not talk about this one, or HR may start asking questions.

And with that we had a good base set to make out first scope. Yup, a system that allowed us to use several motion types, and toggle between them.

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

Now that we have all of our data in a nice clean asset, and we have a database. We need to fill the data base with the assets. But here is where things become a bit more complex.  
  
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

Just in those 2 clips we have almost 4000 sampled frames containing all of the data we need to check if that animation will work for us. While the data is really good, and we try to minimize the amount of it, that is still too much, so we want to have only the necesary data.

There are multiple ways to do this, but the way we choosed to do this is to have a Dynamic Database. This means we can add and remove clips from it at runtime, allowing us save on searches by reducing the amount of sampled frames.

There might be better ways, but due to this system being done by me, and I don't know everything in this world, this is the way we are doing it now.

## What does the matcher need?

At runtime the matcher needs two things:

1. What is the character doing now?
2. What does the character want to do next?

A live pose sampler captures the current character in the same format as the baked frames.

Then a trajectory source predicts the future movement.

For the current version, the trajectory comes from a `NavMeshAgent`. It provides the desired speed, facing direction, and several future points along the real navigation path. Giving the matcher a more complex enigma so solve.

That means the matcher is not simply asking:

> "Which animation moves forward?"

It is asking:

> "Which animation starts from a similar pose and follows this actual corner without running into a wall?"

We obiuslsy had to make this system work with the future. so instead of locking everyone to use the nav agent, we just have a cool Interface that allows use to improve the system in the future to add things like player movement too. (Yea... we are using it for NPCs only, we all know VR characters have no legs)

## The cost function

The cause of my pain, the solution to my problems.. Yup, the cost function.

In simple terms, every candidate frame receives a cost. Smaller is better, may the best one win.

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

So I also compare a small sample from the recent past. That helps preserve the motion phase and reduces transitions where the current frame matches but the direction of movement does not.

### Trajectory

The trajectory cost compares where the desired path says the character should go with where the candidate animation would actually take it.

For each future sample, I compare:

- Position.
- Facing.
- Speed.

Getting the right future trajectory matters a lot, because a frame that looks perfect for the next tenth of a second but runs into a wall half a second later is not actually a good match.

## Searching the database

The current matcher uses brute force.

Yes, really.

Every search tick, it checks every valid frame in every baked clip.

I expected this to be the part where I needed a very clever search structure with clustering, nearest neighbours, and enough math to make the system look important.

But with a small number of long clips sampled at a reasonable rate, the database only contains a few thousand candidates, flat data, no allocations, and searching around fifteen times per second was fast enough.

So I kept the boring version.

A complicated solution is not automatically a better solution. Sometimes it is only a more complicated bug. And remember that I have to maintain this system, So a simple and elegant brute force system was perfect. Also thanks to the simplicity we can multi-thread the search and math.

### Basic Filters?

Ok, i know I just said we have a simple and elegant brute force system, But we also needed a way to prevent things like a jump to happen randomly. So I added a Tag system, it allows for both things, tag data that might not be usable, and catalog the data, things like crouching, drinking water, jumping, falling to roll because the mocap actor doesn't know how to. The obvious.

This tag systems sits on the Editor system so that animators and other people on the team can tag sections of the animations, then at runtime you can exclude the data or you can force to play it. Like telling your character to Jump at a ledge, or to fall... That was painful to record.

## Preventing the matcher from panicking

The mathematically best frame is not always worth switching to. If the matcher changes animation whenever it finds a slightly cheaper frame, the character constantly crossfades between nearly identical options. It looks less like natural locomotion and more like the character is reconsidering every life decision.

So the matcher compares the best candidate against simply continuing the current animation.

It only switches when:

- Enough time has passed since the last switch. (I only lock the change for 0.12 seconds)
- The new candidate is meaningfully better.

This hysteresis made a much bigger visual difference than I expected. Motion matching is not only about finding the best frame. It is also about knowing when to leave a perfectly acceptable one alone.

## Playing the selected frame

The winner may be any frame inside a multi-minute clip. To play it, I use Unity's Playables API with a two-slot mixer.

When switching:

1. The new clip starts at the selected time.
2. The previous clip keeps moving.
3. Both animations crossfade.
4. The old slot is reused for the next switch.

Keeping the outgoing clip moving is important. Freezing it during the blend makes the character look like it briefly got caught on invisible furniture.

The blend duration changes slightly depending on the pose difference. A close match can blend quickly. A worse match gets a little more time to hide the evidence.

## Root motion

I do not use Mecanim's root-motion delta as the main movement source. (hance the problem with the root and shit from before)

Our Motion matching jumps to arbitrary timestamps, and asking the normal root-motion system to behave perfectly after teleporting a playable into the middle of a long clip felt a bit optimistic.

Instead, the baker stores root velocity and rotation data. Every frame, the system samples that data and moves the actual character transform. During a crossfade, the outgoing and incoming root velocities are blended with the same weight as the visible pose. That keeps the animation and the actual movement from disagreeing about which clip is currently in control.

## Feet, obviously

At this point the character can select animations and follow the path. So naturally the feet are still wrong.

So to correct that the system stores whether each foot is grounded. At runtime, grounded feet are checked against the real terrain and receive a small vertical correction through two-bone IK.

The correction is intentionally minimal. I previously experimented with more advanced stride retargeting, predicted footsteps, hard locking, and hip compensation. Those versions were smarter. [(See what Valve did for Half-Life Alyx)](https://media.steampowered.com/apps/valve/2021/Half-Life_Alyx_Locomotion_Slides.pdf)

They also created drifting feet, floating characters, snapping, and knees exploring exciting new directions. Not that the other people that did similar system where wrong, I am just not that intelligent to make it work at scale.

Our final system keeps the original mocap motion and only corrects the difference between the animation's ground and the real ground. On flat terrain, almost nothing changes. On a slope or step, the leg adjusts just enough to reach it.

## The tools mattered as much as the matcher

I ended up making many editor tools during this time. Some to debug, some to preview, some help set up data, others to import the data, and many many more.

But not all stayed with us. I just used them to get the data I needed at the time. Expect for 2.

The first one previews, allows to tag data, and bakes the mocap clips. It can display trajectories, velocities, foot contacts, tags, and other useful information from the mocap clips.

The second is the Test runner, and i mean it, it creates the perfect environments for testing. Allowing me to analyze data, check how consistent things are, and how small changes can affect the matcher. It also makes it really simple for me to showcase the system to my coworkers.

It tracked:

- Which frames were selected.
- When switches happened.
- The path the character followed.
- Any obvious movement or animation problems.
- The matcher settings.
- Ik and Pose data.

And you may ask why spend time making that instead of making the system better? Well, without that information, tuning becomes:

1. Change a number.
2. Watch the character.
3. Decide it feels better.
4. Try to figure out if the problems is me.
5. Forget what it looked like before.
6. Repeat until confidence replaces evidence.

The debugger made the system understandable, while making testing simple and repetable. That may have been more important than any single feature in the cost function.

## So, was it worth it?

For most projects, building motion matching from scratch is probably not worth it. If an existing tool supports your engine version, animation pipeline, and target platform, use it. For us, the situation was different.

We had:

- Our own mocap studio.
- Long raw recordings we wanted to preserve.
- An older Unity version.
- VR performance requirements.
- Several future simulator projects that could reuse the technology.

So yes, for us it was worth it. Not because motion matching is magical.

It is not.

It is a large collection of small systems that must agree about bones, spaces, timing, movement, blending, contacts, and what "good" even means. But now we can add new mocap, bake it, place it in the database, and let the matcher use it without rebuilding an entire locomotion state machine.

And more importantly, when the character makes a terrible decision, I can now see exactly why. Which is useful, because it still makes terrible decisions sometimes. Just like me.