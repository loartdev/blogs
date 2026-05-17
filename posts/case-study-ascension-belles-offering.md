---
title: "Case Study: Ascension Belle's Offering"
date: "2024-11-16"
excerpt: "Explore the making of Ascension: Belle's Offering, a 2D platformer crafted in just three days for Speed Jam"
category: "Game Dev"
tags: ["game-development", "game-design", "indiedev", "gamedev", "game-jam", "game-maker-2"]
featured: false
bannerImage: "https://cdn.hashnode.com/res/hashnode/image/upload/v1731718271336/93a8e03f-a46f-4234-9b1d-e4bdf7698e00.jpeg"
---

## **Overview**

[***Ascension: Belle's Offering***](https://www.loart.dev/projects/ascension-belles-offering) is a 2D Platformer I developed with the help of [**o0shortcake0o**](https://o0shortcake0o.neocities.org/) (an excellent Game Dev and Artist) for [Speed Jam #5](https://itch.io/jam/speedjam5). This was a short game jam; we only had three days to do a complete game that could be speed run, with the topic “ascension.” We instantly came up with the idea of a player trying to climb using grappling and jumping in platforms. This idea was further developed during the game jam, and we ended up with Belle, a soul trying to escape Hades to Olympus by jumping and grappling her way to the top before her time ran out. We also decided to use the Game Jam sponsor LootLocker, and we used their leaderboard API to allow the player to save time and appear on the leaderboard.

![Ascension Belle's Offering Jumping and Grappling](https://images.prismic.io/loartdev-v2/ZpWRYB5LeNNTxK7z_2024-07-1517-01-51-2-.gif?auto=format%2Ccompress&fit=max&w=1920 align="center")

## **Game Description**

The player plays as Belle, a soul trying to escape Hades. To do so, the player must navigate through three zones, Hades, Overworld, and Olympus, while collecting offerings for the gods. Each zone has its unique obstacles, enemies, and challenges that add depth to the gameplay, The game use time as a score and punishment for the player, while rewarding precision, timing, and strategic use of the grappling mechanics.

## **Development Journey**

### **Initial Concept**

When we started the Game Jam, we faced the first challenge: figuring out what we would do; we needed a game that aligned with the theme of “Ascend” while keeping the aspect of speed running. The idea we came up with was to create a simple platformer with 2 main mechanics, jump and grappling, where the player had to **ascend** through various levels. We wanted a simple and responsive control scheme to support the fast-paced gameplay and let the users improve with practice. As this was our first Game Jam, we knew we needed to keep the project scope low compared to other projects we had done before so that we could have a playable game within the limited time frame. We inspire the game's narrative by combining Greek mythology, religious themes, and Minecraft, and we came up with the narrative where Belle ascends from Hades to Olympus.

![Ascension: Belle's Offering - development process (ART)](https://images.prismic.io/loartdev-v2/ZuJ5rxoQrfVKl_9D_abo-dev-1.png?auto=format%2Ccompress&fit=max&w=1920 align="center")

### **Technical Challenges**

When we did the Game Jam, I had almost no experience doing 2D platformer, grappling mechanics, or working in GameMaker, so I had to learn all that in 3 days while also developing the game. This was a big challenge, mainly because my experience and expertise were in Web development, Unreal Engine, 3D, and Multiplayer Games. The good news was that I am pretty good at coding and learning from the documentation itself, allowing me to start working on the project quickly (if you want to learn something, challenge yourself to do that thing you don't know; it works).

To make my life harder, I decided to use GameMaker's physics system, and if you know something about this, you might know how difficult to use this system is. Doing a smooth movement system was a priority, and the grappling mechanic was a core element, so iteration and problem-solving on these elements were crucial for the game. Constant feedback helped shape these mechanics and spot most of the issues so I could solve the problems and improve the system.

Another problem we faced was SFX, which was mainly our fault for not testing this properly before submitting it. This made the SFX too loud, making the game experience quite problematic for the people who did not disable the SFX. If this wasn't bad enough, this issue was solved a couple of minutes after the Game Jam submissions closed; the solution couldn't be uploaded until the voting period ended.

### **Gameplay Design**

The game's core mechanics revolved around movement, grappling, and jumping. So, one of the leading design goals was to ensure these game elements felt satisfying.

*   **Jumping:** The jump was one of the most essential parts of the game; we need it to feel responsive. To do so, we added two systems, **Coyote Time** (allowing players to jump a few frames after leaving a platform) and a **Jump Buffer** (allowing the jump command to trigger once the player lands), making the game feel smoother and more responsive to the player.
    

![a coyote holding a sign that says " that 's all folks "](https://images.prismic.io/loartdev-v2/ZuJ_ExoQrfVKl_9t_wile-e-coyote.gif?auto=format%2Ccompress&fit=max&w=1080 align="center")

*   **Grappling**: The grappling mechanic was tricky to fine-tune. We needed the grappling to reach across the screen but not pass through objects, so the player had to use other strategies. The feedback from the play testers pointed out that it was too hard to use, so we increased the hitbox.
    

![Case Study: Ascension: Belle's Offering — grappling and time ](https://images.prismic.io/loartdev-v2/ZuJ5qxoQrfVKl_8-_abo-dev-.png?auto=format%2Ccompress&fit=max&w=1920 align="center")

### **Time Constraints and Iteration**

Due to the short time we were facing, rapid prototyping was essential, and it took only one day for us to have a playable demo and start applying the feedback we got from our friends. Most early community feedback was positive, particularly regarding the **art style**. However, we received little feedback about the gameplay at this stage.

![Comments on the game page](https://images.prismic.io/loartdev-v2/ZuJ5pxoQrfVKl_86_abo-dev-7.png?auto=format%2Ccompress&fit=max&w=1920 align="center")

We made it to the Game Jam's deadline on time, just for this one to be extended due to [Itch.io](http://Itch.io) sever issues. Instead of just crossing our hands, we added some extra polish to the game, improving menus, sound controls, and more. But these last-minute changes were a mix of exhaustion and excitement, allowing us to add many things, including **bugs**, the most noticeable being the extra loud **SFX** due to the addition of the sound controls.

### **Post-Submission Fixes and Improvemen**[**ts**](https://loartdev.itch.io/ascension-belles-offering)

This was our first Game Jam. Our expectations were to be ranked 208 out of 209, but due to the fantastic work my partner and I put into the game, we ranked 54 out of 209 submissions. We were more than happy with this result, but we knew we could have done way better. There were many areas for solving things like the SFX being too loud, adding the Coyote Time and Jump Buffer, adding a story, making a better tutorial, etc… The feedback we got during the voting period showed us some of them. We spent most of the time solving this, adding the mechanics, and improving the game, knowing that these changes wouldn't change the outcome of the vote (mainly because we couldn't update the game during the voting time). However, we weren't doing it to get a better ranking; we were doing it to become better Game Devs.

#### **Improved Jump Mechanics**

Post-jam, we implemented **Coyote Time** and **Jump Buffering** to refine the jumping system, enhancing the player's control and responsiveness.

#### **Grappling Adjustments**

We expanded the grappling area's hitbox to make the mechanic more intuitive. This adjustment made the gameplay smoother and allowed players to grapple more efficiently.

#### **Leaderboard Adjustments**

To balance the competition, we fixed bugs where players only needed to collect half the items to appear on the leaderboard, which led to more fair and accurate scores.

#### **Aesthetic Adjustments**

We outlined the collectibles to make them stand out better and resolved overlapping UI issues that were previously disrupting gameplay transitions.

### **Art and Music Collaboration**

The game's visual and sound elements were created by **o0shortcake0o**, who created vibrant art assets and composed adaptive music that changed between levels. Each zone had its variation of the core music, where different instruments were used to signal progression through the levels.

## **Key Features**

### **Collectibles and Leaderboards**

Collecting items is an extra challenge that we also used to make the players explore the map; this element also had value for the story of the game, and while it wasn't explicitly in the game, we wanted to add it from the start.

The leaderboards track the overall time, the deathless runs, and the full completion, including getting all the collectibles. This added replayability and encouraged the player to improve their performance.

![Leaderboards](https://images.prismic.io/loartdev-v2/ZuJ5lxoQrfVKl_8q_abo-dev-20.png?auto=format%2Ccompress&fit=max&w=1080 align="center")

### **Zone-Based Difficulty**

The game's difficulty ramped up with the inclusion of spikes, birds, and clouds as environmental hazards, designed to challenge the player's mastery of movement and timing. Each enemy type and platform mechanic introduced new layers of complexity, pushing players to think strategically.

### **Adaptive Music and Checkpoints**

The adaptive music, which shifted in tone as players progressed through the zones, helped create an immersive atmosphere. Checkpoints were introduced to reduce frustration, ensuring players didn't lose all their progress after dying.

## **Key Takeaways**

The biggest takeaway from developing *Ascension: Belle's Offering* was understanding the importance of **feedback and iteration**. The gameplay was rough around the edges during the jam, but we improved the game's mechanics and systems based on the feedback received. This experience also taught us the balance between creating content that matches a theme while delivering tight, enjoyable mechanics that players can easily pick up but require skill to master.

Our first game jam experience was both challenging and rewarding, providing valuable insights into time management, collaboration, and rapid development under pressure. These lessons will be instrumental in future projects as we strive to create more polished and engaging games.

## **Future Plans**

### **Polish and Updates:**

We plan to continue improving the game based on community feedback. Adding new zones or refining existing mechanics could provide even more replay value.

### **Potential Expansion:**

Depending on reception, we may explore expanding the game's narrative, introducing new levels, or developing a broader story surrounding Belle's journey through the afterlife.

![Concept art by o0shortcake0o](https://images.prismic.io/loartdev-v2/ZuJ5lhoQrfVKl_8p_abo-concept-4.png?auto=format%2Ccompress&fit=max&w=3840 align="center")

Concept art by [o0shortcake0o](https://o0shortcake0o.neocities.org/)

Overall, the journey of creating *Ascension: Belle's Offering* during a game jam was a significant learning experience and helped us grow as developers. The combination of art, sound, and mechanics we achieved in such a short time makes us proud, and we are eager to see where our development journey takes us next.

You can play Ascension: Belle’s Offering by clicking [here](https://loartdev.itch.io/ascension-belles-offering)!
