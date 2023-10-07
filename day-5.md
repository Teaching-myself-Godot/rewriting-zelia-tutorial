# Slimes, Breaking tiles, Falling Tiles, Scenery Tiles and Shaders

So for you it is day five, for me it's now day 14. :D

## Why so slow?

The reason is the research I did into Godot stuff you don't get out of the box:

- recipes that are stably invented for version 3, but need some translation to 4.1
- shading language concepts (last time I tried _that_ was back when GLSL was just released)
- which 'physics'-body to use for what and how (Rigid -> Static -> Rigid -> Character -> Area -> Static -> Rigid -> ooooh... I used the wrong method to apply the motion!)
- where's that polygon?! TileMap -> get_used_cells() -> TileData -> no ... TileMap -> get_used_cells() -> TileSetSource -> TileSet**Atlas**Source -> TileData -> get_collision_polygon_points() -> no?? ... yes! ... forgot to draw a polygon!

You're going to be spoiled with some 4.1 reïnvented recipes! (I hope)

## Any other excuses?

And the other reason is: my son wanted to write a 2.5D game last weekend: [Untitled Shoot 'em Up](https://github.com/Teaching-myself-Godot/untitled-shoot-em-up/tree/master)


## What we'll do today

1. [Make a bouncing Slime monster](#make-a-bouncing-slime-monster)
2. [Add the tree-trunk terrain](#add-the-tree-trunk-terrain)
3. [Make tiles Zelia can break](#make-tiles-zelia-can-break)
4. [Allow those breakable tiles to fall down](#allow-those-breakable-tiles-to-fall-down)
5. [Reuse tiles as background scenery](#reuse-tiles-as-background-scenery)
6. [Review my failed attempt to replace my TextureRenditions singleton with shaders](#review-my-failed-attempt-to-replace-my-texturerenditions-singleton-with-shaders)


# Make a bouncing Slime monster

Every game needs one.

1. Download the zip: [assets/green-slime.zip](https://github.com/Teaching-myself-Godot/rewriting-zelia-tutorial/raw/main/assets/green-slime.zip)
2. Create the resource dirs `res://monsters/slime/green`
3. Extract the .png files in that `res://monsters/slime/green` dir 
4. Create a new `CharacterBody2D`-scene
5. Rename it to `Slime`
6. And save it into `res://monsters/slime/slime.tscn`
7. Give slime a child node `AnimatedSprite2D`
8. Navigate to its `Inspector Sprite Frames > SpriteFrame > Animations`
9. Change `default` into `airborne`, add `slime/green/5.png` to it
10. Add `floor_bounce` and add `1.png` - `4.png` to that -> in that order
11. Set `floor_bounce` to `7 fps` for the nicest effect:

![slime](screenshots/slime.png)

## Make the slime collide and bounce 

Because our slime looks a little different depending on what state it's in, let's give it 2 collision shapes:

1. Add a child `CollisionShape2D`-node to `Scene > Slime` 
2. Name it: `AirborneCollisionShape`
3. Pick `CircleShape2D` under `Inspector > Shape`
4. Align it nicely around the `airborne` animation sprite:

![slime airborne coll circle](screenshots/airborne-collision-shape.png)

Next:
1. Add another child `CollisionShape2D`-node to `Scene > Slime` 
2. Name it: `FloorBounceCollisionShape`
3. Pick `CapsuleShape2D` under  `Inspector > Shape`
4. Align it around the first `floor_bounce` animation sprite:

![slime capsule](screenshots/slime-capsule.png)

### Slimes must bounce

Add the slime to the main scene:

1. Open `res://world.tscn`
2. Drag at least one slime scene `res://monsters/slimes/slime.tscn` into the `World`-scene
3. Test the main scene `World` with `F5` and observe that the slime hangs there doing nothing:

![slime hanging there](screenshots/slime-hanging.png)

4. Open the `res://monsters/slimes/slime.tscn` scene
5. Attach a script to it, picking the default values in the dialog
6. You might notice a lot of suggested code for a `CharacterBody2D` - although it could be fun to try it out, it's not what we're looking for.
7. Remove all code and replace the `_physics_process` function body with `pass`:

```gdscript
extends CharacterBody2D


func _physics_process(delta):
	pass
```

### Adding the `MovementState`s

We have 2 animations currently, so let's create 2 movement states to match:
```gdscript
extends CharacterBody2D

enum MovementState { AIRBORNE, FLOOR_BOUNCE }

var movement_state : int

func _ready():
    # assume it starts out hanging in the air
    movement_state = MovementState.AIRBORNE

    # start up the correct animated sprite sprite frames for that state
	$AnimatedSprite2D.animation = "airborne"
	$AnimatedSprite2D.play()

```

We also know the slime must bounce around. We can use some familiar stuff for that:

```gdscript
# We want the level designer to be able to modify stuff like this.
@export var JUMP_VELOCITY = -400.0
var gravity = ProjectSettings.get_setting("physics/2d/default_gravity")
```

Now let's at the very least allow some `move_and_slide()` in the `_physics_process`, applying the gravity:
```gdscript
func _physics_process(delta):
	velocity.y += gravity * delta
    move_and_slide()
```


# Add the `tree-trunk` terrain

Because Zelia (in the original game) runs into tiles that are not just squares, our rewrite must have them as well:

1. Create a new Terrain in `World > Inspector > Terrain Sets > Terrains`: "Tree Trunk"
2. Add the `res://surface_maps/tree-trunk/1.png` to our existing `TileSet` - like we learned on [day 2](day-2.md#making-an-atlas-of-an-image).
3. Be sure to assign Terrain `1` this time, under the `Select` step
4. When you get to the step for `Physics` with the `F`-hotkey we're going to do something new, but first...
5. Don't forget to also create the 1 alternative tile (in case you want to handle all the drag-and-draw painting)

### Create polygons

...TODO screenshot and explicit description here! ...

### Solve Technical debt 3

TODO --> Unloosen tight coupling, separate `World` into `Game` and `Terrain`

# Make tiles Zelia can break

Still not as cool as in the original, but close enough!


# Allow those breakable tiles to fall down

Rigid -> Static -> Rigid -> Character -> Area -> Static -> Rigid -> ooooh...

Spoiler: it was [StaticBody2D](https://docs.godotengine.org/en/stable/classes/class_staticbody2d.html#class-staticbody2d) I wanted all along.

# Reuse tiles as background scenery

My first [shader](https://docs.godotengine.org/en/stable/tutorials/shaders/your_first_shader/your_first_2d_shader.html) was not that fancy at all.

# Review my failed attempt to replace my TextureRenditions singleton with shaders

My [second shader](https://github.com/Teaching-myself-Godot/godot-zelia/blob/no-per-instance-shaders-for-canvas_items/dissipation_shader.gdshader) was pretty cool, but alas: all the fireballs scattered together.
