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

# April 22nd 2025

Another 5 days have passed and a lot has happened (again). Let's see if I still remember the order of things I changed and added (yes I have already began losing track of time, fun stuff).

**Enemies!**

It was about time to fix some issues with enemies. In VR, the code that would detect if you were looking at an enemy was quite broken, meaning you could just look at Thin Man or Robe without them activating or anything. That has now been fixed. Speaking of Thin Man, you know how when he attacks you your camera will fly towards him for a split second? Well that was the first thing I fixed. The base game uses camera FOV to achieve this desired effect, however editing the FOV is not possible in VR (nor would it look really good, *cough cough* LCVR camera distortion effects). So I decided to do something a little more drastic: moving the camera closer to the enemy to emulate the zoom. This works actually pretty good! There were some issues at the beginning where the audio listener would flip out because of the high speed, but that was easily fixed by pinning the audio listener to the player while the zoom is happening, and place it back onto the camera once it has finished completely. I only need to fix one tiny bug that is still present with this: the player position is considered to be "below the floor" (at least for audio occlusion), so I need to offset it a bit up for it to sound normal again.

Next up, the ceiling eye. You probably know how they work in flatscreen, and unfortunately I have to tell you, they work the same in VR. The VRCameraAim script has been completed and can now be used to make the player force look at any position in the scene. This means that yes, you might be looking straight down to the floor in real life, but the ceiling eye will force the camera to look straight at the eye. For now, this behavior is always enabled, but just like in the main game, the effect is lessened once the eye has snapped you towards it, and the effect becomes even less severe if you move your head (which will reduce the strength of the aim trying to look back at the eye). It is still very much impossible to look away completely though, since the further away you look from the eye, the stronger it pulls you back in. I think the disorientation you experience in flatscreen is nearly identical in VR, which was my goal. At a later time I will probably look into making a setting to make the effect much less severe for people with motion sickness, but for now the eye works almost the exact same in VR as it does on flatscreen.

**Flashlight?!**

I don't know why I kept putting this off, as I knew it would be extremely easy to implement (and it was). I finally put the flashlight on the left hand, and disabled a few scripts on it that prevents it from moving out of your hand (e.g. when sprinting it would move backwards). I only need to disable one more thing, since it still moves out of the hand when standing near a wall, but otherwise the flashlight is now properly "unflattened".

**Where am I pointing to?!?**

For now I've been using debug line renders to be able to see where I'm pointing to using my hands. Since there are no hands, and only arms in the game, there is no indicator of where you are actually pointing towards (only the feeling in your hand). So I've grabbed the crosshair from the base game, and placed it on the floor, no wait the wall, no wait I actually placed it on that item, actually, it's everywhere! I made it so that the crosshair is placed on the surface where your hand is pointing to. This should make it a lot easier to pick up items compared to having no visual clues at all. I do need to fix one tiny thing with the crosshair rotation, because sometimes it looks a little bit strange, but overall it should work pretty well. Maybe I'll add an option to completely disable it for the people who have a feel for their aim and don't need visual aid.

**Where... am I...?**

KEYBINDS! Yes, this game has quite a few keybinds! And my controllers do not have a lot of buttons! The combination of these two observations makes me conclude that we have a problem! In the base game, you have an entire keyboard at your disposal, and you can use "Tab" to open the in-game Google Maps or whatever. I don't think I want to assign even more keybinds to this, so what did I do? I placed the map tool into your hotbar! You can grab the map tool using grip and view the beauty that is 90's digital cartography. And good news for the left handed people: you can pick up the map tool using either your left or your right hand, no configuration needed! Picking up the map tool with your right hand will disable item pickup, since your hand is kind of occupied. Here's where the left hand comes in clutch! When picking up the map with your left hand, you can still grab and move items (just like in the base game). You wil become a lot slower, and you lose the ability to rotate items, and if you sprint the map tool goes away, y'know everything that the base game does as well. This also means that the map is not too overpowered in VR, the only benefit is still being able to look around while using it, but you'll still be slow etc.

**Ransacking the shop**

R.E.P.O. has a shop, it's very cool, you can buy stuff, like guns, and grenades, classic shop stuff really. But, you need a place to store this items. The truck is cool, but impractical, so what if you had pockets? The base game has pockets, but that requires an additional **3** keybinds to operate. There's no way that is going to work in VR, so time for something else. I've already made the map tool physically grab-able (grabbable, grab-able, grab... whatever), so why not do the same with the inventory. When holding an item that can be stored, 3 yellow rectangles pop up in front of the player, and if you release the item while pointing towards one of the slots, it will place the held item in that slot. Instead of using sprites, it will just shrink down the item and place it in front of the player (like a belt kind of thing). So if you inventory is filled, you will have three items next to each other, and then the map tool. Grabbing an item uses the Grab keybind (trigger by default) and not the grip keybind, since the items are held in the same way as valuables. This inventory system is much more immersive than what I did with Lethal (and also more complicated than Lethal) and I really like how it turned out so far. I only need to fix a small tiny little issue where you can put items in slots when you are not supposed to (*cough* *cough* tutorial, *cough* shop). Whether this plays nice with multiplayer, I don't know yet, only time will tell.

**So... what's next?**

If you don't include the few fixes I have to do for the items discussed in this post, I should only be left with the pause menu, and UI placement (health, sprint, etc). After that, I can finally work on the multiplayer support, and if that is added, tested, fixed, revamped, all that stuff, the mod should be, well, finished, I guess! I will still take my time, and not rush anything, as I really hope I can make 1.0.0 a very stable release out of the box! Wouldn't want a Lethal 1.0.0 situation again (multiplayer subsystem died if there were a "lot" (4+) of players).

The pause menu should hopefully be relatively simple. I should just be able to re-use the XRRayInteractionManager, and use camera stacking to darken the entire scene while showing the pause menu. Oh, come to think of it, I also need to fix the "Arrow" UI element (which I think is only used in the tutorial, but I will leave nothing neglected in the VR mod!), since it currently points to the wrong coordinates (probably more screen space coords BS).

The UI elements themselves I'm still not too sure on. I can keep a few select elements on the smooth canvas (the one in front of the face), like the tutorial, tips, the "EXTRACTION POINT ACTIVATED" popup, and a few other misc elements, but I am still not certain as to where I would want to place health and sprint and that kind of stuff. I don't like it when there's a persistent UI in front of me (which is why you also don't have that in LCVR and CWVR). I don't mind popups that go away after a bit, but anything that is always visible should go somewhere else IMO.

And then comes the multiplayer support. I have brainstormed some ideas, but not acted on them yet since I want to finish all the other stuff first. I am most likely gonna hook into the PlayerAvatar PhotonView serialization sequence, and hook that into my own NetworkSystem. I'm most likely going to make it serialize a magic value (to make other clients know that this player has the VR mod), some flags (containing what kind of data is being serialized, if any), and then some data (like "hello I'm VR", or tracking data). This might be a breeze, or it might not be, only time will tell.

Thank you for listening to my TED talk (again).

# April 23rd 2025

Don't worry, will not write a wall of text this time.

I fixed most of the small issues described in the previous post, so the flashlight no longer shoots out of your hand when standing near a wall, the inventory now cannot be used if it's disabled (yes you were able to use the inventory when it was completely disabled before, oops) and made a small change in the crosshair that makes it rotate in a more sane way.

For some new things, I tried looking into the arrow thing, and I noticed that it doesn't even work on flatscreen? I might just completely scrap that from VR as well, but for now I've delayed working on it until I finish the UI revamp. I started working on the pause menu, which I've already got in a working state, though it's pretty early. It doesn't yet darken the screen, and the "highlight" colors are rotated and scaled completely wrong. But hey, at least the semibot follows your "cursor", which is kinda cool!

![image](https://github.com/user-attachments/assets/b3c69dd2-1ec8-4f7a-b52d-db24837caae5)

I'm hoping I can finish the pause menu today, though it's a workweek so I don't have a lot of time to work on it today.

Also, Spraty made a very cool logo for RepoXR, but I'll keep that a secret until later :)

# April 29th 2025

Another week has passed, hmm time really flies, and once again, a lot has happened, so prepare for another wall of text!

![image](https://github.com/user-attachments/assets/3806d6c9-1a55-4360-a508-cb06c3dfa255)

It should be evident by the name of the commit, but there have been a lot of changes in the past 6 days, lets see if I can still remember all of them (by cheating and looking at the commit).

**UI & Pause Menu**

Yes, I have finally decided on a place for the UI. As the player inventory and map tool are located at like hip-level, I thought why not put at least the statically visible (always visible) UI down there as well? So I placed them there for now. Whether this will stay forever is still a mystery, but chances are pretty high that this is how it will work in the 1.0 release. I have also fixed and fully implemented the pause menu (which I did waaay too late to be honest), so I can finally leave levels and the tutorial without ALT-F4'ing the game every time.

**MULTIPLAYER AAAAAAH!**

Yes, it has been added! Multiplayer support is finally in the mod, and so far it's fully feature complete (except for some bugs, which I still need to iron out). The networking system hijacks the serialization sequence in the `PhysGrabber` component, which at first was supposed to be `PlayerAvatar`, however the way Photon does serialization is that it combines all the components from the same GameObject and does all serialization in one swoop, so I need to use the last element in the list, because otherwise I would break communication with vanilla clients (which would cause crashes and VERY undefined behavior). I made the networking system use a "frame" system, where is frame has a specific use. For example, the `Announcement` frame tells all connected clients (that have the mod installed) that the sender is a VR player, and the `Rig` frame transmits the positions and rotations of the hand. I've also reparented the grabber, flashlight and map tool so that they are properly placed on the hands and rotate the same way as the person holding them. The multiplayer support even shows the map tool on the same hand that the player is holding them, whether that'd be the left or the right hand, it re-parents if the hands are changed!

**Hmm enemememies!**

I had already started working on getting the camera effects in for a few enemies, but they have now all been implemented. The Upscream now also properly makes you look at them when it attacks you, and I have also added a new option that will redcude the impact of force-look events, which makes them only rotate you on the Y axis (the same axis used for snap/smooth turning). Currently no other enemies need specialized VR support, so this means that enemy support is done and finished.

**Thunderstore, why???**

So, there was a funny bug during testing, that made it so that if one player pressed the Interact button, all the players holding items would press the interact button. At first I obviously though that I had just messed up a patch on a function that dealt with item activation, and yes when I completely removed the patch, it stopped behaving this way. Obviously that patch needed to come back, so I had to fix it, but when taking a good look at the patch, I did not make any sense at all that this bug would be introduced. My patch didn't touch that part of the code in the slightest, so why does this happen? Well, it turns out, [**there was a bug in BepInEx**](https://github.com/BepInEx/HarmonyX/issues/82) that caused a patch (even when the patch would do nothing) to replace certain instructions incorrectly, causing undefined behavior, and the undefined behavior in my case was that everyone used their items if only one person used an item. Normally, this would just be a case of "ok just update bepinex and be done", but unfortunately **Thunderstore** is still using a **2 year old** version of BepInEx, which still has this bug... So I had to write a patch, that patches the patcher, before the patcher patches my patches. Anyways, it works now, but might cause some funny stuff if for some reason a mod relies on this bug (which would be very stupid but you never know).

**Roomscale improvements yipeeeee**

I've also added roomscale crouching, and made an improvement to roomscale movement. The roomscale crouching improvement also adds several checks for "illegal heights" which will force the mod to reset the height if it exceeds certain tresholds (like peeking through the floor or ceiling). In LCVR, this check was only added much later, so that allowed players to quite literally fly using playspace hacks, but that will not work out of the box in RepoXR (horizontal playspace hacks still work fine, as long as you're not running into walls, but I do not care enough to add checks for this, it's your gameplay anyways).

The fun thing is, unlike in LCVR, the legs move when you are moving in your playspace, instead of just sliding over the floor. You will still slide if you move slow enough, but in general the game shouldn't really be able to tell the difference between controller movement and physical movement (animation-wise).

**Camera d'custom**

I've also added a custom camera that will be immediately available in the first version. The custom camera is basically just a copy of the three VR cameras (main, UI, top-layer), but with VR rendering disabled, and a higher priority so they show up on the monitor. The custom camera is a much better fit for when you are recording your gameplay, since you have full control over your FOV, and it is properly centered. If you are not recording, it's recommended to keep this disabled, since otherwise it's an unnecessary load on your GPU (with custom camera enabled, there are basically 9 cameras rendering stuff). There is one thing that doesn't work properly with the custom camera, and that's the yellow/red lines you see when you are tumbling. I might re-add these later (which would mean I have to duplicate and re-create them basically), but for now there are no plans to do that.

**Bug spray**

A lot of bugs have also been fixed, too many to list here, but stuff like inventory bugs, lighting bugs, rendering bugs, etc. There are still around 12 TODO's I'm working on (not including TODO's left in the source code), and more TODO's might be added before 1.0.0, so for now I still do not have an indication of when the mod might release, but at least most of the difficult stuff is over, so it shouldn't be any much longer now!

Anyways, I will keep y'all updated!

# May 7th 2025

More time passed, more changes happened, lets cut right to the chase.

**Settings Menu**

I've finally added the settings menu, where you can change most settings, both in flatscreen and in VR, and most of these settings apply immediately without needing restarts. This took quite some time to implement, since I had to manually recreate the UI as it is made in the base game.

**Custom Tumble UI**

Because the Tumble UI is displayed in a Screen Space Camera canvas, it is not visible when using the custom camera. So to account for this, I've created a clone of the Tumble UI which is displayed on the screen when the custom camera is enabled. Speaking of the Tumble UI, there was a bug, or rather design issue, that caused it to only look normal on my specific headset with my specific VD settings. This has now been fixed, and now it will only look off when using extreme resolutions (so very tiny, or extremely large, both of which are unplayable anyways).

**Interactor Occlusion**

Putting your hand into a wall will now prevent it from picking up items. This fixes an issue/exploit where you can grab items through walls. If you are already holding an item, you will still keep holding it even when you're intersecting with a wall. You just cannot pickup any **new** items when your hand is occluded.

**Camera Effcts**

This game has quite a few camera effects that it uses to make the game more immersive on flatscreen, however these effects can be quite nauseating and uncomfortable in VR. To account for this, I've reduced a few of the camera effects by 75% (which can be reduced even further in the game's base settings), and other effects have been disabled completely.

**BETA!**

Semiwork has released a public beta for the upcoming R.E.P.O. update. This beta kind of threw a wrench into the development of the VR mod, since I now have to account for more UI, and need to design and implement a whole new feature: Expressions.

The first thing the beta made more difficult was the fact that there's now multiple places in the game where a keyboard is required, for example to create a lobby you need to fill in a password (or leave it empty), and to search for lobbies, and probably more stuff as well. I already needed to add a keyboard into the game anyways, but this now needed to be fast tracked since you otherwise were not able to create a private lobby (as the UI for it was also invisible to begin with).

Another feature, overcharge, seems to work perfectly already, which is nice! I am most likely going to move the overchange UI to a different spot, but otherwise this needed no changes in VR at all!

**Player Stats**

Normally, when you pick up the map, you can see your player stats. This didn't work in VR yet, and even if it did, it would be quite annoying to look at, since the map is held by either of your hands, but the UI would be somewhere completely different. So I've decided to mount the stats to the map tool, so when picking it up, the stats slide out of the map display. Depending on which hand is used to pick up the map, the text will be displayed either on the left side, or the right side.

**CH-CH-CH-CHAT!**

Something I refrained from adding in LCVR and CWVR (did CW even have a text chat?), was the chat. Well in R.E.P.O. the chat is just too goofy to *not* implement, so I decided to create it anyways. One reason that makes the chat in R.E.P.O. significantly easier is that there's no chat history, so no annoying UI to deal with. The chat, once displayed, is gone, and ready for the next message. Another reason that I wanted to add the chat in the VR mod was because your chat can be "possessed", and I did want that to be visible.

So I have added the chat, and it even has the "previous" and "next" functionality, so you can easily repeat a message without having to type it out again (which takes, uhh, a bit longer than on PC). Chat commands like `/greenscreen` also seem to work fine in VR!

**Spectating**

Okay, it's not as good as it is in LCVR, but at least spectating now works in VR! It still uses an orbital camera (which can be controlled using the movement joystick), and you are also able to turn the camera with snap/smooth turn, just like when playing the game. Not a lot I can say about this, it's a very simple "slap VR on it" fix.

**What's next?**

Well, not a lot, for real this time. Expressions are most likely the biggest feature that needs to be added before release. I also still have my "secret flashlight feature" that I need to implement, but I think that won't take a lot of time. Once those two are done, it's basically only miscellaneous stuff, like haptic feedback, and maybe some visual changes, but other than that, the mod might be finished.

As always, you'll hear from me.

EDIT: Oh almost forgot, the mod will be targeting the upcoming R.E.P.O. version, so if the mod is finished before then, it will be delayed until the update comes out of beta.
