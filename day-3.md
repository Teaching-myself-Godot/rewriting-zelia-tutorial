# Day 3 - Importing more Tiles the lazy way

'Cus a lazy developer is a good developer.

Today we want to achieve these goals:

1. Test if tiles of different `Atlases` will stick to eachother when assigned to the same `Terrain`
2. If no, never mind, if yes then yay!
3. Detect an `Atlas`-import pattern by `diffing` the `world.tscn` file.
4. Try to auto-import all the `.png` files in `surface_maps` with our own script.

However, _will_ we achieve these goals?

Let's find out. Spoiler: [skip to step 3](#step-3---detecting-an-automatable-pattern-when-importing-a-new-atlas)

## Prerequisites

Code tag for when you lost your project: [after-day-2](https://github.com/Teaching-myself-Godot/godot-zelia/tree/after-day-2)

# Step 1, long answer short

My hypothesis has to be rejected...

## The bad news

You _cannot_ assign the same `Terrain` to 2 different `Atlases` the same way without getting a messy jumble. 

Drawing tiles will therefore work a little differently than in my old editor.

## The good news

We have one less technical debt: `Grass and Dirt` was a good terrain name after all.

The names of `Terrains` will be documenting what they refer to better.

# Step 2, short answer. Period.

Never mind.

# Step 3 - Detecting an automatable pattern when importing a new `Atlas`

Let's add a new `Terrain`, import a new `png`-file as `Atlas` and link up the two together.