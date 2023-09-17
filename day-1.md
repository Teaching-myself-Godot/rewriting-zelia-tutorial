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

You can download her png files from [assets/zelia-player.zip](assets/zelia-player.zip)

