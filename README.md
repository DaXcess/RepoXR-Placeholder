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
