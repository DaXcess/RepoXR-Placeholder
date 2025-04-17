# R.E.P.O. VR Mod Placeholder

This repo(sitory) is a temporary placeholder for the R.E.P.O. VR mod

Progress is slower than usual, as I do not have a lot of free time on my hands, and the free time I do have, I have to balance between building the mod, playing other games (Outer Wilds is pretty awesome), and playing games with friends (ironically, R.E.P.O. is one of them).

The R.E.P.O. devs decided to reinvent the way UI interactions work (manual screen cursor to canvas position checking, instead of just using an onhover event), so that's a bit annoying to deal with right now. I have some of the basic interactions already set up, but some buttons do not work yet (can't select an existing save).

I'm currently working on implementing the rebinding system. Yes, I know that the game has a built-in rebinding system, but it's kind of meh. The settings menu for it is not dynamic, and they manually create all InputActions using `new InputAction("<binding>");`. I'm working on rebuilding this to just use an `InputActionAsset`, which is eventually placed in a `PlayerInput`.

That's it for now.

# March 28th 2025

Okay so the rebinding system has been fully implemented (I think). Also fixed the scrollbars not working with the VR inputs.

Now on to the more difficult stuff: in-game hands/arms. Not really too sure how I am going to implement this, but I'll see what I can extract from the base game.

# April 1st 2025

Haven't done much of anything over the weekend, played a bit too much Outer Wilds (great fucking game much love :heart:, finished both the base game and DLC).

I did start some work on the arms and got a small tiny very small demo running in-editor, but it doesn't work in-game. Most likely Unity's Animation Rigging needs additional setup for it to work in a modded setting. Since the arms are very simple anyways, I'm just going to rebuild it in a way that doesn't rely on Unity's rigging system.

# April 4th 2025

[**Look at these fuckin' hands**](https://bsky.app/profile/daxcess.io/post/3lly5pmgens2n)

# April 6th 2025

Watched the Minecraft movie.

"Chicken Jockey!"
"**YEAAAAAAAAAAAAAAAAAAAAAAAAAAH!!**"

# April 14th 2025

Been a while since I last updated this. I made the **HANDS** better, the laser beam now comes from the hand, and the claw properly shows and has a cool animation. I've also been busy getting some of the UI working, though most of it is now in a placeholder position (in front of face with a smoothed canvas). I still need to move a lot of the UI elements to other spots (and I'm not yet 100% certain as to where to put a lot of the stuff, like health, energy, quota, current extract/total extracts, and the inventory). I do have an idea for the inventory though, but I'll keep that a surprise for now.

I've discovered that the game actually uses 3 cameras to render everything: The base camera (which renders the game), a "top-only" camera, which renders certain elements on top of everything else, which is used in the base game to render the flashlight, spewer when it's attached to your face, and the map, and probably a few more things. This camera also contains most of the post processing effects, which makes VR look soo much cooler compared to all the teaser videos out there right now. I'm going to use this camera to render the player rig (arms, flashlight, map, basically everything from the base game + arms), so "technically" you can't pass your arms through a wall, well, I mean you can, but it always renders op top, so it never cuts off your arms. Last but not least is the "overlay" camera, which I use to render the UI on top of everything else. This means that unlike in LCVR, or CWVR, the UI is **always** visible on top, cannot pass through walls, and doesn't get the post processing effects (which would make some of the UI unreadable). Gotta love the much simpler render pipeline this game uses.

One of the bigger UI things I fixed was the item scanner/discoverer and the value text. In the base game, whenever you discover an item, a box is drawn around this item to indicate to you that you have discovered (or re-discovered) this item. In the base game, they use some cool trickery to calculate the bounding box of the item in screen space so the box is always drawn perfectly over the item. I tried to do the same thing in VR but I couldn't get it to work for the life of me. The base game has it easier because they can do "simple" screen space calculations, but that just does not work in VR, plus I'm also using world space UI instead of screen space UI, which makes all these calculations vastly different. I'm sure there is a way to calculate rectangular, non-rotating bounding box coordinates given an object's position, object's bounds and the camera position, but that's some math I know absolutely nothing about. What I've done now, is just create some UI with the size of the bounding box, and made it always be rotated towards the camera. It works well enough, but fails to encapsulate the object if you're not looking straight at it.

The more code and patches I write, the less multiplayer friendly the mod in it's current state becomes. At the time of writing, the game is on version 0.1.2, and unless Semiwork has changed this in future updates, all of the physics calculations on held items are done by the host. Picking up items in VR always works fine, since the VR client just syncs the "I want to hold this item here" position with the host, however rotating items becomes an issue. The host calculates a lot of these rotations using the camera (aka head) rotation of the player who is holding said item. This means that if a VR client plays with a host that does not have the mod, items will rotate based on where the VR player is looking, and not how they rotate their hands, which makes for quite weird item behavior. It's not detrimental though, and it doesn't make the VR mod completely incompatible with vanilla, but this does mean that it is almost **required** for the host to have the VR mod (whether they themselves are in VR or not) for all the VR interactions to work properly.

Even Zeekerss complained about this behavior of the game, but for different reasons. If all the calculations are always done by the host, it means that if either you or the host has bad internet, items will lag all over the place, even if you're the one holding it. Semiwork could change this by giving ownership of held items to the player holding them, though this could complicate things since multiple players can hold items at the same time. Maybe make the first player who grabs it owner, and transfer ownership to another player holding it when the original owner lets go?

Oh I almost forgot, I also was able to almost complete the entire tutorial, which now also shows the VR controls instead of PC controls! The only part that does not work yet is the inventory, which I do have plans for, but I think I'm keeping that for later. I first have to fix the pause menu, some weird UI stuff when dying, a lot of weird behavior with the rolling drone (it flings the items towards you, which is hilarious, but kind of impractical), and quite a bit of multiplayer fixes (I don't mean stuff like arm sync, I mean like everything just falling apart in multiplayer right now).

**EDIT:** I also enabled [Discussions](https://github.com/DaXcess/RepoXR-Placeholder/discussions) on this repository (and disabled issues, since there is no code), for if anyone has any burning questions they would like to ask or things you might want to suggest.

# April 15th 2025

I've been working on restructuring a bit of the code that deals with initialization and early-scene shenanigans, because this gives me much more control over specifically the loading screen (y'know, that little sprite animation with the semibot inside the truck, which smashes into the level once the map has loaded, that thing). That loading screen is visible during the transition between scenes, and I really really wanted to make it look cool and seamless in VR.

My goal was first to get the loading screen to even show up in the first place. That was quite easy (though it did require that restructuring I was talking about earlier). But next, I wanted to figure out how I can make it look like the loading screen doesn't move, even though during scene loads the camera position may be vastly different from the previous scene, or even worse: the camera might move after a new scene was loaded, making the loading screen move and jitter around, or even worse, be flung so far away it becomes invisible. I was banging my head around trying to fix this last night for so long, but to no avail. Then I woke up today and was like "why the fuck don't I just parent the loading screen to the camera's parent". And like magic everything works now. Even though the seamlessness of the loading screen is such a small detail that I doubt people would even notice (unless you have read all of this), it's just *\*chefs kiss\**. It makes it look so much cooler compared to my solution last night which had the loading screen jump around a lot during the scene transition.

![image](https://github.com/user-attachments/assets/9e194cd4-761f-45b2-a63e-6378cdf63db2)

# April 17th 2025

Aaaaand 6-DoF movement is in (and was scarily easy to implement)! I really expected to run into the same problems that I had with Lethal Company and Content Warning, but the way this game is structured has made it really eays to implement the room-space movement logic (in fact, it's only 12 lines of code, if you take away the comments and empty lines). I am a little suspicious about it, because it was so easy, but hey if it holds up I'm totally fine with it.

Some small improvements have now also been added, like resetting height (and it properly accounting for crouching and tumble), snap/smooth turning (which is already configurable), and hiding the hands on death. I've also added the color change that happens when you get hurt or activate an upgrade (though I haven't tested the upgrade colors yet). It was also time to fix a little issue with the smoothed canvas (the one that currently displays all of the HUD), where it would rotate very weirdly if you died and we're in the "top-down view" spectate state. Since the canvas is constrained to only rotate on the X and Y axis, it will not tilt with the camera, however that breaks if the camera is suddenly rotated by 88 degrees on the X axis (something that happens when you die).

Speaking of dying, the death sequence where you view the map from above for a few seconds now also works properly in VR. It didn't work out of the box, because the game would set the MainCamera Z position to -50, which doesn't work, since the MainCamera has a tracker that keeps it up-to-date with tracking data from the headset, so this position is instantly reset back to *nearly* 0, 0, 0 (depending on where you are standing). So I used one of the camera's parents to set this -50 Z position, and as a final touch, rotate this object so that at the time of death you look straight towards your death point (no matter how much you have rotated your head).

Next up I'm probably going to fix some raycasting issues with enemies. Currently in VR you can literally just look straight at Thin Man and Robe without them activating, probably because the main game uses CameraAim to detect if you're looking at them, but CameraAim is *basically* disabled in VR (technically, it is replaced by VRCameraAim, but that only handles rotation offsets, like snap/smooth turn or the music box trap item, not the actual rotation of your head). So I will probably have to make these checks look for the actual main camera instead of the CameraAim rotation.

After that I will probably start moving some UI elements around. Currently most of the UI (except for the item scanner, and value overlays) are just in front of your face. I do not really know where I want to place all the UI yet (like the health, stamina, etc), but I have some ideas I will experiment with.
