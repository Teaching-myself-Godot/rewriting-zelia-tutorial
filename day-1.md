# Day 1 - Controlling the player

So before we start a day: please look at the prerequisites and make sure you are familiar with all the links referred to.

## Prerequisites

I started learning godot 4.1.1 yesterday.

What I did first is make the example 2D game there, after carefully reading up on the key concepts:

- [Key concepts](https://docs.godotengine.org/en/stable/getting_started/introduction/key_concepts_overview.html)
- [Step by Step](https://docs.godotengine.org/en/stable/getting_started/step_by_step/index.html)
- [Your first 2D game](https://docs.godotengine.org/en/stable/getting_started/first_2d_game/index.html)

In case you, like me, are working in windows and have 2 old monitors connected via laptop docking station: my godot engine crashed while moving it to an external monitor. 

Apparently, it's a [known issue](https://github.com/godotengine/godot/issues/44778) also [here](https://github.com/godotengine/godot/issues/28785).

## A new project

Let's start a new project. 

Because Zelia once started out as an HTML5 webgame it might work well in the web again. Also I want to be able to compile to as many platforms as possible this time, so let's pick the `Compatibility` renderer:
![Compatibility Renderer](screenshots/renderer-choice.png)

## Importing the player assets

Because Zelia has become a pretty big project we should pay attention to directory structure early.

Let's make a subdir in the root folder named `player` with another subdir named `images`.

You can download her png files from [assets/zelia-player.zip](https://github.com/Teaching-myself-Godot/rewriting-zelia-tutorial/raw/main/assets/zelia-player.zip)

Your FileSystem tab should now look like this:

![import-player](screenshots/import-player.png)

Zelia also has a jump sound. Let's create a sounds dir for it.
Here is her [jump-sound.wav](https://github.com/Teaching-myself-Godot/rewriting-zelia-tutorial/raw/main/assets/jump-sound.wav):

![import-player](screenshots/import-player-sound.png)


## Player Node setup

For this bit we'll be guided by the instructions in [Your first 2D game](https://docs.godotengine.org/en/stable/getting_started/first_2d_game/02.player_scene.html). 

If you're missing details on how to execute the next steps, please refer to it.

1. Add an `Area2D` node and rename it to `Player`.
2. Save the `player.tscn` file in `res://player/player.tscn`
3. Make `Player`'s [child nodes not selectable](https://docs.godotengine.org/en/stable/_images/lock_children.webp)
4. Add an `AnimatedSprite2D` child node with a `SpriteFrames` resource
5. Click `SpriteFrames` again to open the `Animations` panel. 

### Setting up the animations

We need the following animation names in the list:
- casting
- dying
- forward
- idle
- jumping
- running

The asset files you downloaded give a good filename hint as to where they belong, but I will list them for you anyway.

Add the images like so:

- casting: `cast-1.png` up to `cast-4.png`
- dying: `faint-1` up to `faint-8.png`
- forward: `forward.png`
- idle:  `stand-1.png`
- jumping: `jump.png`
- running: `run-1.png` up to `run-5.png` _AND_ `run-4.png` down to `run-2.png`

### Higher framerate

When testing the animation for running I noticed that it did not look as smooth as I was used to. Matching the original game let's change the framerate from 5 to 15 for `dying` and `running`:

![animation framerate](screenshots/animation-framerate.png)


### Technical debt 1

In the original game `casting` was not - strictly speaking - an animation, but a set of casting orientations based on an angle. We will probably have to fix that later.


### Add a hitbox

So the original Zelia had pixel perfect collisions. I wrote everything myself, so it was quite a battery hog in modern machines and a performance killer in older machines. However, I mainly did that to avoid having to draw hitboxes and doing polygon based collision math myself.

_If_ we want pixel perfect collision again, we'd probably follow this recipe on [stackoverflow](https://stackoverflow.com/questions/68063306/how-do-you-do-pixel-perfect-collisions-in-godot-engine#answer-74856202). For now, let's make do with a nice pill shape just like in the [guide](https://docs.godotengine.org/en/stable/getting_started/first_2d_game/02.player_scene.html).

1. Create another child node for `Player` of the type `CollisionShape2D`.
2. Choose `CapsuleShape2D` in the inspector tab next to Shape
3. 'Ungroup' the children of `Player` to make the `CollisionShape2D` selectable
4. Align the box like in the screenshot below 
5. And 'group' them again.

![collision shape alignment](screenshots/pillbox.png)

Save everything and test the current scene with F6 (make sure Zelia's in the viewport).

## Setting up the screen

Zelia looks so small!

That makes perfect sense because she's only about 30 pixels tall. The original game resolution was fullscreen and matched the aspect ratio of your active monitor to make the game `320px` wide. 

After stumbling my own way through the settings, I looked up this guide on [resolutions](https://docs.godotengine.org/en/stable/tutorials/rendering/multiple_resolutions.html).

Although I would have preferred the outcome to look like this:

![zelia in pygame](screenshots/zelia-in-sdl-renderer.png)

What I managed to achieve in godot for now looks like this.

![zelia in godot 1](screenshots/zelia-in-godot-renderer.png)

However, dropping the full screen requirement and in stead opting for a resizable window achieved a way better scaling effect, ending up with this:

![zelia in godot 2](screenshots/zelia-in-godot-renderer-2.png)


### The final resizable window setup I'm happy with

1. Open `Project > Project Settings`
2. Go to `Display > Window`
3. Set `Viewport Width` to `320`
4. And `Viewport Height` to `180`
5. Make sure `Mode` is set to `Windowed`
6. And that `Resizable` is checked to `On`
7. Set the `Stretch` mode to `canvas_items`
8. And the `Aspect` to `keep`


