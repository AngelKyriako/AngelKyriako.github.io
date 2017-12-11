---
title: The Big Nom | Ludum Dare 40
---

## Intro

I participated for the first time in an online game jam*, more specifically in  the fourtieth Ludum Dare. A game was developed with the Unity game engine and in this post I am going to share a bit of the process of the development of *'THE BIG NOM'*.

* *A game jam is an event that lasts a few days in which teams of people are challenged to deliver a playable game based on a specific theme.*

## Finding a team
**1-12-2017**

I have no idea how I learned about this jam. At first, I was skeptical about *'sacrificing'* three days of chilling time (and then some more) to join in. I certainly was not in the mood to go at it solo for the Compo* invariant of it. 

Eventually, I found a team that needed more programming guns the previous night before the jam via [crowdforge.io/jams](https://crowdforge.io/jams) and thought why now, it should be fun.

The team consisted of two programmers, one artist and a sound designer.

* *While default game jam entries can be submitted in the next 72 hours by a solo dev or a team, in ludum Dare, Compo is the hardcore mode where a single person makes a game with everything custom made in 48 hours.*

## The theme is unleashed
**2-12-2017, 4.00(am)**

> The more you have, the worse it gets.

Most of us are sleeping at this point, I know for sure I was :).

## Picking up on game design
**2-12-2017, 11.00**

The only things that was already setup were:
- discord group chat
- github empty project

We started gathering up in a discord group quite late the next morning.

In the beginning, we spend a few hours with the other developer discussing game design ideas over a call. That is until it was proven to be a waste of time, since the artist had already started to create a 3D models based on an idea of his choice. 
> You are a princess that eats cakes and could drink teas to increase/descrease her weight. She could jump, walk in 3D space and pickup items. Maybe, we could have buttons on the ground that could trigger other objects to move and then maybe a balanced wood that requires some weight to triggered. What about traps ? They could be triggered when damage the player when they are overweight.


**2-12-2017, 15.00**  
I gathered up questions for the artist (and designer) to be answered in a google doc in order to create a few specific mechanics that would result into the bare minimum playable game he had in his head.


**2-12-2017, 18.00**  
Eventually we are able to start coding based on the following:
- First person camera / controls
- Player eats cake items that raise my weight.
- Player drinks tea items that reset my weight.
- Player jumps as high as allowed by my weight.
- Player picks up box items when at max weight
- Player(at max weight) or boxes can trigger buttons on the ground.
- Buttons on the ground 'do stuff'. 
- An elevator that moves when the player is on it at max weight.
- Dummy floating platforms exist that keep going back and forth no matter what.

Not bad, it has a good *'possibly fun'* / *'difficult to be made'* ratio.

## Time to get dirty
**2-12-2017, 19.00**

The other developer was interested in creating a custom player controller for user input / camera movement, instead of using the standard unity player controller, so he would start working on that.

I would start working on the character & environment gameplay logic far away from the Unity API to ensure we do not do double the work. At some point we would merge our work and recap what we want to work next.

The artist is keeping firing away 3D models.

The sound designer is mostly away from the chat. However, from the google doc he knows what SFX are needed and when he should have them ready.

## Mechanics breakdown
**3-12-2017, 10.00**

At this point I am done with the first draft of the gameplay logic. The other dev needs some more time on camera / controls so I am moving alone on making the environment interactable and setting up a few base prefabs that would allow us to setup the mechanics above.
- Tweenable object prefab: moves between a pair of positions.
- Terrain button prefab: can trigger one or more tweenables
- Consumable object prefab: is destroyed when the player collides with them and apply an effect as they do so.

Based on those three prefabs and some event driven programming we should be able to have whatever we need for the game:
- Anything that moves (door animation, floating platform, elevator*) is a tweenable.
  - the elevator is actually both a tweenable and a terrain button that tweens itself.
- the terrain button triggers multiple tweenable objects.
- The cake and the tea are consumable objects and could be tweenables if need be (.eg drop from the sky at a button press).


## Multiscene setup
**3-12-2017, 14.00**

We have huge communication problems between dev & art department. Because of that we get git conflicts on .unity files very frequently and have huge convos in the chat while trying to resolve them. 

Eventually, I am changing the project to work with multiple scenes loaded at startup and all these get reduced at a great degree.
- Master scene
  - Contains script managers & lights
- LevelSubScene
  - Contains our single level. That way more levels could be setup if we find the time to do so.
- TestSubScene
  - My playground for testing the environment prefabs I am working on.

**3-12-2017, 16.00**

## Dev Gathering

A basic player controller is ready. Its not FPS yet, but it moves, has a state machine and is well written enough to be hooked to my logic code.

I am having a call with the other dev to explain him the gameplay logic code so that he can hook it up with the player controller.

During that call we decide how to move on:
- We are going with to split the player character controller into parts.
  - An FPSController for user input, movement and camera follow.
  - A PickupController for collision detection & player-box interaction.
  - A CharacterController(hook) to be used as a hook point for environment objects to understand as the player and access the player's gameplay logic data (.eg current weight).
- I will continue working with the CharacterController hook and all the environment entity controllers will interact based on this.
- The other dev is going to finish up making camera First Person and take the other tasks which are more coupled with the player character after he finished up.

Meanwhile, our artist keeps firing 3D models on the repo and has already started to setup a level with non interactable objects.

**3-12-2017, 17.00**

After the call, I am breaking down the environment entities so that each one gets its own controller:
  - CakeController & TeaController derive from ConsumableItemController
    - Their consume action is activated when collision is detected with the CharacterController Hook.
  - A FloatingPlatformController that derives from TweenableController
  - An ElevatorController which includes a TerrainButtonController and a TweenableController.
  - A BoxController used as a hook for checking collision of what world object can be picked up and used to trigger a terrain button.
  - A TerrainButtonController that can be visually programmed via drag & drop to trigger tweenable, when pressed by a CharacterController or a BoxController.

## Non First Person Gameplay setup
**3-12-2017, 20.00**

After many hours, I am finished with the first draft of our core mechanics. Prefabs that are in place at this point:
- TerrainButton
- InvisibleTerrainButton
- TweenableWood
- Elevator

My TestScene contains all the above which allows our artists who has now moved to level design to understand what he can do with them.

From this point, most of our environment objects can be created by using the above hooked up in different ways (.eg the elevator is actually an invisible terrain button that triggers the elevator tweenable object).

## Switch to standard First Person Controller
**3-12-2017, 23.00**

The First Person Controller is finished and working but unfortunately does not take care of many edge cases and thus, it does not feel as good as the standard one Unity is offering. We are switching to the standard one and keep the current code in case we find time later to polish.

At this point I have setup more prefabs to be used for level design:
- a FloatingPlatform prefab based on the TweenableController.
- a Door prefab as two TweenableControllers to be triggered by a button. 

  
## Basic Gameplay in place
**4-12-2017, 1.00**

The other dev has now tweaked the standared FPS controller to our gameplay needs.

The Gameplay starts to take form at this point. There are only buttons that trigger doors and you can eat cakes but hey, its a start.

We can validate that stuff are working via Unity Gizmos.

We have some collision detection problems and the level is breaking all over because of the prefab state not being updated properly, but we keep fixing those problems.

## Second Dev Gathering and new Mechanics
**4-12-2017, 2.00**

We are having a call with the other dev after he tweaked the standared FPS controller to our gameplay needs.

Summary:
- He continues with:
  - PickupController
  - MainMenu/Pause UI
- I continue with:
  - Fixing environment prefabs and making them friendlier for the artist to use for level design.
  - Setting up new mechanics.
    - Wooden balance.
      - A box on one end
      - An invisible button at the other other that tweens & destroys the box if pressed at medium weight.
      - Is setup so that it cannot be jumped from the ground when at max weight.
    - Checkpoint.
  
## Gameplay in place
**4-12-2017, 4.00**

New environment mechanics can be introduced blazingly fast at this point, the code is well written and its very easy to update. However, most of the times its just a matter of hooking prefabs together properly and setting up controller settings, rather than coding.

Eventually, after a thousand git conflicts in scenes and prefabs resolved, the gameplay starts to work.

Meanwhile, I have created the checkpoint and wooden balance prefab in the test scene and our sound designer has created a shitload of audio for the game.

Finally, we have found our flow and can work quite well together. Better late than ever !

## The last day

The last day of the jam was a Monday which meant that we did not work full.

**4-12-2017, 17.00pm**

Checkpoints, Floating platforms, Wooden Balance and a kickass elevator are in the level now. The game has started to look cool.

I skipped a couple of hours from work to start earlier, since we had a ton of things to do:
- New Mechanic
  - The player character gets bigger/smaller based on weight, in order to get blocked at narrow places.
- A few tweaks in the prefabs after the level design update were required.
- Audio setup
- HUD UI
- Main Menu UI
- Popup Tips UI would be nice to have

**4-12-2017, 21.00pm**

By night the other dev has finished up with our HUD UI and has started working with MainMenu UI and a popup UI if can be managed.

I have done almost nothing so far since I am fixing prefabs setup incorrectly until the level is playable.

**4-12-2017, 23.00pm**

At midnight our main menu UI is ready and work is in progress on the popup UI by the other dev.

I am currently fixing up theme and sound effect audio on our event hooks.

**4-12-2017, 1.00am**

We have a MainMenu now and Audio is in place. The level and the lights has been updated many times in the last hours and the game is close to its final form.

The popup UI is done as well, we push tips on the player during specific events now. I am starting to work on them.

I am starting making the player's size to be updated based on their weight, to get stuck on narrow places.

## Submitting nightmare

**4-12-2017, 3.30am (30 minutes left)**

More level design issues has been fixed until this point

The guys have started making a build to secure the entry in the jam, while I am finishing up on the popup tip UI spawns.

**4-12-2017, 3.45am (15 minutes left)**

In the windows build, the pickup breaks. A dummy hot fix is pushed on the spot to delay the pickup drop by the other dev.

I just finished with the Tip Popup UI. It's pushed to git as well.

**4-12-2017, 3.55am (5 minutes left)**

The hot fix worked, my tips do not break the game so we are good to go.

The build is uploaded.

The naming discussion took 10 seconds:
- The [ldjam.com page entry](https://ldjam.com/events/ludum-dare/40/chubby-princess) is named Chubby Princess.
- The [itch.com download link](https://linko3d.itch.io/the-big-nom) is named Princess Cake.

The description and a few screenshots are added.


We are done, GG !

## The week after

In the following days, we will polish the game a bit by pushing updates in:
- 3D graphics
- 2D UI
- gameplay mechanics
- level design
- lights
- webGL build
- localization

Eventually a rename is made to [The Big Nom](https://github.com/Linko-3D/LD40).

A gameplay video is uploaded to youtube.
<iframe 
	width="560" height="315"
	src="https://www.youtube.com/embed/HB3FgHqssQU"
	frameborder="0" gesture="media"
	allow="encrypted-media"
	allowfullscreen
></iframe>

I do not really care how many votes it takes, I am very happy with the end result.

I am gonna have fun checking out other ludum dare projects now. There are some pretty cool stuff [there](https://ldjam.com/games) :).
