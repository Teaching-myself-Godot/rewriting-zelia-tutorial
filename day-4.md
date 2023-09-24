# Day 4 - Casting Fireballs

Today we're going to let Zelia cast fireballs.

Want to start from here? 

Clone or download the result of day 3 from [github](https://github.com/Teaching-myself-Godot/godot-zelia/tree/after-day-3)

## The steps for today

1. [Add a `Fireball` scene and test its flying](#add-a-fireball-scene-and-test-its-flying)
2. [Spawn fireballs when she casts](#spawn-fireballs-when-she-casts)
3. [Make fireballs collide with the `TileMap`, not with the `Player`](#make-fireballs-collide-with-the-tilemap-not-with-the-player)
4. [Generate renditions to make the fireball dissipate](#generate-renditions-to-make-the-fireball-dissipate)

# Add a `Fireball` scene and test its flying

We will use a single `.png` image as a resource for the fireball. You can download it here:

[assets/fireball.png](https://github.com/Teaching-myself-Godot/rewriting-zelia-tutorial/raw/main/assets/fireball.png)

## Adding the fireball image asset and Fireball scene

1. Create a resource dir `res://projectiles/fireball`
2. Place `fireball.png` inside
3. Create a new `Area2D` scene and call it `Fireball`
4. Save it in `res://projectiles/fireball/fireball.tscn`
5. Add these child-nodes:
- `AnimatedSprite2D`
- `CollisionShape2D`
6. Add a `SpriteFrames` to the `AnimatedSprite2D`-node
7. Drag the `fireball.png` into its `default` animation
8. Use a `CircleShape2D` for the `CollisionShape2D` and draw it like this:

![Fireball collision shape](screenshots/fireball-collision-shape.png)


Although we are using only one image, we are using `AnimatedSprite2D`, not simply `Sprite2D`.

This is beacause in [step 4](#generate-renditions-to-make-the-fireball-dissipate) we will generate rendition images dynamically for a 'dissipating' animation.

## Let it fly

To make the fireball fly takes a very short script.

Attach a script to the fireball scene by right-clicking `Scene > Fireball` and picking `Attach Script` from the context menu. 

Leave the defaults in place, and write the script:

```gdscript
extends Area2D

# Initialize the fireball with zero speed (x = 0, y = 0)
@export var velocity = Vector2.ZERO

func _physics_process(delta):
	# Update position by velocity-vector
	position += velocity * delta
```

We exported the `velocity`-property so we can test this code.

Run the current scene by pressing `F6`.

Wile the scene is running:
1. Go to `Scene > Remote > root > Fireball`
2. Make sure the game window is visible
3. Find `Inspector > Members > Velocity`
4. Set `x` to `50`:

![remote debug fireball](screenshots/remote_debug_fireball.png)

This makes the fireball fly out the viewport to the right, never to return.

If we're going to spawn hundreds of thousands of them, it will become a dire memory leak.

## Clean it up

To clean up the potential mess, we'll follow some instructions about nodes leaving the viewport from here:

[Enemy script from: My First 2D Game](https://docs.godotengine.org/en/stable/getting_started/first_2d_game/04.creating_the_enemy.html#enemy-script):

Following these steps, we will now:

1. Add a [`VisibleOnScreenNotifier2D`](https://docs.godotengine.org/en/stable/classes/class_visibleonscreennotifier2d.html#class-visibleonscreennotifier2d)
2. In the `2D` scene view make sure the `Rect` surrounds the fireball

![rect surrounds ball](screenshots/rect-surrounds-ball.png)

3. Click on `Node` next to `Inspector`
4. Double click `Signals > screen_exited()`
5. Leave defaults in tact and click `Connect`
6. Now add this line to the new function `_on_visible_on_screen_notifier_2d_screen_exited`:

```gdscript
func _on_visible_on_screen_notifier_2d_screen_exited():
	queue_free()
```

Run the current scene again by pressing `F6` and repeat the remote debugging instructions, setting x to `50`

![remote debug](screenshots/remote_debug_fireball.png)

Now look at the node-tree under `Scene` until the fireball exits the game viewport.

Congratulations, we deleted it.

# Spawn fireballs when she casts

TODO write about fireball spawning!

[Instancing with signals](https://docs.godotengine.org/en/stable/tutorials/scripting/instancing_with_signals.html)

```gdscript
# Cast fireball signal declaration
signal cast_fire_magic(fireball, direction, location)

# Fireball class
var Fireball = preload("res://projectiles/fireball/Fireball.tscn")

func _on_fireball_interval_timer_timeout():
	if movement_state == MovementState.CASTING:
		# elegant, yet no fit:
		var origin = position + Vector2(10, 0).rotated(cast_angle) + Vector2(0, 2)
		cast_fire_magic.emit(Fireball, cast_angle, origin)
```


[Instancing](https://docs.godotengine.org/en/stable/getting_started/step_by_step/instancing.html#doc-instancing)

![link for instancing](https://docs.godotengine.org/en/stable/_images/instancing_scene_link_button.png)

**Connect the signal from World scene!**

```gdscript
# world.gd
func _on_player_cast_fire_magic(Fireball, direction, location):
	var fireball = Fireball.instantiate()
	add_child(fireball)
	fireball.rotation = direction
	fireball.position = location
	fireball.velocity = Vector2.from_angle(direction) * 150
```


# Make fireballs collide with the `TileMap`, not with the `Player`

TODO write about:

- player:   mask and layer 1 (for now)
- world:    mask and layer 1 and 2 (for now)
- fireball: mask and layer 2 (for now)

Handle fireball collision, final script

`DissipateTimer` added.

```gdscript
extends Area2D

@export var velocity = Vector2.ZERO


func _ready():
	$AnimatedSprite2D.play("default")

func _physics_process(delta):
	position += velocity * delta

func _on_visible_on_screen_notifier_2d_screen_exited():
	queue_free()

# collision with something in collision layer/mask 2
func _on_body_entered(body):
	$AnimatedSprite2D.play("dissipate")
	$DissipateTimer.start()
	velocity *= 0.1

func _on_dissipate_timer_timeout():
	queue_free()
```


- Fireball z-index changed: Show behind parent is ON!


# Generate renditions to make the fireball dissipate

- [Singletons (Autoload)](https://docs.godotengine.org/en/stable/tutorials/scripting/singletons_autoload.html)
- [Image](https://docs.godotengine.org/en/stable/classes/class_image.html#class-image)
- [ImageTexture](https://docs.godotengine.org/en/stable/classes/class_imagetexture.html)